# Next.js 스타터킷

Next.js 16 App Router 기반의 모던 웹 스타터킷입니다. 레이아웃, 다크모드, 폼 검증, 전역 상태까지 갖추고 있어 바로 기능 개발을 시작할 수 있습니다.

## 기술 스택

| 구분 | 사용 기술 |
| --- | --- |
| 프레임워크 | Next.js 16 (App Router), React 19 |
| 언어 | TypeScript (strict) |
| 스타일 | Tailwind CSS 4, shadcn/ui (`base-nova`, Base UI 기반) |
| 아이콘 | lucide-react |
| 다크모드 | next-themes |
| 폼 / 검증 | react-hook-form, zod, @hookform/resolvers |
| 전역 상태 | zustand |
| 범용 훅 | usehooks-ts |
| 토스트 | sonner |

## 시작하기

```bash
npm install
cp .env.example .env.local   # 필요 시 환경 변수 수정
npm run dev
```

http://localhost:3000 에서 확인할 수 있습니다.

| 스크립트 | 설명 |
| --- | --- |
| `npm run dev` | 개발 서버 실행 |
| `npm run build` | 프로덕션 빌드 |
| `npm run start` | 프로덕션 서버 실행 |
| `npm run lint` | ESLint 검사 |

## 문서

상세 내용은 `docs/` 폴더에 있습니다. 개발 서버에서는 `/docs`, `/examples` 경로에서도 볼 수 있습니다.

| 문서 | 내용 |
| --- | --- |
| [docs/architecture.md](docs/architecture.md) | 폴더 구조(`src/`), 컴포넌트 계층, 코드 규칙, 문서·예제 삭제 방법 |
| [docs/guide.md](docs/guide.md) | shadcn 컴포넌트, 다크모드·토스트·폼·전역 상태·훅 사용법, 환경 변수 |
