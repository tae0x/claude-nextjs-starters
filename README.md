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

## 폴더 구조와 컴포넌트 계층

폴더가 곧 계층입니다. 의존 방향은 아래에서 위로만 갑니다(상위 계층만 하위 계층을 import).

```
L0 Foundation   app/globals.css, lib/utils.ts, config/site.ts, stores/
L1 Primitives   components/ui/          shadcn 원본 (CLI로만 추가)
L2 Common       components/common/      ui를 조합한 재사용 블록 (로고, 테마 토글, 브레드크럼 등)
L3 Layout       components/layout/      헤더, 푸터, 사이드바 등 영역 단위 조립체
L4 Providers    components/providers/   ThemeProvider
L5 Templates    app/**/layout.tsx       라우트 그룹별 레이아웃
L6 Pages        app/**/page.tsx         페이지
```

```
app/
├─ layout.tsx                  루트 레이아웃 (폰트, 테마, 토스트)
├─ loading.tsx / error.tsx / global-error.tsx / not-found.tsx
├─ (marketing)/                헤더 + 푸터 레이아웃
│  ├─ page.tsx                 랜딩 페이지 (/)
│  ├─ login/                   로그인 폼 예제 (/login)
│  ├─ docs/                    문서 (/docs/*) - 좌측 내비게이션 레이아웃
│  └─ examples/                예제 (/examples/*)
└─ (app-shell)/examples/sidebar-layout/
                               사이드바 앱 셸 예제 (/examples/sidebar-layout)
```

## 문서와 예제

개발 서버를 실행하면 아래 경로에서 사용법과 동작하는 예제를 볼 수 있습니다.

| 경로 | 내용 |
| --- | --- |
| `/docs/getting-started` | 시작하기 (설치, 스크립트, 환경 변수) |
| `/docs/project-structure` | 프로젝트 구조와 코드 규칙 |
| `/docs/components` | 설치된 UI 컴포넌트와 Base UI 사용법 |
| `/docs/hooks` | 훅 라이브러리 (usehooks-ts) |
| `/docs/forms` | 폼 & 검증 |
| `/docs/state` | 상태 관리 (zustand) |
| `/docs/theming` | 테마와 디자인 토큰 |
| `/examples/components` | 컴포넌트 쇼케이스 |
| `/examples/layouts` | 레이아웃 예제 |
| `/examples/forms` | 폼 예제 (React Hook Form + Zod) |
| `/examples/hooks` | 훅 예제 |
| `/examples/sidebar-layout` | 사이드바 앱 셸 (사이드바 + 상단바 + 전역 상태) |

문서·예제가 필요 없다면 `app/(marketing)/docs`, `app/(marketing)/examples`, `app/(app-shell)`, `components/docs`, `components/examples`를 삭제하고 `config/site.ts`의 `mainNav`를 정리하세요.

### 규칙

- 서버 컴포넌트가 기본이며, 상호작용이 필요한 말단 컴포넌트에만 `"use client"`를 씁니다.
- 사이트 이름, 네비게이션, 브레드크럼 라벨은 `config/site.ts` 한 곳에서 관리합니다.
- 경로 별칭은 `@/`(프로젝트 루트)입니다.
- `components/ui`는 shadcn CLI로 추가한 원본이므로 직접 수정을 최소화합니다.
- 클래스 병합은 `cn`(`import { cn } from "cn"`)을 사용합니다.

## shadcn/ui 컴포넌트

이미 설치된 컴포넌트:

- **레이아웃**: separator, sheet, dropdown-menu, tooltip, skeleton, avatar, badge, card, breadcrumb, navigation-menu, sidebar, sonner
- **폼**: input, label, textarea, select, checkbox, radio-group, switch, field
- **일반 UI**: dialog, alert-dialog, alert, popover, tabs, accordion, table, progress, spinner, empty

추가 설치(예: `calendar`, `chart`, `command`, `pagination`, `carousel`, `combobox`, `drawer`, `resizable`):

```bash
npx shadcn@latest add calendar chart
```

> 이 프로젝트는 Radix가 아닌 **Base UI** 기반입니다. Radix 예제의 `asChild` 대신 `render` prop을 사용합니다.
>
> ```tsx
> <Button render={<Link href="/docs" />} nativeButton={false}>문서</Button>
> ```

## 주요 기능 사용법

### 다크모드

`ThemeProvider`가 루트 레이아웃에 적용되어 있고, `ThemeToggle`로 라이트/다크/시스템을 전환합니다. 컴포넌트에서 직접 쓸 때는 `next-themes`의 `useTheme()`을 사용합니다.

### 토스트

```tsx
import { toast } from "sonner";

toast.success("저장되었습니다.");
```

### 폼 검증

`app/(marketing)/login/login-form.tsx`에 react-hook-form + zod + shadcn `Field` 조합 예제가 있습니다.

### 전역 상태 (zustand)

`stores/notification-store.ts`가 예제입니다. 사이드바 앱 셸 예제에서 상단바의 알림 배지와 "알림 추가" 버튼이 같은 스토어를 공유합니다. 스토어는 **클라이언트 컴포넌트에서만** 사용하세요.

### 범용 훅 (usehooks-ts)

`useLocalStorage`, `useMediaQuery`, `useDebounceValue` 등을 제공합니다. SSR 환경에서는 hydration 불일치를 막기 위해 `initializeWithValue: false` 옵션을 사용합니다 (`app/(app-shell)/examples/sidebar-layout/settings/settings-form.tsx` 참고).

## 환경 변수

| 변수 | 설명 |
| --- | --- |
| `NEXT_PUBLIC_SITE_URL` | 사이트 URL (metadata 절대 경로 기준) |
