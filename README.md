# UMW2

โครงการสร้างระบบ Utilities Mobile Workflow รุ่นใหม่ โดยพัฒนาเป็นโมดูลทีละส่วนและเริ่มจาก **Jar Test**

## เริ่มต้นทำงาน

ผู้พัฒนาและ AI ทุกตัวต้องอ่านเอกสารตามลำดับนี้ก่อนเปลี่ยนแปลงโครงการ:

1. [AGENTS.md](AGENTS.md) — กติกาการทำงานและลำดับความน่าเชื่อถือของเอกสาร
2. [CONTEXT.md](CONTEXT.md) — คำศัพท์กลางและความสัมพันธ์ของโดเมน
3. [PROJECT-STATE.md](PROJECT-STATE.md) — สถานะล่าสุด สิ่งที่เสร็จแล้ว และงานถัดไป
4. [Legacy Jar Test specification](JarTest/legacy-jar-test-product-functional-spec-th.md) — พฤติกรรม Jar Test ของระบบเดิม (AS-IS)
5. [Target domain model](docs/architecture/TARGET-DOMAIN-MODEL.md) — โครงสร้างโดเมนของระบบใหม่ (TO-BE)
6. [Architecture decisions](docs/decisions/ADR-0001-organization-business-unit-site-water-source.md) — ข้อตกลงสถาปัตยกรรมที่อนุมัติแล้ว

สารบัญเอกสารทั้งหมดอยู่ที่ [docs/README.md](docs/README.md)

## ทำงานต่อจากอีกเครื่อง

```sh
git clone https://github.com/BKittiphm/UMW2.git
cd UMW2
```

เปิดโฟลเดอร์นี้ใน Codex หรือเครื่องมือ AI ที่ใช้ แล้วสั่ง:

> อ่าน AGENTS.md, CONTEXT.md และ PROJECT-STATE.md แล้วอ่านเอกสารอ้างอิงที่เกี่ยวข้อง สรุปข้อสรุปที่ยืนยันแล้วและประเด็นที่ยังเปิด ก่อนทำงานต่อจาก Immediate next step

หากมีโฟลเดอร์อยู่แล้ว ให้ commit งานที่ค้างก่อนและใช้ `git pull --ff-only` ก่อนเริ่มงาน ทุกครั้งก่อนย้ายเครื่องให้อัปเดต PROJECT-STATE.md แล้ว commit และ push ให้สำเร็จ

Git เก็บไฟล์และประวัติการแก้ไข ส่วนบทสนทนา session ล็อกอิน และเครื่องมือที่ติดตั้งต้องจัดการแยกบนแต่ละเครื่อง

## ขอบเขตปัจจุบัน

- เป้าหมายระยะยาวคือสร้างทั้งแอป โดยเพิ่มโมดูลใหญ่ทีละส่วน
- โมดูลแรกคือ Jar Test
- ระยะแรกต้องรักษาพฤติกรรมระบบเดิมให้ตรวจสอบเทียบกันได้
- ฟังก์ชันอัปเกรดและ database schema จะออกแบบแยกจาก legacy baseline
- ระบบใหม่เตรียมรองรับหลาย Organization และ Global Master ที่ใช้ร่วมกันหลายโมดูล

## สถานะการพัฒนา

ขณะนี้อยู่ในช่วงเก็บ requirement และออกแบบโดเมน ยังไม่มี application code หรือ database schema ที่อนุมัติแล้ว
