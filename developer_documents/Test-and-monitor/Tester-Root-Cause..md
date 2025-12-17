## **การหา Root Cause** (Root Cause Analysis - RCA)

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
