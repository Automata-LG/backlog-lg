---
id: task-010
title: 추첨 시스템 엑셀 다운로드 수정 & 파일 업로드 용량 증가
status: Done
assignee:
  - '@seok'
created_date: '2025-08-22 09:39'
updated_date: '2025-08-22 09:48'
labels:
  - lottery
  - backend
  - excel
dependencies: []
priority: high
---

## Description

추첨 시스템 엑셀 다운로드 헤더 및 데이터 매핑을 수정했습니다.

## Acceptance Criteria
<!-- AC:BEGIN -->
- [x] #1 bullmq-excel.ts의 엑셀 헤더가 6개 컬럼으로 변경됨,데이터 매핑이 새로운 헤더에 맞춰 정상 작동함
<!-- AC:END -->

## Implementation Notes

### 완료된 수정 사항

#### bullmq-excel.ts 파일 수정

1. **엑셀 헤더 변경 (600번째 줄)**
   - 변경 전: '회원번호', '회원명', '연락처', '추첨명', '상태', '참여 횟수', '상품명', '참여일시'
   - 변경 후: '회원번호', '회원명', '연락처', '응모 수', '당첨 여부', '경품명'

2. **데이터 매핑 수정 (644-653번째 줄)**
   - 추첨명(lottery.name) 제거
   - 참여일시(createdAt) 제거
   - 6개 컬럼에 맞춰 데이터 재배열

3. **파일 업로드 용량 확인**
   - server.ts 50번째 줄: 이미 10MB로 설정되어 있음
   - `limits: { fileSize: 10 * 1024 * 1024 }`

파일 위치: /apps/emapp/src/bullmq-excel.ts
