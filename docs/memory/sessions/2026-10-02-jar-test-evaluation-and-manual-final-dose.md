# Jar Test: การประเมินผลและ final dose manual

- วันที่: 2 ตุลาคม 2569
- สถานะ: บันทึกข้อยืนยันล่าสุดของเจ้าของโครงการ
- ขอบเขต: TO-BE ของ UMW2 เท่านั้น ไม่แก้ไข AS-IS ของ UMW เดิม

## คำยืนยันของเจ้าของโครงการ

1. UMW2 ต้องให้ผู้ดูแลเพิ่มและตั้งค่าข้อมูล Master/Site ที่จำเป็นผ่าน UI ได้ หากข้อมูลจริงยังไม่พร้อม
2. ตัดสูตรแนะนำ Pre-chlorine และด่างทับทิมออกจาก UMW2
3. เลือก Jar ที่ผ่านจากทุกรอบตามต้นทุนรวมต่ำสุด; เมื่อต้นทุนเท่ากัน ใช้ปริมาณสารเคมีน้อยกว่าเป็นตัวตัดสิน ตามด้วยรอบที่เก่ากว่าและหมายเลข Jar ที่น้อยกว่า
4. ไม่บังคับให้กรอกผลครบทุกพารามิเตอร์ที่ active เพราะ Site อาจใช้ฟอร์ม Global โดยยังไม่ได้ปรับรายการ; ค่าที่กรอกและผ่าน Bound ให้ถือว่าผ่าน. การตีความที่นำไปใช้คือ Jar ต้องมีผลที่กรอกอย่างน้อยหนึ่งรายการ มิฉะนั้นยังไม่มีข้อมูลให้ประเมิน
5. final dose แก้แบบ manual ได้เมื่อมี Jar ผ่านอย่างน้อยหนึ่งใบ โดยช่วงตรวจแยกต่อสารคือ dose ต่ำสุด–สูงสุดจาก Jar ที่ผ่านทุก round; ทุกสารอยู่ในช่วงเป็น `WITHIN_TESTED_RANGE` และค่านอกช่วงบันทึกได้เมื่อผู้ใช้ยืนยันเป็น `OUTSIDE_TESTED_RANGE_CONFIRMED`
6. เมื่อ final dose เป็น manual ให้คำนวณต้นทุนในหน้าสรุปใหม่จาก dose ที่เลือกกับ contract/ราคาที่ snapshot ในงาน
7. ค่า manual ที่ผ่านการตรวจช่วงไม่ใช่ผลวัดคุณภาพน้ำใหม่ และระบบต้องไม่แสดงว่า “ผ่านการทดลอง” โดยอัตโนมัติ

## ข้อเสนอที่ยังไม่ยืนยัน

- Global precision setting ได้รับการยืนยันภายหลัง ดู [บันทึก Global precision](2026-10-02-global-display-precision-settings.md)
- Team model: สร้างทีม เลือก user จากหลาย Site และกำหนดขอบเขตการมองเห็นหลาย Site/BU ได้อย่างอิสระ โดยต้องออกแบบแยกจากสิทธิ์การกระทำ

## เอกสารที่ปรับ

- [ข้อกำหนด Site Setting](../../../JarTest/jar-test-site-settings-requirements-th.md)
- [ADR-0003](../../decisions/ADR-0003-site-scoped-jar-test-settings.md)
- [ADR-0004](../../decisions/ADR-0004-jar-test-rounds-and-mixing-conditions.md)
- [Target domain model](../../architecture/TARGET-DOMAIN-MODEL.md)
- [Project State](../../../PROJECT-STATE.md)

## การอัปเดต Notion

อัปเดต Data Dictionary ใน Master Databases / Transaction Table (Draft) แล้วสำหรับ `jar_test_final_chemical_doses`, `jar_tests` และ `jar_test_results`: เพิ่ม `manual_range_check_status`, แก้กติกา manual dose ให้เป็นการตรวจช่วง/คำเตือนแทนผลแล็บ, ระบุต้นทุนสรุปที่คำนวณใหม่ และแก้การประเมินผล Jar ให้ใช้เฉพาะค่าที่กรอกจริง.
