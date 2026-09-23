# UMW2 Project State

อัปเดตล่าสุด: 23 กันยายน 2569

ไฟล์นี้เป็นจุดส่งต่องานข้ามเครื่อง ข้าม session และข้าม AI ต้องปรับเมื่อสถานะหรือข้อตกลงสำคัญเปลี่ยน

## Current phase

`Discovery and domain design`

กำลังทำความเข้าใจระบบเดิมและกำหนดโครงสร้างระบบใหม่ PostgreSQL ได้รับอนุมัติเป็นฐานข้อมูลหลักแล้ว แต่ยังไม่มี application code, physical database schema, backend framework หรือ deployment target ที่ได้รับอนุมัติ

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
- ยืนยันว่า `wq_source` เป็นตารางประเภทกลางคงที่ระดับระบบ
- ยืนยันว่า `raw_unit` เป็นรายการแหล่งน้ำดิบจริง ใช้ร่วมข้าม Organization และเชื่อมกับ Site แบบ many-to-many ผ่าน mapping
- อัปเดต Notion draft ของ `site_raw_units` ให้เป็น Site–Raw Unit mapping ที่ไม่มี `organization_id`, ใช้ `capacity_uom_id`, FK actions และ partial unique indexes สำหรับ `custom_name`
- ยืนยันว่า `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit` อยู่ภายใน Organization และต้อง mapping กับ Site ใน Organization เดียวกัน
- อัปเดต Notion DDL draft ให้ `potable_unit` และ `potable_transfer_unit` มี `organization_id`, `wq_source_id`, `updated_at` และ constraint ระดับ Organization ตาม Data Dictionary
- ยืนยันว่า `filtration_subunits.unit_no` เป็นหมายเลขของ Master กลางและ `UNIQUE` ระดับ global; ตาราง Site ใช้ mapping ระบุชุดถังกรองและช่องกรองที่ติดตั้ง
- เลือก PostgreSQL เป็นฐานข้อมูลหลักและ source of truth ของ UMW2 เพื่อรองรับ Jar Test, Global Master และโมดูลในอนาคต
- เจ้าของโครงการอนุมัติ Jar Test Setting ราย Site: พารามิเตอร์/หน่วยน้ำดิบ พารามิเตอร์ผลทดสอบ/Bound และสารเคมี/ราคา; ใช้กับ raw_unit ที่ mapping กับ Site และเก็บ snapshot ต่อการทดสอบ
- ยืนยันว่าเงื่อนไขการกวนและตกตะกอนเป็นข้อมูลของการทดลองแต่ละครั้ง ไม่รวมใน Site Setting และไม่ใช้คำนวณ dose หรือ pass/fail ตามข้อกำหนดปัจจุบัน
- ยืนยันแนวทางออกแบบฐานข้อมูลแบบค่อยเป็นค่อยไป: Excel ที่จะนำมาให้วิเคราะห์เป็น schema draft ตั้งต้น อาจยังไม่ครบทุกตาราง และสามารถเพิ่มตารางตามฟังก์ชันหรือโมดูลระหว่างพัฒนาได้

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
10. PostgreSQL เป็นฐานข้อมูลหลักและ source of truth สำหรับข้อมูลธุรกรรมและ Master Data ของ UMW2
11. `wq_source` เป็นประเภทแหล่งน้ำกลางคงที่ระดับระบบ
12. `raw_unit` เป็นข้อมูลกลางข้าม Organization และ Site–Raw Unit เป็นความสัมพันธ์แบบ many-to-many
13. Unit อีก 4 ประเภทมีขอบเขตภายใน Organization และต้อง mapping กับ Site ใน Organization เดียวกัน
14. DDL draft ของ `potable_unit` และ `potable_transfer_unit` ต้องมี `organization_id` เป็นเจ้าของรายการ; `raw_unit` ไม่ต้องมีคอลัมน์นี้
15. `filtration_subunits.unit_no` เป็นเลข Master กลางที่ไม่ซ้ำระดับ global ไม่ใช่หมายเลขที่สร้างใหม่แยกต่อ Site
16. `site_raw_units` เป็น mapping ระหว่าง Site กับ `raw_unit` กลางโดยไม่เก็บ `organization_id` ซ้ำ; ความจุใช้ `capacity` กับ `capacity_uom_id` และต้องแยก uniqueness ระหว่าง mapping ที่มี/ไม่มี `custom_name`

`water_source` ของ Jar Test ยังคงหมายถึง `raw_unit` เท่านั้น โดยต้องเป็นรายการที่ mapping กับ Site ของงาน

รายละเอียดเหตุผลอยู่ใน `docs/decisions/ADR-0001-organization-business-unit-site-water-source.md`, `docs/decisions/ADR-0002-postgresql-as-primary-database.md` และ `docs/decisions/ADR-0003-site-scoped-jar-test-settings.md`

## Additional accepted decisions (2026-09-23)

17. Jar Test Setting เป็น TO-BE ระดับ Site ครอบคลุมพารามิเตอร์/หน่วยน้ำดิบ พารามิเตอร์ผลทดสอบ/Bound และสารเคมี/ราคา; ใช้กับ raw_unit ที่ mapping กับ Site และงานเก็บ snapshot ค่าตั้งที่ใช้
18. เงื่อนไขการกวนและตกตะกอนเป็นข้อมูลรายงานการทดลอง ไม่รวมใน Site Setting และไม่ใช้คำนวณ dose หรือ pass/fail ตามขอบเขตที่อนุมัติ

## Canonical baseline

พฤติกรรม Jar Test ระบบเดิมอยู่ใน:

`JarTest/legacy-jar-test-product-functional-spec-th.md`

เอกสารนี้เป็น AS-IS baseline เท่านั้น ไม่ใช่ database schema และไม่ใช่ข้อสรุป UX ของระบบใหม่

## Open product and architecture decisions

เรียงตามลำดับที่ควรตัดสิน:

1. cardinality และข้อมูลประกอบของ mapping สำหรับ `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
2. วิธีระบุหน่วยจริงของ `sedimentation_unit` และ `filtration_unit` ที่ชื่อหรือขนาดซ้ำกันในหลาย Site
3. ชื่อ `potable_tranfer_unit` จะคงตามแบบข้อมูลหรือแก้เป็น `potable_transfer_unit`
4. รายการ Global Master ขั้นต่ำที่ Jar Test ต้องใช้ร่วมกับโมดูลในอนาคต
5. ขอบเขต role และ permission ของ Organization, Business Unit และ Site
6. Target workflow ของ Jar Test รุ่นแรก: จำลอง legacy ทุกจุดหรืออนุญาตแก้ UX บางส่วน
7. ค่าและโครงสร้าง Bound รวมถึงช่วงเวลาที่มีผล
8. สูตรแนะนำ Pre-chlorine และด่างทับทิมที่ระบบเดิมใช้
9. Backend framework, ORM/query layer, PostgreSQL hosting และ deployment target
10. Physical database schema, tenant isolation, audit/history และ migration strategy โดยจะพัฒนาแบบ iterative ตามโมดูล
11. Physical DDL ของ `users` และการยืนยัน seed mapping จริงของ `site_raw_units`

รายละเอียดช่องว่างของระบบเดิมดูหัวข้อ 22 ใน legacy specification

## Site settings follow-up decisions

1. รายการพารามิเตอร์ หน่วย Bound และสารเคมี/ราคาจริงสำหรับ Site Setting รวมถึง comparison semantics ของ Bound
2. Physical schema, versioning, permission/audit และพฤติกรรม draft เมื่อ Site ยังตั้งค่าไม่ครบ
3. ความจำเป็นของการตั้งค่าเฉพาะ raw_unit ภายใน Site; ขอบเขตที่อนุมัติปัจจุบันใช้ Site Setting ครอบคลุมทุก raw_unit ที่ mapping

## Immediate next step

รับไฟล์ Excel schema draft มาวิเคราะห์เทียบกับเอกสารปัจจุบัน จากนั้นยืนยัน cardinality และข้อมูลของ mapping สำหรับ unit ภายใน Organization ก่อนลง physical PostgreSQL schema แบบ iterative

## Handoff instructions

ความจำประกอบล่าสุด: [Project Memory](docs/memory/README.md), [บันทึกเริ่มต้น](docs/memory/sessions/2026-09-08-project-foundation.md), [คำชี้แจงโครงสร้างแหล่งน้ำ](docs/memory/sessions/2026-09-09-water-quality-source-structure.md), [การเลือก PostgreSQL](docs/memory/sessions/2026-09-12-postgresql-decision.md), [แนวทาง schema แบบ iterative](docs/memory/sessions/2026-09-14-schema-draft-and-iterative-design.md), [ขอบเขต unit และ Site mapping](docs/memory/sessions/2026-09-15-unit-scope-and-site-mapping.md), [การเพิ่ม organization_id ใน unit เฉพาะ Organization](docs/memory/sessions/2026-09-21-organization-scoped-unit-ddl.md) และ [การปรับ Site–Raw Unit mapping](docs/memory/sessions/2026-09-21-site-raw-unit-mapping.md) ประวัติแชตฉบับเต็มยังไม่ถูกนำเข้า

อ่าน [Jar Test Setting รายสถานี](docs/memory/sessions/2026-09-23-jar-test-site-settings.md) และ [ข้อกำหนด TO-BE](JarTest/jar-test-site-settings-requirements-th.md) ก่อนออกแบบ schema หรือ workflow ที่เกี่ยวข้อง

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
