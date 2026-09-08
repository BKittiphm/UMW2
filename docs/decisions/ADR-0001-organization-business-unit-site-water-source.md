# ADR-0001: Organization, Business Unit, Site and Water-Quality Sources

- Status: Accepted; amended 2026-09-09
- Date: 2026-09-08; amended 2026-09-09
- Scope: Target domain model for UMW2

## Context

ระบบเดิมใช้คำว่า `กิจการประปา` ใน UI แต่ข้อมูลตัวอย่างบางรายการมีลักษณะเป็นสถานี เช่น `สถานีผลิต Head Office` หากนำป้าย UI เดิมไปสร้าง schema โดยตรง จะทำให้ Business Unit และสถานที่ปฏิบัติงานกลายเป็น entity เดียวกัน

โครงการใหม่ต้องรองรับการพัฒนาโมดูลหลายส่วน ใช้ Global Master ร่วมกัน และเตรียมรองรับหลาย Organization ต่อมาผู้ใช้ชี้แจงว่าแหล่งน้ำภายในองค์กรมีหลายประเภท `wq_source` เป็นตัวเลือกประเภท และรายการจริงของแต่ละประเภทอยู่คนละตาราง

## Decision

1. เตรียมระบบให้รองรับหลาย Organization
2. `กิจการประปา` เป็น Business Unit
3. `สถานี` เป็น Site และอยู่ภายใต้ Business Unit
4. หนึ่ง Business Unit มีหลาย Site ได้ แต่ Site หนึ่งอยู่ใน Business Unit เดียว
5. ใช้ `Site/สถานี` เป็นชื่อ canonical โดยตัดคำว่า `ผลิต` ออกจากชื่อ entity
6. `wq_source` เป็นตัวเลือกประเภทแหล่งน้ำ ไม่ใช่รายการแหล่งน้ำจริง
7. รายการแหล่งน้ำแยกเก็บใน `raw_unit`, `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
8. ตารางประเภทแต่ละตารางมี `wq_source_id` เชื่อมกลับ `wq_source`
9. Jar Test ใช้เฉพาะประเภทน้ำดิบ และ `water_source` ใน Jar Test เชื่อมกับรายการจาก `raw_unit`
10. วิธีรองรับ `raw_unit` รายการเดียวให้หลาย Site ใช้ร่วมกันยังไม่ถูกตัดสิน

## Consequences

- ต้องแยกประเภท `wq_source` ออกจากรายการแหล่งน้ำจริงในตารางประเภท
- UI ทั่วไปเลือกประเภทน้ำเพื่อแสดงรายการจากตารางที่สอดคล้องกัน
- UI ของ Jar Test จำกัดประเภทเป็นน้ำดิบและแสดงรายการจาก `raw_unit`
- Query และสิทธิ์ต้องรู้ทั้ง Site, ประเภทน้ำ และรายการจริงตามบริบทของโมดูล
- ห้ามนำความสัมพันธ์ Site–Water Source แบบ many-to-many ที่เคยเสนอไปสร้าง physical schema โดยยังไม่ตัดสินวิธีแชร์ `raw_unit`

## Alternatives considered

### เก็บรายการแหล่งน้ำทุกประเภทในตารางเดียว

ไม่เลือกสำหรับ baseline ปัจจุบัน เพราะเจ้าของโครงการยืนยันว่าประเภทน้ำแต่ละประเภทเป็นตารางแยกกันตามชื่อ

### Business Unit และ Site เป็น entity เดียว

ไม่เลือก เพราะเจ้าของโครงการยืนยันว่าเป็นคนละระดับ และหนึ่ง Business Unit มีหลาย Site

### เชื่อม Site กับรายการ Water Source จริงโดยตรงแบบ many-to-many

เคยถูกบันทึกเป็นความเข้าใจเบื้องต้น แต่ถูกแทนที่สำหรับ Jar Test หลังยืนยันว่า `wq_source` เป็นประเภทและรายการจริงอยู่ใน `raw_unit` วิธีแชร์รายการจริงหลาย Site ยังเป็นคำถามเปิด

## Amendment history

- 2026-09-08: บันทึก Organization, Business Unit, Site และความเข้าใจเบื้องต้นเรื่อง Water Source
- 2026-09-09: แทนที่ส่วน Water Source โดยแยก `wq_source` ซึ่งเป็นประเภทออกจากตารางรายการจริง และกำหนดว่า Jar Test ใช้ `raw_unit`

## Unresolved follow-up decisions

1. `raw_unit` รายการเดียวใช้ร่วมกันหลาย Site ผ่านความสัมพันธ์แบบใด
2. `wq_source` เป็นประเภทกลางร่วมกันหรือเป็นรายการประเภทแยกต่อ Site
3. ชื่อ `potable_tranfer_unit` จะคงตามแบบข้อมูลหรือแก้เป็น `potable_transfer_unit`
4. Global Master เป็น global ข้าม Organization หรือ tenant-scoped
5. ตารางประเภทต้องมี effective dates, active status หรือ Site-specific metadata หรือไม่
6. Jar Test จะอ้าง `raw_unit` โดยตรงหรือผ่าน relationship record ที่รองรับหลาย Site
