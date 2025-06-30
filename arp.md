# 🔍 ARP란 무엇인가요?
**ARP(Address Resolution Protocol)**는 네트워크 상에서 IP 주소 → MAC 주소로 변환해주는 프로토콜입니다. 이더넷과 같은 2계층 통신은 MAC 주소 기반으로 이루어지기 때문에, 네트워크 장치가 데이터를 전송하려면 대상의 MAC 주소를 반드시 알아야 합니다.

# 🧱 ARP가 필요한 이유
항목	설명
IP 주소	네트워크 계층(3계층)의 논리 주소
MAC 주소	데이터 링크 계층(2계층)의 실제 주소
스위치/이더넷	MAC 주소 기반으로 프레임 전달
필요성	IP로 목적지를 알고 있어도, MAC 주소 없이는 실제 전송이 불가능

> 🧠 즉, ARP는 IP에서 MAC으로의 주소 해석(resolution)을 수행합니다.



# 📡 ARP는 어떻게 작동할까?

> IP 주소만으로는 부족하다! 네트워크 통신을 위한 필수 프로토콜, ARP의 내부 동작을 시각적으로 이해하기

---

## 🔍 ARP란 무엇인가요?

\*\*ARP(Address Resolution Protocol)\*\*는 네트워크 상에서 **IP 주소 → MAC 주소로 변환**해주는 프로토콜입니다. 이더넷과 같은 **2계층 통신은 MAC 주소 기반**으로 이루어지기 때문에, 네트워크 장치가 데이터를 전송하려면 대상의 MAC 주소를 반드시 알아야 합니다.

---

## 🧠 시나리오로 보는 ARP 작동 방식

아래 이미지는 ClouDNS에서 제공한 ARP 작동 흐름을 시각적으로 잘 표현하고 있습니다.

![How ARP Works](attachment:/mnt/data/How-Does-ARP-Work.png)

---

### 📘 등장인물

| 장치       | IP 주소       | 설명              |
| -------- | ----------- | --------------- |
| Device A | 123.10.20.1 | 송신자 (ARP 요청 발생) |
| Device B | 123.10.20.2 | 수신자 (ARP 응답 대상) |
| Device C | 123.10.20.3 | 같은 네트워크의 제3자    |
| Device D | 123.10.20.4 | 같은 네트워크의 제4자    |
| Router   | -           | 브로드캐스트 중계자      |

---

## 🔄 ARP 작동 흐름 단계별 설명

### 1️⃣ Device A → ARP Request

Device A가 IP 주소 `123.10.20.2`에 데이터를 전송하고자 할 때, 해당 장치의 **MAC 주소를 알지 못하면 전송 불가**합니다. 이때 ARP 요청 패킷을 생성하여 네트워크에 **브로드캐스트**합니다.

```
"I am looking for 123.10.20.2. What is your MAC address?"
```

* 목적 MAC 주소: `FF:FF:FF:FF:FF:FF` (브로드캐스트)
* 송신 MAC 주소: Device A의 MAC 주소
* 대상 IP 주소: 123.10.20.2

> 이 브로드캐스트는 동일 서브넷의 모든 장치(Device B, C, D 등)가 수신합니다.

---

### 2️⃣ Device B → ARP Reply

요청을 받은 장치 중, \*\*자신의 IP가 요청한 IP와 일치하는 장치(Device B)\*\*만 응답을 합니다.

```
That is me! Device B!  
Here is my MAC address: 00:1b:63:84:45:e6
```

* 목적 MAC 주소: Device A의 MAC 주소 (유니캐스트)
* 송신 MAC 주소: Device B의 MAC 주소
* 자신의 IP와 MAC 주소 포함

> 이 응답은 브로드캐스트가 아닌 **유니캐스트**로 Device A에게 직접 전송됩니다.

---

### 3️⃣ ARP 캐시 업데이트

Device A는 응답을 수신한 후, 다음 정보를 **ARP 테이블에 저장**합니다.

```bash
IP: 123.10.20.2 → MAC: 00:1b:63:84:45:e6
```

이후 동일 장치로 데이터를 전송할 때는 **ARP 요청 없이 바로 MAC 주소 사용**이 가능합니다.

📌 ARP 캐시는 일정 시간 후 만료되며, 다시 요청이 발생할 수 있습니다.

---

## 📦 요약: ARP 프로토콜의 특성

| 항목  | 설명                             |
| --- | ------------------------------ |
| 계층  | OSI 2계층 (데이터 링크)와 3계층(IP) 사이   |
| 역할  | IP → MAC 주소 변환                 |
| 방식  | 브로드캐스트 요청, 유니캐스트 응답            |
| 테이블 | ARP Cache에 저장                  |
| TTL | 일반적으로 수 분 내 캐시 만료              |
| 보안  | 인증 없음 → ARP Spoofing 공격 가능성 존재 |

---

## 🔐 보안 관련 주의점

ARP는 **신뢰 기반 프로토콜**로, 요청/응답에 대한 검증을 하지 않습니다. 이로 인해 **ARP Spoofing / ARP Poisoning**과 같은 공격에 노출될 수 있습니다.

### ✅ 보안 대응 방안

* Static ARP 설정
* Dynamic ARP Inspection(DAI)
* DHCP Snooping과 연계된 IP/MAC 바인딩
* 스위치 포트 보안 설정

---

## 🛠️ 실무 팁: ARP 확인 및 조작 명령어

### 🔍 ARP 테이블 확인

```bash
# Windows
arp -a

# Linux/macOS
ip neigh
```

### 🔧 수동 등록 / 삭제

```bash
# 등록
arp -s 123.10.20.2 00-1b-63-84-45-e6

# 삭제
arp -d 123.10.20.2
```

---

## 📚 참고자료

* [RFC 826 - Address Resolution Protocol](https://datatracker.ietf.org/doc/html/rfc826)
* ClouDNS: How Does ARP Work? (이미지 출처)
* Wireshark를 활용한 ARP 패킷 분석

---

## ✅ 마무리

ARP는 "눈에 보이지 않는 네트워크 연결의 다리"입니다. 단순한 프로토콜처럼 보이지만, **네트워크 통신의 시작점**이자 **보안 위협의 타깃**이 되기도 합니다.
위 시각적 흐름을 통해 ARP의 작동 방식이 더욱 명확해졌기를 바랍니다.

> 다음 글에서는 Wireshark로 직접 ARP 패킷을 분석하는 실습을 소개합니다.

---

필요하시다면 이 글을 Markdown, HTML 또는 PDF 포맷으로 제공해드릴 수 있습니다.
