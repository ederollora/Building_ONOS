


#Building_ONOS

The old [REDMAE.md](old/README.md), you can take a look.

The [Apache Karaf](apache-karaf-3.0.3.tar.gz), [Apache Maven](apache-maven-3.3.1-bin.tar.gz) and [Bazel 3.7.2](bazel-3.7.2-installer-linux-x86_64.sh).

I think the Java already comes with the ONOS. Not sure I will indeed try.

#### Install dependencies

```console
sudo snap install chromium
sudo apt install git git-review curl
```

#### Mininet

```console
git clone https://github.com/mininet/mininet
cd mininet
git tag  # list available versions
git checkout -b mininet-2.3.0 2.3.0  # or whatever version you wish to install


# Modifications:

nano mininet/util/install.sh
 
#1 Change from git to https
# git clone git://github.com/mininet/openflow 
git clone https://github.com/mininet/openflow 
 
# 2 remove the check
#if sudo service openvswitch-controller stop 2>/dev/null; then
#    echo "Stopped running controller"
#fi
#if [ -e /etc/init.d/openvswitch-controller ]; then
#    sudo update-rc.d openvswitch-controller disable
#fi
 
# 3 Remove the check
#if [ "$OVSC" ]; then
	# Switch can run on its own, but
	# Mininet should control the controller
	# This appears to only be an issue on Ubuntu/Debian
#    if sudo service $OVSC stop 2>/dev/null; then
#        echo "Stopped running controller"
#    fi
#    if [ -e /etc/init.d/$OVSC ]; then
#        sudo update-rc.d $OVSC disable
#    fi
#fi
 
# only install the necessary
cd ..
mininet/util/install.sh -nfv
 
sudo mn
h1 ping h2
```

#### Karaf and Maven 

```console
cd ..
sudo apt install pkg-config g++ zlib1g-dev unzip zip 

cd
mkdir -p Downloads; cd Downloads
tar -zxvf apache-karaf-3.0.3.tar.gz -C ../Applications/
tar -zxvf apache-maven-3.3.1-bin.tar.gz -C ../Applications/ 
```

####.bash_profile

```console
export ONOS_ROOT="/home/st1/onos"
source /home/st1/onos/tools/dev/bash_profile
```

####.bashrc

```console
export ONOS_ROOT=~/onos
export MAVEN=~/Applications/apache-maven-3.3.9
export KARAF_ROOT=~/Applications/apache-karaf-3.0.8
export ONOS_APPS=drivers,openflow,proxyarp,fwd,gui2,hostprovider,lldpprovider
```


#### Bazel

```console
wget bazel-6.0.0-pre.20220421.3-installer-linux-x86_64.sh
chmod +x bazel-6.0.0-pre.20220421.3-installer-linux-x86_64.sh
./bazel-6.0.0-pre.20220421.3-installer-linux-x86_64.sh --user
./bazel-3.7.2-installer-linux-x86_64.sh --user 
source ~/onos/.bashrc
cd onos
bazel build onos
bazel run onos-local -- clean debug
```

#### Karaf/Maven
```console
cd ~/onos
mvn clean install
```

 
#### tools/test/bin/onos locahost
#### Password?

```console
tools/test/bin/onos locahost 
# or
ssh -p 8101 onos@localhost
password: rocks
```


