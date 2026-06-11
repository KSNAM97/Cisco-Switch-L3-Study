# 01. Layer 3 Switch Basics (Multi-Layer Switch)

## 📌 L3 Switch란?

**Layer 3 Switch(Multi-Layer Switch)** 는 Layer 2 스위칭 기능과 Layer 3 라우팅 기능을
하나의 장비에서 동시에 수행하는 스위치이다.

- **L2 기능**: MAC 주소 기반 스위칭, VLAN, Trunk, STP
- **L3 기능**: IP 기반 라우팅, Inter-VLAN Routing, 동적 라우팅 프로토콜(EIGRP/OSPF)

## 📊 L2 Switch vs L3 Switch vs Router

| 구분 | L2 Switch | L3 Switch | Router |
| --- | --- | --- | --- |
| 동작 계층 | Layer 2 | Layer 2 + 3 | Layer 3 |
| 판단 기준 | MAC 주소 | MAC + IP | IP 주소 |
| 라우팅 | ❌ | ✅ (하드웨어 기반, 고속) | ✅ (소프트웨어 기반) |
| 포트 밀도 | 높음 | 높음 | 낮음 |
| WAN 인터페이스 | ❌ | 보통 ❌ | ✅ |
| 주 용도 | 액세스 계층 | 분배/코어 계층 | WAN 연결, 외부 라우팅 |

> L3 Switch는 라우팅을 **ASIC(하드웨어)** 으로 처리하여 라우터보다 빠르지만,
> 다양한 WAN 인터페이스나 고급 정책 기능은 라우터가 더 강하다.

## ⚙️ ip routing - 모든 L3 동작의 전제

L3 스위치에서 라우팅 기능은 **기본적으로 비활성화**되어 있을 수 있다.
모든 L3 기능(Routed Port, SVI Inter-VLAN, 동적 라우팅) 전에 반드시 활성화해야 한다.

```bash
en
conf t
!
ip routing
!
end
```

확인:
```bash
show ip route
show running-config | include ip routing
```

## 🔀 L3 인터페이스의 두 가지 형태

L3 스위치에서 IP를 할당하는 방법은 크게 두 가지이다.

### 1) Routed Port (라우티드 포트)
물리 포트를 `no switchport`로 전환하여 L3 포트로 만들고 IP를 직접 할당.
주로 스위치-라우터, 스위치-스위치 간 **P2P L3 링크**에 사용.

```bash
int e0/0
 no switchport
 ip address 13.13.12.1 255.255.255.0
 no sh
```

### 2) SVI (Switched Virtual Interface)
VLAN에 IP를 부여하는 가상 인터페이스. **VLAN Gateway** 및 **관리 IP** 용도.

```bash
int vlan 11
 ip address 13.13.11.1 255.255.255.0
 no sh
```

## 💡 핵심 정리

- L3 Switch = L2 스위칭 + L3 라우팅 (멀티레이어)
- 모든 L3 동작의 전제는 `ip routing` 활성화
- IP 할당 방식: **Routed Port**(물리, P2P) / **SVI**(VLAN, Gateway)
- 라우팅을 하드웨어(ASIC)로 처리해 고속이며 분배/코어 계층에 사용

  ---

🏠 [README](../README.md) | ➡️ 다음: [02. Routed Port](../02_routed-port/routed-port.md)

