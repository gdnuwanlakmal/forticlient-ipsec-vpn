# Configuring an IPsec VPN connection
## Create User Group for Tunnel
```shell
config user group
edit "new-user-group"
    set member "vpnuser1" "vpnuser2"   # Add real users here
next
end
```

## Phase 1 and 2 for Tunnel 2
```shell
config vpn ipsec phase1-interface
edit "ipsec-newgroup"
    set type dynamic
    set interface "port28"
    set mode aggressive
    set peertype any
    set net-device disable
    set mode-cfg enable
    set proposal aes128-sha256 aes256-sha256 aes128-sha1 aes256-sha1
    set comments "VPN: ipsec-newgroup (Created for second user group)"
    set wizard-type dialup-forticlient
    set xauthtype auto
    set authusrgrp "new-user-group"
    set ipv4-start-ip 192.168.184.1
    set ipv4-end-ip 192.168.184.10
    set dns-mode auto
    set ipv4-split-include "ipsec-new_split"
    set save-password enable
    set psksecret <INSERT_NEW_ENCRYPTED_PSK>   # Replace with new PSK
next
end

config vpn ipsec phase2-interface
edit "ipsec-newgroup"
    set phase1name "ipsec-newgroup"
    set proposal aes128-sha1 aes256-sha1 aes128-sha256 aes256-sha256 aes128gcm aes256gcm chacha20poly1305
    set comments "VPN: ipsec-newgroup"
next
end
```

## Create Firewall Policy
### Allow traffic from VPN to internal network:
```shell
config firewall policy
edit 0
    set name "VPN_NewGroup_Access"
    set srcintf "ipsec-newgroup"
    set dstintf "lan"
    set srcaddr "all"
    set dstaddr "Internal_Network"     # Use correct address object or subnet
    set action accept
    set schedule "always"
    set service "ALL"
    set nat enable
next
end
```
