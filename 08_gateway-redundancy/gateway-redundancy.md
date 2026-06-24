# 08. Gateway Redundancy (HSRP & VRRP)

> Gateway 이중화 Protocol — Primary Gateway와 Backup Gateway를 사용하여  
> 망의 안정성과 가용성을 높이는 FHRP (First Hop Redundancy Protocol)

---

## 📌 Gateway 이중화 개요

망의 안정성을 높이기 위해서 두 개 이상의 Gateway를 두어 통신하는 기능으로, **Primary Gateway**와 **Backup Gateway**를 사용한다.  
기본적으로 내부 네트워크 내의 통신 장비들은 Primary Gateway를 통해서 외부 네트워크로 통신하며, Primary Gateway에 장애 발생 시 Backup Gateway를 사용하여 외부로 통신하는 기능이다.

| 종류 | 설명 |
| --- | --- |
| **HSRP** | CISCO 전용 Gateway 이중화 Protocol |
| **VRRP** | 표준 Gateway 이중화 Protocol (IETF) |

> ⚠️ HSRP, VRRP와 같은 Gateway 이중화 Protocol은 **내부망에 대해서만 장애 발생을 인식**한다.  
> 내부 네트워크 구간 장애 발생 시 Standby(Backup) Router가 Active(Master) 권한을 수행하지만,  
> **WAN 구간 장애 발생은 감시하지 않기 때문에** Standby Router가 Active 권한을 수행하지 못한다.  
> → 이를 해결하기 위해 **Track 기능**을 사용한다.

---

## 🟦 HSRP (Hot Standby Router Protocol)

- CISCO에서 개발한 Gateway 이중화 Protocol (표준 : VRRP)
- HSRP는 **Active Router**와 **Standby Router**로 구성된다.

### Active Router
Primary Gateway로 동작하는 Router이며, HSRP에서 **가장 큰 우선순위값을 갖은 Router**가 Active Router로 동작하며, 작은 우선순위값을 갖은 Router가 Standby Router로 동작한다.

### Standby Router
Backup Gateway이며 Active Router에 장애 발생 시 Standby Router가 Virtual Router의 권한을 수행한다.

- HSRP는 Virtual-Router를 생성하며, 내부망에서 외부로 통신 시 모든 트래픽은 Virtual-Router에게 전송된다.
- HSRP는 내부망에서 고유한 가상의 Mac-address를 생성하여 통신한다.
  - **Virtual MAC** : `00-00-0C-07-AC-[group number]`

### HSRP 통신 파라미터

| 항목 | 값 |
| --- | --- |
| Hello-interval | 3초 (Gateway간 교환 주기) |
| Dead-interval | 10초 (Dead interval 안에 Hello 미수신 시 장애로 간주) |
| Multicast | 224.0.0.2 (Router만 수신) |
| UDP Port | 1985 |

---

## 🟩 VRRP (Virtual Router Redundancy Protocol)

- CISCO에서 개발한 HSRP를 표준화한 Protocol
- 기본적인 동작 방식과 설정 방식은 HSRP와 유사하다.
- VRRP는 **Master Router**와 **Backup Router**를 사용하여 Gateway 이중화를 구성한다.
- VRRP는 Virtual-Router를 생성하며 내부망에서 외부로 통신 시 모든 트래픽은 Virtual-Router에게 전송되며 **Master Router**가 Virtual-Router의 권한을 수행한다.
- Backup Router는 대기하다가 Master Router에 장애 발생 시 Virtual-Router의 권한을 수행한다.

### HSRP ↔ VRRP 용어 비교

| HSRP | VRRP |
| --- | --- |
| Active Router | Master Router |
| Standby Router | Backup Router |

### VRRP 특징
- VRRP의 Mac 주소는 고정 주소를 사용한다.  
  - **Virtual MAC** : `00-00-5E-00-01-[group number]`
- **기본적으로 preempt는 ON**

---

## 🧪 Topology

```
                          ┌──────────┐
                          │   ISP    │  13.13.2.2/24
                          │   R2     │  13.13.12.2/24
                          └────┬─────┘
              13.13.10.0/24    │    13.13.20.0/24
              S1/0.12          │         S1/0.23
            ┌─────────────────┴─────────────────┐
            │                                   │
        ┌───┴───┐                          ┌────┴──┐
        │ G/W1  │                          │ G/W2  │
        │  R1   │   OSPF 1 Area 0          │  R3   │
        └───┬───┘                          └────┬──┘
   Fa0/1 200.20.2.251           Fa0/1 200.20.2.252
            │                                   │
            │      Virtual-IP : 200.20.2.254    │
            └───────────────┬───────────────────┘
                            │
                       ┌────┴────┐
                       │   SW1   │
                       └─┬─────┬─┘
                  Fa0/4  │     │ Fa0/5
                ┌────────┘     └────────┐
            ┌───┴───┐               ┌───┴───┐
            │  PC1  │ 200.20.2.4    │  PC2  │ 200.20.2.5
            │  R4   │               │  R5   │
            └───────┘               └───────┘
              G/W : 200.20.2.254       G/W : 200.20.2.254
```

---

## 1️⃣ 공중망 OSPF 구성 (사전 작업)

> G/W1 , G/W2 , ISP 간 OSPF Routing Protocol을 구성한다.  
> Process = 1 , Area = 0 , Router-ID = X.X.X.X  
> OSPF 라우팅 업데이트가 필요한 Interface로만 OSPF Packet이 송신되어야 한다.

### G/W1 (R1)
```cisco
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface serial 1/0.12
 network 13.13.1.0  0.0.0.255 area 0
 network 13.13.10.0 0.0.0.255 area 0
 network 200.20.2.0 0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
```

### G/W2 (R3)
```cisco
router ospf 1
 router-id 3.3.3.3
 passive-interface default
 no passive-interface serial 1/0.23
 network 13.13.3.0  0.0.0.255 area 0
 network 13.13.20.0 0.0.0.255 area 0
 network 200.20.2.0 0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
```

### ISP (R2)
```cisco
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.23
 network 13.13.2.0  0.0.0.255 area 0
 network 13.13.12.0 0.0.0.255 area 0
 network 13.13.10.0 0.0.0.255 area 0
 network 13.13.20.0 0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
```

### 확인
```cisco
G/W1# show ip ospf neighbor
G/W2# show ip ospf neighbor
ISP#  show ip ospf neighbor
G/W1# show ip route
G/W1# ping 13.13.2.2
G/W1# ping 13.13.12.2
```

---

## 2️⃣ HSRP 기본 설정

### EX) G/W1을 Primary Gateway로 동작시키기
- G/W1이 200.20.2.0/24 내부 네트워크에 대해서 Primary Gateway로 동작
- G/W1의 Fa0/1 장애 발생 시 G/W2가 Gateway로 동작
- Virtual-IP = 200.20.2.254

#### G/W1 (R1)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254      ! Virtual-IP 설정 (PC/Server의 Gateway IP)
 standby 1 priority 150         ! 우선순위 (0~255, default 100) - 큰 값이 Active
 standby 1 timer 1 3            ! Hello/Dead interval (default 3/10)
!
```

#### G/W2 (R3)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 100
 standby 1 timer 1 3
!
```

#### 확인
```cisco
G/W1# show standby brief
                   P indicates configured to preempt.
                   |
Interface Grp  Pri P State    Active          Standby         Virtual IP
Fa0/1     1    150   Active   local           200.20.2.252    200.20.2.254

G/W2# show standby brief
Interface Grp  Pri P State    Active          Standby         Virtual IP
Fa0/1     1    100   Standby  200.20.2.251    local           200.20.2.254
```

```cisco
PC1# ping 13.13.2.2
PC1# traceroute 13.13.2.2
  1 200.20.2.251 60 msec 48 msec 40 msec
  2 13.13.10.2   56 msec *      28 msec

PC1# show arp
Internet  200.20.2.254   2   0000.0c07.ac01  ARPA   FastEthernet0/1
```

---

## 3️⃣ HSRP Preempt (장애 복구 후 Active 권한 회수)

### EX) G/W1 장애 복구 60초 후 Active Router로 재선출

#### G/W1 (R1)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 150
 standby 1 timer 1 3
 standby 1 preempt delay minimum 60   ! 장애 복구 60초 후 Active 권한 수행
!
```

#### G/W2 (R3)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 100
 standby 1 timer 1 3
!
```

> **preempt** : 더 높은 priority를 가진 Router가 복구되었을 때 즉시 Active 권한을 가져온다.  
> **delay minimum 60** : 복구 후 라우팅 테이블이 안정화되는 시간을 위해 60초 대기 후 Active 권한 수행.

---

## 4️⃣ HSRP Track (WAN 구간 장애 감지)

### 문제점
HSRP는 내부망에 대해서만 장애 발생을 인식하므로, **WAN 구간 장애 발생 시** Active Router가 외부로 통신이 불가능하더라도 Standby Router가 Active 권한을 수행하지 못한다.

### 해결 — Track 기능

#### G/W1 (R1) — 방법 1 (직접 track)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 150
 standby 1 timer 1 3
 standby 1 preempt delay minimum 60
 standby 1 track serial 1/0.12 100   ! S1/0.12 Down 시 priority 100 차감
!
```

#### G/W1 (R1) — 방법 2 (track object)
```cisco
track 10 interface serial 1/0.12 line-protocol
!
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 150
 standby 1 timer 1 3
 standby 1 preempt delay minimum 60
 standby 1 track 10 decrement 100
!
```

#### G/W2 (R3)
```cisco
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 100
 standby 1 timer 1 3
 standby 1 preempt
!
```

#### 확인 (Track 동작 시)
```cisco
G/W1# sh standby all
FastEthernet0/1 - Group 1
  State is Standby
  Virtual IP address is 200.20.2.254
  Active virtual MAC address is 0000.0c07.ac01
  Hello time 1 sec, hold time 3 sec
  Preemption enabled, delay min 60 secs
  Active router is 200.20.2.252, priority 100
  Standby router is local
  Priority 50 (configured 150)                ! 150 → 50 으로 변경
    Track interface Serial1/0.12 state Down decrement 100
```

---

## 5️⃣ HSRP 부하 분산 (Multi-Group)

### EX) 양방향 이중화 — G/W1, G/W2가 서로 다른 Virtual-IP에 대해 Active로 동작
- **Group 1 (200.20.2.254)** : G/W1 = Active, G/W2 = Standby
- **Group 2 (200.20.2.253)** : G/W2 = Active, G/W1 = Standby

#### G/W1 (R1)
```cisco
track 10 interface serial 1/0.12 line-protocol
!
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 150
 standby 1 timer 1 3
 standby 1 preempt delay minimum 60
 standby 1 track 10 decrement 100
 !
 standby 2 ip 200.20.2.253
 standby 2 priority 100
 standby 2 timer 1 3
 standby 2 preempt
!
```

#### G/W2 (R3)
```cisco
track 20 interface serial 1/0.23 line-protocol
!
interface fastethernet 0/1
 standby 1 ip 200.20.2.254
 standby 1 priority 100
 standby 1 timer 1 3
 standby 1 preempt
 !
 standby 2 ip 200.20.2.253
 standby 2 priority 150
 standby 2 timer 1 3
 standby 2 preempt delay minimum 60
 standby 2 track 20 decrement 100
!
```

#### PC 설정 분리
```cisco
! PC1 (R4)
ip route 0.0.0.0 0.0.0.0 200.20.2.254

! PC2 (R5)
ip route 0.0.0.0 0.0.0.0 200.20.2.253
```

---

## 6️⃣ VRRP 설정

### EX) VRRP로 Gateway 이중화 구성
- G/W1 = Master, G/W2 = Backup
- Virtual-IP = 200.20.2.254
- G/W1 장애 복구 60초 후 Master 재선출
- WAN 장애 시 G/W2가 Master로 동작

#### G/W1 (R1)
```cisco
track 10 interface serial 1/0.12 line-protocol
!
interface fastethernet 0/1
 vrrp 1 ip 200.20.2.254
 vrrp 1 priority 150
 vrrp 1 preempt delay minimum 60
 vrrp 1 track 10 decrement 100
!
```

#### G/W2 (R3)
```cisco
interface fastethernet 0/1
 vrrp 1 ip 200.20.2.254
 vrrp 1 priority 100
 vrrp 1 preempt
!
```

#### 확인
```cisco
G/W1# show vrrp brief all
Interface     Grp Pri Time  Own Pre State   Master addr     Group addr
Fa0/1         1   150 3414      Y   Master  200.20.2.251    200.20.2.254

G/W1# show vrrp interface fa0/1
FastEthernet0/1 - Group 1
  State is Master
  Virtual IP address is 200.20.2.254
  Virtual MAC address is 0000.5e00.0101
  Advertisement interval is 1.000 sec
  Preemption enabled, delay min 60 secs
  Priority is 150
    Track object 10 state Up decrement 100

G/W2# show vrrp all
FastEthernet0/1 - Group 1
  State is Backup
  Virtual IP address is 200.20.2.254
  Virtual MAC address is 0000.5e00.0101
  Advertisement interval is 1.000 sec
  Preemption enabled
  Priority is 100
  Master Router is 200.20.2.251, priority is 150
  Master Down interval is 3.609 sec
```

---

## 📊 HSRP vs VRRP 비교

| 항목 | HSRP | VRRP |
| --- | --- | --- |
| 표준 | Cisco 전용 | IETF 표준 (RFC 5798) |
| Active 명칭 | Active Router | Master Router |
| Standby 명칭 | Standby Router | Backup Router |
| Virtual MAC | `0000.0C07.AC[group]` | `0000.5E00.01[group]` |
| Multicast | 224.0.0.2 | 224.0.0.18 |
| Protocol | UDP 1985 | IP Protocol 112 |
| Hello | 3초 / Dead 10초 | Advertisement 1초 / Master Down 3.x초 |
| Preempt | 기본 **Disabled** | 기본 **Enabled** |
| 설정 명령어 | `standby` | `vrrp` |

---

## 🔍 장애 시나리오 정리

| 장애 위치 | HSRP/VRRP 기본 동작 | Track 적용 시 |
| --- | --- | --- |
| Active의 LAN(Fa0/1) Down | ✅ Standby가 Active 권한 인수 | ✅ 동일 |
| Active의 WAN(S1/0.x) Down | ❌ Standby가 인수하지 못함 (블랙홀 발생) | ✅ priority 감소로 Standby가 인수 |
| Active 복구 (preempt off) | Active 권한 회수 안 함 | — |
| Active 복구 (preempt on/delay) | 지정 시간 후 Active 권한 회수 | — |

---

## 📌 핵심 요약

- Gateway 이중화는 **First-Hop**의 가용성을 보장하기 위한 기술이다.
- HSRP는 **Cisco 전용**, VRRP는 **표준** Protocol이다.
- **Virtual-IP**와 **Virtual-MAC**을 통해 PC는 단일 Gateway IP만 바라보면 된다.
- **WAN 장애 감지**는 `track` 기능으로 priority를 동적으로 조정하여 해결한다.
- **preempt**로 복구 후 우선순위가 높은 Router가 Active 권한을 되찾아온다.
- HSRP의 **Multi-Group** 기능으로 부하 분산(Load Sharing)이 가능하다.
