# opsmanager
ops manager

```` get_host.sh

#! /bin/bash
export PUB_KEY=""
export PRIVATE_KEY=""
export OM_GROUP=""
export OM_AUTH=${PUB_KEY}:${PRIVATE_KEY}
## OpsManager url
export OM_URI="http://opsmanager_url_or_ipaddr:8080/api/public/v1.0/groups" export API_URI="${OM_URI}/${OM_GROUP}/hosts?pretty=true"
curl --user ${OM_AUTH} --digest \ --header "Content-Type: application/json" \ --request GET ${API_URI}


````


```` bash
sh get_hosts.sh | jq -r '.results[] | { "hostname": .hostname, "id": .id, "replicaSetName": .replicaSetName, "typeName": .typeName} ' > topology.json
````

get_metrics.sh

```` get_metrics.sh
#! /bin/bash
GET_DATE=`date "+%Y%m%d%H%M%S"`
export PUB_KEY=""
export PRIVATE_KEY=""
export OM_GROUP=""
export OM_AUTH=${PUB_KEY}:${PRIVATE_KEY} 
export OM_RESOURCE=$1
export GRANULARITY="PT10S" 
export PERIOD="PT8H"
export OM_URI="http://opsmanager_url_or_ipaddr:8080/api/public/v1.0/groups" 
export API_URI="${OM_URI}/${OM_GROUP}/hosts/${OM_RESOURCE}/measurements?gr anularity=${GRANULARITY}&period=${PERIOD}&pretty=true"
curl --user ${OM_AUTH} --digest \
--header "Content-Type: application/json" \
--request GET ${API_URI} > status_${OM_RESOURCE}_${GRANULARITY}_${PERIOD}_${GET_DATE}.json
````


get_disks.sh
````
#! /bin/bash
export PUB_KEY=""
export PRIVATE_KEY="" 
export OM_GROUP=""
export OM_AUTH=${PUB_KEY}:${PRIVATE_KEY} 
export OM_RESOURCE=$1
export OM_URI="http://opsmanager_url_or_ipaddr:8080/api/public/v1.0/groups" 
export API_URI="${OM_URI}/${OM_GROUP}/hosts/${OM_RESOURCE}/disks"
curl --user ${OM_AUTH} --digest \
--header "Content-Type: application/json" \ 
--request GET ${API_URI}
````


```` get_all.sh
#! /bin/bash
for cur in `cat topology.json | jq -r '.id'`
do
sh get_disks.sh $cur
done
````


````
#! /bin/bash
GET_DATE=`date "+%Y%m%d%H%M%S"`
export PUB_KEY=""
export PRIVATE_KEY="" 
export OM_GROUP=""
export OM_AUTH=${PUB_KEY}:${PRIVATE_KEY}
export OM_PARTITION="xvdb" 
export OM_RESOURCE=$1
export GRANULARITY="PT10S" 
export PERIOD="PT8H"
export OM_URI="http://opsmanager_url_or_ipaddr:8080/api/public/v1.0/groups" 
export API_URI="${OM_URI}/${OM_GROUP}/hosts/${OM_RESOURCE}/disks/${OM_PARTI TION}/measurements?granularity=${GRANULARITY}&period=${PERIOD}&pretty=t rue"
curl --user ${OM_AUTH} --digest \
--header "Content-Type: application/json" \
--request GET ${API_URI} > status_partition_${OM_RESOURCE}_${OM_PARTITION}_${GRANULARITY}_${PERI OD}_${GET_DATE}.json
````


````
#! /bin/bash
for cur in `cat topology.json | jq -r '.id'`
do
sh get_metrics.sh $cur
sh get_partition.sh $cur 
done
````