### 첫 단계

이 일련의 글에서는 Nest의 **핵심 기본 사항**을 배우게 됩니다. Nest 애플리케이션의 필수 구성 요소에 익숙해지기 위해, 입문 수준에서 광범위한 내용을 다루는 기본적인 CRUD 애플리케이션을 만들어 볼 것입니다.

#### 언어

우리는 [TypeScript](https://www.typescriptlang.org/)를 사랑하지만, 무엇보다도 [Node.js](https://nodejs.org/en/)를 사랑합니다. 그래서 Nest는 TypeScript와 순수 JavaScript 모두와 호환됩니다. Nest는 최신 언어 기능을 활용하므로, 순수 JavaScript로 사용하려면 [Babel](https://babeljs.io/) 컴파일러가 필요합니다.

우리가 제공하는 예제에서는 주로 TypeScript를 사용하지만, 언제든지 코드 스니펫을 순수 JavaScript 구문으로 **전환할 수 있습니다** (각 스니펫의 오른쪽 상단 모서리에 있는 언어 버튼을 클릭하면 됩니다).

#### 사전 요구 사항

운영 체제에 [Node.js](https://nodejs.org) (버전 >= 16)가 설치되어 있는지 확인해 주세요.

#### 설정

[Nest CLI](/cli/overview)를 사용하면 새 프로젝트를 설정하는 것이 매우 간단합니다. [npm](https://www.npmjs.com/)이 설치되어 있다면, OS 터미널에서 다음 명령어로 새로운 Nest 프로젝트를 생성할 수 있습니다:

```bash
$ npm i -g @nestjs/cli
$ nest new project-name
```

> 정보 **힌트** TypeScript의 [더 엄격한](https://www.typescriptlang.org/tsconfig#strict) 기능 세트로 새 프로젝트를 만들려면 `nest new` 명령에 `--strict` 플래그를 전달하세요.

`project-name` 디렉토리가 생성되고, 노드 모듈과 몇 가지 기본 파일들이 설치되며, `src/` 디렉토리가 생성되어 여러 핵심 파일들로 채워집니다.

<div class="file-tree">
  <div class="item">src</div>
  <div class="children">
    <div class="item">app.controller.spec.ts</div>
    <div class="item">app.controller.ts</div>
    <div class="item">app.module.ts</div>
    <div class="item">app.service.ts</div>
    <div class="item">main.ts</div>
  </div>
</div>

다음은 이 핵심 파일들에 대한 간단한 개요입니다:

|                          |                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------- |
| `app.controller.ts`      | 단일 라우트가 있는 기본 컨트롤러.                                                                  |
| `app.controller.spec.ts` | 컨트롤러를 위한 단위 테스트.                                                                       |
| `app.module.ts`          | 애플리케이션의 루트 모듈.                                                                          |
| `app.service.ts`         | 단일 메서드가 있는 기본 서비스.                                                                    |
| `main.ts`                | Nest 애플리케이션 인스턴스를 생성하기 위해 핵심 함수 `NestFactory`를 사용하는 애플리케이션 진입점. |

`main.ts`는 우리의 애플리케이션을 **부트스트랩**할 비동기 함수를 포함하고 있습니다:

```typescript
@@filename(main)

import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
@@switch
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

Nest 애플리케이션 인스턴스를 생성하기 위해 핵심 `NestFactory` 클래스를 사용합니다. `NestFactory`는 애플리케이션 인스턴스를 생성할 수 있게 하는 몇 가지 정적 메서드를 노출합니다. `create()` 메서드는 `INestApplication` 인터페이스를 충족하는 애플리케이션 객체를 반환합니다. 이 객체는 앞으로의 장에서 설명할 일련의 메서드를 제공합니다. 위의 `main.ts` 예제에서는 단순히 HTTP 리스너를 시작하여 애플리케이션이 인바운드 HTTP 요청을 기다리도록 합니다.

Nest CLI로 구축된 프로젝트는 개발자가 각 모듈을 자체 전용 디렉토리에 유지하는 관행을 따르도록 권장하는 초기 프로젝트 구조를 만듭니다.

> 정보 **힌트** 기본적으로 애플리케이션 생성 중 오류가 발생하면 앱이 코드 `1`로 종료됩니다. 대신 오류를 던지게 하려면 `abortOnError` 옵션을 비활성화하세요 (예: `NestFactory.create(AppModule, {{ '{' }} abortOnError: false {{ '}' }})`).

<app-banner-courses></app-banner-courses>

#### 플랫폼

Nest는 플랫폼에 구애받지 않는 프레임워크를 목표로 합니다. 플랫폼 독립성은 개발자가 여러 종류의 애플리케이션에서 활용할 수 있는 재사용 가능한 논리적 부분을 만들 수 있게 합니다. 기술적으로 Nest는 어댑터만 생성되면 어떤 Node HTTP 프레임워크와도 작동할 수 있습니다. 기본적으로 두 가지 HTTP 플랫폼이 지원됩니다: [express](https://expressjs.com/)와 [fastify](https://www.fastify.io). 여러분의 필요에 가장 잘 맞는 것을 선택할 수 있습니다.

|                    |                                                                                                                                                                                                                                                                                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `platform-express` | [Express](https://expressjs.com/)는 잘 알려진 미니멀리스트 웹 프레임워크입니다. 이는 실전에서 검증된 프로덕션 준비가 된 라이브러리로, 커뮤니티에 의해 구현된 많은 리소스를 가지고 있습니다. `@nestjs/platform-express` 패키지가 기본적으로 사용됩니다. 많은 사용자들이 Express로 충분히 만족하며, 이를 사용하기 위해 특별한 조치를 취할 필요가 없습니다. |
| `platform-fastify` | [Fastify](https://www.fastify.io/)는 최대의 효율성과 속도를 제공하는 데 중점을 둔 고성능, 저오버헤드 프레임워크입니다. 사용 방법은 [여기](/techniques/performance)에서 읽어보세요.                                                                                                                                                                       |

사용되는 플랫폼에 관계없이, 각각은 자체 애플리케이션 인터페이스를 노출합니다. 이들은 각각 `NestExpressApplication`과 `NestFastifyApplication`으로 볼 수 있습니다.

아래 예제와 같이 `NestFactory.create()` 메서드에 타입을 전달하면, `app` 객체는 해당 특정 플랫폼에서만 사용 가능한 메서드를 가지게 됩니다. 그러나 기본 플랫폼 API에 실제로 접근하려는 경우가 아니라면 타입을 **지정할 필요가 없습니다**.

```typescript
const app = await NestFactory.create<NestExpressApplication>(AppModule);
```

#### 애플리케이션 실행

설치 과정이 완료되면, OS 명령 프롬프트에서 다음 명령을 실행하여 인바운드 HTTP 요청을 수신하는 애플리케이션을 시작할 수 있습니다:

```bash
$ npm run start
```

> 정보 **힌트** 개발 프로세스 속도를 높이기 위해 (빌드 속도 20배 향상), `start` 스크립트에 `-b swc` 플래그를 전달하여 [SWC 빌더](/recipes/swc)를 사용할 수 있습니다. 예: `npm run start -- -b swc`.

이 명령은 `src/main.ts` 파일에 정의된 포트에서 HTTP 서버를 수신하는 앱을 시작합니다. 애플리케이션이 실행되면 브라우저를 열고 `http://localhost:3000/`으로 이동하세요. "Hello World!" 메시지가 보일 것입니다.

파일의 변경 사항을 감시하려면 다음 명령으로 애플리케이션을 시작할 수 있습니다:

```bash
$ npm run start:dev
```

이 명령은 파일을 감시하며, 자동으로 서버를 재컴파일하고 다시 로드합니다.

#### 린팅과 포매팅

[CLI](/cli/overview)는 규모에 맞는 신뢰할 수 있는 개발 워크플로우를 구축하기 위해 최선을 다합니다. 따라서 생성된 Nest 프로젝트에는 코드 **린터**와 **포매터**가 사전 설치되어 있습니다 (각각 [eslint](https://eslint.org/)와 [prettier](https://prettier.io/)).

> 정보 **힌트** 포매터와 린터의 역할에 대해 잘 모르시나요? [여기](https://prettier.io/docs/en/comparison.html)에서 차이점을 알아보세요.

최대한의 안정성과 확장성을 보장하기 위해, 우리는 기본 [`eslint`](https://www.npmjs.com/package/eslint)와 [`prettier`](https://www.npmjs.com/package/prettier) CLI 패키지를 사용합니다. 이 설정은 공식 확장 프로그램과의 깔끔한 IDE 통합을 설계상 허용합니다.

IDE가 관련 없는 헤드리스 환경(지속적 통합, Git 훅 등)의 경우, Nest 프로젝트는 바로 사용할 수 있는 `npm` 스크립트와 함께 제공됩니다.

```bash
# eslint로 린트 및 자동 수정
$ npm run lint

# prettier로 포맷
$ npm run format
```
