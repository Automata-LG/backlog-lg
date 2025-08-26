---
id: task-011
title: Cat-Day 이벤트 페이지 구현
status: Done
assignee: ["@minq"]
created_date: "2025-08-08 00:00" # Based on user's request
labels:
  - frontend
  - event
  - UI
dependencies: []
priority: medium
---

## Description

8월 8일 '세계 고양이의 날'을 기념하여 Cat-Day 이벤트 페이지를 구현했습니다. 이 페이지는 이벤트 이미지 섹션, 해시태그 복사 기능, 그리고 Jammy 업로드 버튼을 포함합니다.

### 주요 구현 기능

1.  **이벤트 페이지 UI 렌더링**

    - 여러 이미지 섹션(`SectionImage`)을 사용하여 이벤트 페이지를 구성합니다.
    - `useImagePreloader` 훅을 사용하여 이벤트 관련 이미지(`SECTION_IMAGES`)를 미리 로드합니다.
    - `useResponsiveRatio` 훅을 적용하여 반응형 디자인을 지원합니다.
    - `useFont` 훅을 통해 'paperlogy' 커스텀 폰트를 적용합니다.

2.  **Jammy 업로드 기능**

    - "Jammy 업로드 하기" 버튼을 구현했습니다.
    - 버튼 클릭 시 `handleJammyButtonClick` 함수가 호출되며, `goUrlOnOpenWebView`를 통해 웹뷰에서 `JAMMY_LINK` (현재 TODO 상태)를 엽니다.

3.  **필수 해시태그 복사 기능**
    - "필수 해시태그 복사하기" 버튼을 구현했습니다.
    - 버튼 클릭 시 `handleCopyHashTags` 함수가 호출되어 미리 정의된 해시태그(`"#LG전자멤버십앱 #고양이사진제출의무법준수캠페인 #세계고양이의날"`)를 클립보드에 복사합니다.
    - `showSuccess` 및 `showError` (토스트 메시지)를 통해 복사 성공/실패 피드백을 제공합니다.
    - 빠른 연속 클릭을 방지하는 메커니즘(`isCopyingRef`)을 포함합니다.

## Acceptance Criteria

<!-- AC:BEGIN -->

- [x] #1 이벤트 페이지 UI가 정상적으로 렌더링되는가?
- [x] #2 이미지 프리로딩 및 반응형 디자인이 적용되었는가?
- [x] #3 커스텀 폰트가 올바르게 적용되었는가?
- [x] #4 "Jammy 업로드 하기" 버튼이 클릭 시 웹뷰를 여는가? (링크는 TODO)
- [x] #5 "필수 해시태그 복사하기" 버튼이 해시태그를 클립보드에 정확히 복사하고 피드백을 제공하는가?
<!-- AC:END -->

## Notes

- 관련 파일: `projs/ex-mono-custom-pages/src/events/2025/08-aug/08-cat-day/CatDayEvent.tsx`
- `JAMMY_LINK`는 현재 빈 값으로, 추후 업데이트 필요합니다.
