# force IPv4 on windows

* change regedit

  `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters\`
  
  DWORD key `DisabledComponents`
  
  value 0xff(force disable IPv6) or 0x20(prefer IPv4)