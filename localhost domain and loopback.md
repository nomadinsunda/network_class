# 🧭 `localhost`와 `loopback`의 차이와 내부 작동 원리 완전 정복

---

## 🔍 요약

| 용어          | 의미 요약               |
| ----------- | ------------------- |
| `localhost` | 로컬 시스템을 의미하는 도메인 이름 |
| `loopback`  | 자신에게 되돌아오는 네트워크 경로  |
| `127.0.0.1` | IPv4에서의 루프백 주소      |
| `::1`       | IPv6에서의 루프백 주소      |

---

## 🌐 `localhost`: 도메인 이름

`localhost`는 **자신의 컴퓨터를 지칭하는 도메인 이름**으로, 대부분의 시스템에서 다음처럼 동작합니다:

```bash
localhost → 127.0.0.1 (IPv4)
localhost → ::1       (IPv6)
```

### 📍 DNS에서 어떻게 해석되나?

* `/etc/hosts` (Unix 계열) 또는 `C:\Windows\System32\drivers\etc\hosts` 파일에 기본 설정되어 있음

```txt
127.0.0.1   localhost
::1         localhost
```

* 따라서 DNS 서버에 요청하지 않고, **로컬에서 즉시 이름을 IP로 해석**합니다.

### 📎 관련 특징

* 인터넷을 전혀 거치지 않음
* 네트워크 카드도 거치지 않음 (소프트웨어 스택에서 처리)
* TCP/IP 스택 내부에서 직접 처리됨

---

## 🔁 `loopback`: 되돌아오는 네트워크 경로

**`loopback`은 자신에게 되돌아오는 네트워크 인터페이스 또는 주소 대역**을 의미합니다. 이는 `localhost`가 \*\*의미론적 이름(name)\*\*이라면, `loopback`은 \*\*기능적 동작(function)\*\*입니다.

### 📌 루프백 인터페이스란?

* OS에 기본적으로 존재하는 가상 네트워크 인터페이스입니다.
* 리눅스에선 `lo`라는 이름을 가집니다.

```bash
$ ifconfig lo
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
    inet 127.0.0.1  netmask 255.0.0.0
    inet6 ::1  prefixlen 128  scopeid 0x10<host>
```

### 🔁 루프백 인터페이스의 역할

* 자신의 네트워크 스택을 테스트할 수 있도록 해줌
* 내부 서버 프로세스 통신 (예: DB서버와 웹서버 간)
* 보안 테스트 및 개발 환경에 필수

---

## 📡 `127.0.0.0/8` 대역: IPv4 루프백 주소

* 전체 `127.0.0.0` \~ `127.255.255.255` 대역은 루프백용
* 일반적으로 `127.0.0.1`을 사용

> **루프백으로 보내는 패킷은 절대 NIC(Network Interface Card)로 나가지 않으며, 커널 네트워크 스택 내부에서 즉시 되돌아옵니다.**

---

## 🌍 IPv6 루프백 주소: `::1`

* IPv6에서 루프백 주소는 **`::1`** 단 하나뿐입니다.
* 압축된 형태로, 전체 주소는 `0000:0000:0000:0000:0000:0000:0000:0001`

```bash
ping ::1
```

* IPv6에서 `localhost`는 `::1`로 매핑되며, 이 또한 `/etc/hosts` 또는 OS 기본 설정에 따라 동작

---

## 🧪 예시 실습

### ✅ 1. `localhost`로 웹 서버 테스트

```bash
python -m http.server 8000
```

브라우저에서:

```
http://localhost:8000
```

### ✅ 2. `127.0.0.1`을 사용한 통신

```bash
telnet 127.0.0.1 5432  # PostgreSQL 서버 접속 시도
```

### ✅ 3. `ping` 테스트

```bash
ping 127.0.0.1
ping ::1
ping localhost
```

---

## 🚫 `localhost`와 `loopback` 주소의 보안 측면

| 항목              | 설명                           |
| --------------- | ---------------------------- |
| 외부 접근 불가        | 외부에서 절대로 접근할 수 없음            |
| 내부 서비스 제한 운영 가능 | `127.0.0.1`로 바인딩 시, 외부 노출 없음 |
| 개발 시 테스트 용이     | 실제 배포 전에 로컬 환경에서 미리 테스트 가능   |

**TIP**: 애플리케이션을 `127.0.0.1`에 바인딩하면 보안적으로 더 안전합니다. 외부에서 접근 불가하기 때문입니다.

---

## 🔄 비교 요약: `localhost` vs `loopback`

| 구분        | localhost                    | loopback                  |
| --------- | ---------------------------- | ------------------------- |
| 의미        | 도메인 이름                       | 동작 방식(자기 자신으로 돌아오는 인터페이스) |
| 주소        | 127.0.0.1 (IPv4), ::1 (IPv6) | 127.0.0.0/8, ::1          |
| 역할        | 사람이 기억하기 쉬운 이름               | 내부 통신 경로                  |
| 처리 위치     | 도메인 해석 (hosts 파일)            | 네트워크 스택 내부에서 처리           |
| NIC 사용 여부 | ❌ 없음                         | ❌ 없음                      |

---

## 📘 참고 자료

* [RFC 1122](https://datatracker.ietf.org/doc/html/rfc1122): Requirements for Internet Hosts — Communication Layers
* [IANA IPv4 Special-Purpose Address Registry](https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml)
* [Wikipedia: Loopback](https://en.wikipedia.org/wiki/Loopback)

---

## ✅ 요약

* `localhost`는 이름, `loopback`은 기능
* 실제로는 `127.0.0.1` 혹은 `::1`로 매핑됨
* 개발, 보안, 테스트에서 필수적인 요소
* 로컬 서버 바인딩 시에는 반드시 loopback으로 제한하는 것이 보안적으로 안전

