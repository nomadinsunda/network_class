# 🖧 MAC 주소 완전정복: 네트워크 하부의 실체적 식별자

> IP 주소가 논리적 주소라면, MAC은 물리적 주소다. 네트워크 장비 식별의 핵심 요소, MAC 주소의 구조와 역할을 파헤쳐 보자.

---

## 📌 1. MAC 주소란?

\*\*MAC 주소(Media Access Control Address)\*\*는 \*\*네트워크 인터페이스 카드(NIC)\*\*에 고유하게 부여되는 **물리적 주소**입니다.

* \*\*데이터 링크 계층(OSI 2계층)\*\*에서 사용
* \*\*48비트(6바이트)\*\*로 구성
* **이더넷(Ethernet), Wi-Fi 등 모든 LAN 환경에서 사용**

> ✅ MAC 주소는 로컬 네트워크에서 **프레임 전송의 대상과 출발지**를 식별하는 데 사용됩니다.

---

## 🧱 2. MAC 주소 구조

```
00:1B:63:84:45:E6
│   │   └────────────┐
│   └────────┐       │
└────────┐   │       │
         │   │       └──── NIC 식별자 (Device ID, 3바이트)
         │   └──────────── OUI(Organizationally Unique Identifier, 3바이트)
         └──────────────── 전체 48비트 (6바이트)
```

### 구성요소

| 필드         | 길이   | 설명                      |
| ---------- | ---- | ----------------------- |
| **OUI**    | 24비트 | IEEE가 제조사에게 부여하는 고유 식별자 |
| **NIC ID** | 24비트 | 제조사가 자체적으로 관리하는 장치 식별자  |

### 예시

* `00:1B:63`: Cisco Systems, Inc. (OUI)
* `84:45:E6`: 특정 제품의 고유 장치 번호

🔍 IEEE의 [OUI Lookup Tool](https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries)로 확인 가능

---

## 🌐 3. MAC 주소와 IP 주소의 차이

| 항목        | MAC 주소             | IP 주소         |
| --------- | ------------------ | ------------- |
| **종류**    | 물리 주소              | 논리 주소         |
| **계층**    | 데이터 링크 계층 (2계층)    | 네트워크 계층 (3계층) |
| **범위**    | 로컬 네트워크 내 유효       | 전 세계 범위       |
| **변경 여부** | 일반적으로 고정           | 동적 또는 수동 할당   |
| **예시**    | 00:1B:63:84:45\:E6 | 192.168.0.101 |

---

## 🔄 4. MAC 주소는 어떻게 사용되나?

### 📦 프레임 전송 시

| 송신자    | 수신자                                | MAC 주소      |
| ------ | ---------------------------------- | ----------- |
| 송신 MAC | 수신 MAC                             | 프레임 헤더에 포함됨 |
| ✔️ 스위치 | MAC 주소 테이블 기반으로 프레임 전달             |             |
| ❌ 라우터  | 2계층을 넘어 IP 기반으로 처리됨 (MAC 주소 변경 발생) |             |

### 🧭 ARP (Address Resolution Protocol)

* IP → MAC 매핑을 위한 프로토콜
* `ARP Request → ARP Reply → MAC 주소 학습 → 프레임 전송`

---

## ⚠️ 5. MAC 주소와 보안 이슈

### 🎭 MAC Spoofing

* 공격자가 자신의 NIC의 MAC 주소를 위조하여 **다른 장치로 가장**
* 예: DHCP 인증 우회, IP/MAC 바인딩 우회, MAC 기반 ACL 무력화

### 🚨 DoS 공격

* MAC Flooding → 스위치의 MAC 주소 테이블 초과 → 브로드캐스트로 전환 → **네트워크 혼잡 유발**

### ✅ 대응 방법

| 방식                               | 설명                   |
| -------------------------------- | -------------------- |
| **Port Security**                | 스위치 포트별 MAC 주소 제한    |
| **802.1X 인증**                    | 사용자 기반 인증 후 포트 개방    |
| **DAI (Dynamic ARP Inspection)** | 위조된 ARP 차단           |
| **MAC Filtering**                | ACL 등으로 특정 MAC 허용/차단 |

---

## 🛠️ 6. 실무에서의 MAC 주소 활용

### 🔍 MAC 주소 확인 명령어

```bash
# Windows
ipconfig /all

# Linux / macOS
ip link show

# 공통
arp -a  # ARP 테이블에 등록된 MAC 목록
```

### 🧪 MAC 주소 변경 (Spoofing 실습 예시)

```bash
# Linux
sudo ip link set dev eth0 address 00:11:22:33:44:55

# macOS
sudo ifconfig en0 ether 00:11:22:33:44:55
```

---

## 📚 참고 자료

* [IEEE MAC 주소 등록 데이터베이스](https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries)
* [RFC 894 - Standard for the Transmission of IP Datagrams over Ethernet Networks](https://datatracker.ietf.org/doc/html/rfc894)
* Cisco 스위치 보안 구성 가이드

---

## ✅ 요약

| 항목 | 설명                                        |
| -- | ----------------------------------------- |
| 정의 | 네트워크 장치(NIC)의 고유 식별자                      |
| 계층 | OSI 2계층                                   |
| 길이 | 48비트 (6바이트)                               |
| 형식 | 00:1B:63:84:45\:E6                        |
| 용도 | 프레임의 출발지/도착지 식별                           |
| 보안 | 위조(MAC Spoofing), Flooding 위험 존재          |
| 대응 | Port Security, 802.1X, DAI, MAC Filtering |


