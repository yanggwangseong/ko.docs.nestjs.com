### 컨트롤러

컨트롤러는 들어오는 **요청**을 처리하고 클라이언트에게 **응답**을 반환하는 역할을 담당합니다.

<figure><img class="illustrative-image" src="/assets/Controllers_1.png" /></figure>

컨트롤러의 목적은 애플리케이션에 대한 특정 요청을 받는 것입니다. **라우팅** 메커니즘은 어떤 컨트롤러가 어떤 요청을 받을지 제어합니다. 일반적으로 각 컨트롤러는 하나 이상의 라우트를 가지며, 서로 다른 라우트는 다양한 작업을 수행할 수 있습니다.

기본 컨트롤러를 생성하기 위해 우리는 클래스와 **데코레이터**를 사용합니다. 데코레이터는 클래스에 필요한 메타데이터를 연결하고 Nest가 라우팅 맵을 생성할 수 있게 합니다(요청을 해당 컨트롤러에 연결).

> info **힌트** 내장된 [검증](https://docs.nestjs.com/techniques/validation)을 갖춘 CRUD 컨트롤러를 빠르게 생성하려면 CLI의 [CRUD 생성기](https://docs.nestjs.com/recipes/crud-generator#crud-generator)를 사용할 수 있습니다: `nest g resource [name]`.

#### 라우팅

다음 예제에서는 기본 컨트롤러를 정의하는 데 **필수적인** `@Controller()` 데코레이터를 사용할 것입니다. `cats`의 선택적 라우트 경로 접두사를 지정할 것입니다. `@Controller()` 데코레이터에서 경로 접두사를 사용하면 관련된 라우트 세트를 쉽게 그룹화하고 반복적인 코드를 최소화할 수 있습니다. 예를 들어, `cats` 엔터티와의 상호작용을 관리하는 라우트 세트를 `/cats` 경로 아래에 그룹화할 수 있습니다. 이 경우, 파일의 각 라우트에 대해 경로의 해당 부분을 반복할 필요가 없도록 `@Controller()` 데코레이터에 `cats` 경로 접두사를 지정할 수 있습니다.

```typescript
@@filename(cats.controller)
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll() {
    return 'This action returns all cats';
  }
}
```

> info **힌트** CLI를 사용하여 컨트롤러를 생성하려면 간단히 `$ nest g controller [name]` 명령을 실행하세요.

`@Get()` HTTP 요청 메서드 데코레이터는 `findAll()` 메서드 앞에 위치하여 Nest에게 HTTP 요청에 대한 특정 엔드포인트 핸들러를 생성하도록 지시합니다. 엔드포인트는 HTTP 요청 메서드(GET인 경우)와 라우트 경로에 해당합니다. 라우트 경로란 무엇일까요? 핸들러의 라우트 경로는 컨트롤러에 선언된 (선택적) 접두사와 메서드의 데코레이터에 지정된 경로를 연결하여 결정됩니다. 우리는 모든 라우트에 대해 접두사(`cats`)를 선언했고, 데코레이터에 경로 정보를 추가하지 않았으므로 Nest는 `GET /cats` 요청을 이 핸들러에 매핑할 것입니다. 앞서 언급했듯이, 경로는 선택적 컨트롤러 경로 접두사 **와** 요청 메서드 데코레이터에 선언된 경로 문자열을 모두 포함합니다. 예를 들어, `cats`의 경로 접두사와 `@Get('breed')` 데코레이터를 결합하면 `GET /cats/breed`와 같은 요청에 대한 라우트 매핑이 생성됩니다.

위 예제에서 이 엔드포인트로 GET 요청이 들어오면 Nest는 요청을 사용자 정의 `findAll()` 메서드로 라우팅합니다. 여기서 선택한 메서드 이름은 전적으로 임의적임을 유의하세요. 우리는 경로를 바인딩할 메서드를 선언해야 하지만, Nest는 선택한 메서드 이름에 아무런 의미를 부여하지 않습니다.

이 메서드는 200 상태 코드와 관련 응답을 반환합니다. 이 경우 단순히 문자열입니다. 왜 이런 일이 발생할까요? 이를 설명하기 위해 Nest가 응답을 조작하기 위해 사용하는 두 가지 **다른** 옵션의 개념을 먼저 소개하겠습니다:

<table>
  <tr>
    <td>표준 방식 (권장)</td>
    <td>
      이 내장된 방식을 사용하면, 요청 핸들러가 JavaScript 객체나 배열을 반환할 때 자동으로 JSON으로 직렬화됩니다. 반면, JavaScript 기본 타입(e.g., <code>string</code>, <code>number</code>, <code>boolean</code>)을 반환할 경우, Nest는 값을 직렬화하려 시도하지 않고 단순히 값을 보냅니다. 이는 응답 처리를 단순하게 만듭니다: 값을 반환하기만 하면 Nest가 나머지를 처리합니다.
      <br />
      <br /> 또한 응답의 <strong>상태 코드</strong>는 POST 요청을 제외하고 기본적으로 항상 200입니다(POST 요청은 201을 사용). 이 동작은 핸들러 수준에서 <code>@HttpCode(...)</code> 데코레이터를 추가하여 쉽게 변경할 수 있습니다(자세한 내용은 <a href='controllers#status-code'>상태 코드</a> 참조).
    </td>
  </tr>
  <tr>
    <td>라이브러리 별 방식</td>
    <td>
      우리는 라이브러리 별 (e.g., Express) <a href="https://expressjs.com/en/api.html#res" rel="nofollow" target="_blank">응답 객체</a>를 사용할 수 있으며, 이는 메서드 핸들러 시그니처에 <code>@Res()</code> 데코레이터를 사용하여 주입할 수 있습니다 (e.g., <code>findAll(@Res() response)</code>). 이 접근 방식을 사용하면 해당 객체가 노출하는 기본 응답 처리 메서드를 사용할 수 있습니다. 예를 들어, Express를 사용하는 경우 <code>response.status(200).send()</code>와 같은 코드를 사용하여 응답을 구성할 수 있습니다.
    </td>
  </tr>
</table>

> warning **경고** 핸들러가 `@Res()` 또는 `@Next()`를 사용하고 있음을 Nest가 감지하면, 이는 라이브러리 별 방식을 선택했음을 나타냅니다. 두 접근 방식을 동시에 사용하면, 표준 방식은 **자동으로 비활성화**되어 해당 단일 라우트에 대해 예상대로 작동하지 않습니다. 두 방식을 동시에 사용하려면 (예: 쿠키/헤더만 설정하고 나머지는 프레임워크에 맡기려는 경우), `@Res({ passthrough: true })` 데코레이터에서 `passthrough` 옵션을 `true`로 설정해야 합니다.

<app-banner-devtools></app-banner-devtools>

#### 요청 객체

핸들러는 종종 클라이언트의 **요청** 세부 사항에 접근해야 합니다. Nest는 기본 플랫폼(기본적으로 Express)의 [요청 객체](https://expressjs.com/en/api.html#req)에 접근할 수 있게 합니다. 우리는 핸들러 시그니처에 `@Req()` 데코레이터를 추가하여 Nest에게 요청 객체를 주입하도록 지시할 수 있습니다.

```typescript
@@filename(cats.controller)
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Bind, Get, Req } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  @Bind(Req())
  findAll(request) {
    return 'This action returns all cats';
  }
}
```

> info **힌트** `request: Request` 매개변수 예제와 같이 `express` 타이핑을 활용하려면 `@types/express` 패키지를 설치하세요.

요청 객체는 HTTP 요청을 나타내며, 요청 쿼리 문자열, 매개변수, HTTP 헤더, 본문에 대한 속성을 가지고 있습니다([자세히 보기](https://expressjs.com/en/api.html#req)). 대부분의 경우 이러한 속성을 수동으로 가져올 필요가 없습니다. 대신 `@Body()` 또는 `@Query()`와 같은 전용 데코레이터를 사용할 수 있습니다. 아래는 제공되는 데코레이터와 그것들이 나타내는 기본 플랫폼 별 객체의 목록입니다.

<table>
  <tbody>
    <tr>
      <td><code>@Request(), @Req()</code></td>
      <td><code>req</code></td></tr>
    <tr>
      <td><code>@Response(), @Res()</code><span class="table-code-asterisk">*</span></td>
      <td><code>res</code></td>
    </tr>
    <tr>
      <td><code>@Next()</code></td>
      <td><code>next</code></td>
    </tr>
    <tr>
      <td><code>@Session()</code></td>
      <td><code>req.session</code></td>
    </tr>
    <tr>
      <td><code>@Param(key?: string)</code></td>
      <td><code>req.params</code> / <code>req.params[key]</code></td>
    </tr>
    <tr>
      <td><code>@Body(key?: string)</code></td>
      <td><code>req.body</code> / <code>req.body[key]</code></td>
    </tr>
    <tr>
      <td><code>@Query(key?: string)</code></td>
      <td><code>req.query</code> / <code>req.query[key]</code></td>
    </tr>
    <tr>
      <td><code>@Headers(name?: string)</code></td>
      <td><code>req.headers</code> / <code>req.headers[name]</code></td>
    </tr>
    <tr>
      <td><code>@Ip()</code></td>
      <td><code>req.ip</code></td>
    </tr>
    <tr>
      <td><code>@HostParam()</code></td>
      <td><code>req.hosts</code></td>
    </tr>
  </tbody>
</table>

<sup>\* </sup>기본 HTTP 플랫폼(예: Express 및 Fastify)의 타이핑 호환성을 위해, Nest는 `@Res()` 및 `@Response()` 데코레이터를 제공합니다. `@Res()`는 단순히 `@Response()`의 별칭입니다. 두 데코레이터 모두 기본 플랫폼의 네이티브 `response` 객체 인터페이스를 직접 노출합니다. 이를 사용할 때는 기본 라이브러리의 타이핑(예: `@types/express`)을 가져와야 합니다. 메서드 핸들러에 `@Res()` 또는 `@Response()`를 주입하면 해당 핸들러에 대해 Nest가 **라이브러리 별 모드**로 전환되며, 응답 관리를 직접 담당하게 됩니다. 이렇게 할 때는 `res.json(...)` 또는 `res.send(...)`와 같은 호출을 통해 어떤 형태로든 응답을 발행해야 하며, 그렇지 않으면 HTTP 서버가 멈춰버립니다.

> info **힌트** 자체적인 커스텀 데코레이터를 생성하는 방법을 배우려면 [이곳](/custom-decorators) 장을 방문하세요.

#### 리소스

앞서, 고양이 리소스를 가져오기 위한 엔드포인트(**GET** 라우트)를 정의했습니다. 일반적으로 새로운 레코드를 생성하는 엔드포인트도 제공하고자 할 것입니다. 이를 위해 **POST** 핸들러를 만들어 보겠습니다:

```typescript
@@filename(cats.controller)
import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create(): string {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
@@switch
import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create() {
    return 'This action adds a new cat';
  }

  @Get()
  findAll() {
    return 'This action returns all cats';
  }
}
```

그렇게 간단합니다. Nest는 모든 표준 HTTP 메서드에 대한 데코레이터를 제공합니다: `@Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Patch()`, `@Options()`, 및 `@Head()`. 추가로, `@All()`은 모든 메서드를 처리하는 엔드포인트를 정의합니다.

#### 라우트 와일드카드

패턴 기반 라우트도 지원됩니다. 예를 들어, 별표(\*)는 와일드카드로 사용되며, 임의의 문자 조합과 일치합니다.

```typescript
@Get('ab*cd')
findAll() {
  return 'This route uses a wildcard';
}
```

`'ab*cd'` 라우트 경로는 `abcd`, `ab_cd`, `abecd` 등과 일치합니다. 경로 문자열에서 `?`, `+`, `*`, `()` 문자는 정규 표현식에서의 그 의미의 하위 집합으로 사용될 수 있습니다. 하이픈(`-`)과 점(`.`)은 문자열 기반 경로에서 리터럴로 해석됩니다.

> warning **경고** 라우트 중간의 와일드카드는 Express에서만 지원됩니다.

#### 상태 코드

앞서 언급했듯이, 응답의 **상태 코드**는 기본적으로 **200**이며, POST 요청의 경우 **201**입니다. 핸들러 수준에서 `@HttpCode(...)` 데코레이터를 추가하여 이 동작을 쉽게 변경할 수 있습니다.

```typescript
@Post()
@HttpCode(204)
create() {
  return 'This action adds a new cat';
}
```

> info **힌트** `@nestjs/common` 패키지에서 `HttpCode`를 가져오세요.

종종 상태 코드는 고정된 값이 아니라 다양한 요소에 따라 달라집니다. 이 경우, 라이브러리 별 **응답** 객체(`@Res()`를 사용하여 주입) 또는 오류의 경우 예외를 던지는 방식을 사용할 수 있습니다.

#### 헤더

커스텀 응답 헤더를 지정하려면 `@Header()` 데코레이터를 사용하거나 라이브러리 별 응답 객체를 사용하여 직접 `res.header()`를 호출할 수 있습니다.

```typescript
@Post()
@Header('Cache-Control', 'none')
create() {
  return 'This action adds a new cat';
}
```

> info **힌트** `@nestjs/common` 패키지에서 `Header`를 가져오세요.

#### 리디렉션

특정 URL로 응답을 리디렉션하려면 `@Redirect()` 데코레이터를 사용하거나 라이브러리 별 응답 객체를 사용하여 직접 `res.redirect()`를 호출할 수 있습니다.

`@Redirect()`는 두 개의 인수, `url`과 `statusCode`를 받으며, 둘 다 선택적입니다. `statusCode`의 기본 값은 생략 시 `302` (`Found`)입니다.

```typescript
@Get()
@Redirect('https://nestjs.com', 301)
```

> info **힌트** HTTP 상태 코드나 리디렉션 URL을 동적으로 결정하려는 경우, `HttpRedirectResponse` 인터페이스(`@nestjs/common`에서 제공)를 따르는 객체를 반환하세요.

반환된 값은 `@Redirect()` 데코레이터에 전달된 모든 인수를 덮어씁니다. 예를 들어:

```typescript
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
  if (version && version === '5') {
    return { url: 'https://docs.nestjs.com/v5/' };
  }
}
```

#### 라우트 매개변수

정적 경로를 사용하는 라우트는 요청의 일부로 **동적 데이터**를 수용해야 할 때 작동하지 않습니다(e.g., `GET /cats/1`으로 id가 `1`인 고양이를 가져오는 경우). 매개변수가 있는 라우트를 정의하려면, 라우트 경로의 해당 위치에 라우트 매개변수 **토큰**을 추가하여 요청 URL에서 해당 위치의 동적 값을 캡처할 수 있습니다. 아래 `@Get()` 데코레이터 예제에서 라우트 매개변수 토큰이 이를 보여줍니다. 이러한 방식으로 선언된 라우트 매개변수는 `@Param()` 데코레이터를 사용하여 메서드 시그니처에 추가할 수 있습니다.

> info **힌트** 매개변수가 있는 라우트는 모든 정적 경로 이후에 선언해야 합니다. 이는 매개변수화된 경로가 정적 경로로 향하는 트래픽을 가로채지 않도록 하기 위함입니다.

```typescript
@@filename()
@Get(':id')
findOne(@Param() params: any): string {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
@@switch
@Get(':id')
@Bind(Param())
findOne(params) {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
```

`@Param()`은 메서드 매개변수(`params` 예제에서)를 데코레이트하여 메서드 본문 내에서 해당 데코레이트된 매개변수의 속성으로 **라우트** 매개변수를 사용할 수 있게 합니다. 위 코드에서 보듯이 `params.id`를 참조하여 `id` 매개변수에 접근할 수 있습니다. 데코레이터에 특정 매개변수 토큰을 전달하면, 메서드 본문에서 해당 라우트 매개변수를 직접 이름으로 참조할 수도 있습니다.

> info **힌트** `@nestjs/common` 패키지에서 `Param`을 가져오세요.

```typescript
@@filename()
@Get(':id')
findOne(@Param('id') id: string): string {
  return `This action returns a #${id} cat`;
}
@@switch
@Get(':id')
@Bind(Param('id'))
findOne(id) {
  return `This action returns a #${id} cat`;
}
```

#### 서브 도메인 라우팅

`@Controller` 데코레이터는 `host` 옵션을 받아들여 들어오는 요청의 HTTP 호스트가 특정 값과 일치해야 함을 요구할 수 있습니다.

```typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
```

> **경고** **Fastify**는 중첩 라우터 지원이 부족하기 때문에, 서브 도메인 라우팅을 사용할 때는 (기본) Express 어댑터를 대신 사용해야 합니다.

경로 `path`와 유사하게, `hosts` 옵션은 호스트 이름의 해당 위치에서 동적 값을 캡처하기 위해 토큰을 사용할 수 있습니다. 아래 `@Controller()` 데코레이터 예제에서 호스트 매개변수 토큰이 이를 보여줍니다. 이러한 방식으로 선언된 호스트 매개변수는 `@HostParam()` 데코레이터를 사용하여 메서드 시그니처에 추가할 수 있습니다.

```typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
}
```

#### 스코프

다른 프로그래밍 언어 배경을 가진 사람들에게 Nest에서는 거의 모든 것이 들어오는 요청 간에 공유된다는 점이 예상치 못한 일일 수 있습니다. 우리는 데이터베이스에 대한 연결 풀, 전역 상태를 가진 싱글톤 서비스 등을 가지고 있습니다. Node.js가 각 요청이 별도의 스레드에 의해 처리되는 요청/응답 멀티스레드 무상태 모델을 따르지 않기 때문에, 싱글톤 인스턴스를 사용하는 것은 애플리케이션에 대해 완전히 **안전**합니다.

그러나 컨트롤러의 요청 기반 생명 주기가 원하는 동작일 수 있는 엣지 케이스가 있습니다. 예를 들어, GraphQL 애플리케이션에서의 요청별 캐싱, 요청 추적 또는 다중 테넌시와 같은 경우입니다. 스코프를 제어하는 방법에 대해서는 [여기](/fundamentals/injection-scopes)를 참조하세요.

#### 비동기성

우리는 최신 JavaScript를 사랑하며, 데이터 추출이 주로 **비동기적**이라는 것을 알고 있습니다. 그래서 Nest는 `async` 함수를 지원하고 잘 작동합니다.

> info **힌트** `async / await` 기능에 대해 더 알아보려면 [여기](https://kamilmysliwiec.com/typescript-2-1-introduction-async-await)를 방문하세요.

모든 비동기 함수는 `Promise`를 반환해야 합니다. 이는 Nest가 스스로 해결할 수 있는 지연된 값을 반환할 수 있음을 의미합니다. 다음 예제를 보겠습니다:

```typescript
@@filename(cats.controller)
@Get()
async findAll(): Promise<any[]> {
  return [];
}
@@switch
@Get()
async findAll() {
  return [];
}
```

위 코드는 완전히 유효합니다. 더욱이, Nest 라우트 핸들러는 RxJS [observable 스트림](https://rxjs-dev.firebaseapp.com/guide/observable)을 반환할 수 있어 더욱 강력합니다. Nest는 기본 소스에 자동으로 구독하고 스트림이 완료되면 마지막으로 방출된 값을 가져옵니다.

```typescript
@@filename(cats.controller)
@Get()
findAll(): Observable<any[]> {
  return of([]);
}
@@switch
@Get()
findAll() {
  return of([]);
}
```

위의 두 접근 방식 모두 작동하며, 요구 사항에 맞는 것을 사용할 수 있습니다.

#### 요청 페이로드

이전 POST 라우트 핸들러 예제는 클라이언트 매개변수를 수용하지 않았습니다. 여기에서 `@Body()` 데코레이터를 추가하여 이를 수정해 보겠습니다.

하지만 먼저 (TypeScript를 사용하는 경우), **DTO** (Data Transfer Object) 스키마를 정의해야 합니다. DTO는 네트워크를 통해 전송될 데이터를 정의하는 객체입니다. 우리는 **TypeScript** 인터페이스를 사용하거나 단순한 클래스를 사용하여 DTO 스키마를 정의할 수 있습니다. 흥미롭게도, 여기서는 **클래스**를 사용하는 것을 권장합니다. 왜일까요? 클래스는 JavaScript ES6 표준의 일부이며, 컴파일된 JavaScript에서 실제 엔터티로 유지됩니다. 반면, TypeScript 인터페이스는 트랜스파일링 중에 제거되기 때문에 Nest는 런타임에 이를 참조할 수 없습니다. 이는 **파이프**와 같은 기능이 런타임에 변수의 메타타입에 접근할 수 있을 때 추가적인 가능성을 열어주기 때문에 중요합니다.

`CreateCatDto` 클래스를 만들어 보겠습니다:

```typescript
@@filename(create-cat.dto)
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

기본적인 세 가지 속성만 가지고 있습니다. 그 후, 우리는 새로 생성된 DTO를 `CatsController` 내에서 사용할 수 있습니다:

```typescript
@@filename(cats.controller)
@Post()
async create(@Body() createCatDto: CreateCatDto) {
  return 'This action adds a new cat';
}
@@switch
@Post()
@Bind(Body())
async create(createCatDto) {
  return 'This action adds a new cat';
}
```

> info **힌트** 우리의 `ValidationPipe`는 메서드 핸들러가 받아야 할 속성이 아닌 속성을 필터링할 수 있습니다. 이 경우, 허용 가능한 속성을 화이트리스트로 설정하고, 화이트리스트에 포함되지 않은 속성은 결과 객체에서 자동으로 제거됩니다. `CreateCatDto` 예제에서는 화이트리스트가 `name`, `age`, `breed` 속성입니다. [여기](https://docs.nestjs.com/techniques/validation#stripping-properties)에서 자세히 알아보세요.

#### 오류 처리

오류를 처리하는 것(즉, 예외와 함께 작업하는 것)에 대한 별도의 장이 [여기](/exception-filters)에 있습니다.

#### 전체 리소스 샘플

아래는 여러 가지 데코레이터를 사용하여 기본 컨트롤러를 생성하는 예제입니다. 이 컨트롤러는 내부 데이터를 접근하고 조작하기 위한 몇 가지 메서드를 노출합니다.

```typescript
@@filename(cats.controller)
import { Controller, Get, Query, Post, Body, Put, Param, Delete } from '@nestjs/common';
import { CreateCatDto, UpdateCatDto, ListAllEntities } from './dto';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(@Query() query: ListAllEntities) {
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return `This action removes a #${id} cat`;
  }
}
@@switch
import { Controller, Get, Query, Post, Body, Put, Param, Delete, Bind } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Body())
  create(createCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  @Bind(Query())
  findAll(query) {
    console.log(query);
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  @Bind(Param('id'))
  findOne(id) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  @Bind(Param('id'), Body())
  update(id, updateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  @Bind(Param('id'))
  remove(id) {
    return `This action removes a #${id} cat`;
  }
}
```

> info **힌트** Nest CLI는 **모든 보일러플레이트 코드**를 자동으로 생성하여 우리가 이를 모두 작성하지 않아도 되도록 도와주며, 개발자 경험을 훨씬 더 단순하게 만들어 주는 생성기(schematic)를 제공합니다. 이 기능에 대해 더 알아보려면 [여기](/recipes/crud-generator)를 읽어보세요.

#### 실행 및 작동

위의 컨트롤러가 완전히 정의되었지만, Nest는 여전히 `CatsController`가 존재한다는 것을 알지 못하며, 결과적으로 이 클래스의 인스턴스를 생성하지 않습니다.

컨트롤러는 항상 모듈에 속하며, 따라서 `@Module()` 데코레이터 내에 `controllers` 배열을 포함시킵니다. 아직 루트 `AppModule` 외에 다른 모듈을 정의하지 않았기 때문에, `CatsController`를 도입하기 위해 이를 사용할 것입니다:

```typescript
@@filename(app.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';

@Module({
  controllers: [CatsController],
})
export class AppModule {}
```

우리는 `@Module()` 데코레이터를 사용하여 메타데이터를 모듈 클래스에 첨부했으며, Nest는 이제 어떤 컨트롤러를 마운트해야 하는지 쉽게 반영할 수 있습니다.

#### 라이브러리 별 접근 방식

지금까지 우리는 Nest의 표준 응답 조작 방식을 논의했습니다. 응답을 조작하는 두 번째 방법은 라이브러리 별 [응답 객체](https://expressjs.com/en/api.html#res)를 사용하는 것입니다. 특정 응답 객체를 주입하려면 `@Res()` 데코레이터를 사용해야 합니다. 차이점을 보여주기 위해 `CatsController`를 다음과 같이 다시 작성해 보겠습니다:

```typescript
@@filename()
import { Controller, Get, Post, Res, HttpStatus } from '@nestjs/common';
import { Response } from 'express';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Res() res: Response) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  findAll(@Res() res: Response) {
     res.status(HttpStatus.OK).json([]);
  }
}
@@switch
import { Controller, Get, Post, Bind, Res, Body, HttpStatus } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  @Bind(Res(), Body())
  create(res, createCatDto) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  @Bind(Res())
  findAll(res) {
     res.status(HttpStatus.OK).json([]);
  }
}
```

이 접근 방식은 작동하며, 응답 객체를 완전히 제어할 수 있게 해주어 더 유연성을 제공하지만, 주의해서 사용해야 합니다. 일반적으로 이 접근 방식은 코드가 플랫폼 종속적이게 되고(기본 라이브러리에 따라 응답 객체의 API가 다를 수 있음), 테스트하기 어려워집니다(응답 객체를 모킹해야 함 등).

또한, 위 예제에서는 Interceptors 및 `@HttpCode()` / `@Header()` 데코레이터와 같은 Nest의 표준 응답 처리 기능과의 호환성이 사라집니다. 이를 해결하기 위해 `passthrough` 옵션을 `true`로 설정할 수 있습니다:

```typescript
@@filename()
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.status(HttpStatus.OK);
  return [];
}
@@switch
@Get()
@Bind(Res({ passthrough: true }))
findAll(res) {
  res.status(HttpStatus.OK);
  return [];
}
```

이제 네이티브 응답 객체와 상호 작용할 수 있지만(예를 들어, 특정 조건에 따라 쿠키나 헤더를 설정), 나머지는 프레임워크에 맡길 수 있습니다.
