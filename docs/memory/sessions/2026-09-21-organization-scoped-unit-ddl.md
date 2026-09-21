# บันทึกการคุย: DDL ของ Unit ภายใน Organization

วันที่จัดทำ: 2026-09-21

ประเภท: คำยืนยันโดยตรงของเจ้าของโครงการและการอัปเดตเอกสาร DDL ใน Notion

## เป้าหมายและสถานะก่อนเริ่ม

ตรวจ Notion `Master Databases` แล้วพบว่า Data Dictionary ของ `potable_unit` และ `potable_transfer_unit` มีคอลัมน์มากกว่า DDL ที่แสดงอยู่ และยังไม่มี `organization_id` ทั้งที่ unit สองประเภทนี้ไม่ใช้ข้าม Organization แบบ `raw_unit`

## คำยืนยันของเจ้าของโครงการ

1. เพิ่ม `organization_id` ให้ `potable_unit`
2. เพิ่ม `organization_id` ให้ `potable_transfer_unit`
3. อัปเดต DDL ของสองตารางให้ตรงกับ Data Dictionary
4. เจ้าของโครงการจะจัดการ `site_sedimentation_units` เอง จึงไม่แก้หน้าดังกล่าว
5. `filtration_subunits.unit_no` เป็นหมายเลขใน Master กลาง ไม่ใช่หมายเลขที่สร้างใหม่แยกต่อ Site และ `UNIQUE` ระดับ global ถูกต้อง

## สิ่งที่ทำจริง

- อัปเดตหน้า Notion [potable_unit](https://app.notion.com/p/3d4127d4fb38807b8a4fd2a6719e7997)
  - เพิ่ม `organization_id` ใน Data Dictionary
  - เพิ่ม `organization_id`, `wq_source_id`, `code`, `updated_at` ใน DDL
  - เพิ่ม unique constraint `(organization_id, code)` และดัชนีสำหรับ owner/source
- อัปเดตหน้า Notion [potable_transfer_unit](https://app.notion.com/p/3d4127d4fb38800c8ee5cb2097bf277f)
  - เพิ่ม `organization_id` ใน Data Dictionary
  - เพิ่ม `organization_id`, `wq_source_id`, `updated_at` ใน DDL
  - เพิ่ม unique constraint `(organization_id, name_en)` และดัชนีสำหรับ owner/source
- ตรวจซ้ำหลังบันทึกแล้วพบว่า DDL ทั้งสองหน้ามีคอลัมน์และ constraints ตามที่แก้ไข
- ไม่แก้ `site_sedimentation_units` และไม่แก้ legacy baseline

## ขอบเขตที่ยังเปิดอยู่

- การบังคับให้ Site mapping ของ unit ภายใน Organization เดียวกันทำได้ด้วย composite foreign key, trigger หรือ application validation ยังไม่ได้เลือก
- ค่า `organization_id` ของแถวตัวอย่างเดิมใน Notion ยังต้องเติมตามเจ้าของข้อมูลจริงก่อนใช้เป็น seed
- DDL ของ `sedimentation_unit` และ `filtration_unit` ยังต้องตรวจและปรับให้สอดคล้องกับข้อสรุปเดียวกันตามลำดับงาน

## ขั้นถัดไป

รอเจ้าของโครงการจัดการ `site_sedimentation_units` แล้วจึงทบทวน mapping และ tenant-isolation strategy ก่อนออกแบบ physical PostgreSQL schema สุดท้าย
