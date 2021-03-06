## Google Cloud

#### Install specific version of Gcloud SDK.

```gcloud components update --version 217.0.0```

#### Cluster autoscalling in Kubernetes (urban myth)

```
If less than 5 nodes in cluster than you are in staging area of Google Cloud whatever the version. 
More than 5 nodes and you are more important and you are moved into production infrastructure which is faster. 
It's an automatic process but Control Plane will be offline for a few minutes during transition from less than 5 to over 5. 
If you have autoscailing from 5 to above then you will be constatnly moving from Staging to Production 
causing Control Plane to be offline each time it's moves.

in a previous version of the pricing page for GKE this was mentioned as a footnote as it was used to be the case that 1-5 nodes 
got a free control plane but 6+ was charged. However, the it's now free regardless the amount of nodes. 
But that did not change the parity between staging and production.
```

#### Kubernetes nodes /16 subnet

```
Apparently the /16 subnet is used because it's divided into /24 subnets for each node.
```

#### Run below to get all Firewall rules from All projects that allow traffic from ANYWHERE. Results will be send to open_access.csv file. If you don't have access to a particular project you will get an error.
```
for project in $(gcloud projects list --format="value(projectId)")
do
gcloud beta compute firewall-rules list --project=$project --format="csv($project,name,targetTags.list():label=TARGET_TAGS,sourceRanges.list():label=SRC_RANGES,allowed[].map().firewall_rule().list():label=ALLOW,network)" --filter="sourceRanges=('0.0.0.0/0')" >> open_access.csv
done
```

#### Run below to get all VMs from All projects. Results will be send to all_vms.csv file. If you don't have access to a particular project you will get an error.
```
for project in $(gcloud projects list --format="value(projectId)")
do
gcloud compute instances list --project=$project --format="csv($project,name,status)" >> all_vms.csv
done
```

#### Run below to get all IPs from all instances from All projects 2 versions

```
for project in $(gcloud projects list --format="value(projectId)")
do
gcloud compute instances list --format="value(name,networkInterfaces[0].networkIP)" --project=$project >> ip_list_all_vms.csv
done
```

```
#!/bin/bash 
gcloud projects list | awk '{print $1}' | tail -n +2 | while read line; do 
gcloud config set project $line; 
echo $line >> iplist; 
gcloud compute instances list --format="value(networkInterfaces[0].networkIP)" >> iplist.csv;
done 
```


#### Home Activity Filter in GCP Console/Home/Activity Page (Firewall rule example)

Using console

```
Under GCP Console/Home/Activity Page on the right select Resource type drop-down menu and select the ‘GCE Firewall Rule’. 
Applying this filter will show only firewall activity (created, edited, deleted rules)
```

Using Google Cloud Shell 

```
gcloud logging read
```

Above command prints out log entries from Stackdriver Logging, much like the Activity pane in web console.
If you run the command with no arguments, it'll print the same content that's in the "Activity" pane (from the last 24h). However, you can use Stackdriver Logging filters with this command to filter out what you want from the logs.

Examples

```
gcloud logging read "resource.type=gce_firewall_rule"
gcloud logging read "resource.type=gce_instance AND logName=projects/[PROJECT_ID]/logs/syslog AND textPayload:SyncAddress" --limit 10 --format json
gcloud logging resource-descriptors list --filter="type:instance"
```
```
https://cloud.google.com/sdk/gcloud/reference/logging/read
https://cloud.google.com/logging/docs/reference/tools/gcloud-logging
https://cloud.google.com/logging/docs/api/v2/resource-list
```

#### List all resources in a specific project used by a specific network.

```
Currently, the actual Cloud SDK commands doesn't include this information.

Subnets that are in auto mode cannot be deleted.

If the subnet has resources using it, it'll require to delete those resources first https://cloud.google.com/vpc/docs/using-vpc#deleting_a_subnet_or_vpc_network

To remove your default subnet within a particular region:
gcloud compute networks subnets delete default --region=<your region> 

On Google Cloud Shell if you receive the message: 
- Invalid resource usage: 'Cannot delete auto subnetwork from an auto subnet mode network.'. 
You could try deleting the default network as a workaround by executing the command: 
gcloud compute networks delete <your network> 

On your Google Cloud Shell, if there are resources using the network, it'll display a message saying which resources are using it. For instance: 
- The network resource 'projects/<your project>/global/networks/<your network>' is already being used by 'projects/<your project>/global/<the resource that is using it>'. 
```

#### Resizing root partition in google cloud linux
https://cloud.google.com/compute/docs/disks/create-root-persistent-disks#resizingrootpd
Once you resize the disk while the vm is running, restart the vm to apply new size automatically (most linux vms should automatically detect the new size).

#### Kubectl
The kubectl commands are run in the current config set - not related to the gcloud  active project.

Before any work do

```
gcloud config set core/project PROJECT_ID
gcloud config set compute/zone europe-west2-b
gcloud config set compute/region europe-west2
gcloud config set container/cluster CLUSTER_NAME
gcloud container clusters get-credentials CLUSTER_NAME
kubectl get ns
```
