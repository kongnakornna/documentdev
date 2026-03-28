## **การทำ Unit Test และ Automated Test ในภาษาต่างๆ**
-----------------------------
### ✅ 1. **Unit Testing** คืออะไร?
- การทดสอบฟังก์ชัน/เมธอดแต่ละหน่วยของโค้ดแบบแยกส่วน
- เร็ว ง่ายต่อการค้นหาจุดผิดพลาด
- เป็นพื้นฐานของ Test-Driven Development (TDD)

### ✅ 2. **Automated Testing** คืออะไร?
- การทดสอบที่รันอัตโนมัติ
- ครอบคลุมหลายระดับ: Unit, Integration, End-to-End
- ทำใน CI/CD Pipeline

---

## **ภาษายอดนิยมและ Framework**

### **JavaScript/TypeScript**
```javascript
// Jest - ตัวอย่าง
function sum(a, b) {
  return a + b;
}

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});

// Mocha + Chai
const { expect } = require('chai');
describe('Array', () => {
  describe('#indexOf()', () => {
    it('should return -1 when value not present', () => {
      expect([1,2,3].indexOf(4)).to.equal(-1);
    });
  });
});
```

**Framework ที่นิยม:**
- Jest (นิยมที่สุด)
- Mocha + Chai
- Jasmine
- Vitest (สำหรับ Vite)

---

### **Python**
```python
# unittest (Built-in)
import unittest

def multiply(a, b):
    return a * b

class TestMultiply(unittest.TestCase):
    def test_multiply(self):
        self.assertEqual(multiply(3, 4), 12)
        self.assertEqual(multiply(-1, 1), -1)

if __name__ == '__main__':
    unittest.main()

# pytest (นิยมมาก)
def test_multiply():
    assert multiply(2, 3) == 6

# ใช้ fixture
import pytest

@pytest.fixture
def sample_data():
    return [1, 2, 3, 4, 5]

def test_sum(sample_data):
    assert sum(sample_data) == 15
```

**Framework:**
- pytest (แนะนำ)
- unittest (built-in)
- nose2

---

### **Java**
```java
// JUnit 5
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class CalculatorTest {
    @Test
    void testAddition() {
        Calculator calc = new Calculator();
        assertEquals(5, calc.add(2, 3));
    }
}

// Mockito สำหรับ Mocking
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;
    
    @InjectMocks
    private UserService userService;
    
    @Test
    void testGetUser() {
        when(userRepository.findById(1L)).thenReturn(new User("John"));
        User user = userService.getUser(1L);
        assertEquals("John", user.getName());
    }
}
```

**Framework:**
- JUnit 5 (นิยมที่สุด)
- TestNG
- Mockito (สำหรับ mocking)
- Spock (Groovy-based)

---

### **C# (.NET)**
```csharp
// xUnit
public class CalculatorTests
{
    [Fact]
    public void Add_TwoNumbers_ReturnsSum()
    {
        // Arrange
        var calculator = new Calculator();
        
        // Act
        var result = calculator.Add(3, 4);
        
        // Assert
        Assert.Equal(7, result);
    }
    
    [Theory]
    [InlineData(1, 2, 3)]
    [InlineData(-1, -1, -2)]
    public void Add_MultipleInputs_ReturnsCorrectSum(int a, int b, int expected)
    {
        var calculator = new Calculator();
        var result = calculator.Add(a, b);
        Assert.Equal(expected, result);
    }
}

// NUnit
[TestFixture]
public class CalculatorTests
{
    [Test]
    public void TestAddition()
    {
        Assert.AreEqual(5, Calculator.Add(2, 3));
    }
}
```

**Framework:**
- xUnit (Microsoft แนะนำ)
- NUnit
- MSTest

---

### **Go**
```go
// Testing built-in
package math

import "testing"

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    expected := 5
    if result != expected {
        t.Errorf("Add(2, 3) = %d; want %d", result, expected)
    }
}

// Table-driven tests (รูปแบบที่นิยมใน Go)
func TestAddTable(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 2, 3, 5},
        {"negative", -1, -1, -2},
        {"zero", 0, 5, 5},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", 
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

**Framework:**
- testing (built-in)
- testify (สำหรับ assertion ที่อ่านง่าย)
- gomock (สำหรับ mocking)

---

### **Ruby**
```ruby
# RSpec
describe Calculator do
  describe "#add" do
    it "returns sum of two numbers" do
      calculator = Calculator.new
      expect(calculator.add(2, 3)).to eq(5)
    end
  end
end

# Minitest
require 'minitest/autorun'

class TestCalculator < Minitest::Test
  def test_addition
    assert_equal 5, Calculator.new.add(2, 3)
  end
end
```

**Framework:**
- RSpec (นิยมที่สุด)
- Minitest (built-in)
- Cucumber (BDD)

---

## **Automated Test Types**

### 1. **Unit Tests**
- ทดสอบฟังก์ชันเดียวแบบแยกส่วน
- เร็ว ไม่พึ่งพาภายนอก

### 2. **Integration Tests**
- ทดสอบการทำงานร่วมกันของหลาย components
```javascript
// ตัวอย่าง Integration Test
test('user registration flow', async () => {
  await registerUser({email: 'test@example.com', password: '123'});
  const user = await getUserFromDB('test@example.com');
  expect(user).not.toBeNull();
});
```

### 3. **End-to-End (E2E) Tests**
- ทดสอบระบบทั้งหมดจากผู้ใช้
- ใช้ tools เช่น:
  - Web: Cypress, Playwright, Selenium
  - Mobile: Appium, Espresso (Android), XCTest (iOS)
  - API: Supertest, REST Assured

### 4. **Performance Tests**
- ทดสอบความเร็วและความเสถียร
- Tools: JMeter, k6, Lighthouse

---

## **Best Practices**

### 1. **หลักการ FIRST**
- **F**ast: รันเร็ว
- **I**solated: ไม่พึ่งพากัน
- **R**epeatable: ให้ผลเหมือนเดิมทุกครั้ง
- **S**elf-validating: ผ่าน/ไม่ผ่านชัดเจน
- **T**imely: เขียนพร้อมหรือก่อนโค้ดจริง (TDD)

### 2. **การตั้งชื่อ test ที่ดี**
```javascript
// ไม่ดี
test('test 1', () => { ... })

// ดี
test('calculateTotal should return sum of all items', () => { ... })
test('calculateTotal should apply discount when coupon valid', () => { ... })
```

### 3. **Arrange-Act-Assert Pattern**
```python
def test_withdraw_money():
    # Arrange
    account = BankAccount(balance=100)
    
    # Act
    result = account.withdraw(30)
    
    # Assert
    assert result is True
    assert account.balance == 70
```

### 4. **Test Coverage**
- ใช้ tools วัด coverage:
  - JavaScript: Istanbul, Jest coverage
  - Python: coverage.py, pytest-cov
  - Java: JaCoCo, Cobertura
  - C#: Coverlet, dotCover

### 5. **Mocking และ Stubbing**
- ใช้เมื่อต้องทดสอบแยกจาก dependencies
```javascript
// Jest mocking
jest.mock('./api');
const api = require('./api');
api.getUser.mockResolvedValue({name: 'John'});
```

---

## **CI/CD Integration**

### ตัวอย่าง GitHub Actions
```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
      - name: Run integration tests
        run: npm run test:integration
      - name: Upload coverage
        uses: codecov/codecov-action@v2
```

---

## **Tools เพิ่มเติม**

### 1. **API Testing**
- Postman (Manual + Automation)
- Supertest (Node.js)
- REST Assured (Java)
- requests + pytest (Python)

### 2. **Visual Testing**
- Percy, Applitools
- ทดสอบ UI ไม่เปลี่ยน

### 3. **Load Testing**
- k6, Artillery
- JMeter, Locust

### 4. **Security Testing**
- OWASP ZAP
- Snyk, SonarQube

-----------------------------

## **สรุป**
- **เลือก framework** ให้เหมาะกับภาษาและทีม
- **เริ่มจาก Unit Test** ก่อน แล้วค่อยขยาย
- **ทำให้เป็นส่วนหนึ่งของ workflow** (CI/CD)
- **วัด coverage แต่ไม่ยึดติดกับตัวเลขมากเกินไป**
- **เขียน test ที่อ่านง่ายและบำรุงรักษาได้**

- การเขียน test ที่ดีช่วยลด bug, ทำให้ refactor ปลอดภัย และเพิ่มความมั่นใจในการ deploy!

### การออกแบบและเทคนิค Debugging Code อย่างมีประสิทธิภาพ

#### **1. หลักการออกแบบเพื่อป้องกัน Bug**

### **1.1 Design Patterns ที่ช่วยลด Bug**
```python
# Singleton Pattern - ป้องกัน multiple instances
class DatabaseConnection:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.initialize()
        return cls._instance
    
    def initialize(self):
        # initialization code
        self.connection = None

# Strategy Pattern - ลด conditional complexity
class PaymentStrategy:
    def pay(self, amount):
        pass

class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} via Credit Card")

class PayPalPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} via PayPal")

class PaymentContext:
    def __init__(self, strategy: PaymentStrategy):
        self.strategy = strategy
    
    def execute_payment(self, amount):
        self.strategy.pay(amount)
```

### **1.2 SOLID Principles**
- **S**ingle Responsibility: ฟังก์ชัน/คลาสทำหน้าที่เดียว
- **O**pen-Closed: Open for extension, closed for modification
- **L**iskov Substitution: Subclass ใช้แทน parent ได้
- **I**nterface Segregation: Interface เล็กและเฉพาะเจาะจง
- **D**ependency Inversion: ขึ้นกับ abstraction ไม่ใช่ concrete class
# **SOLID Principles** - คำอธิบายละเอียดพร้อมตัวอย่าง

## **1. Single Responsibility Principle (SRP)**
**"ฟังก์ชัน/คลาส ควรมีเพียงหนึ่งเหตุผลที่จะเปลี่ยนแปลง"**

### ❌ ตัวอย่างที่ผิดหลักการ:
```typescript
class User {
  constructor(private name: string, private email: string) {}
  
  // ผิด: มีหลายหน้าที่ในคลาสเดียว
  saveToDatabase() {
    // หน้าที่: การเข้าถึงข้อมูล
    console.log(`Saving ${this.name} to database...`);
  }
  
  sendEmail(message: string) {
    // หน้าที่: การส่งอีเมล
    console.log(`Sending email to ${this.email}: ${message}`);
  }
  
  validate() {
    // หน้าที่: การตรวจสอบข้อมูล
    return this.name.length > 0 && this.email.includes('@');
  }
  
  generateReport() {
    // หน้าที่: การสร้างรายงาน
    console.log(`Report for ${this.name}`);
  }
}
```

### ✅ ตัวอย่างที่ถูกต้อง:
```typescript
// 1. คลาสสำหรับข้อมูล User
class User {
  constructor(public name: string, public email: string) {}
}

// 2. คลาสสำหรับ validation
class UserValidator {
  validate(user: User): boolean {
    return user.name.length > 0 && user.email.includes('@');
  }
}

// 3. คลาสสำหรับ data access
class UserRepository {
  save(user: User): void {
    console.log(`Saving ${user.name} to database...`);
  }
  
  findById(id: number): User | null {
    // ค้นหาจาก database
    return null;
  }
}

// 4. คลาสสำหรับ email service
class EmailService {
  sendEmail(user: User, message: string): void {
    console.log(`Sending email to ${user.email}: ${message}`);
  }
}

// 5. คลาสสำหรับ report generation
class ReportGenerator {
  generateUserReport(user: User): string {
    return `Report for ${user.name}`;
  }
}

// ใช้งาน
const user = new User("John Doe", "john@example.com");
const validator = new UserValidator();
const repo = new UserRepository();
const emailService = new EmailService();

if (validator.validate(user)) {
  repo.save(user);
  emailService.sendEmail(user, "Welcome!");
}
```

**ประโยชน์:** 
- แก้ไขง่าย (เปลี่ยนการส่งเมลโดยไม่กระทบ validation)
- ทดสอบง่ายขึ้น
- รียูสได้ดีกว่า

---

## **2. Open-Closed Principle (OCP)**
**"Software entities ควรเปิดสำหรับการขยาย แต่ปิดสำหรับการแก้ไข"**

### ❌ ตัวอย่างที่ผิดหลักการ:
```typescript
class DiscountCalculator {
  // ผิด: ทุกครั้งที่เพิ่ม discount type ต้องแก้ไขเมธอดนี้
  calculate(price: number, type: string): number {
    if (type === "REGULAR") {
      return price * 0.9; // 10% discount
    } else if (type === "VIP") {
      return price * 0.8; // 20% discount
    } else if (type === "SUPER_VIP") {
      return price * 0.7; // 30% discount
    }
    // ถ้าเพิ่ม type ใหม่ต้องมาเพิ่ม condition ตรงนี้
    return price;
  }
}
```

### ✅ ตัวอย่างที่ถูกต้อง:
```typescript
// Abstract class หรือ Interface
interface DiscountStrategy {
  calculate(price: number): number;
}

// Concrete implementations
class RegularDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.9;
  }
}

class VIPDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.8;
  }
}

class SuperVIPDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.7;
  }
}

// คลาสหลักที่ไม่ต้องแก้ไขเมื่อเพิ่ม discount type ใหม่
class DiscountCalculator {
  calculate(price: number, strategy: DiscountStrategy): number {
    return strategy.calculate(price);
  }
}

// การใช้งาน
const calculator = new DiscountCalculator();
const price = 1000;

console.log(calculator.calculate(price, new RegularDiscount())); // 900
console.log(calculator.calculate(price, new VIPDiscount())); // 800

// เพิ่ม discount type ใหม่ได้โดยไม่ต้องแก้ DiscountCalculator
class BlackFridayDiscount implements DiscountStrategy {
  calculate(price: number): number {
    return price * 0.5; // 50% off
  }
}

console.log(calculator.calculate(price, new BlackFridayDiscount())); // 500
```

**ประโยชน์:**
- ไม่ต้องเสี่ยงทำของเดิมพังเมื่อเพิ่มฟีเจอร์ใหม่
- สร้างระบบที่ขยายได้ง่าย

---

## **3. Liskov Substitution Principle (LSP)**
**"Objects ของ derived class ควรสามารถแทนที่ objects ของ base class ได้โดยไม่ทำให้โปรแกรมผิดพลาด"**

### ❌ ตัวอย่างที่ผิดหลักการ:
```typescript
class Rectangle {
  protected width: number = 0;
  protected height: number = 0;
  
  setWidth(width: number): void {
    this.width = width;
  }
  
  setHeight(height: number): void {
    this.height = height;
  }
  
  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  // ผิด: Square แทนที่ Rectangle ไม่ได้
  setWidth(width: number): void {
    this.width = width;
    this.height = width; // บังคับให้ height เท่ากับ width
  }
  
  setHeight(height: number): void {
    this.height = height;
    this.width = height; // บังคับให้ width เท่ากับ height
  }
}

// การใช้งานที่ทำให้เกิดปัญหา
function testRectangle(rectangle: Rectangle): void {
  rectangle.setWidth(5);
  rectangle.setHeight(4);
  
  // คาดว่า area = 20 (5 * 4)
  console.log(rectangle.getArea()); // 20
  
  // แต่ถ้าเป็น Square จะได้ 16 (4 * 4) แทน!
}

const rect = new Rectangle();
testRectangle(rect); // ได้ 20 ถูกต้อง

const square = new Square();
testRectangle(square); // ได้ 16 ผิด!
```

### ✅ ตัวอย่างที่ถูกต้อง:
```typescript
// ใช้ Composition แทน Inheritance
interface Shape {
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {}
  
  getArea(): number {
    return this.width * this.height;
  }
}

class Square implements Shape {
  constructor(private side: number) {}
  
  getArea(): number {
    return this.side * this.side;
  }
}

// หรือถ้าต้องการ inheritance จริงๆ
abstract class Shape {
  abstract getArea(): number;
}

class Rectangle extends Shape {
  constructor(private width: number, private height: number) {
    super();
  }
  
  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(private side: number) {
    super();
  }
  
  getArea(): number {
    return this.side * this.side;
  }
}

// การใช้งาน
function printArea(shape: Shape): void {
  console.log(`Area: ${shape.getArea()}`);
}

printArea(new Rectangle(5, 4)); // Area: 20
printArea(new Square(4)); // Area: 16
// ทั้งคู่ใช้ได้ถูกต้อง
```

**ประโยชน์:**
- Polymorphism ทำงานได้ถูกต้อง
- ลด unexpected behavior

---

## **4. Interface Segregation Principle (ISP)**
**"Clients ไม่ควรถูกบังคับให้ขึ้นต่อกับ interfaces ที่พวกเขาไม่ได้ใช้"**

### ❌ ตัวอย่างที่ผิดหลักการ:
```typescript
// Interface ที่ใหญ่เกินไป
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
  code(): void;
  design(): void;
  test(): void;
}

class Programmer implements Worker {
  work(): void { /* ... */ }
  eat(): void { /* ... */ }
  sleep(): void { /* ... */ }
  code(): void { /* ... */ }
  design(): void { /* ... */ } // Programmer อาจไม่ต้องการ design
  test(): void { /* ... */ } // Programmer อาจไม่ต้องการ test
}

class Designer implements Worker {
  work(): void { /* ... */ }
  eat(): void { /* ... */ }
  sleep(): void { /* ... */ }
  code(): void { /* ... */ } // Designer ไม่ต้องการ code!
  design(): void { /* ... */ }
  test(): void { /* ... */ } // Designer อาจไม่ต้องการ test
}
```

### ✅ ตัวอย่างที่ถูกต้อง:
```typescript
// แยก interfaces ออกเป็นส่วนเล็กๆ
interface Human {
  eat(): void;
  sleep(): void;
}

interface Workable {
  work(): void;
}

interface Codable {
  code(): void;
}

interface Designable {
  design(): void;
}

interface Testable {
  test(): void;
}

// แต่ละคลาส implement เฉพาะสิ่งที่ต้องการ
class Programmer implements Human, Workable, Codable, Testable {
  eat(): void { console.log("Programmer eating..."); }
  sleep(): void { console.log("Programmer sleeping..."); }
  work(): void { console.log("Programmer working..."); }
  code(): void { console.log("Programmer coding..."); }
  test(): void { console.log("Programmer testing..."); }
  // ไม่มี design() เพราะไม่จำเป็น
}

class Designer implements Human, Workable, Designable {
  eat(): void { console.log("Designer eating..."); }
  sleep(): void { console.log("Designer sleeping..."); }
  work(): void { console.log("Designer working..."); }
  design(): void { console.log("Designer designing..."); }
  // ไม่มี code() และ test() เพราะไม่จำเป็น
}

// หรือใช้ Interface Inheritance
interface Employee extends Human, Workable {}

interface Developer extends Employee, Codable, Testable {}

interface UXDesigner extends Employee, Designable {}

// ใช้งาน
const programmer: Developer = new Programmer();
programmer.code(); // OK
// programmer.design(); // Error: ไม่มีเมธอดนี้

const designer: UXDesigner = new Designer();
designer.design(); // OK
// designer.code(); // Error: ไม่มีเมธอดนี้
```

**ประโยชน์:**
- ไม่มี unused methods
- เปลี่ยน implementation ได้ง่าย
- ลด coupling

---

## **5. Dependency Inversion Principle (DIP)**
**"High-level modules ควรไม่ขึ้นกับ low-level modules โดยตรง ควรขึ้นกับ abstraction"**

### ❌ ตัวอย่างที่ผิดหลักการ:
```typescript
// High-level module
class PaymentService {
  // ผิด: ขึ้นกับ low-level module โดยตรง
  private paymentProcessor = new StripeProcessor();
  
  processPayment(amount: number): void {
    this.paymentProcessor.process(amount);
  }
}

// Low-level module
class StripeProcessor {
  process(amount: number): void {
    console.log(`Processing $${amount} via Stripe...`);
  }
}

class PayPalProcessor {
  process(amount: number): void {
    console.log(`Processing $${amount} via PayPal...`);
  }
}
```

### ✅ ตัวอย่างที่ถูกต้อง:
```typescript
// 1. กำหนด Abstraction (Interface)
interface PaymentProcessor {
  process(amount: number): void;
}

// 2. Low-level modules ขึ้นกับ abstraction
class StripeProcessor implements PaymentProcessor {
  process(amount: number): void {
    console.log(`Processing $${amount} via Stripe...`);
  }
}

class PayPalProcessor implements PaymentProcessor {
  process(amount: number): void {
    console.log(`Processing $${amount} via PayPal...`);
  }
}

class BankTransferProcessor implements PaymentProcessor {
  process(amount: number): void {
    console.log(`Processing $${amount} via Bank Transfer...`);
  }
}

// 3. High-level module ขึ้นกับ abstraction
class PaymentService {
  constructor(private processor: PaymentProcessor) {}
  
  processPayment(amount: number): void {
    this.processor.process(amount);
  }
}

// 4. Dependency Injection (ผ่าน constructor)
const stripeService = new PaymentService(new StripeProcessor());
stripeService.processPayment(100); // Processing $100 via Stripe...

const paypalService = new PaymentService(new PayPalProcessor());
paypalService.processPayment(200); // Processing $200 via PayPal...

// 5. หรือใช้ Factory Pattern
class PaymentProcessorFactory {
  static create(type: string): PaymentProcessor {
    switch (type) {
      case 'stripe': return new StripeProcessor();
      case 'paypal': return new PayPalProcessor();
      case 'bank': return new BankTransferProcessor();
      default: throw new Error('Unknown processor type');
    }
  }
}

// ใช้งานกับ Dependency Injection Container
class PaymentServiceWithConfig {
  constructor() {}
  
  processPayment(amount: number, processorType: string): void {
    const processor = PaymentProcessorFactory.create(processorType);
    processor.process(amount);
  }
}

const service = new PaymentServiceWithConfig();
service.processPayment(300, 'bank'); // Processing $300 via Bank Transfer...
```

**ตัวอย่างเพิ่มเติมกับ Database:**
```typescript
// Abstraction
interface UserRepository {
  save(user: User): void;
  findById(id: number): User | null;
}

// Concrete implementations
class MySQLUserRepository implements UserRepository {
  save(user: User): void {
    console.log(`Saving user ${user.name} to MySQL...`);
  }
  
  findById(id: number): User | null {
    console.log(`Finding user ${id} from MySQL...`);
    return null;
  }
}

class MongoDBUserRepository implements UserRepository {
  save(user: User): void {
    console.log(`Saving user ${user.name} to MongoDB...`);
  }
  
  findById(id: number): User | null {
    console.log(`Finding user ${id} from MongoDB...`);
    return null;
  }
}

// High-level module
class UserService {
  constructor(private repository: UserRepository) {}
  
  createUser(name: string): void {
    const user = new User(name);
    this.repository.save(user);
  }
}

// การใช้งาน
const mySqlService = new UserService(new MySQLUserRepository());
mySqlService.createUser("John"); // Saving user John to MySQL...

const mongoService = new UserService(new MongoDBUserRepository());
mongoService.createUser("Jane"); // Saving user Jane to MongoDB...
```

**ประโยชน์:**
- เปลี่ยน implementation ได้ง่าย
- ทดสอบง่ายด้วย Mock
- ลด coupling ระหว่าง modules

---

## **สรุปการใช้งาน SOLID ร่วมกัน**

```typescript
// ตัวอย่างระบบ Order ที่ใช้ SOLID ทั้งหมด
interface PaymentProcessor {
  process(amount: number): boolean;
}

interface NotificationService {
  send(message: string): void;
}

// คลาสที่รับผิดชอบเดียว (SRP)
class Order {
  constructor(
    public id: number,
    public amount: number,
    public customerEmail: string
  ) {}
}

// คลาสสำหรับ validation (SRP)
class OrderValidator {
  validate(order: Order): boolean {
    return order.amount > 0 && order.id > 0;
  }
}

// คลาสหลักที่ขึ้นกับ abstraction (DIP)
class OrderProcessor {
  constructor(
    private validator: OrderValidator,
    private paymentProcessor: PaymentProcessor,
    private notificationService: NotificationService
  ) {}
  
  // เปิดรับ extension (OCP)
  process(order: Order): boolean {
    if (!this.validator.validate(order)) {
      return false;
    }
    
    const paymentSuccess = this.paymentProcessor.process(order.amount);
    
    if (paymentSuccess) {
      this.notificationService.send(
        `Order ${order.id} processed successfully`
      );
    }
    
    return paymentSuccess;
  }
}

// Implementations ที่แยกตามหน้าที่ (ISP)
class StripeProcessor implements PaymentProcessor {
  process(amount: number): boolean {
    console.log(`Processing $${amount} via Stripe`);
    return true;
  }
}

class EmailNotificationService implements NotificationService {
  send(message: string): void {
    console.log(`Sending email: ${message}`);
  }
}

// การใช้งาน
const validator = new OrderValidator();
const paymentProcessor = new StripeProcessor();
const notificationService = new EmailNotificationService();

const orderProcessor = new OrderProcessor(
  validator,
  paymentProcessor,
  notificationService
);

const order = new Order(1, 100, "customer@example.com");
orderProcessor.process(order);
```

**ประโยชน์รวมของ SOLID:**
1. **รักษาได้ง่าย** (Maintainable)
2. **ขยายได้ง่าย** (Extensible)  
3. **ทดสอบได้ง่าย** (Testable)
4. **ใช้งานซ้ำได้** (Reusable)
5. **เข้าใจง่าย** (Understandable)

- หลักการเหล่านี้ช่วยสร้างโค้ดคุณภาพสูงที่จัดการและพัฒนาได้ง่ายในระยะยาว!

### **1.3 Defensive Programming**
```javascript
// ตรวจสอบ input
function processUserData(user) {
    if (!user || typeof user !== 'object') {
        throw new Error('Invalid user object');
    }
    
    // Default values
    const name = user.name || 'Guest';
    const age = user.age || 0;
    
    // Range checking
    if (age < 0 || age > 150) {
        throw new Error('Invalid age range');
    }
    
    return { name, age };
}

// TypeScript - ใช้ type system
interface User {
    id: number;
    name: string;
    email?: string; // Optional
}

function getUserInfo(user: User): string {
    return `${user.name} (${user.id})`;
}
```

---

## **2. เทคนิค Debugging ระดับพื้นฐาน**

### **2.1 Print Debugging (โบราณแต่ได้ผล)**
```python
# ใช้ f-string หรือ format
def calculate_discount(price, discount_percent):
    print(f"[DEBUG] Input: price={price}, discount={discount_percent}")
    
    if price <= 0:
        print(f"[ERROR] Invalid price: {price}")
        return 0
    
    discount_amount = price * (discount_percent / 100)
    print(f"[DEBUG] Discount amount: {discount_amount}")
    
    final_price = price - discount_amount
    print(f"[DEBUG] Final price: {final_price}")
    
    return max(final_price, 0)  # ไม่ให้ติดลบ
```

### **2.2 Logging แบบมีโครงสร้าง**
```javascript
// ใช้ logging levels
const LOG_LEVELS = {
    DEBUG: 0,
    INFO: 1,
    WARN: 2,
    ERROR: 3
};

class Logger {
    constructor(level = 'INFO') {
        this.level = LOG_LEVELS[level];
    }
    
    debug(message, data) {
        if (this.level <= LOG_LEVELS.DEBUG) {
            console.log(`[DEBUG] ${message}`, data);
        }
    }
    
    error(message, error) {
        if (this.level <= LOG_LEVELS.ERROR) {
            console.error(`[ERROR] ${message}`, {
                message: error.message,
                stack: error.stack,
                timestamp: new Date().toISOString()
            });
        }
    }
}

// ใช้งาน
const logger = new Logger('DEBUG');
logger.debug('Processing request', { userId: 123 });
```

---

## **3. Debugging Tools สำหรับภาษาต่างๆ**

### **3.1 Python**
```python
# pdb - Python Debugger
import pdb

def problematic_function(x, y):
    result = x + y
    pdb.set_trace()  # Breakpoint
    return result * 2

# ipdb (better version)
# pip install ipdb
import ipdb

def debug_function():
    ipdb.set_trace()
    # ใช้คำสั่ง:
    # n (next), s (step into), c (continue)
    # p variable (print)
    # l (list code)

# ใช้ logging module
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)
logger.debug('This is a debug message')
```

### **3.2 JavaScript/Node.js**
```javascript
// Chrome DevTools Debugging
// ใช้ debugger statement
function calculateTotal(items) {
    let total = 0;
    
    debugger; // Execution จะหยุดที่นี่
    
    for (let item of items) {
        total += item.price * item.quantity;
    }
    
    return total;
}

// Node.js debugging
// เริ่มต้น: node --inspect app.js
// หรือ: node --inspect-brk app.js (หยุดที่บรรทัดแรก)

// ใช้ console.table สำหรับ object/array
const users = [
    { id: 1, name: 'John', age: 25 },
    { id: 2, name: 'Jane', age: 30 }
];
console.table(users);

// console.trace() สำหรับ trace call stack
function a() {
    b();
}
function b() {
    c();
}
function c() {
    console.trace('Trace from function c');
}
```

### **3.3 Java**
```java
// IntelliJ IDEA / Eclipse Debugging
public class DebugExample {
    public static void main(String[] args) {
        int result = calculate(5, 3);
        System.out.println("Result: " + result);
    }
    
    public static int calculate(int a, int b) {
        // Set breakpoint here
        int sum = a + b;
        int product = a * b;
        return sum + product;
    }
}

// Logging with SLF4J + Logback
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Service {
    private static final Logger logger = LoggerFactory.getLogger(Service.class);
    
    public void process() {
        logger.debug("Starting process");
        try {
            // business logic
            logger.info("Process completed successfully");
        } catch (Exception e) {
            logger.error("Error in process", e);
        }
    }
}
```

### **3.4 C#**
```csharp
// Visual Studio Debugging
public class Calculator
{
    public int Add(int a, int b)
    {
        // Set breakpoint by clicking left margin
        int result = a + b;
        
        // Conditional breakpoint: right-click breakpoint
        // Condition: a > 10
        // Action: Print message
        
        return result;
    }
}

// Debug.WriteLine
using System.Diagnostics;

public class DebugExample
{
    public void ProcessData()
    {
        Debug.WriteLine($"Processing started at {DateTime.Now}");
        
        // Only in debug mode
        #if DEBUG
        Console.WriteLine("Debug mode is active");
        #endif
        
        Debug.Assert(value != null, "Value should not be null");
    }
}
```

---

## **4. Advanced Debugging Techniques**

### **4.1 Remote Debugging**
```javascript
// Node.js remote debugging
// Server: node --inspect=0.0.0.0:9229 server.js
// Chrome: chrome://inspect → Configure → Add localhost:9229

// Docker debugging
# Dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000 9229
CMD ["node", "--inspect=0.0.0.0:9229", "server.js"]

# docker run -p 3000:3000 -p 9229:9229 app
```

### **4.2 Memory Debugging**
```python
# Python memory profiling
# pip install memory-profiler
from memory_profiler import profile

@profile
def process_large_data():
    data = [i for i in range(1000000)]
    result = sum(data)
    del data  # Explicit cleanup
    return result

# tracemalloc สำหรับ tracking memory allocation
import tracemalloc

tracemalloc.start()

# ... code ที่ต้องการตรวจสอบ ...

snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')

print("[ Top 10 memory usage ]")
for stat in top_stats[:10]:
    print(stat)
```

### **4.3 Performance Debugging**
```javascript
// Chrome Performance Tab
// 1. Open DevTools → Performance
// 2. Click Record
// 3. Perform actions
// 4. Stop and analyze

// Console.time()
console.time('processing');

// Heavy operation
const result = processLargeArray(data);

console.timeEnd('processing'); // จะแสดงเวลาที่ใช้

// Web Worker สำหรับงานหนัก
const worker = new Worker('worker.js');
worker.postMessage(data);
worker.onmessage = (e) => {
    console.log('Result from worker:', e.data);
};
```

---

## **5. Systematic Debugging Process**

### **5.1 กระบวนการแก้ไขบั๊กแบบมีระบบ**
```
1. **Reproduce** - ทำให้เกิดปัญหาได้เสมอ
2. **Isolate** - แยกส่วนที่ทำให้เกิดปัญหา
3. **Hypothesize** - สร้างสมมติฐาน
4. **Test** - ทดสอบสมมติฐาน
5. **Fix** - แก้ไข
6. **Verify** - ตรวจสอบว่าแก้แล้ว
7. **Prevent** - ป้องกันไม่ให้เกิดอีก
```

### **5.2 Rubber Duck Debugging**
```python
# วิธี: อธิบายโค้ดให้เป็ดยางฟัง
def find_bug_example(numbers):
    # "สวัสดีเป็ดยาง วันนี้ฟังก์ชันนี้จะหาค่ามากสุดในอาเรย์"
    max_num = 0  # "ฉันตั้งค่าเริ่มต้นเป็น 0"
    
    for num in numbers:
        # "ถ้าตัวเลขปัจจุบันมากกว่าค่ามากสุด..."
        if num > max_num:
            max_num = num  # "...ก็อัพเดทค่ามากสุด"
    
    return max_num

# Test case ที่ fail
# print(find_bug_example([-5, -3, -1]))  # ได้ 0 แทนที่จะเป็น -1
# "อ๋อ! ปัญหาคือถ้าตัวเลขติดลบทั้งหมด จะได้ 0 แทน!"
```

### **5.3 Binary Search Debugging**
```javascript
// เมื่อไม่รู้ว่า bug อยู่ตรงไหน
function buggyFunction() {
    step1();
    // แบ่งครึ่ง: ลอง comment ส่วนที่สอง
    step2();
    step3();
    step4();
    
    // ถ้ายังมี bug → bug อยู่ในครึ่งแรก
    // แบ่งครึ่งอีก: step1() หรือ step2()?
    // ทำซ้ำจนเจอบรรทัดที่มีปัญหา
}

// ใช้ git bisect สำหรับ tracking ใน git
/*
git bisect start
git bisect bad          # ปัจจุบันมี bug
git bisect good v1.0    # Version ที่ไม่มี bug
# Git จะ checkout commit กลาง ให้ test
git bisect good/bad     # บอก git ว่าครั้งนี้ good หรือ bad
git bisect reset        # เมื่อเจอ commit ที่มี bug
*/
```

---

## **6. Debugging Tools และ Utilities**

### **6.1 Browser DevTools Tricks**
```javascript
// Live expression monitoring
// ใน Chrome DevTools → Console → "Eye" icon
// พิมพ์: document.activeElement

// Copy object as JSON
console.log(JSON.stringify(userObject, null, 2));

// Monitor events
monitorEvents(document.getElementById('button'), 'click');

// Store global reference
// ใน console: temp1 = $0 (อ้างอิง element ที่เลือกอยู่)
```

### **6.2 Post-mortem Debugging**
```python
# Python core dumps
import faulthandler
import signal

faulthandler.enable()
faulthandler.register(signal.SIGUSR1)  # Dump on signal

# หรือใช้ coredump
import coredump
coredump.enable()

# เมื่อโปรแกรม crash จะได้ backtrace
```

```javascript
// Node.js core dumps
// เริ่มต้น: node --abort-on-uncaught-exception app.js
// หรือใช้ llnode สำหรับ analyze core dump

// Winston logger สำหรับบันทึก error
const winston = require('winston');

const logger = winston.createLogger({
    transports: [
        new winston.transports.File({ 
            filename: 'error.log',
            level: 'error'
        })
    ]
});

process.on('uncaughtException', (error) => {
    logger.error('Uncaught exception:', error);
    process.exit(1);
});
```

---

## **7. Debugging ใน Production**

### **7.1 Structured Logging**
```python
# Python with JSON logs
import json
import logging
from pythonjsonlogger import jsonlogger

logger = logging.getLogger()
logHandler = logging.StreamHandler()
formatter = jsonlogger.JsonFormatter(
    '%(asctime)s %(name)s %(levelname)s %(message)s'
)
logHandler.setFormatter(formatter)
logger.addHandler(logHandler)

logger.error("Payment failed", extra={
    "user_id": 123,
    "order_id": "ORD-456",
    "amount": 99.99,
    "error_code": "INSUFFICIENT_FUNDS"
})
```

### **7.2 Distributed Tracing**
```javascript
// OpenTelemetry สำหรับ microservices
const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
const { SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { JaegerExporter } = require('@opentelemetry/exporter-jaeger');

const provider = new NodeTracerProvider();
provider.addSpanProcessor(
    new SimpleSpanProcessor(new JaegerExporter())
);
provider.register();

// ในแต่ละ request
const trace = require('@opentelemetry/api').trace;
const tracer = trace.getTracer('payment-service');

async function processPayment(request) {
    return tracer.startActiveSpan('processPayment', async (span) => {
        span.setAttribute('user.id', request.userId);
        span.setAttribute('payment.amount', request.amount);
        
        try {
            // processing logic
            span.setStatus({ code: SpanStatusCode.OK });
        } catch (error) {
            span.setStatus({ 
                code: SpanStatusCode.ERROR,
                message: error.message 
            });
            span.recordException(error);
            throw error;
        } finally {
            span.end();
        }
    });
}
```

### **7.3 Feature Flags สำหรับ Debugging**
```javascript
// เปิด/ปิด debug features ใน production
const featureFlags = {
    debugMode: process.env.DEBUG_MODE === 'true',
    logSqlQueries: false,
    enablePerformanceLogging: false
};

function processOrder(order) {
    if (featureFlags.debugMode) {
        console.log('[DEBUG] Processing order:', order.id);
    }
    
    // Business logic
    
    if (featureFlags.logSqlQueries) {
        logDatabaseQueries();
    }
}

// Dynamic feature flags (จาก database/API)
async function checkFeatureFlag(flagName) {
    const response = await fetch(`/api/flags/${flagName}`);
    const data = await response.json();
    return data.enabled;
}
```

---

## **8. Checklist และ Best Practices**

### **Debugging Checklist:**
- [ ] สามารถ reproduce bug ได้เสมอ
- [ ] ตรวจสอบ logs และ error messages
- [ ] ใช้ breakpoints และ step through
- [ ] ตรวจสอบ variable values
- [ ] ลองใช้ test cases ง่ายๆ ก่อน
- [ ] ตรวจสอบ assumptions
- [ ] หา minimal reproduction case

### **Prevention Checklist:**
- [ ] ใช้ static type checking (TypeScript, MyPy)
- [ ] เขียน unit tests
- [ ] ใช้ linter (ESLint, Pylint, SonarQube)
- [ ] Code review
- [ ] ใช้ design patterns
- [ ] Defensive programming
- [ ] Error handling ที่เหมาะสม

### **คำถามที่ควรถามเวลา debug:**
1. อะไรคือ expected behavior?
2. อะไรคือ actual behavior?
3. เกิดเมื่อไหร่? (เวลา, เงื่อนไข)
4. เกิดที่ไหน? (module, function)
5. ทำไมถึงคิดว่านี่คือ bug?
6. มีอะไรเปลี่ยนไปก่อนเกิด bug?
7. มี log หรือ error message อะไรบ้าง?

---

## **สรุป**
การ debug ที่ดีเริ่มตั้งแต่การออกแบบโค้ดที่ดี ใช้ tools ที่เหมาะสมกับภาษานั้นๆ และมีกระบวนการที่เป็นระบบ จำไว้ว่า:
- **อย่าคาดเดา** - ใช้ data และ evidence
- **แยกปัญหา** - Divide and conquer
- **ทำบันทึก** - สิ่งที่ลองแล้วได้ผล/ไม่ได้ผล
- **เรียนรู้** - ทุก bug คือโอกาสเรียนรู้
- **ป้องกัน** - ใช้ lessons learned ป้องกันในอนาคต

#### Happy debugging! 🐛🔍

-----------------------------

# **การหา Root Cause** (Root Cause Analysis - RCA)

## **1. แนวคิดพื้นฐาน**
**Root Cause ≠ Symptom ≠ Immediate Cause**
- **Symptom**: สิ่งที่เราเห็น (โปรแกรม crash, error message)
- **Immediate Cause**: สาเหตุโดยตรง (null pointer, division by zero)
- **Root Cause**: สาเหตุแท้จริงที่อยู่ลึกที่สุด (design flaw, missing validation)

## **2. กระบวนการหา Root Cause แบบเป็นระบบ**

### **2.1 The 5 Whys Technique**
```javascript
// ตัวอย่าง: ระบบชำระเงินล้มเหลว

1. ❓ ทำไมระบบชำระเงินถึงล้มเหลว?
   ✅ เพราะเกิด "Database connection timeout"

2. ❓ ทำไม Database connection ถึง timeout?
   ✅ เพราะ connection pool เต็ม

3. ❓ ทำไม connection pool ถึงเต็ม?
   ✅ เพราะ connections ไม่ถูกปิดหลังจากใช้งาน

4. ❓ ทำไม connections ไม่ถูกปิด?
   ✅ เพราะ developer ลืมเรียก connection.close() ในบางกรณี

5. ❓ ทำไม developer ถึงลืม?
   ✅ เพราะไม่มี coding standard และ code review process
   ✅ เพราะไม่มี unit test สำหรับ connection management

🎯 **Root Cause**: ขาดกระบวนการควบคุมคุณภาพโค้ด
```

### **2.2 Fishbone Diagram (Ishikawa)**
```
               กระบวนการ              เครื่องมือ
                    \                 /
                     \               /
                      v             v
คน → Training ไม่เพียงพอ → ไม่มี Unit Test Framework
                      \           /
                       \         /
                        \       /
              ระบบชำระเงิน Crash
                        /       \
                       /         \
                      /           \
    ขาด Code Review ← วิธีการ      Material
           ↑                ↑           ↑
       Management      Procedure     Technology
```

## **3. เทคนิคการหา Root Cause สำหรับ Software Bugs**

### **3.1 Reproduce with Minimal Case**
```python
# ❌ ปัญหาดั้งเดิม
def process_user_data(users):
    results = []
    for user in users:
        # ... 50 บรรทัดของโค้ด ...
        if user['age'] > 18:
            processed = complex_processing(user)
            results.append(processed)
    return results

# ✅ ลดให้เหลือ minimal case
def test_minimal_case():
    # ลองกับข้อมูลน้อยที่สุด
    users = [{'age': 20}, {'age': 17}, {'age': None}]
    
    # ลองทีละกรณี
    print(process_user_data([{'age': 20}]))  # ทำงาน?
    print(process_user_data([{'age': 17}]))  # ทำงาน?
    print(process_user_data([{'age': None}]))  # Error เกิดตรงนี้!
    
    # พบว่า error จาก age = None
```

### **3.2 Binary Search Debugging**
```typescript
// เมื่อไม่รู้ว่า bug อยู่ช่วงไหนของโค้ด
async function processOrder(orderId: string): Promise<void> {
    // 1. แบ่งครึ่ง: ลอง comment ครึ่งหลัง
    const order = await getOrder(orderId);  // ✅ ครึ่งแรก
    // validateOrder(order);               // ⏸️ ลอง comment
    // processPayment(order);              // ⏸️
    // updateInventory(order);             // ⏸️
    // sendNotification(order);            // ⏸️ ครึ่งหลัง
    
    // 2. ถ้าไม่ error → bug อยู่ในครึ่งหลัง
    // 3. แบ่งครึ่งหลังอีก...
    
    // หรือใช้ conditional breakpoint
    debugger; // ตั้ง condition: order.total < 0
}
```

### **3.3 Event Replay & Log Analysis**
```javascript
// เก็บ context ทั้งหมดเมื่อเกิด error
class ErrorTracker {
    static captureContext(error, context = {}) {
        const errorReport = {
            timestamp: new Date().toISOString(),
            error: {
                message: error.message,
                stack: error.stack,
                type: error.constructor.name
            },
            context: {
                user: context.userId,
                action: context.action,
                inputs: context.inputs,
                environment: {
                    url: window.location.href,
                    userAgent: navigator.userAgent,
                    timestamp: Date.now()
                },
                state: {
                    reduxState: store.getState(),
                    localStorage: { ...localStorage }
                }
            },
            breadcrumbs: this.getBreadcrumbs()
        };
        
        this.sendToServer(errorReport);
    }
    
    static getBreadcrumbs() {
        // เก็บประวัติการกระทำก่อนเกิด error
        return window.breadcrumbs || [];
    }
}

// ใช้งาน
try {
    processPayment();
} catch (error) {
    ErrorTracker.captureContext(error, {
        userId: currentUser.id,
        action: 'process_payment',
        inputs: { amount, paymentMethod }
    });
    throw error;
}
```

## **4. เทคนิคเฉพาะสถานการณ์**

### **4.1 Race Conditions & Concurrency Issues**
```python
import threading
import time
from concurrent.futures import ThreadPoolExecutor
import logging

logging.basicConfig(level=logging.DEBUG)

# ❌ Race condition example
class BankAccount:
    def __init__(self):
        self.balance = 100
        self.lock = threading.Lock()
    
    def withdraw(self, amount):
        # ใช้ logging เพื่อติดตาม
        logging.debug(f"Attempting to withdraw {amount}")
        
        with self.lock:  # 🔒 ใช้ lock
            logging.debug(f"Current balance: {self.balance}")
            
            if self.balance >= amount:
                time.sleep(0.01)  # Simulate processing delay
                self.balance -= amount
                logging.debug(f"Withdrew {amount}, new balance: {self.balance}")
                return True
            else:
                logging.debug("Insufficient funds")
                return False

# Debug race condition
def debug_concurrency():
    account = BankAccount()
    
    # ใช้ ThreadPoolExecutor เพื่อควบคุม threads
    with ThreadPoolExecutor(max_workers=10) as executor:
        futures = []
        for i in range(5):
            future = executor.submit(account.withdraw, 50)
            futures.append(future)
        
        # เก็บผลลัพธ์
        results = [f.result() for f in futures]
    
    print(f"Final balance: {account.balance}")
    print(f"Successful withdrawals: {sum(results)}")
    
    # วิเคราะห์: ถ้า final balance ติดลบ แสดงว่า有 race condition
```

### **4.2 Memory Leaks**
```javascript
// Chrome DevTools Memory Profiling
class MemoryLeakDetector {
    constructor() {
        this.snapshots = [];
        this.leakCandidates = new Set();
    }
    
    takeSnapshot(label) {
        if (window.performance && window.performance.memory) {
            const snapshot = {
                label,
                timestamp: Date.now(),
                usedJSHeapSize: window.performance.memory.usedJSHeapSize,
                totalJSHeapSize: window.performance.memory.totalJSHeapSize,
                nodes: document.getElementsByTagName('*').length,
                eventListeners: this.countEventListeners()
            };
            
            this.snapshots.push(snapshot);
            console.table(this.snapshots);
            
            // ตรวจสอบ memory growth
            if (this.snapshots.length > 1) {
                const growth = snapshot.usedJSHeapSize - 
                             this.snapshots[this.snapshots.length - 2].usedJSHeapSize;
                if (growth > 1000000) { // 1MB growth
                    console.warn(`⚠️ Memory growth detected: ${growth} bytes`);
                }
            }
        }
    }
    
    countEventListeners() {
        // นับ event listeners (อาจทำให้ performance ตก)
        let count = 0;
        const elements = document.querySelectorAll('*');
        elements.forEach(el => {
            if (el._events) {
                count += Object.keys(el._events).length;
            }
        });
        return count;
    }
    
    // ใช้กับ setInterval เพื่อตรวจสอบ
    startMonitoring(interval = 5000) {
        this.intervalId = setInterval(() => {
            this.takeSnapshot(`auto-${Date.now()}`);
        }, interval);
    }
}

// ใช้งาน
const detector = new MemoryLeakDetector();
detector.takeSnapshot('initial');
// ... ทำบางอย่าง ...
detector.takeSnapshot('after-action');
```

### **4.3 Performance Bottlenecks**
```python
import cProfile
import pstats
from line_profiler import LineProfiler
import time

def find_performance_bottlenecks():
    # วิธีที่ 1: cProfile สำหรับ overview
    profiler = cProfile.Profile()
    profiler.enable()
    
    # รันโค้ดที่ต้องการวิเคราะห์
    expensive_operation()
    
    profiler.disable()
    
    # แสดงผลลัพธ์
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumulative')
    stats.print_stats(10)  # แสดง 10 functions ที่ใช้เวลามากที่สุด
    
    # วิธีที่ 2: Line-by-line profiling
    lp = LineProfiler()
    
    # เพิ่มฟังก์ชันที่ต้องการ analyze
    lp.add_function(expensive_operation)
    
    lp.enable()
    expensive_operation()
    lp.disable()
    
    # แสดงผลลัพธ์ line-by-line
    lp.print_stats()
    
    # วิธีที่ 3: Manual timing
    def time_function(func, *args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        end = time.perf_counter()
        
        print(f"{func.__name__} took {end - start:.6f} seconds")
        return result
    
    # วิธีที่ 4: ตรวจสอบ database queries
    import django.db.connection
    from django.db import reset_queries
    
    reset_queries()
    
    # รันโค้ดที่ query database
    users = User.objects.filter(is_active=True).prefetch_related('profile')
    
    print(f"Number of queries: {len(django.db.connection.queries)}")
    for i, query in enumerate(django.db.connection.queries, 1):
        print(f"Query {i}: {query['sql'][:100]}...")
        print(f"Time: {query['time']} seconds")
```

## **5. Tools สำหรับ Root Cause Analysis**

### **5.1 Application Performance Monitoring (APM)**
```yaml
# Datadog Configuration
datadog:
  apm:
    enabled: true
    trace_sample_rate: 1.0
    service_mapping:
      - name: "payment-service"
        type: "web"
      - name: "user-database"
        type: "db"
  
  # Distributed Tracing
  tracing:
    enabled: true
    headers:
      - "x-datadog-trace-id"
      - "x-datadog-parent-id"
  
  # Custom Metrics
  metrics:
    - name: "payment.error_rate"
      type: "rate"
    - name: "api.response_time.p95"
      type: "gauge"
```

### **5.2 Log Aggregation & Analysis**
```python
# Structured Logging with Context
import json
import logging
from pythonjsonlogger import jsonlogger

class StructuredLogger:
    def __init__(self):
        self.logger = logging.getLogger(__name__)
        handler = logging.StreamHandler()
        formatter = jsonlogger.JsonFormatter(
            '%(asctime)s %(name)s %(levelname)s %(message)s %(context)s'
        )
        handler.setFormatter(formatter)
        self.logger.addHandler(handler)
    
    def error_with_context(self, message, error, context=None):
        log_data = {
            "message": message,
            "error": {
                "type": type(error).__name__,
                "message": str(error),
                "stack_trace": self._format_stack_trace(error)
            },
            "context": context or {},
            "investigation_path": self._suggest_investigation(error)
        }
        
        self.logger.error(message, extra={'context': json.dumps(log_data)})
    
    def _suggest_investigation(self, error):
        """แนะนำแนวทางการ investigate ตาม error type"""
        suggestions = {
            "KeyError": "ตรวจสอบว่ามี key นี้ใน dictionary จริงหรือไม่",
            "TypeError": "ตรวจสอบ type ของ arguments",
            "ConnectionError": "ตรวจสอบ network connection และ firewall",
            "TimeoutError": "ตรวจสอบ server load และ query performance",
            "MemoryError": "ตรวจสอบ memory leak และ data size"
        }
        
        return suggestions.get(type(error).__name__, "ตรวจสอบ stack trace")
```

### **5.3 Error Tracking Systems**
```javascript
// Sentry Integration
import * as Sentry from '@sentry/node';
import * as Tracing from '@sentry/tracing';

Sentry.init({
  dsn: 'YOUR_DSN',
  tracesSampleRate: 1.0,
  environment: process.env.NODE_ENV,
  integrations: [
    new Tracing.Integrations.Mysql(),  // Database tracing
    new Tracing.Integrations.Express(), // HTTP request tracing
  ],
  beforeSend(event) {
    // เพิ่ม custom context
    event.extra = {
      ...event.extra,
      user_id: currentUser?.id,
      request_id: requestId,
      feature_flags: activeFeatureFlags
    };
    
    // Group similar errors
    if (event.exception?.values?.[0]?.value?.includes('Database timeout')) {
      event.fingerprint = ['database-timeout'];
    }
    
    return event;
  }
});

// Custom error boundary
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    Sentry.withScope(scope => {
      scope.setExtras(errorInfo);
      scope.setTag('component_stack', errorInfo.componentStack);
      
      // หา root cause จาก user actions
      const userActions = this.getUserActions();
      scope.setContext('user_actions', {
        last_actions: userActions.slice(-5)
      });
      
      Sentry.captureException(error);
    });
  }
  
  getUserActions() {
    // เก็บประวัติการกระทำของผู้ใช้
    return window.userActionLog || [];
  }
}
```

## **6. Root Cause Patterns ตามประเภทปัญหา**

### **6.1 Heisenbugs (Bugs ที่เปลี่ยนพฤติกรรมเมื่อพยายาม debug)**
```python
# สาเหตุทั่วไป: Timing issues, uninitialized memory
import threading

class HeisenbugExample:
    def __init__(self):
        self.value = None  # ไม่ได้ initialize ค่าเริ่มต้น
    
    def thread1(self):
        if self.value is None:
            # ถ้าเพิ่งเพิ่ม print ตรงนี้ อาจเปลี่ยน timing
            # print("Value is null")  # เพิ่มบรรทัดนี้อาจทำให้ bug หาย!
            self.value = 42
    
    def thread2(self):
        # อาจเห็น self.value เป็น None หรือ 42 ขึ้นกับ timing
        result = self.value * 2  # ❌ Crash ถ้า value เป็น None
        return result

# วิธี debug: ใช้ deterministic execution
def debug_heisenbug():
    import random
    
    # 1. เก็บ seed ของ random generator
    random.seed(42)  # ทำให้ reproducible
    
    # 2. ควบคุม thread scheduling
    import threading
    threading.settrace(thread_trace)  # Trace thread switches
    
    # 3. ใช้ deterministic sleep
    def deterministic_sleep():
        pass  # แทนที่ time.sleep() ด้วย mock
    
    # 4. Record และ replay execution
    recorder = ExecutionRecorder()
    recorder.start()
    
    # รันโปรแกรม
    run_program()
    
    recorder.stop()
    recorder.save('execution.log')
    
    # Replay เพื่อ reproduce
    replayer = ExecutionReplayer('execution.log')
    replayer.replay()
```

### **6.2 Bohrbugs (Bugs ที่ reproduce ได้เสมอ)**
```typescript
// สาเหตุทั่วไป: Logic errors, boundary conditions
class BohrbugExample {
    // ตัวอย่าง: Off-by-one error
    calculateAverage(scores: number[]): number {
        // ❌ Bug: ใช้ length - 1 (off-by-one)
        let sum = 0;
        for (let i = 0; i < scores.length - 1; i++) {
            sum += scores[i];
        }
        return sum / (scores.length - 1); // ❌ หารด้วย length - 1
    }
    
    // วิธี debug: Boundary value analysis
    debugBoundaryConditions(): void {
        const testCases = [
            { input: [], expected: 0 }, // Empty array
            { input: [1], expected: 1 }, // Single element
            { input: [1, 2, 3], expected: 2 }, // Normal case
            { input: [0, 0, 0], expected: 0 }, // All zeros
            { input: [1000000, -1000000], expected: 0 }, // Large numbers
            { input: [1.1, 2.2, 3.3], expected: 2.2 }, // Floating point
        ];
        
        testCases.forEach((testCase, index) => {
            try {
                const result = this.calculateAverage(testCase.input);
                if (Math.abs(result - testCase.expected) > 0.0001) {
                    console.log(`Test ${index} failed:`, {
                        input: testCase.input,
                        expected: testCase.expected,
                        actual: result
                    });
                }
            } catch (error) {
                console.log(`Test ${index} crashed:`, error.message);
            }
        });
    }
}
```

## **7. Root Cause Analysis Framework**

### **7.1 Kepner-Tregoe Problem Analysis**
```markdown
## Step 1: Problem Specification
- **What**: ระบบชำระเงินตอบสนองช้า
- **Where**: ใน production environment เท่านั้น
- **When**: เวลา 14:00-16:00 ทุกวัน
- **Extent**: 30% ของผู้ใช้ได้รับผลกระทบ

## Step 2: Problem Analysis
### มีอะไรเปลี่ยนแปลง?
- ✅ Database migration เมื่อ 2 วันก่อน
- ✅ New feature deployment เมื่อเช้า
- ❌ ไม่มี changes ใน payment service

### Comparison: IS vs IS NOT
| Aspect         | IS (เกิดปัญหา)          | IS NOT (ไม่เกิดปัญหา)     |
|----------------|------------------------|--------------------------|
| Time           | 14:00-16:00            | อื่นๆ                    |
| User Region    | Asia                   | Europe/USA               |
| Payment Method | Credit Card            | PayPal                   |
| Amount         | > $100                 | < $100                   |

## Step 3: Root Cause Hypothesis
**สมมติฐาน**: Third-party payment gateway ใน Asia region มี rate limiting
**การทดสอบ**: ส่ง request ขนาดเล็ก (< $100) ในเวลาเดียวกัน → ควรทำงานได้
**ผลลัพธ์**: ทำงานได้ → ยืนยันสมมติฐาน
```

### **7.2 RCA Report Template**
```markdown
```
# ROOT CAUSE ANALYSIS REPORT

## 1. Executive Summary
- **ปัญหา**: Payment processing failures
- **ผลกระทบ**: 30% failure rate, $50K lost revenue
- **Root Cause**: Database connection pool exhaustion

## 2. Timeline
- 14:00: Error rate เริ่มเพิ่ม
- 14:30: Reached 30% failure rate
- 15:00: Team notified
- 16:00: Implemented fix

## 3. Investigation
### Symptoms
- Database connection timeouts
- Increased response time (p95: 2s → 10s)
- Application logs show "Too many connections"

### Immediate Cause
- Connection leak in user session management

### Root Cause
- Missing connection.close() in async/await error handling
- No connection pool monitoring
- Insufficient load testing

## 4. Evidence
- **Logs**: Connection count growing linearly
- **Metrics**: Connection pool at 100% for 2 hours
- **Code Review**: Found 3 missing close() calls

## 5. Corrective Actions
### Short-term
- [x] Restart application servers
- [x] Increase connection pool size

### Long-term
- [ ] Fix connection leak in code
- [ ] Add connection pool monitoring
- [ ] Implement circuit breaker pattern
- [ ] Add automated connection testing

## 6. Preventive Measures
- [ ] Code review checklist สำหรับ resource management
- [ ] Weekly connection pool audit
- [ ] Load testing ใน staging environment
- [ ] Alert สำหรับ connection pool usage > 80%

## 7. Lessons Learned
- Database connections ต้อง managed อย่างระมัดระวัง
- Monitoring ต้องครอบคลุมทั้ง application และ infrastructure metrics
- Error handling ต้อง cleanup resources เสมอ


## **8. คำถามสำคัญสำหรับหา Root Cause**

### **8.1 Diagnostic Questions**
```
1. **Reproducibility**
   - สามารถ reproduce ได้เสมอหรือไม่?
   - ต้องมี conditions อะไรบ้าง?
   - เกิดขึ้นใน environment ไหน?

2. **Timing**
   - เกิดขึ้นเมื่อไหร่?
   - มี pattern หรือไม่? (เวลา, วัน, load)
   - เกิดขึ้นหลังจากอะไร?

3. **Scope**
   - ใคร/อะไรได้รับผลกระทบ?
   - กระทบมากน้อยแค่ไหน?
   - มีอะไรที่ **ไม่** ได้รับผลกระทบ?

4. **Changes**
   - มีอะไรเปลี่ยนแปลงก่อนเกิดปัญหา?
   - Code changes ล่าสุด?
   - Infrastructure changes?
   - Third-party API changes?

5. **Workarounds**
   - มีอะไรที่ทำให้ปัญหาเลวร้ายลง?
   - มีอะไรที่ทำให้ปัญหาดีขึ้น?
   - Temporary fix ที่ได้ผล?
```

### **8.2 Technical Investigation Checklist**
```python
def root_cause_investigation_checklist():
    checklist = {
        'data_issues': [
            'ตรวจสอบ input data validity',
            'ตรวจสอบ data type และ format',
            'ตรวจสอบ data consistency',
            'ตรวจสอบ foreign key constraints',
        ],
        'code_issues': [
            'ตรวจสอบ logic errors',
            'ตรวจสอบ boundary conditions',
            'ตรวจสอบ error handling',
            'ตรวจสอบ resource management',
        ],
        'environment_issues': [
            'ตรวจสอบ dependencies versions',
            'ตรวจสอบ configuration values',
            'ตรวจสอบ permissions',
            'ตรวจสอบ resource limits',
        ],
        'timing_issues': [
            'ตรวจสอบ race conditions',
            'ตรวจสอบ deadlocks',
            'ตรวจสอบ timeout settings',
            'ตรวจสอบ caching issues',
        ],
    }
    
    return checklist
```

## **9. เทคนิคป้องกันปัญหาเกิดซ้ำ**

### **9.1 Post-Mortem Process**
```typescript
class PostMortemManager {
    async conductPostMortem(incident: Incident): Promise<ActionItems> {
        const analysis = await this.analyzeIncident(incident);
        
        return {
            immediateActions: this.getImmediateActions(analysis),
            preventiveActions: this.getPreventiveActions(analysis),
            detectiveActions: this.getDetectiveActions(analysis),
            
            // Blameless culture
            focusOnSystemsNotPeople: true,
            lessonsForWholeTeam: this.extractLessons(analysis),
            
            // Follow-up
            assignees: this.assignActionItems(analysis),
            dueDates: this.setRealisticDeadlines(analysis),
            successMetrics: this.defineSuccessMetrics(analysis)
        };
    }
    
    private getPreventiveActions(analysis: Analysis): PreventiveAction[] {
        return [
            {
                type: 'code_change',
                description: 'Add connection pool monitoring',
                priority: 'HIGH',
                expectedImpact: 'Prevent connection exhaustion'
            },
            {
                type: 'process_change',
                description: 'Add resource management to code review checklist',
                priority: 'MEDIUM',
                expectedImpact: 'Catch similar issues early'
            },
            {
                type: 'testing',
                description: 'Add load testing for connection handling',
                priority: 'HIGH',
                expectedImpact: 'Uncover limits before production'
            }
        ];
    }
}
```

## **สรุป**
การหา root cause ที่ดีต้องมี:
1. **กระบวนการที่เป็นระบบ** - อย่าเดา อย่าตัดสินโดยไม่มีข้อมูล
2. **เครื่องมือที่เหมาะสม** - ใช้ทั้ง technical tools และ analytical frameworks
3. **ทัศนคติที่ถูกต้อง** - Blameless culture, focus on learning
4. **การป้องกันซ้ำ** - ไม่จบแค่การแก้ bug แต่ต้องป้องกันไม่ให้เกิดอีก

**Remember:** "สำหรับทุกปัญหาที่ซับซ้อน มักมีคำอธิบายที่เรียบง่ายและ...ผิด" - ตรวจสอบข้อมูลให้ดีก่อนตัดสินใจ!
