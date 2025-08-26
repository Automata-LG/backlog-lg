---
id: task-013
title: 추첨 시스템 데이터 일관성 버그 수정
status: Done
assignee:
  - '@seok'
created_date: '2025-08-26 05:46'
updated_date: '2025-08-26 06:45'
labels:
  - bug-fix
  - lottery-system
dependencies: []
priority: high
---

## Description

추첨 실행과 DB 저장 간 참여자 수 불일치 및 엑셀 다운로드 시 경품별 당첨자 수 불일치 문제를 해결하여 데이터 일관성을 보장한다

## Acceptance Criteria
<!-- AC:BEGIN -->
- [ ] #1 totalEntries와 실제 저장된 LotteryHistParticipant 레코드 수가 정확히 일치함,엑셀 다운로드 시 경품별 당첨자 수가 DB 데이터와 정확히 일치함,memNo에 앞뒤 공백이 있는 경우에도 정상적으로 처리됨,대량 데이터(1500명) 추첨에서 데이터 일관성이 보장됨
<!-- AC:END -->

## Implementation Notes

문제 1: 참여자 memNo 앞뒤 공백으로 인한 Map 키 불일치 해결 - filterParticipants에서 trim() 처리 추가, Array.from() 중복 호출 제거로 메모리 최적화.

문제 2: 페이징 처리 시 동일 createdAt 값으로 인한 정렬 순서 불안정 해결
- LotteryHistParticipantOrderBy enum에 id_asc, id_desc 추가
- 모든 관련 스키마의 기본 정렬을 createdAt_desc에서 id_desc로 변경
- 안정적인 id 기반 정렬로 페이징 시 데이터 중복/누락 방지

변경 파일:
- apps/emapp/src/router/ai/utils-lottery.ts (memNo trim 처리, Array.from 최적화)
- apps/emapp/src/router/ai/consts-lottery.ts (id 정렬 옵션 추가)
- apps/emapp/src/router/ai/schemas-lottery.ts (기본 정렬 변경)
