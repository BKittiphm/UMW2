# Water Source Clarification — Intent

## Requested outcome

บันทึกความเข้าใจล่าสุดเรื่อง `wq_source`, ตารางประเภทน้ำ และการเลือกน้ำดิบของ Jar Test เพื่อให้ทำงานต่อใน session ถัดไปได้โดยไม่ย้อนกลับไปใช้โมเดลเดิม

## Scope

- ปรับศัพท์กลาง สถานะโครงการ target domain model และ ADR เดิม
- เพิ่มบันทึกการคุยและ checkpoint
- เก็บวิธีแชร์ `raw_unit` หลาย Site เป็น Open Decision
- commit และ push เอกสารที่แก้ไข

## Non-goals

- ไม่ออกแบบ physical database schema
- ไม่ตัดสิน cardinality หรือ ownership ที่ผู้ใช้ยังไม่ได้ยืนยัน
- ไม่แก้ legacy Jar Test baseline
- ไม่สร้าง application code

## BaselineReadSetHint

- `CONTEXT.md`
- `PROJECT-STATE.md`
- `docs/architecture/TARGET-DOMAIN-MODEL.md`
- `docs/decisions/ADR-0001-organization-business-unit-site-water-source.md`
- คำยืนยันและภาพโครงสร้างจากผู้ใช้ในวันที่ 2026-09-09

## BaselineUsageDraft

- Required baseline refs: อ่านครบ
- Missing refs: physical schema และรายละเอียด cardinality ยังไม่มี
- Method reference note: ไม่พบ `ADR-CREATION-GATE.md` และ `AEGIS_ADR_AUTO_BACKFILL.md` ใน Aegis package ที่ติดตั้ง จึงใช้ ADR owner เดิมของ project และตรวจ amendment/baseline sync โดยตรง
- Decision: continue เฉพาะข้อเท็จจริงที่ยืนยัน และ pause สำหรับคำถามที่ค้าง

## ImpactStatementDraft

- Affected layers: domain terminology, target architecture baseline, ADR, project state และ memory
- Corrected interpretation: `wq_source` เป็นประเภท; รายการจริงอยู่ในตารางประเภท; Jar Test ใช้ `raw_unit`
- Preserved boundary: Organization → Business Unit → Site และ legacy behavior
- Compatibility impact: documentation only
