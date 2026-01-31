# 🚗🔧 404 스마트 팩토리 - 근태 관리 및 채팅 시스템

Spring Boot로 구현한 제조업 현장의 근태 관리 시스템입니다.  
자동 출퇴근 체크, 배치 스케줄러를 통한 데이터 정리, WebSocket 기반 실시간 채팅까지 현장에서 실제로 사용할 수 있는 기능을 담았습니다.  
IoT 센서와 연동하여 데이터를 수집하는 Flask 서버와 함께 동작합니다.

# 404found 2차 프로젝트
> **프로젝트의 모든 과정을 담은 상세 시연 영상입니다.** > 이미지 또는 버튼을 클릭하면 유튜브 페이지로 이동합니다.
<div align="center">
  <a href="https://www.youtube.com/watch?v=gPBmVkVSfhc">
    <img src="https://img.youtube.com/vi/gPBmVkVSfhc/maxresdefault.jpg" width="80%" alt="404found 2차 프로젝트 시연영상">
    <br>
    <img src="https://img.shields.io/badge/YouTube-Watch_Video-red?style=for-the-badge&logo=youtube" alt="Youtube Button">
  </a>
</div>

## 📋 목차

- [프로젝트 개요](#프로젝트-개요)
- [주요 기능](#주요-기능)
- [기술 스택](#기술-스택)
- [프로젝트 구조](#프로젝트-구조)
- [설치 및 실행](#설치-및-실행)
- [API 엔드포인트](#api-엔드포인트)
- [WebSocket 이벤트](#websocket-이벤트)
- [환경 변수 설정](#환경-변수-설정)

---

## 🎯 프로젝트 개요

Spring Boot 기반 근태 관리 및 실시간 채팅 시스템으로, 스마트 팩토리의 직원 출퇴근 관리와 커뮤니케이션을 담당합니다.

**주요 역할:**
- 직원 출퇴근 관리 (출근/퇴근/연차/병가 등)
- WebSocket 기반 실시간 채팅
- 월간 급여 요약 및 근태 통계
- 자동 결근 처리 스케줄러

---

## ✨ 주요 기능

### 👥 근태 & 채팅 시스템
| 기능 | 설명 |
|------|------|
| **출퇴근 관리** | 출근/퇴근 처리, 자동 지각 판정 |
| **연차/병가 관리** | 연차/반차/병가 신청 및 차감 |
| **급여 계산** | 일당제/시급제 자동 급여 계산 |
| **실시간 채팅** | WebSocket 기반 단체 채팅 |
| **자동 결근 처리** | 평일 18:01 퇴근 미처리 감지, 00:00:01 결근 자동 기록 |
| **관리자 기능** | 근태 수정/삭제, 월간 통계 |

---

## 🛠️ 기술 스택

### � Spring Boot 백엔드
```
☕ Java 17
🍃 Spring Boot 4.0.1 - 웹 프레임워크
🔌 Spring WebSocket (STOMP) - 실시간 통신
🗄️ Spring Data JPA - ORM
🔐 JWT (jjwt) - 인증
📡 MySQL Connector J - MySQL 드라이버
🔄 Lombok - 코드 자동 생성
```

### 📊 데이터베이스
| 시스템 | 데이터베이스 |
|--------|-------------|
| Spring (근태 시스템) | MySQL (`smart_factory` DB) |

---

## 📁 프로젝트 구조

```
404-spring/hr_service/
│
├── pom.xml                         # Maven 설정
└── src/main/java/com/example/chat_service/
    ├── ChatServiceApplication.java # 메인 애플리케이션
    ├── config/
    │   ├── WebConfig.java          # 웹 설정
    │   └── WebSocketConfig.java    # WebSocket 설정
    ├── controller/
    │   ├── ChatController.java     # 채팅 컨트롤러
    │   ├── AttendanceController.java    # 출퇴근 API
    │   └── AdminAttendanceController.java # 관리자 API
    ├── service/
    │   ├── AttendanceService.java      # 근태 비즈니스 로직
    │   ├── AttendanceAdminService.java # 관리자 비즈니스 로직
    │   └── AttendanceScheduler.java    # 스케줄러
    ├── entity/
    │   ├── Employee.java          # 직원 엔티티
    │   └── AttendanceLog.java     # 근태 기록 엔티티
    ├── repository/
    │   ├── EmployeeRepository.java
    │   └── AttendanceLogRepository.java
    └── dto/
        ├── ChatMessage.java
        └── AttendanceLogResponse.java
```

---

## 🚀 설치 및 실행

### 요구사항
- Java 17
- Maven 3.6+
- MySQL 8.0+

### Spring Boot 실행

```bash
# hr_service 디렉토리로 이동
cd hr_service

# 의존성 설치 및 빌드
mvn clean install

# 서버 실행 (기본 포트 8080)
mvn spring-boot:run

# 또는 JAR 파일로 실행
java -jar target/chat-service-0.0.1-SNAPSHOT.jar
```

---

## 📡 API 엔드포인트

### 🔐 근태 관리 API (Spring Boot - `/api/attendance`)

| 메서드 | 엔드포인트 | 설명 | 요청 본문 |
|--------|-----------|------|----------|
| POST | `/api/attendance/check-in` | 출근 처리 | `{"id": 1}` |
| POST | `/api/attendance/check-out` | 퇴근 처리 | `{"id": 1}` |

### 👑 관리자 API (Spring Boot - `/api/admin/attendance`)

| 메서드 | 엔드포인트 | 설명 | 파라미터 |
|--------|-----------|------|----------|
| POST | `/api/admin/attendance/update` | 근태 상태 일괄 수정 | `employeeId`, `status`, `date`, `endDate` |
| DELETE | `/api/admin/attendance/delete` | 근태 기록 일괄 삭제 | `employeeId`, `date`, `endDate` |
| GET | `/api/admin/attendance/monthly/{employeeId}` | 특정 사원 월간 조회 | `year`, `month` (선택) |
| GET | `/api/admin/attendance/salary/all-summary` | 전 사원 월급 요약 | `year`, `month` (선택) |
| GET | `/api/admin/attendance/monthly/all` | 전 사원 월간 기록 조회 | `year`, `month` (선택) |

---

## 🔌 WebSocket 이벤트

### � 채팅 시스템 (Spring STOMP)

#### 채팅 (채널: `/topic/public`)
| 이벤트 | 설명 |
|-------|------|
| STOMP SUBSCRIBE | `/topic/public` 구독 |
| SEND | `/app/chat.sendMessage` 메시지 전송 |
| SEND | `/app/chat.addUser` 사용자 추가 |

#### 근태 알림 (채널: `/topic/attendance/{employeeId}`)
| 이벤트 | 설명 | 데이터 |
|-------|------|-------|
| `CHECK_IN` | 출근 완료 | `{type, status, time}` |
| `CHECK_OUT` | 퇴근 완료 | `{type, status, time, dailyWage, workingMinutes}` |
| `LEAVE_UPDATE` | 연차/병가 변경 | `{type, status, remainingLeave, remainingSickLeave}` |
| `ABSENT` | 결근 자동 처리 | `{type, status, date}` |

---

## 🗄️ 데이터베이스 스키마

### MySQL (`smart_factory`) - Spring Boot

#### `employee` 테이블
| 컬럼 | 타입 | 설명 |
|------|------|------|
| `id` | BIGINT | PK, 자동 생성 |
| `employee_number` | VARCHAR(8) | 사원번호 (고유) |
| `name` | VARCHAR(100) | 이름 |
| `department` | VARCHAR(50) | 부서 |
| `position` | VARCHAR(50) | 직급 |
| `password_hash` | VARCHAR(255) | 비밀번호 해시 |
| `monthly_salary` | INT | 월급 |
| `hourly_rate` | INT | 시급 |
| `annual_leave` | DOUBLE | 연차 잔여 |
| `sick_leave` | INT | 병가 잔여 |

#### `attendance` 테이블
| 컬럼 | 타입 | 설명 |
|------|------|------|
| `id` | BIGINT | PK, 자동 생성 |
| `employee_id` | BIGINT | FK, 사원 ID |
| `work_date` | DATE | 근무 날짜 |
| `check_in` | TIME | 출근 시간 |
| `check_out` | TIME | 퇴근 시간 |
| `status` | VARCHAR(20) | 상태 (출근/지각/퇴근/연차/병가 등) |
| `working_minutes` | INT | 실제 근무 시간 (분) |
| `daily_wage` | INT | 일당 |

---

## ⚙️ 환경 변수 설정

### Spring Boot (`chat-service/src/main/resources/application.properties`)

```properties
# 데이터베이스 설정
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/smart_factory?serverTimezone=Asia/Seoul
spring.datasource.username=root
spring.datasource.password=1234
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA 설정
spring.jpa.hibernate.ddl-auto=update

```

---

## 🤖 AI 도구 활용 (Cursor)

### Spring Scheduler Cron 표현식 생성
**Before (잘못된 표현식)**
```java
@Scheduled(cron = "0 0 0 * * *")  // 00:00:00 실행 (의도와 다름)
```

**After (Cursor 제안)**
```java
@Scheduled(cron = "1 0 0 * * ?")  // 00:00:01 실행 (정확)
// 초 분 시 일 월 요일
```

### WebSocket STOMP 설정 최적화
- `setApplicationDestinationPrefixes` vs `enableSimpleBroker` 차이 자동 설명
- CORS 설정 오류 해결 (`setAllowedOriginPatterns("*")` 제안)

### @Transactional 적용 범위 개선
- Cursor가 읽기 전용 쿼리에 `readOnly = true` 추가 제안
- 트랜잭션 범위 최소화 가이드

---

## 💡 배운 점 및 개선 과제

### 배운 점
- **Spring Scheduler**: Cron 표현식 기반 자동화 배치 작업 구현
- **@Transactional**: JPA 트랜잭션 관리 및 데이터 일관성 보장
- **WebSocket (STOMP)**: Pub/Sub 패턴으로 실시간 알림 구현
- **Dirty Checking**: JPA 변경 감지로 명시적 UPDATE 없이 DB 동기화
- **Spring Data JPA**: 메서드 네이밍 컨벤션으로 자동 쿼리 생성

### 향후 개선 과제
- **인증/인가**: Spring Security 적용 및 JWT 통합
- **급여 계산 고도화**: 추가 수당, 야간 근무 수당 등 복잡한 정책 반영
- **월간 통계 최적화**: 쿼리 성능 개선 및 인덱싱
- **WebSocket 확장성**: Redis Pub/Sub으로 멀티 인스턴스 환경 대응
- **테스트 코드**: JUnit5로 Scheduler 및 WebSocket 테스트 작성

---

## 🔗 관련 링크

- **404-back (Flask)**: [GitHub 🔗](https://github.com/yeonjaegit/404-back) - MQTT 센서 수집 백엔드
- **ERD 설계**: [ERD Cloud](https://www.erdcloud.com/d/rfbhh56TFNjiobguv)
- **노션 포트폴리오**: [상세 프로젝트 문서](https://www.notion.so/Project-3-2ef62d7f696c80a0926ddc560281b2f2)
- **시연 영상**: [YouTube](https://www.youtube.com/watch?v=gPBmVkVSfhc)

---

**Last Updated**: 2026-01-31

<br/>

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.