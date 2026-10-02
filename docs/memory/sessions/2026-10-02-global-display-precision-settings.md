# Global display precision settings

- วันที่: 2 ตุลาคม 2569
- สถานะ: ข้อกำหนด TO-BE ที่เจ้าของโครงการยืนยันแล้ว

## ข้อสรุป

1. ใช้ `display_precision_settings` เป็น Global Master กลางสำหรับจำนวนทศนิยมและวิธีปัดเศษของทุกโมดูล
2. ลำดับการหา setting คือรายการย่อยของโมดูล → ค่าเริ่มต้นโมดูล → `ALL.DEFAULT`
3. เก็บและคำนวณค่าด้วยความละเอียดเต็มเสมอ; การปัดใช้เพื่อแสดงผลและ Export เท่านั้น
4. ค่าเริ่มต้น Jar Test: dose, stock concentration, ผลคุณภาพน้ำ และต้นทุนต่อ ลบ.ม. 3 ตำแหน่ง; ml และยอดเงินรวม 2 ตำแหน่ง; ใช้ `HALF_UP`
5. ผลคุณภาพน้ำ override จำนวนทศนิยมราย `wq_parameter` ได้

## สิ่งที่ยังเปิด

- Site override, history/version ของค่าตั้ง และรูปแบบรายงานเฉพาะเอกสาร

## อ้างอิง

- [ADR-0008](../../decisions/ADR-0008-global-display-precision-settings.md)
- [Target domain model](../../architecture/TARGET-DOMAIN-MODEL.md)
