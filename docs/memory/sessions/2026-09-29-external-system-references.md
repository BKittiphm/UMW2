# ระบบอ้างอิง UMW เดิมและ MAMIs

วันที่จัดทำ: 29 กันยายน 2569

## ขอบเขตและแหล่งข้อมูล

บันทึกนี้สรุปคำยืนยันโดยตรงของเจ้าของโครงการเรื่องบทบาทของเว็บอ้างอิง ขอบเขตที่อนุญาตให้ทดลอง และการจัดเก็บข้อมูลเข้าสู่ระบบสำหรับ AI หรือผู้พัฒนาที่มาทำงานต่อ

## คำยืนยันจากเจ้าของโครงการ

- `สถานีผลิต Head Office` เป็นขอบเขตทดสอบของ UMW เดิม
- `BU ALD / source AAA` เป็นขอบเขตทดสอบของ MAMIs
- ต้องการไฟล์ที่รวมเว็บอ้างอิงและรายละเอียดว่าใช้เป็นต้นแบบหรือใช้ศึกษาอะไร
- อนุมัติให้จัดทำเอกสารและแนวทางเก็บข้อมูลเข้าสู่ระบบต่อจากการหารือเรื่องความปลอดภัย

## ข้อสรุปที่นำไปใช้

- UMW เดิมมีสถานะเป็น AS-IS baseline ใช้ยืนยันพฤติกรรมระบบเดิม
- MAMIs มีสถานะเป็น comparison reference ใช้หาแนวคิดอัปเกรด แต่ไม่กลายเป็น requirement ของ UMW2 โดยอัตโนมัติ
- ข้อมูลเข้าสู่ระบบไม่เก็บใน Git ให้ใช้ `.codex/local/external-system-access.md` ซึ่งถูก ignore และมีเพียงช่องว่างให้ผู้ใช้กรอกเอง
- การทดลองที่เปลี่ยนข้อมูลต้องลงบันทึกใน `docs/references/EXTERNAL-SYSTEM-EXPLORATION-LOG.md`
- ห้ามสร้าง แก้ ลบ หรือทดลองนอกขอบเขตที่ได้รับอนุญาต และห้ามทำ destructive action

## สิ่งที่ทำจริง

- สร้าง `docs/references/EXTERNAL-SYSTEMS.md`
- สร้าง `docs/references/EXTERNAL-SYSTEM-EXPLORATION-LOG.md`
- เพิ่ม `.codex/local/` ใน `.gitignore` และสร้างไฟล์ access template แบบ local โดยไม่มี credential จริง
- อัปเดต `AGENTS.md`, `docs/README.md` และ `PROJECT-STATE.md` ให้ผู้ทำงานต่อค้นพบกติกานี้ได้

ไม่มีการเข้าเว็บหรือเปลี่ยนข้อมูลใน UMW เดิมและ MAMIs ระหว่างงานนี้

## ข้อมูลที่ยังขาด

- Username, password และ MFA note ของแต่ละระบบยังไม่ได้รับจากผู้ใช้ และตั้งใจไม่บันทึกลง repository
- หากต้องใช้เครื่องใหม่ ผู้ใช้ต้องกรอก credential ในไฟล์ local ใหม่หรือ login ผ่าน browser ด้วยตนเอง

## เอกสารที่ต้องอ่านต่อ

- [`../../references/EXTERNAL-SYSTEMS.md`](../../references/EXTERNAL-SYSTEMS.md)
- [`../../references/EXTERNAL-SYSTEM-EXPLORATION-LOG.md`](../../references/EXTERNAL-SYSTEM-EXPLORATION-LOG.md)
- [`../../../JarTest/legacy-jar-test-product-functional-spec-th.md`](../../../JarTest/legacy-jar-test-product-functional-spec-th.md)
