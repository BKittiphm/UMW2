# Target Domain Model

สถานะ: Working architecture baseline — เฉพาะส่วนที่เจ้าของโครงการยืนยันแล้ว

## Purpose

กำหนดขอบเขตและความสัมพันธ์ระดับแนวคิดของระบบ UMW2 รุ่นใหม่ เพื่อใช้เป็นฐานก่อนออกแบบ database schema และเพื่อไม่ให้ชื่อจาก UI ระบบเดิมกำหนดโครงสร้างข้อมูลใหม่โดยไม่ตั้งใจ

## Confirmed hierarchy and water-source structure

```text
Organization
  └─ Business Unit (กิจการประปา)
       └─ Site (สถานี)
            └─ wq_source (ประเภทแหล่งน้ำ)
                 ├─ raw_unit
                 ├─ potable_unit
                 ├─ potable_tranfer_unit
                 ├─ sedimentation_unit
                 └─ filtration_unit
```

| Entity | Meaning | Confirmed relationship |
| --- | --- | --- |
| Organization | ขอบเขตองค์กรระดับสูงสุด | มีหลาย Business Unit |
| Business Unit | กิจการประปา | อยู่ใน Organization เดียวและมีหลาย Site |
| Site | สถานี | อยู่ใน Business Unit เดียว |
| `wq_source` | ตัวเลือกประเภทแหล่งน้ำ | เมื่อเลือกประเภท ระบบแสดงรายการจากตารางของประเภทนั้น |
| `raw_unit` | รายการแหล่งน้ำดิบ | แต่ละรายการมี `wq_source_id` และเป็นแหล่งข้อมูลของ Jar Test |
| `potable_unit` | รายการน้ำประปา | เป็นตารางแยกและมี `wq_source_id` |
| `potable_tranfer_unit` | รายการจุดส่งจ่ายน้ำประปา | เป็นตารางแยกและมี `wq_source_id`; การสะกดชื่อถาวรยังต้องยืนยัน |
| `sedimentation_unit` | รายการน้ำในส่วนตกตะกอน | เป็นตารางแยกและมี `wq_source_id` |
| `filtration_unit` | รายการน้ำในส่วนกรอง | เป็นตารางแยกและมี `wq_source_id` |

## Jar Test context

องค์กรมีแหล่งน้ำหลายประเภท แต่ Jar Test ใช้เฉพาะน้ำดิบ ดังนั้น `water_source` ใน Jar Test หมายถึงรายการจริงจาก `raw_unit` ไม่ใช่แถวประเภทใน `wq_source` และไม่แสดงรายการจากตารางประเภทอื่น

เส้นทางเชิงแนวคิดคือ `Site → wq_source ประเภทน้ำดิบ → raw_unit หลายรายการ → Jar Test เลือกหนึ่งรายการ` ส่วนวิธีทำให้ `raw_unit` รายการเดียวใช้ร่วมกันหลาย Site ยังต้องตัดสินใจก่อนออกแบบ physical schema

## Invariants

1. Business Unit กับ Site เป็นคนละ entity
2. Site ต้องมี Business Unit เจ้าของเพียงหนึ่งแห่ง
3. `wq_source` เป็นประเภท ไม่ใช่รายการแหล่งน้ำจริง
4. แหล่งน้ำแต่ละประเภทเก็บในตารางแยกตามชื่อและเชื่อมกลับด้วย `wq_source_id`
5. Jar Test เลือกเฉพาะรายการจาก `raw_unit`
6. `water_source` ใน Jar Test ห้ามถูกตีความเป็นรายการจากน้ำทุกประเภท
7. ชื่อ canonical ของ entity คือ Site/สถานี ไม่ใช่สถานีผลิต

## Legacy compatibility boundary

ระบบเดิมใช้ป้าย `กิจการประปา` ในตำแหน่งที่ตัวเลือกตัวอย่างเป็น `สถานีผลิต Head Office` จึงมีความเป็นไปได้ว่าป้ายเดิมรวมความหมาย Business Unit และ Site ไว้ด้วยกัน

การสร้างระบบใหม่ต้องแยก entity ตาม target model ข้างต้น แต่ชั้น UI สำหรับ legacy-compatible flow อาจต้องแสดงข้อความเดิมชั่วคราว การตัดสินใจด้าน UI ไม่เปลี่ยน canonical data model

## Explicitly deferred

- Physical table และ column names
- Primary key strategy
- วิธีเชื่อม `raw_unit` รายการเดียวให้หลาย Site ใช้งานร่วมกัน
- `wq_source` เป็นประเภทกลางหรือรายการประเภทที่สร้างแยกต่อ Site
- คงชื่อ `potable_tranfer_unit` ตามแบบข้อมูลหรือแก้การสะกดเป็น `potable_transfer_unit`
- Global Master เป็น shared ข้าม Organization หรือ tenant-scoped
- effective dates, active/inactive และ metadata ของ `wq_source` กับตารางประเภท
- authorization scope และ data visibility
- snapshot/history strategy ของ Jar Test
- migration จากระบบเดิม

รายการ Deferred เป็น decision ที่ยังเปิดอยู่ ห้ามนำค่าคาดเดาไปทำเป็น constraint ถาวร
