# LXD - Installation: 

## Get ready your **CENTOS** vm 
### Once it is ready loged into the VM [ssh root@centos_VM]
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
~~~

~~~
[root@haproxy ~]# lxc image list
+-------+--------------+--------+---------------------------------+--------------+-----------+---------+-------------------------------+
| ALIAS | FINGERPRINT  | PUBLIC |           DESCRIPTION           | ARCHITECTURE |   TYPE    |  SIZE   |          UPLOAD DATE          |
+-------+--------------+--------+---------------------------------+--------------+-----------+---------+-------------------------------+
|       | eeecaeac13c8 | no     | Centos 7 amd64 (20200714_07:08) | x86_64       | CONTAINER | 83.16MB | Jul 14, 2020 at 10:35am (UTC) |
+-------+--------------+--------+---------------------------------+--------------+-----------+---------+-------------------------------+
[root@haproxy ~]# 
~~~

~~~
lxc list
~~~

~~~
[root@haproxy ~]# lxc list
+---------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
|  NAME   |  STATE  |         IPV4          |                     IPV6                      |   TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
| kmaster | RUNNING | 10.223.187.191 (eth0) | fd42:66b0:5417:7ad2:216:3eff:fe5e:ff25 (eth0) | CONTAINER | 0         |
+---------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
[root@haproxy ~]# 
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

### Create 2 new containers: 

~~~
[root@haproxy ~]# lxc launch images:centos/7/amd64 kworker-1
Creating kworker-1
Starting kworker-1                        
[root@haproxy ~]# 
~~~

~~~
[root@haproxy ~]# time lxc launch images:centos/7/amd64 kworker-2
Creating kworker-2
Starting kworker-2                        

real	0m10.555s
user	0m0.071s
sys	0m0.168s
[root@haproxy ~]# 
~~~

#### ^^ It took only 10 ms to launch the container. 

~~~
[root@haproxy ~]# lxc list
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
|   NAME    |  STATE  |         IPV4          |                     IPV6                      |   TYPE    | SNAPSHOTS |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
| kmaster   | RUNNING | 10.223.187.191 (eth0) | fd42:66b0:5417:7ad2:216:3eff:fe5e:ff25 (eth0) | CONTAINER | 0         |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
| kworker-1 | RUNNING | 10.223.187.203 (eth0) | fd42:66b0:5417:7ad2:216:3eff:fe92:8780 (eth0) | CONTAINER | 0         |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
| kworker-2 | RUNNING | 10.223.187.244 (eth0) | fd42:66b0:5417:7ad2:216:3eff:fe24:a8f6 (eth0) | CONTAINER | 0         |
+-----------+---------+-----------------------+-----------------------------------------------+-----------+-----------+
[root@haproxy ~]# 
~~~

#### Check if they are pingable or not. 

~~~
[root@haproxy ~]# for i in $(lxc list| awk {'print $6'} | awk 'NR >2'); do ping -c2 $i ; done 
PING 10.223.187.191 (10.223.187.191) 56(84) bytes of data.
64 bytes from 10.223.187.191: icmp_seq=1 ttl=64 time=0.102 ms
64 bytes from 10.223.187.191: icmp_seq=2 ttl=64 time=0.130 ms

--- 10.223.187.191 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.102/0.116/0.130/0.014 ms
PING 10.223.187.203 (10.223.187.203) 56(84) bytes of data.
64 bytes from 10.223.187.203: icmp_seq=1 ttl=64 time=0.065 ms
64 bytes from 10.223.187.203: icmp_seq=2 ttl=64 time=0.098 ms

--- 10.223.187.203 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.065/0.081/0.098/0.018 ms
PING 10.223.187.244 (10.223.187.244) 56(84) bytes of data.
64 bytes from 10.223.187.244: icmp_seq=1 ttl=64 time=0.153 ms
64 bytes from 10.223.187.244: icmp_seq=2 ttl=64 time=0.136 ms

--- 10.223.187.244 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.136/0.144/0.153/0.014 ms
[root@haproxy ~]# 
~~~

#### Try to access them: [kmaster]

~~~
[root@haproxy ~]# lxc exec kmaster bash
[root@kmaster ~]# 
[root@kmaster ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
4: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:16:3e:5e:ff:25 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.223.187.191/24 brd 10.223.187.255 scope global dynamic eth0
       valid_lft 3059sec preferred_lft 3059sec
    inet6 fd42:66b0:5417:7ad2:216:3eff:fe5e:ff25/64 scope global mngtmpaddr dynamic 
       valid_lft 3447sec preferred_lft 3447sec
    inet6 fe80::216:3eff:fe5e:ff25/64 scope link 
       valid_lft forever preferred_lft forever
[root@kmaster ~]# 
~~~

~~~
[root@kmaster ~]# ping -c2 google.com
PING google.com (172.217.166.46) 56(84) bytes of data.
64 bytes from bom07s18-in-f14.1e100.net (172.217.166.46): icmp_seq=1 ttl=112 time=76.7 ms
64 bytes from bom07s18-in-f14.1e100.net (172.217.166.46): icmp_seq=2 ttl=112 time=85.1 ms

--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 76.758/80.929/85.100/4.171 ms
[root@kmaster ~]# 
~~~

~~~
[root@kmaster ~]# cat /etc/*release
CentOS Linux release 7.8.2003 (Core)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.8.2003 (Core)
CentOS Linux release 7.8.2003 (Core)
[root@kmaster ~]# 
~~~








