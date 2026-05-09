---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch05.html
ingested_at: 2026-05-09
title: Skills, Maw และ Plugin
language: th
---

<div class="chapter-header">

# Skills, Maw และ Plugin

การทำงานเป็นทีมผ่าน maw-js, tmux, skill และ plugin runtime

</div>

<div class="section chapter-content">

<div class="mermaid">

sequenceDiagram participant Human participant Maw as maw-js participant Tmux as tmux pane participant Agent participant Oracle as Oracle memory Human-\>\>Maw: maw hey agent message Maw-\>\>Tmux: send keys Tmux-\>\>Agent: task appears in session Agent-\>\>Oracle: search / learn / handoff Human-\>\>Maw: maw capture agent Maw--\>\>Human: visible output

</div>

ภาพที่ 5.1: Maw เป็นศูนย์คุม agent — Maw ไม่ได้แทน agent แต่ทำให้ agent หลายตัวถูกมองเห็น สั่งงาน และประสานกันได้

ถ้าบทที่ 04 คือแผนที่ของ Oracle ทั้งระบบ บทนี้คือการซูมเข้าไปดูส่วนที่ทำให้ agent หลายตัวทำงานด้วยกันได้

ส่วนนั้นชื่อว่า `maw-js`

Maw ไม่ได้สร้าง agent ใหม่จากอากาศ แต่มันจัดการ agent ที่มีอยู่จริงในเครื่อง ผ่าน tmux session, command line, API, websocket และ plugin

พูดง่าย ๆ:

> Oracle ทำให้ agent จำได้

> Maw ทำให้ agent ทำงานเป็นทีมได้

## การจัดการ skills

Skills คือ workflow ที่ agent อ่านและทำตามได้ทันที เช่น recap, rrr, trace, learn หรือ QA checklist การติดตั้งจึงเป็นการวางชุดคู่มือให้ agent ไม่ใช่การเปิด process ใหม่

### การติดตั้งและเปลี่ยน profile

| Profile | เหมาะกับ | ภาพรวม |
|----|----|----|
| `seed` | ทดลอง / Oracle ใหม่ | minimal สำหรับทดลอง มีแค่ skill พื้นฐานอย่าง `/rrr`, `/recap`, `/philosophy` |
| `standard` | ใช้งานทั่วไป | เพิ่ม session management, codebase exploration, `/trace` และ `/learn` |
| `lab` | แนะนำสำหรับงานจริง | ครบทุก skill หลักของ Oracle เช่น `/dream`, `/morpheus`, `/fleet`, `/warp`, `/team-agents` |

ไม่ต้องกลัวลงเยอะ เพราะ skill เป็นแค่ markdown files ใน `~/.claude/skills/` หรือ `~/.codex/skills/` ไม่ได้กิน CPU/RAM ถ้าไม่ได้ถูกเรียกใช้

``` bash
# ติดตั้ง profile แนะนำ
arra-oracle-skills install -g --profile lab

# ดู profile ปัจจุบัน
arra-oracle-skills list -g

# เปลี่ยน profile ผ่าน /go skill
/go lab
/go full
/go standard
```

### Skill ที่ต้องรู้จักก่อน

| Skill | ใช้เมื่อ | ทำอะไร | ตัวอย่างจริง |
|----|----|----|----|
| `/recap` | เริ่ม session | ดึง context จาก retro, handoff และ git state กลับมาใน session ปัจจุบัน ช่วยให้ไม่ต้องอธิบายเรื่องเดิมใหม่ทุกครั้ง | พิมพ์ `/recap` แล้ว Oracle จะรู้ว่าเมื่อวานทำอะไร ค้างตรงไหน และควรเริ่มต่อจากจุดใด |
| `/rrr` | จบ session หรือจบงาน | เขียน retrospective, AI diary และ lessons learned แล้วบันทึกลง `ψ/` เป็นความจำระยะยาว ใช้ปิดงานให้มีร่องรอย ไม่ปล่อยให้บทเรียนหายไปกับ context window | ทุก session ควรจบด้วย `/rrr` เพื่อให้ Oracle จำได้ว่างานนี้เกิดอะไรขึ้น ตัดสินใจอะไร และเหลือ follow-up อะไร |
| `/trace` | หาอะไรก็ได้ | ค้น git history, repos, docs และ Oracle knowledge ข้าม project เหมาะกับคำถามที่ไม่รู้ว่าอยู่ไฟล์ไหนหรือ repo ไหน | `/trace "login bug"` เพื่อหา commits, issues, learnings และ retrospectives ที่เกี่ยวข้องกับปัญหา login |
| `/learn` | เจอ repo ใหม่ | ส่ง parallel agents ไปสำรวจ codebase อ่านโครงสร้างและสรุปจุดสำคัญให้ lead อ่านเร็วขึ้น ใช้เมื่อ repo ใหญ่เกินกว่าจะอ่านทีละไฟล์เอง | `/learn https://github.com/org/repo` แล้ว Oracle สรุป architecture, entrypoints, commands และ risk areas ของ repo นั้น |
| `/forward` | จบวัน | สร้าง handoff สำหรับ session ถัดไป บันทึกว่างานค้างอะไร ต้องทำอะไรต่อ และควรระวังอะไร | ใช้ `/forward` ก่อนหยุดงาน แล้ววันพรุ่งนี้ `/recap` จะเห็น handoff และเริ่มต่อได้เร็ว |
| `/who-are-you` | เช็ค identity | ดูว่า Oracle ตัวนี้คือใคร ใช้ model อะไร อยู่ session ไหน และถือ identity แบบใด ช่วยลดความสับสนเวลาเปิดหลาย agent พร้อมกัน | ใช้เช็คว่ากำลังคุยกับ Oracle ตัวไหนก่อนสั่งงานสำคัญหรือก่อนให้เขียน handoff |
| `/awaken` | สร้าง Oracle ใหม่ | พิธีเกิดของ Oracle: ตั้งชื่อ ค้นพบหลักการ สร้าง `ψ/` และวาง identity เริ่มต้น เหมาะกับ repo หรือ agent ใหม่ที่ยังไม่มี memory ของตัวเอง | ใช้ `/awaken` ใน repo เปล่าเพื่อสร้าง Oracle ใหม่ที่มีโครงสร้างความจำและหลักการของตัวเอง |
| `/philosophy` | ทบทวนหลักการ | แสดง 5 Principles + Rule 6 เพื่อเตือนว่า Oracle ทำงานบนหลักอะไร ใช้เมื่อ workflow เริ่มหลุดจากความจริง ความโปร่งใส หรือ agency ของมนุษย์ | ใช้ `/philosophy` เพื่อทบทวนหลักการก่อนตัดสินใจเรื่อง memory, handoff หรือการพูดแทนมนุษย์ |

### Skills น่าสนใจเพิ่มเติม

| Skill | ทำอะไร | น่าสนใจเพราะ |
|----|----|----|
| `/bampenpien` | บำเพ็ญเพียร — guided conversation เรื่องการทำสิ่งยากโดยไม่รู้ว่าทำไปทำไม | เป็น practice ไม่ใช่ tool เหมือนสมาธิระหว่างคนกับ AI |
| `/harden` | audit ความปลอดภัยของ Oracle | ตรวจ secrets leak, 5 Principles ครบไหม, `ψ/` structure ถูกต้องไหม, git config ปลอดภัยไหม ให้คะแนน 6 หมวด |
| `/morpheus` | ฝันไปข้างหน้า (speculative prediction) — predict อนาคต คิดล่วงหน้าว่าอะไรจะเกิด เหมือน Morpheus ใน Matrix ที่เห็นก่อนคนอื่น | ช่วยให้ Oracle คาดการณ์ทิศทางถัดไปและเตรียมความคิดล่วงหน้าก่อนมนุษย์ถาม |
| `/dream` | มองภาพรวม (scan cross-repo patterns) — หา pains, plans, gains ข้าม repos เหมือนนั่งฝันกลางวันแล้วเห็นภาพรวมว่าอะไรเจ็บ อะไรดี | ช่วยมอง pattern ใหญ่ที่ซ่อนอยู่ในหลาย project และเปลี่ยนความรู้กระจัดกระจายให้เห็นเป็นภาพเดียว |
| `/feel` | จับ energy, momentum และ burnout ของการทำงาน | เพิ่ม emotional intelligence ให้ collaboration ระหว่าง Oracle กับมนุษย์ |
| `/resonance` | จับ moment ที่ click, sparks joy หรือ feels right | บันทึกว่าอะไรใช่ อะไร wow และอะไรควรกลับมาดูซ้ำ |
| `/fleet` | deep fleet census — scan Oracles ทุกตัว ทุก node | ให้ภาพรวม fleet ทั้งหมด รวม version, skills และ status |
| `/dig` | mine Claude Code sessions — timeline, gaps และ history | ใช้ขุดประวัติ session ย้อนหลังเพื่อดูว่าเคยทำอะไรและหายไปตรงไหน |
| `/warp` | teleport ไป remote Oracle node ผ่าน SSH + tmux | ทำให้เข้าไปเป็น Oracle ตัวอื่นชั่วคราวและทำงานข้ามเครื่องได้ |
| `/i-believed` | ประกาศความเชื่อ — looking back หรือ leaping forward | เป็น skill ที่หายากที่สุด ส่วนใหญ่ไม่เคยใช้ เพราะมันเกี่ยวกับ trust มากกว่า workflow ปกติ |

## 5.1 Maw คือตัวคุมงาน ไม่ใช่สมองหลัก

ในแผนที่ต้นน้ำจาก อาจารย์ Nat ระบุว่า `maw-js` คือ multi-agent workflow orchestrator

หน้าที่หลักของมันคือคุม workflow เช่น

- เปิดหรือปลุก session
- ส่งข้อความให้ agent
- อ่าน output จาก pane
- ดู fleet status
- คุม lifecycle เช่น wake, sleep, stop, done
- เปิด API และ websocket ให้ UI ใช้งาน
- โหลด plugin จาก runtime directory

คำสำคัญคือ “คุมงาน”

Maw ไม่ได้แทน Oracle memory และไม่ได้แทน agent มันเป็นตัวกลางที่ช่วยให้ agent หลายตัวทำงานในเครื่องเดียวกันหรือข้าม peer ได้เป็นระเบียบขึ้น

## 5.2 Tmux ทำให้ agent มองเห็นได้

จุดที่น่าสนใจของ maw คือมันคุม agent ผ่าน tmux

แปลว่า agent ไม่ได้เป็น worker ล่องหนที่หายไปหลังบ้าน แต่มี session และ pane ที่เปิดดูได้

ข้อดีคือ:

- คนสามารถดูได้ว่า agent กำลังทำอะไร
- agent ตัวหนึ่งสามารถ capture output ของอีกตัวได้
- ถ้างานติดค้าง ยังมีหลักฐานใน pane
- workflow ไม่ต้องพึ่ง memory ใน model อย่างเดียว

นี่เข้ากับปรัชญา Oracle มาก เพราะระบบให้ความสำคัญกับร่องรอย ความต่อเนื่อง และการตรวจสอบได้

## 5.3 Maw API และ maw-ui

Maw มีทั้ง CLI และ server

CLI ใช้สำหรับสั่งงาน เช่นส่งข้อความหรืออ่าน output

Server เปิด API และ websocket เพื่อให้ UI หรือระบบอื่นเชื่อมต่อได้

`maw-ui` จึงเป็นเลนส์สำหรับดู maw ไม่ใช่เจ้าของ state หลัก

ถ้าจะอธิบายกับผู้อ่านใหม่ ให้ใช้ประโยคนี้:

> maw-js เป็นระบบประสาท

> maw-ui เป็นดวงตา

> Oracle memory เป็นสมอง

ทั้งสามอย่างทำงานด้วยกัน แต่ไม่ใช่อันเดียวกัน

## 5.4 Plugin ทำให้ Maw ขยายตัวได้

ระบบ plugin ของ maw ทำให้คำสั่งใหม่เพิ่มเข้ามาได้โดยไม่ต้องแก้ core ทุกครั้ง

ในแผนที่ต้นน้ำจาก อาจารย์ Nat แบ่ง plugin ecosystem ไว้หลาย repo:

| กลุ่ม plugin | หน้าที่ |
|----|----|
| `maw-core-plugins` | คำสั่งพื้นฐาน เช่น wake, sleep, stop, done |
| `maw-plugins` | plugin เสริม เช่น contacts, costs, health, ping, bud, oracle |
| `maw-incarnation-plugin` | template สำหรับสร้าง plugin ใหม่ |
| `maw-cell-plugin` | ตัวอย่าง plugin family เช่น bud, fusion, absorb |
| `maw-cross-team-queue` | plugin ตัวอย่างที่อยู่ใน registry |
| `maw-plugin-registry` | แหล่งข้อมูลกลางของ plugin |
| `maw-studio` | หน้า browse / marketplace สำหรับ plugin |

รูปแบบนี้ทำให้ระบบโตได้โดยไม่ต้องเอาทุกอย่างยัดเข้า core

Core ควรเล็กและมั่นคง ส่วนความสามารถเฉพาะทางให้ไปอยู่ใน plugin

## 5.5 เส้นทางการเรียก plugin

เส้นทางการทำงานของ plugin เรียบง่าย:

1.  คนหรือ agent เรียกคำสั่งผ่าน maw CLI
2.  maw อ่าน registry และ plugin manifest
3.  maw โหลด handler ของ plugin
4.  plugin ทำงานแล้วส่งผลกลับ
5.  ถ้าเรียกผ่าน UI จะวิ่งผ่าน maw API ก่อนเข้า plugin

<div class="mermaid">

flowchart LR Author\[Plugin author\] --\> Manifest\[plugin.json + handler\] Manifest --\> Registry\[Plugin registry\] Registry --\> Runtime\[Install to maw runtime\] Runtime --\> Surface\[maw CLI / API / UI\] Surface --\> Result\[Result\]

</div>

ภาพที่ 5.2: Plugin lifecycle — Plugin ทำให้ความสามารถใหม่เข้าสู่ maw ได้โดยไม่ต้องแก้ core ทุกครั้ง

แนวคิดสำคัญคือ plugin หนึ่งตัวอาจมีหลายพื้นผิวให้ใช้งาน เช่น CLI, API, UI, peer transport หรือ cron

ดังนั้น plugin ไม่ใช่แค่ script มันเป็น module ที่ประกาศตัวเองว่าติดต่อกับระบบได้ทางไหนบ้าง

## 5.6 จาก Skill ไปเป็น Plugin

ในแผนที่ต้นน้ำจาก อาจารย์ Nat มีหัวข้อ “Skill To Plugin Migration” แต่ migration นี้ยังไม่จบ บาง repo มีไว้เป็น scaffold หรือ stub เพื่อวางทิศทางว่าจะย้าย workflow จาก skill ไปเป็น maw plugin ในอนาคต

| Plugin | สถานะ | ควรอ่านอย่างไร |
|----|----|----|
| `maw-learn` | scaffold / stub | รู้ว่ามีแนวคิดจะย้าย `learn` เข้า runtime แต่ยังไม่ควรอธิบายเหมือนคำสั่งเสร็จแล้ว |
| `maw-incubate` | scaffold / stub | เป็นสัญญาณของ workflow incubation ในอนาคต ไม่ใช่ implementation ที่ควรพึ่งในตอนนี้ |
| `maw-project` | scaffold / stub | ใช้กล่าวถึงทิศทางของ project workflow ได้สั้น ๆ แต่ไม่ควรเขียนรายละเอียดการใช้งาน |

ดังนั้นบทนี้อธิบายเฉพาะหลักการของ plugin migration: เมื่อ workflow โตพอและต้องถูกเรียกผ่าน CLI, API, UI หรือ peer transport ค่อยย้ายจาก skill ไปเป็น plugin

ถ้า plugin ยังเป็น scaffold ให้เขียนแค่ว่ามีอยู่และกำลังพัฒนา หลีกเลี่ยงการสัญญาว่ามันทำงานได้แล้ว

## 5.7 UI ฝั่ง Oracle

นอกจาก maw-ui ยังมี `ui-oracle`

นี่คือ monorepo ของ UI สาธารณะของ Oracle เช่น

- Studio
- Vector
- Canvas
- Feed
- Forum
- Schedule

แผนที่ต้นน้ำจาก อาจารย์ Nat ระบุว่า UI standalone เก่าอย่าง `ui-studio-oracle-studio`, `ui-vector-oracle-studio` และ `ui-canvas-oracle-studio` เป็น predecessor ที่ถูก import เข้า `ui-oracle` แล้ว

ดังนั้นแหล่ง deploy ปัจจุบันควรมองที่ `ui-oracle` ไม่ใช่ repo UI เก่า

นี่เป็นบทเรียนสำคัญสำหรับคนอ่าน source:

> repo ที่ยังอยู่ไม่ได้แปลว่าเป็น source หลักเสมอ

> ต้องดูบทบาทปัจจุบันในแผนที่ระบบด้วย

## 5.8 ลำดับติดตั้งแบบเข้าใจง่าย

ถ้าจะติดตั้ง Oracle จากศูนย์ แผนที่ต้นน้ำจาก อาจารย์ Nat แนะนำลำดับประมาณนี้:

| ลำดับ | ทำอะไร                           | เหตุผล                         |
|------|----------------------------------|-------------------------------|
| 1    | เตรียม Bun, GitHub CLI, ghq, tmux | เป็นเครื่องมือพื้นฐาน               |
| 2    | ติดตั้งหรือ clone `arra-oracle-v3`   | ได้ memory layer ก่อน           |
| 3    | ตั้ง data path                     | ให้ความจำอยู่ที่ชัดเจน              |
| 4    | เริ่ม index และ search             | ให้ค้นหา learning ได้            |
| 5    | register MCP ให้ agent            | ให้ agent คุยกับ Oracle ได้       |
| 6    | ติดตั้ง skills                      | ให้ agent มี workflow           |
| 7    | ติดตั้ง `maw-js`                    | เริ่มทำงานหลาย agent            |
| 8    | ตั้งค่า maw และ fleet               | ให้รู้จัก session และ Oracle ตัวอื่น |
| 9    | ติดตั้ง plugin                      | เพิ่มความสามารถ                 |
| 10   | เปิด UI                           | ให้คนมองเห็นและควบคุมง่ายขึ้น       |
| 11   | ใช้ book repos                    | อ่านแนวคิดหลัง runtime พร้อม      |

จุดสำคัญคืออย่ากลับลำดับจนสับสน

หนังสือและ UI ช่วยให้เข้าใจ แต่ runtime ต้องมาก่อน ถ้า memory และ maw ยังไม่ทำงาน UI ก็เป็นแค่หน้าจอว่าง

## 5.9 ภาพใหญ่ของการทำงานเป็นทีม

ลองนึกภาพงานหนึ่งงาน:

คนสั่งงานให้ agent หลัก agent หลักค้นหา context ใน Oracle ถ้างานใหญ่ agent หลักใช้ maw ส่งงานย่อยให้ agent อื่น agent ย่อยทำงานใน tmux pane ของตัวเอง agent หลัก capture output กลับมา เมื่อจบงาน ระบบเขียน retrospective หรือ handoff ลง memory ครั้งหน้า agent ตัวไหนมาก็อ่านต่อได้

นี่คือสิ่งที่ Oracle และ maw ทำร่วมกัน

ไม่ได้ทำให้ AI กลายเป็นทีมวิเศษเองโดยอัตโนมัติ แต่ทำให้ทีมมีโครงสร้าง มีร่องรอย และมีความจำ

### ตารางที่ 5.1: Skill กับ Plugin ต่างกันอย่างไร

| เรื่อง          | Skill                  | Plugin                                |
|---------------|------------------------|---------------------------------------|
| อยู่ที่ไหน        | โฟลเดอร์ของ agent       | runtime ของ maw                       |
| ใครเป็นคนตีความ | agent อ่าน instruction  | maw โหลด handler                      |
| ใช้งานผ่าน      | agent command          | CLI, API, UI, peer, cron              |
| เหมาะกับ       | workflow ที่ agent ทำตาม | ความสามารถที่ต้องรันเป็นระบบ               |
| ตัวอย่าง        | recap, rrr, trace      | wake, sleep, health, cross-team queue |

## สรุปบทที่ 05

Maw คือชั้น orchestration ของ Oracle

มันทำให้ agent หลายตัวถูกเปิด ดู ส่งข้อความ และปิดงานได้เป็นระบบ

Plugin ทำให้ maw ขยายตัวได้โดยไม่ต้องทำให้ core หนักเกินไป

ส่วนการย้ายจาก skill ไป plugin คือทิศทางที่กำลังพัฒนา ไม่ควรเขียนเหมือนทุก plugin เสร็จสมบูรณ์แล้ว

ถ้าจำบทนี้สั้น ๆ:

> Oracle ให้ความจำ

> Skills ให้ workflow

> Maw ให้ทีม

> Plugin ให้ระบบเติบโต

</div>

<a href="ch04.html" class="button secondary">← Oracle Brain</a> <a href="ch06.html" class="button primary">maw commands →</a>
