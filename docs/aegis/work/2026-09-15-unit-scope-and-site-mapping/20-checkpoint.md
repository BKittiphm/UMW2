# Unit Scope and Site Mapping — Checkpoint

## Completed

- [x] ยืนยันว่า `wq_source` เป็นตารางประเภทกลางคงที่ระดับระบบ
- [x] ยืนยันว่า `raw_unit` ใช้ร่วมข้าม Organization ได้
- [x] ยืนยันความสัมพันธ์ Site–Raw Unit แบบ many-to-many ผ่าน mapping
- [x] ยืนยันขอบเขต Organization และการ mapping กับ Site ของ unit อีก 4 ประเภท
- [x] ปรับ glossary, project state, target model, ADR และ project memory

## Resume state

เมื่อกลับมาทำงานต่อ ให้รับและวิเคราะห์ Excel schema draft ก่อน จากนั้นยืนยัน cardinality และข้อมูลของ mapping สำหรับ unit ภายใน Organization

## Open decisions

- cardinality และข้อมูลประกอบของ mapping สำหรับ `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
- วิธีระบุหน่วยจริงเมื่อตารางตกตะกอนหรือกรองมีชื่อหรือขนาดซ้ำกัน
- ชื่อถาวรของ `potable_tranfer_unit`

## Verification

- Legacy Jar Test baseline ไม่ถูกแก้ไข
- รายละเอียดการตรวจลิงก์และสถานะ Git จะบันทึกหลัง commit และ push
