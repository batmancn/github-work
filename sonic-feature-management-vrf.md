# 什么问题

在SONiC中增加管理网VRF功能

# PR

https://github.com/Azure/sonic-buildimage/pull/2411

# PR说明

- What I did
Implement management vrf function follow [this spec](https://github.com/Azure/SONiC/blob/310de5a9d649481e23ec3b048cbe90242c6e063f/mgmt-vrf-design-doc.md), phase 1.

- How I did it
I use DB to set management vrf.
I use 'interfaces-config.sh' to trigger network restart.
For details, refer to 'How to verify it' section.

- How to verify it
  - use default DB settings to restart SONiC
  - use these steps to add management vrf in DB
```
# cat /root/mgmt-vrf.del.json
{
    "MGMT_INTERFACE": {
        "eth0|172.18.8.211/24": null
    }
}

# config load -y /root/mgmt-vrf.del.json

# cat /root/mgmt-vrf.add.json
{
    "MGMT_INTERFACE": {
        "eth0|172.18.8.211/24": {
            "gwaddr": "172.18.8.1",
            "vrfname": "mgmtvrf"
        }
    }
}

# config load -y /root/mgmt-vrf.add.json
```
  - use this command to restart network and set management vrf
```
bash /usr/bin/interfaces-config.sh
```
  - use this command to verify
```
# ip route show table 10
default via 172.18.8.1 dev eth0
broadcast 172.18.8.0 dev eth0 proto kernel scope link src 172.18.8.211
172.18.8.0/24 dev eth0 proto kernel scope link src 172.18.8.211
local 172.18.8.211 dev eth0 proto kernel scope host src 172.18.8.211
broadcast 172.18.8.255 dev eth0 proto kernel scope link src 172.18.8.211

# ip route show
10.0.0.2/31 dev Ethernet4 proto kernel scope link src 10.0.0.2 linkdown
10.0.0.8/31 dev Ethernet16 proto kernel scope link src 10.0.0.8 linkdown
240.127.1.0/24 dev docker0 proto kernel scope link src 240.127.1.1 linkdown
```
  - use these steps to del management vrf in DB
```
# cat /root/mgmt-vrf.del.json
{
    "MGMT_INTERFACE": {
        "eth0|172.18.8.211/24": null
    }
}

# config load -y /root/mgmt-vrf.del.json

# cat /root/mgmt-vrf.old.json
{
    "MGMT_INTERFACE": {
        "eth0|172.18.8.211/24": {
            "gwaddr": "172.18.8.1"
        }
    }
}

# config load -y /root/mgmt-vrf.old.json
```
  - use this command to restart network and set management vrf
```
bash /usr/bin/interfaces-config.sh
```