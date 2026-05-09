---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch06b.html
ingested_at: 2026-05-09
title: Advanced maw
language: th
---

<div class="chapter-header">

# Advanced maw commands

บทที่ 06 สอนคำสั่งหลักแล้ว บทนี้เป็นแผนที่ command surface เต็มเพื่อให้เลือกเครื่องมือถูกงาน

</div>

<div class="section chapter-content">

## 6B.1 Standard และ Extra ต่างจาก Core อย่างไร

บทที่ 06 คือ **Core 12 commands** ที่มากับ maw เสมอ บทนี้คือชั้นถัดไป: **Standard 25 commands** สำหรับงานทั่วไปที่ควรมีใน setup ปกติ และ **Extra 30 commands** สำหรับ plugin เสริม งานข้าม node ทีม agent และ workflow เฉพาะทาง

ทุก command ในบทนี้ใส่ label `standard` หรือ `extra` ชัดเจน คำสั่งที่ยังเป็น scaffold จะมี label `stub`

## 6B.2 Standard 25 commands

| Tier | Command | กลุ่ม | ใช้ทำอะไร |
|----|----|----|----|
| <span class="pill">standard</span> | `maw on <oracle> <event> "act"` | Events | สร้าง trigger ระดับ session ใช้กับ `--once` หรือ `--timeout` ได้ |
| <span class="pill">standard</span> | `maw whoami` | Identity | บอกว่า command นี้รันจาก session/identity ใด |
| <span class="pill">standard</span> | `maw overview` | Monitoring | war room dashboard; alias ที่ใช้บ่อยคือ `warroom` หรือ `ov` |
| <span class="pill">standard</span> | `maw zoom` | Navigation | toggle pane zoom ใน tmux เพื่อโฟกัส pane เดียว |
| <span class="pill">standard</span> | `maw about <oracle>` | Info | แสดงข้อมูล Oracle เช่น role, path, identity หรือ metadata |
| <span class="pill">standard</span> | `maw ui` | UI | จัดการ web UI ที่ใช้มอง fleet/session |
| <span class="pill">standard</span> | `maw kill <target>` | Navigation | kill session/window/pane ที่ระบุ ใช้หลังตรวจให้แน่ใจว่าไม่ใช่งานสำคัญ |
| <span class="pill">standard</span> | `maw transport status` | Federation | diagnostics ของ transport layer ก่อนสื่อสารข้าม node |
| <span class="pill">standard</span> | `maw tmux peek` | Tmux | tmux control verbs สำหรับดู pane/session ในระดับล่าง |
| <span class="pill">standard</span> | `maw tag <target>` | Metadata | set pane metadata เพื่อให้หาและจัดกลุ่มงานง่ายขึ้น |
| <span class="pill">standard</span> | `maw split <target>` | Navigation | split pane แล้ว attach งานที่ต้องดูคู่กัน |
| <span class="pill">standard</span> | `maw panes` | Monitoring | list panes พร้อม metadata ที่ maw รู้จัก |
| <span class="pill">standard</span> | `maw fleet ls` / `health` / `doctor` | Fleet | fleet management, health check และ auto-fix บางส่วน |
| <span class="pill">standard</span> | `maw plugin init` / `build` / `install` | Plugin | plugin lifecycle สำหรับสร้าง, build และติดตั้ง plugin |
| <span class="pill">standard</span> | `maw assign <issue> --oracle N` | Work | assign GitHub issue ให้ Oracle ที่รับผิดชอบ |
| <span class="pill">standard</span> | `maw restart` | Ops | restart maw server ตาม runbook |
| <span class="pill">standard</span> | `maw mega` | Teams | MegaAgent multi-agent teams สำหรับงานใหญ่ |
| <span class="pill">standard</span> | `maw pulse add` / `ls` / `cleanup` | Tasks | fleet-wide task tracking |
| <span class="pill">standard</span> | `maw federation status` | Federation | ดู multi-node status |
| <span class="pill">standard</span> | `maw capture <name> --lines N` | Monitoring | อ่านข้อความจาก tmux pane แบบ text ใช้ก่อน `peek` |
| <span class="pill">standard</span> | `maw soul-sync` | Memory | sync `ψ/memory` ระหว่าง Oracles |
| <span class="pill">standard</span> | `maw view <agent>` | Navigation | attach/create view; alias ที่ใช้ได้คือ `attach` หรือ `a` |
| <span class="pill">standard</span> | `maw session` | Identity | print current session name |
| <span class="pill">standard</span> | `maw locate <oracle>` | Discovery | หา oracle path, session และ fleet entry |
| <span class="pill">standard</span> | `maw check` | Diagnostics | audit tools เช่น ghq, gh, git, tmux และ bun |

## 6B.3 Extra 30 commands

| Tier | Command | Status | ใช้ทำอะไร |
|----|----|----|----|
| <span class="pill">extra</span> | `maw project` | <span class="pill">stub</span> | clone/track repos; ตอนนี้ dispatch ไป workflow ของ `/project` |
| <span class="pill">extra</span> | `maw costs` | ready | ดู token usage per agent |
| <span class="pill">extra</span> | `maw park <agent>` | ready | หยุดชั่วคราวโดยเก็บ context ไว้ |
| <span class="pill">extra</span> | `maw workon <repo> <slug>` | ready | สร้าง worktree + tmux window เพื่อเริ่มงาน code |
| <span class="pill">extra</span> | `maw resume <agent>` | ready | เปิดจากที่ park ไว้ |
| <span class="pill">extra</span> | `maw workspace` | ready | multi-node workspace |
| <span class="pill">extra</span> | `maw triggers` | ready | list active triggers |
| <span class="pill">extra</span> | `maw pair` | ready | bluetooth-style pairing สำหรับ peer |
| <span class="pill">extra</span> | `maw peers` | ready | manage peer aliases |
| <span class="pill">extra</span> | `maw inbox` | ready | agent inbox messages |
| <span class="pill">extra</span> | `maw talk-to <name> "msg"` | ready | สนทนาต่อเนื่องแบบ persistent thread และค้นย้อนหลังได้ |
| <span class="pill">extra</span> | `maw reunion` | ready | trigger federation sync |
| <span class="pill">extra</span> | `maw pr` | ready | สร้างหรือดู PR จาก branch ปัจจุบัน |
| <span class="pill">extra</span> | `maw tab` | ready | manage tmux tabs |
| <span class="pill">extra</span> | `maw completions` | ready | shell completions |
| <span class="pill">extra</span> | `maw find "keyword"` | ready | search ข้าม fleet และ `ψ/memory` |
| <span class="pill">extra</span> | `maw avengers` | ready | Avengers team สำหรับงานหลาย role |
| <span class="pill">extra</span> | `maw cleanup --zombie-agents` | ready | kill zombie panes และ cleanup surface ที่ค้าง |
| <span class="pill">extra</span> | `maw consent` | ready | PIN-consent สำหรับ cross-oracle actions |
| <span class="pill">extra</span> | `maw incubate` | <span class="pill">stub</span> | clone for dev; ตอนนี้ dispatch ไป workflow ของ `/incubate` |
| <span class="pill">extra</span> | `maw learn` | <span class="pill">stub</span> | explore codebase; ตอนนี้ dispatch ไป workflow ของ `/learn` |
| <span class="pill">extra</span> | `maw archive <oracle>` | ready | retire Oracle ด้วย soul-sync แล้ว disable ตาม policy |
| <span class="pill">extra</span> | `maw team create` / `spawn` / `send` | ready | สร้างทีม agent ชั่วคราวสำหรับงานซับซ้อน |
| <span class="pill">extra</span> | `maw art ls` / `get` / `write` | ready | task artifact management |
| <span class="pill">extra</span> | `maw cross-team-queue` | <span class="pill">stub</span> | unified inbox across vaults ยังเป็น scaffold |
| <span class="pill">extra</span> | `maw signals` | ready | อ่าน bud/absorb signals ก่อนจัดการ Oracle คนอื่น |
| <span class="pill">extra</span> | `maw rename <name> <new>` | ready | rename tab หรือ agent |
| <span class="pill">extra</span> | `maw broadcast "msg"` | ready | ส่งข้อความถึงทุก Oracle พร้อมกัน |
| <span class="pill">extra</span> | `maw demo` | ready | simulated session ที่ไม่ต้องใช้ API key |
| <span class="pill">extra</span> | `maw doctor` | ready | diagnostic checks และ auto-heal บางกรณี |

## 6B.4 Patterns ที่ควรจำ

| Pattern | จำแบบง่าย |
|----|----|
| `capture` vs `peek` | `capture` เป็น text และเหมาะกับ automation; `peek` เป็น screenshot ใช้เมื่อจำเป็นต้องเห็นภาพ |
| `park` / `resume` | พักโดยไม่ฆ่า context แล้วกลับมาทำต่อได้ |
| `maw on` vs persistent triggers | `maw on` เป็น session-scoped; trigger ที่ต้องอยู่ยาวควรอยู่ใน config fleet-wide |
| `talk-to` vs `broadcast` | `talk-to` คือคุยต่อเนื่องกับคนเดียว; `broadcast` คือประกาศให้ทั้ง fleet |
| `doctor` vs `check` | `check` audit tools; `doctor` diagnostic และ auto-heal บางส่วน |

## 6B.5 เลือก tier ให้ถูก

<div class="mermaid">

flowchart TD A{ต้องการทำอะไร} A --\>\|เริ่ม / ดูสถานะ / ปลุก / ปิดงาน\| Core\[Core commands\<br/\>บทที่ 06\] A --\>\|ดู pane / จัด tmux / ตรวจ fleet / plugin lifecycle\| Standard\[Standard commands\] A --\>\|worktree / team / federation peer / task artifacts\| Extra\[Extra commands\] A --\>\|stub project learn incubate queue\| Stub\[Extra stub\<br/\>ใช้เป็น intro เท่านั้น\]

</div>

กฎง่าย ๆ คือเริ่มจาก Core ก่อน ถ้า Core ไม่พอค่อยไป Standard และถ้าเป็นงานเฉพาะทางค่อยใช้ Extra โดยเฉพาะคำสั่งที่แตะ peer, workspace, cleanup หรือ team agent

</div>

<a href="ch06.html" class="button secondary">← maw commands</a> <a href="ch07.html" class="button primary">Orchestration →</a>
