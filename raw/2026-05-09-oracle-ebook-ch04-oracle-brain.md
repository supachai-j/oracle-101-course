---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch04.html
ingested_at: 2026-05-09
title: Oracle Brain
language: th
---

<div class="chapter-header">

# Oracle Brain

แผนที่ 5 ชั้นของ Oracle และบทบาทของ memory layer ในระบบจริง

</div>

<div class="section chapter-content">

Oracle ไม่ใช่แอปเดียว และไม่ใช่แค่เครื่องมือค้นหาไฟล์

Oracle คือระบบที่ทำให้คนกับ AI ทำงานต่อเนื่องกันได้มากขึ้น มีความจำร่วมกัน มีคำสั่งที่ใช้ซ้ำได้ และมีทางให้หลาย agent ทำงานด้วยกัน โดยไม่ต้องเริ่มจากศูนย์ทุกครั้ง

ถ้ามองแบบง่ายที่สุด Oracle มี 5 ชั้นใหญ่ ๆ

<figure class="figure-card oracle-map-figure">
<div class="mermaid">
flowchart TB Human[Human] --&gt; Agent[Agent + Skills] Maw[maw-js orchestration] --&gt; Agent Agent --&gt; MCP[Oracle MCP] UI[maw-ui / ui-oracle] --&gt; API[HTTP API] MCP --&gt; Memory[(arra-oracle-v3)] API --&gt; Memory Memory --&gt; SQLite[(SQLite + FTS5)] Memory --&gt; Vector[(LanceDB vectors)] Memory --&gt; Psi[psi vault] Books[Books + Guides] -. knowledge source .-&gt; Agent
</div>
<figcaption><strong>ภาพที่ 4.1: Oracle เป็นระบบ 5 ชั้น</strong> <span>Human ส่งงานให้ Agent; Agent เชื่อมกับ Skills และ Maw; Oracle MCP/HTTP API เชื่อมไปยัง memory, SQLite, LanceDB และ psi vault; UI เป็นเลนส์; Books เป็นแหล่งความรู้ ไม่ใช่ runtime.</span> <em>Oracle ทำให้ agent มีเครื่องมือ มีความจำ และมีทางทำงานเป็นทีม โดยแยก runtime, memory, UI และ documentation ออกจากกัน</em></figcaption>
</figure>

| ชั้นของระบบ | หน้าที่หลัก | ตัวอย่าง repo สำคัญ | คนใช้งานเห็นอะไร |
|----|----|----|----|
| Agent + Skills | ให้ AI ใช้คำสั่งและ workflow เดียวกัน | `arra-oracle-skills-cli` | คำสั่งอย่าง recap, learn, rrr, trace |
| Memory | เก็บและค้นหาความรู้ | `arra-oracle-v3` | ค้นหา learning, handoff, trace, retrospective |
| Orchestration | คุมหลาย agent ให้ทำงานเป็นทีม | `maw-js` | wake, sleep, capture, hey, done |
| UI | หน้าจอดูระบบและทำงานกับ Oracle | `maw-ui`, `ui-oracle` | dashboard, feed, forum, vector, schedule |
| Books + Guides | อธิบายวิธีคิดและวิธีใช้ | `multi-agent-orchestration-book`, `agents-that-remember`, `the-agent-bus` | คู่มือและตัวอย่างใช้งานจริง |

แกนของระบบคือความคิดนี้:

> Agent ไม่ควรจำทุกอย่างไว้ในหัวของตัวเอง เพราะ session จบแล้วความจำหาย

> Oracle จึงทำหน้าที่เป็น external brain ที่ agent หลายตัวใช้ร่วมกันได้

## 4.1 ชั้น Agent และ Skills

ชั้นแรกคือฝั่งที่ AI coding agent ใช้งานโดยตรง เช่น Claude Code, Codex, OpenCode, Cursor, Gemini CLI และ agent อื่น ๆ

เครื่องมือสำคัญคือ `arra-oracle-skills-cli`

มันทำหน้าที่ติดตั้ง skill ไปยังโฟลเดอร์ของแต่ละ agent เช่น Claude มีที่เก็บ skill ของ Claude, Codex มีที่เก็บ skill ของ Codex และ agent อื่นก็มีที่ของตัวเอง

ผลคือ agent หลายตัวสามารถใช้ workflow คล้ายกันได้ เช่น

- เริ่มงานด้วยการ recap ว่าตอนนี้อยู่ตรงไหน
- สำรวจ codebase ด้วย learn
- ค้นหาประวัติหรือบริบทด้วย trace
- สรุปบทเรียนหลังจบงานด้วย rrr
- ส่งต่อ context ให้ session ถัดไปด้วย handoff

Skills ทำให้วิธีทำงานไม่ผูกกับ agent ตัวเดียว ถ้าวันนี้ใช้ Claude แล้วพรุ่งนี้ใช้ Codex ระบบยังพอมีภาษาและขั้นตอนร่วมกัน

## 4.2 ชั้น Memory: หัวใจของ Oracle

ชั้น memory อยู่ใน `arra-oracle-v3`

นี่คือส่วนที่ทำให้ Oracle ต่างจาก command pack ทั่วไป เพราะมันไม่ได้แค่บอก agent ว่าต้องทำอะไร แต่มันเก็บสิ่งที่เคยเรียนรู้ไว้ด้วย

ในแผนที่ต้นน้ำจาก อาจารย์ Nat ระบุว่า `arra-oracle-v3` มีทั้ง MCP server, HTTP API, indexer, vault CLI, search, learn, stats, traces, inbox, forum, feed และ vector map

พูดแบบง่าย:

- MCP คือทางที่ agent คุยกับ Oracle
- HTTP API คือทางที่ UI และ service อื่นคุยกับ Oracle
- SQLite + FTS5 คือฐานค้นหาด้วย keyword
- LanceDB + embeddings คือฐานค้นหาแบบความหมาย
- psi vault คือที่เก็บไฟล์ความรู้ เช่น learning, retrospective, inbox และ trace

การค้นหาของ Oracle จึงไม่ได้มีแค่ว่า “เจอคำนี้ไหม” แต่ยังพยายามหา “เรื่องนี้เกี่ยวกับอะไร” ด้วย

ถ้า vector search ใช้ไม่ได้ ระบบยัง fallback กลับไปใช้ FTS ได้ นี่เป็นแนวคิดสำคัญของระบบจริง: ถ้าส่วนฉลาดล้ม ส่วนพื้นฐานต้องยังทำงานได้

## 4.3 psi vault: ความจำที่จับต้องได้

Oracle ใช้โครงสร้าง `psi` เป็นสมองแบบไฟล์

แนวคิดของมันเรียบง่าย: ความรู้ควรอ่านได้ ย้ายได้ และอยู่รอดนานกว่า session หนึ่งครั้ง

ตัวอย่างกลุ่มข้อมูลใน psi:

| พื้นที่                   | ใช้เก็บอะไร                                 |
|-----------------------|-------------------------------------------|
| inbox                 | handoff, note, งานที่ส่งต่อ                   |
| memory/learnings      | pattern และบทเรียนที่ค้นพบ                    |
| memory/retrospectives | สรุปหลังจบงาน                               |
| traces                | หลักฐานการตามรอย repo, commit, issue, file |
| active                | สถานะงานที่กำลังทำ                           |
| archive               | งานหรือข้อมูลที่จบแล้ว                          |

ข้อดีของการใช้ไฟล์คือ agent และคนอ่านได้เหมือนกัน ถ้าอยาก debug ก็เปิดไฟล์ดูได้ ไม่ต้องเชื่อกล่องดำ

## 4.4 MCP กับ HTTP API แยกหน้าที่กัน

ในแผนที่ต้นน้ำจาก อาจารย์ Nat มีจุดสำคัญหนึ่งข้อ: MCP และ HTTP API เป็นคนละ entrypoint

Agent มักคุยกับ Oracle ผ่าน MCP แบบ stdio ส่วน UI อย่าง Studio, Vector, Canvas, Feed, Forum และ Schedule คุยผ่าน HTTP API

นี่ทำให้ระบบแบ่งงานชัด:

- Agent ใช้ MCP เพื่อ search, learn, trace, handoff
- UI ใช้ HTTP API เพื่อแสดงผลและให้คนกดใช้งาน
- ระบบอื่นอย่าง maw สามารถคุยกับ Oracle ผ่าน API ได้

ค่า port หลักของ Oracle HTTP API คือ `47778`

แต่ในหนังสือควรอธิบายว่า port เป็นรายละเอียดการติดตั้ง ไม่ใช่แก่นของแนวคิด แก่นจริงคือ Oracle แยก “ทางคุยของ agent” กับ “ทางคุยของ UI” ออกจากกัน

## 4.5 UI เป็นเลนส์ ไม่ใช่สมอง

ในแผนที่ต้นน้ำจาก อาจารย์ Nat มี UI สองกลุ่มสำคัญ

กลุ่มแรกคือ `maw-ui` เป็น dashboard สำหรับดูและควบคุม maw เช่น session, pane, websocket, terminal และ fleet

กลุ่มที่สองคือ `ui-oracle` เป็น monorepo ของ UI ฝั่ง Oracle เช่น Studio, Vector, Canvas, Feed, Forum และ Schedule

สิ่งที่ควรจำคือ UI ไม่ใช่เจ้าของข้อมูลหลัก

UI เป็นเลนส์ที่มองเข้าไปในระบบ ข้อมูลจริงอยู่ใน Oracle memory, ไฟล์ config ของ maw, tmux session และ plugin runtime

เพราะฉะนั้นถ้า UI พัง ระบบหลักไม่ควรหาย และถ้าเปลี่ยน UI ข้อมูลเดิมยังควรใช้ต่อได้

## 4.6 Repo หนังสือเป็นผู้บริโภค ไม่ใช่ runtime

ในแผนที่ต้นน้ำจาก อาจารย์ Nat มีหนังสือและ guide หลาย repo เช่น

- `multi-agent-orchestration-book`
- `agents-that-remember`
- `the-agent-bus`

repo เหล่านี้ไม่ได้เป็น dependency ที่ต้องติดตั้งก่อน Oracle จะทำงาน

มันเป็นแหล่งอธิบายแนวคิด ตัวอย่าง และบทเรียนจากระบบจริง

ดังนั้นลำดับที่ถูกต้องคือทำ runtime ให้ทำงานก่อน แล้วค่อยใช้หนังสือเป็นแหล่งเรียนรู้ ไม่ใช่พยายามติดตั้งหนังสือเพื่อให้ระบบรัน

## Repo สำคัญในภาพรวม

ตารางนี้ใช้จำแบบเร็วว่า repo แต่ละตัวอยู่ตรงไหนของระบบ Oracle

| Repo                     | จำแบบง่าย                      |
|--------------------------|-------------------------------|
| `arra-oracle-v3`         | สมองและความจำ                 |
| `arra-oracle-skills-cli` | ตัวติดตั้ง skill ให้ agent         |
| `maw-js`                 | ตัวคุม agent หลายตัว             |
| `maw-ui`                 | หน้าจอดู maw                    |
| `ui-oracle`              | หน้าจอใช้งาน Oracle             |
| `maw-plugins`            | plugin เสริม                   |
| `maw-plugin-registry`    | รายชื่อ plugin ที่เชื่อถือได้         |
| `maw-studio`             | marketplace / หน้าแนะนำ plugin |

## สรุปบทที่ 04

Oracle ไม่ได้พยายามเป็น agent ที่ฉลาดที่สุด

มันพยายามทำให้การทำงานระหว่างคนกับ agent ต่อเนื่องที่สุด

เครื่องมือเปลี่ยนได้ UI เปลี่ยนได้ agent เปลี่ยนได้ แต่ memory, workflow และบริบทควรยังอยู่

นั่นคือเหตุผลที่ Oracle มีทั้ง skills, MCP, HTTP API, psi vault, maw, plugin และ UI อยู่ด้วยกันเป็นระบบเดียว

</div>

<a href="ch03.html" class="button secondary">← ติดตั้งจาก 0</a> <a href="ch05.html" class="button primary">Skills, Maw และ Plugin →</a>
