# บันทึกการคุย: ขอบเขต Unit และการเชื่อมกับ Site

วันที่จัดทำ: 2026-09-15

ประเภท: บันทึกจากคำยืนยันโดยตรงของเจ้าของโครงการในบทสนทนาปัจจุบัน

## เป้าหมายและสถานะก่อนเริ่ม

ก่อนการคุยครั้งนี้ เอกสารยืนยันแล้วว่า `wq_source` เป็นประเภทน้ำ และ Jar Test เลือก `water_source` จาก `raw_unit` แต่ยังไม่ทราบวิธีใช้ `raw_unit` ร่วมกันหลาย Site หรือขอบเขต Organization ของ unit ประเภทอื่น

## คำยืนยันของเจ้าของโครงการ

1. `raw_unit` คือรายการแหล่งน้ำดิบจริงที่อ้างถึงแม่น้ำ สระ หรือแหล่งน้ำดิบอื่น
2. หลาย Site ใช้ `raw_unit` เดียวกันได้ และ Site หนึ่งใช้ `raw_unit` ได้หลายรายการ
3. `raw_unit` ใช้ร่วมข้าม Organization ได้
4. `wq_source` เป็นตารางประเภทกลางคงที่ที่ทุกที่ใช้ร่วมกัน
5. `potable_unit`, `potable_transfer_unit`, `sedimentation_unit` และ `filtration_unit` ต้อง mapping กับ Site
6. Unit 4 ประเภทข้างต้นไม่ใช้ร่วมข้าม Organization แบบ `raw_unit`
7. ชื่อของ `sedimentation_unit` และ `filtration_unit` อาจเหมือนกันตามขนาด แต่หน่วยที่อยู่คนละ Site เป็นคนละสถานที่จริง

## ข้อสรุปที่นำไปใช้

- `wq_source` เป็น reference data ระดับระบบ
- `raw_unit` เป็น global master และสัมพันธ์กับ Site แบบ many-to-many ผ่าน mapping แม้ Site อยู่คนละ Organization
- Jar Test เลือกได้เฉพาะ `raw_unit` ที่ mapping กับ Site ของงาน
- `potable_unit`, `potable_transfer_unit`, `sedimentation_unit` และ `filtration_unit` มีขอบเขตภายใน Organization และต้อง mapping กับ Site ใน Organization เดียวกัน

การใช้ชื่อ `potable_transfer_unit` ในบันทึกนี้สะท้อนคำอธิบายของผู้ใช้เท่านั้น ชื่อ schema ถาวรยังไม่ตัดสินว่าจะคง `potable_tranfer_unit` ตามแบบเดิมหรือแก้การสะกด

## สิ่งที่ยังไม่ยืนยัน

- cardinality ของ mapping สำหรับ unit 4 ประเภทภายใน Organization
- mapping ต้องเก็บตัวแทนหน่วยจริง ชื่อเรียกเฉพาะ Site หมายเลขหน่วย หรือลำดับหรือไม่
- วิธีแยก `sedimentation_unit` และ `filtration_unit` ที่ชื่อหรือขนาดซ้ำกันใน physical schema
- effective dates, active status, metadata และสิทธิ์จัดการ mapping

## สิ่งที่ทำจริง

- ปรับ `AGENTS.md`, `CONTEXT.md`, `PROJECT-STATE.md`, Target Domain Model และ ADR-0001 ให้สอดคล้องกับคำยืนยันนี้
- เพิ่มบันทึกนี้ใน Project Memory และ Documentation Index
- ไม่แก้ legacy Jar Test baseline และไม่ออกแบบ physical schema ก่อนข้อมูล mapping ครบ

## ขั้นถัดไป

รับ Excel schema draft มาวิเคราะห์เทียบกับ [CONTEXT.md](../../../CONTEXT.md), [Target Domain Model](../../architecture/TARGET-DOMAIN-MODEL.md) และ [ADR-0001](../../decisions/ADR-0001-organization-business-unit-site-water-source.md) จากนั้นยืนยันรายละเอียด mapping ที่ยังเปิดอยู่
