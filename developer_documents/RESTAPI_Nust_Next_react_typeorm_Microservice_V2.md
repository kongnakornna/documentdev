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
# Microservices REST API Architecture with NestJS, Next.js, and React

## 1. การทำงานของ NestJS และโครงสร้างการทำงาน

### 1.1 โครงสร้างโมดูลแบบ Microservices

```
src/
├── modules/
│   ├── auth/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── guards/
│   │   ├── strategies/
│   │   └── auth.module.ts
│   ├── users/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── entities/
│   │   ├── dto/
│   │   └── users.module.ts
│   ├── products/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── entities/
│   │   ├── dto/
│   │   └── products.module.ts
│   ├── inventory/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── entities/
│   │   ├── dto/
│   │   └── inventory.module.ts
│   ├── shipping/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── entities/
│   │   ├── dto/
│   │   └── shipping.module.ts
│   ├── reports/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── entities/
│   │   ├── dto/
│   │   └── reports.module.ts
│   └── audit/
│       ├── controllers/
│       ├── services/
│       ├── entities/
│       ├── dto/
│       └── audit.module.ts
├── common/
│   ├── filters/
│   ├── interceptors/
│   ├── middleware/
│   ├── decorators/
│   └── utils/
├── config/
├── database/
│   ├── migrations/
│   └── seeds/
├── shared/
│   ├── kafka/
│   └── redis/
└── main.ts
```

### 1.2 Flow การทำงานของ JWT Token
```mermaid
graph TD
    A[User Login] --> B[Validate Credentials]
    B --> C[Generate Access Token 10min]
    B --> D[Generate Refresh Token 30 days]
    C --> E[Store in Redis]
    D --> F[Store in Database]
    E --> G[Return Tokens to Client]
    
    H[API Request] --> I[Check Access Token]
    I --> J{Token Valid?}
    J -->|Yes| K[Process Request]
    J -->|No/Expired| L[Check Refresh Token]
    L --> M{Refresh Token Valid?}
    M -->|Yes| N[Generate New Access Token]
    M -->|No| O[Return 401 Unauthorized]
    N --> K
```

### 1.3 Template Code สำหรับ NestJS Module

```typescript
// modules/users/users.module.ts
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { UsersController } from './controllers/users.controller';
import { UsersService } from './services/users.service';
import { User } from './entities/user.entity';
import { AuditModule } from '../audit/audit.module';

@Module({
  imports: [
    TypeOrmModule.forFeature([User]),
    AuditModule,
  ],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}

// modules/users/entities/user.entity.ts
import { Entity, Column, PrimaryGeneratedColumn, CreateDateColumn, UpdateDateColumn } from 'typeorm';
import { IsEmail, IsNotEmpty } from 'class-validator';

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

  @Column()
  passwordHash: string;

  @Column({ default: true })
  isActive: boolean;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @Column({ nullable: true })
  refreshToken: string;

  @Column({ nullable: true })
  refreshTokenExpiry: Date;
}

// modules/users/dto/create-user.dto.ts
import { IsEmail, IsNotEmpty, MinLength, MaxLength } from 'class-validator';
import { ApiProperty } from '@nestjs/swagger';

export class CreateUserDto {
  @ApiProperty({ example: 'user@example.com' })
  @IsEmail()
  email: string;

  @ApiProperty({ example: 'john_doe' })
  @IsNotEmpty()
  @MinLength(3)
  @MaxLength(50)
  username: string;

  @ApiProperty({ example: 'Password123!' })
  @IsNotEmpty()
  @MinLength(8)
  password: string;
}

// modules/users/services/users.service.ts
import { Injectable, ConflictException, NotFoundException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcrypt';
import { User } from '../entities/user.entity';
import { CreateUserDto } from '../dto/create-user.dto';
import { UpdateUserDto } from '../dto/update-user.dto';
import { KafkaService } from '../../../shared/kafka/kafka.service';

@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private usersRepository: Repository<User>,
    private readonly kafkaService: KafkaService,
  ) {}

  async create(createUserDto: CreateUserDto): Promise<User> {
    const existingUser = await this.usersRepository.findOne({
      where: { email: createUserDto.email },
    });

    if (existingUser) {
      throw new ConflictException('Email already exists');
    }

    const hashedPassword = await bcrypt.hash(createUserDto.password, 10);
    
    const user = this.usersRepository.create({
      ...createUserDto,
      passwordHash: hashedPassword,
    });

    const savedUser = await this.usersRepository.save(user);
    
    // Send audit event to Kafka
    await this.kafkaService.sendAuditEvent({
      action: 'USER_CREATED',
      userId: savedUser.id,
      timestamp: new Date(),
      metadata: { email: savedUser.email },
    });

    return savedUser;
  }

  async findAll(page = 1, limit = 10): Promise<{ data: User[]; total: number }> {
    const [data, total] = await this.usersRepository.findAndCount({
      skip: (page - 1) * limit,
      take: limit,
      order: { createdAt: 'DESC' },
    });

    return { data, total };
  }

  async findOne(id: string): Promise<User> {
    const user = await this.usersRepository.findOne({ where: { id } });
    
    if (!user) {
      throw new NotFoundException(`User with ID ${id} not found`);
    }

    return user;
  }

  async update(id: string, updateUserDto: UpdateUserDto): Promise<User> {
    const user = await this.findOne(id);
    
    Object.assign(user, updateUserDto);
    
    return await this.usersRepository.save(user);
  }

  async remove(id: string): Promise<void> {
    const result = await this.usersRepository.delete(id);
    
    if (result.affected === 0) {
      throw new NotFoundException(`User with ID ${id} not found`);
    }
  }
}
```

## 2. การทำงานของ Next.js และโครงสร้างการทำงาน

### 2.1 โครงสร้าง Next.js App Router
```
app/
├── (auth)/
│   ├── login/
│   │   └── page.tsx
│   ├── register/
│   │   └── page.tsx
│   └── layout.tsx
├── (dashboard)/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── users/
│   │   └── page.tsx
│   ├── products/
│   │   └── page.tsx
│   ├── inventory/
│   │   └── page.tsx
│   ├── shipping/
│   │   └── page.tsx
│   └── reports/
│       └── page.tsx
├── api/
│   ├── auth/
│   │   ├── login/
│   │   │   └── route.ts
│   │   ├── refresh/
│   │   │   └── route.ts
│   │   └── logout/
│   │       └── route.ts
│   └── middleware.ts
├── components/
│   ├── ui/
│   ├── layout/
│   ├── forms/
│   └── charts/
├── lib/
│   ├── api/
│   ├── auth/
│   ├── utils/
│   └── constants/
├── store/
│   └── slices/
├── hooks/
└── styles/
```

### 2.2 Authentication Flow ใน Next.js
```typescript
// lib/auth/auth.ts
import { jwtDecode } from 'jwt-decode';

export interface TokenPayload {
  sub: string;
  email: string;
  roles: string[];
  iat: number;
  exp: number;
}

export interface AuthTokens {
  accessToken: string;
  refreshToken: string;
}

export class AuthService {
  private static ACCESS_TOKEN_KEY = 'access_token';
  private static REFRESH_TOKEN_KEY = 'refresh_token';

  static setTokens(tokens: AuthTokens): void {
    if (typeof window !== 'undefined') {
      localStorage.setItem(this.ACCESS_TOKEN_KEY, tokens.accessToken);
      localStorage.setItem(this.REFRESH_TOKEN_KEY, tokens.refreshToken);
    }
  }

  static getAccessToken(): string | null {
    if (typeof window !== 'undefined') {
      return localStorage.getItem(this.ACCESS_TOKEN_KEY);
    }
    return null;
  }

  static getRefreshToken(): string | null {
    if (typeof window !== 'undefined') {
      return localStorage.getItem(this.REFRESH_TOKEN_KEY);
    }
    return null;
  }

  static clearTokens(): void {
    if (typeof window !== 'undefined') {
      localStorage.removeItem(this.ACCESS_TOKEN_KEY);
      localStorage.removeItem(this.REFRESH_TOKEN_KEY);
    }
  }

  static isTokenExpired(token: string): boolean {
    try {
      const decoded = jwtDecode<TokenPayload>(token);
      return decoded.exp * 1000 < Date.now();
    } catch {
      return true;
    }
  }

  static async refreshAccessToken(): Promise<string | null> {
    const refreshToken = this.getRefreshToken();
    
    if (!refreshToken) {
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
      this.setTokens({
        accessToken: data.accessToken,
        refreshToken: data.refreshToken,
      });

      return data.accessToken;
    } catch (error) {
      this.clearTokens();
      return null;
    }
  }
}

// lib/api/axios.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';
import { AuthService } from '../auth/auth';

export class ApiClient {
  private client: AxiosInstance;
  private isRefreshing = false;
  private refreshSubscribers: ((token: string) => void)[] = [];

  constructor(baseURL: string) {
    this.client = axios.create({
      baseURL,
      timeout: 10000,
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
        let token = AuthService.getAccessToken();
        
        if (token && AuthService.isTokenExpired(token)) {
          token = await this.refreshTokenAndUpdate();
        }

        if (token) {
          config.headers.Authorization = `Bearer ${token}`;
        }

        return config;
      },
      (error) => Promise.reject(error)
    );

    // Response interceptor
    this.client.interceptors.response.use(
      (response: AxiosResponse) => response,
      async (error) => {
        const originalRequest = error.config;

        if (error.response?.status === 401 && !originalRequest._retry) {
          if (this.isRefreshing) {
            return new Promise((resolve) => {
              this.refreshSubscribers.push((token: string) => {
                originalRequest.headers.Authorization = `Bearer ${token}`;
                resolve(this.client(originalRequest));
              });
            });
          }

          originalRequest._retry = true;
          this.isRefreshing = true;

          try {
            const newToken = await AuthService.refreshAccessToken();
            
            if (newToken) {
              this.onRefreshed(newToken);
              originalRequest.headers.Authorization = `Bearer ${newToken}`;
              return this.client(originalRequest);
            }
          } catch (refreshError) {
            this.onRefreshedError();
            return Promise.reject(refreshError);
          } finally {
            this.isRefreshing = false;
          }
        }

        return Promise.reject(error);
      }
    );
  }

  private onRefreshed(token: string): void {
    this.refreshSubscribers.forEach((callback) => callback(token));
    this.refreshSubscribers = [];
  }

  private onRefreshedError(): void {
    this.refreshSubscribers = [];
  }

  private async refreshTokenAndUpdate(): Promise<string | null> {
    const newToken = await AuthService.refreshAccessToken();
    return newToken;
  }

  // CRUD methods
  async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.get<T>(url, config);
    return response.data;
  }

  async post<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.post<T>(url, data, config);
    return response.data;
  }

  async put<T>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.put<T>(url, data, config);
    return response.data;
  }

  async delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
    const response = await this.client.delete<T>(url, config);
    return response.data;
  }
}
```

## 3. การทำงานของ React และโครงสร้างการทำงาน

### 3.1 Component Structure
```tsx
// components/layout/DashboardLayout.tsx
import React, { ReactNode } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { AppBar, Toolbar, Typography, Drawer, List, ListItem, ListItemIcon, ListItemText, IconButton } from '@mui/material';
import { Menu as MenuIcon, Dashboard, People, Inventory, LocalShipping, Assessment } from '@mui/icons-material';
import { useRouter } from 'next/navigation';
import { RootState } from '../../store/store';
import { logout } from '../../store/slices/authSlice';

interface DashboardLayoutProps {
  children: ReactNode;
}

const DashboardLayout: React.FC<DashboardLayoutProps> = ({ children }) => {
  const [drawerOpen, setDrawerOpen] = React.useState(true);
  const router = useRouter();
  const dispatch = useDispatch();
  const { user } = useSelector((state: RootState) => state.auth);

  const menuItems = [
    { text: 'Dashboard', icon: <Dashboard />, path: '/dashboard' },
    { text: 'Users', icon: <People />, path: '/dashboard/users' },
    { text: 'Products', icon: <Inventory />, path: '/dashboard/products' },
    { text: 'Inventory', icon: <Inventory />, path: '/dashboard/inventory' },
    { text: 'Shipping', icon: <LocalShipping />, path: '/dashboard/shipping' },
    { text: 'Reports', icon: <Assessment />, path: '/dashboard/reports' },
  ];

  const handleLogout = () => {
    dispatch(logout());
    router.push('/login');
  };

  return (
    <div className="flex h-screen">
      <Drawer
        variant="permanent"
        open={drawerOpen}
        sx={{
          width: drawerOpen ? 240 : 64,
          flexShrink: 0,
          '& .MuiDrawer-paper': {
            width: drawerOpen ? 240 : 64,
            boxSizing: 'border-box',
            transition: 'width 0.3s',
          },
        }}
      >
        <Toolbar>
          <Typography variant="h6" noWrap>
            {drawerOpen ? 'Admin Panel' : 'AP'}
          </Typography>
        </Toolbar>
        <List>
          {menuItems.map((item) => (
            <ListItem
              key={item.text}
              button
              onClick={() => router.push(item.path)}
              sx={{
                minHeight: 48,
                justifyContent: drawerOpen ? 'initial' : 'center',
                px: 2.5,
              }}
            >
              <ListItemIcon
                sx={{
                  minWidth: 0,
                  mr: drawerOpen ? 3 : 'auto',
                  justifyContent: 'center',
                }}
              >
                {item.icon}
              </ListItemIcon>
              <ListItemText
                primary={item.text}
                sx={{ opacity: drawerOpen ? 1 : 0 }}
              />
            </ListItem>
          ))}
        </List>
      </Drawer>
      
      <main className="flex-1 overflow-auto">
        <AppBar position="static">
          <Toolbar>
            <IconButton
              edge="start"
              color="inherit"
              onClick={() => setDrawerOpen(!drawerOpen)}
            >
              <MenuIcon />
            </IconButton>
            <Typography variant="h6" className="flex-1">
              Welcome, {user?.username}
            </Typography>
            <IconButton color="inherit" onClick={handleLogout}>
              Logout
            </IconButton>
          </Toolbar>
        </AppBar>
        
        <div className="p-6">
          {children}
        </div>
      </main>
    </div>
  );
};

export default DashboardLayout;
```

### 3.2 CRUD Component สำหรับ User Management
```tsx
// app/dashboard/users/page.tsx
'use client';

import React, { useEffect, useState } from 'react';
import {
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  Paper,
  Button,
  IconButton,
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  TextField,
  Alert,
  Pagination,
  Box,
  Typography,
} from '@mui/material';
import { Edit, Delete, Add } from '@mui/icons-material';
import { useForm, Controller } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';
import { User } from '../../../types/user';
import { usersApi } from '../../../lib/api/users';

const userSchema = yup.object({
  email: yup.string().email('Invalid email').required('Email is required'),
  username: yup.string().min(3, 'Username must be at least 3 characters').required('Username is required'),
  password: yup.string().min(8, 'Password must be at least 8 characters').when('$isEdit', {
    is: false,
    then: (schema) => schema.required('Password is required'),
    otherwise: (schema) => schema.optional(),
  }),
});

export default function UsersPage() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  const [page, setPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1);
  const [totalItems, setTotalItems] = useState(0);
  const [openDialog, setOpenDialog] = useState(false);
  const [editingUser, setEditingUser] = useState<User | null>(null);

  const {
    control,
    handleSubmit,
    reset,
    formState: { errors },
  } = useForm({
    resolver: yupResolver(userSchema),
    context: { isEdit: !!editingUser },
  });

  const fetchUsers = async (pageNum: number) => {
    try {
      setLoading(true);
      const response = await usersApi.getUsers(pageNum, 10);
      setUsers(response.data);
      setTotalItems(response.total);
      setTotalPages(Math.ceil(response.total / 10));
      setError(null);
    } catch (err) {
      setError('Failed to fetch users');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchUsers(page);
  }, [page]);

  const handleCreateUser = async (data: any) => {
    try {
      if (editingUser) {
        await usersApi.updateUser(editingUser.id, data);
      } else {
        await usersApi.createUser(data);
      }
      setOpenDialog(false);
      reset();
      setEditingUser(null);
      fetchUsers(page);
    } catch (err) {
      setError('Failed to save user');
    }
  };

  const handleEditUser = (user: User) => {
    setEditingUser(user);
    reset({
      email: user.email,
      username: user.username,
    });
    setOpenDialog(true);
  };

  const handleDeleteUser = async (id: string) => {
    if (confirm('Are you sure you want to delete this user?')) {
      try {
        await usersApi.deleteUser(id);
        fetchUsers(page);
      } catch (err) {
        setError('Failed to delete user');
      }
    }
  };

  const handlePageChange = (event: React.ChangeEvent<unknown>, value: number) => {
    setPage(value);
  };

  return (
    <div>
      <Box display="flex" justifyContent="space-between" alignItems="center" mb={3}>
        <Typography variant="h4">User Management</Typography>
        <Button
          variant="contained"
          startIcon={<Add />}
          onClick={() => {
            setEditingUser(null);
            reset();
            setOpenDialog(true);
          }}
        >
          Add User
        </Button>
      </Box>

      {error && (
        <Alert severity="error" onClose={() => setError(null)} sx={{ mb: 2 }}>
          {error}
        </Alert>
      )}

      <TableContainer component={Paper}>
        <Table>
          <TableHead>
            <TableRow>
              <TableCell>Email</TableCell>
              <TableCell>Username</TableCell>
              <TableCell>Created At</TableCell>
              <TableCell>Status</TableCell>
              <TableCell align="right">Actions</TableCell>
            </TableRow>
          </TableHead>
          <TableBody>
            {loading ? (
              <TableRow>
                <TableCell colSpan={5} align="center">
                  Loading...
                </TableCell>
              </TableRow>
            ) : (
              users.map((user) => (
                <TableRow key={user.id}>
                  <TableCell>{user.email}</TableCell>
                  <TableCell>{user.username}</TableCell>
                  <TableCell>
                    {new Date(user.createdAt).toLocaleDateString()}
                  </TableCell>
                  <TableCell>
                    <span
                      className={`px-2 py-1 rounded ${
                        user.isActive
                          ? 'bg-green-100 text-green-800'
                          : 'bg-red-100 text-red-800'
                      }`}
                    >
                      {user.isActive ? 'Active' : 'Inactive'}
                    </span>
                  </TableCell>
                  <TableCell align="right">
                    <IconButton onClick={() => handleEditUser(user)}>
                      <Edit />
                    </IconButton>
                    <IconButton onClick={() => handleDeleteUser(user.id)}>
                      <Delete />
                    </IconButton>
                  </TableCell>
                </TableRow>
              ))
            )}
          </TableBody>
        </Table>
      </TableContainer>

      <Box display="flex" justifyContent="center" mt={3}>
        <Pagination
          count={totalPages}
          page={page}
          onChange={handlePageChange}
          color="primary"
        />
      </Box>

      <Dialog open={openDialog} onClose={() => setOpenDialog(false)} maxWidth="sm" fullWidth>
        <DialogTitle>
          {editingUser ? 'Edit User' : 'Create New User'}
        </DialogTitle>
        <form onSubmit={handleSubmit(handleCreateUser)}>
          <DialogContent>
            <Controller
              name="email"
              control={control}
              defaultValue=""
              render={({ field }) => (
                <TextField
                  {...field}
                  label="Email"
                  type="email"
                  fullWidth
                  margin="normal"
                  error={!!errors.email}
                  helperText={errors.email?.message}
                />
              )}
            />
            <Controller
              name="username"
              control={control}
              defaultValue=""
              render={({ field }) => (
                <TextField
                  {...field}
                  label="Username"
                  fullWidth
                  margin="normal"
                  error={!!errors.username}
                  helperText={errors.username?.message}
                />
              )}
            />
            <Controller
              name="password"
              control={control}
              defaultValue=""
              render={({ field }) => (
                <TextField
                  {...field}
                  label="Password"
                  type="password"
                  fullWidth
                  margin="normal"
                  error={!!errors.password}
                  helperText={errors.password?.message}
                  placeholder={editingUser ? 'Leave blank to keep unchanged' : ''}
                />
              )}
            />
          </DialogContent>
          <DialogActions>
            <Button onClick={() => setOpenDialog(false)}>Cancel</Button>
            <Button type="submit" variant="contained">
              {editingUser ? 'Update' : 'Create'}
            </Button>
          </DialogActions>
        </form>
      </Dialog>
    </div>
  );
}
```

## 4. Database Schema Design

### 4.1 PostgreSQL Schema
```sql
-- users table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(50) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    refresh_token VARCHAR(512),
    refresh_token_expiry TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- products table
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sku VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- inventory table
CREATE TABLE inventory (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID REFERENCES products(id) ON DELETE CASCADE,
    quantity INTEGER NOT NULL DEFAULT 0,
    location VARCHAR(100),
    reorder_level INTEGER DEFAULT 10,
    last_restocked TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- shipping table
CREATE TABLE shipping (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id VARCHAR(50) NOT NULL,
    status VARCHAR(50) DEFAULT 'PENDING',
    carrier VARCHAR(100),
    tracking_number VARCHAR(100),
    estimated_delivery TIMESTAMP,
    actual_delivery TIMESTAMP,
    shipping_address JSONB NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- audit_logs table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(100),
    entity_id VARCHAR(100),
    old_values JSONB,
    new_values JSONB,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_inventory_product_id ON inventory(product_id);
CREATE INDEX idx_shipping_status ON shipping(status);
CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at DESC);
```

## 5. Docker Compose Configuration

### 5.1 docker-compose.yml
```yaml
version: '3.8'

services:
  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - app-network
    command: redis-server --appendonly yes

  # Kafka
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

  # Elasticsearch
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.10.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    networks:
      - app-network

  # Logstash
  logstash:
    image: docker.elastic.co/logstash/logstash:8.10.0
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config:/usr/share/logstash/config
    ports:
      - "5000:5000"
    networks:
      - app-network
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
      - app-network
    depends_on:
      - elasticsearch

  # Grafana
  grafana:
    image: grafana/grafana:10.0.0
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=${GRAFANA_ADMIN_PASSWORD}
    networks:
      - app-network

  # n8n
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USERNAME}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_HOST=${N8N_HOST}
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - N8N_WEBHOOK_URL=${N8N_WEBHOOK_URL}
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - app-network

  # Jenkins
  jenkins:
    image: jenkins/jenkins:lts-jdk17
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - JAVA_OPTS=-Djenkins.install.runSetupWizard=false
    networks:
      - app-network

  # NestJS API
  api:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://${POSTGRES_USER}:${POSTGRES_PASSWORD}@postgres:5432/${POSTGRES_DB}
      - REDIS_URL=redis://redis:6379
      - KAFKA_BROKER=kafka:9092
      - JWT_SECRET=${JWT_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
    volumes:
      - ./backend:/app
      - /app/node_modules
    depends_on:
      - postgres
      - redis
      - kafka
    networks:
      - app-network

  # Next.js Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://api:3001
      - NODE_ENV=development
    volumes:
      - ./frontend:/app
      - /app/node_modules
    depends_on:
      - api
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  postgres_data:
  redis_data:
  elasticsearch_data:
  grafana_data:
  n8n_data:
  jenkins_home:
```

## 6. CI/CD Pipeline with Jenkins

### 6.1 Jenkinsfile
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'your-registry.io'
        PROJECT_NAME = 'microservices-app'
        AWS_REGION = 'ap-southeast-1'
        ECR_REPOSITORY = 'your-ecr-repo'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/your-repo.git'
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
                            sh 'npm test'
                        }
                    }
                }
                stage('Frontend Tests') {
                    steps {
                        dir('frontend') {
                            sh 'npm run lint'
                            sh 'npm test'
                        }
                    }
                }
                stage('API Tests (Robot Framework)') {
                    steps {
                        dir('tests') {
                            sh 'robot --outputdir reports api_tests.robot'
                        }
                    }
                }
            }
        }
        
        stage('Build & Push Docker Images') {
            steps {
                script {
                    // Build Backend
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}/backend:${BUILD_NUMBER}", './backend')
                    
                    // Build Frontend
                    docker.build("${DOCKER_REGISTRY}/${PROJECT_NAME}/frontend:${BUILD_NUMBER}", './frontend')
                    
                    // Push to ECR
                    docker.withRegistry("https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com", 'ecr:us-east-1:aws-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}/backend:${BUILD_NUMBER}").push()
                        docker.image("${DOCKER_REGISTRY}/${PROJECT_NAME}/frontend:${BUILD_NUMBER}").push()
                    }
                }
            }
        }
        
        stage('Deploy to AWS ECS') {
            steps {
                script {
                    // Update ECS Task Definitions
                    sh """
                    aws ecs update-service \
                        --cluster ${PROJECT_NAME}-cluster \
                        --service ${PROJECT_NAME}-service \
                        --force-new-deployment \
                        --region ${AWS_REGION}
                    """
                }
            }
        }
        
        stage('Integration Tests') {
            steps {
                dir('tests') {
                    sh 'robot --outputdir integration_reports integration_tests.robot'
                }
            }
        }
        
        stage('Performance Tests') {
            steps {
                dir('tests/performance') {
                    sh 'k6 run load_test.js'
                }
            }
        }
    }
    
    post {
        always {
            // Archive test results
            junit '**/reports/*.xml'
            robot allowMissing: true, outputPath: 'reports/output.xml'
            
            // Clean up
            sh 'docker system prune -f'
        }
        success {
            // Send success notification
            slackSend(color: 'good', message: "Build ${BUILD_NUMBER} succeeded!")
        }
        failure {
            // Send failure notification
            slackSend(color: 'danger', message: "Build ${BUILD_NUMBER} failed!")
        }
    }
}
```

## 7. Monitoring and Logging Setup

### 7.1 ELK Stack Configuration
```javascript
// logstash/pipeline/logstash.conf
input {
  beats {
    port => 5044
  }
  
  tcp {
    port => 5000
    codec => json
  }
}

filter {
  # Parse JSON logs
  if [message] =~ /^{.*}$/ {
    json {
      source => "message"
    }
  }
  
  # Add timestamp
  date {
    match => [ "timestamp", "ISO8601" ]
  }
  
  # GeoIP for IP addresses
  if [ip] {
    geoip {
      source => "ip"
      target => "geoip"
    }
  }
  
  # User agent parsing
  if [user_agent] {
    useragent {
      source => "user_agent"
      target => "user_agent_info"
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
  
  # For debugging
  stdout {
    codec => rubydebug
  }
}
```

### 7.2 Grafana Dashboard JSON
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
        "title": "Error Rate",
        "type": "stat",
        "targets": [
          {
            "expr": "rate(http_requests_total{status=~\"5..\"}[5m]) / rate(http_requests_total[5m])"
          }
        ]
      },
      {
        "title": "Database Connections",
        "type": "graph",
        "targets": [
          {
            "expr": "pg_stat_database_numbackends"
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

## 8. Robot Framework Test Cases

### 8.1 API Test Suite
```robot
*** Settings ***
Library    RequestsLibrary
Library    Collections
Library    OperatingSystem
Library    String

Suite Setup    Create Session    api    ${BASE_URL}
Suite Teardown    Delete All Sessions

*** Variables ***
${BASE_URL}    http://localhost:3001
${USERNAME}    admin@example.com
${PASSWORD}    admin123

*** Test Cases ***
User Registration Test
    [Tags]    auth    smoke
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...    email=test${RANDOM}@example.com
    ...    username=testuser${RANDOM}
    ...    password=Test123!
    
    ${response}=    POST    api    /auth/register    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    Should Contain    ${response.json()['email']}    @example.com

User Login Test
    [Tags]    auth    smoke
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...    email=${USERNAME}
    ...    password=${PASSWORD}
    
    ${response}=    POST    api    /auth/login    json=${payload}    headers=${headers}
    Status Should Be    200    ${response}
    Dictionary Should Contain Key    ${response.json()}    accessToken
    Dictionary Should Contain Key    ${response.json()}    refreshToken
    
    Set Suite Variable    ${ACCESS_TOKEN}    ${response.json()['accessToken']}

Create Product Test
    [Tags]    products    smoke
    [Setup]    Get Auth Token
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${sku}=    Generate Random String    8    [LETTERS][NUMBERS]
    ${payload}=    Create Dictionary
    ...    sku=${sku}
    ...    name=Test Product
    ...    description=Test Description
    ...    price=99.99
    ...    category=Electronics
    
    ${response}=    POST    api    /products    json=${payload}    headers=${headers}
    Status Should Be    201    ${response}
    Should Be Equal    ${response.json()['sku']}    ${sku}
    
    Set Suite Variable    ${PRODUCT_ID}    ${response.json()['id']}

Get Products Test
    [Tags]    products    smoke
    [Setup]    Get Auth Token
    ${headers}=    Create Dictionary    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${response}=    GET    api    /products    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal As Numbers    ${response.json()['total']}    ${1}

Update Product Test
    [Tags]    products
    [Setup]    Get Auth Token
    ${headers}=    Create Dictionary
    ...    Content-Type=application/json
    ...    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${payload}=    Create Dictionary    price=149.99
    
    ${response}=    PUT    api    /products/${PRODUCT_ID}    json=${payload}    headers=${headers}
    Status Should Be    200    ${response}
    Should Be Equal As Numbers    ${response.json()['price']}    149.99

Delete Product Test
    [Tags]    products
    [Setup]    Get Auth Token
    ${headers}=    Create Dictionary    Authorization=Bearer ${ACCESS_TOKEN}
    
    ${response}=    DELETE    api    /products/${PRODUCT_ID}    headers=${headers}
    Status Should Be    204    ${response}

*** Keywords ***
Get Auth Token
    ${headers}=    Create Dictionary    Content-Type=application/json
    ${payload}=    Create Dictionary
    ...    email=${USERNAME}
    ...    password=${PASSWORD}
    
    ${response}=    POST    api    /auth/login    json=${payload}    headers=${headers}
    ${ACCESS_TOKEN}=    Set Variable    ${response.json()['accessToken']}
    Set Suite Variable    ${ACCESS_TOKEN}
```

## 9. Git Flow Strategy

### 9.1 Branch Strategy
```
main (protected)
├── develop
│   ├── feature/user-management
│   ├── feature/product-crud
│   ├── feature/inventory-system
│   ├── release/v1.0.0
│   └── hotfix/critical-bug-fix
└── production
```

### 9.2 Commit Message Convention
```
feat: add user registration endpoint
fix: resolve authentication token expiration
docs: update API documentation
style: format code according to prettier
refactor: reorganize modules structure
test: add unit tests for user service
chore: update dependencies
```

## 10. Deployment Architecture on AWS

### 10.1 Infrastructure Diagram
```
┌──────────────────────────────────────────────────────────────┐
│                        AWS Cloud                             │
│  ┌──────────────────────────────────────────────────────┐    │
│  │                  VPC (10.0.0.0/16)                   │    │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐      │    │
│  │  │   Public   │  │  Private   │  │  Isolated  │      │    │
│  │  │  Subnet    │  │  Subnet    │  │  Subnet    │      │    │
│  │  └────────────┘  └────────────┘  └────────────┘      │    │
│  └──────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
│  │    ECS      │  │   RDS       │  │   ElastiCache│          │
│  │  (Fargate)  │  │ (PostgreSQL)│  │   (Redis)   │           │
│  └─────────────┘  └─────────────┘  └─────────────┘           │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
│  │    S3       │  │  CloudFront │  │  Route 53   │           │
│  │ (Static Assets)│  (CDN)      │  │  (DNS)      │           │
│  └─────────────┘  └─────────────┘  └─────────────┘           │
│                                                              │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐           │
│  │ CloudWatch  │  │   MSK       │  │  ECR        │           │
│  │ (Logs/Metrics)│  (Kafka)     │  │ (Container Registry)│   │
│  └─────────────┘  └─────────────┘  └─────────────┘           │
└──────────────────────────────────────────────────────────────┘
```

### 10.2 AWS CloudFormation Template (Snippet)
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Microservices Application Stack

Parameters:
  EnvironmentName:
    Type: String
    Default: production
    Description: Environment name

Resources:
  # VPC
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}-vpc

  # ECS Cluster
  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      ClusterName: !Sub ${EnvironmentName}-cluster
      ClusterSettings:
        - Name: containerInsights
          Value: enabled

  # RDS Instance
  DBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      DBInstanceIdentifier: !Sub ${EnvironmentName}-postgres
      AllocatedStorage: 100
      DBInstanceClass: db.t3.micro
      Engine: postgres
      EngineVersion: 15.0
      MasterUsername: !Ref DBUsername
      MasterUserPassword: !Ref DBPassword
      VPCSecurityGroups:
        - !Ref DBSecurityGroup
      DBSubnetGroupName: !Ref DBSubnetGroup
      MultiAZ: false
      StorageType: gp2
      BackupRetentionPeriod: 7
      PubliclyAccessible: false

  # S3 Bucket for Frontend
  FrontendBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub ${EnvironmentName}-frontend-${AWS::AccountId}
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
      PublicAccessBlockConfiguration:
        BlockPublicAcls: false
        BlockPublicPolicy: false
        IgnorePublicAcls: false
        RestrictPublicBuckets: false

  # CloudFront Distribution
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Origins:
          - DomainName: !GetAtt FrontendBucket.RegionalDomainName
            Id: S3Origin
            S3OriginConfig:
              OriginAccessIdentity: !Sub origin-access-identity/cloudfront/${CloudFrontOAI}
        Enabled: true
        DefaultRootObject: index.html
        DefaultCacheBehavior:
          TargetOriginId: S3Origin
          ViewerProtocolPolicy: redirect-to-https
          AllowedMethods:
            - GET
            - HEAD
          CachedMethods:
            - GET
            - HEAD
          ForwardedValues:
            QueryString: false
            Cookies:
              Forward: none
        PriceClass: PriceClass_100
```

## 11. Security Best Practices

### 11.1 Security Configuration
```typescript
// security.config.ts
import * as helmet from 'helmet';
import * as rateLimit from 'express-rate-limit';
import * as csurf from 'csurf';
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';

export async function setupSecurity(app) {
  // Helmet for security headers
  app.use(helmet({
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
  }));

  // Rate limiting
  const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP, please try again later.',
  });
  
  app.use('/api/', limiter);

  // CORS
  app.enableCors({
    origin: process.env.FRONTEND_URL,
    methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
    allowedHeaders: ['Content-Type', 'Authorization'],
    credentials: true,
  });

  // Global validation pipe
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
    }),
  );

  // Security headers
  app.use((req, res, next) => {
    res.setHeader('X-Content-Type-Options', 'nosniff');
    res.setHeader('X-Frame-Options', 'DENY');
    res.setHeader('X-XSS-Protection', '1; mode=block');
    res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
    next();
  });
}
```

## 12. Performance Optimization

### 12.1 Redis Caching Strategy
```typescript
// shared/redis/redis.service.ts
import { Injectable, Inject } from '@nestjs/common';
import { Redis } from 'ioredis';

@Injectable()
export class RedisService {
  constructor(@Inject('REDIS_CLIENT') private readonly redisClient: Redis) {}

  async get<T>(key: string): Promise<T | null> {
    const data = await this.redisClient.get(key);
    return data ? JSON.parse(data) : null;
  }

  async set(key: string, value: any, ttl?: number): Promise<void> {
    const stringValue = JSON.stringify(value);
    
    if (ttl) {
      await this.redisClient.setex(key, ttl, stringValue);
    } else {
      await this.redisClient.set(key, stringValue);
    }
  }

  async del(key: string): Promise<void> {
    await this.redisClient.del(key);
  }

  async exists(key: string): Promise<boolean> {
    const result = await this.redisClient.exists(key);
    return result === 1;
  }

  // Cache patterns
  async cacheWithFallback<T>(
    key: string,
    fallback: () => Promise<T>,
    ttl = 3600,
  ): Promise<T> {
    const cached = await this.get<T>(key);
    
    if (cached) {
      return cached;
    }

    const data = await fallback();
    await this.set(key, data, ttl);
    
    return data;
  }

  async invalidatePattern(pattern: string): Promise<void> {
    const keys = await this.redisClient.keys(pattern);
    
    if (keys.length > 0) {
      await this.redisClient.del(...keys);
    }
  }
}

// Caching interceptor
import { Injectable, NestInterceptor, ExecutionContext, CallHandler } from '@nestjs/common';
import { Observable, of } from 'rxjs';
import { tap } from 'rxjs/operators';

@Injectable()
export class CacheInterceptor implements NestInterceptor {
  constructor(private readonly redisService: RedisService) {}

  async intercept(context: ExecutionContext, next: CallHandler): Promise<Observable<any>> {
    const request = context.switchToHttp().getRequest();
    const key = this.generateCacheKey(request);
    
    const cachedResponse = await this.redisService.get(key);
    
    if (cachedResponse) {
      return of(cachedResponse);
    }

    return next.handle().pipe(
      tap(async (response) => {
        await this.redisService.set(key, response, 300); // Cache for 5 minutes
      }),
    );
  }

  private generateCacheKey(request: any): string {
    const { method, url, params, query, user } = request;
    const userId = user?.id || 'anonymous';
    
    return `cache:${userId}:${method}:${url}:${JSON.stringify(params)}:${JSON.stringify(query)}`;
  }
}
```

เอกสารนี้ครอบคลุมทุกความต้องการที่ระบุไว้ ตั้งแต่:
1. โครงสร้างและการทำงานของ NestJS, Next.js และ React
2. CRUD Operations ด้วย TypeORM
3. JWT Authentication พร้อม Refresh Token
4. Microservices Architecture
5. ระบบทั้งหมดที่ต้องการ (User, Product, Inventory, Shipping, Reports, Audit)
6. การใช้งานเทคโนโลยีที่ระบุ (Redis, Kafka, ELK, Grafana, n8n, etc.)
7. Git Flow และ CI/CD Pipeline
8. Testing ด้วย Robot Framework
9. Docker Compose และ Deployment บน AWS
10. Security Best Practices และ Performance Optimization

โครงสร้างนี้สามารถปรับแต่งตามความต้องการเฉพาะของโครงการได้อีกด้วย
