# ADR-0003: Site-scoped Jar Test Settings

- Status: Accepted; amended 2026-09-29, 2026-10-01 and 2026-10-02
- Date: 2026-09-23; amended 2026-09-25, 2026-09-29, 2026-10-01 and 2026-10-02
- Scope: Product and domain behavior for Jar Test in UMW2

## Context

เจ้าของโครงการอนุมัติให้มี Jar Test Setting แยกตาม Site เพื่อรองรับความแตกต่างระหว่างสถานี หลังจากสำรวจหน้าจอ MAMIS พบการตั้งค่าคุณสมบัติน้ำดิบ สารเคมี/ราคา และพารามิเตอร์ผลทดสอบ/Bound ต่อมาวันที่ 25 กันยายน 2569 เจ้าของโครงการยืนยันชุดพารามิเตอร์น้ำดิบ 9 รายการเป็นค่าเริ่มต้นของ Site ใน UMW2 และวันที่ 29 กันยายน 2569 ยืนยันให้ใช้ชุดเดียวกันเป็นพารามิเตอร์ผลทดสอบ เปลี่ยน Soluble Manganese เป็น Dissolved manganese กำหนด Bound ตั้งต้น lower 0/upper ไม่กำหนด และบังคับกรอกค่าน้ำดิบก่อน submit เฉพาะ Turbidity

เจ้าของโครงการให้ข้อสังเกตว่าเงื่อนไขการกวนและตกตะกอนเป็นการบันทึกสภาวะของการทดลอง จึงควรแยกออกจากค่าตั้งรายสถานีและไม่กำหนดให้เป็น input ของสูตรคำนวณในขอบเขตปัจจุบัน

## Decision

1. UMW2 มี Jar Test Setting แยกตาม Site ไม่ใช้ค่าตั้งชุดเดียวทั้งระบบ
2. Site Setting ครอบคลุมพารามิเตอร์และหน่วยของคุณสมบัติน้ำดิบ พารามิเตอร์ผลทดสอบและ Bound รวมถึงสารเคมีที่ Site ใช้; ราคาและผู้ขายเก็บใน `site_chemical_contracts` ตามสัญญาจัดซื้อ
3. Site Setting มีผลกับ raw_unit ทุกแหล่งที่ mapping กับ Site นั้น; water_source ของงานยังเลือกได้เฉพาะ raw_unit ที่ mapping กับ Site
4. งานแต่ละครั้งเก็บสำเนาค่าตั้งที่ใช้ เพื่อรักษาความถูกต้องของข้อมูลย้อนหลังเมื่อมีการเปลี่ยน Setting
5. หาก Site ยังไม่มีค่าตั้งที่จำเป็นนอกเหนือจากพารามิเตอร์น้ำดิบ 9 ค่าเริ่มต้น ระบบต้องแจ้งให้ตั้งค่าก่อนใช้งาน และห้ามยืมค่าตั้งของ Site อื่นหรือใช้ค่าอื่นจาก MAMIS โดยปริยาย
6. เงื่อนไขการกวนและตกตะกอนมี Global default และ override ราย Site เพื่อเติมเป็นค่าเริ่มต้นใน Job Form; ค่าที่ใช้จริงบันทึกแยกตามรอบตาม [ADR-0004](ADR-0004-jar-test-rounds-and-mixing-conditions.md). ข้อสรุปเดิมที่ว่าไม่อยู่ใน Site Setting ถูก supersede เมื่อ 2026-10-01. เงื่อนไขนี้ไม่ใช้คำนวณปริมาณสารหรือผลผ่าน/ไม่ผ่าน
7. ข้อกำหนดนี้เป็น TO-BE เพิ่มเติม; legacy Jar Test baseline คงเดิม
8. เกณฑ์ผลทดสอบมีหนึ่งชุดต่อ Site, Parameter และ Parameter Type; เมื่อเปลี่ยนเกณฑ์ให้แก้รายการเดิมโดยไม่มี effective date หรือประวัติเวอร์ชันในตารางเกณฑ์
9. Bound เปรียบเทียบแบบ inclusive; `lower_bound` ที่เป็น `NULL` ใช้ค่า 0, `upper_bound` ที่เป็น `NULL` หมายถึงไม่มีเพดานบน, ห้าม Bound ว่างทั้งคู่ และห้ามค่าติดลบ
10. การแก้เกณฑ์มีผลกับงานที่ยังไม่ submit เท่านั้น; งานที่ submit แล้วคง snapshot เดิม และผู้ใช้ต้องสร้าง Jar Test ใหม่หากต้องการใช้เกณฑ์ใหม่
11. สารเคมีชนิดเดียวกันรองรับหลาย Vendor และหลายราคาได้ผ่าน `site_chemical_contracts`; `site_chemicals` ไม่เก็บราคา
12. การทดลองเก็บ dose และ snapshot ที่จำเป็นต่อ C1V1 = C2V2/ต้นทุนต่อบีกเกอร์ ส่วน dose สรุปสุดท้ายเก็บแยกจาก dose การทดลอง
13. Site ใหม่สร้าง `site_jar_test_raw_properties` ตั้งต้น 9 แถว ได้แก่ Turbidity, True Color, pH, Conductivity, Temperature, Total Alkalinity as CaCO3, Iron, Total Manganese และ Dissolved manganese
14. `jar_test_raw_water_results` อ้าง mapping ของ Site โดยตรง ระบบแสดงเฉพาะรายการ active ของ Site และกันผลซ้ำด้วยงานกับ mapping
15. Site ใหม่มีพารามิเตอร์ผลทดสอบตั้งต้นชุดเดียวกับ 9 พารามิเตอร์น้ำดิบ โดยใช้ Dissolved manganese แทน Soluble Manganese; Bound ตั้งต้นเป็น lower 0 และ upper `NULL`
16. ค่าน้ำดิบที่บังคับกรอกก่อน submit มีเฉพาะ Turbidity รายการอื่นในชุด active กรอกได้แต่ไม่บังคับ
17. สารเคมีที่ใช้เลือกได้เฉพาะรายการที่ Site mapping ไว้; UMW2 ไม่รวมสูตรแนะนำ Pre-chlorine หรือด่างทับทิม
18. UMW2 สามารถปรับ UX จาก legacy ได้ทันที โดยต้องรักษากฎข้อมูล การคำนวณ และ lifecycle ที่อนุมัติ

## Consequences

- Site ใหม่เริ่มด้วยพารามิเตอร์น้ำดิบ/ผลทดสอบ 9 รายการและ Bound ตั้งต้นที่อนุมัติแล้ว ส่วนสารเคมีและ contract จริงยังต้องตั้งค่าให้ครบก่อนใช้งานจริง
- ผลผ่าน/ไม่ผ่านยังคำนวณจาก Bound โดยระบบ และสูตร dose ที่อนุมัติเดิมไม่เปลี่ยน
- การเลือกแหล่งน้ำยังบังคับผ่าน Site-Raw Unit mapping; Setting ไม่ได้เปลี่ยน raw_unit ให้เป็นข้อมูลเฉพาะ Site
- งานที่ submit แล้วต้องไม่เปลี่ยนค่าที่ใช้เมื่อ Site Setting ถูกแก้ภายหลัง; งานที่ยังไม่ submit คำนวณด้วยเกณฑ์ปัจจุบันได้
- การออกแบบ physical schema, permission และ audit ยังต้องทำแยกก่อน implementation

## Alternatives considered

### ใช้ค่าตั้งชุดเดียวทั้งระบบ

ไม่เลือก เพราะเจ้าของโครงการยืนยันให้ตั้งค่าแยกตาม Site

### ตั้งค่าเฉพาะแต่ละ raw_unit ภายใน Site

ยังไม่เลือกในขอบเขตปัจจุบัน Site Setting หนึ่งชุดใช้กับ raw_unit ที่ mapping กับ Site ทั้งหมด; source-specific override ต้องมีข้อกำหนดและการอนุมัติเพิ่มเติม

### การตั้งค่าเงื่อนไขกวนผสมและตกตะกอน

แก้ไขตามข้อยืนยันวันที่ 1 ตุลาคม 2569: ให้มี Global default และ Site override เพื่อเป็นค่าเริ่มต้นใน job form; Site ที่ต้องการ FLOCCULATION S3 เปิดขั้นนี้ได้. ค่าที่ใช้จริงยังบันทึกแยกตามรอบใน `jar_test_mixing_conditions` และไม่เปลี่ยน dose หรือ pass/fail. รายละเอียดดู [ADR-0004](ADR-0004-jar-test-rounds-and-mixing-conditions.md).

## Open follow-up decisions

1. รายการเพิ่มเติมหรือการปิดพารามิเตอร์จากชุดตั้งต้นของแต่ละ Site
2. รายการสารเคมี สัญญา ผู้ขาย หน่วยราคา และค่าราคาจริงที่จะ mapping ให้แต่ละ Site
3. Physical schema และวิธี snapshot ของ Site Setting
4. Role, permission และ audit ของการแก้ค่าตั้ง
5. พฤติกรรม draft เมื่อยังตั้งค่าไม่ครบ
6. Physical schema ของ Global/Site mixing settings, วิธีรวมค่า, S3, snapshot และการแก้ค่าใน Job Form ยังออกแบบแยกตาม [ADR-0004](ADR-0004-jar-test-rounds-and-mixing-conditions.md)
