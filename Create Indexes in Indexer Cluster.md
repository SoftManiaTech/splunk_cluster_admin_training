How to Create Indexes in Indexer Cluster?

# Step 1: Create Index in Cluster Manager
Connect to Cluster Manager Backend and Login as splunk user.
```bash
sudo su - splunk
```
Go to _cluster folder.
```bash
cd /opt/splunk/etc/manager-apps/_cluster
```
Go to local folder inside _cluster directory.
```bash
ls
cd local
```
Create or Edit the indexes.conf file
```bash
vi indexes.conf
```
Add the below configurations to create a new index and save the indexes.conf file.
(Eg: Lets create an index named "softmania_demo_idx")
```bash
[softmania_demo_idx]
repFactor = auto
homePath = $SPLUNK_DB/softmania_demo_idx/db
coldPath = $SPLUNK_DB/softmania_demo_idx/colddb
thawedPath = $SPLUNK_DB/softmania_demo_idx/thaweddb
```
# Step 2: Push the bundle to peers
We can push the configuration bundle actions using one of the two ways below.
1. Using UI
2. Using CLI

#### 1. Push the bundle actions to peers - Using UI
1. In cluster manager UI, Go to Settings --> Indexer Clustering
2. Click on Edit --> Configuration Bundle Actions
3. Click on Push Changes button

The configuration bundle actions are pushed successfully.

#### 2. Push the bundle actions to peers - Using CLI
In Cluster Manager node, execute the below command to check if restart is required or not.
```bash
/opt/splunk/bin/splunk validate cluster-bundle --check-restart
```
If the last_validation_succeeded=1, then the validation succeeded.
<br/>
<br>
To apply the configuration bundle to peers, execute the below command.
```bash
/opt/splunk/bin/splunk apply cluster-bundle --answer-yes
```
The configuration bundle actions are pushed successfully.

# Step 3: Add data to the index
1. In Indexer_1 UI, Go to Settings --> Add data
2. Click on Upload (files from my computer)
3. Click on Select File and select the file to upload.
4. In Set Source Type page, Click on Save As.
5. In the pop up, enter name "test_logs" as the sourcetype name and click on Save.
6. Click on the Next button.
7. In Input settings screen, check the Host Field Value and select the index "softmania_demo_idx". Click on Review button.
8. Review the details and Click on Submit.

Finally, In Cluster Manager node, go to Settings --> Indexer Clustering --> Indexes. The new index "softmania_demo_idx" will be listed here.
