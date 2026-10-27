# 🗺️ KorePlan - 백엔드 개발 및 엔지니어링 (KorePlan-Back)

> **공공데이터포털 관광 데이터와 OpenAI GPT API를 연동한 개인화 맞춤형 여행 일정 플랫폼**
> 
> 본 리포지토리는 불규칙한 외부 공공데이터를 정제 및 DB화하고, AI 응답 속도 최적화 및 2단계 지능형 장소 매칭 알고리즘을 구현한 백엔드 핵심 엔지니어링 기록입니다.

---

## 👨‍💻 백엔드 핵심 기여 및 담당 역할

| 구분 | 담당 업무 및 기여 내용 |
| :--- | :--- |
| **AI 파이프라인 최적화** | • OpenAI GPT API 연동 및 JSON Schema 포맷 제어를 통한 응답 지연 시간 **70% 단축** (28초 → 7.5초)<br>• Jackson DTO 직렬화 예외 처리 파이프라인 구축 |
| **지능형 매칭 알고리즘** | • GPT 추천 장소와 DB 공공데이터 매핑을 위한 **2단계 알고리즘** 설계/구현<br>• 1단계: Haversine 공간 거리 연산 / 2단계: Levenshtein 문자열 유사도 연산 |
| **데이터 정규화 & DB 구축** | • TourAPI 5만 여 건 데이터 수집, 카테고리/지역 코드 정규화 및 MySQL DB 구조화<br>• 외부 API 의존도 절감 및 조속한 데이터 인덱싱 조회 처리 |
| **인증 & 보안 Architecture** | • Spring Security + OAuth2 (카카오, 네이버, 구글) 소셜 로그인 구현<br>• Stateless JWT 토큰 발급, 검증 및 Refresh Token 재발급 메커니즘 구축 |

---

## 🔥 핵심 트러블슈팅 & 엔지니어링 성과

### 1. GPT API 응답 지연 시간 70% 감축 (28초 → 7.5초)
* **문제 상황:** GPT API가 불필요한 서론/결론 문장 및 비정형 자연어를 생성하면서 평균 28초의 극심한 지연(Latency) 발생 및 파싱 에러 유발.
* **해결 방안:**
  * System Prompt에 **Strict JSON Schema** 포맷 규격을 강제하고 묘사용 토큰 생성 억제.
  * Jackson `ObjectMapper`에 직접 바인딩되는 DTO 파이프라인 설계.
* **성과:** **평균 API 응답 지연 시간 70% 단축 (28초 → 7.5초)** 및 JSON 파싱 에러율 0% 달성.

### 2. 5만 건 DB 연동을 위한 2단계 위치/지명 유사도 매칭 알고리즘
* **문제 상황:** GPT가 생성한 텍스트 장소명(예: "경복궁 근처 맛집")은 외부 명칭으로, 내부 DB의 상세 정보(사진, 주소, ID)와 직접 JOIN 불가.
* **해결 방안:**
  * **1단계 (Haversine spatial filter):** 추천 좌표 기준 반경 N km 이내 DB 후보군 1차 추출 (탐색 범위 95% 이상 축소).
  * **2단계 (Levenshtein Distance):** 공백 및 특수문자 제거 후 문자열 편집 거리 연산을 적용해 최고 유사도 DB `content_id` 매핑.
* **성과:** AI 추천 장소의 DB 매핑 정합성 대폭 향상 및 사용자 맞춤 상세 정보 자동 보완.

### 3. 공공데이터포털 TourAPI 5만 건 수집 및 정규화
* **문제 상황:** 외부 공공데이터 API 호출 시 높은 응답 변동성과 데이터 형식 불일치 문제 존재.
* **해결 방안:** 배치 프로세스를 통해 전국 5만 여 건의 관광지 데이터를 수집 후 카테고리별 정규화 테이블 구조에 저장 및 공간 인덱싱 적용.
* **성과:** 외부 API 실시간 의존성을 없애고 10ms 이하의 빠른 데이터 조회 속도 확보.

---

## 🛠 사용 기술 스택 (Tech Stack)

* **Language & Framework:** Java 17, Spring Boot 3.x, Spring Data JPA, Spring Security
* **Database & Cache:** MySQL 8.0, Redis
* **AI & External API:** OpenAI GPT API (GPT-4o / GPT-3.5-turbo), 공공데이터포털 TourAPI
* **Build & Infra:** Gradle, Docker, Nginx, AWS EC2

---

## 📑 주요 API 명세

| Method | Endpoint | 설명 | 인증 |
| :--- | :--- | :--- | :---: |
| **POST** | `/api/v1/auth/login` | OAuth2 소셜 로그인 및 JWT 토큰 발급 | N |
| **GET** | `/api/v1/places` | 카테고리 및 지역별 관광 데이터 조회 | N |
| **POST** | `/api/v1/schedules/recommend` | GPT 기반 일정 생성 및 2단계 매핑 추천 | Y |
| **GET** | `/api/v1/schedules/{id}` | 생성된 여행 일정 상세 조회 | Y |
