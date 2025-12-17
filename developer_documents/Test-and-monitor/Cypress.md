# **Cypress คืออะไร?**
**Cypress** เป็น **End-to-End (E2E) Testing Framework** สำหรับเว็บแอปพลิเคชันสมัยใหม่ ที่ทำงานบนเบราว์เซอร์โดยตรง

---

## **1. คุณสมบัติเด่นของ Cypress**

### **Key Features:**
```
✅ Real-time Reloads - เห็นการเปลี่ยนแปลงทันที
✅ Time Travel - ย้อนดูสถานะของ test ทีละ step
✅ Automatic Waiting - ไม่ต้องใส่ manual wait
✅ Network Traffic Control - Mock/stub request ได้
✅ Screenshots & Videos - บันทึกการรัน test อัตโนมัติ
✅ Cross-browser Testing - รันบน Chrome, Firefox, Edge
✅ CI/CD Integration - ใช้งานกับ Jenkins, GitHub Actions
✅ Dashboard Service - ดูผล test แบบคลาวด์
```

### **Architecture:**
```
┌─────────────────────────────────────────┐
│           Your Test Code                │
│    (describe, it, cy.commands)         │
└───────────────────┬─────────────────────┘
                    │
┌───────────────────▼─────────────────────┐
│           Cypress Test Runner           │
│  (Node.js Process + Electron Browser)   │
└───────────────────┬─────────────────────┘
                    │
┌───────────────────▼─────────────────────┐
│        Browser (Chrome/Electron)        │
│      ┌──────────────────────────┐      │
│      │      Your App            │      │
│      │  ┌──────────────────┐    │      │
│      │  │  iframe          │    │      │
│      │  │  ┌────────────┐  │    │      │
│      │  │  │ Test Code  │  │    │      │
│      │  │  └────────────┘  │    │      │
│      │  └──────────────────┘    │      │
│      └──────────────────────────┘      │
└─────────────────────────────────────────┘
```

---

## **2. โครงสร้างโปรเจค Cypress**

### **โครงสร้างพื้นฐาน:**
```
cypress-project/
├── cypress/
│   ├── e2e/                    # Test specs (หลัก)
│   │   ├── login.cy.js
│   │   ├── registration.cy.js
│   │   ├── checkout.cy.js
│   │   └── admin/
│   │       └── dashboard.cy.js
│   │
│   ├── fixtures/               # Test data
│   │   ├── users.json
│   │   ├── products.json
│   │   └── api/
│   │       └── responses.json
│   │
│   ├── support/               # Support files
│   │   ├── commands.js       # Custom commands
│   │   ├── e2e.js           # Global beforeEach/afterEach
│   │   └── component-index.html # สำหรับ Component Testing
│   │
│   ├── downloads/            # ไฟล์ที่ดาวน์โหลดจากการ test
│   ├── screenshots/         # Screenshots เมื่อ test fail
│   ├── videos/             # Videos ของการรัน test
│   │
│   └── plugins/             # Plugin configuration
│       └── index.js
│
├── cypress.config.js        # Main configuration
├── package.json
├── .gitignore
└── README.md
```

---

## **3. การตั้งค่าเบื้องต้น**

### **Installation:**
```bash
# 1. สร้างโปรเจคใหม่ (ถ้ายังไม่มี)
npm init -y

# 2. ติดตั้ง Cypress
npm install cypress --save-dev

# 3. เปิด Cypress GUI
npx cypress open

# 4. หรือรันแบบ headless
npx cypress run
```

### **Configuration (cypress.config.js):**
```javascript
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  // Project configuration
  projectId: 'your-project-id', // สำหรับ Cypress Dashboard
  
  // E2E Testing configuration
  e2e: {
    // Base URL สำหรับ test
    baseUrl: 'http://localhost:3000',
    
    // ระบุ spec pattern
    specPattern: 'cypress/e2e/**/*.{js,jsx,ts,tsx}',
    
    // ระบุ support file
    supportFile: 'cypress/support/e2e.js',
    
    // ตั้งค่า viewport
    viewportWidth: 1280,
    viewportHeight: 720,
    
    // Retry จำนวนครั้งเมื่อ test fail
    retries: {
      runMode: 2,    // ใน CI/CD
      openMode: 0    // ใน Cypress App
    },
    
    // Setup node events
    setupNodeEvents(on, config) {
      // ใช้งาน plugins
      require('cypress-mochawesome-reporter/plugin')(on)
      
      // Environment variables
      config.env.API_URL = process.env.API_URL || 'http://localhost:8000'
      
      return config
    }
  },
  
  // Component Testing configuration
  component: {
    devServer: {
      framework: 'react',
      bundler: 'webpack'
    }
  },
  
  // Environment variables
  env: {
    username: 'testuser',
    password: 'testpass123',
    apiUrl: 'https://api.example.com'
  },
  
  // Reporter configuration
  reporter: 'mochawesome',
  reporterOptions: {
    reportDir: 'cypress/reports',
    overwrite: false,
    html: true,
    json: true,
    charts: true
  },
  
  // Video recording
  video: true,
  videoCompression: 32,
  
  // Screenshots
  screenshotOnRunFailure: true,
  
  // Browser configuration
  browsers: [
    {
      name: 'chrome',
      family: 'chromium',
      channel: 'stable',
      displayName: 'Chrome'
    },
    {
      name: 'firefox',
      family: 'firefox',
      channel: 'stable',
      displayName: 'Firefox'
    },
    {
      name: 'edge',
      family: 'chromium',
      channel: 'stable',
      displayName: 'Edge'
    }
  ],
  
  // Parallelization สำหรับ CI/CD
  parallel: true,
  experimentalStudio: true,  // Record tests
  experimentalMemoryManagement: true
})
```

---

## **4. รูปแบบการเขียน Test ใน Cypress**

### **4.1 Basic Test Structure:**
```javascript
// cypress/e2e/login.cy.js

/// <reference types="cypress" />

describe('Login Feature', () => {
  // Hooks
  before(() => {
    // รันครั้งเดียวก่อนทั้งหมด
    cy.log('Setting up test data...')
  })
  
  beforeEach(() => {
    // รันก่อนแต่ละ test
    cy.visit('/login')
  })
  
  afterEach(() => {
    // รันหลังจากแต่ละ test
    cy.log('Test completed')
  })
  
  after(() => {
    // รันครั้งเดียวหลังทั้งหมด
    cy.log('Cleaning up...')
  })
  
  // Test Cases
  it('should login with valid credentials', () => {
    // Arrange
    cy.fixture('users').then((users) => {
      const validUser = users.valid
      
      // Act
      cy.get('[data-testid="email-input"]').type(validUser.email)
      cy.get('[data-testid="password-input"]').type(validUser.password)
      cy.get('[data-testid="login-button"]').click()
      
      // Assert
      cy.url().should('include', '/dashboard')
      cy.get('[data-testid="welcome-message"]')
        .should('contain', `Welcome, ${validUser.name}`)
    })
  })
  
  it('should show error with invalid credentials', () => {
    // Act
    cy.get('[data-testid="email-input"]').type('wrong@email.com')
    cy.get('[data-testid="password-input"]').type('wrongpass')
    cy.get('[data-testid="login-button"]').click()
    
    // Assert
    cy.get('[data-testid="error-message"]')
      .should('be.visible')
      .and('contain', 'Invalid credentials')
  })
  
  it('should validate required fields', () => {
    cy.get('[data-testid="login-button"]').click()
    
    cy.get('[data-testid="email-error"]')
      .should('be.visible')
      .and('contain', 'Email is required')
    
    cy.get('[data-testid="password-error"]')
      .should('be.visible')
      .and('contain', 'Password is required')
  })
})
```

### **4.2 Page Object Pattern:**
```javascript
// cypress/pages/LoginPage.js
class LoginPage {
  elements = {
    emailInput: () => cy.get('[data-testid="email-input"]'),
    passwordInput: () => cy.get('[data-testid="password-input"]'),
    loginButton: () => cy.get('[data-testid="login-button"]'),
    errorMessage: () => cy.get('[data-testid="error-message"]'),
    emailError: () => cy.get('[data-testid="email-error"]'),
    passwordError: () => cy.get('[data-testid="password-error"]')
  }
  
  visit() {
    cy.visit('/login')
    return this
  }
  
  typeEmail(email) {
    this.elements.emailInput().clear().type(email)
    return this
  }
  
  typePassword(password) {
    this.elements.passwordInput().clear().type(password)
    return this
  }
  
  submit() {
    this.elements.loginButton().click()
    return this
  }
  
  login(email, password) {
    this.visit()
    this.typeEmail(email)
    this.typePassword(password)
    this.submit()
  }
}

export default new LoginPage()

// cypress/e2e/login-with-pom.cy.js
import LoginPage from '../pages/LoginPage'

describe('Login with Page Object', () => {
  beforeEach(() => {
    LoginPage.visit()
  })
  
  it('should login successfully', () => {
    cy.fixture('users').then((users) => {
      LoginPage
        .typeEmail(users.valid.email)
        .typePassword(users.valid.password)
        .submit()
      
      cy.url().should('include', '/dashboard')
    })
  })
})
```

### **4.3 Custom Commands:**
```javascript
// cypress/support/commands.js

// Custom Login Command
Cypress.Commands.add('login', (email, password) => {
  cy.session([email, password], () => {
    cy.visit('/login')
    cy.get('[data-testid="email-input"]').type(email)
    cy.get('[data-testid="password-input"]').type(password)
    cy.get('[data-testid="login-button"]').click()
    
    // Verify login success
    cy.url().should('include', '/dashboard')
    cy.getCookie('session_id').should('exist')
  })
})

// Custom API Command
Cypress.Commands.add('apiLogin', (credentials) => {
  return cy.request({
    method: 'POST',
    url: `${Cypress.env('apiUrl')}/login`,
    body: credentials,
    failOnStatusCode: false
  })
})

// Custom Assertion
Cypress.Commands.add('shouldBeVisibleAndContain', { prevSubject: true }, 
  (subject, text) => {
    cy.wrap(subject)
      .should('be.visible')
      .and('contain', text)
    
    return subject
  }
)

// Type with delay (simulate human typing)
Cypress.Commands.add('typeHuman', { prevSubject: true }, 
  (subject, text, delay = 100) => {
    cy.wrap(subject).clear()
    
    for (let char of text) {
      cy.wrap(subject).type(char, { delay })
    }
    
    return subject
  }
)

// Usage ใน test
cy.login('test@example.com', 'password123')
cy.get('[data-testid="welcome"]').shouldBeVisibleAndContain('Welcome')
cy.get('input').typeHuman('Hello World', 50)
```

### **4.4 Fixtures สำหรับ Test Data:**
```json
// cypress/fixtures/users.json
{
  "valid": {
    "name": "John Doe",
    "email": "john@example.com",
    "password": "Password123!",
    "role": "user"
  },
  "admin": {
    "name": "Admin User",
    "email": "admin@example.com",
    "password": "AdminPass123!",
    "role": "admin"
  },
  "invalid": {
    "email": "invalid@email.com",
    "password": "wrong"
  }
}

// cypress/fixtures/products.json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 999.99,
    "category": "electronics"
  },
  {
    "id": 2,
    "name": "T-Shirt",
    "price": 19.99,
    "category": "clothing"
  }
]
```

---

## **5. Advanced Patterns และ Techniques**

### **5.1 API Testing with Cypress:**
```javascript
// cypress/e2e/api.cy.js

describe('API Testing', () => {
  const API_URL = Cypress.env('apiUrl')
  
  it('should get user data', () => {
    cy.request(`${API_URL}/users/1`)
      .then((response) => {
        expect(response.status).to.equal(200)
        expect(response.body).to.have.property('id', 1)
        expect(response.body).to.have.property('name')
        expect(response.headers).to.have.property('content-type')
      })
  })
  
  it('should create new user', () => {
    const newUser = {
      name: 'Jane Doe',
      email: 'jane@example.com'
    }
    
    cy.request('POST', `${API_URL}/users`, newUser)
      .then((response) => {
        expect(response.status).to.equal(201)
        expect(response.body).to.have.property('id')
        expect(response.body.name).to.equal(newUser.name)
      })
  })
  
  it('should handle API errors', () => {
    cy.request({
      method: 'GET',
      url: `${API_URL}/users/999`,
      failOnStatusCode: false
    }).then((response) => {
      expect(response.status).to.equal(404)
      expect(response.body).to.have.property('error')
    })
  })
})
```

### **5.2 Mocking และ Stubbing:**
```javascript
// cypress/e2e/mocking.cy.js

describe('Mocking API Requests', () => {
  beforeEach(() => {
    // Intercept และ mock API calls
    cy.intercept('GET', '/api/products', {
      statusCode: 200,
      body: [
        { id: 1, name: 'Mocked Product', price: 99.99 }
      ]
    }).as('getProducts')
    
    cy.intercept('POST', '/api/orders', {
      statusCode: 201,
      body: { 
        orderId: 'mock-123', 
        status: 'created' 
      },
      delay: 1000 // Simulate network delay
    }).as('createOrder')
    
    cy.visit('/products')
  })
  
  it('should display mocked products', () => {
    cy.wait('@getProducts')
    
    cy.get('[data-testid="product-list"]')
      .should('have.length', 1)
      .first()
      .should('contain', 'Mocked Product')
  })
  
  it('should handle failed API call', () => {
    cy.intercept('GET', '/api/products', {
      statusCode: 500,
      body: { error: 'Server Error' }
    })
    
    cy.visit('/products')
    
    cy.get('[data-testid="error-message"]')
      .should('be.visible')
      .and('contain', 'Failed to load products')
  })
  
  it('should mock sequential responses', () => {
    const responses = [
      { id: 1, name: 'First' },
      { id: 2, name: 'Second' },
      { id: 3, name: 'Third' }
    ]
    
    let callCount = 0
    
    cy.intercept('GET', '/api/items', (req) => {
      req.reply(responses[callCount])
      callCount++
    }).as('getItems')
    
    // ทดสอบ pagination หรือ infinite scroll
    cy.get('[data-testid="load-more"]').click()
    cy.wait('@getItems')
    
    cy.get('[data-testid="load-more"]').click()
    cy.wait('@getItems')
  })
})
```

### **5.3 Database Testing:**
```javascript
// cypress/plugins/index.js
const mysql = require('mysql2/promise')

module.exports = (on, config) => {
  on('task', {
    async queryDB({ query, values = [] }) {
      const connection = await mysql.createConnection({
        host: 'localhost',
        user: 'root',
        password: 'password',
        database: 'test_db'
      })
      
      try {
        const [rows] = await connection.execute(query, values)
        await connection.end()
        return rows
      } catch (error) {
        await connection.end()
        throw error
      }
    },
    
    async resetDB() {
      const connection = await mysql.createConnection({
        host: 'localhost',
        user: 'root',
        password: 'password',
        database: 'test_db'
      })
      
      try {
        await connection.execute('DELETE FROM users WHERE email LIKE "%@test.com"')
        await connection.execute('DELETE FROM orders WHERE created_at < DATE_SUB(NOW(), INTERVAL 1 DAY)')
        await connection.end()
        return null
      } catch (error) {
        await connection.end()
        throw error
      }
    }
  })
}

// cypress/e2e/db-tests.cy.js
describe('Database Tests', () => {
  beforeEach(() => {
    cy.task('resetDB')
  })
  
  it('should verify user creation in database', () => {
    // สร้าง user ผ่าน UI
    cy.visit('/register')
    cy.get('[data-testid="name"]').type('Test User')
    cy.get('[data-testid="email"]').type('test@test.com')
    cy.get('[data-testid="password"]').type('password123')
    cy.get('[data-testid="register-button"]').click()
    
    // ตรวจสอบใน database
    cy.task('queryDB', {
      query: 'SELECT * FROM users WHERE email = ?',
      values: ['test@test.com']
    }).then((results) => {
      expect(results).to.have.length(1)
      expect(results[0].name).to.equal('Test User')
      expect(results[0].email).to.equal('test@test.com')
    })
  })
})
```

### **5.4 Visual Testing:**
```javascript
// cypress/e2e/visual.cy.js
import { addMatchImageSnapshotCommand } from 'cypress-image-snapshot/command'

// Setup command
addMatchImageSnapshotCommand({
  failureThreshold: 0.03, // 3% threshold
  failureThresholdType: 'percent',
  customDiffConfig: { threshold: 0.1 },
  capture: 'viewport'
})

describe('Visual Regression Tests', () => {
  it('should match homepage screenshot', () => {
    cy.visit('/')
    cy.matchImageSnapshot('homepage')
  })
  
  it('should match login page', () => {
    cy.visit('/login')
    cy.matchImageSnapshot('login-page')
  })
  
  it('should test responsive design', () => {
    // Test mobile view
    cy.viewport('iphone-x')
    cy.visit('/')
    cy.matchImageSnapshot('homepage-mobile')
    
    // Test tablet view
    cy.viewport('ipad-2')
    cy.visit('/')
    cy.matchImageSnapshot('homepage-tablet')
    
    // Test desktop view
    cy.viewport(1920, 1080)
    cy.visit('/')
    cy.matchImageSnapshot('homepage-desktop')
  })
  
  it('should ignore dynamic content', () => {
    cy.visit('/dashboard')
    
    // Ignore timestamp elements
    cy.matchImageSnapshot('dashboard', {
      blackout: ['[data-testid="timestamp"]'],
      disableTimersAndAnimations: true
    })
  })
})
```

### **5.5 Performance Testing:**
```javascript
// cypress/e2e/performance.cy.js

describe('Performance Tests', () => {
  it('should measure page load time', () => {
    cy.visit('/', {
      onBeforeLoad: (win) => {
        win.performance.mark('start-loading')
      },
      onLoad: (win) => {
        win.performance.mark('end-loading')
      }
    })
    
    cy.window().then((win) => {
      win.performance.measure('pageLoad', 'start-loading', 'end-loading')
      const measure = win.performance.getEntriesByName('pageLoad')[0]
      
      expect(measure.duration).to.be.lessThan(3000) // ควรโหลดภายใน 3 วินาที
      
      cy.log(`Page loaded in ${measure.duration}ms`)
    })
  })
  
  it('should test network performance', () => {
    cy.intercept('**/*').as('allRequests')
    
    cy.visit('/')
    
    cy.wait('@allRequests').then((interception) => {
      expect(interception.response.statusCode).to.equal(200)
      expect(interception.request.headers).to.have.property('user-agent')
    })
    
    // วัดเวลาการตอบสนองของ API
    cy.intercept('GET', '/api/products').as('products')
    
    cy.get('[data-testid="load-products"]').click()
    
    cy.wait('@products').then((interception) => {
      expect(interception.response.duration).to.be.lessThan(1000)
      cy.log(`API response time: ${interception.response.duration}ms`)
    })
  })
  
  it('should test memory usage', () => {
    cy.visit('/')
    
    // วัด memory ก่อนใช้งาน
    cy.window().then((win) => {
      const initialMemory = win.performance.memory
      cy.log(`Initial memory: ${JSON.stringify(initialMemory)}`)
      
      // ทำ action หลายๆ ครั้ง
      for (let i = 0; i < 10; i++) {
        cy.get('[data-testid="add-item"]').click()
      }
      
      // วัด memory หลังใช้งาน
      cy.window().then((win2) => {
        const finalMemory = win2.performance.memory
        cy.log(`Final memory: ${JSON.stringify(finalMemory)}`)
        
        const memoryIncrease = finalMemory.usedJSHeapSize - initialMemory.usedJSHeapSize
        expect(memoryIncrease).to.be.lessThan(10000000) // ไม่งเพิ่มเกิน 10MB
      })
    })
  })
})
```

---

## **6. CI/CD Integration**

### **6.1 GitHub Actions Configuration:**
```yaml
# .github/workflows/cypress-tests.yml
name: Cypress Tests
on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  cypress-run:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        containers: [1, 2, 3] # รัน parallel 3 containers
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Start development server
        run: npm start &
        env:
          NODE_ENV: test
      
      - name: Wait for server
        run: npx wait-on http://localhost:3000
      
      - name: Run Cypress tests
        uses: cypress-io/github-action@v5
        with:
          browser: chrome
          record: true
          parallel: true
          group: 'UI Tests'
          spec: cypress/e2e/**/*.cy.js
        env:
          CYPRESS_RECORD_KEY: ${{ secrets.CYPRESS_RECORD_KEY }}
          CYPRESS_PROJECT_ID: ${{ secrets.CYPRESS_PROJECT_ID }}
          CYPRESS_baseUrl: http://localhost:3000
      
      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: cypress-results-${{ matrix.containers }}
          path: |
            cypress/screenshots
            cypress/videos
            cypress/reports
      
      - name: Upload to Cypress Dashboard
        if: always()
        uses: cypress-io/cypress-dashboard-action@v1
        with:
          record: true
          group: 'E2E Tests'
          parallel: true
```

### **6.2 Jenkins Pipeline:**
```groovy
// Jenkinsfile
pipeline {
    agent any
    
    environment {
        CYPRESS_PROJECT_ID = credentials('cypress-project-id')
        CYPRESS_RECORD_KEY = credentials('cypress-record-key')
    }
    
    stages {
        stage('Setup') {
            steps {
                sh 'npm ci'
            }
        }
        
        stage('Start Server') {
            steps {
                sh 'npm start &'
                sh 'npx wait-on http://localhost:3000'
            }
        }
        
        stage('Run Tests') {
            parallel {
                stage('Chrome Tests') {
                    steps {
                        sh 'npx cypress run --browser chrome --record --parallel'
                    }
                }
                
                stage('Firefox Tests') {
                    steps {
                        sh 'npx cypress run --browser firefox --record --parallel'
                    }
                }
                
                stage('Component Tests') {
                    steps {
                        sh 'npx cypress run --component --record'
                    }
                }
            }
        }
        
        stage('Reports') {
            steps {
                sh 'npx mochawesome-merge cypress/reports/*.json > merged-report.json'
                sh 'npx marge merged-report.json --reportDir reports'
                
                archiveArtifacts artifacts: 'reports/**/*'
                junit 'cypress/results/**/*.xml'
            }
        }
    }
    
    post {
        always {
            sh 'pkill -f "npm start" || true'
            
            // Send notifications
            emailext (
                subject: "Test Results: ${currentBuild.result}",
                body: "Build ${env.BUILD_NUMBER} completed with status: ${currentBuild.result}",
                to: 'team@example.com'
            )
        }
        
        success {
            // Update deployment status
            sh 'echo "Tests passed successfully"'
        }
        
        failure {
            // Send alert
            sh 'echo "Tests failed. Check Cypress Dashboard for details"'
        }
    }
}
```

---

## **7. Best Practices และ Tips**

### **Do's:**
```javascript
// ✅ ใช้ data-testid แทน CSS selectors
cy.get('[data-testid="login-button"]').click()

// ✅ ใช้ custom commands สำหรับ重复代码
cy.login('user@example.com', 'password')

// ✅ ใช้ fixtures สำหรับ test data
cy.fixture('users.json').then((users) => {
  // ใช้ users.valid, users.admin
})

// ✅ ใช้ Page Object Pattern
LoginPage.typeEmail('test@example.com')

// ✅ ใช้ .should() สำหรับ assertions
cy.get('.element').should('be.visible')
cy.get('.element').should('have.text', 'Expected Text')

// ✅ ใช้ cy.intercept() สำหรับ mocking
cy.intercept('GET', '/api/users', { fixture: 'users.json' })
```

### **Don'ts:**
```javascript
// ❌ หลีกเลี่ยง cy.wait() กับเวลา fixed
cy.wait(5000) // ไม่ดี

// ❌ หลีกเลี่ยง force: true ถ้าไม่จำเป็น
cy.get('button').click({ force: true }) // ไม่ดี

// ❌ หลีกเลี่ยง .then() มากเกินไป
cy.get('input').then(($input) => {
  cy.wrap($input).type('text') // ไม่ดี
})

// ✅ ใช้แบบนี้แทน
cy.get('input').type('text')
```

### **Debugging Tips:**
```javascript
// 1. ใช้ cy.pause() หยุด execution
cy.get('button').click()
cy.pause() // หยุดที่นี่
cy.get('.result').should('be.visible')

// 2. ใช้ cy.debug() แสดงข้อมูล
cy.get('input').debug() // แสดง jQuery object ใน console

// 3. ใช้ .then() กับ console.log
cy.get('.element').then(($el) => {
  console.log('Element found:', $el)
})

// 4. ใช้ Cypress.log() สำหรับ custom logs
Cypress.log({
  name: 'custom',
  message: 'Custom log message',
  consoleProps: () => {
    return {
      'Custom Property': 'value'
    }
  }
})
```

---

## **8. Test Report และ Dashboard**

### **Local Report:**
```json
// package.json scripts
{
  "scripts": {
    "test": "cypress run",
    "test:report": "cypress run --reporter mochawesome",
    "test:chrome": "cypress run --browser chrome",
    "test:firefox": "cypress run --browser firefox",
    "test:record": "cypress run --record --key your-record-key",
    "test:parallel": "cypress run --record --parallel --ci-build-id $BUILD_ID"
  }
}
```

### **Cypress Dashboard Features:**
```
📊 **Analytics Dashboard:**
  - Test Run History
  - Failure Trends
  - Flaky Test Detection
  - Performance Metrics

🔧 **Debugging Tools:**
  - Time Travel Debugger
  - Network Request Logs
  - Console Output
  - Screenshots & Videos

🔄 **CI/CD Integration:**
  - Parallel Test Execution
  - Smart Test Orchestration
  - Load Balancing
  - Test Analytics
```

---

## **สรุป: Cypress เหมาะกับใคร?**

### **เหมาะสำหรับ:**
- **Frontend Developers** ที่ต้องการเขียน E2E tests
- **QA Engineers** ที่ต้องการ automated testing
- **Teams** ที่ใช้ JavaScript/TypeScript
- **Projects** ที่ต้องการ fast feedback loop
- **Applications** ที่มี complex user interactions

### **ข้อดี:**
- Developer-friendly API
- Fast execution
- Great debugging tools
- Active community
- Good documentation

### **ข้อจำกัด:**
- รันได้แค่บน Chromium-based browsers (Firefox รองรับบาง feature)
- ไม่รองรับ multiple tabs/windows
- ต้องรันใน iframe (บางกรณีอาจมีปัญหา)
- ใช้ทรัพยากรค่อนข้างสูง

### **เลือกใช้เมื่อ:**
- ทีมคุ้นเคยกับ JavaScript/TypeScript
- ต้องการ development experience ที่ดี
- ต้องการ real-time feedback
- มี budget สำหรับ Cypress Dashboard (optional)

**Cypress เป็นเครื่องมือที่ทรงพลังสำหรับการเขียนและรัน automated tests ที่มี learning curve ไม่สูง แต่ให้ผลลัพธ์ที่ยอดเยี่ยม!** 🚀



# **การใช้ AI Tools สำหรับตั้งค่าและเขียน Cypress Tests**

## **1. AI Tools รายยอดสำหรับ Cypress**

### **Top AI Tools:**
```
🤖 **GitHub Copilot** - AI pair programming
🤖 **ChatGPT/Claude** - Generate test code
🤖 **Cypress AI** (beta) - Official AI tools
🤖 **Testim** - AI-powered test creation
🤖 **Mabl** - Intelligent test automation
🤖 **Applittools** - Visual AI testing
🤖 **CodiumAI** - Test generation
```

---

## **2. GitHub Copilot + Cypress**

### **2.1 Setup และ Configuration:**
```json
// .vscode/settings.json
{
  "editor.suggestSelection": "recentlyUsed",
  "editor.acceptSuggestionOnEnter": "on",
  "github.copilot.enable": {
    "*": true,
    "javascript": true,
    "typescript": true,
    "plaintext": false,
    "markdown": false,
    "scminput": false
  },
  
  // Cypress-specific settings
  "files.associations": {
    "*.cy.js": "javascript",
    "*.cy.ts": "typescript"
  },
  
  // Snippet suggestions
  "github.copilot.inlineSuggest.enable": true,
  "github.copilot.editor.enableCodeActions": true
}
```

### **2.2 Prompt Patterns สำหรับ Copilot:**
```javascript
// Example 1: Generate complete test file
// Comment: "Write a Cypress test for user login functionality"
// Copilot จะ generate:

describe('User Login', () => {
  beforeEach(() => {
    cy.visit('/login')
  })

  it('should login with valid credentials', () => {
    cy.get('[data-cy=email]').type('user@example.com')
    cy.get('[data-cy=password]').type('password123')
    cy.get('[data-cy=login-btn]').click()
    cy.url().should('include', '/dashboard')
    cy.get('[data-cy=welcome]').should('contain', 'Welcome')
  })

  it('should show error with invalid credentials', () => {
    cy.get('[data-cy=email]').type('wrong@email.com')
    cy.get('[data-cy=password]').type('wrong')
    cy.get('[data-cy=login-btn]').click()
    cy.get('[data-cy=error]').should('be.visible')
  })
})

// Example 2: Generate custom command
// Comment: "Create a custom Cypress command to login user"
// Copilot จะ generate:

Cypress.Commands.add('login', (email, password) => {
  cy.session([email, password], () => {
    cy.visit('/login')
    cy.get('[data-cy=email]').type(email)
    cy.get('[data-cy=password]').type(password)
    cy.get('[data-cy=login-btn]').click()
    cy.url().should('include', '/dashboard')
  })
})

// Example 3: Generate Page Object
// Comment: "Create a Page Object for checkout page"
// Copilot จะ generate:

class CheckoutPage {
  elements = {
    firstName: () => cy.get('[data-cy=first-name]'),
    lastName: () => cy.get('[data-cy=last-name]'),
    email: () => cy.get('[data-cy=email]'),
    address: () => cy.get('[data-cy=address]'),
    city: () => cy.get('[data-cy=city]'),
    zipCode: () => cy.get('[data-cy=zip]'),
    submitBtn: () => cy.get('[data-cy=submit-order]')
  }

  fillForm(data) {
    this.elements.firstName().type(data.firstName)
    this.elements.lastName().type(data.lastName)
    this.elements.email().type(data.email)
    this.elements.address().type(data.address)
    this.elements.city().type(data.city)
    this.elements.zipCode().type(data.zipCode)
  }

  submit() {
    this.elements.submitBtn().click()
  }
}

export default new CheckoutPage()
```

---

## **3. ChatGPT/Claude สำหรับ Cypress Setup**

### **3.1 Prompt Templates:**

#### **Prompt 1: Generate Cypress Configuration**
```
คุณเป็น Senior QA Automation Engineer ที่เชี่ยวชาญ Cypress
ให้สร้างไฟล์ cypress.config.js ที่สมบูรณ์สำหรับโปรเจคที่มี:
- Base URL: http://localhost:3000
- รองรับ TypeScript
- มี video recording
- มี screenshot on failure
- รองรับ multiple browsers
- มี environment variables
- ตั้งค่า reporter เป็น mochawesome
- ตั้งค่า retry 2 ครั้งสำหรับ CI
- ตั้งค่า viewport เป็น 1280x720
- ใช้งาน plugins
- มี security best practices
```

**Output จาก AI:**
```typescript
// cypress.config.ts
import { defineConfig } from 'cypress'
import * as dotenv from 'dotenv'

// Load environment variables
dotenv.config()

export default defineConfig({
  // Project configuration
  projectId: process.env.CYPRESS_PROJECT_ID,
  
  // E2E Testing
  e2e: {
    baseUrl: process.env.CYPRESS_BASE_URL || 'http://localhost:3000',
    specPattern: 'cypress/e2e/**/*.{js,jsx,ts,tsx}',
    supportFile: 'cypress/support/e2e.ts',
    experimentalStudio: true,
    
    // Viewport
    viewportWidth: 1280,
    viewportHeight: 720,
    
    // Retry logic
    retries: {
      runMode: 2,
      openMode: 0
    },
    
    // Setup events
    setupNodeEvents(on, config) {
      // Load plugins
      require('./cypress/plugins/index.ts')(on, config)
      
      // Environment variables
      config.env = {
        ...config.env,
        API_URL: process.env.API_URL,
        AUTH_TOKEN: process.env.AUTH_TOKEN,
        TEST_USER_EMAIL: process.env.TEST_USER_EMAIL,
        TEST_USER_PASSWORD: process.env.TEST_USER_PASSWORD
      }
      
      // Security: Mask sensitive data
      on('task', {
        log(message) {
          console.log(message)
          return null
        },
        maskSensitiveData(value) {
          if (typeof value === 'string') {
            // Mask passwords, tokens, etc.
            return value.replace(
              /(password|token|secret|key)=[^&]+/gi, 
              '$1=***MASKED***'
            )
          }
          return value
        }
      })
      
      return config
    }
  },
  
  // Component Testing
  component: {
    devServer: {
      framework: 'react',
      bundler: 'vite'
    }
  },
  
  // Environment variables
  env: {
    // Add custom env vars here
    TAGS: 'not @ignore',
    GREP: undefined
  },
  
  // Reporter configuration
  reporter: 'mochawesome',
  reporterOptions: {
    reportDir: 'cypress/reports',
    overwrite: false,
    html: true,
    json: true,
    charts: true,
    reportPageTitle: 'Cypress Test Report',
    embeddedScreenshots: true,
    inlineAssets: true
  },
  
  // Videos
  video: true,
  videoCompression: 32,
  videosFolder: 'cypress/videos',
  
  // Screenshots
  screenshotOnRunFailure: true,
  screenshotsFolder: 'cypress/screenshots',
  trashAssetsBeforeRuns: true,
  
  // Browser configuration
  browsers: [
    {
      name: 'chrome',
      family: 'chromium',
      channel: 'stable',
      displayName: 'Chrome',
      version: '116.0.5845.96'
    },
    {
      name: 'firefox',
      family: 'firefox',
      channel: 'stable',
      displayName: 'Firefox',
      version: '116.0'
    },
    {
      name: 'edge',
      family: 'chromium',
      channel: 'stable',
      displayName: 'Edge',
      version: '116.0.1938.54'
    }
  ],
  
  // Parallelization
  parallel: true,
  experimentalMemoryManagement: true,
  
  // Timeouts
  defaultCommandTimeout: 10000,
  execTimeout: 60000,
  taskTimeout: 60000,
  pageLoadTimeout: 60000,
  requestTimeout: 5000,
  responseTimeout: 30000,
  
  // Downloads
  downloadsFolder: 'cypress/downloads',
  
  // Security
  chromeWebSecurity: true,
  modifyObstructiveCode: false
})
```

#### **Prompt 2: Generate Test Structure**
```
สร้างโครงสร้าง Cypress project ที่สมบูรณ์พร้อม:
1. Folder structure
2. Custom commands ที่จำเป็น
3. Page Objects สำหรับระบบ e-commerce
4. Fixtures สำหรับ test data
5. Plugins สำหรับ database access
6. Support files
7. Utility functions
```

**Output จาก AI:**
```bash
# Folder Structure ที่ AI generate
cypress-project/
├── cypress/
│   ├── e2e/
│   │   ├── auth/                 # Authentication tests
│   │   │   ├── login.cy.js
│   │   │   ├── register.cy.js
│   │   │   └── logout.cy.js
│   │   │
│   │   ├── shop/                 # Shopping tests
│   │   │   ├── browse.cy.js
│   │   │   ├── cart.cy.js
│   │   │   └── checkout.cy.js
│   │   │
│   │   ├── admin/                # Admin tests
│   │   │   ├── dashboard.cy.js
│   │   │   └── products.cy.js
│   │   │
│   │   └── api/                  # API tests
│   │       └── api-tests.cy.js
│   │
│   ├── fixtures/
│   │   ├── users.json
│   │   ├── products.json
│   │   ├── orders.json
│   │   └── api/
│   │       └── responses.json
│   │
│   ├── pages/                    # Page Objects
│   │   ├── LoginPage.js
│   │   ├── RegisterPage.js
│   │   ├── ProductPage.js
│   │   ├── CartPage.js
│   │   ├── CheckoutPage.js
│   │   └── AdminPage.js
│   │
│   ├── support/
│   │   ├── commands.js          # Custom commands
│   │   ├── e2e.js              # Global hooks
│   │   └── utils.js            # Utility functions
│   │
│   ├── plugins/
│   │   └── index.js
│   │
│   └── types/                    # TypeScript definitions
│       └── index.d.ts
│
├── cypress.config.js
├── package.json
└── README.md
```

#### **Prompt 3: Generate Complete Test File**
```
เขียน Cypress test สำหรับฟีเจอร์ "Add to Cart" ของ e-commerce website โดยมี requirements:
1. User สามารถ browse products ได้
2. User สามารถ filter products by category
3. User สามารถ sort products by price
4. User สามารถ add product to cart
5. User สามารถ view cart
6. User สามารถ update quantity
7. User สามารถ remove item from cart
8. ใช้ Page Object Pattern
9. ใช้ custom commands
10. ใช้ fixtures สำหรับ test data
11. มีทั้ง positive และ negative tests
12. มี accessibility testing
13. มี visual testing
14. ใช้ TypeScript
```

**Output จาก AI:**
```typescript
// cypress/e2e/shop/add-to-cart.cy.ts
import ProductPage from '../../pages/ProductPage'
import CartPage from '../../pages/CartPage'
import { Product, User } from '../../types'

describe('Add to Cart Feature', () => {
  let testUser: User
  let testProducts: Product[]

  before(() => {
    // Load test data
    cy.fixture('users.json').then((users) => {
      testUser = users.validCustomer
    })
    
    cy.fixture('products.json').then((products) => {
      testProducts = products
    })
    
    // Login user
    cy.login(testUser.email, testUser.password)
  })

  beforeEach(() => {
    // Visit products page before each test
    ProductPage.visit()
  })

  context('Product Browsing', () => {
    it('should display products correctly', () => {
      // Check product listing
      ProductPage.getProductCards().should('have.length.at.least', 1)
      
      // Check product details
      ProductPage.getProductCard(0).within(() => {
        ProductPage.getProductName().should('be.visible')
        ProductPage.getProductPrice().should('be.visible')
        ProductPage.getAddToCartButton().should('be.visible')
      })
      
      // Accessibility check
      cy.injectAxe()
      cy.checkA11y()
    })

    it('should filter products by category', () => {
      const category = 'electronics'
      
      ProductPage.selectCategory(category)
      ProductPage.getProductCards().each(($card) => {
        cy.wrap($card).should('contain', category)
      })
    })

    it('should sort products by price', () => {
      const prices: number[] = []
      
      // Get initial prices
      ProductPage.getProductPrices().each(($price) => {
        const priceText = $price.text()
        const price = parseFloat(priceText.replace(/[^0-9.-]+/g, ''))
        prices.push(price)
      })
      
      // Sort low to high
      ProductPage.sortBy('price-low-high')
      
      // Verify sorting
      ProductPage.getProductPrices().each(($price, index) => {
        const priceText = $price.text()
        const price = parseFloat(priceText.replace(/[^0-9.-]+/g, ''))
        
        if (index > 0) {
          const prevPrice = parseFloat(
            cy.$$(ProductPage.selectors.productPrice).eq(index - 1).text()
              .replace(/[^0-9.-]+/g, '')
          )
          expect(price).to.be.at.least(prevPrice)
        }
      })
    })
  })

  context('Cart Operations', () => {
    it('should add product to cart', () => {
      const product = testProducts[0]
      
      ProductPage.addProductToCart(product.id)
      
      // Verify cart notification
      ProductPage.getCartBadge().should('contain', '1')
      
      // Verify product added to cart
      CartPage.visit()
      CartPage.getCartItems().should('have.length', 1)
      CartPage.getCartItem(0).should('contain', product.name)
    })

    it('should update product quantity in cart', () => {
      const product = testProducts[0]
      
      // Add product first
      ProductPage.addProductToCart(product.id)
      CartPage.visit()
      
      // Increase quantity
      CartPage.increaseQuantity(0, 2)
      CartPage.getItemQuantity(0).should('contain', '3')
      
      // Decrease quantity
      CartPage.decreaseQuantity(0)
      CartPage.getItemQuantity(0).should('contain', '2')
    })

    it('should remove product from cart', () => {
      const product = testProducts[0]
      
      ProductPage.addProductToCart(product.id)
      CartPage.visit()
      
      // Remove item
      CartPage.removeItem(0)
      
      // Verify cart is empty
      CartPage.getEmptyCartMessage().should('be.visible')
      CartPage.getCartItems().should('have.length', 0)
    })

    it('should calculate total price correctly', () => {
      // Add multiple products
      testProducts.slice(0, 3).forEach((product) => {
        ProductPage.addProductToCart(product.id)
      })
      
      CartPage.visit()
      
      let expectedTotal = 0
      
      // Calculate expected total
      CartPage.getCartItems().each(($item, index) => {
        CartPage.getItemPrice(index).then(($price) => {
          const price = parseFloat($price.text().replace(/[^0-9.-]+/g, ''))
          CartPage.getItemQuantity(index).then(($quantity) => {
            const quantity = parseInt($quantity.text())
            expectedTotal += price * quantity
          })
        })
      })
      
      // Verify total
      CartPage.getCartTotal().then(($total) => {
        const total = parseFloat($total.text().replace(/[^0-9.-]+/g, ''))
        expect(total).to.equal(expectedTotal)
      })
    })
  })

  context('Negative Tests', () => {
    it('should not add out of stock product to cart', () => {
      const outOfStockProduct = testProducts.find(p => p.stock === 0)
      
      if (outOfStockProduct) {
        ProductPage.visitProduct(outOfStockProduct.id)
        ProductPage.getAddToCartButton().should('be.disabled')
        ProductPage.getOutOfStockBadge().should('be.visible')
      }
    })

    it('should validate maximum quantity', () => {
      const product = testProducts[0]
      
      ProductPage.addProductToCart(product.id)
      CartPage.visit()
      
      // Try to exceed max quantity
      CartPage.setQuantity(0, product.maxQuantity + 1)
      
      // Should show validation error
      CartPage.getQuantityError(0).should('be.visible')
      CartPage.getItemQuantity(0).should('contain', product.maxQuantity.toString())
    })
  })

  context('Visual Testing', () => {
    it('should match cart page screenshot', () => {
      // Add items to cart
      testProducts.slice(0, 2).forEach((product) => {
        ProductPage.addProductToCart(product.id)
      })
      
      CartPage.visit()
      
      // Take visual snapshot
      cy.matchImageSnapshot('cart-page-with-items')
    })
  })

  afterEach(() => {
    // Clear cart after each test
    cy.clearCart()
  })
})
```

---

## **4. Cypress AI (Official Beta)**

### **4.1 Setup Cypress AI:**
```bash
# 1. ติดตั้ง Cypress AI
npm install -D @cypress/ai

# 2. เพิ่มใน plugins
// cypress/plugins/index.js
const { initPlugin } = require('@cypress/ai')

module.exports = (on, config) => {
  initPlugin(on, config)
  
  // AI Configuration
  config.env.AI_API_KEY = process.env.OPENAI_API_KEY
  config.env.AI_MODEL = 'gpt-4' // หรือ 'gpt-3.5-turbo'
  
  return config
}

# 3. เพิ่มใน support file
// cypress/support/e2e.js
import '@cypress/ai/support'
```

### **4.2 ใช้ Cypress AI Commands:**
```javascript
// 1. AI-Powered Test Generation
cy.ai('Write a test for user registration with validation')

// 2. AI-Powered Selector Generation
cy.ai('Find the best selector for login button')

// 3. AI-Powered Test Debugging
cy.ai('Why is this test failing?', { 
  context: cy.getCurrentTest() 
})

// 4. AI-Powered Test Optimization
cy.ai('Optimize this test for better performance')

// Example: Complete AI-powered test creation
describe('AI-Generated Checkout Tests', () => {
  it('should complete checkout process', () => {
    // AI จะ generate test steps based on description
    cy.ai(`
      Test scenario: User completes checkout
      Steps:
      1. Add product to cart
      2. Proceed to checkout
      3. Fill shipping information
      4. Select payment method
      5. Place order
      6. Verify order confirmation
    `)
  })
})
```

---

## **5. Testim (AI-Powered Testing Platform)**

### **5.1 Integration with Cypress:**
```javascript
// 1. ติดตั้ง Testim SDK
npm install @testim/testim-cli --save-dev

// 2. Testim Configuration
// .testim.config.js
module.exports = {
  project: 'your-project-id',
  token: process.env.TESTIM_TOKEN,
  grid: {
    url: 'https://grid.testim.io',
    project: 'your-project-name'
  },
  cypress: {
    specsFolder: 'cypress/e2e',
    supportFile: 'cypress/support/e2e.js',
    videosFolder: 'cypress/videos',
    screenshotsFolder: 'cypress/screenshots'
  },
  ai: {
    enabled: true,
    model: 'gpt-4',
    suggestions: true,
    autoFix: true
  }
}

// 3. AI-Powered Test Creation
// testim-create-test.js
const { testim } = require('@testim/testim-cli')

async function createAITest() {
  const test = await testim.tests.create({
    name: 'AI-Generated Checkout Test',
    description: 'Test the complete checkout flow',
    aiInstructions: `
      Create a test for e-commerce checkout:
      1. User adds product to cart
      2. User proceeds to checkout
      3. User fills shipping form
      4. User selects payment method
      5. User places order
      6. Verify order confirmation
      
      Use:
      - Page Object pattern
      - Custom commands for login
      - Fixtures for test data
      - Assertions for validation
    `,
    framework: 'cypress',
    tags: ['checkout', 'e2e', 'ai-generated']
  })
  
  console.log('Test created:', test.id)
  
  // Generate test code
  const testCode = await testim.ai.generateTestCode(test.id)
  
  // Save to Cypress folder
  require('fs').writeFileSync(
    'cypress/e2e/ai-checkout.cy.js',
    testCode
  )
}

createAITest()

// 4. Run Tests with AI Analysis
// package.json scripts
{
  "scripts": {
    "test:ai": "testim run --ai-analysis",
    "test:ai-fix": "testim run --ai-auto-fix",
    "test:ai-generate": "testim tests generate --ai"
  }
}
```

### **5.2 AI-Powered Maintenance:**
```javascript
// testim-ai-maintenance.js
const { testim } = require('@testim/testim-cli')

// 1. AI-Powered Flaky Test Detection
testim.ai.analyzeFlakyTests({
  threshold: 0.3, // 30% flakiness threshold
  timeframe: '7d'
}).then(analysis => {
  console.log('Flaky tests found:', analysis.flakyTests)
  
  // AI suggestions for fixing
  analysis.suggestions.forEach(suggestion => {
    console.log(`Fix for ${suggestion.test}:`, suggestion.fix)
  })
})

// 2. AI-Powered Test Optimization
testim.ai.optimizeTests({
  target: 'performance', // 'performance', 'reliability', 'maintainability'
  tests: ['checkout-test', 'login-test']
}).then(optimizations => {
  optimizations.forEach(opt => {
    console.log(`Optimization for ${opt.test}:`)
    console.log('Before:', opt.before)
    console.log('After:', opt.after)
    console.log('Improvement:', opt.improvement)
  })
})

// 3. AI-Powered Self-Healing Tests
// testim-self-healing.js
Cypress.Commands.add('aiFindElement', (description, options = {}) => {
  return cy.task('aiFindElement', {
    description,
    options,
    pageSource: cy.document().then(doc => doc.documentElement.outerHTML)
  }).then(selector => {
    return cy.get(selector)
  })
})

// ใน test
cy.aiFindElement('the login button').click()
cy.aiFindElement('email input field').type('user@example.com')
```

---

## **6. Mabl (Intelligent Test Automation)**

### **6.1 Mabl + Cypress Integration:**
```yaml
# mabl.config.yml
version: 2

cypress:
  enabled: true
  configFile: cypress.config.js
  specPattern: "cypress/e2e/**/*.cy.{js,ts}"
  
ai:
  enabled: true
  model: mabl-ai-v2
  
  # AI Features
  features:
    - test_generation
    - self_healing
    - flaky_detection
    - performance_optimization
  
  # Training data
  training:
    urls:
      - https://your-app.com
    user_flows:
      - login_to_checkout
      - search_to_purchase
  
  # Element intelligence
  element_intelligence:
    enabled: true
    confidence_threshold: 0.8
    fallback_selectors: ['data-testid', 'name', 'id', 'class']

# Test generation templates
test_templates:
  ecommerce_checkout:
    steps:
      - action: navigate
        url: "/products"
      - action: click
        ai_description: "product to add to cart"
      - action: click
        ai_description: "cart icon"
      - action: click
        ai_description: "checkout button"
      - action: fill
        ai_description: "shipping form"
      - action: click
        ai_description: "place order button"
    assertions:
      - ai_description: "order confirmation is visible"
      - ai_description: "order number is displayed"

# CI/CD Integration
ci:
  - name: github-actions
    parallel: true
    browsers:
      - chrome
      - firefox
    regions:
      - us-east-1
      - eu-west-1
```

### **6.2 AI-Powered Test Generation:**
```javascript
// mabl-ai-test-generation.js
const mabl = require('@mablhq/mabl')

// 1. Record user flow with AI
async function recordAndGenerateTest() {
  const test = await mabl.recordFlow({
    name: 'User Registration Flow',
    startUrl: 'https://app.example.com/register',
    aiInstructions: `
      Record a new user registration:
      1. Fill registration form
      2. Submit form
      3. Verify email confirmation
      4. Complete profile setup
    `,
    options: {
      generateAssertions: true,
      generatePageObjects: true,
      optimizeSelectors: true
    }
  })
  
  // Convert to Cypress test
  const cypressTest = await mabl.ai.convertToCypress(test)
  
  // Save test
  require('fs').writeFileSync(
    'cypress/e2e/mabl-registration.cy.js',
    cypressTest
  )
}

// 2. AI-Powered Test Maintenance
async function maintainTests() {
  // Find broken tests
  const brokenTests = await mabl.ai.findBrokenTests({
    reason: 'element_not_found',
    timeframe: '30d'
  })
  
  // AI suggestions for fixes
  for (const test of brokenTests) {
    const fix = await mabl.ai.suggestFix(test, {
      context: test.errorContext,
      alternatives: ['data-testid', 'aria-label', 'text-content']
    })
    
    console.log(`Fix for ${test.name}:`, fix)
    
    // Apply fix automatically
    if (fix.confidence > 0.9) {
      await mabl.ai.applyFix(test, fix)
    }
  }
  
  // Optimize test suite
  const optimization = await mabl.ai.optimizeSuite({
    target: 'execution_time',
    constraints: {
      maintainCoverage: true,
      maxTests: 100
    }
  })
  
  console.log('Optimization plan:', optimization)
}
```

---

## **7. Applitools (Visual AI Testing)**

### **7.1 Cypress + Applitools Integration:**
```bash
# ติดตั้ง
npm install @applitools/eyes-cypress
```

```javascript
// cypress.config.js
const { defineConfig } = require('cypress')

module.exports = defineConfig({
  e2e: {
    setupNodeEvents(on, config) {
      require('@applitools/eyes-cypress')(on, config)
    }
  }
})

// cypress/support/e2e.js
import '@applitools/eyes-cypress/commands'

// AI-Powered Visual Test
// cypress/e2e/visual-ai.cy.js
describe('Visual AI Testing', () => {
  beforeEach(() => {
    cy.eyesOpen({
      appName: 'E-Commerce App',
      testName: Cypress.currentTest.title,
      batchName: 'Visual Regression',
      
      // AI Configuration
      useAi: true,
      aiModel: 'ultrafast-grid-2.0',
      
      // Visual AI Features
      features: {
        layout: true,
        content: true,
        color: true,
        typography: true
      },
      
      // Ignore dynamic content
      ignore: [
        { selector: '.timestamp' },
        { selector: '[data-changing]' }
      ],
      
      // Layout breakpoints
      layoutBreakpoints: [375, 768, 1024, 1280]
    })
  })

  afterEach(() => {
    cy.eyesClose()
  })

  it('should verify homepage with AI', () => {
    cy.visit('/')
    
    // Full page visual check with AI
    cy.eyesCheckWindow({
      tag: 'Homepage',
      fully: true,
      useAi: true,
      
      // AI-powered validation
      aiValidation: {
        detectTextChanges: true,
        detectLayoutShifts: true,
        detectColorChanges: true,
        confidenceThreshold: 0.95
      },
      
      // Layout testing
      layout: [
        { selector: '.header', name: 'Header' },
        { selector: '.navigation', name: 'Navigation' },
        { selector: '.hero', name: 'Hero Section' },
        { selector: '.products', name: 'Products Grid' },
        { selector: '.footer', name: 'Footer' }
      ],
      
      // Content validation
      content: [
        { selector: 'h1', validation: 'text' },
        { selector: '.cta-button', validation: 'visible' }
      ]
    })
  })

  it('should detect visual regressions with AI', () => {
    cy.visit('/product/123')
    
    cy.eyesCheckWindow({
      tag: 'Product Page',
      useAi: true,
      aiFeatures: {
        // Advanced AI capabilities
        smartChecks: true,
        autoMaintenance: true,
        anomalyDetection: true
      },
      
      // Ignore acceptable differences
      ignoreDisplacements: true,
      matchLevel: 'Strict',
      
      // AI-powered baseline management
      baselineEnvName: 'production',
      saveFailedTests: true,
      saveNewTests: true
    })
  })

  it('should test responsive design with AI', () => {
    const viewports = [
      { width: 375, height: 667, name: 'iPhone SE' },
      { width: 768, height: 1024, name: 'iPad' },
      { width: 1280, height: 800, name: 'Laptop' },
      { width: 1920, height: 1080, name: 'Desktop' }
    ]
    
    viewports.forEach(viewport => {
      cy.viewport(viewport.width, viewport.height)
      cy.visit('/')
      
      cy.eyesCheckWindow({
        tag: `Homepage - ${viewport.name}`,
        useAi: true,
        viewportSize: { 
          width: viewport.width, 
          height: viewport.height 
        },
        
        // Responsive AI validation
        responsiveValidation: true,
        layoutBreakpoints: true
      })
    })
  })
})
```

### **7.2 AI-Powered Visual Validation:**
```javascript
// cypress/support/visual-ai-commands.js

// AI-Powered Element Validation
Cypress.Commands.add('validateWithAI', (options = {}) => {
  const {
    selector,
    validationType = 'visual',
    confidence = 0.9,
    features = ['layout', 'content', 'color']
  } = options
  
  return cy.get(selector).then(($element) => {
    return cy.task('aiVisualValidation', {
      element: $element[0].outerHTML,
      validationType,
      confidence,
      features,
      context: cy.state('window').document.documentElement.outerHTML
    })
  })
})

// Usage
cy.get('.product-card').validateWithAI({
  validationType: 'product_card',
  features: ['image', 'price', 'title', 'button'],
  confidence: 0.95
}).then((result) => {
  if (result.passed) {
    cy.log('AI validation passed')
  } else {
    cy.log('AI detected issues:', result.issues)
  }
})

// AI-Powered Dynamic Content Handling
Cypress.Commands.add('ignoreDynamicWithAI', (selectors) => {
  return cy.task('aiIdentifyDynamic', {
    selectors,
    pageSource: cy.document().then(doc => doc.documentElement.outerHTML)
  }).then((dynamicElements) => {
    // Apply ignore regions
    dynamicElements.forEach(element => {
      cy.eyesIgnore(element.selector)
    })
  })
})
```

---

## **8. CodiumAI สำหรับ Test Generation**

### **8.1 Setup CodiumAI:**
```bash
# ติดตั้ง CodiumAI VSCode extension
# หรือใช้ CLI
npm install -g @codiumai/cli
```

### **8.2 Generate Tests with CodiumAI:**
```javascript
// ใช้ comment เพื่อ generate tests
// cypress/e2e/codium-generated.cy.js

// CodiumAI จะ analyze code และ generate tests

/**
 * @codium
 * Generate Cypress tests for the login functionality
 * Include:
 * - Valid login
 * - Invalid credentials
 * - Empty fields validation
 * - Password visibility toggle
 * - Remember me checkbox
 */
describe('Login Tests (CodiumAI Generated)', () => {
  // AI จะ generate tests ตาม comment
})

// หรือใช้ CLI
// codium generate-test --framework cypress --file src/components/Login.js
```

### **8.3 AI-Powered Test Suggestions:**
```javascript
// CodiumAI จะ suggest tests ขณะเขียนโค้ด
// LoginComponent.js
class LoginComponent {
  // CodiumAI จะ suggest:
  // - Test for handleSubmit with valid data
  // - Test for handleSubmit with invalid data
  // - Test for form validation
  // - Test for error handling
}

// Generated test suggestions
describe('LoginComponent', () => {
  it('should submit form with valid data', () => {
    // AI-generated test
  })
  
  it('should show error with invalid data', () => {
    // AI-generated test
  })
})
```

---

## **9. Custom AI Integration**

### **9.1 Build Your Own AI Assistant:**
```javascript
// cypress/plugins/ai-assistant.js
const { Configuration, OpenAIApi } = require('openai')

module.exports = (on, config) => {
  const configuration = new Configuration({
    apiKey: process.env.OPENAI_API_KEY
  })
  
  const openai = new OpenAIApi(configuration)
  
  on('task', {
    async generateTest({ description, context }) {
      const prompt = `
        You are a Cypress test automation expert.
        Generate a Cypress test based on: ${description}
        
        Context: ${JSON.stringify(context)}
        
        Requirements:
        1. Use Page Object pattern
        2. Include both positive and negative tests
        3. Use custom commands where appropriate
        4. Include proper assertions
        5. Handle async operations correctly
        6. Include accessibility testing
        7. Use TypeScript
        
        Generate complete test file with imports and describe block.
      `
      
      const response = await openai.createCompletion({
        model: 'text-davinci-003',
        prompt,
        max_tokens: 2000,
        temperature: 0.7
      })
      
      return response.data.choices[0].text
    },
    
    async analyzeTestFailure({ error, testCode, pageSource }) {
      const prompt = `
        Analyze this Cypress test failure:
        
        Error: ${error}
        
        Test Code: ${testCode}
        
        Page Source: ${pageSource.substring(0,