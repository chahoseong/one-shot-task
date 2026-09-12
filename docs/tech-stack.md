# Tech Stack & Environment (OneShotTask)

본 문서는 **OneShotTask** 프로젝트의 기술 스택, 빌드 환경, 그리고 개발 및 아키텍처 원칙을 정의한 문서입니다.  
프로젝트의 모든 개발자 및 AI 에이전트는 본 문서에 정의된 표준과 규격을 준수해야 합니다.

---

## 1. 기본 원칙 (Core Principles)

* **단일 진실 공급원 (Single Source of Truth, SSOT)**
  - 모든 라이브러리, 플러그인, 종속성 버전은 [`gradle/libs.versions.toml`](../gradle/libs.versions.toml) (Gradle Version Catalog)에서 중앙 집중식으로 관리합니다.
  - `build.gradle.kts` 파일에 라이브러리 문자열이나 버전을 직접 하드코딩하지 않습니다.
* **Modern Android Development (MAD) 준수**
  - 구글 공식 권장(Android Developers) 표준 라이브러리 및 패턴만을 채택합니다.
  - 100% 선언형(Declarative) UI 및 타입 안정성(Type Safety)을 보장합니다.
* **불필요한 레거시 배제**
  - XML 기반 뷰 레이아웃(`res/layout`), `findViewById`, 구형 Fragment, 사양 변경된 레거시 API는 도입하지 않습니다.

---

## 2. 플랫폼 및 빌드 시스템 (Platform & Build)

| 항목 | 규격 / 버전 | 비고 |
| :--- | :--- | :--- |
| **언어 (Language)** | Kotlin `2.2.10` | K2 컴파일러 기반 |
| **빌드 도구 (Build Tool)** | Gradle (Kotlin DSL) | `build.gradle.kts` |
| **AGP (Android Gradle Plugin)** | `9.2.1` | 최신 AGP 9 빌드 체계 |
| **Compile SDK** | `36` (Android 16, Baklava) | 최신 API 지원 |
| **Target SDK** | `36` (Android 16) | Android 16 런타임 표준 준수 |
| **Min SDK** | `24` (Android 7.0) | 호환성과 최신 플랫폼 API의 균형 |
| **Java / JVM 타겟** | Java `11` | Compile & Target Compatibility |

---

## 3. UI 및 디자인 시스템 (UI & Design System)

* **UI 프레임워크: Pure Jetpack Compose**
  - 모든 화면과 UI 컴포넌트는 선언형 Jetpack Compose로 작성합니다.
  - **Compose BOM (Bill of Materials)**: `2026.02.01` 기반으로 라이브러리 간 호환성을 보장합니다.
* **디자인 시스템: Material Design 3 (M3)**
  - `androidx.compose.material3:material3`를 표준으로 사용합니다.
  - 다크/라이트 테마 및 동적 색상(Dynamic Color)을 지원합니다.
* **Edge-to-Edge 및 시스템 바 처리**
  - Android 16(SDK 36)의 필수 사양인 **Edge-to-Edge**를 기본 적용합니다 (`enableEdgeToEdge()`).
  - 상태 표시줄, 네비게이션 바, 키보드(IME) 인셋과 UI 겹침 방지를 위해 `WindowInsets` 및 `Scaffold`의 `innerPadding`을 올바르게 소비(consume)합니다.
* **반응형(Adaptive) 레이아웃 지원**
  - 스마트폰, 폴더블, 태블릿 등 다양한 폼팩터에 대응하기 위해 창 크기 클래스(Window Size Classes) 및 어댑티브 레이아웃 패턴을 준수합니다.

---

## 4. 아키텍처 및 상태 관리 (Architecture & State)

* **Modern Android Architecture (권장 계층 구조)**
  - **UI Layer**: Composable 화면 + `ViewModel` (상태 생산 및 UI 이벤트 처리)
  - **Domain Layer**: 복잡한 비즈니스 로직 캡슐화 (UseCase)
  - **Data Layer**: 데이터 소스 추상화 (Repository)
* **단방향 데이터 흐름 (Unidirectional Data Flow, UDF)**
  - 상태는 위에서 아래로 흐르고(State Down), 이벤트는 아래에서 위로 전달(Event Up)되는 UDF 원칙을 철저히 준수합니다.
  - Composable은 상태 호이스팅(State Hoisting)을 통해 재사용성과 테스트 용이성을 극대화합니다.
* **비동기 동시성 및 상태 홀더**
  - Kotlin Coroutines와 `StateFlow` / `SharedFlow`를 사용하여 생명주기를 인식하는 안전한 비동기 상태를 관리합니다.

---

## 5. 화면 전환 및 제스처 (Navigation & Gestures)

* **Jetpack Navigation 3**
  - 화면 간 전환 및 라우팅은 최신 Jetpack Navigation 3 표준 아키텍처를 따릅니다.
  - 문자열 기반 라우트가 아닌 Kotlin 타입 기반(Type-Safe) 목적지 정의를 원칙으로 합니다.
* **NavigationEvent & 예측 뒤로가기 (Predictive Back)**
  - Android 16(SDK 36)의 시스템 표준 제스처인 Predictive Back 애니메이션을 자연스럽게 지원하기 위해 `NavigationEvent` 라이브러리를 활용합니다.

---

## 6. 테스트 스택 (Testing Framework)

| 분류 | 도구 / 라이브러리 | 용도 |
| :--- | :--- | :--- |
| **단위 테스트 (Unit Test)** | `junit:junit:4.13.2` | 비즈니스 로직 및 유틸리티 검증 |
| **계측 테스트 (Instrumentation)** | `androidx.test.ext:junit:1.3.0` | 안드로이드 환경 단위/통합 테스트 |
| **UI 테스트 (Compose UI)** | `androidx.compose.ui:ui-test-junit4` | Composable 렌더링 및 상호작용 검증 |
| **UI 테스트 러너** | `androidx.test.espresso:espresso-core:3.7.0` | 사용자 인터랙션 검증 및 액션 시뮬레이션 |

---

## 7. 품질, 최적화 및 도구 (Quality & Tooling)

* **코드 축소 및 최적화 (R8 / ProGuard)**
  - 릴리즈 빌드 최적화 및 난독화를 위해 R8을 적용하며, 불필요하거나 과도한 keep 규칙을 지양합니다.
* **정적 분석**
  - Android Lint 및 컴파일러 경고를 통해 코드 결함을 사전에 탐지합니다.
* **Android CLI (`android`)**
  - 에뮬레이터 생명주기 관리, 빌드/배포 자동화, 공식 지식 베이스(docs) 검색을 위한 터미널 도구를 활용합니다.
* **공식 에이전트 스킬 연계 (`.agents/skills/`)**
  - 세부 구현 및 트러블슈팅 시 프로젝트 내 8대 공식 가이드라인 스킬(`edge-to-edge`, `navigation-3`, `navigation-event`, `adaptive`, `styles`, `testing-setup`, `r8-analyzer`, `android-cli`)을 적극 참조합니다.