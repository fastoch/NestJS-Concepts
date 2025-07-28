src = https://www.youtube.com/watch?v=IdsBwplQAMw

# 1. Application Types

There is a misconception that NestJS in only for building APIs.  

In fact, there are 3 types of applications you can build with Nest:

- an HTTP server application, using `NestFactory.create`:
```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

- a Microservice application, using `NestFactory.createMicroservice`:
```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { Transport, MicroserviceOptions } from '@nestjs/microservices';

async function bootstrap() {
  const app = await NestFactory.createMicroservice<MicroserviceOptions>(
    AppModule,
    {
      transport: Transport.TCP,
    },
  )
  await app.listen();
}
bootstrap(); 
```

Microservices are very similar to HTTP servers, except that they can use different transport protocols and 
communicate through internal networks.  

- a standalone application, using `NestFactory.createApplicationContext`:
```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { AppService } from './app.service';

async function bootstrap() {
  const app = await NestFactory.createApplicationContext(AppModule);
  const appService = app.get(AppService);
  appService.doSomething();
}
bootstrap();
```

A standalone application is just an application that doesn't have network listeners.  
They are great for creating scheduled tasks, or CLI tools.

# 2. Modules

All Nest applications need a **root module**.  
Modules are the building blocks of a Nest application.  

At the top, you always have a root module, which can use other modules.  
And those other modules can also use other modules, and so on...  

In terms of implementation, a module is a **class** that is annotated with a `@Module()` **decorator**:
```ts
import { Module } from '@nestjs/common';

@Module({})  // decorator
export class AppModule {}
```

Decorators are very important and are everywhere in NestJS.  

# 3. Decorators

A decorator is a special kind of function that can be attached to classes, methods, properties...  
It can modify their behavior and add metadata to them.  

```ts
@classDecorator()
class MyClass {
  @MethodDecorator()
  myMethod(@ArgDecorator() myArg: string) {
    // ...
  }
}
```

Think of decorators as wearing accessories or suits that give you special abilities.  

# 4. Application Module Graph

In the context of a NestJS application, the "application module graph" refers to the internal **structure** that represents how 
all **modules** within the app are **interconnected**, including their dependencies and relationships to each other.  

Every NestJS app has at least a root module. This is the starting point where Nest begins to construct the application graph.  

To create the application module graph, we need to define relationships between Modules.  

In NestJS, relationships between modules are defined primarily through the `@Module()` decorator, which organizes modules and their interdependencies using four main properties:

- **imports**: this property lists other modules that this module depends on. When you import a module, you can use the providers that it exports
- **exports**: this specifies which providers (services, etc.) the current module makes available to other modules that import it.
- **providers**: these are the services/providers instantiated by the Nest **injector** within this module, which can be shared across the module or exported for use in other modules
- **controllers**: The controllers that belong to this module

By combining these, Nest builds an internal graph to resolve relationships and dependencies between modules and their providers.

# 5. Controllers

Once we have our Modules defined, we can add Controllers inside them.  
Controllers are classes that are annotated with the `@Controller()` decorator.  

**A controller is in charge of receiving incoming requests and returning a response.**  
Anything else is delegated to other classes.  

We can define a route path in the controller decorator, then we add methods to handle the incoming requests.  
Those methods can be annotated with a HTTP verb, and those decorators can take a subroute as parameter.  

```ts
import { Controller } from '@nestjs/common';

@Controller('my-app')  // route path
export class AppController {
  @Get('hello')  // HTTP verb with subroute
  hello() {
    return 'Hello World!';
  }
}
```

# 6. Providers

