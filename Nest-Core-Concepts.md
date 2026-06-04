# NestJS Core Concepts, Properties, Methods, Decorators, and APIs

## What is NestJS?

NestJS is a TypeScript-based Node.js framework used to build scalable backend applications, REST APIs, GraphQL APIs, WebSocket apps, and microservices.

NestJS is heavily inspired by Angular architecture. It uses:

- Modules
- Controllers
- Providers / Services
- Dependency Injection
- Decorators
- Middleware
- Guards
- Pipes
- Interceptors
- Exception Filters

---

# 1. NestFactory

## What is NestFactory?

`NestFactory` is used to bootstrap a NestJS application.

## Common Methods

| Method | Description |
|---|---|
| `NestFactory.create()` | Creates a standard HTTP Nest application |
| `NestFactory.createMicroservice()` | Creates a Nest microservice |
| `NestFactory.createApplicationContext()` | Creates a standalone Nest dependency injection context |

## Example

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';

async function bootstrap() {
  // Creates the Nest application
  const app = await NestFactory.create(AppModule);

  // Starts HTTP server
  await app.listen(3000);
}

bootstrap();
```

---

# 2. INestApplication Methods

When you call `NestFactory.create()`, it returns an application object.

## Common Application Methods

| Method | Description |
|---|---|
| `listen()` | Starts the HTTP server |
| `close()` | Gracefully shuts down the app |
| `init()` | Initializes the app without starting HTTP listener |
| `use()` | Adds middleware |
| `get()` | Retrieves provider instance |
| `select()` | Selects a module context |
| `enableCors()` | Enables CORS |
| `setGlobalPrefix()` | Adds global route prefix |
| `useGlobalGuards()` | Registers global guards |
| `useGlobalPipes()` | Registers global pipes |
| `useGlobalFilters()` | Registers global exception filters |
| `useGlobalInterceptors()` | Registers global interceptors |
| `enableShutdownHooks()` | Enables graceful shutdown lifecycle hooks |
| `connectMicroservice()` | Connects a microservice to the app |
| `startAllMicroservices()` | Starts connected microservices |

## Example

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Adds /api prefix to all routes
  app.setGlobalPrefix('api');

  // Enables CORS for frontend apps
  app.enableCors();

  // Adds global validation
  app.useGlobalPipes(new ValidationPipe());

  await app.listen(3000);
}
```

---

# 3. Modules

## What is a Module?

A module organizes related controllers, services, and providers.

Every NestJS app has at least one root module.

## Main Decorator

```ts
@Module()
```

## Common Module Properties

| Property | Description |
|---|---|
| `imports` | Other modules this module depends on |
| `controllers` | Controllers owned by this module |
| `providers` | Services/providers available in this module |
| `exports` | Providers made available to other modules |

## Example

```ts
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```

---

# 4. Controllers

## What is a Controller?

A controller handles incoming HTTP requests and returns responses.

## Main Decorator

```ts
@Controller()
```

## Common Controller Decorators

| Decorator | Description |
|---|---|
| `@Controller()` | Defines a controller route prefix |
| `@Get()` | Handles HTTP GET |
| `@Post()` | Handles HTTP POST |
| `@Put()` | Handles HTTP PUT |
| `@Patch()` | Handles HTTP PATCH |
| `@Delete()` | Handles HTTP DELETE |
| `@All()` | Handles all HTTP methods |
| `@HttpCode()` | Sets custom response status |
| `@Header()` | Sets response header |
| `@Redirect()` | Redirects request |

## Example

```ts
import { Controller, Get, Post, Body, Param } from '@nestjs/common';

@Controller('users')
export class UsersController {
  @Get()
  findAll() {
    return ['John', 'Mary'];
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return { id, name: 'John' };
  }

  @Post()
  create(@Body() body: any) {
    return {
      message: 'User created',
      data: body,
    };
  }
}
```

---

# 5. Route Parameter Decorators

## Common Parameter Decorators

| Decorator | Description |
|---|---|
| `@Body()` | Reads request body |
| `@Param()` | Reads route parameters |
| `@Query()` | Reads query string |
| `@Headers()` | Reads request headers |
| `@Req()` | Accesses raw request object |
| `@Res()` | Accesses raw response object |
| `@Ip()` | Gets client IP |
| `@Session()` | Reads session data |

## Example

```ts
@Get(':id')
findUser(
  @Param('id') id: string,
  @Query('includeOrders') includeOrders: string,
  @Headers('authorization') token: string,
) {
  return {
    id,
    includeOrders,
    token,
  };
}
```

---

# 6. Providers / Services

## What is a Provider?

A provider is a class managed by NestJS dependency injection.

Common providers include:

- Services
- Repositories
- Factories
- Helpers
- Database clients

## Main Decorator

```ts
@Injectable()
```

## Example

```ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UsersService {
  private users = [];

  findAll() {
    return this.users;
  }

  create(user: any) {
    this.users.push(user);
    return user;
  }
}
```

## Injecting Service into Controller

```ts
@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll();
  }
}
```

---

# 7. Dependency Injection

## What is Dependency Injection?

Dependency Injection allows NestJS to automatically create and inject classes where needed.

## Example

```ts
@Injectable()
export class OrdersService {
  constructor(private readonly usersService: UsersService) {}

  findOrdersForUser(userId: string) {
    const user = this.usersService.findOne(userId);
    return {
      user,
      orders: [],
    };
  }
}
```

---

# 8. Custom Providers

## Common Provider Types

| Type | Description |
|---|---|
| `useClass` | Use a class as provider |
| `useValue` | Inject static value |
| `useFactory` | Create provider dynamically |
| `useExisting` | Alias existing provider |

## Example: useValue

```ts
@Module({
  providers: [
    {
      provide: 'DATABASE_URL',
      useValue: 'postgres://localhost:5432/app',
    },
  ],
})
export class AppModule {}
```

## Inject Custom Provider

```ts
@Injectable()
export class DatabaseService {
  constructor(@Inject('DATABASE_URL') private readonly dbUrl: string) {}
}
```

---

# 9. Middleware

## What is Middleware?

Middleware runs before route handlers.

Common use cases:

- Logging
- Authentication preprocessing
- Request modification
- Request timing

## Example

```ts
import { Injectable, NestMiddleware } from '@nestjs/common';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: any, res: any, next: Function) {
    console.log(`${req.method} ${req.originalUrl}`);
    next();
  }
}
```

## Apply Middleware

```ts
import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common';

@Module({})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('*');
  }
}
```

---

# 10. Guards

## What is a Guard?

A guard decides whether a request is allowed to continue.

Common use cases:

- Authentication
- Authorization
- Role checks
- Permission checks

## Main Interface

```ts
CanActivate
```

## Example

```ts
import { CanActivate, ExecutionContext, Injectable } from '@nestjs/common';

@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();

    // Simple check for token
    return Boolean(request.headers.authorization);
  }
}
```

## Use Guard

```ts
@UseGuards(AuthGuard)
@Get('profile')
getProfile() {
  return { message: 'Protected route' };
}
```

---

# 11. Pipes

## What is a Pipe?

A pipe transforms or validates incoming data.

Common use cases:

- DTO validation
- Type conversion
- Input sanitization

## Built-in Pipes

| Pipe | Description |
|---|---|
| `ValidationPipe` | Validates DTOs |
| `ParseIntPipe` | Converts string to number |
| `ParseBoolPipe` | Converts string to boolean |
| `ParseArrayPipe` | Parses array values |
| `ParseUUIDPipe` | Validates UUID |
| `DefaultValuePipe` | Provides default value |

## Example

```ts
@Get(':id')
findOne(@Param('id', ParseIntPipe) id: number) {
  return {
    id,
    type: typeof id,
  };
}
```

## Global Validation Pipe

```ts
app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
  }),
);
```

---

# 12. DTOs

## What is a DTO?

DTO means Data Transfer Object.

It defines the shape of incoming request data.

## Example

```ts
import { IsEmail, IsString, MinLength } from 'class-validator';

export class CreateUserDto {
  @IsString()
  name: string;

  @IsEmail()
  email: string;

  @MinLength(8)
  password: string;
}
```

## Use DTO

```ts
@Post()
create(@Body() createUserDto: CreateUserDto) {
  return createUserDto;
}
```

---

# 13. Interceptors

## What is an Interceptor?

An interceptor wraps request/response execution.

Common use cases:

- Logging
- Response transformation
- Performance timing
- Caching
- Error mapping

## Example

```ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from '@nestjs/common';

import { Observable, map } from 'rxjs';

@Injectable()
export class ResponseInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      map(data => ({
        success: true,
        data,
      })),
    );
  }
}
```

## Use Interceptor

```ts
@UseInterceptors(ResponseInterceptor)
@Get()
findAll() {
  return ['user1', 'user2'];
}
```

---

# 14. Exception Filters

## What is an Exception Filter?

Exception filters customize error handling.

## Example

```ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
} from '@nestjs/common';

@Catch(HttpException)
export class HttpErrorFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const response = host.switchToHttp().getResponse();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      message: exception.message,
      timestamp: new Date().toISOString(),
    });
  }
}
```

## Use Filter

```ts
@UseFilters(HttpErrorFilter)
@Get()
findAll() {
  throw new NotFoundException('Users not found');
}
```

---

# 15. Built-in HTTP Exceptions

## Common Exceptions

| Exception | HTTP Status |
|---|---|
| `BadRequestException` | 400 |
| `UnauthorizedException` | 401 |
| `ForbiddenException` | 403 |
| `NotFoundException` | 404 |
| `MethodNotAllowedException` | 405 |
| `NotAcceptableException` | 406 |
| `ConflictException` | 409 |
| `GoneException` | 410 |
| `PayloadTooLargeException` | 413 |
| `UnsupportedMediaTypeException` | 415 |
| `UnprocessableEntityException` | 422 |
| `InternalServerErrorException` | 500 |
| `ServiceUnavailableException` | 503 |

## Example

```ts
@Get(':id')
findOne(@Param('id') id: string) {
  if (!id) {
    throw new BadRequestException('User id is required');
  }

  throw new NotFoundException('User not found');
}
```

---

# 16. Decorators

## Common NestJS Decorators

| Decorator | Purpose |
|---|---|
| `@Module()` | Defines a module |
| `@Controller()` | Defines a controller |
| `@Injectable()` | Defines a provider |
| `@Inject()` | Injects custom provider |
| `@Get()` | GET route |
| `@Post()` | POST route |
| `@Put()` | PUT route |
| `@Patch()` | PATCH route |
| `@Delete()` | DELETE route |
| `@Body()` | Request body |
| `@Param()` | Route parameter |
| `@Query()` | Query parameter |
| `@Headers()` | Request header |
| `@UseGuards()` | Applies guards |
| `@UsePipes()` | Applies pipes |
| `@UseInterceptors()` | Applies interceptors |
| `@UseFilters()` | Applies filters |
| `@SetMetadata()` | Adds custom metadata |

---

# 17. Custom Decorators

## What is a Custom Decorator?

A custom decorator extracts repeated logic from controllers.

## Example

```ts
import { createParamDecorator, ExecutionContext } from '@nestjs/common';

export const CurrentUser = createParamDecorator(
  (data: unknown, context: ExecutionContext) => {
    const request = context.switchToHttp().getRequest();
    return request.user;
  },
);
```

## Use Custom Decorator

```ts
@Get('me')
getMe(@CurrentUser() user: any) {
  return user;
}
```

---

# 18. ExecutionContext

## What is ExecutionContext?

`ExecutionContext` gives access to the current request context.

It works with:

- HTTP
- WebSockets
- Microservices

## Example

```ts
canActivate(context: ExecutionContext): boolean {
  const request = context.switchToHttp().getRequest();

  return request.user?.role === 'admin';
}
```

---

# 19. Lifecycle Hooks

## Common Lifecycle Interfaces

| Hook | Description |
|---|---|
| `OnModuleInit` | Runs after module initialization |
| `OnApplicationBootstrap` | Runs after app bootstrap |
| `OnModuleDestroy` | Runs when module is destroyed |
| `BeforeApplicationShutdown` | Runs before shutdown |
| `OnApplicationShutdown` | Runs during shutdown |

## Example

```ts
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';

@Injectable()
export class DatabaseService implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    console.log('Connect to database');
  }

  async onModuleDestroy() {
    console.log('Close database connection');
  }
}
```

---

# 20. Configuration

## What is ConfigModule?

`ConfigModule` manages environment variables.

## Example

```ts
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
    }),
  ],
})
export class AppModule {}
```

## Use ConfigService

```ts
@Injectable()
export class AppService {
  constructor(private readonly configService: ConfigService) {}

  getDatabaseUrl() {
    return this.configService.get<string>('DATABASE_URL');
  }
}
```

---

# 21. REST API Example

## Controller

```ts
@Controller('products')
export class ProductsController {
  constructor(private readonly productsService: ProductsService) {}

  @Get()
  findAll() {
    return this.productsService.findAll();
  }

  @Post()
  create(@Body() body: any) {
    return this.productsService.create(body);
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.productsService.findOne(id);
  }
}
```

## Service

```ts
@Injectable()
export class ProductsService {
  private products = [];

  findAll() {
    return this.products;
  }

  create(product: any) {
    this.products.push(product);
    return product;
  }

  findOne(id: string) {
    return this.products.find(product => product.id === id);
  }
}
```

---

# 22. Microservices

## What is NestJS Microservice Support?

NestJS can build microservices using transports like:

- TCP
- Redis
- NATS
- MQTT
- Kafka
- RabbitMQ
- gRPC

## Example

```ts
const app = await NestFactory.createMicroservice(AppModule, {
  transport: Transport.TCP,
  options: {
    host: 'localhost',
    port: 3001,
  },
});

await app.listen();
```

## Message Pattern

```ts
@MessagePattern({ cmd: 'sum' })
sum(data: number[]): number {
  return data.reduce((a, b) => a + b, 0);
}
```

---

# 23. WebSockets

## What is a WebSocket Gateway?

A gateway handles real-time communication.

## Example

```ts
import {
  WebSocketGateway,
  SubscribeMessage,
  MessageBody,
} from '@nestjs/websockets';

@WebSocketGateway()
export class ChatGateway {
  @SubscribeMessage('message')
  handleMessage(@MessageBody() data: string): string {
    return `Received: ${data}`;
  }
}
```

---

# 24. Testing

## Common Testing Tools

| Tool | Purpose |
|---|---|
| Jest | Unit testing |
| Supertest | E2E HTTP testing |
| TestingModule | NestJS testing module |

## Unit Test Example

```ts
describe('UsersService', () => {
  let service: UsersService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [UsersService],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should return users', () => {
    expect(service.findAll()).toEqual([]);
  });
});
```

---

# 25. Swagger / OpenAPI

## What is Swagger in NestJS?

Swagger documents REST APIs.

## Example

```ts
const config = new DocumentBuilder()
  .setTitle('Users API')
  .setDescription('User management API')
  .setVersion('1.0')
  .addBearerAuth()
  .build();

const document = SwaggerModule.createDocument(app, config);

SwaggerModule.setup('api-docs', app, document);
```

## DTO Swagger Decorators

```ts
export class CreateUserDto {
  @ApiProperty()
  name: string;

  @ApiProperty()
  email: string;
}
```

---

# 26. Authentication

## Common Auth Tools

| Tool | Purpose |
|---|---|
| Passport | Authentication middleware |
| JWT | Token-based authentication |
| Guards | Protect routes |
| bcrypt | Password hashing |

## JWT Guard Example

```ts
@Injectable()
export class JwtAuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();

    const token = request.headers.authorization;

    return Boolean(token);
  }
}
```

---

# 27. Common NestJS CLI Commands

| Command | Description |
|---|---|
| `nest new app-name` | Creates new project |
| `nest generate module users` | Creates module |
| `nest generate controller users` | Creates controller |
| `nest generate service users` | Creates service |
| `nest generate resource users` | Creates CRUD resource |
| `nest build` | Builds app |
| `nest start` | Starts app |
| `nest start --watch` | Starts app in watch mode |

---

# 28. Senior Interview Summary

## Most Important NestJS Concepts

1. Modules organize the application.
2. Controllers handle HTTP requests.
3. Providers/services contain business logic.
4. Dependency Injection wires classes together.
5. Guards handle authorization.
6. Pipes validate and transform data.
7. Interceptors wrap request/response logic.
8. Filters handle exceptions.
9. Middleware runs before guards and controllers.
10. DTOs define request payload shape.
11. ConfigModule manages environment variables.
12. NestFactory bootstraps the app.
13. Microservices allow event/message-based architecture.
14. Swagger documents APIs.
15. TestingModule supports unit and integration testing.

---

# 29. Common Senior Interview Answer

NestJS is a structured backend framework for Node.js that uses TypeScript, decorators, modules, dependency injection, and providers to build scalable applications. I use controllers for request handling, services for business logic, DTOs and pipes for validation, guards for authorization, interceptors for logging and response transformation, and filters for consistent error handling. For production, I configure environment variables, logging, Swagger documentation, global validation, security middleware, and CI/CD deployment.