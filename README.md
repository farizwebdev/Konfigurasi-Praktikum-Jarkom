# Network Laboratory Topology: FarizHusainAlbar

Repositori ini berisi file topologi jaringan untuk **EVE-NG / UNETLAB**. Lab ini mensimulasikan jaringan perusahaan skala kecil-menengah dengan hierarki Core / Distribution dan Access layer, lengkap dengan layanan DHCP, Inter-VLAN Routing, dan konektivitas Internet melalui NAT.

## Deskripsi Proyek
Lab ini mendemonstrasikan konfigurasi routing dan switching fundamental serta layanan jaringan esensial. Topologi terdiri dari Router Gateway, L3 Core Switch, L2 Access Switches, dan beberapa end-devices (Server & Client).

**File Topologi:** `FarizHusainAlbar.unl`

## Topologi & Perangkat
Daftar node yang digunakan dalam lab ini:

| ID Node | Nama Host | Tipe | Image / Template | Fungsi |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **R1** | Router | i86bi_LinuxL3 | Gateway Internet, NAT, Static Routing |
| 2 | **SWITCH CORE** | L3 Switch | i86bi_LinuxL2 (L3 Mode) | Inter-VLAN Routing, DHCP Server, Core Layer |
| 3 | **SW-L2-SAIN** | Switch | i86bi_LinuxL2 | Access Layer (VLAN 10) |
| 4 | **SW-L2-LABT** | Switch | i86bi_LinuxL2 | Access Layer (VLAN 100) |
| 5 | **DNS-Server** | Server | Linux Ubuntu | DNS Services |
| 9 | **Server_LabJarkom**| VPCS | VPCS | Internal Server (VLAN 15) |
| 7, 8 | **VPC-Client** | VPCS | VPCS | End User Clients |

## Tabel VLAN & Pengalamatan IP

### Konfigurasi VLAN & SVI (pada SWITCH CORE)
| VLAN ID | Nama VLAN | Network Subnet | Gateway IP (SVI) | Keterangan |
| :--- | :--- | :--- | :--- | :--- |
| **2** | MGMT-ACCSW | 192.168.2.0/24 | 192.168.2.1 | Manajemen Switch L2 |
| **10** | CLIENT-SAIN | 192.168.10.0/24| 192.168.10.1 | Klien area SAIN (DHCP) |
| **15** | SERVER-FARM | 172.16.0.0/24 | 172.16.0.1 | Server Internal |
| **20** | MGMT-DNS-SERV| 192.168.20.0/24| 192.168.20.1 | DNS Server |
| **100** | CLIENT-LABT | 192.168.100.0/24| 192.168.100.1| Klien area LABT (DHCP) |

### Link Point-to-Point (Layer 3)
| Interface | Perangkat 1 | IP Address | Perangkat 2 | IP Address |
| :--- | :--- | :--- | :--- | :--- |
| **e0/1 - e0/0** | R1 | 10.10.10.1/30 | SWITCH CORE | 10.10.10.2/30 |

## Fitur & Konfigurasi Utama

### 1. Routing (Layer 3)
* **R1 (Gateway):** Menggunakan **Static Routing** untuk mengarahkan trafik balik ke subnet internal (192.168.x.x dan 172.16.x.x) melalui Core Switch (10.10.10.2).
* **SWITCH CORE:** Menggunakan **Default Route** (`ip route 0.0.0.0 0.0.0.0 10.10.10.1`) untuk melempar trafik internet ke R1.
* **Inter-VLAN Routing:** Diaktifkan pada SWITCH CORE (`ip routing`) menggunakan Interface VLAN (SVI).

### 2. Network Address Translation (NAT)
* Diimplementasikan pada **R1**.
* **Tipe:** NAT Overload (PAT) pada interface `Ethernet0/0`.
* **ACL 1:** Mengizinkan subnet internal (192.168.2.0, 10.0, 20.0, 100.0, dan 172.16.0.0) untuk akses internet.

### 3. DHCP Services
* **SWITCH CORE** bertindak sebagai DHCP Server untuk klien.
* **Pool VLAN 10:** Network 192.168.10.0/24.
* **Pool VLAN 100:** Network 192.168.100.0/24.
* **Excluded Address:** IP Gateway (.1) dan IP Manajemen (.20) dikecualikan dari pool.

### 4. Keamanan & Manajemen
* **SSH:** Dikonfigurasi pada switch (Versi 2, Crypto Key generated).
* **Banner MOTD:** Peringatan akses tidak sah ("Authorized Access Only").
* **Spanning Tree:** Mode PVST, fitur Portfast dan BPDU Guard diaktifkan.

## Cara Menjalankan Lab
1.  Pastikan Anda memiliki **EVE-NG** atau **UNETLAB** yang terinstal.
2.  Import file `FarizHusainAlbar.unl` ke dalam folder lab EVE-NG Anda.
3.  Pastikan image IOL (Cisco IOS on Linux) dan Linux QEMU sudah tersedia di server EVE-NG Anda.
4.  Perbaiki permission file: `/optunetlab/wrappers/unl_wrapper -a fixpermissions`.
5.  Nyalakan node dan akses console.

## Author
**Fariz Husain Albar**
