# ADPIA Official Website - Frontend

ADPIA 공식 홈페이지의 프론트엔드입니다.  
동아리 소개, 모집 공지, Q&A, 커뮤니티, 아카이브, 관리자 페이지를 제공합니다.

새탭으로 열기) ctrl+클릭
> Service: <a href="https://adpia.or.kr" target="_blank" rel="noopener noreferrer">ADPIA Official Website</a>  
> Backend Repository: <a href="https://github.com/aga1215/adpia-official-site-backend" target="_blank" rel="noopener noreferrer">adpia-backend</a>

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

ADPIA의 브랜드 컬러와 공식 홈페이지의 성격을 반영하여, 정보 접근성과 운영 편의성을 함께 고려한 UI를 구성했습니다.

```txt
Main Color: #813eb6
```

### Design Principles

- **Card-based Feed**  
  모바일 환경에서도 게시글 정보를 쉽게 확인할 수 있도록 테이블형 목록 대신 카드형 피드 UI를 적용했습니다.

- **Clear Content Status**  
  공지글, 고정글, 비밀글, 댓글 여부 등 게시글 상태를 시각적으로 구분하여 사용자가 목록에서 정보를 빠르게 파악할 수 있도록 했습니다.

- **Public Read, Protected Write**  
  비로그인 사용자도 조회 가능한 게시판은 메뉴와 목록을 볼 수 있도록 하고, 작성 / 수정 / 삭제 버튼은 권한이 있는 사용자에게만 노출했습니다.

- **Responsive Layout**  
  데스크톱뿐 아니라 모바일에서도 주요 메뉴, 게시글 카드, 작성 버튼, 관리자 화면이 자연스럽게 동작하도록 반응형 레이아웃을 적용했습니다.

- **Separated Admin Experience**  
  일반 사용자 화면과 관리자 기능을 분리하여, 관리자 권한이 필요한 기능은 별도 페이지와 조건부 UI로 제공했습니다.

---

## Architecture

Frontend는 Spring Boot Backend API와 통신하며, 이미지와 파일은 Presigned URL을 통해 S3로 직접 업로드합니다.

```txt
React Frontend
  |
  | Axios
  v
Spring Boot Backend API
  |
  | Presigned URL
  v
AWS S3
```

---

## Auth Flow

```txt
1. 사용자가 로그인
2. Access Token 기반으로 API 요청
3. Axios Interceptor가 Authorization Header 추가
4. 401 응답 발생 시 Refresh API 호출
5. 재발급 성공 시 기존 요청 1회 재시도
6. 재발급 실패 시 로그아웃 처리
```

Access Token 만료 시 사용자가 다시 로그인하지 않도록, Axios Interceptor에서 401 응답을 감지하고 Refresh API를 호출하도록 구성했습니다.

---

## File Upload Flow

```txt
1. 사용자가 이미지 또는 파일 선택
2. Frontend가 파일 타입과 크기 검증
3. Backend에 Presigned URL 요청
4. S3에 직접 PUT 업로드
5. 업로드된 URL을 게시글 데이터에 포함
```

파일을 서버로 직접 전송하지 않고 S3에 업로드하도록 구성하여, 서버 부하를 줄이고 업로드 흐름을 단순화했습니다.

---

## Board UI Policy

게시판의 **조회 권한**과 **작성 권한**을 분리하여 UI에 반영했습니다.

| 구분 | 정책 |
| --- | --- |
| 목록 / 상세 조회 | 공개 또는 게시판 정책에 따라 허용 |
| 글쓰기 / 수정 / 삭제 | 로그인 또는 관리자 권한 필요 |
| 비밀글 | 잠금 아이콘 표시 후 비밀번호 검증 |
| 관리자 기능 | 관리자 권한이 있는 사용자에게만 노출 |

예를 들어 활동사진 게시판은 비로그인 사용자도 메뉴와 목록을 볼 수 있도록 하고, 작성 버튼만 권한 조건에 따라 노출합니다.

---

## Technical Decisions

### 카드형 게시글 UI

모바일 환경에서도 게시글 정보를 읽기 쉽게 보여주기 위해 테이블형 UI보다 카드형 피드 UI를 선택했습니다.

### 게시판별 UI 정책 분리

게시판마다 조회 가능 여부, 작성 권한, 댓글 여부가 달라 UI에서도 정책을 분리했습니다.  
이를 통해 메뉴 노출과 작성 버튼 노출을 별도로 제어했습니다.

### Axios Interceptor 기반 인증 처리

Access Token 만료 시 사용자가 매번 다시 로그인하지 않도록 Refresh API를 호출하고, 기존 요청을 한 번 재시도하도록 구성했습니다.

### S3 직접 업로드

이미지와 파일 업로드 시 서버 부하를 줄이기 위해 Presigned URL을 받아 S3로 직접 업로드하도록 구현했습니다.

---

## Troubleshooting

- 비로그인 사용자에게 조회 가능한 게시판 메뉴가 숨겨지는 문제 수정
- Access Token 만료 시 Refresh 요청 후 기존 요청 재시도 처리
- Presigned URL 업로드 시 Content-Type 불일치 문제 대응
- HEIC 이미지의 브라우저 표시 문제를 고려하여 JPEG 변환 방식 검토

---

## Local Development

```bash
pnpm install
pnpm dev
```

Build:

```bash
pnpm build
```

---

## Environment Variables

```env
VITE_API_BASE_URL=http://localhost:8080/api
```

운영 환경에서는 실제 API 주소로 설정합니다.

```env
VITE_API_BASE_URL=https://adpia.or.kr/api
```

---

## Future Improvements

- HEIC 이미지 JPEG 변환 업로드
- 게시글 에디터 UX 개선
- 이미지 lazy loading 적용
- 관리자 통계 기능 추가
- 접근성 개선
