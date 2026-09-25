# บันทึกการคุย: ราคาเคมีและ Transaction ของ Jar Test

วันที่จัดทำ: 2026-09-25

ประเภท: ข้อกำหนด TO-BE ที่เจ้าของโครงการยืนยัน

## เป้าหมายและหลักฐาน

เจ้าของโครงการนำตัวอย่างรายงานสารเคมีประจำเดือนจากหน้างานมาอธิบายว่า สารชนิดเดียวกันอาจมีหลายรายการจัดซื้อและราคาต่างกัน เช่น PACL น้ำ 10% มีมากกว่าหนึ่งราคา จึงตรวจความเหมาะสมของ mapping สารเคมี ราคา และตาราง Transaction ของ Jar Test

## คำยืนยันและข้อสรุป

1. `site_chemicals` เป็น mapping ว่า Site ใช้สาร Master ใดได้ และไม่เก็บราคา
2. `site_chemical_contracts` เก็บผู้ขายและราคาตามสัญญาของสารประจำ Site รองรับหลาย Vendor หรือหลายราคาต่อสารชนิดเดียวกัน
3. `jar_test_chemical_doses` เก็บรายการหยอดสารต่อบีกเกอร์ อ้างสัญญาที่เลือกและเก็บ snapshot ของราคา พร้อมข้อมูลสำหรับคำนวณ C1V1 = C2V2
4. ค่า dose สรุปสุดท้ายต้องเก็บใน `jar_test_final_chemical_doses` แยกจาก dose รายบีกเกอร์ เพราะผู้ใช้ยืนยันค่า final ที่อาจต่างจากบีกเกอร์ที่เลือกได้
5. Transaction Jar Test หลักมี 6 ตาราง: `jar_tests`, `jar_test_raw_water_results`, `jar_test_beakers`, `jar_test_chemical_doses`, `jar_test_results` และ `jar_test_final_chemical_doses`
6. งานที่ submit แล้วต้องใช้ snapshot ของ Bound และราคาเดิม; การตัดสต๊อกจริง/ล็อตรับเข้าเป็นขอบเขตของโมดูล Inventory ในอนาคต

## สิ่งที่ทำจริง

- อัปเดตหน้า Notion `site_chemicals` เพื่อตัดราคาและอธิบายความสัมพันธ์กับ `site_chemical_contracts`
- อัปเดต `jar_test_chemical_doses`, `jar_tests`, `jar_test_beakers` และ `jar_test_results` ใน Notion
- สร้างหน้า `jar_test_final_chemical_doses` ใน Transaction Table (Draft)
- ตรวจว่า FK ของผลตรวจชี้ไป `jar_wq_type` และหน้า dose อ้าง `jar_chemical_type`

## สิ่งที่ยังเปิด

- schema จริงของล็อตรับเข้า/สต๊อกคงเหลือ/การตัดสต๊อก และวิธีคิดต้นทุนสต๊อก
- กติกาว่า 1 บีกเกอร์ใช้ได้กี่สารต่อ `jar_chemical_type`
- physical DDL, trigger หรือ backend validation ที่ยืนยันว่า contract ที่เลือกเป็นของ Site เดียวกับงาน
- รายการสัญญา ผู้ขาย และราคาจริงของแต่ละ Site
