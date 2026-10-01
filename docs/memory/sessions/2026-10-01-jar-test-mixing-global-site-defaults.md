# บันทึกการตัดสินใจ: ค่าเริ่มต้น Global และการตั้งค่าเงื่อนไขกวนผสมราย Site

วันที่: 2026-10-01

ประเภท: คำยืนยันโดยตรงของเจ้าของโครงการ; แก้ไขข้อสรุปก่อนหน้า

## คำยืนยันล่าสุด

- เงื่อนไขกวนผสมและการตกตะกอนต้องมีชุด Global ใช้เป็นค่าเริ่มต้น
- แต่ละ Site ตั้งค่า override จาก Global ได้ เมื่อ Site นั้นมีเงื่อนไขเฉพาะ
- FLOCCULATION S3 เป็นขั้นเสริม: เปิดใช้สำหรับ Site ที่ต้องใช้ได้ (ตัวอย่างในภาพ MAMIs แสดง S3)
- เมื่อเปิด FLOCCULATION S3 ให้แสดงและบันทึกทั้งเวลาและ RPM เช่นเดียวกับ FLOCCULATION S1/S2; เฉพาะ SEDIMENTATION ที่ไม่มี RPM
- ค่าที่ตั้งไว้ต้องนำไปใช้เป็นค่าเริ่มต้นใน Job Form ของ Jar Test
- ค่าตัวเลขที่ยังไม่กรอกยังคงเป็น optional; `0` ที่เป็น placeholder หมายถึงช่องว่าง ไม่ใช่ค่าที่บันทึก และเมื่อแสดงผลให้ใช้ “ไม่ได้ระบุ”
- ค่าที่ใช้จริงในแต่ละรอบยังบันทึกแยกใน `jar_test_mixing_conditions`; เงื่อนไขเหล่านี้เป็นบันทึกสภาวะ ไม่ใช้คำนวณ dose หรือ pass/fail
- เมื่อเพิ่มรอบใหม่ ให้คัดลอกเงื่อนไขที่บันทึกจริงจากรอบก่อนหน้ามาเป็นค่าเริ่มต้น; ไม่โหลด Site Setting ใหม่กลางงาน และการแก้รอบใหม่ไม่กระทบรอบก่อนหน้า

## ความสัมพันธ์กับข้อสรุปเดิม

ข้อสรุป 2026-09-30 ที่ระบุว่าไม่ต้องมี Site Setting/mapping สำหรับเงื่อนไขกวนผสม ถูกแทนที่ในส่วนดังกล่าวโดยคำยืนยันใหม่นี้. ส่วนที่ยังคงใช้ได้คือชุด 5 ขั้นมาตรฐาน, ค่าว่าง/`NULL`, การแสดง “ไม่ได้ระบุ”, และการไม่ใช้เงื่อนไขนี้คำนวณ dose หรือ pass/fail.

## แยก Configuration กับ Transaction

- Global/Site configuration: ค่าเริ่มต้นของขั้นและค่าตั้งที่นำไปเติมใน Job Form
- `jar_test_mixing_conditions`: ค่าที่ผู้ใช้บันทึกไว้จริงในแต่ละรอบ
- เมื่อเปิด FLOCCULATION S3 ให้ Site แล้ว จึงนำขั้นนี้เข้า job form และบันทึกในรอบที่ใช้งาน

ใช้ physical schema ที่ยืนยันแล้ว: `jar_test_mixing_defaults` สำหรับ Global และ `site_jar_test_mixing_settings` สำหรับ Site โดยมี 6 แถวคงที่ต่อ Site และ `UNIQUE(site_id, stage_code)`.

## ยังไม่ยืนยัน (Open Decisions)

- ค่าที่เติมจาก Site Setting ในฟอร์มสร้างงาน Jar Test แก้ได้หรือไม่ (หากแก้ จะกลายเป็นค่าจริงของรอบแรก); รอบถัดไปคัดลอกจากค่าจริงของรอบก่อนหน้าแล้ว
- ไม่มีการ merge แบบราย field: Site มีครบ 6 แถวของตนเองและแก้ค่าในแถวนั้นได้. การแก้ Global ภายหลังไม่เปลี่ยน Site ที่มีแล้ว
- ใช้ `stage_code` คงที่ PRE_OXIDATION, COAGULATION, FLOCCULATION_S1, FLOCCULATION_S2, FLOCCULATION_S3 และ SEDIMENTATION พร้อม display order 10–60 เพื่อให้ S3 อยู่ระหว่าง S2 กับ SEDIMENTATION
- ค่าตั้งและการเปิด S3 จะถูก snapshot ตอนสร้างงานหรือรอบ และผลของการแก้ Site Setting ต่อ draft ที่เปิดอยู่
- permission และ audit ของ Global/Site configuration
- ค่า duration/RPM ของ Global default; ผู้ใช้ยังไม่ได้ระบุตัวเลขตั้งต้น

## เอกสารเจ้าของข้อสรุปล่าสุด

- [`../../../JarTest/jar-test-site-settings-requirements-th.md`](../../../JarTest/jar-test-site-settings-requirements-th.md), JT-SET-032
- [`../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md`](../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md)
- [`../../decisions/ADR-0003-site-scoped-jar-test-settings.md`](../../decisions/ADR-0003-site-scoped-jar-test-settings.md)
- [`../../architecture/TARGET-DOMAIN-MODEL.md`](../../architecture/TARGET-DOMAIN-MODEL.md)
- [`../../../CONTEXT.md`](../../../CONTEXT.md)
