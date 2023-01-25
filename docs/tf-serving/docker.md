# Docker

De aangeraden manier om TensorFlow Serving te gebruiken is binnen een Docker container. Dit is ook de enige goed ondersteunde manier op het moment van schrijven. De software is ook nog volop in ontwikkeling met vele feautures aangeduid als experimenteel.

## Docker Engine installeren van binaries

Er bestaat geen klare versie van Docker om te installeren op het Coral Dev Board met Mendel. Er moet dus een installatie gebeuren van de binaries van Docker voor ARM.

*Uit [[6]](bronnen.md#tensorflow-serving) en [[7]](bronnen.md#tensorflow-serving)*

Download the binaries:

```bash
wget https://download.docker.com/linux/static/stable/armhf/docker-18.09.6.tgz
```

Extract downloaded archive:

```bash
tar xzvf docker-18.09.6.tgz
```

**Optional**: Move the binaries to a directory on your executable path, such as `/usr/bin/`.

```bash
sudo cp docker/* /usr/bin/
```

Docker deamon opstarten:

```bash
sudo dockerd &
```

Testen:

```bash
sudo docker run hello-world
```

### Manage Docker as a non-root user

*Uit [[8]](bronnen.md#tensorflow-serving)*

Docker group aanmaken en de huidige user toevoegen:

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Vervolgens uitloggen en terug in loggen om te testen:

````bash
docker run hello-world
````

## TensorFlow Serving met Docker

*Uit [[9]](bronnen.md#tensorflow-serving)*

Download the TensorFlow Serving Docker image and repo:

````bash
docker pull tensorflow/serving
````

Demo downloaden om te testen:

````bash
git clone https://github.com/tensorflow/serving
````

Location of demo models:

````bash
TESTDATA="$(pwd)/serving/tensorflow_serving/servables/tensorflow/testdata"
````

Start TensorFlow Serving container and open the REST API port:

````bash
docker run -t --rm -p -d 8501:8501 \
    -v "$TESTDATA/saved_model_half_plus_two_cpu:/models/half_plus_two" \
    -e MODEL_NAME=half_plus_two \
    tensorflow/serving &
````

Query the model using the predict API:

````bash
curl -d '{"instances": [1.0, 2.0, 5.0]}' \
    -X POST http://localhost:8501/v1/models/half_plus_two:predict
````

Returns: `{ "predictions": [2.5, 3.0, 4.5] }`

Dit geeft echter errors:

````bash
INFO[2022-12-20T16:47:58.071287864Z] shim containerd-shim started                  address="/containerd-shim/moby/73c2f276b684b32333fcd3f46733a8aaab7e0c807ccc275841d65250fc0d5912/shim.sock" debug=false pid=4268
ERRO[2022-12-20T16:47:58.828490295Z] Handler for POST /v1.39/containers/73c2f276b684b32333fcd3f46733a8aaab7e0c807ccc275841d65250fc0d5912/resize returned error: bad file descriptor: unknown
ERRO[2022-12-20T16:47:58.845288015Z] Handler for POST /v1.39/containers/73c2f276b684b32333fcd3f46733a8aaab7e0c807ccc275841d65250fc0d5912/resize returned error: bad file descriptor: unknown
ERRO[2022-12-20T16:47:58.865144896Z] Handler for POST /v1.39/containers/73c2f276b684b32333fcd3f46733a8aaab7e0c807ccc275841d65250fc0d5912/resize returned error: bad file descriptor: unknown
INFO[2022-12-20T16:47:58.920467178Z] shim reaped                                   id=73c2f276b684b32333fcd3f46733a8aaab7e0c807ccc275841d65250fc0d5912
INFO[2022-12-20T16:47:58.931605699Z] ignoring event                                module=libcontainerd namespace=moby topic=/tasks/delete type="*events.TaskDelete"
WARN[2022-12-20T16:47:59.003208262Z] Failed to delete conntrack state for 172.17.0.2: invalid argument
````
