---
title: 'How NestJS Middleware Works: A complete guide'
date: '2023-04-21'
tags: ['Software Development', 'Node', 'Backend']
draft: false
summary:
images: []
---

## How NestJS Middleware Works: A complete guide

[NestJS](https://nestjs.com/) is a popular open-source framework for building scalable and maintainable server-side applications with Node.js. One of the key features of NestJS is its support for middleware, which allows developers to customize the behavior of their applications by intercepting requests and responses.

In this blog post, we will explore what middleware is, how it works in NestJS, and how you can use it to enhance your applications.


## Table of Contents

- Prequirities
- Introduction to Middleware
- How middleware works
- Creating class middleware
- Creating functional middleware
- Applying middleware to specific routes
- Applying middleware to all routes
- Restricting middleware to specific routes

## Prequirities

Basic understanding of [node](https://nodejs.org/en/docs/guides/getting-started-guide/) and knowledge of creating CRUD application in [nestJS](https://docs.nestjs.com/) is advisable.

## What is Middleware?

Middleware is a software component that sits between an application's request/response cycle and performs specific operations on the request/response objects. It intercepts the request before it reaches the controller and can manipulate the request/response objects as needed.

Middleware can be used to perform a wide range of tasks, such as logging, error handling, authentication, and authorization, to name a few. In NestJS, middleware can be defined as a class or function that implements the NestMiddleware interface.

Some tasks that NestJS can perform are:

- Perform any operations
- Access and make changes to the request and response objects
- Call the next middleware function in the stack
- End the request-response cycle

## How does Middleware work in NestJS?

In NestJS, middleware can be used to intercept requests and responses at different levels of the application. It can be used globally or locally at the controller, method, or route level.

To use middleware in your NestJS application, you need to first create a function or class with an @Injectable decorator that implements the NestMiddleware interface. This interface defines a single method, `use`, which is called when a request is received. The `use` method takes two arguments: the `request` and `response` objects, and a `next` function that is used to pass control to the next middleware in the chain.

## Creating a class middleware

For getting started with the example you can simply clone and run the NestJs typescript starter from NestJs official website.

```

git clone https://github.com/nestjs/typescript-starter.git project

cd project

npm install

npm run start

```

After running the project create a middleware directory inside the src directory for organizing middleware.

```

cd src

mkdir middleware

```

Now inside the middleware directory create a file name class.middlware.ts. Let’s create class middleware inside this file by the following code:

```typescript
import { Injectable, NestMiddleware } from '@nestjs/common'

@Injectable()
export class ClassMiddlewareExample implements NestMiddleware {
  use(req: any, res: any, next: (error?: any) => void) {
    console.log('****this is class middleware*****')

    req.admin = true

    next()
  }
}
```

The above line creates a class middleware name ClassMiddlewareExample. The class middleware must implement NestMiddleware. All the logic of middleware is written inside the use method, req and res are two arguments which are request and response that can be modified in between. In the above example, we are simply logging and adding admin property to the request.

## Creating functional middleware

Now for creating function middleware let’s create another file inside the middleware directory, I will name this file function.middleware.ts.

```typescript
import { Request, Response, NextFunction } from 'express'

export function functionMiddlewareExample(req: Request, res: Response, next: NextFunction) {
  console.log('****function middleware example*****')
}
```

Function middleware is just a normal function with request, response, and NextFunction which can be used as middleware.

## Applying middleware to specific routes.

Now for applying this middleware to specific routes we need to add some routes. For this example, I will be using [Nest CLI](https://docs.nestjs.com/cli/overview) to generate the route name hello.

```

nest g resource hello

```

You can also use middleware locally to apply it to specific routes or controllers. To do this, you need to use the `configure` method of the `MiddlewareConsumer` object:

```typescript
import { AppService } from './app.service'

import { AppController } from './app.controller'

import { HelloModule } from './hello/hello.module'

import { ClassMiddlewareExample } from './middleware/class.middleware'

import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common'

@Module({
  imports: [HelloModule],

  controllers: [AppController],

  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(ClassMiddlewareExample).forRoutes('hello')
  }
}
```

In the example above, we're using the `apply` method to register the middleware and the `forRoutes` method to specify the routes to which the middleware should be applied. The `'hello'` argument specifies that the middleware should be applied to specific hello routes.

```typescript

@Get()

findAll(@Req()request) {

if(request.admin){

return this.helloService.findAll();

}

throw new UnauthorizedException()

}

```

In this example above, I’ve checked to return a response if admin is true and throw an exception when admin is false. If you notice we have modified the request and added property admin to true while creating class middleware.

## Applying middleware to all routes

In this section we will be adding middleware to all the routes present in our application. This will log when we request any endpoints.

```typescript
import { AppService } from './app.service'

import { AppController } from './app.controller'

import { HelloModule } from './hello/hello.module'

import { ClassMiddlewareExample } from './middleware/class.middleware'

import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common'

@Module({
  imports: [HelloModule],

  controllers: [AppController],

  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(ClassMiddlewareExample).forRoutes('*')
  }
}
```

In our AppModule, we implemented the NestModule along with the configure() method and applied our ClassMiddlewareExample middleware to all routes using .forRoutes('\*').

## Restricting middleware to specific routes

While building our application there could be the case that we want to exclude some routes from applying middleware. For this we can chain consumer apply method with exclude method to set which method to exclude in our application.

```typescript
import { AppService } from './app.service'

import { AppController } from './app.controller'

import { HelloModule } from './hello/hello.module'

import { ClassMiddlewareExample } from './middleware/class.middleware'

import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common'

@Module({
  imports: [HelloModule],

  controllers: [AppController],

  providers: [AppService],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(ClassMiddlewareExample).exclude('hello').forRoutes('*')
  }
}
```

In the example above, we're using the `exclude` method to specify that the middleware should not be applied to the `/hello` route. We can also specify which method of the particular route to exclude by passing the path and method imports the RequestMethod enum to reference the desired request method type to the exclude method.

## Conclusion

In this article, you have learned to use Nest middleware. You were introduced to creating custom NestJs middleware and using them locally and globally. All the code examples are available in [this github](https://github.com/bipinparajuli/nestjs-middlware) repo.