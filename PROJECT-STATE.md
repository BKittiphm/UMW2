# UMW2 Project State

อัปเดตล่าสุด: 9 กันยายน 2569

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

- จัดทำบันทึกการคุยพร้อมที่มา เหตุผล ข้อเสนอที่ยังไม่อนุมัติ และพื้นที่สำหรับประวัติแชต
- Repository เชื่อมกับ GitHub แล้ว; checkpoint การสร้างเอกสารเดิมเป็นหลักฐานย้อนหลัง ไม่ใช่สถานะ Git ปัจจุบัน

- สำรวจ flow หลักของ Jar Test ในระบบเดิม
- ทดลองสร้างและปิดงาน Jar Test ด้วยข้อมูลทดสอบที่ `สถานีผลิต Head Office`
- บันทึกหน้ารายการ ฟอร์ม modal แท็บ สารเคมี รอบทดสอบ 6 Jar ผลคุณภาพ สรุปผล และ responsive behavior
- ยืนยันสูตรปริมาณสารละลายเชิงพฤติกรรมตาม `C1V1 = C2V2`
- ยืนยันว่าผ่าน/ไม่ผ่านคำนวณจาก Bound
- จัดทำ legacy product and functional specification
- ยืนยันโครงสร้าง Organization → Business Unit → Site
- ยืนยันว่า Business Unit และ Site เป็นคนละระดับ
- ยืนยันว่า `wq_source` เป็นประเภทน้ำ ไม่ใช่รายการแหล่งน้ำจริง
- ยืนยันว่ารายการน้ำแยกเป็นตาราง `raw_unit`, `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
- ยืนยันว่าแต่ละรายการในตารางประเภทมี `wq_source_id` เชื่อมกลับตารางประเภท
- ยืนยันว่า Jar Test ใช้เฉพาะน้ำดิบและเลือก `water_source` จาก `raw_unit`

## Accepted decisions

1. ระบบใหม่รองรับหลาย Organization ในเชิงโครงสร้าง
2. `กิจการประปา` ใช้ชื่อ canonical ว่า Business Unit
3. `สถานี` ใช้ชื่อ canonical ว่า Site และอยู่ภายใต้ Business Unit
4. ตัดคำว่า `ผลิต` ออกจากชื่อ canonical ของ Site
5. `wq_source` เป็นตัวเลือกประเภทน้ำ เมื่อเลือกแล้วระบบแสดงรายการจากตารางของประเภทนั้น
6. แหล่งน้ำแต่ละประเภทเก็บในตารางแยกกันตามชื่อ
7. ตารางประเภทแต่ละตารางเชื่อมกลับ `wq_source` ด้วย `wq_source_id`
8. Jar Test ใช้เฉพาะน้ำดิบและเชื่อม `water_source` ไปยังรายการใน `raw_unit`
9. Legacy behavior กับ upgrade requirements ต้องอยู่คนละเอกสารและห้ามปะปนกัน

ความเข้าใจเดิมที่ให้ Water Source จริงเชื่อม Site โดยตรงแบบ many-to-many ถูกแทนที่สำหรับ Jar Test แล้ว ความสัมพันธ์ที่รองรับการใช้ `raw_unit` รายการเดียวร่วมกันหลาย Site ยังต้องกำหนดเพิ่ม

รายละเอียดเหตุผลอยู่ใน `docs/decisions/ADR-0001-organization-business-unit-site-water-source.md`

## Canonical baseline

พฤติกรรม Jar Test ระบบเดิมอยู่ใน:

`JarTest/legacy-jar-test-product-functional-spec-th.md`

เอกสารนี้เป็น AS-IS baseline เท่านั้น ไม่ใช่ database schema และไม่ใช่ข้อสรุป UX ของระบบใหม่

## Open product and architecture decisions

เรียงตามลำดับที่ควรตัดสิน:

1. `raw_unit` รายการเดียวที่ใช้ร่วมกันหลาย Site จะเชื่อมผ่านตารางกลาง หรือใช้โครงสร้างอื่น
2. `wq_source` เป็นประเภทกลางร่วมกันหรือเป็นรายการประเภทแยกภายใต้แต่ละ Site
3. ชื่อ `potable_tranfer_unit` จะคงตามแบบข้อมูลหรือแก้เป็น `potable_transfer_unit`
4. Global Master จะเป็น global ข้ามทุก Organization หรือ tenant-scoped ภายใน Organization
5. รายการ Global Master ขั้นต่ำที่ Jar Test ต้องใช้ร่วมกับโมดูลในอนาคต
6. ขอบเขต role และ permission ของ Organization, Business Unit และ Site
7. Target workflow ของ Jar Test รุ่นแรก: จำลอง legacy ทุกจุดหรืออนุญาตแก้ UX บางส่วน
8. ค่าและโครงสร้าง Bound รวมถึงช่วงเวลาที่มีผล
9. สูตรแนะนำ Pre-chlorine และด่างทับทิมที่ระบบเดิมใช้
10. Technology stack และ deployment target
11. Physical database schema, audit/history และ migration strategy

รายละเอียดช่องว่างของระบบเดิมดูหัวข้อ 22 ใน legacy specification

## Immediate next step

ยืนยันวิธีที่ `raw_unit` รายการเดียวถูกใช้ร่วมกันหลาย Site และความเป็นเจ้าของของ `wq_source` จากนั้นจึงปรับ conceptual model ต่อ โดยยังไม่ลง physical database schema

## Handoff instructions

ความจำประกอบล่าสุด: [Project Memory](docs/memory/README.md), [บันทึกเริ่มต้น](docs/memory/sessions/2026-09-08-project-foundation.md) และ [คำชี้แจงโครงสร้างแหล่งน้ำ](docs/memory/sessions/2026-09-09-water-quality-source-structure.md) ประวัติแชตฉบับเต็มยังไม่ถูกนำเข้า

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
