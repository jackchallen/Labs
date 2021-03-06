# Docker Lab Contents
This lab purpose is to install and use Docker to become familiar with Linux based containers and handle some of the common use cases around it.

## Lab Writer and Trainer
Bruno.Cornec@hpe.com

<!--- [comment]: # Table of Content to be added --->

## Objectives of the Docker Lab
At the end of the Lab students should be able to install docker, use the CLI to create a new image, a container, launch an application in it, store data, onfigure the network.

This Lab is intended to be trial and error so that during the session students should understand really what is behind the tool, instead of blindly following instructions, which never teach people anything IMHO. You've been warned ;-)

Expected duration : 120 minutes

## Reference documents
When dealing with the installation and configuration of Docker, the first approach is to look at the reference Web site http://docker.io/: 

Estimated time for the lab is placed in front of each part.

# Environment setup
Estimated time: 15 minutes
## Docker installation
Docker is available externaly from http://docs.docker.com/linux/step_one/ or using your distribution packages, or from github at https://github.com/docker/docker
Version 1.7 is  the current stable release.
As we'll work on an Ubuntu environment for the Lab, you may want to use apt to do the installation of Docker with all its dependencies. As Ubuntu provides an old version of docker, we will use a ppa providing a more up to date version:

`#` **`apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9`**

`#` **`echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list`**

`#` **`apt-get update`**

`#` **`apt-get install lxc-docker`**
```none
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  aufs-tools cgroup-lite git git-man liberror-perl patch
Suggested packages:
  btrfs-tools debootstrap lxc rinse git-daemon-run git-daemon-sysvinit git-doc
  git-el git-email git-gui gitk gitweb git-arch git-bzr git-cvs git-mediawiki
  git-svn diffutils-doc
The following NEW packages will be installed:
  aufs-tools cgroup-lite lxc-docker git git-man liberror-perl patch
0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
Need to get 7,640 kB of archives.
After this operation, 46.9 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://fr.archive.ubuntu.com/ubuntu/ trusty/universe aufs-tools amd64 1:3.2+20130722-1.1 [92.3 kB]
Get:2 https://get.docker.io/ubuntu/ docker/main lxc-docker-1.7.0 amd64 1.7.0 [4,962 kB]
[...]
Fetched 7,640 kB in 8s (884 kB/s)                                              
Selecting previously unselected package aufs-tools.
(Reading database ... 54255 files and directories currently installed.)
Preparing to unpack .../aufs-tools_1%3a3.2+20130722-1.1_amd64.deb ...
Unpacking aufs-tools (1:3.2+20130722-1.1) ...
[...]
Setting up lxc-docker (1.7.0) ...
Adding group docker' (GID 111) ...
Done.
[...]
```
Other distributions should be as easy to deal with by providing the same packages out of the box (Case of most non-commercial distributions such as Debian, Fedora, Mageia, OpenSuSE, …)
Check that the correct version is installed and operational:

`#` **`docker --version`**
```
Docker version 1.7.0, build 0baf609
```
`#` **`docker info`**
```
Containers: 0
Images: 0
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 3.16.0-41-generic
Operating System: Ubuntu 14.04.2 LTS
CPUs: 6
Total Memory: 15.55 GiB
Name: lab6
ID: JQD5:3JVU:YF7H:RZFK:Y36O:PTQ3:QZQ2:C3HF:I3HB:NDKM:5PLF:75J5
WARNING: No swap limit support
```
`#` **`docker `**

[Display online help]

Now that the software has been installed, we'll use it to create and manage containers.
# Using docker
Estimated time: 15 minutes.
## The first container
In order to be able to manage a first container, the easiest approach is to import an existing one, before creating your own. For that we will refer to the public docker registry which contains thousands of ready to be consumed containers:

`#` **`docker run hello-world`**
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from hello-world
a8219747be10: Pull complete 
91c95931e552: Already exists 
hello-world:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:aa03e5d0d5553b4c3473e89c8619cf79df368babd18681cf5daeb82aab55838d
Status: Downloaded newer image for hello-world:latest
Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (Assuming it was not already locally available.)
3. The Docker daemon created a new container from that image which runs
    the executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

For more examples and ideas, visit:
 http://docs.docker.com/userguide/
```

So we've got a success  ! Of course, we do not really go far, but what can you expect from an hello-world example ;-)
However, we can already get some info on our modified docker environment:

`#` **`docker images`**
```
REPOSITORY     TAG            IMAGE ID       CREATED          VIRTUAL SIZE
hello-world    latest         91c95931e552   10 weeks ago     910 B
```
`#` **`docker ps -a`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
4dba332aec93d        hello-world         "/hello"            14 minutes ago      Exited (0) 14 minutes ago                       cocky_hopper        
```
`#` **`docker rm 4dba332aec93d`**
```
dba332aec93d
```
`#` **`docker ps -a`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
`#` **`docker images`**
```
REPOSITORY     TAG            IMAGE ID       CREATED          VIRTUAL SIZE
hello-world    latest         91c95931e552   10 weeks ago     910 B
```

So we see that we now have an image which has been downloaded from the docker public registry, and that a container has been instantiated from that image and is not running anymore. The `rm` command allows to delete the container (but of course not the image which remains available)
## The second container
In order to have a more interesting environment, we'll now look for existing container images in the public docker registry, and choose to use a fedora image on our Ubuntu environment:

`#` **`docker search fedora`**
```
NAME         DESCRIPTION                    STARS    OFFICIAL  AUTOMATED
fedora       Official Fedora 22 base image  175      [OK]       
tutum/fedora Fedora image with SSH access.  7                  [OK]
[...]
```
`#` **`docker pull fedora`**
```
latest: Pulling from fedora
48ecf305d2cf: Pull complete 
ded7cd95e059: Already exists 
fedora:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:10ba981a70632d7764c21deae25c6521db6d39730e1dd8caff90719013858a7b
Status: Downloaded newer image for fedora:latest
```

Once the container image has been downloaded we can view it in our catalog of images:

`#` **`docker images`**
```
REPOSITORY    TAG        IMAGE ID          CREATED            VIRTUAL SIZE
fedora        latest     ded7cd95e059      4 weeks ago        186.5 MB
hello-world   latest     91c95931e552      10 weeks ago       910 B
```

This content is called an image and will serve as the base to create the operational container (here based on Fedora) in which we will process data:

`#` **`docker run -ti ded7cd95e059 /bin/bash`**

`[root@ad9b474525d0 /]#` **`cat /etc/fedora-release`**
```
Fedora release 22 (Twenty Two)
```
`[root@ad9b474525d0 /]#` **`yum install -y wget`**
```
Yum command has been deprecated, redirecting to '/usr/bin/dnf install wget'.
[...]
Fedora 22 - x86_64                             3.7 MB/s |  41 MB     00:11    
Fedora 22 - x86_64 - Updates                   1.7 MB/s | 9.7 MB     00:05    
Last metadata expiration check performed 0:00:04 ago on Tue Jun 30 10:38:14 2015.
Dependencies resolved.
==========================================================================
 Package               Arch   Version        Repository               Size
==========================================================================
Installing:
 libicu                x86_64 54.1-1.fc22    fedora                  8.4 M
 libpsl                x86_64 0.7.0-3.fc22   fedora                   50 k
 wget                  x86_64 1.16.3-1.fc22  fedora                  577 k

Transaction Summary
==========================================================================
Install  3 Packages

Total download size: 9.0 M
Installed size: 31 M
Downloading Packages:
(1/3): libpsl-0.7.0-3.fc22.x86_64.rpm           16 kB/s |  50 kB     00:03    
(2/3): wget-1.16.3-1.fc22.x86_64.rpm           176 kB/s | 577 kB     00:03    
(3/3): libicu-54.1-1.fc22.x86_64.rpm           1.8 MB/s | 8.4 MB     00:04    
--------------------------------------------------------------------------
Total                                                                                                                     1.4 MB/s | 9.0 MB     00:06     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Installing  : libicu-54.1-1.fc22.x86_64                              1/3 
warning: Unable to get systemd shutdown inhibition lock
  Installing  : libpsl-0.7.0-3.fc22.x86_64                             2/3 
  Installing  : wget-1.16.3-1.fc22.x86_64                              3/3 
  Verifying   : wget-1.16.3-1.fc22.x86_64                              1/3 
  Verifying   : libpsl-0.7.0-3.fc22.x86_64                             2/3 
  Verifying   : libicu-54.1-1.fc22.x86_64                              3/3 

Installed:
  libicu.x86_64 54.1-1.fc22 libpsl.x86_64 0.7.0-3.fc22 wget.x86_64 1.16.3-1.fc22                        

Complete!

```
`[root@ad9b474525d0 /]#` **`uname -a`**
```
Linux ad9b474525d0 3.16.0-41-generic #57~14.04.1-Ubuntu SMP Thu Jun 18 18:01:13 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```

So you checked that your container behaves like a Fedora 22 distribution. Only the kernel is shared between the docker host and the docker container. Open another console to view how this container has been created ans is seen:

`#` **`docker ps`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
ad9b474525d0        ded7cd95e059        "/bin/bash"         12 minutes ago      Up 12 minutes                           hopeful_hopper
```
If you logout of the container, you'll see how docker manages that fact: 

`[root@ad9b474525d0 /]#` **`exit`**

`#` **`docker ps`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
`#` **`docker ps -a`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
ad9b474525d0        ded7cd95e059        "/bin/bash"         23 minutes ago      Exited (0) 4 seconds ago                       hopeful_hopper
```

So your container is seen as stopped and you have to use the -a option to see it in the history of containers created, but not active anymore.

Re-create a new container based on the same image, connect to it and look at the packages installed. Check what docker sees. Use the previous commands to perform these tasks.

Answer the questions:
1. Can you download a web page with wget in your container ? Why ? Which steps are needed now ? Why ?
2. Can you connect back to your first container ? (Hint: use docker start to re-enable your dead container and docker attach to re-enter in it)

# Configuring owncloud in a container

Estimated time: 60 minutes.

Based on the work done in the Docker Dojo during a Grenoble Docker Meetup (Cf: https://github.com/Enalean/docker-dojo/tree/master/owncloud).

Owncloud is a web based application providing services such as calendering data or file sharing e.g.
When we want to contain an application such as owncloud, there are a certain number of aspects to take in account and solve:
1. installing the application and its dependencies in the container
2. allow IP configuration for remote access to the application
3. allow data persistence at each invocation of the container
4. allow configuration data persistence at each invocation of the container
One possibility would be to run the container from an image and launch the various commands in the container (as we've done previously). We could put that in a script and launch it systematically when we instantiate a container from an image, or rebuild a prepared image to be instantiated later. But there is a better way to achieve what we want to do, and this is by using the automation process by docker with the dockerfile.

The dockerfile is a way to describe all the operations required to create an image from an initial empty one and stacking all the operations to build at the end the final image ready to be instantiated and consumed and thrown away
Let's start our Dockerfile by creating a simple container from a base image and just installing some software components useful for our environment, and build an image from that:

`#` **`cat > Dockerfile << EOF`**
```
FROM centos:6
RUN yum install -y httpd
EOF
```
`#` **`docker build .`**
```
Sending build context to Docker daemon  12.8 kB
Sending build context to Docker daemon 
Step 0 : FROM centos:6
 ---> a005304e4e74
Step 1 : RUN yum install -y httpd
 ---> Running in ec382fdf21bb
Loaded plugins: fastestmirror
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.2.15-39.el6.centos will be installed
--> Processing Dependency: httpd-tools = 2.2.15-39.el6.centos for package: httpd-2.2.15-39.el6.centos.x86_64
--> Processing Dependency: system-logos >= 7.92.1-1 for package: httpd-2.2.15-39.el6.centos.x86_64
--> Processing Dependency: apr-util-ldap for package: httpd-2.2.15-39.el6.centos.x86_64
--> Processing Dependency: /etc/mime.types for package: httpd-2.2.15-39.el6.centos.x86_64
--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.2.15-39.el6.centos.x86_64
--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.2.15-39.el6.centos.x86_64
--> Running transaction check
---> Package apr.x86_64 0:1.3.9-5.el6_2 will be installed
---> Package apr-util.x86_64 0:1.3.9-3.el6_0.1 will be installed
---> Package apr-util-ldap.x86_64 0:1.3.9-3.el6_0.1 will be installed
---> Package httpd-tools.x86_64 0:2.2.15-39.el6.centos will be installed
---> Package mailcap.noarch 0:2.1.31-2.el6 will be installed
---> Package redhat-logos.noarch 0:60.0.14-12.el6.centos will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==========================================================================
 Package             Arch         Version                 Repository  Size
==========================================================================
Installing:
 httpd               x86_64       2.2.15-39.el6.centos    base       825 k
Installing for dependencies:
 apr                 x86_64       1.3.9-5.el6_2           base       123 k
 apr-util            x86_64       1.3.9-3.el6_0.1         base        87 k
 apr-util-ldap       x86_64       1.3.9-3.el6_0.1         base        15 k
 httpd-tools         x86_64       2.2.15-39.el6.centos    base        75 k
 mailcap             noarch       2.1.31-2.el6            base        27 k
 redhat-logos        noarch       60.0.14-12.el6.centos   base        15 M

Transaction Summary
==========================================================================
Install       7 Package(s)

Total download size: 16 M
Installed size: 19 M
Downloading Packages:
--------------------------------------------------------------------------
Total                                           1.0 MB/s |  16 MB     00:15     
warning: rpmts_HdrFromFdno: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
Importing GPG key 0xC105B9DE:
 Userid : CentOS-6 Key (CentOS 6 Official Signing Key) <centos-6-key@centos.org>                                                                          
 Package: centos-release-6-6.el6.centos.12.2.x86_64 (@CentOS/$releasever)                                                                                 
 From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6                                                                                                           
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
Warning: RPMDB altered outside of yum.
  Installing : apr-1.3.9-5.el6_2.x86_64                                1/7 
  Installing : apr-util-1.3.9-3.el6_0.1.x86_64                         2/7 
  Installing : httpd-tools-2.2.15-39.el6.centos.x86_64                 3/7 
  Installing : apr-util-ldap-1.3.9-3.el6_0.1.x86_64                    4/7 
  Installing : mailcap-2.1.31-2.el6.noarch                             5/7 
  Installing : redhat-logos-60.0.14-12.el6.centos.noarch               6/7 
  Installing : httpd-2.2.15-39.el6.centos.x86_64                       7/7 
  Verifying  : httpd-2.2.15-39.el6.centos.x86_64                       1/7 
  Verifying  : httpd-tools-2.2.15-39.el6.centos.x86_64                 2/7 
  Verifying  : apr-util-ldap-1.3.9-3.el6_0.1.x86_64                    3/7 
  Verifying  : apr-1.3.9-5.el6_2.x86_64                                4/7 
  Verifying  : redhat-logos-60.0.14-12.el6.centos.noarch               5/7 
  Verifying  : mailcap-2.1.31-2.el6.noarch                             6/7 
  Verifying  : apr-util-1.3.9-3.el6_0.1.x86_64                         7/7 

Installed:
  httpd.x86_64 0:2.2.15-39.el6.centos                                           

Dependency Installed:
  apr.x86_64 0:1.3.9-5.el6_2                                                    
  apr-util.x86_64 0:1.3.9-3.el6_0.1                                             
  apr-util-ldap.x86_64 0:1.3.9-3.el6_0.1                                        
  httpd-tools.x86_64 0:2.2.15-39.el6.centos                                     
  mailcap.noarch 0:2.1.31-2.el6                                                 
  redhat-logos.noarch 0:60.0.14-12.el6.centos                                   

Complete!
 ---> 358657a2b6b0
Removing intermediate container ec382fdf21bb
Successfully built 358657a2b6b0
```
`#` **`docker images`**
```
REPOSITORY        TAG       IMAGE ID         CREATED          VIRTUAL SIZE
<none>            <none>    358657a2b6b0     3 minutes ago    274.8 MB
centos            6         a005304e4e74     12 days ago      203.1 MB
fedora            latest    ded7cd95e059     4 weeks ago      186.5 MB
hello-world       latest    91c95931e552     10 weeks ago     910 B
```

So we can verify that a new CentOS 6 image has been downloaded and based on it a new image has been created (without name nor tag, just an ID) containing httpd installed with its dependencies. Check it by instantiating a container based on that image and launching httpd in it:

`#` **`docker run -ti 358657a2b6b0 /bin/bash`**

`[root@babbfd33b239 /]#` **`httpd `**
```
httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.5 for ServerName
```
`[root@babbfd33b239 /]#` **`ps auxww |grep htt`**
```
root        14  0.0  0.0 175336  6360 ?        Ss   14:36   0:00 httpd
apache      15  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      16  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      17  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      18  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      19  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      20  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      21  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
apache      22  0.0  0.0 175336  3824 ?        S    14:36   0:00 httpd
root        24  0.0  0.0   9728  2128 ?        S+   14:37   0:00 grep htt
```
`[root@babbfd33b239 /]#` **`exit`**

`#` **`docker ps -a`**
```
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                      PORTS               NAMES
babbfd33b239        358657a2b6b0        "/bin/bash"         About a minute ago   Exited (0) 16 seconds ago                       sharp_hodgkin
[...]
```
`#` **`docker diff babbfd33b239`**
```
C /root
A /root/.bash_history
C /tmp
C /var
C /var/log
C /var/log/httpd
A /var/log/httpd/access_log
A /var/log/httpd/error_log
C /var/run
C /var/run/httpd
A /var/run/httpd/httpd.pid
```
`#` **`docker history 358657a2b6b0`**
```
IMAGE        CREATED        CREATED BY                            SIZE
358657a2b6b0 13 minutes ago /bin/sh -c yum install -y httpd       71.75 MB           
a005304e4e74 12 days ago    /bin/sh -c #(nop) CMD ["/bin/bash"]   0 B                 
fb9cc58bde0c 12 days ago    /bin/sh -c #(nop) ADD file:36d5dedfe  203.1 MB            
f1b10cd84249 10 weeks ago   /bin/sh -c #(nop) MAINTAINER The Cent 0 B
```

So we checked that we can launch the httpd server from inside an instantiated container made from our image. We also checked how our image was built. Note that the image built is 72 MB larger than the base CentOS 6 one (shown by history) and has sensible modifications shown by the diff command.
It's a good start, but now we would like to have our httpd server started automatically with our container creation. And have attribution accordingly ;-)

`#` **`cat >> Dockerfile << EOF`**
```
MAINTAINER myself@mydomain.org
CMD httpd
EOF
```
`#` **`docker build .`**
```
Sending build context to Docker daemon  12.8 kB
Sending build context to Docker daemon 
Step 0 : FROM centos:6
 ---> a005304e4e74
Step 1 : RUN yum install -y httpd
 ---> Using cache
 ---> 358657a2b6b0
Step 2 : MAINTAINER myself@mydomain.org
 ---> Running in dd5b4613f9ab
 ---> 1c93d00d212f
Removing intermediate container dd5b4613f9ab
Step 3 : CMD httpd
 ---> Running in b9fb6c35de95
 ---> 76cec1da7808
Removing intermediate container b9fb6c35de95
Successfully built 76cec1da7808
```

You can remark that all the first steps are very quick. This is because docker caches steps, and do not redo them as long as there is no change in the Dockerfile. You can modify the Docker file by putting the `MAINTAINER` command first and relaunch the build. You'll see that in that case docker invalidates its cache and restarts.
Now start a container from that image to check the web server is indeed started

`#` **`docker run -ti 76cec1da7808`**
```
httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.6 for ServerName
```
What happened ? Why don't you get a prompt ? Use `docker ps` to see the status of your container and `docker logs` to see what happened. Adapt the dockerfile to solve that issue. Discuss with your trainer if you're stuck !

Try to use a browser (you may want to install lynx) to connect to your web server. Can you do it ? Which IP address do you point to ? You may use `docker exec` to see that IP address.

By default, ports of the containers are not exposed outside of the container. So you can't use your localhost to access your isolated webserver. Check by reaching 127.0.0.1 from your browser. You need to teach that to your container:

`#` **`cat >> Dockerfile << EOF`**
```
EXPOSE 80
EOF
```
`#` **`perl -pi -e 's|D httpd|D /usr/sbin/apachectl -DFOREGROUND -k start|' Dockerfile`**

`#` **`docker build .`**
```
[...]
Successfully built 04d9c18da22a
```
`#` **`docker run -d -p 80:80 04d9c18da22a`**

`#` **`docker ps`**
```
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                NAMES
04d9c18da22a        c1c58f087482        "/bin/sh -c '/usr/sb   4 seconds ago       Up 3 seconds        0.0.0.0:80->80/tcp   thirsty_yalow
```

Now that we have exposed the port, we're able to launch our container in daemon mode (-d) and by redirecting the local port 80 to the container port 80 on which our web server is listening. Try now reaching again your webserver on localhost. You should see a CentOS based page on your Ubuntu distribution.

It's now time to add some content to our web server !
Modify again the Docker file to add owncloud to our image:

`#` **`cat >> Dockerfile << EOF`**
```
RUN yum install -y tar bzip2
ADD https://download.owncloud.org/community/owncloud-7.0.6.tar.bz2 /var/www/html/
RUN cd /var/www/html/ && tar xvfj owncloud-7.0.6.tar.bz2 && rm -f owncloud-7.0.6.tar.bz2
EOF
```
We can directly point to a URL, docker will download the content and extract it in place.
Try now to connect to your owncloud instance at http://localhost/owncloud. What happens ? What should you do next to solve the issue ? Hint, you probably need to add the owncloud dependencies to be able to launch it. Open your Dockerfile and add the following line after the last ADD

**`RUN yum install -y php php-dom php-mbstring php-pdo php-gd`**

With that you should be able to use owncloud ! (Note that you need to use that version with CentOS 6 for a PHP dependency management) But we're not done yet !!! 
If you log on to your owncloud instance, and start customizing it (login/passwd for admin, storage path), you'll have errors first, that we'll fix later on and then if you `docker stop` and `docker rm` the container to relaunch it again, of course, none of this customization will be kept as it's not part of your container content.

So we now have to deal with storage management for our docker container. First we need to solve the error generated when you tried to configure your owncloud instance. We had rights issues. Use the following command to help solve the issue:

`#` **`docker exec b42f9f6f1034 ls -al /var/www/html`**

`#` **`docker exec b42f9f6f1034 ps auxww | grep httpd`**

The principle is that the owner of the httpd process should have the rights on the owncloud directory to read and store files there. So modify you Dockerfile accordingly and retest.

Now you should be able to customize your owncloud instance and start using it.
By now you have probably remarked that the ADD order is done each time, without any benefit from the cache management of Docker. Also you have to each time deal with IDs for containers and images, which is not that convenient. Let's fix that. Download the owncloud tar file in your directory and modify the ADD line:

`#` **`wget https://download.owncloud.org/community/owncloud-7.0.6.tar.bz2`**

`#` **`perl -pi -e 's|ADD https://download.owncloud.org/community/owncloud-7.0.6.tar.bz2|COPY owncloud-7.0.6.tar.bz2|' Dockerfile`**

`#` **`docker build .`**

`#` **`docker build -t owncloud .`**

Next time you re-run the build, the cache effect is optimal. Also you now have tagged your image and use it by its name:

`#` **`docker images`**
```
REPOSITORY      TAG        IMAGE ID       CREATED             VIRTUAL SIZE
owncloud        latest     de9663de44b2   7 minutes ago       568.5 MB
```

Now what would be great, would be to save our content from one run to another in order to find it again right ? And yes, you can ;-) For that, you need to attach a local directory of your host to your container, and point the setup of your owncloud to that directory instead of the one under `/var/www/html/owncloud`.
Create a `/data` directory on your host, mount it in your container under `/data`, and then point your setup ot it:

`#` **`mkdir -p /data`**

`#` **`date > /data/myfile`**

`#` **`cat >> Dockerfile << EOF`**
```
VOLUME /data
EOF
```
`#` **`docker build -t owncloud .`**

`#` **`docker ps `**
```
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                NAMES
29c8f5ca3d76        de9663de44b2        "/bin/sh -c '/usr/sb   18 minutes ago      Up 18 minutes       0.0.0.0:80->80/tcp   prickly_jang
```
`#` **`docker stop 29c8f5ca3d76 `**

`#` **`docker rm 29c8f5ca3d76`**

`#` **`docker run -d -p 80:80 -v /data:/data owncloud:latest`**

Now relaunch your browser to reconfigure again owncloud, but this time configure the data folder as in the following screen shot:

If you encounter issues you need to adapt your Dockerfile so that the apache user is allowed to write on to the /data directory. Your current Dockerfile should look like this at that point:

`#` **`cat Dockerfile`**
```
FROM centos:6
#FROM fedora:latest
RUN yum install -y httpd
MAINTAINER myself@mydomain.org
RUN yum install -y tar bzip2
COPY owncloud-7.0.6.tar.bz2 /var/www/html/
RUN cd /var/www/html/ && tar xvfj owncloud-7.0.6.tar.bz2 && rm -f owncloud-7.0.6.tar.bz2
RUN yum install -y php php-dom php-mbstring php-pdo php-gd
VOLUME /data
RUN chown -R apache:apache /var/www/html/owncloud /data
CMD /usr/sbin/apachectl -DFOREGROUND -k start
EXPOSE 80
```
`#` **`mv /data/myfile /data/bruno/files/`**

`#` **`docker ps`**
```
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                NAMES
23fb18adf4f7        owncloud:latest     "/bin/sh -c '/usr/sb   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp   modest_lalande
```
`#` **`docker stop 23f`**
```
23f          
```
`#` **`docker rm 23f`**
```
23f
```
`#` **`docker run -d -p 80:80 -v /data:/data owncloud:latest`**
```
cca4a1776ef12b256616e69a29753202efe0b1af5dd64fecfb638d2a797b234e
```

At that point you should find again your data on your owncloud instance right ? But what additional pain point do you have ?
Knowing that the owncloud configuration data are located under `/var/www/html/owncloud/config/config.php` adapt the Dockerfile to solve that last issue.

# Using Docker compose
TBD
