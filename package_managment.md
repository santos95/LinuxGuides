# Package managment - process of install, update, configure and remove software 
# Package managment - facilitate soft distribution, simplifies soft instalation, ensure soft compatibility and handle dependencies, mantains system stability
## we can use different tools - apt, apt-get, dpkg (debian package manager) 
## The packages are distributed in .deb format (compressed as ar format) 
## this .deb files contains all the requires files for the program and his instalation.

### to install using the dpkg tool 
    dpkg -i package.deb

### to remove 
    dpkg -r tool_name 

### To install packages manually - we need to be sure that is the appropiate version for our system arquitecture and distribution - To get the distribution that we are running on:
    lsb_release -a

### Installing software manually in linux isn' common - It's a bit dangerous - because have the download the packages throug an insecure networks.

### to search for packages:
    https://packages.ubuntu.com/

