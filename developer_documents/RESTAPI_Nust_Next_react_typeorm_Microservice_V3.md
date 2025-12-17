# Microservices REST API next+ next+react
- https://nestjs.com  backend
- https://next.com/   fontend end
- https://react.dev/
- 
1. อธิบาย การทำงานของ nestjs วาด โครงสร้าง การทำงาน
2. อธิบาย การทำงานของ next วาด โครงสร้าง การทำงาน
3. อธิบาย การทำงานของ react วาด โครงสร้าง การทำงาน
 
CRUD  + ORM  +JWT + MVC
ความต้องการ

1.ระบบ User
2.ระบบ สินค้า
3.ระบบ คลังสินค้า
4.ระบบขนส่ง
5.ระบบรายงาน
6.ระบบ ประวัจิดกาใช้งานระบบ

ระบบที่ นำมาใช้
 Modules mvc design modules mvc design MVC (Model-View-Controller) is a fundamental software design pattern that separates an application into three interconnected parts: the Model (data/logic), the View (user interface/presentation), and the Controller (handles input, manages flow, coordinates Model & View) to promote organization, reusability, and maintainability through separation of concerns. While traditional MVC focuses on these core parts 

1.JWT Resash tiken  using a short-lived access token (10 minutes) for API access and a long-lived refresh token (30 days)   to auto singin from fontend
2.validator Dto
3.TypeORM
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
6.Git flow 
7.ออกแบบ ci/cd jenkins docker aws cloud

ส่งออก แบบ เอกสาร Markdown  .md

# ---------------------------- 

# Microservices REST API Architecture - Comprehensive Guide

## 1. โครงสร้างและการทำงานของ NestJS, Next.js และ React

### 1.1 NestJS Architecture & Flow

**โครงสร้างโมดูลแบบ Microservices:**
```
src/
├── main.ts                          # Application entry point
├── app.module.ts                    # Root module
├── config/                          # Configuration files
├── common/                          # Shared utilities
│   ├── filters/                     # Exception filters
│   ├── interceptors/                # Request/Response interceptors
│   ├── middleware/                  # Global middleware
│   ├── decorators/                  # Custom decorators
│   └── pipes/                       # Validation pipes
├── modules/                         # Business modules
│   ├── auth/                        # Authentication module
│   ├── users/                       # User management
│   ├── products/                    # Product management
│   └── shared/                      # Shared modules
└── database/                        # Database configuration
```

**Flow การทำงาน:**
```typescript
// Request Lifecycle
Client Request → Middleware → Guards → Interceptors → Pipes → Controller → Service → Repository → Database

// Example Middleware
@Injectable()
export class LoggingMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log(`Request ${req.method} ${req.url}`);
    next();
  }
}

// Example Guard
@Injectable()
export class JwtAuthGuard implements CanActivate {
  constructor(private jwtService: JwtService) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const token = this.extractToken(request);
    
    if (!token) throw new UnauthorizedException();
    
    try {
      const payload = await this.jwtService.verifyAsync(token);
      request.user = payload;
    } catch {
      throw new UnauthorizedException();
    }
    
    return true;
  }
}
```

### 1.2 Next.js Architecture (App Router)

**โครงสร้างใหม่:**
```
app/
├── (auth)/                          # Auth route group
│   ├── login/
│   │   ├── page.tsx
│   │   └── layout.tsx
│   └── register/
├── (dashboard)/                     # Dashboard route group
│   ├── layout.tsx                   # Shared layout
│   ├── page.tsx                     # Dashboard home
│   └── users/
│       └── page.tsx
├── api/                             # API routes
│   ├── auth/
│   │   └── route.ts
│   └── middleware.ts
├── components/                      # Reusable components
├── lib/                             # Utilities & config
├── store/                           # State management
└── styles/                          # Global styles
```

**Data Fetching Strategies:**
```typescript
// Server Components (default)
export default async function UsersPage() {
  const users = await getUsers(); // Direct DB access
  
  return (
    <div>
      {users.map(user => <UserCard key={user.id} user={user} />)}
    </div>
  );
}

// Client Components with SWR
'use client';
import useSWR from 'swr';

export default function UsersList() {
  const { data: users, error } = useSWR('/api/users', fetcher);
  
  if (error) return <div>Error loading users</div>;
  if (!users) return <div>Loading...</div>;
  
  return (
    <div>
      {users.map(user => <UserCard key={user.id} user={user} />)}
    </div>
  );
}
```

### 1.3 React Component Architecture

**Atomic Design Pattern:**
```typescript
// Atoms (พื้นฐาน)
export const Button = ({ children, onClick, variant = 'primary' }) => (
  <button className={`btn btn-${variant}`} onClick={onClick}>
    {children}
  </button>
);

// Molecules (กลุ่มของ Atoms)
export const SearchBar = ({ onSearch }) => (
  <div className="search-bar">
    <Input placeholder="Search..." />
    <Button onClick={onSearch}>Search</Button>
  </div>
);

// Organisms (กลุ่มของ Molecules)
export const UserTable = ({ users, onEdit, onDelete }) => (
  <div className="user-table">
    <Table>
      <TableHead>
        <TableRow>
          <TableCell>Name</TableCell>
          <TableCell>Email</TableCell>
          <TableCell>Actions</TableCell>
        </TableRow>
      </TableHead>
      <TableBody>
        {users.map(user => (
          <UserRow 
            key={user.id} 
            user={user}
            onEdit={onEdit}
            onDelete={onDelete}
          />
        ))}
      </TableBody>
    </Table>
  </div>
);

// Custom Hooks
export const useUsers = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetchUsers().then(setUsers).finally(() => setLoading(false));
  }, []);
  
  return { users, loading };
};
```

## 2. CRUD Operations ด้วย TypeORM

### 2.1 Entity Definitions

```typescript
// Base Entity
@CreateDateColumn()
createdAt: Date;

@UpdateDateColumn()
updatedAt: Date;

@DeleteDateColumn()
deletedAt: Date;

// User Entity
@Entity('users')
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  @IsEmail()
  email: string;

  @Column()
  @IsNotEmpty()
  username: string;

  @Column({ select: false })
  passwordHash: string;

  @Column({ default: true })
  isActive: boolean;

  @OneToMany(() => AuditLog, audit => audit.user)
  auditLogs: AuditLog[];

  @BeforeInsert()
  async hashPassword() {
    this.passwordHash = await bcrypt.hash(this.passwordHash, 10);
  }
}

// Product Entity with Relations
@Entity('products')
export class Product {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  sku: string;

  @Column()
  name: string;

  @Column('text')
  description: string;

  @Column('decimal', { precision: 10, scale: 2 })
  price: number;

  @OneToOne(() => Inventory, inventory => inventory.product)
  inventory: Inventory;

  @ManyToMany(() => Category, category => category.products)
  @JoinTable()
  categories: Category[];
}

// Repository Pattern
@Injectable()
export class UsersRepository {
  constructor(
    @InjectRepository(User)
    private repository: Repository<User>,
  ) {}

  async findByEmail(email: string): Promise<User | null> {
    return this.repository.findOne({ 
      where: { email },
      relations: ['roles']
    });
  }

  async findActiveUsers(): Promise<User[]> {
    return this.repository
      .createQueryBuilder('user')
      .where('user.isActive = :isActive', { isActive: true })
      .leftJoinAndSelect('user.roles', 'roles')
      .getMany();
  }

  async softDelete(id: string): Promise<void> {
    await this.repository.softDelete(id);
  }

  async paginate(options: PaginationOptions): Promise<Pagination<User>> {
    const [data, total] = await this.repository.findAndCount({
      skip: (options.page - 1) * options.limit,
      take: options.limit,
      order: { [options.sortBy]: options.sortOrder },
    });

    return {
      data,
      total,
      page: options.page,
      limit: options.limit,
      totalPages: Math.ceil(total / options.limit),
    };
  }
}
```

### 2.2 CRUD Service Implementation

```typescript
// Base Service
export abstract class CrudService<T, CreateDto, UpdateDto> {
  constructor(protected repository: Repository<T>) {}

  async create(createDto: CreateDto): Promise<T> {
    const entity = this.repository.create(createDto);
    return await this.repository.save(entity);
  }

  async findAll(options?: FindManyOptions<T>): Promise<T[]> {
    return await this.repository.find(options);
  }

  async findOne(id: string, relations?: string[]): Promise<T> {
    const entity = await this.repository.findOne({
      where: { id } as any,
      relations,
    });

    if (!entity) {
      throw new NotFoundException(`${this.constructor.name} not found`);
    }

    return entity;
  }

  async update(id: string, updateDto: UpdateDto): Promise<T> {
    const entity = await this.findOne(id);
    Object.assign(entity, updateDto);
    return await this.repository.save(entity);
  }

  async remove(id: string): Promise<void> {
    const result = await this.repository.delete(id);
    
    if (result.affected === 0) {
      throw new NotFoundException(`${this.constructor.name} not found`);
    }
  }
}

// Product Service Implementation
@Injectable()
export class ProductsService extends CrudService<Product, CreateProductDto, UpdateProductDto> {
  constructor(
    @InjectRepository(Product)
    repository: Repository<Product>,
    private readonly inventoryService: InventoryService,
    private readonly eventEmitter: EventEmitter2,
  ) {
    super(repository);
  }

  async createWithInventory(createDto: CreateProductDto): Promise<Product> {
    const queryRunner = this.repository.manager.connection.createQueryRunner();
    
    await queryRunner.connect();
    await queryRunner.startTransaction();

    try {
      // Create product
      const product = this.repository.create(createDto);
      const savedProduct = await queryRunner.manager.save(product);

      // Create inventory record
      const inventory = await this.inventoryService.createForProduct(
        savedProduct.id,
        createDto.initialQuantity || 0,
      );

      // Emit event
      this.eventEmitter.emit('product.created', {
        productId: savedProduct.id,
        sku: savedProduct.sku,
        createdAt: new Date(),
      });

      await queryRunner.commitTransaction();
      
      savedProduct.inventory = inventory;
      return savedProduct;
    } catch (error) {
      await queryRunner.rollbackTransaction();
      throw error;
    } finally {
      await queryRunner.release();
    }
  }

  async searchProducts(criteria: ProductSearchCriteria): Promise<Product[]> {
    const query = this.repository.createQueryBuilder('product')
      .leftJoinAndSelect('product.inventory', 'inventory')
      .leftJoinAndSelect('product.categories', 'categories');

    if (criteria.keyword) {
      query.where('product.name LIKE :keyword OR product.description LIKE :keyword', {
        keyword: `%${criteria.keyword}%`,
      });
    }

    if (criteria.categoryId) {
      query.andWhere('categories.id = :categoryId', {
        categoryId: criteria.categoryId,
      });
    }

    if (criteria.minPrice !== undefined) {
      query.andWhere('product.price >= :minPrice', {
        minPrice: criteria.minPrice,
      });
    }

    if (criteria.maxPrice !== undefined) {
      query.andWhere('product.price <= :maxPrice', {
        maxPrice: criteria.maxPrice,
      });
    }

    return await query
      .skip((criteria.page - 1) * criteria.limit)
      .take(criteria.limit)
      .getMany();
  }
}
```

## 3. JWT Authentication พร้อม Refresh Token

### 3.1 Complete Auth Flow

```typescript
// Auth Module Structure
auth/
├── strategies/
│   ├── jwt.strategy.ts
│   └── refresh-token.strategy.ts
├── guards/
│   ├── jwt-auth.guard.ts
│   └── roles.guard.ts
├── decorators/
│   └── current-user.decorator.ts
├── interfaces/
│   └── token-payload.interface.ts
└── auth.service.ts

// Token Service
@Injectable()
export class TokenService {
  constructor(
    private readonly jwtService: JwtService,
    private readonly redisService: RedisService,
    private readonly configService: ConfigService,
  ) {}

  async generateAccessToken(user: User): Promise<string> {
    const payload: TokenPayload = {
      sub: user.id,
      email: user.email,
      username: user.username,
      roles: user.roles.map(role => role.name),
    };

    return this.jwtService.signAsync(payload, {
      secret: this.configService.get('JWT_ACCESS_SECRET'),
      expiresIn: '10m',
    });
  }

  async generateRefreshToken(user: User): Promise<string> {
    const payload: TokenPayload = {
      sub: user.id,
      email: user.email,
    };

    const refreshToken = this.jwtService.signAsync(payload, {
      secret: this.configService.get('JWT_REFRESH_SECRET'),
      expiresIn: '30d',
    });

    // Store in Redis with user ID as key
    await this.redisService.set(
      `refresh_token:${user.id}`,
      refreshToken,
      30 * 24 * 60 * 60, // 30 days in seconds
    );

    return refreshToken;
  }

  async validateRefreshToken(token: string): Promise<User | null> {
    try {
      const payload = await this.jwtService.verifyAsync(token, {
        secret: this.configService.get('JWT_REFRESH_SECRET'),
      });

      // Check if token exists in Redis
      const storedToken = await this.redisService.get(`refresh_token:${payload.sub}`);
      
      if (storedToken !== token) {
        return null;
      }

      return await this.usersService.findById(payload.sub);
    } catch {
      return null;
    }
  }

  async revokeRefreshToken(userId: string): Promise<void> {
    await this.redisService.del(`refresh_token:${userId}`);
  }

  async rotateTokens(oldRefreshToken: string): Promise<TokenPair> {
    const user = await this.validateRefreshToken(oldRefreshToken);
    
    if (!user) {
      throw new UnauthorizedException('Invalid refresh token');
    }

    // Revoke old token
    await this.revokeRefreshToken(user.id);

    // Generate new tokens
    const [accessToken, refreshToken] = await Promise.all([
      this.generateAccessToken(user),
      this.generateRefreshToken(user),
    ]);

    return { accessToken, refreshToken };
  }
}

// Auth Controller
@Controller('auth')
export class AuthController {
  constructor(private readonly authService: AuthService) {}

  @Post('login')
  @HttpCode(HttpStatus.OK)
  async login(@Body() loginDto: LoginDto): Promise<TokenResponse> {
    return this.authService.login(loginDto);
  }

  @Post('refresh')
  @HttpCode(HttpStatus.OK)
  async refresh(@Body() refreshDto: RefreshTokenDto): Promise<TokenResponse> {
    return this.authService.refreshTokens(refreshDto.refreshToken);
  }

  @Post('logout')
  @UseGuards(JwtAuthGuard)
  @HttpCode(HttpStatus.OK)
  async logout(@Req() req: RequestWithUser): Promise<void> {
    await this.authService.logout(req.user.id);
  }

  @Post('logout-all')
  @UseGuards(JwtAuthGuard)
  @HttpCode(HttpStatus.OK)
  async logoutAll(@Req() req: RequestWithUser): Promise<void> {
    await this.authService.logoutAll(req.user.id);
  }
}

// Frontend Token Management
class TokenManager {
  private static readonly ACCESS_TOKEN_KEY = 'access_token';
  private static readonly REFRESH_TOKEN_KEY = 'refresh_token';

  static async refreshTokens(): Promise<boolean> {
    const refreshToken = this.getRefreshToken();
    
    if (!refreshToken) {
      return false;
    }

    try {
      const response = await fetch('/api/auth/refresh', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ refreshToken }),
      });

      if (response.ok) {
        const { accessToken, refreshToken: newRefreshToken } = await response.json();
        this.setTokens(accessToken, newRefreshToken);
        return true;
      }
    } catch (error) {
      console.error('Token refresh failed:', error);
    }

    this.clearTokens();
    return false;
  }

  static async getValidToken(): Promise<string | null> {
    let token = this.getAccessToken();
    
    if (token && this.isTokenExpired(token)) {
      const refreshed = await this.refreshTokens();
      
      if (refreshed) {
        token = this.getAccessToken();
      } else {
        token = null;
      }
    }

    return token;
  }

  static setupTokenRefresh(): void {
    // Refresh token 1 minute before expiry
    setInterval(async () => {
      const token = this.getAccessToken();
      
      if (token && this.willTokenExpireSoon(token, 60)) {
        await this.refreshTokens();
      }
    }, 30000); // Check every 30 seconds
  }
}
```

### 3.2 Axios Interceptor สำหรับ Token Refresh

```typescript
// api/interceptors.ts
const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
});

api.interceptors.request.use(
  async (config) => {
    const token = await TokenManager.getValidToken();
    
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    return config;
  },
  (error) => Promise.reject(error)
);

api.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;
    
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      
      const refreshed = await TokenManager.refreshTokens();
      
      if (refreshed) {
        const token = TokenManager.getAccessToken();
        originalRequest.headers.Authorization = `Bearer ${token}`;
        return api(originalRequest);
      }
    }
    
    return Promise.reject(error);
  }
);
```

## 4. Microservices Architecture

### 4.1 Service Breakdown

```yaml
services:
  api-gateway:           # Port: 3000
    - Routes requests
    - Authentication
    - Rate limiting
  
  user-service:          # Port: 3001
    - User management
    - Authentication
    - Profile management
  
  product-service:       # Port: 3002
    - Product catalog
    - Product search
    - Category management
  
  inventory-service:     # Port: 3003
    - Stock management
    - Inventory tracking
    - Reorder alerts
  
  order-service:         # Port: 3004
    - Order processing
    - Order tracking
    - Payment integration
  
  shipping-service:      # Port: 3005
    - Shipping calculation
    - Carrier integration
    - Tracking updates
  
  notification-service:  # Port: 3006
    - Email notifications
    - SMS alerts
    - Push notifications
  
  audit-service:         # Port: 3007
    - Activity logging
    - Audit trails
    - Compliance reporting
```

### 4.2 Communication Patterns

```typescript
// Kafka Event Producer
@Injectable()
export class EventProducer {
  constructor(
    @Inject('KAFKA_PRODUCER')
    private readonly producer: Producer,
  ) {}

  async publish(topic: string, event: Event): Promise<void> {
    await this.producer.send({
      topic,
      messages: [
        {
          key: event.entityId,
          value: JSON.stringify(event),
          timestamp: new Date().toISOString(),
        },
      ],
    });
  }
}

// Event Types
export enum EventType {
  USER_CREATED = 'user.created',
  USER_UPDATED = 'user.updated',
  USER_DELETED = 'user.deleted',
  PRODUCT_CREATED = 'product.created',
  ORDER_PLACED = 'order.placed',
  INVENTORY_UPDATED = 'inventory.updated',
}

// Event Consumer
@Injectable()
export class EventConsumer {
  constructor(
    private readonly auditService: AuditService,
    private readonly notificationService: NotificationService,
  ) {}

  @EventPattern(EventType.USER_CREATED)
  async handleUserCreated(event: UserCreatedEvent): Promise<void> {
    // Log to audit trail
    await this.auditService.log({
      action: 'USER_CREATED',
      userId: event.payload.userId,
      entityType: 'USER',
      entityId: event.payload.userId,
      metadata: event.payload,
    });

    // Send welcome email
    await this.notificationService.sendWelcomeEmail(
      event.payload.email,
      event.payload.username,
    );
  }

  @EventPattern(EventType.INVENTORY_UPDATED)
  async handleInventoryUpdated(event: InventoryUpdatedEvent): Promise<void> {
    // Check reorder level
    if (event.payload.newQuantity <= event.payload.reorderLevel) {
      await this.notificationService.sendReorderAlert(
        event.payload.productId,
        event.payload.productName,
        event.payload.newQuantity,
      );
    }
  }
}

// gRPC Service Definition
@GrpcService('ProductService')
export class ProductGrpcService implements IProductService {
  constructor(private readonly productService: ProductService) {}

  async getProduct(
    request: GetProductRequest,
  ): Promise<GetProductResponse> {
    const product = await this.productService.findOne(request.id);
    
    return {
      product: {
        id: product.id,
        name: product.name,
        sku: product.sku,
        price: product.price.toString(),
        description: product.description,
        inStock: product.inventory.quantity > 0,
      },
    };
  }

  async searchProducts(
    request: SearchProductsRequest,
  ): Promise<SearchProductsResponse> {
    const result = await this.productService.search({
      keyword: request.keyword,
      categoryId: request.categoryId,
      minPrice: request.minPrice ? parseFloat(request.minPrice) : undefined,
      maxPrice: request.maxPrice ? parseFloat(request.maxPrice) : undefined,
      page: request.page,
      limit: request.limit,
    });

    return {
      products: result.data.map(product => ({
        id: product.id,
        name: product.name,
        sku: product.sku,
        price: product.price.toString(),
      })),
      total: result.total,
      page: result.page,
      totalPages: result.totalPages,
    };
  }
}
```

### 4.3 API Gateway Configuration

```typescript
// Gateway Module
@Module({
  imports: [
    ClientsModule.register([
      {
        name: 'USER_SERVICE',
        transport: Transport.TCP,
        options: { host: 'user-service', port: 3001 },
      },
      {
        name: 'PRODUCT_SERVICE',
        transport: Transport.TCP,
        options: { host: 'product-service', port: 3002 },
      },
    ]),
  ],
  controllers: [GatewayController],
})
export class GatewayModule {}

// Gateway Controller
@Controller()
export class GatewayController {
  constructor(
    @Inject('USER_SERVICE') private userClient: ClientProxy,
    @Inject('PRODUCT_SERVICE') private productClient: ClientProxy,
  ) {}

  @Post('users')
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles('admin')
  async createUser(@Body() createUserDto: CreateUserDto) {
    return this.userClient.send('createUser', createUserDto);
  }

  @Get('products')
  async getProducts(@Query() query: ProductQueryDto) {
    return this.productClient.send('getProducts', query);
  }

  // Health check endpoints
  @Get('health')
  async healthCheck() {
    const services = [
      { name: 'user-service', client: this.userClient },
      { name: 'product-service', client: this.productClient },
    ];

    const results = await Promise.allSettled(
      services.map(async ({ name, client }) => {
        try {
          await client.send('health', {}).toPromise();
          return { name, status: 'healthy' };
        } catch {
          return { name, status: 'unhealthy' };
        }
      })
    );

    return {
      status: results.every(r => r.status === 'fulfilled' && r.value.status === 'healthy')
        ? 'healthy'
        : 'degraded',
      services: results.map(r => r.status === 'fulfilled' ? r.value : r.reason),
      timestamp: new Date().toISOString(),
    };
  }
}
```

## 5. ระบบทั้งหมดที่ต้องการ

### 5.1 User Management System

```typescript
// User Role Management
@Entity('roles')
export class Role {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  name: string;

  @Column('text', { array: true, default: [] })
  permissions: string[];

  @ManyToMany(() => User, user => user.roles)
  users: User[];
}

// Permission System
export enum Permission {
  USER_CREATE = 'user:create',
  USER_READ = 'user:read',
  USER_UPDATE = 'user:update',
  USER_DELETE = 'user:delete',
  PRODUCT_CREATE = 'product:create',
  PRODUCT_READ = 'product:read',
  // ... more permissions
}

// Permission Guard
@Injectable()
export class PermissionsGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredPermissions = this.reflector.get<Permission[]>(
      'permissions',
      context.getHandler(),
    );

    if (!requiredPermissions) {
      return true;
    }

    const request = context.switchToHttp().getRequest();
    const user = request.user;

    if (!user || !user.roles) {
      return false;
    }

    const userPermissions = user.roles.flatMap(role => role.permissions);
    
    return requiredPermissions.every(permission =>
      userPermissions.includes(permission),
    );
  }
}

// User Profile Management
@Injectable()
export class UserProfileService {
  async updateProfile(userId: string, updateDto: UpdateProfileDto): Promise<User> {
    const user = await this.usersService.findOne(userId);
    
    // Validate email uniqueness if changing email
    if (updateDto.email && updateDto.email !== user.email) {
      const existing = await this.usersService.findByEmail(updateDto.email);
      if (existing) {
        throw new ConflictException('Email already in use');
      }
    }

    Object.assign(user, updateDto);
    return await this.usersRepository.save(user);
  }

  async changePassword(
    userId: string,
    changePasswordDto: ChangePasswordDto,
  ): Promise<void> {
    const user = await this.usersService.findOne(userId, true);
    
    const isOldPasswordValid = await bcrypt.compare(
      changePasswordDto.oldPassword,
      user.passwordHash,
    );

    if (!isOldPasswordValid) {
      throw new BadRequestException('Old password is incorrect');
    }

    user.passwordHash = await bcrypt.hash(changePasswordDto.newPassword, 10);
    await this.usersRepository.save(user);

    // Emit password changed event
    this.eventEmitter.emit('user.password-changed', {
      userId: user.id,
      timestamp: new Date(),
    });

    // Invalidate all refresh tokens
    await this.tokenService.revokeRefreshToken(user.id);
  }
}
```

### 5.2 Product Management System

```typescript
// Product Variants
@Entity('product_variants')
export class ProductVariant {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @ManyToOne(() => Product, product => product.variants)
  product: Product;

  @Column()
  sku: string;

  @Column()
  name: string;

  @Column('jsonb')
  attributes: Record<string, any>;

  @Column('decimal', { precision: 10, scale: 2 })
  price: number;

  @Column({ default: 0 })
  quantity: number;
}

// Product Categories with Hierarchy
@Entity('categories')
export class Category {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  name: string;

  @Column({ nullable: true })
  description: string;

  @Column({ nullable: true })
  slug: string;

  @ManyToOne(() => Category, category => category.children)
  parent: Category;

  @OneToMany(() => Category, category => category.parent)
  children: Category[];

  @ManyToMany(() => Product, product => product.categories)
  products: Product[];

  @Column({ default: 0 })
  sortOrder: number;

  @Column({ default: true })
  isActive: boolean;
}

// Product Search Service with Elasticsearch
@Injectable()
export class ProductSearchService {
  constructor(
    @Inject('ELASTICSEARCH_CLIENT')
    private readonly elasticsearch: Client,
  ) {}

  async indexProduct(product: Product): Promise<void> {
    await this.elasticsearch.index({
      index: 'products',
      id: product.id,
      body: {
        id: product.id,
        name: product.name,
        sku: product.sku,
        description: product.description,
        price: product.price,
        categories: product.categories?.map(c => c.name) || [],
        inStock: product.inventory?.quantity > 0,
        createdAt: product.createdAt,
        updatedAt: product.updatedAt,
      },
    });
  }

  async searchProducts(query: SearchQuery): Promise<SearchResult> {
    const { q, categories, minPrice, maxPrice, page = 1, limit = 20 } = query;
    
    const from = (page - 1) * limit;

    const searchBody: any = {
      query: {
        bool: {
          must: [],
          filter: [],
        },
      },
      sort: [{ _score: 'desc' }, { createdAt: 'desc' }],
      from,
      size: limit,
      aggs: {
        categories: {
          terms: {
            field: 'categories.keyword',
            size: 10,
          },
        },
        price_range: {
          range: {
            field: 'price',
            ranges: [
              { to: 50 },
              { from: 50, to: 100 },
              { from: 100, to: 200 },
              { from: 200 },
            ],
          },
        },
      },
    };

    if (q) {
      searchBody.query.bool.must.push({
        multi_match: {
          query: q,
          fields: ['name^3', 'description^2', 'sku'],
          fuzziness: 'AUTO',
        },
      });
    }

    if (categories?.length) {
      searchBody.query.bool.filter.push({
        terms: {
          'categories.keyword': categories,
        },
      });
    }

    if (minPrice !== undefined || maxPrice !== undefined) {
      const range: any = {};
      if (minPrice !== undefined) range.gte = minPrice;
      if (maxPrice !== undefined) range.lte = maxPrice;
      
      searchBody.query.bool.filter.push({
        range: { price: range },
      });
    }

    const response = await this.elasticsearch.search({
      index: 'products',
      body: searchBody,
    });

    const products = response.hits.hits.map(hit => ({
      ...hit._source,
      score: hit._score,
    }));

    return {
      products,
      total: response.hits.total.value,
      page,
      limit,
      totalPages: Math.ceil(response.hits.total.value / limit),
      aggregations: response.aggregations,
    };
  }
}
```

### 5.3 Inventory Management System

```typescript
// Inventory Entity with Transactions
@Entity('inventory')
export class Inventory {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @OneToOne(() => Product, product => product.inventory)
  @JoinColumn()
  product: Product;

  @Column({ default: 0 })
  quantity: number;

  @Column({ default: 10 })
  reorderLevel: number;

  @Column({ nullable: true })
  location: string;

  @Column({ nullable: true })
  lastRestocked: Date;

  @OneToMany(() => InventoryTransaction, transaction => transaction.inventory)
  transactions: InventoryTransaction[];
}

@Entity('inventory_transactions')
export class InventoryTransaction {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @ManyToOne(() => Inventory, inventory => inventory.transactions)
  inventory: Inventory;

  @Column()
  type: TransactionType; // RECEIVED, SOLD, ADJUSTED, RETURNED

  @Column()
  quantity: number;

  @Column()
  previousQuantity: number;

  @Column()
  newQuantity: number;

  @Column({ nullable: true })
  referenceId: string; // Order ID, etc.

  @Column({ nullable: true })
  notes: string;

  @CreateDateColumn()
  createdAt: Date;

  @ManyToOne(() => User)
  createdBy: User;
}

// Inventory Service
@Injectable()
export class InventoryService {
  async updateQuantity(
    inventoryId: string,
    quantity: number,
    type: TransactionType,
    referenceId?: string,
    notes?: string,
    userId?: string,
  ): Promise<InventoryTransaction> {
    const inventory = await this.inventoryRepository.findOne({
      where: { id: inventoryId },
      relations: ['product'],
    });

    if (!inventory) {
      throw new NotFoundException('Inventory not found');
    }

    const previousQuantity = inventory.quantity;
    const newQuantity = previousQuantity + quantity;

    if (newQuantity < 0) {
      throw new BadRequestException('Insufficient stock');
    }

    // Update inventory
    inventory.quantity = newQuantity;
    
    if (type === TransactionType.RECEIVED) {
      inventory.lastRestocked = new Date();
    }

    await this.inventoryRepository.save(inventory);

    // Create transaction record
    const transaction = this.transactionRepository.create({
      inventory,
      type,
      quantity,
      previousQuantity,
      newQuantity,
      referenceId,
      notes,
      createdBy: userId ? { id: userId } : undefined,
    });

    const savedTransaction = await this.transactionRepository.save(transaction);

    // Check reorder level
    if (newQuantity <= inventory.reorderLevel) {
      await this.notifyLowStock(inventory);
    }

    // Emit inventory updated event
    await this.eventProducer.publish('inventory.updated', {
      productId: inventory.product.id,
      productName: inventory.product.name,
      oldQuantity: previousQuantity,
      newQuantity,
      transactionType: type,
      timestamp: new Date(),
    });

    return savedTransaction;
  }

  private async notifyLowStock(inventory: Inventory): Promise<void> {
    // Send notification to warehouse manager
    await this.notificationService.sendLowStockAlert({
      productId: inventory.product.id,
      productName: inventory.product.name,
      currentQuantity: inventory.quantity,
      reorderLevel: inventory.reorderLevel,
      location: inventory.location,
    });

    // Create restock task in n8n
    await this.n8nService.createRestockTask({
      productSku: inventory.product.sku,
      productName: inventory.product.name,
      requiredQuantity: inventory.reorderLevel * 2,
      priority: 'HIGH',
    });
  }

  async getStockHistory(
    inventoryId: string,
    startDate?: Date,
    endDate?: Date,
    page = 1,
    limit = 50,
  ): Promise<Pagination<InventoryTransaction>> {
    const query = this.transactionRepository
      .createQueryBuilder('transaction')
      .where('transaction.inventoryId = :inventoryId', { inventoryId })
      .leftJoinAndSelect('transaction.createdBy', 'createdBy')
      .orderBy('transaction.createdAt', 'DESC');

    if (startDate) {
      query.andWhere('transaction.createdAt >= :startDate', { startDate });
    }

    if (endDate) {
      query.andWhere('transaction.createdAt <= :endDate', { endDate });
    }

    const [data, total] = await query
      .skip((page - 1) * limit)
      .take(limit)
      .getManyAndCount();

    return {
      data,
      total,
      page,
      limit,
      totalPages: Math.ceil(total / limit),
    };
  }
}
```

### 5.4 Shipping Management System

```typescript
// Shipping Entity
@Entity('shipping')
export class Shipping {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  orderId: string;

  @ManyToOne(() => User)
  customer: User;

  @Column({ type: 'jsonb' })
  shippingAddress: ShippingAddress;

  @Column({ type: 'jsonb' })
  billingAddress: BillingAddress;

  @Column()
  carrier: Carrier; // DHL, FedEx, UPS, etc.

  @Column({ nullable: true })
  trackingNumber: string;

  @Column({ type: 'enum', enum: ShippingStatus, default: ShippingStatus.PENDING })
  status: ShippingStatus;

  @Column('decimal', { precision: 10, scale: 2 })
  shippingCost: number;

  @Column({ nullable: true })
  estimatedDelivery: Date;

  @Column({ nullable: true })
  actualDelivery: Date;

  @Column({ type: 'jsonb', default: [] })
  trackingHistory: TrackingEvent[];

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}

// Shipping Service with Carrier Integration
@Injectable()
export class ShippingService {
  constructor(
    private readonly carrierFactory: CarrierFactory,
    private readonly notificationService: NotificationService,
  ) {}

  async createShipping(order: Order): Promise<Shipping> {
    // Calculate shipping cost
    const shippingCost = await this.calculateShippingCost(
      order.shippingAddress,
      order.items,
    );

    // Create shipping record
    const shipping = this.shippingRepository.create({
      orderId: order.id,
      customer: order.customer,
      shippingAddress: order.shippingAddress,
      billingAddress: order.billingAddress,
      carrier: order.preferredCarrier || this.selectBestCarrier(order),
      shippingCost,
      estimatedDelivery: this.calculateEstimatedDelivery(order),
    });

    const savedShipping = await this.shippingRepository.save(shipping);

    // Generate shipping label
    const label = await this.generateShippingLabel(savedShipping);
    savedShipping.labelUrl = label.url;
    savedShipping.trackingNumber = label.trackingNumber;

    await this.shippingRepository.save(savedShipping);

    // Notify customer
    await this.notificationService.sendShippingConfirmation({
      orderId: order.id,
      trackingNumber: label.trackingNumber,
      customerEmail: order.customer.email,
      estimatedDelivery: savedShipping.estimatedDelivery,
    });

    return savedShipping;
  }

  async updateTracking(shippingId: string): Promise<Shipping> {
    const shipping = await this.shippingRepository.findOne({
      where: { id: shippingId },
    });

    if (!shipping) {
      throw new NotFoundException('Shipping not found');
    }

    const carrier = this.carrierFactory.getCarrier(shipping.carrier);
    const trackingInfo = await carrier.getTrackingInfo(shipping.trackingNumber);

    shipping.status = this.mapCarrierStatus(trackingInfo.status);
    shipping.trackingHistory = trackingInfo.events;

    if (trackingInfo.delivered) {
      shipping.actualDelivery = trackingInfo.deliveredDate;
      shipping.status = ShippingStatus.DELIVERED;

      // Update order status
      await this.orderService.markAsDelivered(shipping.orderId);
    }

    return await this.shippingRepository.save(shipping);
  }

  private async calculateShippingCost(
    address: ShippingAddress,
    items: OrderItem[],
  ): Promise<number> {
    const totalWeight = items.reduce(
      (sum, item) => sum + (item.product.weight * item.quantity),
      0,
    );

    const totalValue = items.reduce(
      (sum, item) => sum + (item.product.price * item.quantity),
      0,
    );

    const dimensions = this.calculateTotalDimensions(items);

    // Get rates from multiple carriers
    const rates = await Promise.all([
      this.carrierFactory.getCarrier('DHL').getRate({
        address,
        weight: totalWeight,
        dimensions,
        value: totalValue,
      }),
      this.carrierFactory.getCarrier('FEDEX').getRate({
        address,
        weight: totalWeight,
        dimensions,
        value: totalValue,
      }),
      this.carrierFactory.getCarrier('UPS').getRate({
        address,
        weight: totalWeight,
        dimensions,
        value: totalValue,
      }),
    ]);

    // Return the best rate
    return Math.min(...rates.map(r => r.cost));
  }
}
```

### 5.5 Reporting System

```typescript
// Report Templates
export enum ReportType {
  SALES_SUMMARY = 'sales_summary',
  INVENTORY_STATUS = 'inventory_status',
  USER_ACTIVITY = 'user_activity',
  SHIPPING_PERFORMANCE = 'shipping_performance',
  FINANCIAL_STATEMENT = 'financial_statement',
}

// Report Service
@Injectable()
export class ReportService {
  async generateReport(
    type: ReportType,
    filters: ReportFilters,
    format: ReportFormat = ReportFormat.PDF,
  ): Promise<ReportResult> {
    const data = await this.getReportData(type, filters);
    const template = await this.getTemplate(type);
    
    const rendered = await this.renderTemplate(template, data);
    
    if (format === ReportFormat.PDF) {
      const pdf = await this.generatePDF(rendered);
      return {
        content: pdf,
        contentType: 'application/pdf',
        filename: this.generateFilename(type, filters),
      };
    } else if (format === ReportFormat.EXCEL) {
      const excel = await this.generateExcel(data);
      return {
        content: excel,
        contentType: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet',
        filename: this.generateFilename(type, filters, 'xlsx'),
      };
    } else {
      return {
        content: JSON.stringify(data, null, 2),
        contentType: 'application/json',
        filename: this.generateFilename(type, filters, 'json'),
      };
    }
  }

  private async getReportData(type: ReportType, filters: ReportFilters): Promise<any> {
    switch (type) {
      case ReportType.SALES_SUMMARY:
        return await this.generateSalesSummary(filters);
      case ReportType.INVENTORY_STATUS:
        return await this.generateInventoryStatus(filters);
      case ReportType.USER_ACTIVITY:
        return await this.generateUserActivity(filters);
      case ReportType.SHIPPING_PERFORMANCE:
        return await this.generateShippingPerformance(filters);
      case ReportType.FINANCIAL_STATEMENT:
        return await this.generateFinancialStatement(filters);
      default:
        throw new BadRequestException('Invalid report type');
    }
  }

  private async generateSalesSummary(filters: ReportFilters): Promise<SalesSummary> {
    const query = this.orderRepository
      .createQueryBuilder('order')
      .select([
        'DATE(order.createdAt) as date',
        'COUNT(order.id) as orderCount',
        'SUM(order.totalAmount) as totalRevenue',
        'AVG(order.totalAmount) as averageOrderValue',
      ])
      .where('order.status = :status', { status: OrderStatus.COMPLETED });

    if (filters.startDate) {
      query.andWhere('order.createdAt >= :startDate', { startDate: filters.startDate });
    }

    if (filters.endDate) {
      query.andWhere('order.createdAt <= :endDate', { endDate: filters.endDate });
    }

    if (filters.productId) {
      query
        .innerJoin('order.items', 'item')
        .andWhere('item.productId = :productId', { productId: filters.productId });
    }

    const dailyStats = await query
      .groupBy('DATE(order.createdAt)')
      .orderBy('date', 'DESC')
      .getRawMany();

    // Product performance
    const productPerformance = await this.orderRepository
      .createQueryBuilder('order')
      .select([
        'product.id as productId',
        'product.name as productName',
        'SUM(item.quantity) as totalSold',
        'SUM(item.price * item.quantity) as totalRevenue',
      ])
      .innerJoin('order.items', 'item')
      .innerJoin('item.product', 'product')
      .where('order.status = :status', { status: OrderStatus.COMPLETED })
      .andWhere('order.createdAt BETWEEN :startDate AND :endDate', {
        startDate: filters.startDate || new Date('2024-01-01'),
        endDate: filters.endDate || new Date(),
      })
      .groupBy('product.id, product.name')
      .orderBy('totalRevenue', 'DESC')
      .limit(10)
      .getRawMany();

    return {
      period: {
        start: filters.startDate,
        end: filters.endDate,
      },
      summary: {
        totalOrders: dailyStats.reduce((sum, day) => sum + parseInt(day.orderCount), 0),
        totalRevenue: dailyStats.reduce((sum, day) => sum + parseFloat(day.totalRevenue), 0),
        averageOrderValue: dailyStats.reduce((sum, day) => sum + parseFloat(day.averageOrderValue), 0) / dailyStats.length,
      },
      dailyStats,
      productPerformance,
      trends: await this.calculateSalesTrends(dailyStats),
    };
  }

  async scheduleReport(
    type: ReportType,
    schedule: ReportSchedule,
    recipients: string[],
  ): Promise<ScheduledReport> {
    const scheduledReport = this.scheduledReportRepository.create({
      type,
      schedule,
      recipients,
      filters: schedule.filters,
      format: schedule.format,
      isActive: true,
    });

    return await this.scheduledReportRepository.save(scheduledReport);
  }

  private async processScheduledReports(): Promise<void> {
    const now = new Date();
    const scheduledReports = await this.scheduledReportRepository.find({
      where: { isActive: true },
    });

    for (const report of scheduledReports) {
      if (this.shouldRunReport(report, now)) {
        try {
          const result = await this.generateReport(
            report.type,
            report.filters,
            report.format,
          );

          await this.sendReportEmail(report.recipients, result);
          
          await this.scheduledReportRepository.update(report.id, {
            lastRun: now,
            nextRun: this.calculateNextRun(report.schedule, now),
          });
        } catch (error) {
          console.error(`Failed to generate scheduled report ${report.id}:`, error);
          
          // Notify admin about failure
          await this.notificationService.sendReportFailure({
            reportId: report.id,
            reportType: report.type,
            error: error.message,
          });
        }
      }
    }
  }
}
```

### 5.6 Audit System

```typescript
// Audit Module
@Injectable()
export class AuditService {
  async logAuditEvent(auditEvent: AuditEvent): Promise<AuditLog> {
    const log = this.auditLogRepository.create({
      ...auditEvent,
      ipAddress: this.getClientIp(auditEvent.request),
      userAgent: auditEvent.request?.headers['user-agent'],
      metadata: {
        ...auditEvent.metadata,
        url: auditEvent.request?.url,
        method: auditEvent.request?.method,
      },
    });

    return await this.auditLogRepository.save(log);
  }

  async getAuditLogs(filters: AuditFilter): Promise<Pagination<AuditLog>> {
    const query = this.auditLogRepository
      .createQueryBuilder('log')
      .leftJoinAndSelect('log.user', 'user')
      .orderBy('log.createdAt', 'DESC');

    if (filters.userId) {
      query.andWhere('log.userId = :userId', { userId: filters.userId });
    }

    if (filters.action) {
      query.andWhere('log.action = :action', { action: filters.action });
    }

    if (filters.entityType) {
      query.andWhere('log.entityType = :entityType', { entityType: filters.entityType });
    }

    if (filters.entityId) {
      query.andWhere('log.entityId = :entityId', { entityId: filters.entityId });
    }

    if (filters.startDate) {
      query.andWhere('log.createdAt >= :startDate', { startDate: filters.startDate });
    }

    if (filters.endDate) {
      query.andWhere('log.createdAt <= :endDate', { endDate: filters.endDate });
    }

    if (filters.search) {
      query.andWhere(
        '(log.action ILIKE :search OR log.entityType ILIKE :search OR log.entityId ILIKE :search)',
        { search: `%${filters.search}%` }
      );
    }

    const [data, total] = await query
      .skip((filters.page - 1) * filters.limit)
      .take(filters.limit)
      .getManyAndCount();

    return {
      data,
      total,
      page: filters.page,
      limit: filters.limit,
      totalPages: Math.ceil(total / filters.limit),
    };
  }

  async exportAuditLogs(filters: AuditFilter, format: ExportFormat): Promise<Buffer> {
    const logs = await this.getAuditLogs({ ...filters, limit: 10000 });
    
    if (format === ExportFormat.CSV) {
      return this.convertToCSV(logs.data);
    } else if (format === ExportFormat.EXCEL) {
      return this.convertToExcel(logs.data);
    } else {
      return Buffer.from(JSON.stringify(logs.data, null, 2));
    }
  }

  // Audit Interceptor for automatic logging
  @Injectable()
  export class AuditInterceptor implements NestInterceptor {
    intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
      const request = context.switchToHttp().getRequest();
      const response = context.switchToHttp().getResponse();
      const user = request.user;
      const startTime = Date.now();

      return next.handle().pipe(
        tap(async (data) => {
          const duration = Date.now() - startTime;
          
          await this.auditService.logAuditEvent({
            action: this.getActionName(context),
            userId: user?.id,
            entityType: this.getEntityType(context),
            entityId: this.getEntityId(context, data),
            oldValues: request.body,
            newValues: data,
            ipAddress: request.ip,
            userAgent: request.headers['user-agent'],
            statusCode: response.statusCode,
            duration,
            request: {
              url: request.url,
              method: request.method,
              params: request.params,
              query: request.query,
              body: this.sanitizeRequestBody(request.body),
            },
            metadata: {
              userAgent: request.headers['user-agent'],
              referer: request.headers['referer'],
              duration,
            },
          });
        }),
        catchError(async (error) => {
          const duration = Date.now() - startTime;
          
          await this.auditService.logAuditEvent({
            action: this.getActionName(context),
            userId: user?.id,
            entityType: this.getEntityType(context),
            entityId: this.getEntityId(context),
            oldValues: request.body,
            newValues: null,
            ipAddress: request.ip,
            userAgent: request.headers['user-agent'],
            statusCode: error.status || 500,
            duration,
            error: {
              message: error.message,
              stack: process.env.NODE_ENV === 'development' ? error.stack : undefined,
            },
            request: {
              url: request.url,
              method: request.method,
              params: request.params,
              query: request.query,
              body: this.sanitizeRequestBody(request.body),
            },
            metadata: {
              error: true,
              duration,
            },
          });

          throw error;
        }),
      );
    }
  }
}
```

## 6. การใช้งานเทคโนโลยีที่ระบุ

### 6.1 Redis Configuration & Usage

```typescript
// Redis Module
@Module({
  providers: [
    {
      provide: 'REDIS_CLIENT',
      useFactory: async () => {
        const client = new Redis({
          host: configService.get('REDIS_HOST'),
          port: configService.get('REDIS_PORT'),
          password: configService.get('REDIS_PASSWORD'),
          db: configService.get('REDIS_DB'),
          retryStrategy: (times) => {
            const delay = Math.min(times * 50, 2000);
            return delay;
          },
        });

        client.on('error', (err) => {
          console.error('Redis Client Error:', err);
        });

        client.on('connect', () => {
          console.log('Redis connected successfully');
        });

        return client;
      },
      inject: [ConfigService],
    },
    RedisService,
  ],
  exports: ['REDIS_CLIENT', RedisService],
})
export class RedisModule {}

// Advanced Redis Service
@Injectable()
export class RedisService {
  constructor(@Inject('REDIS_CLIENT') private readonly redis: Redis) {}

  // Cache with automatic invalidation
  async cache<T>(
    key: string,
    fetcher: () => Promise<T>,
    ttl: number = 3600,
    tags: string[] = [],
  ): Promise<T> {
    // Try to get from cache
    const cached = await this.get<T>(key);
    if (cached !== null) {
      return cached;
    }

    // Fetch fresh data
    const data = await fetcher();
    
    // Store in cache
    await this.set(key, data, ttl);
    
    // Store tag relationships
    if (tags.length > 0) {
      await this.addTags(key, tags);
    }

    return data;
  }

  async invalidateTags(tags: string[]): Promise<void> {
    const pipeline = this.redis.pipeline();
    
    for (const tag of tags) {
      const keys = await this.redis.smembers(`tag:${tag}`);
      
      for (const key of keys) {
        pipeline.del(key);
        pipeline.srem(`tag:${tag}`, key);
      }
    }
    
    await pipeline.exec();
  }

  // Rate limiting
  async isRateLimited(key: string, limit: number, window: number): Promise<boolean> {
    const current = await this.redis.get(key);
    
    if (current === null) {
      await this.redis.setex(key, window, 1);
      return false;
    }
    
    const count = parseInt(current);
    
    if (count >= limit) {
      return true;
    }
    
    await this.redis.incr(key);
    return false;
  }

  // Distributed lock
  async acquireLock(
    key: string,
    ttl: number = 10000,
    retryDelay: number = 100,
    maxRetries: number = 10,
  ): Promise<string | null> {
    const lockId = crypto.randomUUID();
    
    for (let i = 0; i < maxRetries; i++) {
      const result = await this.redis.set(
        key,
        lockId,
        'NX',
        'PX',
        ttl,
      );
      
      if (result === 'OK') {
        return lockId;
      }
      
      if (i < maxRetries - 1) {
        await new Promise(resolve => setTimeout(resolve, retryDelay));
      }
    }
    
    return null;
  }

  async releaseLock(key: string, lockId: string): Promise<boolean> {
    const script = `
      if redis.call("GET", KEYS[1]) == ARGV[1] then
        return redis.call("DEL", KEYS[1])
      else
        return 0
      end
    `;
    
    const result = await this.redis.eval(script, 1, key, lockId);
    return result === 1;
  }
}
```

### 6.2 Kafka Setup & Event Streaming

```typescript
// Kafka Module
@Module({
  imports: [ConfigModule],
  providers: [
    {
      provide: 'KAFKA_PRODUCER',
      useFactory: (configService: ConfigService) => {
        return new Producer({
          'bootstrap.servers': configService.get('KAFKA_BROKERS'),
          'client.id': 'nestjs-producer',
          'acks': 'all',
          'retries': 3,
          'compression.type': 'gzip',
        });
      },
      inject: [ConfigService],
    },
    {
      provide: 'KAFKA_CONSUMER',
      useFactory: (configService: ConfigService) => {
        return new KafkaConsumer({
          'bootstrap.servers': configService.get('KAFKA_BROKERS'),
          'group.id': 'nestjs-consumer',
          'auto.offset.reset': 'earliest',
          'enable.auto.commit': false,
        });
      },
      inject: [ConfigService],
    },
    EventProducer,
    EventConsumer,
  ],
  exports: [EventProducer, EventConsumer],
})
export class KafkaModule {}

// Event Schema Registry
export class EventSchemaRegistry {
  private static schemas: Map<string, any> = new Map();

  static register(schema: EventSchema): void {
    this.schemas.set(schema.type, schema);
  }

  static validate(event: Event): boolean {
    const schema = this.schemas.get(event.type);
    
    if (!schema) {
      return false;
    }

    // Validate against JSON Schema
    const ajv = new Ajv();
    const validate = ajv.compile(schema.schema);
    return validate(event.payload);
  }

  static getSchema(type: string): EventSchema | undefined {
    return this.schemas.get(type);
  }
}

// Register schemas
EventSchemaRegistry.register({
  type: 'user.created',
  version: '1.0.0',
  schema: {
    type: 'object',
    required: ['userId', 'email', 'username', 'createdAt'],
    properties: {
      userId: { type: 'string', format: 'uuid' },
      email: { type: 'string', format: 'email' },
      username: { type: 'string' },
      createdAt: { type: 'string', format: 'date-time' },
    },
  },
});

// Event Dead Letter Queue Handler
@Injectable()
export class DLQHandler {
  constructor(
    @Inject('KAFKA_PRODUCER') private producer: Producer,
    private readonly logger: LoggerService,
  ) {}

  async handleFailedEvent(
    event: Event,
    error: Error,
    context: any,
  ): Promise<void> {
    const dlqEvent: DLQEvent = {
      originalEvent: event,
      error: {
        message: error.message,
        stack: error.stack,
        timestamp: new Date().toISOString(),
      },
      context,
      retryCount: context.retryCount || 0,
      processedAt: new Date().toISOString(),
    };

    // Send to DLQ topic
    await this.producer.send({
      topic: 'events.dlq',
      messages: [
        {
          key: event.id,
          value: JSON.stringify(dlqEvent),
        },
      ],
    });

    // Log for monitoring
    this.logger.error('Event processing failed, sent to DLQ', {
      eventId: event.id,
      eventType: event.type,
      error: error.message,
    });

    // Alert if too many failures
    if (context.retryCount >= 3) {
      await this.sendAlert(event, error);
    }
  }

  private async sendAlert(event: Event, error: Error): Promise<void> {
    // Send alert to monitoring system
    // This could be Slack, Email, PagerDuty, etc.
  }
}
```

### 6.3 ELK Stack Integration

```typescript
// Logging Interceptor
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  constructor(private readonly elasticsearchService: ElasticsearchService) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const response = context.switchToHttp().getResponse();
    const startTime = Date.now();

    const logEntry: LogEntry = {
      timestamp: new Date().toISOString(),
      level: 'info',
      service: 'api-gateway',
      request: {
        id: request.id,
        method: request.method,
        url: request.url,
        ip: request.ip,
        userAgent: request.headers['user-agent'],
        userId: request.user?.id,
      },
    };

    return next.handle().pipe(
      tap((data) => {
        const duration = Date.now() - startTime;
        
        logEntry.response = {
          statusCode: response.statusCode,
          duration,
          size: Buffer.byteLength(JSON.stringify(data), 'utf8'),
        };

        logEntry.level = response.statusCode >= 400 ? 'warn' : 'info';
        
        this.elasticsearchService.indexLog(logEntry);
      }),
      catchError((error) => {
        const duration = Date.now() - startTime;
        
        logEntry.response = {
          statusCode: error.status || 500,
          duration,
          error: error.message,
        };

        logEntry.level = 'error';
        logEntry.error = {
          message: error.message,
          stack: error.stack,
        };

        this.elasticsearchService.indexLog(logEntry);
        
        throw error;
      }),
    );
  }
}

// Elasticsearch Service
@Injectable()
export class ElasticsearchService {
  constructor(
    @Inject('ELASTICSEARCH_CLIENT')
    private readonly client: Client,
  ) {}

  async indexLog(log: LogEntry): Promise<void> {
    const index = `logs-${new Date().toISOString().split('T')[0]}`;
    
    await this.client.index({
      index,
      body: log,
    });
  }

  async searchLogs(query: LogQuery): Promise<LogSearchResult> {
    const { q, level, service, userId, startDate, endDate, page = 1, size = 50 } = query;
    
    const from = (page - 1) * size;

    const searchBody: any = {
      query: {
        bool: {
          must: [],
          filter: [],
        },
      },
      sort: [{ timestamp: 'desc' }],
      from,
      size,
    };

    if (q) {
      searchBody.query.bool.must.push({
        query_string: {
          query: q,
          fields: ['message', 'request.url', 'error.message'],
        },
      });
    }

    if (level) {
      searchBody.query.bool.filter.push({
        term: { level },
      });
    }

    if (service) {
      searchBody.query.bool.filter.push({
        term: { service },
      });
    }

    if (userId) {
      searchBody.query.bool.filter.push({
        term: { 'request.userId': userId },
      });
    }

    if (startDate || endDate) {
      const range: any = {};
      if (startDate) range.gte = startDate;
      if (endDate) range.lte = endDate;
      
      searchBody.query.bool.filter.push({
        range: { timestamp: range },
      });
    }

    const response = await this.client.search({
      index: 'logs-*',
      body: searchBody,
    });

    return {
      hits: response.hits.hits.map(hit => ({
        ...hit._source,
        id: hit._id,
        score: hit._score,
      })),
      total: response.hits.total.value,
      page,
      size,
      totalPages: Math.ceil(response.hits.total.value / size),
    };
  }

  async getAggregatedMetrics(timeRange: string): Promise<AggregatedMetrics> {
    const response = await this.client.search({
      index: 'logs-*',
      body: {
        size: 0,
        query: {
          range: {
            timestamp: {
              gte: `now-${timeRange}`,
            },
          },
        },
        aggs: {
          by_service: {
            terms: {
              field: 'service.keyword',
              size: 10,
            },
          },
          by_level: {
            terms: {
              field: 'level.keyword',
            },
          },
          response_times: {
            percentiles: {
              field: 'response.duration',
              percents: [50, 95, 99],
            },
          },
          hourly_trend: {
            date_histogram: {
              field: 'timestamp',
              calendar_interval: 'hour',
            },
            aggs: {
              avg_response_time: {
                avg: {
                  field: 'response.duration',
                },
              },
              error_count: {
                filter: {
                  term: { level: 'error' },
                },
              },
            },
          },
        },
      },
    });

    return response.aggregations;
  }
}
```

### 6.4 Grafana Monitoring Setup

```yaml
# grafana/provisioning/dashboards/dashboard.yml
apiVersion: 1

providers:
  - name: 'microservices-dashboards'
    orgId: 1
    folder: 'Microservices'
    type: file
    disableDeletion: false
    updateIntervalSeconds: 10
    allowUiUpdates: true
    options:
      path: /etc/grafana/provisioning/dashboards
```

```json
{
  "dashboard": {
    "title": "Microservices Performance Dashboard",
    "panels": [
      {
        "title": "API Response Times",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])",
            "legendFormat": "{{service}} - {{route}}",
            "refId": "A"
          }
        ],
        "gridPos": { "h": 8, "w": 12, "x": 0, "y": 0 }
      },
      {
        "title": "Error Rate",
        "type": "stat",
        "targets": [
          {
            "expr": "sum(rate(http_requests_total{status=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m])) * 100",
            "format": "percentunit",
            "refId": "A"
          }
        ],
        "gridPos": { "h": 4, "w": 6, "x": 12, "y": 0 }
      },
      {
        "title": "Database Connections",
        "type": "gauge",
        "targets": [
          {
            "expr": "pg_stat_database_numbackends",
            "refId": "A"
          }
        ],
        "gridPos": { "h": 4, "w": 6, "x": 18, "y": 0 }
      }
    ]
  }
}
```

### 6.5 n8n Workflow Automation

```typescript
// n8n Integration Service
@Injectable()
export class N8nService {
  constructor(private readonly httpService: HttpService) {}

  async triggerWorkflow(
    workflowId: string,
    data: any,
  ): Promise<void> {
    const n8nUrl = process.env.N8N_WEBHOOK_URL;
    
    await this.httpService.post(
      `${n8nUrl}/webhook/${workflowId}`,
      data,
      {
        headers: {
          'Content-Type': 'application/json',
          'X-N8N-API-KEY': process.env.N8N_API_KEY,
        },
      },
    ).toPromise();
  }

  async createRestockTask(data: RestockTaskData): Promise<void> {
    await this.triggerWorkflow('restock-alert', {
      event: 'inventory.low-stock',
      timestamp: new Date().toISOString(),
      data: {
        productId: data.productId,
        productName: data.productName,
        currentQuantity: data.currentQuantity,
        reorderLevel: data.reorderLevel,
        requiredQuantity: data.requiredQuantity,
        priority: data.priority,
      },
    });
  }

  async sendUserWelcomeEmail(data: WelcomeEmailData): Promise<void> {
    await this.triggerWorkflow('user-welcome-email', {
      event: 'user.created',
      timestamp: new Date().toISOString(),
      data: {
        userId: data.userId,
        email: data.email,
        username: data.username,
        welcomeLink: data.welcomeLink,
      },
    });
  }

  async processOrderFulfillment(data: OrderFulfillmentData): Promise<void> {
    await this.triggerWorkflow('order-fulfillment', {
      event: 'order.placed',
      timestamp: new Date().toISOString(),
      data: {
        orderId: data.orderId,
        customerEmail: data.customerEmail,
        items: data.items,
        shippingAddress: data.shippingAddress,
        totalAmount: data.totalAmount,
      },
    });
  }
}
```

## 7. Git Flow และ CI/CD Pipeline

### 7.1 Git Flow Strategy

```bash
# Branch naming conventions
feature/      # New features
bugfix/       # Bug fixes
hotfix/       # Critical production fixes
release/      # Release preparation
chore/        # Maintenance tasks
docs/         # Documentation updates

# Example workflow
git checkout -b feature/user-authentication
git commit -m "feat: add JWT authentication"
git push origin feature/user-authentication

# Create Pull Request
# After review and approval
git checkout develop
git merge --no-ff feature/user-authentication
git branch -d feature/user-authentication
git push origin develop
```

### 7.2 Complete CI/CD Pipeline

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ develop, main ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run linting
      run: npm run lint
    
    - name: Run unit tests
      run: npm test
      env:
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
        REDIS_URL: redis://localhost:6379
    
    - name: Run API tests with Robot Framework
      run: |
        pip install robotframework
        pip install robotframework-requests
        npm run test:api
    
    - name: Upload coverage reports
      uses: codecov/codecov-action@v3
    
    - name: Upload test results
      if: always()
      uses: actions/upload-artifact@v3
      with:
        name: test-results
        path: |
          test-results/
          coverage/
          robot-reports/

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/develop'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Login to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
    
    - name: Build and push backend
      uses: docker/build-push-action@v4
      with:
        context: ./backend
        push: true
        tags: |
          ${{ secrets.DOCKER_USERNAME }}/microservices-backend:latest
          ${{ secrets.DOCKER_USERNAME }}/microservices-backend:${{ github.sha }}
    
    - name: Build and push frontend
      uses: docker/build-push-action@v4
      with:
        context: ./frontend
        push: true
        tags: |
          ${{ secrets.DOCKER_USERNAME }}/microservices-frontend:latest
          ${{ secrets.DOCKER_USERNAME }}/microservices-frontend:${{ github.sha }}
    
    - name: Update deployment manifest
      run: |
        sed -i "s|image:.*microservices-backend.*|image: ${{ secrets.DOCKER_USERNAME }}/microservices-backend:${{ github.sha }}|" k8s/deployment.yaml
        sed -i "s|image:.*microservices-frontend.*|image: ${{ secrets.DOCKER_USERNAME }}/microservices-frontend:${{ github.sha }}|" k8s/deployment.yaml
    
    - name: Deploy to staging
      uses: appleboy/ssh-action@v0.1.5
      with:
        host: ${{ secrets.STAGING_HOST }}
        username: ${{ secrets.STAGING_USERNAME }}
        key: ${{ secrets.STAGING_SSH_KEY }}
        script: |
          cd /opt/microservices
          git pull origin develop
          docker-compose -f docker-compose.staging.yml pull
          docker-compose -f docker-compose.staging.yml up -d

  production-deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Deploy to production
      uses: appleboy/ssh-action@v0.1.5
      with:
        host: ${{ secrets.PRODUCTION_HOST }}
        username: ${{ secrets.PRODUCTION_USERNAME }}
        key: ${{ secrets.PRODUCTION_SSH_KEY }}
        script: |
          cd /opt/microservices
          git pull origin main
          
          # Run database migrations
          docker-compose -f docker-compose.prod.yml run --rm api npm run migration:run
          
          # Deploy services
          docker-compose -f docker-compose.prod.yml pull
          docker-compose -f docker-compose.prod.yml up -d --scale api=3
          
          # Health check
          sleep 30
          curl -f http://localhost:3000/health || exit 1
          
          # Clean up old images
          docker image prune -f
```

## 8. Testing ด้วย Robot Framework

### 8.1 Comprehensive Test Suite

```robot
*** Settings ***
Documentation    Microservices API Test Suite
Library          RequestsLibrary
Library          Collections
Library          OperatingSystem
Library          String
Library          DateTime
Library          DatabaseLibrary
Library          JSONLibrary

Suite Setup      Suite Setup
Suite Teardown   Suite Teardown
Test Setup       Test Setup
Test Teardown    Test Setup

*** Variables ***
${BASE_URL}      http://localhost:3000/api
${DB_HOST}       localhost
${DB_PORT}       5432
${DB_NAME}       test_db
${DB_USER}       postgres
${DB_PASSWORD}   postgres

*** Test Cases ***

User Registration Success
    [Documentation]    Test successful user registration
    [Tags]             auth    smoke    registration
    ${headers}=        Create Dictionary    Content-Type=application/json
    ${email}=          Generate Random String    8    [LOWER]
    ${email}=          Catenate    SEPARATOR=@    ${email}    example.com
    ${payload}=        Create Dictionary
    ...                email=${email}
    ...                username=testuser${RANDOM}
    ...                password=Test123!
    
    ${response}=       POST    ${BASE_URL}/auth/register    json=${payload}    headers=${headers}
    
    Status Should Be    201    ${response}
    Should Be Equal     ${response.json()['email']}    ${email}
    Dictionary Should Contain Key    ${response.json()}    id
    
    # Verify in database
    Check If Exists In Database
    ...    SELECT COUNT(*) FROM users WHERE email = '${email}'

User Registration Validation
    [Documentation]    Test registration validation errors
    [Tags]             auth    validation
    [Template]         Test Invalid Registration
    
    # email        username    password    expected_error
    invalid-email  testuser    Test123!    Email is invalid
    ${EMPTY}       testuser    Test123!    Email should not be empty
    test@ex.com    ${EMPTY}    Test123!    Username should not be empty
    test@ex.com    testuser    weak        Password is too weak

User Login Success
    [Documentation]    Test successful login
    [Tags]             auth    smoke    login
    ${headers}=        Create Dictionary    Content-Type=application/json
    ${payload}=        Create Dictionary
    ...                email=admin@example.com
    ...                password=Admin123!
    
    ${response}=       POST    ${BASE_URL}/auth/login    json=${payload}    headers=${headers}
    
    Status Should Be    200    ${response}
    Dictionary Should Contain Key    ${response.json()}    accessToken
    Dictionary Should Contain Key    ${response.json()}    refreshToken
    
    Set Suite Variable    ${ACCESS_TOKEN}    ${response.json()['accessToken']}
    Set Suite Variable    ${REFRESH_TOKEN}    ${response.json()['refreshToken']}

Product CRUD Operations
    [Documentation]    Test complete product lifecycle
    [Tags]             product    crud    smoke
    [Setup]            Get Auth Token
    
    # Create Product
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    ${sku}=            Generate Random String    10    [LOWER][NUMBERS]
    ${payload}=        Create Dictionary
    ...                sku=${sku}
    ...                name=Test Product ${RANDOM}
    ...                description=Test Description
    ...                price=99.99
    ...                category=Electronics
    ...                initialQuantity=100
    
    ${response}=       POST    ${BASE_URL}/products    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    ${product_id}=     Set Variable    ${response.json()['id']}
    
    # Get Product
    ${response}=       GET    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal     ${response.json()['sku']}    ${sku}
    
    # Update Product
    ${update_payload}= Create Dictionary    price=149.99
    ${response}=       PUT    ${BASE_URL}/products/${product_id}    json=${update_payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal As Numbers    ${response.json()['price']}    149.99
    
    # Delete Product
    ${response}=       DELETE    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    204    ${response}
    
    # Verify deletion
    ${response}=       GET    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    404    ${response}

Inventory Management
    [Documentation]    Test inventory operations
    [Tags]             inventory    smoke
    [Setup]            Get Auth Token
    
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    # Get inventory
    ${response}=       GET    ${BASE_URL}/inventory    headers=${headers}
    Status Should Be    200    ${response}
    ${inventory}=      Set Variable    ${response.json()['data'][0]}
    
    # Update stock
    ${payload}=        Create Dictionary
    ...                quantity=50
    ...                type=RECEIVED
    ...                notes=Restock from supplier
    
    ${response}=       POST    ${BASE_URL}/inventory/${inventory['id']}/adjust    json=${payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal As Numbers    ${response.json()['newQuantity']}    ${inventory['quantity'] + 50}
    
    # Check stock history
    ${response}=       GET    ${BASE_URL}/inventory/${inventory['id']}/history    headers=${headers}
    Status Should Be    200    ${response}
    Length Should Be    ${response.json()['data']}    ${1}

Order Processing Flow
    [Documentation]    Test complete order flow
    [Tags]             order    e2e
    [Setup]            Get Auth Token
    
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    # Create order
    ${payload}=        Create Dictionary
    ...                items=${[
    ...                    {"productId": "prod-123", "quantity": 2},
    ...                    {"productId": "prod-456", "quantity": 1}
    ...                ]}
    ...                shippingAddress=${{
    ...                    "street": "123 Main St",
    ...                    "city": "Bangkok",
    ...                    "postalCode": "10110",
    ...                    "country": "Thailand"
    ...                }}
    
    ${response}=       POST    ${BASE_URL}/orders    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    ${order_id}=       Set Variable    ${response.json()['id']}
    
    # Process payment
    ${payload}=        Create Dictionary
    ...                paymentMethod=credit_card
    ...                paymentDetails=${{
    ...                    "cardNumber": "4111111111111111",
    ...                    "expiry": "12/25",
    ...                    "cvc": "123"
    ...                }}
    
    ${response}=       POST    ${BASE_URL}/orders/${order_id}/pay    json=${payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal     ${response.json()['status']}    PAID
    
    # Verify inventory updated
    ${response}=       GET    ${BASE_URL}/inventory/prod-123    headers=${headers}
    ${current_stock}=  Set Variable    ${response.json()['quantity']}
    Should Be True     ${current_stock} < ${initial_stock}

Performance Test - Load Testing
    [Documentation]    Test API performance under load
    [Tags]             performance    load
    [Template]         Load Test Endpoint
    
    # endpoint          method    expected_status    concurrent_users    requests_per_user
    /api/products       GET       200                10                  100
    /api/auth/login     POST      200                5                   50
    /api/orders         POST      201                3                   20

*** Keywords ***

Suite Setup
    Connect To Database    psycopg2    ${DB_NAME}    ${DB_USER}    ${DB_PASSWORD}    ${DB_HOST}    ${DB_PORT}
    
    # Create test data
    Execute SQL String
    ...    INSERT INTO users (id, email, username, password_hash) 
    ...    VALUES ('test-user-id', 'admin@example.com', 'admin', 'hashed_password')
    
    Execute SQL String
    ...    INSERT INTO products (id, sku, name, price, inventory_quantity)
    ...    VALUES ('prod-123', 'TEST-001', 'Test Product', 99.99, 100)

Test Setup
    # Clear test data between tests
    Execute SQL String    DELETE FROM orders WHERE user_id = 'test-user-id'
    Execute SQL String    DELETE FROM inventory_transactions WHERE created_by = 'test-user-id'

Test Invalid Registration
    [Arguments]    ${email}    ${username}    ${password}    ${expected_error}
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...            email=${email}
    ...            username=${username}
    ...            password=${password}
    
    ${response}=   POST    ${BASE_URL}/auth/register    json=${payload}    headers=${headers}
    
    Status Should Be    400    ${response}
    Should Contain      ${response.json()['message']}    ${expected_error}

Get Auth Token
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...            email=admin@example.com
    ...            password=Admin123!
    
    ${response}=   POST    ${BASE_URL}/auth/login    json=${payload}    headers=${headers}
    ${ACCESS_TOKEN}=    Set Variable    ${response.json()['accessToken']}
    Set Suite Variable    ${ACCESS_TOKEN}

Load Test Endpoint
    [Arguments]    ${endpoint}    ${method}    ${expected_status}    ${concurrent_users}    ${requests_per_user}
    
    ${total_requests}=    Evaluate    ${concurrent_users} * ${requests_per_user}
    ${start_time}=    Get Current Date
    
    FOR    ${i}    IN RANGE    ${concurrent_users}
        ${thread}=    Start Thread    Run Concurrent Requests    ${endpoint}    ${method}    ${requests_per_user}
    END
    
    Wait Until All Threads Are Done
    
    ${end_time}=    Get Current Date
    ${duration}=    Subtract Date From Date    ${end_time}    ${start_time}
    ${rps}=         Evaluate    ${total_requests} / ${duration}
    
    Log    Performance Results: ${rps:.2f} requests/second
    Should Be True    ${rps} > 50    Minimum 50 requests/second required

Run Concurrent Requests
    [Arguments]    ${endpoint}    ${method}    ${count}
    FOR    ${j}    IN RANGE    ${count}
        ${response}=    Run Keyword If    '${method}' == 'GET'    GET    ${BASE_URL}${endpoint}
        ...    ELSE IF    '${method}' == 'POST'    POST    ${BASE_URL}${endpoint}
        
        Status Should Be    ${expected_status}    ${response}
    END
```

## 9. Docker Compose Configuration

### 9.1 Complete docker-compose.yml

```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-microservices}
      POSTGRES_USER: ${POSTGRES_USER:-admin}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-admin123}
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./postgres/init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5
    deploy:
      resources:
        limits:
          memory: 1G
        reservations:
          memory: 512M

  # Redis Cache
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD:-redis123}
    volumes:
      - redis_data:/data
    ports:
      - "6379:6379"
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD", "redis-cli", "--raw", "incr", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Kafka Cluster
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SYNC_LIMIT: 2
    ports:
      - "2181:2181"
    networks:
      - microservices-network

  kafka:
    image: confluentinc/cp-kafka:7.4.0
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
    ports:
      - "9092:9092"
    volumes:
      - kafka_data:/var/lib/kafka/data
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD", "kafka-topics", "--list", "--bootstrap-server", "localhost:9092"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Kafka UI
  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    depends_on:
      - kafka
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
      DYNAMIC_CONFIG_ENABLED: 'true'
    ports:
      - "8080:8080"
    networks:
      - microservices-network

  # Elasticsearch
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
      - cluster.name=es-microservices
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9200/_cluster/health"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Logstash
  logstash:
    image: docker.elastic.co/logstash/logstash:8.10.0
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
    ports:
      - "5000:5000"
      - "9600:9600"
    networks:
      - microservices-network
    depends_on:
      - elasticsearch

  # Kibana
  kibana:
    image: docker.elastic.co/kibana/kibana:8.10.0
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    networks:
      - microservices-network
    depends_on:
      - elasticsearch

  # Grafana
  grafana:
    image: grafana/grafana:10.0.0
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_ADMIN_PASSWORD:-admin123}
      - GF_INSTALL_PLUGINS=grafana-piechart-panel
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
      - ./grafana/dashboards:/var/lib/grafana/dashboards
    ports:
      - "3000:3000"
    networks:
      - microservices-network

  # Prometheus
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--storage.tsdb.retention.time=200h'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"
    networks:
      - microservices-network

  # n8n
  n8n:
    image: n8nio/n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER:-admin}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD:-admin123}
      - N8N_HOST=${N8N_HOST:-localhost}
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - N8N_WEBHOOK_URL=https://your-domain.com/
      - GENERIC_TIMEZONE=Asia/Bangkok
      - N8N_USER_MANAGEMENT_DISABLED=false
      - N8N_DIAGNOSTICS_ENABLED=false
    volumes:
      - n8n_data:/home/node/.n8n
    ports:
      - "5678:5678"
    networks:
      - microservices-network

  # Backend API Service
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile
      args:
        - NODE_ENV=production
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://:${REDIS_PASSWORD}@redis:6379
      - KAFKA_BROKER=kafka:9092
      - ELASTICSEARCH_HOST=http://elasticsearch:9200
      - JWT_ACCESS_SECRET=${JWT_ACCESS_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
      - LOG_LEVEL=info
    volumes:
      - ./backend:/app
      - /app/node_modules
    ports:
      - "3001:3001"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      kafka:
        condition: service_healthy
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first
      resources:
        limits:
          memory: 1G
        reservations:
          memory: 512M

  # Frontend Application
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      args:
        - NEXT_PUBLIC_API_URL=http://api:3001
    environment:
      - NEXT_PUBLIC_API_URL=http://api:3001
      - NODE_ENV=production
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next
    ports:
      - "3000:3000"
    depends_on:
      - api
    networks:
      - microservices-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/ssl:/etc/nginx/ssl
    ports:
      - "80:80"
      - "443:443"
    depends_on:
      - frontend
      - api
    networks:
      - microservices-network

  # Portainer for container management
  portainer:
    image: portainer/portainer-ce:latest
    command: -H unix:///var/run/docker.sock
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    ports:
      - "9000:9000"
    networks:
      - microservices-network

networks:
  microservices-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

volumes:
  postgres_data:
  redis_data:
  kafka_data:
  elasticsearch_data:
  grafana_data:
  prometheus_data:
  n8n_data:
  portainer_data:
```

### 9.2 Nginx Configuration

```nginx
# nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 100M;

    # Gzip Settings
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:;" always;

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=100r/m;
    limit_req_zone $binary_remote_addr zone=auth:10m rate=10r/m;

    # Load balancing upstreams
    upstream backend {
        least_conn;
        server api:3001 max_fails=3 fail_timeout=30s;
        server api2:3001 max_fails=3 fail_timeout=30s;
        server api3:3001 max_fails=3 fail_timeout=30s;
        keepalive 32;
    }

    upstream frontend {
        server frontend:3000;
        keepalive 32;
    }

    server {
        listen 80;
        server_name _;
        return 301 https://$host$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name your-domain.com;

        ssl_certificate /etc/nginx/ssl/certificate.crt;
        ssl_certificate_key /etc/nginx/ssl/private.key;
        
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;
        
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
        ssl_session_tickets off;

        # Frontend
        location / {
            proxy_pass http://frontend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
            proxy_buffering off;
            proxy_read_timeout 300s;
        }

        # API Gateway
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
            
            # Timeouts
            proxy_connect_timeout 30s;
            proxy_send_timeout 30s;
            proxy_read_timeout 30s;
            
            # Buffering
            proxy_buffering on;
            proxy_buffer_size 4k;
            proxy_buffers 8 4k;
            proxy_busy_buffers_size 8k;
        }

        # Authentication endpoints - stricter rate limiting
        location /api/auth/ {
            limit_req zone=auth burst=5 nodelay;
            
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Health check endpoint
        location /health {
            access_log off;
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
        }

        # Static files
        location /static/ {
            alias /app/static/;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }

        # Deny access to hidden files
        location ~ /\. {
            deny all;
            access_log off;
            log_not_found off;
        }
    }
}
```

## 10. Security Best Practices และ Performance Optimization

### 10.1 Security Implementation

```typescript
// Security Configuration Module
@Module({
  imports: [
    ConfigModule,
    ThrottlerModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        throttlers: [
          {
            ttl: config.get('THROTTLE_TTL', 60),
            limit: config.get('THROTTLE_LIMIT', 100),
          },
        ],
      }),
    }),
  ],
  providers: [
    {
      provide: APP_GUARD,
      useClass: ThrottlerGuard,
    },
    SecurityService,
  ],
})
export class SecurityModule {}

// Security Service
@Injectable()
export class SecurityService {
  constructor(
    private readonly configService: ConfigService,
    private readonly auditService: AuditService,
  ) {}

  async validatePassword(password: string): Promise<ValidationResult> {
    const errors: string[] = [];
    
    // Length check
    if (password.length < 8) {
      errors.push('Password must be at least 8 characters long');
    }
    
    // Complexity check
    const hasUpperCase = /[A-Z]/.test(password);
    const hasLowerCase = /[a-z]/.test(password);
    const hasNumbers = /\d/.test(password);
    const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password);
    
    if (!hasUpperCase) {
      errors.push('Password must contain at least one uppercase letter');
    }
    
    if (!hasLowerCase) {
      errors.push('Password must contain at least one lowercase letter');
    }
    
    if (!hasNumbers) {
      errors.push('Password must contain at least one number');
    }
    
    if (!hasSpecialChar) {
      errors.push('Password must contain at least one special character');
    }
    
    // Check against common passwords
    const commonPasswords = ['password', '123456', 'qwerty'];
    if (commonPasswords.includes(password.toLowerCase())) {
      errors.push('Password is too common');
    }
    
    // Check password history (last 5 passwords)
    const isReused = await this.checkPasswordHistory(password);
    if (isReused) {
      errors.push('Password has been used recently');
    }
    
    return {
      isValid: errors.length === 0,
      errors,
    };
  }

  async sanitizeInput(input: string): Promise<string> {
    // Remove HTML tags
    let sanitized = input.replace(/<[^>]*>/g, '');
    
    // Escape special characters
    sanitized = sanitized
      .replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#x27;')
      .replace(/\//g, '&#x2F;');
    
    return sanitized;
  }

  async validateEmail(email: string): Promise<boolean> {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    
    if (!emailRegex.test(email)) {
      return false;
    }
    
    // Check domain MX records
    const domain = email.split('@')[1];
    try {
      const mxRecords = await dns.promises.resolveMx(domain);
      return mxRecords.length > 0;
    } catch {
      return false;
    }
  }

  async logSecurityEvent(event: SecurityEvent): Promise<void> {
    await this.auditService.logAuditEvent({
      action: event.type,
      userId: event.userId,
      entityType: 'SECURITY',
      entityId: event.id,
      oldValues: null,
      newValues: event.details,
      ipAddress: event.ip,
      userAgent: event.userAgent,
      metadata: {
        severity: event.severity,
        source: event.source,
        timestamp: new Date().toISOString(),
      },
    });

    // Alert on high severity events
    if (event.severity === 'HIGH') {
      await this.sendSecurityAlert(event);
    }
  }

  async generateCSRFToken(): Promise<string> {
    return crypto.randomBytes(32).toString('hex');
  }

  async validateCSRFToken(token: string, sessionToken: string): Promise<boolean> {
    const expected = await this.getExpectedCSRFToken(sessionToken);
    return crypto.timingSafeEqual(
      Buffer.from(token),
      Buffer.from(expected),
    );
  }
}

// Input Validation Pipe
@Injectable()
export class CustomValidationPipe extends ValidationPipe {
  constructor() {
    super({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      },
      exceptionFactory: (errors) => {
        const result = errors.map((error) => ({
          property: error.property,
          message: error.constraints[Object.keys(error.constraints)[0]],
        }));
        return new BadRequestException(result);
      },
    });
  }
}

// SQL Injection Protection
@EntityRepository(User)
export class UserRepository extends Repository<User> {
  async safeFindByEmail(email: string): Promise<User | null> {
    // Using parameterized queries to prevent SQL injection
    return this.createQueryBuilder('user')
      .where('user.email = :email', { email })
      .andWhere('user.isActive = :isActive', { isActive: true })
      .getOne();
  }

  async searchUsersSafe(searchTerm: string): Promise<User[]> {
    // Escape special characters for LIKE
    const escapedTerm = searchTerm.replace(/[%_]/g, '\\$&');
    
    return this.createQueryBuilder('user')
      .where('user.username LIKE :term', { term: `%${escapedTerm}%` })
      .orWhere('user.email LIKE :term', { term: `%${escapedTerm}%` })
      .getMany();
  }
}
```

### 10.2 Performance Optimization

```typescript
// Caching Strategy
@Injectable()
export class CacheService {
  constructor(private readonly redisService: RedisService) {}

  // Multi-level caching
  async getWithCache<T>(
    key: string,
    fetcher: () => Promise<T>,
    options: CacheOptions = {},
  ): Promise<T> {
    const {
      ttl = 3600,
      staleWhileRevalidate = 600,
      tags = [],
      forceRefresh = false,
    } = options;

    if (forceRefresh) {
      return await this.fetchAndCache(key, fetcher, ttl, tags);
    }

    // Try to get fresh data
    const fresh = await this.redisService.get<T>(`fresh:${key}`);
    if (fresh !== null) {
      return fresh;
    }

    // Try to get stale data
    const stale = await this.redisService.get<T>(`stale:${key}`);
    if (stale !== null) {
      // Refresh in background
      this.refreshInBackground(key, fetcher, ttl, tags);
      return stale;
    }

    // Fetch and cache
    return await this.fetchAndCache(key, fetcher, ttl, tags);
  }

  private async fetchAndCache<T>(
    key: string,
    fetcher: () => Promise<T>,
    ttl: number,
    tags: string[],
  ): Promise<T> {
    const data = await fetcher();
    
    // Store as fresh
    await this.redisService.set(`fresh:${key}`, data, ttl);
    
    // Store as stale (with longer TTL)
    await this.redisService.set(`stale:${key}`, data, ttl + 600);
    
    // Store tag relationships
    for (const tag of tags) {
      await this.redisService.sadd(`tag:${tag}`, key);
    }
    
    return data;
  }

  private async refreshInBackground<T>(
    key: string,
    fetcher: () => Promise<T>,
    ttl: number,
    tags: string[],
  ): Promise<void> {
    // Use queue for background refresh
    setImmediate(async () => {
      try {
        await this.fetchAndCache(key, fetcher, ttl, tags);
      } catch (error) {
        console.error(`Background refresh failed for key ${key}:`, error);
      }
    });
  }
}

// Database Query Optimization
@EntityRepository(Product)
export class ProductRepository extends Repository<Product> {
  async findProductsWithOptimizedQuery(
    criteria: ProductCriteria,
  ): Promise<Product[]> {
    const query = this.createQueryBuilder('product')
      .select([
        'product.id',
        'product.name',
        'product.sku',
        'product.price',
        'product.createdAt',
        'inventory.quantity',
        'category.name',
      ])
      .leftJoin('product.inventory', 'inventory')
      .leftJoin('product.categories', 'category')
      .where('product.isActive = :isActive', { isActive: true })
      .andWhere('inventory.quantity > 0');

    if (criteria.categoryId) {
      query.andWhere('category.id = :categoryId', { categoryId: criteria.categoryId });
    }

    if (criteria.minPrice) {
      query.andWhere('product.price >= :minPrice', { minPrice: criteria.minPrice });
    }

    if (criteria.maxPrice) {
      query.andWhere('product.price <= :maxPrice', { maxPrice: criteria.maxPrice });
    }

    // Use indexes
    query.useIndex('idx_product_price');
    query.useIndex('idx_product_category');

    // Pagination
    query.skip((criteria.page - 1) * criteria.limit)
      .take(criteria.limit);

    // Explain plan for debugging
    if (process.env.NODE_ENV === 'development') {
      const explain = await query.explain();
      console.log('Query explain:', explain);
    }

    return query.getMany();
  }

  async batchUpdateProducts(updates: ProductUpdate[]): Promise<void> {
    const chunkSize = 1000;
    
    for (let i = 0; i < updates.length; i += chunkSize) {
      const chunk = updates.slice(i, i + chunkSize);
      
      await this.createQueryBuilder()
        .update(Product)
        .set({
          price: () => 'CASE id ' + 
            chunk.map(update => `WHEN '${update.id}' THEN ${update.price}`).join(' ') +
            ' END',
          updatedAt: new Date(),
        })
        .whereInIds(chunk.map(u => u.id))
        .execute();
    }
  }
}

// Connection Pool Optimization
// database.config.ts
export const databaseConfig: TypeOrmModuleOptions = {
  type: 'postgres',
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT),
  username: process.env.DB_USERNAME,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  entities: [__dirname + '/../**/*.entity{.ts,.js}'],
  synchronize: false,
  migrationsRun: true,
  logging: process.env.NODE_ENV === 'development',
  maxQueryExecutionTime: 1000,
  extra: {
    // Connection pool settings
    max: 20, // Maximum number of connections
    min: 5,  // Minimum number of connections
    idleTimeoutMillis: 30000,
    connectionTimeoutMillis: 2000,
  },
  cache: {
    type: 'redis',
    options: {
      host: process.env.REDIS_HOST,
      port: parseInt(process.env.REDIS_PORT),
      password: process.env.REDIS_PASSWORD,
    },
    duration: 30000, // 30 seconds cache
    ignoreErrors: true,
  },
};
```

### 10.3 Monitoring and Alerting

```typescript
// Monitoring Service
@Injectable()
export class MonitoringService {
  constructor(
    private readonly httpService: HttpService,
    private readonly logger: Logger,
  ) {}

  async checkServiceHealth(): Promise<HealthStatus[]> {
    const services = [
      { name: 'API', url: 'http://api:3001/health' },
      { name: 'Database', url: 'http://postgres:5432' },
      { name: 'Redis', url: 'http://redis:6379' },
      { name: 'Kafka', url: 'http://kafka:9092' },
      { name: 'Elasticsearch', url: 'http://elasticsearch:9200' },
    ];

    const checks = await Promise.allSettled(
      services.map(async (service) => {
        try {
          await this.httpService.get(service.url).toPromise();
          return { ...service, status: 'healthy', timestamp: new Date() };
        } catch (error) {
          return { 
            ...service, 
            status: 'unhealthy', 
            error: error.message,
            timestamp: new Date() 
          };
        }
      })
    );

    const results = checks.map(check => 
      check.status === 'fulfilled' ? check.value : check.reason
    );

    // Log unhealthy services
    const unhealthy = results.filter(r => r.status === 'unhealthy');
    if (unhealthy.length > 0) {
      this.logger.error('Unhealthy services detected', { unhealthy });
      await this.sendAlert(unhealthy);
    }

    return results;
  }

  async collectMetrics(): Promise<SystemMetrics> {
    const metrics: SystemMetrics = {
      timestamp: new Date(),
      cpu: await this.getCpuUsage(),
      memory: await this.getMemoryUsage(),
      disk: await this.getDiskUsage(),
      network: await this.getNetworkStats(),
      process: await this.getProcessStats(),
      database: await this.getDatabaseMetrics(),
      redis: await this.getRedisMetrics(),
    };

    // Store metrics in timeseries database
    await this.storeMetrics(metrics);

    return metrics;
  }

  private async getDatabaseMetrics(): Promise<DatabaseMetrics> {
    const queryRunner = this.dataSource.createQueryRunner();
    
    try {
      const connections = await queryRunner.query(
        'SELECT count(*) as count FROM pg_stat_activity WHERE state = \'active\''
      );
      
      const locks = await queryRunner.query(
        'SELECT count(*) as count FROM pg_locks WHERE granted = false'
      );
      
      const size = await queryRunner.query(
        'SELECT pg_database_size(current_database()) as size'
      );

      return {
        connections: parseInt(connections[0].count),
        waitingLocks: parseInt(locks[0].count),
        databaseSize: parseInt(size[0].size),
      };
    } finally {
      await queryRunner.release();
    }
  }

  async setupPerformanceMonitoring(): Promise<void> {
    // Monitor response times
    setInterval(async () => {
      const metrics = await this.collectMetrics();
      
      // Check thresholds
      if (metrics.memory.usage > 0.8) {
        await this.sendAlert({
          type: 'HIGH_MEMORY_USAGE',
          severity: 'WARNING',
          details: `Memory usage at ${(metrics.memory.usage * 100).toFixed(2)}%`,
          timestamp: new Date(),
        });
      }
      
      if (metrics.database.connections > 15) {
        await this.sendAlert({
          type: 'HIGH_DB_CONNECTIONS',
          severity: 'WARNING',
          details: `Database connections at ${metrics.database.connections}`,
          timestamp: new Date(),
        });
      }
    }, 60000); // Check every minute
  }
}
```

### 10.4 Load Balancing and Auto-scaling

```yaml
# docker-compose.scale.yml
version: '3.8'

services:
  api:
    deploy:
      mode: replicated
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first
      rollback_config:
        parallelism: 0
        delay: 10s
        failure_action: pause
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      resources:
        limits:
          cpus: '0.5'
          memory: 1G
        reservations:
          cpus: '0.25'
          memory: 512M
      placement:
        constraints:
          - node.role == worker
      labels:
        - "traefik.enable=true"
        - "traefik.http.routers.api.rule=PathPrefix(`/api`)"
        - "traefik.http.services.api.loadbalancer.sticky=true"
        - "traefik.http.services.api.loadbalancer.sticky.cookie.name=api_session"

  traefik:
    image: traefik:v2.10
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=admin@example.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "./letsencrypt:/letsencrypt"
    networks:
      - microservices-network
```

เอกสารนี้ครอบคลุมทุกหัวข้อที่ระบุไว้อย่างละเอียด:

1. **NestJS Architecture** - โครงสร้างโมดูล, Dependency Injection, Middleware, Guards, Interceptors
2. **CRUD with TypeORM** - Entities, Repositories, Transactions, Relations, Query Builder
3. **JWT Authentication** - Access/Refresh Tokens, Token Rotation, Redis Storage, Axios Interceptors
4. **Microservices Architecture** - Service Breakdown, Kafka Events, gRPC, API Gateway
5. **Complete Systems** - User, Product, Inventory, Shipping, Reports, Audit Systems
6. **Technology Stack** - Redis, Kafka, ELK, Grafana, n8n Integration
7. **Git Flow & CI/CD** - Branch Strategy, GitHub Actions, Docker Builds, Deployment
8. **Robot Framework Testing** - API Tests, Database Tests, Performance Tests
9. **Docker Compose** - Multi-service Setup, Networking, Volumes, Health Checks
10. **Security & Performance** - Input Validation, SQL Injection Protection, Caching, Monitoring

ระบบนี้พร้อมสำหรับการใช้งานจริงใน production environment พร้อมรองรับ scalability, security, และ maintainability

# ----------- #############################

# ตัวอย่างโค้ดแบบละเอียดทั้งหมด

## 1. NestJS Architecture - โครงสร้างโมดูล, Dependency Injection, Middleware, Guards, Interceptors

### โครงสร้างโมดูล
```typescript
// src/app.module.ts
import { Module, CacheModule } from '@nestjs/common';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { TypeOrmModule } from '@nestjs/typeorm';
import { ThrottlerModule } from '@nestjs/throttler';
import { ScheduleModule } from '@nestjs/schedule';
import { BullModule } from '@nestjs/bull';
import { EventEmitterModule } from '@nestjs/event-emitter';

@Module({
  imports: [
    // Configuration
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV || 'development'}`,
    }),

    // Database
    TypeOrmModule.forRootAsync({
      imports: [ConfigModule],
      useFactory: (configService: ConfigService) => ({
        type: 'postgres',
        host: configService.get('DB_HOST'),
        port: configService.get('DB_PORT'),
        username: configService.get('DB_USERNAME'),
        password: configService.get('DB_PASSWORD'),
        database: configService.get('DB_NAME'),
        entities: [__dirname + '/**/*.entity{.ts,.js}'],
        synchronize: configService.get('NODE_ENV') === 'development',
        logging: configService.get('NODE_ENV') === 'development',
        cache: {
          type: 'redis',
          options: {
            host: configService.get('REDIS_HOST'),
            port: configService.get('REDIS_PORT'),
          },
        },
      }),
      inject: [ConfigService],
    }),

    // Rate limiting
    ThrottlerModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: (config: ConfigService) => ({
        ttl: config.get('THROTTLE_TTL', 60),
        limit: config.get('THROTTLE_LIMIT', 100),
      }),
    }),

    // Caching
    CacheModule.registerAsync({
      imports: [ConfigModule],
      useFactory: (configService: ConfigService) => ({
        store: 'redis',
        host: configService.get('REDIS_HOST'),
        port: configService.get('REDIS_PORT'),
        ttl: configService.get('CACHE_TTL', 3600),
      }),
      inject: [ConfigService],
    }),

    // Queue
    BullModule.forRootAsync({
      imports: [ConfigModule],
      useFactory: (configService: ConfigService) => ({
        redis: {
          host: configService.get('REDIS_HOST'),
          port: configService.get('REDIS_PORT'),
        },
      }),
      inject: [ConfigService],
    }),

    // Event emitter
    EventEmitterModule.forRoot(),

    // Scheduled tasks
    ScheduleModule.forRoot(),

    // Application modules
    AuthModule,
    UsersModule,
    ProductsModule,
    InventoryModule,
    OrdersModule,
    ShippingModule,
    ReportsModule,
    AuditModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

### Dependency Injection Example
```typescript
// src/modules/users/users.module.ts
import { Module, forwardRef } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';
import { User } from './entities/user.entity';
import { UserRepository } from './repositories/user.repository';
import { AuditModule } from '../audit/audit.module';
import { NotificationModule } from '../notification/notification.module';

@Module({
  imports: [
    TypeOrmModule.forFeature([User]),
    forwardRef(() => AuditModule), // Circular dependency
    NotificationModule,
  ],
  controllers: [UsersController],
  providers: [
    UsersService,
    UserRepository,
    {
      provide: 'USER_CONFIG',
      useValue: {
        maxLoginAttempts: 5,
        passwordExpiryDays: 90,
      },
    },
    {
      provide: 'EMAIL_SERVICE',
      useClass: EmailService,
    },
  ],
  exports: [UsersService, TypeOrmModule],
})
export class UsersModule {}
```

### Middleware Example
```typescript
// src/common/middleware/logging.middleware.ts
import { Injectable, NestMiddleware, Logger } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggingMiddleware implements NestMiddleware {
  private logger = new Logger('HTTP');

  use(req: Request, res: Response, next: NextFunction) {
    const { method, originalUrl, ip } = req;
    const userAgent = req.get('user-agent') || '';
    const startTime = Date.now();

    // Log request
    this.logger.log(
      `${method} ${originalUrl} - ${userAgent} ${ip}`,
    );

    // Capture response
    res.on('finish', () => {
      const { statusCode } = res;
      const contentLength = res.get('content-length');
      const duration = Date.now() - startTime;

      const logLevel = statusCode >= 400 ? 'error' : 'log';
      this.logger[logLevel](
        `${method} ${originalUrl} ${statusCode} ${contentLength} - ${duration}ms - ${userAgent} ${ip}`,
      );
    });

    next();
  }
}
```

### Guard Example
```typescript
// src/modules/auth/guards/jwt-auth.guard.ts
import {
  Injectable,
  CanActivate,
  ExecutionContext,
  UnauthorizedException,
  ForbiddenException,
} from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { JwtService } from '@nestjs/jwt';
import { Request } from 'express';

@Injectable()
export class JwtAuthGuard implements CanActivate {
  constructor(
    private readonly jwtService: JwtService,
    private readonly reflector: Reflector,
  ) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const token = this.extractTokenFromHeader(request);

    if (!token) {
      throw new UnauthorizedException('No token provided');
    }

    try {
      const payload = await this.jwtService.verifyAsync(token, {
        secret: process.env.JWT_SECRET,
      });

      // Attach user to request
      request.user = payload;

      // Check roles if required
      const requiredRoles = this.reflector.get<string[]>(
        'roles',
        context.getHandler(),
      );

      if (requiredRoles) {
        const hasRole = requiredRoles.some((role) =>
          payload.roles?.includes(role),
        );

        if (!hasRole) {
          throw new ForbiddenException(
            `Required roles: ${requiredRoles.join(', ')}`,
          );
        }
      }

      return true;
    } catch (error) {
      if (error.name === 'TokenExpiredError') {
        throw new UnauthorizedException('Token expired');
      }
      throw new UnauthorizedException('Invalid token');
    }
  }

  private extractTokenFromHeader(request: Request): string | undefined {
    const [type, token] = request.headers.authorization?.split(' ') ?? [];
    return type === 'Bearer' ? token : undefined;
  }
}
```

### Interceptor Example
```typescript
// src/common/interceptors/transform.interceptor.ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
} from '@nestjs/common';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';
import { classToPlain } from 'class-transformer';

export interface Response<T> {
  success: boolean;
  data: T;
  timestamp: string;
  path: string;
}

@Injectable()
export class TransformInterceptor<T>
  implements NestInterceptor<T, Response<T>>
{
  intercept(
    context: ExecutionContext,
    next: CallHandler,
  ): Observable<Response<T>> {
    const request = context.switchToHttp().getRequest();
    
    return next.handle().pipe(
      map((data) => ({
        success: true,
        data: classToPlain(data) as T,
        timestamp: new Date().toISOString(),
        path: request.url,
        requestId: request.requestId,
      })),
    );
  }
}

// src/common/interceptors/timeout.interceptor.ts
import {
  Injectable,
  NestInterceptor,
  ExecutionContext,
  CallHandler,
  RequestTimeoutException,
} from '@nestjs/common';
import { Observable, throwError, TimeoutError } from 'rxjs';
import { catchError, timeout } from 'rxjs/operators';

@Injectable()
export class TimeoutInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(
      timeout(10000), // 10 seconds timeout
      catchError((err) => {
        if (err instanceof TimeoutError) {
          return throwError(() => new RequestTimeoutException());
        }
        return throwError(() => err);
      }),
    );
  }
}
```

## 2. CRUD with TypeORM - Entities, Repositories, Transactions, Relations, Query Builder

### Entity Example
```typescript
// src/modules/users/entities/user.entity.ts
import {
  Entity,
  Column,
  PrimaryGeneratedColumn,
  CreateDateColumn,
  UpdateDateColumn,
  DeleteDateColumn,
  OneToMany,
  ManyToMany,
  JoinTable,
  BeforeInsert,
  BeforeUpdate,
  Index,
} from 'typeorm';
import { Exclude } from 'class-transformer';
import { AuditLog } from '../../audit/entities/audit-log.entity';
import { Role } from './role.entity';

@Entity('users')
@Index('IDX_USER_EMAIL', ['email'], { unique: true })
@Index('IDX_USER_USERNAME', ['username'], { unique: true })
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  email: string;

  @Column({ unique: true })
  username: string;

  @Column()
  @Exclude()
  passwordHash: string;

  @Column({ nullable: true })
  firstName: string;

  @Column({ nullable: true })
  lastName: string;

  @Column({ default: true })
  isActive: boolean;

  @Column({ default: false })
  isVerified: boolean;

  @Column({ nullable: true })
  phoneNumber: string;

  @Column({ nullable: true })
  avatarUrl: string;

  @Column({ nullable: true })
  lastLoginAt: Date;

  @Column({ default: 0 })
  loginAttempts: number;

  @Column({ nullable: true })
  lockedUntil: Date;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @DeleteDateColumn()
  deletedAt: Date;

  @OneToMany(() => AuditLog, (auditLog) => auditLog.user)
  auditLogs: AuditLog[];

  @ManyToMany(() => Role, (role) => role.users, { cascade: true })
  @JoinTable({
    name: 'user_roles',
    joinColumn: {
      name: 'userId',
      referencedColumnName: 'id',
    },
    inverseJoinColumn: {
      name: 'roleId',
      referencedColumnName: 'id',
    },
  })
  roles: Role[];

  @BeforeInsert()
  @BeforeUpdate()
  normalizeEmail() {
    if (this.email) {
      this.email = this.email.toLowerCase().trim();
    }
  }

  @BeforeInsert()
  setDefaultValues() {
    if (!this.username && this.email) {
      this.username = this.email.split('@')[0];
    }
  }
}

// src/modules/users/entities/role.entity.ts
@Entity('roles')
export class Role {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  name: string;

  @Column('text', { array: true, default: [] })
  permissions: string[];

  @Column({ default: true })
  isActive: boolean;

  @ManyToMany(() => User, (user) => user.roles)
  users: User[];
}
```

### Repository Pattern
```typescript
// src/modules/users/repositories/user.repository.ts
import { Repository, DataSource, EntityManager } from 'typeorm';
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { User } from '../entities/user.entity';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';

@Injectable()
export class UserRepository extends Repository<User> {
  constructor(
    private dataSource: DataSource,
    @InjectRepository(User)
    private repository: Repository<User>,
  ) {
    super(repository.target, repository.manager, repository.queryRunner);
  }

  async createUser(createUserDto: CreateUserDto): Promise<User> {
    const user = this.create(createUserDto);
    return await this.save(user);
  }

  async findByEmail(email: string): Promise<User | null> {
    return await this.findOne({
      where: { email: email.toLowerCase() },
      relations: ['roles'],
    });
  }

  async findById(id: string): Promise<User | null> {
    return await this.findOne({
      where: { id },
      relations: ['roles'],
    });
  }

  async findActiveUsers(
    page: number = 1,
    limit: number = 10,
  ): Promise<{ users: User[]; total: number }> {
    const [users, total] = await this.findAndCount({
      where: { isActive: true, deletedAt: null },
      relations: ['roles'],
      skip: (page - 1) * limit,
      take: limit,
      order: { createdAt: 'DESC' },
    });

    return { users, total };
  }

  async updateUser(id: string, updateUserDto: UpdateUserDto): Promise<User> {
    await this.update(id, updateUserDto);
    return await this.findById(id);
  }

  async softDeleteUser(id: string): Promise<void> {
    await this.softDelete(id);
  }

  async incrementLoginAttempts(userId: string): Promise<void> {
    await this.increment({ id: userId }, 'loginAttempts', 1);
  }

  async resetLoginAttempts(userId: string): Promise<void> {
    await this.update(userId, {
      loginAttempts: 0,
      lockedUntil: null,
    });
  }
}
```

### Service with Transactions
```typescript
// src/modules/users/services/users.service.ts
import {
  Injectable,
  ConflictException,
  NotFoundException,
  BadRequestException,
} from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { DataSource, EntityManager } from 'typeorm';
import * as bcrypt from 'bcrypt';
import { UserRepository } from '../repositories/user.repository';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';
import { User } from '../entities/user.entity';
import { AuditService } from '../../audit/services/audit.service';

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(UserRepository)
    private readonly userRepository: UserRepository,
    private readonly dataSource: DataSource,
    private readonly auditService: AuditService,
  ) {}

  async create(createUserDto: CreateUserDto): Promise<User> {
    // Check if user exists
    const existingUser = await this.userRepository.findByEmail(
      createUserDto.email,
    );

    if (existingUser) {
      throw new ConflictException('Email already exists');
    }

    // Start transaction
    const queryRunner = this.dataSource.createQueryRunner();
    await queryRunner.connect();
    await queryRunner.startTransaction();

    try {
      // Hash password
      const salt = await bcrypt.genSalt(10);
      const hashedPassword = await bcrypt.hash(createUserDto.password, salt);

      // Create user
      const user = this.userRepository.create({
        ...createUserDto,
        passwordHash: hashedPassword,
      });

      const savedUser = await queryRunner.manager.save(user);

      // Add default role
      const defaultRole = await queryRunner.manager.findOne('Role', {
        where: { name: 'user' },
      });

      if (defaultRole) {
        await queryRunner.manager
          .createQueryBuilder()
          .insert()
          .into('user_roles')
          .values({
            userId: savedUser.id,
            roleId: defaultRole.id,
          })
          .execute();
      }

      // Log audit
      await this.auditService.log({
        action: 'USER_CREATED',
        userId: savedUser.id,
        entityType: 'USER',
        entityId: savedUser.id,
        metadata: {
          email: savedUser.email,
          username: savedUser.username,
        },
      });

      await queryRunner.commitTransaction();
      return savedUser;
    } catch (error) {
      await queryRunner.rollbackTransaction();
      throw error;
    } finally {
      await queryRunner.release();
    }
  }

  async findAll(
    page: number = 1,
    limit: number = 10,
    search?: string,
  ): Promise<{ users: User[]; total: number; page: number; totalPages: number }> {
    const queryBuilder = this.userRepository
      .createQueryBuilder('user')
      .leftJoinAndSelect('user.roles', 'roles')
      .where('user.deletedAt IS NULL');

    if (search) {
      queryBuilder.andWhere(
        '(user.email ILIKE :search OR user.username ILIKE :search OR user.firstName ILIKE :search OR user.lastName ILIKE :search)',
        { search: `%${search}%` },
      );
    }

    const [users, total] = await queryBuilder
      .skip((page - 1) * limit)
      .take(limit)
      .orderBy('user.createdAt', 'DESC')
      .getManyAndCount();

    return {
      users,
      total,
      page,
      totalPages: Math.ceil(total / limit),
    };
  }

  async findOne(id: string): Promise<User> {
    const user = await this.userRepository.findById(id);
    
    if (!user) {
      throw new NotFoundException(`User with ID ${id} not found`);
    }

    return user;
  }

  async update(id: string, updateUserDto: UpdateUserDto): Promise<User> {
    const user = await this.findOne(id);
    
    // If updating email, check if it's unique
    if (updateUserDto.email && updateUserDto.email !== user.email) {
      const existing = await this.userRepository.findByEmail(updateUserDto.email);
      if (existing) {
        throw new ConflictException('Email already exists');
      }
    }

    // If updating password, hash it
    if (updateUserDto.password) {
      const salt = await bcrypt.genSalt(10);
      updateUserDto.password = await bcrypt.hash(updateUserDto.password, salt);
    }

    const updatedUser = await this.userRepository.updateUser(id, updateUserDto);
    
    await this.auditService.log({
      action: 'USER_UPDATED',
      userId: id,
      entityType: 'USER',
      entityId: id,
      metadata: updateUserDto,
    });

    return updatedUser;
  }

  async remove(id: string): Promise<void> {
    const user = await this.findOne(id);
    await this.userRepository.softDeleteUser(id);
    
    await this.auditService.log({
      action: 'USER_DELETED',
      userId: id,
      entityType: 'USER',
      entityId: id,
      metadata: {
        email: user.email,
        username: user.username,
      },
    });
  }

  async validateUser(email: string, password: string): Promise<User | null> {
    const user = await this.userRepository.findByEmail(email);
    
    if (!user || !user.isActive) {
      return null;
    }

    // Check if account is locked
    if (user.lockedUntil && user.lockedUntil > new Date()) {
      throw new BadRequestException('Account is temporarily locked');
    }

    const isPasswordValid = await bcrypt.compare(password, user.passwordHash);
    
    if (!isPasswordValid) {
      // Increment login attempts
      await this.userRepository.incrementLoginAttempts(user.id);
      
      if (user.loginAttempts >= 4) { // 5th attempt
        const lockDuration = 15 * 60 * 1000; // 15 minutes
        await this.userRepository.update(user.id, {
          lockedUntil: new Date(Date.now() + lockDuration),
        });
        throw new BadRequestException(
          'Too many failed attempts. Account locked for 15 minutes',
        );
      }
      
      return null;
    }

    // Reset login attempts on successful login
    await this.userRepository.resetLoginAttempts(user.id);
    
    // Update last login
    await this.userRepository.update(user.id, {
      lastLoginAt: new Date(),
    });

    return user;
  }
}
```

### Query Builder Example
```typescript
// src/modules/products/services/products.service.ts
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, Between, Like, In } from 'typeorm';
import { Product } from '../entities/product.entity';

@Injectable()
export class ProductsService {
  constructor(
    @InjectRepository(Product)
    private readonly productRepository: Repository<Product>,
  ) {}

  async searchProducts(filters: {
    categoryId?: string;
    minPrice?: number;
    maxPrice?: number;
    inStock?: boolean;
    search?: string;
    page?: number;
    limit?: number;
    sortBy?: string;
    sortOrder?: 'ASC' | 'DESC';
  }): Promise<{ products: Product[]; total: number }> {
    const {
      categoryId,
      minPrice,
      maxPrice,
      inStock,
      search,
      page = 1,
      limit = 20,
      sortBy = 'createdAt',
      sortOrder = 'DESC',
    } = filters;

    const queryBuilder = this.productRepository
      .createQueryBuilder('product')
      .leftJoinAndSelect('product.inventory', 'inventory')
      .leftJoinAndSelect('product.categories', 'categories')
      .leftJoinAndSelect('product.variants', 'variants')
      .where('product.isActive = :isActive', { isActive: true });

    // Apply filters
    if (categoryId) {
      queryBuilder.andWhere('categories.id = :categoryId', { categoryId });
    }

    if (minPrice !== undefined) {
      queryBuilder.andWhere('product.price >= :minPrice', { minPrice });
    }

    if (maxPrice !== undefined) {
      queryBuilder.andWhere('product.price <= :maxPrice', { maxPrice });
    }

    if (inStock !== undefined) {
      queryBuilder.andWhere('inventory.quantity > 0');
    }

    if (search) {
      queryBuilder.andWhere(
        '(product.name ILIKE :search OR product.description ILIKE :search OR product.sku ILIKE :search)',
        { search: `%${search}%` },
      );
    }

    // Count total
    const total = await queryBuilder.getCount();

    // Apply pagination and sorting
    const products = await queryBuilder
      .skip((page - 1) * limit)
      .take(limit)
      .orderBy(`product.${sortBy}`, sortOrder)
      .getMany();

    return { products, total };
  }

  async getProductStats(): Promise<any> {
    const stats = await this.productRepository
      .createQueryBuilder('product')
      .select([
        'COUNT(product.id) as totalProducts',
        'SUM(CASE WHEN inventory.quantity > 0 THEN 1 ELSE 0 END) as inStockProducts',
        'SUM(CASE WHEN inventory.quantity = 0 THEN 1 ELSE 0 END) as outOfStockProducts',
        'AVG(product.price) as averagePrice',
        'MIN(product.price) as minPrice',
        'MAX(product.price) as maxPrice',
      ])
      .leftJoin('product.inventory', 'inventory')
      .getRawOne();

    const categoryStats = await this.productRepository
      .createQueryBuilder('product')
      .select([
        'categories.name as categoryName',
        'COUNT(product.id) as productCount',
        'AVG(product.price) as averagePrice',
      ])
      .leftJoin('product.categories', 'categories')
      .groupBy('categories.name')
      .orderBy('productCount', 'DESC')
      .getRawMany();

    return {
      ...stats,
      categoryStats,
    };
  }

  async bulkUpdatePrices(updates: Array<{ id: string; price: number }>) {
    const caseStatements = updates
      .map((update, index) => `WHEN '${update.id}' THEN ${update.price}`)
      .join(' ');

    await this.productRepository
      .createQueryBuilder()
      .update(Product)
      .set({
        price: () => `CASE id ${caseStatements} ELSE price END`,
        updatedAt: new Date(),
      })
      .whereInIds(updates.map((u) => u.id))
      .execute();
  }
}
```

## 3. JWT Authentication - Access/Refresh Tokens, Token Rotation, Redis Storage, Axios Interceptors

### Auth Service with Redis
```typescript
// src/modules/auth/services/auth.service.ts
import {
  Injectable,
  UnauthorizedException,
  BadRequestException,
} from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { ConfigService } from '@nestjs/config';
import { RedisService } from '../../redis/services/redis.service';
import { UsersService } from '../../users/services/users.service';
import { LoginDto } from '../dto/login.dto';
import { RegisterDto } from '../dto/register.dto';

export interface TokenPayload {
  sub: string;
  email: string;
  username: string;
  roles: string[];
}

export interface TokenPair {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
}

@Injectable()
export class AuthService {
  constructor(
    private readonly jwtService: JwtService,
    private readonly configService: ConfigService,
    private readonly redisService: RedisService,
    private readonly usersService: UsersService,
  ) {}

  async login(loginDto: LoginDto): Promise<TokenPair> {
    const user = await this.usersService.validateUser(
      loginDto.email,
      loginDto.password,
    );

    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }

    return await this.generateTokens(user);
  }

  async register(registerDto: RegisterDto): Promise<TokenPair> {
    const user = await this.usersService.create(registerDto);
    return await this.generateTokens(user);
  }

  async refreshTokens(refreshToken: string): Promise<TokenPair> {
    try {
      // Verify refresh token
      const payload = await this.jwtService.verifyAsync(refreshToken, {
        secret: this.configService.get('JWT_REFRESH_SECRET'),
      });

      // Check if token exists in Redis (not revoked)
      const storedToken = await this.redisService.get(
        `refresh_token:${payload.sub}`,
      );

      if (!storedToken || storedToken !== refreshToken) {
        throw new UnauthorizedException('Invalid refresh token');
      }

      // Get user
      const user = await this.usersService.findOne(payload.sub);
      
      // Remove old refresh token
      await this.redisService.del(`refresh_token:${user.id}`);
      
      // Generate new tokens
      return await this.generateTokens(user);
    } catch (error) {
      if (error.name === 'TokenExpiredError') {
        throw new UnauthorizedException('Refresh token expired');
      }
      throw new UnauthorizedException('Invalid refresh token');
    }
  }

  async logout(userId: string): Promise<void> {
    // Remove refresh token from Redis
    await this.redisService.del(`refresh_token:${userId}`);
    
    // Add to blacklist (optional)
    await this.redisService.setex(
      `blacklist:${userId}:${Date.now()}`,
      3600, // 1 hour
      'logged_out',
    );
  }

  async logoutAll(userId: string): Promise<void> {
    // Remove all user sessions
    const keys = await this.redisService.keys(`session:${userId}:*`);
    
    if (keys.length > 0) {
      await this.redisService.del(...keys);
    }
    
    // Remove refresh token
    await this.redisService.del(`refresh_token:${userId}`);
  }

  async validateToken(token: string): Promise<TokenPayload> {
    try {
      return await this.jwtService.verifyAsync(token, {
        secret: this.configService.get('JWT_SECRET'),
      });
    } catch (error) {
      throw new UnauthorizedException('Invalid token');
    }
  }

  async isTokenRevoked(userId: string, token: string): Promise<boolean> {
    const blacklisted = await this.redisService.get(`blacklist:${userId}`);
    return blacklisted === token;
  }

  private async generateTokens(user: any): Promise<TokenPair> {
    const payload: TokenPayload = {
      sub: user.id,
      email: user.email,
      username: user.username,
      roles: user.roles?.map((role) => role.name) || [],
    };

    const [accessToken, refreshToken] = await Promise.all([
      this.jwtService.signAsync(payload, {
        secret: this.configService.get('JWT_SECRET'),
        expiresIn: this.configService.get('JWT_ACCESS_EXPIRY', '15m'),
      }),
      this.jwtService.signAsync(
        { sub: user.id },
        {
          secret: this.configService.get('JWT_REFRESH_SECRET'),
          expiresIn: this.configService.get('JWT_REFRESH_EXPIRY', '7d'),
        },
      ),
    ]);

    // Store refresh token in Redis
    await this.redisService.setex(
      `refresh_token:${user.id}`,
      7 * 24 * 60 * 60, // 7 days in seconds
      refreshToken,
    );

    // Store session
    await this.redisService.setex(
      `session:${user.id}:${Date.now()}`,
      7 * 24 * 60 * 60,
      JSON.stringify({
        userAgent: 'web', // Could be from request
        ip: '127.0.0.1',
        createdAt: new Date(),
      }),
    );

    return {
      accessToken,
      refreshToken,
      expiresIn: 15 * 60, // 15 minutes in seconds
    };
  }
}
```

### Axios Interceptors in Frontend
```typescript
// frontend/lib/api/axios.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';
import { TokenManager } from './token-manager';

class ApiClient {
  private client: AxiosInstance;
  private refreshPromise: Promise<string> | null = null;

  constructor(baseURL: string) {
    this.client = axios.create({
      baseURL,
      timeout: 30000,
      headers: {
        'Content-Type': 'application/json',
      },
    });

    this.setupInterceptors();
  }

  private setupInterceptors(): void {
    // Request interceptor
    this.client.interceptors.request.use(
      async (config) => {
        const token = await TokenManager.getAccessToken();
        
        if (token) {
          config.headers.Authorization = `Bearer ${token}`;
        }
        
        // Add request ID for tracing
        config.headers['X-Request-ID'] = this.generateRequestId();
        
        return config;
      },
      (error) => Promise.reject(error),
    );

    // Response interceptor
    this.client.interceptors.response.use(
      (response: AxiosResponse) => response,
      async (error) => {
        const originalRequest = error.config;
        
        // Handle token expiration
        if (
          error.response?.status === 401 &&
          error.response?.data?.message === 'Token expired' &&
          !originalRequest._retry
        ) {
          originalRequest._retry = true;
          
          try {
            const newToken = await this.refreshToken();
            
            if (newToken) {
              originalRequest.headers.Authorization = `Bearer ${newToken}`;
              return this.client(originalRequest);
            }
          } catch (refreshError) {
            // Redirect to login on refresh failure
            TokenManager.clearTokens();
            window.location.href = '/login';
            return Promise.reject(refreshError);
          }
        }
        
        // Handle rate limiting
        if (error.response?.status === 429) {
          const retryAfter = error.response.headers['retry-after'];
          await this.delay(retryAfter * 1000);
          return this.client(originalRequest);
        }
        
        // Handle network errors
        if (!error.response) {
          console.error('Network error:', error);
          // You might want to show a network error message to the user
        }
        
        return Promise.reject(error);
      },
    );
  }

  private async refreshToken(): Promise<string | null> {
    // Prevent multiple refresh calls
    if (this.refreshPromise) {
      return this.refreshPromise;
    }
    
    this.refreshPromise = TokenManager.refreshToken()
      .finally(() => {
        this.refreshPromise = null;
      });
    
    return this.refreshPromise;
  }

  private generateRequestId(): string {
    return `${Date.now()}-${Math.random().toString(36).substr(2, 9)}`;
  }

  private delay(ms: number): Promise<void> {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }

  // CRUD methods
  public get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    return this.client.get<T>(url, config).then((res) => res.data);
  }

  public post<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    return this.client.post<T>(url, data, config).then((res) => res.data);
  }

  public put<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    return this.client.put<T>(url, data, config).then((res) => res.data);
  }

  public delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    return this.client.delete<T>(url, config).then((res) => res.data);
  }

  public patch<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    return this.client.patch<T>(url, data, config).then((res) => res.data);
  }
}

// frontend/lib/api/token-manager.ts
export class TokenManager {
  private static readonly ACCESS_TOKEN_KEY = 'access_token';
  private static readonly REFRESH_TOKEN_KEY = 'refresh_token';
  private static readonly TOKEN_EXPIRY_KEY = 'token_expiry';

  static setTokens(tokens: { accessToken: string; refreshToken: string; expiresIn: number }): void {
    if (typeof window === 'undefined') return;
    
    localStorage.setItem(this.ACCESS_TOKEN_KEY, tokens.accessToken);
    localStorage.setItem(this.REFRESH_TOKEN_KEY, tokens.refreshToken);
    
    const expiryTime = Date.now() + (tokens.expiresIn * 1000);
    localStorage.setItem(this.TOKEN_EXPIRY_KEY, expiryTime.toString());
  }

  static getAccessToken(): string | null {
    if (typeof window === 'undefined') return null;
    return localStorage.getItem(this.ACCESS_TOKEN_KEY);
  }

  static getRefreshToken(): string | null {
    if (typeof window === 'undefined') return null;
    return localStorage.getItem(this.REFRESH_TOKEN_KEY);
  }

  static clearTokens(): void {
    if (typeof window === 'undefined') return;
    
    localStorage.removeItem(this.ACCESS_TOKEN_KEY);
    localStorage.removeItem(this.REFRESH_TOKEN_KEY);
    localStorage.removeItem(this.TOKEN_EXPIRY_KEY);
  }

  static isTokenExpired(): boolean {
    const expiry = localStorage.getItem(this.TOKEN_EXPIRY_KEY);
    if (!expiry) return true;
    
    return Date.now() > parseInt(expiry);
  }

  static async refreshToken(): Promise<string | null> {
    const refreshToken = this.getRefreshToken();
    
    if (!refreshToken) {
      this.clearTokens();
      return null;
    }

    try {
      const response = await fetch('/api/auth/refresh', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ refreshToken }),
      });

      if (!response.ok) {
        throw new Error('Failed to refresh token');
      }

      const data = await response.json();
      this.setTokens(data);
      
      return data.accessToken;
    } catch (error) {
      this.clearTokens();
      throw error;
    }
  }
}

// Export singleton instance
export const apiClient = new ApiClient(process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3001');
```

## 4. Microservices Architecture - Service Breakdown, Kafka Events, gRPC, API Gateway

### Service Breakdown Example
```typescript
// src/modules/orders/orders.module.ts - Order Service
@Module({
  imports: [
    TypeOrmModule.forFeature([Order, OrderItem]),
    ClientsModule.register([
      {
        name: 'PRODUCT_SERVICE',
        transport: Transport.KAFKA,
        options: {
          client: {
            clientId: 'order-service',
            brokers: ['localhost:9092'],
          },
          consumer: {
            groupId: 'order-consumer',
          },
        },
      },
      {
        name: 'PAYMENT_SERVICE',
        transport: Transport.GRPC,
        options: {
          package: 'payment',
          protoPath: join(__dirname, '../payment/payment.proto'),
          url: 'localhost:50051',
        },
      },
    ]),
    EventEmitterModule.forRoot(),
  ],
  controllers: [OrdersController],
  providers: [
    OrdersService,
    OrderRepository,
    {
      provide: 'ORDER_CONFIG',
      useFactory: (configService: ConfigService) => ({
        taxRate: configService.get('TAX_RATE', 0.07),
        shippingCost: configService.get('SHIPPING_COST', 50),
      }),
      inject: [ConfigService],
    },
  ],
})
export class OrdersModule {}
```

### Kafka Event Producer
```typescript
// src/modules/kafka/producers/order.producer.ts
import { Injectable, Inject, Logger } from '@nestjs/common';
import { ClientKafka } from '@nestjs/microservices';
import { EventEmitter2 } from '@nestjs/event-emitter';

export interface OrderCreatedEvent {
  orderId: string;
  customerId: string;
  totalAmount: number;
  items: Array<{
    productId: string;
    quantity: number;
    price: number;
  }>;
  timestamp: string;
}

export interface PaymentProcessedEvent {
  orderId: string;
  paymentId: string;
  status: 'SUCCESS' | 'FAILED';
  amount: number;
  timestamp: string;
}

@Injectable()
export class OrderProducer {
  private readonly logger = new Logger(OrderProducer.name);

  constructor(
    @Inject('KAFKA_PRODUCER') private readonly kafkaClient: ClientKafka,
    private readonly eventEmitter: EventEmitter2,
  ) {}

  async publishOrderCreated(event: OrderCreatedEvent): Promise<void> {
    try {
      await this.kafkaClient.emit('order.created', {
        key: event.orderId,
        value: JSON.stringify(event),
        headers: {
          eventType: 'order.created',
          timestamp: new Date().toISOString(),
          source: 'order-service',
        },
      });

      this.logger.log(`Order created event published: ${event.orderId}`);
      
      // Also emit local event for other services in the same process
      this.eventEmitter.emit('order.created', event);
    } catch (error) {
      this.logger.error(`Failed to publish order created event: ${error.message}`);
      throw error;
    }
  }

  async publishPaymentProcessed(event: PaymentProcessedEvent): Promise<void> {
    try {
      await this.kafkaClient.emit('payment.processed', {
        key: event.orderId,
        value: JSON.stringify(event),
      });

      this.logger.log(`Payment processed event published: ${event.orderId}`);
    } catch (error) {
      this.logger.error(`Failed to publish payment processed event: ${error.message}`);
      
      // Retry logic
      await this.retryPublish('payment.processed', event);
    }
  }

  private async retryPublish(topic: string, event: any, retryCount = 0): Promise<void> {
    const maxRetries = 3;
    
    if (retryCount >= maxRetries) {
      this.logger.error(`Max retries reached for topic ${topic}`);
      // Send to dead letter queue
      await this.sendToDLQ(topic, event);
      return;
    }

    try {
      await new Promise(resolve => setTimeout(resolve, 1000 * (retryCount + 1)));
      await this.kafkaClient.emit(topic, {
        key: event.orderId || event.id,
        value: JSON.stringify(event),
      });
    } catch (error) {
      await this.retryPublish(topic, event, retryCount + 1);
    }
  }

  private async sendToDLQ(topic: string, event: any): Promise<void> {
    await this.kafkaClient.emit(`${topic}.dlq`, {
      key: event.orderId || event.id,
      value: JSON.stringify({
        ...event,
        dlqReason: 'Max retries exceeded',
        timestamp: new Date().toISOString(),
      }),
    });
  }
}
```

### Kafka Event Consumer
```typescript
// src/modules/inventory/consumers/inventory.consumer.ts
import {
  Injectable,
  Logger,
  OnModuleInit,
  OnModuleDestroy,
} from '@nestjs/common';
import { Kafka, Consumer, EachMessagePayload } from 'kafkajs';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class InventoryConsumer implements OnModuleInit, OnModuleDestroy {
  private readonly logger = new Logger(InventoryConsumer.name);
  private consumer: Consumer;

  constructor(
    private readonly configService: ConfigService,
    private readonly inventoryService: InventoryService,
  ) {
    const kafka = new Kafka({
      clientId: 'inventory-service',
      brokers: [this.configService.get('KAFKA_BROKER')],
    });

    this.consumer = kafka.consumer({
      groupId: 'inventory-consumer-group',
      sessionTimeout: 30000,
      heartbeatInterval: 3000,
    });
  }

  async onModuleInit(): Promise<void> {
    await this.connect();
    await this.subscribeToTopics();
    await this.startConsuming();
  }

  async onModuleDestroy(): Promise<void> {
    await this.disconnect();
  }

  private async connect(): Promise<void> {
    try {
      await this.consumer.connect();
      this.logger.log('Kafka consumer connected');
    } catch (error) {
      this.logger.error(`Failed to connect Kafka consumer: ${error.message}`);
      throw error;
    }
  }

  private async disconnect(): Promise<void> {
    try {
      await this.consumer.disconnect();
      this.logger.log('Kafka consumer disconnected');
    } catch (error) {
      this.logger.error(`Failed to disconnect Kafka consumer: ${error.message}`);
    }
  }

  private async subscribeToTopics(): Promise<void> {
    const topics = [
      'order.created',
      'order.cancelled',
      'inventory.adjusted',
    ];

    await Promise.all(
      topics.map((topic) =>
        this.consumer.subscribe({
          topic,
          fromBeginning: false,
        }),
      ),
    );
  }

  private async startConsuming(): Promise<void> {
    await this.consumer.run({
      eachMessage: async (payload: EachMessagePayload) => {
        const { topic, partition, message } = payload;
        
        try {
          const event = JSON.parse(message.value.toString());
          
          this.logger.debug(
            `Received message: Topic=${topic}, Partition=${partition}, Offset=${message.offset}`,
          );

          await this.processEvent(topic, event);

          // Commit offset
          await this.consumer.commitOffsets([
            {
              topic,
              partition,
              offset: (parseInt(message.offset) + 1).toString(),
            },
          ]);
        } catch (error) {
          this.logger.error(
            `Failed to process message: Topic=${topic}, Partition=${partition}, Error=${error.message}`,
          );
          
          // Send to error topic for manual processing
          await this.sendToErrorTopic(topic, message, error);
        }
      },
    });
  }

  private async processEvent(topic: string, event: any): Promise<void> {
    switch (topic) {
      case 'order.created':
        await this.handleOrderCreated(event);
        break;
      case 'order.cancelled':
        await this.handleOrderCancelled(event);
        break;
      case 'inventory.adjusted':
        await this.handleInventoryAdjusted(event);
        break;
      default:
        this.logger.warn(`Unknown topic: ${topic}`);
    }
  }

  private async handleOrderCreated(event: any): Promise<void> {
    const { orderId, items } = event;
    
    this.logger.log(`Processing order created: ${orderId}`);
    
    try {
      // Reduce inventory for each item
      for (const item of items) {
        await this.inventoryService.reduceStock(
          item.productId,
          item.quantity,
          orderId,
          'ORDER_CREATED',
        );
      }
      
      this.logger.log(`Inventory updated for order: ${orderId}`);
    } catch (error) {
      this.logger.error(`Failed to update inventory for order ${orderId}: ${error.message}`);
      throw error;
    }
  }

  private async handleOrderCancelled(event: any): Promise<void> {
    const { orderId, items } = event;
    
    this.logger.log(`Processing order cancelled: ${orderId}`);
    
    try {
      // Restore inventory for each item
      for (const item of items) {
        await this.inventoryService.increaseStock(
          item.productId,
          item.quantity,
          orderId,
          'ORDER_CANCELLED',
        );
      }
      
      this.logger.log(`Inventory restored for cancelled order: ${orderId}`);
    } catch (error) {
      this.logger.error(`Failed to restore inventory for order ${orderId}: ${error.message}`);
      throw error;
    }
  }

  private async sendToErrorTopic(
    topic: string,
    message: any,
    error: Error,
  ): Promise<void> {
    // Implementation for sending to error topic
  }
}
```

### gRPC Service Implementation
```protobuf
// proto/payment.proto
syntax = "proto3";

package payment;

service PaymentService {
  rpc ProcessPayment (PaymentRequest) returns (PaymentResponse);
  rpc GetPayment (GetPaymentRequest) returns (PaymentResponse);
  rpc RefundPayment (RefundRequest) returns (RefundResponse);
}

message PaymentRequest {
  string order_id = 1;
  string customer_id = 2;
  double amount = 3;
  string currency = 4;
  PaymentMethod payment_method = 5;
  PaymentDetails details = 6;
}

message PaymentResponse {
  string payment_id = 1;
  string order_id = 2;
  PaymentStatus status = 3;
  double amount = 4;
  string currency = 5;
  string transaction_id = 6;
  string message = 7;
  string timestamp = 8;
}

message GetPaymentRequest {
  string payment_id = 1;
}

message RefundRequest {
  string payment_id = 1;
  double amount = 2;
  string reason = 3;
}

message RefundResponse {
  string refund_id = 1;
  PaymentStatus status = 2;
  double amount = 3;
  string message = 4;
  string timestamp = 5;
}

enum PaymentMethod {
  CREDIT_CARD = 0;
  BANK_TRANSFER = 1;
  PAYPAL = 2;
  CRYPTO = 3;
}

enum PaymentStatus {
  PENDING = 0;
  SUCCESS = 1;
  FAILED = 2;
  REFUNDED = 3;
}

message PaymentDetails {
  string card_number = 1;
  string expiry_date = 2;
  string cvv = 3;
  string bank_account = 4;
  string paypal_email = 5;
  string crypto_wallet = 6;
}
```

```typescript
// src/modules/payment/grpc/payment.grpc.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { GrpcMethod } from '@nestjs/microservices';
import { PaymentService } from '../services/payment.service';

@Injectable()
export class PaymentGrpcService {
  private readonly logger = new Logger(PaymentGrpcService.name);

  constructor(private readonly paymentService: PaymentService) {}

  @GrpcMethod('PaymentService', 'ProcessPayment')
  async processPayment(data: any): Promise<any> {
    this.logger.log(`Processing payment for order: ${data.order_id}`);
    
    try {
      const payment = await this.paymentService.processPayment({
        orderId: data.order_id,
        customerId: data.customer_id,
        amount: data.amount,
        currency: data.currency,
        paymentMethod: data.payment_method,
        details: data.details,
      });

      return {
        payment_id: payment.id,
        order_id: payment.orderId,
        status: payment.status,
        amount: payment.amount,
        currency: payment.currency,
        transaction_id: payment.transactionId,
        message: 'Payment processed successfully',
        timestamp: payment.createdAt.toISOString(),
      };
    } catch (error) {
      this.logger.error(`Payment processing failed: ${error.message}`);
      
      return {
        payment_id: '',
        order_id: data.order_id,
        status: 'FAILED',
        amount: data.amount,
        currency: data.currency,
        transaction_id: '',
        message: error.message,
        timestamp: new Date().toISOString(),
      };
    }
  }

  @GrpcMethod('PaymentService', 'GetPayment')
  async getPayment(data: any): Promise<any> {
    const payment = await this.paymentService.findById(data.payment_id);
    
    if (!payment) {
      return {
        payment_id: '',
        order_id: '',
        status: 'NOT_FOUND',
        amount: 0,
        currency: '',
        transaction_id: '',
        message: 'Payment not found',
        timestamp: '',
      };
    }

    return {
      payment_id: payment.id,
      order_id: payment.orderId,
      status: payment.status,
      amount: payment.amount,
      currency: payment.currency,
      transaction_id: payment.transactionId,
      message: 'Payment found',
      timestamp: payment.createdAt.toISOString(),
    };
  }
}
```

### API Gateway Configuration
```typescript
// src/gateway/gateway.module.ts
import { Module, CacheModule } from '@nestjs/common';
import { ClientsModule, Transport } from '@nestjs/microservices';
import { ThrottlerModule } from '@nestjs/throttler';
import { GatewayController } from './gateway.controller';
import { GatewayService } from './gateway.service';
import { AuthGuard } from './guards/auth.guard';
import { RateLimitGuard } from './guards/rate-limit.guard';

@Module({
  imports: [
    CacheModule.register(),
    ThrottlerModule.forRoot({
      ttl: 60,
      limit: 100,
    }),
    ClientsModule.register([
      {
        name: 'USER_SERVICE',
        transport: Transport.TCP,
        options: {
          host: 'user-service',
          port: 3001,
        },
      },
      {
        name: 'PRODUCT_SERVICE',
        transport: Transport.TCP,
        options: {
          host: 'product-service',
          port: 3002,
        },
      },
      {
        name: 'ORDER_SERVICE',
        transport: Transport.TCP,
        options: {
          host: 'order-service',
          port: 3003,
        },
      },
      {
        name: 'PAYMENT_SERVICE',
        transport: Transport.GRPC,
        options: {
          package: 'payment',
          protoPath: join(__dirname, '../payment/payment.proto'),
          url: 'payment-service:50051',
        },
      },
    ]),
  ],
  controllers: [GatewayController],
  providers: [
    GatewayService,
    AuthGuard,
    RateLimitGuard,
    {
      provide: APP_GUARD,
      useClass: AuthGuard,
    },
    {
      provide: APP_GUARD,
      useClass: RateLimitGuard,
    },
  ],
})
export class GatewayModule {}
```

```typescript
// src/gateway/gateway.controller.ts
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  Query,
  UseInterceptors,
  CacheInterceptor,
  Inject,
} from '@nestjs/common';
import { ClientProxy, MessagePattern } from '@nestjs/microservices';
import { Observable, firstValueFrom } from 'rxjs';

@Controller()
@UseInterceptors(CacheInterceptor)
export class GatewayController {
  constructor(
    @Inject('USER_SERVICE') private userClient: ClientProxy,
    @Inject('PRODUCT_SERVICE') private productClient: ClientProxy,
    @Inject('ORDER_SERVICE') private orderClient: ClientProxy,
    @Inject('PAYMENT_SERVICE') private paymentClient: ClientProxy,
  ) {}

  @Get('users/:id')
  async getUser(@Param('id') id: string): Promise<any> {
    return firstValueFrom(
      this.userClient.send({ cmd: 'get_user' }, { id }),
    );
  }

  @Post('users')
  async createUser(@Body() createUserDto: any): Promise<any> {
    return firstValueFrom(
      this.userClient.send({ cmd: 'create_user' }, createUserDto),
    );
  }

  @Get('products')
  async getProducts(@Query() query: any): Promise<any> {
    return firstValueFrom(
      this.productClient.send({ cmd: 'get_products' }, query),
    );
  }

  @Post('orders')
  async createOrder(@Body() createOrderDto: any): Promise<any> {
    // Validate request
    // ...
    
    // Call order service
    const order = await firstValueFrom(
      this.orderClient.send({ cmd: 'create_order' }, createOrderDto),
    );
    
    // Process payment
    const payment = await firstValueFrom(
      this.paymentClient.send(
        { cmd: 'process_payment' },
        {
          order_id: order.id,
          customer_id: order.customerId,
          amount: order.totalAmount,
          payment_method: createOrderDto.paymentMethod,
        },
      ),
    );
    
    // Update order with payment status
    await firstValueFrom(
      this.orderClient.send(
        { cmd: 'update_order_payment' },
        {
          orderId: order.id,
          paymentStatus: payment.status,
          paymentId: payment.payment_id,
        },
      ),
    );
    
    return {
      ...order,
      payment,
    };
  }

  @Get('health')
  async healthCheck(): Promise<any> {
    const services = [
      { name: 'user-service', client: this.userClient },
      { name: 'product-service', client: this.productClient },
      { name: 'order-service', client: this.orderClient },
      { name: 'payment-service', client: this.paymentClient },
    ];

    const healthResults = await Promise.allSettled(
      services.map(async ({ name, client }) => {
        try {
          const response = await firstValueFrom(
            client.send({ cmd: 'health' }, {}),
          );
          return { name, status: 'healthy', data: response };
        } catch (error) {
          return { name, status: 'unhealthy', error: error.message };
        }
      }),
    );

    const allHealthy = healthResults.every(
      (result) =>
        result.status === 'fulfilled' && result.value.status === 'healthy',
    );

    return {
      status: allHealthy ? 'healthy' : 'degraded',
      timestamp: new Date().toISOString(),
      services: healthResults.map((result) =>
        result.status === 'fulfilled' ? result.value : result.reason,
      ),
    };
  }

  // Webhook endpoint for external services
  @Post('webhooks/stripe')
  async stripeWebhook(@Body() body: any): Promise<any> {
    // Validate webhook signature
    // ...
    
    // Route to appropriate service based on event type
    const eventType = body.type;
    
    switch (eventType) {
      case 'payment_intent.succeeded':
        await firstValueFrom(
          this.paymentClient.send(
            { cmd: 'handle_stripe_payment_success' },
            body.data.object,
          ),
        );
        break;
      case 'payment_intent.failed':
        await firstValueFrom(
          this.paymentClient.send(
            { cmd: 'handle_stripe_payment_failure' },
            body.data.object,
          ),
        );
        break;
    }
    
    return { received: true };
  }
}
```

## 5. Complete Systems - User, Product, Inventory, Shipping, Reports, Audit Systems

### User System Controller
```typescript
// src/modules/users/controllers/users.controller.ts
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
  UseInterceptors,
  ClassSerializerInterceptor,
  HttpStatus,
  HttpCode,
  ParseUUIDPipe,
} from '@nestjs/common';
import {
  ApiTags,
  ApiOperation,
  ApiResponse,
  ApiBearerAuth,
  ApiQuery,
  ApiParam,
} from '@nestjs/swagger';
import { UsersService } from '../services/users.service';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';
import { User } from '../entities/user.entity';
import { JwtAuthGuard } from '../../auth/guards/jwt-auth.guard';
import { RolesGuard } from '../../auth/guards/roles.guard';
import { Roles } from '../../auth/decorators/roles.decorator';
import { PaginationDto } from '../../../common/dto/pagination.dto';

@ApiTags('Users')
@Controller('users')
@UseInterceptors(ClassSerializerInterceptor)
@ApiBearerAuth()
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Post()
  @ApiOperation({ summary: 'Create a new user' })
  @ApiResponse({ status: 201, description: 'User created successfully' })
  @ApiResponse({ status: 400, description: 'Invalid input data' })
  @ApiResponse({ status: 409, description: 'User already exists' })
  async create(@Body() createUserDto: CreateUserDto): Promise<User> {
    return await this.usersService.create(createUserDto);
  }

  @Get()
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles('admin')
  @ApiOperation({ summary: 'Get all users with pagination' })
  @ApiResponse({ status: 200, description: 'List of users' })
  @ApiQuery({ name: 'page', required: false, type: Number })
  @ApiQuery({ name: 'limit', required: false, type: Number })
  @ApiQuery({ name: 'search', required: false, type: String })
  async findAll(@Query() paginationDto: PaginationDto): Promise<{
    users: User[];
    total: number;
    page: number;
    totalPages: number;
  }> {
    return await this.usersService.findAll(
      paginationDto.page,
      paginationDto.limit,
      paginationDto.search,
    );
  }

  @Get(':id')
  @UseGuards(JwtAuthGuard)
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiParam({ name: 'id', type: String })
  @ApiResponse({ status: 200, description: 'User found' })
  @ApiResponse({ status: 404, description: 'User not found' })
  async findOne(@Param('id', ParseUUIDPipe) id: string): Promise<User> {
    return await this.usersService.findOne(id);
  }

  @Put(':id')
  @UseGuards(JwtAuthGuard)
  @ApiOperation({ summary: 'Update user' })
  @ApiParam({ name: 'id', type: String })
  @ApiResponse({ status: 200, description: 'User updated' })
  @ApiResponse({ status: 404, description: 'User not found' })
  async update(
    @Param('id', ParseUUIDPipe) id: string,
    @Body() updateUserDto: UpdateUserDto,
  ): Promise<User> {
    return await this.usersService.update(id, updateUserDto);
  }

  @Delete(':id')
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles('admin')
  @HttpCode(HttpStatus.NO_CONTENT)
  @ApiOperation({ summary: 'Delete user' })
  @ApiParam({ name: 'id', type: String })
  @ApiResponse({ status: 204, description: 'User deleted' })
  @ApiResponse({ status: 404, description: 'User not found' })
  async remove(@Param('id', ParseUUIDPipe) id: string): Promise<void> {
    await this.usersService.remove(id);
  }

  @Get(':id/audit-logs')
  @UseGuards(JwtAuthGuard, RolesGuard)
  @Roles('admin')
  @ApiOperation({ summary: 'Get user audit logs' })
  async getAuditLogs(@Param('id', ParseUUIDPipe) id: string): Promise<any> {
    // Implementation for getting user audit logs
  }
}
```

### Product System Service
```typescript
// src/modules/products/services/products.service.ts
import {
  Injectable,
  NotFoundException,
  BadRequestException,
} from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, In, Like, Between } from 'typeorm';
import { Product } from '../entities/product.entity';
import { CreateProductDto } from '../dto/create-product.dto';
import { UpdateProductDto } from '../dto/update-product.dto';
import { InventoryService } from '../../inventory/services/inventory.service';
import { EventEmitter2 } from '@nestjs/event-emitter';

@Injectable()
export class ProductsService {
  constructor(
    @InjectRepository(Product)
    private readonly productRepository: Repository<Product>,
    private readonly inventoryService: InventoryService,
    private readonly eventEmitter: EventEmitter2,
  ) {}

  async create(createProductDto: CreateProductDto): Promise<Product> {
    // Check if SKU already exists
    const existingProduct = await this.productRepository.findOne({
      where: { sku: createProductDto.sku },
    });

    if (existingProduct) {
      throw new BadRequestException('Product with this SKU already exists');
    }

    // Create product
    const product = this.productRepository.create(createProductDto);
    const savedProduct = await this.productRepository.save(product);

    // Create inventory record
    await this.inventoryService.create({
      productId: savedProduct.id,
      quantity: createProductDto.initialQuantity || 0,
      reorderLevel: createProductDto.reorderLevel || 10,
      location: createProductDto.location,
    });

    // Emit event
    this.eventEmitter.emit('product.created', {
      productId: savedProduct.id,
      sku: savedProduct.sku,
      name: savedProduct.name,
      price: savedProduct.price,
      timestamp: new Date(),
    });

    return savedProduct;
  }

  async findAll(filters: {
    categoryId?: string;
    minPrice?: number;
    maxPrice?: number;
    inStock?: boolean;
    search?: string;
    page?: number;
    limit?: number;
  }): Promise<{ products: Product[]; total: number }> {
    const {
      categoryId,
      minPrice,
      maxPrice,
      inStock,
      search,
      page = 1,
      limit = 20,
    } = filters;

    const queryBuilder = this.productRepository
      .createQueryBuilder('product')
      .leftJoinAndSelect('product.inventory', 'inventory')
      .leftJoinAndSelect('product.categories', 'categories')
      .where('product.isActive = :isActive', { isActive: true });

    // Apply filters
    if (categoryId) {
      queryBuilder.andWhere('categories.id = :categoryId', { categoryId });
    }

    if (minPrice !== undefined) {
      queryBuilder.andWhere('product.price >= :minPrice', { minPrice });
    }

    if (maxPrice !== undefined) {
      queryBuilder.andWhere('product.price <= :maxPrice', { maxPrice });
    }

    if (inStock !== undefined) {
      queryBuilder.andWhere('inventory.quantity > 0');
    }

    if (search) {
      queryBuilder.andWhere(
        '(product.name LIKE :search OR product.description LIKE :search OR product.sku LIKE :search)',
        { search: `%${search}%` },
      );
    }

    // Count total
    const total = await queryBuilder.getCount();

    // Apply pagination
    const products = await queryBuilder
      .skip((page - 1) * limit)
      .take(limit)
      .orderBy('product.createdAt', 'DESC')
      .getMany();

    return { products, total };
  }

  async findOne(id: string): Promise<Product> {
    const product = await this.productRepository.findOne({
      where: { id },
      relations: ['inventory', 'categories', 'variants'],
    });

    if (!product) {
      throw new NotFoundException(`Product with ID ${id} not found`);
    }

    return product;
  }

  async update(id: string, updateProductDto: UpdateProductDto): Promise<Product> {
    const product = await this.findOne(id);
    
    // Update product
    Object.assign(product, updateProductDto);
    const updatedProduct = await this.productRepository.save(product);

    // Emit event
    this.eventEmitter.emit('product.updated', {
      productId: id,
      updatedFields: Object.keys(updateProductDto),
      timestamp: new Date(),
    });

    return updatedProduct;
  }

  async remove(id: string): Promise<void> {
    const product = await this.findOne(id);
    
    // Soft delete
    await this.productRepository.softDelete(id);

    // Emit event
    this.eventEmitter.emit('product.deleted', {
      productId: id,
      sku: product.sku,
      timestamp: new Date(),
    });
  }

  async getLowStockProducts(threshold: number = 10): Promise<Product[]> {
    return await this.productRepository
      .createQueryBuilder('product')
      .leftJoinAndSelect('product.inventory', 'inventory')
      .where('inventory.quantity <= :threshold', { threshold })
      .andWhere('product.isActive = :isActive', { isActive: true })
      .orderBy('inventory.quantity', 'ASC')
      .getMany();
  }

  async updateProductPrice(id: string, price: number): Promise<Product> {
    const product = await this.findOne(id);
    
    const oldPrice = product.price;
    product.price = price;
    
    const updatedProduct = await this.productRepository.save(product);

    // Emit price change event
    this.eventEmitter.emit('product.price_changed', {
      productId: id,
      oldPrice,
      newPrice: price,
      percentageChange: ((price - oldPrice) / oldPrice) * 100,
      timestamp: new Date(),
    });

    return updatedProduct;
  }
}
```

### Inventory System Service
```typescript
// src/modules/inventory/services/inventory.service.ts
import {
  Injectable,
  NotFoundException,
  BadRequestException,
} from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, DataSource } from 'typeorm';
import { Inventory } from '../entities/inventory.entity';
import { InventoryTransaction } from '../entities/inventory-transaction.entity';
import { CreateInventoryDto } from '../dto/create-inventory.dto';
import { AdjustInventoryDto } from '../dto/adjust-inventory.dto';
import { TransactionType } from '../enums/transaction-type.enum';

@Injectable()
export class InventoryService {
  constructor(
    @InjectRepository(Inventory)
    private readonly inventoryRepository: Repository<Inventory>,
    @InjectRepository(InventoryTransaction)
    private readonly transactionRepository: Repository<InventoryTransaction>,
    private readonly dataSource: DataSource,
  ) {}

  async create(createInventoryDto: CreateInventoryDto): Promise<Inventory> {
    // Check if inventory already exists for this product
    const existing = await this.inventoryRepository.findOne({
      where: { productId: createInventoryDto.productId },
    });

    if (existing) {
      throw new BadRequestException(
        'Inventory already exists for this product',
      );
    }

    const inventory = this.inventoryRepository.create(createInventoryDto);
    return await this.inventoryRepository.save(inventory);
  }

  async adjustStock(
    inventoryId: string,
    adjustInventoryDto: AdjustInventoryDto,
  ): Promise<InventoryTransaction> {
    const { quantity, type, referenceId, notes } = adjustInventoryDto;

    const queryRunner = this.dataSource.createQueryRunner();
    await queryRunner.connect();
    await queryRunner.startTransaction();

    try {
      // Lock inventory row for update
      const inventory = await queryRunner.manager
        .createQueryBuilder(Inventory, 'inventory')
        .setLock('pessimistic_write')
        .where('inventory.id = :id', { id: inventoryId })
        .getOne();

      if (!inventory) {
        throw new NotFoundException('Inventory not found');
      }

      const previousQuantity = inventory.quantity;
      let newQuantity = previousQuantity;

      // Calculate new quantity based on transaction type
      switch (type) {
        case TransactionType.RECEIVED:
        case TransactionType.RETURNED:
          newQuantity = previousQuantity + quantity;
          break;
        case TransactionType.SOLD:
        case TransactionType.DAMAGED:
        case TransactionType.ADJUSTED:
          newQuantity = previousQuantity - quantity;
          break;
        default:
          throw new BadRequestException('Invalid transaction type');
      }

      // Check for negative stock
      if (newQuantity < 0) {
        throw new BadRequestException('Insufficient stock');
      }

      // Update inventory
      inventory.quantity = newQuantity;
      
      if (type === TransactionType.RECEIVED) {
        inventory.lastRestocked = new Date();
      }

      await queryRunner.manager.save(inventory);

      // Create transaction record
      const transaction = this.transactionRepository.create({
        inventoryId,
        type,
        quantity,
        previousQuantity,
        newQuantity,
        referenceId,
        notes,
      });

      const savedTransaction = await queryRunner.manager.save(transaction);

      // Check reorder level
      if (newQuantity <= inventory.reorderLevel) {
        // Emit low stock event
        await queryRunner.manager
          .createQueryBuilder()
          .insert()
          .into('events')
          .values({
            type: 'inventory.low_stock',
            payload: {
              inventoryId,
              productId: inventory.productId,
              currentQuantity: newQuantity,
              reorderLevel: inventory.reorderLevel,
            },
            createdAt: new Date(),
          })
          .execute();
      }

      await queryRunner.commitTransaction();
      return savedTransaction;
    } catch (error) {
      await queryRunner.rollbackTransaction();
      throw error;
    } finally {
      await queryRunner.release();
    }
  }

  async getInventoryHistory(
    inventoryId: string,
    page: number = 1,
    limit: number = 50,
    startDate?: Date,
    endDate?: Date,
  ): Promise<{
    transactions: InventoryTransaction[];
    total: number;
    page: number;
    totalPages: number;
  }> {
    const queryBuilder = this.transactionRepository
      .createQueryBuilder('transaction')
      .where('transaction.inventoryId = :inventoryId', { inventoryId })
      .orderBy('transaction.createdAt', 'DESC');

    if (startDate) {
      queryBuilder.andWhere('transaction.createdAt >= :startDate', {
        startDate,
      });
    }

    if (endDate) {
      queryBuilder.andWhere('transaction.createdAt <= :endDate', { endDate });
    }

    const [transactions, total] = await queryBuilder
      .skip((page - 1) * limit)
      .take(limit)
      .getManyAndCount();

    return {
      transactions,
      total,
      page,
      totalPages: Math.ceil(total / limit),
    };
  }

  async getStockLevels(): Promise<
    Array<{
      productId: string;
      productName: string;
      sku: string;
      quantity: number;
      reorderLevel: number;
      status: 'IN_STOCK' | 'LOW_STOCK' | 'OUT_OF_STOCK';
    }>
  > {
    const inventoryItems = await this.inventoryRepository
      .createQueryBuilder('inventory')
      .leftJoinAndSelect('inventory.product', 'product')
      .select([
        'inventory.id',
        'inventory.quantity',
        'inventory.reorderLevel',
        'product.id',
        'product.name',
        'product.sku',
      ])
      .where('product.isActive = :isActive', { isActive: true })
      .getMany();

    return inventoryItems.map((item) => ({
      productId: item.product.id,
      productName: item.product.name,
      sku: item.product.sku,
      quantity: item.quantity,
      reorderLevel: item.reorderLevel,
      status:
        item.quantity === 0
          ? 'OUT_OF_STOCK'
          : item.quantity <= item.reorderLevel
          ? 'LOW_STOCK'
          : 'IN_STOCK',
    }));
  }

  async bulkUpdateInventory(
    updates: Array<{
      productId: string;
      quantity: number;
      type: TransactionType;
      referenceId?: string;
    }>,
  ): Promise<void> {
    const queryRunner = this.dataSource.createQueryRunner();
    await queryRunner.connect();
    await queryRunner.startTransaction();

    try {
      for (const update of updates) {
        const inventory = await queryRunner.manager.findOne(Inventory, {
          where: { productId: update.productId },
        });

        if (!inventory) {
          throw new NotFoundException(
            `Inventory not found for product ${update.productId}`,
          );
        }

        const previousQuantity = inventory.quantity;
        let newQuantity = previousQuantity;

        switch (update.type) {
          case TransactionType.RECEIVED:
            newQuantity = previousQuantity + update.quantity;
            break;
          case TransactionType.SOLD:
            newQuantity = previousQuantity - update.quantity;
            break;
        }

        if (newQuantity < 0) {
          throw new BadRequestException(
            `Insufficient stock for product ${update.productId}`,
          );
        }

        inventory.quantity = newQuantity;
        await queryRunner.manager.save(inventory);

        // Create transaction
        const transaction = this.transactionRepository.create({
          inventoryId: inventory.id,
          type: update.type,
          quantity: update.quantity,
          previousQuantity,
          newQuantity,
          referenceId: update.referenceId,
        });

        await queryRunner.manager.save(transaction);
      }

      await queryRunner.commitTransaction();
    } catch (error) {
      await queryRunner.rollbackTransaction();
      throw error;
    } finally {
      await queryRunner.release();
    }
  }
}
```

### Shipping System Service
```typescript
// src/modules/shipping/services/shipping.service.ts
import {
  Injectable,
  NotFoundException,
  BadRequestException,
} from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Shipping } from '../entities/shipping.entity';
import { CreateShippingDto } from '../dto/create-shipping.dto';
import { UpdateShippingDto } from '../dto/update-shipping.dto';
import { ShippingStatus } from '../enums/shipping-status.enum';
import { CarrierService } from './carrier.service';
import { NotificationService } from '../../notification/services/notification.service';

@Injectable()
export class ShippingService {
  constructor(
    @InjectRepository(Shipping)
    private readonly shippingRepository: Repository<Shipping>,
    private readonly carrierService: CarrierService,
    private readonly notificationService: NotificationService,
  ) {}

  async create(createShippingDto: CreateShippingDto): Promise<Shipping> {
    // Calculate shipping cost
    const shippingCost = await this.calculateShippingCost(
      createShippingDto.shippingAddress,
      createShippingDto.packageDimensions,
      createShippingDto.packageWeight,
    );

    // Get estimated delivery date
    const estimatedDelivery = await this.calculateEstimatedDelivery(
      createShippingDto.shippingAddress,
      createShippingDto.carrier,
    );

    // Create shipping record
    const shipping = this.shippingRepository.create({
      ...createShippingDto,
      shippingCost,
      estimatedDelivery,
      status: ShippingStatus.PENDING,
    });

    const savedShipping = await this.shippingRepository.save(shipping);

    // Generate shipping label
    const label = await this.carrierService.generateLabel({
      shippingId: savedShipping.id,
      carrier: createShippingDto.carrier,
      shippingAddress: createShippingDto.shippingAddress,
      packageDimensions: createShippingDto.packageDimensions,
      packageWeight: createShippingDto.packageWeight,
    });

    // Update with tracking number
    savedShipping.trackingNumber = label.trackingNumber;
    savedShipping.labelUrl = label.labelUrl;
    savedShipping.status = ShippingStatus.LABEL_CREATED;

    await this.shippingRepository.save(savedShipping);

    // Send notification
    await this.notificationService.sendShippingLabel({
      email: createShippingDto.customerEmail,
      trackingNumber: label.trackingNumber,
      labelUrl: label.labelUrl,
      estimatedDelivery,
    });

    return savedShipping;
  }

  async updateStatus(
    shippingId: string,
    status: ShippingStatus,
    trackingData?: any,
  ): Promise<Shipping> {
    const shipping = await this.findOne(shippingId);

    shipping.status = status;

    if (trackingData) {
      shipping.trackingHistory = [
        ...(shipping.trackingHistory || []),
        {
          status,
          location: trackingData.location,
          timestamp: new Date(),
          description: trackingData.description,
        },
      ];

      if (status === ShippingStatus.DELIVERED) {
        shipping.actualDelivery = new Date();
        
        // Update order status
        await this.updateOrderStatus(shipping.orderId, 'DELIVERED');
      }
    }

    return await this.shippingRepository.save(shipping);
  }

  async getTrackingInfo(shippingId: string): Promise<any> {
    const shipping = await this.findOne(shippingId);

    if (!shipping.trackingNumber) {
      throw new BadRequestException('No tracking number available');
    }

    // Get tracking info from carrier
    const trackingInfo = await this.carrierService.getTracking(
      shipping.carrier,
      shipping.trackingNumber,
    );

    // Update local tracking history
    if (trackingInfo.events?.length > 0) {
      const newEvents = trackingInfo.events.filter(
        (event) =>
          !shipping.trackingHistory?.some(
            (h) =>
              h.status === event.status &&
              h.timestamp.getTime() === new Date(event.timestamp).getTime(),
          ),
      );

      if (newEvents.length > 0) {
        shipping.trackingHistory = [
          ...(shipping.trackingHistory || []),
          ...newEvents.map((event) => ({
            status: event.status,
            location: event.location,
            timestamp: new Date(event.timestamp),
            description: event.description,
          })),
        ];

        // Update status if changed
        if (trackingInfo.currentStatus !== shipping.status) {
          shipping.status = trackingInfo.currentStatus;

          if (trackingInfo.currentStatus === ShippingStatus.DELIVERED) {
            shipping.actualDelivery = new Date(trackingInfo.deliveredAt);
          }
        }

        await this.shippingRepository.save(shipping);
      }
    }

    return {
      shipping,
      trackingInfo,
    };
  }

  async calculateShippingCost(
    address: any,
    dimensions: any,
    weight: number,
  ): Promise<number> {
    // Get rates from multiple carriers
    const rates = await Promise.all([
      this.carrierService.getRate('DHL', {
        address,
        dimensions,
        weight,
      }),
      this.carrierService.getRate('FEDEX', {
        address,
        dimensions,
        weight,
      }),
      this.carrierService.getRate('UPS', {
        address,
        dimensions,
        weight,
      }),
    ]);

    // Return the lowest rate
    return Math.min(...rates.map((r) => r.rate));
  }

  async findOne(id: string): Promise<Shipping> {
    const shipping = await this.shippingRepository.findOne({
      where: { id },
    });

    if (!shipping) {
      throw new NotFoundException(`Shipping with ID ${id} not found`);
    }

    return shipping;
  }

  private async calculateEstimatedDelivery(
    address: any,
    carrier: string,
  ): Promise<Date> {
    const transitTime = await this.carrierService.getTransitTime(
      carrier,
      address,
    );

    const deliveryDate = new Date();
    deliveryDate.setDate(deliveryDate.getDate() + transitTime);

    // Adjust for weekends
    while (deliveryDate.getDay() === 0 || deliveryDate.getDay() === 6) {
      deliveryDate.setDate(deliveryDate.getDate() + 1);
    }

    return deliveryDate;
  }

  private async updateOrderStatus(orderId: string, status: string): Promise<void> {
    // Call order service to update status
    // This would typically be done via message queue or HTTP call
  }
}
```

### Reports System Service
```typescript
// src/modules/reports/services/reports.service.ts
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, Between } from 'typeorm';
import * as ExcelJS from 'exceljs';
import * as PDFDocument from 'pdfkit';
import { Order } from '../../orders/entities/order.entity';
import { Product } from '../../products/entities/product.entity';
import { User } from '../../users/entities/user.entity';

export interface SalesReport {
  period: {
    start: Date;
    end: Date;
  };
  summary: {
    totalOrders: number;
    totalRevenue: number;
    averageOrderValue: number;
    uniqueCustomers: number;
  };
  dailyStats: Array<{
    date: string;
    orders: number;
    revenue: number;
    averageOrderValue: number;
  }>;
  topProducts: Array<{
    productId: string;
    productName: string;
    sku: string;
    quantitySold: number;
    revenue: number;
  }>;
  topCustomers: Array<{
    userId: string;
    email: string;
    orders: number;
    totalSpent: number;
  }>;
}

export interface InventoryReport {
  timestamp: Date;
  totalProducts: number;
  inStockProducts: number;
  outOfStockProducts: number;
  lowStockProducts: number;
  inventoryValue: number;
  lowStockItems: Array<{
    productId: string;
    productName: string;
    sku: string;
    currentQuantity: number;
    reorderLevel: number;
    status: string;
  }>;
}

@Injectable()
export class ReportsService {
  constructor(
    @InjectRepository(Order)
    private readonly orderRepository: Repository<Order>,
    @InjectRepository(Product)
    private readonly productRepository: Repository<Product>,
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
  ) {}

  async generateSalesReport(
    startDate: Date,
    endDate: Date,
  ): Promise<SalesReport> {
    // Get orders in date range
    const orders = await this.orderRepository.find({
      where: {
        createdAt: Between(startDate, endDate),
        status: 'COMPLETED',
      },
      relations: ['items', 'items.product', 'customer'],
    });

    // Calculate daily stats
    const dailyStatsMap = new Map<string, { orders: number; revenue: number }>();

    orders.forEach((order) => {
      const date = order.createdAt.toISOString().split('T')[0];
      const existing = dailyStatsMap.get(date) || { orders: 0, revenue: 0 };
      
      existing.orders += 1;
      existing.revenue += order.totalAmount;
      
      dailyStatsMap.set(date, existing);
    });

    const dailyStats = Array.from(dailyStatsMap.entries()).map(
      ([date, stats]) => ({
        date,
        orders: stats.orders,
        revenue: stats.revenue,
        averageOrderValue: stats.revenue / stats.orders,
      }),
    );

    // Calculate product sales
    const productSalesMap = new Map<
      string,
      { name: string; sku: string; quantity: number; revenue: number }
    >();

    orders.forEach((order) => {
      order.items.forEach((item) => {
        const existing = productSalesMap.get(item.productId) || {
          name: item.product.name,
          sku: item.product.sku,
          quantity: 0,
          revenue: 0,
        };
        
        existing.quantity += item.quantity;
        existing.revenue += item.price * item.quantity;
        
        productSalesMap.set(item.productId, existing);
      });
    });

    const topProducts = Array.from(productSalesMap.entries())
      .map(([productId, stats]) => ({
        productId,
        productName: stats.name,
        sku: stats.sku,
        quantitySold: stats.quantity,
        revenue: stats.revenue,
      }))
      .sort((a, b) => b.revenue - a.revenue)
      .slice(0, 10);

    // Calculate customer stats
    const customerStatsMap = new Map<
      string,
      { email: string; orders: number; totalSpent: number }
    >();

    orders.forEach((order) => {
      const customerId = order.customerId;
      const existing = customerStatsMap.get(customerId) || {
        email: order.customer?.email || 'Unknown',
        orders: 0,
        totalSpent: 0,
      };
      
      existing.orders += 1;
      existing.totalSpent += order.totalAmount;
      
      customerStatsMap.set(customerId, existing);
    });

    const topCustomers = Array.from(customerStatsMap.entries())
      .map(([userId, stats]) => ({
        userId,
        email: stats.email,
        orders: stats.orders,
        totalSpent: stats.totalSpent,
      }))
      .sort((a, b) => b.totalSpent - a.totalSpent)
      .slice(0, 10);

    return {
      period: { start: startDate, end: endDate },
      summary: {
        totalOrders: orders.length,
        totalRevenue: orders.reduce((sum, order) => sum + order.totalAmount, 0),
        averageOrderValue:
          orders.length > 0
            ? orders.reduce((sum, order) => sum + order.totalAmount, 0) /
              orders.length
            : 0,
        uniqueCustomers: customerStatsMap.size,
      },
      dailyStats,
      topProducts,
      topCustomers,
    };
  }

  async generateInventoryReport(): Promise<InventoryReport> {
    const products = await this.productRepository.find({
      where: { isActive: true },
      relations: ['inventory'],
    });

    const lowStockItems = products
      .filter(
        (product) =>
          product.inventory?.quantity <= product.inventory?.reorderLevel,
      )
      .map((product) => ({
        productId: product.id,
        productName: product.name,
        sku: product.sku,
        currentQuantity: product.inventory?.quantity || 0,
        reorderLevel: product.inventory?.reorderLevel || 0,
        status:
          product.inventory?.quantity === 0
            ? 'OUT_OF_STOCK'
            : 'LOW_STOCK',
      }));

    const inventoryValue = products.reduce((total, product) => {
      return total + product.price * (product.inventory?.quantity || 0);
    }, 0);

    return {
      timestamp: new Date(),
      totalProducts: products.length,
      inStockProducts: products.filter(
        (p) => (p.inventory?.quantity || 0) > 0,
      ).length,
      outOfStockProducts: products.filter(
        (p) => (p.inventory?.quantity || 0) === 0,
      ).length,
      lowStockProducts: lowStockItems.length,
      inventoryValue,
      lowStockItems,
    };
  }

  async exportSalesReportToExcel(
    report: SalesReport,
  ): Promise<ExcelJS.Buffer> {
    const workbook = new ExcelJS.Workbook();
    
    // Summary sheet
    const summarySheet = workbook.addWorksheet('Summary');
    summarySheet.columns = [
      { header: 'Metric', key: 'metric', width: 30 },
      { header: 'Value', key: 'value', width: 20 },
    ];
    
    summarySheet.addRows([
      { metric: 'Total Orders', value: report.summary.totalOrders },
      { metric: 'Total Revenue', value: report.summary.totalRevenue },
      { metric: 'Average Order Value', value: report.summary.averageOrderValue },
      { metric: 'Unique Customers', value: report.summary.uniqueCustomers },
    ]);

    // Daily stats sheet
    const dailySheet = workbook.addWorksheet('Daily Stats');
    dailySheet.columns = [
      { header: 'Date', key: 'date', width: 15 },
      { header: 'Orders', key: 'orders', width: 10 },
      { header: 'Revenue', key: 'revenue', width: 15 },
      { header: 'Average Order Value', key: 'avgOrderValue', width: 20 },
    ];
    
    report.dailyStats.forEach((stat) => {
      dailySheet.addRow({
        date: stat.date,
        orders: stat.orders,
        revenue: stat.revenue,
        avgOrderValue: stat.averageOrderValue,
      });
    });

    // Top products sheet
    const productsSheet = workbook.addWorksheet('Top Products');
    productsSheet.columns = [
      { header: 'Product Name', key: 'name', width: 30 },
      { header: 'SKU', key: 'sku', width: 15 },
      { header: 'Quantity Sold', key: 'quantity', width: 15 },
      { header: 'Revenue', key: 'revenue', width: 15 },
    ];
    
    report.topProducts.forEach((product) => {
      productsSheet.addRow({
        name: product.productName,
        sku: product.sku,
        quantity: product.quantitySold,
        revenue: product.revenue,
      });
    });

    return await workbook.xlsx.writeBuffer();
  }

  async exportSalesReportToPDF(report: SalesReport): Promise<Buffer> {
    return new Promise((resolve, reject) => {
      const doc = new PDFDocument({ margin: 50 });
      const buffers: Buffer[] = [];

      doc.on('data', buffers.push.bind(buffers));
      doc.on('end', () => {
        const pdfBuffer = Buffer.concat(buffers);
        resolve(pdfBuffer);
      });

      // Add content
      doc.fontSize(20).text('Sales Report', { align: 'center' });
      doc.moveDown();
      
      doc.fontSize(12).text(`Period: ${report.period.start.toDateString()} - ${report.period.end.toDateString()}`);
      doc.moveDown();
      
      // Summary section
      doc.fontSize(14).text('Summary');
      doc.fontSize(12).text(`Total Orders: ${report.summary.totalOrders}`);
      doc.text(`Total Revenue: $${report.summary.totalRevenue.toFixed(2)}`);
      doc.text(`Average Order Value: $${report.summary.averageOrderValue.toFixed(2)}`);
      doc.text(`Unique Customers: ${report.summary.uniqueCustomers}`);
      doc.moveDown();

      // Top products section
      doc.fontSize(14).text('Top Products');
      report.topProducts.forEach((product, index) => {
        doc.fontSize(12).text(
          `${index + 1}. ${product.productName} (${product.sku}) - ${product.quantitySold} sold - $${product.revenue.toFixed(2)}`,
        );
      });

      doc.end();
    });
  }

  async scheduleReport(
    reportType: string,
    schedule: string,
    email: string,
  ): Promise<void> {
    // Store schedule in database
    // This would be processed by a scheduled task
  }
}
```

### Audit System Service
```typescript
// src/modules/audit/services/audit.service.ts
import { Injectable, Inject } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, Between, Like } from 'typeorm';
import { AuditLog } from '../entities/audit-log.entity';
import { CreateAuditLogDto } from '../dto/create-audit-log.dto';
import { ClientProxy } from '@nestjs/microservices';

export interface AuditFilter {
  userId?: string;
  action?: string;
  entityType?: string;
  entityId?: string;
  startDate?: Date;
  endDate?: Date;
  search?: string;
  page?: number;
  limit?: number;
}

@Injectable()
export class AuditService {
  constructor(
    @InjectRepository(AuditLog)
    private readonly auditLogRepository: Repository<AuditLog>,
    @Inject('KAFKA_PRODUCER') private readonly kafkaClient: ClientProxy,
  ) {}

  async log(createAuditLogDto: CreateAuditLogDto): Promise<AuditLog> {
    const auditLog = this.auditLogRepository.create(createAuditLogDto);
    const savedLog = await this.auditLogRepository.save(auditLog);

    // Send to Kafka for centralized logging
    await this.kafkaClient.emit('audit.logged', {
      ...savedLog,
      timestamp: new Date().toISOString(),
    });

    return savedLog;
  }

  async findAll(filters: AuditFilter): Promise<{
    logs: AuditLog[];
    total: number;
    page: number;
    totalPages: number;
  }> {
    const {
      userId,
      action,
      entityType,
      entityId,
      startDate,
      endDate,
      search,
      page = 1,
      limit = 50,
    } = filters;

    const queryBuilder = this.auditLogRepository
      .createQueryBuilder('log')
      .leftJoinAndSelect('log.user', 'user')
      .orderBy('log.createdAt', 'DESC');

    if (userId) {
      queryBuilder.andWhere('log.userId = :userId', { userId });
    }

    if (action) {
      queryBuilder.andWhere('log.action = :action', { action });
    }

    if (entityType) {
      queryBuilder.andWhere('log.entityType = :entityType', { entityType });
    }

    if (entityId) {
      queryBuilder.andWhere('log.entityId = :entityId', { entityId });
    }

    if (startDate && endDate) {
      queryBuilder.andWhere('log.createdAt BETWEEN :startDate AND :endDate', {
        startDate,
        endDate,
      });
    }

    if (search) {
      queryBuilder.andWhere(
        '(log.action LIKE :search OR log.entityType LIKE :search OR log.entityId LIKE :search OR user.email LIKE :search)',
        { search: `%${search}%` },
      );
    }

    const [logs, total] = await queryBuilder
      .skip((page - 1) * limit)
      .take(limit)
      .getManyAndCount();

    return {
      logs,
      total,
      page,
      totalPages: Math.ceil(total / limit),
    };
  }

  async getUserActivity(userId: string, days: number = 30): Promise<any> {
    const startDate = new Date();
    startDate.setDate(startDate.getDate() - days);

    const logs = await this.auditLogRepository.find({
      where: {
        userId,
        createdAt: Between(startDate, new Date()),
      },
      order: { createdAt: 'DESC' },
      take: 100,
    });

    // Group by action type
    const activityByAction = logs.reduce((acc, log) => {
      acc[log.action] = (acc[log.action] || 0) + 1;
      return acc;
    }, {});

    // Group by date
    const activityByDate = logs.reduce((acc, log) => {
      const date = log.createdAt.toISOString().split('T')[0];
      acc[date] = (acc[date] || 0) + 1;
      return acc;
    }, {});

    return {
      totalActivities: logs.length,
      activityByAction,
      activityByDate,
      recentActivities: logs.slice(0, 20),
    };
  }

  async exportAuditLogs(filters: AuditFilter): Promise<string> {
    const { logs } = await this.findAll(filters);

    // Convert to CSV format
    const headers = [
      'Timestamp',
      'User ID',
      'Action',
      'Entity Type',
      'Entity ID',
      'IP Address',
      'User Agent',
      'Metadata',
    ];

    const rows = logs.map((log) => [
      log.createdAt.toISOString(),
      log.userId,
      log.action,
      log.entityType,
      log.entityId,
      log.ipAddress,
      log.userAgent,
      JSON.stringify(log.metadata),
    ]);

    const csvContent = [
      headers.join(','),
      ...rows.map((row) => row.join(',')),
    ].join('\n');

    return csvContent;
  }

  async cleanupOldLogs(days: number = 90): Promise<number> {
    const cutoffDate = new Date();
    cutoffDate.setDate(cutoffDate.getDate() - days);

    const result = await this.auditLogRepository
      .createQueryBuilder()
      .delete()
      .where('createdAt < :cutoffDate', { cutoffDate })
      .execute();

    return result.affected || 0;
  }
}
```

## 6. Technology Stack - Redis, Kafka, ELK, Grafana, n8n Integration

### Redis Service
```typescript
// src/modules/redis/services/redis.service.ts
import { Injectable, Inject, Logger } from '@nestjs/common';
import Redis from 'ioredis';

@Injectable()
export class RedisService {
  private readonly logger = new Logger(RedisService.name);

  constructor(@Inject('REDIS_CLIENT') private readonly redis: Redis) {}

  async get<T>(key: string): Promise<T | null> {
    try {
      const data = await this.redis.get(key);
      return data ? JSON.parse(data) : null;
    } catch (error) {
      this.logger.error(`Error getting key ${key}: ${error.message}`);
      return null;
    }
  }

  async set(
    key: string,
    value: any,
    ttl?: number,
  ): Promise<void> {
    try {
      const stringValue = JSON.stringify(value);
      
      if (ttl) {
        await this.redis.setex(key, ttl, stringValue);
      } else {
        await this.redis.set(key, stringValue);
      }
    } catch (error) {
      this.logger.error(`Error setting key ${key}: ${error.message}`);
      throw error;
    }
  }

  async del(key: string): Promise<void> {
    try {
      await this.redis.del(key);
    } catch (error) {
      this.logger.error(`Error deleting key ${key}: ${error.message}`);
      throw error;
    }
  }

  async exists(key: string): Promise<boolean> {
    try {
      const result = await this.redis.exists(key);
      return result === 1;
    } catch (error) {
      this.logger.error(`Error checking existence of key ${key}: ${error.message}`);
      return false;
    }
  }

  async increment(key: string, by: number = 1): Promise<number> {
    try {
      return await this.redis.incrby(key, by);
    } catch (error) {
      this.logger.error(`Error incrementing key ${key}: ${error.message}`);
      throw error;
    }
  }

  async decrement(key: string, by: number = 1): Promise<number> {
    try {
      return await this.redis.decrby(key, by);
    } catch (error) {
      this.logger.error(`Error decrementing key ${key}: ${error.message}`);
      throw error;
    }
  }

  async hset(key: string, field: string, value: any): Promise<void> {
    try {
      await this.redis.hset(key, field, JSON.stringify(value));
    } catch (error) {
      this.logger.error(`Error HSET key ${key}: ${error.message}`);
      throw error;
    }
  }

  async hget<T>(key: string, field: string): Promise<T | null> {
    try {
      const data = await this.redis.hget(key, field);
      return data ? JSON.parse(data) : null;
    } catch (error) {
      this.logger.error(`Error HGET key ${key}: ${error.message}`);
      return null;
    }
  }

  async sadd(key: string, members: string[]): Promise<number> {
    try {
      return await this.redis.sadd(key, ...members);
    } catch (error) {
      this.logger.error(`Error SADD key ${key}: ${error.message}`);
      throw error;
    }
  }

  async smembers(key: string): Promise<string[]> {
    try {
      return await this.redis.smembers(key);
    } catch (error) {
      this.logger.error(`Error SMEMBERS key ${key}: ${error.message}`);
      return [];
    }
  }

  async publish(channel: string, message: any): Promise<number> {
    try {
      return await this.redis.publish(channel, JSON.stringify(message));
    } catch (error) {
      this.logger.error(`Error publishing to channel ${channel}: ${error.message}`);
      throw error;
    }
  }

  async subscribe(
    channel: string,
    callback: (message: any) => void,
  ): Promise<void> {
    try {
      this.redis.subscribe(channel, (err, count) => {
        if (err) {
          this.logger.error(`Error subscribing to channel ${channel}: ${err.message}`);
          return;
        }
        this.logger.log(`Subscribed to ${count} channels`);
      });

      this.redis.on('message', (ch, message) => {
        if (ch === channel) {
          try {
            callback(JSON.parse(message));
          } catch (error) {
            this.logger.error(`Error parsing message from channel ${channel}: ${error.message}`);
          }
        }
      });
    } catch (error) {
      this.logger.error(`Error subscribing to channel ${channel}: ${error.message}`);
      throw error;
    }
  }

  async acquireLock(
    key: string,
    ttl: number = 10000,
    retryDelay: number = 100,
    maxRetries: number = 10,
  ): Promise<string | null> {
    const lockId = Date.now().toString();
    
    for (let i = 0; i < maxRetries; i++) {
      const result = await this.redis.set(
        key,
        lockId,
        'NX',
        'PX',
        ttl,
      );
      
      if (result === 'OK') {
        return lockId;
      }
      
      if (i < maxRetries - 1) {
        await new Promise(resolve => setTimeout(resolve, retryDelay));
      }
    }
    
    return null;
  }

  async releaseLock(key: string, lockId: string): Promise<boolean> {
    const script = `
      if redis.call("GET", KEYS[1]) == ARGV[1] then
        return redis.call("DEL", KEYS[1])
      else
        return 0
      end
    `;
    
    try {
      const result = await this.redis.eval(script, 1, key, lockId);
      return result === 1;
    } catch (error) {
      this.logger.error(`Error releasing lock ${key}: ${error.message}`);
      return false;
    }
  }

  async getKeys(pattern: string): Promise<string[]> {
    try {
      return await this.redis.keys(pattern);
    } catch (error) {
      this.logger.error(`Error getting keys with pattern ${pattern}: ${error.message}`);
      return [];
    }
  }

  async flushAll(): Promise<void> {
    try {
      await this.redis.flushall();
    } catch (error) {
      this.logger.error(`Error flushing all keys: ${error.message}`);
      throw error;
    }
  }

  async getMemoryInfo(): Promise<any> {
    try {
      const info = await this.redis.info('memory');
      const lines = info.split('\r\n');
      const memoryInfo: any = {};
      
      lines.forEach(line => {
        if (line.includes(':')) {
          const [key, value] = line.split(':');
          memoryInfo[key] = value;
        }
      });
      
      return memoryInfo;
    } catch (error) {
      this.logger.error(`Error getting memory info: ${error.message}`);
      return {};
    }
  }
}
```

### Kafka Configuration Module
```typescript
// src/modules/kafka/kafka.module.ts
import { Module, Global } from '@nestjs/common';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { ClientsModule, Transport } from '@nestjs/microservices';
import { KafkaService } from './services/kafka.service';
import { OrderProducer } from './producers/order.producer';
import { InventoryConsumer } from './consumers/inventory.consumer';

@Global()
@Module({
  imports: [
    ClientsModule.registerAsync([
      {
        name: 'KAFKA_PRODUCER',
        imports: [ConfigModule],
        useFactory: (configService: ConfigService) => ({
          transport: Transport.KAFKA,
          options: {
            client: {
              clientId: configService.get('KAFKA_CLIENT_ID', 'nestjs-app'),
              brokers: configService
                .get('KAFKA_BROKERS', 'localhost:9092')
                .split(','),
              ssl: configService.get('KAFKA_SSL') === 'true',
              sasl: configService.get('KAFKA_USERNAME')
                ? {
                    mechanism: 'plain',
                    username: configService.get('KAFKA_USERNAME'),
                    password: configService.get('KAFKA_PASSWORD'),
                  }
                : undefined,
            },
            producer: {
              allowAutoTopicCreation: true,
              transactionTimeout: 30000,
            },
            consumer: {
              groupId: configService.get(
                'KAFKA_CONSUMER_GROUP',
                'nestjs-consumer',
              ),
              sessionTimeout: 30000,
              heartbeatInterval: 3000,
              allowAutoTopicCreation: true,
            },
          },
        }),
        inject: [ConfigService],
      },
    ]),
  ],
  providers: [KafkaService, OrderProducer, InventoryConsumer],
  exports: [KafkaService, OrderProducer, 'KAFKA_PRODUCER'],
})
export class KafkaModule {}
```

### ELK Logging Service
```typescript
// src/modules/logging/services/elk.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { Client } from '@elastic/elasticsearch';
import { ConfigService } from '@nestjs/config';

export interface LogEntry {
  timestamp: Date;
  level: 'info' | 'warn' | 'error' | 'debug';
  service: string;
  message: string;
  context?: string;
  userId?: string;
  requestId?: string;
  metadata?: any;
  error?: {
    message: string;
    stack?: string;
  };
}

@Injectable()
export class ElkService {
  private readonly logger = new Logger(ElkService.name);
  private client: Client;

  constructor(private readonly configService: ConfigService) {
    this.client = new Client({
      node: this.configService.get('ELASTICSEARCH_NODE'),
      auth: {
        username: this.configService.get('ELASTICSEARCH_USERNAME'),
        password: this.configService.get('ELASTICSEARCH_PASSWORD'),
      },
    });
  }

  async log(logEntry: LogEntry): Promise<void> {
    try {
      const index = this.getIndexName();
      
      await this.client.index({
        index,
        body: {
          ...logEntry,
          '@timestamp': logEntry.timestamp.toISOString(),
        },
      });
    } catch (error) {
      this.logger.error(`Failed to send log to ELK: ${error.message}`);
      // Fallback to console logging
      console.error('ELK Logging failed:', logEntry);
    }
  }

  async searchLogs(query: any): Promise<any> {
    try {
      const index = this.getIndexName();
      
      const response = await this.client.search({
        index,
        body: query,
      });
      
      return response.body;
    } catch (error) {
      this.logger.error(`Failed to search logs: ${error.message}`);
      throw error;
    }
  }

  async getLogStats(timeRange: string = '1d'): Promise<any> {
    try {
      const index = this.getIndexName();
      
      const response = await this.client.search({
        index,
        body: {
          size: 0,
          query: {
            range: {
              '@timestamp': {
                gte: `now-${timeRange}`,
              },
            },
          },
          aggs: {
            by_level: {
              terms: {
                field: 'level.keyword',
              },
            },
            by_service: {
              terms: {
                field: 'service.keyword',
              },
            },
            hourly: {
              date_histogram: {
                field: '@timestamp',
                calendar_interval: 'hour',
              },
              aggs: {
                error_count: {
                  filter: {
                    term: { level: 'error' },
                  },
                },
              },
            },
          },
        },
      });
      
      return response.body.aggregations;
    } catch (error) {
      this.logger.error(`Failed to get log stats: ${error.message}`);
      throw error;
    }
  }

  private getIndexName(): string {
    const date = new Date();
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    
    return `logs-${year}.${month}.${day}`;
  }

  async checkHealth(): Promise<boolean> {
    try {
      const health = await this.client.cluster.health();
      return health.body.status !== 'red';
    } catch (error) {
      this.logger.error(`ELK health check failed: ${error.message}`);
      return false;
    }
  }
}
```

### Grafana Dashboard Service
```typescript
// src/modules/monitoring/services/grafana.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { HttpService } from '@nestjs/axios';
import { ConfigService } from '@nestjs/config';
import { firstValueFrom } from 'rxjs';

export interface Dashboard {
  title: string;
  panels: Panel[];
}

export interface Panel {
  title: string;
  type: 'graph' | 'stat' | 'table' | 'gauge';
  targets: Target[];
  gridPos: {
    h: number;
    w: number;
    x: number;
    y: number;
  };
}

export interface Target {
  expr: string;
  legendFormat?: string;
  refId: string;
}

@Injectable()
export class GrafanaService {
  private readonly logger = new Logger(GrafanaService.name);
  private readonly baseUrl: string;
  private readonly apiKey: string;

  constructor(
    private readonly configService: ConfigService,
    private readonly httpService: HttpService,
  ) {
    this.baseUrl = this.configService.get('GRAFANA_URL');
    this.apiKey = this.configService.get('GRAFANA_API_KEY');
  }

  async createDashboard(dashboard: Dashboard): Promise<string> {
    try {
      const response = await firstValueFrom(
        this.httpService.post(
          `${this.baseUrl}/api/dashboards/db`,
          {
            dashboard: {
              ...dashboard,
              id: null,
              version: 0,
              timezone: 'browser',
            },
            overwrite: false,
          },
          {
            headers: {
              Authorization: `Bearer ${this.apiKey}`,
              'Content-Type': 'application/json',
            },
          },
        ),
      );

      return response.data.uid;
    } catch (error) {
      this.logger.error(`Failed to create dashboard: ${error.message}`);
      throw error;
    }
  }

  async createMicroservicesDashboard(): Promise<string> {
    const dashboard: Dashboard = {
      title: 'Microservices Monitoring',
      panels: [
        {
          title: 'API Response Times',
          type: 'graph',
          targets: [
            {
              expr: 'rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])',
              legendFormat: '{{service}} - {{route}}',
              refId: 'A',
            },
          ],
          gridPos: { h: 8, w: 12, x: 0, y: 0 },
        },
        {
          title: 'Error Rate',
          type: 'stat',
          targets: [
            {
              expr: 'sum(rate(http_requests_total{status=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) * 100',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 12, y: 0 },
        },
        {
          title: 'Database Connections',
          type: 'gauge',
          targets: [
            {
              expr: 'pg_stat_database_numbackends',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 18, y: 0 },
        },
        {
          title: 'Redis Memory Usage',
          type: 'gauge',
          targets: [
            {
              expr: 'redis_memory_used_bytes / redis_memory_max_bytes * 100',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 0, y: 8 },
        },
        {
          title: 'Kafka Lag',
          type: 'graph',
          targets: [
            {
              expr: 'kafka_consumer_lag',
              legendFormat: '{{topic}} - {{consumer_group}}',
              refId: 'A',
            },
          ],
          gridPos: { h: 8, w: 12, x: 12, y: 8 },
        },
      ],
    };

    return await this.createDashboard(dashboard);
  }

  async createBusinessMetricsDashboard(): Promise<string> {
    const dashboard: Dashboard = {
      title: 'Business Metrics',
      panels: [
        {
          title: 'Total Orders',
          type: 'stat',
          targets: [
            {
              expr: 'orders_total',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 0, y: 0 },
        },
        {
          title: 'Revenue',
          type: 'stat',
          targets: [
            {
              expr: 'revenue_total',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 6, y: 0 },
        },
        {
          title: 'Active Users',
          type: 'stat',
          targets: [
            {
              expr: 'users_active',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 12, y: 0 },
        },
        {
          title: 'Conversion Rate',
          type: 'stat',
          targets: [
            {
              expr: 'orders_total / visits_total * 100',
              refId: 'A',
            },
          ],
          gridPos: { h: 4, w: 6, x: 18, y: 0 },
        },
        {
          title: 'Sales Trend',
          type: 'graph',
          targets: [
            {
              expr: 'rate(orders_total[1d])',
              refId: 'A',
            },
          ],
          gridPos: { h: 8, w: 24, x: 0, y: 4 },
        },
      ],
    };

    return await this.createDashboard(dashboard);
  }

  async getDashboard(uid: string): Promise<any> {
    try {
      const response = await firstValueFrom(
        this.httpService.get(`${this.baseUrl}/api/dashboards/uid/${uid}`, {
          headers: {
            Authorization: `Bearer ${this.apiKey}`,
          },
        }),
      );

      return response.data;
    } catch (error) {
      this.logger.error(`Failed to get dashboard: ${error.message}`);
      throw error;
    }
  }

  async listDashboards(): Promise<any[]> {
    try {
      const response = await firstValueFrom(
        this.httpService.get(`${this.baseUrl}/api/search`, {
          headers: {
            Authorization: `Bearer ${this.apiKey}`,
          },
        }),
      );

      return response.data;
    } catch (error) {
      this.logger.error(`Failed to list dashboards: ${error.message}`);
      throw error;
    }
  }

  async deleteDashboard(uid: string): Promise<void> {
    try {
      await firstValueFrom(
        this.httpService.delete(`${this.baseUrl}/api/dashboards/uid/${uid}`, {
          headers: {
            Authorization: `Bearer ${this.apiKey}`,
          },
        }),
      );
    } catch (error) {
      this.logger.error(`Failed to delete dashboard: ${error.message}`);
      throw error;
    }
  }
}
```

### n8n Integration Service
```typescript
// src/modules/integration/services/n8n.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { HttpService } from '@nestjs/axios';
import { ConfigService } from '@nestjs/config';
import { firstValueFrom } from 'rxjs';

export interface N8nWorkflow {
  name: string;
  nodes: any[];
  connections: any;
}

export interface WorkflowExecution {
  workflowId: string;
  data: any;
}

@Injectable()
export class N8nService {
  private readonly logger = new Logger(N8nService.name);
  private readonly baseUrl: string;
  private readonly apiKey: string;

  constructor(
    private readonly configService: ConfigService,
    private readonly httpService: HttpService,
  ) {
    this.baseUrl = this.configService.get('N8N_URL');
    this.apiKey = this.configService.get('N8N_API_KEY');
  }

  async triggerWorkflow(
    workflowId: string,
    data: any,
  ): Promise<any> {
    try {
      const response = await firstValueFrom(
        this.httpService.post(
          `${this.baseUrl}/webhook/${workflowId}`,
          data,
          {
            headers: {
              'Content-Type': 'application/json',
              'X-N8N-API-KEY': this.apiKey,
            },
          },
        ),
      );

      return response.data;
    } catch (error) {
      this.logger.error(`Failed to trigger workflow ${workflowId}: ${error.message}`);
      throw error;
    }
  }

  async createUserWelcomeWorkflow(userId: string, userData: any): Promise<void> {
    const workflowData = {
      event: 'user.registered',
      timestamp: new Date().toISOString(),
      data: {
        userId,
        email: userData.email,
        username: userData.username,
        welcomeLink: `${this.configService.get('APP_URL')}/welcome/${userId}`,
      },
    };

    await this.triggerWorkflow('user-welcome', workflowData);
  }

  async createOrderConfirmationWorkflow(orderId: string, orderData: any): Promise<void> {
    const workflowData = {
      event: 'order.created',
      timestamp: new Date().toISOString(),
      data: {
        orderId,
        customerEmail: orderData.customerEmail,
        items: orderData.items,
        totalAmount: orderData.totalAmount,
        shippingAddress: orderData.shippingAddress,
        estimatedDelivery: orderData.estimatedDelivery,
      },
    };

    await this.triggerWorkflow('order-confirmation', workflowData);
  }

  async createLowStockAlertWorkflow(
    productId: string,
    productData: any,
  ): Promise<void> {
    const workflowData = {
      event: 'inventory.low_stock',
      timestamp: new Date().toISOString(),
      data: {
        productId,
        productName: productData.name,
        sku: productData.sku,
        currentQuantity: productData.currentQuantity,
        reorderLevel: productData.reorderLevel,
        lastRestocked: productData.lastRestocked,
      },
    };

    await this.triggerWorkflow('low-stock-alert', workflowData);
  }

  async createMonthlyReportWorkflow(): Promise<void> {
    const workflowData = {
      event: 'report.monthly',
      timestamp: new Date().toISOString(),
      data: {
        month: new Date().getMonth() + 1,
        year: new Date().getFullYear(),
        recipients: this.configService.get('REPORT_RECIPIENTS').split(','),
      },
    };

    await this.triggerWorkflow('monthly-report', workflowData);
  }

  async createBackupWorkflow(): Promise<void> {
    const workflowData = {
      event: 'backup.daily',
      timestamp: new Date().toISOString(),
      data: {
        databases: ['users', 'products', 'orders', 'inventory'],
        storage: 's3',
        bucket: this.configService.get('BACKUP_BUCKET'),
        retentionDays: 30,
      },
    };

    await this.triggerWorkflow('daily-backup', workflowData);
  }

  async testWorkflowConnection(): Promise<boolean> {
    try {
      await firstValueFrom(
        this.httpService.get(`${this.baseUrl}/health`, {
          headers: {
            'X-N8N-API-KEY': this.apiKey,
          },
        }),
      );
      return true;
    } catch (error) {
      this.logger.error(`n8n connection test failed: ${error.message}`);
      return false;
    }
  }

  async getWorkflowExecutions(
    workflowId: string,
    limit: number = 50,
  ): Promise<any[]> {
    try {
      const response = await firstValueFrom(
        this.httpService.get(
          `${this.baseUrl}/api/v1/workflows/${workflowId}/executions`,
          {
            params: { limit },
            headers: {
              'X-N8N-API-KEY': this.apiKey,
            },
          },
        ),
      );

      return response.data;
    } catch (error) {
      this.logger.error(`Failed to get workflow executions: ${error.message}`);
      return [];
    }
  }
}
```

## 7. Git Flow & CI/CD - Branch Strategy, GitHub Actions, Docker Builds, Deployment

### Branch Strategy
```bash
# Branch naming convention
git checkout -b feature/user-authentication
git checkout -b bugfix/login-validation
git checkout -b hotfix/critical-security-fix
git checkout -b release/v1.2.0
git checkout -b chore/update-dependencies
git checkout -b docs/update-api-docs

# Workflow example
# 1. Create feature branch from develop
git checkout develop
git pull origin develop
git checkout -b feature/new-feature

# 2. Develop and commit
git add .
git commit -m "feat: add new feature implementation"
git push origin feature/new-feature

# 3. Create pull request to develop
# 4. After review and approval, merge to develop
git checkout develop
git merge --no-ff feature/new-feature
git push origin develop

# 5. Delete feature branch
git branch -d feature/new-feature
git push origin --delete feature/new-feature

# 6. Create release branch
git checkout -b release/v1.0.0 develop

# 7. Prepare release (bump version, update changelog)
# 8. Merge to main and tag
git checkout main
git merge --no-ff release/v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin main --tags

# 9. Merge back to develop
git checkout develop
git merge --no-ff release/v1.0.0
git push origin develop

# 10. Delete release branch
git branch -d release/v1.0.0
```

### GitHub Actions CI/CD Pipeline
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ develop ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15-alpine
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
      
      redis:
        image: redis:7-alpine
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379

    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'

    - name: Install dependencies
      run: npm ci

    - name: Run linter
      run: npm run lint

    - name: Run unit tests
      run: npm test
      env:
        NODE_ENV: test
        DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test
        REDIS_URL: redis://localhost:6379
        JWT_SECRET: test-secret

    - name: Run API tests with Robot Framework
      run: |
        pip install robotframework robotframework-requests
        npm run test:api

    - name: Upload test results
      uses: actions/upload-artifact@v3
      if: always()
      with:
        name: test-results
        path: |
          coverage/
          test-results/
          robot-reports/

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/develop'
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2

    - name: Log in to Container Registry
      uses: docker/login-action@v2
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v4
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=semver,pattern={{version}}
          type=semver,pattern={{major}}.{{minor}}
          type=sha,prefix={{branch}}-

    - name: Build and push backend
      uses: docker/build-push-action@v4
      with:
        context: ./backend
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max

    - name: Build and push frontend
      uses: docker/build-push-action@v4
      with:
        context: ./frontend
        push: true
        tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}-frontend:${{ github.sha }}
        cache-from: type=gha
        cache-to: type=gha,mode=max

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/develop'
    environment: staging
    
    steps:
    - name: Deploy to staging
      uses: appleboy/ssh-action@v0.1.5
      with:
        host: ${{ secrets.STAGING_HOST }}
        username: ${{ secrets.STAGING_USERNAME }}
        key: ${{ secrets.STAGING_SSH_KEY }}
        script: |
          cd /opt/microservices
          git pull origin develop
          docker-compose -f docker-compose.staging.yml pull
          docker-compose -f docker-compose.staging.yml up -d --build
          docker system prune -f

  deploy-production:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    environment: production
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ secrets.AWS_REGION }}

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Deploy to ECS
      uses: aws-actions/amazon-ecs-deploy-task-definition@v1
      with:
        task-definition: task-definition.json
        service: microservices-service
        cluster: microservices-cluster
        wait-for-service-stability: true

    - name: Run database migrations
      run: |
        aws ecs run-task \
          --cluster microservices-cluster \
          --task-definition migration-task \
          --launch-type FARGATE \
          --network-configuration "awsvpcConfiguration={subnets=[${{ secrets.SUBNET_ID }}],securityGroups=[${{ secrets.SECURITY_GROUP_ID }}],assignPublicIp=ENABLED}"

  notify:
    needs: [test, build, deploy-staging, deploy-production]
    runs-on: ubuntu-latest
    if: always()
    
    steps:
    - name: Send Slack notification
      uses: 8398a7/action-slack@v3
      with:
        status: ${{ job.status }}
        channel: '#deployments'
        username: 'GitHub Actions'
        icon_emoji: ':github:'
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### Dockerfile Examples
```dockerfile
# backend/Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY tsconfig*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM node:18-alpine AS production

WORKDIR /app

# Install runtime dependencies
RUN apk add --no-cache tini

# Copy built application
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package*.json ./

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Change ownership
RUN chown -R nodejs:nodejs /app

# Switch to non-root user
USER nodejs

# Use tini as init process
ENTRYPOINT ["/sbin/tini", "--"]

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3001/health', (r) => {if(r.statusCode !== 200) throw new Error()})"

# Start application
CMD ["node", "dist/main.js"]
```

```dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy source code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM nginx:alpine

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Copy built application
COPY --from=builder /app/.next /usr/share/nginx/html

# Create non-root user
RUN addgroup -g 1001 -S nginx && \
    adduser -S nginx -u 1001

# Change ownership
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    chown -R nginx:nginx /etc/nginx/conf.d

# Switch to non-root user
USER nginx

EXPOSE 3000

CMD ["nginx", "-g", "daemon off;"]
```

### Deployment Configuration
```yaml
# docker-compose.prod.yml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis_data:/data
    networks:
      - backend
    restart: unless-stopped

  backend:
    image: ${DOCKER_REGISTRY}/backend:${TAG}
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://:${REDIS_PASSWORD}@redis:6379
      - JWT_SECRET=${JWT_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
    networks:
      - backend
      - frontend
    restart: unless-stopped
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first

  frontend:
    image: ${DOCKER_REGISTRY}/frontend:${TAG}
    networks:
      - frontend
    restart: unless-stopped
    deploy:
      replicas: 2

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
    networks:
      - frontend
    restart: unless-stopped
    depends_on:
      - frontend

networks:
  backend:
    driver: bridge
  frontend:
    driver: bridge

volumes:
  postgres_data:
  redis_data:
```

## 8. Robot Framework Testing - API Tests, Database Tests, Performance Tests

### API Test Suite
```robot
*** Settings ***
Documentation    Microservices API Test Suite
Library          RequestsLibrary
Library          Collections
Library          OperatingSystem
Library          String
Library          DateTime
Library          DatabaseLibrary
Library          JSONLibrary
Library          Process

Suite Setup      Suite Setup
Suite Teardown   Suite Teardown
Test Setup       Test Setup
Test Teardown    Test Teardown

*** Variables ***
${BASE_URL}      http://localhost:3000/api
${ADMIN_EMAIL}   admin@example.com
${ADMIN_PASSWORD}    Admin123!
${TEST_USER_EMAIL}   test.user+${RANDOM}@example.com
${TEST_USER_PASSWORD}    Test123!

*** Test Cases ***

User Registration API Test
    [Documentation]    Test user registration API
    [Tags]             api    auth    smoke
    ${headers}=        Create Dictionary    Content-Type=application/json
    ${email}=          Generate Random Email
    ${payload}=        Create Dictionary
    ...                email=${email}
    ...                username=testuser${RANDOM}
    ...                password=${TEST_USER_PASSWORD}
    ...                firstName=Test
    ...                lastName=User
    
    ${response}=       POST    ${BASE_URL}/auth/register    json=${payload}    headers=${headers}
    
    Status Should Be    201    ${response}
    Should Be Equal     ${response.json()['email']}    ${email}
    Dictionary Should Contain Key    ${response.json()}    id
    Dictionary Should Contain Key    ${response.json()}    username
    
    # Verify in database
    ${db_user}=        Query    SELECT * FROM users WHERE email = '${email}'
    Should Not Be Empty    ${db_user}
    
    # Set test user ID for subsequent tests
    Set Suite Variable    ${TEST_USER_ID}    ${response.json()['id']}

User Login API Test
    [Documentation]    Test user login API
    [Tags]             api    auth    smoke
    ${headers}=        Create Dictionary    Content-Type=application/json
    ${payload}=        Create Dictionary
    ...                email=${ADMIN_EMAIL}
    ...                password=${ADMIN_PASSWORD}
    
    ${response}=       POST    ${BASE_URL}/auth/login    json=${payload}    headers=${headers}
    
    Status Should Be    200    ${response}
    Dictionary Should Contain Key    ${response.json()}    accessToken
    Dictionary Should Contain Key    ${response.json()}    refreshToken
    Dictionary Should Contain Key    ${response.json()}    expiresIn
    
    # Store tokens for subsequent tests
    ${access_token}=    Set Variable    ${response.json()['accessToken']}
    ${refresh_token}=    Set Variable    ${response.json()['refreshToken']}
    Set Suite Variable    ${ACCESS_TOKEN}    ${access_token}
    Set Suite Variable    ${REFRESH_TOKEN}    ${refresh_token}

Product CRUD API Test
    [Documentation]    Test product CRUD operations
    [Tags]             api    products    crud
    [Setup]            Get Auth Token
    
    # Create product
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    ${sku}=            Generate Random String    10    [LOWER][NUMBERS]
    ${payload}=        Create Dictionary
    ...                sku=${sku}
    ...                name=Test Product ${RANDOM}
    ...                description=Test Product Description
    ...                price=99.99
    ...                category=Electronics
    ...                initialQuantity=100
    
    ${response}=       POST    ${BASE_URL}/products    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    ${product_id}=     Set Variable    ${response.json()['id']}
    Set Suite Variable    ${PRODUCT_ID}    ${product_id}
    
    # Get product
    ${response}=       GET    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal     ${response.json()['sku']}    ${sku}
    Should Be Equal As Numbers    ${response.json()['price']}    99.99
    
    # Update product
    ${update_payload}= Create Dictionary    price=149.99    description=Updated Description
    ${response}=       PUT    ${BASE_URL}/products/${product_id}    json=${update_payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal As Numbers    ${response.json()['price']}    149.99
    Should Be Equal     ${response.json()['description']}    Updated Description
    
    # Delete product
    ${response}=       DELETE    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    204    ${response}
    
    # Verify deletion
    ${response}=       GET    ${BASE_URL}/products/${product_id}    headers=${headers}
    Status Should Be    404    ${response}

Order Flow API Test
    [Documentation]    Test complete order flow
    [Tags]             api    orders    e2e
    [Setup]            Get Auth Token
    Create Test Product
    
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    # Create order
    ${payload}=        Create Dictionary
    ...                items=${[{"productId": "${PRODUCT_ID}", "quantity": 2}]}
    ...                shippingAddress=${{
    ...                    "street": "123 Test Street",
    ...                    "city": "Bangkok",
    ...                    "postalCode": "10110",
    ...                    "country": "Thailand"
    ...                }}
    ...                paymentMethod=credit_card
    
    ${response}=       POST    ${BASE_URL}/orders    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    ${order_id}=       Set Variable    ${response.json()['id']}
    
    # Verify order status
    Should Be Equal     ${response.json()['status']}    PENDING
    Should Be Equal As Numbers    ${response.json()['totalAmount']}    199.98
    
    # Process payment
    ${payment_payload}= Create Dictionary
    ...                paymentMethod=credit_card
    ...                paymentDetails=${{
    ...                    "cardNumber": "4111111111111111",
    ...                    "expiry": "12/25",
    ...                    "cvc": "123"
    ...                }}
    
    ${response}=       POST    ${BASE_URL}/orders/${order_id}/pay    json=${payment_payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal     ${response.json()['status']}    PAID
    
    # Verify inventory updated
    ${response}=       GET    ${BASE_URL}/inventory/${PRODUCT_ID}    headers=${headers}
    ${current_quantity}=    Set Variable    ${response.json()['quantity']}
    Should Be Equal As Numbers    ${current_quantity}    98    # 100 - 2

API Validation Tests
    [Documentation]    Test API validation
    [Tags]             api    validation
    [Template]         Test Invalid Registration
    
    # email                username        password    expected_error
    invalid-email         testuser        Test123!    Email is invalid
    ${EMPTY}              testuser        Test123!    Email should not be empty
    test@example.com      ${EMPTY}        Test123!    Username should not be empty
    test@example.com      testuser        weak        Password is too weak
    test@example.com      t               Test123!    Username must be at least 3 characters
    test@example.com      verylongusername1234567890    Test123!    Username must be at most 50 characters

Performance Load Test
    [Documentation]    Test API performance under load
    [Tags]             performance    load
    [Setup]            Get Auth Token
    
    ${headers}=        Create Dictionary
    ...                Content-Type=application/json
    ...                Authorization=Bearer ${ACCESS_TOKEN}
    
    ${start_time}=     Get Current Date
    
    FOR    ${i}    IN RANGE    100
        ${response}=    GET    ${BASE_URL}/products    headers=${headers}
        Status Should Be    200    ${response}
    END
    
    ${end_time}=       Get Current Date
    ${duration}=       Subtract Date From Date    ${end_time}    ${start_time}
    ${rps}=            Evaluate    100 / ${duration}
    
    Log    Performance: ${rps:.2f} requests per second
    Should Be True    ${rps} > 50    Minimum 50 requests/second required

Database Integration Test
    [Documentation]    Test database integration
    [Tags]             database    integration
    
    # Test database connection
    Row Count Is Equal    SELECT 1    1
    
    # Test data consistency
    ${users}=    Query    SELECT COUNT(*) as count FROM users WHERE is_active = true
    ${response}=    GET    ${BASE_URL}/users
    ${api_count}=    Set Variable    ${response.json()['total']}
    
    Should Be Equal As Numbers    ${users[0][0]}    ${api_count}

*** Keywords ***

Suite Setup
    Connect To Database    psycopg2
    ...    ${DB_NAME}
    ...    ${DB_USER}
    ...    ${DB_PASSWORD}
    ...    ${DB_HOST}
    ...    ${DB_PORT}
    
    Create Session    api    ${BASE_URL}

Suite Teardown
    Delete All Sessions
    Disconnect From Database

Test Setup
    # Clear test data
    Execute SQL String
    ...    DELETE FROM orders WHERE customer_id = '${TEST_USER_ID}'
    
    Execute SQL String
    ...    DELETE FROM inventory_transactions WHERE created_by = '${TEST_USER_ID}'

Test Teardown
    Run Keyword If Test Failed    Log Test Failure

Get Auth Token
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...            email=${ADMIN_EMAIL}
    ...            password=${ADMIN_PASSWORD}
    
    ${response}=   POST    /auth/login    json=${payload}    headers=${headers}
    ${ACCESS_TOKEN}=    Set Variable    ${response.json()['accessToken']}
    Set Suite Variable    ${ACCESS_TOKEN}

Generate Random Email
    ${random}=    Generate Random String    8    [LOWER]
    ${email}=     Catenate    SEPARATOR=@    test.${random}    example.com
    [Return]      ${email}

Create Test Product
    ${headers}=    Create Dictionary
    ...            Content-Type=application/json
    ...            Authorization=Bearer ${ACCESS_TOKEN}
    
    ${sku}=        Generate Random String    10    [LOWER][NUMBERS]
    ${payload}=    Create Dictionary
    ...            sku=${sku}
    ...            name=Test Product ${RANDOM}
    ...            description=Test Description
    ...            price=99.99
    ...            category=Electronics
    ...            initialQuantity=100
    
    ${response}=   POST    /products    json=${payload}    headers=${headers}
    ${PRODUCT_ID}=    Set Variable    ${response.json()['id']}
    Set Suite Variable    ${PRODUCT_ID}

Test Invalid Registration
    [Arguments]    ${email}    ${username}    ${password}    ${expected_error}
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...            email=${email}
    ...            username=${username}
    ...            password=${password}
    
    ${response}=   POST    /auth/register    json=${payload}    headers=${headers}
    
    Status Should Be    400    ${response}
    Should Contain      ${response.json()['message']}    ${expected_error}

Log Test Failure
    ${timestamp}=    Get Current Date    result_format=timestamp
    Log    Test failed at ${timestamp}
    Capture Page Screenshot    failure-${timestamp}.png
```

### Database Test Suite
```robot
*** Settings ***
Documentation    Database Test Suite
Library          DatabaseLibrary
Library          Collections
Library          OperatingSystem

Suite Setup      Database Setup
Suite Teardown   Database Teardown

*** Variables ***
${DB_HOST}       localhost
${DB_PORT}       5432
${DB_NAME}       test_db
${DB_USER}       postgres
${DB_PASSWORD}   postgres

*** Test Cases ***

Database Connection Test
    [Documentation]    Test database connection
    [Tags]             database    smoke
    Row Count Is Equal    SELECT 1    1

Table Structure Test
    [Documentation]    Test table structure
    [Tags]             database    structure
    
    # Test users table
    Table Must Exist    users
    Check Column Exists    users    id
    Check Column Exists    users    email
    Check Column Exists    users    username
    Check Column Exists    users    password_hash
    Check Column Exists    users    is_active
    Check Column Exists    users    created_at
    
    # Test unique constraints
    ${result}=    Query    SELECT COUNT(*) FROM information_schema.table_constraints WHERE table_name = 'users' AND constraint_type = 'UNIQUE'
    Should Be Equal As Numbers    ${result[0][0]}    2    # email and username should be unique

Data Integrity Test
    [Documentation]    Test data integrity
    [Tags]             database    integrity
    
    # Insert test data
    Execute SQL String
    ...    INSERT INTO users (id, email, username, password_hash, is_active)
    ...    VALUES ('test-id-1', 'test1@example.com', 'testuser1', 'hashed', true)
    
    # Test foreign key constraints
    Run Keyword And Expect Error    *foreign key constraint*
    ...    Execute SQL String
    ...    INSERT INTO audit_logs (id, user_id, action)
    ...    VALUES ('audit-1', 'non-existent-user', 'TEST')

Transaction Test
    [Documentation]    Test database transactions
    [Tags]             database    transactions
    
    Execute SQL String    BEGIN
    
    # Insert multiple records
    Execute SQL String
    ...    INSERT INTO users (id, email, username, password_hash, is_active)
    ...    VALUES ('tx-test-1', 'tx1@example.com', 'txuser1', 'hashed', true)
    
    Execute SQL String
    ...    INSERT INTO users (id, email, username, password_hash, is_active)
    ...    VALUES ('tx-test-2', 'tx2@example.com', 'txuser2', 'hashed', true)
    
    # Rollback transaction
    Execute SQL String    ROLLBACK
    
    # Verify records were not saved
    ${count}=    Row Count    SELECT * FROM users WHERE id LIKE 'tx-test-%'
    Should Be Equal As Numbers    ${count}    0

Index Performance Test
    [Documentation]    Test index performance
    [Tags]             database    performance
    
    # Check if indexes exist
    ${indexes}=    Query
    ...    SELECT indexname FROM pg_indexes WHERE tablename = 'users'
    
    Log    Found indexes: ${indexes}
    
    # Verify critical indexes
    ${has_email_idx}=    Evaluate    any('idx_user_email' in str(row) for row in ${indexes})
    Should Be True    ${has_email_idx}

Backup and Restore Test
    [Documentation]    Test backup and restore
    [Tags]             database    backup
    
    # Create backup
    ${backup_file}=    Set Variable    /tmp/test_backup.sql
    ${result}=         Run Process    pg_dump
    ...    -h    ${DB_HOST}
    ...    -p    ${DB_PORT}
    ...    -U    ${DB_USER}
    ...    -d    ${DB_NAME}
    ...    -f    ${backup_file}
    ...    env:PGPASSWORD=${DB_PASSWORD}
    
    Should Be Equal As Integers    ${result.rc}    0
    
    # Restore from backup
    ${result}=         Run Process    psql
    ...    -h    ${DB_HOST}
    ...    -p    ${DB_PORT}
    ...    -U    ${DB_USER}
    ...    -d    test_restore
    ...    -f    ${backup_file}
    ...    env:PGPASSWORD=${DB_PASSWORD}
    
    Should Be Equal As Integers    ${result.rc}    0

*** Keywords ***

Database Setup
    Connect To Database    psycopg2
    ...    ${DB_NAME}
    ...    ${DB_USER}
    ...    ${DB_PASSWORD}
    ...    ${DB_HOST}
    ...    ${DB_PORT}
    
    # Clean up test data
    Execute SQL String    DELETE FROM audit_logs WHERE user_id LIKE 'test-%'
    Execute SQL String    DELETE FROM users WHERE id LIKE 'test-%'

Database Teardown
    Disconnect From Database

Check Column Exists
    [Arguments]    ${table}    ${column}
    ${result}=    Query
    ...    SELECT column_name FROM information_schema.columns WHERE table_name = '${table}' AND column_name = '${column}'
    Should Not Be Empty    ${result}
```

### Performance Test Suite
```robot
*** Settings ***
Documentation    Performance Test Suite
Library          RequestsLibrary
Library          Collections
Library          DateTime
Library          Process
Library          String

Suite Setup      Performance Suite Setup
Suite Teardown   Performance Suite Teardown

*** Variables ***
${BASE_URL}      http://localhost:3000/api
${CONCURRENT_USERS}    10
${REQUESTS_PER_USER}   100
${TOTAL_REQUESTS}      ${${CONCURRENT_USERS} * ${REQUESTS_PER_USER}}

*** Test Cases ***

Load Test API Endpoints
    [Documentation]    Load test critical API endpoints
    [Tags]             performance    load
    
    @{endpoints}=    Create List
    ...    /products
    ...    /users
    ...    /auth/login
    ...    /orders
    
    FOR    ${endpoint}    IN    @{endpoints}
        ${result}=    Run Load Test    ${endpoint}    GET
        Log    ${endpoint}: ${result['rps']:.2f} RPS, ${result['avg_response_time']:.2f}ms avg
        Should Be True    ${result['rps']} > 50
        Should Be True    ${result['avg_response_time']} < 1000
    END

Stress Test User Registration
    [Documentation]    Stress test user registration endpoint
    [Tags]             performance    stress
    
    ${start_time}=    Get Current Date
    
    FOR    ${i}    IN RANGE    50
        ${email}=    Generate Random String    8    [LOWER]
        ${email}=    Catenate    SEPARATOR=@    ${email}    example.com
        ${payload}=    Create Dictionary
        ...    email=${email}
        ...    username=user${i}
        ...    password=Test123!
        
        POST    ${BASE_URL}/auth/register    json=${payload}
        ...    expected_status=201
    END
    
    ${end_time}=    Get Current Date
    ${duration}=    Subtract Date From Date    ${end_time}    ${start_time}
    ${rps}=    Evaluate    50 / ${duration}
    
    Log    User registration: ${rps:.2f} RPS
    Should Be True    ${rps} > 5

Concurrent Order Processing Test
    [Documentation]    Test concurrent order processing
    [Tags]             performance    concurrency
    
    @{processes}=    Create List
    
    FOR    ${i}    IN RANGE    ${CONCURRENT_USERS}
        ${process}=    Start Process    python    scripts/concurrent_orders.py    ${i}
        Append To List    ${processes}    ${process}
    END
    
    # Wait for all processes to complete
    FOR    ${process}    IN    @{processes}
        ${result}=    Wait For Process    ${process}
        Log    Process ${result.pid} completed with RC ${result.rc}
        Should Be Equal As Integers    ${result.rc}    0
    END

Database Query Performance Test
    [Documentation]    Test database query performance
    [Tags]             performance    database
    
    ${start_time}=    Get Current Date
    
    FOR    ${i}    IN RANGE    1000
        ${response}=    GET    ${BASE_URL}/products
        Status Should Be    200    ${response}
    END
    
    ${end_time}=    Get Current Date
    ${duration}=    Subtract Date From Date    ${end_time}    ${start_time}
    ${qps}=    Evaluate    1000 / ${duration}
    
    Log    Database queries: ${qps:.2f} QPS
    Should Be True    ${qps} > 100

Memory Usage Test
    [Documentation]    Test memory usage under load
    [Tags]             performance    memory
    
    ${before_memory}=    Get Memory Usage
    
    # Generate load
    FOR    ${i}    IN RANGE    10000
        GET    ${BASE_URL}/products    expected_status=200
    END
    
    ${after_memory}=    Get Memory Usage
    ${memory_increase}=    Evaluate    ${after_memory} - ${before_memory}
    
    Log    Memory increase: ${memory_increase:.2f} MB
    Should Be True    ${memory_increase} < 100

Response Time Percentile Test
    [Documentation]    Test response time percentiles
    [Tags]             performance    percentile
    
    @{response_times}=    Create List
    
    FOR    ${i}    IN RANGE    1000
        ${start}=    Get Current Date
        GET    ${BASE_URL}/products    expected_status=200
        ${end}=    Get Current Date
        ${duration}=    Subtract Date From Date    ${end}    ${start}
        ${ms}=    Evaluate    ${duration} * 1000
        Append To List    ${response_times}    ${ms}
    END
    
    Sort List    ${response_times}
    
    ${p50}=    Evaluate    ${response_times}[499]
    ${p95}=    Evaluate    ${response_times}[949]
    ${p99}=    Evaluate    ${response_times}[989]
    
    Log    P50: ${p50:.2f}ms, P95: ${p95:.2f}ms, P99: ${p99:.2f}ms
    Should Be True    ${p95} < 500
    Should Be True    ${p99} < 1000

*** Keywords ***

Performance Suite Setup
    Create Session    api    ${BASE_URL}
    ${access_token}=    Get Auth Token
    Set Suite Variable    ${ACCESS_TOKEN}    ${access_token}

Performance Suite Teardown
    Delete All Sessions

Run Load Test
    [Arguments]    ${endpoint}    ${method}
    ${start_time}=    Get Current Date
    
    FOR    ${i}    IN RANGE    ${TOTAL_REQUESTS}
        Run Keyword If    '${method}' == 'GET'
        ...    GET    ${endpoint}
        ...    ELSE IF    '${method}' == 'POST'
        ...    POST    ${endpoint}    json={}
    END
    
    ${end_time}=    Get Current Date
    ${duration}=    Subtract Date From Date    ${end_time}    ${start_time}
    ${rps}=    Evaluate    ${TOTAL_REQUESTS} / ${duration}
    
    [Return]    {'rps': ${rps}, 'duration': ${duration}}

Get Memory Usage
    ${result}=    Run Process    ps    -o    rss=    -p    ${PID}
    ${memory_kb}=    Convert To Number    ${result.stdout.strip()}
    ${memory_mb}=    Evaluate    ${memory_kb} / 1024
    [Return]    ${memory_mb}

Get Auth Token
    ${payload}=    Create Dictionary
    ...    email=admin@example.com
    ...    password=Admin123!
    
    ${response}=    POST    /auth/login    json=${payload}
    [Return]    ${response.json()['accessToken']}
```

## 9. Docker Compose - Multi-service Setup, Networking, Volumes, Health Checks

### Complete docker-compose.yml
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    container_name: microservices-postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-microservices}
      POSTGRES_USER: ${POSTGRES_USER:-admin}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-admin123}
      POSTGRES_INITDB_ARGS: "--encoding=UTF-8 --locale=C"
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./postgres/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./postgres/backup:/backup
    ports:
      - "${POSTGRES_PORT:-5432}:5432"
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    restart: unless-stopped
    deploy:
      resources:
        limits:
          memory: 1G
          cpus: '1.0'
        reservations:
          memory: 512M
          cpus: '0.5'

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: microservices-redis
    command: >
      redis-server
      --appendonly yes
      --requirepass ${REDIS_PASSWORD:-redis123}
      --maxmemory 512mb
      --maxmemory-policy allkeys-lru
    volumes:
      - redis_data:/data
      - ./redis/redis.conf:/usr/local/etc/redis/redis.conf
    ports:
      - "${REDIS_PORT:-6379}:6379"
    networks:
      - backend
    healthcheck:
      test: ["CMD", "redis-cli", "--raw", "incr", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    deploy:
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M

  # Kafka Cluster
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    container_name: microservices-zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SYNC_LIMIT: 2
    ports:
      - "2181:2181"
    networks:
      - backend
    healthcheck:
      test: ["CMD", "bash", "-c", "echo ruok | nc localhost 2181"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped

  kafka:
    image: confluentinc/cp-kafka:7.4.0
    container_name: microservices-kafka
    depends_on:
      zookeeper:
        condition: service_healthy
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "true"
    ports:
      - "9092:9092"
    volumes:
      - kafka_data:/var/lib/kafka/data
    networks:
      - backend
    healthcheck:
      test: ["CMD", "kafka-topics", "--list", "--bootstrap-server", "localhost:9092"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 60s
    restart: unless-stopped

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    container_name: microservices-kafka-ui
    depends_on:
      kafka:
        condition: service_started
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
      DYNAMIC_CONFIG_ENABLED: "true"
      KAFKA_CLUSTERS_0_PROPERTIES_SECURITY_PROTOCOL: PLAINTEXT
    ports:
      - "8080:8080"
    networks:
      - backend
    restart: unless-stopped

  # Elasticsearch Stack
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.0
    container_name: microservices-elasticsearch
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
      - cluster.name=es-microservices
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9200/_cluster/health"]
      interval: 30s
      timeout: 10s
      retries: 5
    restart: unless-stopped
    ulimits:
      memlock:
        soft: -1
        hard: -1

  logstash:
    image: docker.elastic.co/logstash/logstash:8.10.0
    container_name: microservices-logstash
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/logs:/usr/share/logstash/logs
    ports:
      - "5000:5000"
      - "9600:9600"
    networks:
      - backend
    depends_on:
      elasticsearch:
        condition: service_healthy
    restart: unless-stopped

  kibana:
    image: docker.elastic.co/kibana/kibana:8.10.0
    container_name: microservices-kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    networks:
      - backend
    depends_on:
      elasticsearch:
        condition: service_healthy
    restart: unless-stopped

  # Monitoring Stack
  prometheus:
    image: prom/prometheus:latest
    container_name: microservices-prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/rules:/etc/prometheus/rules
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--storage.tsdb.retention.time=200h'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"
    networks:
      - backend
    restart: unless-stopped

  grafana:
    image: grafana/grafana:10.0.0
    container_name: microservices-grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_ADMIN_PASSWORD:-admin123}
      - GF_INSTALL_PLUGINS=grafana-piechart-panel,grafana-clock-panel
      - GF_FEATURE_TOGGLES_ENABLE=publicDashboards
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
      - ./grafana/dashboards:/var/lib/grafana/dashboards
    ports:
      - "3000:3000"
    networks:
      - backend
    restart: unless-stopped

  # n8n Workflow Automation
  n8n:
    image: n8nio/n8n
    container_name: microservices-n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER:-admin}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD:-admin123}
      - N8N_HOST=${N8N_HOST:-localhost}
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - N8N_WEBHOOK_URL=${N8N_WEBHOOK_URL}
      - GENERIC_TIMEZONE=Asia/Bangkok
      - N8N_USER_MANAGEMENT_DISABLED=false
      - N8N_DIAGNOSTICS_ENABLED=false
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=${POSTGRES_DB}
      - DB_POSTGRESDB_USER=${POSTGRES_USER}
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n
    ports:
      - "5678:5678"
    networks:
      - backend
    depends_on:
      postgres:
        condition: service_healthy
    restart: unless-stopped

  # Backend API Service
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile
      args:
        - NODE_ENV=production
    image: microservices-backend:latest
    container_name: microservices-api
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://:${REDIS_PASSWORD}@redis:6379
      - KAFKA_BROKER=kafka:9092
      - ELASTICSEARCH_HOST=http://elasticsearch:9200
      - JWT_ACCESS_SECRET=${JWT_ACCESS_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
      - LOG_LEVEL=info
      - PORT=3001
    volumes:
      - ./backend:/app
      - /app/node_modules
      - ./logs:/app/logs
    ports:
      - "3001:3001"
    networks:
      - backend
      - frontend
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      kafka:
        condition: service_started
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    restart: unless-stopped
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first
      resources:
        limits:
          memory: 1G
          cpus: '1.0'
        reservations:
          memory: 512M
          cpus: '0.5'

  # Frontend Application
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      args:
        - NEXT_PUBLIC_API_URL=http://api:3001
    image: microservices-frontend:latest
    container_name: microservices-frontend
    environment:
      - NEXT_PUBLIC_API_URL=http://api:3001
      - NODE_ENV=production
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next
    ports:
      - "3000:3000"
    networks:
      - frontend
    depends_on:
      - api
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: unless-stopped
    deploy:
      replicas: 2
      update_config:
        parallelism: 1
        delay: 10s

  # Nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    container_name: microservices-nginx
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/ssl:/etc/nginx/ssl
      - ./nginx/logs:/var/log/nginx
    ports:
      - "80:80"
      - "443:443"
    networks:
      - frontend
      - backend
    depends_on:
      - frontend
      - api
    restart: unless-stopped

  # Portainer for container management
  portainer:
    image: portainer/portainer-ce:latest
    container_name: microservices-portainer
    command: -H unix:///var/run/docker.sock
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    ports:
      - "9000:9000"
    networks:
      - backend

  # Watchtower for automatic updates
  watchtower:
    image: containrrr/watchtower
    container_name: microservices-watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - WATCHTOWER_CLEANUP=true
      - WATCHTOWER_POLL_INTERVAL=300
      - WATCHTOWER_LABEL_ENABLE=true
    command: --interval 30
    restart: unless-stopped

  # Backup service
  backup:
    image: prodrigestivill/postgres-backup-local
    container_name: microservices-backup
    volumes:
      - ./backup:/backups
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - POSTGRES_HOST=postgres
      - POSTGRES_DB=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_EXTRA_OPTS=-Z9 --schema=public --blobs
      - SCHEDULE=@daily
      - BACKUP_KEEP_DAYS=7
      - BACKUP_KEEP_WEEKS=4
      - BACKUP_KEEP_MONTHS=6
      - HEALTHCHECK_PORT=8080
    networks:
      - backend
    depends_on:
      postgres:
        condition: service_healthy
    restart: unless-stopped

networks:
  backend:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
  frontend:
    driver: bridge
    ipam:
      config:
        - subnet: 172.21.0.0/16

volumes:
  postgres_data:
    driver: local
    driver_opts:
      type: none
      device: ${PWD}/data/postgres
      o: bind
  redis_data:
    driver: local
    driver_opts:
      type: none
      device: ${PWD}/data/redis
      o: bind
  kafka_data:
    driver: local
  elasticsearch_data:
    driver: local
    driver_opts:
      type: none
      device: ${PWD}/data/elasticsearch
      o: bind
  grafana_data:
    driver: local
  prometheus_data:
    driver: local
  n8n_data:
    driver: local
  portainer_data:
    driver: local
```

### Nginx Configuration
```nginx
# nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Log format
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for" '
                    '$request_time $upstream_response_time';

    access_log /var/log/nginx/access.log main;

    # Basic settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 100M;

    # Gzip compression
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:;" always;

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=api:10m rate=100r/m;
    limit_req_zone $binary_remote_addr zone=auth:10m rate=10r/m;

    # Load balancing
    upstream backend {
        least_conn;
        server api:3001 max_fails=3 fail_timeout=30s;
        keepalive 32;
    }

    upstream frontend {
        server frontend:3000;
        keepalive 32;
    }

    # HTTP server (redirect to HTTPS)
    server {
        listen 80;
        server_name _;
        return 301 https://$host$request_uri;
    }

    # HTTPS server
    server {
        listen 443 ssl http2;
        server_name your-domain.com;

        # SSL configuration
        ssl_certificate /etc/nginx/ssl/certificate.crt;
        ssl_certificate_key /etc/nginx/ssl/private.key;
        
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;
        
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;
        ssl_session_tickets off;

        # Frontend
        location / {
            proxy_pass http://frontend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
            proxy_buffering off;
            proxy_read_timeout 300s;
        }

        # API Gateway
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
            
            # Timeouts
            proxy_connect_timeout 30s;
            proxy_send_timeout 30s;
            proxy_read_timeout 30s;
            
            # Buffering
            proxy_buffering on;
            proxy_buffer_size 4k;
            proxy_buffers 8 4k;
            proxy_busy_buffers_size 8k;
        }

        # Authentication endpoints
        location /api/auth/ {
            limit_req zone=auth burst=5 nodelay;
            
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Health check endpoint
        location /health {
            access_log off;
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
        }

        # WebSocket support
        location /ws/ {
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_read_timeout 86400s;
            proxy_send_timeout 86400s;
        }

        # Static files
        location /static/ {
            alias /app/static/;
            expires 1y;
            add_header Cache-Control "public, immutable";
            access_log off;
        }

        # Deny access to hidden files
        location ~ /\. {
            deny all;
            access_log off;
            log_not_found off;
        }
    }
}
```

## 10. Security & Performance - Input Validation, SQL Injection Protection, Caching, Monitoring

### Security Configuration
```typescript
// src/config/security.config.ts
import { ConfigService } from '@nestjs/config';
import { ThrottlerModuleOptions } from '@nestjs/throttler';
import { HelmetOptions } from 'helmet';

export const securityConfig = {
  // Helmet security headers
  helmet: (configService: ConfigService): HelmetOptions => ({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'", "'unsafe-inline'", "'unsafe-eval'"],
        imgSrc: ["'self'", "data:", "https:"],
        connectSrc: [
          "'self'",
          configService.get('APP_URL'),
          configService.get('API_URL'),
        ],
        fontSrc: ["'self'", "https:", "data:"],
        objectSrc: ["'none'"],
        mediaSrc: ["'self'"],
        frameSrc: ["'none'"],
        formAction: ["'self'"],
        baseUri: ["'self'"],
      },
    },
    hsts: {
      maxAge: 31536000, // 1 year
      includeSubDomains: true,
      preload: true,
    },
    frameguard: {
      action: 'deny',
    },
    hidePoweredBy: true,
    noSniff: true,
    xssFilter: true,
  }),

  // Rate limiting
  throttler: (configService: ConfigService): ThrottlerModuleOptions => ({
    throttlers: [
      {
        ttl: configService.get('THROTTLE_TTL', 60),
        limit: configService.get('THROTTLE_LIMIT', 100),
      },
    ],
  }),

  // CORS configuration
  cors: (configService: ConfigService) => ({
    origin: configService.get('CORS_ORIGIN', '*').split(','),
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH', 'OPTIONS'],
    allowedHeaders: [
      'Content-Type',
      'Authorization',
      'X-Requested-With',
      'X-Request-ID',
    ],
    exposedHeaders: ['X-RateLimit-Limit', 'X-RateLimit-Remaining'],
    credentials: true,
    maxAge: 86400, // 24 hours
  }),

  // Input validation
  validation: {
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
    transformOptions: {
      enableImplicitConversion: true,
    },
    validationError: {
      target: false,
      value: false,
    },
  },
};
```

### Input Validation and Sanitization
```typescript
// src/common/pipes/validation.pipe.ts
import {
  PipeTransform,
  Injectable,
  ArgumentMetadata,
  BadRequestException,
} from '@nestjs/common';
import { validate } from 'class-validator';
import { plainToInstance } from 'class-transformer';
import sanitizeHtml from 'sanitize-html';

@Injectable()
export class ValidationPipe implements PipeTransform<any> {
  async transform(value: any, { metatype }: ArgumentMetadata) {
    if (!metatype || !this.toValidate(metatype)) {
      return value;
    }

    // Sanitize input
    value = this.sanitizeInput(value);

    const object = plainToInstance(metatype, value);
    const errors = await validate(object, {
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
    });

    if (errors.length > 0) {
      const formattedErrors = this.formatErrors(errors);
      throw new BadRequestException({
        message: 'Validation failed',
        errors: formattedErrors,
      });
    }

    return object;
  }

  private toValidate(metatype: Function): boolean {
    const types: Function[] = [String, Boolean, Number, Array, Object];
    return !types.includes(metatype);
  }

  private sanitizeInput(value: any): any {
    if (typeof value === 'string') {
      return sanitizeHtml(value.trim(), {
        allowedTags: [],
        allowedAttributes: {},
      });
    }

    if (Array.isArray(value)) {
      return value.map((item) => this.sanitizeInput(item));
    }

    if (value && typeof value === 'object') {
      const sanitized = {};
      for (const key in value) {
        sanitized[key] = this.sanitizeInput(value[key]);
      }
      return sanitized;
    }

    return value;
  }

  private formatErrors(errors: any[]): any[] {
    return errors.map((error) => ({
      property: error.property,
      constraints: error.constraints,
      children: error.children ? this.formatErrors(error.children) : [],
    }));
  }
}

// DTO with validation
import {
  IsEmail,
  IsNotEmpty,
  MinLength,
  MaxLength,
  Matches,
  IsOptional,
  IsEnum,
  IsNumber,
  IsPositive,
  IsUUID,
} from 'class-validator';
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';

export class CreateUserDto {
  @ApiProperty({ example: 'user@example.com' })
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @ApiProperty({ example: 'john_doe' })
  @IsNotEmpty()
  @MinLength(3)
  @MaxLength(50)
  @Matches(/^[a-zA-Z0-9_]+$/, {
    message: 'Username can only contain letters, numbers and underscores',
  })
  username: string;

  @ApiProperty({ example: 'Password123!' })
  @IsNotEmpty()
  @MinLength(8)
  @MaxLength(100)
  @Matches(/(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/, {
    message:
      'Password must contain at least one uppercase letter, one lowercase letter, one number and one special character',
  })
  password: string;

  @ApiPropertyOptional({ example: 'John' })
  @IsOptional()
  @MaxLength(100)
  firstName?: string;

  @ApiPropertyOptional({ example: 'Doe' })
  @IsOptional()
  @MaxLength(100)
  lastName?: string;

  @ApiPropertyOptional({ example: '+1234567890' })
  @IsOptional()
  @Matches(/^\+?[1-9]\d{1,14}$/, {
    message: 'Phone number must be in E.164 format',
  })
  phoneNumber?: string;
}

export class UpdateProductDto {
  @ApiPropertyOptional({ example: 'Updated Product Name' })
  @IsOptional()
  @MaxLength(255)
  name?: string;

  @ApiPropertyOptional({ example: 'Updated product description' })
  @IsOptional()
  @MaxLength(2000)
  description?: string;

  @ApiPropertyOptional({ example: 99.99 })
  @IsOptional()
  @IsNumber()
  @IsPositive()
  price?: number;

  @ApiPropertyOptional({ example: 'Electronics' })
  @IsOptional()
  @MaxLength(100)
  category?: string;
}
```

### SQL Injection Protection
```typescript
// src/common/utils/sql-injection.utils.ts
import { Logger } from '@nestjs/common';

export class SqlInjectionUtils {
  private static readonly logger = new Logger(SqlInjectionUtils.name);

  static sanitizeInput(input: string): string {
    if (!input) return input;

    // Remove SQL keywords
    const sqlKeywords = [
      'SELECT',
      'INSERT',
      'UPDATE',
      'DELETE',
      'DROP',
      'UNION',
      'OR',
      'AND',
      'WHERE',
      'FROM',
      'JOIN',
      'HAVING',
      'GROUP BY',
      'ORDER BY',
      'LIMIT',
      'OFFSET',
    ];

    let sanitized = input;
    sqlKeywords.forEach((keyword) => {
      const regex = new RegExp(`\\b${keyword}\\b`, 'gi');
      sanitized = sanitized.replace(regex, '');
    });

    // Remove special characters that could be used in SQL injection
    sanitized = sanitized.replace(/[;'"\\\-\-]/g, '');

    // Log suspicious input
    if (sanitized !== input) {
      this.logger.warn(`SQL injection attempt detected: ${input}`);
    }

    return sanitized.trim();
  }

  static validateInput(input: string, maxLength: number = 255): boolean {
    if (!input) return true;

    // Check length
    if (input.length > maxLength) {
      return false;
    }

    // Check for SQL injection patterns
    const sqlPatterns = [
      /(\b(SELECT|INSERT|UPDATE|DELETE|DROP|UNION)\b)/i,
      /(\b(OR|AND)\b\s+\d+\s*=\s*\d+)/i,
      /(['"]\s*(\+\s*)?\d+)/i,
      /(--|\/\*|\*\/)/,
      /(EXEC\s*\(|EXECUTE\s*\(|sp_)/i,
    ];

    for (const pattern of sqlPatterns) {
      if (pattern.test(input)) {
        this.logger.warn(`SQL injection pattern detected: ${input}`);
        return false;
      }
    }

    return true;
  }

  static escapeLikeString(input: string): string {
    if (!input) return input;
    
    // Escape special characters for LIKE clause
    return input.replace(/[%_]/g, '\\$&');
  }
}

// Secure repository methods
import { Repository, QueryRunner } from 'typeorm';
import { SqlInjectionUtils } from '../utils/sql-injection.utils';

export class SecureRepository<T> extends Repository<T> {
  async findSafe(
    conditions: any,
    options?: any,
  ): Promise<T[]> {
    // Sanitize input conditions
    const sanitizedConditions = this.sanitizeConditions(conditions);
    
    return await this.find({
      where: sanitizedConditions,
      ...options,
    });
  }

  async findOneSafe(
    conditions: any,
    options?: any,
  ): Promise<T | null> {
    const sanitizedConditions = this.sanitizeConditions(conditions);
    
    return await this.findOne({
      where: sanitizedConditions,
      ...options,
    });
  }

  private sanitizeConditions(conditions: any): any {
    if (typeof conditions === 'string') {
      return SqlInjectionUtils.sanitizeInput(conditions);
    }

    if (Array.isArray(conditions)) {
      return conditions.map((condition) => this.sanitizeConditions(condition));
    }

    if (conditions && typeof conditions === 'object') {
      const sanitized = {};
      for (const key in conditions) {
        if (typeof conditions[key] === 'string') {
          sanitized[key] = SqlInjectionUtils.sanitizeInput(conditions[key]);
        } else {
          sanitized[key] = conditions[key];
        }
      }
      return sanitized;
    }

    return conditions;
  }

  async executeSafeQuery(
    query: string,
    parameters: any[] = [],
  ): Promise<any> {
    // Validate query
    if (!this.isSafeQuery(query)) {
      throw new Error('Potentially unsafe query detected');
    }

    // Sanitize parameters
    const sanitizedParameters = parameters.map((param) => {
      if (typeof param === 'string') {
        return SqlInjectionUtils.sanitizeInput(param);
      }
      return param;
    });

    return await this.query(query, sanitizedParameters);
  }

  private isSafeQuery(query: string): boolean {
    const unsafePatterns = [
      /(\bDROP\s+\b(TABLE|DATABASE)\b)/i,
      /(\bTRUNCATE\s+\bTABLE\b)/i,
      /(\bDELETE\s+FROM\s+\w+\s+WHERE\s+1\s*=\s*1)/i,
      /(;\s*--)/,
      /(EXEC\s*\(|EXECUTE\s*\(|sp_)/i,
    ];

    for (const pattern of unsafePatterns) {
      if (pattern.test(query)) {
        return false;
      }
    }

    return true;
  }
}
```

### Caching Strategy
```typescript
// src/modules/caching/services/cache.service.ts
import { Injectable, Inject, Logger } from '@nestjs/common';
import { CACHE_MANAGER } from '@nestjs/cache-manager';
import { Cache } from 'cache-manager';

export interface CacheOptions {
  ttl?: number;
  tags?: string[];
  staleWhileRevalidate?: number;
  forceRefresh?: boolean;
}

@Injectable()
export class CacheService {
  private readonly logger = new Logger(CacheService.name);
  private readonly defaultTTL = 3600; // 1 hour

  constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}

  async get<T>(key: string): Promise<T | null> {
    try {
      const value = await this.cacheManager.get<T>(key);
      
      if (value && this.isStale(value)) {
        // Return stale data but refresh in background
        this.refreshInBackground(key);
        return value.data;
      }
      
      return value ? value.data : null;
    } catch (error) {
      this.logger.error(`Error getting cache key ${key}: ${error.message}`);
      return null;
    }
  }

  async set<T>(
    key: string,
    value: T,
    options: CacheOptions = {},
  ): Promise<void> {
    try {
      const { ttl = this.defaultTTL, tags = [] } = options;
      
      const cacheEntry = {
        data: value,
        timestamp: Date.now(),
        ttl: ttl * 1000, // Convert to milliseconds
        tags,
      };

      await this.cacheManager.set(key, cacheEntry, ttl * 1000);

      // Store tag relationships
      if (tags.length > 0) {
        await this.addToTags(key, tags);
      }
    } catch (error) {
      this.logger.error(`Error setting cache key ${key}: ${error.message}`);
    }
  }

  async delete(key: string): Promise<void> {
    try {
      await this.cacheManager.del(key);
    } catch (error) {
      this.logger.error(`Error deleting cache key ${key}: ${error.message}`);
    }
  }

  async deleteByPattern(pattern: string): Promise<void> {
    try {
      const keys = await this.getKeys(pattern);
      
      for (const key of keys) {
        await this.cacheManager.del(key);
      }
    } catch (error) {
      this.logger.error(
        `Error deleting cache by pattern ${pattern}: ${error.message}`,
      );
    }
  }

  async invalidateTags(tags: string[]): Promise<void> {
    try {
      for (const tag of tags) {
        const tagKey = `tag:${tag}`;
        const keys = await this.cacheManager.get<string[]>(tagKey);
        
        if (keys && keys.length > 0) {
          // Delete all keys associated with this tag
          for (const key of keys) {
            await this.cacheManager.del(key);
          }
          
          // Clear the tag
          await this.cacheManager.del(tagKey);
        }
      }
    } catch (error) {
      this.logger.error(
        `Error invalidating cache tags ${tags}: ${error.message}`,
      );
    }
  }

  async cacheWithFallback<T>(
    key: string,
    fetcher: () => Promise<T>,
    options: CacheOptions = {},
  ): Promise<T> {
    const { forceRefresh = false, staleWhileRevalidate = 600 } = options;

    // Try to get from cache first (unless force refresh)
    if (!forceRefresh) {
      const cached = await this.get<T>(key);
      if (cached !== null) {
        return cached;
      }
    }

    // Fetch fresh data
    const data = await fetcher();
    
    // Store in cache
    await this.set(key, data, options);
    
    return data;
  }

  async getOrSet<T>(
    key: string,
    fetcher: () => Promise<T>,
    options: CacheOptions = {},
  ): Promise<T> {
    return this.cacheWithFallback(key, fetcher, options);
  }

  async clearAll(): Promise<void> {
    try {
      await this.cacheManager.reset();
    } catch (error) {
      this.logger.error(`Error clearing cache: ${error.message}`);
    }
  }

  async getStats(): Promise<any> {
    // Implementation would depend on the cache store
    // For Redis, you might get memory usage, keys count, etc.
    return {
      store: 'memory', // or 'redis'
      size: 'unknown',
      keys: 0,
    };
  }

  private async addToTags(key: string, tags: string[]): Promise<void> {
    for (const tag of tags) {
      const tagKey = `tag:${tag}`;
      const currentKeys = (await this.cacheManager.get<string[]>(tagKey)) || [];
      
      if (!currentKeys.includes(key)) {
        await this.cacheManager.set(tagKey, [...currentKeys, key], 0);
      }
    }
  }

  private isStale(cacheEntry: any): boolean {
    if (!cacheEntry || !cacheEntry.timestamp || !cacheEntry.ttl) {
      return false;
    }

    const age = Date.now() - cacheEntry.timestamp;
    const staleThreshold = cacheEntry.ttl * 0.8; // Consider stale after 80% of TTL

    return age > staleThreshold;
  }

  private async refreshInBackground(key: string): Promise<void> {
    // This would trigger a background refresh of the cache
    // Implementation depends on your application logic
    this.logger.debug(`Background refresh triggered for key: ${key}`);
  }

  private async getKeys(pattern: string): Promise<string[]> {
    // Implementation depends on the cache store
    // Redis supports keys pattern matching
    return [];
  }
}
```

### Monitoring Service
```typescript
// src/modules/monitoring/services/monitoring.service.ts
import { Injectable, Logger } from '@nestjs/common';
import { HttpService } from '@nestjs/axios';
import { ConfigService } from '@nestjs/config';
import { EventEmitter2 } from '@nestjs/event-emitter';
import { firstValueFrom } from 'rxjs';

export interface ServiceHealth {
  name: string;
  status: 'healthy' | 'unhealthy' | 'degraded';
  responseTime: number;
  timestamp: Date;
  error?: string;
}

export interface SystemMetrics {
  timestamp: Date;
  cpu: {
    usage: number;
    loadAverage: number[];
  };
  memory: {
    total: number;
    used: number;
    free: number;
    usage: number;
  };
  disk: {
    total: number;
    used: number;
    free: number;
    usage: number;
  };
  network: {
    bytesIn: number;
    bytesOut: number;
    connections: number;
  };
}

@Injectable()
export class MonitoringService {
  private readonly logger = new Logger(MonitoringService.name);
  private metrics: SystemMetrics[] = [];
  private readonly MAX_METRICS_HISTORY = 1000;

  constructor(
    private readonly configService: ConfigService,
    private readonly httpService: HttpService,
    private readonly eventEmitter: EventEmitter2,
  ) {
    // Start monitoring
    this.startMonitoring();
  }

  private startMonitoring(): void {
    // Collect metrics every minute
    setInterval(() => {
      this.collectMetrics().catch((error) => {
        this.logger.error(`Error collecting metrics: ${error.message}`);
      });
    }, 60000);

    // Check service health every 30 seconds
    setInterval(() => {
      this.checkServiceHealth().catch((error) => {
        this.logger.error(`Error checking service health: ${error.message}`);
      });
    }, 30000);
  }

  async collectMetrics(): Promise<SystemMetrics> {
    const metrics: SystemMetrics = {
      timestamp: new Date(),
      cpu: await this.getCpuMetrics(),
      memory: await this.getMemoryMetrics(),
      disk: await this.getDiskMetrics(),
      network: await this.getNetworkMetrics(),
    };

    // Store metrics
    this.metrics.push(metrics);
    
    if (this.metrics.length > this.MAX_METRICS_HISTORY) {
      this.metrics = this.metrics.slice(-this.MAX_METRICS_HISTORY);
    }

    // Emit metrics event
    this.eventEmitter.emit('metrics.collected', metrics);

    // Check thresholds
    await this.checkThresholds(metrics);

    return metrics;
  }

  async checkServiceHealth(): Promise<ServiceHealth[]> {
    const services = [
      {
        name: 'API',
        url: `${this.configService.get('API_URL')}/health`,
        timeout: 5000,
      },
      {
        name: 'Database',
        url: `${this.configService.get('DATABASE_URL')}`,
        type: 'database',
      },
      {
        name: 'Redis',
        url: this.configService.get('REDIS_URL'),
        type: 'redis',
      },
      {
        name: 'Kafka',
        url: `${this.configService.get('KAFKA_BROKER')}`,
        type: 'kafka',
      },
      {
        name: 'Elasticsearch',
        url: `${this.configService.get('ELASTICSEARCH_HOST')}/_cluster/health`,
      },
    ];

    const healthChecks = await Promise.allSettled(
      services.map(async (service) => {
        const startTime = Date.now();
        
        try {
          let status: 'healthy' | 'unhealthy' = 'unhealthy';
          
          if (service.type === 'database') {
            // Check database connection
            status = 'healthy'; // Would actually check DB connection
          } else if (service.type === 'redis') {
            // Check Redis connection
            status = 'healthy'; // Would actually check Redis connection
          } else if (service.type === 'kafka') {
            // Check Kafka connection
            status = 'healthy'; // Would actually check Kafka connection
          } else {
            // HTTP health check
            const response = await firstValueFrom(
              this.httpService.get(service.url, {
                timeout: service.timeout,
              }),
            );
            
            status = response.status === 200 ? 'healthy' : 'unhealthy';
          }

          const responseTime = Date.now() - startTime;

          return {
            name: service.name,
            status,
            responseTime,
            timestamp: new Date(),
          };
        } catch (error) {
          return {
            name: service.name,
            status: 'unhealthy',
            responseTime: Date.now() - startTime,
            timestamp: new Date(),
            error: error.message,
          };
        }
      }),
    );

    const results = healthChecks.map((result) =>
      result.status === 'fulfilled' ? result.value : result.reason,
    );

    // Emit health check results
    this.eventEmitter.emit('health.check', results);

    // Alert on unhealthy services
    const unhealthy = results.filter((r) => r.status === 'unhealthy');
    if (unhealthy.length > 0) {
      await this.alertUnhealthyServices(unhealthy);
    }

    return results;
  }

  async getMetricsHistory(
    from?: Date,
    to?: Date,
    limit: number = 100,
  ): Promise<SystemMetrics[]> {
    let filtered = this.metrics;

    if (from) {
      filtered = filtered.filter((m) => m.timestamp >= from);
    }

    if (to) {
      filtered = filtered.filter((m) => m.timestamp <= to);
    }

    return filtered.slice(-limit);
  }

  async getAggregatedMetrics(
    interval: 'minute' | 'hour' | 'day' = 'hour',
  ): Promise<any> {
    const now = new Date();
    let startTime: Date;

    switch (interval) {
      case 'minute':
        startTime = new Date(now.getTime() - 60 * 60 * 1000); // 1 hour
        break;
      case 'hour':
        startTime = new Date(now.getTime() - 24 * 60 * 60 * 1000); // 24 hours
        break;
      case 'day':
        startTime = new Date(now.getTime() - 7 * 24 * 60 * 60 * 1000); // 7 days
        break;
    }

    const metrics = await this.getMetricsHistory(startTime, now);

    // Aggregate metrics
    const aggregated = {
      cpu: {
        avg: this.calculateAverage(metrics.map((m) => m.cpu.usage)),
        max: Math.max(...metrics.map((m) => m.cpu.usage)),
        min: Math.min(...metrics.map((m) => m.cpu.usage)),
      },
      memory: {
        avg: this.calculateAverage(metrics.map((m) => m.memory.usage)),
        max: Math.max(...metrics.map((m) => m.memory.usage)),
        min: Math.min(...metrics.map((m) => m.memory.usage)),
      },
      disk: {
        avg: this.calculateAverage(metrics.map((m) => m.disk.usage)),
        max: Math.max(...metrics.map((m) => m.disk.usage)),
        min: Math.min(...metrics.map((m) => m.disk.usage)),
      },
      count: metrics.length,
    };

    return aggregated;
  }

  private async checkThresholds(metrics: SystemMetrics): Promise<void> {
    const thresholds = {
      cpu: this.configService.get('CPU_THRESHOLD', 80),
      memory: this.configService.get('MEMORY_THRESHOLD', 80),
      disk: this.configService.get('DISK_THRESHOLD', 90),
    };

    const alerts = [];

    if (metrics.cpu.usage > thresholds.cpu) {
      alerts.push({
        type: 'HIGH_CPU_USAGE',
        severity: 'WARNING',
        value: metrics.cpu.usage,
        threshold: thresholds.cpu,
        timestamp: metrics.timestamp,
      });
    }

    if (metrics.memory.usage > thresholds.memory) {
      alerts.push({
        type: 'HIGH_MEMORY_USAGE',
        severity: 'WARNING',
        value: metrics.memory.usage,
        threshold: thresholds.memory,
        timestamp: metrics.timestamp,
      });
    }

    if (metrics.disk.usage > thresholds.disk) {
      alerts.push({
        type: 'HIGH_DISK_USAGE',
        severity: 'CRITICAL',
        value: metrics.disk.usage,
        threshold: thresholds.disk,
        timestamp: metrics.timestamp,
      });
    }

    if (alerts.length > 0) {
      await this.sendAlerts(alerts);
    }
  }

  private async sendAlerts(alerts: any[]): Promise<void> {
    for (const alert of alerts) {
      this.logger.warn(`Alert: ${alert.type} - ${alert.value}%`);
      
      // Emit alert event
      this.eventEmitter.emit('alert.triggered', alert);
      
      // Send to external alerting system (Slack, Email, etc.)
      await this.sendExternalAlert(alert);
    }
  }

  private async sendExternalAlert(alert: any): Promise<void> {
    // Implementation for sending alerts to external systems
    // This could be Slack, Email, PagerDuty, etc.
    const webhookUrl = this.configService.get('ALERT_WEBHOOK_URL');
    
    if (webhookUrl) {
      try {
        await firstValueFrom(
          this.httpService.post(webhookUrl, {
            text: `🚨 Alert: ${alert.type}\nSeverity: ${alert.severity}\nValue: ${alert.value}%\nThreshold: ${alert.threshold}%\nTime: ${alert.timestamp}`,
          }),
        );
      } catch (error) {
        this.logger.error(`Failed to send external alert: ${error.message}`);
      }
    }
  }

  private async alertUnhealthyServices(services: ServiceHealth[]): Promise<void> {
    const message = `Unhealthy services detected:\n${services
      .map((s) => `• ${s.name}: ${s.error || 'Unknown error'}`)
      .join('\n')}`;

    this.logger.error(message);

    // Send alert
    await this.sendExternalAlert({
      type: 'UNHEALTHY_SERVICES',
      severity: 'CRITICAL',
      message,
      timestamp: new Date(),
    });
  }

  private async getCpuMetrics(): Promise<any> {
    // Implementation would use os module or system commands
    const os = await import('os');
    
    return {
      usage: 0, // Would calculate actual CPU usage
      loadAverage: os.loadavg(),
    };
  }

  private async getMemoryMetrics(): Promise<any> {
    const os = await import('os');
    const total = os.totalmem();
    const free = os.freemem();
    const used = total - free;
    
    return {
      total,
      used,
      free,
      usage: (used / total) * 100,
    };
  }

  private async getDiskMetrics(): Promise<any> {
    // Implementation would use system commands or a library
    return {
      total: 0,
      used: 0,
      free: 0,
      usage: 0,
    };
  }

  private async getNetworkMetrics(): Promise<any> {
    // Implementation would use system commands
    return {
      bytesIn: 0,
      bytesOut: 0,
      connections: 0,
    };
  }

  private calculateAverage(numbers: number[]): number {
    if (numbers.length === 0) return 0;
    return numbers.reduce((sum, num) => sum + num, 0) / numbers.length;
  }
}cmoni
```


ทั้งหมดนี้คือตัวอย่างโค้ดที่ครอบคลุมทุกหัวข้อที่คุณต้องการ:

1. **NestJS Architecture** - โครงสร้างโมดูล, Dependency Injection, Middleware, Guards, Interceptors
2. **CRUD with TypeORM** - Entities, Repositories, Transactions, Relations, Query Builder
3. **JWT Authentication** - Access/Refresh Tokens, Token Rotation, Redis Storage, Axios Interceptors
4. **Microservices Architecture** - Service Breakdown, Kafka Events, gRPC, API Gateway
5. **Complete Systems** - User, Product, Inventory, Shipping, Reports, Audit Systems
6. **Technology Stack** - Redis, Kafka, ELK, Grafana, n8n Integration
7. **Git Flow & CI/CD** - Branch Strategy, GitHub Actions, Docker Builds, Deployment
8. **Robot Framework Testing** - API Tests, Database Tests, Performance Tests
9. **Docker Compose** - Multi-service Setup, Networking, Volumes, Health Checks
10. **Security & Performance** - Input Validation, SQL Injection Protection, Caching, Monitoring