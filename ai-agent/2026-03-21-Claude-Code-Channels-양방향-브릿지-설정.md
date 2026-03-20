### Context
Claude Code Channels는 Anthropic의 AI 에이전트 도구인 Claude Code를 Discord나 Telegram과 연결하여, 사용자가 채팅 인터페이스를 통해 직접 에이전트에게 작업을 지시하고 실시간으로 응답을 받을 수 있게 하는 양방향 브릿지(Bidirectional Bridge) 시스템입니다. 개발자가 로컬 환경이 아닌 외부 메신저를 통해 에이전트와 소통하며 워크플로우를 효율적으로 관리하기 위해 설계되었습니다.

### Core
Claude Code Channels를 설정하고 실행하기 위한 핵심 요구사항과 절차는 다음과 같습니다.

* **런타임 요구사항**: 반드시 Bun 런타임이 설치되어 있어야 합니다.
* **설치**: 패키지 매니저를 통해 Claude Code 내장 기능을 활성화하거나 관련 채널 패키지를 설치합니다.
* **페어링**:
    * 사용자가 선택한 플랫폼(Discord 또는 Telegram)의 봇 토큰을 설정합니다.
    * 페어링 명령어를 실행하여 로컬 CLI 세션과 원격 메신저 채널을 인증합니다.
* **세션 활성화**: 메신저로부터 명령어를 수신하려면 로컬 시스템에서 Claude Code 프로세스가 활성화되어 있어야 합니다. 이벤트는 에이전트가 실행 중일 때만 동기적으로 전달됩니다.

```bash
# Bun을 통한 Claude Code 실행 및 채널 연결 예시
bun x @anthropic-ai/claude-code channels --platform discord
```

### Insight
Claude Code Channels는 개발자가 터미널을 상시 모니터링하지 않아도 원격에서 에이전트를 제어할 수 있게 함으로써 에이전트 워크플로우의 접근성을 크게 향상합니다. 다만, 해당 시스템은 로컬 CLI 세션이 활성화된 상태에서만 작동하므로 서버리스 환경보다는 로컬 워크스테이션이나 항상 켜져 있는 개발 서버에서 운용하기에 적합합니다. 보안 측면에서는 플랫폼별 봇 토큰 관리와 로컬 세션 인증을 철저히 분리하여 관리하는 것이 중요합니다.

**출처:** [Anthropic Claude Code Documentation](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview)