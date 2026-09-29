# บันทึกการคุย: การออกแบบ Chemical Master

วันที่จัดทำ: 2026-09-29

ประเภท: แบบร่าง TO-BE ที่รอการยืนยัน

## เป้าหมายและสถานะก่อนเริ่ม

เจ้าของโครงการขอให้ออกแบบตาราง `chemicals` หลังตรวจพบว่า Master Databases ชุดหลักมีตาราง mapping และ contract ที่อ้าง `chemicals(id)` แต่ยังไม่มีหน้าเจ้าของ Master ดังกล่าว

## หลักฐานและข้อกำหนดเดิม

- `site_chemicals` เป็น mapping ว่า Site ใช้สารใดได้และไม่เก็บราคา
- `site_chemical_contracts` เก็บ Vendor, Contract และราคา รองรับหลายสัญญาต่อสารเดียวกัน
- `chemical_type_mappings` รองรับหลายบทบาทต่อสารหนึ่งรายการ
- งาน Jar Test เลือกหนึ่งสารต่อ `jar_chemical_type` ระดับงานและเก็บ snapshot ค่าที่ใช้จริง

## ข้อเสนอที่จัดทำ

- วาง `chemicals` เป็น Global Master ของตัวสารหรือสูตรผลิตภัณฑ์
- ไม่เก็บ Organization, Site, Vendor, Contract, ราคา หรือบทบาท Jar Test ในตารางนี้
- แยกรายการเมื่อรูปแบบหรือความเข้มข้นมีผลต่อการใช้งาน เช่น PACL น้ำ 10% กับ PACL ผง
- ใช้ `physical_form` (`LIQUID`, `SOLID`, `GAS`) แทน `is_gas`
- เพิ่ม default unit และ default stock concentration เป็นค่าช่วยกรอก; ธุรกรรมยังเก็บ snapshot จริง
- เพิ่ม audit columns และ constraint สำหรับ code, physical form และคู่ค่าความเข้มข้น/หน่วย

## สิ่งที่ทำจริง

- สร้างหน้า `chemicals` ใน Notion `Master Databases → Global Master Data`
- ใส่ Data Dictionary, PostgreSQL DDL draft, constraints, ตัวอย่างรายการ และความสัมพันธ์กับตารางที่มีอยู่
- ตั้งสถานะหน้าเป็น Done ในความหมายว่าเอกสารแบบร่างครบถ้วน ไม่ใช่การอนุมัติ physical schema
- ไม่ seed `default_uom_id` เพราะยังไม่ได้ยืนยัน ID หน่วยจริง
- สร้าง [ADR-0007](../../decisions/ADR-0007-global-chemical-master.md) สถานะ `Proposed`

## สิ่งที่ยังต้องยืนยัน

1. ยืนยันว่า `chemicals` เป็น Global Master ข้าม Organization ตามข้อเสนอ
2. ยืนยันหลักการแยกสูตร/รูปแบบเป็นคนละ `chemical_id`
3. ยืนยันชื่อ canonical ของ `jar_chemical_type` ที่ปัจจุบันมีรายการซ้ำใน Notion
4. ยืนยันหน่วยและ seed data จริงก่อนนำ DDL ไปสร้างฐานข้อมูล

