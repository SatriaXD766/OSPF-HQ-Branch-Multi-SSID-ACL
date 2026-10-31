<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20,25,30&height=300&section=header&text=Satria%20Bagas%20Sejati&fontSize=52&fontColor=fff&animation=fadeIn&fontAlignY=35&desc=Si%20Anak%20Networking%20%7C%20552012610009%20%7C%20Advanced%20Lab&descAlignY=58&descSize=18" />

<div align="center">

[![Typing SVG](https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=800&size=26&duration=2500&pause=400&color=00FFAB&background=0A0A23FF&center=true&vCenter=true&multiline=true&width=1200&height=170&lines=Self-Study+Lab+%7C+OSPF+%2B+ACL+%F0%9F%94%A5;Mengulik+Protokol+Routing+Dinamis+OSPF+Project+Pribadi+Self+Study;No+RTO+fr+fr+%F0%9F%98%8E)](https://git.io/typing-svg)

![Cisco](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Level](https://img.shields.io/badge/Level-ADVANCED-FF6B00?style=for-the-badge&logo=cisco&logoColor=white)
![Status](https://img.shields.io/badge/Self_Study-100%25_Loss_BLOCKED-00FF00?style=for-the-badge&logo=checkmarx&logoColor=white)

</div>

---
### 👋 Tentang Project Ini

> **Status:** Mahasiswa Semester 1 - Teknik Informatika (552012610009)
> **Project:** Personal Advanced Lab - Eksplorasi Networking dengan Cisco Packet Tracert sebagai project pribadi dan portofolio.

Aku sengaja ngoprek topologi yang lebih kompleks dari Project Semester 1 (yang cuma VLAN & Static Routing) biar pas masuk Project semester 2 bisa buat project yang lebih kompleks. Topologi ini adalah **Hybrid HQ-Branch dengan OSPF + Security ACL**.

### ✨ Apa Yang Aku Pelajari di Lab Ini?
Ini yang bikin beda dari project Semester 1:

- **Routing Dinamis:** OSPF Area 0 Single Area, Point-to-Point Link (192.168.100.0/30) bukan cuma static route
- **DHCP Relay:** DHCP Server terpusat di HQ (VLAN 99) + `ip helper-address` di Branch, jadi Branch nggak perlu bikin server lagi
- **Multi-SSID Workaround:** Packet Tracer nggak support Multi-SSID 1 AP, jadi solusinya pakai 2 AP fisik (AP-Work VLAN 30, AP-Guest VLAN 40)
- **Security:** Extended ACL `BLOCK-GUEST` - Memblokir Guest Wi-Fi mengakses Server internal HQ

### 🧪 Bukti Keberhasilan!

| No | Test | Hasil | Status |
| :--- | :--- | :--- | :--- |
| 1 | Guest-Phone (VLAN 40) -> Server HQ (VLAN 99) | **Request Timed Out (100% Loss)** | ✅ **ACL Berhasil Block!** |
| 2 | Branch-PC1 (VLAN 30 - Karyawan) -> Server HQ | Reply | ✅ Akses Karyawan Normal |
| 3 | HQ-PC1 -> Branch-PC1 | Reply | ✅ OSPF Connected |

**Screenshot Bukti:**
![Bukti ACL Berhasil](bukti-acl-block.png)
*Ping dari Guest-Phone ke Server HQ 192.168.99.1 = 100% Loss, artinya ACL BLOCK-GUEST bekerja!*

![Bukti Akses Karyawan Normal](bukti-akses-karyawan-normal.png)
*Ping dari Branch-PC1 ke Server HQ 192.168.99.10 = 0% Loss, artinya Akses Karyawan ke Server Normal!*

![Bukti OSPF Terhubung](OSPF-Connected.png)
*Ping dari HQ-PC1 ke Branch-PC1 192.168.20.10 = 0% Loss, artinya Topologi OSPF saling terhubung! (all connected)*

### 📁 Isi Repo
- `OSPF-HQ-Branch-ACL.pkt` 
- `bukti-acl-block.png`
- `OSPF-HQ-Branch-ACL.png` 

### 🛠️ Tools
- Cisco Packet Tracer 8.x
- 2911 Router, 2960 Switch, Smartphone-PT

---
**By:** Satria Bagas Sejati - 552012610009  

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20,25,30&height=170&section=footer&text=From+VLAN+to+OSPF&fontSize=32&fontColor=fff&animation=twinkling&fontAlignY=60&desc=%5BPING+SUCCESS%5D+Self-Study+Lab+%7C+Ready+for+Project+Semester+2&descAlignY=85&descSize=17" />
</div>
