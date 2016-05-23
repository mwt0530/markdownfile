##capability

```
pI_new = pI
pP_new = (X&fP) | (pI & fI) 
pE_new = fE ? pP_new : 0 
```

- pam_cap.so
设置pam
pam_cap only manipulate the inheritable capabilities (so it 
does not actually grant any permitted/effective capability 
at all，it also only deals with users and not groups (not even primary groups).

----

effective通过capset()获取permitted包含的能力
- Unless pE contains CAP_SETPCAP, Linux will
only allow a process to add a capabilities to pI that are
present in pP. No special privilege is required to remove
capabilities from pI.
- a process’ permitted set can receive
capabilities which are not in its bounding set, so long as
the capabilities are present in both f I and pI.
