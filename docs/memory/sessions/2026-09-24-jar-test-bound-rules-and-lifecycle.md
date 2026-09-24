# บันทึกการคุย: กติกา Bound และ lifecycle ของ Jar Test

วันที่จัดทำ: 2026-09-24

ประเภท: ข้อกำหนด TO-BE ที่เจ้าของโครงการยืนยัน

## เป้าหมายและสถานะก่อนเริ่ม

กำหนดกติกา Bound ของ `site_jar_test_bounds` ที่ยังเปิดอยู่หลังจากอัปเดต Data Dictionary ใน Notion เพื่อให้การประเมินผล Jar Test และการเปลี่ยนเกณฑ์มีพฤติกรรมแน่นอน

## คำยืนยันของเจ้าของโครงการ

1. ไม่อนุญาตให้ `lower_bound` และ `upper_bound` เป็น `NULL` ทั้งคู่
2. ค่าที่วัดได้เท่ากับขอบล่างหรือขอบบนถือว่าผ่าน (inclusive)
3. `lower_bound = NULL` ตีความเป็น 0 และ `upper_bound = NULL` หมายถึงไม่มีเพดานบน
4. Bound ต้องไม่เป็นค่าติดลบ
5. เกณฑ์ปัจจุบันมีหนึ่งรายการต่อ Site, Parameter และ Parameter Type; หากเปลี่ยนเกณฑ์ให้แก้รายการเดิม
6. งานที่ยังไม่ submit สามารถคำนวณผลใหม่จากเกณฑ์ปัจจุบันได้
7. งานที่ submit แล้วคงใช้ snapshot เกณฑ์เดิม หากต้องการใช้เกณฑ์ใหม่ต้องสร้าง Jar Test ใหม่
8. รายการ Appearance Color ของระบบเดิม ให้ผู้ตั้งค่าเลือก `Apparent Color` จาก `wq_parameter` เอง ไม่สร้างการจับคู่ชื่ออัตโนมัติ

## ผลกระทบต่อเอกสารและ Data Dictionary

- อัปเดตหน้า Notion `site_jar_test_bounds` ด้วย unique key ระดับ Site + Parameter + Type และ check constraints ตามกติกา Bound
- อัปเดต requirement, ADR, Context, Target Domain Model และ Project State ให้ย้าย comparison semantics และ lifecycle ออกจากประเด็นค้าง
- ไม่แก้ legacy specification เพราะเป็น AS-IS baseline ของระบบเดิม

## สิ่งที่ยังเปิด

- ค่า Bound จริง หน่วย และรายการพารามิเตอร์ของแต่ละ Site
- physical schema ของ snapshot และสถานะ submit
- permission และ audit ของผู้แก้เกณฑ์
