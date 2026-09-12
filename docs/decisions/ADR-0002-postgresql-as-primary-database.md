# ADR-0002: PostgreSQL as the Primary Database

- Status: Accepted
- Date: 2026-09-12
- Scope: Primary transactional database for UMW2

## Context

UMW2 จะพัฒนาเป็นระบบหลายโมดูล โดยเริ่มจาก Jar Test และมี Global Master ที่หลายโมดูลใช้ร่วมกัน ระบบต้องรองรับความสัมพันธ์ของข้อมูลหลายระดับ การทำธุรกรรม ความถูกต้องของข้อมูล การตรวจสอบย้อนหลัง และการเตรียมรองรับหลาย Organization ในอนาคต

มีการพิจารณา Dataverse สำหรับแนวทาง Power Apps + Power Automate และฐานข้อมูลเชิงสัมพันธ์อื่น แต่เจ้าของโครงการยืนยันว่าจะสร้างโมดูลเพิ่มเติมอีกหลายส่วนและเลือก PostgreSQL เป็นฐานข้อมูลหลัก เพื่อไม่ให้แกนข้อมูลของระบบผูกกับ Power Platform และเพื่อรองรับการขยายระบบระยะยาว

## Decision

1. ใช้ PostgreSQL เป็นฐานข้อมูลหลักและ source of truth สำหรับข้อมูลธุรกรรมและ Master Data ของ UMW2
2. Jar Test และโมดูลที่จะเพิ่มในอนาคตต้องออกแบบ schema บน PostgreSQL และใช้ Global Master ร่วมกันตามขอบเขตที่ได้รับอนุมัติ
3. การเลือก PostgreSQL ไม่ได้อนุมัติ physical schema, hosting provider, ORM, backend framework หรือ deployment target; เรื่องเหล่านี้ยังต้องตัดสินใจแยกต่างหาก
4. Logic ทางธุรกิจ เช่น `C1V1 = C2V2` และการประเมินผลด้วย Bound ต้องมีเจ้าของที่ชั้น Domain/Application และบังคับความถูกต้องด้วย transaction, constraint และ validation ที่เหมาะสม ไม่ซ่อน business logic ทั้งหมดไว้ใน database trigger
5. ข้อมูล Jar Test ที่ต้องตรวจสอบย้อนหลังควรเก็บ input สำคัญ ค่า Bound และเวอร์ชันกฎที่ใช้คำนวณตาม strategy ที่จะออกแบบภายหลัง

## Consequences

- ต้องออกแบบ Global Master และ schema ของแต่ละโมดูลให้ใช้ foreign key, constraint และ transaction ของ PostgreSQL อย่างเหมาะสม
- ต้องกำหนด tenant isolation สำหรับหลาย Organization ก่อนลง physical schema
- ต้องเลือกวิธี migration, backup, restore, audit/history และ high-availability ให้สอดคล้องกับ deployment target
- หากใช้ Power Apps หรือ Power Automate ในอนาคต ทั้งสองส่วนจะเป็น client/integration หรือ workflow รอบระบบ ไม่ใช่เจ้าของข้อมูลหลักแทน PostgreSQL เว้นแต่มี ADR ใหม่มาแทนที่
- ทีมต้องดูแล application API และ authorization layer แยกจากฐานข้อมูล

## Alternatives considered

### Microsoft Dataverse

เหมาะกับระบบภายในที่สร้างบน Power Apps และ Power Automate เป็นหลัก แต่ไม่เลือกเป็นฐานข้อมูลหลัก เพราะทิศทาง UMW2 คือระบบหลายโมดูลระยะยาวและต้องการลดการผูกแกนข้อมูลกับ Power Platform รวมถึงรักษาความยืดหยุ่นด้าน application stack และ deployment

### Microsoft SQL Server or Azure SQL

รองรับระบบเชิงสัมพันธ์ได้ แต่ยังไม่มีข้อกำหนดด้านโครงสร้างพื้นฐานหรือ license ที่ทำให้ต้องเลือก Microsoft SQL stack จึงไม่เลือกเป็นค่าเริ่มต้น

### SharePoint Lists

ไม่เลือก เพราะโครงสร้าง Jar Test และ Global Master ต้องการความสัมพันธ์ constraint และ transaction ที่เหมาะกับ relational database

## Unresolved follow-up decisions

1. รูปแบบ tenant isolation ของ Organization
2. Physical schema และ naming convention
3. Backend framework, data-access strategy และ ORM/query layer
4. PostgreSQL hosting และ deployment target
5. Backup, restore, retention และ disaster-recovery policy
6. Audit/history และ snapshot strategy ของ Jar Test
7. Migration strategy จากระบบเดิม

