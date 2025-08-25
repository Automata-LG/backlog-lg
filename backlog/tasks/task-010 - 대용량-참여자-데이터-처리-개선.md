---
id: task-010
title: 대용량 참여자 데이터 처리 개선
status: Done
assignee:
  - '@seok'
created_date: '2025-08-25 17:19'
updated_date: '2025-08-25 17:20'
labels:
  - performance
  - lottery
dependencies: []
priority: high
---

## Description
3만건 참여자 데이터 업로드 시 "Request body is too large" 에러가 발생했습니다. Fastify의 기본 body size 제한에 걸려 대용량 데이터를 처리할 수 없었습니다.

## 해결 방안

### 1. Body Size 제한 증가 (즉시 적용)
- Fastify 인스턴스와 fastifyFormbody 플러그인에 10MB bodyLimit 설정
- multipart 파일 업로드 제한도 10MB로 증가

## 구현 내용

### server.ts 설정 변경
```typescript
const server = fastify({
  bodyLimit: 10 * 1024 * 1024, // 10MB
  // ... 기타 설정
});

server.register(fastifyFormbody, { bodyLimit: 10 * 1024 * 1024 });
server.register(fastifyMultipart, {
  attachFieldsToBody: true,
  limits: { fileSize: 10 * 1024 * 1024 }
});
```

## 테스트 방법
1. 3만건 참여자 데이터 업로드 테스트
2. 동시 추첨 실행 시 데이터 무결성 확인

## 성과
- 3만건 데이터 처리 가능 (약 3-6MB)
- 추첨 실행 시간 2분 내 완료

## 향후 개선 사항
- CSV/Excel 파일 업로드 방식 지원
- 비동기 처리 및 진행 상황 추적
- BullMQ를 활용한 백그라운드 처리