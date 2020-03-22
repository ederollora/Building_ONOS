# Building_ONOS

The main goal of this repository is to provide a brief guideline on which conbination of ONOS, Bazel, Java work well together to be able to locally build ONOS. I also give some guidelines on how to get containers or a tar.gz-ed file. The latest changes in the ONOS' wiki mention that latest working Bazel version is 1.0.0 and that you need to install JDK 11 for ONOS 2.2.0 and later versions. I remember starting with Bazel 0.27 for versions around 1.14 (not working anymore) but now I find it difficult to find which ONOS' tags, Bazel versions and Java JDKs work well together. 

I have made a script to buila all combinations of ONOS (1.14.0 to 2.3.0), Bazel (0.27.0 to 2.2.0) and Java (8 for ONOS < 2.2.0 and 11 for ONOS >=  2.2.0) possibilities. The only working combinations are shown here (20/03/2020). I am unaware if the combinations that did not build is because I did not install an specific dependency though I followed all tutorials I found to make sure it did not happen. These tests were performed in Ubuntu 18 for now, I will try the same in Ubuntu 16.

|ONOS tag|Bazel version|Java JDK| Status                 
|:-:|:-:|:-:|:-:|
|2.2.1|1.0.0|11| Correct|
|2.2.3|1.0.0|11| Correct|
|2.2.1|1.0.1|11| Correct|
|2.2.3|1.0.1|11| Correct|
|2.2.1|1.1.0|11| Correct|
|2.2.3|1.1.0|11| Correct|
|2.2.1|1.2.0|11| Correct|
|2.2.3|1.2.0|11| Correct|
|2.2.1|1.2.1|11| Correct|
|2.2.3|1.2.1|11| Correct|

To sum it up, I have only been able to build ONOS tags 2.2.1 and 2.3.0 using Bazel 1.x.x version (Ubuntu 18). I have not been able to build any other ONOS version from 1.14.0. If you managed to, let me know and I will include it. You can check my tests in `built_versions.txt` file. I am planning to add the testing script and automated installing script too. You might find soon the specific building errors in each of the tests found in `built_versions.txt` file. If you feel like I have errors please send me an email to eoza@fotonik.dtu.dk so I can correct the errors.  

# [METHOD 1] Installing ONOS in your Ubuntu 16/18:

What to instal?
- Java (Amazon Corretto)
- Bazel (binary installer)
- ONOS (+ dependencies)

## Java 

In the [ONOS wiki](https://wiki.onosproject.org/display/ONOS/Developer+Quick+Start) they suggest that you might not need to install a JDK: "Starting with ONOS 2.2, we no longer require to install a JDK in your system to build and run ONOS when using Bazel, as we use a version of OpenJDK 11 that is shipped with Bazel.". You might still need to install a JRE or JDK to run onos-lib-gen, etc. or run ONOS without using Bazel. 

### Amazon Corretto

Use Amazon Corretto as suggested by [ONOS' wiki](https://wiki.onosproject.org/display/ONOS/Developer+Quick+Start). Following denoted steps are taken from [Amazon Corretto 8](https://docs.aws.amazon.com/corretto/latest/corretto-8-ug/generic-linux-install.html) and [Amazon Corretto 11](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/generic-linux-install.html). You probably only need one of the JDKs but feel free to install both:

```console
wget -O- https://apt.corretto.aws/corretto.key | sudo apt-key add -
sudo add-apt-repository 'deb https://apt.corretto.aws stable main' -y
```

#### Install JDK 8
```console
sudo apt-get update
sudo apt-get install java-1.8.0-amazon-corretto-jdk java-common -y
```

#### Install JDK 11
```console
sudo apt-get update
sudo apt-get install java-11-amazon-corretto-jdk java-common -y
```

`update-java-alternatives` is contained within `java-common` and is very useful to swift between JDK 8 and 11 to be used as primary JDKs. I used it to switch between versions when performing multiple building tests but if you install JDK 8 and 11 it can also be benefitial for you too. 

For instance, say your default JDK and system paths point to JDK 8 and you want to switch to JDK 11. You can always run ` java -version`  to check your current Java version.
```console
~$ java -version
openjdk version "1.8.0_242"
OpenJDK Runtime Environment Corretto-8.242.08.1 (build 1.8.0_242-b08)
OpenJDK 64-Bit Server VM Corretto-8.242.08.1 (build 25.242-b08, mixed mode)
```

We first check the directory name used by Amazon corretto to install JDK 11 (follownig commands described before):
```console
~$ ls -l /usr/lib/jvm/
total 8
drwxr-xr-x 9 root root 4096 Mar 10 11:11 java-11-amazon-corretto
drwxr-xr-x 7 root root 4096 Mar 18 12:25 java-1.8.0-amazon-corretto
```

Then use JDK 11 to switch:
```console
sudo update-java-alternatives -s java-11-amazon-corretto
```

Check it out:
```console
~$ java -version
openjdk version "11.0.6" 2020-01-14 LTS
OpenJDK Runtime Environment Corretto-11.0.6.10.1 (build 11.0.6+10-LTS)
OpenJDK 64-Bit Server VM Corretto-11.0.6.10.1 (build 11.0.6+10-LTS, mixed mode)
```

## Bazel

Instead fo installing from the repository, I prefer using a binary installer:

Install required packages
```console
sudo apt install pkg-config g++ zlib1g-dev unzip zip 
```

Bazel website recommends installing OpenJDK (although ONOS wiki mentions that ONOS 2.2.0 and on are built using the built-in JDK 11 in Bazel). Bazel website recommends installing `openjdk-8-jdk` or `openjdk-11-jdk` but you are probably OK if you followed the previous steps using Amazon Corretto. Let me know if doing any other thing makes any trouble.

Anyway you need to visit this website: [Bazel releases page on GitHub](https://github.com/bazelbuild/bazel/releases). Click on [Tags](https://github.com/bazelbuild/bazel/tags) and navigate to Bazel 1.0.0 or any that you'd like to install but keep in mind the table at the beginning of this repository. As of 20/03/2020 installing Bazel 0.27.0 or 2.1.0 will not build any ONOS tag/version. Therefore, choose probably a Bazel 1.x.x version. Go to [Bazel 1.0.0](https://github.com/bazelbuild/bazel/releases/tag/1.0.0) (for instance) and download file [`bazel-1.0.0-installer-linux-x86_64.sh`](https://github.com/bazelbuild/bazel/releases/download/1.0.0/bazel-1.0.0-installer-linux-x86_64.sh).

You might as well do it in the console:
```console
cd
mkdir -p Downloads; cd Downloads
wget https://github.com/bazelbuild/bazel/releases/download/1.0.0/bazel-1.0.0-installer-linux-x86_64.sh
```

Give run permissions and install it:
```console
cd ~/Downloads
chmod +x bazel-1.0.0-installer-linux-x86_64.sh
./bazel-1.0.0-installer-linux-x86_64.sh --user
```

After installing it, add `$HOME/bin` to your PATH variable and source the `bazel-complete.bash` file. We add it to .bashrc and source it afterwards:
```console
echo 'export PATH="$PATH:$HOME/bin"' >> ~/.bashrc
echo 'source $HOME/.bazel/bin/bazel-complete.bash' >> ~/.bashrc
source ~/.bashrc
```

If you need to change the Bazel version I usually run this command and it removes the installed directories (sometimes you might need to run as sudo) and then you can reinstall another Bazel version:
```console
rm -rf ~/.bazel ~/.bazelrc ~/.cache/bazel /usr/local/bin/bazel /etc/bazelrc /usr/local/lib/bazel
```

## ONOS

Some dependencies:

```console
sudo apt-get install git curl zip unzip python python3 bzip2
```

Clone ONOS:
```console
cd
git clone https://gerrit.onosproject.org/onos
```

Checkout an specific tag, choose from here:
```console
cd ~/onos
git checkout 2.3.0
git describe --tags
```

Say we choose 2.3.0 and include `bash_profile`:
```console
echo 'export ONOS_ROOT=~/onos' >> ~/.bashrc
echo 'source $ONOS_ROOT/tools/dev/bash_profile' >> ~/.bashrc
source ~/.bashrc
```

Build onos with Bazel:

```console
cd ~/onos
bazel build onos
```

When the building ends the output should show some text that can be closely compared to this one:
```console
  (...)
  bazel-bin/models/polatis/onos-models-polatis-oar.oar
  bazel-bin/models/ciena/waveserverai/onos-models-ciena-waveserverai-oar.oar
INFO: Elapsed time: 1775.666s, Critical Path: 579.17s
INFO: 2506 processes: 2127 linux-sandbox, 2 local, 377 worker.
INFO: Build completed successfully, 2668 total actions
```

You can now run ONOS with this command:
```console
bazel run onos-local -- clean debug
```

After some seconds (40 or more), open another terminal and run this command to connect to the CLI:
```console
~$ cd ~/onos
~$ tools/test/bin/onos localhost

Welcome to Open Network Operating System (ONOS)!
     ____  _  ______  ____     
    / __ \/ |/ / __ \/ __/   
   / /_/ /    / /_/ /\ \     
   \____/_/|_/\____/___/     
                               
Documentation: wiki.onosproject.org      
Tutorials:     tutorials.onosproject.org 
Mailing lists: lists.onosproject.org     

Come help out! Find out how at: contribute.onosproject.org 

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or type 'logout' to exit ONOS session.

eoza@root > apps -s -a                                                                           16:12:04
*  15 org.onosproject.optical-model        2.3.0    Optical Network Model
*  33 org.onosproject.drivers              2.3.0    Default Drivers
*  42 org.onosproject.gui2                 2.3.0    ONOS GUI2
*  54 org.onosproject.hostprovider         2.3.0    Host Location Provider
*  55 org.onosproject.lldpprovider         2.3.0    LLDP Link Provider
*  56 org.onosproject.openflow-base        2.3.0    OpenFlow Base Provider
*  57 org.onosproject.openflow             2.3.0    OpenFlow Provider Suite

eoza@root >  
```

## Create apps

For now, refer to [this website](https://wiki.onosproject.org/display/ONOS/Template+Application+Tutorial#TemplateApplicationTutorial-GenerateanewbaseONOSapplicationproject). I will try to sum the steps soon. To make sure that the steps works fine, you might still need to install Apache Karaf and Maven as described in previous Wiki versions [like this one](https://wiki.onosproject.org/display/ONOS15/Installing+and+Running+ONOS) (refer only to "Install Karaf, Maven:" part). I am not complete aware of how app creation is done anymore (give me a couple of days) but I guess either with onos-create-app or importing ~/onos to Jetbrains IntelliJ IDEA and creatin

# [METHOD 2] ONOS and Docker

If you are not insterested in having ONOS cloned locally and modify and compile things locally, you can always install Docker and ONOS containers if you need the ONOS controller running. 

## Install Docker in Ubuntu 16:

```console
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
apt-cache policy docker-ce # Check docker versions
sudo apt-get install -y docker-ce
sudo systemctl status docker # check Docker status
```

## Install  Docker in Ubuntu 18:

```console
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
```

You could first look into [ONOS' wiki](https://hub.docker.com/r/onosproject/onos/) to be sure of all the suggestions and commands, but I try to list them now.

## Pull latest ONOS Docker container

Get the latest (see also next command) [ONOS container](https://hub.docker.com/r/onosproject/onos/):
```console
sudo docker pull onosproject/onos
```

## And/else pull an specific ONOS tagged version

You can get a list of tags in the console by running: 
 ```console
 # https://stackoverflow.com/questions/28320134/how-can-i-list-all-tags-for-a-docker-image-on-a-remote-registry
~$ wget -q https://registry.hub.docker.com/v1/repositories/onosproject/onos/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}'

latest
1.10.0
1.10.10
1.10.11
1.10.12
(...)
2.2.1-b4
2.2.1-b5
2.2.x
2.3.0
 ```

Getting an specific tag could be a better idea, check here [the tags](https://hub.docker.com/r/onosproject/onos/tags) too on your browser:
```console
sudo docker pull onosproject/onos:2.3.0
```

## Run the container 

Run the container (with name onos1, used also to stop/remove it later) and ssh into ONOS' CLI (password: karaf). Several ways to start the container, check them at the aforementioned website:
```console
sudo docker run -t -d -p 8181:8181 -p 8101:8101 -p 5005:5005 -p 830:830 --env JAVA_DEBUG_PORT="0.0.0.0:5005" --name onos1 onosproject/onos:2.2.1 debug
ssh -p 8101 karaf@`docker-ip onos1`
```

## A Bash prompt for the container

You might need a bash prompt on it:
```console
sudo docker exec -it `sudo docker ps -aqf "name=onos1"` /bin/bash
```

Check the container and stop/remove the container ny its name:
```console
sudo docker ps -a
sudo docker container stop onos1
sudo docker container rm onos1
```

# [Method 3] ONOS and tar.gz files

Instead of getting a container, you can also download ready-to-run ONOS tar.gz files. [ONOS website](https://wiki.onosproject.org/display/ONOS/Installing+ONOS+from+onos.tar.gz+file). The fiels can be downloaded from [this website](https://wiki.onosproject.org/display/ONOS/Downloads)
```console
cd ¨/Downloads
wget https://repo1.maven.org/maven2/org/onosproject/onos-releases/2.3.0/onos-2.3.0.tar.gz
```


