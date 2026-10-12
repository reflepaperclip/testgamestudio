# User Flow Diagrams

## Core Loop Flow: Stealth → Info Collection → Escape

```mermaid
stateDiagram-v2
    [*] --> MainMenu
    MainMenu --> NewGame: 신규 게임 시작
    NewGame --> Tutorial: 첫 플레이 여부 확인
    Tutorial --> Gameplay: 튜토리얼 완료 또는 스키ップ
    Gameplay --> StealthPhase: 플레이어 자유 탐색 시작
    
    StealthPhase --> InfoCollection: 정보 객체 접근 및 상호작용
    InfoCollection --> PostCollection: 정보 획득 완료
    PostCollection --> EscapePhase: 탈출 모드 활성화
    EscapePhase --> Exit: 안전 지점 도달
    Exit --> MainMenu: 미션 종료 또는 메인 메뉴 복귀
    
    note right of StealthPhase
        - 은신도 게이지 관리
        - 적 AI 회피
        - 이동 경로 계획
    end note
    
    note right of InfoCollection
        - 상호작용 거리 내 진입
        - 고유 키 입력 (E키)
        - 진행도 바 채우기 (5초 기본)
        - 적 감지 시 중단 가능
    end note
    
    note right of EscapePhase
        - 속도 증가한 적 회피
        - 경로 탐색 및 최적화
        - 탈출점 도달 시 승리
    end note
```

## Screen Transition Flow

```mermaid
stateDiagram-v2
    [*] --> MainHUD
    MainHUD --> PauseMenu: Esc 키 또는 패드 시작
    PauseMenu --> MainHUD: "재개" 선택 또는 Esc
    PauseMenu --> Settings: "옵션" 선택
    Settings --> PauseMenu: "돌아가기" 선택
    Settings --> MainHUD: "메인 메뉴로 복귀" 선택
    MainHUD --> Notification: 이벤트 발생 (적 발견, 목표 완료 등)
    Notification --> MainHUD: 5초 경과 또는 사용자 확인
    MainHUD --> HelpOverlay: 도움말 요청 또는 첫 플레이
    HelpOverlay --> MainHUD: 닫기 입력 또는 10초 타임아웃
    MainHUD --> GameOver: 탐지됨 (FAILED 상태)
    GameOver --> MainMenu: "다시 시도" 또는 "메인 메뉴"
    MainHUD --> Clear: 탈출 성공 (ESCAPED 상태)
    Clear --> MainMenu: "다음 미션" 또는 "메인 메뉴"
    
    note right of GameOver
        - "탐지됨! 실패..." 표시
        - 리플레이 옵션 제공
    end note
    
    note right of Clear
        - "탈출 성공!" 표시
        - 업적 잠금 해제 가능
        - 다음 미션_unlock
    end note
```

## Interaction Flow: Player → Object → System

```mermaid
sequenceDiagram
    participant Player
    participant HUD
    participant GameWorld
    participant AI
    
    Player->>HUD: 접근 시 상호작용 프롬프트 표시
    HUD->>Player: "E키로 상호작용" 텍스트 표시
    Player->>HUD: E키 입력 (유지)
    HUD->>GameWorld: 상호작용 시작 신호
    GameWorld->>AI: 거리 및 각도 조건 확인
    AI-->>GameWorld: 조건 충족 여부 판단
    alt 조건 충족
        GameWorld->>GameWorld: 획득 진행도 증가 (시간 기반)
        GameWorld-->>HUD: 진행도 업데이트
        alt 진행도 100% 달성
            GameWorld->>HUD: 정보 획득 완료 알림
            HUD->>Player: "목표: 탈출지점 도달" 표시
            GameWorld->>AI: 적 행동 상태 변경 (경보 레벨 상승)
        alt 플레이어가 범위 외 이동
            GameWorld->>GameWorld: 상호작용 취소
            HUD->>Player: 프롬프트 숨기기
        alt 적에게 탐지됨
            GameWorld->>AI: 탐지 상태 전환
            AI-->>HUD: 위험 수준 업데이트
            HUD->>AlertFlash: 화면 가장자리 플래시 트리거
        end
    else 조건 불충족
        HUD->>Player: "조건이 만족되지 않음" 피드백 (간헐적)
    end
```

## Onboarding Flow: New Player Experience

```mermaid
stateDiagram-v2
    [*] --> MainMenu
    MainMenu --> Tutorial: 최초 실행 플래그 확인
    
    Tutorial --> Intro: 게임 소개 화면
    Intro --> Movement: 조작 튜토리얼 시작
    Movement --> Stealth: 은신 시스템 설명
    Stealth --> InfoObject: 정보 수집 방법 설명
    InfoObject --> Collection: 실제 상호작용 체험
    Collection --> Escape: 탈출 방법 설명
    Escape --> Completion: 튜토리얼 미션 성공
    Completion --> Gameplay: 메인 게임으로 이동
    
    note right of Intro
        -.skip 가능 (설정에서)
    end note
    
    note right of Movement
        - WASD 이동 및 점프 설명
        - 포커스 이동 순서 안내
    end note
    
    note right of Stealth
        - 은신도 게이지 시각화
        - 적 시야 원뿔 표시
        - 커버 메커니즘 설명
    end note
    
    note right of InfoObject
        - 상호작용 프롬프트 표시
        - 진행도 바 채우기 체험
        - 획득 효과음 및 시각 효과
    end note
    
    note right of Escape
        - 안전 지점 도달 방법
        - 미션 완료 피드백
    end note
```

## Settings Menu Flow

```mermaid
stateDiagram-v2
    [*] --> MainHUD
    MainHUD --> PauseMenu: Esc 키
    PauseMenu --> Settings: "설정" 메뉴 선택
    
    Settings --> Graphics: 그래픽 설정
    Graphics --> Settings: "돌아가기" 선택
    
    Settings --> Controls: 컨트롤 리매핑
    Controls --> Settings: "완료" 저장
    Controls --> MainHUD: "취소" 없이 닫기 (변경사항 버림)
    
    Settings --> Audio: 사운드 설정
    Audio --> Settings: "돌아가기" 선택
    
    Settings --> Gameplay: 게임플레이 설정
    Gameplay --> Settings: "돌아가기" 선택
    
    Settings --> Accessibility: 접근성 옵션
    Accessibility --> Settings: "적용" 저장
    Accessibility --> MainHUD: "취소" 없이 닫기
    
    Settings --> MainHUD: "메인 메뉴로 복귀" 선택
    MainHUD --> MainMenu: "메인 메뉴로 복귀" 선택 from PauseMenu
```

## Accessibility Validation Flow

```mermaid
stateDiagram-v2
    [*] --> Baseline
    Baseline --> ColorBlindTest: 색약 모드 토글
    ColorBlindTest --> VisualNovel: 핵심 정보는 색상만이 아님 확인
    VisualNovel --> TextScaling: 텍스트 크기 확대 확인
    TextScaling --> LayoutBreak: 레이아웃 깨짐 여부 확인
    LayoutBreak --> InputRemap: 입력 리매핑 확인
    InputRemap --> Keyboard: 키보드 리매핑
    InputRemap --> Controller: 컨트롤러 리매핑
    Controller --> Motor: 모터 기능 테스트
    Motor --> Complete: 모든 테스트 통과
    
    note right of ColorBlindTest
        - 적색/녹색 구분 테스트
        - 대안적 구분 수단 (두께, 패턴) 검증
    end note
    
    note right of LayoutBreak
        - 해상도 변경 시 요소 겹침 여부
        - 중요한 정보가 화면 밖으로 벗어남 여부
    end note
    
    note right of Motor
        - 대체 입력 장치 호환성
        - 키보드 전량 리매핑 가능성
    end note
```