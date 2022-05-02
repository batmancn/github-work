# 什么问题

As ofpbuf_put_uninit will realloc memory when _alloc_memory is not enough, so return pointer will be change.

# patch

https://mail.openvswitch.org/pipermail/ovs-discuss/2016-January/039755.html