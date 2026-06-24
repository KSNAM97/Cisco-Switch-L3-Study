# Cisco Switch L3 Study

[![Cisco](https://img.shields.io/badge/Cisco-IOS-blue)]()
[![Switch](https://img.shields.io/badge/L3-Switch-orange)]()
[![GNS3](https://img.shields.io/badge/GNS3-Lab-green)]()
[![License](https://img.shields.io/badge/License-MIT-yellow)]()

**Cisco Layer 3 Switch (Routed Port / SVI / VTP / Inter-VLAN Routing / EIGRP / OSPF / EtherChannel / HSRP / VRRP)**

> - [Part 1 Layer 2](https://github.com/KSNAM97/CISCO-SWITCH-L2STUDY)
> - **Part 2 Layer 3**

---

Cisco IOS **L3 Switch(Multi-Layer Switch)** 학습 저장소.  
L2 기능 위에 `ip routing`을 활성화하여 라우팅을 수행하고,  
**Routed Port / SVI / Inter-VLAN Routing / 동적 라우팅 (EIGRP, OSPF) / EtherChannel / Gateway 이중화 (HSRP, VRRP)** 까지 다룬다.  
**CCNA / CCNP** 학습용 정리.

> L2 저장소 : [CISCO-SWITCH-L2STUDY](https://github.com/KSNAM97/CISCO-SWITCH-L2STUDY)

| 항목 | 내용 |
| --- | --- |
| 대상 장비 | Cisco IOS (IOSvL2 / 3560 / 3650 L3 스위치), Router |
| 다루는 기술 | ip routing, Routed Port, SVI, Inter-VLAN Routing, EIGRP, OSPF, EtherChannel, HSRP, VRRP |
| 실습 환경 | GNS3 / Cisco Packet Tracer |
| 주요 키워드 | L3 Switch, no switchport, interface vlan, passive-interface, EIGRP, OSPF, EtherChannel, PAgP, LACP, HSRP, VRRP, FHRP |

---

```
cisco-switch-l3study/
├─ 01_l3-switch-basics/    # L2 vs L3 Switch, ip routing 개념
├─ 02_routed-port/         # no switchport, Routed Port (L3 인터페이스)
├─ 03_svi/                 # SVI (Switched Virtual Interface), VLAN Gateway
├─ 04_inter-vlan/          # Inter-VLAN Routing (SVI 기반)
├─ 05_eigrp/               # EIGRP 동적 라우팅
├─ 06_ospf/                # OSPF 동적 라우팅, passive-interface
├─ 07_etherchannel/        # EtherChannel (PAgP/LACP, L2/L3 Link Aggregation)
├─ 08_gateway-redundancy/  # HSRP / VRRP (Gateway 이중화, FHRP)
├─ labs/                   # 실습 토폴로지 (Preconfig 포함)
│   ├─ lab01_routed-port-eigrp/
│   ├─ lab02_svi-vtp-ospf/
│   └─ lab03_hsrp-vrrp/
├─ LICENSE
└─ README.md
```

---

## 목차 (Chapters)

| # | 챕터 | 핵심 키워드 |
| --- | --- | --- |
| 01 | [L3 Switch Basics](./01_l3-switch-basics/l3-switch-basics.md) | L2 vs L3 Switch, `ip routing`, Multi-Layer Switch, SVI vs Routed Port |
| 02 | [Routed Port](./02_routed-port/routed-port.md) | `no switchport`, L3 포트, P2P 링크, IP 직접 부여 |
| 03 | [SVI](./03_svi/svi.md) | Switched Virtual Interface, VLAN Gateway, IP 인터페이스 |
| 04 | [Inter-VLAN Routing](./04_inter-vlan/inter-vlan.md) | SVI 기반 VLAN 간 라우팅, Trunk Allowed VLAN |
| 05 | [EIGRP](./05_eigrp/eigrp.md) | EIGRP AS 100, `network`, `no auto-summary`, DUAL |
| 06 | [OSPF](./06_ospf/ospf.md) | OSPF Process/Area/Router-ID, `passive-interface default`, LSA |
| 07 | [EtherChannel](./07_etherchannel/etherchannel.md) | PAgP/LACP, L2/L3 EtherChannel, `channel-group`, port-channel |
| **08** | **[Gateway Redundancy (HSRP & VRRP)](./08_gateway-redundancy/gateway-redundancy.md)** | **FHRP, Virtual-IP/MAC, Active/Standby, Master/Backup, Preempt, Track** |

---

## 실습 (Labs)

| # | 랩 | 내용 |
| --- | --- | --- |
| Lab 01 | [Routed Port + EIGRP](./labs/lab01_routed-port-eigrp) | `no switchport` 기반 L3 포트 + EIGRP 100 (SW1/SW2/SW3/R4) |
| Lab 02 | [SVI + VTP + OSPF](./labs/lab02_svi-vtp-ospf) | VTP로 VLAN 동기화 + SVI Gateway + OSPF (SW1/SW2/SW3) |
| **Lab 03** | **[HSRP / VRRP](./labs/lab03_hsrp-vrrp)** | **OSPF 공중망 + HSRP/VRRP 이중화 + Track으로 WAN 장애 감지** |

각 Lab은 `topology`와 `preconfig/*.txt` 형태로 제공 (GNS3 기준).

---

## 핵심 개념 요약

- **L3 Switch**: L2 기능 + L3 라우팅 기능 동시 지원
- **ip routing**: L3 기능 활성화 (L3 스위치에서 필수)
- **Routed Port**: `no switchport`로 L3 포트화, IP 직접 부여 (P2P 링크)
- **SVI**: VLAN에 IP를 부여한 가상 인터페이스, VLAN Gateway IP
- **Inter-VLAN Routing**: SVI 또는 Routed Port를 통한 VLAN 간 통신
- **EIGRP**: Cisco Advanced Distance Vector, DUAL 알고리즘, 빠른 수렴
- **OSPF**: Link-State, Area 기반, SPF(Dijkstra) 알고리즘
- **EtherChannel**: 다중 링크 1개로 묶기 + STP Blocking 회피 (PAgP/LACP)
- **HSRP / VRRP**: First-Hop 이중화, Virtual-IP/MAC, Active(Master) ↔ Standby(Backup)

---

## Routed Port vs SVI

| 구분 | Routed Port | SVI |
| --- | --- | --- |
| 정의 | L3 물리 포트 (단일 인터페이스) | L3 가상 인터페이스 (VLAN 단위) |
| 설정 | `no switchport` | `interface vlan X` |
| IP 부여 | 포트에 직접 | VLAN 인터페이스에 |
| 용도 | P2P L3 링크 (스위치-라우터 등) | VLAN Gateway, 내부 호스트 IP |
| VLAN | 종속 (VLAN 없음) | VLAN 종속 |
| 전제 조건 | `ip routing` | `ip routing` |

---

## PAgP vs LACP

| 구분 | PAgP | LACP |
| --- | --- | --- |
| 표준 | Cisco 전용 | IEEE 802.3ad |
| 최대 링크 | 8개 | 16개 (8 Active + 8 Standby) |
| 모드 (능동) | `desirable` | `active` |
| 모드 (수동) | `auto` | `passive` |
| 강제 모드 | `on` (협상 없음) | `on` (협상 없음) |

---

## HSRP vs VRRP

| 항목 | HSRP | VRRP |
| --- | --- | --- |
| 표준 | Cisco 전용 | IETF 표준 (RFC 5798) |
| Active 명칭 | Active Router | Master Router |
| Standby 명칭 | Standby Router | Backup Router |
| Virtual MAC | `0000.0C07.AC[group]` | `0000.5E00.01[group]` |
| Multicast | 224.0.0.2 | 224.0.0.18 |
| Protocol | UDP 1985 | IP Protocol 112 |
| Hello / Dead | 3초 / 10초 | 1초 / 3.x초 |
| Preempt | 기본 **Disabled** | 기본 **Enabled** |
| 명령어 | `standby` | `vrrp` |

---

## 실습 환경

| 도구 | 용도 |
| --- | --- |
| **GNS3** | 실습 토폴로지 시뮬레이션 |
| **Cisco IOSvL2** | L3 Switch 이미지 |
| **Cisco Packet Tracer** | 보조 실습 |
| **MobaXterm** | 콘솔 접속 |
| **Git / GitHub** | 문서 관리 |

---

## License

[MIT License](./LICENSE)
