# Water Source Clarification — Checkpoint

## TodoCheckpointDraft

- [x] รับคำอธิบายและภาพโครงสร้าง
- [x] ยืนยันว่าแต่ละประเภทเป็นตารางแยก
- [x] ยืนยันว่าแต่ละตารางมี `wq_source_id`
- [x] ยืนยันว่า Jar Test ใช้ `raw_unit`
- [x] ปรับ glossary, target model, ADR และ project state
- [x] เพิ่ม session memory
- [x] ตรวจความสอดคล้อง ลิงก์ และ legacy baseline
- [x] เตรียม commit และ push; ผลสำเร็จตรวจจาก Git history และ `origin/main`

## ResumeStateHint

เมื่อกลับมาคุย ให้อ่าน `docs/memory/sessions/2026-09-09-water-quality-source-structure.md` และเริ่มจากวิธีแชร์ `raw_unit` หนึ่งรายการระหว่างหลาย Site

## DriftCheckDraft

- Intent alignment: aligned
- Confirmed facts separated from open decisions: yes
- Legacy baseline changed: no
- Decision: pause-for-user

## Evidence refs

- `90-evidence.md`
- `99-reflection.md`

## Next step

เมื่อผู้ใช้กลับมา ให้ถามวิธีแชร์ `raw_unit` หนึ่งรายการระหว่างหลาย Site ก่อนตัดสิน schema
