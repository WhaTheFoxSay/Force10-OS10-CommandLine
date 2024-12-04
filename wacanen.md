# Force10 OS10- Command Line Interfaces
### Membuka Dasaran Warung (Konfigurasi awal) dari perangkat Dell Force10 OS10.
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________

Repo ini sebelumnya bersifat private karena beberapa kali saya gunakan sebagai dokumentasi hasil oprekan saya dengan jaringan lokal.

Saat ini beberapa hal yang menyangkut sudah bersih dan aman untuk di publis.

Tujuan utamanya ialah sebagai bahan refrensi apabila teman teman mebutuhkan beberapa pengetahuan terkait konfigurasi dari perangkat Dell Force10 OS10 ini.

#### First of all, thank you for my office [Wownet] where I chat and do lots of things that make me more knowledgeable :D

_______________________________________________________________
_______________________________________________________________
_______________________________________________________________
_______________________________________________________________

### Cara set nama host untuk perangkat
```
OS10# configure terminal
OS10(config)# hostname [Tulis nama hostname yang akan diset Contoh Warkop-Wownet1]
Warkop-Wownet1(config)# end
Warkop-Wownet1# write memory
```
* * *
### Cara enable service SSH atau Telnet
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# ip ssh server enable
Warkop-Wownet1(config)# end
 
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# ip telnet server enable
Warkop-Wownet1(config)# end
 
Warkop-Wownet1# write memory
```
* * *
### Cara disable Service SSH atau Telnet
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# no ip ssh server enable
Warkop-Wownet1(config)# end
 
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# no ip telnet server enable
Warkop-Wownet1(config)# end

Warkop-Wownet1# write memory
```
* * *
## Konfigurasi POOL

#### Dynamic
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# ip dhcp server
Warkop-Wownet1(config-dhcp)# pool [nama-pool]
Warkop-Wownet1(config-dhcp-D-POOL)# network [Network/SubnetMask]
Warkop-Wownet1(config-dhcp-D-POOL)# default-router [Default Router IP]
Warkop-Wownet1(config-dhcp-D-POOL)# dns-server [DNS server IP]
```
* * *

#### Static
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# ip dhcp server
Warkop-Wownet1(config-dhcp)# pool [nama-pool]
Warkop-Wownet1(config-dhcp-S-POOL)# host-address [IP-Address]
Warkop-Wownet1(config-dhcp-S-POOL)# hardware-address [Mac-Address]
Warkop-Wownet1(config-dhcp-S-POOL)# default-router [Default Router IP]
Warkop-Wownet1(config-dhcp-S-POOL)# dns-server [DNS server IP]
```
* * *
### Mengaktifkan DHCP SERVER
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# ip dhcp server
Warkop-Wownet1(config-dhcp)# no disable
```
* * *
## Konfigurasi Port-Channels sebagai Link-Agregations [LAG]

Kita memakai 2 buah Switch yang sama (disini kita pakai DELL S-6000 sebagai Redudancy untuk konfigurasi dan memakai port 3 dan port 4 pada kedua switch.
Port-Channel yang kita gunakan mengunakan "7")

##### Pada Switch pertama kita konfigurasikan seperti ini
```
DELL-Warkop-1# configure terminal
DELL-Warkop-1(config)# interface port-channel 7
DELL-Warkop-1(conf-if-po-7)# exit
DELL-Warkop-1(config)# interface range ethernet 1/1/3-1/1/4
DELL-Warkop-1(conf-range-eth1/1/3-1/1/4)# channel-group 7 mode active 
DELL-Warkop-1(conf-range-eth1/1/3-1/1/4)# end
DELL-Warkop-1# show interface port-channel summary 
LAG     Mode      Status    Uptime              Ports
7       L2        up        00:00:28            Eth 1/1/3 (Up)
                                                Eth 1/1/4 (Up)

```

##### Sama halnya dengan switch pertama, switch kedua kita konfigurasikan seperti ini
```
DELL-Warkop-2# configure terminal
DELL-Warkop-2(config)# interface port-channel 7
DELL-Warkop-2(conf-if-po-7)# exit
DELL-Warkop-2(config)# interface range ethernet 1/1/3-1/1/4
DELL-Warkop-2(conf-range-eth1/1/3-1/1/4)# channel-group 7 mode active 
DELL-Warkop-2(conf-range-eth1/1/3-1/1/4)# end
DELL-Warkop-2# show interface port-channel summary 
LAG     Mode      Status    Uptime              Ports
7       L2        up        00:00:56            Eth 1/1/3 (Up)
                                                Eth 1/1/4 (Up)

```

* * *
## Membuat VLAN.
Langkah awal, kita verifikasi dahulu mengecek apakah vlan yang akan kita buat sudah ada atau belum dengan cara kita show list dari vlan.
Sebagai contoh kita akan membuat vlan 90.

```
Warkop-Wownet1# show vlan
Codes: * - Default VLAN, M - Management VLAN, R - Remote Port Mirroring VLANs,
       @ - Attached to Virtual Network, P - Primary, C - Community, I - Isolated,
       S - VLAN-Stack VLAN
Q: A - Access (Untagged), T - Tagged
    NUM    Status    Description                     Q Ports
*   1      Active                                    A Eth1/1/1-1/1/32 

```
Setelah melihat list dan vlan belum dibuat, kita bisa lanjut ke tahap selanjutnya.
```
Warkop-Wownet1# configure terminal 
Warkop-Wownet1(config)# interface vlan 90
Warkop-Wownet1(conf-if-vl-90)# end
```
Kita verifikasikan kembali vlan yang sudah kita buat dengan cara show vlan
```
Warkop-Wownet1# show vlan
Codes: * - Default VLAN, M - Management VLAN, R - Remote Port Mirroring VLANs,
       @ - Attached to Virtual Network, P - Primary, C - Community, I - Isolated,
       S - VLAN-Stack VLAN
Q: A - Access (Untagged), T - Tagged
    NUM    Status    Description                     Q Ports
*   1      Active                                    A Eth1/1/1-1/1/32 
    90     Inactive     
```
### Membuat Range VLAN.
Sama seperti sebelumnya, hanya saja ada tambahan command yang akan kita buat.
Sebagai contoh kita akan membuat vlan 21, 22, 23 dan 25 secara bersamaan.
Lalu diakhir langkah kita akan show vlan kembali untuk verifikasi.
```
Warkop-Wownet1# configure terminal 
Warkop-Wownet1(config)# interface range vlan 21-23,25
Warkop-Wownet1(conf-range-vl-21-23,25)# end
Warkop-Wownet1# show vlan
Codes: * - Default VLAN, M - Management VLAN, R - Remote Port Mirroring VLANs,
       @ - Attached to Virtual Network, P - Primary, C - Community, I - Isolated,
       S - VLAN-Stack VLAN
Q: A - Access (Untagged), T - Tagged
    NUM    Status    Description                     Q Ports
*   1      Active                                    A Eth1/1/1-1/1/32 
    21     Inactive                                  
    22     Inactive                                  
    23     Inactive                                  
    25     Inactive                                  
    90     Inactive       
 ```



* * *
## Konfigurasi VLAN Mode Trunk pada Interface.
Sebagai contoh disini kita akan memmbuat sebuah port interface menjadi mode trunk, kita mabil contoh port 7 akan kita set mode trunk dengan vlan 21 dan 25 yang akan kita luruskan ke Mikrotik-CHR warkop dengan VLAN-ID yang sama.

```
Warkop-Wownet1# show interface status
--------------------------------------------------------------------------------------------------
Port            Description     Status   Speed    Duplex   Mode Vlan Tagged-Vlans
--------------------------------------------------------------------------------------------------
Eth 1/1/1                       up       40G      full     T    1    1234
Eth 1/1/2                       down     0        full     A    1    -
Eth 1/1/3                       down     0        full     A    1    -
Eth 1/1/4                       down     0        full     A    1    -
Eth 1/1/5                       down     0        full     A    1    -
Eth 1/1/6                       down     0        full     A    1    -
Eth 1/1/7                       down     0        full     A    1    -
Eth 1/1/8                       up       40G      full     A    1    -
Eth 1/1/9                       up       40G      full     A    1    -
Eth 1/1/10                      down     0        full     A    1    -
Eth 1/1/11                      down     0        full     A    1    -
Eth 1/1/12                      down     0        full     A    1    -
Eth 1/1/13                      down     0        full     A    1    -
Eth 1/1/14                      down     0        full     A    1    -
Eth 1/1/15                      down     0        full     A    1    -
Eth 1/1/16                      down     0        full     A    1    -
Eth 1/1/17                      down     0        full     A    1    -
Eth 1/1/18                      down     0        full     A    1    -
Eth 1/1/19                      down     0        full     A    1    -
Eth 1/1/20                      down     0        full     A    1    -
Eth 1/1/21                      down     0        full     A    1    -
Eth 1/1/22                      down     0        full     A    1    -
Eth 1/1/23                      down     0        full     A    1    -
Eth 1/1/24                      down     0        full     A    1    -
Eth 1/1/25                      down     0        full     A    1    -
Eth 1/1/26                      down     0        full     A    1    -
Eth 1/1/27                      down     0        full     A    1    -
Eth 1/1/28                      down     0        full     A    1    -
Eth 1/1/29                      down     0        full     A    1    -
Eth 1/1/30                      down     0        full     A    1    -
Eth 1/1/31                      down     0        full     A    1    -
Eth 1/1/32                      down     0        full     A    1    -
--------------------------------------------------------------------------------------------------
```

Dari show data diatas kita lihat port 7 dalam mode Akses Vlan-1 yang mana itu adalah defconf untuk keseluruhan port.
Kita lalu masuk ke interface dari port 7.
* * *
```
Warkop-Wownet1# configure terminal
Warkop-Wownet1(config)# interface ethernet 1/1/7
Warkop-Wownet1(conf-if-eth1/1/7)# switchport mode trunk
Warkop-Wownet1(conf-if-eth1/1/7)# switchport trunk allowed vlan 21,25
Warkop-Wownet1(conf-if-eth1/1/7)# switchport access vlan 1
Warkop-Wownet1(conf-if-eth1/1/7)# end
```
Setelah itu kita bisa verifikasi melalui show interfaces untuk melihat perubahan status pada port7
```
Warkop-Wownet1# show interface status

--------------------------------------------------------------------------------------------------
Port            Description     Status   Speed    Duplex   Mode Vlan Tagged-Vlans
--------------------------------------------------------------------------------------------------
Eth 1/1/1                       up       40G      full     A    1    -
Eth 1/1/2                       down     0        full     A    1    -
Eth 1/1/3                       down     0        full     A    1    -
Eth 1/1/4                       down     0        full     A    1    -
Eth 1/1/5                       down     0        full     A    1    -
Eth 1/1/6                       down     0        full     A    1    -
Eth 1/1/7                       down     0        full     T    1    21,25
Eth 1/1/8                       up       40G      full     A    1    -
Eth 1/1/9                       up       40G      full     A    1    -
Eth 1/1/10                      down     0        full     A    1    -
Eth 1/1/11                      down     0        full     A    1    -
Eth 1/1/12                      down     0        full     A    1    -
Eth 1/1/13                      down     0        full     A    1    -
Eth 1/1/14                      down     0        full     A    1    -
Eth 1/1/15                      down     0        full     A    1    -
Eth 1/1/16                      down     0        full     A    1    -
Eth 1/1/17                      down     0        full     A    1    -
Eth 1/1/18                      down     0        full     A    1    -
Eth 1/1/19                      down     0        full     A    1    -
Eth 1/1/20                      down     0        full     A    1    -
Eth 1/1/21                      down     0        full     A    1    -
Eth 1/1/22                      down     0        full     A    1    -
Eth 1/1/23                      down     0        full     A    1    -
Eth 1/1/24                      down     0        full     A    1    -
Eth 1/1/25                      down     0        full     A    1    -
Eth 1/1/26                      down     0        full     A    1    -
Eth 1/1/27                      down     0        full     A    1    -
Eth 1/1/28                      down     0        full     A    1    -
Eth 1/1/29                      down     0        full     A    1    -
Eth 1/1/30                      down     0        full     A    1    -
Eth 1/1/31                      down     0        full     A    1    -
Eth 1/1/32                      down     0        full     A    1    -
--------------------------------------------------------------------------------------------------
```

## Penerapan FCoE (FIP Snooping) Dell Force10 MXL

```
Warkop-Wownet1#
configure
default vlan-id 20
interface TenGigabit
Ethernet 0/1no ip address
mtu 2500
portmode hybrid
switchport
protocol lldp
dcbx port-role auto-downstream
spanning-tree pvst edge-port
no shutdown
exit

interface TenGigabitEthernet 0/2
no ip address
mtu 2500
portmode hybrid
switchport
protocol lldp
dcbx port-role auto-downstream
spanning-tree pvst edge-portno shutdown
exit

interface TenGigabitEthernet 0/51
no ip address
mtu 2500port-channel-protocol LACP
port-channel 5 mode active
protocol lldp
no advertise dcbx-tlv ets-reco
dcbx port-role auto-upstream
no shutdown
exit

interface TenGigabitEthernet 0/52
no ip addressmtu 2500
port-channel-protocol LACP
port-channel 5 mode active
protocol lldp
no advertise dcbx-tlv ets-reco
dcbx port-role auto-upstream
no shutdown
exit
```
