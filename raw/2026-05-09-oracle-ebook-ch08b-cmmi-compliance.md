---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch08b.html
ingested_at: 2026-05-09
title: CMMI & Compliance
language: th
---

<div class="chapter-header">

# CMMI & Compliance

บทที่ 08 อธิบาย Workflow UseCase จาก request ถึง done บทนี้ลงรายละเอียดเรื่องเอกสารกำกับ, QA gate และ traceability ที่เดินคู่กับ PR

</div>

<div class="section chapter-content">

## CMMI คืออะไร

CMMI (Capability Maturity Model Integration) คือ framework สำหรับวัดความสมบูรณ์ของกระบวนการพัฒนา software — ยิ่ง level สูง กระบวนการยิ่งเป็นระบบ วัดผลได้ และปรับปรุงต่อเนื่อง แบ่งเป็น 5 ระดับ:

| Level | ชื่อ                     | ความหมายแบบง่าย                        |
|-------|------------------------|---------------------------------------|
| 1     | Initial                | ทำได้แต่ไม่มีระบบ ขึ้นกับคน                  |
| 2     | Managed                | มี process พื้นฐาน plan/track/review     |
| 3     | Defined                | process เป็นมาตรฐานทั้งองค์กร มีเอกสารกำกับ |
| 4     | Quantitatively Managed | วัดผลด้วยข้อมูล metrics                   |
| 5     | Optimizing             | ปรับปรุงต่อเนื่องจาก data                  |

Oracle workflow ตั้งเป้า Level 3 — มี process มาตรฐาน มีเอกสาร `SRS`, `SDD`, `UAT` และ `RTM` กำกับทุก project

## Compliance Standards ที่เป็นประโยชน์

ไม่ใช่แค่ CMMI — การพัฒนา software ที่ดีควรรู้จักมาตรฐานเหล่านี้:

| Standard | เกี่ยวกับ | ใช้ตอนไหน |
|----|----|----|
| ISO/IEC/IEEE 29148 | Software Requirements (SRS) | เขียน requirement ให้ชัด ตรวจสอบได้ |
| IEEE 1016 | Software Design (SDD) | ออกแบบ architecture, component, data flow |
| IEEE 829 | Software Testing (UAT) | วางแผน test case ที่ human/QA อ่านได้ |
| WCAG 2.1/2.2 | Web Accessibility | ทำ web ให้คนพิการใช้ได้ contrast, keyboard, screen reader |
| OWASP Top 10 | Web Security | ป้องกัน injection, XSS, broken auth, CSRF |
| PDPA | Personal Data Protection | ปกป้องข้อมูลส่วนบุคคล (กฎหมายไทย) |
| SOC 2 | Security & Availability | audit ระบบ cloud/SaaS ด้าน security |
| ISO 25010 | Software Quality | วัดคุณภาพ: reliability, performance, usability, security |

Oracle ใช้ CMMI เป็นโครง แต่ combine กับ standards เหล่านี้ตามความเหมาะสมของแต่ละ project เช่น project ที่มี web UI ต้องดู WCAG + OWASP ด้วย

## 8B.1 ทำไมเอกสารต้องอยู่ใน PR เดียวกัน

ถ้า code เปลี่ยน behavior แต่เอกสารไม่เปลี่ยน ระบบจะเริ่มมีความจริงสองชุด: ความจริงใน production กับความจริงใน docs. Oracle workflow จึงใช้กฎง่าย ๆ คือ user-facing change ต้อง update docs ใน PR เดียวกัน

เอกสารไม่ใช่พิธีกรรม แต่เป็นทางให้คน, QA และ Oracle รุ่นถัดไปตอบได้ว่า requirement นี้มาจากไหน ออกแบบอย่างไร ทดสอบด้วยอะไร และ merge ใน PR ใด

## 8B.2 Doc-Update Mandate

| ประเภท PR | ต้อง update docs ไหม | เหตุผล |
|----|----|----|
| เพิ่ม/แก้ user-facing functionality | ต้อง | behavior, UI, API หรือ workflow เปลี่ยน |
| แก้ bug ที่ผู้ใช้เห็น | ต้อง | ต้องมี UAT หรือ regression note |
| เปลี่ยน data flow / integration | ต้อง | SDD และ RTM ต้องตามให้ทัน |
| refactor ที่ behavior ไม่เปลี่ยน | ยกเว้นได้ | ถ้าไม่มีผลต่อผู้ใช้หรือ architecture contract |
| typo, dependency bump, tests-only | ยกเว้นได้ | ถ้าไม่มี behavior change จริง |

ถ้าไม่แน่ใจว่าเข้าข่าย user-facing ไหม ให้ถือว่าเข้าข่ายก่อน แล้วใส่ note สั้น ๆ ใน PR ว่ามี doc impact หรือไม่มีเพราะอะไร

## 8B.3 Layout เอกสารต่อ project

| Path | มาตรฐาน / หน้าที่ | อัปเดตเมื่อ |
|----|----|----|
| `docs/SRS.md` | ISO 29148, requirement และ acceptance criteria | มี requirement ใหม่หรือ behavior เปลี่ยน |
| `docs/SDD.md` | IEEE 1016, design, API, component, data flow | architecture หรือ implementation contract เปลี่ยน |
| `docs/UAT.md` | IEEE 829, test case ที่ human/QA อ่านได้ | มี scenario ใหม่, bug fix, regression หรือ release gate |
| `docs/RTM.md` | Traceability matrix | ทุก requirement ที่โยงกับ code/QA/PR |
| `docs/README.md` | แผนที่เอกสารของ project | เมื่อเพิ่มเอกสารใหม่หรือเปลี่ยน flow การอ่าน |
| `build/docs/` | generated DOCX/PPTX/XLSX | สร้างเมื่อ Wind สั่งเท่านั้น และควร gitignored |

``` bash
scripts/init-project-docs.sh <project-name>
```

bootstrap script ควรสร้าง markdown skeleton ให้ครบ แต่ไม่ควรเดาข้อมูล business แทนเจ้าของงาน

## 8B.4 QA + docs gate

<div class="mermaid">

flowchart LR Dev\[Dev creates PR\] --\> Code\[Code changes\] Dev --\> Docs\[Docs update\<br/\>SRS / SDD / UAT / RTM\] Code --\> Kati\[Kati QA\] Docs --\> Kati Kati --\> Gate{PASS?} Gate --\>\|FAIL\| Bug\[BUG note\<br/\>same branch / same PR\] Bug --\> Dev Gate --\>\|PASS\| Review\[Gale architecture review\] Review --\> Decision\[Wind / project owner\<br/\>merge decision\]

</div>

เมื่อ Kati FAIL ให้กลับไป branch เดิม PR เดิม worktree เดิม ไม่เปิดงานใหม่โดยไม่จำเป็น เพราะ trace จะขาดและ RTM จะตามยาก

## 8B.5 RTM: เส้นด้ายที่โยงทุกอย่าง

RTM ไม่ต้องซับซ้อน แต่ต้องตอบได้ว่าหนึ่ง requirement ไปแตะส่วนไหนของระบบและถูกตรวจอย่างไร

| Column      | ตัวอย่าง            | คำอธิบาย                   |
|-------------|-------------------|---------------------------|
| REQ-id      | `REQ-WH-001`      | รหัส requirement ที่อ่านซ้ำได้  |
| SRS section | `SRS 2.1`         | requirement อยู่ตรงไหน      |
| SDD section | `SDD 4.3`         | design ที่รองรับ requirement |
| Code files  | `src/routes/...`  | ไฟล์หลักที่ implement         |
| UAT test ID | `UAT-014`         | test case ที่พิสูจน์ behavior  |
| PR SHA      | `PR #18 / abc123` | หลักฐาน merge หรือ review   |

``` text
REQ-id → SRS section → SDD section → code files → UAT test ID → PR SHA
```

## 8B.6 Mermaid mandate

diagram ในเอกสาร project ควรอยู่เป็น Mermaid code block ใน markdown เพื่อให้ PR review เห็น diff ได้ ไม่แยกเป็นไฟล์ภาพที่ตรวจยาก เช่น `.svg`, `.drawio` หรือ `.puml`

<div class="mermaid">

flowchart TB Markdown\[Markdown docs\] --\> Review\[Readable PR diff\] Markdown --\> Render\[Rendered docs site\] Separate\[Separate image files\] --\> HardDiff\[Hard to review\] HardDiff --\> Risk\[Design drift / stale diagrams\]

</div>

## 8B.7 Doc conversion boundary

| งาน | Autonomous? | เหตุผล |
|----|----|----|
| สร้าง/แก้ markdown docs ใน PR | ได้ | diff ได้ ตรวจได้ และใช้ token คุ้ม |
| ตรวจว่ามี SRS/SDD/UAT/RTM ครบไหม | ได้ | เป็น checklist ที่ deterministic |
| แปลง `md` เป็น DOCX/PPTX/XLSX | ไม่ได้ ถ้า Wind ไม่สั่ง | เปลือง token/เวลา และ output binary ตรวจ diff ยาก |
| เดา business requirement เอง | ไม่ได้ | ต้องให้มนุษย์หรือ source of truth ยืนยัน |

หลักจำง่าย: markdown เป็น source of truth; DOCX/PPTX/XLSX เป็น export artifact เมื่อมีคำสั่งเฉพาะ

## 8B.8 Checklist ก่อน merge

- PR ระบุว่าเป็น REQ, CR, BUG, QA, DOC หรือ deploy change
- ถ้า user-facing มี SRS/SDD/UAT/RTM ที่เกี่ยวข้องใน PR เดียวกัน
- UAT มี test case ที่ Kati หรือมนุษย์อ่านแล้วทำตามได้
- RTM โยง requirement ไปถึง code, test และ PR ได้
- diagram เป็น Mermaid ใน markdown ไม่ใช่ภาพแยก
- ไม่มี generated docs ที่ควรอยู่ใน `build/docs/` หลุดเข้า source tree
- ถ้าแปลงเอกสารเป็น DOCX/PPTX/XLSX ต้องมีคำสั่งจาก Wind ชัดเจน

## 8B.9 จุดจบบท

CMMI ทำให้งานอธิบายย้อนหลังได้ว่า “ทำไมถึงทำแบบนี้” และ “พิสูจน์แล้วอย่างไร” เมื่อ workflow มีเอกสารและ traceability แล้ว บทต่อไปจะขยับไป Autonomous Ops: ระบบที่ช่วยติดตาม, แจ้งเตือน และดูแล loop เหล่านี้ได้บางส่วน

</div>

<a href="ch08.html" class="button secondary">← Workflow UseCase</a> <a href="ch09.html" class="button primary">Autonomous Ops →</a>
