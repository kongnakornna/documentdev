### คู่มือเกณฑ์การทำ Code Review
#### Bug Fix Review Template

### Bug Description
- **Bug:** [Description]
- **Impact:** [What was affected]
- **Reproduction steps:** [How to reproduce]

### Fix Analysis
- [ ] Root cause correctly identified
- [ ] Fix addresses the root cause, not symptoms
- [ ] Fix doesn't introduce new bugs
- [ ] All related edge cases considered

### Testing Focus Areas
- [ ] Bug is fixed in all scenarios
- [ ] Regression tests added
- [ ] Similar patterns checked in codebase

---------------

###### แปลภาษาไทย ใช้โครงสร้างเดิม

---------------
### คู่มือเกณฑ์การทำ Code Review

## การแก้ไขบั๊ก
## เทมเพลตการตรวจสอบการแก้ไขบั๊ก

### คำอธิบายบั๊ก
- **บั๊ก:** [คำอธิบาย]
- **ผลกระทบ:** [สิ่งที่ได้รับผลกระทบ]
- **ขั้นตอนการเกิดซ้ำ:** [วิธีทำให้บั๊กเกิดขึ้นซ้ำ]

### การวิเคราะห์การแก้ไข
- [ ] ระบุสาเหตุรากเหง้าได้ถูกต้อง
- [ ] การแก้ไขจัดการกับสาเหตุรากเหง้า ไม่ใช่เพียงอาการ
- [ ] การแก้ไขไม่ได้นำบั๊กใหม่เข้ามา
- [ ] พิจารณากรณีขอบเขตที่เกี่ยวข้องทั้งหมดแล้ว

### พื้นที่โฟกัสสำหรับการทดสอบ
- [ ] แก้ไขบั๊กในทุกสถานการณ์แล้ว
- [ ] เพิ่มการทดสอบเพื่อป้องกันการถดถอย (Regression tests)
- [ ] ตรวจสอบรูปแบบที่คล้ายกันในโค้ดเบสแล้ว

---------------
