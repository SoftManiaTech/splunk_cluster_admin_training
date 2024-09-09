# How to Clean Indexes in Indexer cluster
## Step-1: Add a Lower retention policy to indexes
In Cluster Manager node, 
Login as splunk user
```bash
sudo su - splunk
```
Go to local folder in _cluster directory
```bash
cd /opt/splunk/etc/manager-apps/_cluster/local/
```
Edit indexes.conf
```bash
vi indexes.conf
```
Add the below configuration to the index which you want to clean and save the file.
```bash
[index_name]
frozenTimePeriodInSecs = 10
rotatePeriodInSecs = 10
maxHotIdleSecs = 100
```
## Step-2: Deploy the bundle to peer nodes
In Cluster Manager node, execute the below command in CLI
```bash
/opt/splunk/bin/splunk validate cluster-bundle --check-restart
```
The last_validation_succeeded=1 field indicates that validation succeeded.
<br>
To apply the configuration bundle to the peers, run this CLI command
```bash
/opt/splunk/bin/splunk apply cluster-bundle --answer-yes
```
## Step-3: Check the index
In one of Search head, run below query & check the count field, it should be 0
```bash
| tstats count where index=idx1 by index
```
If the count field is not 0, Wait for few minutes, run the query again (It takes some time to clean all the data across the cluster)
<br>
If the count field is 0, then the index is cleaned completely. 
<br>
## Step-4: Remove the Lower retention policy of the Indexes
In the Cluster Manager node, 
create or edit $SPLUNK_HOME/etc/manager-apps/_cluster/local/indexes.conf remove the lines from the respective index stanza. 
## Step-5: Deploy the bundle
In the Cluster Manager node itself, execute the below command in CLI
```bash
/opt/splunk/bin/splunk validate cluster-bundle --check-restart
```
The last_validation_succeeded=1 field indicates that validation succeeded.
<br>
To apply the configuration bundle to the peers, run this CLI command
```bash
/opt/splunk/bin/splunk apply cluster-bundle --answer-yes
```
<br>
