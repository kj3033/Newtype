### Context
n8n의 HTTP Request 노드를 사용하여 외부 웹사이트의 데이터를 수집(Web Scraping)할 때, 기본적으로 노드가 사용하는 User-Agent가 'n8n'으로 설정되어 있어 봇 탐지 시스템(Bot Detection)에 의해 차단되는 경우가 빈번합니다. 이를 방지하기 위해 실제 브라우저와 유사한 환경임을 명시하는 User-Agent를 헤더에 직접 주입하여 차단을 우회하는 전략이 필요합니다.

### Core
HTTP Request 노드의 헤더 섹션에 아래와 같이 구성하여 요청을 전송합니다.

* 헤더 이름: User-Agent
* 헤더 값: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36

JSON 모드에서의 설정 예시는 다음과 같습니다.

```json
{
  "headers": {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"
  }
}
```

### Insight
User-Agent를 변경하는 것만으로 단순한 봇 차단은 우회할 수 있지만, Cloudflare나 Akamai와 같은 고도화된 탐지 솔루션은 TLS Fingerprinting이나 JavaScript 렌더링 여부를 함께 검사합니다. 단순히 헤더를 수정하는 것은 초기 단계의 대응이며, 더 강력한 보호가 적용된 사이트의 경우 n8n의 HTTP Request 노드 대신 브라우저 자동화 도구(Puppeteer, Playwright)를 호출하거나 별도의 프록시 서비스를 사용하는 것이 더욱 효과적입니다.

**출처:** [n8n HTTP Request Node Documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)