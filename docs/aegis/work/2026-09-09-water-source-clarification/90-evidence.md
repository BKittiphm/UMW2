# Water Source Clarification — Evidence

## EvidenceBundleDraft

### Confirmed source

- Direct user confirmation: `wq_source` เป็นประเภท
- Direct user confirmation: ตารางประเภทแยกกันตามชื่อ
- Direct user confirmation: แต่ละตารางมี `wq_source_id`
- Direct user confirmation: ตัวอย่าง Site → ประเภท → หลายรายการตรงกับความหมาย
- Direct user statement: Jar Test ใช้เฉพาะน้ำดิบและ `water_source` เชื่อมกับแหล่งน้ำดิบ

### Documentation checks

- Markdown relative links: pass
- `git diff --check`: pass
- Active target documents contain no remaining assertion that Jar Test uses the old direct Site–Water Source many-to-many model
- Table name follows the supplied diagram as `potable_tranfer_unit`; corrected spelling remains an Open Decision

### Legacy baseline preservation

- File: `JarTest/legacy-jar-test-product-functional-spec-th.md`
- Working-tree diff: none
- SHA-256: `75D1956E9CF9398A2C19E286A2CD6FC65041F99CEB508079556235D88DABBE8F`

## Evidence assessment

- Covered scope: terminology, target conceptual model, existing ADR amendment, project state and session memory
- Uncovered scope: physical schema, exact cardinality, `raw_unit` sharing across Sites and ownership of `wq_source`
- Residual risk: the attached diagram is not stored in the repository; its confirmed meaning is preserved as text
- Confidence grade: A for recorded user confirmations; unknowns remain explicitly open
