# ADR-0007: Global Chemical Master และการแยกสูตรผลิตภัณฑ์

- Status: Proposed
- Date: 2026-09-29
- Scope: Chemical master shared by Jar Test and future modules (TO-BE)

## Context

แบบร่าง Jar Test มี `site_chemicals`, `chemical_type_mappings` และ `site_chemical_contracts` แล้ว แต่ยังขาดตารางเจ้าของรายการสารเคมี `chemicals` ใน Master Databases ชุดหลัก ตารางเดิมใต้พื้นที่ JAR มีเพียงชื่อสาร, `is_gas`, หน่วย และ `organization_id` ซึ่งยังแยกรูปแบบของแข็ง/ของเหลว/ก๊าซและสูตรความเข้มข้นได้ไม่ชัด

ตัวอย่างข้อมูลหน้างานแสดงว่าสารสูตรเดียวกัน เช่น PACL น้ำ 10% อาจซื้อจากหลายผู้ขายและหลายราคา ขณะเดียวกัน PACL น้ำ 10% กับ PACL ผงเป็นรายการที่มีรูปแบบและการใช้งานต่างกัน

## Proposed decision

1. ให้ `chemicals` เป็น Global Master ของตัวสารหรือสูตรผลิตภัณฑ์ และไม่เก็บ `organization_id`, `site_id`, Vendor, Contract หรือราคา
2. สารสูตรเดียวกันจากหลาย Vendor ใช้ `chemical_id` เดียวกัน แล้วแยกสัญญาและราคาผ่าน `site_chemical_contracts`
3. สารที่ต่างรูปแบบหรือความเข้มข้นจนมีผลต่อการใช้งานและราคาเป็นคนละ Master เช่น `PACL_LIQ_10` กับ `PACL_POWDER`
4. ใช้ `physical_form` ที่รองรับ `LIQUID`, `SOLID` และ `GAS` แทน `is_gas`
5. ไม่เก็บ `chemical_type_id` ใน `chemicals`; ใช้ `chemical_type_mappings` เพราะสารหนึ่งรายการมีได้หลายบทบาทใน Jar Test
6. `chemicals` ไม่เก็บ default stock concentration; ผู้ใช้ต้องกรอกค่าความเข้มข้นและหน่วยที่ใช้จริงทุกงานใน `jar_test_selected_chemicals` แล้วงานและ dose เก็บ snapshot ของค่านั้น
7. Site ที่ใช้สารรายการใดกำหนดผ่าน `site_chemicals`; ผู้ขายและราคากำหนดผ่าน `site_chemical_contracts`

## Consequences

- รายการสารเดียวกันไม่ต้องสร้างซ้ำตาม Site หรือ Vendor
- การรายงานข้าม Site และข้าม Organization ใช้รหัสสารเดียวกันได้
- ต้องยืนยันหน่วยใน `uoms` ก่อน seed `default_uom_id` และก่อนให้ผู้ใช้เลือกหน่วยความเข้มข้นจริงในงาน
- ถ้าภายหลังต้องมีชื่อหรือรายการเฉพาะ Organization ควรเพิ่ม mapping/override แยก โดยไม่เปลี่ยนความหมายของ Global Master โดยปริยาย
- Physical DDL สุดท้าย, primary key strategy, tenant isolation และ inventory stock model ยังอยู่ในขอบเขตที่เลื่อนออกไป

## Current draft

สร้างหน้า `chemicals` ใน Notion Master Databases แล้ว โดยมี `code`, ชื่อสองภาษา, `physical_form`, `specification`, หน่วยตั้งต้น, สถานะ และ audit columns พร้อม DDL draft และตัวอย่างรายการ ความเข้มข้นตั้งต้นไม่เก็บใน Master ตามคำยืนยันวันที่ 29 กันยายน 2569

## Approval status

แบบนี้เป็นข้อเสนอที่จัดทำตามคำขอให้ออกแบบตารางเมื่อวันที่ 29 กันยายน 2569 ยังต้องได้รับคำยืนยันโดยตรงจากเจ้าของโครงการก่อนเปลี่ยนสถานะ ADR เป็น `Accepted` และก่อนใช้เป็น physical schema

