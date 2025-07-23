## 1. 브라우저에 `codesquad.kr` 입력 후 화면 출력까지의 과정

1. **URL 파싱**  
   - 브라우저가 입력된 문자열을 분석하여 프로토콜(`http/https`), 호스트(`codesquad.kr`), 경로 등으로 분리  

2. **DNS 조회**  
   - 로컬 DNS 캐시 → 운영체제 DNS 캐시 → ISP DNS → 루트 → TLD → 권한 DNS 서버 순으로 IP 주소 획득  

3. **TCP 연결 수립 (3-way handshake)**  
   4. 클라이언트 → 서버에 `SYN`  
   5. 서버 → 클라이언트에 `SYN+ACK`  
   6. 클라이언트 → 서버에 `ACK`  

7. **(HTTPS인 경우) TLS 핸드쉐이크**  
   - 인증서 교환 → 대칭 키 생성 → 암호화 채널 수립  

5. **HTTP 요청 전송**  
   - `GET / HTTP/1.1` + Host 헤더 등  

6. **서버 측 처리**  
   - 웹서버 → 웹 프레임워크 → 컨트롤러 → 비즈니스 로직 → View 또는 정적 리소스 조회  

7. **HTTP 응답**  
   - 상태 코드(200 OK 등) + 헤더 + 바디(HTML/CSS/JS)  

8. **브라우저 렌더링**  
   - HTML 파싱 → DOM 생성 → CSS 파싱 → CSSOM 생성 → 렌더 트리 구성 → 레이아웃/페인트 → JS 실행  

---

## 2. OSI 7계층 vs TCP/IP 4계층

| 계층 모델 | OSI 7계층               | TCP/IP 4계층           |
|---------|-----------------------|----------------------|
| 1계층    | 물리 계층 (Physical)     | 링크 계층 (Link)       |
| 2계층    | 데이터 링크 (Data Link)  |                        |
| 3계층    | 네트워크 (Network)       | 인터넷 계층 (Internet) |
| 4계층    | 전송 (Transport)         | 전송 계층 (Transport)  |
| 5계층    | 세션 (Session)           |                        |
| 6계층    | 표현 (Presentation)      |                        |
| 7계층    | 응용 (Application)       | 응용 계층 (Application)|

- **차이점**  
  - OSI는 이론적 모델, TCP/IP는 실제 프로토콜 스택  
  - OSI는 세션·표현 계층까지 세분화, TCP/IP는 실용적 통합 모델  

---

## 3. HTTP의 Stateless란?

- **정의**: 서버가 클라이언트의 이전 요청 상태를 기억하지 않는 특성  
- **장점**:  
  - **확장성**: 각 요청을 독립 처리 → 여러 대 서버로 요청 분산 용이  
  - **고가용성**: 서버 장애 시에도 다른 서버로 즉시 전환  

> **Stateless 서버 → 여러 대 서버 가능 → 고가용성**  
>  
> 서버가 상태를 유지하지 않기 때문에 로드밸런서 뒤에 여러 대를 띄워도 개별 서버 간 세션 동기화 없이 요청 처리 가능

---

## 4. State(상태) 관리를 해야 하는 이유

- **왜 필요한가?**  
  - 로그인 유지, 쇼핑카트, 사용자 선호 설정 등 “연속성 있는 서비스” 구현  
  - 무상태 HTTP 상에서 사용자 식별·권한 부여를 위해  

---

### 4-1. 주요 3가지 관리 방법

1. **쿠키(Cookie)**  
   - 클라이언트(브라우저)에 키·값 저장  
   - 유효기간, 경로, 도메인 설정 가능  

2. **서버 세션(Session)**  
   - 서버 메모리(또는 DB)에 세션 데이터 저장  
   - 클라이언트에는 세션 ID만 쿠키로 전달  

3. **토큰 기반 인증(Token, ex. JWT)**  
   - 상태 비저장(stateless) 방식  
   - 토큰 안에 정보(Claims)를 담아 자체 검증  

---

### 4-2. 쿠키 vs 세션

| 구분          | 쿠키                                    | 세션                                    |
|-------------|---------------------------------------|----------------------------------------|
| 저장 위치     | 클라이언트(브라우저)                     | 서버(메모리/DB)                         |
| 보안         | 탈취 위험 → HttpOnly/Secure 옵션 사용 권장  | 세션 ID만 쿠키에 저장 → 서버에서 검증    |
| 확장성       | 서버 부담 없음                           | 세션 저장소 확장 필요                   |

---

## 5. JWT(JSON Web Token)

- **구성 요소**  
  1. **Header**: 토큰 타입(`JWT`), 해싱 알고리즘(`HS256` 등)  
  2. **Payload**: 클레임(등록·공개·비공개 클레임)  
  3. **Signature**:  
     ```
     HMACSHA256(
       base64UrlEncode(header) + "." + base64UrlEncode(payload),
       secret
     )
     ```

---

## 6. Access Token vs Refresh Token

| 구분            | Access Token                  | Refresh Token                  |
|---------------|------------------------------|-------------------------------|
| 유효 기간       | 짧음 (분~시간 단위)             | 김 (수일~수주 단위)            |
| 용도           | API 리소스 접근 허가            | Access Token 재발급 용도      |
| 저장 위치/관리 | 클라이언트(메모리/쿠키/스토리지) | 보안성 높은 곳(HTTP-only 쿠키)  |

---

## 7. CI/CD 용어 정리

- **CI(Continuous Integration)**  
  - 개발된 코드를 자주(통합) 빌드·테스트하여 품질 유지  

- **CD(Continuous Delivery)**  
  - **지속적 배포 준비**: 언제든 프로덕션 수준 릴리즈 가능 상태로 유지  

- **CD(Continuous Deployment)**  
  - **지속적 자동 배포**: 모든 변경 사항을 자동으로 실 프로덕션에 배포  

---

## 8. Observability

- **구성 요소**:  
  1. **로그 (Logging)**  
     - 도구: ELK Stack (Elasticsearch, Logstash, Kibana), Fluentd, Loki  
  2. **메트릭 (Metrics)**  
     - 도구: Prometheus, Grafana, InfluxDB  
  3. **트레이스 (Tracing)**  
     - 도구: Jaeger, Zipkin, OpenTelemetry  