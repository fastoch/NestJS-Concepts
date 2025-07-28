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

In terms of implementation, a module is a **class** that is annotated with a module **decorator**:
```ts
import { Module } from '@nestjs/common';

@Module({})  // decorator
export class AppModule {}
```

Decorators are very important and are everywhere in NestJS.  

# 3. Decorators

A decorator is a special kind of function that can be attached to methods, classes, and properties.  
It can modify their behavior and add metadata to them.  

Think of decorators as wearing accessories or suits that give you special abilities.
