##### **เอกสารประกอบการพัฒนา Java Spring Boot + ORM + JWT + REST API**  
---
**(Development Guide: Java Spring Boot + ORM + JWT + REST API)**  

---

##### **1. บทนำ (Introduction)**  
- ระบบ REST API ที่พัฒนาด้วย **Java Spring Boot** ร่วมกับ **ORM (JPA/Hibernate)** สำหรับจัดการข้อมูล, **JWT (JSON Web Token)** สำหรับการรักษาความปลอดภัย และออกแบบให้เป็น **RESTful API** เพื่อให้บริการแก่แอปพลิเคชันฝั่ง Client โดยเน้นความปลอดภัย, ประสิทธิภาพ, และการบำรุงรักษาที่ง่าย  

- A REST API system developed with **Java Spring Boot**, integrated with **ORM (JPA/Hibernate)** for data management, **JWT (JSON Web Token)** for security, and designed as a **RESTful API** to serve client applications. Focus is on security, performance, and maintainability.

---

##### **2. บทนิยาม (Definitions)**  
- **Spring Boot**: Framework สำหรับพัฒนา Java-based web applications แบบ standalone  
- **ORM (Object-Relational Mapping)**: เทคนิคสำหรับแปลงข้อมูลระหว่างระบบฐานข้อมูลเชิงสัมพันธ์และวัตถุเชิงโปรแกรม  
- **JWT (JSON Web Token)**: โปรโตคอลสำหรับสร้าง access token ที่ปลอดภัย  
- **REST API**: รูปแบบสถาปัตยกรรมสำหรับการสื่อสารผ่าน HTTP methods (GET, POST, PUT, DELETE)  
- **Dependency Injection**: หลักการออกแบบเพื่อลดการผูกพันระหว่างคอมโพเนนต์  

- **Spring Boot**: Framework for developing standalone Java-based web applications  
- **ORM (Object-Relational Mapping)**: Technique for converting data between relational databases and object-oriented programming  
- **JWT (JSON Web Token)**: Protocol for creating secure access tokens  
- **REST API**: Architectural style for communication via HTTP methods (GET, POST, PUT, DELETE)  
- **Dependency Injection**: Design principle to reduce coupling between components  

---


##### **3. บทหัวข้อ (Topics)**  
 3.1 การตั้งค่าโครงการ (Project Setup)  
 3.2 การกำหนดโครงสร้างฐานข้อมูล (Database Schema Design)  
 3.3 การสร้าง Entity และ Repository (Entity & Repository Creation)  
 3.4 การสร้าง REST Controller (REST Controller Creation)  
 3.5 การกำหนดความปลอดภัยด้วย Spring Security + JWT (Security with Spring Security + JWT)  
 3.6 การตรวจสอบข้อมูล (Validation)  
 3.7 การจัดการข้อผิดพลาด (Error Handling)  
 3.8 การทดสอบ (Testing)  
 3.9 การปรับใช้ (Deployment)  

---
##### **4.REST API** 

 - REST API เป็นรูปแบบสถาปัตยกรรม (Architectural Style) สำหรับการสร้างเว็บเซอร์วิซ ที่ใช้ HTTP protocol ในการสื่อสารระหว่าง Client และ Server 

##### **REST API (Representational State Transfer API)**

##### **ความหมายและแนวคิด**
**REST API** เป็นรูปแบบสถาปัตยกรรม (Architectural Style) สำหรับการสร้างเว็บเซอร์วิซ ที่ใช้ HTTP protocol ในการสื่อสารระหว่าง **Client** และ **Server**

##### **6 Constraints (หลักการสำคัญ)**

#### 1. **Client-Server Architecture**
   - Client และ Server แยกจากกันโดยสิ้นเชิง
   - Server ดูแลจัดการข้อมูล, Client ดูแล User Interface

#### 2. **Stateless**
   - **ทุก Request ต้องมีข้อมูลครบถ้วน** สำหรับ Server ในการประมวลผล
   - Server ไม่เก็บ Session State ของ Client
   ```
   GET /users/123
   Authorization: Bearer <token>  ← ต้องส่ง token ทุกครั้ง
   ```

#### 3. **Cacheable**
   - Response ต้องระบุได้ว่าสามารถ Cache หรือไม่
   ```
   HTTP/1.1 200 OK
   Cache-Control: max-age=3600
   ```

#### 4. **Uniform Interface**
   - **Resource-Based**: ทุกอย่างเป็น Resource (users, products, orders)
   - **Manipulation through Representations**: จัดการผ่าน JSON/XML
   - **Self-descriptive Messages**: ข้อมูลใน Request/Response ชัดเจน
   - **HATEOAS**: Hypermedia as the Engine of Application State

#### 5. **Layered System**
   - Client ไม่จำเป็นต้องรู้ว่าเชื่อมต่อกับ Server ชั้นไหน
   ```
   Client → Load Balancer → Server → Database
   ```

#### 6. **Code on Demand (Optional)**
   - Server สามารถส่ง code (เช่น JavaScript) ให้ Client รันได้

#### **RESTful Design Principles**

#### **1. Resource Naming (การตั้งชื่อ Resource)**
```http
/users          ← Collection ของ users
/users/123      ← User รายเดียว ID 123
/users/123/orders  ← Orders ของ user 123
```

#### **2. HTTP Methods (CRUD Operations)**
| Method | ความหมาย | ตัวอย่าง |
|--------|----------|----------|
| **GET** | ดึงข้อมูล | `GET /products` |
| **POST** | สร้างใหม่ | `POST /products` |
| **PUT** | อัพเดททั้งหมด | `PUT /products/123` |
| **PATCH** | อัพเดทบางส่วน | `PATCH /products/123` |
| **DELETE** | ลบ | `DELETE /products/123` |

#### **3. HTTP Status Codes**

```http
200 OK                    ← สำเร็จ
201 Created               ← สร้างใหม่สำเร็จ
204 No Content            ← ลบสำเร็จ (ไม่มีเนื้อหา)
400 Bad Request           ← Request ผิดรูปแบบ
401 Unauthorized          ← ไม่มีสิทธิ์
403 Forbidden             ← ถูกห้าม
404 Not Found             ← ไม่พบ Resource
500 Internal Server Error ← Server Error
```

#### **ตัวอย่าง REST API Endpoints**

#### **User Management API**
```http
# ดึงข้อมูล users ทั้งหมด
GET    /api/v1/users

# ดึงข้อมูล user ID 123
GET    /api/v1/users/123

# สร้าง user ใหม่
POST   /api/v1/users
Body: {"name": "John", "email": "john@example.com"}

# อัพเดท user
PUT    /api/v1/users/123
Body: {"name": "John Updated"}

# ลบ user
DELETE /api/v1/users/123
```

#### **Filtering, Sorting, Pagination**
```http
GET /api/v1/products?category=electronics&sort=price&page=1&limit=10
```

#### **รูปแบบข้อมูล (Data Formats)**

#### **JSON (ที่นิยมที่สุด)**
```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

#### **XML**
```xml
<user>
  <id>123</id>
  <name>John Doe</name>
  <email>john@example.com</email>
</user>
```

#### **Versioning (การจัดการเวอร์ชัน)**
```http
/api/v1/users     ← เวอร์ชัน 1
/api/v2/users     ← เวอร์ชัน 2
```

#### **Authentication (การยืนยันตัวตน)**

```http
# 1. API Key
GET /api/data
X-API-Key: your-api-key

# 2. Bearer Token (JWT)
GET /api/data
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

# 3. Basic Auth
GET /api/data
Authorization: Basic base64(username:password)
```

#### **ข้อดีของ REST API**
1. **Stateless** → Scalable ได้ง่าย
2. **ใช้ HTTP Standard** → เข้าใจและใช้งานง่าย
3. **Client-Server Separation** → พัฒนาแยกกันได้
4. **Cacheable** → เพิ่ม Performance
5. **Uniform Interface** → Consistent Design

#### **ข้อเสีย**
1. **Over-fetching/Under-fetching** (แก้ด้วย GraphQL)
2. **ไม่มีมาตรฐานโครงสร้าง Response** (ต้องกำหนดเอง)
3. **Versioning** อาจซับซ้อน

#### **ตัวอย่างจริงใน Spring Boot**
```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {
    
    @GetMapping
    public ResponseEntity<List<User>> getUsers() {
        // ดึงข้อมูล users
        return ResponseEntity.ok(users);
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        // สร้าง user ใหม่
        return ResponseEntity.status(201).body(createdUser);
    }
}
```

#### **REST vs SOAP**
| ด้าน | REST | SOAP |
|------|------|------|
| โปรโตคอล | HTTP | HTTP, SMTP, TCP |
| Data Format | JSON, XML | XML เท่านั้น |
| State | Stateless | Stateful/Stateless |
| Performance | ดีกว่า | ช้ากว่า |
| Learning Curve | ง่าย | ยาก |

#### **Best Practices**
1. ใช้ **Nouns** ไม่ใช่ Verbs (`/users` ไม่ใช่ `/getUsers`)
2. ใช้ **Plural Nouns** (`/products` ไม่ใช่ `/product`)
3. ใช้ **Hyphens** ไม่ใช่ underscores (`/user-profiles`)
4. ใช้ **Query Parameters** สำหรับ filtering
5. ใช้ **HTTP Status Codes** ให้ถูกต้อง
6. ใช้ **Versioning** ใน URL

- REST API เป็นมาตรฐานที่นิยมที่สุดสำหรับการสร้าง Web Services ในปัจจุบัน เพราะใช้ง่าย โครงสร้างชัดเจน และทำงานได้ดีกับ HTTP protocol ที่มีอยู่แล้ว

##### **5. ออกแบบคู่มือ (Manual Design)**  

##### **โครงสร้างโฟลเดอร์ (Folder Structure)**  

src/main/java/com/example/
├── config/           // Configuration classes
├── controller/       // REST Controllers
├── dto/             // Data Transfer Objects
├── entity/          // JPA Entities
├── repository/      // JPA Repositories
├── service/         // Business logic
├── security/        // JWT & Security
└── exception/       // Custom exceptions


##### **ขั้นตอนการทำงาน (Step-by-Step Process)**  
1. สร้าง Spring Boot Project ด้วย **Spring Initializr**  
2. กำหนด dependencies: Spring Web, Spring Data JPA, Spring Security, JWT, Database Driver  
3. กำหนดการเชื่อมต่อฐานข้อมูลใน `application.properties`  
4. สร้าง Entity classes และกำหนดความสัมพันธ์  
5. สร้าง Repository interfaces  
6. สร้าง Service layer สำหรับ business logic  
7. สร้าง REST Controllers  
8. ตั้งค่า Spring Security + JWT  
9. เพิ่ม validation และ exception handling  
10. ทดสอบด้วย Postman/Unit Tests  

---

##### **6. ออกแบบ Workflow**  

- mermaid
- graph TD
```
    A[Client Request] --> B{JWT Valid?}
    B -->|No| C[Return 401 Unauthorized]
    B -->|Yes| D[Spring Security Filter]
    D --> E[DispatcherServlet]
    E --> F[Controller Layer]
    F --> G[Service Layer]
    G --> H[Repository Layer]
    H --> I[(Database)]
    I --> H
    H --> G
    G --> F
    F --> J[Return JSON Response]
```
- This is a **Spring Security JWT authentication flow diagram** that visualizes the request processing pipeline in a typical Spring Boot application with JWT-based authentication. Here's the breakdown:

##### **Flow Explanation:**

##### **1. Initial Request Phase**
- **A**: Client sends HTTP request with JWT token (usually in `Authorization` header)
- **B**: Spring Security checks if the JWT token is valid (signature, expiration, etc.)

##### **2. Authentication Decision**
- **C**: If JWT is **invalid** → Immediately returns `401 Unauthorized`
- **D**: If JWT is **valid** → Request proceeds through Spring Security filter chain

##### **3. Spring Processing Pipeline**
- **D**: Spring Security extracts user details/authorities from JWT and sets up `SecurityContext`
- **E**: Request reaches **DispatcherServlet** (Spring MVC's front controller)
- **F**: DispatcherServlet routes to appropriate **Controller** based on @RequestMapping

##### **4. Business Logic Flow**
- **F → G**: Controller calls **Service layer** (business logic)
- **G → H**: Service calls **Repository layer** (data access)
- **H → I**: Repository interacts with **Database** via JPA/Spring Data

##### **5. Response Flow**
- **I → H → G → F**: Data flows back up through the layers
- **J**: Controller returns **JSON response** (typically with `@ResponseBody` or `@RestController`)

##### **Key Components Implied:**

1. **JWT Filter**: Custom `OncePerRequestFilter` that validates tokens
2. **Security Configuration**: `WebSecurityConfigurerAdapter` or `SecurityFilterChain` bean
3. **Authentication Provider**: Extracts claims and creates `Authentication` object
4. **Controller-Service-Repository**: Standard Spring layered architecture

##### **Typical Implementation Snippets:**

```java
// JWT Filter
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, 
                                    HttpServletResponse response, 
                                    FilterChain chain) {
        // Extract & validate JWT
        // Set Authentication in SecurityContextHolder
    }
}

// Security Configuration
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) {
        http.addFilterBefore(jwtAuthenticationFilter(), 
                            UsernamePasswordAuthenticationFilter.class)
            .authorizeRequests()
            .antMatchers("/api/public/**").permitAll()
            .anyRequest().authenticated();
        return http.build();
    }
}
```

##### **Request/Response Headers:**
- **Request**: `Authorization: Bearer <jwt-token>`
- **Response**: `Content-Type: application/json`

- This architecture provides **stateless authentication** where each request carries its own authentication information, making it scalable for distributed systems.
---

##### **7. TASK LIST Template (Excel/Sheet)**  

##### **English Version**  
| Task ID | Task Name                          | Priority | Status      | Assigned To | Start Date | Due Date   |
|---------|------------------------------------|----------|-------------|-------------|------------|------------|
| T001    | Project Setup                      | High     | Completed   | Developer   | 2024-01-01 | 2024-01-05 |
| T002    | Database Design                    | High     | In Progress | DBA         | 2024-01-03 | 2024-01-10 |
| T003    | Entity & Repository Creation       | High     | Pending     | Developer   | 2024-01-06 | 2024-01-12 |
| T004    | JWT Security Setup                 | High     | Pending     | Security    | 2024-01-10 | 2024-01-15 |
| T005    | REST API Development               | Medium   | Pending     | Developer   | 2024-01-12 | 2024-01-20 |
| T006    | Validation & Error Handling        | Medium   | Pending     | Developer   | 2024-01-18 | 2024-01-25 |
| T007    | Testing                            | Medium   | Pending     | QA          | 2024-01-22 | 2024-01-30 |
| T008    | Deployment                         | High     | Pending     | DevOps      | 2024-01-28 | 2024-02-05 |

##### **ภาษาไทย**  
| รหัสงาน | ชื่องาน                         | ความสำคัญ | สถานะ      | ผู้รับผิดชอบ | วันที่เริ่ม | วันครบกำหนด |
|---------|--------------------------------|-----------|------------|-------------|------------|-------------|
| T001    | การตั้งค่าโครงการ               | สูง       | เสร็จสิ้น   | Developer   | 2024-01-01 | 2024-01-05  |
| T002    | ออกแบบฐานข้อมูล                | สูง       | กำลังทำ     | DBA         | 2024-01-03 | 2024-01-10  |
| T003    | สร้าง Entity และ Repository    | สูง       | รอเริ่มงาน  | Developer   | 2024-01-06 | 2024-01-12  |
| T004    | ตั้งค่าความปลอดภัย JWT         | สูง       | รอเริ่มงาน  | Security    | 2024-01-10 | 2024-01-15  |
| T005    | พัฒนา REST API                 | ปานกลาง   | รอเริ่มงาน  | Developer   | 2024-01-12 | 2024-01-20  |
| T006    | การตรวจสอบและจัดการข้อผิดพลาด   | ปานกลาง   | รอเริ่มงาน  | Developer   | 2024-01-18 | 2024-01-25  |
| T007    | การทดสอบ                       | ปานกลาง   | รอเริ่มงาน  | QA          | 2024-01-22 | 2024-01-30  |
| T008    | การปรับใช้                     | สูง       | รอเริ่มงาน  | DevOps      | 2024-01-28 | 2024-02-05  |

---

##### **8. CHECKLIST Template (Excel/Sheet)**  

##### **English Version**  
| Category       | Task                                             | Done | Remarks               |
|----------------|--------------------------------------------------|------|-----------------------|
| Project Setup  | Initialize Spring Boot project                   | [ ]  |                       |
|                | Add required dependencies                        | [ ]  |                       |
| Database       | Design ER diagram                                | [ ]  |                       |
|                | Create tables                                    | [ ]  |                       |
| Security       | Configure Spring Security                        | [ ]  |                       |
|                | Implement JWT token generation                   | [ ]  |                       |
|                | Implement JWT token validation                   | [ ]  |                       |
| API Development| Create Entity classes                            | [ ]  |                       |
|                | Create Repository interfaces                     | [ ]  |                       |
|                | Create Service classes                           | [ ]  |                       |
|                | Create Controller endpoints                      | [ ]  |                       |
| Validation     | Add input validation                             | [ ]  |                       |
|                | Implement exception handling                     | [ ]  |                       |
| Testing        | Unit tests for services                          | [ ]  |                       |
|                | Integration tests for API                        | [ ]  |                       |
| Deployment     | Configure application.properties for production  | [ ]  |                       |
|                | Deploy to server/cloud                           | [ ]  |                       |

##### **ภาษาไทย**  
| หมวดหมู่       | งาน                                               | เสร็จ | หมายเหตุ               |
|----------------|--------------------------------------------------|------|-----------------------|
| การตั้งค่าโครงการ | เริ่มต้นโครงการ Spring Boot                     | [ ]  |                       |
|                | เพิ่ม dependencies ที่จำเป็น                    | [ ]  |                       |
| ฐานข้อมูล      | ออกแบบ ER diagram                               | [ ]  |                       |
|                | สร้างตารางฐานข้อมูล                             | [ ]  |                       |
| ความปลอดภัย    | ตั้งค่า Spring Security                          | [ ]  |                       |
|                | พัฒนาการสร้าง JWT token                         | [ ]  |                       |
|                | พัฒนาการตรวจสอบ JWT token                       | [ ]  |                       |
| การพัฒนา API   | สร้าง Entity classes                            | [ ]  |                       |
|                | สร้าง Repository interfaces                     | [ ]  |                       |
|                | สร้าง Service classes                           | [ ]  |                       |
|                | สร้าง Controller endpoints                      | [ ]  |                       |
| การตรวจสอบข้อมูล | เพิ่มการตรวจสอบข้อมูลนำเข้า                     | [ ]  |                       |
|                | พัฒนาการจัดการข้อผิดพลาด                         | [ ]  |                       |
| การทดสอบ       | Unit tests สำหรับ service                       | [ ]  |                       |
|                | Integration tests สำหรับ API                    | [ ]  |                       |
| การปรับใช้     | ตั้งค่า application.properties สำหรับ production | [ ]  |                       |
|                | ปรับใช้บนเซิร์ฟเวอร์/cloud                      | [ ]  |                       |

---

##### **ไฟล์ Excel**  
- เนื่องจากข้อจำกัดของรูปแบบข้อความ ฉันได้ออกแบบเทมเพลตด้านบนสำหรับ **TASK LIST** และ **CHECKLIST** ซึ่งสามารถคัดลอกไปใช้ใน Excel โดยตรง  

- **TASK LIST**: สร้างแผ่นงานชื่อ "Task List" และใช้ตารางตามตัวอย่าง  
- **CHECKLIST**: สร้างแผ่นงานชื่อ "Checklist" และใช้ตารางตามตัวอย่าง  

##### สำหรับไฟล์ Excel จริงสามารถสร้างโดย:
1. เปิดโปรแกรม Excel
2. คัดลอกตารางด้านบนไปวาง
3. บันทึกไฟล์เป็นชื่อ `Spring_Boot_Project_Template.xlsx`
4. เพิ่มแผ่นงานแยกสำหรับภาษาอังกฤษและภาษาไทย

---
##### **ระบบ ERP + CRM + IoT Monitoring ด้วย Java Spring Boot**
##### **Enterprise Integration Platform Design**

##### **1. โครงสร้างระบบโดยรวม (System Architecture)**
```
mermaid
graph TB
    subgraph "External Systems"
        IoT[IoT Devices/Sensors]
        Mobile[Mobile Apps]
        WebPortal[Web Portal]
        ThirdParty[3rd Party APIs]
    end
    
    subgraph "API Gateway Layer"
        GW[Spring Cloud Gateway]
        LB[Load Balancer]
        Auth[Authentication Service]
    end
    
    subgraph "Microservices Architecture"
        subgraph "ERP Module"
            MS1[Inventory Service]
            MS2[Accounting Service]
            MS3[HR Service]
            MS4[Procurement Service]
        end
        
        subgraph "CRM Module"
            MS5[Customer Service]
            MS6[Sales Service]
            MS7[Marketing Service]
            MS8[Support Service]
        end
        
        subgraph "IoT Module"
            MS9[Device Management]
            MS10[Real-time Monitoring]
            MS11[Alert Service]
            MS12[Analytics Service]
        end
        
        subgraph "Shared Services"
            MS13[User Management]
            MS14[Notification Service]
            MS15[Reporting Service]
            MS16[File Storage Service]
        end
    end
    
    subgraph "Infrastructure"
        DB[(Database Cluster)]
        Cache[Redis Cache]
        MQ[Message Queue - Kafka]
        ES[Elasticsearch]
    end
    
    IoT -->|MQTT/HTTP| GW
    Mobile --> GW
    WebPortal --> GW
    ThirdParty --> GW
    
    GW --> MS1
    GW --> MS5
    GW --> MS9
    
    MS9 --> MQ
    MQ --> MS10
    MQ --> MS11
    
    MS1 --> DB
    MS5 --> DB
    MS9 --> DB
    
    MS15 --> ES
```

##### **2. โมดูลหลัก (Core Modules)**

##### **2.1 ERP Module**
```
java
// ตัวอย่าง Entity Structure
@Entity
@Table(name = "erp_organizations")
public class Organization {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    private String name;
    private String taxId;
    
    @OneToMany(mappedBy = "organization")
    private List<Department> departments;
    
    @OneToMany(mappedBy = "organization")
    private List<Branch> branches;
}

// Inventory Management
@Entity
@Table(name = "erp_inventory")
public class InventoryItem {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    private String sku;
    private String name;
    private String description;
    
    @Enumerated(EnumType.STRING)
    private InventoryCategory category;
    
    private BigDecimal quantity;
    private BigDecimal reorderLevel;
    private String unit;
    
    @ManyToOne
    @JoinColumn(name = "warehouse_id")
    private Warehouse warehouse;
    
    @OneToMany(mappedBy = "item")
    private List<InventoryTransaction> transactions;
}

// Accounting Module
@Entity
@Table(name = "erp_accounting_ledgers")
public class Ledger {
    @Id
    private String accountCode;
    
    private String accountName;
    
    @Enumerated(EnumType.STRING)
    private AccountType type; // ASSET, LIABILITY, EQUITY, REVENUE, EXPENSE
    
    private BigDecimal balance;
    
    @OneToMany(mappedBy = "ledger")
    private List<JournalEntry> journalEntries;
}


##### **2.2 CRM Module**
java
@Entity
@Table(name = "crm_customers")
public class Customer {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    private String customerCode;
    private String name;
    private String email;
    private String phone;
    
    @Enumerated(EnumType.STRING)
    private CustomerType type; // INDIVIDUAL, CORPORATE
    
    @Embedded
    private Address address;
    
    @OneToMany(mappedBy = "customer")
    private List<ContactPerson> contactPersons;
    
    @OneToMany(mappedBy = "customer")
    private List<Opportunity> opportunities;
    
    @OneToMany(mappedBy = "customer")
    private List<SupportTicket> tickets;
}

// Sales Pipeline
@Entity
@Table(name = "crm_opportunities")
public class Opportunity {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    private String name;
    private String description;
    
    @Enumerated(EnumType.STRING)
    private OpportunityStage stage; // PROSPECT, QUALIFICATION, PROPOSAL, NEGOTIATION, CLOSED
    
    private BigDecimal expectedValue;
    private LocalDate closeDate;
    
    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;
    
    @ManyToOne
    @JoinColumn(name = "assigned_to")
    private User assignedTo;
}


##### **2.3 IoT Monitoring Module**
java
@Entity
@Table(name = "iot_devices")
public class IoTDevice {
    @Id
    private String deviceId; // Unique device identifier
    
    private String name;
    private String description;
    
    @Enumerated(EnumType.STRING)
    private DeviceType type; // SENSOR, ACTUATOR, GATEWAY
    
    private String model;
    private String firmwareVersion;
    
    @Embedded
    private Location location;
    
    private LocalDateTime lastSeen;
    private DeviceStatus status;
    
    @OneToMany(mappedBy = "device")
    private List<DeviceTelemetry> telemetryData;
}

// Real-time Telemetry
@Entity
@Table(name = "iot_telemetry")
@Table(name = "iot_telemetry", indexes = {
    @Index(name = "idx_device_timestamp", columnList = "device_id, timestamp DESC")
})
public class DeviceTelemetry {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    @ManyToOne
    @JoinColumn(name = "device_id")
    private IoTDevice device;
    
    private LocalDateTime timestamp;
    
    @Column(columnDefinition = "JSONB")
    private String payload; // JSON data from device
    
    private Double value;
    private String unit;
    private String metricName;
}

// Alert Configuration
@Entity
@Table(name = "iot_alert_rules")
public class AlertRule {
    @Id @GeneratedValue(strategy = GenerationType.UUID)
    private String id;
    
    private String name;
    private String description;
    
    @ManyToOne
    @JoinColumn(name = "device_id")
    private IoTDevice device;
    
    private String condition; // e.g., "temperature > 30"
    
    @Enumerated(EnumType.STRING)
    private AlertSeverity severity; // INFO, WARNING, CRITICAL
    
    private boolean enabled;
    
    @OneToMany(mappedBy = "alertRule")
    private List<AlertLog> alerts;
}

```
##### **3. Spring Boot Configuration**

##### **3.1 Multi-Module Project Structure**
```
erp-crm-iot-system/
├── pom.xml (Parent)
├── api-gateway/
├── service-registry/
├── config-server/
├── erp-module/
│   ├── inventory-service/
│   ├── accounting-service/
│   ├── hr-service/
│   └── procurement-service/
├── crm-module/
│   ├── customer-service/
│   ├── sales-service/
│   ├── marketing-service/
│   └── support-service/
├── iot-module/
│   ├── device-management/
│   ├── monitoring-service/
│   ├── alert-service/
│   └── analytics-service/
└── shared-modules/
    ├── common-lib/
    ├── security-module/
    └── notification-service/
```

##### **3.2 Main Application Configuration**
```
yaml
##### application.yml
spring:
  application:
    name: erp-crm-iot-system
  
  ##### Database Configuration
  datasource:
    url: jdbc:postgresql://localhost:5432/erp_system
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
  
  ##### JPA Configuration
  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
        jdbc:
          batch_size: 20
        order_inserts: true
        order_updates: true
  
  ##### Kafka for IoT Events
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: iot-group
      auto-offset-reset: earliest
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer

##### JWT Configuration
jwt:
  secret: ${JWT_SECRET:your-256-bit-secret}
  expiration: 86400000 ##### 24 hours
  refresh-expiration: 604800000 ##### 7 days

##### IoT MQTT Configuration
iot:
  mqtt:
    broker-url: tcp://localhost:1883
    username: ${MQTT_USERNAME}
    password: ${MQTT_PASSWORD}
    client-id: erp-iot-server
    qos: 1
```

##### **4. Security & Authentication**
```
java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Autowired
    private JwtAuthenticationFilter jwtAuthenticationFilter;
    
    @Autowired
    private CustomUserDetailsService userDetailsService;
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .cors().and()
            .csrf().disable()
            .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .antMatchers("/api/iot/ingest/**").permitAll() // IoT device data ingestion
                .antMatchers("/api/admin/**").hasRole("ADMIN")
                .antMatchers("/api/erp/**").hasAnyRole("ERP_USER", "ADMIN")
                .antMatchers("/api/crm/**").hasAnyRole("CRM_USER", "ADMIN")
                .antMatchers("/api/iot/**").hasAnyRole("IOT_USER", "ADMIN")
                .anyRequest().authenticated()
            .and()
            .addFilterBefore(jwtAuthenticationFilter, 
                           UsernamePasswordAuthenticationFilter.class);
    }
    
    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        config.addAllowedOriginPattern("*");
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        source.registerCorsConfiguration("/**", config);
        return new CorsFilter(source);
    }
}
```

##### **5. IoT Real-time Monitoring Implementation**
```
java
@Service
public class IoTMonitoringService {
    
    @Autowired
    private KafkaTemplate<String, TelemetryData> kafkaTemplate;
    
    @Autowired
    private DeviceRepository deviceRepository;
    
    @Autowired
    private AlertService alertService;
    
    // MQTT Listener for IoT devices
    @Service
    @Slf4j
    public class MqttMessageHandler implements MqttCallback {
        
        private MqttClient mqttClient;
        
        @PostConstruct
        public void init() {
            try {
                mqttClient = new MqttClient(
                    "tcp://localhost:1883", 
                    "erp-iot-server"
                );
                mqttClient.setCallback(this);
                mqttClient.connect();
                mqttClient.subscribe("iot/+/telemetry");
            } catch (MqttException e) {
                log.error("MQTT Connection failed", e);
            }
        }
        
        @Override
        public void messageArrived(String topic, MqttMessage message) {
            String payload = new String(message.getPayload());
            processTelemetryData(topic, payload);
        }
        
        private void processTelemetryData(String topic, String payload) {
            try {
                TelemetryData telemetry = objectMapper.readValue(payload, TelemetryData.class);
                
                // Update device last seen
                deviceRepository.updateLastSeen(telemetry.getDeviceId(), LocalDateTime.now());
                
                // Store in database
                telemetryRepository.save(telemetry);
                
                // Publish to Kafka for real-time processing
                kafkaTemplate.send("iot-telemetry", telemetry.getDeviceId(), telemetry);
                
                // Check for alerts
                alertService.checkAlerts(telemetry);
                
            } catch (Exception e) {
                log.error("Error processing telemetry data", e);
            }
        }
    }
    
    // WebSocket for real-time updates
    @Configuration
    @EnableWebSocketMessageBroker
    public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
        
        @Override
        public void configureMessageBroker(MessageBrokerRegistry config) {
            config.enableSimpleBroker("/topic", "/queue");
            config.setApplicationDestinationPrefixes("/app");
        }
        
        @Override
        public void registerStompEndpoints(StompEndpointRegistry registry) {
            registry.addEndpoint("/ws-iot")
                   .setAllowedOriginPatterns("*")
                   .withSockJS();
        }
    }
    
    @Controller
    public class IoTWebSocketController {
        
        @Autowired
        private SimpMessagingTemplate messagingTemplate;
        
        @MessageMapping("/iot/telemetry")
        @SendTo("/topic/telemetry")
        public TelemetryData handleTelemetry(TelemetryData telemetry) {
            return telemetry;
        }
        
        public void sendAlert(Alert alert) {
            messagingTemplate.convertAndSend("/topic/alerts", alert);
        }
    }
}

```
##### **6. Integration Points**
```
java
// ERP-CRM Integration
@Service
@Transactional
public class SalesOrderService {
    
    @Autowired
    private OrderRepository orderRepository;
    
    @Autowired
    private InventoryServiceClient inventoryService;
    
    @Autowired
    private AccountingServiceClient accountingService;
    
    public SalesOrder createOrder(CreateOrderRequest request) {
        // 1. Create sales order in CRM
        SalesOrder order = new SalesOrder();
        order.setCustomerId(request.getCustomerId());
        order.setItems(request.getItems());
        order.setStatus(OrderStatus.PENDING);
        
        orderRepository.save(order);
        
        // 2. Check inventory availability (ERP Integration)
        inventoryService.reserveInventory(order);
        
        // 3. Create invoice in accounting system
        accountingService.createInvoice(order);
        
        // 4. Update CRM opportunity if exists
        updateOpportunityIfExists(order);
        
        return order;
    }
}

// IoT-ERP Integration
@Service
public class SmartInventoryService {
    
    @KafkaListener(topics = "iot-inventory", groupId = "erp-group")
    public void handleInventoryUpdate(InventoryUpdateEvent event) {
        // IoT sensors update inventory counts automatically
        inventoryRepository.updateQuantity(
            event.getSku(), 
            event.getLocation(), 
            event.getNewQuantity()
        );
        
        // Trigger reorder if below threshold
        checkReorderLevel(event.getSku());
        
        // Notify warehouse staff
        notificationService.sendNotification(
            "Inventory updated for SKU: " + event.getSku(),
            NotificationType.INVENTORY_UPDATE
        );
    }
}
```

##### **7. Dashboard & Reporting**
```
java
@RestController
@RequestMapping("/api/dashboard")
@PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
public class DashboardController {
    
    @Autowired
    private DashboardService dashboardService;
    
    @GetMapping("/sales-summary")
    public ResponseEntity<SalesSummary> getSalesSummary(
            @RequestParam LocalDate startDate,
            @RequestParam LocalDate endDate) {
        return ResponseEntity.ok(dashboardService.getSalesSummary(startDate, endDate));
    }
    
    @GetMapping("/iot-device-status")
    public ResponseEntity<DeviceStatusSummary> getDeviceStatus() {
        return ResponseEntity.ok(dashboardService.getDeviceStatusSummary());
    }
    
    @GetMapping("/real-time-metrics")
    public SseEmitter streamRealTimeMetrics() {
        SseEmitter emitter = new SseEmitter(Long.MAX_VALUE);
        
        // Real-time metrics streaming
        scheduledExecutorService.scheduleAtFixedRate(() -> {
            try {
                DashboardMetrics metrics = dashboardService.getRealTimeMetrics();
                emitter.send(metrics);
            } catch (Exception e) {
                emitter.completeWithError(e);
            }
        }, 0, 5, TimeUnit.SECONDS);
        
        return emitter;
    }
}

// Using Elasticsearch for analytics
@Repository
public interface AnalyticsRepository extends ElasticsearchRepository<BusinessEvent, String> {
    
    @Query("{\"bool\": {\"must\": [{\"term\": {\"eventType\": \"?0\"}}, " +
           "{\"range\": {\"timestamp\": {\"gte\": \"?1\", \"lte\": \"?2\"}}}]}}")
    List<BusinessEvent> findByEventTypeAndTimestampBetween(
        String eventType, 
        LocalDateTime start, 
        LocalDateTime end
    );
    
    @Aggregation(pipeline = {
        "{\"$match\": {\"timestamp\": {\"$gte\": ?0, \"$lte\": ?1}}}",
        "{\"$group\": {\"_id\": \"$category\", \"count\": {\"$sum\": 1}}}"
    })
    List<CategoryCount> getEventCountsByCategory(
        LocalDateTime start, 
        LocalDateTime end
    );
}

```
##### **8. TASK LIST สำหรับโครงการ**
```
| Phase | Module | Task | Priority | Estimated Days |
|-------|--------|------|----------|----------------|
| 1 | Infrastructure | Setup Microservices Architecture | High | 5 |
| 1 | Security | Implement JWT + Role-based Access | High | 3 |
| 1 | Database | Design & Create PostgreSQL Schema | High | 7 |
| 2 | ERP | Inventory Management System | High | 10 |
| 2 | ERP | Accounting Module | High | 12 |
| 2 | ERP | HR Management | Medium | 8 |
| 3 | CRM | Customer Management | High | 7 |
| 3 | CRM | Sales Pipeline | High | 6 |
| 3 | CRM | Support Ticket System | Medium | 5 |
| 4 | IoT | Device Registration & Management | High | 5 |
| 4 | IoT | MQTT/Kafka Integration | High | 4 |
| 4 | IoT | Real-time Monitoring Dashboard | High | 8 |
| 4 | IoT | Alert & Notification System | Medium | 6 |
| 5 | Integration | ERP-CRM Data Sync | High | 5 |
| 5 | Integration | IoT-ERP Inventory Sync | High | 4 |
| 6 | Reporting | Business Intelligence Dashboard | Medium | 10 |
| 6 | Mobile | React Native Mobile App | Medium | 15 |
| 7 | DevOps | CI/CD Pipeline Setup | High | 5 |
| 7 | DevOps | Docker & Kubernetes Deployment | High | 7 |
```
##### **9. CHECKLIST สำหรับแต่ละโมดูล**

##### **ERP Module Checklist**
- [x] Inventory Management (CRUD)
- [x] Purchase Order Processing
- [ ] Sales Order Processing
- [ ] Accounting Ledger System
- [ ] Invoice Generation
- [ ] Payment Processing
- [ ] HR Employee Management
- [ ] Payroll System
- [ ] Tax Calculation
- [ ] Reporting & Analytics

##### **CRM Module Checklist**
- [ ] Customer Profile Management
- [ ] Contact Management
- [ ] Sales Opportunity Tracking
- [ ] Lead Management
- [ ] Marketing Campaign
- [ ] Support Ticket System
- [ ] Customer Communication Log
- [ ] Sales Commission Calculation
- [ ] Customer Segmentation
- [ ] Customer Satisfaction Survey

##### **IoT Module Checklist**
- [ ] Device Registration & Authentication
- [ ] MQTT/HTTP Protocol Support
- [ ] Real-time Data Ingestion
- [ ] Telemetry Data Storage
- [ ] Alert Rule Engine
- [ ] Device Firmware Management
- [ ] Geolocation Tracking
- [ ] Predictive Maintenance
- [ ] Energy Consumption Monitoring
- [ ] Real-time Dashboard

##### **10. เทคโนโลยีที่แนะนำ (Recommended Tech Stack)**
 
##### **Backend:**
- Java 17+ 
- Spring Boot 3.x
- Spring Cloud (Gateway, Config, Discovery)
- Spring Security + JWT
- Spring Data JPA + Hibernate
- Spring WebFlux (for reactive IoT)

##### **Database:**
- PostgreSQL (Transactional data)
- TimescaleDB (IoT time-series data)
- Redis (Caching & Session)
- Elasticsearch (Analytics & Search)
- MongoDB (Optional for flexible schemas)

##### **Messaging & Streaming:**
- Apache Kafka (Event streaming)
- MQTT Broker (Mosquitto/EMQX)
- RabbitMQ (Optional for queuing)

##### **Frontend:**
- React.js with TypeScript (Admin Dashboard)
- React Native (Mobile App)
- Chart.js/D3.js (Data Visualization)
- Socket.io/WebSocket (Real-time updates)

##### **DevOps:**
- Docker & Docker Compose
- Kubernetes (Production)
- Jenkins/GitLab CI
- Prometheus + Grafana (Monitoring)
- ELK Stack (Logging)

##### **11. แนวทางการพัฒนา (Development Guidelines)**

1. **Domain-Driven Design (DDD)** สำหรับแต่ละ bounded context
2. **Event-Driven Architecture** สำหรับการสื่อสารระหว่าง microservices
3. **API First Design** ใช้ OpenAPI/Swagger สำหรับ documentation
4. **十二要素แอปพลิเคชัน (12-Factor App)** สำหรับ cloud-native
5. **Clean Architecture** สำหรับ separation of concerns
6. **Test-Driven Development** ด้วย coverage > 80%
7. **Continuous Integration/Deployment** pipeline

##### **12. ขนาดทีมและระยะเวลา (Team & Timeline)**

##### **ทีมพัฒนาแนะนำ:**
- Backend Developers: 4-5 คน
- Frontend Developers: 2-3 คน
- IoT Specialist: 1-2 คน
- DevOps Engineer: 1-2 คน
- QA Engineer: 2 คน
- Project Manager: 1 คน

##### **ระยะเวลาพัฒนา:**
- Phase 1 (Foundation): 4-6 สัปดาห์
- Phase 2 (ERP Core): 8-10 สัปดาห์
- Phase 3 (CRM Core): 6-8 สัปดาห์
- Phase 4 (IoT Integration): 6-8 สัปดาห์
- Phase 5 (Integration & Testing): 4-6 สัปดาห์
- Phase 6 (Deployment & Training): 2-4 สัปดาห์

**รวมประมาณ: 6-8 เดือน สำหรับ MVP**

------------

##### **CODE REVIEW & PULL REQUEST TEMPLATE**
##### **Template 2 ภาษา (ไทย/อังกฤษ)**

---

##### **ภาษาอังกฤษ (English Version)**

##### **1. PULL REQUEST TEMPLATE**

##### **PR Title Format:**
 
##### [Type] Brief description of changes
 
**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`

##### **PR Description Template:**

##### Description
<!-- Describe the purpose of this PR -->

##### Type of Change
- [x] New feature (non-breaking change)
- [ ] Bug fix (non-breaking change)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Code refactoring
- [ ] Performance improvement
- [ ] Test coverage improvement
- [ ] Other (please describe):

##### Related Issues
<!-- Link to JIRA tickets or GitHub issues -->
- Fixes <issue_number>
- Related to <issue_number>

##### Changes Made
<!-- List specific changes -->
1. update function changes password
2. add news validate type username
3. add news validate type password

##### Technical Details
<!-- Technical implementation details -->
- Edit SQL in Power Query
- UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition;

##### Testing Performed
<!-- Describe tests you've performed -->
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [x] Manual testing performed
- [x] All existing tests pass

##### Test Cases:
| Test Scenario | Expected Result | Actual Result | Status |
|--------------|----------------|---------------|--------|
|              |                |               |        |

##### Screenshots/Recordings
<!-- Add screenshots or screen recordings if applicable -->
- 

##### Database Changes
- [x] No database changes
- [ ] Migration added
- [ ] Data migration needed
- [ ] Updated entity models

##### Migration Script:
##### sql
-- Add SQL changes here
 

##### API Changes
- [ ] No API changes
- [ ] New endpoints added
- [ ] Existing endpoints modified
- [ ] Breaking changes to API

##### API Documentation:
##### yaml
##### Example of API changes
 

##### Deployment Notes
<!-- Any special deployment instructions -->
- 

##### Checklist
- [ ] My code follows the project's coding standards
- [ ] I have performed a self-review of my code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published

##### Review Focus Areas
<!-- Highlight areas you want reviewers to pay special attention to -->
1. Edit SQL in Power Query Areas
2. ADD new SQL in Power Query Areas
3. Delete bug Query Areas
 
---

##### **2. CODE REVIEW CHECKLIST TEMPLATE (English)**

##### **Reviewer Assignment:**
- **Primary Reviewer:** @
- **Secondary Reviewer:** @
- **QA Reviewer:** @
- **Security Reviewer:** @

##### **Review Status:** 
- [x] **Review in Progress**
- [ ] **Changes Requested**
- [ ] **Approved**
- [ ] **Rejected**

##### **REVIEW CHECKLIST:**

##### **A. CODE QUALITY & STANDARDS**
| Check Item | Status | Comments |
|------------|--------|----------|
| **1. Code follows project coding standards** | ⬜ Pass ⬜ Fail | |
| **2. Consistent naming conventions** | ⬜ Pass ⬜ Fail | |
| **3. No code duplication (DRY principle)** | ⬜ Pass ⬜ Fail | |
| **4. Proper separation of concerns** | ⬜ Pass ⬜ Fail | |
| **5. Methods/classes have single responsibility** | ⬜ Pass ⬜ Fail | |
| **6. No dead/unused code** | ⬜ Pass ⬜ Fail | |
| **7. Code is self-documenting** | ⬜ Pass ⬜ Fail | |
| **8. Comments exist for complex logic** | ⬜ Pass ⬜ Fail | |
| **9. No commented-out code** | ⬜ Pass ⬜ Fail | |

##### **B. JAVA & SPRING SPECIFIC**
| Check Item | Status | Comments |
|------------|--------|----------|
| **10. Proper dependency injection (constructor)** | ⬜ Pass ⬜ Fail | |
| **11. No field injection (use constructor)** | ⬜ Pass ⬜ Fail | |
| **12. Appropriate use of Lombok annotations** | ⬜ Pass ⬜ Fail | |
| **13. Proper exception handling** | ⬜ Pass ⬜ Fail | |
| **14. Logging at appropriate levels** | ⬜ Pass ⬜ Fail | |
| **15. Thread safety considerations** | ⬜ Pass ⬜ Fail | |
| **16. Proper use of Optional** | ⬜ Pass ⬜ Fail | |
| **17. Streams used appropriately** | ⬜ Pass ⬜ Fail | |
| **18. Immutable objects where possible** | ⬜ Pass ⬜ Fail | |

##### **C. SPRING BOOT & REST API**
| Check Item | Status | Comments |
|------------|--------|----------|
| **19. Proper RESTful design** | ⬜ Pass ⬜ Fail | |
| **20. Appropriate HTTP methods** | ⬜ Pass ⬜ Fail | |
| **21. Correct HTTP status codes** | ⬜ Pass ⬜ Fail | |
| **22. Input validation with @Valid/@Validated** | ⬜ Pass ⬜ Fail | |
| **23. DTOs used instead of entities in controllers** | ⬜ Pass ⬜ Fail | |
| **24. Proper error response format** | ⬜ Pass ⬜ Fail | |
| **25. API versioning implemented** | ⬜ Pass ⬜ Fail | |
| **26. Pagination for list endpoints** | ⬜ Pass ⬜ Fail | |

##### **D. DATABASE & JPA**
| Check Item | Status | Comments |
|------------|--------|----------|
| **27. Proper entity design (JPA annotations)** | ⬜ Pass ⬜ Fail | |
| **28. Lazy loading used appropriately** | ⬜ Pass ⬜ Fail | |
| **29. No N+1 query problems** | ⬜ Pass ⬜ Fail | |
| **30. Proper transaction management** | ⬜ Pass ⬜ Fail | |
| **31. Indexes on frequently queried columns** | ⬜ Pass ⬜ Fail | |
| **32. Proper cascade configurations** | ⬜ Pass ⬜ Fail | |
| **33. Audit fields (createdAt, updatedAt)** | ⬜ Pass ⬜ Fail | |
| **34. Optimistic locking (@Version) where needed** | ⬜ Pass ⬜ Fail | |

##### **E. SECURITY**
| Check Item | Status | Comments |
|------------|--------|----------|
| **35. No sensitive data in logs** | ⬜ Pass ⬜ Fail | |
| **36. Proper authentication/authorization** | ⬜ Pass ⬜ Fail | |
| **37. Input sanitization** | ⬜ Pass ⬜ Fail | |
| **38. SQL injection prevention** | ⬜ Pass ⬜ Fail | |
| **39. XSS prevention** | ⬜ Pass ⬜ Fail | |
| **40. CSRF protection** | ⬜ Pass ⬜ Fail | |
| **41. CORS configuration** | ⬜ Pass ⬜ Fail | |
| **42. Password hashing (BCrypt)** | ⬜ Pass ⬜ Fail | |

##### **F. PERFORMANCE**
| Check Item | Status | Comments |
|------------|--------|----------|
| **43. Efficient database queries** | ⬜ Pass ⬜ Fail | |
| **44. Proper caching implementation** | ⬜ Pass ⬜ Fail | |
| **45. Memory usage considerations** | ⬜ Pass ⬜ Fail | |
| **46. Async processing where beneficial** | ⬜ Pass ⬜ Fail | |
| **47. Batch operations for bulk data** | ⬜ Pass ⬜ Fail | |
| **48. Connection pooling configured** | ⬜ Pass ⬜ Fail | |

##### **G. TESTING**
| Check Item | Status | Comments |
|------------|--------|----------|
| **49. Unit tests cover new functionality** | ⬜ Pass ⬜ Fail | |
| **50. Test coverage adequate (>80%)** | ⬜ Pass ⬜ Fail | |
| **51. Integration tests for APIs** | ⬜ Pass ⬜ Fail | |
| **52. Test data setup/cleanup** | ⬜ Pass ⬜ Fail | |
| **53. No hardcoded test values** | ⬜ Pass ⬜ Fail | |
| **54. Mocking done appropriately** | ⬜ Pass ⬜ Fail | |
| **55. Edge cases tested** | ⬜ Pass ⬜ Fail | |
| **56. Performance tests if applicable** | ⬜ Pass ⬜ Fail | |

##### **H. IOT SPECIFIC (if applicable)**
| Check Item | Status | Comments |
|------------|--------|----------|
| **57. Device authentication secure** | ⬜ Pass ⬜ Fail | |
| **58. Message queue handling robust** | ⬜ Pass ⬜ Fail | |
| **59. Real-time processing efficient** | ⬜ Pass ⬜ Fail | |
| **60. Alerting mechanism proper** | ⬜ Pass ⬜ Fail | |
| **61. Data retention policy** | ⬜ Pass ⬜ Fail | |
| **62. Device state management** | ⬜ Pass ⬜ Fail | |

##### **REVIEW SCORING:**
| Category | Score (1-5) | Weight | Weighted Score |
|----------|-------------|--------|----------------|
| Code Quality | | 25% | |
| Functionality | | 25% | |
| Security | | 20% | |
| Performance | | 15% | |
| Testing | | 15% | |
| **TOTAL** | | **100%** | |

**Overall Score:** ⬜ **Excellent (90-100%)** ⬜ **Good (75-89%)** ⬜ **Needs Improvement (60-74%)** ⬜ **Poor (<60%)**

##### **REVIEWER COMMENTS:**

##### **Positive Feedback:**
1. 
2. 
3. 

##### **Issues Found:**
| Line | File | Issue | Severity | Suggestion |
|------|------|-------|----------|------------|
| | | | ⬜ Critical ⬜ High ⬜ Medium ⬜ Low | |
| | | | ⬜ Critical ⬜ High ⬜ Medium ⬜ Low | |
| | | | ⬜ Critical ⬜ High ⬜ Medium ⬜ Low | |

##### **Security Vulnerabilities:**
- [ ] No security issues found
- [ ] Security issues identified (see details above)

##### **Technical Debt:**
- [ ] No new technical debt introduced
- [ ] Technical debt identified (document in JIRA)

##### **REVIEW DECISION:**
- [ ] **✅ APPROVE** - Ready to merge
- [ ] **⚠️ APPROVE WITH COMMENTS** - Minor issues, can merge after addressing
- [ ] **🔄 REQUEST CHANGES** - Significant issues, needs rework
- [ ] **❌ REJECT** - Major problems, needs complete re-implementation

**Reviewer Signature:** ____________________
**Date:** ____________________

---

##### **ภาษาไทย (Thai Version)**

##### **1. เทมเพลต PULL REQUEST**

##### **รูปแบบหัวข้อ PR:**
 
- [ประเภท] คำอธิบายสั้นๆ ของการเปลี่ยนแปลง
 
**ประเภท:** `feat` (ฟีเจอร์ใหม่), `fix` (แก้ไขบั๊ก), `docs` (เอกสาร), `style` (รูปแบบ), `refactor` (ปรับโครงสร้าง), `test` (ทดสอบ), `chore` (งานดูแล), `perf` (ประสิทธิภาพ), `ci` (integration), `build` (build system)

##### **เทมเพลตรายละเอียด PR:**
 
##### คำอธิบาย
<!-- อธิบายวัตถุประสงค์ของ PR นี้ -->

##### ประเภทของการเปลี่ยนแปลง
- [ ] ฟีเจอร์ใหม่ (ไม่ breaking change)
- [x] แก้ไขบั๊ก (ไม่ breaking change)
- [x] Breaking change (การเปลี่ยนแปลงที่ทำให้ฟังก์ชันการทำงานเดิมไม่ทำงานตามที่คาดหวัง)
- [ ] อัพเดทเอกสาร
- [ ] ปรับโครงสร้างโค้ด
- [ ] ปรับปรุงประสิทธิภาพ
- [ ] เพิ่มการทดสอบ
- [ ] อื่นๆ (โปรดระบุ):

##### Issue ที่เกี่ยวข้อง
<!-- ลิงก์ไปยัง JIRA tickets หรือ GitHub issues -->
- แก้ไข <หมายเลข issue>
- เกี่ยวข้องกับ <หมายเลข issue>

##### การเปลี่ยนแปลงที่ทำ
<!-- รายการการเปลี่ยนแปลงเฉพาะ -->
1. 
2. 
3. 

##### รายละเอียดทางเทคนิค
<!-- รายละเอียดการ implement -->
- 
- 

##### การทดสอบที่ทำ
<!-- อธิบายการทดสอบที่ทำ -->
- [ ] เพิ่ม/อัพเดท unit tests
- [ ] เพิ่ม/อัพเดท integration tests
- [ ] ทดสอบด้วยมือ
- [ ] ทุก test เดิมผ่าน

##### กรณีทดสอบ:
| สถานการณ์ทดสอบ | ผลลัพธ์ที่คาดหวัง | ผลลัพธ์จริง | สถานะ |
|----------------|-------------------|-------------|--------|
|  load ช้า 10 วิ  | load ไม่เกิน 5 วิ    |  load  6 วิ  | ปกติ   |

##### ภาพหน้าจอ/การบันทึกวิดีโอ
<!-- เพิ่มภาพหน้าจอหรือวิดีโอถ้ามี -->
- 

##### การเปลี่ยนแปลงฐานข้อมูล
- [ ] ไม่มีการเปลี่ยนแปลงฐานข้อมูล
- [ ] เพิ่ม migration
- [ ] ต้องการ data migration
- [ ] อัพเดท entity models

##### สคริปต์ Migration:
- sql
-- เพิ่มการเปลี่ยนแปลง SQL ที่นี่
 

##### การเปลี่ยนแปลง API
- [ ] ไม่มีการเปลี่ยนแปลง API
- [ ] เพิ่ม endpoint ใหม่
- [ ] แก้ไข endpoint ที่มีอยู่
- [ ] Breaking changes ต่อ API

##### เอกสาร API:
-yaml
##### ตัวอย่างการเปลี่ยนแปลง API
-

##### หมายเหตุการ deploy
<!-- คำแนะนำพิเศษสำหรับการ deploy -->
- 

##### Checklist
- [ ] โค้ดของฉันเป็นไปตามมาตรฐานของโปรเจค
- [ ] ฉันได้ตรวจสอบโค้ดของตัวเองแล้ว
- [ ] ฉันได้เพิ่ม comment ในส่วนที่เข้าใจยาก
- [ ] ฉันได้อัพเดทเอกสารที่เกี่ยวข้อง
- [ ] การเปลี่ยนแปลงของฉันไม่ทำให้เกิด warning ใหม่
- [ ] ฉันได้เพิ่ม tests ที่พิสูจน์ว่า fix ใช้งานได้หรือ feature ทำงาน
- [ ] Test ทั้งใหม่และเดิมผ่านเมื่อรันในเครื่อง
- [ ] การเปลี่ยนแปลงที่เกี่ยวข้องอื่นๆ ได้ถูก merge และ publish แล้ว

##### จุดที่ต้องการให้ reviewer เน้น
<!-- ระบุจุดที่ต้องการให้ reviewer ให้ความสนใจเป็นพิเศษ -->
1. 
2. 
3. 
 

---

##### **2. เทมเพลตตรวจสอบโค้ด (CODE REVIEW CHECKLIST)**

##### **ผู้ตรวจสอบ:**
- **ผู้ตรวจสอบหลัก:** @
- **ผู้ตรวจสอบรอง:** @
- **ผู้ตรวจสอบ QA:** @
- **ผู้ตรวจสอบความปลอดภัย:** @

##### **สถานะการตรวจสอบ:**
- [ ] **กำลังตรวจสอบ**
- [ ] **ขอให้แก้ไข**
- [ ] **อนุมัติ**
- [ ] **ปฏิเสธ**

##### **CHECKLIST การตรวจสอบ:**

##### **A. คุณภาพโค้ดและมาตรฐาน**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **1. โค้ดเป็นไปตามมาตรฐานของโปรเจค** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **2. การตั้งชื่อมีความสม่ำเสมอ** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **3. ไม่มีโค้ดซ้ำซ้อน (หลัก DRY)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **4. แยกความรับผิดชอบอย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **5. เมธอด/คลาสมีหน้าที่เดียว** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **6. ไม่มีโค้ดที่ตายแล้ว/ไม่ได้ใช้** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **7. โค้ดอธิบายตัวเองได้** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **8. มี comment สำหรับ logic ที่ซับซ้อน** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **9. ไม่มีโค้ดที่ถูก comment ออก** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **B. JAVA & SPRING เฉพาะ**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **10. Dependency injection ที่ถูกต้อง (constructor)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **11. ไม่ใช้ field injection (ใช้ constructor)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **12. ใช้ Lombok annotations อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **13. จัดการ exception อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **14. มี logging ในระดับที่เหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **15. พิจารณา thread safety** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **16. ใช้ Optional อย่างถูกต้อง** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **17. ใช้ Streams อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **18. ใช้ immutable objects เท่าที่ทำได้** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **C. SPRING BOOT & REST API**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **19. ออกแบบ RESTful อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **20. ใช้ HTTP methods อย่างถูกต้อง** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **21. ส่ง HTTP status codes ที่ถูกต้อง** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **22. ตรวจสอบ input ด้วย @Valid/@Validated** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **23. ใช้ DTOs แทน entities ใน controllers** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **24. รูปแบบ error response ที่เหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **25. มี API versioning** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **26. มี pagination สำหรับ endpoints รายการ** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **D. ฐานข้อมูลและ JPA**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **27. ออกแบบ entity อย่างเหมาะสม (JPA annotations)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **28. ใช้ lazy loading อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **29. ไม่มีปัญหา N+1 queries** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **30. จัดการ transactions อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **31. มี indexes ในคอลัมน์ที่ query บ่อย** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **32. ตั้งค่า cascade อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **33. มีฟิลด์ audit (createdAt, updatedAt)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **34. มี optimistic locking (@Version) ที่จำเป็น** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **E. ความปลอดภัย**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **35. ไม่มีข้อมูล sensitive ใน logs** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **36. การยืนยันตัวตน/สิทธิ์อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **37. การทำความสะอาด input** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **38. ป้องกัน SQL injection** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **39. ป้องกัน XSS** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **40. ป้องกัน CSRF** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **41. ตั้งค่า CORS** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **42. การ hash รหัสผ่าน (BCrypt)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **F. ประสิทธิภาพ**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **43. Database queries มีประสิทธิภาพ** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **44. การ implement caching อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **45. พิจารณาการใช้ memory** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **46. ใช้ async processing ที่เป็นประโยชน์** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **47. Batch operations สำหรับข้อมูลจำนวนมาก** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **48. ตั้งค่า connection pooling** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **G. การทดสอบ**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **49. Unit tests คลุมฟังก์ชันการทำงานใหม่** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **50. ความครอบคลุมการทดสอบเพียงพอ (>80%)** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **51. มี integration tests สำหรับ APIs** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **52. การตั้งค่า/ล้างข้อมูลทดสอบ** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **53. ไม่มีค่าทดสอบที่ hardcode** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **54. การ mock อย่างเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **55. ทดสอบ edge cases** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **56. ทดสอบประสิทธิภาพถ้ามี** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **H. IoT เฉพาะ (ถ้ามี)**
| รายการตรวจสอบ | สถานะ | ความคิดเห็น |
|---------------|--------|-------------|
| **57. การยืนยันตัวตนอุปกรณ์ปลอดภัย** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **58. การจัดการ message queue มั่นคง** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **59. การประมวลผล real-time มีประสิทธิภาพ** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **60. กลไกการแจ้งเตือนเหมาะสม** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **61. นโยบายการเก็บข้อมูล** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |
| **62. การจัดการสถานะอุปกรณ์** | ⬜ ผ่าน ⬜ ไม่ผ่าน | |

##### **การให้คะแนน:**
| หมวดหมู่ | คะแนน (1-5) | น้ำหนัก | คะแนนถ่วงน้ำหนัก |
|----------|-------------|--------|------------------|
| คุณภาพโค้ด | | 25% | |
| ฟังก์ชันการทำงาน | | 25% | |
| ความปลอดภัย | | 20% | |
| ประสิทธิภาพ | | 15% | |
| การทดสอบ | | 15% | |
| **รวม** | | **100%** | |

**คะแนนรวม:** ⬜ **ยอดเยี่ยม (90-100%)** ⬜ **ดี (75-89%)** ⬜ **ต้องปรับปรุง (60-74%)** ⬜ **ไม่ดี (<60%)**

##### **ความคิดเห็นผู้ตรวจสอบ:**

##### **ข้อดี:**
1. 
2. 
3. 

##### **ปัญหาที่พบ:**
| บรรทัด | ไฟล์ | ปัญหา | ความรุนแรง | คำแนะนำ |
|--------|------|-------|------------|---------|
| | | | ⬜ รุนแรงมาก ⬜ รุนแรง ⬜ ปานกลาง ⬜ ต่ำ | |
| | | | ⬜ รุนแรงมาก ⬜ รุนแรง ⬜ ปานกลาง ⬜ ต่ำ | |
| | | | ⬜ รุนแรงมาก ⬜ รุนแรง ⬜ ปานกลาง ⬜ ต่ำ | |

##### **ช่องโหว่ความปลอดภัย:**
- [x] ไม่พบปัญหาความปลอดภัย
- [ ] พบปัญหาความปลอดภัย (ดูรายละเอียดด้านบน)

##### **หนี้ทางเทคนิค:**
- [ ] ไม่ได้สร้างหนี้ทางเทคนิคใหม่
- [ ] พบหนี้ทางเทคนิค (บันทึกใน JIRA)

##### **การตัดสินใจ:**
- [ ] **✅ อนุมัติ** - พร้อม merge
- [ ] **⚠️ อนุมัติพร้อมความคิดเห็น** - ปัญหาเล็กน้อย สามารถ merge ได้หลังแก้ไข
- [ ] **🔄 ขอให้แก้ไข** - ปัญหาสำคัญ ต้องปรับปรุงใหม่
- [ ] **❌ ปฏิเสธ** - ปัญหาร้ายแรง ต้อง implement ใหม่ทั้งหมด

**ลายเซ็นผู้ตรวจสอบ:** ____________________
**วันที่:** ____________________

---

##### **3. EXCEL TEMPLATE STRUCTURE**

##### **ไฟล์: `code-review-checklist.xlsx`**

##### **แผ่นงาน 1: PR Dashboard**
-excel
| PR ID | Title | Author | Date | Status | Primary Reviewer | Score | Link |
|-------|-------|--------|------|--------|------------------|-------|------|
 

##### **แผ่นงาน 2: Review Checklist (ใช้ข้อมูลจาก template ด้านบน)**

##### **แผ่นงาน 3: Metrics**
- excel
| Reviewer | PRs Reviewed | Avg. Score | Avg. Time | Critical Issues Found |
|----------|--------------|------------|-----------|-----------------------|


##### **แผ่นงาน 4: Issues Tracking**
- excel
| PR ID | Issue Type | Severity | File | Line | Status | Fixed Date |
|-------|------------|----------|------|------|--------|------------|


##### **4. BEST PRACTICES FOR REVIEWERS**

##### **ภาษาอังกฤษ:**
1. **Be constructive, not critical** - Focus on the code, not the person
2. **Explain the "why"** - Don't just say it's wrong, explain why
3. **Use code suggestions** - Provide specific code examples
4. **Review in reasonable chunks** - Don't review 1000+ lines at once
5. **Respond promptly** - Don't leave PRs hanging
6. **Acknowledge good work** - Positive feedback is important
7. **Check for tests** - No code change without tests
8. **Consider security implications** - Always think about security

##### **ภาษาไทย:**
1. **วิจารณ์อย่างสร้างสรรค์** - โฟกัสที่โค้ด ไม่ใช่ที่คน
2. **อธิบาย "ทำไม"** - ไม่ใช่แค่บอกว่าผิด แต่ต้องอธิบายว่าทำไม
3. **ให้คำแนะนำที่เป็นโค้ด** - ให้ตัวอย่างโค้ดที่เฉพาะเจาะจง
4. **ตรวจสอบในปริมาณที่เหมาะสม** - อย่าตรวจสอบทีละ 1000+ บรรทัด
5. **ตอบกลับทันเวลา** - อย่าทิ้ง PR ค้างไว้
6. **ให้กำลังใจเมื่อทำดี** - คำชมเป็นสิ่งสำคัญ
7. **ตรวจสอบการทดสอบ** - ไม่ควรมีการเปลี่ยนแปลงโค้ดโดยไม่มี test
8. **พิจารณาด้านความปลอดภัย** - คิดถึงความปลอดภัยเสมอ

---

##### **5. AUTOMATED CHECKS PRE-REVIEW**

##### **สำหรับ Git Hooks หรือ CI/CD:**
yaml
##### .pre-commit-config.yaml
repos:
  - repo: https://github.com/checkstyle/checkstyle
    rev: 10.12.5
    hooks:
      - id: checkstyle
        args: [--config, google_checks.xml]
        
  - repo: https://github.com/spotbugs/spotbugs
    rev: 4.8.2
    hooks:
      - id: spotbugs
        
  - repo: https://github.com/pmd/pmd
    rev: 6.55.0
    hooks:
      - id: pmd
        
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.1
    hooks:
      - id: gitleaks


##### **สำหรับ Maven/Gradle:**
xml
<!-- pom.xml -->
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <version>3.2.2</version>
        <configuration>
            <configLocation>google_checks.xml</configLocation>
        </configuration>
        <executions>
            <execution>
                <phase>validate</phase>
                <goals>
                    <goal>check</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
    
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-pmd-plugin</artifactId>
        <version>3.20.0</version>
    </plugin>
    
    <plugin>
        <groupId>com.github.spotbugs</groupId>
        <artifactId>spotbugs-maven-plugin</artifactId>
        <version>4.7.3.5</version>
    </plugin>
</plugins>


เทมเพลตนี้พร้อมใช้งานสำหรับทั้งทีมพัฒนาไทยและต่างชาติ สามารถปรับแต่งเพิ่มเติมตามความต้องการของทีมและโปรเจคได้

** ระบบนี้สามารถขยายได้ตามความต้องการของธุรกิจ และรองรับการทำงานร่วมกับระบบอื่นๆ ผ่าน API Gateway และ Message Queue **

**เอกสารนี้พร้อมใช้งานสำหรับการพัฒนาโปรเจกต์ Spring Boot แบบครบวงจร** 🚀