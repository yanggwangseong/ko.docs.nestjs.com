### 소개

Nest(NestJS)는 효율적이고 확장 가능한 [Node.js](https://nodejs.org/) 서버 사이드 애플리케이션을 구축하기 위한 프레임워크입니다. 진보적인 JavaScript를 사용하며, [TypeScript](http://www.typescriptlang.org/)로 구축되어 이를 완벽하게 지원합니다(그러면서도 개발자가 순수 JavaScript로 코딩할 수 있게 합니다). 또한 OOP(객체 지향 프로그래밍), FP(함수형 프로그래밍), FRP(함수형 반응형 프로그래밍)의 요소들을 결합합니다.

내부적으로 Nest는 [Express](https://expressjs.com/)(기본값)와 같은 강력한 HTTP 서버 프레임워크를 사용하며, 선택적으로 [Fastify](https://github.com/fastify/fastify)를 사용하도록 구성할 수도 있습니다!

Nest는 이러한 일반적인 Node.js 프레임워크(Express/Fastify) 위에 추상화 계층을 제공하지만, 동시에 개발자에게 이들의 API를 직접 노출시킵니다. 이를 통해 개발자는 기본 플랫폼에서 사용 가능한 수많은 서드파티 모듈을 자유롭게 사용할 수 있습니다.

#### 철학

최근 몇 년 동안 Node.js 덕분에 JavaScript는 프론트엔드와 백엔드 애플리케이션 모두에서 웹의 "공용어"가 되었습니다. 이로 인해 [Angular](https://angular.dev/), [React](https://github.com/facebook/react), [Vue](https://github.com/vuejs/vue)와 같은 훌륭한 프로젝트들이 탄생했으며, 이들은 개발자의 생산성을 향상시키고 빠르고, 테스트 가능하며, 확장 가능한 프론트엔드 애플리케이션을 만들 수 있게 해줍니다. 그러나 Node(와 서버 사이드 JavaScript)를 위한 훌륭한 라이브러리, 헬퍼, 도구들이 많이 존재함에도 불구하고, 이들 중 어느 것도 주요 문제인 **아키텍처**를 효과적으로 해결하지 못했습니다.

Nest는 개발자와 팀이 고도로 테스트 가능하고, 확장 가능하며, 느슨하게 결합되고, 쉽게 유지보수할 수 있는 애플리케이션을 만들 수 있게 해주는 즉시 사용 가능한 애플리케이션 아키텍처를 제공합니다. 이 아키텍처는 Angular에서 크게 영감을 받았습니다.

#### 설치

시작하려면 [Nest CLI](/cli/overview)로 프로젝트를 스캐폴딩하거나 스타터 프로젝트를 클론할 수 있습니다(둘 다 동일한 결과를 만들어냅니다).

Nest CLI로 프로젝트를 구축하려면 다음 명령을 실행하세요. 이는 새로운 프로젝트 디렉토리를 만들고, 초기 핵심 Nest 파일과 지원 모듈로 디렉토리를 채워 프로젝트의 관례적인 기본 구조를 만듭니다. **Nest CLI**로 새 프로젝트를 만드는 것은 처음 사용하는 사용자에게 권장됩니다. 우리는 [첫 단계](first-steps)에서 이 접근 방식을 계속 사용할 것입니다.

```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

> 정보 **힌트** 더 엄격한 기능 세트로 새로운 TypeScript 프로젝트를 만들려면 `nest new` 명령에 `--strict` 플래그를 전달하세요.

#### 대안

또는 **Git**을 사용하여 TypeScript 스타터 프로젝트를 설치하려면:

```bash
$ git clone https://github.com/nestjs/typescript-starter.git project
$ cd project
$ npm install
$ npm run start
```

> 정보 **힌트** git 히스토리 없이 저장소를 클론하고 싶다면 [degit](https://github.com/Rich-Harris/degit)을 사용할 수 있습니다.

브라우저를 열고 [`http://localhost:3000/`](http://localhost:3000/)로 이동하세요.

JavaScript 버전의 스타터 프로젝트를 설치하려면 위의 명령 시퀀스에서 `javascript-starter.git`을 사용하세요.

**npm**(또는 **yarn**)을 사용하여 핵심 및 지원 파일을 설치하여 처음부터 새 프로젝트를 수동으로 만들 수도 있습니다. 이 경우에는 당연히 프로젝트 보일러플레이트 파일을 직접 만들어야 합니다.

```bash
$ npm i --save @nestjs/core @nestjs/common rxjs reflect-metadata
```
