---
id: task-009
title: 추첨 결과 경품 순서 관리 기능 구현
status: Done
assignee:
  - '@seok'
created_date: '2025-08-25 17:11'
updated_date: '2025-08-25 17:13'
labels:
  - lottery
  - api
dependencies: []
priority: high
---

## Description
추첨 결과 발표 생성/수정 시 경품과 당첨자의 순서를 변경할 수 없는 이슈가 발생했습니다. 여러 LotteryHist 레코드를 하나의 LotteryResult로 합칠 때 경품 순서가 한국어 가나다순으로 자동 정렬되어, 원하는 순서대로 표시할 수 없었습니다.

## 해결 방안
1. **데이터 모델 개선**
   - LotteryResult 모델에 `prizes` JSON 필드 추가
   - 결과 발표별로 경품 순서를 독립적으로 관리

2. **어드민 API 개선**
   - 결과 생성/수정 시 경품 자동 계산 및 저장
   - 생성/수정 API에서 prizes 파라미터로 순서 지정 가능
   - 경품 내용 변경 방지 검증 로직 추가 (순서만 변경 가능)

3. **앱 API 개선**
   - 저장된 경품 순서대로 표시
   - 데이터 무결성 검증 추가
   - 기존 하위 호환성 제거 - prizes 필드 필수로 변경

## 구현 내용

### 1. Prisma 스키마 변경
```prisma
model LotteryResult {
  // ... 기존 필드
  prizes Json // 계산된 경품 배열 [{prizeName: string, winnerCount: number}] - 필수 필드로 변경
  // ... 기타 필드
}
```

### 2. 주요 함수 구현
- `calculatePrizesFromHists`: 여러 이력에서 경품 합산 및 기본 정렬(가나다순)
- `validateSamePrizeContent`: 경품 내용 변경 방지 검증 (순서만 변경 가능)
- `parseLotteryPrizes`: 중앙화된 에러 처리 적용

### 3. API 스키마 동기화
- `adminLotteryDetailOutputSchema`: hists 배열에 `allowsDuplicateEntry: booleanZod` 필드 추가
- `adminLotteryResultDetailOutputSchema`: hists 배열에 `allowsDuplicateEntry: booleanZod` 필드 추가
- `adminLotteryHistOutputItemSchema`: `allowsDuplicateEntry` 필드 포함 확인

### 4. 문서 동기화
- **API 문서** (`lottery-api-reference.mdx`):
  - 추첨 상세 조회 응답에 `canExecute`, `prizeSummary` 필드 추가
  - 모든 API 응답 예시가 실제 스키마와 일치하도록 수정
- **ERD 문서** (`lottery-erd.mdx`):
  - LotteryResult의 prizes 필드 설명: "기본 가나다순, 순서 지정 가능"으로 수정

### 5. 에러 코드 추가
- `LOTTERY_RESULT_PRIZE_ORDER_ONLY` (FDE-L-204): 경품 내용은 변경 불가, 순서만 변경 가능
- `LOTTERY_RESULT_DATA_MISMATCH` (FDE-L-205): 저장된 데이터 불일치
- `INVALID_PRIZE_DATA` (FDE-L-505): 잘못된 경품 데이터 형식

## 영향 범위
- `/projs/EMAPP-LGE-Repo/packages/prisma/prisma/schema.prisma`
- `/projs/EMAPP-LGE-Repo/apps/emapp/src/router/externalAdmin-lottery.ts`
- `/projs/EMAPP-LGE-Repo/apps/emapp/src/router/externalApp-lottery.ts`
- `/projs/EMAPP-LGE-Repo/apps/emapp/src/router/ai/utils-lottery.ts`
- `/projs/EMAPP-LGE-Repo/apps/emapp/src/router/ai/consts-lottery.ts`
- `/projs/EMAPP-LGE-Repo/apps/emapp/src/router/ai/schemas-lottery.ts`
- `/docs/lottery-system/lottery-api-reference.mdx`
- `/docs/lottery-system/lottery-erd.mdx`

## 테스트 방법
1. 추첨 결과 생성 후 경품 순서 확인 (기본 가나다순)
2. 결과 수정 API에서 prizes 파라미터로 순서 변경
3. 앱에서 변경된 순서대로 표시되는지 확인
4. 경품 내용 변경 시도 시 `LOTTERY_RESULT_PRIZE_ORDER_ONLY` 에러 확인
5. 데이터 무결성 검증 동작 확인

## 주의사항
- 기존 하위 호환성 제거 - prizes 필드 필수로 변경