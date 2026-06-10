# 06. OSPF (Open Shortest Path First)

## 📌 OSPF란?

**OSPF** 는 개방형 표준 **Link-State** 라우팅 프로토콜이다.
SPF(Dijkstra) 알고리즘으로 최단 경로를 계산하며, Area 기반의 계층 구조를 가진다.

| 항목 | 내용 |
| --- | --- |
| 분류 | Link-State |
| 알고리즘 | SPF (Dijkstra) |
| Administrative Distance | 110 |
| Metric | Cost (기본: 100Mbps / 대역폭) |
| 멀티캐스트 | 224.0.0.5 / 224.0.0.6 |
| 구조 | Area 기반 (Area 0 = Backbone) |

## ⚙️ 기본 설정

```bash
en
conf t
!
ip routing
!
router ospf 1
 router-id 1.1.1.1
 network 13.13.11.0 0.0.0.255 area 0
 network 13.13.12.0 0.0.0.255 area 0
!
end
```

| 명령어 | 설명 |
| --- | --- |
| `router ospf 1` | OSPF Process ID (로컬 의미, 이웃과 달라도 됨) |
| `router-id X.X.X.X` | OSPF 라우터 식별자 (고유해야 함) |
| `network ... area 0` | 광고 네트워크 + Wildcard + Area 지정 |

## 🔇 passive-interface

라우팅 업데이트가 **필요 없는 인터페이스**로 OSPF 패킷이 송신되는 것을 막는다.
PC가 연결된 SVI나 Loopback처럼 이웃이 없는 곳은 passive 처리한다.

```bash
router ospf 1
 passive-interface default          ! 모든 인터페이스를 passive로
 no passive-interface vlan12        ! 이웃이 있는 인터페이스만 해제
```

| 명령어 | 설명 |
| --- | --- |
| `passive-interface default` | 모든 인터페이스를 passive로 설정 |
| `no passive-interface vlanX` | 통신이 필요한 인터페이스만 해제 |

> 💡 보안 + 효율 측면에서 권장되는 구성. 불필요한 Hello를 차단한다.

## 🌐 실습 구성 (Lab 02 기준)

### SW1
```bash
router ospf 1
 router-id 1.1.1.1
 passive-interface default
 no passive-interface vlan12
 network 13.13.11.0 0.0.0.255 area 0
 network 13.13.12.0 0.0.0.255 area 0
 network 13.13.1.0 0.0.0.255 area 0
```

### SW2
```bash
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface vlan12
 no passive-interface vlan13
 network 13.13.13.0 0.0.0.255 area 0
 network 13.13.12.0 0.0.0.255 area 0
 network 13.13.2.0 0.0.0.255 area 0
```

### SW3
```bash
router ospf 1
 router-id 3.3.3.3
 passive-interface default
 no passive-interface vlan13
 network 13.13.13.0 0.0.0.255 area 0
 network 13.13.14.0 0.0.0.255 area 0
 network 13.13.3.0 0.0.0.255 area 0
```

## ✅ 검증

```bash
show run | section router ospf
show ip ospf neighbor
show ip route
show ip ospf interface brief
```

### Neighbor 예시 (SW2)
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/BDR        00:00:30    13.13.13.3      Vlan13
1.1.1.1           1   FULL/DR         00:00:31    13.13.12.1      Vlan12
```

> 이웃 상태가 **FULL** 이면 정상적으로 LSDB 동기화가 완료된 것이다.
> OSPF 학습 경로는 라우팅 테이블에 `O` 코드로 표시된다.

## 📊 EIGRP vs OSPF

| 구분 | EIGRP | OSPF |
| --- | --- | --- |
| 분류 | Advanced Distance Vector | Link-State |
| 알고리즘 | DUAL | SPF (Dijkstra) |
| 표준 | Cisco (현재는 공개) | 개방형 표준 |
| AD | 90 | 110 |
| Metric | BW + Delay | Cost |
| 구조 | 평면적(AS) | 계층적(Area) |
| 식별 | AS 번호 일치 | Process ID(로컬), Area 일치 |

## 💡 핵심 정리

- OSPF = 개방형 표준 Link-State, SPF 알고리즘
- Process ID는 로컬, **Area와 네트워크 정보**가 이웃 형성에 중요
- `passive-interface default` + `no passive-interface`로 효율적 구성
- 이웃 상태 **FULL** = 정상, 라우팅 테이블에 `O`로 표시
