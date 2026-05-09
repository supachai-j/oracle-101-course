---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch09.html
ingested_at: 2026-05-09
title: Autonomous Ops
language: th
---

<div class="chapter-header">

# Autonomous Ops

PM2, cron, systemd, trigger และ runbook สำหรับระบบที่ดูแลตัวเองได้บางส่วน

</div>

<div class="section chapter-content">

Autonomous ops แปลแบบง่ายคือ "ระบบช่วยดูแลตัวเองได้บางส่วน" แต่ไม่ได้แปลว่า "ระบบทำทุกอย่างเองโดยไม่ต้องมีคน"

ใน Oracle, autonomous ops คือการทำให้งานประจำเหล่านี้เกิดขึ้นได้อย่างสม่ำเสมอ:

- เปิด service ที่ต้องรันตลอด
- ตรวจว่า service ยัง alive
- ส่ง event หรือ trigger เมื่องานเกิดขึ้น
- เก็บ log และ feed ให้ทีมอ่าน
- เรียก Oracle มาช่วยเมื่อมีปัญหา
- ทำ handoff หรือ retrospective เมื่อ session ใกล้จบ

### Discord Dev Server architecture

Discord เป็น SDLC tracking surface ของทีม ไม่ใช่ source of truth ของ code แต่เป็นที่ที่มนุษย์และ Oracle มองเห็น lifecycle เดียวกัน

| Category | Channel ตัวอย่าง | หน้าที่ |
|----|----|----|
| Command Center | `announcements`, `standup`, `fleet-status`, `inbox` | ประกาศ สรุปวัน ดูสถานะ fleet และรับข้อความเข้า |
| Project Forums | forum channel แยกตาม project | เก็บ REQ, CR, BUG, QA, DOC, DEPLOY, DONE ต่อ project |
| Cross-Project | `active-crs`, `deploy-queue`, `qa-dashboard` | ดูงานข้าม project และคิว deploy/QA |
| Oracle Family | log channel ต่อ Oracle | เก็บ heartbeat, DONE, STUCK และ operational notes |

รายละเอียด tag เช่น `REQ`, `CR`, `BUG`, `QA`, `DOC`, `DEPLOY` และ `DONE` อยู่ในบทที่ 08 แล้ว บทนี้สนใจมากกว่าว่า automation จะ post ไปที่ channel ไหนและเมื่อไรควรหยุดรอมนุษย์

<div class="mermaid">

flowchart TB Wind\[Wind\] --\> Gale\[Gale\] Gale --\> Forum\[Project Forum\<br/\>REQ / CR / BUG\] Forum --\> Dev\[Leaf / Bamboo\] Dev --\> PR\[Pull Request\] PR --\> Kati\[Kati QA\] Kati --\> Dashboard\[qa-dashboard\] Kati --\> Queue\[deploy-queue\] Queue --\> Done\[Forum tag DONE\] Gale --\> Fleet\[Oracle Family logs\]

</div>

### Bot integration

Oracle ไม่ควร spam Discord ทุก event ที่เกิดขึ้น หลักที่ดีคือ post เฉพาะจุดที่มนุษย์ต้องเห็น เช่น task created, QA result, deploy ready, done หรือ stuck/escalation

| Event | Discord action | ข้อควรระวัง |
|----|----|----|
| Wind request | Gale สร้าง forum post พร้อม tag `REQ`/`CR`/`BUG` | ต้องเลือก project forum ให้ถูก |
| Task Brief ready | post scope, acceptance criteria, owner | อย่าใส่ข้อมูลลับเกินจำเป็น |
| PR ready | post link และ route ไป Kati | dev ส่ง Kati โดยตรง ไม่ต้องรอ Gale relay |
| QA PASS/FAIL | post ใน project forum และ dashboard | FAIL ต้องชี้ branch/PR เดิม |
| Deploy ready | post deploy queue | รอ approval ตาม policy |
| Done | เปลี่ยน tag เป็น `DONE` | ต้องทำหลัง merge/deploy/maw done ครบ |

### Project lifecycle script

`project-lifecycle.sh` คือ automation ที่ช่วยให้ repo, Discord, docs และ channel map ไม่แยกโลกกัน

| Mode | ทำอะไร | ผลลัพธ์ที่ควรตรวจ |
|----|----|----|
| `create` | สร้าง GitHub repo, `ghq clone`, bootstrap docs, สร้าง Discord channels/forum, update channel map | repo เปิดได้, docs อยู่ครบ, Discord post ได้, map resolve ถูก |
| `remove` | ลบ/ปิด Discord surface, update channel map, ย้าย directory ไป `/tmp`, archive repo ตาม policy | ไม่มี ghost channel และไม่มีการ `rm -rf` ที่เสี่ยง |
| `audit` | เทียบ Discord กับ GitHub repos และ docs | รายงาน ghosts, missing channels, missing docs, stale map |

<div class="mermaid">

flowchart LR Create\[create\] --\> Repo\[GitHub repo\] Create --\> Clone\[ghq clone\] Create --\> Docs\[init docs\] Create --\> Discord\[Discord forum\] Create --\> Map\[channel map\] Audit\[audit\] --\> Repo Audit --\> Discord Audit --\> Map Remove\[remove\] --\> Archive\[archive / move to tmp\] Remove --\> CleanMap\[clean channel map\]

</div>

### ขอบเขต automation สำหรับเอกสาร

Autonomous ops ช่วยเตือนหรือ bootstrap เอกสารได้ แต่กฎ CMMI เต็มอยู่ใน [บท 08B: CMMI & Compliance](ch08b.html) บทนี้จำแค่สองเส้นแบ่ง: markdown docs ใน PR เดียวกับ code ทำได้อัตโนมัติ ส่วนการแปลงเป็น DOCX/PPTX/XLSX ต้องให้ Wind สั่งเอง

### Auto-standup cron

Auto-standup คือ cron/report ที่ช่วยให้เช้าวันใหม่เริ่มจากสถานะจริง ไม่ใช่ความทรงจำลอย ๆ

<div class="mermaid">

sequenceDiagram participant Cron participant Maw as maw server participant Oracle as Oracle memory participant Discord Cron-\>\>Maw: read fleet sessions Cron-\>\>Oracle: read feed / handoff / recent retro Cron-\>\>Discord: post standup summary Discord--\>\>Cron: visible status for humans

</div>

Standup ที่ดีควรบอก active work, blocked work, PR waiting QA, deploy queue, และ agent ที่เงียบผิดปกติ

### Event triggers: agent-idle และ agent-crash

| Trigger | ตัวอย่าง action | Human boundary |
|----|----|----|
| `agent-idle` | เงียบเกิน threshold → `maw hey` ถาม status หรือให้ Gale capture | ถ้าเงียบยาวและมี production risk ให้ escalate |
| `agent-crash` | แจ้ง Gale พร้อมชื่อ agent/window และ log ล่าสุด | อย่า restart ทับก่อน capture evidence |
| `qa-failed` | post BUG และส่งกลับ dev branch เดิม | fail เกิน 3 รอบให้ Wind ตัดสินใจ |
| `pr-merged` | แจ้ง dev home base ให้ run `maw done` | ถ้า window ค้างเกินเวลา ให้ cleanup ตาม runbook |

### ระบบที่ต้องรันอยู่ตลอด

จาก source ต้นน้ำของอาจารย์ Nat, runtime สำคัญมีหลายตัว:

| Runtime | หน้าที่ | Port / endpoint |
|----|----|----|
| `arra-oracle-v3` MCP | ให้ agent เรียกเครื่องมือ memory ผ่าน MCP stdio | stdio |
| `arra-oracle-v3` HTTP API | ให้ UI และระบบอื่น search/stats/menu/learn | `:47778` |
| `maw-js` server | API, websocket, tmux orchestration, plugin runtime | `:3456` |
| `maw-ui` | dashboard/lens สำหรับดู maw | ผ่าน `maw serve` หรือ dev UI |
| `ui-oracle` apps | public UI เช่น studio, vector, canvas, feed, forum, schedule | buildwithoracle hosts |

ถ้าระบบเหล่านี้ดับ Oracle จะยังเป็น idea ที่ดี แต่ใช้งานจริงได้ไม่เต็มที่ เพราะ agent จะ search ไม่ได้ UI จะมองไม่เห็น และ maw จะคุม fleet ไม่ได้

### PM2, cron, systemd ใช้ต่างกันอย่างไร

สำหรับผู้อ่านใหม่ ให้จำแบบนี้:

| เครื่องมือ | เหมาะกับ | ตัวอย่างใน Oracle |
|----|----|----|
| `pm2` | process ที่เป็น Node/Bun app และควรรันค้าง | Oracle HTTP API, maw server |
| `cron` | งานที่ต้องรันตามเวลา | reindex, health check, backup, daily report |
| `systemd` | service ระดับเครื่องที่ต้อง boot แล้วขึ้นเอง | long-running daemon, watchdog, reverse proxy helper |
| triggers/hooks | งานที่เกิดจาก event | หลัง commit, หลัง task done, หลัง feed event, หลัง handoff |

ไม่จำเป็นต้องใช้ทุกตัวกับทุกงาน จุดสำคัญคือเลือกให้ตรงงาน

### PM2: ดูแล process ที่ต้อง alive

PM2 เหมาะกับ service แบบ "ต้องเปิดไว้" เช่น Oracle API หรือ maw server

แนวคิดคือ:

- start process
- ตั้งชื่อ process
- ถ้า crash ให้ restart
- ดู log ได้
- save process list
- reboot เครื่องแล้วยัง restore ได้

ไอเดียภาพประกอบ:

<div class="mermaid">

flowchart LR PM2\[pm2\] --\> OracleAPI\[Oracle HTTP API\<br/\>:47778\] PM2 --\> MawServe\[maw serve\<br/\>:3456\] OracleAPI --\> SQLite\[(oracle.db)\] OracleAPI --\> Vector\[(LanceDB)\] MawServe --\> Tmux\[tmux sessions\] MawServe --\> Plugins\[plugin runtime\]

</div>

ข้อดีของ PM2 คือเข้าใจง่ายและเหมาะกับ Node/Bun ecosystem ข้อควรระวังคืออย่าปล่อย log โตไม่จำกัด และต้องรู้ว่า environment variable ที่ service ใช้อยู่มาจากไหน

ตัวอย่าง environment ที่สำคัญ:

| Env               | ใช้ทำอะไร                         |
|-------------------|----------------------------------|
| `ORACLE_DATA_DIR` | ชี้ไปที่ data store เช่น `~/.oracle`  |
| `ORACLE_DB_PATH`  | ชี้ SQLite database ถ้าต้อง override |
| `ORACLE_PORT`     | port ของ Oracle HTTP API         |
| `MAW_HOME`        | แยก instance ของ maw             |
| `MAW_PORT`        | port ของ maw server              |
| `MAW_CONFIG_DIR`  | ตำแหน่ง config และ fleet          |

### cron: งานตามเวลา

cron เหมาะกับงานที่ไม่ต้องรันตลอด แต่ต้องเกิดซ้ำ เช่นทุกชั่วโมงหรือทุกวัน

ตัวอย่างงานที่เหมาะ:

| รอบเวลา  | งาน                          | เหตุผล                |
|----------|------------------------------|----------------------|
| ทุก 5 นาที | health check service         | รู้เร็วถ้า API ดับ        |
| ทุกชั่วโมง  | sync feed หรือ index บางส่วน   | ให้ search ไม่ stale   |
| ทุกวัน     | backup `oracle.db` และ `psi` | กันข้อมูลหาย            |
| ทุกคืน     | สรุป task/feed                | ช่วยให้เช้าวันต่อไปเริ่มง่าย |

ไอเดียภาพประกอบ:

<div class="mermaid">

sequenceDiagram participant Cron as cron participant Health as health check participant Oracle as Oracle API participant Maw as maw status participant Inbox as psi/inbox Cron-\>\>Health: run every 5 minutes Health-\>\>Oracle: GET /api/stats Health-\>\>Maw: check /api/sessions Health-\>\>Inbox: write alert or handoff if failed

</div>

cron เป็นของง่าย แต่ต้องระวังเรื่อง path และ environment เพราะ cron ไม่ได้มี shell context เหมือน terminal ปกติ งานที่รันผ่าน cron ควร log ชัดว่าเริ่มเมื่อไร จบเมื่อไร และ fail เพราะอะไร

### systemd: service ระดับเครื่อง

systemd เหมาะเมื่ออยากให้ service เป็นส่วนหนึ่งของเครื่องจริง ๆ เช่น boot แล้วขึ้นเอง หรือมี dependency กับ network

PM2 เหมาะกับ app-level process ส่วน systemd เหมาะกับ machine-level service

ตารางเปรียบเทียบ:

| คำถาม                               | PM2           | systemd           |
|-------------------------------------|---------------|-------------------|
| ใช้ง่ายกับ Node/Bun ไหม                | มาก           | ได้ แต่ต้องเขียน unit |
| ดู log ยังไง                          | `pm2 logs`    | `journalctl`      |
| restart เมื่อ crash ได้ไหม             | ได้            | ได้                |
| เหมาะกับหลาย app ใน user space ไหม   | ใช่            | แล้วแต่ config      |
| เหมาะกับ service ที่ต้องเริ่มตอน boot ไหม | ได้ผ่าน startup | ใช่โดยตรง          |

ใน production จริง อาจใช้ systemd เพื่อคุม PM2 อีกชั้น หรือใช้ systemd คุม service โดยตรงก็ได้ สิ่งที่สำคัญกว่าเครื่องมือคือ runbook ต้องชัดว่า "ถ้าดับ ให้ดูตรงไหน และ restart อย่างไร"

### triggers: งานที่เกิดจาก event

triggers คือ automation ที่ไม่ได้เกิดตามเวลา แต่เกิดเพราะมี event

ตัวอย่าง:

| Event               | Trigger ที่ควรทำ                    |
|---------------------|-----------------------------------|
| task started        | เขียน feed event                   |
| task completed      | ส่ง `maw hey gale` หรือสร้าง handoff |
| PR merged           | cleanup worktree                  |
| health check failed | แจ้ง lead Oracle                   |
| context ใกล้เต็ม      | ทำ retrospective หรือ forward      |
| plugin installed    | update registry/feed              |

ในแผนที่ต้นน้ำจาก อาจารย์ Nat มีแนวคิด plugin surfaces หลายแบบ: CLI, API, peer transport, hooks, cron, module ซึ่งหมายความว่า plugin ไม่ได้เป็นแค่คำสั่ง terminal แต่สามารถเป็นส่วนหนึ่งของ runtime automation ได้

ไอเดียภาพประกอบ:

<div class="mermaid">

flowchart TB Event\[Event\<br/\>task done / health fail / PR merged\] --\> Trigger\[Trigger or hook\] Trigger --\> Feed\[Oracle feed\] Trigger --\> Maw\[maw hey / maw capture\] Trigger --\> Psi\[psi handoff / retro\] Trigger --\> Human\[Human decision if risk is high\]

</div>

### Autonomous ไม่เท่ากับไร้คนคุม

จุดสำคัญที่สุดของบทนี้คือ autonomous ops ต้องมีขอบเขต

ระบบควรทำเองได้:

- restart service ที่ crash
- ตรวจ health
- เก็บ log
- สรุปสถานะ
- เปิด task ที่ปลอดภัยและมีขอบเขต
- เตือนเมื่อมีปัญหา

ระบบไม่ควรทำเองโดยไม่มีมนุษย์:

- merge งาน production ที่มี risk สูง
- push force
- ลบข้อมูลถาวร
- เปลี่ยน config สำคัญโดยไม่บอก
- ปิด error จริงเพื่อให้ log ดูสะอาด
- ตัดสินใจ business แทนเจ้าของงาน

Oracle ที่ดีจึงไม่ใช่ระบบที่ "ทำอะไรก็ได้" แต่เป็นระบบที่รู้ว่าอะไรควรทำเอง อะไรควรถาม และอะไรต้องหยุด

### ตาราง runbook สำหรับ ebook

ใส่ตารางนี้เป็น template ในบท:

| สถานการณ์ | ตรวจอะไร | ใครรับผิดชอบ | Action |
|----|----|----|----|
| Oracle search ใช้ไม่ได้ | HTTP API + MCP + data path | Admin / Orchestrator | ตรวจ service status, log, `ORACLE_DATA_DIR` |
| maw ส่งข้อความไม่ได้ | `maw serve`, tmux sessions, fleet config | Admin / Orchestrator | `maw health`, `maw ls`, `maw doctor` |
| UI ไม่เห็นข้อมูล | API host, CORS, `?host=` parameter | Admin | ตรวจ browser console + API endpoint |
| worker ไม่ตอบ | tmux pane, session status | Orchestrator | `maw capture`, ส่ง reminder, restart ถ้าจำเป็น |
| memory ไม่อัพเดท | indexer, FTS5, vector DB | Admin | reindex หรือตรวจ ollama status |
| task จบแล้วไม่รายงาน | heartbeat protocol | Orchestrator | ตรวจ pane แล้วส่ง reminder ให้ report DONE/STUCK |

### Operating loop: วงจรดูแลตัวเอง

Autonomous ops ใน Oracle ควรถูกมองเป็น loop:

<div class="mermaid">

flowchart LR Observe\[Observe\<br/\>logs, feed, health\] --\> Decide\[Decide\<br/\>safe auto or human needed\] Decide --\> Act\[Act\<br/\>restart, notify, delegate\] Act --\> Record\[Record\<br/\>handoff, retro, learning\] Record --\> Improve\[Improve\<br/\>adjust trigger/runbook\] Improve --\> Observe

</div>

ทุกครั้งที่ระบบแก้ปัญหา ควรมีร่องรอยเหลือไว้ เช่น feed, handoff, learning หรือ retrospective เพราะ Oracle ไม่ได้วัดความเก่งจากการไม่เคยล้ม แต่วัดจากการล้มแล้วเรียนรู้ได้

### จาก skill ไป plugin: ทำไมสำคัญกับ ops

ในอดีต งานอย่าง `/learn`, `/incubate`, `/project` อยู่ในรูปแบบ skill ที่ agent อ่านและทำตาม แต่ในแผนที่ต้นน้ำจาก อาจารย์ Nat เห็นทิศทางใหม่คือย้ายบางงานไปเป็น maw plugin

ความหมายแบบง่าย:

| แบบเก่า                          | แบบใหม่                              |
|---------------------------------|-------------------------------------|
| Skill อยู่ใน folder ของแต่ละ agent | Plugin อยู่ใน runtime ของระบบ maw     |
| Agent แต่ละตัวตีความเอง            | Runtime มี contract กลาง             |
| ใช้ได้ดีใน session เดี่ยว            | ใช้ได้ผ่าน CLI, API, peer, hook        |
| automation ทำยากกว่า             | automation ต่อ trigger/cron ได้ง่ายกว่า |

นี่คือก้าวสำคัญของ autonomous ops เพราะถ้างานกลายเป็น plugin ที่มี CLI/API/peer/hook surface งานนั้นจะถูกเรียกโดยคน, agent, UI หรือ automation ได้เหมือนกัน

### บทสรุป

บทที่ 7 พูดเรื่อง "ใครทำอะไร" ในครอบครัว Oracle

บทที่ 8 พูดเรื่อง "งานไหลอย่างไร" ตั้งแต่ request ถึง done

บทที่ 8B พูดเรื่อง "พิสูจน์งานอย่างไร" ด้วยเอกสารและ traceability

บทที่ 9 พูดเรื่อง "ระบบทำงานต่อได้อย่างไร" เมื่อไม่มีใครนั่งจ้อง terminal ตลอดเวลา

สองบทนี้เชื่อมกันตรงนี้:

- ครอบครัว Oracle ทำให้แบ่งงานได้
- delegation tiers ทำให้ไม่ส่งงานผิดระดับ
- maw ทำให้การประสานงานมองเห็นได้
- memory ทำให้การเรียนรู้ไม่หาย
- pm2/cron/systemd/triggers ทำให้ระบบดูแลตัวเองได้บางส่วน
- มนุษย์ยังเป็นเจ้าของการตัดสินใจสำคัญ

ถ้าผู้อ่านจำได้แค่อย่างเดียว ให้จำว่า Oracle ไม่ใช่แค่ AI ที่ตอบคำถาม แต่เป็นระบบทำงานร่วมกันที่มีทีม มีความจำ มีเครื่องมือ และมีขอบเขตความรับผิดชอบ

</div>

<a href="ch08b.html" class="button secondary">← CMMI &amp; Compliance</a> <a href="ch10.html" class="button primary">Troubleshooting →</a>
