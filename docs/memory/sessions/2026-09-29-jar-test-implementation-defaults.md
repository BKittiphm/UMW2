# ข้อกำหนดตั้งต้นและความครบถ้วนของ Jar Test

วันที่จัดทำ: 29 กันยายน 2569

ประเภท: คำยืนยันโดยตรงของเจ้าของโครงการ

## เป้าหมายและสถานะก่อนเริ่ม

หลังจากโครงสร้างหลักของ Jar Test ได้รับการยืนยันแล้ว ยังมีคำถามเรื่องพารามิเตอร์ผลทดสอบ ค่า Bound ตั้งต้น ฟิลด์บังคับ จำนวนรอบ/Jar contract สารเคมี และขอบเขตการปรับ UX

## คำยืนยันของเจ้าของโครงการ

1. ทุก Site ใช้พารามิเตอร์น้ำดิบและผลทดสอบตั้งต้นชุด 9 รายการตาม MAMIs โดยเปลี่ยน Soluble Manganese เป็น Dissolved manganese: Turbidity, True Color, pH, Conductivity, Temperature, Total Alkalinity as CaCO3, Iron, Total Manganese และ Dissolved manganese
2. Bound ตั้งต้นของพารามิเตอร์ผลทดสอบเป็น lower 0 และ upper ไม่กำหนด (`NULL`)
3. ในข้อมูลน้ำดิบ บังคับกรอกก่อน submit เฉพาะ Turbidity
4. ผู้ใช้ต้องกดเข้า Jar ที่เลือกเพื่อกรอกผล งาน submit ได้เมื่อผลของ Jar ทุกใบที่เลือกครบ หากเลือกทดสอบ N Jar ต้องครบทั้ง N Jar
5. งานใหม่เริ่มต้นด้วยหนึ่งรอบ ผู้ใช้เพิ่มรอบได้ แต่ละรอบรองรับหมายเลข Jar 1–6 และเลือกใช้น้อยกว่า 6 ได้
6. สารแต่ละรายการใช้ contract เดียวตลอดงาน Contract ต้องเลือกในหน้าตั้งค่าก่อนเริ่ม Jar Test; หากต้องการใช้ราคาอื่นให้เปลี่ยน Setting ก่อนสร้างงาน
7. `chemicals` เป็น Global Master ข้าม Organization และ PACL น้ำ 10% กับ PACL ผงเป็นคนละรายการสาร
8. สารเคมีที่เลือกในงานมาจากรายการที่ Site mapping/เปิดใช้ไว้เท่านั้น สูตรแนะนำสารเคมีอัตโนมัติยังเลื่อนออกไป
9. UMW2 ปรับ UX จากระบบเดิมได้ทันที โดยต้องรักษากฎข้อมูล การคำนวณ และ lifecycle ที่อนุมัติ
10. ข้อมูลจริงของ Site และ mapping จะจัดทำภายหลัง เพราะโครงสร้างข้อมูลจริงยังอยู่ระหว่างตรวจสอบ

## ผลต่อการออกแบบ

- แยก requiredness ของค่าน้ำดิบออกจากความครบถ้วนของผลราย Jar: raw water บังคับเฉพาะ Turbidity แต่ผลของ Jar ที่เลือกต้องครบตามพารามิเตอร์ผลทดสอบ active
- ไม่สร้าง Jar ครบ 6 โดยปริยายหากผู้ใช้เลือกน้อยกว่า; validation ตรวจเฉพาะ Jar ที่เลือก
- `jar_test_selected_chemicals` ต้องอ้าง contract snapshot หนึ่งรายการต่อสารและใช้ซ้ำทุก round/beaker ของงาน
- Site ใหม่มี seed สำหรับพารามิเตอร์และ Bound แต่ยังต้องตั้งสาร/contract จริงก่อนเริ่มงาน

## สิ่งที่ทำจริง

- ปรับ requirement, `CONTEXT.md`, target domain model และ ADR-0003/0004/0005
- เปลี่ยน ADR-0007 จาก Proposed เป็น Accepted
- ปรับ Project State และดัชนีเอกสารให้ชี้ข้อสรุปล่าสุด

## สิ่งที่ยังเปิด

- รายการสาร สัญญา ราคา หน่วย และ mapping จริงของแต่ละ Site
- ชื่อ physical table canonical ของประเภทสารเคมีที่ยังซ้ำใน Notion
- Physical DDL, snapshot implementation, permission/audit และ validation message
- สูตรแนะนำ Pre-chlorine และด่างทับทิม

## เอกสารเจ้าของข้อสรุป

- [`../../../JarTest/jar-test-site-settings-requirements-th.md`](../../../JarTest/jar-test-site-settings-requirements-th.md)
- [`../../architecture/TARGET-DOMAIN-MODEL.md`](../../architecture/TARGET-DOMAIN-MODEL.md)
- [`../../decisions/ADR-0003-site-scoped-jar-test-settings.md`](../../decisions/ADR-0003-site-scoped-jar-test-settings.md)
- [`../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md`](../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md)
- [`../../decisions/ADR-0005-jar-test-chemical-selection-per-job.md`](../../decisions/ADR-0005-jar-test-chemical-selection-per-job.md)
- [`../../decisions/ADR-0007-global-chemical-master.md`](../../decisions/ADR-0007-global-chemical-master.md)
