#### **คู่มือการพัฒนา Web Application ERP ด้วย AngularJS + TypeScript**

#### **1. โครงสร้าง TypeScript แบบเข้าใจง่าย**

#### **โครงสร้างโฟลเดอร์พื้นฐาน:**
```
src/
├── types/                    # TypeScript Type Definitions
│   ├── api.d.ts
│   ├── user.d.ts
│   └── erp.d.ts
├── interfaces/              # TypeScript Interfaces
│   ├── IUser.ts
│   ├── IAuth.ts
│   └── IApiResponse.ts
├── constants/              # Constants and Enums
│   ├── ApiEndpoints.ts
│   └── UserRoles.ts
├── utils/                  # Utility functions
│   ├── tokenManager.ts
│   └── apiHelper.ts
└── app.ts                  # Main application file
```

#### **ตัวอย่างไฟล์ TypeScript พื้นฐาน:**

**ไฟล์: `types/user.d.ts`**
```typescript
declare namespace ERP {
  interface User {
    id: number;
    username: string;
    email: string;
    role: UserRole;
    department?: string;
  }
  
  type UserRole = 'admin' | 'manager' | 'employee' | 'guest';
}
```

**ไฟล์: `interfaces/IAuth.ts`**
```typescript
export interface IAuthToken {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
  tokenType: string;
}

export interface ILoginCredentials {
  username: string;
  password: string;
  rememberMe?: boolean;
}

export interface IApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
  statusCode: number;
}
```

**ไฟล์: `utils/tokenManager.ts`**
```typescript
export class TokenManager {
  private static ACCESS_TOKEN_KEY = 'erp_access_token';
  private static REFRESH_TOKEN_KEY = 'erp_refresh_token';
  
  static setTokens(accessToken: string, refreshToken: string): void {
    localStorage.setItem(this.ACCESS_TOKEN_KEY, accessToken);
    localStorage.setItem(this.REFRESH_TOKEN_KEY, refreshToken);
  }
  
  static getAccessToken(): string | null {
    return localStorage.getItem(this.ACCESS_TOKEN_KEY);
  }
  
  static getRefreshToken(): string | null {
    return localStorage.getItem(this.REFRESH_TOKEN_KEY);
  }
  
  static clearTokens(): void {
    localStorage.removeItem(this.ACCESS_TOKEN_KEY);
    localStorage.removeItem(this.REFRESH_TOKEN_KEY);
  }
  
  static isAuthenticated(): boolean {
    return !!this.getAccessToken();
  }
}
```

## **2. โครงสร้าง AngularJS แบบเข้าใจง่าย**

#### **โครงสร้างโฟลเดอร์ AngularJS + TypeScript:**
```
erp-app/
├── src/
│   ├── app/
│   │   ├── core/                    # Core modules
│   │   │   ├── auth/               # Authentication
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── auth.interceptor.ts
│   │   │   │   └── auth.guard.ts
│   │   │   ├── api/                # API services
│   │   │   │   ├── api.config.ts
│   │   │   │   ├── api.service.ts
│   │   │   │   └── user.service.ts
│   │   │   └── models/             # Models/Interfaces
│   │   │       ├── user.model.ts
│   │   │       └── api-response.model.ts
│   │   ├── modules/                # Feature modules
│   │   │   ├── login/
│   │   │   │   ├── login.controller.ts
│   │   │   │   ├── login.component.ts
│   │   │   │   └── login.template.html
│   │   │   ├── dashboard/
│   │   │   └── inventory/
│   │   ├── shared/                 # Shared components
│   │   │   ├── components/
│   │   │   ├── directives/
│   │   │   └── filters/
│   │   └── app.module.ts          # Main module
│   ├── assets/
│   ├── index.html
│   └── tsconfig.json              # TypeScript config
├── package.json
└── webpack.config.js
```

#### **ขั้นตอนการตั้งค่า TypeScript + AngularJS:**

**ไฟล์: `tsconfig.json`**
```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "lib": ["es6", "dom"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

#### **ระบบ Login JWT + Refresh Token:**

**ไฟล์: `app/core/auth/auth.service.ts`**
```typescript
import { IAuthToken, ILoginCredentials } from '../../interfaces/IAuth';

export class AuthService {
  static $inject = ['$http', '$q', '$state', 'API_CONFIG'];
  
  private tokenRefreshInterval: any;
  
  constructor(
    private $http: ng.IHttpService,
    private $q: ng.IQService,
    private $state: ng.ui.IStateService,
    private API_CONFIG: any
  ) {}
  
  /**
   * เข้าสู่ระบบ
   */
  async login(credentials: ILoginCredentials): Promise<IAuthToken> {
    try {
      const response = await this.$http.post<IAuthToken>(
        `${this.API_CONFIG.BASE_URL}/auth/login`,
        credentials
      ).then(res => res.data);
      
      this.setSession(response);
      this.startTokenRefresh();
      
      return response;
    } catch (error) {
      throw this.handleAuthError(error);
    }
  }
  
  /**
   * ตั้งค่า Session หลังจาก Login สำเร็จ
   */
  private setSession(authResult: IAuthToken): void {
    const expiresAt = Date.now() + (authResult.expiresIn * 1000);
    
    localStorage.setItem('access_token', authResult.accessToken);
    localStorage.setItem('refresh_token', authResult.refreshToken);
    localStorage.setItem('expires_at', JSON.stringify(expiresAt));
  }
  
  /**
   * ออกจากระบบ
   */
  logout(): void {
    localStorage.removeItem('access_token');
    localStorage.removeItem('refresh_token');
    localStorage.removeItem('expires_at');
    
    clearInterval(this.tokenRefreshInterval);
    this.$state.go('login');
  }
  
  /**
   * ตรวจสอบว่ามี Token อยู่หรือไม่
   */
  isAuthenticated(): boolean {
    const expiresAt = JSON.parse(localStorage.getItem('expires_at') || '0');
    return Date.now() < expiresAt;
  }
  
  /**
   * Refresh Token อัตโนมัติ
   */
  private async refreshToken(): Promise<void> {
    const refreshToken = localStorage.getItem('refresh_token');
    
    if (!refreshToken) {
      this.logout();
      return;
    }
    
    try {
      const response = await this.$http.post<IAuthToken>(
        `${this.API_CONFIG.BASE_URL}/auth/refresh`,
        { refreshToken }
      ).then(res => res.data);
      
      this.setSession(response);
    } catch (error) {
      console.error('Token refresh failed:', error);
      this.logout();
    }
  }
  
  /**
   * เริ่มต้นการ Refresh Token อัตโนมัติ
   */
  private startTokenRefresh(): void {
    // Refresh token 5 นาทีก่อนหมดอายุ
    const refreshTime = 5 * 60 * 1000; // 5 minutes
    
    this.tokenRefreshInterval = setInterval(() => {
      if (this.isAuthenticated()) {
        this.refreshToken();
      }
    }, refreshTime);
  }
  
  /**
   * ดึง Access Token ปัจจุบัน
   */
  getAccessToken(): string | null {
    return localStorage.getItem('access_token');
  }
  
  private handleAuthError(error: any): Error {
    if (error.status === 401) {
      return new Error('ชื่อผู้ใช้หรือรหัสผ่านไม่ถูกต้อง');
    }
    if (error.status === 403) {
      return new Error('ไม่มีสิทธิ์เข้าถึงระบบ');
    }
    return new Error('เกิดข้อผิดพลาดในการเชื่อมต่อ');
  }
}
```

**ไฟล์: `app/core/auth/auth.interceptor.ts`**
```typescript
export class AuthInterceptor implements ng.IHttpInterceptor {
  static $inject = ['$q', '$injector', '$localStorage'];
  
  constructor(
    private $q: ng.IQService,
    private $injector: ng.auto.IInjectorService,
    private $localStorage: any
  ) {}
  
  /**
   * Intercept HTTP requests
   */
  request = (config: ng.IRequestConfig): ng.IRequestConfig => {
    const token = this.$localStorage.get('access_token');
    
    if (token && this.isApiRequest(config.url)) {
      config.headers = config.headers || {};
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    return config;
  }
  
  /**
   * Intercept HTTP responses
   */
  responseError = (response: ng.IHttpResponse<any>): ng.IPromise<any> => {
    if (response.status === 401 && !response.config.url?.includes('/auth/')) {
      const authService = this.$injector.get('authService');
      const $http = this.$injector.get('$http');
      
      // ลอง Refresh Token
      return this.refreshTokenAndRetry(response.config, authService, $http);
    }
    
    return this.$q.reject(response);
  }
  
  /**
   * Refresh Token และลอง request ใหม่
   */
  private refreshTokenAndRetry(
    originalRequest: ng.IRequestConfig,
    authService: any,
    $http: ng.IHttpService
  ): ng.IPromise<any> {
    const refreshToken = this.$localStorage.get('refresh_token');
    
    if (!refreshToken) {
      authService.logout();
      return this.$q.reject(new Error('Session expired'));
    }
    
    return $http.post('/api/auth/refresh', { refreshToken })
      .then((response: any) => {
        authService.setSession(response.data);
        
        // Retry original request with new token
        originalRequest.headers.Authorization = `Bearer ${response.data.accessToken}`;
        return $http(originalRequest);
      })
      .catch(() => {
        authService.logout();
        return this.$q.reject(new Error('Session expired'));
      });
  }
  
  private isApiRequest(url?: string): boolean {
    return url ? url.includes('/api/') : false;
  }
}
```

**ไฟล์: `app/core/auth/auth.guard.ts`**
```typescript
export class AuthGuard {
  static $inject = ['$state', 'authService'];
  
  constructor(
    private $state: ng.ui.IStateService,
    private authService: any
  ) {}
  
  /**
   * ตรวจสอบสิทธิ์ก่อนเข้าหน้า
   */
  canActivate(): boolean | ng.IPromise<boolean> {
    if (this.authService.isAuthenticated()) {
      return true;
    }
    
    // Redirect to login
    this.$state.go('login');
    return false;
  }
  
  /**
   * ตรวจสอบ role
   */
  hasRole(requiredRole: string): boolean {
    const user = this.authService.getCurrentUser();
    return user && user.role === requiredRole;
  }
}
```

**ไฟล์: `app/core/api/api.service.ts`**
```typescript
export class ApiService {
  static $inject = ['$http', 'API_CONFIG'];
  
  constructor(
    private $http: ng.IHttpService,
    private API_CONFIG: any
  ) {}
  
  /**
   * Generic GET request
   */
  async get<T>(endpoint: string, params?: any): Promise<T> {
    return this.$http.get<T>(`${this.API_CONFIG.BASE_URL}${endpoint}`, { params })
      .then(response => response.data);
  }
  
  /**
   * Generic POST request
   */
  async post<T>(endpoint: string, data: any): Promise<T> {
    return this.$http.post<T>(`${this.API_CONFIG.BASE_URL}${endpoint}`, data)
      .then(response => response.data);
  }
  
  /**
   * Generic PUT request
   */
  async put<T>(endpoint: string, data: any): Promise<T> {
    return this.$http.put<T>(`${this.API_CONFIG.BASE_URL}${endpoint}`, data)
      .then(response => response.data);
  }
  
  /**
   * Generic DELETE request
   */
  async delete<T>(endpoint: string): Promise<T> {
    return this.$http.delete<T>(`${this.API_CONFIG.BASE_URL}${endpoint}`)
      .then(response => response.data);
  }
  
  /**
   * Upload file
   */
  async upload<T>(endpoint: string, formData: FormData): Promise<T> {
    return this.$http.post<T>(`${this.API_CONFIG.BASE_URL}${endpoint}`, formData, {
      headers: { 'Content-Type': undefined },
      transformRequest: angular.identity
    }).then(response => response.data);
  }
}
```

#### **ตัวอย่าง Login Component:**

**ไฟล์: `app/modules/login/login.controller.ts`**
```typescript
export class LoginController {
  static $inject = ['$state', 'authService', 'toastr'];
  
  credentials: ILoginCredentials = {
    username: '',
    password: '',
    rememberMe: false
  };
  
  isLoading = false;
  errorMessage = '';
  
  constructor(
    private $state: ng.ui.IStateService,
    private authService: any,
    private toastr: any
  ) {}
  
  /**
   * ฟังก์ชัน Login
   */
  async login(): Promise<void> {
    this.isLoading = true;
    this.errorMessage = '';
    
    try {
      await this.authService.login(this.credentials);
      this.toastr.success('เข้าสู่ระบบสำเร็จ', 'Success');
      this.$state.go('dashboard');
    } catch (error: any) {
      this.errorMessage = error.message || 'เกิดข้อผิดพลาดในการเข้าสู่ระบบ';
      this.toastr.error(this.errorMessage, 'Error');
    } finally {
      this.isLoading = false;
    }
  }
  
  /**
   * ตรวจสอบ form validation
   */
  isValidForm(): boolean {
    return !!this.credentials.username && !!this.credentials.password;
  }
}
```

#### **ไฟล์: `app/app.module.ts`**
```typescript
import * as angular from 'angular';
import { AuthService } from './core/auth/auth.service';
import { AuthInterceptor } from './core/auth/auth.interceptor';
import { ApiService } from './core/api/api.service';
import { LoginController } from './modules/login/login.controller';

const erpApp = angular.module('erpApp', [
  'ui.router',
  'ngStorage',
  'toastr'
]);

// Configuration
erpApp.constant('API_CONFIG', {
  BASE_URL: 'https://api.your-erp.com/v1',
  TIMEOUT: 30000
});

// Services
erpApp.service('authService', AuthService);
erpApp.service('apiService', ApiService);

// Interceptors
erpApp.factory('authInterceptor', ['$q', '$injector', '$localStorage', 
  ($q, $injector, $localStorage) => new AuthInterceptor($q, $injector, $localStorage)
]);

erpApp.config(['$httpProvider', ($httpProvider: ng.IHttpProvider) => {
  $httpProvider.interceptors.push('authInterceptor');
}]);

// Controllers
erpApp.controller('LoginController', LoginController);

// Routes
erpApp.config(['$stateProvider', '$urlRouterProvider', 
  ($stateProvider: ng.ui.IStateProvider, $urlRouterProvider: ng.ui.IUrlRouterProvider) => {
    
    $urlRouterProvider.otherwise('/login');
    
    $stateProvider
      .state('login', {
        url: '/login',
        templateUrl: 'app/modules/login/login.template.html',
        controller: 'LoginController',
        controllerAs: 'vm'
      })
      .state('dashboard', {
        url: '/dashboard',
        templateUrl: 'app/modules/dashboard/dashboard.template.html',
        controller: 'DashboardController',
        controllerAs: 'vm',
        resolve: {
          auth: ['authService', (authService: any) => {
            return authService.isAuthenticated() || Promise.reject('Unauthorized');
          }]
        }
      });
  }
]);
```

## **3. หลักการความปลอดภัยสำหรับ ERP System**

#### **แนวทางปฏิบัติด้านความปลอดภัย:**

1. **Token Management:**
   - เก็บ Token ใน localStorage (หรือใช้ HttpOnly cookies สำหรับเพิ่มความปลอดภัย)
   - ตั้งค่า Token expiration ที่เหมาะสม (15-30 นาทีสำหรับ access token)
   - ใช้ refresh token ที่มีอายุยาวกว่า (7-30 วัน)

2. **API Security:**
   ```typescript
   // ตัวอย่าง secure headers
   const secureHeaders = {
     'Content-Type': 'application/json',
     'Authorization': `Bearer ${token}`,
     'X-Requested-With': 'XMLHttpRequest',
     'X-Content-Type-Options': 'nosniff',
     'X-Frame-Options': 'DENY'
   };
   ```

3. **Role-Based Access Control (RBAC):**
   ```typescript
   export class PermissionService {
     private permissions = {
       admin: ['*'],
       manager: ['view_dashboard', 'manage_inventory', 'view_reports'],
       employee: ['view_dashboard', 'view_own_data']
     };
     
     hasPermission(userRole: string, requiredPermission: string): boolean {
       const userPermissions = this.permissions[userRole] || [];
       return userPermissions.includes('*') || userPermissions.includes(requiredPermission);
     }
   }
   ```

4. **Input Validation:**
   ```typescript
   export class ValidationService {
     static validateEmail(email: string): boolean {
       const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
       return regex.test(email);
     }
     
     static sanitizeInput(input: string): string {
       return input.replace(/[<>]/g, '');
     }
   }
   ```

#### **คำแนะนำเพิ่มเติม:**

1. **ใช้ HTTPS เสมอ** สำหรับทุกการสื่อสาร
2. **Implement CSRF Protection** สำหรับ state-changing operations
3. **Rate Limiting** ที่ API level
4. **Logging และ Monitoring** สำหรับตรวจจับกิจกรรมผิดปกติ
5. **Regular Token Rotation** เพื่อลดความเสี่ยงจากการถูกขโมย token

#### **Package.json ตัวอย่าง:**
```json
{
  "name": "erp-angularjs-typescript",
  "version": "1.0.0",
  "dependencies": {
    "angular": "^1.8.2",
    "angular-ui-router": "^1.0.29",
    "ngstorage": "^0.3.11",
    "angular-toastr": "^2.1.1"
  },
  "devDependencies": {
    "typescript": "^4.9.4",
    "ts-loader": "^9.4.2",
    "webpack": "^5.75.0",
    "webpack-cli": "^5.0.1",
    "@types/angular": "^1.8.4",
    "@types/angular-ui-router": "^1.1.47"
  }
}
```

- โครงสร้างนี้จะช่วยให้คุณพัฒนา ERP Web Application ที่มีความปลอดภัย การจัดการ Token ที่เหมาะสม และง่ายต่อการบำรุงรักษาในระยะยาว

<hr>

```
  ERP Web Application ที่มีความปลอดภัย การจัดการ Token ที่เหมาะสม 
```