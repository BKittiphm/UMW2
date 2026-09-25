# ADR-0006: Schema พื้นฐานของ Role Type และ Users

- Status: Accepted
- Date: 2026-09-25
- Scope: Global Master / identity baseline (TO-BE draft)

## Context

ก่อนหน้านี้ Notion มีตัวอย่างบทบาทผู้ใช้ แต่ยังไม่มี schema ของ `role_type` และหน้า `users` ยังว่าง ทำให้ตารางธุรกรรมที่อ้างผู้ใช้ใช้ชื่อ FK ไม่สอดคล้องกัน และยังไม่มีโครงสร้างพื้นฐานสำหรับรหัสพนักงาน, BU, Site, username และรหัสผ่าน

เจ้าของโครงการต้องการ schema พื้นฐานก่อน แล้วจะเติมรายการหรือรายละเอียดเพิ่มเติมใน Master ภายหลัง จึงต้องแยกบทบาทออกจาก permission รายเมนู และต้องไม่เก็บรหัสผ่านแบบ plaintext

## Decision

1. ใช้ `role_type` เป็น Global Master ของบทบาท มี `id`, `code` ที่ไม่ซ้ำ, ชื่อภาษาไทย/อังกฤษ, `scope_level`, `description`, `is_active`, `created_at` และ `updated_at`
2. กำหนด `scope_level` เบื้องต้นเป็น `SYSTEM`, `ORGANIZATION`, `BUSINESS_UNIT` และ `SITE` เพื่อใช้จำกัดขอบเขตข้อมูล ไม่ถือว่าเป็น permission รายเมนู
3. บันทึก role ตั้งต้น 8 รายการ: `SUPER_ADMIN`, `ADMIN`, `OUTSOURCE`, `OPERATOR`, `SHIFT_LEADER`, `SUPERVISOR`, `MANAGER` และ `DIRECTOR`
4. ใช้ `users` เป็นบัญชีผู้ใช้พื้นฐาน โดยมี `employee_code` และ `username` เป็นค่า unique, `phone`, `organization_id`, `business_unit_id`, `site_id`, `role_type_id`, `password_hash`, สถานะใช้งาน และ timestamps
5. `organization_id`, `business_unit_id` และ `site_id` อนุญาต NULL ตามระดับบัญชี; ผู้ใช้ระดับ Organization ใช้ `organization_id` ได้โดยไม่ต้องผูก BU/Site และถ้ามี Site ต้องอยู่ใน BU/Organization เดียวกัน
6. ใช้ชื่อ `password_hash` แทน `password`; ระบบต้อง hash และไม่ส่งค่ารหัสผ่านจริงกลับใน API
7. หากผู้ใช้ต้องรับผิดชอบหลาย Site ให้เพิ่ม `user_sites` เป็น mapping แยก โดย `site_id` ใน `users` ทำหน้าที่เป็น Site หลัก/ค่าเริ่มต้นในระยะนี้
8. Permission รายเมนูและ mapping `role_type` กับ permission จะออกแบบใน `permissions`/`role_permissions` หลังรายการฟังก์ชันได้รับการยืนยัน

ร่างนี้กำหนด `employee_code` ให้ไม่ซ้ำระดับระบบเพื่อให้ค้นหาและอ้างอิงได้ง่าย หากข้อมูลจริงอนุญาตรหัสซ้ำข้าม Organization ให้เปลี่ยนเป็น unique ตาม Organization/Business Unit ตอนทำ physical schema

## Consequences

- ตาราง Jar Test และ audit สามารถอ้าง `users(id)` เป็นชื่อเดียวกันได้
- ขอบเขตระดับ BU/Site ถูกอธิบายไว้ตั้งแต่ต้น แต่ยังไม่บังคับ policy authorization ทั้งระบบ
- การรองรับหลาย Site ต่อผู้ใช้และ permission แบบละเอียดจะเพิ่มตารางในระยะถัดไป
- Schema ใน Notion เป็น draft สำหรับออกแบบโดเมน ยังไม่ใช่ physical DDL ที่อนุมัติเรื่อง provider, password policy, RLS หรือ migration

## Alternatives considered

- เก็บ `password` ตรง ๆ: ไม่เลือก เพราะเสี่ยงเปิดเผยข้อมูลลับ
- ใส่ permission ทุกเมนูไว้ใน `role_type`: ไม่เลือก เพราะทำให้ Master บทบาทผูกกับรายละเอียดฟังก์ชันที่ยังไม่สรุป
- บังคับผู้ใช้ทุกคนต้องมี Site เดียว: ไม่เลือก เพราะผู้ใช้ระดับระบบ, Organization หรือ BU อาจดูแลหลาย Site

## Evidence

- คำยืนยันของเจ้าของโครงการวันที่ 25 กันยายน 2569 ให้สร้าง schema `role_type`, schema พื้นฐาน `users` และอัปเดตตารางที่ค้าง
- หน้า Notion `role_type` และ `users` ที่อัปเดตใน Master Databases
