### Context
n8n에서 AI Agent 노드를 통해 Google Gemini 모델을 사용할 때, 도구(Tools) 호출 시 `400 Bad Request` 에러가 발생하는 문제가 확인되었습니다. 구체적으로는 'Unknown name "const" at tools[0].function_declarations'라는 메시지와 함께 요청이 차단됩니다. 이는 Gemini API가 JSON 스키마 내의 `const` 필드를 지원하지 않아 발생하는 호환성 문제입니다.

### Core
Gemini API가 `const` 키워드를 인식하지 못하므로, 직접 정의한 커스텀 도구의 경우 `const` 대신 `enum` 배열을 사용하여 스키마를 수정해야 합니다.

*수정 전 (에러 발생)*
```json
{
  "type": "string",
  "const": "production"
}
```

*수정 후 (권장)*
```json
{
  "type": "string",
  "enum": ["production"]
}
```

n8n 설정 측면에서는 Gemini를 유지해야 한다면 Fallback 노드를 통해 OpenAI(GPT)나 Claude 모델을 대기시켜 워크플로우의 안정성을 확보해야 합니다.

### Insight
Gemini API는 현재 JSON 스키마 구현체로서 `const` 키워드를 지원하지 않는 엄격한 파라미터 유효성 검사를 수행합니다. 이는 OpenAI나 Anthropic(Claude)이 동일한 스키마를 문제없이 처리하는 것과 대조적인 부분입니다. n8n의 AI Agent 노드가 내부적으로 생성하는 도구 정의 스키마를 Gemini 사양에 맞춰 자동으로 변환(Transpilation)해주기 전까지는 `enum`을 사용하는 우회 전략이 가장 안정적입니다. 향후 n8n 패치를 통해 이 호환성 레이어가 개선될지 지속적인 모니터링이 필요합니다.

**출처:** [n8n Community: Gemini Tool definition error](https://community.n8n.io/t/google-gemini-tool-calling-error-unknown-name-const/153549)