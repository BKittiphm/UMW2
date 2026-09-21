# ADR-0001: Organization, Business Unit, Site and Water-Quality Sources

- Status: Accepted; amended 2026-09-15
- Date: 2026-09-08; amended 2026-09-09, 2026-09-15
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
10. `wq_source` เป็นตารางประเภทกลางคงที่ระดับระบบ
11. `raw_unit` เป็นรายการแหล่งน้ำดิบจริง เช่น แม่น้ำหรือสระ ใช้ร่วมข้าม Organization ได้
12. Site และ `raw_unit` มีความสัมพันธ์แบบ many-to-many ผ่าน mapping; Jar Test เลือกได้เฉพาะ `raw_unit` ที่ mapping กับ Site ของงาน
13. `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit` อยู่ภายใน Organization และต้อง mapping กับ Site ใน Organization เดียวกัน
14. DDL draft ของ `potable_unit` และ `potable_transfer_unit` ต้องมี `organization_id` เป็นเจ้าของรายการโดยตรง; `raw_unit` ยังคงเป็น Global Master โดยไม่ใช้ `organization_id`
15. `filtration_subunits.unit_no` เป็นหมายเลขของ Master กลางที่ไม่ซ้ำระดับ global; Site mapping เป็นผู้ระบุว่าชุดถังกรองใดมีช่องกรองหมายเลขใด
16. `site_raw_units` เป็น Site–Raw Unit mapping ที่ไม่เก็บ `organization_id` ซ้ำ; รายละเอียดเฉพาะจุดใช้ `custom_name`, `capacity` และ `capacity_uom_id` และร่าง DDL ต้องแยก uniqueness ของ mapping ที่มี/ไม่มี `custom_name`

## Consequences

- ต้องแยกประเภท `wq_source` ออกจากรายการแหล่งน้ำจริงในตารางประเภท
- UI ทั่วไปเลือกประเภทน้ำเพื่อแสดงรายการจากตารางที่สอดคล้องกัน
- UI ของ Jar Test จำกัดประเภทเป็นน้ำดิบและแสดงรายการจาก `raw_unit`
- Query และสิทธิ์ต้องรู้ทั้ง Site, ประเภทน้ำ และรายการจริงตามบริบทของโมดูล
- Physical schema ต้องมี relationship record สำหรับ Site–Raw Unit และบังคับให้ Jar Test อ้างเฉพาะรายการที่ Site ใช้ได้
- Physical schema ต้องป้องกัน mapping ของ unit ภายใน Organization ไปยัง Site ของคนละ Organization
- DDL ของ unit ที่เป็น Organization-scoped ต้องเก็บ owner scope ให้ตรวจสอบได้ และ constraint ระดับ Organization ต้องไม่ทำให้รายการของคนละ Organization ชนกัน
- Site–Raw Unit mapping ต้องตรวจสอบสิทธิ์ผ่าน Site ซึ่งเป็นผู้สืบทอด Organization; ไม่เพิ่ม `organization_id` ซ้ำใน relationship record

## Alternatives considered

### เก็บรายการแหล่งน้ำทุกประเภทในตารางเดียว

ไม่เลือกสำหรับ baseline ปัจจุบัน เพราะเจ้าของโครงการยืนยันว่าประเภทน้ำแต่ละประเภทเป็นตารางแยกกันตามชื่อ

### Business Unit และ Site เป็น entity เดียว

ไม่เลือก เพราะเจ้าของโครงการยืนยันว่าเป็นคนละระดับ และหนึ่ง Business Unit มีหลาย Site

### ให้ unit ทุกประเภทใช้ร่วมข้าม Organization

ไม่เลือก เพราะผู้ใช้ยืนยันว่าเฉพาะ `raw_unit` ใช้ร่วมข้าม Organization ได้ ส่วน unit อีก 4 ประเภทมีขอบเขตภายใน Organization

## Amendment history

- 2026-09-08: บันทึก Organization, Business Unit, Site และความเข้าใจเบื้องต้นเรื่อง Water Source
- 2026-09-09: แทนที่ส่วน Water Source โดยแยก `wq_source` ซึ่งเป็นประเภทออกจากตารางรายการจริง และกำหนดว่า Jar Test ใช้ `raw_unit`
- 2026-09-15: กำหนดขอบเขต `wq_source` และ `raw_unit`, ยืนยัน Site–Raw Unit mapping และกำหนดขอบเขต Organization ของ unit ประเภทอื่น
- 2026-09-21: เจ้าของโครงการยืนยันให้ `potable_unit` และ `potable_transfer_unit` มี `organization_id` ใน DDL draft และยืนยันว่า `filtration_subunits.unit_no` เป็น Master number ระดับ global
- 2026-09-21: ปรับร่าง `site_raw_units` ให้ไม่มี `organization_id`, ใช้ `capacity_uom_id`, FK actions และ partial unique indexes ตาม `custom_name`

## Unresolved follow-up decisions

1. cardinality และข้อมูลประกอบของ mapping สำหรับ `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
2. ต้องแยกหน่วยจริงของ `sedimentation_unit` และ `filtration_unit` ที่ชื่อหรือขนาดซ้ำกันด้วยข้อมูลใด
3. ชื่อ `potable_tranfer_unit` จะคงตามแบบข้อมูลหรือแก้เป็น `potable_transfer_unit`
4. ตารางประเภทและ mapping ต้องมี effective dates, active status หรือ Site-specific metadata หรือไม่
5. Jar Test จะอ้าง `raw_unit` โดยตรงพร้อม constraint หรืออ้าง Site–Raw Unit relationship record โดยตรง
6. Physical DDL ของ `users` และ seed mapping จริงของ `site_raw_units` ยังไม่ถูกยืนยัน
