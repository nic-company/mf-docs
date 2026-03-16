---
sidebar_position: 1
displayed_sidebar: "taskDocSidebar"
title: "mf-lib-shared 환경구성"
---

# mf-lib-shared 환경구성
**@nic/mf-lib-shared** 는 Micro Frontend (Next.js) 프로젝트에서 공통으로 사용되는 라이브러리를 제공하는 공유 라이브러리 패키지 입니다.


:::info 공통 라이브러리의 역할
* 공통 라이브러리는 다음과 같은 공유 자산을 모든 마이크로 프론트엔드(Host 및 Remote 앱)에 제공합니다:
  - **UI 컴포넌트**: 디자인 시스템, 공통 UI 요소 (Button, Input, Modal 등)
  - **유틸리티 함수**: 날짜 포맷팅, 데이터 변환, 검증 로직 등
  - **타입 정의**: 공통 인터페이스, 타입, Enum 등
  - **설정 파일**: ESLint, Prettier, TypeScript 공통 설정
  - **상수**: API 엔드포인트, 환경 변수, 설정 값 등
  - **훅(Hooks)**: 커스텀 React 훅
  - **스타일**: 공통 스타일, 테마, CSS 변수

* 마이크로 프론트엔드에서 공통 라이브러리가 필요한 이유
  1. **코드 중복 방지**
    - 여러 마이크로 프론트엔드에서 동일한 코드를 반복 작성하지 않음
    - Single Source of Truth 원칙 구현

  2. **일관성 유지**
    - 모든 앱에서 동일한 UI/UX 제공
    - 디자인 시스템의 중앙 관리
    - 브랜드 아이덴티티 통일

  3. **유지보수성 향상**
    - 한 곳에서 수정하면 모든 앱에 반영
    - 버그 수정 및 개선 사항의 빠른 전파

  4. **개발 생산성 증대**
    - 검증된 컴포넌트/함수 재사용
    - 개발 시간 단축
    - 표준화된 개발 패턴 제공

  5. **타입 안정성**
    - 공통 타입 정의로 타입 불일치 방지
    - TypeScript의 강력한 타입 체킹 활용

* 공통 라이브러리 사용 방식
  - 모노레포 환경에서는 워크스페이스 기능을 통해 공통 라이브러리를 참조합니다:

```json
// Host 또는 Remote 앱의 package.json
{ "dependencies": { "@nic/mf-lib-shared": "file:../mf-lib-shared" } }
// 또는 npm install @nic/mf-lib-shared 과 같이 설치하여 사용.
```

```tsx
// 실제 사용 예시
import { Button, Card, Input, Badge, cn } from '@nic/mf-lib-shared/components';
import { formatDate, validateEmail } from '@nic/mf-lib-shared/utils';
import type { User, ApiResponse } from '@nic/mf-lib-shared/types';

function UserProfile() {
  const user: User = { /* ... */ };
  
  return (
    <Card>
      <h1>{user.name}</h1>
      <p>{formatDate(user.createdAt)}</p>
      <Button>프로필 수정</Button>
    </Card>
  );
}
```
:::


### 1. 패키지 생성
```sh
mkdir mf-lib-shared
cd mf-lib-shared
# -y 모든 설정을 default로 진행
npm init -y
```


### 2. 디렉토리 구조 생성 (기본구조)
```sh
mf-lib-shared/src/
├─components          # 공통 컴포넌트
│  ├─shadcn              # Shadcn UI 컴포넌트
│  │  ├─components
│  │  │  └─ui            # Shadcn UI 컴포넌트 목록
│  │  │     ├─button
│  │  │     └─...             
│  │  └─lib
│  │     └─utils         # shadcn utils
│  │        └─index.ts        
│  ├─providers
│  └─...
├─config              # 공통 설정
│  ├─eslint
│  └─prettier
├─design-system       # 공통 디자인 시스템
├─hooks               # 공통 훅
├─types               # 공통 타입
└─utils               # 공통 유틸리티 함수
```
:::info 설명
* 상황에 따라 필요한 컴포넌트, 유틸리티 함수, 타입 정의, 설정 파일 등을 제공하기 위한 디렉토리 구조를 생성하면 됩니다.
:::


### 3. package.json 설정 (구조 수정 필요, 추후 수정 예정)
```json
{
  "name": "@nic/mf-lib-shared",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0"
  }
}
```
:::info 설명
**주요 필드:**
* `name`: 패키지의 기본 엔트리 포인트 (레거시 방식, 단일 엔트리)
* `types`: TypeScript 타입 정의 파일의 위치
* `exports`: Node.js 12+에서 도입된 최신 엔트리 포인트 정의 방식

**exports 필드 구조**:
```json
"exports": {
  ".": {
    "types": "./dist/index.d.ts",  // TypeScript 타입 정의(types를 맨 위에 위치하는 것이 관례)
    "default": "./dist/index.js"    // 실제 JavaScript 모듈
  },
  "./components": {
    "types": "./dist/components/index.d.ts",
    "default": "./dist/components/index.js"
  },
  "./utils": {
    "types": "./dist/utils/index.d.ts",
    "default": "./dist/utils/index.js"
  },
  "./types": {
    "types": "./dist/types/index.d.ts",
    "default": "./dist/types/index.js"
  },
  "./config/eslint": "./dist/config/eslint/index.js",
  "./config/eslint/base": "./dist/config/eslint/base.js",
  "./config/eslint/react": "./dist/config/eslint/react.js",
  "./config/prettier": "./dist/config/prettier/index.js",
  "./styles": "./lib/styles/index.css", // dist 대신 lib 사용(workspace 패키지이므로 소스 직접 참조)
  "./styles/tokens": "./lib/styles/tokens.css", // dist 대신 lib 사용(workspace 패키지이므로 소스 직접 참조)
  "./styles/base": "./lib/styles/base.css" // dist 대신 lib 사용(workspace 패키지이므로 소스 직접 참조)
}
```

**중요**: 
- `exports`는 **빌드된 파일** (`./dist/`)을 참조해야 합니다
- 소스 파일(`./lib/`)이 아닌 컴파일된 결과물을 참조
- `types`와 `default`를 함께 지정하여 타입과 런타임 코드를 명확히 구분
- `default`는 모든 모듈 시스템(CommonJS, ESM)에서 사용 가능한 fallback 옵션

**장점**: 
- **명시적인 API 관리**: exports에 정의하지 않은 파일은 외부에서 접근 불가
- **서브패스 Export**: 각 기능별로 독립적인 import 경로 제공
- **Tree-shaking 최적화**: 필요한 모듈만 import하여 번들 크기 감소
- **타입 안정성**: types 필드로 TypeScript 지원 강화
- **개발 경험 향상**: IDE 자동완성, 명확한 import 경로

**실제 사용 예시**:
```tsx
// 각 서브패스로 필요한 것만 import
import { Button, Card } from '@rm/monorepo-mf-shared-library/components';
import { formatDate } from '@rm/monorepo-mf-shared-library/utils';
import type { User } from '@rm/monorepo-mf-shared-library/types';

// config 파일도 export 경로를 통해 사용
import eslintConfig from '@rm/monorepo-mf-shared-library/config/eslint';
import prettierConfig from '@rm/monorepo-mf-shared-library/config/prettier';
```

**config 파일도 빌드 대상**:
- ESLint, Prettier 설정 파일도 TypeScript로 작성되므로 `./dist/config/`에서 빌드된 파일을 참조
- `files: ["dist"]` 설정으로 배포 시 dist 폴더만 포함되도록 관리
  - ESLint, Prettier 설정 파일을 처음부터 JS 파일로 만들어 두면, 빌드 과정 없이도 `files` 설정을 통해 해당 JS 파일을 바로 배포할 수 있습니다. 그러나 이 방법은 권장되지 않습니다.

**styles 파일은 dist 대신 lib 사용(workspace 패키지이므로 소스 직접 참조)**:
```json
"styles": "./lib/styles/index.css",
"styles/tokens": "./lib/styles/tokens.css",
"styles/base": "./lib/styles/base.css"
```
:::




### 4. tsconfig.json 설정
```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./lib",
    "declaration": true,
    "declarationMap": true,
    "noEmit": false,
    "allowImportingTsExtensions": false
  },
  "include": ["lib/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

:::info 설명
**주요 설정 옵션**:

* **`extends`**: 루트의 `tsconfig.base.json`을 상속하여 공통 설정을 재사용
  - 모노레포 전체의 기본 TypeScript 설정을 일관되게 유지

* **`outDir`**: 컴파일된 JavaScript 파일과 타입 정의 파일이 생성될 디렉토리
  - `./dist`로 설정하여 빌드 결과물을 별도 폴더에 분리

* **`rootDir`**: 컴파일할 소스 파일의 루트 디렉토리
  - `./lib`로 설정하여 소스 코드의 시작점 명시
  - outDir의 디렉토리 구조가 rootDir과 동일하게 생성됨

* **`declaration`**: TypeScript 타입 정의 파일(`.d.ts`) 자동 생성
  - `true`로 설정하여 각 `.ts` 파일에 대응하는 `.d.ts` 파일 생성
  - 다른 패키지에서 이 라이브러리를 사용할 때 타입 지원 제공

* **`declarationMap`**: 타입 정의 파일의 소스맵(`.d.ts.map`) 생성
  - `true`로 설정하여 IDE에서 타입 정의로 이동 시 원본 TypeScript 파일로 이동 가능
  - 디버깅 및 개발 경험 향상

* **`noEmit`**: 컴파일 결과물 생성 여부 제어
  - `false`로 설정하여 실제로 `.js`와 `.d.ts` 파일을 생성
  - (Vite 등 일부 번들러는 타입 체크만 하고 빌드는 하지 않기 위해 `true`를 사용하기도 함)

* **`allowImportingTsExtensions`**: `.ts` 확장자를 명시한 import 허용 여부
  - `false`로 설정하여 표준 JavaScript 호환 import 방식 사용 (`import from './module'` 형태)

* **`include`**: 컴파일 대상 파일 패턴
  - `lib/**/*`로 설정하여 lib 폴더 하위의 모든 파일 포함

* **`exclude`**: 컴파일에서 제외할 파일/폴더
  - `node_modules`, `dist` 폴더 제외하여 불필요한 컴파일 방지

**빌드 결과**:
```
@nic/mf-lib-shared/
├── lib/                      # 소스 코드 (rootDir)
│   ├── components/
│   │   └── button/
│   │       └── Button.tsx
│   └── utils/
│       └── format.ts
└── dist/                     # 빌드 결과물 (outDir)
    ├── components/
    │   └── button/
    │       ├── Button.js          # 컴파일된 JavaScript
    │       ├── Button.d.ts        # 타입 정의 파일
    │       └── Button.d.ts.map    # 소스맵
    └── utils/
        ├── format.js
        ├── format.d.ts
        └── format.d.ts.map
```
:::

:::info `dts` 타입 파일 관련(<span class="admonition-title">*.d.ts</span> 타입파일 생성)
* **TypeScript 컴파일러의 기본 기능으로 생성**
  - 현재 프로젝트에서는 별도의 dts 플러그인 없이 TypeScript 컴파일러(tsc)의 기본 기능만으로 타입 정의 파일이 생성되고 있습니다.
* **핵심 설정**:
```json
// tsconfig.json
"declaration": true,
"declarationMap": true,
```
  - `tsconfig.json`의 `"declaration": true` 옵션이 핵심입니다. 이 옵션이 활성화되면 TypeScript 컴파일러가 `.js` 파일과 함께 `.d.ts` 타입 정의 파일을 자동으로 생성합니다.
* **빌드 과정**:
  - 루트에서 `pnpm run build:shared-library` 명령어를 실행하면, 스크립트가 동작합니다.
  - `pnpm --filter @rm/monorepo-mf-shared-library build` 실행.
  - 공유 라이브러리 shared library 의 build 스크립트가 동작합니다.
  - shared library 의 build 스크립트는 `package.json` 파일에 정의되어 있습니다.
    ```json
    "scripts": {
      "build": "tsc",
    }
    ```
  - 결과적으로 tsc 명령어가 실행됩니다.
  - tsc가 tsconfig.json 설정을 읽고:
    - lib/**/* 폴더의 TypeScript 파일들을 컴파일
    - dist 폴더에 .js 파일 생성
    - declaration: true 옵션으로 .d.ts 타입 정의 파일 자동 생성
    - declarationMap: true 옵션으로 .d.ts.map 파일도 생성
  - 실행 흐름:
    - 루트 build:shared-library → pnpm --filter로 패키지 선택 → shared-library의 build 스크립트 실행 → tsc 실행 → JS + .d.ts 파일 생성

결국 사용자가 직접 tsc를 입력하지 않았지만, pnpm의 필터 기능을 통해 자동으로 해당 패키지의 tsc 명령어가 실행된 것입니다.

:::







## ESLint, Prettier 설정 공유 관련
---
* 모든 리모트 애플리케이션에서 공통으로 사용하기 위하여 **공유 라이브러리 패키지에** **ESLint와 Prettier 설정** 파일을 만들고, 처음부터 `*.js` 파일로 생성하여 빌드 과정을 거치지 않고 바로 사용할 수 있도록 합니다.
  - `package.json` 파일의 **ESLint, Prettier** 부분
  ```json
  {
    "exports": {
      //...
      // highlight-start
      "./config/eslint": "./dist/config/eslint/index.js",
      "./config/eslint/base": "./dist/config/eslint/base.js",
      "./config/eslint/react": "./dist/config/eslint/react.js",
      "./config/prettier": "./dist/config/prettier/index.js"
      // highlight-end
    },
  }
  ```
* 각 리포트 앱에서 사용할 때는 다음과 같이 사용합니다.
  ```tsx
  // eslint.config.ts
  import { react } from '@rm/monorepo-mf-shared-library/config/eslint';

  export default defineConfig([
    globalIgnores(['dist']),
    ...react,
    // 필요시 이 앱에만 적용할 추가 규칙을 여기에 작성
    // {
    //   rules: {
    //     // 앱별 커스텀 규칙
    //   }
    // }
  ]);
  ```
  ```tsx
  // prettier.config.mjs
  import sharedConfig from '@rm/monorepo-mf-shared-library/config/prettier';

  /**
  * Prettier 설정
  * 공통 라이브러리의 Prettier 설정을 가져와 사용
  * 
  * @type {import('prettier').Config}
  */
  export default {
    ...sharedConfig,
    // 필요시 이 앱에만 적용할 추가 설정
    // printWidth: 100,
  };
  ```







## Shadcn/ui 공유 라이브러리에 적용
---
* 각각의 리모트 앱에서 사용할 UI 컴포넌트를 공유 라이브러리(@rm/monorepo-mf-shared-library)에서 가져와 사용할 수 있게합니다. 이는 각각의 리모트 앱에서 Shadcn/ui 컴포넌트를 설치하는 것보다 훨씬 효율적입니다.


### 1. Shadcn/ui 사용을 위한 의존성 설치
* 공유 라이브러리(@rm/monorepo-mf-shared-library)에 Shadcn/ui 컴포넌트를 적용하기 위하여 다음과 같이 몇가지 설치를 진행합니다.
```sh
# 공유 라이브러리 루트에서
pnpm add class-variance-authority clsx tailwind-merge lucide-react
pnpm add -D tailwindcss @base-ui/react
```
* 이 4개는 런타임에 필요한 의존성입니다.

| 패키지 | 역할 |
| :--- | :--- |
| class-variance-authority (cva) | 컴포넌트 variant (variant, size 등) 타입 안전하게 정의 |
| clsx | 조건부 className 병합 유틸 |
| tailwind-merge | Tailwind 클래스 충돌 해결 (px-2 px-4 → px-4) |
| lucide-react | 아이콘 컴포넌트 라이브러리 |


* TailwindCSS를 설치하기 위하여 다음과 같이 설치를 진행합니다. 빌드 시 필요합니다.

| 패키지 | 역할 |
| :--- | :--- |
| tailwindcss | 공유 라이브러리의 styles/tokens.css, styles/base.css 등을 **빌드할 때** 필요 |
| @base-ui/react | Headless UI 프리미티브 — Button, Select 등 접근성(a11y) 기반 컴포넌트 |




### 2. cn 유틸리티 생성
* `monorepo-mf-shared-library/lib/components/shadcn/lib/utils/index.ts` 파일을 생성합니다.
```ts
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```


### 3. tsconfig.json 경로설정
* shadcn이 생성하는 컴포넌트의 경로 패턴을 해석하기 위하여 다음과 같이 설정합니다.
```json
{
  "compilerOptions": {
    // ...
    "baseUrl": ".",
    "paths": {
      "@/lib/*": ["./lib/components/shadcn/lib/*"],
      "@/components/*": ["./lib/components/shadcn/components/*"],
      "@/hooks/*": ["./lib/components/shadcn/hooks/*"]
    }
    // ...
  }
}
```
* baseUrl: "." 은 paths가 모노레포 루트가 아닌 패키지 자신의 폴더를 기준으로 경로를 해석하도록 앵커를 재설정하기 위해 필요합니다. 모노레포의 tsconfig.base.json에도 baseUrl이 설정되어있으므로 baseUrl을 설정하지 않으면 전체 모노레포 루트를 시작 경로로 인식하게 됩니다.



### 4. components.json 추가(shadcn CLI용)
```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "base-vega",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "",
    "css": "lib/styles/index.css", // 공유라이브러리 styles 위치치
    "baseColor": "neutral",
    "cssVariables": true,
    "prefix": ""
  },
  "iconLibrary": "lucide",
  "rtl": false,
  "aliases": {
    "components": "@/components", // 공유라이브러리 tsconfig.json 경로설정에 따라 설정
    "utils": "@/lib/utils", // 공유라이브러리 tsconfig.json 경로설정에 따라 설정
    "ui": "@/components/ui", // 공유라이브러리 tsconfig.json 경로설정에 따라 설정
    "lib": "@/lib", // 공유라이브러리 tsconfig.json 경로설정에 따라 설정
    "hooks": "@/hooks" // 공유라이브러리 tsconfig.json 경로설정에 따라 설정
  }
}
```
* 추 후 공유 라이브러리에 **shadcn/ui** 컴포넌트를 설치하려면 공유 라이브러리 루트에서 다음 명령어를 실행하면 됩니다.
  ```sh
  cd packages/monorepo-mf-shared-library
  pnpm dlx shadcn@latest add button card input
  ```
  * `pnpm dlx`는 `npx`와 같은 역할을 하는 명령어입니다.



### 5. 기존 설치된 shadcn/ui 컴포넌트 이동
* 만약 기존에 이미 설치된 **shadcn/ui** 컴포넌트가 있다면, 공유 라이브러리 `lib/components/shadcn/components/ui` 폴더로 이동시킵니다.
* 이동시킨 후 모든 UI 컴포넌트를 export 하는 `index.ts` 파일을 `lib/components/shadcn/components/ui` 폴더 내부에 생성하고 다음과 같이 입력합니다.
  ```ts
  export * from "./alert-dialog"
  export * from "./badge"
  export * from "./button"
  export * from "./card"
  export * from "./combobox"
  export * from "./dropdown-menu"
  export * from "./field"
  export * from "./input"
  export * from "./input-group"
  export * from "./label"
  export * from "./select"
  export * from "./separator"
  export * from "./textarea"
  ```

* 또한 `lib/components/index.ts` 파일에 다음과 같이 shadcn/ui 컴포넌트를 export 하는 코드를 추가합니다.
  ```ts
  // shadcn components export
  export * from "./shadcn/components/ui/index";
  export * from "./shadcn/lib/utils/index";
  ```



### 6. Tailwind 스캔 경로 설정
* **Tailwind v4의 클래스 스캔 방식**
  - Tailwind CSS v4 (@tailwindcss/vite)는 @import 'tailwindcss'가 선언된 CSS 파일을 기준으로, 해당 Vite 프로젝트의 소스 파일들을 자동으로 스캔합니다. 기본적으로 스캔 범위는 해당 앱의 프로젝트 루트 이하입니다.
  - 따라서 shadcn/ui 컴포넌트는 공유 라이브러리에 위치해 있으므로, 사용하는 각각의 리모트 앱에서는 shadcn 컴포넌트가 사용하는 Tailwind class를 스캔하지 못합니다.
* 그래서 각 리모트 앱에서는 공유 라이브러리의 Tailwind 스캔 경로를 다음과 같이 설정해줘야 합니다.
  ```css
  @source "../../../../packages/monorepo-mf-shared-library/lib";
  ```
  - 하지만 이렇게 하면 모든 리모트 앱에 넣어줘야하는 번거로움이 있습니다. 따라서 이미 각 리모트 앱에서 제공하고 있는 **bridge.tsx** 파일내부 코드의 공유 라이브러리 `micro-frontend.css` 파일을 통해서 제공하도록 합니다.
    - 공유 라이브러리의 `lib/styles/micro-frontend.css` 파일의 내부 코드에서 아래쪽 부분에 다음 코드를 추가합니다.
    ```css
    /* 공유 라이브러리 UI 컴포넌트(shadcn) Tailwind 스캔 */
    /* 각 리모트앱에서 shadcn 컴포넌트를 사용할 때 사용하는 shadcn class을 읽지 못하므로 다음 경로에서 스캔하도록 설정정 */
    @source "../components";
    ```
    - 이렇게 하면 각 리모트 앱에서는 공유 라이브러리의 Tailwind 스캔 경로를 설정하지 않아도 되고 **bridge.tsx** 파일을 통해 적용됩니다.


### 7. 각 앱에서 UI 컴포넌트 사용하기
* 각 앱에서 다음과 같이 shadcn/ui 컴포넌트를 사용할 수 있습니다.
  ```tsx
  import { Button } from "@rm/monorepo-mf-shared-library/components/ui"
  ```
  

### 8. tsc-alias를 사용하여 빌드 후 경로 별칭을 자동으로 상대 경로로 변환
* 공유 라이브러리의 빌드 결과물 dist/ 안의 JS 파일에 alias로 설정된 경로(예: @/lib/utils)가 그대로 남아있고, 호스트 앱, 리모트 앱의 Vite가 해당 경로를 찾지 못하는 문제가 있을 수 있습니다. 이를 해결하기 위하여 tsc-alias를 사용하여 빌드 후 경로 별칭을 자동으로 상대 경로로 변환합니다.
  ```sh
  # 공유 라이브러리 루트에서 설치
  pnpm add -D tsc-alias
  ```
  - tsc-alias는 TypeScript 컴파일러의 경로 별칭을 자동으로 상대 경로로 변환해주는 플러그인입니다.
  - 이를 사용하면 공유 라이브러리의 빌드 결과물 dist/ 안의 JS 파일에 alias로 설정된 경로(예: @/lib/utils)가 그대로 남아있고, 호스트 앱, 리모트 앱의 Vite가 해당 경로를 찾지 못하는 문제가 해결됩니다.
  - 이를 사용하기 위해서는 공유 라이브러리의 `package.json` 파일에 다음과 같이 추가합니다.
  ```json
  "scripts": {
		// ...
		"build": "tsc && tsc-alias",
		// ...
	},
  ```
  * 이제 공유 라이브러리를 다시 빌드하고 각 앱에서 UI 컴포넌트를 사용할 때 문제없이 사용할 수 있습니다.






