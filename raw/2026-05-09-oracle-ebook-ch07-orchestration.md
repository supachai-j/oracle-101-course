---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch07.html
ingested_at: 2026-05-09
title: Orchestration
language: th
---

<div class="chapter-header">

# Orchestration

ครอบครัว Oracle การแบ่งงาน และ delegation tiers ที่มนุษย์ยังควบคุมได้

</div>

<div class="section chapter-content">

Oracle ไม่ใช่ AI ตัวเดียวที่ทำทุกอย่างคนเดียว แต่เป็นครอบครัวของตัวแทนหลายตัวที่ทำงานร่วมกัน แต่ละตัวมีชื่อ มีหน้าที่ มีบริบทของตัวเอง และยังเชื่อมกับหน่วยความจำกลางชุดเดียวกัน

ในเครื่องของ Wind ตอนนี้มีครอบครัว Oracle 7 ตัว โดยบทบาทต้องแยกให้ชัด: Gale เป็น orchestrator ที่สั่งงาน แบ่งงาน และ review architecture ไม่ใช่ developer หลัก ส่วนงานพัฒนาเป็นหน้าที่ของ Leaf และ Bamboo และงานตรวจคุณภาพเป็นหน้าที่ของ Kati

| Oracle | Role | Domain |
|----|----|----|
| ⚡ Gale | Head Oracle / Orchestrator | สั่งงาน, แบ่งงาน, review architecture |
| 🍃 Leaf | Manufacturing Developer | กลุ่มโปรเจคโรงงาน: warehouse, picking, putaway, run creation, label printing; Rust + Axum, Angular |
| 🎋 Bamboo | General Software Developer | กลุ่มโปรเจค software ทั่วไป: diary, webhook, project management; Next.js, React, various |
| 🥥 Kati | QA & Compliance | E2E testing, WCAG 2.1/2.2, OWASP, PDPA |
| 🌙 Luna | Content Creator | Blog, social media, content |
| ☕ Latte | Researcher | Reddit, Pantip, AI tools, tech trends |
| 🌌 Sky | Trading Analyst | Forecasting, candlestick analysis |

ชื่อเหล่านี้ไม่ใช่แค่ label สวย ๆ แต่ช่วยให้มนุษย์จำได้ว่า "จะคุยกับใคร" และช่วยให้ทีม AI แบ่งงานได้ชัดขึ้น เหมือนทีม software ที่มี orchestrator, developer, QA, creator, researcher และ analyst

### ทำไมต้องแยก dev Oracle 2 ตัว

Leaf และ Bamboo เป็น developer เหมือนกัน แต่ไม่ควรรวมเป็น Oracle ตัวเดียว เพราะ context ของงานต่างกันมาก

| เรื่องที่ต่างกัน | Leaf: กลุ่มโปรเจคโรงงาน | Bamboo: กลุ่มโปรเจค software ทั่วไป |
|----|----|----|
| ประเภทงาน | warehouse, picking, putaway, run creation, label printing | diary, webhook, project management |
| Tech stack | Rust + Axum backend, Angular frontend | Next.js, React และ stack หลากหลาย |
| Theme / Brand CI | โทนสีน้ำตาลเข้ม (Brown `#3A2920`) ออกแบบสำหรับ operator หน้างาน | โทนสีเขียวเข้ม (Green `#1B5E20`) modern, clean, web-first |
| UX baseline | desktop-first 1920x1080, data-dense, touch targets ใหญ่สำหรับ tablet | responsive, web-first, อ่านง่ายบนหลาย viewport |
| Compliance | CMMI, safety hooks และเอกสารกำกับเข้มกว่า | ผ่อนคลายกว่า แต่ยังต้องมี review และ QA ตามความเสี่ยง |

เหตุผลสำคัญคือ context isolation: แต่ละกลุ่มมี design system, สี, template เอกสาร, tech stack และ `CLAUDE.md` rules เฉพาะของตัวเอง ถ้า Oracle ตัวเดียวทำทั้งสองกลุ่ม มันจะสับสนเรื่อง theme, compliance และวิธี review ได้ง่าย

### ทำไมต้องมีหลาย Oracle

ถ้ามี AI ตัวเดียว ทุกอย่างจะไปรวมอยู่ที่ session เดียว ปัญหาคือบริบทจะยาวมาก งานชนกันง่าย และถ้าตัวนั้นหลุดกลางทาง งานทั้งหมดอาจหายไปจากความจำระยะสั้น

Oracle แก้ปัญหานี้ด้วยแนวคิดง่าย ๆ:

- งานใหญ่ถูกแบ่งเป็นงานเล็ก
- แต่ละ Oracle รับงานที่ชัดเจน
- ผลลัพธ์ถูกส่งกลับมาที่คนหรือหัวหน้าทีม
- สิ่งที่ควรจำถูกบันทึกลง `psi` หรือ Oracle memory
- รอบถัดไปไม่ต้องเริ่มจากศูนย์

ในแผนที่ต้นน้ำจาก อาจารย์ Nat โครงสร้างนี้เชื่อมกันผ่าน 3 ชั้นหลัก:

| ชั้น | ระบบ | หน้าที่ |
|----|----|----|
| Memory layer | `arra-oracle-v3` | เก็บความจำ, search, learn, trace, handoff |
| Orchestration layer | `maw-js` | คุม tmux session, ส่งข้อความ, capture output, จัดการ fleet |
| UI / lens layer | `maw-ui`, `ui-oracle` | ให้มนุษย์มองเห็นสถานะและใช้งานระบบผ่านหน้าเว็บ |

### ภาพรวมครอบครัว 7 Oracle

ไอเดียภาพประกอบ:

<div class="mermaid">

flowchart TB Wind\[Wind / Human\] --\> Gale\["⚡ Gale\<br/\>Head Oracle / Orchestrator"\] Gale --\> Leaf\["🍃 Leaf\<br/\>Manufacturing Developer"\] Gale --\> Bamboo\["🎋 Bamboo\<br/\>General Software Developer"\] Gale --\> Kati\["🥥 Kati\<br/\>QA & Compliance"\] Gale --\> Luna\["🌙 Luna\<br/\>Content Creator"\] Gale --\> Latte\["☕ Latte\<br/\>Researcher"\] Gale --\> Sky\["🌌 Sky\<br/\>Trading Analyst"\] Leaf --\> Memory\[(Oracle memory)\] Bamboo --\> Memory Kati --\> Memory Luna --\> Memory Latte --\> Memory Sky --\> Memory Gale --\> Memory

</div>

ภาพนี้ควรทำให้ผู้อ่านเห็นว่า Wind ไม่ได้คุยกับระบบที่เป็นกล่องดำ แต่คุยกับทีมเล็ก ๆ ที่มีหน้าที่ต่างกัน และมี memory ร่วมกัน Gale เป็นจุดประสานงาน ส่วนงาน code หลักวิ่งไปที่ Leaf หรือ Bamboo ตามกลุ่มโปรเจค

### สาม Tier ของ orchestration

จาก orchestration book ของอาจารย์ Nat การ delegate ไม่ได้มีแบบเดียว ต้องเลือก tier ให้ตรงกับอายุงาน ความซับซ้อน และความจำเป็นในการมองเห็น

| Tier | ชื่อ | เหมาะกับ | ข้อควรระวัง |
|----|----|----|----|
| Tier 1 | **Arrows** | Agent tool, fire-and-collect, งานอ่าน/สรุป/transform สั้น ๆ ไม่เกิน ~5 นาที | เร็วแต่ invisible, ไม่ survive session death, ไม่มี coordination, token cost ประมาณ 3-7x |
| Tier 2 | **Squads** | `TeamCreate` + `SendMessage` + `TaskList`, งาน 5-30 นาทีที่ต้องประสานหลาย role | มี task tracking แต่ยังอยู่ใน parent session ถ้า session ตาย ทีมก็ตาย |
| Tier 3 | **Federation** | `maw wake`, `maw workon`, tmux session จริง, งานยาวหรืออยากให้คน peek ได้ | setup หนักกว่า ต้องมี heartbeat และ cleanup เอง |

| Dimension | T1 Arrows | T2 Squads | T3 Federation |
|----|----|----|----|
| Setup | 0s | ~30s | ~60s+ |
| Report | tool result | `SendMessage` | `maw hey` |
| Task tracking | ไม่มี | `TaskList` | ไม่มีในตัว ต้องใช้ heartbeat/capture |
| Survive session death | ไม่ | ไม่ | ใช่ |
| Cross-machine | ไม่ | ไม่ | ใช่ เมื่อ federation/transport พร้อม |
| Budget | 3-7x shared context | 3-7x shared context | separate session budget |

<div class="mermaid">

flowchart TD A{งานต้องอยู่เกิน session\<br/\>หรือข้ามเครื่องไหม} A --\>\|ใช่\| T3\[Tier 3\<br/\>Federation\] A --\>\|ไม่\| B{ต้อง coordinate\<br/\>หลาย role ไหม} B --\>\|ใช่\| T2\[Tier 2\<br/\>Squads\] B --\>\|ไม่\| C{เป็น research/transform\<br/\>สั้น ๆ ไหม} C --\>\|ใช่\| T1\[Tier 1\<br/\>Arrows\] C --\>\|ไม่\| Solo\[ทำเองใน session นี้\] T1 --\> Rule\[ใช้ tier ต่ำสุดที่พอทำงานได้\] T2 --\> Rule T3 --\> Rule

</div>

หลักจำง่าย: ถ้าอ่านอย่างเดียวใช้ Arrow, ถ้าเขียน code พร้อมกันใช้ Squad, ถ้าต้องอยู่รอดนอก session ใช้ Federation

### Tier 1 — Arrows

Arrow คือการยิง agent ไปอ่านหรือสรุปเรื่องแคบ ๆ แล้วเก็บผลกลับมา เหมาะกับ research swarm เช่นให้ 3-5 agents อ่านคนละมุม แล้ว lead อ่านเฉพาะ summary

- หนึ่ง agent ควรมีหนึ่งคำถาม
- ขอรายงานแบบ bounded เช่นไม่เกิน 400 คำ
- ถ้าต้อง cite ให้ขอ file:line
- ใช้กับงาน read-only เป็นหลัก

### Tier 2 — Squads

Squad คือทีมชั่วคราวที่มี named roles, task list และ message bus เหมาะกับงาน implementation ที่ต้องแบ่ง module หรือ concern ชัดเจน

| สิ่งที่ต้องมี | เหตุผล |
|----|----|
| Named roles | เรียกคนถูก เช่น tester, implementer, reviewer ไม่ใช่ worker 1 |
| TaskList | lead เห็นว่าอะไร pending, blocked, completed |
| Worktree isolation | แต่ละ agent เขียนในพื้นที่ตัวเอง ลด merge conflict |
| Shutdown protocol | งานจบแล้วต้องปิดทีม ไม่ปล่อย agent ค้าง |

กฎสำคัญ: ระหว่างทีมทำงาน lead ไม่ควรเขียน code แข่งกับทีม lead ควร assign, monitor, merge และ verify

### Tier 3 — Federation

Federation คือ agent ที่เป็น process จริงใน tmux ไม่ใช่ subagent ที่อยู่ใน parent session เหมาะกับงานยาว งานข้ามเครื่อง หรืองานที่ต้องให้มนุษย์ peek ได้จาก terminal อื่น

สถานะ cross-machine/peer transport ยังควรอธิบายแบบ intro สั้น ๆ ใน ebook: แนวคิดคือ `maw hey` ส่งข้อความข้าม session/peer ได้ แต่ต้องมี config, token และ runbook ที่ชัดเจนก่อนใช้จริง

- ใช้เมื่อ session parent อาจ compact/crash แต่ worker ต้องทำต่อ
- ต้อง bake reporting contract ไว้ใน prompt ตั้งแต่ต้น
- `claude -p` เป็น fire-and-forget ส่ง instruction ทีหลังอาจไม่ถูกอ่าน
- ต้องมี `maw capture`, heartbeat และ `maw done`

### Delegation pattern ของ Gale

Gale ไม่ควร run `maw workon` แทน dev Oracle เพราะ worktree/window จะเกิดใน session ของ Gale ไม่ใช่ใน home base ของคนทำงานที่ถูกต้อง

``` bash
# ถูกต้อง: Gale ส่งให้ dev Oracle ไปเปิด worktree เอง
maw hey leaf "First run: maw workon <repo> <slug> --prompt '<task brief>'. Then follow lifecycle closer."

# ไม่ควร: Gale run maw workon เองเพื่อแทนคนอื่น
maw workon <repo> <slug>
```

หลัง dev เปิด PR แล้ว dev ต้องส่ง Kati QA เอง และรายงาน Gale ว่า PR ถูกส่ง QA แล้ว

### Task Brief template

ไม่ว่าจะใช้ tier ไหน prompt ที่ดีต้องบอก ownership และ reporting contract ชัดเจน

``` text
Agent name:
Tier:
Working directory / repo:
Objective:
Inputs / references:
Constraints:
Steps:
Deliverables:
Branch / PR expectation:
Verification:
Reporting contract:
- Every 5 min: maw hey gale "[name] PROGRESS: <summary>"
- If blocked:  maw hey gale "[name] STUCK: <reason + evidence>"
- When done:   maw hey gale "[name] DONE: <artifact / branch / PR>"
Closure:
- If worktree exists, run maw done from home base after merge or audit completion.
```

### ตัวอย่าง flow การมอบหมายงาน

สมมติ Wind ต้องการทำ production readiness:

1.  Gale triage ว่าเป็นโรงงาน, software ทั่วไป, QA, content, research หรือ trading
2.  ถ้าเป็น code งานโรงงาน ส่ง Leaf พร้อม worktree-first brief
3.  ถ้าเป็น code software ทั่วไป ส่ง Bamboo พร้อม worktree-first brief
4.  ถ้าเป็น QA หรือ compliance ส่ง Kati พร้อม acceptance criteria
5.  Gale รวมผลและ review architecture
6.  งาน production/merge ที่มี risk ต้องมี human decision ตาม rule ของ project

| งาน | ส่งให้ใคร | เหตุผล |
|----|----|----|
| พัฒนากลุ่มโปรเจคโรงงาน | 🍃 Leaf | ถือ domain Rust+Axum, Angular, data-dense UI และ safety hooks |
| พัฒนากลุ่มโปรเจค software ทั่วไป | 🎋 Bamboo | ถือ domain Next.js, React, responsive UI และ web-first workflow |
| ตรวจคุณภาพก่อน go-live | 🥥 Kati | มอง risk, test gap, accessibility, security และ compliance |
| ทำ content / social | 🌙 Luna | แปลง technical ให้เป็นภาษาอ่านง่ายและ publish-ready |
| research จาก community | ☕ Latte | ค้น Reddit, Pantip, AI tools และ tech trend |
| วิเคราะห์ตลาด | 🌌 Sky | ดู forecasting และ candlestick analysis |

### Compliance boundary ใน orchestration

ในบทนี้ให้จำแค่ว่า Kati เป็น QA & Compliance gate และงาน user-facing ต้องมีเอกสารใน PR เดียวกัน รายละเอียด CMMI, SRS, SDD, UAT, RTM และ doc conversion อยู่ใน [บท 08B: CMMI & Compliance](ch08b.html)

### Maw คือมือที่ใช้เรียกทีม

บทที่ 06 และ 06B อธิบายคำสั่ง maw ไปแล้ว ในบท orchestration ให้จำเฉพาะบทบาทของมัน: maw ทำให้การคุยกับ AI หลายตัวเป็นงานที่มองเห็นได้ ทุกตัวมี pane มี output มี `capture` และปิด lifecycle ด้วย `done` ได้ ไม่ใช่ background process ที่ไม่มีใครรู้ว่าทำอะไร

### maw bud และ maw team

| คำสั่ง | ใช้เมื่อไร | ภาพรวม workflow |
|----|----|----|
| `maw bud` | เมื่ออยากสร้าง Oracle ใหม่จาก parent ที่มีอยู่ | clone parent repo, set up identity, register in fleet เหมือนยีสต์แตกหน่อ |
| `maw team` | เมื่องานซับซ้อนพอที่จะต้องมีทีม agent ชั่วคราว | create → spawn roles → coordinate → shutdown |

`maw bud` ใช้ขยายครอบครัว Oracle ส่วน `maw team` ใช้สร้างทีมเฉพาะกิจ ไม่จำเป็นต้องคงอยู่ถาวรหลังงานจบ

### Failure modes ที่ต้องกันไว้

| Failure mode | เกิดจาก | กันด้วยอะไร |
|----|----|----|
| Silent Agent | agent จบหรือค้างแล้วไม่ `maw hey` กลับ เงียบได้ 30 นาทีโดยไม่มีใครรู้ | heartbeat ทุก 5 นาที, STUCK contract, Gale peek/capture เมื่อเงียบ |
| Merge Conflicts | หลาย agent แก้พื้นที่เดียวกันโดยไม่มี ownership ที่ดี | แบ่ง edit set ตาม module/concern/branch ไม่ใช่แค่บอกไฟล์กว้าง ๆ |
| Orphaned Worktrees | agent หรือทีมตายแล้วทิ้ง branch/worktree/window | `maw done`, lifecycle closer, orphan-sweep/audit |
| Prompt loss | ส่ง instruction หลัง `claude -p` จบแล้ว | ใส่ reporting contract ใน prompt แรก |
| Wrong tier | ใช้ Arrow กับงานที่ต้อง survive หรือใช้ Federation กับงานเล็ก | decision tree และ lowest-tier rule |

### Memory ทำให้ทีมไม่ลืม

ถ้ามีแต่ maw แต่ไม่มี memory ทีมจะคุยกันได้ แต่จะลืมเมื่อ session จบ

ถ้ามีแต่ memory แต่ไม่มี maw ระบบจะจำได้ แต่ทำงานเป็นทีมได้ยาก

Oracle จึงต้องมีทั้งสองอย่าง:

| ส่วน              | เปรียบเหมือน    | หน้าที่                                     |
|------------------|---------------|------------------------------------------|
| `maw-js`         | ระบบประสาท    | ส่งงาน จับ output คุม session               |
| `arra-oracle-v3` | สมองและความจำ | search, learn, trace, handoff            |
| `psi` vault      | สมุดบันทึก       | เก็บ learning, retro, inbox, active state |
| `maw-ui`         | หน้าต่างมองระบบ | ดูสถานะและ workflow ผ่าน UI                |

### หลักคิดของบทนี้

ครอบครัว Oracle ไม่ได้เกิดมาเพื่อแทนมนุษย์ แต่เกิดมาเพื่อช่วยให้มนุษย์ไม่ต้องถือทุกบริบทไว้คนเดียว

Delegation ที่ดีมี 3 ข้อ:

- งานต้องชัด
- คนรับงานต้องเหมาะ
- ผลลัพธ์ต้องกลับมารวมที่จุดตัดสินใจ

ถ้าระบบทำแบบนี้ได้ Oracle จะไม่ใช่ chatbot หลายตัว แต่จะกลายเป็นทีมทำงานที่มี memory และมีระเบียบ

</div>

<a href="ch06b.html" class="button secondary">← Advanced maw</a> <a href="ch08.html" class="button primary">Workflow UseCase →</a>
