# UMW2 Project State

อัปเดตล่าสุด: 8 กันยายน 2569

ไฟล์นี้เป็นจุดส่งต่องานข้ามเครื่อง ข้าม session และข้าม AI ต้องปรับเมื่อสถานะหรือข้อตกลงสำคัญเปลี่ยน

## Current phase

`Discovery and domain design`

กำลังทำความเข้าใจระบบเดิมและกำหนดโครงสร้างระบบใหม่ ยังไม่มี application code, database schema หรือ technology stack ที่ได้รับอนุมัติ

## Product direction

- เป้าหมายคือสร้างทั้งแอป Utilities Mobile Workflow รุ่นใหม่
- พัฒนาเป็นโมดูลใหญ่ทีละส่วน
- เริ่มจาก Jar Test
- สร้าง legacy-compatible baseline ก่อน แล้วจึงออกแบบการอัปเกรดแยกเป็นลำดับต่อไป
- มี Global Master ที่หลายโมดูลใช้ร่วมกัน
- เตรียมรองรับหลาย Organization ตั้งแต่โครงสร้างพื้นฐาน แม้ระยะแรกใช้ภายในองค์กรเดียว

## Completed

- สำรวจ flow หลักของ Jar Test ในระบบเดิม
- ทดลองสร้างและปิดงาน Jar Test ด้วยข้อมูลทดสอบที่ `สถานีผลิต Head Office`
- บันทึกหน้ารายการ ฟอร์ม modal แท็บ สารเคมี รอบทดสอบ 6 Jar ผลคุณภาพ สรุปผล และ responsive behavior
- ยืนยันสูตรปริมาณสารละลายเชิงพฤติกรรมตาม `C1V1 = C2V2`
- ยืนยันว่าผ่าน/ไม่ผ่านคำนวณจาก Bound
- จัดทำ legacy product and functional specification
- ยืนยันโครงสร้าง Organization → Business Unit → Site
- ยืนยันว่า Business Unit และ Site เป็นคนละระดับ
- ยืนยันว่า Water Source เป็น Global Master และเชื่อมกับ Site แบบหลายต่อหลาย

## Accepted decisions

1. ระบบใหม่รองรับหลาย Organization ในเชิงโครงสร้าง
2. `กิจการประปา` ใช้ชื่อ canonical ว่า Business Unit
3. `สถานี` ใช้ชื่อ canonical ว่า Site และอยู่ภายใต้ Business Unit
4. ตัดคำว่า `ผลิต` ออกจากชื่อ canonical ของ Site
5. Water Source เป็น Global Master
6. Site หนึ่งใช้หลาย Water Source ได้ และ Water Source หนึ่งใช้ร่วมกันหลาย Site ได้
7. Jar Test ต้องเลือก Water Source ที่ได้รับการผูกไว้กับ Site ที่เลือก
8. Legacy behavior กับ upgrade requirements ต้องอยู่คนละเอกสารและห้ามปะปนกัน

รายละเอียดเหตุผลอยู่ใน `docs/decisions/ADR-0001-organization-business-unit-site-water-source.md`

## Canonical baseline

พฤติกรรม Jar Test ระบบเดิมอยู่ใน:

`JarTest/legacy-jar-test-product-functional-spec-th.md`

เอกสารนี้เป็น AS-IS baseline เท่านั้น ไม่ใช่ database schema และไม่ใช่ข้อสรุป UX ของระบบใหม่

## Open product and architecture decisions

เรียงตามลำดับที่ควรตัดสิน:

1. Global Master จะเป็น global ข้ามทุก Organization หรือ tenant-scoped ภายใน Organization
2. Water Source เดียวสามารถใช้ข้ามหลาย Business Unit ได้หรือจำกัดภายใน Business Unit เดียว
3. รายการ Global Master ขั้นต่ำที่ Jar Test ต้องใช้ร่วมกับโมดูลในอนาคต
4. ขอบเขต role และ permission ของ Organization, Business Unit และ Site
5. Target workflow ของ Jar Test รุ่นแรก: จำลอง legacy ทุกจุดหรืออนุญาตแก้ UX บางส่วน
6. ค่าและโครงสร้าง Bound รวมถึงช่วงเวลาที่มีผล
7. สูตรแนะนำ Pre-chlorine และด่างทับทิมที่ระบบเดิมใช้
8. Technology stack และ deployment target
9. Physical database schema, audit/history และ migration strategy

รายละเอียดช่องว่างของระบบเดิมดูหัวข้อ 22 ใน legacy specification

## Immediate next step

ตัดสินขอบเขตความเป็นเจ้าของ Global Master ในระบบ multi-organization จากนั้นออกแบบ conceptual master-data model สำหรับ Jar Test โดยยังไม่ลง physical database schema จนกว่าข้อตกลงหลักจะครบ

## Handoff instructions

เมื่อเริ่มต่อจากเครื่องหรือ AI ตัวใหม่:

1. Clone หรือ pull repository ล่าสุด
2. เปิดโฟลเดอร์ราก `UMW2`
3. อ่าน `AGENTS.md` และไฟล์ตาม Required reading order
4. ตรวจว่า working tree มีไฟล์ที่ยังไม่ commit หรือไม่
5. สรุป Accepted decisions และ Open decisions ก่อนเสนอการเปลี่ยนแปลง
6. ทำต่อจาก `Immediate next step` โดยไม่ย้อนตัดสินเรื่องที่ Accepted แล้ว เว้นแต่เจ้าของโครงการต้องการเปลี่ยน

Prompt เริ่มงานที่ใช้ได้:

> อ่าน AGENTS.md, CONTEXT.md และ PROJECT-STATE.md ให้ครบ แล้วอ่านเอกสารที่ลิงก์ตามลำดับ สรุปสถานะปัจจุบัน ข้อกำหนดที่ห้ามเปลี่ยน และคำถามถัดไปหนึ่งข้อก่อนทำงานต่อ ห้ามเดาคำตอบของ Open decisions

## Repository status

- Project root: โฟลเดอร์ที่ clone repository นี้ (เครื่องเริ่มต้นใช้ `E:\Project\UMW2`)
- Git repository: initialize แล้ว ใช้ branch `main`
- Remote GitHub repository: https://github.com/BKittiphm/UMW2
- ตรวจสถานะการส่งข้อมูลล่าสุดด้วย `git status` และ `git ls-remote origin refs/heads/main`
- Application code: ยังไม่มี
