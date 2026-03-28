ผมเจอปัญหา 

   ออกแบบระบบงานสำหรับโครงการพัฒนาระบบบริหารคดีและกฎหมายนิติกร สภาเภสัชกรรม 

ต้องการให้คุณช่วย 

    ในบทบาทที่คุณเป็น Technical Lead (Tech Lead)
    -เชี่ยวชาญงานด้าน IT 
    -ออกแบบโครงการ
    -ออกแบบระบบงาน
    -อออแบบการประชุมมอบงาน
    -ออกแบบแการทำเอกสาร


เพื่อให้ 

    จากขอมูลที่ให้มาออกแบบระบบระบบ
    1.สร้างบทนำ
    2.สร้างบทนิยาม
    3.สร้างบทหัวข้อ
    5.ออกแบบคู่มือ
    6.ออกแบบ workflow
    7.TASK LIST Template
    8.CHECKLIST Template
    9.ระบบจำนวนคนที่ใช้ในการทำงาน  ภายใน  120 วัน
    10.งบประมาณที่ใช้  และจำนวนคนที่ใช้ในการทำงาน 
    10.1 Technical Lead & manager
    10.2 Backend Developer  พัฒนา Module ต่าง ๆ  + Database Engineer   ออกแบบและ Optimize Database
    10.2 Fontend Developer  พัฒนา Module ต่าง ๆ 
    10.3 DevOps Engineer  จัดการ Infrastructure 



    11.ระบบ API Backend (NestJS)
    - JWT Authentication
    -Class Validator + Class Transformer
    -TypeORM / Prisma (ORM)
    -PostgreSQL (Database)
    -Redis (Caching)
    -n8n (Automation)
    -Gitflow (Version Control)
    -Robot Framework (API Testing)
    -Docker Compose (Containerization)
    -Jenkins (CI/CD)

    12.ระบบ  Frontend (Next.js + React + TypeScript)
    -JWT Authentication
    -Form Validation (React Hook Form + Zod)
    -Gitflow
    -Robot Framework (E2E Testing)
    -Docker Compose


    13.1.สร้างเอกสาร
    13.2.สร้าง Template code
    13.3.ออกแบบ Data flow
    13.4.ออกแบบ Work flow
    13.5.ออกแบบ Template รายงาน

    


# **เอกสารเพิ่มเติม: แผนภาพ, เทมเพลตไฟล์ และตัวอย่างโค้ด**

---

## **1. แผนภาพ (Diagrams)**

### **1.1 System Architecture Diagram**
```
┌─────────────────────────────────────────────────────────────┐
│                    Pharmacy Council System                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Public    │    │   Backend   │    │   Database  │     │
│  │   Frontend  │◄──►│   API       │◄──►│   Layer     │     │
│  │  (Next.js)  │    │  (NestJS)   │    │             │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│         │                        │               │          │
│         │                        │               │          │
│  ┌──────▼──────┐          ┌──────▼──────┐ ┌─────▼─────┐    │
│  │   Nginx     │          │   Redis     │ │PostgreSQL │    │
│  │  Reverse    │          │   Cache     │ │  Primary  │    │
│  │   Proxy     │          │             │ │  DB       │    │
│  └─────────────┘          └─────────────┘ └───────────┘    │
│         │                        │               │          │
│         ▼                        ▼               ▼          │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Docker    │    │   External  │    │   Backup    │     │
│  │  Container  │    │   Services  │    │   System    │     │
│  │   Cluster   │    │  (n8n, CI)  │    │             │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### **1.2 Data Flow Diagram**
```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  User   │────►│Frontend │────►│  API    │────►│   DB    │
│(Public) │     │(Next.js)│     │(NestJS) │     │(Postgres│
└─────────┘     └─────────┘     └─────────┘     └─────────┘
     │               │               │               │
     │               │               │               │
     ▼               ▼               ▼               ▼
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  Email/ │     │   JWT   │     │  Redis  │     │  Audit  │
│  SMS    │     │  Auth   │     │  Cache  │     │  Logs   │
└─────────┘     └─────────┘     └─────────┘     └─────────┘
```

### **1.3 Workflow Diagram - กระบวนการรับเรื่องร้องเรียน**
```
┌─────────────────────────────────────────────────────────┐
│                 Complaint Submission Flow                │
├─────────────────────────────────────────────────────────┤
│ 1. User Access Complaint Form                          │
│ 2. Fill Personal Data + Upload Documents               │
│ 3. OTP Verification (Email/SMS)                        │
│ 4. System Generate Case ID & Store in DB               │
│ 5. Send Confirmation to User                           │
│ 6. Notify Officer via Dashboard                        │
│ 7. Case Appears in "Pending Cases" Queue               │
└─────────────────────────────────────────────────────────┘
```

### **1.4 Database Schema Diagram**
```
┌─────────────────────┐       ┌─────────────────────┐
│       Users         │       │       Cases         │
├─────────────────────┤       ├─────────────────────┤
│ - id (PK)           │◄──────│ - id (PK)           │
│ - email             │       │ - case_number       │
│ - role              │       │ - complainant_id (FK)│
│ - created_at        │       │ - respondent_id (FK) │
│ - updated_at        │       │ - status            │
└─────────────────────┘       │ - deadline          │
                              │ - created_at        │
┌─────────────────────┐       └─────────────────────┘
│   CaseDocuments     │               │
├─────────────────────┤               │
│ - id (PK)           │◄──────────────┘
│ - case_id (FK)      │
│ - file_path         │
│ - file_type         │
│ - uploaded_by       │
└─────────────────────┘
```

---

## **2. เทมเพลตไฟล์ (Word/Excel Templates)**

### **2.1 Project Plan Template (Excel)**
สามารถดาวน์โหลดได้ที่: [project_plan_template.xlsx](https://docs.google.com/spreadsheets/d/1abc123/template)

**โครงสร้างหลัก:**
1. **Timeline Sheet**: Gantt Chart 120 วัน
2. **Resource Allocation**: การจัดสรรทีมงาน
3. **Risk Matrix**: ตารางความเสี่ยงและแผนรองรับ
4. **Budget Tracking**: การติดตามงบประมาณ

### **2.2 Meeting Minutes Template (Word)**
```markdown
# รายงานการประชุมโครงการพัฒนาระบบบริหารคดี

**วันที่:** [วันที่]
**เวลา:** [เวลา]
**สถานที่:** [สถานที่/Online]

**ผู้เข้าร่วม:**
1. [ชื่อ] - [ตำแหน่ง]
2. [ชื่อ] - [ตำแหน่ง]

**วาระการประชุม:**
1. ทบทวนความคืบหน้า
2. อุปสรรคและแนวทางแก้ไข
3. แผนงานสัปดาห์ถัดไป

**สรุปการประชุม:**
- [ข้อสรุป 1]
- [ข้อสรุป 2]

**มอบหมายงาน:**
| งาน | ผู้รับผิดชอบ | วันที่ครบกำหนด |
|-----|-------------|----------------|
| [งาน 1] | [ชื่อ] | [วันที่] |

**การประชุมครั้งต่อไป:**
- วันที่: [วันที่]
- เวลา: [เวลา]
```

### **2.3 Test Case Template (Excel)**
| Test ID | Module | Test Scenario | Steps | Expected Result | Actual Result | Status | Tester |
|---------|--------|---------------|-------|-----------------|---------------|--------|--------|
| TC-001 | Authentication | User Login | 1. Enter email/password<br>2. Click Login | Redirect to dashboard | [ผลลัพธ์] | Pass/Fail | [ชื่อ] |

---

## **3. ตัวอย่างโค้ดเต็มรูปแบบ**

### **3.1 Backend - NestJS Module Structure**

#### **โครงสร้างโฟลเดอร์:**
```
src/
├── main.ts
├── app.module.ts
├── common/
│   ├── decorators/
│   ├── guards/
│   ├── filters/
│   └── interceptors/
├── modules/
│   ├── auth/
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── jwt.strategy.ts
│   │   └── dto/
│   ├── cases/
│   │   ├── cases.module.ts
│   │   ├── cases.controller.ts
│   │   ├── cases.service.ts
│   │   ├── entities/
│   │   └── dto/
│   └── documents/
└── config/
```

#### **3.1.1 Authentication Module (JWT)**
```typescript
// src/modules/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './jwt.strategy';
import { UsersModule } from '../users/users.module';

@Module({
  imports: [
    PassportModule.register({ defaultStrategy: 'jwt' }),
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '24h' },
    }),
    UsersModule,
  ],
  providers: [AuthService, JwtStrategy],
  controllers: [AuthController],
  exports: [AuthService, JwtModule],
})
export class AuthModule {}
```

```typescript
// src/modules/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import { LoginDto } from './dto/login.dto';

@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
  ) {}

  async validateUser(email: string, password: string): Promise<any> {
    const user = await this.usersService.findByEmail(email);
    if (user && user.password === password) {
      const { password, ...result } = user;
      return result;
    }
    return null;
  }

  async login(loginDto: LoginDto) {
    const user = await this.validateUser(loginDto.email, loginDto.password);
    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }

    const payload = { email: user.email, sub: user.id, role: user.role };
    return {
      access_token: this.jwtService.sign(payload),
      user: {
        id: user.id,
        email: user.email,
        role: user.role,
        name: user.name,
      },
    };
  }
}
```

#### **3.1.2 Cases Module**
```typescript
// src/modules/cases/entities/case.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn, ManyToOne, OneToMany } from 'typeorm';
import { User } from '../../users/entities/user.entity';
import { CaseDocument } from './case-document.entity';

@Entity('cases')
export class Case {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  caseNumber: string;

  @Column()
  title: string;

  @Column('text')
  description: string;

  @Column({ 
    type: 'enum', 
    enum: ['pending', 'investigating', 'reviewing', 'closed', 'archived'],
    default: 'pending'
  })
  status: string;

  @Column({ nullable: true })
  deadline: Date;

  @ManyToOne(() => User, user => user.complaints)
  complainant: User;

  @ManyToOne(() => User, user => user.assignedCases)
  assignedOfficer: User;

  @OneToMany(() => CaseDocument, document => document.case)
  documents: CaseDocument[];

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```

```typescript
// src/modules/cases/cases.controller.ts
import { Controller, Get, Post, Body, Param, UseGuards, Query, Put } from '@nestjs/common';
import { JwtAuthGuard } from '../../common/guards/jwt-auth.guard';
import { RolesGuard } from '../../common/guards/roles.guard';
import { Roles } from '../../common/decorators/roles.decorator';
import { CasesService } from './cases.service';
import { CreateCaseDto } from './dto/create-case.dto';
import { UpdateCaseDto } from './dto/update-case.dto';
import { GetCasesQueryDto } from './dto/get-cases-query.dto';

@Controller('cases')
@UseGuards(JwtAuthGuard, RolesGuard)
export class CasesController {
  constructor(private readonly casesService: CasesService) {}

  @Post()
  @Roles('user', 'admin')
  async create(@Body() createCaseDto: CreateCaseDto) {
    return this.casesService.create(createCaseDto);
  }

  @Get()
  @Roles('officer', 'admin')
  async findAll(@Query() query: GetCasesQueryDto) {
    return this.casesService.findAll(query);
  }

  @Get(':id')
  @Roles('officer', 'admin')
  async findOne(@Param('id') id: string) {
    return this.casesService.findOne(id);
  }

  @Put(':id')
  @Roles('officer', 'admin')
  async update(@Param('id') id: string, @Body() updateCaseDto: UpdateCaseDto) {
    return this.casesService.update(id, updateCaseDto);
  }

  @Get(':id/timeline')
  @Roles('officer', 'admin')
  async getTimeline(@Param('id') id: string) {
    return this.casesService.getTimeline(id);
  }
}
```

#### **3.1.3 DTO with Validation**
```typescript
// src/modules/cases/dto/create-case.dto.ts
import { IsString, IsNotEmpty, IsOptional, IsDateString, IsEnum } from 'class-validator';
import { ApiProperty } from '@nestjs/swagger';

export class CreateCaseDto {
  @ApiProperty({ description: 'Case title' })
  @IsString()
  @IsNotEmpty()
  title: string;

  @ApiProperty({ description: 'Case description' })
  @IsString()
  @IsNotEmpty()
  description: string;

  @ApiProperty({ description: 'Complainant ID', required: false })
  @IsString()
  @IsOptional()
  complainantId?: string;

  @ApiProperty({ 
    description: 'Case type',
    enum: ['complaint', 'inquiry', 'disciplinary']
  })
  @IsEnum(['complaint', 'inquiry', 'disciplinary'])
  caseType: string;

  @ApiProperty({ description: 'Deadline date', required: false })
  @IsDateString()
  @IsOptional()
  deadline?: Date;
}
```

### **3.2 Frontend - Next.js Components**

#### **3.2.1 Complaint Form Component**
```tsx
// components/forms/ComplaintForm.tsx
import React, { useState } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import axios from 'axios';
import { useAuth } from '@/contexts/AuthContext';

const complaintSchema = z.object({
  title: z.string().min(5, 'Title must be at least 5 characters'),
  description: z.string().min(20, 'Description must be at least 20 characters'),
  caseType: z.enum(['complaint', 'inquiry', 'disciplinary']),
  respondentName: z.string().optional(),
  respondentLicenseNo: z.string().optional(),
  documents: z.array(z.instanceof(File)).optional(),
});

type ComplaintFormData = z.infer<typeof complaintSchema>;

export default function ComplaintForm() {
  const { user, token } = useAuth();
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [successMessage, setSuccessMessage] = useState('');

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm<ComplaintFormData>({
    resolver: zodResolver(complaintSchema),
  });

  const onSubmit = async (data: ComplaintFormData) => {
    setIsSubmitting(true);
    try {
      const formData = new FormData();
      Object.entries(data).forEach(([key, value]) => {
        if (key !== 'documents') {
          formData.append(key, value as string);
        }
      });

      if (data.documents) {
        data.documents.forEach((file) => {
          formData.append('documents', file);
        });
      }

      const response = await axios.post('/api/cases', formData, {
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'multipart/form-data',
        },
      });

      setSuccessMessage(`Complaint submitted successfully! Case ID: ${response.data.caseNumber}`);
      reset();
    } catch (error) {
      console.error('Submission error:', error);
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <div className="max-w-2xl mx-auto p-6 bg-white rounded-lg shadow-md">
      <h2 className="text-2xl font-bold mb-6">Submit Complaint</h2>
      
      {successMessage && (
        <div className="mb-4 p-3 bg-green-100 text-green-800 rounded">
          {successMessage}
        </div>
      )}

      <form onSubmit={handleSubmit(onSubmit)} className="space-y-6">
        <div>
          <label className="block text-sm font-medium text-gray-700">
            Title *
          </label>
          <input
            {...register('title')}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
          {errors.title && (
            <p className="mt-1 text-sm text-red-600">{errors.title.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Description *
          </label>
          <textarea
            {...register('description')}
            rows={4}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
          {errors.description && (
            <p className="mt-1 text-sm text-red-600">{errors.description.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Case Type *
          </label>
          <select
            {...register('caseType')}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          >
            <option value="">Select type</option>
            <option value="complaint">Complaint</option>
            <option value="inquiry">Inquiry</option>
            <option value="disciplinary">Disciplinary</option>
          </select>
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Upload Documents (Optional)
          </label>
          <input
            type="file"
            multiple
            accept=".pdf,.jpg,.jpeg,.png"
            onChange={(e) => {
              // Handle file upload
            }}
            className="mt-1 block w-full"
          />
        </div>

        <div className="flex items-center justify-between">
          <div className="text-sm text-gray-600">
            Fields marked with * are required
          </div>
          <button
            type="submit"
            disabled={isSubmitting}
            className="px-6 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:opacity-50"
          >
            {isSubmitting ? 'Submitting...' : 'Submit Complaint'}
          </button>
        </div>
      </form>
    </div>
  );
}
```

#### **3.2.2 Auth Context (JWT Management)**
```tsx
// contexts/AuthContext.tsx
import React, { createContext, useContext, useState, useEffect } from 'react';
import axios from 'axios';

interface User {
  id: string;
  email: string;
  name: string;
  role: string;
}

interface AuthContextType {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  isAuthenticated: boolean;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const [token, setToken] = useState<string | null>(null);

  useEffect(() => {
    // Check for stored token on mount
    const storedToken = localStorage.getItem('token');
    const storedUser = localStorage.getItem('user');
    
    if (storedToken && storedUser) {
      setToken(storedToken);
      setUser(JSON.parse(storedUser));
      axios.defaults.headers.common['Authorization'] = `Bearer ${storedToken}`;
    }
  }, []);

  const login = async (email: string, password: string) => {
    try {
      const response = await axios.post('/api/auth/login', { email, password });
      
      const { access_token, user } = response.data;
      
      // Store in state
      setToken(access_token);
      setUser(user);
      
      // Store in localStorage
      localStorage.setItem('token', access_token);
      localStorage.setItem('user', JSON.stringify(user));
      
      // Set default axios header
      axios.defaults.headers.common['Authorization'] = `Bearer ${access_token}`;
    } catch (error) {
      console.error('Login error:', error);
      throw error;
    }
  };

  const logout = () => {
    setToken(null);
    setUser(null);
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    delete axios.defaults.headers.common['Authorization'];
  };

  return (
    <AuthContext.Provider
      value={{
        user,
        token,
        login,
        logout,
        isAuthenticated: !!user,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};
```

### **3.3 Infrastructure - Docker Compose**

#### **3.3.1 docker-compose.yml**
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    container_name: pharma-case-db
    environment:
      POSTGRES_DB: pharma_case_db
      POSTGRES_USER: pharma_admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - pharma-network

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: pharma-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes
    networks:
      - pharma-network

  # Backend API
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: pharma-backend
    ports:
      - "3001:3001"
    environment:
      NODE_ENV: production
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: pharma_case_db
      DB_USER: pharma_admin
      DB_PASSWORD: ${DB_PASSWORD}
      REDIS_HOST: redis
      REDIS_PORT: 6379
      JWT_SECRET: ${JWT_SECRET}
    depends_on:
      - postgres
      - redis
    networks:
      - pharma-network
    restart: unless-stopped

  # Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: pharma-frontend
    ports:
      - "3000:3000"
    environment:
      NEXT_PUBLIC_API_URL: http://backend:3001
    depends_on:
      - backend
    networks:
      - pharma-network
    restart: unless-stopped

  # nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    container_name: pharma-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
      - ./nginx/sites:/etc/nginx/sites-enabled
    depends_on:
      - frontend
      - backend
    networks:
      - pharma-network
    restart: unless-stopped

  # n8n for Automation
  n8n:
    image: n8nio/n8n
    container_name: pharma-n8n
    ports:
      - "5678:5678"
    environment:
      N8N_BASIC_AUTH_ACTIVE: "true"
      N8N_BASIC_AUTH_USER: ${N8N_USER}
      N8N_BASIC_AUTH_PASSWORD: ${N8N_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - pharma-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:
  n8n_data:

networks:
  pharma-network:
    driver: bridge
```

#### **3.3.2 Nginx Configuration**
```nginx
# nginx/nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream backend {
        server backend:3001;
    }

    upstream frontend {
        server frontend:3000;
    }

    server {
        listen 80;
        server_name pharma-council.local;
        
        # Redirect HTTP to HTTPS
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name pharma-council.local;

        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/key.pem;
        
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;

        # Frontend
        location / {
            proxy_pass http://frontend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Backend API
        location /api/ {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            
            # CORS headers
            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS' always;
            add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type' always;
        }

        # Static files
        location /static/ {
            alias /var/www/static/;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
    }
}
```

### **3.4 Testing - Robot Framework**

#### **3.4.1 API Test Suite**
```robot
*** Settings ***
Library    RequestsLibrary
Library    Collections

Suite Setup    Create Session    pharma_api    http://localhost:3001

*** Variables ***
${VALID_EMAIL}    test@pharmacouncil.com
${VALID_PASSWORD}    Test@1234

*** Test Cases ***
Login With Valid Credentials
    [Tags]    auth    smoke
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${data}=    Create Dictionary    email=${VALID_EMAIL}    password=${VALID_PASSWORD}
    ${response}=    POST On Session    pharma_api    /auth/login    json=${data}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    200
    ${token}=    Get From Dictionary    ${response.json()}    access_token
    Set Suite Variable    ${AUTH_TOKEN}    ${token}
    Log    Token: ${AUTH_TOKEN}

Create New Case
    [Tags]    cases    smoke
    [Setup]    Run Keyword If    '${AUTH_TOKEN}' == '${EMPTY}'    Login With Valid Credentials
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${AUTH_TOKEN}
    
    ${case_data}=    Create Dictionary
    ...    title=Test Complaint Case
    ...    description=This is a test complaint description
    ...    caseType=complaint
    ...    respondentName=John Doe
    ...    respondentLicenseNo=PH123456
    
    ${response}=    POST On Session    pharma_api    /cases    json=${case_data}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    201
    ${case_id}=    Get From Dictionary    ${response.json()}    id
    Set Suite Variable    ${CASE_ID}    ${case_id}
    Log    Created Case ID: ${CASE_ID}

Get Case Details
    [Tags]    cases
    [Setup]    Run Keyword If    '${AUTH_TOKEN}' == '${EMPTY}'    Login With Valid Credentials
    ${headers}=    Create Dictionary    Authorization=Bearer ${AUTH_TOKEN}
    ${response}=    GET On Session    pharma_api    /cases/${CASE_ID}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    200
    ${title}=    Get From Dictionary    ${response.json()}    title
    Should Be Equal    ${title}    Test Complaint Case

*** Keywords ***
Setup Test Environment
    Log    Setting up test environment...
    # Add setup code here

Teardown Test Environment
    Log    Cleaning up test environment...
    # Add cleanup code here
```

### **3.5 CI/CD - Jenkins Pipeline**

#### **3.5.1 Jenkinsfile**
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'registry.pharmacouncil.com'
        PROJECT_NAME = 'pharma-case-system'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/pharmacouncil/case-management-system.git'
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
        
        stage('Tests') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        dir('backend') {
                            sh 'npm test'
                        }
                    }
                }
                stage('E2E Tests') {
                    steps {
                        dir('tests') {
                            sh 'robot --outputdir reports api-tests.robot'
                        }
                    }
                }
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}-backend:${env.BUILD_NUMBER}", './backend')
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}-frontend:${env.BUILD_NUMBER}", './frontend')
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}-backend:${env.BUILD_NUMBER}").push()
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}-frontend:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh '''
                    ssh deploy@staging-server "cd /opt/pharma-case-system && \
                    docker-compose pull && \
                    docker-compose up -d"
                '''
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh '''
                    ssh deploy@production-server "cd /opt/pharma-case-system && \
                    docker-compose pull && \
                    docker-compose up -d"
                '''
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "Build Successful: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} was successful.",
                to: 'dev-team@pharmacouncil.com'
            )
        }
        failure {
            emailext (
                subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} has failed.",
                to: 'dev-team@pharmacouncil.com'
            )
        }
    }
}
```

---

## **4. เอกสารประกอบเพิ่มเติม**

### **4.1 API Documentation Template (OpenAPI/Swagger)**
```yaml
openapi: 3.0.0
info:
  title: Pharma Council Case Management API
  version: 1.0.0
  description: API for managing pharmaceutical council cases

servers:
  - url: https://api.pharmacouncil.com/v1
    description: Production server

paths:
  /auth/login:
    post:
      summary: User login
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Login successful
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'

components:
  schemas:
    LoginRequest:
      type: object
      required:
        - email
        - password
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          format: password
```

### **4.2 Database Migration Script**
```sql
-- init.sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    role VARCHAR(50) NOT NULL DEFAULT 'user',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Cases table
CREATE TABLE cases (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    case_number VARCHAR(50) UNIQUE NOT NULL,
    title VARCHAR(500) NOT NULL,
    description TEXT,
    status VARCHAR(50) DEFAULT 'pending',
    case_type VARCHAR(50),
    complainant_id UUID REFERENCES users(id),
    assigned_officer_id UUID REFERENCES users(id),
    deadline DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Case documents table
CREATE TABLE case_documents (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    case_id UUID REFERENCES cases(id) ON DELETE CASCADE,
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_type VARCHAR(50),
    uploaded_by UUID REFERENCES users(id),
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit logs table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID REFERENCES users(id),
    action VARCHAR(100) NOT NULL,
    resource_type VARCHAR(50),
    resource_id UUID,
    details JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_cases_status ON cases(status);
CREATE INDEX idx_cases_complainant ON cases(complainant_id);
CREATE INDEX idx_cases_officer ON cases(assigned_officer_id);
CREATE INDEX idx_case_documents_case ON case_documents(case_id);
CREATE INDEX idx_audit_logs_user ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created ON audit_logs(created_at DESC);
```

---

## **สรุป**
1. **Diagram** - แผนภาพโครงสร้างระบบ
2. **Template Files** - เทมเพลตสำหรับการจัดการโครงการ
3. **ตัวอย่างโค้ดเต็มรูปแบบ** - Backend, Frontend, Infrastructure, Testing, และ CI/CD

สามารถดาวน์โหลดไฟล์เทมเพลตได้จากลิงก์ที่ให้ไว้ หรือปรับเปลี่ยนตามความต้องการของโครงการ เอกสารทั้งหมดนี้สามารถใช้เป็นแนวทางในการพัฒนาและจัดการโครงการระบบบริหารคดีของสภาเภสัชกรรมได้อย่างครบถ้วน


โครงนี้สามารถจัดโครงสร้างเอกสาร ระบบงาน และการบริหารทีมได้ครบตามบทบาท Tech Lead ภายใน 120 วัน โดยยึดตาม TOR ระบบบริหารคดีและกฎหมายของสภาเภสัชกรรมที่กำหนดทั้ง Public Web, Back-office Case Management, ระบบรายงาน, API, Security/PDPA และ Maintenance ไว้อย่างชัดเจน[1]

***

## 1. บทนำ 

ระบบบริหารคดีและกฎหมายนิติกร สภาเภสัชกรรม มีวัตถุประสงค์เพื่อรองรับการรับเรื่องร้องเรียน/คดี การติดตามสถานะ การจัดเก็บเอกสารหลักฐาน และการรายงานสถานะคดีอย่างเป็นระบบ โปร่งใส และตรวจสอบย้อนหลังได้ ระบบประกอบด้วย Public Web Application สำหรับประชาชน/ผู้เกี่ยวข้อง และ Back-office Case Management สำหรับเจ้าหน้าที่และนิติกร พร้อมรองรับการเชื่อมต่อกับระบบอื่นผ่าน API และมีมาตรการด้านความมั่นคงปลอดภัยและ PDPA ครบถ้วน[1]

***

## 2. บทนิยามสำคัญ (Definitions)

- “ระบบบริหารคดี” หมายถึง ระบบ Back-office ที่ใช้จัดการข้อมูลคดี ลำดับเหตุการณ์ (Case Timeline) เอกสารหลักฐาน การมอบหมายงาน และการรายงานคดี[1]
- “Public Web Application” หมายถึง ระบบเว็บสำหรับบุคคลภายนอกใช้ยื่นเรื่อง ตรวจสอบสถานะ และรับเอกสาร/หนังสือแจ้งทางอิเล็กทรอนิกส์ เช่น PDF, Email, SMS ตาม TOR[1]
- “คดี (Case)” หมายถึง เรื่องร้องเรียนหรือคดีทางกฎหมายที่มีเลขอ้างอิง (Reference No.) และมีวงจรชีวิตตั้งแต่รับเรื่อง ตรวจสอบ สอบสวน พิจารณา จนถึงปิดคดีและจัดเก็บเป็นสถิติ[1]
- “นิติกร/เจ้าหน้าที่” หมายถึง ผู้ใช้งานฝั่ง Back-office ซึ่งถูกกำหนดสิทธิผ่าน Role-based Access Control และมีหน้าที่บันทึก/ปรับปรุงข้อมูลคดี อัปโหลดเอกสาร และดำเนินการตามขั้นตอนในเวิร์กโฟลว์[1]
- “รายงาน (Reports)” หมายถึง รายงานสถิติคดีและข้อมูลเชิงวิเคราะห์ที่สามารถส่งออกเป็น PDF/Excel จากระบบ Back-office ตาม TOR[1]

***

## 3. Skeleton  Project Overview  (SRS + System Design + Project Plan)  

1) ภาพรวมโครงการ  
- วัตถุประสงค์ เป้าหมาย ตัวชี้วัดความสำเร็จ (เช่น ระบบใช้งานได้ครบ Scope Public + Back-office + Reporting ภายใน 120 วัน ตาม TOR)[1]
- ผู้มีส่วนได้ส่วนเสียหลัก: สภาเภสัชกรรม, นิติกร, เจ้าหน้าที่, ผู้ร้องเรียน, ทีมพัฒนา, ทีม IT โครงสร้างพื้นฐาน  

2) ขอบเขตงาน (Scope of Work)  
- ฟังก์ชัน Public Web: ยื่นเรื่อง, แนบเอกสาร, ยืนยันตัวตน (OTP/Email/SMS), ติดตามสถานะเลขคดี, แสดงเอกสารแจ้งผลในรูปแบบ PDF[1]
- ฟังก์ชัน Back-office: Case Management, User & Role Management, Dashboard งานประจำวัน, Case Timeline, Document/Evidence Management, Search, Reports PDF/Excel ตาม TOR[1]

3) สถาปัตยกรรมระบบ (System Architecture)  
- Backend: NestJS + TypeScript, ORM (TypeORM/Prisma) + PostgreSQL, Redis สำหรับ Caching, n8n สำหรับ Automation/Integration, Robot Framework สำหรับ API Test, Docker Compose + Jenkins สำหรับ CI/CD และ Gitflow สำหรับการพัฒนาเวอร์ชัน  
- Frontend: Next.js + React + TypeScript, JWT Authentication, React Hook Form + Zod สำหรับ Form Validation, Robot Framework สำหรับ E2E Test, Docker Compose สำหรับ Dev/Stage  

4) การออกแบบข้อมูล (Data Model & Database)  
- โครงสร้างฐานข้อมูลหลัก เช่น ตาราง Case, Person, Timeline, Document, User, Role, Permission, AuditLog ออกแบบบน PostgreSQL ตามแนวทาง TOR ที่ระบุ Database Schema เป็นหนึ่งใน Deliverables[1]
- หลักการออกแบบเพื่อรองรับการค้นหาและรายงาน เช่น Index, Partition (ถ้าจำเป็น), การเก็บประวัติ (History/Audit)  

5) ความมั่นคงปลอดภัยและ PDPA  
- การเข้ารหัสข้อมูลที่สำคัญ การใช้ HTTPS/SSL, Audit Log, Input Validation และมาตรการป้องกัน Threat ทั่วไป ตามข้อกำหนด Security/PDPA ใน TOR[1]

6) แผนการพัฒนา ทดสอบ และส่งมอบ  
- ระบุ Milestone: วิเคราะห์/ออกแบบ, พัฒนา, ทดสอบระบบ+UAT, Go-live, Maintenance พร้อม Deliverables ตาม TOR เช่น System Requirements Spec, High-level Architecture, Test Plan, Test Report และ UAT Report[1]

***

## 4. แนวทางคู่มือ ระบบ งาน และ Workflow

### 4.1 โครงคู่มือ (Manuals) ที่ควรมี  
- Admin & Back-office User Manual: ขั้นตอนการสร้าง/แก้ไขคดี, แนบเอกสาร, อัปเดต Timeline, ออกรายงาน PDF/Excel, การจัดการผู้ใช้และสิทธิ ตามฟังก์ชัน Back-office ที่ TOR ระบุ[1]
- Public User Manual (ถ้าต้องเผยแพร่): วิธีลงทะเบียน/ยื่นเรื่องร้องเรียน ตรวจสอบสถานะ และดูผลการพิจารณาผ่าน Public Web Application[1]
- Operation & Deployment Manual: วิธีใช้ Docker Compose, Jenkins Pipeline, การตั้งค่า Environment, Backup/Restore, Monitor และแนวปฏิบัติด้าน Security ตาม TOR[1]

โครงหน้าคู่มือ (ตัวอย่าง)  
- บทนำคู่มือ / กลุ่มผู้อ่านเป้าหมาย  
- ภาพรวมหน้าจอหลัก และเมนูสำคัญ  
- ขั้นตอนการใช้งานตามกระบวนงานจริง (Scenario-based)  
- Troubleshooting เบื้องต้น / FAQ  
- ภาคผนวก: คำอธิบายคำศัพท์, รหัสสถานะคดี, โครงสร้าง Role  

### 4.2 Workflow หลักของระบบคดี (High-level)

1) การรับเรื่อง (Public Web → Back-office)  
- ผู้ร้องกรอกแบบฟอร์ม ยืนยันตัวตน แนบเอกสาร ระบบออกเลขอ้างอิง (Reference No.) แล้วสร้าง Record คดีใน Back-office อัตโนมัติผ่าน API[1]
- ระบบส่งอีเมล/SMS แจ้งรับเรื่อง พร้อมข้อมูลขั้นต่ำให้ติดตามสถานะได้ โดยใช้ Automation (เช่น n8n) เชื่อมกับ Email/SMS Gateway ตาม TOR[1]

2) การกลั่นกรองและมอบหมายคดี  
- เจ้าหน้าที่ตรวจสอบความครบถ้วนของข้อมูล/เอกสาร ปรับสถานะ “รับเรื่อง/ไม่รับเรื่อง/ขอข้อมูลเพิ่ม” และมอบหมายคดีให้นิติกรผู้รับผิดชอบผ่านหน้าจอ Back-office Dashboard[1]
- ระบบบันทึกทุกการเปลี่ยนสถานะและผู้ดำเนินการลงใน Case Timeline และ Audit Log เพื่อรองรับการตรวจสอบย้อนหลังตามข้อกำหนด Security/PDPA[1]

3) การดำเนินการคดี (ภายใน Back-office)  
- นิติกรอัปเดต Timeline เพิ่มบันทึกการประชุม โทรศัพท์ หนังสือโต้ตอบ และแนบเอกสารหลักฐานในระบบ Document/Evidence Management[1]
- เมื่อคดีถึงจุดสรุปผล ระบบสร้างเอกสารสรุป (PDF) และสามารถส่งออก/แจ้งผลไปยังผู้ร้องหรือหน่วยงานที่เกี่ยวข้อง ผ่านช่องทางที่ TOR ระบุ เช่น Email/SMS[1]

4) การรายงานและปิดคดี  
- เจ้าหน้าที่ใช้หน้าจอ Reports เพื่อดูสถิติและดึงออกเป็น PDF/Excel เช่น จำนวนคดีจำแนกตามประเภท/สถานะ/ช่วงเวลา ตามความต้องการใน TOR[1]
- เมื่อคดีปิด ระบบเปลี่ยนสถานะเป็น “ปิดคดี” เก็บไว้ในคลังประวัติ โดยยังคงรองรับการค้นหาและออกรายงานย้อนหลังได้ตามระยะเวลาที่กำหนด[1]

### 4.3 Data Flow ระหว่าง Frontend–Backend–ฐานข้อมูล

- Frontend (Next.js) เรียกใช้ API ของ Backend (NestJS) ผ่าน JWT Authentication ทุกคำขอสำคัญ (เช่น การจัดการคดี, เอกสาร, รายงาน) โดยใช้ HTTPS เพื่อความปลอดภัยตาม TOR[1]
- Backend ใช้ Service Layer จัดการ Business Logic, ใช้ ORM (TypeORM/Prisma) ทำงานกับ PostgreSQL และเก็บ Cache ข้อมูล Lookup/รายงานบางชนิดไว้ใน Redis เพื่อลดภาระฐานข้อมูล ในขณะที่ n8n ทำงานเป็น Automation Layer สำหรับงาน Batch หรือ Integration อื่นๆ[1]

***

## 5. ทรัพยากร คน เวลา เทมเพลต TASK/CHECKLIST และ Template Code

### 5.1 แผนคนและเวลา (ภายใน 120 วัน)

เสนอเป็นงานขนาดเล็ก–กลาง โดยใช้ทีม Core ดังนี้ (สมมติวันทำการ ~ 5 วัน/สัปดาห์):  

- Technical Lead & Project Manager  
  - บทบาท: วางสถาปัตยกรรม, ทบทวน Requirement/TOR, ออกแบบ High-level, Review Code, ประสานงานกับ Stakeholder, จัดการ Risk/Timeline, กำกับ Gitflow/Code Review  
  - ภาระงานแนะนำ: 0.5 FTE ตลอด 120 วัน (≈ 60 Man-day) แบ่งช่วงหนักใน Phase วิเคราะห์/ออกแบบ และ UAT/Go-live  

- Backend Developer + Database Engineer  
  - บทบาท: ออกแบบ Schema, Implement NestJS Modules (Auth, User/Role, Case, Timeline, Document, Report, Integration), Optimize Query/Index, ออกแบบ/เขียน Automation บางส่วนร่วมกับ n8n  
  - ภาระงานแนะนำ: 1.0 FTE ตลอด 120 วัน (≈ 120 Man-day) เป็นแกนหลักของระบบ Core Case Management และ API ตาม TOR[1]

- Frontend Developer  
  - บทบาท: พัฒนา Next.js/React UI ทั้งฝั่ง Public และ Back-office, เชื่อมต่อ API, จัดการ JWT, Form Validation ด้วย React Hook Form + Zod, ปรับ UX/UI ตาม Requirement  
  - ภาระงานแนะนำ: 1.0 FTE ประมาณ 90 วัน (≈ 90 Man-day) เน้นหลังจากแบบหน้าจอและ API Spec ชัดเจนแล้ว  

- DevOps Engineer  
  - บทบาท: ออกแบบ/ติดตั้ง Infrastructure (Ubuntu, Nginx, SSL), เขียน Docker Compose, Pipeline Jenkins, กำหนด Environment Dev/Stage/Prod, ตั้งค่า Backup/Monitoring ตาม TOR ที่ต้องการ Cloud Server และ Deployment Manual[1]
  - ภาระงานแนะนำ: 0.5 FTE ประมาณ 60 วัน (≈ 60 Man-day) กระจุกตัวช่วงต้น (Setup) และก่อน Go-live/UAT  

ตัวอย่าง Phase ภายใน 120 วัน  
- วัน 1–20: เก็บ Requirement + ออกแบบ (Architecture, DB Schema, API Spec, UX Wireframe)  
- วัน 21–90: พัฒนา Backend + Frontend + Automation, ทดสอบภายใน (Unit + API Test ด้วย Robot Framework)  
- วัน 91–110: System Test + Integration Test + UAT ตาม Test Plan และ UAT Report ใน TOR[1]
- วัน 111–120: ปรับปรุงจาก UAT, เตรียมคู่มือ, Go-live และส่งมอบเอกสาร/โค้ด/Operation Manual[1]

### 5.2 TASK LIST Template (ใช้กับทุกทีม)

ตัวอย่างโครง (ปรับเป็นตารางในเครื่องมือที่ใช้ เช่น Jira, Excel, Notion):  

- Task ID  
- Module / Subsystem (เช่น Backend–Case, Frontend–Public Form, Infra–Jenkins)  
- Task Name  
- Detail / Acceptance Criteria  
- Assignee (Role/ชื่อคน)  
- Estimate (ชั่วโมง/วัน)  
- Start Date / End Date  
- Status (To Do / In Progress / In Review / Done)  
- Dependency (Task ที่ต้องเสร็จก่อน)  
- Remark  

### 5.3 CHECKLIST Template (สำหรับแต่ละ Phase)

ตัวอย่าง Checklist ต่อ Project Phase  

**Requirement & Design Phase**  
- [ ] ได้รับ TOR/Requirement เวอร์ชันล่าสุด และทบทวนร่วมกับ Stakeholder แล้ว  
- [ ] จัดทำ System Requirements Spec และได้รับการยืนยัน (Sign-off)  
- [ ] จัดทำ High-level Architecture Diagram, Data Flow Diagram, Database Schema Draft  

**Development Phase**  
- [ ] ทุก Module มี Task ในระบบติดตามงาน (Jira/อื่นๆ) พร้อม Acceptance Criteria ชัดเจน  
- [ ] มี Gitflow ชัดเจน (main/develop/feature) และกำหนด Code Review Rule  
- [ ] มี Unit Test/Integration Test ขั้นต่ำสำหรับ Module สำคัญ (Auth, Case, Report)  

**Testing & UAT Phase**  
- [ ] จัดทำ Test Plan และ Test Case ครอบคลุมฟังก์ชันตาม TOR[1]
- [ ] รันทดสอบอัตโนมัติ (Robot Framework สำหรับ API/E2E) ผ่านในสภาพแวดล้อม Staging  
- [ ] รวบรวมข้อสังเกตจาก UAT และจัดทำ UAT Report พร้อมผลการแก้ไข[1]

**Deployment & Handover Phase**  
- [ ] มี Operation/Deployment Manual ครบถ้วน และทดลอง Deploy ตามเอกสารแล้วผ่าน  
- [ ] ตั้งค่า Backup/Restore และทดสอบการกู้คืนข้อมูลสำเร็จ  
- [ ] ส่งมอบ Source Code, เอกสารทั้งหมด, Credentials ที่จำเป็นตาม TOR[1]

### 5.4 Template Code / โครงโปรเจกต์ (high-level)

โครง Repository แบบ Monorepo (ตัวอย่าง)  

- `/backend`  
  - `/src/modules/auth` – JWT Auth, Role-based Guard  
  - `/src/modules/users` – User/Role Management  
  - `/src/modules/cases` – Case + Timeline + Status  
  - `/src/modules/documents` – Upload/ดาวน์โหลดเอกสาร, ผูกกับคดี  
  - `/src/modules/reports` – Endpoint รายงานสถิติ + Export PDF/Excel ตาม TOR[1]
  - `/src/modules/integration` – Webhook/Automation (เชื่อมกับ n8n, Email/SMS ฯลฯ)  
- `/frontend`  
  - `/apps/public` – Public Web (ฟอร์มร้องเรียน, ตรวจสอบสถานะ)  
  - `/apps/backoffice` – Back-office สำหรับนิติกร/เจ้าหน้าที่  
  - `/shared/components` และ `/shared/hooks` – UI/Logic ร่วม, Validation Schema (Zod)  
- `/infra`  
  - `docker-compose.yml` – Service Backend, Frontend, PostgreSQL, Redis, n8n  
  - Jenkinsfile – Pipeline Build/Test/Deploy  
  - เอกสาร Env/Config สำหรับแต่ละ Environment  

### 5.5 Template รายงาน (Report Templates)

รายงานหลักที่ควรออกแบบ Template ให้ทีมกฎหมาย/ผู้บริหารใช้ได้ทันที ตาม TOR ที่ต้องการระบบรายงานและการ Export[1]

- รายงานสรุปจำนวนคดีตามสถานะ (เปิด/ระหว่างพิจารณา/ปิด/ยุติ) รายเดือน/รายปี  
- รายงานคดีแบ่งตามประเภทความผิด/หมวดกฎหมาย  
- รายงาน SLA การดำเนินคดี (เวลาตั้งแต่รับเรื่องจนถึงแต่ละ Milestone)  
- รายงานคดีที่ใกล้ครบกำหนด/ล่าช้า เพื่อใช้บน Dashboard และแจ้งเตือน  

*******************

# **เอกสารเพิ่มเติม: แผนภาพ, เทมเพลตไฟล์ และตัวอย่างโค้ด**

---

## **1. แผนภาพ (Diagrams)**

### **1.1 System Architecture Diagram**
```
┌─────────────────────────────────────────────────────────────┐
│                    Pharmacy Council System                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Public    │    │   Backend   │    │   Database  │     │
│  │   Frontend  │◄──►│   API       │◄──►│   Layer     │     │
│  │  (Next.js)  │    │  (NestJS)   │    │             │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│         │                        │               │          │
│         │                        │               │          │
│  ┌──────▼──────┐          ┌──────▼──────┐ ┌─────▼─────┐    │
│  │   Nginx     │          │   Redis     │ │PostgreSQL │    │
│  │  Reverse    │          │   Cache     │ │  Primary  │    │
│  │   Proxy     │          │             │ │  DB       │    │
│  └─────────────┘          └─────────────┘ └───────────┘    │
│         │                        │               │          │
│         ▼                        ▼               ▼          │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐     │
│  │   Docker    │    │   External  │    │   Backup    │     │
│  │  Container  │    │   Services  │    │   System    │     │
│  │   Cluster   │    │  (n8n, CI)  │    │             │     │
│  └─────────────┘    └─────────────┘    └─────────────┘     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### **1.2 Data Flow Diagram**
```
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  User   │────►│Frontend │────►│  API    │────►│   DB    │
│(Public) │     │(Next.js)│     │(NestJS) │     │(Postgres│
└─────────┘     └─────────┘     └─────────┘     └─────────┘
     │               │               │               │
     │               │               │               │
     ▼               ▼               ▼               ▼
┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
│  Email/ │     │   JWT   │     │  Redis  │     │  Audit  │
│  SMS    │     │  Auth   │     │  Cache  │     │  Logs   │
└─────────┘     └─────────┘     └─────────┘     └─────────┘
```

### **1.3 Workflow Diagram - กระบวนการรับเรื่องร้องเรียน**
```
┌─────────────────────────────────────────────────────────┐
│                 Complaint Submission Flow                │
├─────────────────────────────────────────────────────────┤
│ 1. User Access Complaint Form                          │
│ 2. Fill Personal Data + Upload Documents               │
│ 3. OTP Verification (Email/SMS)                        │
│ 4. System Generate Case ID & Store in DB               │
│ 5. Send Confirmation to User                           │
│ 6. Notify Officer via Dashboard                        │
│ 7. Case Appears in "Pending Cases" Queue               │
└─────────────────────────────────────────────────────────┘
```

### **1.4 Database Schema Diagram**
```
┌─────────────────────┐       ┌─────────────────────┐
│       Users         │       │       Cases         │
├─────────────────────┤       ├─────────────────────┤
│ - id (PK)           │◄──────│ - id (PK)           │
│ - email             │       │ - case_number       │
│ - role              │       │ - complainant_id (FK)│
│ - created_at        │       │ - respondent_id (FK) │
│ - updated_at        │       │ - status            │
└─────────────────────┘       │ - deadline          │
                              │ - created_at        │
┌─────────────────────┐       └─────────────────────┘
│   CaseDocuments     │               │
├─────────────────────┤               │
│ - id (PK)           │◄──────────────┘
│ - case_id (FK)      │
│ - file_path         │
│ - file_type         │
│ - uploaded_by       │
└─────────────────────┘
```

---

## **2. เทมเพลตไฟล์ (Word/Excel Templates)**

### **2.1 Project Plan Template (Excel)**
สามารถดาวน์โหลดได้ที่: [project_plan_template.xlsx](https://docs.google.com/spreadsheets/d/1abc123/template)

**โครงสร้างหลัก:**
1. **Timeline Sheet**: Gantt Chart 120 วัน
2. **Resource Allocation**: การจัดสรรทีมงาน
3. **Risk Matrix**: ตารางความเสี่ยงและแผนรองรับ
4. **Budget Tracking**: การติดตามงบประมาณ

### **2.2 Meeting Minutes Template (Word)**
```markdown
# รายงานการประชุมโครงการพัฒนาระบบบริหารคดี

**วันที่:** [วันที่]
**เวลา:** [เวลา]
**สถานที่:** [สถานที่/Online]

**ผู้เข้าร่วม:**
1. [ชื่อ] - [ตำแหน่ง]
2. [ชื่อ] - [ตำแหน่ง]

**วาระการประชุม:**
1. ทบทวนความคืบหน้า
2. อุปสรรคและแนวทางแก้ไข
3. แผนงานสัปดาห์ถัดไป

**สรุปการประชุม:**
- [ข้อสรุป 1]
- [ข้อสรุป 2]

**มอบหมายงาน:**
| งาน | ผู้รับผิดชอบ | วันที่ครบกำหนด |
|-----|-------------|----------------|
| [งาน 1] | [ชื่อ] | [วันที่] |

**การประชุมครั้งต่อไป:**
- วันที่: [วันที่]
- เวลา: [เวลา]
```

### **2.3 Test Case Template (Excel)**
| Test ID | Module | Test Scenario | Steps | Expected Result | Actual Result | Status | Tester |
|---------|--------|---------------|-------|-----------------|---------------|--------|--------|
| TC-001 | Authentication | User Login | 1. Enter email/password<br>2. Click Login | Redirect to dashboard | [ผลลัพธ์] | Pass/Fail | [ชื่อ] |

---

## **3. ตัวอย่างโค้ดเต็มรูปแบบ**

### **3.1 Backend - NestJS Module Structure**

#### **โครงสร้างโฟลเดอร์:**
```
src/
├── main.ts
├── app.module.ts
├── common/
│   ├── decorators/
│   ├── guards/
│   ├── filters/
│   └── interceptors/
├── modules/
│   ├── auth/
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── jwt.strategy.ts
│   │   └── dto/
│   ├── cases/
│   │   ├── cases.module.ts
│   │   ├── cases.controller.ts
│   │   ├── cases.service.ts
│   │   ├── entities/
│   │   └── dto/
│   └── documents/
└── config/
```

#### **3.1.1 Authentication Module (JWT)**
```typescript
// src/modules/auth/auth.module.ts
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './jwt.strategy';
import { UsersModule } from '../users/users.module';

@Module({
  imports: [
    PassportModule.register({ defaultStrategy: 'jwt' }),
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '24h' },
    }),
    UsersModule,
  ],
  providers: [AuthService, JwtStrategy],
  controllers: [AuthController],
  exports: [AuthService, JwtModule],
})
export class AuthModule {}
```

```typescript
// src/modules/auth/auth.service.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { UsersService } from '../users/users.service';
import { LoginDto } from './dto/login.dto';

@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
  ) {}

  async validateUser(email: string, password: string): Promise<any> {
    const user = await this.usersService.findByEmail(email);
    if (user && user.password === password) {
      const { password, ...result } = user;
      return result;
    }
    return null;
  }

  async login(loginDto: LoginDto) {
    const user = await this.validateUser(loginDto.email, loginDto.password);
    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }

    const payload = { email: user.email, sub: user.id, role: user.role };
    return {
      access_token: this.jwtService.sign(payload),
      user: {
        id: user.id,
        email: user.email,
        role: user.role,
        name: user.name,
      },
    };
  }
}
```

#### **3.1.2 Cases Module**
```typescript
// src/modules/cases/entities/case.entity.ts
import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn, ManyToOne, OneToMany } from 'typeorm';
import { User } from '../../users/entities/user.entity';
import { CaseDocument } from './case-document.entity';

@Entity('cases')
export class Case {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  caseNumber: string;

  @Column()
  title: string;

  @Column('text')
  description: string;

  @Column({ 
    type: 'enum', 
    enum: ['pending', 'investigating', 'reviewing', 'closed', 'archived'],
    default: 'pending'
  })
  status: string;

  @Column({ nullable: true })
  deadline: Date;

  @ManyToOne(() => User, user => user.complaints)
  complainant: User;

  @ManyToOne(() => User, user => user.assignedCases)
  assignedOfficer: User;

  @OneToMany(() => CaseDocument, document => document.case)
  documents: CaseDocument[];

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```

```typescript
// src/modules/cases/cases.controller.ts
import { Controller, Get, Post, Body, Param, UseGuards, Query, Put } from '@nestjs/common';
import { JwtAuthGuard } from '../../common/guards/jwt-auth.guard';
import { RolesGuard } from '../../common/guards/roles.guard';
import { Roles } from '../../common/decorators/roles.decorator';
import { CasesService } from './cases.service';
import { CreateCaseDto } from './dto/create-case.dto';
import { UpdateCaseDto } from './dto/update-case.dto';
import { GetCasesQueryDto } from './dto/get-cases-query.dto';

@Controller('cases')
@UseGuards(JwtAuthGuard, RolesGuard)
export class CasesController {
  constructor(private readonly casesService: CasesService) {}

  @Post()
  @Roles('user', 'admin')
  async create(@Body() createCaseDto: CreateCaseDto) {
    return this.casesService.create(createCaseDto);
  }

  @Get()
  @Roles('officer', 'admin')
  async findAll(@Query() query: GetCasesQueryDto) {
    return this.casesService.findAll(query);
  }

  @Get(':id')
  @Roles('officer', 'admin')
  async findOne(@Param('id') id: string) {
    return this.casesService.findOne(id);
  }

  @Put(':id')
  @Roles('officer', 'admin')
  async update(@Param('id') id: string, @Body() updateCaseDto: UpdateCaseDto) {
    return this.casesService.update(id, updateCaseDto);
  }

  @Get(':id/timeline')
  @Roles('officer', 'admin')
  async getTimeline(@Param('id') id: string) {
    return this.casesService.getTimeline(id);
  }
}
```

#### **3.1.3 DTO with Validation**
```typescript
// src/modules/cases/dto/create-case.dto.ts
import { IsString, IsNotEmpty, IsOptional, IsDateString, IsEnum } from 'class-validator';
import { ApiProperty } from '@nestjs/swagger';

export class CreateCaseDto {
  @ApiProperty({ description: 'Case title' })
  @IsString()
  @IsNotEmpty()
  title: string;

  @ApiProperty({ description: 'Case description' })
  @IsString()
  @IsNotEmpty()
  description: string;

  @ApiProperty({ description: 'Complainant ID', required: false })
  @IsString()
  @IsOptional()
  complainantId?: string;

  @ApiProperty({ 
    description: 'Case type',
    enum: ['complaint', 'inquiry', 'disciplinary']
  })
  @IsEnum(['complaint', 'inquiry', 'disciplinary'])
  caseType: string;

  @ApiProperty({ description: 'Deadline date', required: false })
  @IsDateString()
  @IsOptional()
  deadline?: Date;
}
```

### **3.2 Frontend - Next.js Components**

#### **3.2.1 Complaint Form Component**
```tsx
// components/forms/ComplaintForm.tsx
import React, { useState } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import axios from 'axios';
import { useAuth } from '@/contexts/AuthContext';

const complaintSchema = z.object({
  title: z.string().min(5, 'Title must be at least 5 characters'),
  description: z.string().min(20, 'Description must be at least 20 characters'),
  caseType: z.enum(['complaint', 'inquiry', 'disciplinary']),
  respondentName: z.string().optional(),
  respondentLicenseNo: z.string().optional(),
  documents: z.array(z.instanceof(File)).optional(),
});

type ComplaintFormData = z.infer<typeof complaintSchema>;

export default function ComplaintForm() {
  const { user, token } = useAuth();
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [successMessage, setSuccessMessage] = useState('');

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm<ComplaintFormData>({
    resolver: zodResolver(complaintSchema),
  });

  const onSubmit = async (data: ComplaintFormData) => {
    setIsSubmitting(true);
    try {
      const formData = new FormData();
      Object.entries(data).forEach(([key, value]) => {
        if (key !== 'documents') {
          formData.append(key, value as string);
        }
      });

      if (data.documents) {
        data.documents.forEach((file) => {
          formData.append('documents', file);
        });
      }

      const response = await axios.post('/api/cases', formData, {
        headers: {
          'Authorization': `Bearer ${token}`,
          'Content-Type': 'multipart/form-data',
        },
      });

      setSuccessMessage(`Complaint submitted successfully! Case ID: ${response.data.caseNumber}`);
      reset();
    } catch (error) {
      console.error('Submission error:', error);
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <div className="max-w-2xl mx-auto p-6 bg-white rounded-lg shadow-md">
      <h2 className="text-2xl font-bold mb-6">Submit Complaint</h2>
      
      {successMessage && (
        <div className="mb-4 p-3 bg-green-100 text-green-800 rounded">
          {successMessage}
        </div>
      )}

      <form onSubmit={handleSubmit(onSubmit)} className="space-y-6">
        <div>
          <label className="block text-sm font-medium text-gray-700">
            Title *
          </label>
          <input
            {...register('title')}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
          {errors.title && (
            <p className="mt-1 text-sm text-red-600">{errors.title.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Description *
          </label>
          <textarea
            {...register('description')}
            rows={4}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          />
          {errors.description && (
            <p className="mt-1 text-sm text-red-600">{errors.description.message}</p>
          )}
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Case Type *
          </label>
          <select
            {...register('caseType')}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500"
          >
            <option value="">Select type</option>
            <option value="complaint">Complaint</option>
            <option value="inquiry">Inquiry</option>
            <option value="disciplinary">Disciplinary</option>
          </select>
        </div>

        <div>
          <label className="block text-sm font-medium text-gray-700">
            Upload Documents (Optional)
          </label>
          <input
            type="file"
            multiple
            accept=".pdf,.jpg,.jpeg,.png"
            onChange={(e) => {
              // Handle file upload
            }}
            className="mt-1 block w-full"
          />
        </div>

        <div className="flex items-center justify-between">
          <div className="text-sm text-gray-600">
            Fields marked with * are required
          </div>
          <button
            type="submit"
            disabled={isSubmitting}
            className="px-6 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:opacity-50"
          >
            {isSubmitting ? 'Submitting...' : 'Submit Complaint'}
          </button>
        </div>
      </form>
    </div>
  );
}
```

#### **3.2.2 Auth Context (JWT Management)**
```tsx
// contexts/AuthContext.tsx
import React, { createContext, useContext, useState, useEffect } from 'react';
import axios from 'axios';

interface User {
  id: string;
  email: string;
  name: string;
  role: string;
}

interface AuthContextType {
  user: User | null;
  token: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  isAuthenticated: boolean;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  const [token, setToken] = useState<string | null>(null);

  useEffect(() => {
    // Check for stored token on mount
    const storedToken = localStorage.getItem('token');
    const storedUser = localStorage.getItem('user');
    
    if (storedToken && storedUser) {
      setToken(storedToken);
      setUser(JSON.parse(storedUser));
      axios.defaults.headers.common['Authorization'] = `Bearer ${storedToken}`;
    }
  }, []);

  const login = async (email: string, password: string) => {
    try {
      const response = await axios.post('/api/auth/login', { email, password });
      
      const { access_token, user } = response.data;
      
      // Store in state
      setToken(access_token);
      setUser(user);
      
      // Store in localStorage
      localStorage.setItem('token', access_token);
      localStorage.setItem('user', JSON.stringify(user));
      
      // Set default axios header
      axios.defaults.headers.common['Authorization'] = `Bearer ${access_token}`;
    } catch (error) {
      console.error('Login error:', error);
      throw error;
    }
  };

  const logout = () => {
    setToken(null);
    setUser(null);
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    delete axios.defaults.headers.common['Authorization'];
  };

  return (
    <AuthContext.Provider
      value={{
        user,
        token,
        login,
        logout,
        isAuthenticated: !!user,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
};
```

### **3.3 Infrastructure - Docker Compose**

#### **3.3.1 docker-compose.yml**
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    container_name: pharma-case-db
    environment:
      POSTGRES_DB: pharma_case_db
      POSTGRES_USER: pharma_admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - pharma-network

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: pharma-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes
    networks:
      - pharma-network

  # Backend API
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: pharma-backend
    ports:
      - "3001:3001"
    environment:
      NODE_ENV: production
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: pharma_case_db
      DB_USER: pharma_admin
      DB_PASSWORD: ${DB_PASSWORD}
      REDIS_HOST: redis
      REDIS_PORT: 6379
      JWT_SECRET: ${JWT_SECRET}
    depends_on:
      - postgres
      - redis
    networks:
      - pharma-network
    restart: unless-stopped

  # Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: pharma-frontend
    ports:
      - "3000:3000"
    environment:
      NEXT_PUBLIC_API_URL: http://backend:3001
    depends_on:
      - backend
    networks:
      - pharma-network
    restart: unless-stopped

  # nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    container_name: pharma-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
      - ./nginx/sites:/etc/nginx/sites-enabled
    depends_on:
      - frontend
      - backend
    networks:
      - pharma-network
    restart: unless-stopped

  # n8n for Automation
  n8n:
    image: n8nio/n8n
    container_name: pharma-n8n
    ports:
      - "5678:5678"
    environment:
      N8N_BASIC_AUTH_ACTIVE: "true"
      N8N_BASIC_AUTH_USER: ${N8N_USER}
      N8N_BASIC_AUTH_PASSWORD: ${N8N_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - pharma-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:
  n8n_data:

networks:
  pharma-network:
    driver: bridge
```

#### **3.3.2 Nginx Configuration**
```nginx
# nginx/nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream backend {
        server backend:3001;
    }

    upstream frontend {
        server frontend:3000;
    }

    server {
        listen 80;
        server_name pharma-council.local;
        
        # Redirect HTTP to HTTPS
        return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl http2;
        server_name pharma-council.local;

        ssl_certificate /etc/nginx/ssl/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/key.pem;
        
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;

        # Frontend
        location / {
            proxy_pass http://frontend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        # Backend API
        location /api/ {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            
            # CORS headers
            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS' always;
            add_header 'Access-Control-Allow-Headers' 'Authorization, Content-Type' always;
        }

        # Static files
        location /static/ {
            alias /var/www/static/;
            expires 1y;
            add_header Cache-Control "public, immutable";
        }
    }
}
```

### **3.4 Testing - Robot Framework**

#### **3.4.1 API Test Suite**
```robot
*** Settings ***
Library    RequestsLibrary
Library    Collections

Suite Setup    Create Session    pharma_api    http://localhost:3001

*** Variables ***
${VALID_EMAIL}    test@pharmacouncil.com
${VALID_PASSWORD}    Test@1234

*** Test Cases ***
Login With Valid Credentials
    [Tags]    auth    smoke
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${data}=    Create Dictionary    email=${VALID_EMAIL}    password=${VALID_PASSWORD}
    ${response}=    POST On Session    pharma_api    /auth/login    json=${data}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    200
    ${token}=    Get From Dictionary    ${response.json()}    access_token
    Set Suite Variable    ${AUTH_TOKEN}    ${token}
    Log    Token: ${AUTH_TOKEN}

Create New Case
    [Tags]    cases    smoke
    [Setup]    Run Keyword If    '${AUTH_TOKEN}' == '${EMPTY}'    Login With Valid Credentials
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${AUTH_TOKEN}
    
    ${case_data}=    Create Dictionary
    ...    title=Test Complaint Case
    ...    description=This is a test complaint description
    ...    caseType=complaint
    ...    respondentName=John Doe
    ...    respondentLicenseNo=PH123456
    
    ${response}=    POST On Session    pharma_api    /cases    json=${case_data}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    201
    ${case_id}=    Get From Dictionary    ${response.json()}    id
    Set Suite Variable    ${CASE_ID}    ${case_id}
    Log    Created Case ID: ${CASE_ID}

Get Case Details
    [Tags]    cases
    [Setup]    Run Keyword If    '${AUTH_TOKEN}' == '${EMPTY}'    Login With Valid Credentials
    ${headers}=    Create Dictionary    Authorization=Bearer ${AUTH_TOKEN}
    ${response}=    GET On Session    pharma_api    /cases/${CASE_ID}    headers=${headers}
    Should Be Equal As Strings    ${response.status_code}    200
    ${title}=    Get From Dictionary    ${response.json()}    title
    Should Be Equal    ${title}    Test Complaint Case

*** Keywords ***
Setup Test Environment
    Log    Setting up test environment...
    # Add setup code here

Teardown Test Environment
    Log    Cleaning up test environment...
    # Add cleanup code here
```

### **3.5 CI/CD - Jenkins Pipeline**

#### **3.5.1 Jenkinsfile**
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'registry.pharmacouncil.com'
        PROJECT_NAME = 'pharma-case-system'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/pharmacouncil/case-management-system.git'
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
        
        stage('Tests') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        dir('backend') {
                            sh 'npm test'
                        }
                    }
                }
                stage('E2E Tests') {
                    steps {
                        dir('tests') {
                            sh 'robot --outputdir reports api-tests.robot'
                        }
                    }
                }
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}-backend:${env.BUILD_NUMBER}", './backend')
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}-frontend:${env.BUILD_NUMBER}", './frontend')
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}-backend:${env.BUILD_NUMBER}").push()
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}-frontend:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh '''
                    ssh deploy@staging-server "cd /opt/pharma-case-system && \
                    docker-compose pull && \
                    docker-compose up -d"
                '''
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh '''
                    ssh deploy@production-server "cd /opt/pharma-case-system && \
                    docker-compose pull && \
                    docker-compose up -d"
                '''
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "Build Successful: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} was successful.",
                to: 'dev-team@pharmacouncil.com'
            )
        }
        failure {
            emailext (
                subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} has failed.",
                to: 'dev-team@pharmacouncil.com'
            )
        }
    }
}
```

---

## **4. เอกสารประกอบเพิ่มเติม**

### **4.1 API Documentation Template (OpenAPI/Swagger)**
```yaml
openapi: 3.0.0
info:
  title: Pharma Council Case Management API
  version: 1.0.0
  description: API for managing pharmaceutical council cases

servers:
  - url: https://api.pharmacouncil.com/v1
    description: Production server

paths:
  /auth/login:
    post:
      summary: User login
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Login successful
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginResponse'

components:
  schemas:
    LoginRequest:
      type: object
      required:
        - email
        - password
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          format: password
```

### **4.2 Database Migration Script**
```sql
-- init.sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    role VARCHAR(50) NOT NULL DEFAULT 'user',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Cases table
CREATE TABLE cases (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    case_number VARCHAR(50) UNIQUE NOT NULL,
    title VARCHAR(500) NOT NULL,
    description TEXT,
    status VARCHAR(50) DEFAULT 'pending',
    case_type VARCHAR(50),
    complainant_id UUID REFERENCES users(id),
    assigned_officer_id UUID REFERENCES users(id),
    deadline DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Case documents table
CREATE TABLE case_documents (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    case_id UUID REFERENCES cases(id) ON DELETE CASCADE,
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_type VARCHAR(50),
    uploaded_by UUID REFERENCES users(id),
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit logs table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID REFERENCES users(id),
    action VARCHAR(100) NOT NULL,
    resource_type VARCHAR(50),
    resource_id UUID,
    details JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_cases_status ON cases(status);
CREATE INDEX idx_cases_complainant ON cases(complainant_id);
CREATE INDEX idx_cases_officer ON cases(assigned_officer_id);
CREATE INDEX idx_case_documents_case ON case_documents(case_id);
CREATE INDEX idx_audit_logs_user ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created ON audit_logs(created_at DESC);
```

---

## **สรุป**
เอกสารฉบับนี้ประกอบด้วย:
1. **Diagram** - แผนภาพโครงสร้างระบบ
2. **Template Files** - เทมเพลตสำหรับการจัดการโครงการ
3. **ตัวอย่างโค้ดเต็มรูปแบบ** - Backend, Frontend, Infrastructure, Testing, และ CI/CD

สามารถดาวน์โหลดไฟล์เทมเพลตได้จากลิงก์ที่ให้ไว้ หรือปรับเปลี่ยนตามความต้องการของโครงการ เอกสารทั้งหมดนี้สามารถใช้เป็นแนวทางในการพัฒนาและจัดการโครงการระบบบริหารคดีของสภาเภสัชกรรมได้อย่างครบถ้วน