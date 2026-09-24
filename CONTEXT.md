# UMW2 Domain Context

คำศัพท์กลางสำหรับระบบ UMW2 เพื่อให้เจ้าของโครงการ ผู้พัฒนา และ AI ใช้คำเดียวกันโดยไม่สับสนระหว่างโครงสร้างองค์กร สถานที่ และข้อมูลอ้างอิง

## Language

**Organization**:
ขอบเขตองค์กรระดับสูงสุดของระบบ ระบบใหม่ต้องเตรียมรองรับมากกว่าหนึ่ง Organization แม้การใช้งานเริ่มต้นจะอยู่ภายในองค์กรเดียว

**Business Unit (กิจการประปา)**:
หน่วยงานด้านกิจการประปาภายใน Organization หนึ่ง Business Unit มีได้หลาย Site
_Avoid_: ใช้แทน Site หรือสถานี

**Site (สถานี)**:
สถานที่ปฏิบัติงานที่อยู่ภายใต้ Business Unit หนึ่งแห่ง
_Avoid_: สถานีผลิต (สำหรับชื่อ canonical ของระบบใหม่), กิจการประปา

**WQ Source (`wq_source`)**:
ประเภทแหล่งน้ำกลางคงที่ระดับระบบที่ผู้ใช้เลือกเพื่อให้ระบบแสดงรายการจากตารางของประเภทนั้น `wq_source` ไม่ใช่รายการแหล่งน้ำจริง
_Avoid_: ใช้แทนรายการใน `raw_unit`, `potable_unit` หรือตารางประเภทอื่น

**Raw Unit (`raw_unit`)**:
รายการแหล่งน้ำดิบจริง เช่น แม่น้ำหรือสระ อยู่ภายใต้ประเภทน้ำดิบ เชื่อมกลับไปยัง `wq_source` ใช้ร่วมกันข้าม Organization และเป็นแหล่งข้อมูลที่ Jar Test ใช้เลือก `water_source`

**Site–Unit Mapping**:
ความสัมพันธ์ที่กำหนดว่ารายการจากตาราง unit ใดใช้ได้ที่ Site ใด สำหรับ `raw_unit` mapping ข้าม Organization ได้; สำหรับ `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit` mapping ต้องอยู่ใน Organization เดียวกัน ปัจจุบัน DDL draft ของ `potable_unit` และ `potable_transfer_unit` ระบุ `organization_id` เป็นเจ้าของรายการโดยตรง

`site_raw_units` เป็น mapping ระหว่าง Site กับ `raw_unit` กลาง จึงไม่เก็บ `organization_id` ซ้ำในแถว mapping; ขอบเขต Organization อนุมานผ่าน `site → business_unit → organization` ส่วนรายละเอียดเฉพาะจุดอยู่ที่ `custom_name`, `capacity` และ `capacity_uom_id`

**Water Source ใน Jar Test**:
ชื่อเชิงฟังก์ชันของรายการน้ำดิบที่เลือกจาก `raw_unit` ไม่ได้หมายถึงประเภท `wq_source` หรือรายการน้ำทุกประเภทในองค์กร

**Global Master**:
ข้อมูลอ้างอิงกลางที่ออกแบบให้หลาย Site และหลายโมดูลใช้งานร่วมกัน ขอบเขตการแบ่งข้อมูลระหว่าง Organization ยังต้องตัดสินใจ

**Jar Test**:
กระบวนการทดสอบเพื่อหาอัตราจ่ายสารเคมีที่เหมาะสมกับตัวอย่างน้ำ โดยบันทึกข้อมูลน้ำดิบ สารเคมี รอบทดลอง ผลคุณภาพ การประเมินด้วย Bound และผลสรุป

**Jar Test Site Settings**:
ชุดค่าตั้ง Jar Test ระดับ Site สำหรับกำหนดพารามิเตอร์และหน่วยของคุณสมบัติน้ำดิบ พารามิเตอร์ผลทดสอบและ Bound รวมถึงสารเคมีและราคา ใช้กับ raw_unit ที่ mapping กับ Site นั้น เงื่อนไขการกวนและตกตะกอนเป็นข้อมูลของการทดลองแต่ละครั้ง ไม่ใช่ Site Setting

**Bound**:
เกณฑ์ต่ำสุดหรือสูงสุดที่ใช้ประเมินผลคุณภาพน้ำเป็นผ่านหรือไม่ผ่าน ระบบเป็นผู้คำนวณสถานะจาก Bound โดยใช้ขอบเขตแบบ inclusive: ค่าที่เท่ากับขอบล่างหรือขอบบนถือว่าผ่าน `lower_bound` ที่เป็น `NULL` หมายถึง 0, `upper_bound` ที่เป็น `NULL` หมายถึงไม่มีเพดานบน และห้ามให้ทั้งสองค่าเป็น `NULL`

**Test Round (รอบทดสอบ)**:
ชุดการทดลอง Jar Test หนึ่งรอบ ซึ่งในระบบเดิมประกอบด้วย Jar 1 ถึง Jar 6

**Jar**:
ภาชนะทดลองหนึ่งใบใน Test Round ซึ่งมีอัตราจ่ายสารเคมี ผลคุณภาพ และผลผ่านหรือไม่ผ่านของตนเอง

**Stock Concentration**:
ความเข้มข้นของสารละลายตั้งต้นที่ใช้ร่วมกับ target dose ในการคำนวณตามหลัก C1V1 = C2V2

## Relationships

- Jar Test Site Settings กำหนดแยกตาม Site และใช้กับ raw_unit ทุกแหล่งที่ mapping กับ Site นั้น
- งาน Jar Test เก็บสำเนาค่าตั้งที่ใช้ไว้ตรวจย้อนหลัง
- เกณฑ์ Bound ปัจจุบันมีได้หนึ่งชุดต่อ Site, Parameter และ Parameter Type; การแก้เกณฑ์มีผลกับงานที่ยังไม่ submit เท่านั้น งานที่ submit แล้วคงใช้ snapshot เดิม และต้องสร้าง Jar Test ใหม่หากต้องการใช้เกณฑ์ใหม่
- เงื่อนไขการกวนและตกตะกอนบันทึกเป็นข้อมูลของการทดลองแต่ละครั้ง ไม่ใช่ Site Setting และไม่ใช้คำนวณ dose หรือผลผ่าน/ไม่ผ่านตามข้อกำหนดที่อนุมัติ
- หนึ่ง **Organization** มีหลาย **Business Unit**
- หนึ่ง **Business Unit** อยู่ใน Organization เดียวและมีหลาย **Site**
- หนึ่ง **Site** อยู่ใน Business Unit เดียว
- **WQ Source** เป็นตารางกลางคงที่ที่ทุก Organization และ Site ใช้ร่วมกัน
- **WQ Source** แต่ละประเภทแสดงรายการจากตารางแยก ได้แก่ `raw_unit`, `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` หรือ `filtration_unit`
- แต่ละรายการในตารางประเภทเชื่อมกลับไปยัง **WQ Source** ด้วย `wq_source_id`
- หนึ่ง **Site** ใช้ **Raw Unit** ได้หลายรายการ และ **Raw Unit** หนึ่งรายการเชื่อมกับหลาย Site ได้ แม้อยู่คนละ Organization
- **Potable Unit**, **Potable Transfer Unit**, **Sedimentation Unit** และ **Filtration Unit** ต้องเชื่อมกับ Site ผ่าน mapping ภายใน Organization เดียวกัน
- DDL draft ของ **Potable Unit** และ **Potable Transfer Unit** ต้องมี `organization_id`; `raw_unit` ยังคงเป็น Global Master และไม่ใช้ `organization_id`
- **Jar Test** ใช้เฉพาะประเภทน้ำดิบและเลือกรายการ **Raw Unit** ที่มี mapping กับ Site ของงาน
- หนึ่ง **Test Round** มี Jar จำนวน 6 ใบตาม legacy baseline

## Resolved Ambiguities

- Jar Test Setting เป็นฟังก์ชัน TO-BE ที่ตั้งค่าแยกตาม Site; ใช้กับ raw_unit ที่ mapping กับ Site และไม่แทนที่ Site-Raw Unit mapping
- Jar Test Site Settings ครอบคลุมพารามิเตอร์/หน่วยน้ำดิบ พารามิเตอร์ผลทดสอบ/Bound และสารเคมี/ราคา; เงื่อนไขการกวนและตกตะกอนเป็นข้อมูลต่อการทดลอง
- Bound ใช้การเปรียบเทียบแบบ inclusive; `lower_bound = NULL` คือ 0, `upper_bound = NULL` คือไม่มีเพดานบน และห้าม Bound ว่างทั้งคู่
- งาน Jar Test ต้องเก็บสำเนาค่าตั้งที่ใช้ และห้ามใช้ค่าตัวอย่างจาก MAMIS เป็นค่าเริ่มต้นของ UMW2

- `กิจการประปา` และ `สถานี` เป็นคนละระดับ
- `Business Unit` คือกิจการประปา ส่วน `Site` คือสถานีภายใต้กิจการประปา
- คำ canonical ของระบบใหม่คือ `สถานี`; คำว่า `สถานีผลิต` คงไว้เฉพาะเมื่ออ้างข้อความจากระบบเดิม
- `wq_source` เป็นประเภท ส่วนรายการแหล่งน้ำจริงอยู่ในตารางแยกตามประเภท
- `water_source` ของ Jar Test คือรายการจาก `raw_unit` ไม่ใช่ `wq_source` และไม่รวมแหล่งน้ำประเภทอื่น
- `wq_source` เป็นข้อมูลกลางคงที่ระดับระบบ
- `raw_unit` ใช้ร่วมข้าม Organization ได้ผ่าน mapping กับ Site
- `site_raw_units` เป็น mapping ของ Site–Raw Unit ที่ไม่ต้องมี `organization_id`; ร่าง DDL ใช้ `capacity_uom_id`, FK ไป `users(id)` สำหรับ audit และแยก unique กรณี `custom_name` เป็น NULL/ไม่เป็น NULL
- Unit อีก 4 ประเภทไม่ใช้ร่วมข้าม Organization
- `potable_unit` และ `potable_transfer_unit` ต้องมี `organization_id` ใน DDL draft เพื่อระบุ Organization เจ้าของรายการ
- `filtration_subunits.unit_no` เป็นหมายเลขของ Master กลาง จึงไม่ซ้ำระดับ global; ความสัมพันธ์กับ Site อยู่ที่ `site_filtration_subunits`

## Open Ambiguities

- cardinality ที่แน่นอนและข้อมูลประกอบของ mapping สำหรับ `potable_unit`, `potable_tranfer_unit`, `sedimentation_unit` และ `filtration_unit`
- แต่ละ mapping ต้องระบุหน่วยจริง ชื่อเรียกเฉพาะ Site หรือลำดับหน่วยหรือไม่
- ชื่อ `potable_tranfer_unit` ที่ปรากฏในแบบข้อมูลจะคงการสะกดเดิมหรือเปลี่ยนเป็น `potable_transfer_unit`
- ตัวเลือกที่ระบบเดิมติดป้ายว่า `กิจการประปา` แท้จริงเป็น Business Unit, Site หรือค่าผสมของทั้งสองระดับ
