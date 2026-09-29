# บันทึกการคุย: การออกแบบ Chemical Master

วันที่จัดทำ: 2026-09-29

ประเภท: คำยืนยัน TO-BE และแบบร่าง schema

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
- เพิ่ม default unit สำหรับอธิบายปริมาณของสาร แต่ไม่เพิ่ม default stock concentration ใน Master
- เพิ่ม audit columns และ constraint สำหรับ code กับ physical form

## สิ่งที่ทำจริง

- สร้างหน้า `chemicals` ใน Notion `Master Databases → Global Master Data`
- ใส่ Data Dictionary, PostgreSQL DDL draft, constraints, ตัวอย่างรายการ และความสัมพันธ์กับตารางที่มีอยู่
- ตั้งสถานะหน้าเป็น Done ในความหมายว่าเอกสารแบบร่างครบถ้วน ไม่ใช่การอนุมัติ physical schema
- ไม่ seed `default_uom_id` เพราะยังไม่ได้ยืนยัน ID หน่วยจริง
- สร้าง [ADR-0007](../../decisions/ADR-0007-global-chemical-master.md) สถานะ `Proposed`

## คำยืนยันเพิ่มเติม

เจ้าของโครงการยืนยันว่าไม่ใช้ `default_stock_concentration`: ผู้ใช้ต้องกรอกความเข้มข้นและหน่วยของสารที่ใช้จริงทุกงาน Jar Test จึงตัดสองคอลัมน์ default concentration ออกจากหน้า `chemicals` และกำหนด `jar_test_selected_chemicals.stock_concentration` กับ `stock_concentration_uom_id` เป็น `NOT NULL`; ค่าความเข้มข้นต้องมากกว่า 0

## คำยืนยันล่าสุด

เจ้าของโครงการยืนยันวันที่ 29 กันยายน 2569 ว่า `chemicals` เป็น Global Master ข้าม Organization และ PACL น้ำ 10% กับ PACL ผงเป็นคนละรายการสารตามหลักการแยกรูปแบบ/สูตรที่มีผลต่อการใช้งาน จึงเปลี่ยน ADR-0007 เป็น Accepted

## สิ่งที่ยังต้องยืนยัน

1. ยืนยันชื่อ canonical ของ `jar_chemical_type` ที่ปัจจุบันมีรายการซ้ำใน Notion
2. ยืนยันหน่วยและ seed data จริงก่อนนำ DDL ไปสร้างฐานข้อมูล

