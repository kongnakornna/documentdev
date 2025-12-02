###  DevOps Jenkins  & GitHub Actions  php  คู่มือ แบบละเอียด Step by step

- เริ่มต้นทำ CI/CD ของ PHP ด้วย Jenkins และ GitHub Actions ทำได้โดยเตรียมโปรเจกต์ PHP ให้รองรับการรันทดสอบ แล้วค่อยตั้งค่า Pipeline บน Jenkins และ Workflow บน GitHub Actions ให้ทำงานอัตโนมัติเมื่อมีการ push โค้ดขึ้น GitHub. ด้านล่างเป็นคู่มือแบบ Step by step แยกเป็น 2 ส่วน: Jenkins และ GitHub Actions สำหรับโปรเจกต์ PHP ทั่วไป (เช่น Laravel หรือ PHP ธรรมดา).[^1][^2][^3][^4][^5][^6]

***

## เตรียมโปรเจกต์ PHP + GitHub

1. สร้าง/เตรียมโปรเจกต์ PHP
    - ใช้ Git จัดการซอร์สโค้ด แล้วสร้าง repo บน GitHub.
    - แนะนำให้ใช้ Composer จัดการ dependency และใช้ PHPUnit/ Pest หรือ framework test อื่นสำหรับรันทดสอบอัตโนมัติ.[^3][^1]
2. เพิ่มไฟล์พื้นฐานในโปรเจกต์
    - `composer.json` (กำหนด dependency และสคริปต์ เช่น `"test": "phpunit"`).
    - โฟลเดอร์ `tests/` และไฟล์ทดสอบเบื้องต้น เพื่อให้ CI รันแล้วไม่ล้ม.[^5][^1]
3. จัดการ secret / ตัวแปร environment
    - ใน GitHub ให้ไปที่ Settings → Secrets and variables → Actions แล้วเพิ่มค่าที่ต้องใช้ เช่น `DB_HOST`, `DB_PASSWORD`, `SSH_KEY` (ถ้าต้อง deploy ผ่าน SSH).
    - ใน Jenkins ให้เพิ่ม Credentials (เช่น SSH username/password หรือ key, GitHub token) ที่จะใช้ใน Pipeline.[^7][^8]

***

## Jenkins: ติดตั้งและเชื่อมต่อกับ GitHub

1. ติดตั้ง Jenkins และปลั๊กอินหลัก
    - ติดตั้ง Jenkins บนเซิร์ฟเวอร์ (Linux/Windows ก็ได้) แล้วเข้าเว็บ UI.
    - แนะนำให้ติดตั้งปลั๊กอิน: Git, GitHub, Pipeline (และถ้าใช้ PHP CodeSniffer/ PHPStan ก็มีปลั๊กอินเสริมได้).[^9][^1][^7]
2. ติดตั้ง PHP และเครื่องมือบน Jenkins agent
    - บนเครื่องที่ Jenkins ใช้รัน job ให้ติดตั้ง PHP, Composer, PHPUnit/เครื่องมือทดสอบ, git ให้เรียบร้อย.
    - ถ้าใช้ Docker สามารถสร้าง image ที่มี PHP + Composer + extensions ที่ต้องใช้สำหรับโปรเจกต์ PHP แล้วให้ Jenkins ใช้ image นั้นเป็น agent.[^1][^3]
3. เชื่อม Jenkins กับ GitHub (webhook)
    - ใน GitHub repo: ไปที่ Settings → Webhooks → Add webhook ใส่ `http(s)://<JENKINS_URL>/github-webhook/` เป็น Payload URL และเลือก events สำหรับ Push/ Pull request.[^8][^10][^7]
    - ใน Jenkins:
        - ไปที่ Manage Jenkins → Configure System กำหนด GitHub server (ถ้าต้องใช้ OAuth/Token).
        - ใน job/pipeline ให้ติ๊ก “GitHub hook trigger for GITScm polling” เพื่อให้ Jenkins trigger จาก webhook ได้.[^11][^7]
4. สร้าง Pipeline Job สำหรับ PHP
    - ใน Jenkins เลือก “New Item” → ตั้งชื่อ → เลือก “Pipeline”.
    - ในส่วน Pipeline เลือก “Pipeline script from SCM” เพื่อดึง Jenkinsfile จาก GitHub repo แล้วใส่ URL repo และ branch ที่ต้องการ.[^3][^9]
5. เขียน Jenkinsfile (ตัวอย่างโครงสำหรับ PHP)
สร้างไฟล์ `Jenkinsfile` ใน root ของโปรเจกต์ เช่น:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/your-php-repo.git'
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'composer install --no-interaction --prefer-dist'
            }
        }

        stage('Run tests') {
            steps {
                sh './vendor/bin/phpunit'
            }
        }

        stage('Build/Prepare artifacts') {
            steps {
                sh 'php -l index.php'
            }
        }

        stage('Deploy to server') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                # ตัวอย่าง deploy ผ่าน SSH
                rsync -avz --delete ./ user@your-server:/var/www/yourapp
                '''
            }
        }
    }
}
```

    - แบ่ง stage ให้ชัดเจน: Checkout → Install → Test → Build → Deploy จะช่วยให้ debug ง่าย.
    - ปรับคำสั่ง `sh` ให้ตรงกับ framework ที่ใช้ (เช่น Laravel: `php artisan test`, `php artisan migrate`, ฯลฯ).[^2][^1][^3]
6. ทดสอบ Pipeline บน Jenkins
    - กด “Build Now” ครั้งแรกเพื่อดูว่า Jenkinsfile ทำงานครบทุก stage หรือไม่.
    - แก้ไข permission, path, และคำสั่งต่างๆ ถ้ามี error จาก PHP/Composer หรือ SSH.[^12][^13][^5]

***

## GitHub Actions: CI สำหรับ PHP

1. สร้าง Workflow file
    - ในโปรเจกต์ สร้างโฟลเดอร์ `.github/workflows` แล้วสร้างไฟล์ เช่น `ci-php.yml`.
    - ไฟล์นี้คือที่กำหนดว่าเวลา push/pull request ให้รันอะไรบ้าง.[^4][^6]
2. โครงพื้นฐานของ workflow (PHP)

```yaml
name: PHP CI

on:
  push:
    branches: [ "main", "develop" ]
  pull_request:
    branches: [ "main", "develop" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.2'
          extensions: mbstring, pdo_mysql
          coverage: none

      - name: Install dependencies
        run: composer install --no-interaction --prefer-dist

      - name: Run tests
        run: ./vendor/bin/phpunit
```

    - ใช้ action `shivammathur/setup-php` เพื่อเตรียม PHP + extensions ได้สะดวก.
    - ปรับ `php-version` และ extensions ให้ตรงตามโปรเจกต์ เช่น Laravel ต้องใช้ pdo_mysql, mbstring ฯลฯ.[^14][^15][^4]
3. เพิ่มขั้นตอนคุณภาพโค้ด (ถ้าต้องการ)
    - เพิ่ม step สำหรับ `phpcs` (PHP_CodeSniffer) หรือ `phpstan` เพื่อตรวจ style/ static analysis.
    - ตัวอย่าง: ติดตั้งด้วย Composer แล้วรัน `vendor/bin/phpcs` หรือ `vendor/bin/phpstan analyse` ใน workflow.[^1][^3]
4. เพิ่มขั้นตอน Deploy ด้วย GitHub Actions (เลือกได้)
    - ถ้าต้องการให้ deploy อัตโนมัติหลังจากเทสผ่าน ให้เพิ่ม job หรือ step ใหม่ที่รันเฉพาะบน branch main.
    - ใช้ action จาก Marketplace เช่น action ที่ deploy ผ่าน SSH หรือ rsync แล้วใช้ secret (เช่น `SSH_HOST`, `SSH_USER`, `SSH_KEY`) จาก GitHub Secrets.[^16][^6][^17]

***

## การออกแบบ Flow: Jenkins + GitHub Actions ใช้ร่วมกันยังไง

1. ใช้ Jenkins เป็น CI/CD หลัก + GitHub Actions เป็น pre-check
    - ใช้ GitHub Actions รัน test แบบเร็วๆ ทุก push/PR เพื่อ feedback ไว (unit test, lint).
    - ให้ Jenkins เน้นงาน build ใหญ่, integration test, และ deploy ไปยังหลาย environment (staging/production).[^2][^9][^14]
2. หรือใช้ GitHub Actions ทั้ง CI+CD แล้วให้ Jenkins ทำงานเฉพาะใน infra ภายใน
    - กรณีที่ใช้ Jenkins ภายในองค์กรสำหรับงานที่ต้องเข้าถึงระบบภายใน (เช่น database ภายใน, private network).
    - GitHub Actions อาจ trigger Jenkins ผ่าน webhook หรือ API เพื่อต่อ pipeline ระหว่างกัน.[^7][^8][^11]

ถ้าต้องการ ตัวอย่างจริงสำหรับ Laravel หรือโครงสร้างไฟล์ทั้ง repo (เช่น ตัวอย่าง `composer.json`, `phpunit.xml`, และ workflow ที่รองรับ MySQL) ระบุ framework (Laravel/CodeIgniter/Plain PHP) และวิธี deploy (เช่น Apache, Nginx, Docker, Kubernetes) แล้วจะช่วยออกแบบ Pipeline และไฟล์ทุกอย่างให้ครบเป็นโครงโปรเจกต์ได้.


<span style="display:none">[^18][^19][^20]</span>

 

[^1]: https://www.jenkins.io/solutions/php/

[^2]: https://www.geeksforgeeks.org/devops/setting-up-a-ci-cd-pipeline-for-php-applications-using-jenkins/

[^3]: https://dev.to/kennibravo/pipeline-101-for-php-and-laravel-projects-with-jenkins-and-docker-47gh

[^4]: https://ma.ttias.be/a-github-ci-workflow-tailored-to-laravel-applications/

[^5]: https://www.somkiat.cc/php-continuous-integration-with-jenkins/

[^6]: https://docs.github.com/actions/using-workflows/workflow-syntax-for-github-actions

[^7]: https://plugins.jenkins.io/github/

[^8]: https://www.blazemeter.com/blog/how-to-integrate-your-github-repository-to-your-jenkins-project

[^9]: https://www.jenkins.io/doc/pipeline/tour/hello-world/

[^10]: https://kodekloud.com/community/t/jenkins-github-webhook-integration/292526

[^11]: https://dzone.com/articles/adding-a-github-webhook-in-your-jenkins-pipeline

[^12]: https://www.youtube.com/watch?v=5GtH-nDEEK8

[^13]: https://www.youtube.com/watch?v=64VkDEiLCY8

[^14]: https://dev.to/rafi021/step-by-step-guide-laravel-cicd-with-github-actions-i89

[^15]: https://www.ducxinh.com/en/techblog/automating-laravel-cicd-with-github-actions

[^16]: https://github.com/marketplace/actions/deploy-php-actions

[^17]: https://stackoverflow.com/questions/62447902/env-files-in-github-actions-ci-cd-workflows-how-to-provide-these-into-the-work

[^18]: https://dev.to/ehtesham_ali_abc367f36a5b/jenkins-with-php-run-your-first-pipeline-16e7

[^19]: https://gist.github.com/471ae1a98267e20530d989f64f5290ee

[^20]: https://www.youtube.com/watch?v=KxAElkZ1Hs4

---------------



# Comprehensive DevOps Guide: Jenkins & GitHub Actions with PHP

## **Version 1: English**

### **PART 1: TASK LIST CRITERIA GUIDE**

#### **1.1 Project Setup Tasks**

- [ ]  Initialize Git repository with proper .gitignore
- [ ]  Set up PHP project structure (src/, tests/, config/)
- [ ]  Configure composer.json with dependencies
- [ ]  Create Dockerfile for PHP environment
- [ ]  Set up docker-compose.yml for local development
- [ ]  Configure PHPUnit for testing
- [ ]  Set up environment configuration files
- [ ]  Create initial directory structure documentation


#### **1.2 CI/CD Pipeline Tasks**

- [ ]  Configure Jenkinsfile or GitHub Actions workflow
- [ ]  Set up build stage (dependencies, linting)
- [ ]  Configure test stage (unit, integration tests)
- [ ]  Set up security scanning stage
- [ ]  Configure artifact generation/packaging
- [ ]  Set up deployment stages (dev/staging/prod)
- [ ]  Configure notifications (Slack/Email)
- [ ]  Set up monitoring and logging


#### **1.3 Quality Assurance Tasks**

- [ ]  Configure PHP CodeSniffer (PSR standards)
- [ ]  Set up PHPStan/PHP Insights for static analysis
- [ ]  Configure SonarQube/SonarCloud integration
- [ ]  Set up test coverage reporting
- [ ]  Configure automated browser testing
- [ ]  Set up performance testing


### **PART 2: CHECKLIST CRITERIA GUIDE**

#### **2.1 Pre-commit Checklist**
 
- [ ] Code follows PSR-12 coding standards
- [ ] No syntax errors (php -l check)
- [ ] All existing tests pass locally
- [ ] New functionality has corresponding tests
- [ ] Documentation updated if needed
- [ ] No sensitive data committed (API keys, passwords)
- [ ] Commit messages follow conventional commits


#### **2.2 Pull Request Checklist**
 
- [ ] PR description clearly explains changes
- [ ] Link to related issue/ticket included
- [ ] All CI/CD pipeline checks passing
- [ ] Code coverage maintained or improved
- [ ] Security scan results reviewed
- [ ] Performance impact assessed
- [ ] Database migrations tested (if applicable)
- [ ] Backward compatibility maintained
 

#### **2.3 Deployment Checklist**
 
- [ ] All tests pass in CI environment
- [ ] Security vulnerabilities addressed
- [ ] Performance metrics within acceptable range
- [ ] Database backups completed (if applicable)
- [ ] Deployment plan documented
- [ ] Rollback plan documented and tested
- [ ] Stakeholders notified
- [ ] Monitoring alerts configured for deployment
 

### **PART 3: CODE REVIEW CRITERIA GUIDE**

#### **3.1 Code Quality Standards**
```yaml
Architecture:
  - Follows SOLID principles
  - Proper separation of concerns
  - No God objects or anti-patterns

Code Style:
  - PSR-12 compliance
  - Consistent naming conventions
  - Appropriate comments (not excessive)

Performance:
  - Efficient algorithms
  - Proper database query optimization
  - Memory usage considerations

Security:
  - Input validation and sanitization
  - SQL injection prevention
  - XSS protection
  - Authentication/authorization checks

Testing:
  - Adequate test coverage (>80%)
  - Meaningful test cases
  - Edge cases considered
```

#### **3.2 Review Process**
```
1. Initial Review (15 minutes max per PR)
   - Quick architecture assessment
   - Check for obvious issues
   - Determine if deeper review needed

2. Detailed Review
   - Line-by-line code examination
   - Test case review
   - Security vulnerability check

3. Automated Checks Verification
   - CI pipeline results
   - Static analysis reports
   - Test coverage reports
```

#### **3.3 Sample Jenkins Pipeline for PHP**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Setup') {
            steps {
                sh 'composer install --no-progress --prefer-dist'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'vendor/bin/phpcs --standard=PSR12 src/'
                sh 'vendor/bin/phplint src/'
            }
        }
        
        stage('Test') {
            steps {
                sh 'vendor/bin/phpunit --coverage-clover=coverage.xml'
            }
        }
        
        stage('Security Scan') {
            steps {
                sh 'vendor/bin/security-checker security:check'
            }
        }
        
        stage('Build') {
            steps {
                sh 'composer dump-autoload --optimize'
            }
        }
        
        stage('Deploy to Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh './deploy.sh staging'
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "Pipeline Successful: ${env.JOB_NAME}",
                body: "Build ${env.BUILD_NUMBER} completed successfully",
                to: 'team@example.com'
            )
        }
        failure {
            slackSend (
                channel: '#alerts',
                message: "Pipeline Failed: ${env.JOB_NAME} - Build ${env.BUILD_NUMBER}"
            )
        }
    }
}
```

#### **3.4 Sample GitHub Actions Workflow**
```yaml
name: PHP CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ROOT_PASSWORD: root
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        extensions: mbstring, xml, ctype, iconv, intl, pdo_mysql
        coverage: xdebug
    
    - name: Validate composer.json
      run: composer validate
    
    - name: Install dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: Run PHP CodeSniffer
      run: vendor/bin/phpcs --standard=PSR12 src/
    
    - name: Run PHPStan
      run: vendor/bin/phpstan analyse src/
    
    - name: Run tests
      run: vendor/bin/phpunit --coverage-clover=coverage.xml
      env:
        DB_HOST: 127.0.0.1
        DB_PORT: 3306
        DB_DATABASE: testdb
        DB_USERNAME: root
        DB_PASSWORD: root
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v1
      with:
        file: ./coverage.xml
```

---

## **Version 2: ภาษาไทย**

### **ส่วนที่ 1: คู่มือเกณฑ์การทำ Task List**

#### **1.1 งานตั้งค่าโปรเจค**

- [ ]  เริ่มต้น Git repository พร้อม .gitignore ที่เหมาะสม
- [ ]  ตั้งค่าโครงสร้างโปรเจค PHP (src/, tests/, config/)
- [ ]  กำหนดค่า composer.json พร้อม dependencies
- [ ]  สร้าง Dockerfile สำหรับสภาพแวดล้อม PHP
- [ ]  ตั้งค่า docker-compose.yml สำหรับพัฒนาท้องถิ่น
- [ ]  กำหนดค่า PHPUnit สำหรับการทดสอบ
- [ ]  ตั้งค่าไฟล์คอนฟิก environment
- [ ]  สร้างเอกสารโครงสร้างไดเรกทอรีเริ่มต้น


#### **1.2 งาน Pipeline CI/CD**

- [ ]  กำหนดค่า Jenkinsfile หรือ GitHub Actions workflow
- [ ]  ตั้งค่า stage build (dependencies, linting)
- [ ]  กำหนดค่า stage test (unit, integration tests)
- [ ]  ตั้งค่า stage security scanning
- [ ]  กำหนดการสร้าง/แพ็กเกจ artifact
- [ ]  ตั้งค่า stage deployment (dev/staging/prod)
- [ ]  กำหนดค่าการแจ้งเตือน (Slack/Email)
- [ ]  ตั้งค่าการตรวจสอบและบันทึก日志


#### **1.3 งานตรวจสอบคุณภาพ**
```
- [ ]  กำหนดค่า PHP CodeSniffer (มาตรฐาน PSR)
- [ ]  ตั้งค่า PHPStan/PHP Insights สำหรับ static analysis
- [ ]  กำหนดการรวม SonarQube/SonarCloud
- [ ]  ตั้งค่าการรายงาน test coverage
- [ ]  กำหนดค่า automated browser testing
- [ ]  ตั้งค่าการทดสอบประสิทธิภาพ
```

### **ส่วนที่ 2: คู่มือเกณฑ์การทำ Check List**

#### **2.1 Check List ก่อน Commit**
 
- [ ] รหัสตามมาตรฐาน PSR-12
- [ ] ไม่มีข้อผิดพลาดทางไวยากรณ์ (ตรวจสอบด้วย php -l)
- [ ] ทดสอบทั้งหมดผ่านในเครื่องท้องถิ่น
- [ ] ฟังก์ชันการทำงานใหม่มีการทดสอบที่สอดคล้องกัน
- [ ] อัพเดตเอกสารหากจำเป็น
- [ ] ไม่มีข้อมูลสำคัญถูก commit (คีย์ API, รหัสผ่าน)
- [ ] ข้อความ commit ตามแบบ conventional commits
 

#### **2.2 Check List Pull Request**
 
- [ ] คำอธิบาย PR อธิบายการเปลี่ยนแปลงอย่างชัดเจน
- [ ] รวมลิงก์ไปยังปัญหา/ตั๋วที่เกี่ยวข้อง
- [ ] การตรวจสอบ pipeline CI/CD ทั้งหมดผ่าน
- [ ] รักษาหรือปรับปรุง code coverage
- [ ] ตรวจสอบผลการสแกนความปลอดภัย
- [ ] ประเมินผลกระทบต่อประสิทธิภาพ
- [ ] ทดสอบการย้ายฐานข้อมูล (ถ้ามี)
- [ ] รักษาความเข้ากันได้ย้อนหลัง
 

#### **2.3 Check List การ Deploy**
 
- [ ] ทดสอบทั้งหมดผ่านในสภาพแวดล้อม CI
- [ ] แก้ไขช่องโหว่ความปลอดภัย
- [ ] ตัวชี้วัดประสิทธิภาพอยู่ในช่วงที่ยอมรับได้
- [ ] สำรองข้อมูลฐานข้อมูลเสร็จสิ้น (ถ้ามี)
- [ ] บันทึกแผนการ deploy
- [ ] บันทึกและทดสอบแผน rollback
- [ ] แจ้งผู้มีส่วนได้ส่วนเสีย
- [ ] กำหนดค่าการแจ้งเตือนสำหรับการ deploy
 

### **ส่วนที่ 3: คู่มือเกณฑ์การทำ Code Review**

#### **3.1 มาตรฐานคุณภาพโค้ด**
```yaml
สถาปัตยกรรม:
  - ปฏิบัติตามหลักการ SOLID
  - การแยกส่วนรับผิดชอบอย่างเหมาะสม
  - ไม่มี God objects หรือ anti-patterns

สไตล์โค้ด:
  - ปฏิบัติตาม PSR-12
  - ข้อตกลงการตั้งชื่อที่สม่ำเสมอ
  - ความคิดเห็นที่เหมาะสม (ไม่มากเกินไป)

ประสิทธิภาพ:
  - อัลกอริทึมที่มีประสิทธิภาพ
  - การเพิ่มประสิทธิภาพคิวรีฐานข้อมูลที่เหมาะสม
  - การพิจารณาการใช้หน่วยความจำ

ความปลอดภัย:
  - การตรวจสอบและการทำให้ปลอดภัยของอินพุต
  - การป้องกัน SQL injection
  - การป้องกัน XSS
  - การตรวจสอบการรับรองความถูกต้อง/การอนุญาต

การทดสอบ:
  - การครอบคลุมการทดสอบที่เพียงพอ (>80%)
  - กรณีทดสอบที่มีความหมาย
  - พิจารณากรณีขอบเขต
```

#### **3.2 กระบวนการตรวจสอบ**
```
1. การตรวจสอบเบื้องต้น (สูงสุด 15 นาทีต่อ PR)
   - การประเมินสถาปัตยกรรมอย่างรวดเร็ว
   - ตรวจหาปัญหาที่เห็นได้ชัด
   - ตัดสินใจว่าต้องการการตรวจสอบลึกหรือไม่

2. การตรวจสอบรายละเอียด
   - การตรวจสอบโค้ดทีละบรรทัด
   - การตรวจสอบกรณีทดสอบ
   - การตรวจสอบช่องโหว่ความปลอดภัย

3. การยืนยันการตรวจสอบอัตโนมัติ
   - ผลลัพธ์ pipeline CI
   - รายงาน static analysis
   - รายงาน test coverage
```

#### **3.3 Jenkins Pipeline สำหรับ PHP (ตัวอย่าง)**
```groovy
pipeline {
    agent any
    
    stages {
        stage('ตั้งค่า') {
            steps {
                sh 'composer install --no-progress --prefer-dist'
            }
        }
        
        stage('ตรวจสอบโค้ด') {
            steps {
                sh 'vendor/bin/phpcs --standard=PSR12 src/'
                sh 'vendor/bin/phplint src/'
            }
        }
        
        stage('ทดสอบ') {
            steps {
                sh 'vendor/bin/phpunit --coverage-clover=coverage.xml'
            }
        }
        
        stage('สแกนความปลอดภัย') {
            steps {
                sh 'vendor/bin/security-checker security:check'
            }
        }
        
        stage('สร้าง') {
            steps {
                sh 'composer dump-autoload --optimize'
            }
        }
        
        stage('ปรับใช้สู่ Staging') {
            when {
                branch 'develop'
            }
            steps {
                sh './deploy.sh staging'
            }
        }
    }
    
    post {
        success {
            emailext (
                subject: "Pipeline สำเร็จ: ${env.JOB_NAME}",
                body: "การสร้าง ${env.BUILD_NUMBER} เสร็จสมบูรณ์",
                to: 'team@example.com'
            )
        }
        failure {
            slackSend (
                channel: '#alerts',
                message: "Pipeline ล้มเหลว: ${env.JOB_NAME} - การสร้าง ${env.BUILD_NUMBER}"
            )
        }
    }
}
```

#### **3.4 GitHub Actions Workflow (ตัวอย่าง)**
```yaml
name: PHP CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mysql:
        image: mysql:5.7
        env:
          MYSQL_ROOT_PASSWORD: root
        ports:
          - 3306:3306
        options: --health-cmd="mysqladmin ping" --health-interval=10s --health-timeout=5s --health-retries=3
    
    steps:
    - uses: actions/checkout@v2
    
    - name: ตั้งค่า PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'
        extensions: mbstring, xml, ctype, iconv, intl, pdo_mysql
        coverage: xdebug
    
    - name: ตรวจสอบ composer.json
      run: composer validate
    
    - name: ติดตั้ง dependencies
      run: composer install --prefer-dist --no-progress
    
    - name: เรียกใช้ PHP CodeSniffer
      run: vendor/bin/phpcs --standard=PSR12 src/
    
    - name: เรียกใช้ PHPStan
      run: vendor/bin/phpstan analyse src/
    
    - name: เรียกใช้การทดสอบ
      run: vendor/bin/phpunit --coverage-clover=coverage.xml
      env:
        DB_HOST: 127.0.0.1
        DB_PORT: 3306
        DB_DATABASE: testdb
        DB_USERNAME: root
        DB_PASSWORD: root
    
    - name: อัพโหลด coverage ไปยัง Codecov
      uses: codecov/codecov-action@v1
      with:
        file: ./coverage.xml
```

### **คำแนะนำเพิ่มเติมสำหรับทีมไทย:**
1. **การสื่อสาร**: กำหนดช่องทางการสื่อสารที่ชัดเจนสำหรับทีม DevOps
2. **การบันทึก**: บันทึกการเปลี่ยนแปลงทั้งหมดเป็นภาษาไทยและอังกฤษ
3. **การฝึกอบรม**: จัดฝึกอบรม Jenkins/GitHub Actions พื้นฐาน
4. **การสำรองข้อมูล**: มีแผนสำรองข้อมูลสำหรับการตั้งค่า Jenkins
5. **การตรวจสอบ**: ทบทวนกระบวนการทุกไตรมาสเพื่อปรับปรุง

คู่มือนี้สามารถปรับปรุงตามความต้องการเฉพาะของทีมและองค์กรได้
