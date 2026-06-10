# 🌐 Cisco Layer 3 Switch Study (Multi-Layer Switch)

**Cisco Layer 3 Switch (Routed Port / SVI / VTP / Inter-VLAN Routing / EIGRP / OSPF)**

---

Cisco IOS 기반 **L3 Switch(Multi-Layer Switch)** 실습 스터디 자료입니다.  
L2 스위치 지식을 바탕으로, `ip routing`을 활성화한 멀티레이어 스위치에서  
**Routed Port / SVI / Inter-VLAN Routing / 동적 라우팅(EIGRP, OSPF)** 을 다룹니다.  
**CCNA / CCNP** 수준의 학습 및 실습을 목표로 합니다.

> 📌 L2 학습 자료: [CISCO-SWITCH-L2STUDY](https://github.com/KSNAM97/CISCO-SWITCH-L2STUDY)

| 항목 | 내용 |
| --- | --- |
| 대상 장비 | Cisco IOS (IOSvL2 / 3560 / 3650 등 L3 지원 스위치), Router |
| 핵심 주제 | ip routing, Routed Port, SVI, Inter-VLAN Routing, EIGRP, OSPF |
| 실습 환경 | GNS3 / Cisco Packet Tracer |
| 키워드 | L3 Switch, no switchport, interface vlan, passive-interface, EIGRP, OSPF, Loopback |

---

## 📂 디렉터리 구조

```
cisco-switch-l3study/
├── 01_l3-switch-basics/   # L2 vs L3 Switch, ip routing, 멀티레이어 개념
├── 02_routed-port/        # no switchport, Routed Port(L3 물리 포트)
├── 03_svi/                # SVI(Switched Virtual Interface), VLAN Gateway
├── 04_inter-vlan/         # Inter-VLAN Routing (SVI 기반)
├── 05_eigrp/              # EIGRP 동적 라우팅
├── 06_ospf/               # OSPF 동적 라우팅, passive-interface
├── labs/                  # 통합 실습 (Preconfig 포함)
│   ├── lab01_routed-port-eigrp/
│   └── lab02_svi-vtp-ospf/
├── LICENSE
└── README.md
```

---

## 📖 학습 챕터 (Chapters)

| # | 주제 | 내용 |
| --- | --- | --- |
| 01 | [L3 Switch Basics](./01_l3-switch-basics/l3-switch-basics.md) | L2 vs L3 Switch, `ip routing`, Multi-Layer Switch 개념, SVI vs Routed Port |
| 02 | [Routed Port](./02_routed-port/routed-port.md) | `no switchport`, L3 물리 포트, P2P 링크, IP 직접 할당 |
| 03 | [SVI](./03_svi/svi.md) | Switched Virtual Interface, VLAN Gateway, 관리용 IP |
| 04 | [Inter-VLAN Routing](./04_inter-vlan/inter-vlan.md) | SVI 기반 VLAN 간 라우팅, Trunk Allowed VLAN |
| 05 | [EIGRP](./05_eigrp/eigrp.md) | EIGRP 100, `network`, `no auto-summary`, DUAL |
| 06 | [OSPF](./06_ospf/ospf.md) | OSPF Process/Area/Router-ID, `passive-interface default`, LSA |

---

## 🧪 실습 (Labs)

| # | 주제 | 내용 |
| --- | --- | --- |
| Lab 01 | [Routed Port + EIGRP](./labs/lab01_routed-port-eigrp) | `no switchport` L3 포트 + EIGRP 100 라우팅 (SW1/SW2/SW3/R4) |
| Lab 02 | [SVI + VTP + OSPF](./labs/lab02_svi-vtp-ospf) | VTP로 VLAN 동기화 → SVI Gateway → OSPF 라우팅 (SW1/SW2/SW3) |

각 Lab 폴더에는 `topology` 이미지와 `preconfig/*.txt` (GNS3용 사전 설정)가 포함됩니다.

---

## 💡 핵심 요약

- **L3 Switch**: L2 스위칭 + L3 라우팅을 동시에 수행하는 멀티레이어 스위치
- **ip routing**: L3 라우팅 기능 활성화 (모든 L3 동작의 전제 조건)
- **Routed Port**: `no switchport`로 만든 L3 물리 포트, IP를 직접 할당 (P2P 링크용)
- **SVI**: VLAN에 IP를 부여하는 가상 인터페이스, VLAN Gateway 및 관리 IP 용도
- **Inter-VLAN Routing**: SVI 또는 Routed Port를 통해 VLAN 간 통신 수행
- **EIGRP**: Cisco의 Advanced Distance Vector, DUAL 알고리즘, 빠른 수렴
- **OSPF**: Link-State, Area 기반, SPF(Dijkstra) 알고리즘
- **passive-interface**: 불필요한 인터페이스로 라우팅 업데이트 송신 차단

---

## 📊 Routed Port vs SVI 비교

| 구분 | Routed Port | SVI |
| --- | --- | --- |
| 동작 계층 | L3 (물리 포트) | L3 (VLAN 가상 인터페이스) |
| 설정 명령 | `no switchport` | `interface vlan X` |
| IP 할당 | 포트에 직접 | VLAN에 부여 |
| 주 용도 | P2P L3 링크 (스위치-라우터 간) | VLAN Gateway, 관리 IP |
| VLAN 소속 | 없음 (라우팅 전용) | 특정 VLAN |
| 공통 전제 | `ip routing` 필요 | `ip routing` 필요 |

---

## 🛠️ 실습 환경

| 도구 | 용도 |
| --- | --- |
| **GNS3** | 토폴로지 구성 및 시뮬레이션 |
| **Cisco IOSvL2 / 3560 / 3650** | L3 Switch 이미지 |
| **Cisco Packet Tracer** | 간편 실습 |
| **MobaXterm** | 콘솔 접속 |
| **Git / GitHub** | 버전 관리 |

---

## License

[MIT License](./LICENSE)

작성: [KSNAM97](https://github.com/KSNAM97)
