# design-token-builder

Claude Skill ที่สัมภาษณ์ requirement แล้วสร้างชุด Design Token 2 ชั้น (global → semantic) สำหรับ Mobile App ตามมาตรฐาน W3C DTCG พร้อม HTML preview และสร้าง Variables กับหน้า Foundation ลง Figma ให้อัตโนมัติ

**เวอร์ชันปัจจุบัน:** 3.1.1 (MVP1 lean) — ดูรายละเอียดที่ [CHANGELOG](design-token-builder/CHANGELOG.md)

> **Breaking change จาก 1.x:** ตั้งแต่ 3.0.0 รองรับ 1 brand, 1 colour mode, Mobile App เท่านั้น และหยุดที่ Tier 2 — ไม่มี Dark mode, custom theme, Webview และ component token โครงสร้าง JSON จึงไม่เข้ากันกับไฟล์จาก 1.x ถ้ายังต้องใช้ความสามารถเหล่านี้ให้ใช้ [v1.1.1](https://github.com/Blingkusano/design-token-builder/tree/v1.1.1)

## เปิดดูได้เลยจากเบราว์เซอร์

| หน้า | เนื้อหา |
|---|---|
| [วิธีใช้งาน](https://blingkusano.github.io/design-token-builder/instruction.html) | ขั้นตอนติดตั้งและใช้งานแบบทีละขั้น |
| [Token preview — sample](https://blingkusano.github.io/design-token-builder/token-preview-sample.html) | หน้า preview จากรอบทดสอบ 3.1.0 (ชื่อโปรเจกต์ทดสอบ ไม่ใช่โปรเจกต์จริง) |
| [Skill audit report](https://blingkusano.github.io/design-token-builder/audit-report.html) | ผลตรวจ SKILL.md เวอร์ชัน 3.1.1 ตามเกณฑ์ 12 ข้อ |

## โครงสร้าง repo

```
design-token-builder/      ตัว skill — ดาวน์โหลดทั้งโฟลเดอร์แล้วบีบเป็น .zip เพื่ออัปโหลดเข้า Claude
  SKILL.md                 คำสั่งหลัก
  CHANGELOG.md             ประวัติเวอร์ชัน
  reference/               ไฟล์อ้างอิง 8 ไฟล์ ขาดไม่ได้แม้แต่ไฟล์เดียว
examples/                  ผลลัพธ์จากรอบทดสอบ (token JSON + HTML preview) ไม่ต้องใส่ใน zip ของ skill
docs/                      หน้าเว็บสาธารณะ เสิร์ฟผ่าน GitHub Pages
```

## ติดตั้งแบบย่อ

1. [ดาวน์โหลด .zip เวอร์ชันล่าสุด](https://github.com/Blingkusano/design-token-builder/releases/latest/download/design-token-builder-skill.zip) — หรือดาวน์โหลดโฟลเดอร์ `design-token-builder/` แล้วบีบเป็น `.zip` เอง โดยให้ **ตัวโฟลเดอร์เป็นตัวนอกสุด** ไม่ใช่บีบไฟล์ลอย ๆ
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
