# LXD - Installation: 

## Get ready your **CENTOS** vm 

~~~
yum update -y 
yum install epel-release.noarch -y 
yum update -y 
yum clean all
~~~

~~~
cat /etc/redhat-release 
~~~

~~~
yum install yum-plugin-copr
yum copr enable ngompa/snapcore-el7 -y
yum install snapd -y
~~~

~~~
systemctl enable --now snapd.socket
~~~

~~~
grubby --args="user_namespace.enable=1" --update-kernel="$(grubby --default-kernel)"
grubby --args="namespace.unpriv_enable=1" --update-kernel="$(grubby --default-kernel)"
sudo sh -c 'echo "user.max_user_namespaces=3883" > /etc/sysctl.d/99-userns.conf'
~~~

~~~
reboot
~~~

~~~
snap install lxd -y
snap install lxd 
~~~

~~~
lxc list 
lxd init
~~~

~~~
lxc image list images:
lxc image list images: | grep -i "Centos 7 amd64"
~~~

~~~
lxc launch images:centos/7/amd64 kmaster
lxc list
~~~

~~~
[root@haproxy ~]# lxc info kmaster
~~~

~~~
Name: kmaster
Location: none
Remote: unix://
Architecture: x86_64
Created: 2020/07/14 10:35 UTC
Status: Running
Type: container
Profiles: default
Pid: 2338
Ips:
  eth0:	inet	10.223.187.191	veth06f70a53
  eth0:	inet6	fd42:66b0:5417:7ad2:216:3eff:fe5e:ff25	veth06f70a53
  eth0:	inet6	fe80::216:3eff:fe5e:ff25	veth06f70a53
  lo:	inet	127.0.0.1
  lo:	inet6	::1
Resources:
  Processes: 11
  CPU usage:
    CPU usage (in seconds): 2
  Memory usage:
    Memory (current): 26.86MB
    Memory (peak): 57.03MB
  Network usage:
    lo:
      Bytes received: 0B
      Bytes sent: 0B
      Packets received: 0
      Packets sent: 0
    eth0:
      Bytes received: 9.83kB
      Bytes sent: 3.52kB
      Packets received: 81
      Packets sent: 37
[root@haproxy ~]# 
~~~




