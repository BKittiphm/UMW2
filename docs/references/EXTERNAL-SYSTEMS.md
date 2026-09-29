# External System References

เอกสารนี้เป็นทะเบียนเว็บและแอปที่ใช้ศึกษาระบบเดิมหรือใช้เป็นต้นแบบประกอบการออกแบบ UMW2 โดยแยกบทบาทของหลักฐานและขอบเขตที่อนุญาตให้ทดลองไว้อย่างชัดเจน

> ห้ามบันทึก username, password, session token, cookie หรือ secret ใด ๆ ลงไฟล์นี้หรือไฟล์อื่นที่ Git ติดตาม ให้เก็บข้อมูลเข้าสู่ระบบไว้เฉพาะเครื่องใน `.codex/local/external-system-access.md`

## ความหมายของบทบาทอ้างอิง

- **AS-IS baseline** คือหลักฐานว่าระบบที่ใช้อยู่ทำงานอย่างไรในปัจจุบัน ใช้ยืนยันพฤติกรรมเดิม แต่ไม่ใช่ข้อกำหนดว่าระบบ UMW2 ต้องลอกทุกอย่าง
- **Comparison reference** คือระบบเปรียบเทียบที่ใช้หาแนวคิดหรือช่องว่างของฟังก์ชัน ข้อมูลจากระบบนี้จะกลายเป็นข้อกำหนด UMW2 ได้ต่อเมื่อเจ้าของโครงการยืนยันแล้วเท่านั้น
- ข้อกำหนด TO-BE ที่อนุมัติ, ADR ที่ Accepted และคำยืนยันล่าสุดของเจ้าของโครงการมีอำนาจเหนือข้อสังเกตจากหน้าจอทุกระบบ

## 1. UMW เดิม

| รายการ | รายละเอียด |
| --- | --- |
| บทบาท | AS-IS baseline ของระบบ UMW เดิม |
| หน้าหลัก | <https://ew.umworkflow.net/umwwebapp/home> |
| Jar Test | <https://ew.umworkflow.net/umwwebapp/jartest> |
| ขอบเขตที่อนุญาตให้สร้างหรือแก้ข้อมูลทดสอบ | `สถานีผลิต Head Office` เท่านั้น |
| เอกสารสรุปหลักฐาน | [`../../JarTest/legacy-jar-test-product-functional-spec-th.md`](../../JarTest/legacy-jar-test-product-functional-spec-th.md) |
| Access profile ในไฟล์ local | `UMW legacy` |

ใช้ระบบนี้เพื่อยืนยัน workflow, field, validation, calculation และพฤติกรรมของ UMW เดิมเท่านั้น หากพบแนวทางอัปเกรดให้เขียนเป็น TO-BE requirement แยกจาก legacy baseline

## 2. MAMIs

| รายการ | รายละเอียด |
| --- | --- |
| บทบาท | Comparison reference สำหรับหาแนวคิดอัปเกรด Jar Test |
| Dashboard | <https://mamis.uu.co.th/dashboard> |
| รายการงาน | <https://mamis.uu.co.th/job> |
| สร้างงาน Jar Test | <https://mamis.uu.co.th/job/create-job-jartest> |
| ดำเนินการ Jar Test | <https://mamis.uu.co.th/job-operator/conduct-jartest> |
| Records | <https://mamis.uu.co.th/record> |
| ขอบเขตที่อนุญาตให้ทดลอง | `BU ALD / source AAA` เท่านั้น |
| Access profile ในไฟล์ local | `MAMIs` |

หัวข้อที่เคยใช้ศึกษา ได้แก่ Jar Test Setting, Test Result Parameter, คุณสมบัติน้ำดิบ, การเพิ่ม Property, เงื่อนไขกวนผสม/ตกตะกอน, การเลือกสารเคมี, dose และผลทดสอบ ข้อสังเกตเหล่านี้เป็นเพียงข้อมูลเปรียบเทียบจนกว่าจะมีคำยืนยันให้ใช้กับ UMW2

## กติกาการเข้าไปศึกษาและทดลอง

1. เริ่มจากการอ่านหรือสังเกตหน้าจอก่อนเสมอ
2. ก่อนกดบันทึก ให้ตรวจระบบและขอบเขตที่เลือกอีกครั้ง:
   - UMW เดิม: `สถานีผลิต Head Office`
   - MAMIs: `BU ALD / source AAA`
3. ห้ามสร้าง แก้ ลบ หรือทดลองกับข้อมูลนอกขอบเขตข้างต้น
4. ห้ามทำ destructive action แม้อยู่ในขอบเขตทดสอบ เว้นแต่เจ้าของโครงการสั่งโดยตรงและระบุเป้าหมายชัดเจน
5. บันทึกการทดลองที่เปลี่ยนข้อมูลทุกครั้งใน [`EXTERNAL-SYSTEM-EXPLORATION-LOG.md`](EXTERNAL-SYSTEM-EXPLORATION-LOG.md) โดยไม่ใส่รหัสผ่าน ข้อมูลส่วนบุคคล หรือข้อมูลลับ
6. หาก browser มี session ที่ login อยู่แล้ว ให้ใช้ session นั้นได้ แต่ห้ามคัดลอก cookie หรือ token ออกมาเก็บในเอกสาร
7. หากหน้าจอหรือสิทธิ์ไม่ตรงกับเอกสารนี้ ให้หยุดก่อนเปลี่ยนข้อมูลและขอคำยืนยันจากเจ้าของโครงการ

## การเก็บข้อมูลเข้าสู่ระบบ

ไฟล์สำหรับข้อมูลเข้าสู่ระบบเฉพาะเครื่องคือ:

`.codex/local/external-system-access.md`

โฟลเดอร์นี้ถูก ignore จาก Git จึงไม่ตามไปเครื่องอื่น ผู้ใช้ต้องกรอก credential เองในแต่ละเครื่อง หรือใช้ browser session ที่ login อยู่แล้ว ห้ามย้าย credential ผ่าน commit, issue, pull request หรือเอกสาร Notion ของโครงการ
