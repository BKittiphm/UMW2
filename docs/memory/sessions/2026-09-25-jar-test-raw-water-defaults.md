# บันทึกการคุย: พารามิเตอร์น้ำดิบตั้งต้น 9 รายการ

วันที่จัดทำ: 2026-09-25

ประเภท: คำยืนยันของเจ้าของโครงการและการปรับแบบร่าง

## คำยืนยัน

เจ้าของโครงการยืนยันให้ทุก Site มีพารามิเตอร์น้ำดิบตั้งต้น 9 รายการใน `site_jar_test_raw_properties`: Turbidity, True Color, pH, Conductivity, Temperature, Total Alkalinity as CaCO3, Iron, Total Manganese และ Dissolved manganese

Admin เพิ่มพารามิเตอร์จาก `wq_parameter` หรือปิดรายการระดับ Site ได้ ต่อมาวันที่ 29 กันยายน 2569 เจ้าของโครงการยืนยันให้ใช้ชุดเดียวกันเป็นพารามิเตอร์ผลทดสอบ กำหนด Bound ตั้งต้น lower 0/upper ไม่กำหนด และบังคับกรอกค่าน้ำดิบก่อน submit เฉพาะ Turbidity ดู [บันทึกข้อยืนยันล่าสุด](2026-09-29-jar-test-implementation-defaults.md)

## ผลต่อ Transaction

- `jar_test_raw_water_results` อ้าง `site_jar_test_raw_properties` แทนการอ้าง `wq_parameter` โดยตรง
- mapping ต้องเป็นของ Site เดียวกับ `jar_tests` และ active ตอนสร้างหรือแก้ไขงาน
- `UNIQUE (jar_test_id, site_jar_test_raw_property_id)` กันการบันทึกซ้ำ
- หน้ากรอกแสดง 9 ค่าเริ่มต้นพร้อมรายการ active ที่ Admin เพิ่ม การไม่กรอกค่ายังไม่สร้างแถวผล และก่อน submit บังคับเฉพาะ Turbidity

## สิ่งที่ทำจริง

- ปรับ Notion `jar_test_raw_water_results` และตั้งสถานะ Done
- ชี้แจง `site_jar_test_raw_properties` ว่าชุด 9 รายการได้รับอนุมัติเป็นค่าเริ่มต้นของ UMW2
- ปรับ requirement, target model, ADR-0003, context และ project state

## สิ่งที่ยังเปิด

- รายการเพิ่มเติมหรือการปิดพารามิเตอร์จากชุดตั้งต้นของแต่ละ Site
- รายละเอียดทางกายภาพของ constraint/validation ก่อน submit
