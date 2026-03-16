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






