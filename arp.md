# 🔍 ARP 완전 정복: IP와 MAC 사이의 필연적인 다리

> 네트워크의 근간을 이해하려면 반드시 알아야 할 프로토콜 — ARP의 원리, 구조, 보안 이슈까지 깊이 있는 해부

---

## 📌 1. ARP란?

\*\*ARP(Address Resolution Protocol)\*\*는 **IPv4 네트워크에서 IP 주소를 MAC 주소로 변환**하는 데 사용되는 필수 프로토콜입니다.

> ✅ IP는 논리 주소, MAC은 물리 주소
> 네트워크에서 실제 데이터를 전송하려면 목적지 MAC 주소가 반드시 필요합니다.

---

## 🧱 2. ARP가 필요한 이유

| 항목          | 설명                                     |
| ----------- | -------------------------------------- |
| **IP 주소**   | 네트워크 계층(3계층)의 논리 주소                    |
| **MAC 주소**  | 데이터 링크 계층(2계층)의 실제 주소                  |
| **스위치/이더넷** | MAC 주소 기반으로 프레임 전달                     |
| **필요성**     | IP로 목적지를 알고 있어도, MAC 주소 없이는 실제 전송이 불가능 |

> 🧠 즉, ARP는 IP에서 MAC으로의 주소 해석(resolution)을 수행합니다.

---

## 📘 3. ARP 동작 원리

### 💡 예시 시나리오

* PC A: IP = 192.168.1.10
* PC B: IP = 192.168.1.20
* PC A가 PC B에게 패킷을 전송하고 싶을 때:

### 🔄 ARP 요청 / 응답 흐름

1. **ARP Request**

   * 송신자: 192.168.1.10
   * 목적지: "192.168.1.20의 MAC 주소를 알려주세요!"
   * 대상 MAC 주소: `FF:FF:FF:FF:FF:FF` (브로드캐스트)

2. **ARP Reply**

   * 응답자: 192.168.1.20
   * 내용: "나는 192.168.1.20이고, 내 MAC은 aa\:bb\:cc\:dd\:ee\:ff야"
   * 대상 MAC 주소: 192.168.1.10의 MAC (유니캐스트)

3. **캐시 저장**

   * 송신자는 응답받은 MAC 주소를 \*\*ARP 테이블(캐시)\*\*에 저장하고 다음부터는 ARP 요청 없이 전송

---

## 📦 4. ARP 패킷 구조

```
0                7 8               15 16              31
+----------------+------------------+------------------+
| Hardware Type  | Protocol Type    | Hardware Size    |
+----------------+------------------+------------------+
| Protocol Size  | Opcode           | Sender MAC       |
+----------------+------------------+------------------+
| Sender IP      | Target MAC       | Target IP        |
+------------------------------------------------------+
```

* **Hardware Type**: 1 (이더넷)
* **Protocol Type**: 0x0800 (IPv4)
* **Opcode**: 1=Request, 2=Reply
* **Sender/Target MAC, IP**: 요청자와 응답자의 정보

---

## 🔐 5. 보안 이슈: ARP Spoofing / ARP Poisoning

ARP는 **신뢰 기반의 비보안 프로토콜**입니다. 인증도, 검증도 없기 때문에 다양한 공격이 가능합니다.

### 💣 ARP Spoofing

* 공격자가 자신을 **게이트웨이인 척 위장**하여 트래픽을 가로채는 기법
* 예: Attacker → "나는 192.168.1.1(Default Gateway)이야, MAC 주소는 내 거야"
* 피해자: 공격자에게 모든 인터넷 트래픽 전송 → **MITM(중간자 공격)** 발생

### 🔎 대응 방법

| 방법                               | 설명                        |
| -------------------------------- | ------------------------- |
| **Static ARP**                   | 중요한 장비에 대해 정적 ARP 테이블 설정  |
| **Dynamic ARP Inspection (DAI)** | 스위치가 ARP 패킷을 검증           |
| **Port Security**                | MAC 주소 제한 및 학습 제어         |
| **IP/MAC 바인딩**                   | DHCP Snooping과 연계하여 위조 방지 |

---

## 🛠️ 6. 실무에서의 활용

### 🧪 ARP 테이블 확인

```bash
# Windows
arp -a

# Linux/macOS
ip neigh
```

### 🔧 수동 ARP 등록

```bash
# Windows
arp -s 192.168.1.20 aa-bb-cc-dd-ee-ff

# Linux/macOS
ip neigh add 192.168.1.20 lladdr aa:bb:cc:dd:ee:ff dev eth0
```

### 🧹 ARP 테이블 초기화

```bash
# Windows
arp -d *

# Linux
ip neigh flush all
```

---

## 🔄 7. ARP의 한계와 진화

### IPv6에서는 ARP가 사용되지 않는다?

> 맞습니다. IPv6에서는 ARP를 대체하기 위해 \*\*NDP (Neighbor Discovery Protocol)\*\*를 사용합니다.

| 프로토콜       | 역할                       | 계층            |
| ---------- | ------------------------ | ------------- |
| ARP (IPv4) | IP → MAC 해석              | 데이터 링크 (2계층)  |
| NDP (IPv6) | 주소 해석 + 라우터 발견 + 상태 검사 등 | 네트워크 계층 (3계층) |

---

## 📌 정리: ARP는 “보이지 않는 다리”

| 요소        | 설명                       |
| --------- | ------------------------ |
| **정의**    | IP 주소를 MAC 주소로 변환하는 프로토콜 |
| **방식**    | 브로드캐스트 요청 + 유니캐스트 응답     |
| **보안 위험** | 인증 없는 구조 → ARP 스푸핑       |
| **실무 활용** | ping 안될 때 ARP 테이블 먼저 확인  |
| **미래 방향** | IPv6에서는 NDP로 대체됨         |

---

## 📚 참고 자료

* [RFC 826 - ARP Specification](https://datatracker.ietf.org/doc/html/rfc826)
* Wireshark ARP 패킷 캡처 실습
* Cisco ARP Inspection Best Practice

