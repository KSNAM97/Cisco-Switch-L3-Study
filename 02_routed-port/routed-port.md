# 02. Routed Port (L3 물리 포트)

## 📌 Routed Port란?

**Routed Port** 는 스위치의 물리 포트를 L2 스위치포트가 아닌
**L3 라우티드 포트**로 전환하여 IP 주소를 직접 할당한 포트이다.

- 기본적으로 스위치 포트는 `switchport` (L2) 상태이다.
- `no switchport` 명령으로 해당 포트를 L3 포트로 전환한다.
- VLAN에 소속되지 않으며, 라우터의 인터페이스처럼 동작한다.

## ⚙️ 설정 방법

```bash
en
conf t
!
ip routing
!
int e0/0
 no switchport
 ip address 13.13.12.1 255.255.255.0
 no sh
!
end
```

| 명령어 | 설명 |
| --- | --- |
| `no switchport` | 포트를 L2 → L3 (Routed Port)로 전환 |
| `ip address X.X.X.X /M` | 라우티드 포트에 IP 직접 할당 |
| `no sh` | 인터페이스 활성화 |

## 🔗 주요 용도 — P2P L3 링크

Routed Port는 주로 다음 구간에 사용된다.

- **스위치 ↔ 라우터** 연결 (예: SW2 e0/2 ↔ R4 fa0/0)
- **스위치 ↔ 스위치** L3 백본 링크 (예: SW1 e0/0 ↔ SW2 e0/0)
- **Loopback** 등 가상 L3 인터페이스도 함께 사용

```
[SW1] e0/0 ---- 13.13.12.0/24 ---- e0/0 [SW2]
 13.13.12.1                          13.13.12.2
```

## 📊 Routed Port vs SVI

| 구분 | Routed Port | SVI |
| --- | --- | --- |
| 설정 | `no switchport` | `interface vlan X` |
| VLAN 소속 | 없음 | 특정 VLAN |
| 용도 | P2P L3 링크 | VLAN Gateway |
| 비유 | 라우터의 물리 인터페이스 | 라우터의 서브인터페이스(논리) |

## ✅ 검증

```bash
show ip interface brief
show interfaces status
show ip route
```

## 💡 핵심 정리

- Routed Port = `no switchport`로 만든 L3 물리 포트
- VLAN에 소속되지 않고 IP를 직접 할당
- 스위치-라우터, 스위치-스위치 간 P2P L3 링크에 사용
- 사용 전 `ip routing` 활성화 필수
