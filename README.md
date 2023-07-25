## Starting the dockerized testbed

Navigate to the directory containing the docker-compose.yaml file and use the command:

`sudo docker-compose up -d`

## Registering the UE

Before starting the UERANSIM UE simulation, we need to register our subscriber to the 5G core. To do this we can log in to the Open5Gs webui at localhost:3000/ with username `admin` and password `1423`. Alterinativelly, we can use the CLI tool to register the users directly from the mongodb container.

The following `open5gs-dbctl.sh` script can be executed inside the mongodb container, to add a subscriber without having access to the open5gs webui:

Gain shell access to the mongodb container:

`sudo docker exec -it mongodb bash`

Navigate to the scripts directory:

`cd scripts/`

Execute the script with the following arguments: imsi, key, opc

`./open5gs-dbctl.sh add <imsi> <key> <opc>`

In our case, it should be:

`./open5gs-dbctl.sh add 208930000000001 0C0A34601D4F07677303652C0462535B 63bfa50ee6523365ff14c1f45f88737d`

the output of this command should be something like:

```
root@43ac84dc385c:/# ./open5gs-dbctl.sh add 208930000000001 0C0A34601D4F07677303652C0462535B 63bfa50ee6523365ff14c1f45f88737d
MongoDB shell version v5.0.8
connecting to: mongodb://localhost:27017/open5gs?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("2a85f2de-61d4-4542-9aa3-326ab0eec925") }
MongoDB server version: 5.0.8
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("62711caaf253cb8eba938434")
})
```

For the `open5gs-dbctl.sh` code, check [here](https://github.com/open5gs/open5gs/blob/main/misc/db/open5gs-dbctl).

After registering the UE with the command above, or on the Open5GS webui, create a new UE instance:

`sudo docker exec -it ueransim-ue-0 bash`

`./nr-ue -c ./oai-ue.yaml`

You don't need to do the same for the gNB, as it is already done in the docker-compose.yaml file.

## Wireshark filter

To capture all 5G core traffic, use the following filter:

`ip.src == 172.21.0.111 || ip.dst == 172.21.0.111 || ip.src == 172.21.0.101 || ip.dst == 172.21.0.101 || ip.src == 172.21.0.103 || ip.dst == 172.21.0.103 || ip.src == 172.21.0.100 || ip.dst == 172.21.0.100 || ip.src == 172.21.0.104 || ip.dst == 172.21.0.104 || ip.src == 172.21.0.105 || ip.dst == 172.21.0.105 || ip.src == 172.21.0.114 || ip.dst == 172.21.0.114 || ip.src == 172.21.0.106 || ip.dst == 172.21.0.106 || ip.src == 172.21.0.107 || ip.dst == 172.21.0.107 || ip.src == 172.21.0.120 || ip.dst == 172.21.0.120 || ip.src == 172.21.0.108 || ip.dst == 172.21.0.108 || ip.src == 172.21.0.109 || ip.dst == 172.21.0.109 || ip.src == 172.21.0.110 || ip.dst == 172.21.0.110 || ip.src == 172.21.0.112 || ip.dst == 10.45.0.2`
