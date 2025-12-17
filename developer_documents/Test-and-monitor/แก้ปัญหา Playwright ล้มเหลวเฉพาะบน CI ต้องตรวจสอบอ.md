 # แก้ปัญหา Playwright ล้มเหลวเฉพาะบน CI ต้องตรวจสอบอะไรบ้าง

ปัญหา "Works on my machine, fails on CI" เป็นเรื่องคลาสสิกมากสำหรับ Automated Testing ครับ โดยเฉพาะกับ Playwright ที่ทำงานเร็วมาก

หากเทสต์ผ่านในเครื่อง Local แต่พังบน CI (GitHub Actions) ให้ตรวจสอบ 5 จุดสำคัญนี้ตามลำดับครับ:

### 1. ปัญหา Headless Mode และ Timing (พบบ่อยที่สุด)

บนเครื่องเรา (Local) มักรันแบบ Headed (เห็นเบราว์เซอร์) หรือเครื่องแรงกว่า CI ทำให้การเรนเดอร์ต่างกัน

* **อาการ:** เทสต์พังเพราะหา Element ไม่เจอ (Timeout) หรือหน้าเว็บโหลดไม่ทัน
* **วิธีแก้:**
    * **เพิ่ม Timeout ใน Config:** ใน CI เครื่องมักจะช้ากว่า ลองเพิ่ม `timeout` ใน `playwright.config.ts` สำหรับ CI โดยเฉพาะ

```typescript
timeout: process.env.CI ? 60 * 1000 : 30 * 1000, // CI รอ 60วิ, Local รอ 30วิ
```

    * **อย่าใช้ Hard Wait:** ห้ามใช้ `page.waitForTimeout(5000)` เด็ดขาด ให้ใช้ Web-First Assertions แทน เช่น `await expect(locator).toBeVisible()` ซึ่งมันจะรอให้อัตโนมัติและฉลาดกว่า[^1]


### 2. เช็ค Trace Viewer (เครื่องมือช่วยชีวิต)

Playwright มีฟีเจอร์ "บันทึกกล่องดำ" ที่เรียกว่า Trace Viewer ซึ่งบอกได้หมดว่าเกิดอะไรขึ้น

* **วิธีใช้:**

1. ใน `playwright.config.ts` ตั้งค่า `trace: 'on-first-retry'` (หรือ `'retain-on-failure'`)
2. เมื่อเทสต์พังใน GitHub Actions ให้ไปโหลดไฟล์ **Artifacts** (ชื่อ `playwright-report` หรือ `trace.zip` ตามที่คุณตั้งใน workflow)
3. แตกไฟล์ แล้วไปที่เว็บ [trace.playwright.dev](https://trace.playwright.dev) แล้วลากไฟล์ `trace.zip` ลงไป
4. **สิ่งที่จะเห็น:** คุณจะเห็นหน้าจอวินาทีที่พัง, Network Request ที่อาจจะ Failed (เช่น API 500), และ Console Log ของ Browser ซึ่งช่วยให้รู้สาเหตุได้ทันที[^2][^3]


### 3. ปัญหา Environment Variables ไม่ครบ

* **อาการ:** ล็อกอินไม่ได้, เชื่อมต่อ Database ไม่ได้
* **ตรวจสอบ:** เช็คไฟล์ `.github/workflows` ว่าได้ใส่ `env:` ครบไหม โดยเฉพาะพวก `DATABASE_URL`, `NEXT_PUBLIC_API_URL`, หรือ `AUTH_SECRET`
* **Note:** Next.js (ตั้งแต่ v14) ต้องการ Environment Variables บางตัวตอน **Build Time** ด้วย ไม่ใช่แค่ตอน Run Time


### 4. ปัญหา Missing Dependencies (Linux)

* **อาการ:** Browser เปิดไม่ขึ้น หรือ error แปลกๆ เกี่ยวกับ Library
* **ตรวจสอบ:** ในไฟล์ Workflow บรรทัด `npx playwright install --with-deps` สำคัญมาก เพราะ Linux ใน CI ต้องการ Library ของระบบ (เช่น FFMPEG, GStreamer) ที่เครื่อง Mac/Windows เรามีอยู่แล้วแต่ Linux ไม่มี[^4]


### 5. ปัญหา Media/Animation

* **อาการ:** เทสต์กดปุ่มไม่ได้เพราะมี Animation บัง หรือฟอนต์เรนเดอร์ไม่เหมือนกัน
* **วิธีแก้:** Playwright ใน CI (Linux) เรนเดอร์ฟอนต์ต่างจาก Mac/Windows ทำให้ Screenshot Test (Visual Comparison) มักจะพัง
    * ถ้าทำ Visual Testing แนะนำให้รัน Docker ในเครื่อง Local เพื่อให้ Environment เหมือน CI 100% หรือใช้บริการอย่าง Percy/Chromatic แทนการทำเอง

**สรุป:**
ให้เริ่มจาก **ข้อ 2 (Trace Viewer)** ก่อนครับ เพราะมันจะบอกใบ้ได้ชัดเจนที่สุดว่าทำไมเทสต์ถึงพัง (เช่น หน้าขาวเพราะ JS Error, หรือเน็ตช้าจน Timeout) แล้วค่อยไปปรับแก้ตามข้ออื่นๆ ครับ
<span style="display:none">[^10][^11][^12][^13][^14][^15][^16][^17][^18][^19][^20][^5][^6][^7][^8][^9]</span>

<div align="center">⁂</div>

[^1]: https://github.com/microsoft/playwright/issues/13383

[^2]: https://www.alphabin.co/blog/how-to-use-playwright-trace-viewer

[^3]: https://www.qable.io/blog/how-to-use-playwright-trace-viewer-for-faster-debugging

[^4]: https://playwright.dev/docs/ci

[^5]: https://javascript.plainenglish.io/why-your-playwright-tests-fail-in-ci-but-pass-locally-and-how-to-fix-it-54fa19836737

[^6]: https://stackoverflow.com/questions/79247754/playwright-test-pass-locally-but-fails-on-gitlab-ci

[^7]: https://dev.to/playwright/how-to-reproduce-ci-failures-locally-in-playwright-lfl

[^8]: https://github.com/microsoft/playwright/issues/23297

[^9]: https://www.reddit.com/r/Playwright/comments/1ix1e82/successful_tests_locally_but_failed_in_the_ci/

[^10]: https://playwright.dev/docs/debug

[^11]: https://www.alphabin.co/blog/fix-flaky-playwright-tests

[^12]: https://autify.com/blog/playwright-timeout

[^13]: https://stackoverflow.com/questions/78106987/playwright-headless-waiting-issues

[^14]: https://semaphore.io/blog/flaky-tests-playwright

[^15]: https://playwright.dev/docs/trace-viewer-intro

[^16]: https://github.com/microsoft/playwright/issues/38276

[^17]: https://playwright.dev/docs/best-practices

[^18]: https://playwright.dev/docs/ci-intro

[^19]: https://playwright.dev/docs/release-notes

[^20]: https://raphaelferrand.com/posts/playwright-debugging-ci/

