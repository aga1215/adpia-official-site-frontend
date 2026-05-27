
---

# Frontend README

```md
# ADPIA Official Website - Frontend

ADPIA 공식 홈페이지의 프론트엔드입니다.  
동아리 소개, 모집 공지, Q&A, 커뮤니티, 아카이브, 관리자 페이지를 제공합니다.

> Service: https://adpia.or.kr  

---

## Overview

ADPIA Official Website는 대학생 광고 연합 동아리 ADPIA의 공식 웹사이트입니다.

Frontend는 React와 TypeScript 기반으로 구현했으며, 방문자용 페이지, 게시판 UI, 인증 흐름, 관리자 페이지, 파일 업로드 UI를 담당합니다.

---

## Role

- React / TypeScript 기반 화면 구현
- Tailwind CSS 기반 반응형 UI 구현
- JWT 인증 상태 관리
- Axios Interceptor 기반 토큰 재발급 처리
- 게시판별 UI 정책 분리
- S3 Presigned URL 기반 파일 업로드 연동
- 관리자 회원 관리 화면 구현
- 모바일 환경 대응

---

## Tech Stack

- React
- TypeScript
- Vite
- Tailwind CSS
- Axios
- React Router
- Nginx
- Docker
- GitHub Actions

---

## Main Features

- 메인 페이지 및 ADPIA 소개 페이지
- 모집 공지 / Q&A 게시판
- 비회원 Q&A 작성 및 비밀글 열람 UI
- 커뮤니티 게시판
- 활동사진 / OB 게시판
- 아카이브 게시판
- 로그인 / 회원가입 / 이메일 인증
- 관리자 회원 관리 페이지
- 이미지 / 파일 업로드
- 반응형 레이아웃

---

## UI Direction

ADPIA의 브랜드 컬러를 기준으로 전체 UI를 구성했습니다.

```txt
Main Color: #813eb6

주요 UI 방향:

모바일 대응을 고려한 카드형 게시글 피드
공지글, 비밀글, 고정글 상태를 시각적으로 구분
조회 가능한 게시판은 비로그인 사용자에게도 노출
작성 / 수정 / 삭제 버튼은 권한이 있는 사용자에게만 노출
관리자 기능과 일반 사용자 기능을 명확히 분리
Architecture
React Frontend
  |
  | Axios
  v
Spring Boot Backend API
  |
  | Presigned URL
  v
AWS S3
Auth Flow
1. 사용자가 로그인
2. Access Token 기반으로 API 요청
3. Axios Interceptor가 Authorization Header 추가
4. 401 응답 발생 시 Refresh API 호출
5. 재발급 성공 시 기존 요청 1회 재시도
6. 재발급 실패 시 로그아웃 처리
File Upload Flow
1. 사용자가 이미지 또는 파일 선택
2. Frontend가 파일 타입과 크기 검증
3. Backend에 Presigned URL 요청
4. S3에 직접 PUT 업로드
5. 업로드된 URL을 게시글 데이터에 포함
Board UI Policy

게시판의 조회와 작성 권한을 분리했습니다.

목록 / 상세 조회: 공개 또는 게시판 정책에 따라 허용
글쓰기 / 수정 / 삭제: 로그인 또는 관리자 권한 필요
비밀글: 잠금 아이콘 표시 후 비밀번호 검증

예를 들어 활동사진 게시판은 비로그인 사용자도 메뉴와 목록을 볼 수 있도록 하고, 작성 버튼만 권한 조건에 따라 노출합니다.

Technical Decisions
카드형 게시글 UI

모바일 환경에서도 게시글 정보를 읽기 쉽게 보여주기 위해 테이블형 UI보다 카드형 피드 UI를 선택했습니다.

게시판별 UI 정책 분리

게시판마다 조회 가능 여부, 작성 권한, 댓글 여부가 달라 UI에서도 정책을 분리했습니다.
이를 통해 메뉴 노출과 작성 버튼 노출을 별도로 제어했습니다.

Axios Interceptor 기반 인증 처리

Access Token 만료 시 사용자가 매번 다시 로그인하지 않도록 Refresh API를 호출하고, 기존 요청을 한 번 재시도하도록 구성했습니다.

S3 직접 업로드

이미지와 파일 업로드 시 서버 부하를 줄이기 위해 Presigned URL을 받아 S3로 직접 업로드하도록 구현했습니다.

Troubleshooting
비로그인 사용자에게 조회 가능한 게시판 메뉴가 숨겨지는 문제 수정
Access Token 만료 시 Refresh 요청 후 기존 요청 재시도 처리
Presigned URL 업로드 시 Content-Type 불일치 문제 대응
HEIC 이미지의 브라우저 표시 문제를 고려하여 JPEG 변환 방식 검토
Local Development
pnpm install
pnpm dev

Build:

pnpm build
Environment Variables
VITE_API_BASE_URL=http://localhost:8080/api

운영 환경에서는 실제 API 주소로 설정합니다.

VITE_API_BASE_URL=https://adpia.or.kr/api
Future Improvements
HEIC 이미지 JPEG 변환 업로드
게시글 에디터 UX 개선
이미지 lazy loading 적용
관리자 통계 기능 추가
접근성 개선
