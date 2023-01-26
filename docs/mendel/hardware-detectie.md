# Hardware Detectie

## Een kijk naar Pipelining met meerdere Edge TPU's

Er bestaat reeds een bibliotheek, ontwikkeld door het Coral team, om een TensorFlow model te verdelen, of *pipelining*, over meerdere Edge TPU's, genaamd *Coral Model Pipelining*. Exact wat nodig is voor dit project.

> “An example showing how to pipeline a model across multiple Edge TPUs, allowing you to significantly increase throughput for large models such as Inception.”

*Aanhaling uit [[7]](bronnen.md#mendel-linux)*

Deze bibliotheek bestaat zowel in Python [[8]](bronnen.md#mendel-linux) als in C++ [[9]](bronnen.md#mendel-linux). Om te weten te komen hoe deze eventueel in dit project kunnen gebruikt worden moet er opzoek gegaan worden hoe deze code de Edge TPU detecteerd en ermee communiceert.
Onderstaande code uit de *pycoral* bibliotheek toont ons hoe dit in zijn werk gaat.

```python
from pycoral.utils.edgetpu import list_edge_tpus
```

```python
def _get_devices(num_devices):
  """Returns list of device names in usb:N or pci:N format.
  This function prefers returning PCI Edge TPU first.
  Args:
    num_devices: int, number of devices expected
  Returns:
    list of devices in pci:N and/or usb:N format
  Raises:
    RuntimeError: if not enough devices are available
  """
  edge_tpus = list_edge_tpus()
```

*Bovenstaande code uit: pycoral/examples/model_pipelining_classify_image.py [[10]](bronnen.md#mendel-linux)*

```python
from pycoral.utils.edgetpu import list_edge_tpus
```

```python
from pycoral.pybind._pywrap_coral import ListEdgeTpus as list_edge_tpus
```

*Bovenstaande code uit: pycoral/pycoral/utils/edgetpu.py [[11]](bronnen.md#mendel-linux)*

```cpp
m.def(
"ListEdgeTpus",
      []() {
        py::list device_list;
        for (const auto& item :
             edgetpu::EdgeTpuManager::GetSingleton()->EnumerateEdgeTpu()) {
          py::dict device;
          device["type"] =
              item.type == edgetpu::DeviceType::kApexPci ? "pci" : "usb";
          device["path"] = item.path;
          device_list.append(device);
        }
        return device_list;
      },
      R"pbdoc(
      Lists all available Edge TPU devices.
      Returns:
        A list of dictionary items, each representing an Edge TPU in the system.
        Each dictionary includes a "type" (either "usb" or "pci") and a
        "path" (the device location in the system). Note: The order of the
        Edge TPUs in this list are not guaranteed to be consistent across
        system reboots.
    )pbdoc");
```

*Bovenstaande code uit: google-coral/pycoral/src/coral_wrapper.cc [[12]](bronnen.md#mendel-linux)*

```c
class EDGETPU_EXPORT EdgeTpuManager {
 public:
  using DeviceOptions = std::unordered_map<std::string, std::string>;
  struct DeviceEnumerationRecord {
    DeviceType type;
    std::string path;

    // Returns true if two enumeration records point to the same device.
    friend bool operator==(const DeviceEnumerationRecord& lhs,
                           const DeviceEnumerationRecord& rhs) {
      return (lhs.type == rhs.type) && (lhs.path == rhs.path);
    }

    // Returns true if two enumeration records point to defferent devices.
    friend bool operator!=(const DeviceEnumerationRecord& lhs,
                           const DeviceEnumerationRecord& rhs) {
      return !(lhs == rhs);
    }
  };

  // Returns pointer to the singleton object, or nullptr if not supported on
  // this platform.
  static EdgeTpuManager* GetSingleton();

  // NewEdgeTpuContext family functions has been deprecated and will be removed
  // in the future. Please use OpenDevice for new code.
  //
  // These functions return an unique_ptr to EdgeTpuContext, with
  // the intention that the device will be closed, and associate resources
  // released, when the unique_ptr leaves scope.
  //
  // These functions seek exclusive ownership of the opened devices. As they
  // cannot open devices already opened by OpenDevice, and vice versa.
  // Devices opened through these functions would have attribute
  // "ExclusiveOwnership", which can be queried through
  // #EdgeTpuContext::GetDeviceOptions().

  // Creates a new Edge TPU context to be assigned to Tflite::Interpreter. The
  // Edge TPU context is associated with the default TPU device. May be null
  // if underlying device cannot be found or open. Caller owns the returned new
  // context and should destroy the context either implicity or explicitly after
  // all interpreters sharing this context are destroyed.
  virtual std::unique_ptr<EdgeTpuContext> NewEdgeTpuContext() = 0;

  // Same as above, but the created context is associated with the specified
  // type.
  virtual std::unique_ptr<EdgeTpuContext> NewEdgeTpuContext(
      DeviceType device_type) = 0;

  // Same as above, but the created context is associated with the specified
  // type and device path.
  virtual std::unique_ptr<EdgeTpuContext> NewEdgeTpuContext(
      DeviceType device_type, const std::string& device_path) = 0;

  // Same as above, but the created context is associated with the given device
  // type, path and options.
  //
  // Available options are:
  //  - "Performance": ["Low", "Medium", "High", "Max"] (Default is "Max")
  //  - "Usb.AlwaysDfu": ["True", "False"] (Default is "False")
  //  - "Usb.MaxBulkInQueueLength": ["0",.., "255"] (Default is "32")
  virtual std::unique_ptr<EdgeTpuContext> NewEdgeTpuContext(
      DeviceType device_type, const std::string& device_path,
      const DeviceOptions& options) = 0;
```

```c
// Enumerates all connected Edge TPU devices.
  virtual std::vector<DeviceEnumerationRecord> EnumerateEdgeTpu() const = 0;

  // OpenDevice family of functions return a shared_ptr to EdgeTpuContext, with
  // the intention that the device can be shared among multiple software
  // components.
  //
  // These functions seek shared ownership of the opened devices. As they
  // cannot open devices already opened by NewEdgeTpuContext, and vice versa.
  // The device would be closed after the last reference leaves scope.

  // Opens the default Edge TPU device.
  //
  // Multiple invocations of this function could return handle to the same
  // device, but there is no guarantee.
  //
  // Returns a shared pointer to Edge TPU device. The shared_ptr could point to
  // nullptr in case of error.
```

*Bovenstaande code uit: google-coral/edgetpu/libedgetpu/edgetpu.h [[13]](bronnen.md#mendel-linux)*

```c
// This header defines C API to provide edge TPU support for TensorFlow Lite
// framework. It is only available for non-NNAPI use cases.
//
// Typical API usage from C++ code involves serveral steps:
//
// 1. Create tflite::FlatBufferModel which may contain edge TPU custom op.
//
// auto model =
//    tflite::FlatBufferModel::BuildFromFile(model_file_name.c_str());
//
// 2. Create tflite::Interpreter.
//
// tflite::ops::builtin::BuiltinOpResolver resolver;
// std::unique_ptr<tflite::Interpreter> interpreter;
// tflite::InterpreterBuilder(model, resolver)(&interpreter);
//
// 3. Enumerate edge TPU devices.
//
// size_t num_devices;
// std::unique_ptr<edgetpu_device, decltype(&edgetpu_free_devices)> devices(
//     edgetpu_list_devices(&num_devices), &edgetpu_free_devices);
//
// assert(num_devices > 0);
// const auto& device = devices.get()[0];
//
// 4. Modify interpreter with the delegate.
//
// auto* delegate =
//     edgetpu_create_delegate(device.type, device.path, nullptr, 0);
// interpreter->ModifyGraphWithDelegate({delegate, edgetpu_free_delegate});
//
// 5. Prepare input tensors and run inference.
//
// interpreter->AllocateTensors();
//   .... (Prepare input tensors)
// interpreter->Invoke();
//   .... (Retrieve the result from output tensors)
```

```c
enum edgetpu_device_type {
  EDGETPU_APEX_PCI = 0,
  EDGETPU_APEX_USB = 1,
};

struct edgetpu_device {
  enum edgetpu_device_type type;
  const char* path;
};

struct edgetpu_option {
  const char* name;
  const char* value;
};

// Returns array of connected edge TPU devices.
EDGETPU_EXPORT struct edgetpu_device* edgetpu_list_devices(size_t* num_devices);
```

```c
class EDGETPU_EXPORT EdgeTpuManager {
 public:
  using DeviceOptions = std::unordered_map<std::string, std::string>;
  virtual std::unique_ptr<EdgeTpuContext> NewEdgeTpuContext(
      DeviceType device_type, const std::string& device_path,
      const DeviceOptions& options) = 0;
```

*Bovenstaande code uit: google-coral/edgetpu/libedgetpu/edgetpu_c.h [[14]](bronnen.md#mendel-linux)*

Bij het runnen van de `coral_wrapper` voor de detectie van TPU's krijg je voor de enige gevonden `edgetpu_device`, met de structuur dat hierboven is gedefinieerd:

```bash
{'type': 'pci', 'path': '/dev/apex_0'}
```

Dit is dus het enige dat Coral nodig heeft om de Edge TPU op een bordje te vinden.

```c
absl::Status SetTensorBuffer(tflite::Interpreter* interpreter, int tensor_index,
                             const void* buffer, size_t buffer_size);

// Returns TPU context or nullptr if requested TPU context is not available.
//
// Parameter `device`:
//   - ""      -- any TPU device
//   - "usb"   -- any TPU device on USB bus
//   - "pci"   -- any TPU device on PCIe bus
//   - ":N"    -- N-th TPU device, e.g. ":0"
//   - "usb:N" -- N-th TPU device on USB bus, e.g. "usb:0"
//   - "pci:N" -- N-th TPU device on PCIe bus, e.g. "pci:0"
//
// Parameter `options`:
//   See edgetpu.h for details.
//
// All TPUs are always enumerated in the same order assuming hardware
// configuration doesn't change (no added/removed devices between enumerations).
// Under the assumption above, the same index N will always point to the same
// device.
//
// Consider 2 USB devices and 4 PCIe devices connected to the host. The way to
// reference specifically USB devices:
//   "usb:0", "usb:1".
// The way to reference specifically PCIe devices:
//   "pci:0", "pci:1", "pci:2", "pci:3".
// The generic way to reference all devices (no assumption about device type):
//   ":0", ":1", ":2", ":3", ":4", ":5".
std::shared_ptr<edgetpu::EdgeTpuContext> GetEdgeTpuContext(
    const std::string& device,
    const edgetpu::EdgeTpuManager::DeviceOptions& options = {});

// The same as above but crashes if requested TPU context is not available.
inline std::shared_ptr<edgetpu::EdgeTpuContext> GetEdgeTpuContextOrDie(
    const std::string& device,
    const edgetpu::EdgeTpuManager::DeviceOptions& options = {}) {
  return CHECK_NOTNULL(GetEdgeTpuContext(device, options));
}

// The same as previously defined `GetEdgeTpuContext` except `device` parameter
// is replaced with two separate ones: `device_type` and `device_index`.
//
// Custom options would only be passed when `device_type` and `device_index` are
// non-empty.
std::shared_ptr<edgetpu::EdgeTpuContext> GetEdgeTpuContext(
    absl::optional<edgetpu::DeviceType> device_type = absl::nullopt,
    absl::optional<int> device_index = absl::nullopt,
    const edgetpu::EdgeTpuManager::DeviceOptions& options = {});

// The same as above but crashes if requested TPU context is not available.
inline std::shared_ptr<edgetpu::EdgeTpuContext> GetEdgeTpuContextOrDie(
    absl::optional<edgetpu::DeviceType> device_type = absl::nullopt,
    absl::optional<int> device_index = absl::nullopt,
    const edgetpu::EdgeTpuManager::DeviceOptions& options = {}) {
  return CHECK_NOTNULL(GetEdgeTpuContext(device_type, device_index, options));
}
```

*Bovenstaande code uit: google-coral/libcoral/coral/tflite_utils.h [[15]](bronnen.md#mendel-linux)*

#### Conclusie omtrent bestaande *Model Pipelining* bibliotheek

Uit dit onderzoek naar hoe de *Model Pipelinging* bibliotheek werkt is gebleken dat de bestaande code niet bruikbaar is. Deze verwacht namelijk dat de TPU's te vinden zijn via de PCI of USB interfaces als PCI of USB peripherals. Beide zijn echter niet deelbaar over een netwerk. Er moet dus een andere manier gevonden worden om de TensorFlow Light modellen over de TPU's te verdelen.

---

## Hardware detectie in Debian

In dit gedeelte wordt er onderzocht hoe hardware detectie in een Debian Linux distributie kan gebeuren. Dit om meer te weten te komen over de Edge TPU's gevonden kunnen worden in het systeem.

Er bestaan een hele hoop packages om hardware op te lijsten:
- lshw
- hwinfo
- inxi
- mount
- dmesg
- lspci

### lshw

*Uit [[17]](bronnen.md#mendel-linux)*

#### Installatie

```bash
sudo apt-get install lshw
```

#### Uitvoering

```bash
mendel@wishful-dog:~$ sudo lshw -short
H/W path  Device  Class      Description
========================================
                  system     Freescale i.MX8MQ Phanbell
/0                bus        Motherboard
/0/0              processor  cpu
/0/1              processor  cpu
/0/2              processor  cpu
/0/3              processor  cpu
/0/4              processor  idle-states
/0/5              processor  l2-cache0
/0/6              memory     985MiB System memory
/0/100            bridge     Synopsys, Inc.
/0/100/0  p2p0    network    QCA6174 802.11ac Wireless Network Adapter
/0/101            bridge     Synopsys, Inc.
/0/101/0          generic    Non-VGA unclassified device
/0/7              generic    Virtual I/O device
/1        usb1    bus        xHCI Host Controller
/2        usb2    bus        xHCI Host Controller
/3        usb0    network    Ethernet interface
/4        wlan0   network    Wireless interface
/5        eth0    network    Ethernet interface
```

#### Conclusie

Deze detectie gaat niet diep genoeg.


### hwinfo

#### Installatie

```bash
sudo apt-get install hwinfo
```

#### Uitvoering

```bash
mendel@wishful-dog:~$ sudo hwinfo --short
cpu:
                       CPU
                       CPU
                       CPU
                       CPU
network:
  p2p0                 Qualcomm Atheros QCA6174 802.11ac Wireless Network Adapter
  wlan0                Qualcomm Atheros QCA6174 802.11ac Wireless Network Adapter
  eth0                 ARM Ethernet controller
network interface:
  eth0                 Ethernet network interface
  p2p0                 Ethernet network interface
  usb1                 USB-Link network interface
  usb0                 USB-Link network interface
  wlan0                WLAN network interface
  lo                   Loopback network interface
disk:
  /dev/mmcblk0         Disk
  /dev/mmcblk0boot0    Disk
  /dev/mmcblk0rpmb     Disk
  /dev/mmcblk0boot1    Disk
partition:
  /dev/mmcblk0p1       Partition
  /dev/mmcblk0p2       Partition
  /dev/mmcblk0p3       Partition
  /dev/mmcblk0p4       Partition
usb controller:
                       ARM USB XHCI controller
                       ARM USB controller
                       ARM USB controller
bridge:
                       Synopsys PCI bridge
                       Synopsys PCI bridge
hub:
                       Linux Foundation 2.0 root hub
                       Linux Foundation 3.0 root hub
memory:
                       Main Memory
unknown:
                       System peripheral
                       Virtio Unclassified device
```

#### Conclusie

Het is uit deze lijst niet af te leiden, maar uit verder onderzoek is gebleken dat de TPU de `System peripheral` is die hier vanonder opgelijst staat.

### inxi

*Uit [[18]](bronnen.md#mendel-linux) en [[19]](bronnen.md#mendel-linux)*

#### Installatie

Instaleer inxi en mesa-utils voor glxinfo.

```bash
sudo apt-get install inxi
sudo apt-get install mesa-utils
```

#### Uitvoering

```bash
mendel@wishful-dog:~$ sudo inxi -Fx
System:    Host: wishful-dog Kernel: 4.14.98-imx aarch64 bits: 64 com
piler: gcc v: 8.3.0 Console: tty 0
           Distro: Mendel GNU/Linux 5 (Eagle)
Machine:   Type: ARM Device System: Freescale i.MX8MQ Phanbell details: N/A
serial: 1c1779d6f32efb39
CPU:       Topology: Quad Core model: N/A variant: cortex-a53 bits: 64 type: MCP arch: ARMv8 rev: 4
           features: Use -f option to see features bogomips: 0
           Speed: 500 MHz min/max: 500/1500 MHz Core speeds (MHz): 1: 500 2: 500 3: 500 4: 500
Graphics:  Device-1: imx8mq-hdmi driver: i.mx8_hdp v: N/A bus ID: N/A
           Device-2: imx8mq-gpu driver: galcore v: kernel bus ID: N/A
           Device-3: imx-display-subsystem driver: imx_drm v: N/A bus ID: N/
A
           Display: server: No display server data found. Headless machine? tty: 120x30

           Message: Advanced graphics data unavailable in console for root.
Audio:     Device-1: imx8mq-hdmi driver: i.mx8_hdp bus ID: N/A
           Device-2: simple-audio-card driver: asoc_simple_card bus ID: N/A
           Device-3: edgetpu-audio-card driver: edgetpu_audio_card bus ID: N/A
           Sound Server: ALSA v: k4.14.98-imx
Network:   Device-1: Qualcomm Atheros QCA6174 802.11ac Wireless Network Adapter driver: hif_pci v: N/A
           bus ID: 0000:01:00.0
           IF: p2p0 state: down mac: 00:0a:f5:89:89:81
           Device-2: imx8mq-fec driver: fec v: N/A port: N/A bus ID: N/A
           IF-ID-1: eth0 state: up speed: 100 Mbps duplex: full mac: 7c:d9:5c:b1:e3:72
           IF-ID-2: usb0 state: down mac: 02:22:78:0d:f6:df
           IF-ID-3: usb1 state: down mac: 02:22:78:0d:f6:de
           IF-ID-4: wlan0 state: down mac: 7c:d9:5c:b1:e3:73
Drives:    Local Storage: total: 7.28 GiB used: 1.71 GiB (23.5%)
           ID-1: /dev/mmcblk0 model: 8GTF4R size: 7.28 GiB
Partition: ID-1: / size: 4.99 GiB used: 1.68 GiB (33.7%) fs: ext4 dev: /dev/mmcblk0p4
           ID-2: /boot size: 124.0 MiB used: 29.8 MiB (24.0%) fs: ext2 4mdev: /dev/mmcblk0p1
           ID-3: /home size: 1.91 GiB used: 6.6 MiB (0.3%) fs: ext4 d ev: /dev/mmcblk0p3
Sensors:   System Temperatures: cpu: N/A mobo: N/A
           Fan Speeds (RPM): cpu: 0
Info:      Processes: 130 Uptime: 24m Memory: 986.0 MiB used: 213 .1 MiB (21.6%) Init: systemd runlevel: 5 Compilers:
           gcc: 8.3.0 Shell: editorConfig v: 5.0.3 inxi: 3.0.32
```

#### Conclusie

Edgetpu duikt enkel op in Audio.

### mount

Met `mount` kan je zien welke hardware er allemaal gemount is in het systeem.

#### Uitvoering

```bash
mendel@wishful-dog:~$ mount | column -t
/dev/mmcblk0p4  on  /                           type  ext4       (rw,noatime,data=ordered)
devtmpfs        on  /dev                        type  devtmpfs   (rw,relatime,size=340336k,nr_inodes=85084,mode=755)
sysfs           on  /sys                        type  sysfs      (rw,nosuid,nodev,noexec,relatime)
proc            on  /proc                       type  proc       (rw,nosuid,nodev,noexec,relatime)
tmpfs           on  /dev/shm                    type  tmpfs      (rw,nosuid,nodev)
devpts          on  /dev/pts                    type  devpts     (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)

tmpfs           on  /run                        type  tmpfs      (rw,nosuid,nodev,mode=755)
tmpfs           on  /run/lock                   type  tmpfs      (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs           on  /sys/fs/cgroup              type  tmpfs      (ro,nosuid,nodev,noexec,mode=755)
cgroup2         on  /sys/fs/cgroup/unified      type  cgroup2    (rw,nosuid,nodev,noexec,relatime,nsdelegate)
cgroup          on  /sys/fs/cgroup/systemd      type  cgroup     (rw,nosuid,nodev,noexec,relatime,xattr,name=systemd)
pstore          on  /sys/fs/pstore              type  pstore     (rw,nosuid,nodev,noexec,relatime)
cgroup          on  /sys/fs/cgroup/devices      type  cgroup     (rw,nosuid,nodev,noexec,relatime,devices)
cgroup          on  /sys/fs/cgroup/net_prio     type  cgroup     (rw,nosuid,nodev,noexec,relatime,net_prio)
cgroup          on  /sys/fs/cgroup/hugetlb      type  cgroup     (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup          on  /sys/fs/cgroup/pids         type  cgroup     (rw,nosuid,nodev,noexec,relatime,pids)
cgroup          on  /sys/fs/cgroup/cpu,cpuacct  type  cgroup     (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup          on  /sys/fs/cgroup/freezer      type  cgroup     (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup          on  /sys/fs/cgroup/memory       type  cgroup     (rw,nosuid,nodev,noexec,relatime,memory)
cgroup          on  /sys/fs/cgroup/cpuset       type  cgroup     (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup          on  /sys/fs/cgroup/perf_event   type  cgroup     (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup          on  /sys/fs/cgroup/blkio        type  cgroup     (rw,nosuid,nodev,noexec,relatime,blkio)
debugfs         on  /sys/kernel/debug           type  debugfs    (rw,relatime)
mqueue          on  /dev/mqueue                 type  mqueue     (rw,relatime)
hugetlbfs       on  /dev/hugepages              type  hugetlbfs  (rw,relatime,pagesize=2M)
configfs        on  /sys/kernel/config          type  configfs   (rw,relatime)
tmpfs           on  /var/log                    type  tmpfs      (rw,relatime)
/dev/mmcblk0p3  on  /home                       type  ext4       (rw,nosuid,nodev,noatime,data=ordered)
/dev/mmcblk0p1  on  /boot                       type  ext2       (rw,noatime,errors=continue)
tmpfs           on  /run/user/1000              type  tmpfs      (rw,nosuid,nodev,relatime,size=100960k,mode=700,uid=100
0,gid=1000)
```

### Conclusie

PCI peripherals worden niet gemount en worden hier dus niet opgelijst.

### dmesg

Deze lijst heel veel informatie op. Te veel om hier helemaal op te lijsten.
Er is geweten dat de Edge TPU een PCI peripheral is, dus in onderstaande output is enkel informatie over PCI behouden.

```bash
mendel@wishful-dog:/$ dmesg
...
[    0.000000]     PCI I/O : 0xffff7dfffee00000 - 0xffff7dffffe00000   (    16 MB)

...

[    3.989554] imx6q-pcie 33800000.pcie: 33800000.pcie supply epdev_on not found, using dummy regulator
[    4.000679] OF: PCI: host bridge /pcie@0x33800000 ranges:
[    4.006149] OF: PCI:   No bus range found for /pcie@0x33800000, using [bus 00-ff]
[    4.013654] OF: PCI:    IO 0x1ff80000..0x1ff8ffff -> 0x00000000
[    4.019643] OF: PCI:   MEM 0x18000000..0x1fefffff -> 0x18000000
[    4.030420] imx6q-pcie 33800000.pcie: pcie phy pll is locked.
[    4.089626] imx6q-pcie 33800000.pcie: Link: Gen2 disabled
[    4.095038] imx6q-pcie 33800000.pcie: Link up, Gen1
[    4.102814] imx6q-pcie 33800000.pcie: PCI host bridge to bus 0000:00
[    4.109235] pci_bus 0000:00: root bus resource [bus 00-ff]
[    4.114888] pci_bus 0000:00: root bus resource [io  0x0000-0xffff]
[    4.121121] pci_bus 0000:00: root bus resource [mem 0x18000000-0x1fefffff]
[    4.128131] pci 0000:00:00.0: [16c3:abcd] type 01 class 0x060400
[    4.128166] pci 0000:00:00.0: reg 0x10: [mem 0x00000000-0x000fffff 64bit]
[    4.128182] pci 0000:00:00.0: reg 0x38: [mem 0x00000000-0x0000ffff pref]
[    4.128237] pci 0000:00:00.0: supports D1
[    4.128247] pci 0000:00:00.0: PME# supported from D0 D1 D3hot D3cold
[    4.128844] pci 0000:01:00.0: [168c:003e] type 00 class 0x028000
[    4.129007] pci 0000:01:00.0: reg 0x10: [mem 0x00000000-0x001fffff 64bit]
[    4.129536] pci 0000:01:00.0: PME# supported from D0 D3hot D3cold
[    4.141165] pci 0000:00:00.0: BAR 14: assigned [mem 0x18000000-0x181fffff]
[    4.148179] pci 0000:00:00.0: BAR 0: assigned [mem 0x18200000-0x182fffff 64bit]
[    4.155518] pci 0000:00:00.0: BAR 6: assigned [mem 0x18300000-0x1830ffff pref]
[    4.162827] pci 0000:01:00.0: BAR 0: assigned [mem 0x18000000-0x181fffff 64bit]
[    4.170199] pci 0000:00:00.0: PCI bridge to [bus 01-ff]
[    4.175501] pci 0000:00:00.0:   bridge window [mem 0x18000000-0x181fffff]
[    4.182321] pci 0000:00:00.0: Max Payload Size set to  128/ 128 (was  128), Max Read Rq  128
[    4.190894] pci 0000:01:00.0: Max Payload Size set to  128/ 256 (was  128), Max Read Rq  128
[    4.199847] pcieport 0000:00:00.0: Signaling PME with IRQ 234
[    4.205768] pcieport 0000:00:00.0: AER enabled with IRQ 234
[    4.212393] imx6q-pcie 33c00000.pcie: 33c00000.pcie supply epdev_on not found, using dummy regulator
[    4.224344] OF: PCI: host bridge /pcie@0x33c00000 ranges:
[    4.229838] OF: PCI:   No bus range found for /pcie@0x33c00000, using [bus 00-ff]
[    4.237466] OF: PCI:    IO 0x27f80000..0x27f8ffff -> 0x00000000
[    4.243472] OF: PCI:   MEM 0x20000000..0x27efffff -> 0x20000000
[    4.254219] imx6q-pcie 33c00000.pcie: pcie phy pll is locked.
[    4.314584] imx6q-pcie 33c00000.pcie: Link up, Gen2
[    4.321563] imx6q-pcie 33c00000.pcie: PCI host bridge to bus 0001:00
[    4.327950] pci_bus 0001:00: root bus resource [bus 00-ff]
[    4.333516] pci_bus 0001:00: root bus resource [io  0x10000-0x1ffff] (bus address [0x0000-0xffff])
[    4.342501] pci_bus 0001:00: root bus resource [mem 0x20000000-0x27efffff]
[    4.349472] pci 0001:00:00.0: [16c3:abcd] type 01 class 0x060400
[    4.349493] pci 0001:00:00.0: reg 0x10: [mem 0x00000000-0x000fffff 64bit]
[    4.349502] pci 0001:00:00.0: reg 0x38: [mem 0x00000000-0x0000ffff pref]
[    4.349536] pci 0001:00:00.0: supports D1
[    4.349542] pci 0001:00:00.0: PME# supported from D0 D1 D3hot D3cold
[    4.349855] pci 0001:01:00.0: [1ac1:089a] type 00 class 0x0000ff
[    4.349970] pci 0001:01:00.0: reg 0x10: [mem 0x00000000-0x00003fff 64bit pref]
[    4.350017] pci 0001:01:00.0: reg 0x18: [mem 0x00000000-0x000fffff 64bit pref]
[    4.361181] pci 0001:00:00.0: BAR 0: assigned [mem 0x20000000-0x200fffff 64bit]
[    4.368566] pci 0001:00:00.0: BAR 15: assigned [mem 0x20100000-0x202fffff pref]
[    4.375891] pci 0001:00:00.0: BAR 6: assigned [mem 0x20300000-0x2030ffff pref]
[    4.383186] pci 0001:00:00.0: PCI bridge to [bus 01-ff]
[    4.388427] pci 0001:00:00.0:   bridge window [mem 0x20100000-0x202fffff pref]
[    4.395723] pci 0001:00:00.0: Max Payload Size set to  128/ 128 (was  128), Max Read Rq  128
[    4.404218] pci 0001:01:00.0: Max Payload Size set to  128/ 256 (was  128), Max Read Rq  128
[    4.413153] pcieport 0001:00:00.0: Signaling PME with IRQ 363
[    4.419101] pcieport 0001:00:00.0: AER enabled with IRQ 363
[    4.428956] asoc-simple-card sound-header: snd-soc-dummy-dai <-> 30010000.sai mapping ok
[    4.437100] asoc-simple-card sound-header: ASoC: no DMI vendor name!
[    4.455054] snvs_rtc 30370000.snvs:snvs-rtc-lp: setting system clock to 1970-01-01 00:00:01 UTC (1)
[    4.464843] VSD_3V3: disabling
[    4.468435] buck3: disabling
[    4.478219] ALSA device list:
[    4.481218]   #0: edgetpu-audio-card
[    4.484799]   #1: 40-pin Header

...

[    5.821141] apex: module is from the staging directory, the quality is unknown, you have been warned.
[    5.838022] apex 0001:01:00.0: can't enable device: BAR 0 [mem 0x00000000-0x00003fff 64bit pref] not claimed
[    5.839938] wlan: loading driver v4.5.23.1
[    5.848044] apex 0001:01:00.0: BAR 2: assigned [mem 0x20100000-0x201fffff 64bit pref]
[    5.853743] hif_pci_probe:, con_mode= 0x0
[    5.861524] apex 0001:01:00.0: BAR 0: assigned [mem 0x20200000-0x20203fff 64bit pref]
[    5.865088] PCI device id is 003e :003e
[    5.872997] apex 0001:01:00.0: enabling device (0000 -> 0002)
[    5.875847] hif_pci 0000:01:00.0: BAR 0: assigned [mem 0x18000000-0x181fffff 64bit]
[    5.883332] Can't support > 32 bit dma.
[    5.889597] hif_pci 0000:01:00.0: enabling device (0000 -> 0002)
[    5.894449] Can't support > 32 bit dma.
[    5.902962]
                hif_pci_configure : num_desired MSI set to 1
[    6.130477] hif_pci_probe: ramdump base 0xffff800024200000 size 2095136
[    6.145863] NUM_DEV=1 FWMODE=0x2 FWSUBMODE=0x0 FWBR_BUF 0
```

#### Conclusie

Weer wordt de `edgetpu-audio-card` wel geïdentificeerd, maar de TPU zelf niet. Als geweten is dat de driver hiervoor `apex` kan deze echter wel geïdentificeerd worden als `PCI device id is 003e :003e` in het onderste gedeelte.

### lspci

Hiermee kunnen we specifiek PCI informatie oplijsten [[20]](bronnen.md#mendel-linux).

#### Uitvoering

```bash
mendel@wishful-dog:/$ lspci
0000:00:00.0 PCI bridge: Synopsys, Inc. DWC_usb3 (rev 01)
0000:01:00.0 Network controller: Qualcomm Atheros QCA6174 802.11ac Wireless Network Adapter (rev 32)
0001:00:00.0 PCI bridge: Synopsys, Inc. DWC_usb3 (rev 01)
0001:01:00.0 System peripheral: Device 1ac1:089a
```

#### Conclusie

Deze `0001:01:00.0 System peripheral: Device 1ac1:089a` is de TPU. Het adres komt voor in de *M.2 or Mini PCIe Accelerator* documentatie van Coral zelf [[21]](bronnen.md#mendel-linux). Hieruit kan dus afgeleid worden dat de Coral Dev Board dezelfde Edge TPU hardware bevat als aanwezig op deze *accelerators*.

> "The `03` (hier 0001:01:00.0) number and `System peripheral` name might be different, because those are host-system specific, but as long as you see a device listed with `089a` then you're okay to proceed."

TPU's worden in de Coral code, zoals gezien in de vorige sectie, gedetecteerd via USB of PCI, dus dit is uiteraard de plaats waar ze te vinden zijn.

> Each PCI peripheral is identified by a *bus* number, a *device* number, and a *function* number [[22]](bronnen.md#mendel-linux).

Een meer gericht commando om de TPU te vinden uit deze documentatie is `lspci -nn | grep 089a`

```bash
mendel@wishful-dog:/$ lspci -nn | grep 089a
0001:01:00.0 System peripheral [0880]: Device [1ac1:089a]
```

Om niet herkende apparaten mogelijks toch te kunnen identificeren kan de lokale pci-id definitions upgedate worden met `sudo update-pciids`. Na dit te doen wordt de TPU wel herkend.

```bash
mendel@wishful-dog:/$ lspci -nn | grep 089a
0001:01:00.0 System peripheral [0880]: Global Unichip Corp. Coral Edge TPU [1ac1:089a]
```

Uit deze documentatie kan nu afgeleid worden wat nodig is om een TPU toe te voegen aan een Linux systeem, en dus hoe Debian moet worden aangepast om met een TPU via PCI te werken.

### PCI in het Linux systeem

#### /proc/bus/pci/devices

De Coral PCIe driver voor de TPU noemt "Apex". Dit wetende kunnen we de TPU ook herkennen in `/proc/bus/pci/devices`.

```bash
mendel@wishful-dog:/$ cat /proc/bus/pci/devices
0000 16c3abcd ea 18200004 0 0 0 0 18300000 100000 0 0 0 0 0 10000 pcieport
0100 168c003e eb 18000004 0 0 0 0 0 0 200000 0 0 0 0 0 0 hif_pci
0000 16c3abcd 16b 20000004 0 0 0 0 0 20300000 100000 0 0 0 0 0 10000 pcieport
0100 1ac1089a 1eb 2020000c 0 2010000c 0 0 0 0 4000 0 100000 0 0 0 0 apex
```

De laatste genaamd `apex` is hier dus de TPU.

#### /sys/bus/pci

*Uit [[23]](bronnen.md#mendel-linux)*

De TPU kan nu ook worden teruggevonden in `/sys/bus/pci/devices/0001:01:00.0`. Dit zou een mogelijkse weg kunnen zijn om de TPU te detecteren.

```bash
mendel@wishful-dog:/sys/bus/pci/devices/0001:01:00.0$ ls -l
total 0
drwxr-xr-x 3 root root       0 Feb 14  2019 apex
-rw-r--r-- 1 root root    4096 Nov  8 17:04 broken_parity_status
-r--r--r-- 1 root root    4096 Nov  8 16:14 class
-rw-r--r-- 1 root root    4096 Nov  8 16:14 config
-r--r--r-- 1 root root    4096 Nov  8 17:04 consistent_dma_mask_bits
-r--r--r-- 1 root root    4096 Nov  8 15:19 current_link_speed
-r--r--r-- 1 root root    4096 Nov  8 15:19 current_link_width
-rw-r--r-- 1 root root    4096 Nov  8 17:04 d3cold_allowed
-r--r--r-- 1 root root    4096 Nov  8 16:14 device
-r--r--r-- 1 root root    4096 Nov  8 17:04 devspec
-r--r--r-- 1 root root    4096 Nov  8 17:04 dma_mask_bits
lrwxrwxrwx 1 root root       0 Nov  8 17:04 driver -> ../../../../../../bus/pci/drivers/apex
-rw-r--r-- 1 root root    4096 Nov  8 17:04 driver_override
-rw-r--r-- 1 root root    4096 Nov  8 17:04 enable
-r--r--r-- 1 root root    4096 Nov  8 16:14 irq
-r--r--r-- 1 root root    4096 Nov  8 17:04 local_cpulist
-r--r--r-- 1 root root    4096 Nov  8 17:04 local_cpus
-r--r--r-- 1 root root    4096 Nov  8 17:04 max_link_speed
-r--r--r-- 1 root root    4096 Nov  8 17:04 max_link_width
-r--r--r-- 1 root root    4096 Nov  8 17:04 modalias
-rw-r--r-- 1 root root    4096 Nov  8 17:04 msi_bus
drwxr-xr-x 2 root root       0 Nov  8 17:04 msi_irqs
-rw-r--r-- 1 root root    4096 Nov  8 17:04 numa_node
drwxr-xr-x 2 root root       0 Nov  8 17:04 power
--w--w---- 1 root root    4096 Nov  8 17:04 remove
--w--w---- 1 root root    4096 Nov  8 17:04 rescan
--w------- 1 root root    4096 Nov  8 17:04 reset
-r--r--r-- 1 root root    4096 Nov  8 16:14 resource
-rw------- 1 root root   16384 Nov  8 17:04 resource0
-rw------- 1 root root 1048576 Nov  8 17:04 resource2
-r--r--r-- 1 root root    4096 Nov  8 15:19 revision
lrwxrwxrwx 1 root root       0 Nov  8 17:04 subsystem -> ../../../../../../bus/pci
-r--r--r-- 1 root root    4096 Nov  8 15:19 subsystem_device
-r--r--r-- 1 root root    4096 Nov  8 15:19 subsystem_vendor
-rw-r--r-- 1 root root    4096 Nov  8 17:04 uevent
-r--r--r-- 1 root root    4096 Feb 14  2019 vendor
```
