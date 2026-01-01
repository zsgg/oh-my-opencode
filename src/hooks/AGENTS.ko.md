# 훅 지식 베이스 (HOOKS KNOWLEDGE BASE)

## 개요 (OVERVIEW)

에이전트의 동작을 가로채거나 수정하는 라이프사이클 훅(Lifecycle hooks)입니다. 문맥(Context) 주입, 규칙 강제, 에러 복구, 이벤트 알림 등의 기능을 수행합니다.

## 구조 (STRUCTURE)

```
hooks/
├── agent-usage-reminder/       # 특화 에이전트 사용 권장 알림
├── anthropic-context-window-limit-recovery/     # 토큰 제한 시 Claude 자동 압축
├── auto-slash-command/         # /command 패턴 자동 감지 및 실행
├── auto-update-checker/        # 버전 업데이트 알림
├── background-notification/    # 백그라운드 작업 완료 시 OS 알림
├── claude-code-hooks/          # Claude Code settings.json 통합
├── comment-checker/            # AI의 과도한 주석 생성 방지
│   ├── filters/                # 필터링 규칙 (docstring, directive, bdd 등)
│   └── output/                 # 출력 포맷팅
├── compaction-context-injector/ # 압축(compaction) 중 문맥 주입
├── directory-agents-injector/  # AGENTS.md 파일 자동 주입
├── directory-readme-injector/  # README.md 파일 자동 주입
├── empty-message-sanitizer/    # 빈 메시지 정리
├── interactive-bash-session/   # Tmux 세션 관리
├── keyword-detector/           # ultrawork/search 키워드 감지
├── non-interactive-env/        # CI/헤드리스 환경 처리
├── preemptive-compaction/      # 선제적 세션 압축
├── ralph-loop/                 # 완료될 때까지의 자기 참조 개발 루프
├── rules-injector/             # .claude/rules/의 조건부 규칙 주입
├── session-recovery/           # 세션 에러 복구
├── think-mode/                 # Thinking 트리거 자동 감지
├── thinking-block-validator/   # 메시지 내 Thinking 블록 검증
├── context-window-monitor.ts   # 컨텍스트 사용량 모니터링 (독립형)
├── empty-task-response-detector.ts
├── session-notification.ts     # 유휴 상태 시 OS 알림 (독립형)
├── todo-continuation-enforcer.ts # TODO 완료 강제 (독립형)
└── tool-output-truncator.ts    # 장황한 출력 잘라내기 (독립형)
```

## 훅 카테고리 (HOOK CATEGORIES)

| 카테고리 (Category) | 훅 (Hooks) | 용도 (Purpose) |
|----------|-------|---------|
| 컨텍스트 주입 (Context Injection) | directory-agents-injector, directory-readme-injector, rules-injector, compaction-context-injector | 관련 문맥 자동 주입 |
| 세션 관리 (Session Management) | session-recovery, anthropic-context-window-limit-recovery, preemptive-compaction, empty-message-sanitizer | 세션 라이프사이클 관리 |
| 출력 제어 (Output Control) | comment-checker, tool-output-truncator | 에이전트 출력 품질 제어 |
| 알림 (Notifications) | session-notification, background-notification, auto-update-checker | OS/사용자 알림 |
| 동작 강제 (Behavior Enforcement) | todo-continuation-enforcer, keyword-detector, think-mode, agent-usage-reminder | 에이전트 동작 규범 강제 |
| 환경 (Environment) | non-interactive-env, interactive-bash-session, context-window-monitor | 런타임 환경 적응 |
| 호환성 (Compatibility) | claude-code-hooks | Claude Code settings.json 지원 |

## 훅 추가 방법 (HOW TO ADD A HOOK)

1. 디렉토리 생성: `src/hooks/my-hook/`
2. 파일 생성:
   - `index.ts`: `createMyHook(input: PluginInput)` 내보내기
   - `constants.ts`: 훅 이름 상수 정의
   - `types.ts`: TypeScript 인터페이스 정의 (선택 사항)
   - `storage.ts`: 지속성 상태 관리 (선택 사항)
3. 이벤트 핸들러 반환: `{ PreToolUse?, PostToolUse?, UserPromptSubmit?, Stop?, onSummarize? }`
4. `src/hooks/index.ts`에서 내보내기(export)
5. 메인 플러그인에 등록

## 훅 이벤트 (HOOK EVENTS)

| 이벤트 (Event) | 타이밍 (Timing) | 차단 가능 여부 (Can Block) | 유스케이스 (Use Case) |
|-------|--------|-----------|----------|
| PreToolUse | 도구 실행 전 | 예 | 입력값 검증 및 수정 |
| PostToolUse | 도구 실행 후 | 아니요 | 문맥 추가, 경고 표시 |
| UserPromptSubmit | 사용자 프롬프트 입력 시 | 예 | 메시지 주입, 실행 차단 |
| Stop | 세션 유휴 상태 시 | 아니요 | 후속 프롬프트 주입 |
| onSummarize | 세션 압축 중 | 아니요 | 중요 문맥 보존 |

## 공통 패턴 (COMMON PATTERNS)

- **저장소 (Storage)**: 여러 세션에 걸쳐 상태를 유지하려면 `storage.ts`와 JSON 파일을 사용하세요.
- **세션당 한 번**: 중복 주입을 방지하기 위해 `Set`을 사용하여 주입된 경로를 추적하세요.
- **메시지 주입**: 이벤트 핸들러에서 `{ messages: [...] }`를 반환하세요.
- **차단 (Blocking)**: PreToolUse에서 `{ blocked: true, message: "사유" }`를 반환하세요.

## 안티 패턴 - 훅 (ANTI-PATTERNS - HOOKS)

- **PreToolUse에서의 무거운 계산**: 모든 도구 호출을 느리게 만듭니다.
- **명확한 이유 없는 차단**: 항상 사용자가 조치할 수 있는 메시지를 제공하세요.
- **중복 주입**: 세션별로 이미 주입된 내용을 추적하세요.
- **에러 무시**: 항상 try/catch를 사용하고, 실패를 기록하며 세션이 중단되지 않게 하세요.
