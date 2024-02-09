
## Configure Indexer Cluster 

### Cluster Manager:

#### Login to Cluster Manager using SSH:
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode manager -replication_factor 4 -search_factor 3 -secret your_key -cluster_label cluster1

./splunk restart

```

### Peer nodes (All indexers)

#### Login to peer node using SSH:
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode peer -manager_uri https://10.160.31.200:8089 -replication_port 9887 -secret your_key

./splunk restart

```

## Configure Search Head Cluster

### Deployer 

#### Login to Deployer using SSH

##### Navigate to system/local directory.
```bash
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

./splunk init shcluster-config -auth admin:Rahuman@4314 -mgmt_uri https://SH1_IP_OR_DNS:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP_OR_DNS:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 2

#### Execute below commands
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk init shcluster-config -auth admin:Rahuman@4314 -mgmt_uri https://SH2_IP_OR_DNS:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP_OR_DNS:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 3

#### Execute below commands
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk init shcluster-config -auth admin:Rahuman@4314 -mgmt_uri https://SH3_IP_OR_DNS:8089 -replication_port 9000 -replication_factor 3 -conf_deploy_fetch_url http://DEPLOYER_IP_OR_DNS:8089 -secret pass_4_Symm_Key -shcluster_label shcluster1

./splunk restart
```
### Search Head 1
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk bootstrap shcluster-captain -servers_list "https://52.199.82.244:8089,https://54.168.7.26:8089,https://54.168.97.221:8089" -auth admin:Rahuman@4314

```

## Connect Search Head Cluster with Indexer Cluster

### Search Head 1, 2, 3
```bash
sudo su - splunk

cd /opt/splunk/bin

./splunk edit cluster-config -mode searchhead -master_uri https://18.139.109.202:8089 -secret Rahuman@4314 

./splunk restart

```
