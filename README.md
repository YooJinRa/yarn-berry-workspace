# Yarn berry, Monorepo 연습

### project별 node 버전 관리하기

- nvm 설치하기
- vscode extension `vscode nvm integration`
- project root 폴더에 `.nvmrc` 파일 생성하기
- terminal에서 `nvm list` 명령어 검색하여 원하는 node version 확인하기
- `.nvmrc` 파일에 원하는 node version 작성하기
  - `lts/gallium` 또는 `v16.18.1` 두 가지 방식으로 버전 작성 가능함
- vscode 재 실행시킨 후 터미널에 아래와 같이 메시지 잘 나오는 지 확인하기

```
nvm use
Found '... /yarn-berry-workspace/.nvmrc' with version <lts/gallium>
Now using node v16.18.1 (npm v8.19.2)
```

### nvm을 설치하는 이유

- project 별로 node 버전을 관리하여 사용가능
- 이때 vscode extension 중 `vscode nvm integration` 과 함께 사용하면 더 수월
  - `vscode nvm integration` 통해 자동으로 맞는 node version으로 switch 해줌
- 만약 nvm list에 원하는 노드 버전이 없다고 하면 다음과 같은 명령어를 통해 원하는 버전 설치 가능
  - `nvm install lts/gallium`

### yarn berry typescript 오류 해결하기

- `yarn add -D typescript`
- `yarn dlx @yarnpkg/sdks vscode` -> 작업영역에 대한 typescript 버전 허용 환경 설정이 vscode 우측 하단에 표출
- 허용 버튼을 클릭하여 허용함

### Zero Install

- https://yarnpkg.com/features/zero-installs

### .vscode/extensions.json 파일

- 상기 파일에 아래와 같이 extension 아이디 값을 넣으면, 초기 유저가 해당 extension을 다운 받을 수 있도록 vscode가 안내해줌

```
{
  "recommendations": [
    "arcanis.vscode-zipfs"
  ]
}
```

### 공통으로 사용하는 파일 관리

- `/packages` 폴더에 관리

```
// lib 폴더 이동
cd packages/lib

// package.json 파일 생성
yarn init

// typescript 설치
yarn add typescript
```

- `packages/lib/package.json` 파일 수정

```
{
  "name": "@project/lib", // @이름으로 변경
  "version": "1.0.0",
  "private": true,
  "main": "./src/index.ts", // main으로 참조할 파일 추가
  "dependencies": {
    "typescript": "^5.0.4"
  }
}
```

- `packages/lib/tsconfig.json` 파일 생성

```
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "newLine": "lf",
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true,
    "paths": {}
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```

- packages/lib/src/index.ts 파일 생성후, 코드 입력

### 공통으로 사용하는 코드를 프로젝트에서 사용할 수 있도록 의존성 주입

- 루트에서 다음 명령어를 실행
  - `yarn workspace @project/web add @project/lib`

---

### Monorepo란?

- 여러 개의 개별 프로젝트를, 잘 정의된 관계를 통해서 하나의 Repo에 담은 것
- https://news.hada.io/topic?id=6061

### Monorepo 장단점

#### 장점

- 내가 담당하는 프로젝트가 아니라도 개선의 여지가 있다면 자유롭게 수정 가능
- 다른 사람의 코드에 자주 기웃거릴수 있음
- 내가 알지 못했던 Official Document API를 통해서 문제를 더 깔끔하게 해결 가능.
- 동료들간의 상호작용으로 기술적 비지니스적 성장 할 기회가 많이 열림

#### 단점

- 개발환경을 구성하는데 투자필요
- 코드 관리의 어려움 (코드 소유권 어떻게?)
  - github에서 코드 소유자 설정 가능 : https://docs.github.com/ko/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners
- 대규모 리팩토링이 쉬워지는게 장점이자, 단점

```
구글의 경우 매일 모노리포에 4만 5,000건 이상의 변경이 이뤄진다. 애플리케이션의 개발자 수가 증가하고 애플리케이션 내 구성 요소의 수가 확대됨에 따라 오버헤드에서 기하급수적인 문제가 된다.

원문보기:
https://www.itworld.co.kr/insight/214234#csidx6c5414e0caa383f937b71490000fcac
```

- 생각해보기 : https://news.hada.io/topic?id=7839

### Monorepo를 개발하는 경우

1. Library 개발

- 예시 : https://github.com/babel/babel

2. 디자인시스템 관리

- 예시 : https://github.com/radix-ui/primitives

3. B2C, B2B, admin을 하나로 관리

4. Monolith -> Microservice

- 참고자료 : (토스) https://speakerdeck.com/raon0211/toseuyi-maikeuropeuronteuendeu-akitegceo-geurigo-jadonghwa
