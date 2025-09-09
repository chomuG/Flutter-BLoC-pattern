# Flutter BLoC Pattern

Flutter BLoC 패턴 학습 자료

## 프로젝트 소개

Flutter에서 BLoC (Business Logic Component) 패턴을 체계적으로 학습하기 위한 종합적인 학습 자료입니다. BLoC 라이브러리 공식 문서를 기반으로 한국어로 번역하고 정리한 상세한 가이드를 포함합니다.

## 학습 자료 구성

### Bloc Study.md
- BLoC 패턴의 기본 개념과 원리
- flutter_bloc 패키지 사용법
- 상태 관리의 필요성과 BLoC의 장점
- 실제 구현 예제와 코드

### Bloc Solution Page.md
- BLoC 0.13.0에서 2.0.0으로의 업그레이드 가이드
- 버전별 문법 변경사항 정리
- 마이그레이션 방법과 주의사항
- 새로운 기능과 위젯 소개

### 추가 자료
- HTML 버전 학습 자료
- PDF 버전 참고 문서

## 주요 학습 내용

### BLoC 패턴 기초
- **Business Logic Component**의 개념
- UI와 비즈니스 로직의 분리
- Event, State, BLoC의 역할과 관계
- 단방향 데이터 플로우

### 핵심 컴포넌트
- **Event**: 사용자 액션과 시스템 이벤트
- **State**: 앱의 상태 데이터
- **BLoC**: 이벤트를 받아 상태를 생성하는 로직
- **Repository**: 데이터 접근 계층

### Flutter 위젯 활용
- **BlocBuilder**: 상태에 따른 UI 빌드
- **BlocListener**: 상태 변화 감지 및 사이드 이펙트 처리
- **BlocProvider**: BLoC 인스턴스 제공
- **MultiBlocProvider**: 다중 BLoC 제공

## 버전 업그레이드 가이드

### 주요 변경사항 (0.13.0 → 2.0.0)

#### 1. Event 추가 문법 변경
```dart
// 기존
Bloc.dispatch(event);

// 변경
Bloc.add(event);
```

#### 2. Transform 함수 변경
```dart
// 기존
transform(events, next)

// 변경  
transformEvents(events, next)
```

#### 3. Dispose 함수 변경
```dart
// 기존
Bloc.dispose();

// 변경
Bloc.close();
```

#### 4. BlocProvider 사용법 개선
```dart
// 새로운 BLoC 생성
BlocProvider<BlocA>(
  create: (BuildContext context) => BlocA(),
  child: ChildA(),
);

// 기존 BLoC 전달
BlocProvider.value(
  value: existingBloc,
  child: ChildA(),
);
```

## 새로운 기능

### MultiBlocProvider
여러 BLoC을 효율적으로 제공
```dart
MultiBlocProvider(
  providers: [
    BlocProvider<BlocA>(create: (context) => BlocA()),
    BlocProvider<BlocB>(create: (context) => BlocB()),
  ],
  child: MyApp(),
);
```

### MultiBlocListener
다중 BLoC 리스너 지원
```dart
MultiBlocListener(
  listeners: [
    BlocListener<BlocA, BlocAState>(listener: (context, state) {}),
    BlocListener<BlocB, BlocBState>(listener: (context, state) {}),
  ],
  child: ChildWidget(),
);
```

### RepositoryProvider
Repository 패턴 지원
```dart
RepositoryProvider(
  create: (context) => MyRepository(),
  child: MyApp(),
);
```

## 학습 목표

### 이론적 이해
- BLoC 패턴의 철학과 원리
- 상태 관리의 중요성
- 아키텍처 패턴의 선택 기준

### 실무 적용
- 복잡한 상태 관리 시나리오 해결
- 테스트 가능한 코드 작성
- 재사용 가능한 컴포넌트 설계

### 성능 최적화
- 불필요한 리빌드 방지
- 메모리 관리와 리소스 해제
- 효율적인 상태 업데이트

## 참고 자료

### 공식 문서
- https://bloclibrary.dev/
- https://pub.dev/packages/flutter_bloc

### 학습 추천 순서
1. Bloc Study.md - 기본 개념 학습
2. 간단한 Counter 예제 구현
3. Bloc Solution Page.md - 버전 업그레이드 가이드
4. 실전 프로젝트 적용

## 장점과 특징

### 개발 생산성
- 명확한 코드 구조와 패턴
- 테스트 코드 작성 용이성
- 팀 개발시 일관된 아키텍처

### 유지보수성
- UI와 로직의 완전한 분리
- 재사용 가능한 비즈니스 로직
- 확장 가능한 아키텍처

### 성능 최적화
- 선택적 UI 업데이트
- 메모리 효율적인 상태 관리
- 자동 리소스 관리

## 학습 환경

- **Flutter**: 2.x 이상
- **Dart**: 2.1 이상  
- **패키지**: flutter_bloc, bloc
- **참고**: 공식 BLoC 라이브러리 문서

이 학습 자료는 BLoC 패턴을 처음 접하는 개발자부터 기존 프로젝트를 업그레이드하려는 개발자까지 모두에게 유용한 종합적인 가이드입니다.