
# วิธีตั้งค่า CI/CD ให้รัน Playwright หรือ Cypress อัตโนมัติ

สำหรับการตั้งค่า CI/CD เพื่อรัน Automated Testing บน **GitHub Actions** (ซึ่งเป็นมาตรฐานยอดนิยมสำหรับ Next.js/React Stack ของคุณ) มีขั้นตอนที่ชัดเจนครับ

ผมได้เตรียมสคริปต์สำหรับทั้ง **Playwright** (ที่แนะนำ) และ **Cypress** โดยเน้นการปรับจูนให้เหมาะกับ Next.js ที่สุดครับ

***

### 1. การตั้งค่า Playwright (แนะนำ)

Playwright เหมาะกับ Next.js มากเพราะมีฟีเจอร์ `webServer` ในตัว ช่วยจัดการการเปิด-ปิด Server ให้อัตโนมัติ ไม่ต้องเขียนสคริปต์รอพอร์ตเองครับ

#### ขั้นตอนที่ 1: ตั้งค่า `playwright.config.ts`

ก่อนไปที่ CI คุณต้องบอกให้ Playwright รู้ว่าจะเปิดแอปยังไง (ควรใช้ Production Build เพื่อความเสถียร)

```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  // ... config อื่นๆ
  webServer: {
    command: 'npm run build && npm run start', // Build และ Start App
    url: 'http://localhost:3000',             // รอจนกว่า URL นี้จะเข้าได้
    reuseExistingServer: !process.env.CI,     // ถ้าเป็นเครื่อง local ไม่ต้องเปิดใหม่
    timeout: 120 * 1000,                      // รอได้สูงสุด 2 นาที
  },
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',                  // เก็บ Trace เมื่อเทสต์พัง (ช่วย Debug ใน CI)
  },
});
```


#### ขั้นตอนที่ 2: สร้างไฟล์ Workflow

สร้างไฟล์ `.github/workflows/playwright.yml` ในโปรเจกต์ของคุณ:

```yaml
name: Playwright Tests
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        cache: 'npm' # ช่วย cache node_modules ให้อัตโนมัติ

    - name: Install dependencies
      run: npm ci

    - name: Install Playwright Browsers
      run: npx playwright install --with-deps # ติดตั้ง Browser และ Dependencies ของระบบ

    - name: Run Playwright tests
      run: npx playwright test
      env:
        CI: true
        # ใส่ Environment Variables ที่จำเป็น เช่น Database URL (ถ้าใช้ Docker Service)
        # DATABASE_URL: ${{ secrets.DATABASE_URL }} 

    - name: Upload Playwright Report
      uses: actions/upload-artifact@v4
      if: always() # อัปโหลดเสมอแม้เทสต์จะพัง
      with:
        name: playwright-report
        path: playwright-report/
        retention-days: 30
```


***

### 2. การตั้งค่า Cypress

Cypress มี Official Action ที่ช่วยลดความยุ่งยาก แต่คุณต้องจัดการเรื่องการรอ Server ให้ดี (Wait-on)

สร้างไฟล์ `.github/workflows/cypress.yml`:

```yaml
name: Cypress Tests
on: [push]

jobs:
  cypress-run:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Cypress Run
        uses: cypress-io/github-action@v6
        with:
          build: npm run build    # คำสั่ง Build Next.js
          start: npm run start    # คำสั่ง Start Server
          wait-on: 'http://localhost:3000' # รอจนกว่า URL นี้จะตอบสนอง 200 OK
          wait-on-timeout: 120
          browser: chrome
        env:
          # แนะนำให้ตั้งค่านี้ใน GitHub Secrets สำหรับ Record ลง Dashboard (ถ้ามี)
          # CYPRESS_RECORD_KEY: ${{ secrets.CYPRESS_RECORD_KEY }}
          # GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


***

### 3. คำแนะนำเพิ่มเติมสำหรับปี 2025 (Pro Tips)

1. **จัดการ Database (PostgreSQL):**
    * ใน CI คุณไม่มี Database จริงรออยู่ แนะนำให้ใช้ **Docker Service** ใน GitHub Actions เพื่อจำลอง PostgreSQL ขึ้นมาเฉพาะตอนเทสต์
    * หรือใช้ `dotenv` โหลด `.env.test` เพื่อชี้ไปที่ Database สำหรับเทสต์โดยเฉพาะ
2. **Sharding (สำหรับโปรเจกต์ใหญ่):**
    * ถ้า Test Case เริ่มเยอะ (รันนานกว่า 10 นาที) Playwright รองรับการทำ **Sharding** (แบ่งเทสต์เป็นส่วนๆ รันพร้อมกันหลายเครื่อง) ได้ฟรีบน GitHub Actions ช่วยลดเวลารอได้มหาศาล[^1]
3. **Artifacts \& Traces:**
    * ในไฟล์ Playwright ผมตั้ง `if: always()` ไว้ที่ขั้นตอน Upload Artifact นี่สำคัญมาก เพราะถ้าเทสต์พัง คุณจะสามารถดาวน์โหลดไฟล์ Zip มาเปิดดู **Trace Viewer** ได้ว่าหน้าจอเป็นยังไงตอน error ซึ่งแก้บั๊กได้ไวกว่าดูแค่ Log มากครับ[^2]

**สรุป:** เริ่มต้นด้วย **Playwright Config** ด้านบน แล้ววางไฟล์ YAML ลงไป GitHub จะรันเทสต์ให้คุณทันทีที่มีการ Push โค้ดใหม่ครับ
<span style="display:none">[^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^20][^3][^4][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: https://testkube.io/resources/scaling-playwright-in-ci-cd-pipelines-playwright-user-event

[^2]: https://sergeipetrukhin.vercel.app/github-actions

[^3]: https://github.com/clerk/clerk-playwright-nextjs

[^4]: https://playwright.dev/docs/ci-intro

[^5]: https://github.com/TomDiekmann/nextjs-playwright-testing

[^6]: https://nextjs.org/docs/pages/guides/testing/playwright

[^7]: https://blog.prototyp.digital/cypress-ci-workflow-guide/

[^8]: https://playwright.dev/docs/test-webserver

[^9]: https://autify.com/blog/playwright-github-actions

[^10]: https://www.youtube.com/watch?v=Dq5Prsxrd-k

[^11]: https://www.tothenew.com/blog/playwright-with-ci-cd-harnessing-headless-browsers-xvfb-for-seamless-automation-in-node-js/

[^12]: https://www.thisdot.co/blog/integrating-playwright-tests-into-your-github-workflow-with-vercel

[^13]: https://learn.cypress.io/tutorials/running-our-tests-with-github-actions

[^14]: https://blog.nashtechglobal.com/playwright-into-ci-cd-workflows/

[^15]: https://hackernoon.com/using-nextjs-playwright-github-actions-to-make-a-to-do-app

[^16]: https://docs.cypress.io/app/continuous-integration/github-actions

[^17]: https://playwright.dev/docs/ci

[^18]: https://github.com/cypress-io/github-action

[^19]: https://dev.to/aswani25/integrating-playwright-with-cicd-pipelines-1g1m

[^20]: https://jurajsim.hashnode.dev/running-cypress-in-parallel-using-github-actions

