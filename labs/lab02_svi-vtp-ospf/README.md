# Lab 02. SVI + VTP + OSPF

**VTP** 로 VLAN을 동기화하고, **SVI** 를 VLAN Gateway로 구성한 뒤,
**OSPF (Process 1, Area 0)** 로 전 구간을 라우팅하는 실습입니다.

---

## 🗺️ 토폴로지

![Topology](./topology/topology-svi.png)

```
   [SW1] VTP Server                [SW2] VTP Client              [SW3] VTP Client
   VLAN12: 13.13.12.1   Gi0/0 ───── Gi0/0 VLAN12:13.13.12.12 ──── Gi0/1
   VLAN11: 13.13.11.1               Gi0/1 VLAN13:13.13.13.2  Gi0/1 VLAN13: 13.13.13.3
   Loopback0: 13.13.1.1            Loopback0: 13.13.2.2           VLAN14: 13.13.14.1
        │ Gi3/2-3                                                Loopback0: 13.13.3.3
   [PC1][PC2] VLAN11                                                 │ Gi3/2-3
   .101 / .102                                                   [PC3][PC4] VLAN14
                                                                  .101 / .102
```

---

## 📋 IP / VLAN 설계

| 장비 | VTP 역할 | Loopback 0 | SVI |
| --- | --- | --- | --- |
| SW1 | Server | 13.13.1.1/24 | VLAN11: 13.13.11.1 / VLAN12: 13.13.12.1 |
| SW2 | Client | 13.13.2.2/24 | VLAN12: 13.13.12.2 / VLAN13: 13.13.13.2 |
| SW3 | Client | 13.13.3.3/24 | VLAN13: 13.13.13.3 / VLAN14: 13.13.14.1 |

| PC | VLAN | IP | Gateway |
| --- | --- | --- | --- |
| PC1 | 11 | 13.13.11.101/24 | 13.13.11.1 |
| PC2 | 11 | 13.13.11.102/24 | 13.13.11.1 |
| PC3 | 14 | 13.13.14.101/24 | 13.13.14.1 |
| PC4 | 14 | 13.13.14.102/24 | 13.13.14.1 |

**VTP**: domain = `SOL`, password = `Sol1234`

---

## ⚙️ 구성 순서

```
1) Trunk 설정 + VTP (Server/Client, domain/password)
2) VLAN 11-14 생성 (SW1 Server에서 생성 → Client로 동기화)
3) Trunk Allowed VLAN + Access 포트 설정
4) SVI 생성 (VLAN Gateway IP 할당) + ip routing
5) Loopback 0 생성
6) OSPF 구성 (Process 1 / Area 0 / Router-ID / passive-interface)
7) 검증 (neighbor / route / ping)
```

설정 파일은 [`preconfig/`](./preconfig) 폴더 참고.

---

## ✅ 검증

```bash
show vtp status
show interfaces trunk
show ip ospf neighbor
show ip route
show ip ospf interface brief
```

> 이웃 상태가 **FULL** 이면 정상. OSPF 경로는 라우팅 테이블에 `O`로 표시됩니다.
