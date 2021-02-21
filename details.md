### STEP BY STEP PROCEDURE FOR SETTING UP A EKS CLUSTER,AWSCLI,KUBECTL AND LAUNCHING JENKINS SERVER 
### INSTALLING AND MANAGING PLUGGINS AND FREE STYLE JOB CONFIGURATION AND RUNNING A FREE STYLE JOB.

### Launhing instances for admin instance and jenkins server .
* Login to root account of aws and launch an EC2 instance (t2.nano) with amazon linux ami image  
* Download keypair to connect to ec2 instance). store this file with name keypair-admin-instance
* wait till the instance is launched and give it a name admin-instance
* launch mobaxterm on your laptop and connect to admin-instance using the
* setup a new repository on github. Name it ncd-realtime-project-repo

* launch 2nd ec2 instance (t2.nano) , with below properties
* use ubuntu 18 imagee thats eligibe under free tier eligible
* under ingress rules add http traffic for 8080 port (to allow access to jenkins UI) or keep as all traffic
* give the name jenkins-server to this ec2 instance
* use the same keypair that was used to create first ec2 instance. By using the same keypair we will be able to login to both instances with the same key. This will keep things simple
* login to jenkins-server ec2 instance using keypair-admin-stance

### Setup a awsCLI,kubectl,
* Click View Instances, and give it a few minutes to enter the running state.
* Once the instance is fully created, check the checkbox next to it and click Connect at the top of the window.
* In the Connect to your instance dialog, select EC2 Instance Connect (browser-based SSH connection).
* Click Connect.
* In the command line window, check the AWS CLI version:
* aws --version
* It should be an older version.
Download v2:
* curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
Unzip the file:
* unzip awscliv2.zip
See where the current AWS CLI is installed:
* which aws
It should be /usr/bin/aws.
Update it:
* sudo ./aws/install --bin-dir /usr/bin --install-dir /usr/bin/aws-cli --update
Check the version of AWS CLI:
* aws --version
It should now be updated.
Configure the CLI:
* aws configure 
For AWS Access Key ID, paste in the access key ID you copied earlier,
For AWS Secret Access Key, paste in the secret access key you copied earlier.
if not copied click on username in home page of AWS management console.
In that click on SECURITY CREDENTIALS.
In that click on ACCESS KEY
Download ACCESS KEY 
Open the downloaded file 

* For Default region name, enter us-east-1.
* For Default output format, enter json.
Download kubectl:
* curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl
* Apply execute permissions to the binary:
* chmod +x ./kubectl
* Copy the binary to a directory in your path:
* mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
* Ensure kubectl is installed:
* kubectl version --short --client
Download eksctl:
* curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
Move the extracted binary to /usr/bin:
* sudo mv /tmp/eksctl /usr/bin
Get the version of eksctl:
* eksctl version
See the options with eksctl:
* eksctl help
Provision an EKS cluster with three worker nodes in us-east-1:
* eksctl create cluster --name dev --version 1.16 --region us-east-1 --nodegroup-name standard-workers --node-type t3.micro --nodes 3 --nodes-min 1 --nodes-max 4 --managed
* It takes 10-15 min to setup a eks cluster.
* TO CHECK WHETHER CLUSTER IS CREATED OR NoT GO TO ekscluster in search AND SEARCH clusters in Amazon eks . You can also see the status.
* As there in the command three instances have to be created automatically and also three nodes have to created. 
* check nodes in eks cluster>>cluster>>dev>>compute>>nodeports

### Install jenkins,maven and java on JENKINS-SERVER instance.

Go to mobax
open the jenkins server instance using public ip and and pem key

* cd $HOME
* git clone https://github.com/ncodeit-io/devops-cloud        # clone the whole repo
* cd devops-cloud/mini-project
* sudo ./install_java8.sh          # install java required by jenkins
* export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
* sudo ./install_jenkins.sh       # install jenkins
* apt install maven 
* mvn -version
* java -version

open url `http://<public-ip-of-jenkins-server-ec2-instance>:8080`
finish the jenkins installation with `recommnded plugins`

### Installing the pluggins 

Jenkins plugins Installation
Install the following plugins in jenkins (Jenkins Dashboard -> Manage Jenkins -> Manage Plugins -> Available )
search for following plugins and select all
* slack Notification Plugin
* nexus artifact uploader
* nexus platform
* git parameter plugin
* pipeline utility steps plugin
* Artifactory
* Maven Release Plug-in
* Pipeline Maven Integration Plugin
* disk-usage plugin
* Dashboard View
click Install without restart

### Configuring the plugins 

* go to Jenkins Dashboard ->
* Manage Jenkins -> Global Tool configuration -> Add Maven
* give Name as Maven
* Unselect Install automatically -> give MAVEN_HOME as /usr/share/maven
* click Apply , wait till its saved

### Free style job configuration
Lets create the first Jenkins Job , its a simple Free style job
* go to Jenkins Dashboard
* New Item -> give 01-freestyle-job
* click Freestyle project
* click OK
* under Source Code Management select Git
* for Repository URL give https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git
* for Branch to Build give */master
* under Build
* click Add build step -> Execute command shell
type any normal command in that shell eg:echo hello world
Apply , wait for saved
save
 
### Run the free style job and check if the Artifact reached nexus repo
* go to Jenkins Dashboard 
* click the newly created job 01-freestyle-job
* Build now
* under Build History -> click on latest running build
* clikc Console Output
* Jenkins will download all the dependencies to run the job
wait till Finished: SUCCESS



