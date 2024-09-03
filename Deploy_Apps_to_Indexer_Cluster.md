## How to Deploy Apps to Indexer Cluster

#### Once you have downloaded the Apps/Add-ons from the splunkbase, navigate to your downloads directory

```bash
cd /c/Users/murugan/Downloads/
```

#### Copy the Apps/Add-ons package from your local laptop to Cluster Manager server backend

```bash
scp -i "yourkey.pem" your_app_tar_file_name.tgz ec2-user@YOUR_CLUSTER_MANAGER_PUBLIC_IP:/tmp
```

#### Login to Cluster Manager backend using SSH

#### Switch user to root & navigate to /tmp directory

```bash
sudo su

cd /tmp
```

#### Change the ownership of the package

```bash
chown -R splunk:splunk your_app_tar_file_name.tgz
```

#### Switch user to splunk & navigate to /tmp directory

```bash
sudo su - splunk

cd /tmp
```

#### Extract the package to the configuration bundle directory

```bash
tar -xvf your_app_tar_file_name.tgz -C /opt/splunk/etc/manager-apps/ 
```

#### Navigate to the configuration bundle directory & check if the App folder is present

cd /opt/splunk/etc/manager-apps/

ls

#### Navigate to the Splunk bin directory

```bash
cd /opt/splunk/bin
```

#### Validate the bundle

```bash
./splunk validate cluster-bundle --check-restart

```
#### Check the bundle status

```bash
./splunk show cluster-bundle-status
```

#### Apply the bundle 

```bash
./splunk apply cluster-bundle
```

#### Check the status of the bundle

```bash
./splunk show cluster-bundle-status
```
