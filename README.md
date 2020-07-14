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
~~~



