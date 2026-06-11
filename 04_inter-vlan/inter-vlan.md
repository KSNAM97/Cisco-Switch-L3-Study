# 04. Inter-VLAN Routing (SVI 기반)

## 📌 Inter-VLAN Routing이란?

서로 다른 VLAN은 기본적으로 **L2에서 통신할 수 없다** (Broadcast Domain 분리).
VLAN 간 통신을 위해서는 L3 장비를 거치는 **Inter-VLAN Routing**이 필요하다.

## 🔀 Inter-VLAN Routing 방식

| 방식 | 설명 | 특징 |
| --- | --- | --- |
| Router-on-a-Stick | 라우터 1개 + Trunk + 서브인터페이스 | 대역폭 병목, L2 학습용 |
| **L3 Switch (SVI)** | 멀티레이어 스위치에서 SVI를 Gateway로 사용 | **고속, 실무 표준** |
| Routed Port | L3 물리 포트로 직접 라우팅 | P2P 링크용 |

> 본 챕터는 L3 Switch의 **SVI 기반 Inter-VLAN Routing**을 다룬다.

## ⚙️ 구성 순서

```
1) ip routing 활성화
2) VLAN 생성 (또는 VTP로 동기화)
3) Trunk / Access 포트 설정 + allowed vlan
4) 각 VLAN에 SVI 생성 후 Gateway IP 할당
5) PC의 Gateway를 해당 SVI IP로 설정
```

## 🖥️ 예시 구성

### Trunk / Access + Allowed VLAN
```bash
en
conf t
!
int gi0/0
 switchport mode trunk
 switchport trunk allowed vlan 12
!
int range gi3/2-3
 switchport mode access
 switchport access vlan 11
!
end
```

### SVI Gateway 생성
```bash
en
conf t
!
ip routing
!
int vlan 11
 ip address 13.13.11.1 255.255.255.0
 no sh
!
int vlan 12
 ip address 13.13.12.1 255.255.255.0
 no sh
!
end
```

## 🔑 Trunk Allowed VLAN 핵심

- Trunk 링크에서 **허용된 VLAN만** 태깅되어 전달된다.
- `switchport trunk allowed vlan 12,13` → VLAN 12, 13만 통과
- 불필요한 VLAN을 제한하여 보안 및 대역폭 효율을 높인다.

## ✅ 검증

```bash
show ip route
show interfaces trunk
show vlan brief
ping 13.13.14.1   ! 다른 VLAN Gateway로 통신 확인
```

## 💡 핵심 정리

- VLAN 간 통신은 반드시 L3 라우팅 필요
- L3 Switch에서는 **SVI를 Gateway로 사용**하는 방식이 표준
- Trunk의 `allowed vlan`으로 통과 VLAN을 제어
- PC의 Gateway는 해당 VLAN의 SVI IP로 설정

  ---

**⬅️ 이전: [02. Routed Port](../02_routed-port/routed-port.md) | 🏠 [README](../README.md) | ➡️ 다음: [04. Inter-VLAN Routing](../04_inter-vlan/inter-vlan.md)**

