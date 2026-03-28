## **การทำ Automated Test ในภาษาต่างๆ**
### ✅ **Automated Testing** คืออะไร?
- การทดสอบที่รันอัตโนมัติ
- ครอบคลุมหลายระดับ: Unit, Integration, End-to-End
- ทำใน CI/CD Pipeline
- การทดสอบ โดย Tools 
- การทดสอบ โดย AI 
- การเก็บ Loging ผล Test



### **การทำ Automated Test ในภาษาต่างๆ พร้อม Tools และ AI**

### **1. Overview ของ Automated Testing**

### **ระดับของการทดสอบ (Testing Pyramid)**
```
        /\
       /  \
      / E2E \      (น้อยที่สุด)
     /--------\
    /Integration\
   /-------------\
  /    Unit       \   (มากที่สุด)
 /_________________\
```

### **ประเภทของการทดสอบ**
```typescript
enum TestType {
  // ระดับล่าง (มากที่สุด)
  UNIT = "UNIT",           // ทดสอบฟังก์ชันเดียว
  COMPONENT = "COMPONENT", // ทดสอบ component
  
  // ระดับกลาง
  INTEGRATION = "INTEGRATION", // ทดสอบหลาย components
  CONTRACT = "CONTRACT",       // ทดสอบ API contracts
  
  // ระดับบน (น้อยที่สุด)
  E2E = "E2E",               // ทดสอบจากผู้ใช้
  PERFORMANCE = "PERFORMANCE", // ทดสอบ performance
  SECURITY = "SECURITY"       // ทดสอบ security
}
```

---

## **2. Automated Testing ในภาษาต่างๆ**

### **2.1 JavaScript/TypeScript**
```typescript
// 📦 Package.json สำหรับ Test Stack
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:e2e": "cypress run",
    "test:integration": "jest --testPathPattern=integration",
    "test:performance": "k6 run tests/performance.js"
  },
  "devDependencies": {
    // Unit Testing
    "jest": "^29.0.0",
    "ts-jest": "^29.0.0",
    "@types/jest": "^29.0.0",
    
    // Mocking
    "jest-mock-extended": "^3.0.0",
    "msw": "^1.0.0", // Mock Service Worker
    
    // Assertion Libraries
    "@testing-library/react": "^14.0.0",
    "@testing-library/dom": "^9.0.0",
    "@testing-library/user-event": "^14.0.0",
    
    // E2E Testing
    "cypress": "^12.0.0",
    "@cypress/react": "^6.0.0",
    
    // API Testing
    "supertest": "^6.0.0",
    
    // Performance Testing
    "k6": "^0.45.0",
    
    // Visual Testing
    "@percy/cli": "^1.0.0",
    
    // AI Testing Tools
    "testim": "^1.0.0",
    "mabl": "^1.0.0"
  }
}

// 🧪 Jest Configuration (jest.config.js)
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.d.ts',
    '!src/index.ts',
    '!src/**/__tests__/**'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  testMatch: [
    '<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}',
    '<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}'
  ],
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  reporters: [
    'default',
    ['jest-junit', {
      outputDirectory: 'test-results',
      outputName: 'jest-junit.xml'
    }],
    ['jest-html-reporters', {
      publicPath: './test-results',
      filename: 'test-report.html',
      expand: true
    }]
  ]
};

// 🧠 Unit Test Examples
import { Calculator, UserService } from './services';

describe('Calculator', () => {
  // Basic Unit Test
  it('should add two numbers correctly', () => {
    const calc = new Calculator();
    expect(calc.add(2, 3)).toBe(5);
  });

  // Parameterized Test
  test.each([
    [1, 2, 3],
    [0, 0, 0],
    [-1, 1, 0],
    [1.5, 2.5, 4]
  ])('adds %i + %i = %i', (a, b, expected) => {
    expect(new Calculator().add(a, b)).toBe(expected);
  });

  // Async Test
  it('should fetch user data', async () => {
    const userService = new UserService();
    const user = await userService.getUser(1);
    expect(user).toHaveProperty('id', 1);
    expect(user).toHaveProperty('name');
  });

  // Mocking Test
  it('should call API with correct parameters', async () => {
    const mockFetch = jest.fn();
    mockFetch.mockResolvedValue({ json: () => ({ id: 1, name: 'John' }) });
    
    global.fetch = mockFetch;
    const userService = new UserService();
    
    await userService.getUser(1);
    
    expect(mockFetch).toHaveBeenCalledWith(
      'https://api.example.com/users/1'
    );
  });
});

// 🧩 Component Testing (React)
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('should render login form', () => {
    render(<LoginForm onSubmit={jest.fn()} />);
    
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /login/i })).toBeInTheDocument();
  });

  it('should validate email format', async () => {
    const user = userEvent.setup();
    const onSubmit = jest.fn();
    
    render(<LoginForm onSubmit={onSubmit} />);
    
    const emailInput = screen.getByLabelText(/email/i);
    const submitButton = screen.getByRole('button', { name: /login/i });
    
    await user.type(emailInput, 'invalid-email');
    await user.click(submitButton);
    
    expect(screen.getByText(/invalid email/i)).toBeInTheDocument();
    expect(onSubmit).not.toHaveBeenCalled();
  });

  it('should submit form with valid data', async () => {
    const user = userEvent.setup();
    const onSubmit = jest.fn();
    
    render(<LoginForm onSubmit={onSubmit} />);
    
    await user.type(screen.getByLabelText(/email/i), 'test@example.com');
    await user.type(screen.getByLabelText(/password/i), 'password123');
    await user.click(screen.getByRole('button', { name: /login/i }));
    
    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123'
    });
  });
});

// 🔗 Integration Test
import request from 'supertest';
import app from '../app';

describe('User API Integration', () => {
  it('should create a new user', async () => {
    const newUser = {
      name: 'John Doe',
      email: 'john@example.com',
      password: 'secure123'
    };

    const response = await request(app)
      .post('/api/users')
      .send(newUser)
      .expect(201);

    expect(response.body).toMatchObject({
      id: expect.any(Number),
      name: newUser.name,
      email: newUser.email
    });
    expect(response.body).not.toHaveProperty('password');
  });

  it('should return 400 for invalid data', async () => {
    const invalidUser = {
      name: '', // Empty name
      email: 'invalid-email'
    };

    await request(app)
      .post('/api/users')
      .send(invalidUser)
      .expect(400);
  });
});

// 🌐 E2E Testing with Cypress
// cypress/integration/user_registration.spec.js
describe('User Registration E2E', () => {
  beforeEach(() => {
    cy.visit('/register');
  });

  it('should register new user successfully', () => {
    // Fill form
    cy.get('[data-testid="name-input"]').type('John Doe');
    cy.get('[data-testid="email-input"]').type('john@example.com');
    cy.get('[data-testid="password-input"]').type('Password123!');
    cy.get('[data-testid="confirm-password-input"]').type('Password123!');
    
    // Submit form
    cy.get('[data-testid="submit-button"]').click();
    
    // Assertions
    cy.url().should('include', '/dashboard');
    cy.get('[data-testid="welcome-message"]')
      .should('contain', 'Welcome, John');
    
    // Verify API call
    cy.intercept('POST', '/api/users').as('createUser');
    cy.wait('@createUser').then((interception) => {
      expect(interception.request.body).to.deep.equal({
        name: 'John Doe',
        email: 'john@example.com',
        password: 'Password123!'
      });
    });
  });

  it('should show validation errors', () => {
    cy.get('[data-testid="submit-button"]').click();
    
    cy.get('[data-testid="name-error"]')
      .should('contain', 'Name is required');
    cy.get('[data-testid="email-error"]')
      .should('contain', 'Email is required');
  });
});

// 🏎️ Performance Testing with k6
// tests/performance/user_load_test.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

export const errorRate = new Rate('errors');

export const options = {
  stages: [
    { duration: '2m', target: 100 }, // Ramp up to 100 users
    { duration: '5m', target: 100 }, // Stay at 100 users
    { duration: '2m', target: 0 },   // Ramp down to 0
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% of requests < 500ms
    errors: ['rate<0.1'],             // Error rate < 10%
    'http_reqs{status:200}': ['rate>100'], // >100 req/sec for 200 OK
  },
};

export default function () {
  const credentials = {
    email: `user${__VU}@test.com`,
    password: 'password123'
  };

  const loginRes = http.post(
    'https://api.example.com/login',
    JSON.stringify(credentials),
    { headers: { 'Content-Type': 'application/json' } }
  );

  check(loginRes, {
    'login successful': (r) => r.status === 200,
    'has auth token': (r) => r.json('token') !== null,
  }) || errorRate.add(1);

  if (loginRes.status === 200) {
    const token = loginRes.json('token');
    const headers = { Authorization: `Bearer ${token}` };

    // Test authenticated endpoints
    const profileRes = http.get(
      'https://api.example.com/profile',
      { headers }
    );

    check(profileRes, {
      'profile fetched': (r) => r.status === 200,
    }) || errorRate.add(1);
  }

  sleep(1);
}

// 🎨 Visual Testing with Percy
// tests/visual/homepage.spec.js
import { percySnapshot } from '@percy/cli';

describe('Homepage Visual Tests', () => {
  it('should match homepage screenshot', () => {
    cy.visit('/');
    
    // Wait for all images to load
    cy.get('img').each(($img) => {
      cy.wrap($img).should('be.visible');
    });
    
    // Take Percy snapshot
    percySnapshot('Homepage');
  });

  it('should match mobile view', () => {
    cy.viewport('iphone-x');
    cy.visit('/');
    percySnapshot('Homepage - Mobile');
  });

  it('should match dark mode', () => {
    cy.visit('/');
    cy.get('[data-testid="theme-toggle"]').click();
    percySnapshot('Homepage - Dark Mode');
  });
});
```

### **2.2 Python**
```python
# 📦 requirements-test.txt
# Unit Testing
pytest==7.4.0
pytest-cov==4.1.0
pytest-mock==3.11.1
pytest-asyncio==0.21.0
pytest-xdist==3.3.1  # Parallel testing

# Test Frameworks
unittest2==1.1.0
nose2==0.14.0

# Mocking
moto==4.1.13  # AWS mocking
responses==0.23.1  # HTTP mocking
factory-boy==3.3.0  # Test data factories

# API Testing
requests-mock==1.11.0
httpx==0.24.1

# E2E Testing
selenium==4.11.0
playwright==1.36.0
pytest-playwright==0.4.0

# Performance Testing
locust==2.15.0

# AI Testing
testim==1.0.0

# Report Generation
pytest-html==3.2.0
allure-pytest==2.13.0
pytest-testrail==2.9.0

# 🧪 pytest Configuration (pytest.ini)
[pytest]
testpaths = tests
python_files = test_*.py *_test.py
python_classes = Test* *Test
python_functions = test_*
addopts = 
    -v
    --strict-markers
    --strict-config
    --tb=short
    --cov=src
    --cov-report=html
    --cov-report=xml:coverage.xml
    --cov-report=term-missing
    --junitxml=test-results/junit.xml
    --html=test-results/report.html
    -n auto  # Run tests in parallel
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: integration tests
    e2e: end-to-end tests
    performance: performance tests
    smoke: smoke tests

# 🧠 Unit Test Examples
import pytest
from unittest.mock import Mock, patch, MagicMock
from datetime import datetime
from decimal import Decimal

# Test class
class Calculator:
    def add(self, a: float, b: float) -> float:
        return a + b
    
    def divide(self, a: float, b: float) -> float:
        if b == 0:
            raise ValueError("Cannot divide by zero")
        return a / b

# Basic unit test
def test_add():
    calc = Calculator()
    assert calc.add(2, 3) == 5
    assert calc.add(-1, 1) == 0
    assert calc.add(0, 0) == 0

# Test with exception
def test_divide_by_zero():
    calc = Calculator()
    with pytest.raises(ValueError) as exc_info:
        calc.divide(10, 0)
    assert str(exc_info.value) == "Cannot divide by zero"

# Parameterized test
@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
    (1.5, 2.5, 4.0),
    (Decimal('1.5'), Decimal('2.5'), Decimal('4.0'))
])
def test_add_parametrized(a, b, expected):
    calc = Calculator()
    assert calc.add(a, b) == expected

# Fixture example
@pytest.fixture
def calculator():
    """Fixture to create Calculator instance"""
    return Calculator()

@pytest.fixture
def sample_data():
    """Fixture for sample data"""
    return {
        'numbers': [1, 2, 3, 4, 5],
        'sum': 15,
        'average': 3.0
    }

def test_with_fixtures(calculator, sample_data):
    result = sum(sample_data['numbers'])
    assert result == sample_data['sum']
    
    # Use calculator fixture
    total = 0
    for num in sample_data['numbers']:
        total = calculator.add(total, num)
    assert total == sample_data['sum']

# Mocking example
class PaymentProcessor:
    def __init__(self, api_client):
        self.api_client = api_client
    
    def process_payment(self, amount: float, card_token: str) -> dict:
        response = self.api_client.post(
            '/payments',
            json={'amount': amount, 'card_token': card_token}
        )
        return response.json()

def test_payment_processor():
    # Create mock API client
    mock_client = Mock()
    mock_response = Mock()
    mock_response.json.return_value = {
        'id': 'pay_123',
        'status': 'succeeded',
        'amount': 100.0
    }
    mock_client.post.return_value = mock_response
    
    # Create processor with mock
    processor = PaymentProcessor(mock_client)
    
    # Test
    result = processor.process_payment(100.0, 'tok_123')
    
    # Assertions
    assert result['status'] == 'succeeded'
    mock_client.post.assert_called_once_with(
        '/payments',
        json={'amount': 100.0, 'card_token': 'tok_123'}
    )

# Async test
import asyncio

class AsyncService:
    async def fetch_data(self, url: str) -> dict:
        # Simulate async operation
        await asyncio.sleep(0.1)
        return {'data': 'test'}

@pytest.mark.asyncio
async def test_async_service():
    service = AsyncService()
    result = await service.fetch_data('http://example.com')
    assert result == {'data': 'test'}

# Database testing with fixtures
import sqlite3
import tempfile

@pytest.fixture
def test_database():
    """Create a test database"""
    with tempfile.NamedTemporaryFile(suffix='.db') as tmp:
        conn = sqlite3.connect(tmp.name)
        cursor = conn.cursor()
        
        # Create test table
        cursor.execute('''
            CREATE TABLE users (
                id INTEGER PRIMARY KEY,
                name TEXT NOT NULL,
                email TEXT UNIQUE NOT NULL
            )
        ''')
        
        # Insert test data
        cursor.execute(
            "INSERT INTO users (name, email) VALUES (?, ?)",
            ('John Doe', 'john@example.com')
        )
        
        conn.commit()
        yield conn
        conn.close()

def test_database_operations(test_database):
    cursor = test_database.cursor()
    
    # Test read
    cursor.execute("SELECT * FROM users WHERE email = ?", ('john@example.com',))
    user = cursor.fetchone()
    
    assert user is not None
    assert user[1] == 'John Doe'
    
    # Test write
    cursor.execute(
        "INSERT INTO users (name, email) VALUES (?, ?)",
        ('Jane Doe', 'jane@example.com')
    )
    test_database.commit()
    
    cursor.execute("SELECT COUNT(*) FROM users")
    count = cursor.fetchone()[0]
    assert count == 2

# 🔗 Integration Test
import requests
from requests_mock import Mocker

def test_api_integration():
    with Mocker() as mocker:
        # Mock external API
        mocker.get(
            'https://api.example.com/users/1',
            json={'id': 1, 'name': 'John Doe'},
            status_code=200
        )
        
        # Test our code that calls the API
        response = requests.get('https://api.example.com/users/1')
        
        assert response.status_code == 200
        data = response.json()
        assert data['id'] == 1
        assert data['name'] == 'John Doe'

# 🌐 E2E Testing with Playwright
import pytest
from playwright.sync_api import Page, expect

@pytest.mark.e2e
def test_user_registration_e2e(page: Page):
    # Navigate to registration page
    page.goto("http://localhost:3000/register")
    
    # Fill registration form
    page.fill('[data-testid="name-input"]', 'John Doe')
    page.fill('[data-testid="email-input"]', 'john@example.com')
    page.fill('[data-testid="password-input"]', 'Password123!')
    page.fill('[data-testid="confirm-password-input"]', 'Password123!')
    
    # Submit form
    page.click('[data-testid="submit-button"]')
    
    # Verify successful registration
    expect(page).to_have_url("http://localhost:3000/dashboard")
    expect(page.locator('[data-testid="welcome-message"]')).to_contain_text(
        'Welcome, John'
    )
    
    # Take screenshot for documentation
    page.screenshot(path="test-results/registration-success.png")

@pytest.mark.e2e
def test_form_validation_e2e(page: Page):
    page.goto("http://localhost:3000/register")
    page.click('[data-testid="submit-button"]')
    
    # Verify validation messages
    expect(page.locator('[data-testid="name-error"]')).to_contain_text(
        'Name is required'
    )
    expect(page.locator('[data-testid="email-error"]')).to_contain_text(
        'Email is required'
    )

# 🏎️ Performance Testing with Locust
# tests/performance/locustfile.py
from locust import HttpUser, task, between, constant
from locust.contrib.fasthttp import FastHttpUser
import random

class WebsiteUser(HttpUser):
    wait_time = between(1, 5)  # Wait 1-5 seconds between tasks
    
    @task(3)  # Weight: 3
    def view_products(self):
        self.client.get("/products")
    
    @task(2)  # Weight: 2
    def view_product_detail(self):
        product_id = random.randint(1, 100)
        self.client.get(f"/products/{product_id}")
    
    @task(1)  # Weight: 1 (less frequent)
    def add_to_cart(self):
        product_id = random.randint(1, 100)
        self.client.post(
            "/cart/items",
            json={"product_id": product_id, "quantity": 1}
        )
    
    def on_start(self):
        """Called when a user starts"""
        # Login first
        self.client.post(
            "/login",
            json={"email": "test@example.com", "password": "password123"}
        )

class APIUser(FastHttpUser):
    wait_time = constant(1)  # Constant wait time
    
    @task
    def get_user_profile(self):
        headers = {"Authorization": "Bearer test_token"}
        self.client.get("/api/profile", headers=headers)
    
    @task
    def create_order(self):
        headers = {"Authorization": "Bearer test_token"}
        self.client.post(
            "/api/orders",
            json={"items": [{"product_id": 1, "quantity": 2}]},
            headers=headers
        )

# Run with: locust -f tests/performance/locustfile.py

# 🤖 AI-Powered Testing with Testim
# testimio_test.py
from testim import Testim
import json

class TestimAITests:
    def __init__(self, api_key):
        self.testim = Testim(api_key)
    
    def create_ai_test(self, app_url, test_name):
        """Create AI-powered test using Testim"""
        test_config = {
            "name": test_name,
            "url": app_url,
            "recordingMode": "ai",
            "testSteps": [
                {
                    "type": "navigate",
                    "url": app_url
                },
                {
                    "type": "ai_instructions",
                    "instructions": "Register a new user with valid credentials"
                }
            ]
        }
        
        test_id = self.testim.create_test(test_config)
        return test_id
    
    def run_test_and_analyze(self, test_id):
        """Run test and get AI analysis"""
        result = self.testim.run_test(test_id)
        
        if result['status'] == 'failed':
            # AI-powered failure analysis
            analysis = self.testim.analyze_failure(
                test_id,
                result['failureScreenshot']
            )
            
            print(f"AI Analysis: {analysis['rootCause']}")
            print(f"Suggested Fix: {analysis['suggestedFix']}")
        
        return result

# 📊 Test Report Generation
import pytest
import json
from datetime import datetime

@pytest.hookimpl(tryfirst=True, hookwrapper=True)
def pytest_runtest_makereport(item, call):
    """Create custom test reports"""
    outcome = yield
    report = outcome.get_result()
    
    if report.when == "call":
        # Create detailed report
        test_report = {
            "test_name": item.name,
            "nodeid": item.nodeid,
            "duration": report.duration,
            "outcome": report.outcome,
            "timestamp": datetime.now().isoformat(),
            "metadata": {
                "tags": [mark.name for mark in item.iter_markers()],
                "fixtures": list(item.fixturenames)
            }
        }
        
        if report.failed:
            test_report["error"] = {
                "message": str(report.longrepr),
                "traceback": report.longreprtext
            }
        
        # Save to file
        with open(f"test-results/{item.name}.json", "w") as f:
            json.dump(test_report, f, indent=2)

# Generate Allure reports
# tests/conftest.py
import allure
import pytest

@pytest.hookimpl(tryfirst=True, hookwrapper=True)
def pytest_runtest_makereport(item, call):
    outcome = yield
    rep = outcome.get_result()
    
    if rep.when == "call" and rep.failed:
        # Attach screenshot on failure
        if hasattr(item, "page"):  # Playwright test
            screenshot = item.page.screenshot()
            allure.attach(
                screenshot,
                name="screenshot",
                attachment_type=allure.attachment_type.PNG
            )
        
        # Attach page HTML
        if hasattr(item, "page"):
            html = item.page.content()
            allure.attach(
                html,
                name="page_html",
                attachment_type=allure.attachment_type.HTML
            )

# Example test with Allure annotations
@allure.feature("User Authentication")
@allure.story("User Registration")
@allure.severity(allure.severity_level.CRITICAL)
def test_user_registration():
    """Test user registration functionality"""
    with allure.step("Navigate to registration page"):
        # Navigation code
    
    with allure.step("Fill registration form"):
        # Form filling code
    
    with allure.step("Submit form"):
        # Form submission code
    
    with allure.step("Verify successful registration"):
        # Verification code
        assert True
    
    # Attach test data
    allure.attach(
        json.dumps({"test_data": "value"}, indent=2),
        name="test_data",
        attachment_type=allure.attachment_type.JSON
    )
```

### **2.3 Java**
```java
// 📦 pom.xml สำหรับ Test Stack
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <dependencies>
        <!-- Unit Testing -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.9.3</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-params</artifactId>
            <version>5.9.3</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Mocking -->
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>5.3.1</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-junit-jupiter</artifactId>
            <version>5.3.1</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Assertions -->
        <dependency>
            <groupId>org.assertj</groupId>
            <artifactId>assertj-core</artifactId>
            <version>3.24.2</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Integration Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <version>3.1.0</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Test Containers -->
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>testcontainers</artifactId>
            <version>1.18.3</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>1.18.3</version>
            <scope>test</scope>
        </dependency>
        
        <!-- E2E Testing -->
        <dependency>
            <groupId>io.github.bonigarcia</groupId>
            <artifactId>webdrivermanager</artifactId>
            <version>5.4.1</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.10.0</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Performance Testing -->
        <dependency>
            <groupId>io.gatling.highcharts</groupId>
            <artifactId>gatling-charts-highcharts</artifactId>
            <version>3.9.5</version>
            <scope>test</scope>
        </dependency>
        
        <!-- Report Generation -->
        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-reporting</artifactId>
            <version>1.9.3</version>
            <scope>test</scope>
        </dependency>
        
        <dependency>
            <groupId>io.qameta.allure</groupId>
            <artifactId>allure-junit5</artifactId>
            <version>2.22.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- Surefire Plugin for Unit Tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <includes>
                        <include>**/*Test.java</include>
                        <include>**/*Tests.java</include>
                    </includes>
                    <properties>
                        <configurationParameters>
                            junit.jupiter.execution.parallel.enabled=true
                            junit.jupiter.execution.parallel.mode.default=concurrent
                        </configurationParameters>
                    </properties>
                    <reportsDirectory>${project.build.directory}/test-results</reportsDirectory>
                    <argLine>-XX:+EnableDynamicAgentLoading</argLine>
                </configuration>
            </plugin>
            
            <!-- Failsafe Plugin for Integration Tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>3.0.0</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>integration-test</goal>
                            <goal>verify</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            
            <!-- JaCoCo for Code Coverage -->
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.10</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>report</id>
                        <phase>test</phase>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>check</id>
                        <goals>
                            <goal>check</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <rule>
                                    <element>BUNDLE</element>
                                    <limits>
                                        <limit>
                                            <counter>LINE</counter>
                                            <value>COVEREDRATIO</value>
                                            <minimum>0.80</minimum>
                                        </limit>
                                    </limits>
                                </rule>
                            </rules>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>

// 🧪 Unit Test Examples
import org.junit.jupiter.api.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import static org.mockito.Mockito.*;
import static org.assertj.core.api.Assertions.*;

import java.util.*;
import java.util.stream.Stream;

// Basic Unit Test
class CalculatorTest {
    private Calculator calculator;
    
    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }
    
    @Test
    @DisplayName("Test addition of two numbers")
    void testAddition() {
        int result = calculator.add(2, 3);
        assertThat(result).isEqualTo(5);
    }
    
    @Test
    @DisplayName("Test division by zero throws exception")
    void testDivisionByZero() {
        assertThatThrownBy(() -> calculator.divide(10, 0))
            .isInstanceOf(ArithmeticException.class)
            .hasMessageContaining("divide by zero");
    }
    
    @ParameterizedTest
    @CsvSource({
        "1, 2, 3",
        "0, 0, 0",
        "-1, 1, 0",
        "10, -5, 5"
    })
    @DisplayName("Parameterized addition test")
    void testAdditionParameterized(int a, int b, int expected) {
        assertThat(calculator.add(a, b)).isEqualTo(expected);
    }
    
    @ParameterizedTest
    @MethodSource("provideTestData")
    @DisplayName("Addition with method source")
    void testAdditionWithMethodSource(int a, int b, int expected) {
        assertThat(calculator.add(a, b)).isEqualTo(expected);
    }
    
    private static Stream<Arguments> provideTestData() {
        return Stream.of(
            Arguments.of(1, 2, 3),
            Arguments.of(0, 0, 0),
            Arguments.of(-1, 1, 0)
        );
    }
    
    @Test
    @Tag("slow")
    @DisplayName("Slow integration test")
    void testSlowIntegration() {
        // This test is tagged as slow
        assertThat(true).isTrue();
    }
}

// Mocking Example
class PaymentServiceTest {
    @Mock
    private PaymentGateway paymentGateway;
    
    private PaymentService paymentService;
    
    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        paymentService = new PaymentService(paymentGateway);
    }
    
    @Test
    @DisplayName("Test successful payment processing")
    void testProcessPaymentSuccess() {
        // Arrange
        PaymentRequest request = new PaymentRequest("order123", 100.0);
        PaymentResponse expectedResponse = new PaymentResponse("txn123", "SUCCESS");
        
        when(paymentGateway.process(any(PaymentRequest.class)))
            .thenReturn(expectedResponse);
        
        // Act
        PaymentResponse actualResponse = paymentService.processPayment(request);
        
        // Assert
        assertThat(actualResponse.getTransactionId())
            .isEqualTo("txn123");
        assertThat(actualResponse.getStatus())
            .isEqualTo("SUCCESS");
        
        verify(paymentGateway
```     
------------------------------------


## การใช้ n8n สำหรับ Automated Testing และ Monitoring**

## **1. n8n คืออะไร?**
**n8n** เป็น **workflow automation platform** แบบ open-source ที่ช่วยสร้าง automation workflows โดยไม่ต้องเขียนโค้ด (low-code/no-code)

---

## **2. ทำไมต้องใช้ n8n สำหรับ Testing?**

### **Benefits:**
```
✅ **No-Code/Low-Code** - สร้าง workflow ได้โดยไม่ต้องเขียนโค้ด
✅ **Integration** - เชื่อมต่อกับ tools ต่างๆ ได้มากมาย
✅ **Schedule** - ตั้งเวลา run tests อัตโนมัติ
✅ **Monitoring** - Monitor tests และแจ้งเตือน
✅ **Dashboard** - ดูผลลัพธ์ใน UI
✅ **Self-hosted** - ควบคุมข้อมูลได้ 100%
✅ **Extensible** - เขียน custom nodes ได้
```

### **Use Cases สำหรับ Testing:**
```
1. **API Testing Automation** - ทดสอบ API อัตโนมัติ
2. **E2E Test Orchestration** - จัดการ execution ของ Cypress/Selenium
3. **Test Result Monitoring** - ตรวจสอบและแจ้งเตือน
4. **Test Data Management** - จัดการ test data
5. **Environment Setup** - เตรียม environment ก่อน test
6. **Report Generation** - สร้าง reports อัตโนมัติ
```

---

## **3. ติดตั้ง n8n**

### **3.1 Docker Installation (แนะนำ):**
```yaml
# docker-compose.yml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"  # n8n UI
    environment:
      - N8N_PROTOCOL=https
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_ENCRYPTION_KEY=your-encryption-key
      - N8N_USER_FOLDER=/home/node/.n8n
      - WEBHOOK_URL=https://your-domain.com
      - N8N_METRICS=true
      - N8N_DIAGNOSTICS_ENABLED=true
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=168 # 7 days
      - N8N_ENDPOINT_WEBHOOK_TEST=https://your-domain.com
    volumes:
      - n8n_data:/home/node/.n8n
      - ./test-reports:/test-reports  # Mount test reports folder
      - ./test-scripts:/test-scripts  # Mount test scripts
    networks:
      - n8n_network

  postgres:
    image: postgres:15
    container_name: n8n_postgres
    restart: unless-stopped
    environment:
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=n8n_password
      - POSTGRES_DB=n8n
      - POSTGRES_NON_ROOT_USER=n8n
      - POSTGRES_NON_ROOT_PASSWORD=n8n_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - n8n_network

  redis:
    image: redis:7-alpine
    container_name: n8n_redis
    restart: unless-stopped
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - n8n_network

volumes:
  n8n_data:
  postgres_data:
  redis_data:

networks:
  n8n_network:
    driver: bridge
```

### **3.2 NPM Installation:**
```bash
# ติดตั้ง n8n globally
npm install n8n -g

# หรือติดตั้งแบบ project
npm init -y
npm install n8n

# เริ่มต้น n8n
n8n start

# เริ่มต้นด้วย options
n8n start \
  --tunnel \
  --skip-webhook-deregistration \
  --webhook-url=https://your-domain.com

# ใช้กับ docker compose
docker-compose up -d
```

---

## **4. Workflows สำหรับ Testing**

### **4.1 API Testing Workflow**

```json
{
  "name": "API Test Suite Runner",
  "nodes": [
    {
      "parameters": {},
      "id": "trigger-schedule",
      "name": "Schedule Trigger",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1,
      "position": [250, 300],
      "webhookId": "api-test-schedule"
    },
    {
      "parameters": {
        "url": "={{$env.API_BASE_URL}}/health",
        "options": {}
      },
      "id": "api-health-check",
      "name": "Health Check API",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 2,
      "position": [450, 300]
    },
    {
      "parameters": {
        "conditions": {
          "string": [
            {
              "value1": "={{$json['statusCode']}}",
              "operation": "equals",
              "value2": "200"
            }
          ]
        },
        "options": {}
      },
      "id": "check-response",
      "name": "Check Response",
      "type": "n8n-nodes-base.if",
      "typeVersion": 1,
      "position": [650, 300]
    },
    {
      "parameters": {
        "message": "✅ API Health Check PASSED\nStatus: {{$json['statusCode']}}\nResponse Time: {{$json['responseTime']}}ms",
        "additionalFields": {
          "parse_mode": "Markdown"
        }
      },
      "id": "telegram-success",
      "name": "Send Success Telegram",
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1,
      "position": [850, 200]
    },
    {
      "parameters": {
        "message": "❌ API Health Check FAILED\nStatus: {{$json['statusCode']}}\nError: {{$json['error']}}\nResponse: {{$json['body']}}",
        "additionalFields": {
          "parse_mode": "Markdown"
        }
      },
      "id": "telegram-failure",
      "name": "Send Failure Telegram",
      "type": "n8n-nodes-base.telegram",
      "typeVersion": 1,
      "position": [850, 400]
    },
    {
      "parameters": {
        "path": "/test-reports/api-test-{{$now.format('YYYY-MM-DD-HH-mm')}}.json",
        "options": {}
      },
      "id": "save-report",
      "name": "Save Test Report",
      "type": "n8n-nodes-base.writeFile",
      "typeVersion": 1,
      "position": [1050, 300]
    }
  ],
  "connections": {
    "Schedule Trigger": {
      "main": [
        [
          {
            "node": "Health Check API",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Health Check API": {
      "main": [
        [
          {
            "node": "Check Response",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Check Response": {
      "main": [
        [
          {
            "node": "Send Success Telegram",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Send Failure Telegram",
            "type": "main",
            "index": 1
          }
        ]
      ]
    },
    "Send Success Telegram": {
      "main": [
        [
          {
            "node": "Save Test Report",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Send Failure Telegram": {
      "main": [
        [
          {
            "node": "Save Test Report",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

### **4.2 Cypress Test Runner Workflow:**

```json
{
  "name": "Cypress Test Automation",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "hours",
              "minutesInterval": 0
            }
          ]
        }
      },
      "id": "schedule-trigger",
      "name": "Schedule Trigger",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1,
      "position": [250, 300]
    },
    {
      "parameters": {
        "command": "cd /test-scripts && npm run test:ci",
        "options": {
          "doNotForwardError": true
        }
      },
      "id": "execute-cypress",
      "name": "Execute Cypress Tests",
      "type": "n8n-nodes-base.executeCommand",
      "typeVersion": 1,
      "position": [450, 300]
    },
    {
      "parameters": {
        "functionCode": "const result = items[0].json;\nconst stdout = result.stdout || '';\nconst stderr = result.stderr || '';\n\n// Parse test results\nconst passedMatch = stdout.match(/Passed:\\s*(\\d+)/);\nconst failedMatch = stdout.match(/Failed:\\s*(\\d+)/);\nconst durationMatch = stdout.match(/Duration:\\s*(.+)/);\n\nreturn [{\n  json: {\n    passed: passedMatch ? parseInt(passedMatch[1]) : 0,\n    failed: failedMatch ? parseInt(failedMatch[1]) : 0,\n    duration: durationMatch ? durationMatch[1] : 'N/A',\n    rawOutput: stdout,\n    errorOutput: stderr,\n    executionTime: new Date().toISOString()\n  }\n}];"
      },
      "id": "parse-results",
      "name": "Parse Test Results",
      "type": "n8n-nodes-base.function",
      "typeVersion": 1,
      "position": [650, 300]
    },
    {
      "parameters": {
        "conditions": {
          "number": [
            {
              "value1": "={{$json['failed']}}",
              "operation": "equals",
              "value2": 0
            }
          ]
        },
        "options": {}
      },
      "id": "check-test-result",
      "name": "Check Test Result",
      "type": "n8n-nodes-base.if",
      "typeVersion": 1,
      "position": [850, 300]
    },
    {
      "parameters": {
        "url": "={{$env.SLACK_WEBHOOK_URL}}",
        "authentication": "genericCredentialType",
        "genericAuthType": "httpHeaderAuth",
        "sendBody": true,
        "options": {
          "body": {
            "text": "🎉 *Cypress Tests PASSED*\\n*Passed:* {{$json['passed']}}\\n*Duration:* {{$json['duration']}}\\n*Time:* {{$json['executionTime']}}"
          }
        }
      },
      "id": "slack-success",
      "name": "Send Slack Success",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 2,
      "position": [1050, 200]
    },
    {
      "parameters": {
        "url": "={{$env.SLACK_WEBHOOK_URL}}",
        "authentication": "genericCredentialType",
        "genericAuthType": "httpHeaderAuth",
        "sendBody": true,
        "options": {
          "body": {
            "text": "❌ *Cypress Tests FAILED*\\n*Passed:* {{$json['passed']}}\\n*Failed:* {{$json['failed']}}\\n*Duration:* {{$json['duration']}}\\n```{{$json['errorOutput']}}```"
          }
        }
      },
      "id": "slack-failure",
      "name": "Send Slack Failure",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 2,
      "position": [1050, 400]
    },
    {
      "parameters": {
        "operation": "create",
        "documentId": "cypress-report-{{$now.format('YYYY-MM-DD-HH-mm')}}",
        "content": "={{JSON.stringify($json, null, 2)}}",
        "options": {}
      },
      "id": "save-to-google-docs",
      "name": "Save to Google Docs",
      "type": "n8n-nodes-base.googleDocs",
      "typeVersion": 1,
      "position": [1250, 300]
    }
  ],
  "connections": {
    "Schedule Trigger": {
      "main": [
        [
          {
            "node": "Execute Cypress Tests",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Execute Cypress Tests": {
      "main": [
        [
          {
            "node": "Parse Test Results",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Parse Test Results": {
      "main": [
        [
          {
            "node": "Check Test Result",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Check Test Result": {
      "main": [
        [
          {
            "node": "Send Slack Success",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Send Slack Failure",
            "type": "main",
            "index": 1
          }
        ]
      ]
    },
    "Send Slack Success": {
      "main": [
        [
          {
            "node": "Save to Google Docs",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Send Slack Failure": {
      "main": [
        [
          {
            "node": "Save to Google Docs",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  }
}
```

---

## **5. Advanced Testing Workflows**

### **5.1 End-to-End Test Orchestration:**

![n8n E2E Test Workflow](https://raw.githubusercontent.com/n8n-io/n8n/master/docs/images/n8n-screenshot.png)

```javascript
// Custom Node สำหรับ Cypress Execution
// cypress-node.js
const { Node } = require('n8n-core');
const { nodeDescription } = require('./CypressNode.description');
const { exec } = require('child_process');
const path = require('path');

class CypressNode extends Node {
  constructor() {
    super(nodeDescription);
  }

  async execute() {
    const items = this.getInputData();
    const returnItems = [];

    for (let itemIndex = 0; itemIndex < items.length; itemIndex++) {
      const cypressConfig = items[itemIndex].json;
      
      // Build Cypress command
      const command = this.buildCypressCommand(cypressConfig);
      
      // Execute Cypress
      const result = await this.executeCypress(command);
      
      // Parse results
      const parsedResults = this.parseCypressOutput(result);
      
      returnItems.push({
        json: {
          ...cypressConfig,
          ...parsedResults,
          executionTime: new Date().toISOString()
        }
      });
    }

    return this.prepareOutputData(returnItems);
  }

  buildCypressCommand(config) {
    const {
      specPath,
      browser = 'chrome',
      headless = true,
      record = false,
      parallel = false,
      envVars = {}
    } = config;

    let command = 'npx cypress run';
    
    if (specPath) {
      command += ` --spec "${specPath}"`;
    }
    
    if (browser) {
      command += ` --browser ${browser}`;
    }
    
    if (headless) {
      command += ' --headless';
    }
    
    if (record) {
      command += ' --record';
    }
    
    if (parallel) {
      command += ' --parallel';
    }
    
    // Add environment variables
    const envString = Object.entries(envVars)
      .map(([key, value]) => `${key}=${value}`)
      .join(',');
    
    if (envString) {
      command += ` --env ${envString}`;
    }
    
    return command;
  }

  executeCypress(command) {
    return new Promise((resolve, reject) => {
      exec(command, { cwd: process.cwd() }, (error, stdout, stderr) => {
        resolve({
          error,
          stdout,
          stderr,
          exitCode: error ? error.code : 0
        });
      });
    });
  }

  parseCypressOutput(result) {
    const { stdout, stderr, exitCode } = result;
    
    // Extract test results from stdout
    const passedMatch = stdout.match(/(\d+) passing/);
    const failedMatch = stdout.match(/(\d+) failing/);
    const durationMatch = stdout.match(/\((.+)\)/);
    
    return {
      passed: passedMatch ? parseInt(passedMatch[1]) : 0,
      failed: failedMatch ? parseInt(failedMatch[1]) : 0,
      duration: durationMatch ? durationMatch[1] : 'N/A',
      exitCode,
      rawOutput: stdout,
      errorOutput: stderr,
      success: exitCode === 0
    };
  }
}

module.exports = CypressNode;
```

### **5.2 Test Data Management Workflow:**

```json
{
  "name": "Test Data Management",
  "nodes": [
    {
      "parameters": {
        "operation": "executeQuery",
        "query": "DELETE FROM test_users WHERE created_at < NOW() - INTERVAL '7 days'"
      },
      "id": "cleanup-old-data",
      "name": "Cleanup Old Test Data",
      "type": "n8n-nodes-base.postgres",
      "typeVersion": 2,
      "position": [250, 300]
    },
    {
      "parameters": {
        "operation": "executeQuery",
        "query": "INSERT INTO test_users (email, name, role, created_at) VALUES {{$json.values}}",
        "additionalFields": {}
      },
      "id": "generate-test-users",
      "name": "Generate Test Users",
      "type": "n8n-nodes-base.postgres",
      "typeVersion": 2,
      "position": [450, 300]
    },
    {
      "parameters": {
        "functionCode": "// Generate test user data\nconst users = [];\nconst roles = ['admin', 'user', 'moderator'];\n\nfor (let i = 0; i < 10; i++) {\n  users.push({\n    email: `testuser${i}@example.com`,\n    name: `Test User ${i}`,\n    role: roles[i % roles.length],\n    created_at: new Date().toISOString()\n  });\n}\n\n// Format for PostgreSQL\nconst values = users.map(u => \n  `('${u.email}', '${u.name}', '${u.role}', '${u.created_at}')`\n).join(',');\n\nreturn [{\n  json: {\n    values,\n    count: users.length,\n    users\n  }\n}];"
      },
      "id": "create-test-data",
      "name": "Create Test Data",
      "type": "n8n-nodes-base.function",
      "typeVersion": 1,
      "position": [650, 300]
    },
    {
      "parameters": {
        "resource": "sheet",
        "operation": "append",
        "spreadsheetId": "={{$env.GOOGLE_SHEET_ID}}",
        "range": "TestUsers!A:D",
        "options": {}
      },
      "id": "export-to-sheet",
      "name": "Export to Google Sheet",
      "type": "n8n-nodes-base.googleSheets",
      "typeVersion": 3,
      "position": [850, 300]
    },
    {
      "parameters": {
        "url": "={{$env.WEBHOOK_URL}}/test-data/ready",
        "options": {}
      },
      "id": "notify-completion",
      "name": "Notify Completion",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 2,
      "position": [1050, 300]
    }
  ]
}
```

---

## **6. Integration กับ Testing Tools**

### **6.1 n8n + Cypress Integration:**

```bash
# Cypress Test Script สำหรับ n8n
// cypress/run-tests.js
const cypress = require('cypress');
const fs = require('fs');
const path = require('path');

async function runTests(specs = [], options = {}) {
  const results = [];
  
  for (const spec of specs) {
    console.log(`Running test: ${spec}`);
    
    const result = await cypress.run({
      spec,
      browser: options.browser || 'chrome',
      headless: options.headless !== false,
      reporter: options.reporter || 'spec',
      reporterOptions: options.reporterOptions,
      config: {
        video: options.video || false,
        screenshotOnRunFailure: options.screenshotOnRunFailure || true,
        baseUrl: options.baseUrl || 'http://localhost:3000'
      },
      env: options.env || {}
    });
    
    results.push({
      spec,
      ...result
    });
    
    // Save individual report
    const reportDir = path.join(__dirname, 'reports');
    if (!fs.existsSync(reportDir)) {
      fs.mkdirSync(reportDir, { recursive: true });
    }
    
    fs.writeFileSync(
      path.join(reportDir, `${spec.replace(/\//g, '_')}.json`),
      JSON.stringify(result, null, 2)
    );
  }
  
  // Generate summary
  const summary = {
    total: results.length,
    passed: results.filter(r => r.totalFailed === 0).length,
    failed: results.filter(r => r.totalFailed > 0).length,
    totalTests: results.reduce((sum, r) => sum + r.totalTests, 0),
    totalPassed: results.reduce((sum, r) => sum + r.totalPassed, 0),
    totalFailed: results.reduce((sum, r) => sum + r.totalFailed, 0),
    results: results.map(r => ({
      spec: r.spec,
      status: r.totalFailed === 0 ? 'PASSED' : 'FAILED',
      tests: r.totalTests,
      passed: r.totalPassed,
      failed: r.totalFailed,
      duration: r.totalDuration
    }))
  };
  
  fs.writeFileSync(
    path.join(reportDir, 'summary.json'),
    JSON.stringify(summary, null, 2)
  );
  
  return summary;
}

// สำหรับ n8n execution
if (require.main === module) {
  const specs = process.argv.slice(2);
  runTests(specs).then(summary => {
    console.log(JSON.stringify(summary));
    process.exit(summary.failed > 0 ? 1 : 0);
  });
}

module.exports = { runTests };
```

### **6.2 n8n + Test Result Monitoring:**

```javascript
// test-monitor-workflow.js
module.exports = async function (workflow) {
  workflow.name = 'Test Result Monitor';
  
  // Schedule trigger - ทุก 15 นาที
  const schedule = workflow.addNode('scheduleTrigger', {
    rule: {
      interval: [
        {
          field: 'minutes',
          minutesInterval: 15
        }
      ]
    }
  });
  
  // HTTP Request - ดึง test results จาก API
  const fetchResults = workflow.addNode('httpRequest', {
    url: '={{$env.TEST_RESULTS_API}}/latest',
    options: {}
  });
  
  // Parse results
  const parseResults = workflow.addNode('function', {
    functionCode: `
      const results = items[0].json;
      const now = new Date();
      const fiveMinutesAgo = new Date(now.getTime() - 5 * 60 * 1000);
      
      // Filter recent failures
      const recentFailures = results.filter(result => {
        const resultTime = new Date(result.timestamp);
        return result.status === 'FAILED' && resultTime > fiveMinutesAgo;
      });
      
      // Calculate metrics
      const totalTests = results.reduce((sum, r) => sum + r.total, 0);
      const passedTests = results.reduce((sum, r) => sum + r.passed, 0);
      const successRate = totalTests > 0 ? (passedTests / totalTests) * 100 : 0;
      
      return [{
        json: {
          recentFailures,
          totalTests,
          passedTests,
          successRate: successRate.toFixed(2),
          lastUpdated: now.toISOString()
        }
      }];
    `
  });
  
  // Condition check
  const conditionCheck = workflow.addNode('if', {
    conditions: {
      number: [{
        value1: '={{$json["successRate"]}}',
        operation: 'lessThan',
        value2: '95'
      }]
    }
  });
  
  // Alert if success rate < 95%
  const sendAlert = workflow.addNode('httpRequest', {
    url: '={{$env.SLACK_WEBHOOK_URL}}',
    authentication: 'genericCredentialType',
    sendBody: true,
    options: {
      body: {
        text: "⚠️ *Test Success Rate Alert*\\n*Current Rate:* {{$json['successRate']}}%\\n*Failed Tests:* {{$json['recentFailures'].length}}\\n{{#each $json['recentFailures']}}• {{this.test_name}} at {{this.timestamp}}\\n{{/each}}"
      }
    }
  });
  
  // Save to database
  const saveToDB = workflow.addNode('postgres', {
    operation: 'executeQuery',
    query: `
      INSERT INTO test_metrics 
      (success_rate, total_tests, passed_tests, timestamp) 
      VALUES ({{$json.successRate}}, {{$json.totalTests}}, {{$json.passedTests}}, '{{$json.lastUpdated}}')
    `
  });
  
  // Connect nodes
  workflow.connect(schedule, fetchResults);
  workflow.connect(fetchResults, parseResults);
  workflow.connect(parseResults, conditionCheck);
  workflow.connect(conditionCheck, sendAlert, 0); // If condition true
  workflow.connect(conditionCheck, saveToDB, 1); // If condition false
  workflow.connect(sendAlert, saveToDB);
  
  return workflow;
};
```

---

## **7. n8n Workflow Templates สำหรับ Testing**

### **7.1 Comprehensive Test Suite Runner:**

```yaml
# n8n-test-orchestrator.yaml
name: "Test Suite Orchestrator"
nodes:
  - node:
      name: "Manual Trigger"
      type: "n8n-nodes-base.manualTrigger"
      position: [250, 300]
      parameters:
        activate: true
  
  - node:
      name: "Start All Tests"
      type: "n8n-nodes-base.function"
      position: [450, 300]
      parameters:
        functionCode: |
          return [{
            json: {
              testSuites: [
                { name: "Unit Tests", command: "npm test" },
                { name: "Integration Tests", command: "npm run test:integration" },
                { name: "E2E Tests", command: "npm run test:e2e" },
                { name: "API Tests", command: "npm run test:api" },
                { name: "Performance Tests", command: "npm run test:performance" }
              ],
              environment: {
                NODE_ENV: "test",
                API_URL: "http://localhost:3000",
                DATABASE_URL: "postgresql://test:test@localhost:5432/test_db"
              }
            }
          }];
  
  - node:
      name: "Setup Test Environment"
      type: "n8n-nodes-base.executeCommand"
      position: [650, 200]
      parameters:
        command: "docker-compose -f docker-compose.test.yml up -d"
        options:
          doNotForwardError: true
  
  - node:
      name: "Wait for Services"
      type: "n8n-nodes-base.function"
      position: [650, 300]
      parameters:
        functionCode: |
          const { exec } = require('child_process');
          
          return new Promise((resolve) => {
            const checkService = () => {
              exec('curl -f http://localhost:3000/health', (error) => {
                if (error) {
                  setTimeout(checkService, 5000);
                } else {
                  resolve([{ json: { status: "ready" } }]);
                }
              });
            };
            checkService();
          });
  
  - node:
      name: "Execute Test Suite"
      type: "n8n-nodes-base.splitInBatches"
      position: [850, 300]
      parameters:
        batchSize: 1
  
  - node:
      name: "Run Test Command"
      type: "n8n-nodes-base.executeCommand"
      position: [1050, 300]
      parameters:
        command: "={{$json.command}}"
        options:
          doNotForwardError: true
  
  - node:
      name: "Parse Test Results"
      type: "n8n-nodes-base.function"
      position: [1250, 300]
      parameters:
        functionCode: |
          const result = items[0].json;
          
          // Extract test metrics
          const passedMatch = result.stdout.match(/(\\d+) passing/);
          const failedMatch = result.stdout.match(/(\\d+) failing/);
          const skippedMatch = result.stdout.match(/(\\d+) pending/);
          
          return [{
            json: {
              ...$json,
              passed: passedMatch ? parseInt(passedMatch[1]) : 0,
              failed: failedMatch ? parseInt(failedMatch[1]) : 0,
              skipped: skippedMatch ? parseInt(skippedMatch[1]) : 0,
              exitCode: result.exitCode,
              success: result.exitCode === 0,
              rawOutput: result.stdout.substring(0, 1000), // Limit size
              timestamp: new Date().toISOString()
            }
          }];
  
  - node:
      name: "Aggregate Results"
      type: "n8n-nodes-base.aggregate"
      position: [1450, 300]
      parameters:
        aggregation: "mergeByFields"
        fieldsToMatch: []
        fieldsToAggregate:
          - field: "passed"
            aggregation: "sum"
          - field: "failed"
            aggregation: "sum"
          - field: "skipped"
            aggregation: "sum"
        options: {}
  
  - node:
      name: "Generate Report"
      type: "n8n-nodes-base.function"
      position: [1650, 300]
      parameters:
        functionCode: |
          const aggregated = items[0].json;
          const total = aggregated.passed + aggregated.failed + aggregated.skipped;
          const successRate = total > 0 ? (aggregated.passed / total) * 100 : 0;
          
          // Generate HTML report
          const htmlReport = \`
          <!DOCTYPE html>
          <html>
          <head>
            <title>Test Execution Report</title>
            <style>
              body { font-family: Arial, sans-serif; margin: 40px; }
              .summary { background: #f5f5f5; padding: 20px; border-radius: 5px; }
              .metric { margin: 10px 0; }
              .passed { color: green; }
              .failed { color: red; }
              .skipped { color: orange; }
            </style>
          </head>
          <body>
            <h1>Test Execution Report</h1>
            <div class="summary">
              <h2>Summary</h2>
              <div class="metric"><strong>Total Tests:</strong> \${total}</div>
              <div class="metric passed"><strong>Passed:</strong> \${aggregated.passed}</div>
              <div class="metric failed"><strong>Failed:</strong> \${aggregated.failed}</div>
              <div class="metric skipped"><strong>Skipped:</strong> \${aggregated.skipped}</div>
              <div class="metric"><strong>Success Rate:</strong> \${successRate.toFixed(2)}%</div>
              <div class="metric"><strong>Execution Time:</strong> \${new Date().toLocaleString()}</div>
            </div>
          </body>
          </html>
          \`;
          
          return [{
            json: {
              ...aggregated,
              total,
              successRate: successRate.toFixed(2),
              htmlReport,
              timestamp: new Date().toISOString()
            }
          }];
  
  - node:
      name: "Send Notifications"
      type: "n8n-nodes-base.splitInBatches"
      position: [1850, 300]
      parameters:
        batchSize: 1
  
  - node:
      name: "Send Email Report"
      type: "n8n-nodes-base.emailSend"
      position: [2050, 200]
      parameters:
        fromEmail: "tests@company.com"
        toEmail: "={{$env.NOTIFICATION_EMAIL}}"
        subject: "Test Execution Report - {{$json.successRate}}% Success"
        text: "Test execution completed. Success rate: {{$json.successRate}}%"
        html: "={{$json.htmlReport}}"
        options: {}
  
  - node:
      name: "Send Slack Alert if Failed"
      type: "n8n-nodes-base.if"
      position: [2050, 400]
      parameters:
        conditions:
          number:
            - value1: "={{$json['failed']}}"
              operation: "greaterThan"
              value2: "0"
  
  - node:
      name: "Send Slack Notification"
      type: "n8n-nodes-base.httpRequest"
      position: [2250, 400]
      parameters:
        url: "={{$env.SLACK_WEBHOOK_URL}}"
        sendBody: true
        options:
          body:
            text: "⚠️ *Test Execution Completed with Failures*\\n*Success Rate:* {{$json.successRate}}%\\n*Failed Tests:* {{$json.failed}}\\n*View Report:* {{$env.REPORT_URL}}"
  
  - node:
      name: "Cleanup Environment"
      type: "n8n-nodes-base.executeCommand"
      position: [2450, 300]
      parameters:
        command: "docker-compose -f docker-compose.test.yml down -v"
```

---

## **8. Deployment และ Production Setup**

### **8.1 n8n Production Configuration:**

```yaml
# n8n-production.yml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    container_name: n8n-production
    restart: always
    ports:
      - "5678:5678"
    environment:
      # Core
      - N8N_PROTOCOL=https
      - N8N_HOST=your-domain.com
      - N8N_PORT=5678
      - N8N_ENCRYPTION_KEY=${ENCRYPTION_KEY}
      
      # Database
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=${DB_PASSWORD}
      
      # Queue
      - QUEUE_HEALTH_CHECK_ACTIVE=true
      - EXECUTIONS_MODE=queue
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=168