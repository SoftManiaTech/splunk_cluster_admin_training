
## Configure Indexer Cluster 

### Cluster Manager:

#### Login to Cluster Manager using SSH:
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode manager -replication_factor 3 -search_factor 2 -secret your_key -cluster_label cluster1

./splunk restart

```

### Peer nodes (All indexers)

#### Login to peer node using SSH:
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode peer -manager_uri https://YOUR_CLUSTER_MANAGER_IP:8089 -replication_port 9887 -secret your_key

./splunk restart

```

## Configure Search Head Cluster

### Deployer 

#### Login to Deployer using SSH

##### Navigate to system/local directory.
```bash
sudo su - splunk

cd /opt/splunk/etc/system/local/
```
#### Add below contents to server.conf file.
```bash
vi server.conf
```
```bash
[shclustering]
pass4SymmKey = SoftManiaSHClusterKey
shcluster_label = shcluster1
```
#### Restart the Deployer.

```bash
/opt/splunk/bin/splunk restart
```

#### Open Server.conf file and copy the encrypted key to your notepad.

```bash
cat /opt/splunk/etc/system/local/server.conf
```


### Search Head 1

#### Execute below commands
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk init shcluster-config -auth admin:YOUR_PASSWD -mgmt_uri https://SH1_IP:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 2

#### Execute below commands
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk init shcluster-config -auth admin:YOUR_PASSWD -mgmt_uri https://SH2_IP:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 3

#### Execute below commands
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk init shcluster-config -auth admin:YOUR_PASSWD -mgmt_uri https://SH3_IP:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 1
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk bootstrap shcluster-captain -servers_list "https://YOUR_SH1_IP:8089,https://YOUR_SH2_IP:8089,https://YOUR_SH3_IP:8089" -auth admin:YOUR_SH!_PASSWORD

./splunk show shcluster-status -auth admin:YOUR_SH1_PASSWORD
```

### Note: In case, you need to restart the search head cluster, Use rolling restart command.

```bash
splunk rolling-restart shcluster-members
```

## Connect Search Head Cluster with Indexer Cluster

### Search Head 1, 2, 3
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode searchhead -manager_uri https://YOUR_CLUSTER_MANAGER_IP:8089 -secret YOUR_INDEXER_CLUSTER_SECRET_KEY 

./splunk restart

```

## Configure Deployment Server & Forwarders

### Login to Universal Forwarder 1

```bash
sudo su - splunk

/opt/splunkforwarder/bin/splunk status

cd /opt/splunkforwarder/bin

./splunk set deploy-poll YOUR_DEPLOYMENT_SERVER_IP:8089

./splunk set default-hostname "Universal_Forwarder_1"

./splunk set servername "Universal_Forwarder_1"

./splunk restart

```
Note: Repeat the above steps in all your Universal Forwarders


## Deploy Add-on to Universal Forwarders using Deployment Server

### Upload the Addon package from your local/laptop to Deployment Server

```bash
cd /c/Users/yourusername/Downloads/

scp -i "yourkey.pem" splunk-add-on-for-unix-and-linux_8100.tgz ec2-user@your_public_ip_or_dns:/tmp

```

### Extract the package to the deployment bundle directory

### Login to Deployment Server

```bash
sudo su 

cd /tmp

chown -R splunk:splunk splunk-add-on-for-unix-and-linux_8100.tgz

sudo su - splunk

cd /tmp/

tar -xvf splunk-add-on-for-unix-and-linux_8100.tgz -C /opt/splunk/etc/deployment-apps/

cd /opt/splunk/etc/deployment-apps/

```

## Connect Forwarders with Indexer Cluster

### Best Practice: Implement Indexer Discovery

#### In Cluster Manager

```bash
sudo su - splunk

cd /opt/splunk/etc/system/local/

vi server.conf

[indexer_discovery]
pass4SymmKey = IndexerDiscoveryKey123
polling_rate = 10
indexerWeightByDiskCapacity = true

/opt/splunk/bin/splunk restart

```

#### In Universal Forwarders

```bash
sudo su - splunk

cd /opt/splunkforwarder/etc/system/local/

ls

vi outputs.conf

[indexer_discovery:manager1]
pass4SymmKey = IndexerDiscoveryKey123
manager_uri = https://your_cluster_manager_ip:8089

[tcpout:group1]
autoLBFrequency = 30
forceTimebasedAutoLB = true
indexerDiscovery = manager1
useACK=true

[tcpout]
defaultGroup = group1

cd /opt/splunkforwarder/bin/

./splunk restart
```
Repeat the same steps in all other Forwarders as well (Forwarder 2, etc.)
