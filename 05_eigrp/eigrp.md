# 05. EIGRP (Enhanced Interior Gateway Routing Protocol)

## 📌 EIGRP란?

**EIGRP** 는 Cisco가 개발한 **Advanced Distance Vector** 라우팅 프로토콜이다.
DUAL(Diffusing Update Algorithm)을 사용하여 빠르고 안정적인 수렴을 제공한다.

| 항목 | 내용 |
| --- | --- |
| 분류 | Advanced Distance Vector (Hybrid) |
| 알고리즘 | DUAL |
| Administrative Distance | 90 (내부), 170 (외부) |
| Metric | Bandwidth + Delay 기반 (기본) |
| 멀티캐스트 | 224.0.0.10 |
| AS 번호 | 동일 AS끼리만 이웃 형성 |

## ⚙️ 기본 설정

```bash
en
conf t
!
ip routing
!
router eigrp 100
 no auto-summary
 network 13.13.10.0 0.0.0.255
 network 13.13.12.0 0.0.0.255
!
end
```

| 명령어 | 설명 |
| --- | --- |
| `router eigrp 100` | EIGRP 활성화, **100 = AS 번호** (이웃 간 동일해야 함) |
| `no auto-summary` | 클래스 자동 요약 비활성화 (서브넷 그대로 광고) |
| `network X.X.X.X 0.0.0.255` | 광고할 네트워크 + Wildcard Mask |

## 🌐 실습 구성 (Lab 01 기준)

```
[SW1] --- [SW2] --- [SW3]
            │
          [R4] --- VLAN(13.13.44.0)
```

### SW1
```bash
router eigrp 100
 no auto-summary
 network 13.13.10.0 0.0.0.255
 network 13.13.12.0 0.0.0.255
```

### SW2
```bash
router eigrp 100
 no auto-summary
 network 13.13.20.0 0.0.0.255
 network 13.13.12.0 0.0.0.255
 network 13.13.23.0 0.0.0.255
 network 13.13.22.0 0.0.0.255
```

### SW3
```bash
router eigrp 100
 no auto-summary
 network 13.13.30.0 0.0.0.255
 network 13.13.23.0 0.0.0.255
```

### R4
```bash
router eigrp 100
 no auto-summary
 network 13.13.22.0 0.0.0.255
 network 13.13.44.0 0.0.0.255
```

## ✅ 검증

```bash
show ip eigrp neighbors
show ip route eigrp
show ip protocols
show ip eigrp topology
```

EIGRP 학습 경로는 라우팅 테이블에 `D` 코드로 표시된다.

## 💡 핵심 정리

- EIGRP = Cisco의 Advanced Distance Vector, DUAL 알고리즘
- **AS 번호(100)** 가 이웃 간 동일해야 인접 관계 성립
- `no auto-summary`로 서브넷 그대로 광고
- `network` + Wildcard Mask로 광고 네트워크 지정
- 라우팅 테이블에 `D`로 표시
