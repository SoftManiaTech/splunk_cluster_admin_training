## How to Deploy Apps to Search Head Cluster

#### Once you have downloaded the Apps/Add-ons from the splunkbase, navigate to your downloads directory

```bash
cd /c/Users/murugan/Downloads/
```

#### Copy the Apps/Add-ons package from your local laptop to Deployer server backend

```bash
scp -i "yourkey.pem" your_app_tar_file_name ec2-user@YOUR_Deployer_PUBLIC_IP:/tmp
```

#### Login to Deployer backend using SSH

#### Switch user to root & navigate to /tmp directory

```bash
sudo su

cd /tmp
```

#### Change the ownership of the package

```bash
chown -R splunk:splunk your_app_tar_file_name
```

#### Switch user to splunk & navigate to /tmp directory

```bash
sudo su - splunk

cd /tmp
```

#### Extract the package to the configuration bundle directory

```bash
tar -xvf your_app_tar_file_name -C /opt/splunk/etc/shcluster/apps/ 
```

#### Navigate to the configuration bundle directory & check if the App folder is present
```bash
cd /opt/splunk/etc/shcluster/apps/

ls
```
#### Navigate to the Splunk bin directory

```bash
cd /opt/splunk/bin
```

#### To check the cluster status, run the following command on any cluster member:

```bash
./splunk show shcluster-status --verbose
```
### Note: In the output, verify that the value for the captain's service_ready_flag is 1.


#### In Deployer - run below command

```bash
./splunk apply shcluster-bundle --answer-yes -target https://ANY_SEARCH_HEAD_IP:8089 -auth DEPLOYER_USERNAME:DEPLOYER_PASSWORD
```
