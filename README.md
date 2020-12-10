# Tanzu with HAproxy with 3 Networks

vCenter with Kubernetes and HAproxy can be set up with 2 network (default) and/or 3 networks (more production like).
This guide will focus on the 3 network set up. 

The over all steps are (provided the vCenter and ESXi(x3) hosts are up and running including shared storage) loading the HAproxy appliance 
into vCenter (https://github.com/haproxytech/vmware-haproxy/releases) and then enableling in vCenter the kubernetes option. 
The quick start guide is here: https://core.vmware.com/resource/vsphere-tanzu-quick-start-guide#section1


**Setup HAproxy**

**vCenter enablement**

**Trouble shooting**

very rough notes will clean up soon......

```
HAproxy trouble shooting (HA-proxy)
------------------------------------
grep -F '192.168.4.128' /etc/haproxy/haproxy.cfg

ip r (Routing table)
cat /etc/iproute2/rt_tables (get routing number 2 trough 252)


echo "4	workload" >>/etc/iproute2/rt_tables
echo "5	frontend" >>/etc/iproute2/rt_tables

ip r add default via 192.168.3.1 table workload
ip r add default via 192.168.4.1 table frontend

ip rule add from 192.168.3.0/24 lookup workload
ip rule add from 192.168.4.0/24 lookup frontend

ip rule
ip r show table main

The vip file
------------
cat /etc/vmware/anyip-routes.cfg   
ls /etc/systemd/network #you can see the .network files for each interface and verify
cat /etc/systemd/network/*.network

journalctl -xeu haproxy
journalctl -u anyip-routes


Here is my way to do a real time watch what is going and what vSphere has done in that process:
Get the root password to login the supervisor vms, which is run command from vc as root /usr/lib/vmware-wcp/decryptK8Pwd.py
SSH login the api ip ( the start ip you enter into the enable process”) as root, and tail-f /var/log/container/* you will see all realtime activities. If there is something wrong, you will able to see it happening and repeating ...
that’s all. This works for both NSX and HAproxy mode (edited) 

/var/log/vmware/wcp/wcpsvc.log
/usr/lib/vmware-wcp/decryptK8Pwd.py


- On the supervisor vm test with nslookup if vCenter.domain.com resolved forward and reverse!

Trouble shooting guide
https://github.com/haproxytech/vmware-haproxy/blob/master/docs/virtual-ip-config.md


As an FYI there is also a nice curl command to run from the SC to validate you can talk to HA proxy correclty
curl -k -X GET --user 'wcp:VMware1!' -H "Content-Type: application/json" https://172.17.31.116:5556/v2

Look for errors

/var/log/vmware-imc/ 
/var/log/cloud-init/*?

```



