# 기능 지식 베이스 (FEATURES KNOWLEDGE BASE)

## 개요 (OVERVIEW)

Claude Code 호환 레이어 및 핵심 기능 모듈입니다. Claude Code의 설정, 명령어, 스킬, MCP, 훅(Hook)이 OpenCode에서 원활하게 작동하도록 지원합니다.

## 구조 (STRUCTURE)

```
features/
├── background-agent/           # 백그라운드 작업 관리
│   ├── manager.ts              # 작업 라이프사이클, 알림
│   ├── manager.test.ts
│   └── types.ts
├── builtin-commands/           # 내장 슬래시 명령어 정의
├── builtin-skills/             # 내장 스킬 (playwright 등)
│   └── */SKILL.md              # 각 스킬별 개별 디렉토리
├── claude-code-agent-loader/   # ~/.claude/agents/*.md에서 에이전트 로드
├── claude-code-command-loader/ # ~/.claude/commands/*.md에서 명령어 로드
├── claude-code-mcp-loader/     # .mcp.json에서 MCP 로드
│   └── env-expander.ts         # ${VAR} 환경변수 확장
├── claude-code-plugin-loader/  # installed_plugins.json에서 외부 플러그인 로드
├── claude-code-session-state/  # 세션 상태 지속성 관리
├── opencode-skill-loader/      # OpenCode 및 Claude 경로에서 스킬 로드
├── skill-mcp-manager/          # 스킬에 내장된 MCP 서버
│   ├── manager.ts              # 지연 로딩(Lazy-loading) MCP 클라이언트 라이프사이클
│   └── types.ts
└── hook-message-injector/      # 대화에 메시지 주입
```

## 로더 우선순위 (LOADER PRIORITY)

각 로더는 여러 디렉토리에서 읽어오며, 가장 높은 우선순위가 먼저 적용됩니다.

| 로더 (Loader) | 우선순위 순서 (Priority Order) |
|--------|---------------|
| 명령어 (Commands) | `.opencode/command/` > `~/.config/opencode/command/` > `.claude/commands/` > `~/.claude/commands/` |
| 스킬 (Skills) | `.opencode/skill/` > `~/.config/opencode/skill/` > `.claude/skills/` > `~/.claude/skills/` |
| 에이전트 (Agents) | `.claude/agents/` > `~/.claude/agents/` |
| MCPs | `.claude/.mcp.json` > `.mcp.json` > `~/.claude/.mcp.json` |

## 로더 추가 방법 (HOW TO ADD A LOADER)

1. 디렉토리 생성: `src/features/claude-code-my-loader/`
2. 파일 생성:
   - `loader.ts`: `load()` 함수를 포함한 메인 로더 로직
   - `types.ts`: TypeScript 인터페이스 정의
   - `index.ts`: 배럴 내보내기 (Barrel export)
3. 패턴: 여러 디렉토리에서 읽고, 우선순위에 따라 병합한 뒤, 정규화된 설정을 반환합니다.

## 백그라운드 에이전트 상세 (BACKGROUND AGENT SPECIFICS)

- **작업 라이프사이클**: pending(대기) → running(실행 중) → completed(완료)/failed(실패)
- **알림**: 작업 완료 시 OS 알림 전송 (설정 가능)
- **결과 조회**: task_id를 사용하는 `background_output` 도구 사용
- **취소**: task_id 또는 all=true를 사용하는 `background_cancel` 도구 사용

## 설정 토글 (CONFIG TOGGLES)

`oh-my-opencode.json`에서 기능을 비활성화할 수 있습니다.

```json
{
  "claude_code": {
    "mcp": false,      // .mcp.json 로딩 건너뛰기
    "commands": false, // commands/*.md 로딩 건너뛰기
    "skills": false,   // skills/*/SKILL.md 로딩 건너뛰기
    "agents": false,   // agents/*.md 로딩 건너뛰기
    "hooks": false     // settings.json 훅 건너뛰기
  }
}
```

## 훅 메시지 인젝터 (HOOK MESSAGE INJECTOR)

- **용도**: 대화의 특정 지점에 시스템 메시지를 주입합니다.
- **타이밍**: PreToolUse, PostToolUse, UserPromptSubmit, Stop
- **형식**: `{ messages: [{ role: "user", content: "..." }] }` 형식을 반환합니다.

## MCP 로더 (claude-code-mcp-loader)

`.mcp.json` 파일에서 MCP 서버 설정을 로드합니다. Claude Code와 완전히 호환됩니다.

### 파일 위치 (우선순위 순서)

| 경로 (Path) | 범위 (Scope) | 설명 (Description) |
|------|-------|-------------|
| `~/.claude/.mcp.json` | 사용자 (user) | 사용자 전역 MCP 서버 |
| `./.mcp.json` | 프로젝트 (project) | 프로젝트 전용 MCP 서버 |
| `./.claude/.mcp.json` | 로컬 (local) | 로컬 재정의 (git에서 무시됨) |

### .mcp.json 형식

```json
{
  "mcpServers": {
    "server-name": {
      "type": "stdio|http|sse",
      "command": "npx",
      "args": ["-y", "@anthropics/mcp-server-example"],
      "env": {
        "API_KEY": "${MY_API_KEY}"
      },
      "disabled": false
    }
  }
}
```

### 서버 유형 (Server Types)

| 유형 (Type) | 필수 필드 | 설명 (Description) |
|------|-----------------|-------------|
| `stdio` (기본값) | `command`, `args?`, `env?` | 로컬 서브프로세스 MCP |
| `http` | `url`, `headers?` | HTTP 기반 원격 MCP |
| `sse` | `url`, `headers?` | SSE 기반 원격 MCP |

### 환경 변수 확장

모든 문자열 필드에서 `${VAR}` 구문을 지원합니다.

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["${HOME}/mcp-server/index.js"],
      "env": {
        "API_KEY": "${MY_API_KEY}",
        "DEBUG": "${DEBUG:-false}"
      }
    }
  }
}
```

### 예시 (Examples)

**stdio (로컬 서브프로세스)**:
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropics/mcp-server-filesystem", "/path/to/dir"]
    }
  }
}
```

**http (원격)**:
```json
{
  "mcpServers": {
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/api",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}"
      }
    }
  }
}
```

**서버 비활성화**:
```json
{
  "mcpServers": {
    "expensive-server": {
      "command": "...",
      "disabled": true
    }
  }
}
```

### 변환 (Transformation)

Claude Code 형식 → OpenCode 형식 변환:

| Claude Code | OpenCode |
|-------------|----------|
| `type: "stdio"` | `type: "local"` |
| `type: "http\|sse"` | `type: "remote"` |
| `command` + `args` | `command: [cmd, ...args]` |
| `env` | `environment` |
| `headers` | `headers` |

## 스킬 MCP 매니저 (SKILL MCP MANAGER)

- **용도**: 스킬의 YAML 프론트매터에 포함된 MCP 서버를 관리합니다.
- **라이프사이클**: 지연된 클라이언트 로딩, 세션 범위 내 정리 작업을 수행합니다.
- **설정**: 스킬의 YAML 프론트매터에 있는 `mcp` 필드가 서버 설정을 정의합니다.
- **도구**: `skill_mcp` 도구를 통해 MCP 기능(도구, 리소스, 프롬프트)을 노출합니다.

## 내장 스킬 (BUILTIN SKILLS)

- **위치**: `src/features/builtin-skills/*/SKILL.md`
- **사용 가능**: `playwright` (브라우저 자동화)
- **비활성화**: 설정에서 `disabled_skills: ["playwright"]` 추가

## 안티 패턴 - 기능 (ANTI-PATTERNS - FEATURES)

- **로딩 시 블로킹**: 로더는 시작 시 실행되므로 속도를 빠르게 유지하세요.
- **에러 핸들링 누락**: 항상 try/catch를 사용하고, 실패를 기록하며, 에러 시 빈 값을 반환하세요.
- **우선순위 무시**: 높은 우선순위 디렉토리가 낮은 우선순위를 덮어써야 합니다.
- **사용자 파일 수정**: 로더는 읽기 전용이며, ~/.claude/ 디렉토리에 직접 쓰지 마세요.
