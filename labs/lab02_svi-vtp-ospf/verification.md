# Lab 02 검증 결과 (OSPF)

## SW1 - OSPF Neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:31    13.13.12.2      Vlan12
```

## SW1 - Routing Table (요약)
```text
13.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
C        13.13.1.0/24 is directly connected, Loopback0
L        13.13.1.1/32 is directly connected, Loopback0
O        13.13.2.2/32 [110/2] via 13.13.12.2, Vlan12
O        13.13.3.3/32 [110/3] via 13.13.12.2, Vlan12
C        13.13.11.0/24 is directly connected, Vlan11
L        13.13.11.1/32 is directly connected, Vlan11
C        13.13.12.0/24 is directly connected, Vlan12
L        13.13.12.1/32 is directly connected, Vlan12
O        13.13.13.0/24 [110/2] via 13.13.12.2, Vlan12
```

## SW1 - OSPF Interface Brief
```text
Interface    PID   Area   IP Address/Mask    Cost  State Nbrs F/C
Lo0          1     0      13.13.1.1/24       1     LOOP  0/0
Vl12         1     0      13.13.12.1/24      1     DR    1/1
Vl11         1     0      13.13.11.1/24      1     DR    0/0
```

## SW2 - OSPF Neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/BDR        00:00:30    13.13.13.3      Vlan13
1.1.1.1           1   FULL/DR         00:00:31    13.13.12.1      Vlan12
```

## SW3 - OSPF Neighbor
```text
Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:33    13.13.13.2      Vlan13
```

> 모든 이웃이 **FULL** 상태이면 LSDB 동기화 완료 = 정상 동작.
