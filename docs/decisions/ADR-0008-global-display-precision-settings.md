# ADR-0008: Global display precision settings

- Status: Accepted
- Date: 2026-10-02
- Scope: การแสดงผลและ Export ของทุกโมดูลใน UMW2

## Context

Jar Test มีตัวเลขหลายชนิดที่ต้องแสดงความละเอียดต่างกัน เช่น dose, ผลคุณภาพน้ำ, ปริมาตรสารละลาย และต้นทุน การกำหนดทศนิยมไว้ในแต่ละหน้าจอจะทำให้ดูแลยากและใช้ไม่สอดคล้องกับโมดูลในอนาคต

## Decision

1. สร้าง Global Master ชื่อ `display_precision_settings` ใช้ร่วมทุกโมดูล
2. หนึ่งรายการมี `module_code`, `setting_key`, `decimal_places`, `rounding_mode`, `is_active` และคำอธิบาย โดยใช้ `UNIQUE(module_code, setting_key)`
3. หา setting ตามลำดับ: รายการย่อยของโมดูล → ค่าเริ่มต้นโมดูล → `ALL.DEFAULT`
4. กำหนด `decimal_places` ได้ 0–6 และใช้ `HALF_UP` เป็นค่าเริ่มต้น
5. ระบบเก็บและคำนวณความละเอียดเต็มเสมอ; setting นี้ใช้เฉพาะการแสดงผลและ Export ไม่ใช้ตัดสิน Bound, pass/fail หรือคำนวณต้นทุน
6. ค่าเริ่มต้น Jar Test: dose, stock concentration, ผลคุณภาพน้ำ และต้นทุนต่อ ลบ.ม. 3 ตำแหน่ง; ปริมาตรสารละลาย ml และยอดเงินรวม 2 ตำแหน่ง; ผลคุณภาพน้ำ override ราย `wq_parameter` ได้

## Consequences

- ผู้ดูแลมีหน้าตั้งค่ากลางเพียงแห่ง และโมดูลใหม่ใช้ fallback เดียวกันได้
- ตัวเลขที่เห็นบนหน้าจออาจปัดต่างจากค่าภายใน แต่ผลการคำนวณและ Bound ยังคงใช้ค่าจริง
- Site override, version history ของค่าตั้ง และรูปแบบรายงานเฉพาะเอกสารยังไม่อยู่ในขอบเขตที่อนุมัติ

## Evidence

- คำยืนยันของเจ้าของโครงการวันที่ 2 ตุลาคม 2569
