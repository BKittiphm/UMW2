# บันทึกการคุย: Jar Test หลายรอบและสภาวะกวนผสม

วันที่จัดทำ: 2026-09-25

ประเภท: คำยืนยันของเจ้าของโครงการและแบบร่าง Transaction

## เป้าหมายและสถานะก่อนเริ่ม

หลังเทียบแบบร่าง 6 ตารางกับ MAMIs และ UMW เดิม พบว่าร่างยังไม่รองรับการแยกรอบการทดลองและบันทึกขั้นตอนกวนผสมได้ครบ

## คำยืนยันของเจ้าของโครงการ

1. แหล่งน้ำดิบที่เลือกต้องมี mapping กับ Site ของงาน
2. UMW2 ต้องรองรับการทดลองหลายรอบ
3. ให้มีตาราง `jar_test_mixing_conditions`
4. ไม่เก็บ `operating_status_id`
5. วันและเวลาวัดคุณภาพน้ำใช้วันและเวลาทดสอบของงานเดียวกัน ไม่แยกฟิลด์

## ข้อเสนอและเหตุผลของ AI

- เพิ่ม `jar_test_rounds` และย้ายความสัมพันธ์ของบีกเกอร์ให้ชี้รอบ เพื่อให้ Jar 1–6 เริ่มหมายเลขใหม่ได้ทุกรอบ
- ให้ `jar_test_mixing_conditions` อ้างรอบและเก็บขั้นตอนแบบหลายแถว เพื่อรองรับขั้นที่มีหรือไม่มี RPM โดยไม่บังคับชื่อขั้นตาม MAMIs
- ให้หัวงานอ้าง `site_raw_unit_id` และตรวจว่า mapping ยังเปิดใช้งานและเป็นของ Site เดียวกัน

## สิ่งที่ทำจริง

- เพิ่มร่าง `jar_test_rounds` และ `jar_test_mixing_conditions` ใน Notion Transaction Table (Draft)
- ปรับร่าง `jar_tests` และ `jar_test_beakers` ใน Notion ให้เชื่อมตามลำดับงาน → รอบ → บีกเกอร์
- ปรับคำอธิบาย constraint ของ `jar_test_chemical_doses` ให้ชัดว่า unique ต่อบีกเกอร์และสัญญายังไม่บังคับหนึ่งสารต่อประเภทตลอดงาน
- ปรับ requirement, target model, ADR และ project state ใน repository

## สิ่งที่ยังขาด

- ประเด็นเลือกสารหนึ่งรายการต่อประเภทได้รับคำยืนยันภายหลังว่าใช้ตลอดทั้งงานแล้ว ดู [บันทึกการเลือกสาร](2026-09-25-jar-test-chemical-selection.md) และ [ADR-0005](../../decisions/ADR-0005-jar-test-chemical-selection-per-job.md)
- physical DDL, วิธีบังคับ Site ข้าม FK, การยืนยันจำนวน Jar ก่อน submit และการย้ายข้อมูลจากร่างเดิม

## อ่านต่อ

- [ADR-0004](../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md)
- [Target Domain Model](../../architecture/TARGET-DOMAIN-MODEL.md)
- [Jar Test Setting requirement](../../../JarTest/jar-test-site-settings-requirements-th.md)
- [Legacy Jar Test baseline](../../../JarTest/legacy-jar-test-product-functional-spec-th.md)
