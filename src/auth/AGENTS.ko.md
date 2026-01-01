# 인증 지식 베이스 (AUTH KNOWLEDGE BASE)

## 개요 (OVERVIEW)

Gemini 모델을 위한 Google Antigravity OAuth 구현체입니다. 토큰 관리, fetch 요청 가로채기(interception), thinking 블록 추출 및 응답 변환 등의 기능을 수행합니다.

## 구조 (STRUCTURE)

```
auth/
└── antigravity/
    ├── plugin.ts         # 메인 플러그인 내보내기(export), 훅 등록
    ├── oauth.ts          # OAuth 흐름, 토큰 획득
    ├── token.ts          # 토큰 저장, 갱신(refresh) 로직
    ├── fetch.ts          # Fetch 인터셉터 (622라인) - URL 재작성, 재시도 처리
    ├── response.ts       # 응답 변환, 스트리밍 처리
    ├── thinking.ts       # Thinking 블록 추출 및 변환
    ├── thought-signature-store.ts  # Thinking 블록을 위한 시그니처 캐싱
    ├── message-converter.ts        # 메시지 형식 변환
    ├── request.ts        # 요청 빌드, 헤더 처리
    ├── project.ts        # 프로젝트 ID 관리
    ├── tools.ts          # OAuth를 위한 도구 등록
    ├── constants.ts      # API 엔드포인트, 모델 매핑
    └── types.ts          # TypeScript 인터페이스
```

## 주요 컴포넌트 (KEY COMPONENTS)

| 파일 (File) | 용도 (Purpose) |
|------|---------|
| `fetch.ts` | 핵심 인터셉터 - URL을 재작성하고, 토큰을 관리하며, 재시도를 처리함 |
| `thinking.ts` | `<antThinking>` 블록을 추출하고 OpenCode 호환을 위해 변환함 |
| `response.ts` | 스트리밍 응답 및 SSE 파싱을 처리함 |
| `oauth.ts` | Google 계정을 위한 브라우저 기반 OAuth 흐름 담당 |
| `token.ts` | 토큰 지속성 유지, 만료 확인 및 갱신(refresh) 처리 |

## 작동 원리 (HOW IT WORKS)

1. **가로채기 (Intercept)**: `fetch.ts`가 Anthropic/Google 엔드포인트로 향하는 요청을 가로챕니다.
2. **재작성 (Rewrite)**: URL을 Antigravity 프록시 엔드포인트로 재작성합니다.
3. **인증 (Auth)**: 저장된 OAuth 자격 증명에서 Bearer 토큰을 주입합니다.
4. **응답 (Response)**: 스트리밍 응답을 파싱하고 thinking 블록을 추출합니다.
5. **변환 (Transform)**: OpenCode에서 사용할 수 있도록 응답 형식을 정규화합니다.

## 안티 패턴 - 인증 (ANTI-PATTERNS - AUTH)

- **직접 API 호출**: 항상 fetch 인터셉터를 거쳐야 합니다.
- **코드 내 토큰 저장**: `token.ts`의 저장 레이어를 사용하세요.
- **갱신 무시**: 요청 전에 항상 토큰 만료 여부를 확인하세요.
- **OAuth에서의 블로킹**: OAuth 흐름은 비동기이므로, 메인 스레드를 절대 차단하지 마세요.

## 참고 사항 (NOTES)

- **다중 계정**: 부하 분산을 위해 최대 10개의 Google 계정을 지원합니다.
- **폴백 (Fallback)**: 속도 제한(rate limit) 발생 시 자동으로 다음 사용 가능한 계정으로 전환합니다.
- **Thinking 블록**: 확장된 thinking 기능을 위해 보존 및 변환됩니다.
- **프록시 (Proxy)**: Google AI Studio 접근을 위해 Antigravity 프록시를 사용합니다.
