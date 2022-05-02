https://github.com/Azure/sonic-buildimage/pull/3354

**- What I did **

This PR brings the functionality of being able to handle BGP traps in sonic-frr.
For detail of how to use, I give a [demo](https://github.com/batmancn/sonic-buildimage/tree/feature/enable-snmp-trap-support-demo) that use BGP traps to track BGP peer status, and set into APPL_DB.

**- How to verify **

I test my demo like this:
1. use this PR
2. create BGP peer
3. up/down BGP peer, and check syslog

```
# router bgp 65003
# neighbor 192.168.10.9 shutdown
# no neighbor 192.168.10.9 shutdown

Aug 16 04:42:03.483442 ... INFO snmp#bgp-peer-status: [bgp-peer-status] setDbPeerStatus: key = BGP_NEIGHBOR:192.168.10.9, status = Idel
Aug 16 04:45:59.367653 ... INFO snmp#bgp-peer-status: [bgp-peer-status] setDbPeerStatus: key = BGP_NEIGHBOR:192.168.10.9, status = Established
```