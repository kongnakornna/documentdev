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

# ##################################
# Microservices REST API System Documentation

## 1. โครงสร้างและการทำงานของ NestJS (Backend)

### 1.1 โครงสร้าง Architecture
```
nestjs-microservice/
├── src/
│   ├── common/
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── pipes/
│   ├── config/
│   ├── database/
│   │   └── migrations/
│   ├── modules/
│   │   ├── auth/
│   │   │   ├── controllers/
│   │   │   ├── services/
│   │   │   ├── strategies/
│   │   │   └── dto/
│   │   ├── users/
│   │   ├── products/
│   │   ├── inventory/
│   │   ├── shipping/
│   │   ├── reports/
│   │   └── audit-log/
│   ├── shared/
│   │   ├── kafka/
│   │   ├── redis/
│   │   └── elasticsearch/
│   └── main.ts
├── test/
├── docker/
├── .env
└── docker-compose.yml
```

### 1.2 Flow การทำงาน
```mermaid
graph TD
    A[Client Request] --> B[Global Middleware]
    B --> C[Validation Pipe]
    C --> D[JWT Guard]
    D --> E[Controller]
    E --> F[Service Layer]
    F --> G[Repository/ORM]
    G --> H[Database]
    F --> I[Kafka Producer]
    I --> J[Microservices]
    F --> K[Redis Cache]
    F --> L[Elasticsearch]
```

### 1.3 Core Components
- **Modules**: แบ่งตาม business domain
- **Controllers**: รับ request และส่ง response
- **Services**: Business logic
- **DTOs**: Data Transfer Objects สำหรับ validation
- **Entities**: Database models
- **Repositories**: Data access layer

### 1.4 Template Code Example
```typescript
// user.controller.ts
import { Controller, Get, Post, Body, UseGuards } from '@nestjs/common';
import { JwtAuthGuard } from '../auth/guards/jwt-auth.guard';
import { CreateUserDto } from './dto/create-user.dto';
import { UserService } from './user.service';

@Controller('users')
@UseGuards(JwtAuthGuard)
export class UserController {
  constructor(private readonly userService: UserService) {}

  @Post()
  async create(@Body() createUserDto: CreateUserDto) {
    return this.userService.create(createUserDto);
  }

  @Get()
  async findAll() {
    return this.userService.findAll();
  }
}

// user.service.ts
import { Injectable, Inject } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { User } from './entities/user.entity';
import { CreateUserDto } from './dto/create-user.dto';
import { KafkaService } from '../../shared/kafka/kafka.service';

@Injectable()
export class UserService {
  constructor(
    @InjectRepository(User)
    private userRepository: Repository<User>,
    private kafkaService: KafkaService,
  ) {}

  async create(createUserDto: CreateUserDto): Promise<User> {
    const user = this.userRepository.create(createUserDto);
    const savedUser = await this.userRepository.save(user);
    
    // Send event to Kafka
    await this.kafkaService.send('user.created', {
      userId: savedUser.id,
      email: savedUser.email,
      timestamp: new Date(),
    });
    
    return savedUser;
  }
}
```

## 2. โครงสร้างและการทำงานของ Next.js (Frontend)

### 2.1 โครงสร้าง Architecture
```
nextjs-frontend/
├── app/
│   ├── api/
│   │   └── auth/
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   ├── dashboard/
│   │   ├── users/
│   │   ├── products/
│   │   ├── inventory/
│   │   ├── shipping/
│   │   └── reports/
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── common/
│   │   ├── Header/
│   │   ├── Sidebar/
│   │   ├── DataTable/
│   │   └── Charts/
│   ├── forms/
│   └── modals/
├── lib/
│   ├── api/
│   │   ├── axios-client.ts
│   │   ├── auth.ts
│   │   └── services/
│   ├── hooks/
│   ├── utils/
│   └── constants/
├── store/
│   ├── slices/
│   └── index.ts
├── styles/
└── public/
```

### 2.2 Flow การทำงาน
```mermaid
graph LR
    A[User Browser] --> B[Next.js Server]
    B --> C[Middleware]
    C --> D[Route Handler]
    D --> E[API Call to Backend]
    E --> F[NestJS Backend]
    F --> G[Database]
    D --> H[React Component]
    H --> I[Redux Store]
    I --> J[UI Render]
```

### 2.3 Core Features
- **App Router**: File-based routing
- **Server Components**: Server-side rendering
- **Client Components**: Interactive UI
- **API Routes**: Backend-for-frontend
- **Middleware**: Authentication & authorization

### 2.4 Template Code Example
```typescript
// app/dashboard/users/page.tsx
'use client';

import { useEffect, useState } from 'react';
import { DataTable } from '@/components/common/DataTable';
import { UserForm } from '@/components/forms/UserForm';
import { userService } from '@/lib/api/services/userService';
import { User } from '@/lib/types';

export default function UsersPage() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    try {
      const data = await userService.getAll();
      setUsers(data);
    } catch (error) {
      console.error('Error fetching users:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="container mx-auto p-6">
      <h1 className="text-2xl font-bold mb-6">User Management</h1>
      <UserForm onSuccess={fetchUsers} />
      <DataTable 
        data={users}
        columns={[
          { header: 'ID', accessor: 'id' },
          { header: 'Name', accessor: 'name' },
          { header: 'Email', accessor: 'email' },
          { header: 'Role', accessor: 'role' },
          { header: 'Actions', accessor: 'actions' },
        ]}
        loading={loading}
      />
    </div>
  );
}
```

## 3. โครงสร้างและการทำงานของ React

### 3.1 โครงสร้าง Component Hierarchy
```
App
├── Layout
│   ├── Header
│   ├── Sidebar
│   └── MainContent
├── Routes
│   ├── AuthRoutes
│   │   ├── Login
│   │   └── Register
│   └── ProtectedRoutes
│       ├── Dashboard
│       │   ├── UserManagement
│       │   │   ├── UserList
│       │   │   ├── UserForm
│       │   │   └── UserDetail
│       │   ├── ProductManagement
│       │   ├── InventoryManagement
│       │   ├── ShippingManagement
│       │   └── ReportDashboard
│       └── Settings
└── Providers
    ├── ReduxProvider
    ├── ThemeProvider
    └── AuthProvider
```

### 3.2 State Management Flow
```mermaid
graph TD
    A[User Action] --> B[Component]
    B --> C[Action Creator]
    C --> D[Redux Store]
    D --> E[Reducer]
    E --> F[State Update]
    F --> G[Component Re-render]
    G --> H[API Call]
    H --> I[Backend]
```

### 3.3 Template Code Example
```typescript
// components/common/DataTable.tsx
import React from 'react';
import { Table, Button, Space } from 'antd';

interface Column {
  header: string;
  accessor: string;
  render?: (value: any, record: any) => React.ReactNode;
}

interface DataTableProps {
  data: any[];
  columns: Column[];
  loading?: boolean;
  onEdit?: (record: any) => void;
  onDelete?: (record: any) => void;
}

export const DataTable: React.FC<DataTableProps> = ({
  data,
  columns,
  loading = false,
  onEdit,
  onDelete,
}) => {
  const tableColumns = columns.map(col => ({
    title: col.header,
    dataIndex: col.accessor,
    key: col.accessor,
    render: col.render || ((text: any) => text),
  }));

  if (onEdit || onDelete) {
    tableColumns.push({
      title: 'Actions',
      key: 'actions',
      render: (_: any, record: any) => (
        <Space size="small">
          {onEdit && (
            <Button type="link" onClick={() => onEdit(record)}>
              Edit
            </Button>
          )}
          {onDelete && (
            <Button type="link" danger onClick={() => onDelete(record)}>
              Delete
            </Button>
          )}
        </Space>
      ),
    });
  }

  return (
    <Table
      columns={tableColumns}
      dataSource={data}
      loading={loading}
      rowKey="id"
      pagination={{ pageSize: 10 }}
    />
  );
};
```

## 4. Data Flow Design

### 4.1 Overall System Flow
```mermaid
graph TB
    subgraph "Frontend Layer"
        A[React Components]
        B[Next.js API Routes]
        C[Redux Store]
    end
    
    subgraph "Backend Layer"
        D[NestJS Gateway]
        E[Auth Service]
        F[User Service]
        G[Product Service]
        H[Inventory Service]
        I[Shipping Service]
        J[Report Service]
    end
    
    subgraph "Infrastructure"
        K[PostgreSQL]
        L[Redis Cache]
        M[Kafka]
        N[Elasticsearch]
        O[Redis Queue]
    end
    
    A --> B
    B --> D
    D --> E
    E --> F
    F --> K
    F --> L
    F --> M
    M --> N
    G --> K
    H --> K
    I --> K
    J --> N
```

### 4.2 Authentication Flow
1. User login → JWT token generation
2. Token storage in HTTP-only cookies
3. Each request includes token in Authorization header
4. NestJS validates token using JWT strategy
5. Redis checks token blacklist (for logout)

### 4.3 CRUD Operations Flow
```
CREATE:
Frontend → POST /api/resource → NestJS Controller → 
Service → Repository → Database → 
Kafka Event → Audit Log

READ:
Frontend → GET /api/resource → NestJS Controller → 
Service → Redis Cache Check → 
[Cache Hit: Return] / [Cache Miss: Database → Cache]

UPDATE:
Frontend → PUT /api/resource/:id → NestJS Controller → 
Service → Repository → Database → 
Kafka Event → Elasticsearch Update

DELETE:
Frontend → DELETE /api/resource/:id → NestJS Controller → 
Service → Soft Delete → Database → 
Kafka Event → Audit Log
```

## 5. Workflow Design

### 5.1 Development Workflow
```mermaid
graph LR
    A[Feature Branch] --> B[Development]
    B --> C[Code Review]
    C --> D[Merge to Main]
    D --> E[CI/CD Pipeline]
    E --> F[Automated Testing]
    F --> G[Docker Build]
    G --> H[Deploy to Staging]
    H --> I[Integration Testing]
    I --> J[Deploy to Production]
```

### 5.2 CI/CD Pipeline
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
      redis:
        image: redis:7
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
        env:
          DATABASE_URL: postgresql://postgres:postgres@localhost:5432/test_db
          REDIS_URL: redis://localhost:6379
  
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Build Docker images
        run: docker-compose -f docker-compose.prod.yml build
      
      - name: Push to Container Registry
        run: |
          echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin
          docker-compose -f docker-compose.prod.yml push
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to AWS
        uses: appleboy/ssh-action@v0.1.4
        with:
          host: ${{ secrets.AWS_HOST }}
          username: ${{ secrets.AWS_USER }}
          key: ${{ secrets.AWS_SSH_KEY }}
          script: |
            cd /var/www/microservices
            docker-compose pull
            docker-compose up -d
```

## 6. Database Design

### 6.1 PostgreSQL Schema
```sql
-- Users Table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    role VARCHAR(50) DEFAULT 'user',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login_at TIMESTAMP
);

-- Products Table
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category VARCHAR(100),
    brand VARCHAR(100),
    weight DECIMAL(10,2),
    dimensions JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Inventory Table
CREATE TABLE inventory (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    warehouse_id UUID REFERENCES warehouses(id),
    quantity INTEGER NOT NULL DEFAULT 0,
    reserved_quantity INTEGER DEFAULT 0,
    min_stock_level INTEGER DEFAULT 10,
    max_stock_level INTEGER DEFAULT 1000,
    last_restocked_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit Log Table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    action VARCHAR(100) NOT NULL,
    resource_type VARCHAR(100),
    resource_id UUID,
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 7. Template Reports Design

### 7.1 Sales Report Template
```json
{
  "report": {
    "type": "sales_summary",
    "period": "monthly",
    "date_range": {
      "start": "2024-01-01",
      "end": "2024-01-31"
    },
    "metrics": {
      "total_sales": {
        "value": 1500000,
        "currency": "THB",
        "growth": 15.5
      },
      "total_orders": {
        "value": 1200,
        "growth": 8.2
      },
      "average_order_value": {
        "value": 1250,
        "currency": "THB"
      },
      "top_products": [
        {
          "product_id": "prod_001",
          "name": "Product A",
          "quantity_sold": 450,
          "revenue": 562500
        }
      ]
    },
    "charts": {
      "daily_sales": "line_chart",
      "category_distribution": "pie_chart",
      "regional_sales": "bar_chart"
    }
  }
}
```

### 7.2 Inventory Report Template
```json
{
  "report": {
    "type": "inventory_status",
    "generated_at": "2024-01-31T23:59:59Z",
    "summary": {
      "total_products": 1500,
      "total_value": 25000000,
      "low_stock_items": 45,
      "out_of_stock_items": 12,
      "overstock_items": 23
    },
    "details": {
      "low_stock": [
        {
          "product_id": "prod_045",
          "name": "Product B",
          "current_stock": 8,
          "min_stock_level": 10,
          "last_restock": "2024-01-15"
        }
      ],
      "slow_moving": [
        {
          "product_id": "prod_123",
          "name": "Product C",
          "current_stock": 150,
          "days_in_stock": 90,
          "turnover_rate": 0.2
        }
      ]
    }
  }
}
```

## 8. Docker Compose Configuration

### 8.1 docker-compose.yml
```yaml
version: '3.8'

services:
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

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - microservices-network

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

  elasticsearch:
    image: elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    networks:
      - microservices-network

  kibana:
    image: kibana:8.10.0
    depends_on:
      - elasticsearch
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    networks:
      - microservices-network

  nestjs-api:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKERS=kafka:9092
      - ELASTICSEARCH_URL=http://elasticsearch:9200
    depends_on:
      - postgres
      - redis
      - kafka
      - elasticsearch
    networks:
      - microservices-network

  nextjs-frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "8080:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://nestjs-api:3000
    depends_on:
      - nestjs-api
    networks:
      - microservices-network

  grafana:
    image: grafana/grafana:10.0.0
    ports:
      - "3001:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_PASSWORD}
    volumes:
      - grafana_data:/var/lib/grafana
    networks:
      - microservices-network

  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - microservices-network

volumes:
  postgres_data:
  redis_data:
  elasticsearch_data:
  grafana_data:
  prometheus_data:

networks:
  microservices-network:
    driver: bridge
```

## 9. Monitoring and Observability

### 9.1 Grafana Dashboard Template
```json
{
  "dashboard": {
    "title": "Microservices Monitoring",
    "panels": [
      {
        "title": "API Response Time",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])",
            "legendFormat": "{{endpoint}}"
          }
        ]
      },
      {
        "title": "Database Connections",
        "type": "stat",
        "targets": [
          {
            "expr": "pg_stat_database_numbackends{datname=\"$database\"}"
          }
        ]
      },
      {
        "title": "Redis Memory Usage",
        "type": "gauge",
        "targets": [
          {
            "expr": "redis_memory_used_bytes / redis_memory_max_bytes * 100"
          }
        ]
      }
    ]
  }
}
```

## 10. Security Implementation

### 10.1 JWT Configuration
```typescript
// auth/jwt.strategy.ts
import { Injectable } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';
import { ConfigService } from '@nestjs/config';
import { RedisService } from '../shared/redis/redis.service';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(
    private configService: ConfigService,
    private redisService: RedisService,
  ) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: configService.get('JWT_SECRET'),
      passReqToCallback: true,
    });
  }

  async validate(req: Request, payload: any) {
    // Check token blacklist in Redis
    const token = ExtractJwt.fromAuthHeaderAsBearerToken()(req);
    const isBlacklisted = await this.redisService.get(`blacklist:${token}`);
    
    if (isBlacklisted) {
      throw new Error('Token is blacklisted');
    }

    return {
      userId: payload.sub,
      email: payload.email,
      role: payload.role,
    };
  }
}
```

## 11. Testing Strategy

### 11.1 Robot Framework API Tests
```robot
*** Settings ***
Library    RequestsLibrary
Library    Collections

*** Variables ***
${BASE_URL}    http://localhost:3000
${VALID_USER}    {"email": "test@example.com", "password": "password123"}

*** Test Cases ***
User Registration Test
    [Documentation]    Test user registration endpoint
    Create Session    api    ${BASE_URL}
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${response}=    POST On Session    api    /auth/register    json=${VALID_USER}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    201
    Dictionary Should Contain Key    ${response.json()}    id
    Dictionary Should Contain Key    ${response.json()}    email

Product CRUD Test
    [Documentation]    Test complete product CRUD operations
    [Setup]    Get Auth Token
    Create Session    api    ${BASE_URL}
    ${headers}=    Create Dictionary    Authorization=Bearer ${TOKEN}    Content-Type=application/json
    
    # Create Product
    ${product_data}=    Create Dictionary    name=Test Product    price=99.99    sku=TEST001
    ${create_response}=    POST On Session    api    /products    json=${product_data}    headers=${headers}
    ${product_id}=    Set Variable    ${create_response.json()}[id]
    
    # Get Product
    ${get_response}=    GET On Session    api    /products/${product_id}    headers=${headers}
    Should Be Equal As Strings    ${get_response.status_code}    200
    
    # Update Product
    ${update_data}=    Create Dictionary    price=149.99
    ${update_response}=    PATCH On Session    api    /products/${product_id}    json=${update_data}    headers=${headers}
    Should Be Equal As Strings    ${update_response.status_code}    200
    
    # Delete Product
    ${delete_response}=    DELETE On Session    api    /products/${product_id}    headers=${headers}
    Should Be Equal As Strings    ${delete_response.status_code}    200

*** Keywords ***
Get Auth Token
    Create Session    api    ${BASE_URL}
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${response}=    POST On Session    api    /auth/login    json=${VALID_USER}    headers=${headers}
    ${TOKEN}=    Set Variable    ${response.json()}[access_token]
    Set Test Variable    ${TOKEN}
```

## 12. Deployment Architecture

### 12.1 AWS Infrastructure
```mermaid
graph TB
    subgraph "AWS Cloud"
        A[Route 53] --> B[CloudFront]
        B --> C[Application Load Balancer]
        C --> D[ECS Cluster]
        
        subgraph "ECS Services"
            E[NestJS API]
            F[Next.js Frontend]
        end
        
        D --> G[VPC]
        G --> H[RDS PostgreSQL]
        G --> I[ElastiCache Redis]
        G --> J[MSK Kafka]
        
        K[S3 Bucket] --> L[Static Assets]
        M[CloudWatch] --> N[Logs & Metrics]
    end
    
    O[Users] --> A
```

### 12.2 Jenkins Pipeline
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'ecr.amazonaws.com'
        AWS_REGION = 'ap-southeast-1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/company/microservices.git'
            }
        }
        
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm test'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        sh 'npm run test:integration'
                    }
                }
                stage('API Tests') {
                    steps {
                        sh 'robot --outputdir reports api-tests/'
                    }
                }
            }
        }
        
        stage('Build & Push') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/nestjs-api:${env.BUILD_ID}")
                    docker.push("${DOCKER_REGISTRY}/nestjs-api:${env.BUILD_ID}")
                    
                    docker.build("${DOCKER_REGISTRY}/nextjs-frontend:${env.BUILD_ID}")
                    docker.push("${DOCKER_REGISTRY}/nextjs-frontend:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Deploy Staging') {
            steps {
                sh '''
                    aws ecs update-service \
                    --cluster staging-cluster \
                    --service nestjs-api \
                    --force-new-deployment
                    
                    aws ecs update-service \
                    --cluster staging-cluster \
                    --service nextjs-frontend \
                    --force-new-deployment
                '''
            }
        }
        
        stage('Deploy Production') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                    aws ecs update-service \
                    --cluster production-cluster \
                    --service nestjs-api \
                    --force-new-deployment
                    
                    aws ecs update-service \
                    --cluster production-cluster \
                    --service nextjs-frontend \
                    --force-new-deployment
                '''
            }
        }
    }
    
    post {
        always {
            junit '**/test-results.xml'
            archiveArtifacts '**/reports/*'
        }
    }
}
```

## 13. Code Comment Template

### 13.1 NestJS Service Template
```typescript
/**
 * @module UserService
 * @description Service responsible for user management operations
 * 
 * @author Your Name
 * @created 2024-01-01
 * @updated 2024-01-15
 * 
 * @example
 * // Usage example:
 * const userService = new UserService(repository, kafkaService);
 * const user = await userService.create({ email: 'test@example.com' });
 * 
 * @see {@link UserController} for API endpoints
 * @see {@link UserEntity} for database model
 */
@Injectable()
export class UserService {
  /**
   * @constructor
   * @param {Repository<User>} userRepository - TypeORM user repository
   * @param {KafkaService} kafkaService - Kafka message service
   * @param {RedisService} redisService - Redis caching service
   */
  constructor(
    @InjectRepository(User)
    private readonly userRepository: Repository<User>,
    private readonly kafkaService: KafkaService,
    private readonly redisService: RedisService,
  ) {}

  /**
   * Create a new user
   * 
   * @async
   * @param {CreateUserDto} createUserDto - User creation data
   * @returns {Promise<User>} Created user object
   * 
   * @throws {ConflictException} If email already exists
   * @throws {BadRequestException} If validation fails
   * 
   * @fires UserCreatedEvent via Kafka
   * @caches User data in Redis
   * 
   * @example
   * const user = await userService.create({
   *   email: 'john@example.com',
   *   password: 'secure123'
   * });
   */
  async create(createUserDto: CreateUserDto): Promise<User> {
    // Implementation...
  }

  /**
   * Find user by email
   * 
   * @async
   * @param {string} email - User email address
   * @returns {Promise<User | null>} User object or null
   * 
   * @cacheStrategy Check cache first, then database
   * @cacheKey `user:email:${email}`
   * @cacheTTL 300 seconds
   */
  async findByEmail(email: string): Promise<User | null> {
    // Implementation...
  }
}
```

## 14. Task Management Workflow

### 14.1 Gitflow Workflow
```
Main Branches:
- main: Production-ready code
- develop: Integration branch

Supporting Branches:
- feature/*: New features
- release/*: Release preparation
- hotfix/*: Critical bug fixes
- bugfix/*: Non-critical bug fixes
```

### 14.2 Development Process
1. Create feature branch from develop
2. Implement feature with TDD
3. Create pull request
4. Code review & automated tests
5. Merge to develop
6. Create release branch
7. Testing & bug fixes
8. Merge to main and tag release
9. Deploy to production

## 15. Conclusion

ระบบ Microservices REST API ที่ออกแบบมานี้มีคุณสมบัติครบถ้วนตามความต้องการ โดยประกอบด้วย:

1. **Backend (NestJS)**: Modular architecture, TypeORM, JWT authentication
2. **Frontend (Next.js + React)**: Server-side rendering, client-side interaction
3. **Database**: PostgreSQL with proper indexing and relations
4. **Caching**: Redis for performance optimization
5. **Message Queue**: Kafka for event-driven architecture
6. **Monitoring**: Grafana + Prometheus for observability
7. **Logging**: ELK stack for centralized logging
8. **CI/CD**: Jenkins pipeline with automated testing
9. **Deployment**: Docker containers on AWS ECS
10. **Testing**: Robot Framework for API testing

ระบบนี้สามารถขยายได้ง่าย สร้างความมั่นใจในความปลอดภัย และรองรับการเติบโตของธุรกิจในอนาคต