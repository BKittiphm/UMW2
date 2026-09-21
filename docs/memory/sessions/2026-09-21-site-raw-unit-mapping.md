# บันทึกการคุย: Site–Raw Unit mapping

วันที่จัดทำ: 2026-09-21

ประเภท: การตรวจและปรับปรุง DDL draft ใน Notion ตามคำขอของเจ้าของโครงการ

## เป้าหมายและสถานะก่อนเริ่ม

ตรวจหน้า Notion `site_raw_units` ซึ่งเป็น mapping ระหว่างสถานีกับ `raw_unit` กลาง พบว่าโครงสร้างหลักถูกต้อง แต่ยังขาด DDL, ใช้ชื่อ `User(id)` ไม่ตรงกับหน้าที่ใช้ `users(id)` และ Unique Constraint เดิมไม่ป้องกันแถวซ้ำเมื่อ `custom_name` เป็น `NULL`

## คำยืนยันและข้อสรุป

- `raw_unit` เป็น Master กลางข้าม Organization
- `site_raw_units` จึงไม่ต้องมี `organization_id`; Organization อนุมานผ่าน `site → business_unit → organization`
- ความจุเฉพาะจุดใช้ `capacity` และ `capacity_uom_id`
- Site ลบแล้วลบ mapping ตาม (`CASCADE`); Raw Unit และ UOM ที่ถูกใช้งานห้ามลบ (`RESTRICT`); ผู้แก้ไขที่ถูกลบให้เก็บ mapping ไว้และตั้งค่า FK เป็น NULL (`SET NULL`)
- กรณีไม่มี `custom_name` ต้องมี mapping ค่าเริ่มต้นได้หนึ่งรายการต่อ Site–Raw Unit; กรณีมีชื่อให้ไม่ซ้ำกันภายในคู่เดียวกัน

## สิ่งที่ทำจริง

- อัปเดตหน้า Notion [`site_raw_units`](https://app.notion.com/p/3e2127d4fb3880ca9bfcde92e19430c5)
- ปรับ Data Dictionary เป็น `capacity_uom_id` และ `users(id)` พร้อมคำอธิบาย canonical ว่า “สถานี” และ “แหล่งน้ำดิบจริง”
- เพิ่ม callout ยืนยันว่าไม่ต้องมี `organization_id`
- เพิ่ม DDL `site_raw_units`, capacity check, FK actions, partial unique indexes และดัชนีค้นหา Raw Unit
- เพิ่ม SQL template สำหรับ seed mapping โดยไม่แต่งคู่ Site–Raw Unit จริง
- เปลี่ยนสถานะหน้า Notion เป็น `Done`
- ตรวจซ้ำหลังบันทึกแล้วพบว่าหน้ามี DDL, constraints และสถานะ `Done` ตามที่แก้ไข

## ข้อมูลที่ยังขาดและขั้นถัดไป

- หน้า Notion `user` ยังว่าง ต้องกำหนด DDL ของ `users` ก่อนรัน FK `updated_by_user_id`
- ยังไม่มี seed mapping คู่ Site–Raw Unit ที่เจ้าของโครงการยืนยัน จึงเก็บเป็น SQL template แทนข้อมูลจริง
- Physical PostgreSQL schema, tenant isolation และกลยุทธ์ audit/history ยังเป็นงานระยะออกแบบ ไม่ใช่ migration ที่รันจริง

## เอกสารที่เกี่ยวข้อง

- [CONTEXT.md](../../CONTEXT.md)
- [TARGET-DOMAIN-MODEL.md](../../architecture/TARGET-DOMAIN-MODEL.md)
- [ADR-0001](../../decisions/ADR-0001-organization-business-unit-site-water-source.md)
