# บันทึกการคุย: การเลือกสารเคมีระดับงาน Jar Test

วันที่จัดทำ: 2026-09-25

ประเภท: คำยืนยันของเจ้าของโครงการและการปรับแบบร่าง

## สถานะก่อนเริ่มและคำยืนยัน

แบบร่างมีหลายรอบแล้ว แต่การหยอดสารอ้างสัญญาโดยตรงและ unique เพียงต่อบีกเกอร์/สัญญา จึงยังไม่บังคับกฎหนึ่งสารต่อประเภท เจ้าของโครงการถามวิธีตัดสิน และยืนยันตามข้อเสนอของ AI: หนึ่งงานเลือกสารหนึ่งรายการต่อประเภท ใช้สารเดิมทุกรอบ; เปลี่ยน dose ได้ และถ้าจะเทียบสารคนละตัวให้แยกงาน

## เหตุผลและขอบเขต

- ข้อเสนอของ AI อิง flow UMW เดิมที่เลือกสารก่อนทดลองหลายรอบ และต้องการให้รอบต่าง ๆ เทียบปริมาณของสารเดียวกัน
- `site_chemical_contracts` หลายฉบับสำหรับสารเดียวกันต่างกันที่ผู้ขาย/ราคา ไม่ใช่สารคนละรายการ
- วันที่ 29 กันยายน 2569 เจ้าของโครงการยืนยันให้สารแต่ละรายการใช้ contract เดียวตลอดงาน โดยเลือกจาก Site Setting ก่อนสร้างงาน หากต้องการราคาอื่นต้องเปลี่ยน Setting ก่อนเริ่มงานใหม่ ดู [บันทึกข้อยืนยันล่าสุด](2026-09-29-jar-test-implementation-defaults.md)

## สิ่งที่ทำจริง

- เพิ่ม Notion `jar_test_selected_chemicals` ใน Master Databases → Transaction Table (Draft)
- ปรับ Notion `jar_test_chemical_doses`, `jar_test_final_chemical_doses`, `jar_tests` และหน้า JAR อธิบายการอ้างสารที่เลือกระดับงาน
- ปรับ requirement, target architecture, project state, context และ [ADR-0005](../../decisions/ADR-0005-jar-test-chemical-selection-per-job.md)

## สิ่งที่ยังขาด

- physical PostgreSQL DDL และวิธีบังคับความสัมพันธ์ข้ามตาราง
- พฤติกรรมเมื่อแก้สารหรือ contract ในงาน draft ที่บันทึก dose แล้ว
- รายการสาร สัญญา และราคาจริงของแต่ละ Site

## อ่านต่อ

- [Target Domain Model](../../architecture/TARGET-DOMAIN-MODEL.md)
- [Jar Test Setting requirement](../../../JarTest/jar-test-site-settings-requirements-th.md)
- [บันทึกหลายรอบ](2026-09-25-jar-test-multi-round-and-mixing.md)
