# Target Domain Model

สถานะ: Working architecture baseline — เฉพาะส่วนที่เจ้าของโครงการยืนยันแล้ว

## Purpose

กำหนดขอบเขตและความสัมพันธ์ระดับแนวคิดของระบบ UMW2 รุ่นใหม่ เพื่อใช้เป็นฐานก่อนออกแบบ database schema และเพื่อไม่ให้ชื่อจาก UI ระบบเดิมกำหนดโครงสร้างข้อมูลใหม่โดยไม่ตั้งใจ

## Confirmed hierarchy and relationships

```text
Organization
  └─ Business Unit (กิจการประปา)
       └─ Site (สถานี)

Site  * ─── *  Water Source (แหล่งน้ำ / Global Master)
          ผ่านความสัมพันธ์ Site–Water Source
```

| Entity | Meaning | Confirmed relationship |
| --- | --- | --- |
| Organization | ขอบเขตองค์กรระดับสูงสุด | มีหลาย Business Unit |
| Business Unit | กิจการประปา | อยู่ใน Organization เดียวและมีหลาย Site |
| Site | สถานี | อยู่ใน Business Unit เดียว |
| Water Source | แหล่งน้ำใน Global Master | เชื่อมได้หลาย Site และ Site เชื่อมได้หลาย Water Source |
| Site–Water Source | ความสัมพันธ์อนุญาตให้ Site ใช้แหล่งน้ำ | เป็น many-to-many association |

## Jar Test context

Jar Test ต้องระบุ Site และ Water Source ที่ใช้ในการทดสอบ ระบบต้องตรวจว่า Water Source ดังกล่าวอยู่ในรายการที่ผูกกับ Site นั้น

ใน physical schema อาจให้ Jar Test อ้างถึง relationship record ของ Site–Water Source โดยตรง หรือเก็บ Site และ Water Source แยกพร้อม constraint ก็ได้ การตัดสินใจนี้เลื่อนไปขั้นออกแบบ schema

## Invariants

1. Business Unit กับ Site เป็นคนละ entity
2. Site ต้องมี Business Unit เจ้าของเพียงหนึ่งแห่ง
3. Water Source ห้ามถูกทำซ้ำเพียงเพราะหลาย Site ใช้แหล่งเดียวกัน
4. การผูก Site–Water Source เป็นแหล่งความจริงว่าที่ Site ใดเลือกแหล่งน้ำใดได้
5. Jar Test ห้ามใช้คู่ Site–Water Source ที่ไม่มีความสัมพันธ์อนุญาต
6. ชื่อ canonical ของ entity คือ Site/สถานี ไม่ใช่สถานีผลิต

## Legacy compatibility boundary

ระบบเดิมใช้ป้าย `กิจการประปา` ในตำแหน่งที่ตัวเลือกตัวอย่างเป็น `สถานีผลิต Head Office` จึงมีความเป็นไปได้ว่าป้ายเดิมรวมความหมาย Business Unit และ Site ไว้ด้วยกัน

การสร้างระบบใหม่ต้องแยก entity ตาม target model ข้างต้น แต่ชั้น UI สำหรับ legacy-compatible flow อาจต้องแสดงข้อความเดิมชั่วคราว การตัดสินใจด้าน UI ไม่เปลี่ยน canonical data model

## Explicitly deferred

- Physical table และ column names
- Primary key strategy
- Global Master เป็น shared ข้าม Organization หรือ tenant-scoped
- กฎอนุญาตให้ Water Source เชื่อม Site ข้าม Business Unit
- effective dates, default source, active/inactive และ metadata ของ Site–Water Source
- authorization scope และ data visibility
- snapshot/history strategy ของ Jar Test
- migration จากระบบเดิม

รายการ Deferred เป็น decision ที่ยังเปิดอยู่ ห้ามนำค่าคาดเดาไปทำเป็น constraint ถาวร
