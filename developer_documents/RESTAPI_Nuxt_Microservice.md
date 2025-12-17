
# Microservices REST API  Nuxt
- https://nestjs.com  backend
- https://nuxt.com/   fontend end
- https://react.dev/
- 
1. อธิบาย การทำงานของ nestjs วาด โครงสร้าง การทำงาน
2. อธิบาย การทำงานของ nuxt วาด โครงสร้าง การทำงาน
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

 # Microservices REST API Documentation with Nuxt.js

## 1. โครงสร้างการทำงานของ Nuxt.js

### 1.1 Architecture Overview
```
┌─────────────────────────────────────────────────────┐
│                    Client (Browser)                  │
└──────────────────────────┬──────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────┐
│                 Nuxt.js Application                 │
│  ┌──────────────────────────────────────────────┐  │
│  │          SSR (Server-Side Rendering)         │  │
│  │  ┌─────────────┐  ┌──────────────────────┐  │  │
│  │  │   Routing   │  │   Vue.js Components  │  │  │
│  │  └─────────────┘  └──────────────────────┘  │  │
│  │                                              │  │
│  │  ┌────────────────────────────────────────┐  │  │
│  │  │         API Middleware Layer           │  │  │
│  │  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────────┐  │  │  │
│  │  │  │Auth │ │Validation│ │Cache │ │Logging│  │  │  │
│  │  │  └─────┘ └─────┘ └─────┘ └─────────┘  │  │  │
│  │  └────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────┘  │
│                                                    │
│  ┌──────────────────────────────────────────────┐  │
│  │            Backend Services                  │  │
│  │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐        │  │
│  │  │User  │ │Product│ │Inventory│ │Shipping│        │  │
│  │  └──────┘ └──────┘ └──────┘ └──────┘        │  │
│  └──────────────────────────────────────────────┘  │
└──────────────────────────┬──────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────┐
│                Database & Infrastructure            │
│  ┌──────────┐ ┌─────────┐ ┌────────┐ ┌──────────┐  │
│  │PostgreSQL│ │  Redis  │ │ Kafka  │ │Elasticsearch││
│  └──────────┘ └─────────┘ └────────┘ └──────────┘  │
└─────────────────────────────────────────────────────┘
```

### 1.2 Nuxt.js Directory Structure
```
nuxt-app/
├── .nuxt/                    # Build files
├── assets/                   # Static assets
├── components/               # Vue.js components
├── composables/              # Composable functions
├── layouts/                  # Layout components
├── middleware/               # Route middleware
│   ├── auth.ts              # JWT authentication
│   ├── validation.ts        # Request validation
│   └── cache.ts             # Cache management
├── pages/                    # Application pages
├── plugins/                  # Nuxt.js plugins
│   ├── axios.ts             # API client
│   ├── validator.ts         # Form validation
│   └── jwt.ts               # JWT utilities
├── server/                   # Server-side
│   ├── api/                 # API routes
│   │   ├── user/
│   │   ├── product/
│   │   ├── inventory/
│   │   └── shipping/
│   ├── middleware/          # Server middleware
│   └── utils/               # Server utilities
├── stores/                   # Pinia stores
├── types/                    # TypeScript types
├── utils/                    # Utility functions
├── .env                      # Environment variables
├── nuxt.config.ts           # Nuxt.js configuration
├── docker-compose.yml       # Docker Compose
└── package.json
```

## 2. Template Code Structure

### 2.1 Base API Controller Template
```typescript
// server/api/base.controller.ts
import { H3Event } from 'h3'
import { validateRequest } from '~/server/middleware/validator'
import { cacheMiddleware } from '~/server/middleware/cache'
import { logger } from '~/server/utils/logger'

export abstract class BaseController {
  protected async handleRequest<T>(
    event: H3Event,
    handler: () => Promise<T>,
    options?: {
      validateSchema?: any
      cacheKey?: string
      cacheTTL?: number
    }
  ): Promise<{ data: T; success: boolean; message?: string }> {
    try {
      // Request validation
      if (options?.validateSchema) {
        await validateRequest(event, options.validateSchema)
      }

      // Cache check
      if (options?.cacheKey) {
        const cached = await cacheMiddleware.get(options.cacheKey)
        if (cached) {
          return {
            data: cached as T,
            success: true,
            message: 'Data retrieved from cache'
          }
        }
      }

      // Execute handler
      const data = await handler()

      // Set cache
      if (options?.cacheKey && data) {
        await cacheMiddleware.set(
          options.cacheKey,
          data,
          options.cacheTTL || 300
        )
      }

      // Log successful operation
      logger.info({
        path: event.path,
        method: event.method,
        status: 'success'
      })

      return { data, success: true }
    } catch (error: any) {
      // Log error
      logger.error({
        path: event.path,
        method: event.method,
        error: error.message,
        stack: error.stack
      })

      throw createError({
        statusCode: error.statusCode || 500,
        statusMessage: error.message || 'Internal Server Error'
      })
    }
  }
}
```

### 2.2 Model Template with ORM
```typescript
// server/models/base.model.ts
import { Model, DataTypes, Sequelize } from 'sequelize'
import { logger } from '~/server/utils/logger'

export abstract class BaseModel<T extends Model> extends Model {
  static initialize(sequelize: Sequelize) {
    throw new Error('Method not implemented')
  }

  static associate(models: any) {
    // To be implemented by child classes
  }

  static async findWithCache(id: number, cacheKey: string): Promise<T | null> {
    const cache = await useStorage('redis').getItem(cacheKey)
    if (cache) {
      logger.debug(`Cache hit for ${cacheKey}`)
      return cache as T
    }

    const result = await this.findByPk(id)
    if (result) {
      await useStorage('redis').setItem(cacheKey, result, { ttl: 300 })
    }

    return result
  }

  // Audit trail method
  static async createWithAudit(data: any, userId: number): Promise<T> {
    const transaction = await this.sequelize!.transaction()
    
    try {
      const result = await this.create(data, { transaction })
      
      // Log to audit system
      await AuditLog.create({
        userId,
        action: 'CREATE',
        tableName: this.tableName,
        recordId: result.id,
        oldData: null,
        newData: data,
        ipAddress: null,
        userAgent: null
      }, { transaction })

      await transaction.commit()
      return result
    } catch (error) {
      await transaction.rollback()
      throw error
    }
  }
}

// server/models/user.model.ts
import { Table, Column, Model, DataType, HasMany } from 'sequelize-typescript'
import { AuditLog } from './audit.model'
import { Product } from './product.model'

@Table({
  tableName: 'users',
  timestamps: true,
  paranoid: true
})
export class User extends BaseModel<User> {
  @Column({
    type: DataType.INTEGER,
    primaryKey: true,
    autoIncrement: true
  })
  id!: number

  @Column({
    type: DataType.STRING(100),
    allowNull: false,
    unique: true,
    validate: {
      isEmail: true
    }
  })
  email!: string

  @Column({
    type: DataType.STRING(255),
    allowNull: false
  })
  password!: string

  @Column({
    type: DataType.STRING(100),
    allowNull: false
  })
  firstName!: string

  @Column({
    type: DataType.STRING(100),
    allowNull: false
  })
  lastName!: string

  @Column({
    type: DataType.ENUM('admin', 'manager', 'staff', 'customer'),
    defaultValue: 'customer'
  })
  role!: string

  @Column({
    type: DataType.BOOLEAN,
    defaultValue: true
  })
  isActive!: boolean

  @HasMany(() => AuditLog)
  auditLogs!: AuditLog[]

  @HasMany(() => Product)
  products!: Product[]

  // JWT token generation
  async generateToken(): Promise<string> {
    const jwt = useJWT()
    return await jwt.sign({
      userId: this.id,
      email: this.email,
      role: this.role
    })
  }

  // Password validation
  async validatePassword(password: string): Promise<boolean> {
    const bcrypt = useBcrypt()
    return await bcrypt.compare(password, this.password)
  }
}
```

### 2.3 Service Layer Template
```typescript
// server/services/user.service.ts
import { User } from '~/server/models/user.model'
import { AuditLog } from '~/server/models/audit.model'
import { KafkaProducer } from '~/server/utils/kafka'
import { CacheService } from '~/server/services/cache.service'

export class UserService {
  private cacheService: CacheService
  private kafkaProducer: KafkaProducer

  constructor() {
    this.cacheService = new CacheService()
    this.kafkaProducer = new KafkaProducer()
  }

  async createUser(userData: any, createdBy: number): Promise<User> {
    // Publish to Kafka for async processing
    await this.kafkaProducer.send('user.created', {
      ...userData,
      createdBy,
      timestamp: new Date()
    })

    // Create user with audit trail
    const user = await User.createWithAudit(userData, createdBy)

    // Invalidate cache
    await this.cacheService.invalidate('users:*')

    return user
  }

  async getUserById(id: number): Promise<User | null> {
    const cacheKey = `user:${id}`
    
    return await User.findWithCache(id, cacheKey) as User | null
  }

  async updateUser(id: number, updateData: any, updatedBy: number): Promise<User> {
    const transaction = await User.sequelize!.transaction()
    
    try {
      const user = await User.findByPk(id, { transaction })
      if (!user) {
        throw new Error('User not found')
      }

      const oldData = { ...user.get() }
      await user.update(updateData, { transaction })

      // Audit log
      await AuditLog.create({
        userId: updatedBy,
        action: 'UPDATE',
        tableName: 'users',
        recordId: id,
        oldData,
        newData: updateData
      }, { transaction })

      // Publish update event
      await this.kafkaProducer.send('user.updated', {
        userId: id,
        updatedBy,
        changes: updateData,
        timestamp: new Date()
      })

      await transaction.commit()

      // Invalidate cache
      await this.cacheService.invalidate(`user:${id}`)
      await this.cacheService.invalidate('users:*')

      return user
    } catch (error) {
      await transaction.rollback()
      throw error
    }
  }

  async deleteUser(id: number, deletedBy: number): Promise<boolean> {
    const transaction = await User.sequelize!.transaction()
    
    try {
      const user = await User.findByPk(id, { transaction })
      if (!user) {
        throw new Error('User not found')
      }

      const oldData = { ...user.get() }
      await user.destroy({ transaction })

      // Audit log
      await AuditLog.create({
        userId: deletedBy,
        action: 'DELETE',
        tableName: 'users',
        recordId: id,
        oldData,
        newData: null
      }, { transaction })

      // Publish delete event
      await this.kafkaProducer.send('user.deleted', {
        userId: id,
        deletedBy,
        timestamp: new Date()
      })

      await transaction.commit()

      // Invalidate cache
      await this.cacheService.invalidate(`user:${id}`)
      await this.cacheService.invalidate('users:*')

      return true
    } catch (error) {
      await transaction.rollback()
      throw error
    }
  }

  async getUsersWithPagination(
    page: number = 1,
    limit: number = 10,
    filters?: any
  ): Promise<{ users: User[]; total: number; page: number; totalPages: number }> {
    const cacheKey = `users:page:${page}:limit:${limit}:filters:${JSON.stringify(filters)}`
    
    const cached = await this.cacheService.get(cacheKey)
    if (cached) {
      return cached
    }

    const offset = (page - 1) * limit
    
    const { rows: users, count: total } = await User.findAndCountAll({
      where: filters,
      limit,
      offset,
      order: [['createdAt', 'DESC']]
    })

    const totalPages = Math.ceil(total / limit)
    
    const result = {
      users,
      total,
      page,
      totalPages
    }

    await this.cacheService.set(cacheKey, result, 300)
    
    return result
  }
}
```

### 2.4 API Route Template
```typescript
// server/api/users/index.get.ts
import { defineEventHandler } from 'h3'
import { UserController } from '~/server/controllers/user.controller'
import { authMiddleware } from '~/server/middleware/auth'
import { validateQuery } from '~/server/middleware/validator'
import { userQuerySchema } from '~/server/validators/user.validator'

const controller = new UserController()

export default defineEventHandler(async (event) => {
  // Authentication middleware
  await authMiddleware(event, ['admin', 'manager'])
  
  // Validation middleware
  await validateQuery(event, userQuerySchema)
  
  const query = getQuery(event)
  const page = parseInt(query.page as string) || 1
  const limit = parseInt(query.limit as string) || 10
  
  return await controller.getUsers(event, page, limit)
})
```

### 2.5 JWT Authentication Middleware
```typescript
// server/middleware/auth.ts
import { H3Event } from 'h3'
import { verify } from 'jsonwebtoken'

export const authMiddleware = async (
  event: H3Event,
  allowedRoles?: string[]
): Promise<void> => {
  const authHeader = getHeader(event, 'Authorization')
  
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    throw createError({
      statusCode: 401,
      statusMessage: 'Unauthorized: No token provided'
    })
  }

  const token = authHeader.substring(7)
  const config = useRuntimeConfig()

  try {
    const decoded = verify(token, config.jwtSecret) as any
    
    // Check if user exists and is active
    const user = await User.findByPk(decoded.userId)
    if (!user || !user.isActive) {
      throw createError({
        statusCode: 401,
        statusMessage: 'Unauthorized: User not found or inactive'
      })
    }

    // Role-based access control
    if (allowedRoles && allowedRoles.length > 0) {
      if (!allowedRoles.includes(user.role)) {
        throw createError({
          statusCode: 403,
          statusMessage: 'Forbidden: Insufficient permissions'
        })
      }
    }

    // Attach user to event context
    event.context.user = user
    event.context.auth = decoded

  } catch (error: any) {
    throw createError({
      statusCode: 401,
      statusMessage: `Unauthorized: ${error.message}`
    })
  }
}
```

## 3. Data Flow Design

### 3.1 User Registration Flow
```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌──────────┐
│  Client │────▶│  Nuxt   │────▶│  Auth   │────▶│ Database │
│         │     │  API    │     │ Service │     │          │
└─────────┘     └─────────┘     └─────────┘     └──────────┘
                     │               │                │
                     │               │                │
                     │          ┌────▼────┐          │
                     │          │  Kafka  │          │
                     │          │Producer │          │
                     │          └────┬────┘          │
                     │               │                │
                     │          ┌────▼────┐          │
                     │          │ Audit   │          │
                     │          │ Logger  │◀─────────┘
                     │          └─────────┘
                     │               │
                ┌────▼─────┐        │
                │ Response │        │
                │  Client  │        │
                └──────────┘        │
                                    ▼
                             ┌─────────────┐
                             │ Elastic-    │
                             │   search    │
                             │  (Logging)  │
                             └─────────────┘
```

### 3.2 Product Order Flow
```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌──────────┐
│  Client │────▶│  Order  │────▶│Inventory│────▶│Database  │
│         │     │ Service │     │ Service │     │          │
└─────────┘     └─────────┘     └─────────┘     └──────────┘
                     │               │                │
                     │               │                │
                ┌────▼─────┐   ┌────▼────┐          │
                │Shipping  │   │  Kafka  │          │
                │ Service  │   │(Events) │          │
                └────┬─────┘   └────┬────┘          │
                     │               │                │
                ┌────▼─────┐   ┌────▼────┐          │
                │Payment   │   │Notification│        │
                │Gateway   │   │ Service  │◀────────┘
                └──────────┘   └──────────┘
                                    │
                               ┌────▼────┐
                               │  Redis  │
                               │ (Cache) │
                               └─────────┘
```

## 4. Workflow Design

### 4.1 CI/CD Pipeline Workflow
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

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
        ports:
          - 5432:5432
      redis:
        image: redis:alpine
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
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run linting
      run: npm run lint
      
    - name: Run tests
      run: npm run test
      env:
        DATABASE_URL: postgres://postgres:postgres@localhost:5432/test_db
        REDIS_URL: redis://localhost:6379
        
    - name: Run Robot Framework tests
      run: |
        pip install robotframework
        robot tests/api_tests.robot

  build-and-deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ap-southeast-1
        
    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1
      
    - name: Build, tag, and push image to Amazon ECR
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: nuxt-microservices
        IMAGE_TAG: ${{ github.sha }}
      run: |
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
        
    - name: Deploy to EC2
      uses: appleboy/ssh-action@v0.1.4
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ${{ secrets.EC2_USERNAME }}
        key: ${{ secrets.EC2_SSH_KEY }}
        script: |
          cd /app/nuxt-microservices
          docker-compose pull
          docker-compose up -d
          docker system prune -f
```

### 4.2 Monitoring Workflow
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Application │────▶│   Logstash  │────▶│ Elastic-    │
│    Logs     │     │             │     │   search    │
└─────────────┘     └─────────────┘     └─────────────┘
                                                   │
                                              ┌────▼────┐
                                              │ Kibana  │
                                              │  (UI)   │
                                              └────┬────┘
                                                   │
┌─────────────┐     ┌─────────────┐          ┌────▼────┐
│ Application │────▶│   Prometheus│◀─────────│Grafana  │
│  Metrics    │     │             │          │         │
└─────────────┘     └─────────────┘          └─────────┘
                                                   │
                                              ┌────▼────┐
                                              │ Alert   │
                                              │Manager  │
                                              └─────────┘
```

## 5. Report Template Design

### 5.1 Sales Report Template
```typescript
// server/services/report.service.ts
export class ReportService {
  async generateSalesReport(
    startDate: Date,
    endDate: Date,
    filters?: any
  ): Promise<SalesReport> {
    const cacheKey = `sales_report:${startDate.toISOString()}:${endDate.toISOString()}:${JSON.stringify(filters)}`
    
    // Check cache
    const cached = await this.cacheService.get(cacheKey)
    if (cached) {
      return cached
    }

    const report = {
      summary: await this.getSalesSummary(startDate, endDate),
      dailySales: await this.getDailySales(startDate, endDate),
      topProducts: await this.getTopProducts(startDate, endDate, 10),
      revenueByCategory: await this.getRevenueByCategory(startDate, endDate),
      customerMetrics: await this.getCustomerMetrics(startDate, endDate),
      exportData: await this.getExportData(startDate, endDate)
    }

    // Cache for 1 hour
    await this.cacheService.set(cacheKey, report, 3600)
    
    return report
  }

  private async getSalesSummary(startDate: Date, endDate: Date): Promise<SalesSummary> {
    const result = await Order.sequelize!.query(`
      SELECT 
        COUNT(*) as total_orders,
        SUM(total_amount) as total_revenue,
        AVG(total_amount) as average_order_value,
        COUNT(DISTINCT customer_id) as unique_customers
      FROM orders
      WHERE order_date BETWEEN :startDate AND :endDate
        AND status = 'completed'
    `, {
      replacements: { startDate, endDate },
      type: QueryTypes.SELECT
    })

    return result[0] as SalesSummary
  }

  private async getExportData(startDate: Date, endDate: Date): Promise<ExportData> {
    const data = await Order.findAll({
      where: {
        orderDate: {
          [Op.between]: [startDate, endDate]
        }
      },
      include: [
        {
          model: OrderItem,
          include: [Product]
        },
        {
          model: Customer
        }
      ],
      raw: true,
      nest: true
    })

    return {
      format: 'csv',
      data: this.convertToCSV(data),
      generatedAt: new Date(),
      recordCount: data.length
    }
  }
}
```

### 5.2 Audit Log Report Template
```typescript
// server/templates/audit-report.hbs
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Audit Report - {{reportDate}}</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        .header { text-align: center; margin-bottom: 30px; }
        .table { width: 100%; border-collapse: collapse; margin-top: 20px; }
        .table th, .table td { border: 1px solid #ddd; padding: 12px; text-align: left; }
        .table th { background-color: #f4f4f4; }
        .summary { background-color: #f9f9f9; padding: 20px; margin-bottom: 20px; }
        .footer { margin-top: 40px; text-align: center; color: #666; }
    </style>
</head>
<body>
    <div class="header">
        <h1>System Audit Report</h1>
        <p>Generated on: {{generatedAt}}</p>
        <p>Period: {{startDate}} to {{endDate}}</p>
    </div>

    <div class="summary">
        <h2>Summary</h2>
        <p>Total Activities: {{summary.totalActivities}}</p>
        <p>Total Users: {{summary.totalUsers}}</p>
        <p>Most Active User: {{summary.mostActiveUser}}</p>
    </div>

    <h2>Activity Details</h2>
    <table class="table">
        <thead>
            <tr>
                <th>Timestamp</th>
                <th>User</th>
                <th>Action</th>
                <th>Table</th>
                <th>Record ID</th>
                <th>IP Address</th>
            </tr>
        </thead>
        <tbody>
            {{#each activities}}
            <tr>
                <td>{{this.timestamp}}</td>
                <td>{{this.user}}</td>
                <td>{{this.action}}</td>
                <td>{{this.table}}</td>
                <td>{{this.recordId}}</td>
                <td>{{this.ipAddress}}</td>
            </tr>
            {{/each}}
        </tbody>
    </table>

    <div class="footer">
        <p>Report generated by Nuxt.js Microservices System</p>
        <p>Page {{page}} of {{totalPages}}</p>
    </div>
</body>
</html>
```

## 6. Infrastructure Configuration

### 6.1 Docker Compose Configuration
```yaml
# docker-compose.yml
version: '3.8'

services:
  # Nuxt.js Application
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/app_db
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKERS=kafka:9092
      - ELASTICSEARCH_URL=http://elasticsearch:9200
    depends_on:
      - postgres
      - redis
      - kafka
      - elasticsearch
    networks:
      - app-network
    restart: unless-stopped

  # PostgreSQL Database
  postgres:
    image: postgres:14-alpine
    environment:
      - POSTGRES_DB=app_db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init-db:/docker-entrypoint-initdb.d
    ports:
      - "5432:5432"
    networks:
      - app-network
    restart: unless-stopped

  # Redis Cache
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"
    networks:
      - app-network
    restart: unless-stopped

  # Kafka
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "2181:2181"
    networks:
      - app-network

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
      - app-network

  # ELK Stack
  elasticsearch:
    image: elasticsearch:8.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - app-network

  logstash:
    image: logstash:8.5.0
    volumes:
      - ./logstash/config:/usr/share/logstash/config
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - "5000:5000"
    depends_on:
      - elasticsearch
    networks:
      - app-network

  kibana:
    image: kibana:8.5.0
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
    networks:
      - app-network

  # Monitoring
  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    ports:
      - "9090:9090"
    networks:
      - app-network

  grafana:
    image: grafana/grafana:latest
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
    ports:
      - "3001:3000"
    depends_on:
      - prometheus
    networks:
      - app-network

  # n8n Workflow Automation
  n8n:
    image: n8nio/n8n:latest
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=password
      - WEBHOOK_URL=http://localhost:5678
    ports:
      - "5678:5678"
    volumes:
      - n8n-data:/home/node/.n8n
    networks:
      - app-network

  # Jenkins
  jenkins:
    image: jenkins/jenkins:lts-jdk11
    privileged: true
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins-data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  postgres-data:
  redis-data:
  elasticsearch-data:
  prometheus-data:
  grafana-data:
  n8n-data:
  jenkins-data:
```

### 6.2 AWS Deployment Configuration
```bash
#!/bin/bash
# deploy-aws.sh

# Variables
APP_NAME="nuxt-microservices"
ECR_REPO="123456789.dkr.ecr.ap-southeast-1.amazonaws.com"
ENVIRONMENT="production"
VERSION="1.0.0"

# Login to ECR
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin $ECR_REPO

# Build and push Docker image
docker build -t $ECR_REPO/$APP_NAME:$VERSION .
docker push $ECR_REPO/$APP_NAME:$VERSION

# Deploy to EC2
ssh -i "~/.ssh/aws-key.pem" ec2-user@$EC2_INSTANCE_IP << EOF
  # Pull latest image
  aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin $ECR_REPO
  docker pull $ECR_REPO/$APP_NAME:$VERSION
  
  # Stop and remove old container
  docker stop $APP_NAME || true
  docker rm $APP_NAME || true
  
  # Run new container
  docker run -d \
    --name $APP_NAME \
    --network host \
    -p 3000:3000 \
    -e NODE_ENV=production \
    -e DATABASE_URL=$DATABASE_URL \
    -e REDIS_URL=$REDIS_URL \
    -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
    -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
    -e S3_BUCKET=$S3_BUCKET \
    $ECR_REPO/$APP_NAME:$VERSION
    
  # Clean up old images
  docker system prune -f
EOF

echo "Deployment completed successfully!"
```

## 7. Robot Framework Test Template

```robot
# tests/api_tests.robot
*** Settings ***
Library    Collections
Library    RequestsLibrary
Library    DatabaseLibrary
Library    String

Suite Setup    Connect To Database    psycopg2    ${DB_NAME}    ${DB_USER}    ${DB_PASSWORD}    ${DB_HOST}    ${DB_PORT}
Suite Teardown    Disconnect From Database

*** Variables ***
${BASE_URL}    http://localhost:3000
${DB_HOST}    localhost
${DB_PORT}    5432
${DB_NAME}    test_db
${DB_USER}    postgres
${DB_PASSWORD}    password

*** Test Cases ***
User Registration Test
    [Document