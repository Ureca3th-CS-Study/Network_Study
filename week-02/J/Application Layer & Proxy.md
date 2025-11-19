# Appliacation Layer

## **DNS와 도메인 네임**

### 도메인 네임이 필요한 이유

호스트는 IP 주소로 식별되지만,

- 기억하기 어렵고
- IP는 변경될 수 있기 때문에
    
    사람 친화적인 문자열 주소인 **도메인(domain name)** 을 사용한다.
    

예:

```
www.example.com
developers.naver.com
```

이 도메인은 DNS 서버에 저장되어 **IP 주소와 1:1 매핑**된다.

### DNS 서버 구조 (계층적)

도메인 명은 점(.)을 기준으로 계층적이다.

예: `www.example.com.`

- 루트 도메인 `.`
- 최상위 도메인(TLD): `com`
- 2단계 도메인: `example`
- 3단계 도메인: `www`

이 구조를 관리하는 DNS 서버도 **계층형 구조**다.

### DNS 조회 흐름

1. 클라이언트 → 로컬 DNS 서버에게 질의
2. 로컬 DNS가 모르면
    
    → 루트 DNS → TLD DNS → 하위 DNS 순으로 재귀적으로 탐색
    
3. 최종 IP 주소를 찾아 클라이언트에게 전달
4. 결과는 **DNS 캐시**에 저장해 재사용

### DNS 레코드

도메인에 다양한 정보가 매핑되는데 이를 **DNS 레코드**라고 한다.

| 타입 | 설명 |
| --- | --- |
| **A** | 도메인 → IPv4 |
| **AAAA** | 도메인 → IPv6 |
| **CNAME** | 다른 도메인을 가리키는 별칭 |
| **NS** | 해당 도메인을 관리하는 DNS 서버 |
| **MX** | 메일 서버 정보 |

예:

```
example.com   A      1.2.3.4
www.example.com   CNAME   example.com
```

## **URI, URL, URN**

### URI란?

**URI (Uniform Resource Identifier)**

→ 웹 상의 자원을 식별하기 위한 통일된 규칙

URI 종류

- **URL**: 위치 기반 식별자
- **URN**: 이름 기반 식별자

대부분은 URL 방식 사용.

### URL 구조

![image.png](attachment:f9d482ab-299f-4312-8742-3b1ea26dddc2:image.png)

| 구성 요소 | 설명 |
| --- | --- |
| **scheme** | 사용하는 프로토콜 (http, https 등) |
| **authority** | host + port |
| **path** | 자원 경로 |
| **query** | 추가 파라미터 (`?key=value&key2=value2`) |
| **fragment** | 자원의 특정 위치 (HTML 내부 점프 등) |

## **HTTP의 특징 (핵심 4가지)**

### **요청–응답(Request/Response) 기반**

클라이언트가 요청하면 서버가 응답하는 구조.

### **미디어 독립적 (Media-Independent)**

HTML, JSON, PDF, 이미지, 영상 등 **어떤 형식이든** 전달 가능.

→ 형식은 MIME 타입(=미디어 타입)으로 표현.

예:

`text/html`, `application/json`, `image/png`

### **스테이트리스(Stateless)**

서버는 클라이언트의 상태를 기억하지 않음.

→ 서버 **확장성·견고성** 증가

→ 필요한 경우 **쿠키/세션/토큰** 등을 사용해 상태를 유지.

### **지속 연결(Persistent Connection, Keep-Alive)**

![image.png](attachment:db66500d-c71d-4b4c-a6e3-41b4848fbde4:image.png)

HTTP/1.1 이후 기본적으로 하나의 TCP 연결로 여러 요청을 처리.

→ 성능 향상.

### HTTP/2·HTTP/3 간단 정리

- **HTTP/2**
    - 바이너리 프로토콜
    - 헤더 압축
    - 서버 푸시
    - 멀티플렉싱 → HOL 블로킹 해결
- **HTTP/3**
    - TCP → **UDP 기반 QUIC**로 변경
    - 더 빠르고 끊김 적음
    

## **HTTP 메시지 구조**

HTTP 메시지는 다음 3개로 구성된다.

```
시작 라인
헤더 라인들
(빈 줄)
메시지 본문(Body)
```

### 요청 메시지 구조 (Request)

```
GET /home HTTP/1.1      ← 요청 라인
Host: example.com        ← 헤더
User-Agent: Chrome        ← 헤더

{body}
```

요청 라인의 구성:

- **메서드**
- **요청 대상(path 또는 URL)**
- **HTTP 버전**

### 응답 메시지 구조 (Response)

```
HTTP/1.1 200 OK            ← 상태 라인
Content-Type: text/html     ← 헤더

<html>...</html>
```

상태 라인 구성:

- **HTTP 버전**
- **상태 코드(3자리 숫자)**
- **이유 구문**

## **HTTP 메서드 정리**

| 메서드 | 설명 |
| --- | --- |
| **GET** | 조회 |
| **HEAD** | GET과 동일하지만 Body 없음 |
| **POST** | 자원 생성 / 작업 처리 |
| **PUT** | 전체 수정(덮어쓰기) |
| **PATCH** | 부분 수정 |
| **DELETE** | 자원 삭제 |
| **OPTIONS** | 지원 메서드 확인 |
| **CONNECT/TRACE** | 특수 목적 |

### PUT vs PATCH 예시

**기존 데이터:**

```
{id:1, title:"A", contents:"B"}
```

**PATCH**

```
{title:"수정"}  → title만 변경
```

**PUT**

```
{title:"수정"}  → 전체 덮어쓰므로 contents 사라짐
```

## **HTTP 상태 코드**

### 200번대 – 성공

| 코드 | 의미 |
| --- | --- |
| **200 OK** | 정상 처리 완료 |
| **201 Created** | 새 리소스 생성 |
| **202 Accepted** | 요청은 받았지만 처리 중 |
| **204 No Content** | 처리 성공, 본문 없음 |

### 300번대 – 리다이렉션

| 코드 | 설명 |
| --- | --- |
| **301 Moved Permanently** | 영구 이동(GET로 바뀔 수 있음) |
| **308 Permanent Redirect** | 영구 이동(메서드 유지) |
| **302 Found** | 임시 이동(메서드 변경 가능) |
| **303 See Other** | 반드시 GET으로 재요청 |
| **307 Temporary Redirect** | 메서드 유지 |

**영구적 리다이렉션( permanent recdirection)**

자원이 완전히 새로운 곳으로 이동하여 경로가 영구적으로 재지정되는 것

자원의 위치가 영구적으로 변경되었음을 시사하므로, 기존 URL에 요청 메시지를 보내면 항상 새로운 URL로 리다이렉트 된다. 

만약 어떤 URL에 요청을 보낸 결과로 영구적인 리다이렉션 관련 상태 코드를 응답받았다면 요청을 보낸 기존의 URL은 기억할 필요가 없다

**일시적 리다이렉션 (emporary redirection)**

자원의 위치가 임시로 변경되었거나 임시로 사용할 URL이 필요한 경우에 주로 사용

만약 어떤 URL에 대해 일시적인 리다이렉션 관련 상태 코드를 응답 받았다면 영구적인 리다이렉션과는 달리 여전히 요청을 보낸 기존의 URL을 기억해야 한다.

301 (Moved Permanently)과 308 (Permanent Redirect), 그리고 302 (Found)와 303 (See Other), 307 (Temporary Redirect)은 재요청 메서드가 어떻게 변경되는지에 따라 구분할 수 있다. 

앞선 예시로 확인했듯이 클라이언트가 리다이렉션 상태 코드를 수신할 경우 Location 헤더에 명시된 URL로 즉시 재요청을 보내어 새로운 URL에 대한 응답을 받게 된다. 

이때 처음으로 요청을 보낸 메서드가 POST 등 GET이 아닌 메서드일 경우,  301의 재요청 메서드는 GET으로 변경될 가능성이 있지만, 308의 재요청 메서드는 첫 번째 요청 메서드에서 변경되지 않는다. 

즉, 301의 애매모호함을 보완하기 위해 만들어진 상태 코드가 308이라고 할 수 있다.

### 400번대 – 클라이언트 오류

| 코드 | 설명 |
| --- | --- |
| **400 Bad Request** | 잘못된 요청 |
| **401 Unauthorized** | 인증 필요 (Identity 문제) |
| **403 Forbidden** | 권한 부족 (Permission 문제) |
| **404 Not Found** | 리소스 없음 |
| **405 Method Not Allowed** | 지원하지 않는 메서드 |

### 500번대 – 서버 오류

| 코드 | 설명 |
| --- | --- |
| **500 Internal Server Error** | 서버 내부 오류(대표) |
| **502 Bad Gateway** | 중간 서버의 응답 오류 |

## **HTTP 주요 헤더 정리**

### 요청 헤더 (Request Headers)

| 헤더 | 설명 |
| --- | --- |
| **Host** | 요청 대상 도메인/IP |
| **User-Agent** | 클라이언트 장치/브라우저 정보 |
| **Referer** | 이전 페이지(유입 경로) |

### 응답 헤더 (Response Headers)

| 헤더 | 설명 |
| --- | --- |
| **Server** | 서버 소프트웨어 정보 |
| **Location** | 리다이렉트·새 자원 위치 |
| **Allow** | 해당 URL이 지원하는 메서드 목록(405 때 자주 사용) |

### 공통 헤더 (Request/Response 모두)

| 헤더 | 설명 |
| --- | --- |
| **Date** | 메시지 생성 시각 |
| **Content-Type** | 본문 미디어 타입 (json, html 등) |
| **Content-Length** | 본문 크기(Byte) |
| **Content-Language** | 본문에 사용된 자연어 (ko-KR 등) |
| **Content-Encoding** | 본문 압축 방식 (gzip, br 등) |
| **Connection** | keep-alive 또는 close |

## **HTTP 쿠키(Cookie)**

HTTP는 기본적으로 “상태를 기억하지 않기” 때문에,

- 로그인 유지
- 3일 동안 보지 않기
- 장바구니 유지
    
    같은 기능을 **그 자체로는 구현할 수 없다.**
    

이를 해결하는 대표적인 기술이 바로 쿠키(Cookie)이다.

### 쿠키란?

- 서버가 생성해서 클라이언트(브라우저)에 저장하는 **이름=값 쌍 데이터**
- 필요 시 브라우저가 **자동으로 서버에게 다시 전송**
- 주로 로그인/세션 관리, 사용자 설정 저장 등에 사용

**서버 → 클라이언트**

쿠키 전달

```
Set-Cookie: name=minchul; Max-Age=3600
```

**클라이언트 → 서버**

쿠키 자동 포함

```
Cookie: name=minchul
```

### 쿠키 주요 속성 정리

| 속성 | 설명 |
| --- | --- |
| **Domain** | 이 쿠키를 전송할 도메인 지정 |
| **Path** | 특정 URL 경로일 때만 쿠키 전송 |
| **Expires** | 특정 날짜로 만료 시점 지정 |
| **Max-Age** | 초 단위 만료 기간 지정 |
| **Secure** | HTTPS일 때만 쿠키 전송 |
| **HttpOnly** | JS(document.cookie)로 접근 불가 → XSS 방어 |

### 쿠키 예시

**서버가 쿠키 내려보냄**

```
Set-Cookie: sessionId=abc123; Max-Age=86400; Secure; HttpOnly
```

**브라우저 요청 시 자동 포함**

```
Cookie: sessionId=abc123
```

> 쿠키는 HTTP의 stateless 한계를 보완하기 위해 클라이언트에 상태 정보를 저장하고 자동 전송하는 기술이다.
> 

## **HTTP 캐시(Cache)**

웹 캐시는 **응답 자원(HTML, 이미지 등)을 임시 저장해 반복 요청 시 재사용**함으로써

- 속도 증가
- 서버 부하 감소
- 네트워크 비용 절약

을 실현하는 기술이다.

### 캐시의 기초 개념

**캐시 유효기간을 지정하는 헤더들**

| 헤더 | 설명 |
| --- | --- |
| **Expires** | 특정 날짜까지 유효 |
| **Cache-Control: max-age=초** | 응답을 몇 초 동안 캐싱할지 |

예)

```
Cache-Control: max-age=1200
```

→ 20분 동안 캐시 사용 가능

### 캐시 무효화와 “신선도(Freshness)”

캐시된 데이터는 **원본 데이터가 변경될 수 있기 때문에** 유효기간이 존재한다.

유효기간이 지나면 클라이언트는 서버에게 **원본이 변경되었는지 확인**한다.

방법은 2가지:

**날짜 기반 검증 — If-Modified-Since**

**요청**

```
If-Modified-Since: Fri, 23 Aug 2024 09:00:00 GMT
```

**서버의 3가지 응답**

| 상황 | 응답 |
| --- | --- |
| 변경됨 | `200 OK` + 새 데이터 |
| 변경 안됨 | `304 Not Modified` (본문 없음) |
| 삭제됨 | `404 Not Found` |

### **ETag 기반 검증 — If-None-Match**

ETag(Entity Tag)는 **자원의 버전을 나타내는 식별자**이다.

**요청**

```
If-None-Match: "abc123"
```

**서버 응답**

- 변경됨 → `200 OK` + 새 데이터
- 변경 없음 → `304 Not Modified`
- 삭제됨 → `404 Not Found`

> 304 Not Modified는 "**캐시 써도 됨**"을 의미하는 매우 중요한 상태 코드이다.
> 

## **콘텐츠 협상(Content Negotiation)**

같은 URL이라도 표현 방식(언어, 타입, 인코딩)이 다를 수 있다.

예시)

- Google 검색 결과 → 한국어 페이지 or 영어 페이지
- JSON 응답 or XML 응답
- gzip 압축 or br 압축

이때 클라이언트가 **원하는 표현(송수신 가능한 자원의 형태)**을 서버에게 전달하는 기술이 콘텐츠 협상이다.

### 콘텐츠 협상 관련 헤더

| 헤더 | 역할 |
| --- | --- |
| **Accept** | 선호하는 미디어 타입 |
| **Accept-Language** | 선호하는 언어 |
| **Accept-Encoding** | gzip, br 등 압축 방식 |

### 우선순위(q 값)

q값은 선호도를 나타냄

**1에 가까울수록 선호도가 높다.**

예시:

```
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Accept: text/html,application/xml;q=0.9,text/plain;q=0.6
```

의미:

- 언어는 한국어 가장 선호, 영어도 가능
- HTML > XML > TEXT 순으로 선호

## **HTTPS — HTTP + TLS 보안**

HTTP는 평문(plain text) 통신이기 때문에

- 도청
- 위·변조
- 중간자 공격

위험이 있다.

이를 해결하기 위해 나온 것이 **HTTPS**이며,

내부적으로 **TLS 프로토콜을 이용해 암호화**한다.

### HTTPS 동작 과정 (핵심 3단계)

**① TCP 3-way Handshake**

기본 연결 설정

**② TLS Handshake**

**가장 중요한 단계**

- 클라이언트/서버 → 서로 인증 (인증서 사용)
- 암호화에 사용할 **세션 키 생성**
- 이후의 데이터는 모두 암호화

**③ 암호화된 HTTP 메시지 송수신**

HTTPS가 본격적으로 동작

### TLS Handshake 핵심 메시지 요약

![image.png](attachment:609fc3e5-9e96-4024-9af0-163934b69fb4:image.png)

**클라이언트 → 서버 : ClientHello**

- 지원 가능한 암호 방식(암호 스위트)
- 사용할 TLS 버전
- 난수(random)
- 기타 확장 정보

**서버 → 클라이언트 : ServerHello**

- 선택된 암호 스위트
- 서버 난수
- 이후 메시지 암호화에 필요한 정보

**Server → Client : Certificate**

- 서버 인증서 전달
- 브라우저는 CA 체인 기반으로 인증서 검증

**서로 Finished 메시지 교환**

핸드셰이크 완료

→ 이제 모든 HTTP 메시지는 TLS로 암호화됨

### HTTPS에서 인증서란?

SSL/TLS 인증서는

**“내가 진짜 example.com 맞습니다”**

라는 사실을 증명하기 위한 파일.

인증서는 CA(인증 기관)가 발급하며,

브라우저는 OS와 함께 내장된 CA 목록을 기준으로 **서명(chain)** 을 검증한다.

> 인증서 검증이 실패하면 주의 요함 / 보안 경고 페이지가 뜨는 이유.
> 

# Proxy & Stable Traffic

## 오리진 서버와 중간 서버(Forward Proxy · Reverse Proxy)

일반적인 네트워크 설명은

> 클라이언트 ↔ 단일 서버
> 
> 
> 구도로 단순화해서 보여주지만,
> 

실제 현실은 **그보다 훨씬 복잡**하다.

![image.png](attachment:5e1ec3ae-fda4-4dde-9d99-c7f7d17f5d00:image.png)

**실제 구성**

- 클라이언트와 서버 사이에는 수십 개의 네트워크 장비 존재
- 서버는 단일하지 않고 대부분 다중화(Multi-AZ, 멀티 서버)
- 캐시 서버, 인증 서버, CDN, 로드밸런서 등 중간 서버가 다수 존재

즉 최종적으로 **클라이언트의 요청을 처리하고 응답을 생성하는 진짜 서버**는

**오리진 서버(Origin Server)** 이다.

그리고 클라이언트 ↔ 오리진 사이에는 여러 종류의 **중간 서버**가 있다.

### 포워드 프록시(Forward Proxy)

**클라이언트 편에 위치하는 대리인 서버**

![image.png](attachment:08fcd2d8-f986-4099-995e-a4a8abfe6a74:image.png)

**특징**

- 클라이언트가 직접 서버로 가지 않고 **프록시가 대신 요청**
- 캐싱 기능 제공
- 클라이언트 익명성 보호 (IP 숨기기)
- 접근 제한(필터링)
- VPN, 회사 내부 Proxy 등이 대표적

### 리버스 프록시(Reverse Proxy, Gateway)

**서버 편에 위치하는 문지기·게이트키퍼 역할**

![image.png](attachment:513d0704-401d-43ef-85bf-1e7215b881a8:image.png)

**특징**

- 클라이언트는 리버스 프록시를 *서버처럼 인식*
- 오리진 서버 앞단에서 요청을 받아 뒤쪽 서버에게 분배
- 부하 분산(로드밸런서), 캐시, SSL 종료(SSL offloading) 등 수행
- Nginx, HAProxy, Envoy 등이 대표적

## 가용성(Availability)과 고가용성(High Availability, HA)

**가용성 정의**

시스템이 “정상적으로 기능할 수 있는 시간의 비율”

```
가용성 = 업타임 / (업타임 + 다운타임)
```

예시(연간 기준):

| 가용성 | 연간 다운타임 |
| --- | --- |
| 99% | 3.65일 |
| 99.9% | 8.7시간 |
| 99.99% | 52.5분 |
| 99.999%(파이브 나인) | 5.26분 |

대규모 서비스(은행, 쇼핑몰, SaaS)는 주로

**99.99% ~ 99.999%** 를 목표로 한다.

### 왜 다운타임이 발생하는가?

- 과도한 트래픽
- 서버 장애 / 네트워크 장애
- 소프트웨어 버그
- 보안 공격
- 자연재해

따라서 문제가 안 생기도록 하는 것보다

**문제가 생겨도 버티도록 만드는 것(결함 감내, Fault Tolerance)**

이 더 중요하다.

## 결함 감내 · 다중화 · 헬스 체크

### 다중화(Multi-AZ, Replica)

서버를 여러 대 두면, 한 서버가 죽어도 나머지가 요청을 처리할 수 있다.

**핵심 개념**

- **Failover(장애 전환)**
    
    고장난 서버 → 살아있는 서버로 자동 전환
    
- **헬스 체크(Health Check)**
    
    로드밸런서가 주기적으로 서버 상태 점검
    
- **하트비트(Heartbeat)**
    
    서버들끼리 **살아있음** 신호 주고받기
    

## 로드밸런싱(Load Balancing)

서버가 여러 대라면

트래픽을 **균등하게** 분배해야 한다.

그 역할을 하는 것이 **로드밸런서(load balancer)**.

![image.png](attachment:45a9527f-cac5-4194-995c-a1fdb0f59beb:image.png)

### 로드밸런싱 알고리즘

**라운드 로빈 (Round Robin)**

서버를 순서대로 번갈아가며 배정

**최소 연결 (Least Connection)**

현재 “연결 수”가 가장 적은 서버로 보냄

→ WebSocket, 장기 연결 서비스에 유용

**IP 해시 (IP Hash)**

같은 클라이언트 → 같은 서버로

→ **세션 유지** 필요한 서비스에 사용

**가중치 기반 (Weighted)**

성능이 좋은 서버에 더 많은 트래픽 배정

```
server A weight=2
server B weight=1
→ A에 B의 2배 요청 배정
```

**랜덤(Random)**

무작위 선택

## 스케일링(Scaling) — 스케일 업 vs 스케일 아웃

![image.png](attachment:e2208982-1b38-45b2-b1c9-deb253fb3961:image.png)

### 스케일 업 (Scale-Up, 수직 확장)

서버 한 대의 성능을 올리는 방식

예)

- CPU 업그레이드
- 메모리 추가
- 더 좋은 장비로 교체

**장점**

- 단순
- 관리 쉬움

**단점**

- 장비 가격이 매우 비쌈
- 확장의 끝이 있음
- 하나가 고장나면 서비스 다운

### 스케일 아웃 (Scale-Out, 수평 확장)

서버 자체를 여러 대 늘리는 방식

**장점**

- 확장성 뛰어남
- 고가용성 확보 쉬움
- 장애 발생 시 대체 가능

**단점**

- 구성 복잡
- 로드밸런싱 필요
- 인프라 관리 필요

오늘날 대부분의 서비스는

**스케일 아웃 기반 + 로드밸런싱** 구조

### 오토스케일링(Auto Scaling)

트래픽이 갑자기 증가하는 상황(티켓팅, 세일 기간 등)에 대비해

**자동으로 서버를 늘리고**,

트래픽이 줄면 **자동으로 축소**하는 기능.

AWS, GCP, Azure 모두 기본 제공.

## 실제 로드밸런싱 예 — Nginx 구성 파일 간단 예시

Nginx는 웹 서버이자 리버스 프록시, 로드밸런서 역할도 수행 가능하다.

```
upstream backend {
    least_conn;		  # 최소 연결 알고리즘
    server 10.10.10.2 weight=1;
    server 10.10.10.3 weight=2;
    server 10.10.10.4 backup;
}

server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://backend;
    }
}
```

### 동작 설명

- `/`로 들어오는 요청은 backend 그룹으로 전달
- 10.10.10.3은 가중치 2 → 더 많은 요청
- 10.10.10.4는 backup → 다른 두 서버가 죽을 때만 사용
- least_conn → 연결 적은 서버부터 배정

## 업스트림 / 다운스트림 vs 인바운드 / 아웃바운드

| 개념 | 의미 | 예 |
| --- | --- | --- |
| 업스트림 | 클라이언트 → 서버 | 요청(Upload) |
| 다운스트림 | 서버 → 클라이언트 | 응답(Download) |
| 인바운드 | 외부 → 내부 네트워크 진입 | 외부 이용자가 내 서버 접속 |
| 아웃바운드 | 내부 → 외부 네트워크 | 내부 직원이 외부 사이트 접속 |