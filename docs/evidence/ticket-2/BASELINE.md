# Ticket 2 — AI Response Validation & Safe Error Handling

## Baseline

Branch: `bob/ticket-2`

Starting point: `d53e4e7`

## Verified Findings

### 1. Unsafe JSON parsing

`server.ts` directly parses Gemini output:

`JSON.parse(resultText)`

If Gemini returns malformed JSON, parsing throws and the request falls into
the generic 500 handler.

### 2. No runtime validation of AI response

The Gemini response schema defines required fields, but the parsed response
is returned directly to the client without application-level validation.

Required contract-analysis fields include:

- title
- riskScore
- summary
- plainEnglishTranslation
- redFlags
- questionsForAttorney

### 3. Internal error exposure

The contract-analysis endpoint can return:

`err.message`

This may expose implementation/provider details to the client.

### 4. Related findings outside Ticket 2

Similar raw error handling exists in:

- AI legal assistant
- Stripe checkout

These are recorded but are outside the initial Ticket 2 implementation scope.

## Ticket 2 Goal

Make `/api/analyze-contract` fail safely when AI output is malformed,
incomplete, or otherwise invalid.

## Planned Requirements

1. Safely parse AI JSON.
2. Validate required response fields.
3. Reject malformed or incomplete AI responses.
4. Return a stable user-safe error response.
5. Keep detailed diagnostic information server-side.
6. Preserve successful contract analysis behavior.
7. Preserve Ticket 1 PDF extraction behavior.
8. Add automated tests for valid and invalid AI responses.

## Baseline Status

No Ticket 2 application code has been changed yet.
