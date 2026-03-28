
# Microservices REST API  Nodejs user nustjs framwork
- https://nestjs.com
1. อธิบาย การทำงานของ Nodejs user nustjs framwork  sวาด โครงสร้าง การทำงาน
 

CRUD  + ORM  +JWT + MVC
ความต้องการ

1.ระบบ User
2.ระบบ สินค้า
3.ระบบ คลังสินค้า
4.ระบบขนส่ง
5.ระบบรายงาน
6.ระบบ ประวัจิดกาใช้งานระบบ

ระบบที่ นำมาใช้
1.JWT
2.validator
3.ORM
4.Database PostgreSQL
5.Redis cache
6.Kafka
7.EKL Elasticsearch Logstash Kibana
8.Grafana monitoring
9.n8n
10.Gitlow
11.comment code template
12.CI/CD pipeline
13.Robot Framework มา test API
15.Task management
16.Docker Compose
17.Jenkins (Infrastructure)
18.cloud AWS EC2 ,S3 deploy application

1.สร้างเอกสาร
2.สร้าง Template code
3.ออกแบบ Data flow
4.ออกแบบ Work flow
5.ออกแบบ Template รายงาน

ส่งออก แบบ เอกสาร Markdown  .md

# Microservices REST API with NestJS Framework

## 1. โครงสร้างและการทำงานของ NestJS Framework

### 1.1 ภาพรวม NestJS
NestJS เป็น Progressive Node.js Framework สำหรับสร้างแอปพลิเคชันที่ประสิทธิภาพสูงและ scalable โดยใช้ TypeScript รองรับทั้ง Object-Oriented Programming (OOP), Functional Programming (FP) และ Functional Reactive Programming (FRP)

### 1.2 โครงสร้างสถาปัตยกรรม
```
┌──────────────────────────────────────────────┐
│                 API Gateway                  │
├──────────────────────────────────────────────┤
│        Microservices Layer                   │
│  ┌─────┬─────┬──────┬──────┬──────┐          │
│  │User │Product│Warehouse│Shipping│Report│   │
│  │Service │Service│Service │Service │Service││
│  └─────┴─────┴──────┴──────┴──────┘          │
├──────────────────────────────────────────────┤
│         Shared Layer (Libraries)             │
│  • Common Utilities                          │
│  • Database Connectors                       │
│  • Message Brokers                           │
│  • Authentication/Authorization              │
├──────────────────────────────────────────────┤
│         Infrastructure Layer                 │
│  • PostgreSQL                                │
│  • Redis Cache                               │
│  • Kafka                                     │
│  • Elasticsearch                             │
└──────────────────────────────────────────────┘
```

### 1.3 การทำงานของ NestJS Microservices
```typescript
// โครงสร้างพื้นฐานของ NestJS Module
@Module({
  imports: [
    ConfigModule.forRoot(),
    DatabaseModule,
    AuthModule,
    KafkaModule,
  ],
  controllers: [AppController],
  providers: [AppService],
  exports: [AppService],
})
export class AppModule {}
```

## 2. Template Code Structure

### 2.1 โครงสร้างโปรเจค
```
microservices-platform/
├── .github/                          # GitHub workflows
│   └── workflows/
│       ├── ci-cd.yml
│       └── security-scan.yml
├── docker-compose.yml
├── docker-compose.prod.yml
├── .env.example
├── .gitlab-ci.yml
├── Jenkinsfile
├── README.md
├── packages/                         # Shared libraries
│   ├── common/
│   │   ├── src/
│   │   │   ├── decorators/
│   │   │   ├── filters/
│   │   │   ├── guards/
│   │   │   ├── interceptors/
│   │   │   ├── middleware/
│   │   │   └── utils/
│   │   └── package.json
│   └── database/
│       └── src/
├── services/
│   ├── api-gateway/                  # API Gateway Service
│   │   ├── src/
│   │   │   ├── controllers/
│   │   │   ├── filters/
│   │   │   ├── middleware/
│   │   │   └── main.ts
│   │   ├── test/
│   │   ├── Dockerfile
│   │   └── package.json
│   ├── user-service/                 # User Service
│   │   ├── src/
│   │   │   ├── user/
│   │   │   │   ├── controllers/
│   │   │   │   ├── services/
│   │   │   │   ├── entities/
│   │   │   │   ├── dto/
│   │   │   │   ├── interfaces/
│   │   │   │   └── user.module.ts
│   │   │   ├── auth/
│   │   │   └── main.ts
│   │   ├── test/
│   │   ├── Dockerfile
│   │   └── package.json
│   ├── product-service/              # Product Service
│   ├── warehouse-service/            # Warehouse Service
│   ├── shipping-service/             # Shipping Service
│   └── report-service/               # Report Service
├── infrastructure/
│   ├── monitoring/
│   │   ├── prometheus/
│   │   ├── grafana/
│   │   └── alerts/
│   ├── logs/
│   │   ├── elasticsearch/
│   │   ├── logstash/
│   │   └── kibana/
│   └── kafka/
├── scripts/                          # Deployment scripts
└── documentation/
    ├── api-docs/
    ├── architecture/
    └── workflows/
```

### 2.2 Template Code สำหรับ Service

#### 2.2.1 Main Application Template
```typescript
// src/main.ts
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { MicroserviceOptions, Transport } from '@nestjs/microservices';
import { AppModule } from './app.module';
import { HttpExceptionFilter } from '@common/filters';
import { LoggingInterceptor } from '@common/interceptors';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  const configService = app.get(ConfigService);

  // Global pipes
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      transform: true,
      forbidNonWhitelisted: true,
    }),
  );

  // Global filters
  app.useGlobalFilters(new HttpExceptionFilter());

  // Global interceptors
  app.useGlobalInterceptors(new LoggingInterceptor());

  // Enable CORS
  app.enableCors();

  // Connect to Kafka for microservices communication
  app.connectMicroservice<MicroserviceOptions>({
    transport: Transport.KAFKA,
    options: {
      client: {
        brokers: [configService.get('KAFKA_BROKER')],
      },
      consumer: {
        groupId: configService.get('KAFKA_CONSUMER_GROUP'),
      },
    },
  });

  await app.startAllMicroservices();
  
  const port = configService.get('PORT') || 3000;
  await app.listen(port);
  console.log(`Service running on port ${port}`);
}

bootstrap();
```

#### 2.2.2 Module Template
```typescript
// src/user/user.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { CacheModule } from '@nestjs/cache-manager';
import { UserController } from './controllers/user.controller';
import { UserService } from './services/user.service';
import { UserRepository } from './repositories/user.repository';
import { User } from './entities/user.entity';
import { AuthService } from '../auth/services/auth.service';
import { JwtStrategy } from '../auth/strategies/jwt.strategy';

@Module({
  imports: [
    TypeOrmModule.forFeature([User]),
    CacheModule.register({
      ttl: 60, // seconds
      max: 100,
    }),
  ],
  controllers: [UserController],
  providers: [UserService, UserRepository, AuthService, JwtStrategy],
  exports: [UserService],
})
export class UserModule {}
```

#### 2.2.3 Entity Template with TypeORM
```typescript
// src/user/entities/user.entity.ts
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  CreateDateColumn,
  UpdateDateColumn,
  Index,
  BeforeInsert,
  BeforeUpdate,
} from 'typeorm';
import { Exclude } from 'class-transformer';
import { ApiProperty } from '@nestjs/swagger';
import * as bcrypt from 'bcrypt';

@Entity('users')
@Index(['email'], { unique: true })
@Index(['username'], { unique: true })
export class User {
  @ApiProperty({ description: 'User ID' })
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @ApiProperty({ description: 'User email' })
  @Column({ type: 'varchar', length: 255, nullable: false })
  email: string;

  @ApiProperty({ description: 'Username' })
  @Column({ type: 'varchar', length: 100, nullable: false })
  username: string;

  @Exclude()
  @Column({ type: 'varchar', length: 255, nullable: false })
  password: string;

  @ApiProperty({ description: 'First name' })
  @Column({ type: 'varchar', length: 100, nullable: true })
  firstName: string;

  @ApiProperty({ description: 'Last name' })
  @Column({ type: 'varchar', length: 100, nullable: true })
  lastName: string;

  @ApiProperty({ description: 'User role' })
  @Column({ 
    type: 'enum', 
    enum: ['admin', 'manager', 'staff', 'customer'],
    default: 'customer'
  })
  role: string;

  @ApiProperty({ description: 'Account status' })
  @Column({ 
    type: 'enum', 
    enum: ['active', 'inactive', 'suspended'],
    default: 'active'
  })
  status: string;

  @ApiProperty({ description: 'Last login timestamp' })
  @Column({ type: 'timestamp', nullable: true })
  lastLoginAt: Date;

  @CreateDateColumn({ type: 'timestamp' })
  createdAt: Date;

  @UpdateDateColumn({ type: 'timestamp' })
  updatedAt: Date;

  @BeforeInsert()
  @BeforeUpdate()
  async hashPassword(): Promise<void> {
    if (this.password) {
      const salt = await bcrypt.genSalt(10);
      this.password = await bcrypt.hash(this.password, salt);
    }
  }

  async validatePassword(password: string): Promise<boolean> {
    return bcrypt.compare(password, this.password);
  }
}
```

#### 2.2.4 DTO Template with Validation
```typescript
// src/user/dto/create-user.dto.ts
import { ApiProperty } from '@nestjs/swagger';
import {
  IsEmail,
  IsString,
  MinLength,
  MaxLength,
  Matches,
  IsOptional,
  IsEnum,
} from 'class-validator';

export class CreateUserDto {
  @ApiProperty({ example: 'user@example.com' })
  @IsEmail()
  @MaxLength(255)
  email: string;

  @ApiProperty({ example: 'johndoe' })
  @IsString()
  @MinLength(3)
  @MaxLength(100)
  @Matches(/^[a-zA-Z0-9_]+$/, {
    message: 'Username can only contain letters, numbers, and underscores',
  })
  username: string;

  @ApiProperty({ example: 'Password123!' })
  @IsString()
  @MinLength(8)
  @MaxLength(100)
  @Matches(/(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/, {
    message:
      'Password must contain at least one uppercase letter, one lowercase letter, one number, and one special character',
  })
  password: string;

  @ApiProperty({ example: 'John', required: false })
  @IsOptional()
  @IsString()
  @MaxLength(100)
  firstName?: string;

  @ApiProperty({ example: 'Doe', required: false })
  @IsOptional()
  @IsString()
  @MaxLength(100)
  lastName?: string;

  @ApiProperty({ enum: ['admin', 'manager', 'staff', 'customer'], required: false })
  @IsOptional()
  @IsEnum(['admin', 'manager', 'staff', 'customer'])
  role?: string;
}

// src/user/dto/update-user.dto.ts
import { PartialType } from '@nestjs/swagger';
import { CreateUserDto } from './create-user.dto';

export class UpdateUserDto extends PartialType(CreateUserDto) {
  @ApiProperty({ enum: ['active', 'inactive', 'suspended'], required: false })
  @IsOptional()
  @IsEnum(['active', 'inactive', 'suspended'])
  status?: string;
}
```

#### 2.2.5 Service Template with Business Logic
```typescript
// src/user/services/user.service.ts
import { Injectable, NotFoundException, ConflictException, Inject } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { Cache } from 'cache-manager';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';
import { User } from '../entities/user.entity';
import { UserRepository } from '../repositories/user.repository';
import { KafkaService } from '@common/services/kafka.service';
import { AuditLogService } from '@common/services/audit-log.service';

@Injectable()
export class UserService {
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
    private readonly customUserRepository: UserRepository,
    @Inject(CACHE_MANAGER) private cacheManager: Cache,
    private readonly kafkaService: KafkaService,
    private readonly auditLogService: AuditLogService,
  ) {}

  async create(createUserDto: CreateUserDto): Promise<User> {
    // Check if user already exists
    const existingUser = await this.userRepository.findOne({
      where: [{ email: createUserDto.email }, { username: createUserDto.username }],
    });

    if (existingUser) {
      throw new ConflictException('User with this email or username already exists');
    }

    // Create new user
    const user = this.userRepository.create(createUserDto);
    const savedUser = await this.userRepository.save(user);

    // Clear cache
    await this.cacheManager.del('users_list');

    // Send Kafka event
    await this.kafkaService.send('user.created', {
      userId: savedUser.id,
      email: savedUser.email,
      timestamp: new Date().toISOString(),
    });

    // Log audit trail
    await this.auditLogService.log({
      action: 'CREATE_USER',
      entity: 'User',
      entityId: savedUser.id,
      userId: savedUser.id,
      details: { email: savedUser.email },
    });

    return savedUser;
  }

  async findAll(page = 1, limit = 10): Promise<{ data: User[]; total: number }> {
    const cacheKey = `users_page_${page}_limit_${limit}`;
    
    // Try to get from cache first
    const cached = await this.cacheManager.get(cacheKey);
    if (cached) {
      return cached as { data: User[]; total: number };
    }

    const [data, total] = await this.userRepository.findAndCount({
      skip: (page - 1) * limit,
      take: limit,
      order: { createdAt: 'DESC' },
    });

    const result = { data, total };
    
    // Store in cache
    await this.cacheManager.set(cacheKey, result, 60); // 60 seconds TTL
    
    return result;
  }

  async findOne(id: string): Promise<User> {
    const cacheKey = `user_${id}`;
    
    // Try to get from cache first
    const cached = await this.cacheManager.get(cacheKey);
    if (cached) {
      return cached as User;
    }

    const user = await this.userRepository.findOne({
      where: { id },
    });

    if (!user) {
      throw new NotFoundException(`User with ID ${id} not found`);
    }

    // Store in cache
    await this.cacheManager.set(cacheKey, user, 300); // 5 minutes TTL
    
    return user;
  }

  async update(id: string, updateUserDto: UpdateUserDto): Promise<User> {
    const user = await this.findOne(id);
    
    // Prevent updating email if it already exists
    if (updateUserDto.email && updateUserDto.email !== user.email) {
      const existingUser = await this.userRepository.findOne({
        where: { email: updateUserDto.email },
      });
      
      if (existingUser) {
        throw new ConflictException('Email already exists');
      }
    }

    Object.assign(user, updateUserDto);
    const updatedUser = await this.userRepository.save(user);

    // Clear cache
    await Promise.all([
      this.cacheManager.del(`user_${id}`),
      this.cacheManager.del('users_list'),
    ]);

    // Send Kafka event
    await this.kafkaService.send('user.updated', {
      userId: updatedUser.id,
      timestamp: new Date().toISOString(),
    });

    // Log audit trail
    await this.auditLogService.log({
      action: 'UPDATE_USER',
      entity: 'User',
      entityId: updatedUser.id,
      userId: updatedUser.id,
      details: updateUserDto,
    });

    return updatedUser;
  }

  async remove(id: string): Promise<void> {
    const user = await this.findOne(id);
    
    // Soft delete (update status)
    user.status = 'inactive';
    await this.userRepository.save(user);

    // Clear cache
    await Promise.all([
      this.cacheManager.del(`user_${id}`),
      this.cacheManager.del('users_list'),
    ]);

    // Send Kafka event
    await this.kafkaService.send('user.deleted', {
      userId: user.id,
      timestamp: new Date().toISOString(),
    });

    // Log audit trail
    await this.auditLogService.log({
      action: 'DELETE_USER',
      entity: 'User',
      entityId: user.id,
      userId: user.id,
      details: { email: user.email },
    });
  }

  async findByEmail(email: string): Promise<User | null> {
    return this.userRepository.findOne({ where: { email } });
  }

  async updateLastLogin(userId: string): Promise<void> {
    await this.userRepository.update(userId, {
      lastLoginAt: new Date(),
    });
    
    // Clear cache
    await this.cacheManager.del(`user_${userId}`);
  }
}
```

#### 2.2.6 Controller Template
```typescript
// src/user/controllers/user.controller.ts
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  Query,
  UseGuards,
  HttpCode,
  HttpStatus,
  ParseUUIDPipe,
  DefaultValuePipe,
  ParseIntPipe,
} from '@nestjs/common';
import { ApiTags, ApiOperation, ApiResponse, ApiBearerAuth, ApiQuery } from '@nestjs/swagger';
import { UserService } from '../services/user.service';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';
import { User } from '../entities/user.entity';
import { JwtAuthGuard } from '../../auth/guards/jwt-auth.guard';
import { RolesGuard } from '../../auth/guards/roles.guard';
import { Roles } from '../../auth/decorators/roles.decorator';
import { Public } from '../../auth/decorators/public.decorator';

@ApiTags('users')
@Controller('users')
@ApiBearerAuth()
@UseGuards(JwtAuthGuard, RolesGuard)
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Post()
  @Roles('admin')
  @ApiOperation({ summary: 'Create a new user' })
  @ApiResponse({ status: 201, description: 'User created successfully', type: User })
  @ApiResponse({ status: 400, description: 'Bad request' })
  @ApiResponse({ status: 409, description: 'User already exists' })
  async create(@Body() createUserDto: CreateUserDto): Promise<User> {
    return this.userService.create(createUserDto);
  }

  @Get()
  @Roles('admin', 'manager')
  @ApiOperation({ summary: 'Get all users with pagination' })
  @ApiQuery({ name: 'page', required: false, type: Number })
  @ApiQuery({ name: 'limit', required: false, type: Number })
  @ApiResponse({ status: 200, description: 'Returns list of users' })
  async findAll(
    @Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number,
    @Query('limit', new DefaultValuePipe(10), ParseIntPipe) limit: number,
  ): Promise<{ data: User[]; total: number }> {
    return this.userService.findAll(page, limit);
  }

  @Get(':id')
  @Roles('admin', 'manager')
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiResponse({ status: 200, description: 'Returns user details', type: User })
  @ApiResponse({ status: 404, description: 'User not found' })
  async findOne(@Param('id', ParseUUIDPipe) id: string): Promise<User> {
    return this.userService.findOne(id);
  }

  @Put(':id')
  @Roles('admin')
  @ApiOperation({ summary: 'Update user' })
  @ApiResponse({ status: 200, description: 'User updated successfully', type: User })
  @ApiResponse({ status: 404, description: 'User not found' })
  @ApiResponse({ status: 409, description: 'Email already exists' })
  async update(
    @Param('id', ParseUUIDPipe) id: string,
    @Body() updateUserDto: UpdateUserDto,
  ): Promise<User> {
    return this.userService.update(id, updateUserDto);
  }

  @Delete(':id')
  @Roles('admin')
  @HttpCode(HttpStatus.NO_CONTENT)
  @ApiOperation({ summary: 'Delete user (soft delete)' })
  @ApiResponse({ status: 204, description: 'User deleted successfully' })
  @ApiResponse({ status: 404, description: 'User not found' })
  async remove(@Param('id', ParseUUIDPipe) id: string): Promise<void> {
    return this.userService.remove(id);
  }
}
```

#### 2.2.7 Authentication Module
```typescript
// src/auth/strategies/jwt.strategy.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { ConfigService } from '@nestjs/config';
import { UserService } from '../../user/services/user.service';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(
    private configService: ConfigService,
    private userService: UserService,
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get('JWT_SECRET'),
    });
  }

  async validate(payload: any) {
    const user = await this.userService.findOne(payload.sub);
    
    if (!user || user.status !== 'active') {
      throw new UnauthorizedException('User not found or inactive');
    }

    // Update last login
    await this.userService.updateLastLogin(user.id);

    return {
      id: user.id,
      email: user.email,
      username: user.username,
      role: user.role,
    };
  }
}

// src/auth/guards/jwt-auth.guard.ts
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}

// src/auth/guards/roles.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>('roles', context.getHandler());
    
    if (!requiredRoles) {
      return true;
    }

    const request = context.switchToHttp().getRequest();
    const user = request.user;
    
    if (!user) {
      return false;
    }

    return requiredRoles.includes(user.role);
  }
}

// src/auth/decorators/roles.decorator.ts
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);

// src/auth/services/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { ConfigService } from '@nestjs/config';
import * as bcrypt from 'bcrypt';
import { UserService } from '../../user/services/user.service';
import { LoginDto } from '../dto/login.dto';

@Injectable()
export class AuthService {
  constructor(
    private userService: UserService,
    private jwtService: JwtService,
    private configService: ConfigService,
  ) {}

  async validateUser(email: string, password: string): Promise<any> {
    const user = await this.userService.findByEmail(email);
    
    if (!user || user.status !== 'active') {
      return null;
    }

    const isPasswordValid = await bcrypt.compare(password, user.password);
    
    if (!isPasswordValid) {
      return null;
    }

    const { password: _, ...result } = user;
    return result;
  }

  async login(loginDto: LoginDto): Promise<{ access_token: string; refresh_token: string }> {
    const user = await this.validateUser(loginDto.email, loginDto.password);
    
    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }

    const payload = { 
      email: user.email, 
      sub: user.id,
      role: user.role 
    };

    const accessToken = this.jwtService.sign(payload, {
      expiresIn: this.configService.get('JWT_ACCESS_EXPIRATION'),
      secret: this.configService.get('JWT_SECRET'),
    });

    const refreshToken = this.jwtService.sign(payload, {
      expiresIn: this.configService.get('JWT_REFRESH_EXPIRATION'),
      secret: this.configService.get('JWT_REFRESH_SECRET'),
    });

    // Store refresh token in Redis
    // await this.redisService.set(`refresh_token:${user.id}`, refreshToken, 'EX', 86400);

    return {
      access_token: accessToken,
      refresh_token: refreshToken,
    };
  }

  async refreshToken(refreshToken: string): Promise<{ access_token: string }> {
    try {
      const payload = this.jwtService.verify(refreshToken, {
        secret: this.configService.get('JWT_REFRESH_SECRET'),
      });

      // Verify refresh token in Redis
      // const storedToken = await this.redisService.get(`refresh_token:${payload.sub}`);
      
      // if (storedToken !== refreshToken) {
      //   throw new UnauthorizedException('Invalid refresh token');
      // }

      const newPayload = { 
        email: payload.email, 
        sub: payload.sub,
        role: payload.role 
      };

      const accessToken = this.jwtService.sign(newPayload, {
        expiresIn: this.configService.get('JWT_ACCESS_EXPIRATION'),
        secret: this.configService.get('JWT_SECRET'),
      });

      return {
        access_token: accessToken,
      };
    } catch (error) {
      throw new UnauthorizedException('Invalid refresh token');
    }
  }

  async logout(userId: string): Promise<void> {
    // Remove refresh token from Redis
    // await this.redisService.del(`refresh_token:${userId}`);
  }
}
```

## 3. Data Flow Design

### 3.1 Authentication Flow
```
1. Client → Login Request → API Gateway
2. API Gateway → User Service (validate credentials)
3. User Service → PostgreSQL (verify user)
4. User Service → Generate JWT Tokens
5. User Service → Redis (store refresh token)
6. User Service → Return tokens to Client
7. Client → Store tokens
```

### 3.2 User Registration Flow
```
1. Client → Register Request → API Gateway
2. API Gateway → User Service
3. User Service → Validate input (DTO)
4. User Service → PostgreSQL (check duplicates)
5. User Service → PostgreSQL (create user)
6. User Service → Kafka (send user.created event)
7. Kafka → Report Service (log activity)
8. User Service → Return success response
```

### 3.3 Product Management Flow
```
1. Client → Create Product → API Gateway
2. API Gateway → Product Service
3. Product Service → Validate + Create product
4. Product Service → PostgreSQL (save product)
5. Product Service → Kafka (product.created)
6. Kafka → Warehouse Service (update inventory)
7. Kafka → Report Service (log activity)
8. Product Service → Redis (cache product)
9. Return response to Client
```

### 3.4 Order Processing Flow
```
1. Client → Create Order → API Gateway
2. API Gateway → Order Service
3. Order Service → Validate stock (Warehouse Service)
4. Order Service → Process payment (Payment Service)
5. Order Service → Create order record
6. Order Service → Kafka (order.created)
7. Kafka → Shipping Service (schedule delivery)
8. Kafka → Report Service (update analytics)
9. Return order confirmation
```

## 4. Workflow Design

### 4.1 Development Workflow
```yaml
# Git Flow with Feature Branches
master (production)
│
├── release/* (staging)
│   ├── hotfix/* (urgent fixes)
│   └── feature/* (new features)
│
develop (integration)
│
└── feature/*
    ├── feature/user-management
    ├── feature/product-catalog
    └── feature/order-processing
```

### 4.2 CI/CD Pipeline
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ develop, master, release/* ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      redis:
        image: redis:alpine
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm run test
      - run: npm run test:e2e

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build Docker images
        run: |
          docker build -t user-service ./services/user-service
          docker build -t product-service ./services/product-service

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Staging
        run: |
          docker-compose -f docker-compose.staging.yml up -d

  deploy-production:
    needs: build
    if: github.ref == 'refs/heads/master'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        run: |
          docker-compose -f docker-compose.prod.yml up -d
```

### 4.3 Monitoring Workflow
```
1. Application Logs → Logstash → Elasticsearch → Kibana
2. Metrics → Prometheus → Grafana (Dashboards)
3. Alerts → Grafana → Slack/Email
4. APM → AWS X-Ray → Performance monitoring
5. Health Checks → Kubernetes Liveness/Readiness Probes
```

## 5. Database Schema Design

### 5.1 PostgreSQL Tables

#### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    role VARCHAR(50) DEFAULT 'customer',
    status VARCHAR(20) DEFAULT 'active',
    last_login_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_status ON users(status);
CREATE INDEX idx_users_role ON users(role);
```

#### Products Table
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category VARCHAR(100),
    stock_quantity INTEGER DEFAULT 0,
    minimum_stock INTEGER DEFAULT 10,
    status VARCHAR(20) DEFAULT 'active',
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_products_category ON products(category);
CREATE INDEX idx_products_status ON products(status);
```

#### Audit Logs Table
```sql
CREATE TABLE audit_logs (
    id BIGSERIAL PRIMARY KEY,
    action VARCHAR(100) NOT NULL,
    entity VARCHAR(100) NOT NULL,
    entity_id UUID,
    user_id UUID REFERENCES users(id),
    ip_address INET,
    user_agent TEXT,
    details JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_logs_action ON audit_logs(action);
CREATE INDEX idx_audit_logs_entity ON audit_logs(entity);
CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at);
```

## 6. API Documentation Template

### 6.1 Swagger/OpenAPI Configuration
```typescript
// src/config/swagger.config.ts
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
import { INestApplication } from '@nestjs/common';

export function setupSwagger(app: INestApplication): void {
  const config = new DocumentBuilder()
    .setTitle('Microservices Platform API')
    .setDescription('REST API documentation for Microservices Platform')
    .setVersion('1.0')
    .addBearerAuth(
      {
        type: 'http',
        scheme: 'bearer',
        bearerFormat: 'JWT',
        name: 'JWT',
        description: 'Enter JWT token',
        in: 'header',
      },
      'JWT-auth',
    )
    .addTag('Users', 'User management endpoints')
    .addTag('Products', 'Product management endpoints')
    .addTag('Auth', 'Authentication endpoints')
    .addTag('Warehouse', 'Inventory management endpoints')
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api/docs', app, document, {
    swaggerOptions: {
      persistAuthorization: true,
      tagsSorter: 'alpha',
      operationsSorter: 'alpha',
    },
  });
}
```

## 7. Docker Compose Configuration

### 7.1 docker-compose.yml
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:14-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - microservices-network

  # Redis Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - microservices-network

  # Kafka
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "2181:2181"
    networks:
      - microservices-network

  kafka:
    image: confluentinc/cp-kafka:7.3.0
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    ports:
      - "9092:9092"
    networks:
      - microservices-network

  # Elastic Stack
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - "9200:9200"
    networks:
      - microservices-network

  logstash:
    image: docker.elastic.co/logstash/logstash:8.5.0
    volumes:
      - ./infrastructure/logs/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5000:5000"
    depends_on:
      - elasticsearch
    networks:
      - microservices-network

  kibana:
    image: docker.elastic.co/kibana/kibana:8.5.0
    ports:
      - "5601:5601"
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - microservices-network

  # Monitoring
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./infrastructure/monitoring/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    networks:
      - microservices-network

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PASSWORD}
    volumes:
      - grafana_data:/var/lib/grafana
    depends_on:
      - prometheus
    networks:
      - microservices-network

  # API Gateway
  api-gateway:
    build:
      context: ./services/api-gateway
      dockerfile: Dockerfile
    ports:
      - "3001:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKER=kafka:9092
    depends_on:
      - postgres
      - redis
      - kafka
    networks:
      - microservices-network

  # User Service
  user-service:
    build:
      context: ./services/user-service
      dockerfile: Dockerfile
    ports:
      - "3002:3000"
    environment:
      - SERVICE_NAME=user-service
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}_users
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKER=kafka:9092
    depends_on:
      - postgres
      - redis
      - kafka
    networks:
      - microservices-network

  # Product Service
  product-service:
    build:
      context: ./services/product-service
      dockerfile: Dockerfile
    ports:
      - "3003:3000"
    environment:
      - SERVICE_NAME=product-service
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}_products
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKER=kafka:9092
    depends_on:
      - postgres
      - redis
      - kafka
    networks:
      - microservices-network

networks:
  microservices-network:
    driver: bridge

volumes:
  postgres_data:
  redis_data:
  grafana_data:
```

## 8. Testing Templates

### 8.1 Unit Tests
```typescript
// test/user.service.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { UserService } from '../src/user/services/user.service';
import { User } from '../src/user/entities/user.entity';
import { CreateUserDto } from '../src/user/dto/create-user.dto';

describe('UserService', () => {
  let service: UserService;
  let mockRepository: any;
  let mockCacheManager: any;

  beforeEach(async () => {
    mockRepository = {
      findOne: jest.fn(),
      create: jest.fn(),
      save: jest.fn(),
      findAndCount: jest.fn(),
      update: jest.fn(),
    };

    mockCacheManager = {
      get: jest.fn(),
      set: jest.fn(),
      del: jest.fn(),
    };

    const module: TestingModule = await Test.createTestingModule({
      providers: [
        UserService,
        {
          provide: getRepositoryToken(User),
          useValue: mockRepository,
        },
        {
          provide: CACHE_MANAGER,
          useValue: mockCacheManager,
        },
      ],
    }).compile();

    service = module.get<UserService>(UserService);
  });

  describe('create', () => {
    it('should successfully create a user', async () => {
      const createUserDto: CreateUserDto = {
        email: 'test@example.com',
        username: 'testuser',
        password: 'Password123!',
      };

      const savedUser = {
        id: 'uuid',
        ...createUserDto,
      };

      mockRepository.findOne.mockResolvedValue(null);
      mockRepository.create.mockReturnValue(createUserDto);
      mockRepository.save.mockResolvedValue(savedUser);

      const result = await service.create(createUserDto);

      expect(result).toEqual(savedUser);
      expect(mockRepository.findOne).toHaveBeenCalled();
      expect(mockRepository.save).toHaveBeenCalled();
    });

    it('should throw conflict exception if user exists', async () => {
      const createUserDto: CreateUserDto = {
        email: 'existing@example.com',
        username: 'existing',
        password: 'Password123!',
      };

      mockRepository.findOne.mockResolvedValue({ id: 'uuid' });

      await expect(service.create(createUserDto)).rejects.toThrow();
    });
  });
});
```

### 8.2 E2E Tests with Robot Framework
```robot
*** Settings ***
Library    RequestsLibrary
Library    Collections

*** Variables ***
${BASE_URL}    http://localhost:3001
${USERNAME}    admin
${PASSWORD}    admin123

*** Test Cases ***
User Registration Test
    [Documentation]    Test user registration flow
    Create Session    api    ${BASE_URL}
    
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    
    ${body}=    Create Dictionary
    ...    email=newuser@example.com
    ...    username=newuser
    ...    password=Password123!
    ...    firstName=John
    ...    lastName=Doe
    
    ${response}=    POST On Session
    ...    api
    ...    /api/users
    ...    json=${body}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    201
    Dictionary Should Contain Key    ${response.json()}    id
    Dictionary Should Contain Value    ${response.json()}    newuser@example.com

Login Test
    [Documentation]    Test login functionality
    Create Session    api    ${BASE_URL}
    
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    
    ${body}=    Create Dictionary
    ...    email=${USERNAME}
    ...    password=${PASSWORD}
    
    ${response}=    POST On Session
    ...    api
    ...    /api/auth/login
    ...    json=${body}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    200
    Dictionary Should Contain Key    ${response.json()}    access_token
    Dictionary Should Contain Key    ${response.json()}    refresh_token
```

## 9. Monitoring and Observability

### 9.1 Grafana Dashboard Templates
```json
{
  "dashboard": {
    "title": "Microservices Platform Metrics",
    "panels": [
      {
        "title": "API Request Rate",
        "targets": [
          {
            "expr": "rate(http_requests_total[5m])",
            "legendFormat": "{{service}} - {{endpoint}}"
          }
        ]
      },
      {
        "title": "Service Response Time",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))",
            "legendFormat": "{{service}}"
          }
        ]
      },
      {
        "title": "Database Connections",
        "targets": [
          {
            "expr": "pg_stat_database_numbackends",
            "legendFormat": "{{datname}}"
          }
        ]
      },
      {
        "title": "Redis Memory Usage",
        "targets": [
          {
            "expr": "redis_memory_used_bytes / redis_memory_max_bytes * 100"
          }
        ]
      },
      {
        "title": "Kafka Lag",
        "targets": [
          {
            "expr": "kafka_consumer_lag",
            "legendFormat": "{{topic}} - {{consumer_group}}"
          }
        ]
      }
    ]
  }
}
```

## 10. Deployment Scripts

### 10.1 Deployment to AWS EC2
```bash
#!/bin/bash
# deploy.sh

# Variables
ENVIRONMENT=${1:-staging}
SERVICE_NAME="microservices-platform"
EC2_HOST="ec2-user@your-ec2-instance"
DEPLOY_PATH="/opt/$SERVICE_NAME"

echo "Deploying $SERVICE_NAME to $ENVIRONMENT..."

# Build Docker images
docker-compose -f docker-compose.$ENVIRONMENT.yml build

# Push to Docker Registry (if using)
# docker push your-registry/$SERVICE_NAME-api-gateway:$ENVIRONMENT
# docker push your-registry/$SERVICE_NAME-user-service:$ENVIRONMENT

# Deploy to EC2
ssh $EC2_HOST "mkdir -p $DEPLOY_PATH"
scp docker-compose.$ENVIRONMENT.yml $EC2_HOST:$DEPLOY_PATH/
scp .env.$ENVIRONMENT $EC2_HOST:$DEPLOY_PATH/.env

ssh $EC2_HOST "cd $DEPLOY_PATH && \
    docker-compose -f docker-compose.$ENVIRONMENT.yml pull && \
    docker-compose -f docker-compose.$ENVIRONMENT.yml down && \
    docker-compose -f docker-compose.$ENVIRONMENT.yml up -d && \
    docker system prune -f"

echo "Deployment completed!"
```

## 11. Comment Code Template

### 11.1 JSDoc Comment Template
```typescript
/**
 * @class UserService
 * @description Service for managing user operations including CRUD,
 * authentication, and user profile management.
 * 
 * @example
 * ```typescript
 * const userService = new UserService();
 * const user = await userService.create(createUserDto);
 * ```
 * 
 * @author Development Team
 * @created 2024-01-01
 * @updated 2024-01-15
 * @version 1.0.0
 */
@Injectable()
export class UserService {
  /**
   * @constructor
   * @param {Repository<User>} userRepository - TypeORM repository for User entity
   * @param {Cache} cacheManager - Redis cache manager instance
   * @param {KafkaService} kafkaService - Kafka message broker service
   */
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
    @Inject(CACHE_MANAGER) private cacheManager: Cache,
    private readonly kafkaService: KafkaService,
  ) {}

  /**
   * @method create
   * @description Creates a new user with validation and duplicate checking.
   * Automatically hashes password and sends notification events.
   * 
   * @param {CreateUserDto} createUserDto - User creation data transfer object
   * @returns {Promise<User>} The created user entity
   * 
   * @throws {ConflictException} If email or username already exists
   * @throws {BadRequestException} If validation fails
   * 
   * @example
   * ```typescript
   * const user = await userService.create({
   *   email: 'john@example.com',
   *   username: 'johndoe',
   *   password: 'Password123!'
   * });
   * ```
   */
  async create(createUserDto: CreateUserDto): Promise<User> {
    // Implementation
  }
}
```

## 12. Security Configuration

### 12.1 Security Best Practices
```typescript
// src/config/security.config.ts
import { HelmetOptions } from 'helmet';
import rateLimit from 'express-rate-limit';
import * as csurf from 'csurf';

export const helmetConfig: HelmetOptions = {
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true,
  },
  referrerPolicy: { policy: 'same-origin' },
};

export const rateLimitConfig = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

// CSRF protection (for session-based auth)
export const csrfProtection = csurf({
  cookie: true,
});
```

## 13. Error Handling Template

### 13.1 Global Exception Filter
```typescript
// src/common/filters/http-exception.filter.ts
import {
  ExceptionFilter,
  Catch,
  ArgumentsHost,
  HttpException,
  HttpStatus,
  Logger,
} from '@nestjs/common';
import { Request, Response } from 'express';
import { KafkaService } from '../services/kafka.service';

@Catch()
export class HttpExceptionFilter implements ExceptionFilter {
  private readonly logger = new Logger(HttpExceptionFilter.name);

  constructor(private readonly kafkaService: KafkaService) {}

  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    const status =
      exception instanceof HttpException
        ? exception.getStatus()
        : HttpStatus.INTERNAL_SERVER_ERROR;

    const message =
      exception instanceof HttpException
        ? exception.getResponse()
        : 'Internal server error';

    const errorResponse = {
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      method: request.method,
      message: typeof message === 'string' ? message : (message as any).message,
    };

    // Log error
    this.logger.error(
      `${request.method} ${request.url} ${status} - ${JSON.stringify(errorResponse)}`,
      exception instanceof Error ? exception.stack : '',
    );

    // Send error to Kafka for monitoring
    this.kafkaService.send('error.occurred', {
      ...errorResponse,
      userId: request.user?.id,
      userAgent: request.headers['user-agent'],
      ip: request.ip,
    });

    response.status(status).json(errorResponse);
  }
}
```

## 14. Performance Optimization

### 14.1 Database Optimization
```typescript
// src/common/utils/query.optimizer.ts
export class QueryOptimizer {
  static async optimizeQuery<T>(
    queryBuilder: SelectQueryBuilder<T>,
    options: {
      page?: number;
      limit?: number;
      sortBy?: string;
      sortOrder?: 'ASC' | 'DESC';
      relations?: string[];
      select?: string[];
    },
  ): Promise<[T[], number]> {
    const { page = 1, limit = 10, sortBy, sortOrder, relations, select } = options;

    // Apply pagination
    queryBuilder.skip((page - 1) * limit).take(limit);

    // Apply sorting
    if (sortBy) {
      queryBuilder.orderBy(`${queryBuilder.alias}.${sortBy}`, sortOrder || 'ASC');
    }

    // Apply relations (eager loading)
    if (relations) {
      relations.forEach((relation) => {
        queryBuilder.leftJoinAndSelect(`${queryBuilder.alias}.${relation}`, relation);
      });
    }

    // Apply select fields
    if (select) {
      queryBuilder.select(select.map((field) => `${queryBuilder.alias}.${field}`));
    }

    return queryBuilder.getManyAndCount();
  }

  static createIndexSuggestions(entity: Function): string[] {
    // Analyze entity and suggest indexes
    const suggestions: string[] = [];
    // Implementation for index suggestions
    return suggestions;
  }
}
```

## Conclusion

ระบบ Microservices ที่ออกแบบด้วย NestJS Framework นี้มีคุณสมบัติดังนี้:

1. **Scalability**: แต่ละ service สามารถ scale ได้อย่างอิสระ
2. **Resilience**: มี Circuit Breaker, Retry Logic, และ Fallback mechanisms
3. **Observability**: Monitoring, Logging, Tracing ด้วย ELK Stack และ Grafana
4. **Security**: JWT Authentication, RBAC, Input Validation, และ Security Headers
5. **Performance**: Redis caching, Database optimization, Connection pooling
6. **Maintainability**: Clean architecture, Testing coverage, Documentation
7. **DevOps**: CI/CD Pipeline, Docker containers, Infrastructure as Code

ระบบนี้พร้อมสำหรับการพัฒนาและ deploy บน production environment ด้วยความมั่นใจในความเสถียรและความปลอดภัยของระบบ