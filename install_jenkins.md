# INSTALL JAVA 
## MANUAL METHOD   
### DOWNLOAD JDK MANUALLY FROM A BROWSER OR USING WGET
    wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz

### Once the tar is downloaded can be verified by the tarball checksum
    sha256sum jdk-17_linux-x64_bin.tar.gz

#### Compare the hash value from the above output to the checksum value on the Oracle download page. If they match, that means the file’s integrity is validated.

### Extract the files and move to /opt
    tar xvzf jdk-17_linux-x64_bin.tar.gz -C /opt

### Set the enviroment variables
    export JAVA_HOME=/opt/jdk-17.0.2
    export PATH=$PATH:$JAVA_HOME/bin

### test the java_home variable and java installation
    echo $JAVA_VERSION
    java -version


journalctl -xeu jenkins.service"