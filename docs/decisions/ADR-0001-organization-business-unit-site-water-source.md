# ADR-0001: Organization, Business Unit, Site and Water Source

- Status: Accepted
- Date: 2026-09-08
- Scope: Target domain model for UMW2

## Context

ระบบเดิมใช้คำว่า `กิจการประปา` ใน UI แต่ข้อมูลตัวอย่างบางรายการมีลักษณะเป็นสถานี เช่น `สถานีผลิต Head Office` หากนำป้าย UI เดิมไปสร้าง schema โดยตรง จะทำให้ Business Unit และสถานที่ปฏิบัติงานกลายเป็น entity เดียวกัน

โครงการใหม่ต้องรองรับการพัฒนาโมดูลหลายส่วน ใช้ Global Master ร่วมกัน และเตรียมรองรับหลาย Organization นอกจากนี้แหล่งน้ำหนึ่งแหล่งสามารถถูกใช้ร่วมกันหลาย Site ได้

## Decision

1. เตรียมระบบให้รองรับหลาย Organization
2. `กิจการประปา` เป็น Business Unit
3. `สถานี` เป็น Site และอยู่ภายใต้ Business Unit
4. หนึ่ง Business Unit มีหลาย Site ได้ แต่ Site หนึ่งอยู่ใน Business Unit เดียว
5. ใช้ `Site/สถานี` เป็นชื่อ canonical โดยตัดคำว่า `ผลิต` ออกจากชื่อ entity
6. Water Source เป็น Global Master ไม่ใช่ข้อมูลลูกที่เป็นของ Site เดียว
7. Site และ Water Source มีความสัมพันธ์แบบ many-to-many
8. การเลือก Water Source ใน Jar Test ต้องถูกจำกัดด้วยความสัมพันธ์ของ Site ที่เลือก

## Consequences

- ต้องมีแนวคิด relationship ระหว่าง Site และ Water Source ใน data model
- ไม่สร้าง Water Source ซ้ำเมื่อหลาย Site ใช้แหล่งเดียวกัน
- UI ต้องเลือกหรือทราบ Site ก่อนจึงตรวจรายการ Water Source ที่ใช้ได้
- รายงานสามารถแยก Organization, Business Unit, Site และ Water Source ได้อย่างถูกต้อง
- Physical schema จะต้องมี constraint ป้องกัน Jar Test อ้างคู่ Site–Water Source ที่ไม่ได้รับอนุญาต
- ต้องมีการตัดสินใจเพิ่มเติมว่า Global Master แยกตาม Organization หรือใช้ร่วมกันข้าม Organization

## Alternatives considered

### Water Source เป็นลูกของ Site

ไม่เลือก เพราะแหล่งน้ำหนึ่งแหล่งใช้ร่วมกันหลาย Site ทำให้ต้องสร้างข้อมูลซ้ำและเสี่ยงข้อมูลไม่ตรงกัน

### Business Unit และ Site เป็น entity เดียว

ไม่เลือก เพราะเจ้าของโครงการยืนยันว่าเป็นคนละระดับ และหนึ่ง Business Unit มีหลาย Site

### Water Source เป็นลูกของ Business Unit

ยังไม่ใช้เป็น canonical ownership เพราะอาจจำกัดการแบ่งปันข้าม Site หรือข้าม Business Unit ก่อนนโยบายดังกล่าวได้รับการตัดสินใจ ความสัมพันธ์ Site–Water Source แสดงสิทธิ์การใช้งานได้ตรงกว่า

## Unresolved follow-up decisions

1. Global Master เป็น global ข้าม Organization หรือ tenant-scoped
2. Water Source เดียวเชื่อม Site ที่อยู่คนละ Business Unit ได้หรือไม่
3. relationship ต้องมี effective dates, active status, default source หรือ Site-specific metadata หรือไม่
4. Jar Test จะอ้าง relationship record โดยตรงหรือใช้ composite constraint
