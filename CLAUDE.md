# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 명령어

```bash
npm run dev      # 개발 서버 (http://localhost:3000)
npm run build    # 프로덕션 빌드 (cacheComponents prerender 오류는 빌드에서도 드러남)
npm run lint     # ESLint (eslint-config-next core-web-vitals + typescript)
npx tsc --noEmit # 타입 검사
npx shadcn@latest add <name>  # shadcn 컴포넌트 추가
```

테스트 러너는 설정되어 있지 않다. 동작 검증은 개발 서버 + Playwright MCP(`.mcp.json`에 설정됨)로 한다.

## Next.js 버전 주의

Next.js 16.4 / React 19.3이며 학습 데이터와 다른 API가 있다. 코드를 쓰기 전에 `node_modules/next/dist/docs/`의 해당 가이드를 읽는다 (`AGENTS.md` 지침). 예를 들어 에러 바운더리의 복구 prop은 `reset`이 아니라 `retry`다 (`app/error.tsx`).

`next.config.ts`에서 `cacheComponents: true`와 `partialPrefetching: true`가 켜져 있다. 동적 API(`cookies`, `headers`, `Date.now` 등)를 쓰면 Suspense 경계나 `use cache` 처리가 필요하다.

## 아키텍처

### 폴더가 곧 계층 (README에도 정의됨)

의존 방향은 아래에서 위로만 간다. 상위 계층만 하위 계층을 import 한다.

`config/site.ts`, `stores/`, `lib/utils.ts` → `components/ui` → `components/common` → `components/layout` → `components/providers` → `app/**/layout.tsx` → `app/**/page.tsx`

### 라우트 그룹 2개

- `app/(marketing)/` — `SiteHeader` + `SiteFooter` 레이아웃. 랜딩, `/login`, `/docs/*`, `/examples/*`가 여기에 속한다. `docs/layout.tsx`는 좌측 내비게이션과 이전/다음 링크가 추가된 중첩 레이아웃이다.
- `app/(app-shell)/examples/sidebar-layout/` — `SidebarProvider` + `AppSidebar` + `ShellHeader` 앱형 레이아웃. 마케팅 레이아웃과 별개로 동작한다.

### `config/site.ts`가 네비게이션의 단일 출처

`mainNav`, `docsNav`, `examplesNav`, `shellNav`, `routeLabels`가 모두 여기에 있다. 페이지를 추가하거나 지우면 이 파일을 함께 수정해야 한다.
- `docsNav`의 순서가 곧 `DocPager`의 이전/다음 문서 순서다 (`docsPages`로 평탄화됨).
- `Breadcrumbs`는 경로 세그먼트를 `routeLabels`로 변환한다. 라벨이 없으면 세그먼트 원문이 노출된다.

### shadcn/ui는 Base UI 기반 (`base-nova`)

Radix가 아니므로 `asChild`가 없다. 대신 `render` prop을 쓴다. 링크 버튼은 `<Button render={<Link href="..." />} nativeButton={false}>` 형태다. `components/ui`는 CLI로 추가한 원본이므로 직접 수정을 최소화한다.

### 클래스 병합과 경로

- `cn`은 `@/lib/utils`가 아니라 `cn` 패키지에서 import 한다: `import { cn } from "cn"`.
- 경로 별칭은 `@/*` (프로젝트 루트).

### 클라이언트/서버 경계

- 서버 컴포넌트가 기본이며 `"use client"`는 말단에만 쓴다. 현재 경로가 필요한 컴포넌트(`MainNav`, `DocsNav`, `DocPager`, `Breadcrumbs`, `AppSidebar`)는 `usePathname`을 쓰는 클라이언트 컴포넌트다.
- zustand 스토어(`stores/`)는 클라이언트 컴포넌트에서만 import 한다.
- `usehooks-ts`의 `useLocalStorage`, `useMediaQuery` 등은 hydration 불일치를 막기 위해 `initializeWithValue: false`를 쓴다 (`hooks/use-mobile.ts`, `settings-form.tsx` 참고).
- `ThemeProvider`(next-themes, class 방식)는 `app/layout.tsx`에 있고 `<html>`에 `suppressHydrationWarning`이 필요하다. 다크 변형은 `globals.css`의 `@custom-variant dark`와 연결된다.

## 환경 변수

`NEXT_PUBLIC_SITE_URL`만 사용한다 (`config/site.ts`의 `siteConfig.url`, `metadataBase`의 기준). 없으면 `http://localhost:3000`이다. 예시는 `.env.example`에 있다.

## Git

`.playwright-mcp/`(Playwright MCP 산출물)는 gitignore 대상이다.
