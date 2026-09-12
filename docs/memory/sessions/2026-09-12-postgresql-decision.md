# บันทึกการตัดสินใจ: PostgreSQL เป็นฐานข้อมูลหลัก

วันที่จัดทำ: 2026-09-12

ประเภท: บันทึกจากคำยืนยันโดยตรงของเจ้าของโครงการในบทสนทนาปัจจุบัน

## เป้าหมายและสถานะก่อนเริ่ม

เจ้าของโครงการสอบถามฐานข้อมูลที่เหมาะกับ Jar Test หลังจากพิจารณาความเป็นไปได้ของ Power Apps, Dataverse และ Power Automate โดยทิศทางของ UMW2 คือพัฒนาอีกหลายโมดูลและใช้ Global Master ร่วมกัน

ก่อนการยืนยันครั้งนี้ โครงการยังไม่มี technology stack หรือฐานข้อมูลที่ได้รับอนุมัติ

## คำยืนยันของเจ้าของโครงการ

เจ้าของโครงการเลือก PostgreSQL เพราะจะพัฒนาโมดูลอื่นเพิ่มอีกหลายส่วน และสั่งให้บันทึกการตัดสินใจเป็น ADR

## สิ่งที่บันทึกแล้ว

- สร้าง [ADR-0002](../../decisions/ADR-0002-postgresql-as-primary-database.md) กำหนด PostgreSQL เป็นฐานข้อมูลหลักและ source of truth ของ UMW2
- ปรับ [Target Domain Model](../../architecture/TARGET-DOMAIN-MODEL.md) และ [Project State](../../../PROJECT-STATE.md) ให้สะท้อน baseline ปัจจุบัน
- ไม่แก้ legacy Jar Test specification เพราะการเลือกฐานข้อมูลเป็น TO-BE decision ไม่ใช่พฤติกรรมระบบเดิม

## ขอบเขตที่ยังไม่อนุมัติ

- Physical schema และ naming convention
- วิธี tenant isolation สำหรับหลาย Organization
- Backend framework และ ORM/query layer
- PostgreSQL hosting และ deployment target
- Backup, restore, audit/history และ migration strategy

## ขั้นถัดไป

ทำต่อจาก Open Decision เดิมเรื่องการแชร์ `raw_unit` ระหว่างหลาย Site และ ownership ของ `wq_source` จากนั้นจึงกำหนด tenant isolation ก่อนออกแบบ physical PostgreSQL schema

