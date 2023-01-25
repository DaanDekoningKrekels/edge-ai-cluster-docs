# Master script

Op de master is een bash script aanwezig dat het mogelijk maakt om vanaf de master de status van ieder node te bekijken, ieder node te remounten en uit te schakelen.

```bash
~/node-statuses.sh
```

Bij het uitvoeren van bovenstaand commando op de master, zal er gecontroleerd worden of de SSH-poort (22) van iedere node beschikbaar is. 
- Als deze beschikbaar is wordt de temperatuur van de node weergegeven en de status van de NFS-mount.
- Als deze niet beschikbaar is zal er een foutmelding getoond worden.

Bij ieder SSH-commando is het `ConnectTimeout=5` argument meegegeven om ervoor te zorgen dat het script niet blijft vasthangen bij een onbereikbare node.

```bash
~/node-statuses.sh remount
```

Bij het uitvoeren van bovenstaand commando, zal op ieder node waarvan SSH-poort 22 beschikbaar is het volgende commando automatisch worden uitgevoerd: `sudo systemctl restart mnt-share.mount`. Dit commando is handig als de master na de nodes is opgestart waardoor de nodes niet konden verbinden met de NFS-map.

```bash
~/node-statuses.sh poweroff
```

Bij het uitvoeren van bovenstaand commando, zal op ieder node waarvan SSH-poort 22 beschikbaar is het commando `sudo poweroff` worden uitgevoerd. Dat is handig als je de hele cluster wil uitschakelen zonder dit node-per-node te doen.

Na het uitvoeren van `remount` en `poweroff` zal het script alsnog controleren of de nodes beschikbaar zijn en eventueel de temperatuur en mount-status weergeven.

Voor de temperatuuruitlezing is de Coral documentatie [(Manage the PCIe module temperature, z.d.)](bronnen.md#master-beheerder-script) gebruikt als referentie.

## Bash script

```bash
#!/bin/bash

# Controlleert of poort 22 van de node beschikbaar is en
# verbindt via SSH met alle nodes om de temperatuur en status van NFS op te vragen.

NODES=( '192.168.88.12' '192.168.88.13' '192.168.88.14' '192.168.88.15' )


OPTION=$1

case "$OPTION" in
        "remount")
                echo "***ALLE NODES OPNIEUW MOUNTEN***"
                for NODE in "${NODES[@]}"; do
		        echo "**$NODE**"
		        if nc -z $NODE 22 2>/dev/null; then
		                # Restart commando uitvoeren
        		        ssh -o ConnectTimeout=5 -t mendel@"$NODE" "sudo systemctl restart mnt-share.mount"
        		else
        		        echo "$NODE niet beschikbaar"
       			fi
			echo ""
		done
		;;
        "poweroff")
                echo "***ALLE NODES OPNIEUW UITSCHAKELEN***"
                for NODE in "${NODES[@]}"; do
                        echo "**$NODE**"
                        if nc -z $NODE 22 2>/dev/null; then
                                # Restart commando uitvoeren
                                ssh -o ConnectTimeout=5 -t mendel@"$NODE" "sudo poweroff"
                        else
                                echo "$NODE niet beschikbaar"
                        fi
                        echo ""
                done
                ;;
        "?")
                echo "- Gebruik de parameter 'remount' om alle nfs shares te remounten."
                echo "- Gebruik te parameter 'poweroff' om alle nodes uit te schakelen."
		exit
		;;
        ":")
                echo "No argument value for option $OPTARG"
                ;;
        *)
                # Should not occur
                #echo "Unknown error while processing options"
                ;;
esac

for NODE in "${NODES[@]}"; do
	echo "**$NODE**"
	if nc -z $NODE 22 2>/dev/null; then
		echo $(expr $(ssh -o ConnectTimeout=5 mendel@"$NODE" "cat /sys/class/apex/apex_0/temp") / 1000) "°C"
		# Timeout van 5 seconden, moest node niet beschikbar zijn
		# -t zodat de kleuren mee worden weergegeven
		ssh -o ConnectTimeout=5 -t mendel@"$NODE" "sudo systemctl status mnt-share.mount"
	else
		echo "$NODE niet beschikbaar"
	fi
	echo ""
done
```

## Voorbeeld output


<pre><font color="#8AE234"><b>mendel@purple-mole</b></font>:<font color="#729FCF"><b>~</b></font>$ ./node-statuses.sh 
192.168.88.12
55 °C
<font color="#8AE234"><b>●</b></font> mnt-share.mount - NFS share van de Master node
   Loaded: loaded (/etc/systemd/system/mnt-share.mount; enabled; vendor preset: enabled)
   Active: <font color="#8AE234"><b>active (mounted)</b></font> since Mon 2022-11-14 16:34:13 UTC; 55min ago
    Where: /mnt/share
     What: 192.168.88.11:/home/mendel/share
    Tasks: 0 (limit: 797)
   Memory: 304.0K
   CGroup: /system.slice/mnt-share.mount
Nov 14 16:34:13 wishful-dog systemd[1]: Mounting NFS share van de Master node...
Nov 14 16:34:13 wishful-dog systemd[1]: Mounted NFS share van de Master node.
Connection to 192.168.88.12 closed.
192.168.88.13
55 °C
<font color="#8AE234"><b>●</b></font> mnt-share.mount - NFS share van de Master node
   Loaded: loaded (/etc/systemd/system/mnt-share.mount; enabled; vendor preset: enabled)
   Active: <font color="#8AE234"><b>active (mounted)</b></font> since Mon 2022-11-14 15:57:49 UTC; 1h 32min ago
    Where: /mnt/share
     What: 192.168.88.11:/home/mendel/share
    Tasks: 0 (limit: 797)
   Memory: 340.0K
   CGroup: /system.slice/mnt-share.mount
Nov 14 15:57:48 undefined-yarn systemd[1]: Mounting NFS share van de Master node...
Nov 14 15:57:49 undefined-yarn systemd[1]: Mounted NFS share van de Master node.
Connection to 192.168.88.13 closed.
192.168.88.14
53 °C
<font color="#8AE234"><b>●</b></font> mnt-share.mount - NFS share van de Master node
   Loaded: loaded (/etc/systemd/system/mnt-share.mount; enabled; vendor preset: enabled)
   Active: <font color="#8AE234"><b>active (mounted)</b></font> since Mon 2022-11-14 16:09:23 UTC; 1h 20min ago
    Where: /mnt/share
     What: 192.168.88.11:/home/mendel/share
    Tasks: 0 (limit: 797)
   Memory: 356.0K
   CGroup: /system.slice/mnt-share.mount
Nov 14 16:09:23 purple-kid systemd[1]: Mounting NFS share van de Master node...
Nov 14 16:09:23 purple-kid systemd[1]: Mounted NFS share van de Master node.
Connection to 192.168.88.14 closed.
192.168.88.15
192.168.88.15 niet beschikbaar
</pre>