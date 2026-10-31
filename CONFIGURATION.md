# ⚙️ CONFIGURATION.md - Advanced Lab OSPF HQ-Branch + Multi-SSID + ACL

> **Topologi:** HEAD OFFICE (HQ) SITE <-> BRANCH OFFICE SITE  
> **Routing:** OSPF Area 0 Single Area - P2P Link 10.10.10.0/30  
> **File:** `OSPF-HQ-Branch-ACL.pkt`

---

### 1. IP Address & VLAN Table 

| Site | VLAN | ID Jaringan | Gateway (Router) | DHCP Pool | Device di Topologi |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **HQ** | 10 | 192.168.10.0/24 | 192.168.10.1 | 192.168.10.0 | PC-PT HQ-PC 1 |
| **HQ** | 99 | 192.168.99.0/24 | 192.168.99.1 | 192.168.99.0 | Server-PT SRV-DHCP |
| **P2P Link** | - | 10.10.10.0/30 | - | - | HQ-Router G0/1 ( .1 ) <-> Branch-Router G0/1 ( .2 ) |
| **Branch** | 20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.0 | PC-PT Branch-PC1 |
| **Branch** | 30 | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.0 | AP-Branch 1 Wi-Fi Karyawan + Laptop-PT Corp-Laptop |
| **Branch** | 40 | 192.168.40.0/24 | 192.168.40.1 | 192.168.40.0 | AP-Branch 2 Wi-Fi Tamu + SMARTPHONE-PT Guest-Phone |

### 2. Konfigurasi Switch

**2960 HQ Switch (HQ-SW)**
```CISCO
enable
conf t
vlan 10
 name HQ-PC
vlan 99
 name SRV-DHCP
exit

interface range f0/1-2
 switchport mode access
interface f0/1
 switchport access vlan 99
interface f0/2
 switchport access vlan 10

interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,99

**2960 Branch Switch (Branch-SW)**
```CISCO
enable
conf t
vlan 20
 name Branch-PC
vlan 30
 name WiFi-Karyawan
vlan 40
 name WiFi-Tamu
exit

interface f0/1
 switchport mode access
 switchport access vlan 20
interface f0/2
 switchport mode access
 switchport access vlan 30
interface f0/3
 switchport mode access
 switchport access vlan 40

interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 20,30,40
interface g0/2
 switchport mode trunk
 ! Trunk ke Router Branch
```
### 3. Konfigurasi Router HQ (2911 - HQ-Router)
```CISCO
enable
 conf t
hostname HQ-Router

! Inter-VLAN
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
interface g0/0.99
 encapsulation dot1Q 99
 ip address 192.168.99.1 255.255.255.0

! Link OSPF ke Branch
interface g0/1
 ip address 10.10.10.1 255.255.255.252
 no shutdown

! OSPF Area 0
router ospf 1
 router-id 1.1.1.1
 network 192.168.10.0 0.0.0.255 area 0
 network 192.168.99.0 0.0.0.255 area 0
 network 10.10.10.0 0.0.0.3 area 0
end
wr
```
### 4. Konfigurasi Router Branch (2911 - Branch-Router)
```CISCO
enable
conf t
hostname Branch-Router

interface g0/0
 no shutdown
interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 ip helper-address 192.168.99.10
interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 ip helper-address 192.168.99.10
interface g0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
 ip helper-address 192.168.99.10

interface g0/1
 ip address 10.10.10.2 255.255.255.252
 no shutdown

router ospf 1
 router-id 2.2.2.2
 network 192.168.20.0 0.0.0.255 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 192.168.40.0 0.0.0.255 area 0
 network 10.10.10.0 0.0.0.3 area 0

! --- ACL SECURITY: BLOKIR GUEST KE SERVER ---
ip access-list extended BLOCK-GUEST
 deny ip 192.168.40.0 0.0.0.255 192.168.99.0 0.0.0.255
 permit ip any any

interface g0/0.40
 ip access-group BLOCK-GUEST in
end
wr

```
### 5. Konfigurasi Server & Wireless
**Server-PT SRV-DHCP (192.168.99.10)**
```CISCO
1. Set Static IP: 192.168.99.10 / 255.255.255.0 / Gateway 192.168.99.1 / DNS 192.168.99.10
2. Services > DHCP > Buat 5 Pool:
   - poolVLAN10 - 192.168.10.0, Gateway 192.168.10.1
   - poolVLAN99 - 192.168.99.0, Gateway 192.168.99.1
   - poolVLAN20 - 192.168.20.0, Gateway 192.168.20.1
   - poolVLAN30 - 192.168.30.0, Gateway 192.168.30.1
   - poolVLAN40 - 192.168.40.0, Gateway 192.168.40.1
   - DNS Server: 192.168.99.10 untuk semua pool
**AccessPoint-PT AP-Branch 1 (Wi-Fi Karyawan - VLAN 30)**
   - SSID: WiFi-Karyawan
   - Authentication: WPA2-PSK, Pass: karyawan123
   - Port 1: Connect ke Switch VLAN 30
**AccessPoint-PT AP-Branch 2 (Wi-Fi Tamu - VLAN 40)**
   - SSID: WiFi-Tamu
   - Authentication: WPA2-PSK, Pass: tamu12345
   - Port 1: Connect ke Switch VLAN 40
```
### 6. Verifikasi & Pengujian Final
```CISCO
! Cek OSPF
show ip ospf neighbor
! Harus muncul FULL/ - 1.1.1.1 di Branch dan 2.2.2.2 di HQ

show ip route ospf
! Harus ada route 192.168.x.x via 10.10.10.x

! Cek ACL
show access-lists BLOCK-GUEST
! Hit counter deny akan naik saat Guest-Phone ping Server
