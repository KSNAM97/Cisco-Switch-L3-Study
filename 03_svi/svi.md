# 03. SVI (Switched Virtual Interface)

## 📌 SVI란?

> **SVI = VLAN에 IP 주소를 설정하는 가상 인터페이스**

- SVI는 스위치 내부에 생성되는 **VLAN 기반의 논리적 가상 인터페이스**이다.
- 일반적인 물리 인터페이스가 아니라, **특정 VLAN에 IP를 설정**하기 위해 사용한다.

## 🔍 SVI의 개념

- 스위치의 일반 포트는 기본적으로 **Layer 2**로 동작한다.
- L2 Switchport는 IP를 직접 갖지 않고, VLAN에 소속되어 **Frame을 전달**한다.
- 예를 들어 PC들이 VLAN 10에 연결되어 있으면, 같은 VLAN 안에서 L2 통신이 가능하다.
- 하지만 **다른 네트워크/다른 VLAN과 통신하려면 Gateway가 필요**하다.
- 이때 Multi-Layer Switch에서는 VLAN 10에 대한 Gateway를 **SVI로 구성**한다.

```
[PC - VLAN 11]  ---- L2 ----  [L3 Switch]
                               └ int vlan 11 (13.13.11.1)  ← Gateway(SVI)
                               └ int vlan 12 (13.13.12.1)
        VLAN 11 ↔ VLAN 12 통신은 SVI를 거쳐 L3 라우팅됨
```

## 🎯 SVI의 두 가지 용도

### 1) 스위치 관리용 IP
- L2 스위치는 장비 자체에 접속하기 위해 관리용 IP가 필요하다.
- SVI에 IP를 설정하여 **Telnet, SSH, Ping, SNMP** 같은 관리 접속에 사용한다.

### 2) VLAN의 Gateway 역할
- Multi-Layer Switch에서는 VLAN마다 SVI를 생성하고 IP를 설정하여
  각 VLAN의 **Default Gateway**로 사용할 수 있다.

## ⚙️ 설정 방법

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

> ⚠️ SVI가 `up` 상태가 되려면 해당 VLAN이 VLAN DB에 존재하고,
> 그 VLAN에 속한 포트가 하나 이상 active 상태여야 한다.

## ✅ 검증

```bash
show ip interface brief
show vlan brief
show ip route connected
```

## 💡 핵심 정리

- SVI = VLAN에 IP를 부여하는 가상 인터페이스 (`interface vlan X`)
- 용도 ① 스위치 관리용 IP, ② VLAN의 Default Gateway
- VLAN과 active 포트가 있어야 SVI가 up 됨
- 사용 전 `ip routing` 활성화 필수 (라우팅 용도일 경우)
