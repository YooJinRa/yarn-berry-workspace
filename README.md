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
