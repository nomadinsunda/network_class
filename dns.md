# 🌐 DNS 완벽 가이드: 도메인 이름 시스템의 모든 것

## 🔎 개요: DNS란?

DNS(Domain Name System)는 **도메인 이름을 IP 주소로 변환**해주는 **인터넷의 전화번호부** 역할을 합니다.

* `www.google.com` ➝ `142.250.206.100`
* 사람이 이해하기 쉬운 도메인 → 컴퓨터가 이해할 수 있는 IP 주소

DNS가 없다면, 우리는 웹사이트에 접속할 때마다 IP 주소를 직접 입력해야 합니다.

---

## 🏗️ DNS 아키텍처: 계층적 구조

DNS는 \*\*루트(Root)\*\*를 정점으로 하는 **계층적 구조**를 가집니다.

```
[.]  (Root)
 ├── [com]
 │    └── [google]
 │         └── www.google.com
 └── [org]
      └── [wikipedia]
           └── www.wikipedia.org
```

* **Root Domain**: DNS의 최상위 (`.` 로 표기됨)
* **TLD (Top-Level Domain)**: `.com`, `.net`, `.org`, `.kr` 등
* **Second-Level Domain**: `google`, `wikipedia` 등
* **Subdomain**: `www`, `mail`, `blog` 등

---

## 🧭 이름 해석 과정 (Name Resolution)

사용자가 브라우저에서 `www.google.com`을 입력하면 다음과 같은 순서로 IP 주소가 조회됩니다.

### 🔄 1. **로컬 캐시 확인**

* 운영체제나 브라우저에 IP 주소가 캐시되어 있는지 확인

### 🔄 2. **hosts 파일 확인**

* `/etc/hosts` 또는 `C:\Windows\System32\drivers\etc\hosts`

### 🔄 3. **재귀(Recursive) 질의 → 로컬 DNS 서버**

* 보통 ISP 또는 기업 내 DNS 서버에 질의
* 재귀 질의는 **대신 찾아달라**는 의미

### 🔄 4. **DNS 서버의 반복 질의 과정**

로컬 DNS 서버가 정보를 모를 경우:

```
1. Root 서버에 질의: .com 어디 있나요?
2. TLD 서버에 질의: google.com은 어디 있나요?
3. Authoritative(권한 있는) 서버에 질의: www.google.com은 어디 있나요?
```

### 🔄 5. **IP 주소 반환**

* 권한 있는 DNS 서버에서 응답한 IP 주소가 브라우저로 전달되고, 해당 IP로 접속

---

## 📦 주요 DNS 레코드 타입

| 유형      | 설명                        | 예시                               |
| ------- | ------------------------- | -------------------------------- |
| `A`     | IPv4 주소                   | `example.com → 93.184.216.34`    |
| `AAAA`  | IPv6 주소                   | `example.com → 2606:2800::...`   |
| `CNAME` | 별칭 지정                     | `www.example.com → example.com`  |
| `MX`    | 메일 서버                     | `mail.example.com`               |
| `NS`    | 권한 있는 네임서버                | `ns1.example.com`                |
| `PTR`   | IP → 도메인 (역방향 조회)         | `93.184.216.34 → example.com`    |
| `TXT`   | 텍스트 정보 (SPF, DMARC 포함)    | `v=spf1 include:_spf.google.com` |
| `SRV`   | 서비스 위치 지정                 | SIP, LDAP 등                      |
| `SOA`   | Start of Authority, 관리 정보 | 영역의 시작점 정보                       |

---

## 🛡️ DNS 보안: 위협과 방어

DNS는 **보안에 취약한 프로토콜** 중 하나입니다.

### 주요 위협

| 공격 방식               | 설명                    |
| ------------------- | --------------------- |
| DNS Spoofing        | 가짜 응답을 보내 IP를 속임      |
| DNS Cache Poisoning | DNS 서버 캐시에 악의적 레코드 주입 |
| DNS Amplification   | 요청을 증폭시켜 DDoS 공격 수행   |
| DNS Hijacking       | DNS 요청을 공격자 서버로 리디렉션  |

### 방어 메커니즘

* **DNSSEC (DNS Security Extensions)**
  👉 응답의 무결성 및 출처 검증 (서명 사용)

* **DoT (DNS over TLS), DoH (DNS over HTTPS)**
  👉 암호화된 DNS 요청 전송으로 도청/변조 방지

* **Split DNS**
  👉 내부와 외부에서 서로 다른 응답 반환

---

## 🧪 실전: DNS 레코드 확인 도구

### 1. `dig` (Linux/macOS)

```bash
dig www.google.com
dig +trace www.google.com
dig -x 8.8.8.8  # 역방향
```

### 2. `nslookup` (Windows/Linux)

```bash
nslookup www.example.com
server 8.8.8.8
```

### 3. `host`

```bash
host www.naver.com
```

---

## 💼 실무 활용 사례

### 1. **웹사이트 트래픽 관리**

* 여러 `A` 레코드로 부하 분산 (Round-Robin DNS)
* CDN과 함께 CNAME 사용 (ex: `cdn.example.com → cloudfront.net`)

### 2. **기업 내 내부 DNS 서버 운영**

* 내부 전용 도메인: `internal.corp.local`
* Active Directory, 메일 서버, VPN 서비스

### 3. **도메인 소유권 인증**

* TXT 레코드를 통한 Google, AWS, GitHub 인증

---

## 📈 캐시와 TTL

* 모든 DNS 응답은 **TTL(Time-To-Live)** 값을 포함함
* TTL이 짧으면 변경 반영 빠름, 길면 성능 향상

```bash
example.com.  3600  IN  A  93.184.216.34  ← 1시간 동안 캐시
```

---

## 🧠 정리

| 키워드    | 설명 요약                        |
| ------ | ---------------------------- |
| DNS    | 도메인 이름을 IP로 변환하는 시스템         |
| 계층 구조  | Root → TLD → SLD → Subdomain |
| 재귀 질의  | 클라이언트가 최종 결과 받을 때까지 탐색       |
| 레코드 타입 | A, CNAME, MX, TXT 등          |
| 보안     | DNSSEC, DoH, DoT 필수 고려       |

---

## 📚 참고 자료

* [RFC 1034](https://datatracker.ietf.org/doc/html/rfc1034): Domain Names - Concepts and Facilities
* [RFC 1035](https://datatracker.ietf.org/doc/html/rfc1035): Domain Names - Implementation and Specification
* [ICANN DNSSEC](https://www.icann.org/dnssec)
* [Cloudflare DNS 설명서](https://www.cloudflare.com/learning/dns/)

