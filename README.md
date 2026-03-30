# network portfolio

> **ชุตินันท์ หมายสุข** · รหัสนักศึกษา 673380401-6 · Section 03

---

## สารบัญ

- [เกี่ยวกับ Portfolio นี้](#-เกี่ยวกับ-portfolio-นี้)
- [Labs](#-labs)
  - [Lab 1 — Basic Network & Protocol Analysis](#lab-1--basic-network--protocol-analysis)
  - [Lab 2 — Secure & Scalable VLAN Design](#lab-2--secure--scalable-vlan-design-router-on-a-stick)
  - [Lab 3 — MIME File Transfer over Router-on-a-Stick](#lab-3--mime-file-transfer-over-router-on-a-stick)
  - [Lab 4 — Simulated Internet with Stateful vs Stateless](#lab-4--simulated-internet-with-stateful-vs-stateless)
  - [Lab 5 — Internet Edge + ISP Serial WAN + Microservices](#lab-5--internet-edge--isp-serial-wan--microservices)
- [Assignments](#-assignments)
  - [Assignment 1 — Essay: เครือข่ายในชีวิตประจำวัน](#assignment-1--essay-เครือข่ายในชีวิตประจำวัน)
  - [Assignment 2 — Topology: ประเภทของเครือข่าย](#assignment-2--topology-ประเภทของเครือข่าย)
  - [Assignment 3 — Not Simple Network (Packet Tracer)](#assignment-3--not-simple-network-packet-tracer)
  - [Assignment 4 — TCP/UDP Protocol Analysis](#assignment-4--tcpudp-protocol-analysis)


---

##  เกี่ยวกับ Portfolio นี้

Portfolio นี้รวบรวมงานทั้งหมดจากวิชาเครือข่ายคอมพิวเตอร์ ซึ่งประกอบด้วย Lab จากการทดลองใน Cisco Packet Tracer และ Assignment ที่เกี่ยวข้องกับการวิเคราะห์โปรโตคอลในระดับต่างๆ ของ OSI Model ตั้งแต่ชั้น Physical ไปจนถึง Application Layer

---
![Getting_Started_with_Cisco_Packet_Tracer_certificate_chutinan-m-kkumail-com_2736423f-3d0d-492a-b880-e9b6c21462d3_page-0001](https://github.com/user-attachments/assets/71c85e50-27e5-45c1-bcdd-ee3e422c7d15)


##  Labs

### Lab 1 — Basic Network & Protocol Analysis

> **เป้าหมาย:** สร้างเครือข่ายพื้นฐานและวิเคราะห์กระบวนการทำงานของโปรโตคอล ARP และ ICMP

**อุปกรณ์ที่ใช้:**
- Router 2911 (R1), Switch 2960 (S1), PC 2 เครื่อง (PC1, PC2)

**สิ่งที่ทำใน Lab:**
- กำหนดค่า IP Address แบบ Static บน PC และ Router
- ทดสอบการเชื่อมต่อด้วย `ping` และตรวจสอบ ARP Table ด้วย `arp -a`
- ตรวจสอบ MAC Address Table บน Switch ด้วย `show mac address-table`
- จำลองปัญหาเครือข่าย เช่น Wrong Subnet Mask, Interface Shutdown, Incorrect Gateway แล้วแก้ไข

**ผลการตรวจสอบ (Worksheet 1):**

| Checkpoint | คำสั่ง | ผล |
|---|---|---|
| R1 Interface Status | `show ip int brief` | G0/0 UP/UP ✅ |
| S1 VLAN Config | `show vlan brief` | VLAN 1 Active ✅ |
| PC1 Connectivity | `ping 192.168.1.11` | Success (4 replies) ✅ |
| ARP Resolution | `arp -a` on PC1 | 2+ entries ✅ |
| MAC Learning | `show mac address-table` | 2 dynamic entries ✅ |

**สิ่งที่ได้เรียนรู้:**
- ARP ทำงานก่อน TCP เสมอ เพื่อแปลง IP เป็น MAC Address ก่อนส่ง Ethernet Frame
- Switch ทำงานที่ Layer 2 จึงไม่ต้องการ IP Address ในการ Forward Frame
- TTL ในส่วนหัว IP ช่วยป้องกันไม่ให้ Packet วนซ้ำไม่สิ้นสุดในเครือข่าย

---

### Lab 2 — Secure & Scalable VLAN Design (Router-on-a-Stick)

> **เป้าหมาย:** ออกแบบและตั้งค่า VLAN สำหรับแยก Traffic ระหว่างกลุ่มผู้ใช้ที่แตกต่างกัน

**สิ่งที่ทำใน Lab:**
- สร้าง VLAN หลายชุดบน Switch และกำหนด Access/Trunk Port
- ตั้งค่า Subinterface บน Router ด้วย `encapsulation dot1Q` เพื่อทำ Inter-VLAN Routing
- ทดสอบการเชื่อมต่อข้ามและภายใน VLAN

**คำสั่งหลักที่ใช้:**
```
! บน Router
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

! บน Switch
switchport mode trunk
switchport access vlan 10
```

**สิ่งที่ได้เรียนรู้:**
- VLAN แยก Broadcast Domain ทำให้เครือข่ายปลอดภัยและมีประสิทธิภาพมากขึ้น
- Router-on-a-Stick ช่วยให้ใช้ Router เพียงพอร์ตเดียวจัดการได้หลาย VLAN

---

### Lab 3 — MIME File Transfer over Router-on-a-Stick

> **เป้าหมาย:** ส่งไฟล์ข้ามเครือข่าย VLAN ต่างกัน โดยวิเคราะห์กระบวนการทุก Layer ของ OSI

**ทีมงาน:**
- รัฐภูมิ แฝงฤทธิ์หลง (653380373-7) — Network Architects
- ชุตินันท์ หมายสุข (673380401-6) — ผู้บันทึกและวิเคราะห์
- ภัทราพร ศรีชนะ (673380419-7) — QA / Test

**Topology:**
```
PC1 (VLAN 10: 192.168.10.10)
        \
         [S1] --- trunk --- [R1]
        /
PC2-Server (VLAN 20: 192.168.20.20)
```

**การวิเคราะห์ Packet (Simulation Mode แทน Wireshark):**

| Layer | Field | สิ่งที่สังเกต |
|---|---|---|
| Ethernet | Src/Dst MAC | ทิศทาง Frame |
| 802.1Q | VLAN ID | การแบ่ง Segment |
| IP | TTL | พฤติกรรมการ Routing |
| TCP | Flags | Reliability |
| Payload | JSON | MIME Metadata |

**การ Troubleshoot ที่ทำ:**

| สถานการณ์ | อาการ | การแก้ไข |
|---|---|---|
| Broken Routing (Shutdown Subinterface) | Ping ไม่ผ่าน | `no shutdown` บน Subinterface |
| Wrong Gateway | ARP Incomplete | แก้ Default Gateway บน PC |

**สิ่งที่ได้เรียนรู้:**
- ข้อมูลระดับ Application (JSON/MIME) ยังคงครบถ้วนหลังผ่านกระบวนการ Encapsulation หลายชั้น
- Routing Decision เกิดที่ Layer 3 บน Router โดยอ้างอิง IP และ Routing Table

---

### Lab 4 — Simulated Internet with Stateful vs Stateless

> **เป้าหมาย:** จำลองระบบ Internet ที่มี 2 LAN เชื่อมกันผ่าน Public Network และทดสอบ API แบบ Stateless กับ Stateful

**ทีมงาน (5 คน):**
- รัฐภูมิ / จีรภัทร — Network Architects
- ชุตินันท์ / ยุทธนา — IaC / DevOps
- ภัทราพร — QA / Test

**Topology:**
```
        10.10.12.0/30 "Public/Internet"
         ┌──────────────────────┐
   R1 (10.10.12.1)       R2 (10.10.12.2)
         │                      │
LAN A: 192.168.10.0/24   LAN B: 192.168.20.0/24
   ServerA (10)               ServerB (10)
   ClientA (50)               ClientB (50)
```

**การตั้งค่าหลัก (R1):**
```
interface g0/0
 ip address 10.10.12.1 255.255.255.252
 ip nat outside
interface g0/1
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
ip route 192.168.20.0 255.255.255.0 10.10.12.2
access-list 10 permit 192.168.10.0 0.0.0.255
ip nat inside source list 10 interface g0/0 overload
```

**ผลการทดสอบ Stateless vs Stateful API:**

| คุณสมบัติ | Stateless API (Port 3000) | Stateful API (Port 3001) |
|---|---|---|
| จำ Request เดิมได้? | ❌ ไม่จำ | ✅ จำได้ผ่าน Session ID |
| เหมาะกับ | Public REST API | Shopping Cart, User Session |
| ผล Lab | ตอบกลับทันที | ต้องส่ง Session-ID ใน Header |

**สิ่งที่ได้เรียนรู้:**
- NAT ไม่กระทบต่อ Logical Session Identity ของแอปพลิเคชัน
- Static Route + NAT Overload ช่วยให้ Private LAN เข้าถึง Remote Service ได้
- Stateful Service ต้องการ Session Header ในทุก Request ที่ข้ามเครือข่าย

---

### Lab 5 — Internet Edge + ISP Serial WAN + Microservices

> **เป้าหมาย:** จำลองการ Deploy ระบบ Enterprise จริง โดยใช้ Serial WAN เชื่อม 2 Site ผ่าน ISP Domain พร้อม NAT และ Microservices บน ServerA/B

**อุปกรณ์ที่ใช้:**
- Router 2901 x2 (R1, R2), Switch 2960 x2, PC x2, Cloud (Internet)
- Serial Link (สายสีแดง) เชื่อม R1 ↔ R2 ผ่าน ISP Network

**Topology:**
```
        INTERNET (DHCP)
             │
         R1 G0/0 [NAT OUTSIDE]
             │
         R1 S0/0/0 ── 100.10.10.0/30 ── R2 S0/0/0
             │                                │
         R1 G0/1 (LAN A)              R2 G0/1 (LAN B)
         192.168.10.1                 192.168.20.1
             │                                │
          ServerA                          ServerB
      (Week03 Microservices)         (Week03 Microservices)
```

**Address Table:**

| Device | Interface | IP | Role |
|---|---|---|---|
| R1 | G0/0 | DHCP | Internet (NAT Outside) |
| R1 | S0/0/0 | 100.10.10.1/30 | ISP Serial Link |
| R1 | G0/1 | 192.168.10.1/24 | LAN A Gateway |
| R2 | S0/0/0 | 100.10.10.2/30 | ISP Serial Link |
| R2 | G0/1 | 192.168.20.1/24 | LAN B Gateway |
| ServerA | eth0 | 192.168.10.10 | Microservices A |
| ServerB | eth0 | 192.168.20.10 | Microservices B |

**การตั้งค่าหลัก — R1 (Internet Edge Router):**
```
interface g0/0
 ip address dhcp
 ip nat outside
 no shutdown
interface g0/1
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 no shutdown
interface s0/0/0
 ip address 100.10.10.1 255.255.255.252
 clock rate 64000
 no shutdown
ip route 192.168.20.0 255.255.255.0 100.10.10.2
ip route 0.0.0.0 0.0.0.0 g0/0
access-list 10 permit 192.168.10.0 0.0.0.255
ip nat inside source list 10 interface g0/0 overload
```

**การตั้งค่าหลัก — R2 (Branch Router):**
```
interface g0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
interface s0/0/0
 ip address 100.10.10.2 255.255.255.252
 no shutdown
ip route 192.168.10.0 255.255.255.0 100.10.10.1
ip route 0.0.0.0 0.0.0.0 100.10.10.1
```

**ผลการทดสอบ (Part 3 — WAN + Internet Testing):**

| การทดสอบจาก LAN A | ปลายทาง | ผล |
|---|---|---|
| ping Gateway R1 | 192.168.10.1 | ✅ Pass |
| ping Serial R2 | 100.10.10.2 | ✅ Pass |
| ping ServerB (LAN B) | 192.168.20.10 | ✅ Pass |
| ping R1 → R2 ผ่าน Serial | 100.10.10.2 | ✅ 5/5 (100%) |

**Routing Table ที่ตรวจสอบได้:**
- **R1:** Static route → 192.168.20.0/24 via 100.10.10.2, Default route → G0/0 (Internet)
- **R2:** Static route → 192.168.10.0/24 via 100.10.10.1, Default route → 100.10.10.1

**สิ่งที่ได้เรียนรู้:**
- Serial Interface ต้องกำหนด `clock rate` ฝั่ง DCE เพื่อให้ Link ทำงาน
- NAT Overload (PAT) ให้ทุกเครื่องใน LAN ออก Internet ผ่าน IP เดียวได้
- Default Route `0.0.0.0/0` บน R2 ชี้กลับ R1 เพื่อให้ Traffic ออก Internet ได้ผ่าน Edge Router
- Routing ข้าม WAN ต้องมี Static Route ทั้งสองทิศทาง มิฉะนั้น Reply Packet จะหาทางกลับไม่ได้

---

## 📝 Assignments

### Assignment 1 — Essay: เครือข่ายในชีวิตประจำวัน

> **หัวข้อ:** เครือข่ายคอมพิวเตอร์ที่ฉันรู้จักในชีวิตประจำวัน

เขียน Essay เกี่ยวกับ **Google Maps / GPS** ในฐานะตัวอย่างการใช้เครือข่ายในชีวิตจริงของนักศึกษาต่างจังหวัดที่เดินทางมาเรียนที่ขอนแก่น โดยอธิบายกระบวนการที่เกิดขึ้นตั้งแต่การส่งคำขอผ่านเครือข่ายโทรศัพท์ ผ่าน TCP/IP ไปยัง Server ของ Google และรับเส้นทางกลับมา รวมถึงอธิบายข้อจำกัดเมื่ออยู่ในพื้นที่อับสัญญาณ

**ประเด็นหลักที่นำเสนอ:**
- การทำงานร่วมกันของ TCP/IP และ GPS Satellite
- กระบวนการ Client-Server ในแอปพลิเคชันนำทาง
- วิธีแก้ปัญหาเบื้องต้นเมื่อเครือข่ายขัดข้อง

---

### Assignment 2 — Topology: ประเภทของเครือข่าย

> **เป้าหมาย:** ตอบคำถามและสร้าง Topology จำลองใน Cisco Packet Tracer แสดงเครือข่าย 3 ประเภท

| คำถาม | คำตอบ |
|---|---|
| เครือข่ายที่เล็กที่สุดคืออะไร? | Peer-to-Peer (P2P) — PC 2 เครื่องเชื่อมโดยตรง ไม่ต้องใช้ Switch หรือ Router |
| เครือข่ายที่ต้องใช้ Switch? | LAN ที่มีอุปกรณ์ตั้งแต่ 3 เครื่องขึ้นไป |
| เครือข่ายที่ต้องใช้ Router? | Internetwork — เชื่อม LAN หลายวงเข้าด้วยกัน Router ทำหน้าที่เป็น Default Gateway |

---

### Assignment 3 — Not Simple Network (Packet Tracer)

> **เป้าหมาย:** สร้างเครือข่ายขนาดใหญ่ขึ้นใน Cisco Packet Tracer ที่มีความซับซ้อนกว่า Lab พื้นฐาน

ไฟล์ Packet Tracer: `Assignments3_673380401_6.pkt`

**สิ่งที่ทำ:**
- ออกแบบเครือข่ายที่มีหลาย Subnet
- กำหนดค่า Routing ระหว่าง Network
- ทดสอบการเชื่อมต่อระหว่างอุปกรณ์ต่างเครือข่าย

---

### Assignment 4 — TCP/UDP Protocol Analysis

> **เป้าหมาย:** วิเคราะห์โปรโตคอล HTTP, FTP, DNS, SMTP, POP3 ผ่าน Simulation Mode ใน Packet Tracer

**ส่วนที่ 1 — HTTP & TCP (3-Way Handshake)**

กระบวนการเชื่อมต่อไปยัง Server `192.168.1.254`:

| ขั้นตอน | Packet | Flag | ความหมาย |
|---|---|---|---|
| 1.3 | SYN | SYN=1, ACK=0 | Client ขอเชื่อมต่อ |
| 1.4 | SYN-ACK | SYN=1, ACK=1 | Server ยืนยันและเชื่อมกลับ |
| 1.5 | ACK | SYN=0, ACK=1 | Client ยืนยัน → ESTABLISHED |

**ส่วนที่ 2 — FTP Protocol**
- แยก Control Connection (Port 21) และ Data Connection (Port 20)
- FTP ใช้ TCP เพราะต้องการ Command Accuracy สูง ป้องกันคำสั่งหายระหว่างทาง

**ส่วนที่ 3 — DNS & UDP**
- DNS ใช้ UDP (Port 53) เพราะต้องการความเร็ว ไม่ต้องรอ Handshake
- UDP Header เรียบง่ายกว่า TCP มาก: Source Port, Destination Port, Length, Checksum เท่านั้น

**ส่วนที่ 4 — Email (SMTP & POP3)**
- SMTP (Port 25) — ส่งออกอีเมล
- POP3 (Port 110) — ดึงอีเมลเข้าเครื่อง
- ทั้งคู่ทำงานบน TCP เพราะอีเมลเป็นข้อมูลที่ห้ามสูญหาย

**บทสรุปเปรียบเทียบ TCP vs UDP:**

| คุณสมบัติ | TCP | UDP |
|---|---|---|
| การเชื่อมต่อ | Connection-oriented (ต้อง Handshake) | Connectionless |
| ความน่าเชื่อถือ | สูง (มี ACK, Sequence) | ต่ำ (ไม่มียืนยัน) |
| ความเร็ว | ช้ากว่า | เร็วกว่า |
| ใช้ใน | HTTP, FTP, Email | DNS |

---



*ชุตินันท์ หมายสุข · 673380401-6 · Section 03*
