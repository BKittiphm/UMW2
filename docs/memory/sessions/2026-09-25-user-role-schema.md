# บันทึกการคุย: Schema Role Type และ Users

วันที่จัดทำ: 2026-09-25

ประเภท: คำยืนยันของเจ้าของโครงการและการปรับแบบร่าง

## เป้าหมายและสถานะก่อนเริ่ม

เจ้าของโครงการขอให้สร้าง schema สำหรับประเภทบทบาทผู้ใช้, schema พื้นฐานของผู้ใช้ที่มีรหัสพนักงาน เบอร์โทร BU Site role username และ password รวมทั้งอัปเดตตารางที่เหลือใน Notion ให้เรียบร้อย

ก่อนเริ่ม หน้า `role_type` มีเพียงตัวอย่างรายการ role ไม่มี constraints และหน้า `users` ยังไม่มีเนื้อหา ส่วน `job_type`, `site_chemical_contracts` และ `jar_tests` ยังมีหมายเหตุหรือ FK ที่ต้องตรวจ

## คำยืนยันและการตัดสินใจ

- `role_type` เป็น Master กลางของบทบาท ไม่ใช่ permission รายเมนูโดยตรง
- เพิ่ม `code` ที่ไม่ซ้ำและ `scope_level` ระดับ SYSTEM, ORGANIZATION, BUSINESS_UNIT หรือ SITE
- ใช้ role ตั้งต้น 8 รายการ: Super Admin, Admin, Outsource, Operator, Shift Leader, Supervisor, Manager และ Director
- `users` ต้องรองรับ employee code, phone, Organization/BU/Site, role, username และรหัสผ่าน
- ร่างแรกกำหนด `employee_code` และ `username` ไม่ซ้ำระดับระบบ; หากข้อมูลจริงมีรหัสซ้ำข้าม Organization ให้ปรับเป็น unique ตาม Organization/BU ตอนทำ physical schema
- เก็บรหัสผ่านเป็น `password_hash` เท่านั้น ไม่เก็บ plaintext password
- Organization/BU/Site เป็น nullable ตามระดับผู้ใช้; ผู้ใช้ระดับ Organization ใช้ `organization_id` ได้โดยไม่ต้องผูก BU/Site และถ้ามี Site ต้องอยู่ใน BU/Organization เดียวกัน
- หากต้องดูแลหลาย Site ให้เพิ่ม mapping `user_sites` ในอนาคต; ตอนนี้ `site_id` เป็น Site หลัก/ค่าเริ่มต้น
- permission รายเมนูและ authorization policy ละเอียดจะออกแบบภายหลัง

## สิ่งที่ทำจริง

- อัปเดตหน้า Notion `role_type` ด้วย schema, ค่าเริ่มต้น 8 role และคำอธิบาย scope
- สร้างหน้า Notion `users` ด้วย schema, FK, password safety และกติกาความสัมพันธ์ BU/Site
- แก้ `job_type` ให้ตัวอย่างคำอธิบายใช้รหัสจริง (`WQM`, `MTR`, `BKW`, `SLG`, `PMP` ฯลฯ) และตั้งสถานะ Done
- แก้สถานะ `site_chemical_contracts` เป็น Done พร้อมล้างหมายเหตุ FK เดิมที่ล้าสมัย
- แก้ FK ของ `jar_tests.tested_by_user_id` เป็น `users(id)` และตั้งสถานะ Done
- อัปเดต `CONTEXT.md`, `PROJECT-STATE.md`, `docs/architecture/TARGET-DOMAIN-MODEL.md` และเพิ่ม ADR-0006

## สิ่งที่ยังเปิด

- รายการ permission รายเมนูและ mapping `role_permissions`
- นโยบายยืนยันตัวตนจริง เช่น provider, password policy, reset, MFA และ session
- การรองรับผู้ใช้หลาย Site ด้วย `user_sites`
- Physical PostgreSQL DDL, RLS, audit และ migration
- seed mapping จริงของผู้ใช้กับ BU/Site

## เอกสารที่เกี่ยวข้อง

- [ADR-0006](../../decisions/ADR-0006-user-role-type-schema.md)
- [Target Domain Model](../../architecture/TARGET-DOMAIN-MODEL.md)
- [CONTEXT](../../../CONTEXT.md)
- [PROJECT-STATE](../../../PROJECT-STATE.md)
