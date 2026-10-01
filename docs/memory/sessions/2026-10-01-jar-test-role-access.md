# บันทึกการตัดสินใจ: สิทธิ์ผู้ใช้โมดูล Jar Test

วันที่: 2026-10-01

ประเภท: คำยืนยันโดยตรงของเจ้าของโครงการ

## คำยืนยันล่าสุด

- `OPERATOR` เป็นผู้ปฏิบัติงาน Jar Test (นักวิทยาศาสตร์) จึงทำ flow ได้ครบ: สร้างงาน, กรอกคุณสมบัติน้ำดิบ, dose และผล Jar, เพิ่มรอบ, ตรวจผล, ปรับ final dose และ submit งาน
- `SUPERVISOR`, `MANAGER` และ `DIRECTOR` ใช้ Jar Test เพื่อดูข้อมูลเท่านั้น และดูได้ทุก Site ภายใต้ Business Unit ของบัญชี
- กลุ่มดูอย่างเดียวห้ามสร้างงาน แก้ไขข้อมูล เพิ่มรอบ ปรับ final dose หรือ submit
- `ADMIN` และ `SUPER_ADMIN` ไม่อยู่ในกลุ่มดูอย่างเดียว; รายละเอียดสิทธิ์ผู้ดูแลรายฟังก์ชันยังไม่ต้องกำหนดในรอบนี้

## ขอบเขตที่ยังต้องออกแบบ

- JT-SET-035 เก็บพฤติกรรมที่เจ้าของโครงการระบุสำหรับ Jar Test ในระดับ requirement ของโมดูล
- เจ้าของโครงการแจ้งว่ารายละเอียด User อาจต้องทบทวนภายหลัง จึงยังไม่สรุปการผูก role กับบัญชีจริง, scope ของ Operator, inheritance ของ role หรือ schema/กลไก permission สำหรับระบบ
- สิทธิ์ของ `SHIFT_LEADER` และ `OUTSOURCE` ยังไม่ได้รับการยืนยัน

## เอกสารเจ้าของข้อสรุป

- [`../../../JarTest/jar-test-site-settings-requirements-th.md`](../../../JarTest/jar-test-site-settings-requirements-th.md), JT-SET-035
- [`../../architecture/TARGET-DOMAIN-MODEL.md`](../../architecture/TARGET-DOMAIN-MODEL.md)
- [`../../../CONTEXT.md`](../../../CONTEXT.md)
