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
# Microservices REST API System with Nuxt, NestJS, and React

## 1. การทำงานของ NestJS และโครงสร้าง

### โครงสร้างพื้นฐานของ NestJS
```
src/
├── main.ts                          # Entry point
├── app.module.ts                    # Root module
├── common/                          # Shared resources
│   ├── decorators/                  # Custom decorators
│   ├── filters/                     # Exception filters
│   ├── guards/                      # Authentication guards
│   ├── interceptors/                # Interceptors
│   ├── middleware/                  # Middleware
│   └── pipes/                       # Validation pipes
├── config/                          # Configuration
│   ├── database.config.ts
│   ├── redis.config.ts
│   └── kafka.config.ts
├── modules/                         # Feature modules
│   ├── auth/                        # Authentication module
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── strategies/              # JWT strategies
│   │   ├── guards/                  # Auth guards
│   │   └── dto/                     # Data transfer objects
│   ├── users/                       # User module
│   │   ├── users.module.ts
│   │   ├── users.controller.ts
│   │   ├── users.service.ts
│   │   ├── entities/                # TypeORM entities
│   │   └── dto/
│   ├── products/                    # Product module
│   ├── inventory/                   # Inventory module
│   ├── shipping/                    # Shipping module
│   ├── reports/                     # Reports module
│   └── audit-log/                   # Audit log module
├── database/                        # Database configuration
│   ├── migrations/
│   └── seeds/
└── shared/                          # Shared services
    ├── cache/                       # Redis cache service
    ├── kafka/                       # Kafka producers/consumers
    └── logger/                      # ELK logger service
```

### Data Flow ใน NestJS
```
Client Request → Middleware → Guards → Interceptors → 
Route Handler (Controller) → Service → Repository (ORM) → 
Database → Response → Interceptors → Client
```

### Workflow Authentication with JWT
```
1. User Login → AuthService.validateUser()
2. If valid → JWTService.sign() → Return access_token
3. Subsequent requests → AuthGuard → JWTStrategy.verify()
4. If valid → Attach user to request → Continue to route handler
```

### Example Template Code - NestJS Module

```typescript
// auth.module.ts
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';
import { AuthController } from './auth.controller';
import { AuthService } from './auth.service';
import { JwtStrategy } from './strategies/jwt.strategy';
import { UsersModule } from '../users/users.module';
import { config } from '../config/auth.config';

@Module({
  imports: [
    PassportModule,
    JwtModule.register({
      secret: config.jwtSecret,
      signOptions: { expiresIn: config.jwtExpiresIn },
    }),
    UsersModule,
  ],
  controllers: [AuthController],
  providers: [AuthService, JwtStrategy],
  exports: [AuthService],
})
export class AuthModule {}

// auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import * as bcrypt from 'bcrypt';

@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
  ) {}

  async validateUser(email: string, password: string): Promise<any> {
    const user = await this.usersService.findByEmail(email);
    if (user && await bcrypt.compare(password, user.password)) {
      const { password, ...result } = user;
      return result;
    }
    return null;
  }

  async login(user: any) {
    const payload = { 
      email: user.email, 
      sub: user.id,
      roles: user.roles 
    };
    return {
      access_token: this.jwtService.sign(payload),
      user: {
        id: user.id,
        email: user.email,
        name: user.name,
        roles: user.roles,
      },
    };
  }
}
```

## 2. การทำงานของ Nuxt และโครงสร้าง

### โครงสร้างพื้นฐานของ Nuxt 3
```
nuxt-app/
├── app.vue                          # Root component
├── nuxt.config.ts                   # Nuxt configuration
├── tsconfig.json                    # TypeScript config
├── package.json
├── public/                          # Static files
├── server/                          # Server-side
│   ├── api/                         # API routes
│   ├── middleware/                  # Server middleware
│   └── plugins/                     # Server plugins
├── composables/                     # Composable functions
│   ├── useAuth.ts                   # Authentication composable
│   ├── useApi.ts                    # API client composable
│   └── useValidation.ts             # Validation composable
├── components/                      # Vue components
│   ├── common/                      # Shared components
│   │   ├── Button/
│   │   ├── Input/
│   │   └── Modal/
│   ├── layout/                      # Layout components
│   │   ├── Header.vue
│   │   ├── Sidebar.vue
│   │   └── Footer.vue
│   └── ui/                          # UI components
├── layouts/                         # Page layouts
│   ├── default.vue                  # Default layout
│   └── auth.vue                     # Auth layout
├── pages/                           # Application pages
│   ├── index.vue                    # Home page
│   ├── login.vue                    # Login page
│   ├── dashboard/                   # Dashboard pages
│   │   ├── index.vue
│   │   ├── products/
│   │   ├── inventory/
│   │   ├── shipping/
│   │   └── reports/
│   └── admin/                       # Admin pages
├── plugins/                         # Vue plugins
│   ├── auth.client.ts               # Auth plugin
│   └── api.client.ts                # API plugin
├── stores/                          # Pinia stores
│   ├── auth.store.ts                # Auth store
│   ├── user.store.ts                # User store
│   └── notification.store.ts        # Notification store
├── utils/                           # Utility functions
│   ├── validators/                  # Form validators
│   ├── helpers/                     # Helper functions
│   └── constants/                   # Constants
└── styles/                          # Global styles
    ├── main.scss
    └── variables.scss
```

### Data Flow ใน Nuxt
```
User Action → Vue Component → Composable/Store → 
API Service → Backend (NestJS) → Response → 
Update Store → Re-render Component
```

### Workflow Authentication
```
1. User visits login page → Input credentials
2. Submit → useAuth().login() → API call to NestJS
3. Success → Store JWT in localStorage/cookies
4. Set auth state in Pinia store → Redirect to dashboard
5. Subsequent requests → Inject Authorization header
6. Token expired → useAuth().refreshToken() or redirect to login
```

### Example Template Code - Nuxt Component

```vue
<!-- pages/login.vue -->
<template>
  <div class="login-container">
    <form @submit.prevent="handleSubmit" class="login-form">
      <h2>Login</h2>
      
      <div class="form-group">
        <label for="email">Email</label>
        <input
          id="email"
          v-model="form.email"
          type="email"
          :class="{ 'error': errors.email }"
          @blur="validateField('email')"
        />
        <span v-if="errors.email" class="error-message">
          {{ errors.email }}
        </span>
      </div>

      <div class="form-group">
        <label for="password">Password</label>
        <input
          id="password"
          v-model="form.password"
          type="password"
          :class="{ 'error': errors.password }"
          @blur="validateField('password')"
        />
        <span v-if="errors.password" class="error-message">
          {{ errors.password }}
        </span>
      </div>

      <button type="submit" :disabled="loading">
        {{ loading ? 'Logging in...' : 'Login' }}
      </button>

      <div v-if="error" class="alert alert-error">
        {{ error }}
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useAuth } from '~/composables/useAuth'
import { loginSchema } from '~/utils/validators/auth'

const { login, loading, error } = useAuth()

const form = ref({
  email: '',
  password: ''
})

const errors = ref<Record<string, string>>({})

const validateField = (field: string) => {
  try {
    loginSchema.pick({ [field]: true }).parse({
      [field]: form.value[field as keyof typeof form.value]
    })
    errors.value[field] = ''
  } catch (validationError: any) {
    errors.value[field] = validationError.errors[0].message
  }
}

const handleSubmit = async () => {
  try {
    await loginSchema.parse(form.value)
    await login(form.value)
    // Redirect happens in the composable
  } catch (validationError: any) {
    if (validationError.errors) {
      validationError.errors.forEach((err: any) => {
        errors.value[err.path[0]] = err.message
      })
    }
  }
}
</script>
```

## 3. การทำงานของ React และโครงสร้าง

### โครงสร้างพื้นฐานของ React with TypeScript
```
react-app/
├── public/
│   └── index.html
├── src/
│   ├── main.tsx                     # Entry point
│   ├── App.tsx                      # Root component
│   ├── vite.config.ts               # Vite configuration
│   ├── types/                       # TypeScript types
│   │   ├── user.ts
│   │   ├── product.ts
│   │   └── api.ts
│   ├── api/                         # API clients
│   │   ├── axiosClient.ts           # Axios instance
│   │   ├── authApi.ts               # Auth API
│   │   ├── productApi.ts            # Product API
│   │   └── interceptors/            # Axios interceptors
│   ├── components/                  # Reusable components
│   │   ├── common/
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   └── Modal/
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Layout.tsx
│   │   └── ui/
│   │       ├── LoadingSpinner.tsx
│   │       └── ErrorBoundary.tsx
│   ├── hooks/                       # Custom hooks
│   │   ├── useAuth.ts
│   │   ├── useApi.ts
│   │   └── useLocalStorage.ts
│   ├── context/                     # React Context
│   │   ├── AuthContext.tsx
│   │   └── ThemeContext.tsx
│   ├── pages/                       # Page components
│   │   ├── Login.tsx
│   │   ├── Dashboard.tsx
│   │   ├── Products/
│   │   │   ├── ProductList.tsx
│   │   │   ├── ProductForm.tsx
│   │   │   └── ProductDetail.tsx
│   │   └── Reports/
│   ├── utils/                       # Utility functions
│   │   ├── validators/
│   │   ├── formatters/
│   │   └── constants.ts
│   ├── styles/                      # CSS modules/Styled Components
│   │   ├── global.css
│   │   └── themes/
│   └── routes/                      # React Router configuration
│       ├── AppRoutes.tsx
│       ├── PrivateRoute.tsx
│       └── PublicRoute.tsx
├── package.json
└── tsconfig.json
```

### Data Flow ใน React
```
User Action → Component → Event Handler → 
Custom Hook → API Call → Context/State Update → 
Re-render Components → Display Result
```

### Workflow with React Query
```
1. Component mounts → useQuery() hook
2. Fetch data from API → Cache response
3. Display loading state → Show data
4. User action → useMutation() → Update API
5. Invalidate query → Refetch data → Update UI
```

### Example Template Code - React Component

```tsx
// src/components/products/ProductList.tsx
import React, { useState } from 'react';
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { productApi } from '../../api/productApi';
import { Product } from '../../types/product';
import { Button, Table, Modal, message } from 'antd';
import ProductForm from './ProductForm';

const ProductList: React.FC = () => {
  const [isModalVisible, setIsModalVisible] = useState(false);
  const [editingProduct, setEditingProduct] = useState<Product | null>(null);
  const queryClient = useQueryClient();

  // Fetch products
  const { data: products, isLoading, error } = useQuery({
    queryKey: ['products'],
    queryFn: () => productApi.getAll(),
  });

  // Delete mutation
  const deleteMutation = useMutation({
    mutationFn: (id: number) => productApi.delete(id),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['products'] });
      message.success('Product deleted successfully');
    },
    onError: (error: any) => {
      message.error(`Delete failed: ${error.message}`);
    },
  });

  const columns = [
    {
      title: 'ID',
      dataIndex: 'id',
      key: 'id',
    },
    {
      title: 'Name',
      dataIndex: 'name',
      key: 'name',
    },
    {
      title: 'Price',
      dataIndex: 'price',
      key: 'price',
      render: (price: number) => `$${price.toFixed(2)}`,
    },
    {
      title: 'Stock',
      dataIndex: 'stock',
      key: 'stock',
    },
    {
      title: 'Actions',
      key: 'actions',
      render: (_: any, record: Product) => (
        <div>
          <Button
            type="link"
            onClick={() => {
              setEditingProduct(record);
              setIsModalVisible(true);
            }}
          >
            Edit
          </Button>
          <Button
            type="link"
            danger
            onClick={() => {
              if (window.confirm('Are you sure?')) {
                deleteMutation.mutate(record.id);
              }
            }}
          >
            Delete
          </Button>
        </div>
      ),
    },
  ];

  if (error) {
    return <div>Error loading products</div>;
  }

  return (
    <div>
      <div style={{ marginBottom: 16 }}>
        <Button
          type="primary"
          onClick={() => {
            setEditingProduct(null);
            setIsModalVisible(true);
          }}
        >
          Add Product
        </Button>
      </div>

      <Table
        columns={columns}
        dataSource={products}
        loading={isLoading}
        rowKey="id"
      />

      <Modal
        title={editingProduct ? 'Edit Product' : 'Add Product'}
        open={isModalVisible}
        onCancel={() => setIsModalVisible(false)}
        footer={null}
      >
        <ProductForm
          product={editingProduct}
          onSuccess={() => {
            setIsModalVisible(false);
            queryClient.invalidateQueries({ queryKey: ['products'] });
          }}
        />
      </Modal>
    </div>
  );
};

export default ProductList;
```

## 4. ระบบทั้งหมดและ Integration

### Database Schema Design (PostgreSQL)

```sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    roles JSONB DEFAULT '["user"]',
    is_active BOOLEAN DEFAULT true,
    last_login TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Products table
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category_id INTEGER REFERENCES categories(id),
    unit VARCHAR(50),
    reorder_level INTEGER DEFAULT 10,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Inventory table
CREATE TABLE inventory (
    id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    warehouse_id INTEGER REFERENCES warehouses(id),
    quantity INTEGER NOT NULL DEFAULT 0,
    reserved_quantity INTEGER DEFAULT 0,
    batch_number VARCHAR(100),
    expiry_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(product_id, warehouse_id, batch_number)
);

-- Shipping table
CREATE TABLE shipping (
    id SERIAL PRIMARY KEY,
    order_id VARCHAR(100) UNIQUE NOT NULL,
    tracking_number VARCHAR(100),
    customer_id INTEGER REFERENCES customers(id),
    shipping_address JSONB NOT NULL,
    shipping_method VARCHAR(50),
    status VARCHAR(50) DEFAULT 'pending',
    estimated_delivery DATE,
    actual_delivery DATE,
    shipping_cost DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit log table
CREATE TABLE audit_logs (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(100),
    entity_id INTEGER,
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Microservices Communication with Kafka

```typescript
// kafka-producer.service.ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { Client, ClientKafka, Transport } from '@nestjs/microservices';
import { ConfigService } from '@nestjs/config';

@Injectable()
export class KafkaProducerService implements OnModuleInit {
  @Client({
    transport: Transport.KAFKA,
    options: {
      client: {
        brokers: ['kafka:9092'],
      },
      producer: {
        allowAutoTopicCreation: true,
      },
    },
  })
  client: ClientKafka;

  async onModuleInit() {
    await this.client.connect();
  }

  async sendInventoryUpdate(productId: number, quantity: number) {
    await this.client.emit('inventory.updated', {
      productId,
      quantity,
      timestamp: new Date().toISOString(),
    });
  }

  async sendAuditLog(data: any) {
    await this.client.emit('audit.log', {
      ...data,
      timestamp: new Date().toISOString(),
    });
  }
}

// kafka-consumer.service.ts
import { Controller } from '@nestjs/common';
import { EventPattern, Payload } from '@nestjs/microservices';
import { ElasticsearchService } from '@nestjs/elasticsearch';

@Controller()
export class KafkaConsumerService {
  constructor(private readonly elasticsearchService: ElasticsearchService) {}

  @EventPattern('audit.log')
  async handleAuditLog(@Payload() data: any) {
    // Index to Elasticsearch for EKL
    await this.elasticsearchService.index({
      index: 'audit-logs',
      body: data,
    });

    // Also send to Redis for real-time dashboard
    // ... Redis operations
  }
}
```

## 5. CI/CD Pipeline with Jenkins

### Jenkins Pipeline Script (Jenkinsfile)

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'your-registry.com'
        AWS_EC2_INSTANCE = 'your-ec2-instance'
        AWS_S3_BUCKET = 'your-s3-bucket'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'git@github.com:your-org/your-repo.git'
            }
        }
        
        stage('Install Dependencies') {
            parallel {
                stage('Backend') {
                    steps {
                        dir('backend') {
                            sh 'npm ci'
                        }
                    }
                }
                stage('Frontend') {
                    steps {
                        dir('frontend') {
                            sh 'npm ci'
                        }
                    }
                }
            }
        }
        
        stage('Lint & Test') {
            parallel {
                stage('Backend Tests') {
                    steps {
                        dir('backend') {
                            sh 'npm run lint'
                            sh 'npm run test'
                            sh 'npm run test:e2e'
                        }
                    }
                }
                stage('Frontend Tests') {
                    steps {
                        dir('frontend') {
                            sh 'npm run lint'
                            sh 'npm run test'
                        }
                    }
                }
                stage('Robot Framework API Tests') {
                    steps {
                        dir('tests/robot-framework') {
                            sh 'robot --outputdir reports api_tests.robot'
                        }
                    }
                }
            }
        }
        
        stage('Build & Package') {
            steps {
                dir('backend') {
                    sh 'npm run build'
                }
                dir('frontend') {
                    sh 'npm run build'
                }
                script {
                    docker.build("${DOCKER_REGISTRY}/app:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}") {
                        docker.image("${DOCKER_REGISTRY}/app:${env.BUILD_ID}").push()
                    }
                }
            }
        }
        
        stage('Deploy to AWS') {
            steps {
                script {
                    // Deploy to EC2
                    sshagent(['aws-ec2-key']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ec2-user@${AWS_EC2_INSTANCE} '
                                docker pull ${DOCKER_REGISTRY}/app:${env.BUILD_ID}
                                docker-compose down
                                docker-compose up -d
                            '
                        """
                    }
                    
                    // Upload artifacts to S3
                    sh """
                        aws s3 sync frontend/dist s3://${AWS_S3_BUCKET}/frontend/
                        aws s3 cp backend/dist s3://${AWS_S3_BUCKET}/backend/ --recursive
                    """
                }
            }
        }
        
        stage('Monitoring & Notification') {
            steps {
                // Send notification to Slack
                slackSend(
                    channel: '#deployments',
                    message: "Deployment ${env.BUILD_ID} completed successfully!"
                )
                
                // Trigger Grafana annotation
                sh """
                    curl -X POST http://grafana:3000/api/annotations \
                    -H "Authorization: Bearer ${GRAFANA_TOKEN}" \
                    -H "Content-Type: application/json" \
                    -d '{
                        "text": "Deployment ${env.BUILD_ID}",
                        "tags": ["deployment", "success"]
                    }'
                """
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            slackSend(
                channel: '#deployments',
                message: "Deployment ${env.BUILD_ID} failed! Check Jenkins logs."
            )
        }
    }
}
```

## 6. Docker Compose Configuration

```yaml
version: '3.8'

services:
  # Backend Services
  nestjs-api:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:pass@postgres:5432/mydb
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKER=kafka:9092
    depends_on:
      - postgres
      - redis
      - kafka
    networks:
      - app-network

  # Frontend Services
  nuxt-app:
    build: ./frontend-nuxt
    ports:
      - "3001:3000"
    environment:
      - API_URL=http://nestjs-api:3000
    depends_on:
      - nestjs-api
    networks:
      - app-network

  react-app:
    build: ./frontend-react
    ports:
      - "3002:3000"
    environment:
      - REACT_APP_API_URL=http://nestjs-api:3000
    depends_on:
      - nestjs-api
    networks:
      - app-network

  # Database
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init-db:/docker-entrypoint-initdb.d
    ports:
      - "5432:5432"
    networks:
      - app-network

  # Cache
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"
    networks:
      - app-network

  # Message Queue
  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
      - app-network

  kafka:
    image: confluentinc/cp-kafka:7.4.0
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

  # Monitoring Stack
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - app-network

  kibana:
    image: docker.elastic.co/kibana/kibana:8.10.0
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
    networks:
      - app-network

  logstash:
    image: docker.elastic.co/logstash/logstash:8.10.0
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - "5000:5000"
    depends_on:
      - elasticsearch
    networks:
      - app-network

  grafana:
    image: grafana/grafana:10.0.0
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    ports:
      - "3003:3000"
    networks:
      - app-network

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    ports:
      - "9090:9090"
    networks:
      - app-network

  # Automation
  n8n:
    image: n8nio/n8n
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=password
    ports:
      - "5678:5678"
    volumes:
      - n8n-data:/home/node/.n8n
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  postgres-data:
  redis-data:
  elasticsearch-data:
  grafana-data:
  prometheus-data:
  n8n-data:
```

## 7. Template สำหรับรายงาน

```typescript
// Report Template System
interface ReportTemplate {
  id: string;
  name: string;
  description: string;
  category: 'inventory' | 'sales' | 'shipping' | 'user' | 'audit';
  parameters: ReportParameter[];
  query: string;
  format: 'pdf' | 'excel' | 'csv' | 'html';
  schedule?: ReportSchedule;
  recipients: string[];
}

interface ReportParameter {
  name: string;
  type: 'date' | 'string' | 'number' | 'select';
  required: boolean;
  defaultValue?: any;
  options?: SelectOption[];
}

interface ReportSchedule {
  frequency: 'daily' | 'weekly' | 'monthly' | 'quarterly';
  time: string;
  dayOfWeek?: number;
  dayOfMonth?: number;
}

// Example: Inventory Report Template
const inventoryReportTemplate: ReportTemplate = {
  id: 'inventory-001',
  name: 'Inventory Stock Report',
  description: 'รายงานสต๊อกสินค้าคงคลัง',
  category: 'inventory',
  parameters: [
    {
      name: 'startDate',
      type: 'date',
      required: true,
      defaultValue: new Date().toISOString().split('T')[0]
    },
    {
      name: 'endDate',
      type: 'date',
      required: true,
      defaultValue: new Date().toISOString().split('T')[0]
    },
    {
      name: 'warehouseId',
      type: 'select',
      required: false,
      options: [
        { value: '1', label: 'คลังหลัก' },
        { value: '2', label: 'คลังสาขา 1' },
        { value: '3', label: 'คลังสาขา 2' }
      ]
    }
  ],
  query: `
    SELECT 
      p.id,
      p.sku,
      p.name,
      p.category,
      SUM(i.quantity) as total_quantity,
      SUM(i.reserved_quantity) as reserved_quantity,
      w.name as warehouse_name,
      MIN(i.expiry_date) as earliest_expiry
    FROM products p
    LEFT JOIN inventory i ON p.id = i.product_id
    LEFT JOIN warehouses w ON i.warehouse_id = w.id
    WHERE i.created_at BETWEEN :startDate AND :endDate
    AND (:warehouseId IS NULL OR i.warehouse_id = :warehouseId)
    GROUP BY p.id, p.sku, p.name, p.category, w.name
    ORDER BY total_quantity DESC
  `,
  format: 'excel',
  schedule: {
    frequency: 'daily',
    time: '08:00'
  },
  recipients: ['manager@company.com', 'inventory@company.com']
};
```

## 8. Comment Code Template

```typescript
/**
 * @module ProductService
 * @description Service for product-related business logic
 * @version 1.0.0
 * @author Your Name
 * @created 2024-01-15
 * @updated 2024-01-20
 */

import { Injectable, Inject, Logger } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository, EntityManager } from 'typeorm';
import { Product } from './entities/product.entity';
import { CreateProductDto } from './dto/create-product.dto';
import { UpdateProductDto } from './dto/update-product.dto';
import { CacheService } from '../shared/cache/cache.service';
import { KafkaProducerService } from '../shared/kafka/kafka-producer.service';

/**
 * @class ProductService
 * @classdesc Service handling all product operations including CRUD,
 * inventory updates, and cache management
 * 
 * @example
 * ```typescript
 * const products = await productService.findAll({ page: 1, limit: 10 });
 * ```
 */
@Injectable()
export class ProductService {
  private readonly logger = new Logger(ProductService.name);

  constructor(
    @InjectRepository(Product)
    private readonly productRepository: Repository<Product>,
    @Inject(CacheService)
    private readonly cacheService: CacheService,
    @Inject(KafkaProducerService)
    private readonly kafkaProducer: KafkaProducerService,
    private readonly entityManager: EntityManager,
  ) {}

  /**
   * @method create
   * @description Create a new product with inventory initialization
   * @param {CreateProductDto} createProductDto - Product data
   * @returns {Promise<Product>} Created product with inventory
   * 
   * @throws {ConflictException} If product SKU already exists
   * @throws {BadRequestException} If validation fails
   * 
   * @example
   * ```typescript
   * const product = await productService.create({
   *   sku: 'PROD-001',
   *   name: 'Laptop',
   *   price: 999.99
   * });
   * ```
   */
  async create(createProductDto: CreateProductDto): Promise<Product> {
    try {
      // Check if SKU already exists
      const existingProduct = await this.productRepository.findOne({
        where: { sku: createProductDto.sku }
      });

      if (existingProduct) {
        throw new ConflictException(`Product with SKU ${createProductDto.sku} already exists`);
      }

      // Start transaction
      return await this.entityManager.transaction(async (transactionalEntityManager) => {
        // Create product
        const product = this.productRepository.create(createProductDto);
        const savedProduct = await transactionalEntityManager.save(product);

        // Initialize inventory
        const inventory = transactionalEntityManager.create(Inventory, {
          productId: savedProduct.id,
          quantity: 0,
          warehouseId: 1 // Default warehouse
        });
        await transactionalEntityManager.save(inventory);

        // Clear cache
        await this.cacheService.deletePattern('products:*');

        // Send Kafka event
        await this.kafkaProducer.sendProductCreated({
          productId: savedProduct.id,
          sku: savedProduct.sku,
          timestamp: new Date().toISOString()
        });

        this.logger.log(`Product created: ${savedProduct.sku}`);
        
        return savedProduct;
      });
    } catch (error) {
      this.logger.error(`Failed to create product: ${error.message}`, error.stack);
      throw error;
    }
  }

  /**
   * @method findAll
   * @description Retrieve all products with pagination and filtering
   * @param {Object} options - Pagination and filter options
   * @param {number} options.page - Page number (default: 1)
   * @param {number} options.limit - Items per page (default: 10)
   * @param {string} options.search - Search term
   * @param {string} options.category - Filter by category
   * @returns {Promise<PaginatedResult<Product>>} Paginated products
   * 
   * @example
   * ```typescript
   * const result = await productService.findAll({
   *   page: 1,
   *   limit: 20,
   *   search: 'laptop',
   *   category: 'electronics'
   * });
   * ```
   */
  async findAll(options: {
    page: number;
    limit: number;
    search?: string;
    category?: string;
  }): Promise<PaginatedResult<Product>> {
    const cacheKey = `products:page:${options.page}:limit:${options.limit}:search:${options.search}:category:${options.category}`;
    
    // Try cache first
    const cached = await this.cacheService.get<PaginatedResult<Product>>(cacheKey);
    if (cached) {
      this.logger.debug('Returning cached products');
      return cached;
    }

    // Build query
    const queryBuilder = this.productRepository
      .createQueryBuilder('product')
      .leftJoinAndSelect('product.inventory', 'inventory')
      .leftJoinAndSelect('product.category', 'category');

    if (options.search) {
      queryBuilder.where('product.name LIKE :search OR product.sku LIKE :search', {
        search: `%${options.search}%`
      });
    }

    if (options.category) {
      queryBuilder.andWhere('category.slug = :category', {
        category: options.category
      });
    }

    // Paginate
    const [items, total] = await queryBuilder
      .skip((options.page - 1) * options.limit)
      .take(options.limit)
      .getManyAndCount();

    const result = {
      items,
      total,
      page: options.page,
      limit: options.limit,
      totalPages: Math.ceil(total / options.limit)
    };

    // Cache for 5 minutes
    await this.cacheService.set(cacheKey, result, 300);

    return result;
  }
}
```

## 9. Task Management Integration

```typescript
// task-management.service.ts
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { HttpService } from '@nestjs/axios';
import { firstValueFrom } from 'rxjs';

export interface Task {
  id: string;
  title: string;
  description?: string;
  status: 'todo' | 'in_progress' | 'done';
  priority: 'low' | 'medium' | 'high' | 'urgent';
  assigneeId?: string;
  dueDate?: Date;
  tags: string[];
  metadata: Record<string, any>;
}

@Injectable()
export class TaskManagementService {
  private readonly apiUrl: string;
  private readonly apiKey: string;

  constructor(
    private readonly configService: ConfigService,
    private readonly httpService: HttpService,
  ) {
    this.apiUrl = this.configService.get('TASK_MANAGEMENT_API_URL');
    this.apiKey = this.configService.get('TASK_MANAGEMENT_API_KEY');
  }

  /**
   * Create a deployment task when CI/CD pipeline runs
   */
  async createDeploymentTask(deploymentData: {
    version: string;
    environment: string;
    changes: string[];
    author: string;
  }): Promise<Task> {
    const taskData = {
      title: `Deploy ${deploymentData.version} to ${deploymentData.environment}`,
      description: `Deployment task for version ${deploymentData.version}`,
      status: 'todo',
      priority: 'high',
      tags: ['deployment', deploymentData.environment],
      metadata: {
        version: deploymentData.version,
        environment: deploymentData.environment,
        changes: deploymentData.changes,
        author: deploymentData.author,
        createdAt: new Date().toISOString(),
      },
    };

    const response = await firstValueFrom(
      this.httpService.post<Task>(`${this.apiUrl}/tasks`, taskData, {
        headers: { Authorization: `Bearer ${this.apiKey}` },
      }),
    );

    return response.data;
  }

  /**
   * Create bug report task
   */
  async createBugTask(bugData: {
    title: string;
    description: string;
    severity: 'low' | 'medium' | 'high' | 'critical';
    stepsToReproduce: string[];
    expectedBehavior: string;
    actualBehavior: string;
    reporter: string;
  }): Promise<Task> {
    const taskData = {
      title: `Bug: ${bugData.title}`,
      description: bugData.description,
      status: 'todo',
      priority: bugData.severity === 'critical' ? 'urgent' : 'high',
      tags: ['bug', bugData.severity],
      metadata: {
        severity: bugData.severity,
        stepsToReproduce: bugData.stepsToReproduce,
        expectedBehavior: bugData.expectedBehavior,
        actualBehavior: bugData.actualBehavior,
        reporter: bugData.reporter,
        reportedAt: new Date().toISOString(),
      },
    };

    const response = await firstValueFrom(
      this.httpService.post<Task>(`${this.apiUrl}/tasks`, taskData, {
        headers: { Authorization: `Bearer ${this.apiKey}` },
      }),
    );

    return response.data;
  }
}
```

## 10. Robot Framework API Tests

```robot
*** Settings ***
Documentation     API Tests for Microservices System
Library           RequestsLibrary
Library           Collections
Library           String
Library           OperatingSystem
Library           DatabaseLibrary
Test Setup        Create Session    api    ${BASE_URL}
Test Teardown     Delete All Sessions

*** Variables ***
${BASE_URL}       http://localhost:3000
${DB_HOST}        localhost
${DB_PORT}        5432
${DB_NAME}        testdb
${DB_USER}        testuser
${DB_PASSWORD}    testpass

*** Test Cases ***
User Registration Test
    [Documentation]    Test user registration API
    [Tags]    user    auth    smoke
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    
    ${payload}=    Create Dictionary
    ...    email=test${RANDOM}@example.com
    ...    password=Test@1234
    ...    firstName=Test
    ...    lastName=User
    
    ${response}=    POST    api    /auth/register
    ...    json=${payload}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    201
    Dictionary Should Contain Key    ${response.json()}    access_token
    Dictionary Should Contain Key    ${response.json()}    user
    
    # Verify user in database
    Connect To Database    psycopg2    ${DB_NAME}    ${DB_USER}    ${DB_PASSWORD}    ${DB_HOST}    ${DB_PORT}
    Check If Exists In Database    SELECT id FROM users WHERE email='${payload['email']}'

User Login Test
    [Documentation]    Test user login API
    [Tags]    user    auth    smoke
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    
    ${payload}=    Create Dictionary
    ...    email=admin@example.com
    ...    password=Admin@1234
    
    ${response}=    POST    api    /auth/login
    ...    json=${payload}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    200
    Dictionary Should Contain Key    ${response.json()}    access_token
    ${token}=    Get From Dictionary    ${response.json()}    access_token
    Set Suite Variable    ${ACCESS_TOKEN}    ${token}

Create Product Test
    [Documentation]    Test product creation API
    [Tags]    product    crud
    [Setup]    Get Authentication Token
    
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${sku}=    Generate Random String    8    [LETTERS][NUMBERS]
    ${payload}=    Create Dictionary
    ...    sku=PROD-${sku}
    ...    name=Test Product ${sku}
    ...    description=Test product description
    ...    price=99.99
    ...    category=electronics
    
    ${response}=    POST    api    /products
    ...    json=${payload}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    201
    Dictionary Should Contain Key    ${response.json()}    id
    ${product_id}=    Get From Dictionary    ${response.json()}    id
    Set Test Variable    ${PRODUCT_ID}    ${product_id}
    
    # Verify in database
    Connect To Database    psycopg2    ${DB_NAME}    ${DB_USER}    ${DB_PASSWORD}    ${DB_HOST}    ${DB_PORT}
    Check If Exists In Database    SELECT id FROM products WHERE id=${PRODUCT_ID}

Get Products With Pagination Test
    [Documentation]    Test products pagination API
    [Tags]    product    pagination
    [Setup]    Get Authentication Token
    
    ${headers}=    Create Dictionary
    ...    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${response}=    GET    api    /products
    ...    params=page=1&limit=10
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    200
    Dictionary Should Contain Key    ${response.json()}    items
    Dictionary Should Contain Key    ${response.json()}    total
    Dictionary Should Contain Key    ${response.json()}    page
    Dictionary Should Contain Key    ${response.json()}    limit
    
    ${items}=    Get From Dictionary    ${response.json()}    items
    Length Should Be    ${items}    10

Update Inventory Test
    [Documentation]    Test inventory update API
    [Tags]    inventory    update
    [Setup]    Get Authentication Token
    
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${payload}=    Create Dictionary
    ...    quantity=100
    ...    warehouseId=1
    ...    action=add
    
    ${response}=    PATCH    api    /inventory/${PRODUCT_ID}
    ...    json=${payload}
    ...    headers=${headers}
    
    Should Be Equal As Strings    ${response.status_code}    200
    Dictionary Should Contain Key    ${response.json()}    quantity
    Should Be Equal As Numbers    ${response.json()['quantity']}    100

*** Keywords ***
Get Authentication Token
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    
    ${payload}=    Create Dictionary
    ...    email=admin@example.com
    ...    password=Admin@1234
    
    ${response}=    POST    api    /auth/login
    ...    json=${payload}
    ...    headers=${headers}
    
    ${token}=    Get From Dictionary    ${response.json()}    access_token
    Set Suite Variable    ${ACCESS_TOKEN}    ${token}

Generate Random String
    [Arguments]    ${length}=8    ${chars}=[LETTERS][NUMBERS]
    ${random}=    Generate Random String    ${length}    ${chars}
    [Return]    ${random}
```

## 11. Monitoring and Observability

```yaml
# prometheus/prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']

scrape_configs:
  - job_name: 'nestjs-api'
    static_configs:
      - targets: ['nestjs-api:3000']
    metrics_path: '/metrics'
    
  - job_name: 'postgres'
    static_configs:
      - targets: ['postgres-exporter:9187']
      
  - job_name: 'redis'
    static_configs:
      - targets: ['redis-exporter:9121']
      
  - job_name: 'kafka'
    static_configs:
      - targets: ['kafka-exporter:9308']
      
  - job_name: 'node'
    static_configs:
      - targets: ['nestjs-api:9100']

# alerts.yml
groups:
  - name: api_alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_request_duration_seconds_count{status=~"5.."}[5m]) / rate(http_request_duration_seconds_count[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate on {{ $labels.service }}"
          description: "Error rate is {{ $value }} for service {{ $labels.service }}"
          
      - alert: HighResponseTime
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "High response time on {{ $labels.endpoint }}"
          description: "95th percentile response time is {{ $value }}s"
          
      - alert: ServiceDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Service {{ $labels.job }} is down"
          description: "Service {{ $labels.job }} has been down for more than 1 minute"
```

## 12. Deployment Architecture on AWS

```
AWS Architecture:
├── VPC
│   ├── Public Subnets
│   │   ├── Application Load Balancer
│   │   └── NAT Gateway
│   ├── Private Subnets
│   │   ├── EC2 Auto Scaling Group (NestJS API)
│   │   ├── EC2 Auto Scaling Group (Nuxt Frontend)
│   │   ├── EC2 Auto Scaling Group (React Frontend)
│   │   ├── RDS PostgreSQL (Multi-AZ)
│   │   ├── ElastiCache Redis Cluster
│   │   ├── MSK Kafka Cluster
│   │   └── Elasticsearch Service
│   └── S3 Buckets
│       ├── frontend-assets
│       ├── backup-storage
│       └── log-archive
├── CloudFront (CDN)
├── Route 53 (DNS)
├── CloudWatch (Monitoring)
├── IAM Roles & Policies
└── Secrets Manager (Credentials)
```

## 13. Security Implementation

```typescript
// security.config.ts
import { ConfigService } from '@nestjs/config';
import { HelmetOptions } from 'helmet';
import * as rateLimit from 'express-rate-limit';
import * as slowDown from 'express-slow-down';

export const securityConfig = (configService: ConfigService) => ({
  // Helmet security headers
  helmet: {
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'", "'unsafe-inline'", "'unsafe-eval'"],
        imgSrc: ["'self'", "data:", "https:"],
        connectSrc: ["'self'", "https://api.example.com"],
        fontSrc: ["'self'", "https:", "data:"],
        objectSrc: ["'none'"],
        mediaSrc: ["'self'"],
        frameSrc: ["'none'"],
      },
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true,
    },
  } as HelmetOptions,

  // Rate limiting
  rateLimit: rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP, please try again later.',
    skipSuccessfulRequests: false,
  }),

  // Slow down
  slowDown: slowDown({
    windowMs: 15 * 60 * 1000,
    delayAfter: 100,
    delayMs: (hits) => hits * 100,
  }),

  // CORS
  cors: {
    origin: configService.get('ALLOWED_ORIGINS').split(','),
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
    allowedHeaders: [
      'Content-Type',
      'Authorization',
      'X-Requested-With',
      'Accept',
      'Origin',
    ],
    exposedHeaders: ['Content-Length', 'X-Total-Count'],
    credentials: true,
    maxAge: 86400, // 24 hours
  },

  // JWT configuration
  jwt: {
    secret: configService.get('JWT_SECRET'),
    expiresIn: configService.get('JWT_EXPIRES_IN', '1d'),
    refreshSecret: configService.get('JWT_REFRESH_SECRET'),
    refreshExpiresIn: configService.get('JWT_REFRESH_EXPIRES_IN', '7d'),
  },
});
```

## สรุป

ระบบ Microservices นี้ถูกออกแบบให้:

1. **Scalable**: แต่ละบริการสามารถ scale ได้อิสระกัน
2. **Resilient**: มีระบบคิวข้อความและ caching ช่วยลดภาระ
3. **Observable**: มีระบบ monitoring และ logging ที่ครบวงจร
4. **Secure**: มีการ implement security best practices
5. **Maintainable**: โครงสร้าง code ที่ clean และมี documentation
6. **Automated**: CI/CD pipeline ที่สมบูรณ์
7. **Testable**: มี test coverage ที่ครอบคลุม

ระบบนี้สามารถรองรับธุรกิจขนาดกลางถึงใหญ่ได้อย่างมีประสิทธิภาพ และพร้อมสำหรับการพัฒนาเพิ่มเติมในอนาคต