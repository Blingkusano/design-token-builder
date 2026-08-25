# design-token-builder

Claude Skill ที่สัมภาษณ์ requirement แล้วสร้างชุด Design Token ตามมาตรฐาน W3C DTCG พร้อม HTML preview และสร้าง Variables กับหน้า Foundation ลง Figma ให้อัตโนมัติ

**เวอร์ชันปัจจุบัน:** 1.1.0 — ดูรายละเอียดที่ [CHANGELOG](design-token-builder/CHANGELOG.md)

## เปิดดูได้เลยจากเบราว์เซอร์

| หน้า | เนื้อหา |
|---|---|
| [วิธีใช้งาน](../../) | ขั้นตอนติดตั้งและใช้งานแบบทีละขั้น |
| [Token preview — sample](../../) | ตัวอย่างหน้า preview ที่ skill สร้างให้ (ค่าสมมติ) |
| [Skill audit report](../../) | ผลตรวจ SKILL.md ตามเกณฑ์ 12 ข้อ |

> ลิงก์ในตารางจะใช้งานได้หลังเปิด GitHub Pages แล้ว (Settings → Pages → Branch `main`, folder `/docs`) จากนั้นแทนที่ `../../` ด้วย URL ของ Pages

## โครงสร้าง repo

```
design-token-builder/      ตัว skill — ดาวน์โหลดทั้งโฟลเดอร์แล้วบีบเป็น .zip เพื่ออัปโหลดเข้า Claude
  SKILL.md                 คำสั่งหลัก
  CHANGELOG.md             ประวัติเวอร์ชัน
  reference/               ไฟล์อ้างอิง 6 ไฟล์ ขาดไม่ได้แม้แต่ไฟล์เดียว
docs/                      หน้าเว็บสาธารณะ เสิร์ฟผ่าน GitHub Pages
```

## ติดตั้งแบบย่อ

1. ดาวน์โหลดโฟลเดอร์ `design-token-builder/` แล้วบีบเป็น `.zip` โดยให้ **ตัวโฟลเดอร์เป็นตัวนอกสุด** ไม่ใช่บีบไฟล์ลอย ๆ
2. ที่ claude.ai เปิด **Settings → Capabilities → Code execution and file creation**
3. ไปที่ **Customize → Skills → + → Upload skill** แล้วเลือกไฟล์ `.zip`
4. Toggle เปิด แล้วต่อ **Figma connector** ที่ Settings → Connectors

ขั้นตอนเต็มพร้อมภาพรวมอยู่ในหน้า *วิธีใช้งาน*

## ผลลัพธ์ที่ได้

- **Token JSON** ตามมาตรฐาน W3C DTCG — ไฟล์ที่ส่งต่อให้ developer ได้จริง
- **HTML preview** สำหรับตรวจด้วยตาก่อนส่งขึ้น Figma
- **Figma Variables** สามคอลเลกชัน `Global` / `Color` / `Foundation` พร้อมหน้า Foundation ครบทุกหน้า (ถ้าเลือก)

## License

ยังไม่ได้กำหนด
