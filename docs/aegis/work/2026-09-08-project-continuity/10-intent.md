# Project Continuity — Intent

## Requested outcome

สร้างชุดเอกสารใน project root ที่ทำให้เจ้าของโครงการ ผู้พัฒนา หรือ AI สามารถย้ายเครื่อง เปิด repository และทำงานต่อได้โดยไม่ต้องพึ่งความจำของบทสนทนาเดิม

## Scope

- จุดเริ่มอ่านและกติกาสำหรับ AI
- คำศัพท์กลางของโดเมน
- สถานะล่าสุด ข้อตกลง และ next step
- target domain model ที่ยืนยันแล้ว
- ADR สำหรับ Organization, Business Unit, Site และ Water Source
- เชื่อมโยง legacy Jar Test baseline เดิมโดยไม่เปลี่ยนเนื้อหา

## Non-goals

- ไม่สร้าง application code
- ไม่ออกแบบ physical database schema
- ไม่เปลี่ยน legacy baseline
- ไม่ initialize Git หรือ push GitHub
- ไม่ตัดสิน Open decisions แทนเจ้าของโครงการ

## BaselineReadSetHint

- `JarTest/legacy-jar-test-product-functional-spec-th.md`
- ข้อสรุปโดยตรงจากเจ้าของโครงการในบทสนทนาปัจจุบัน

## BaselineUsageDraft

- Required baseline refs: legacy Jar Test specification และคำยืนยันด้าน domain model
- Acknowledged before work: yes
- Cited by target docs: yes
- Missing refs: source repository และ approved database architecture ยังไม่มี
- Decision: continue โดยจำกัดงานที่ continuity documentation

## ImpactStatementDraft

- Affected layers: project documentation, terminology, architecture decision record, handoff protocol
- Canonical owners: `CONTEXT.md`, `PROJECT-STATE.md`, target architecture document และ ADR
- Preserved invariant: legacy baseline เป็น AS-IS และไม่ถูกแก้เพื่อให้ตรงกับ TO-BE
- Compatibility boundary: ไม่มี code หรือ runtime behavior ถูกเปลี่ยน
