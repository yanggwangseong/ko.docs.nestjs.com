### 프로바이더

프로바이더는 Nest의 기본 개념입니다. 많은 기본 Nest 클래스들은 프로바이더로 취급될 수 있습니다 – 서비스, 리포지토리, 팩토리, 헬퍼 등등. 프로바이더의 주요 아이디어는 **주입**될 수 있다는 것입니다. 이는 객체들이 서로 다양한 관계를 생성할 수 있게 해주며, 이러한 객체들을 "연결"하는 기능을 대부분 Nest 런타임 시스템에 위임할 수 있음을 의미합니다.

<figure><img class="illustrative-image" src="/assets/Components_1.png" /></figure>

이전 장에서는 간단한 `CatsController`를 구축했습니다. 컨트롤러는 HTTP 요청을 처리하고 더 복잡한 작업은 **프로바이더**에 위임해야 합니다. 프로바이더는 [모듈](/modules)에서 `providers`로 선언되는 일반적인 JavaScript 클래스입니다.

> info **힌트** Nest는 의존성을 보다 객체 지향적인 방식으로 설계하고 조직할 수 있는 가능성을 제공하므로, [SOLID](https://en.wikipedia.org/wiki/SOLID) 원칙을 따르는 것을 강력히 권장합니다.

#### 서비스

간단한 `CatsService`를 생성하는 것부터 시작해 보겠습니다. 이 서비스는 데이터 저장 및 검색을 담당하며, `CatsController`에서 사용되도록 설계되었기 때문에 프로바이더로 정의하기에 좋은 후보입니다.

```typescript
@@filename(cats.service)
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
@@switch
import { Injectable } from '@nestjs/common';

@Injectable()
export class CatsService {
  constructor() {
    this.cats = [];
  }

  create(cat) {
    this.cats.push(cat);
  }

  findAll() {
    return this.cats;
  }
}
```

> info **힌트** CLI를 사용하여 서비스를 생성하려면 간단히 `$ nest g service cats` 명령을 실행하세요.

우리의 `CatsService`는 하나의 속성과 두 개의 메서드를 가진 기본 클래스입니다. 유일한 새로운 기능은 `@Injectable()` 데코레이터를 사용한다는 점입니다. `@Injectable()` 데코레이터는 메타데이터를 첨부하여 `CatsService`가 Nest [IoC](https://en.wikipedia.org/wiki/Inversion_of_control) 컨테이너에 의해 관리될 수 있는 클래스임을 선언합니다. 참고로, 이 예제는 아마도 다음과 같은 `Cat` 인터페이스도 사용하고 있을 것입니다:

```typescript
@@filename(interfaces/cat.interface)
export interface Cat {
  name: string;
  age: number;
  breed: string;
}
```

이제 고양이를 검색할 서비스 클래스를 만들었으니, 이를 `CatsController` 내에서 사용해 보겠습니다:

```typescript
@@filename(cats.controller)
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
@@switch
import { Controller, Get, Post, Body, Bind, Dependencies } from '@nestjs/common';
import { CatsService } from './cats.service';

@Controller('cats')
@Dependencies(CatsService)
export class CatsController {
  constructor(catsService) {
    this.catsService = catsService;
  }

  @Post()
  @Bind(Body())
  async create(createCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll() {
    return this.catsService.findAll();
  }
}
```

`CatsService`는 **주입**을 통해 클래스 생성자에서 주입됩니다. `private` 구문을 사용한 점에 주목하세요. 이 단축 구문은 `catsService` 멤버를 동시에 선언하고 초기화할 수 있게 해줍니다.

#### 의존성 주입

Nest는 일반적으로 **의존성 주입**으로 알려진 강력한 디자인 패턴을 중심으로 구축되었습니다. 이 개념에 대해 공식 [Angular](https://angular.dev/guide/di) 문서에서 훌륭한 기사를 읽는 것을 권장합니다.

Nest에서는 TypeScript의 기능 덕분에 의존성을 관리하는 것이 매우 쉽습니다. 의존성은 타입만으로 해결되기 때문입니다. 아래 예제에서 Nest는 `CatsService`의 인스턴스를 생성하고 반환함으로써 `catsService`를 해결할 것입니다(또는 싱글톤의 정상적인 경우, 이미 다른 곳에서 요청된 경우 기존 인스턴스를 반환). 이 의존성은 컨트롤러의 생성자에 해결되어 전달됩니다 (또는 지정된 속성에 할당됩니다):

```typescript
constructor(private catsService: CatsService) {}
```

#### 스코프

프로바이더는 일반적으로 애플리케이션 생명 주기와 동기화된 생명 주기("스코프")를 가집니다. 애플리케이션이 부트스트랩될 때, 모든 의존성이 해결되어야 하므로 모든 프로바이더가 인스턴스화됩니다. 마찬가지로, 애플리케이션이 종료될 때, 각 프로바이더는 파괴됩니다. 그러나 프로바이더의 생명 주기를 **요청 기반**으로 만드는 방법도 있습니다. 이러한 기술에 대해 더 알고 싶다면 [여기](/fundamentals/injection-scopes)를 참조하세요.

<app-banner-courses></app-banner-courses>

#### 커스텀 프로바이더

Nest는 프로바이더 간의 관계를 해결하는 내장된 제어의 역전("IoC") 컨테이너를 가지고 있습니다. 이 기능은 위에서 설명한 의존성 주입 기능의 기초가 되지만, 지금까지 설명한 것보다 훨씬 더 강력합니다. 프로바이더를 정의하는 여러 가지 방법이 있습니다: 일반 값, 클래스, 비동기 또는 동기 팩토리를 사용할 수 있습니다. 더 많은 예제는 [여기](/fundamentals/dependency-injection)에서 확인할 수 있습니다.

#### 선택적 프로바이더

때때로, 반드시 해결될 필요는 없는 의존성이 있을 수 있습니다. 예를 들어, 클래스가 **구성 객체**에 의존하지만, 전달되지 않으면 기본값을 사용해야 하는 경우입니다. 이러한 경우, 프로바이더의 부족이 오류를 일으키지 않기 때문에 의존성은 선택적이 됩니다.

프로바이더가 선택적임을 나타내려면 생성자 시그니처에 `@Optional()` 데코레이터를 사용하세요.

```typescript
import { Injectable, Optional, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  constructor(@Optional() @Inject('HTTP_OPTIONS') private httpClient: T) {}
}
```

위 예제에서는 커스텀 프로바이더를 사용하고 있으므로 `HTTP_OPTIONS` 커스텀 **토큰**을 포함하고 있습니다. 이전 예제에서는 생성자를 통해 의존성을 표시하는 클래스 기반 주입을 보여주었습니다. 커스텀 프로바이더와 관련된 토큰에 대해 더 알고 싶다면 [여기](/fundamentals/custom-providers)를 참조하세요.

#### 프로퍼티 기반 주입

지금까지 사용한 기술은 생성자 기반 주입(constructor-based injection)이라고 불립니다. 프로바이더가 생성자 메서드를 통해 주입되기 때문입니다. 매우 특정한 경우에 **프로퍼티 기반 주입**이 유용할 수 있습니다. 예를 들어, 최상위 클래스가 하나 이상의 프로바이더에 의존할 때, 하위 클래스의 생성자에서 `super()`를 호출하여 모든 프로바이더를 전달하는 것은 매우 번거로울 수 있습니다. 이를 피하기 위해 `@Inject()` 데코레이터를 프로퍼티 수준에서 사용할 수 있습니다.

```typescript
import { Injectable, Inject } from '@nestjs/common';

@Injectable()
export class HttpService<T> {
  @Inject('HTTP_OPTIONS')
  private readonly httpClient: T;
}
```

> warning **경고** 클래스가 다른 클래스를 확장하지 않는 경우, **생성자 기반** 주입을 항상 선호해야 합니다. 생성자는 필요한 의존성을 명확히 나타내며, `@Inject`로 주석이 달린 클래스 속성보다 더 나은 가시성을 제공합니다.

#### 프로바이더 등록

이제 프로바이더(`CatsService`)를 정의했고, 이 서비스를 소비하는 컨트롤러(`CatsController`)도 있으므로, Nest가 주입을 수행할 수 있도록 서비스를 등록해야 합니다. 이를 위해 모듈 파일(`app.module.ts`)을 수정하고, `@Module()` 데코레이터의 `providers` 배열에 서비스를 추가합니다.

```typescript
@@filename(app.module)
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';
import { CatsService } from './cats/cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class AppModule {}
```

이제 Nest는 `CatsController` 클래스의 의존성을 해결할 수 있습니다.

이제 우리의 디렉토리 구조는 다음과 같아야 합니다:

<div class="file-tree">
<div class="item">src</div>
<div class="children">
<div class="item">cats</div>
<div class="children">
<div class="item">dto</div>
<div class="children">
<div class="item">create-cat.dto.ts</div>
</div>
<div class="item">interfaces</div>
<div class="children">
<div class="item">cat.interface.ts</div>
</div>
<div class="item">cats.controller.ts</div>
<div class="item">cats.service.ts</div>
</div>
<div class="item">app.module.ts</div>
<div class="item">main.ts</div>
</div>
</div>

#### 수동 인스턴스화

지금까지는 Nest가 의존성 해결의 대부분 세부 사항을 자동으로 처리하는 방법에 대해 논의했습니다. 특정 상황에서는 내장된 의존성 주입 시스템을 벗어나 프로바이더를 수동으로 검색하거나 인스턴스화해야 할 수도 있습니다. 아래에서 두 가지 주제에 대해 간략히 논의합니다.

기존 인스턴스를 가져오거나 프로바이더를 동적으로 인스턴스화하려면 [모듈 참조](https://docs.nestjs.com/fundamentals/module-ref)를 사용할 수 있습니다.

`bootstrap()` 함수 내에서 프로바이더를 가져오려면 (예: 컨트롤러가 없는 스탠드얼론 애플리케이션이나 부트스트랩 중 구성 서비스를 활용하려는 경우) [스탠드얼론 애플리케이션](https://docs.nestjs.com/standalone-applications)을 참조하세요.
