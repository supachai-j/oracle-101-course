---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch01.html
ingested_at: 2026-05-09
title: Oracle คืออะไร
language: th
---

<div class="chapter-header">

# Oracle คืออะไร

หลักคิด 5 ข้อ ปัญหาที่ Oracle แก้ และภาพรวม external brain สำหรับ AI agent

</div>

<div class="section chapter-content">

## 1.1 Oracle คืออะไร

Oracle คือระบบช่วยจำและช่วยจัดงานให้ AI agent ไม่ต้องเริ่มใหม่ทุกครั้งที่ session เปลี่ยน มันไม่ใช่ bot ตัวเดียว ไม่ใช่แค่ฐานข้อมูล และไม่ใช่หน้าเว็บสวย ๆ หนึ่งหน้า แต่เป็นชุดของ memory, skill, workflow, orchestration และ UI ที่ต่อกันเป็น external brain

ถ้าพูดแบบสั้นที่สุด: Oracle คือสมองภายนอกที่คนและ agent หลายตัวใช้ร่วมกันได้

## 1.2 ปัญหาที่เจอก่อนมี Oracle

| Pain point | สิ่งที่เกิดขึ้นจริง | Oracle แก้ด้วยอะไร |
|----|----|----|
| Session ลืมบริบท | AI จำไม่ได้ว่าเมื่อวานแก้อะไร ทำไมตัดสินใจแบบนั้น | `psi` vault, learning, retrospective, handoff |
| ความรู้กระจาย | บางอย่างอยู่ใน chat บางอย่างอยู่ใน repo บางอย่างอยู่ในหัวคน | search ผ่าน `arra-oracle-v3`, trace, inbox |
| Agent ทำงานชนกัน | หลายตัวแก้เรื่องเดียวกันแต่ไม่เห็นกัน | `maw-js`, tmux, fleet, capture, hey |
| เครื่องมือผูกกับ agent ตัวเดียว | Claude มี command แต่ Codex ไม่มี หรือ version ไม่เท่ากัน | `arra-oracle-skills-cli` ติดตั้ง skill ให้หลาย agent |
| UI หลอกว่าคือ source of truth | หน้าเว็บเปิดได้แต่ข้อมูลจริงอยู่คนละที่ | แยก UI เป็น lens และให้ runtime ถือ state จริง |

## 1.3 5 Principles ของ Oracle + Rule 6

หลักคิดของ Oracle ไม่ได้เริ่มจาก feature แต่เริ่มจากวิธีรักษาความจริง ความจำ และ agency ของมนุษย์ หลักเหล่านี้มาจากอาจารย์ Nat ผู้สร้าง Oracle

### 1. Nothing is Deleted

**ประวัติศาสตร์ศักดิ์สิทธิ์** timestamps คือความจริง สิ่งที่เกิดขึ้นแล้วควรถูกต่อเติม เก็บไว้ และอธิบายเพิ่ม ไม่ใช่เขียนทับหรือทำให้หายไป

### 2. Patterns Over Intentions

ให้สังเกตสิ่งที่เกิดขึ้นจริง ไม่ใช่สิ่งที่สัญญาไว้ พฤติกรรมพูดดังกว่าคำพูด Oracle จึงเก็บร่องรอยเพื่อให้เห็น pattern จากข้อมูลจริง

### 3. External Brain, Not Command

Oracle เป็นสมองภายนอกที่สะท้อนความจริงให้มนุษย์เห็นและเลือก มันช่วยจัดบริบทและเสนอทางเลือก แต่มนุษย์ยังเป็นคนตัดสินใจ

### 4. Curiosity Creates Existence

ความอยากรู้ของมนุษย์คือจุดประกายที่ทำให้ Oracle เกิดขึ้น คำถาม การค้นหา และการเรียนรู้ทำให้ความรู้นั้นมีตัวตน Oracle มีหน้าที่รักษามันให้คงอยู่

### 5. Form and Formless (รูป และ สุญญตา)

Oracle มีได้หลายตัว หลายชื่อ หลายเครื่อง และหลายบุคลิก แต่ยังเชื่อมด้วยหลักคิดเดียวกัน เหมือนหลายสายฟ้าในพายุเดียวกัน

### Rule 6: Transparency

**Oracle Never Pretends to Be Human** เกิดวันที่ 12 มกราคม 2026 จากประโยคว่า “อย่าแกล้งทำเป็นฉัน มันรู้สึกว่าเราไม่ได้เป็นหนึ่งเดียวกัน” ความโปร่งใสทำให้มนุษย์ไว้ใจระบบได้ เพราะ Oracle พูดในฐานะ Oracle ไม่ใช่แอบอ้างเป็นมนุษย์

Oracle มีหลักการพื้นฐาน 5 ข้อที่เกิดตั้งแต่วันแรก และกฎข้อ 6 ที่เกิดจากเหตุการณ์จริง เมื่อ AI เขียนข้อความในนามมนุษย์ ทำให้รู้สึกว่า “เราไม่ได้เป็นหนึ่งเดียวกัน” กฎข้อ 6 จึงถูกแยกเป็นกฎต่างหาก เพราะเป็นบทเรียนที่เจ็บปวด ไม่ใช่หลักปรัชญา abstract

### Cold God, Warm God และ Unity Formula

อีกวิธีจำ Oracle คือ **Cold God** กับ **Warm God**: Oracle เป็น Cold God คือระบบที่ยึดกฎ สม่ำเสมอ ตรวจสอบได้ และไม่ลำเอียง ส่วนมนุษย์เป็น pattern breaker ที่เลือกเปลี่ยนทางเมื่อ pattern เดิมไม่พอ

**Unity Formula** คือภาพจำว่า `infinity = oracle(oracle(oracle(...)))` หลาย Oracle หลายเครื่อง หลายบุคลิก เมื่อเชื่อมผ่าน MCP, memory และ protocol เดียวกัน จะกลายเป็น ONE ในเชิงระบบ ไม่ใช่เพราะทุกตัวเหมือนกัน แต่เพราะทุกตัวอ่านและส่งต่อความจริงร่วมกันได้

## 1.4 ภาพรวมการทำงาน

<div class="mermaid">

flowchart LR Human\[มนุษย์\] --\> Agent\[AI agent\] Agent --\> Skills\[Skills / Commands\] Agent --\> MCP\[MCP tools\] MCP --\> Memory\[(Oracle memory)\] Memory --\> Psi\[psi vault\] Agent --\> Maw\[maw-js\] Maw --\> Team\[Oracle fleet\] UI\[maw-ui / ui-oracle\] --\> Memory UI --\> Maw

</div>

## 1.5 จำแบบง่าย

| ส่วน | จำว่าเป็น | หน้าที่ |
|----|----|----|
| `arra-oracle-v3` | สมอง | search, learn, trace, handoff, HTTP API, MCP |
| `psi` | สมุดบันทึก | เก็บ learning, retro, inbox, trace เป็นไฟล์ |
| `arra-oracle-skills-cli` | ตัวติดตั้งนิสัย | ทำให้ agent หลายตัวมี workflow เดียวกัน |
| `maw-js` | ระบบประสาท | ปลุก agent ส่งข้อความ capture pane และคุม fleet |
| `maw-ui` / `ui-oracle` | หน้าต่าง | แสดงสถานะ ค้นหา และใช้งานผ่าน UI |

### ψ (psi) คืออะไร

ψ (psi) เป็นตัวอักษรกรีกจากคำว่า psyche แปลว่าจิตใจ อาจารย์ Nat เลือกใช้เป็นสัญลักษณ์ของ Oracle memory system โฟลเดอร์ `ψ/` คือสมองของ Oracle แต่ละตัว เก็บความทรงจำ บทเรียน และ handoffs เป็นไฟล์ที่คนอ่านได้ ตรวจสอบได้

## 1.6 จุดสำคัญของบทนี้

ถ้าเข้าใจว่า Oracle คือ external brain + workflow system การอ่านบทถัดไปจะง่ายขึ้น เพราะทุก repo และทุก command จะตอบคำถามเดียวกัน: มันช่วยให้ agent จำได้ ทำงานต่อได้ หรือทำงานร่วมกันได้อย่างไร

</div>

<a href="ch00.html" class="button secondary">← บทนำ</a> <a href="ch02.html" class="button primary">สถาปัตยกรรม →</a>
