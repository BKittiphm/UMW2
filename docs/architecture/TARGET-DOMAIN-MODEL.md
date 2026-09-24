# Target Domain Model

สถานะ: Working architecture baseline — เฉพาะส่วนที่เจ้าของโครงการยืนยันแล้ว

## Technology baseline

- PostgreSQL เป็นฐานข้อมูลหลักและ source of truth ของ UMW2 สำหรับ Global Master, Jar Test และโมดูลในอนาคต
- การตัดสินใจนี้ยังไม่กำหนด physical schema, backend framework, ORM, hosting หรือ deployment target
- เหตุผลและผลกระทบอยู่ใน [ADR-0002](../decisions/ADR-0002-postgresql-as-primary-database.md)

## Purpose

กำหนดขอบเขตและความสัมพันธ์ระดับแนวคิดของระบบ UMW2 รุ่นใหม่ เพื่อใช้เป็นฐานก่อนออกแบบ database schema และเพื่อไม่ให้ชื่อจาก UI ระบบเดิมกำหนดโครงสร้างข้อมูลใหม่โดยไม่ตั้งใจ

## Confirmed hierarchy, scope and water-source structure

```text
System-wide reference data
  wq_source (fixed categories)
  raw_unit (raw-water sources, such as rivers or ponds)

Organization
  └─ Business Unit (กิจการประปา)
       └─ Site (สถานี)

Site  * ─── *  raw_unit                 (cross-Organization mapping allowed)
Site  ─── mapping ─── potable_unit       (same Organization only)
Site  ─── mapping ─── potable_tranfer_unit (same Organization only)
Site  ─── mapping ─── sedimentation_unit (same Organization only)
Site  ─── mapping ─── filtration_unit    (same Organization only)
```

| Entity | Meaning | Confirmed relationship |
| --- | --- | --- |
| Organization | ขอบเขตองค์กรระดับสูงสุด | มีหลาย Business Unit |
| Business Unit | กิจการประปา | อยู่ใน Organization เดียวและมีหลาย Site |
| Site | สถานี | อยู่ใน Business Unit เดียว |
| `wq_source` | ตัวเลือกประเภทแหล่งน้ำคงที่ระดับระบบ | เมื่อเลือกประเภท ระบบแสดงรายการจากตารางของประเภทนั้น |
| `raw_unit` | รายการแหล่งน้ำดิบจริง เช่น แม่น้ำหรือสระ | เป็นข้อมูลกลางข้าม Organization, มี `wq_source_id`, และเป็นแหล่งข้อมูลของ Jar Test |
| `potable_unit` | รายการน้ำประปา | อยู่ภายใน Organization, มี `wq_source_id`, และ mapping กับ Site ใน Organization เดียวกัน |
| `potable_tranfer_unit` | รายการจุดส่งจ่ายน้ำประปา | อยู่ภายใน Organization, มี `wq_source_id`, และ mapping กับ Site ใน Organization เดียวกัน; การสะกดชื่อถาวรยังต้องยืนยัน |
| `sedimentation_unit` | รายการน้ำในส่วนตกตะกอน | อยู่ภายใน Organization, มี `wq_source_id`, และ mapping กับ Site ใน Organization เดียวกัน |
| `filtration_unit` | รายการน้ำในส่วนกรอง | อยู่ภายใน Organization, มี `wq_source_id`, และ mapping กับ Site ใน Organization เดียวกัน |

## Jar Test context

### Site-scoped Jar Test settings (approved TO-BE)

- แต่ละ Site มี Jar Test Setting ของตนเอง ไม่ใช้ค่าตั้งชุดเดียวทั่วระบบ
- Setting กำหนดพารามิเตอร์และหน่วยของคุณสมบัติน้ำดิบ พารามิเตอร์ผลทดสอบและ Bound รวมถึงสารเคมีที่ใช้และราคา
- ค่าตั้งของ Site ใช้กับ raw_unit ทุกแหล่งที่ mapping กับ Site นั้น; Jar Test ยังคงเลือกได้เฉพาะ raw_unit ที่ mapping กับ Site ของงาน
- งาน Jar Test เก็บสำเนาค่าตั้งที่ใช้ เพื่อไม่ให้การแก้ Setting ในอนาคตเปลี่ยนข้อมูลย้อนหลัง
- เกณฑ์ Bound มีหนึ่งชุดต่อ Site, Parameter และ Parameter Type; แก้เกณฑ์โดยปรับรายการเดิม ไม่มีช่วงวันมีผลในขอบเขตปัจจุบัน
- การประเมิน Bound ใช้ขอบเขตรวม (`lower <= measured <= upper` เมื่อมี upper); `lower_bound = NULL` หมายถึง 0, `upper_bound = NULL` หมายถึงไม่มีเพดานบน และห้าม Bound ว่างทั้งคู่
- เกณฑ์ที่แก้ไขมีผลกับงานที่ยังไม่ submit; งานที่ submit แล้วคงใช้ snapshot เดิม และต้องสร้าง Jar Test ใหม่หากต้องการประเมินด้วยเกณฑ์ใหม่
- เงื่อนไขการกวนและตกตะกอนเป็นข้อมูลของการทดลองแต่ละครั้ง ไม่อยู่ใน Site Setting และไม่ใช้คำนวณปริมาณสารหรือผลผ่าน/ไม่ผ่านในขอบเขตที่อนุมัติ
- หาก Site ยังไม่มีค่าตั้งที่จำเป็น ระบบต้องแจ้งว่าต้องตั้งค่าก่อนใช้งาน และห้ามเลือกใช้ค่าเริ่มต้นจาก MAMIS หรือ Site อื่นโดยเงียบ
- ข้อกำหนดนี้เป็น TO-BE เพิ่มเติม ไม่แก้หรือแทนที่ legacy baseline

องค์กรมีแหล่งน้ำหลายประเภท แต่ Jar Test ใช้เฉพาะน้ำดิบ ดังนั้น `water_source` ใน Jar Test หมายถึงรายการจริงจาก `raw_unit` ไม่ใช่แถวประเภทใน `wq_source` และไม่แสดงรายการจากตารางประเภทอื่น

เส้นทางเชิงแนวคิดคือ `Site → Site–Raw Unit mapping → raw_unit → Jar Test เลือกหนึ่งรายการ` โดย `raw_unit` หนึ่งรายการเชื่อมกับหลาย Site ได้แม้ Site อยู่คนละ Organization

## Invariants

1. Business Unit กับ Site เป็นคนละ entity
2. Site ต้องมี Business Unit เจ้าของเพียงหนึ่งแห่ง
3. `wq_source` เป็นประเภท ไม่ใช่รายการแหล่งน้ำจริง
4. แหล่งน้ำแต่ละประเภทเก็บในตารางแยกตามชื่อและเชื่อมกลับด้วย `wq_source_id`
5. Jar Test เลือกเฉพาะรายการจาก `raw_unit` ที่มี mapping กับ Site ของงาน
6. `water_source` ใน Jar Test ห้ามถูกตีความเป็นรายการจากน้ำทุกประเภท
7. ชื่อ canonical ของ entity คือ Site/สถานี ไม่ใช่สถานีผลิต
8. `raw_unit` เป็นข้อมูลกลางข้าม Organization แต่ unit อีก 4 ประเภทมีขอบเขตภายใน Organization
9. `filtration_subunits.unit_no` เป็นหมายเลขของ Master กลางที่ไม่ซ้ำระดับ global; การติดตั้งจริงของแต่ละ Site ระบุผ่าน `site_filtration_subunits`

## Current schema-draft clarification

- DDL draft ของ `potable_unit` และ `potable_transfer_unit` ใช้ `organization_id` ระบุ Organization เจ้าของรายการโดยตรง และต้องใช้ร่วมกับ mapping ที่ผูกกับ Site ใน Organization เดียวกัน
- การระบุ `organization_id` ในสองตารางนี้เป็นการยืนยันขอบเขตของ schema draft; tenant isolation ของทุก mapping, authorization และ physical schema ทั้งระบบยังต้องออกแบบให้ครบก่อนอนุมัติเป็น schema สุดท้าย

### Site–Raw Unit mapping draft

- `site_raw_units` เป็น relationship ระหว่าง Site กับ `raw_unit` กลาง จึงไม่เก็บ `organization_id` ซ้ำ; Organization อนุมานผ่าน Site
- รายละเอียดเฉพาะจุดรับน้ำดิบอยู่ที่ `custom_name`, `capacity` และ `capacity_uom_id`
- Draft DDL ใช้ `ON DELETE CASCADE` กับ Site, `ON DELETE RESTRICT` กับ Raw Unit และหน่วยวัด และ `ON DELETE SET NULL` กับผู้แก้ไขข้อมูล
- กรณี `custom_name` เป็น NULL อนุญาต mapping ค่าเริ่มต้นได้หนึ่งรายการต่อ Site–Raw Unit; กรณีมีชื่อให้ชื่อไม่ซ้ำกันภายในคู่เดียวกัน
- รายละเอียดนี้เป็น schema draft ใน Notion; physical schema สุดท้ายและ seed mapping จริงยังต้องยืนยันร่วมกับตาราง `users` และข้อมูล Site

## Legacy compatibility boundary

ระบบเดิมใช้ป้าย `กิจการประปา` ในตำแหน่งที่ตัวเลือกตัวอย่างเป็น `สถานีผลิต Head Office` จึงมีความเป็นไปได้ว่าป้ายเดิมรวมความหมาย Business Unit และ Site ไว้ด้วยกัน

การสร้างระบบใหม่ต้องแยก entity ตาม target model ข้างต้น แต่ชั้น UI สำหรับ legacy-compatible flow อาจต้องแสดงข้อความเดิมชั่วคราว การตัดสินใจด้าน UI ไม่เปลี่ยน canonical data model

## Explicitly deferred

- physical schema และ snapshot representation ของ Jar Test Site Settings
- รายการพารามิเตอร์ หน่วย Bound และสารเคมี/ราคาที่แต่ละ Site จะตั้งค่า
- permission และ audit ของผู้แก้ไข Site Settings
- พฤติกรรมของ draft เมื่อ Site ยังไม่มีค่าตั้งครบ
- การตั้งค่าเฉพาะ raw_unit ภายใน Site เป็นส่วนขยายที่ยังไม่อนุมัติ
- Physical table และ column names
- PostgreSQL hosting, backend framework, ORM/query layer และ deployment target
- Primary key strategy
- คงชื่อ `potable_tranfer_unit` ตามแบบข้อมูลหรือแก้การสะกดเป็น `potable_transfer_unit`
- cardinality, effective dates, active/inactive และ metadata ของ mapping สำหรับ unit ที่อยู่ภายใน Organization
- การแยกหน่วยจริงของ `sedimentation_unit` และ `filtration_unit` เมื่อชื่อหรือขนาดซ้ำกันในหลาย Site
- authorization scope และ data visibility
- migration จากระบบเดิม

รายการ Deferred เป็น decision ที่ยังเปิดอยู่ ห้ามนำค่าคาดเดาไปทำเป็น constraint ถาวร
