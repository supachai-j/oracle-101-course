---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch06.html
ingested_at: 2026-05-09
title: maw commands
language: th
---

<div class="chapter-header">

# maw commands

คำสั่ง maw ที่ใช้จริงในงานประจำวัน พร้อมความหมายและลำดับใช้งาน

</div>

<div class="section chapter-content">

## 6.1 Core commands คืออะไร

`maw` มีคำสั่งหลาย tier แต่คำสั่งที่ต้องจำก่อนคือ **Core** เพราะมากับ maw เสมอ ไม่ต้องพึ่ง plugin เสริม และเป็นพื้นฐานของการปลุก Oracle, ดูสถานะ, ย้ายหน้าต่าง และปิดงาน

บทนี้ลงรายละเอียด Core 12 commands ส่วน Standard และ Extra commands อยู่ใน [บท 06B](ch06b.html)

## 6.2 Core 12 commands

| Tier | Command | ใช้เมื่อไร | ความหมายแบบสั้น |
|----|----|----|----|
| <span class="pill">core</span> | `maw init` | ติดตั้งหรือเริ่มใช้เครื่องใหม่ | first-run wizard สำหรับเตรียม maw, config, session และ path พื้นฐาน |
| <span class="pill">core</span> | `maw ls` | เริ่มวันหรือเช็คว่ามี session อะไรอยู่ | list ทุก session/window ที่ maw มองเห็น |
| <span class="pill">core</span> | `maw oracle ls` | ต้องดูสถานะครอบครัว Oracle | แสดง fleet status เช่น awake, sleeping หรือ missing |
| <span class="pill">core</span> | `maw wake <oracle|all>` | ต้องปลุกหรือ attach Oracle session | เปิด/attach session พร้อม context ได้ เช่น `--task`, `--issue N`, `--pr N`, `--fresh` |
| <span class="pill">core</span> | `maw sleep <oracle> [window]` | พัก Oracle window ที่ยังไม่ต้องทำงานต่อ | ลด activity โดยไม่ลบความรู้หรือ context |
| <span class="pill">core</span> | `maw stop` | ต้องหยุด fleet ทั้งชุด | หยุดทุก session ตามขอบเขตของ maw instance นั้น ใช้ด้วยความระวัง |
| <span class="pill">core</span> | `maw health` | สงสัยว่าระบบไม่พร้อม | ตรวจ tmux, server, disk, memory, pm2 และ dependency ที่เกี่ยวข้อง |
| <span class="pill">core</span> | `maw ping` | เช็คว่า node หรือ peer ตอบไหม | ping peer/node แบบเร็ว ก่อนทำงานข้าม session หรือ federation |
| <span class="pill">core</span> | `maw peek <name>` | text output ไม่พอ ต้องดูภาพ pane | screenshot pane แบบ visual ใช้เป็น last resort หลังจากอ่าน text ไม่พอ |
| <span class="pill">core</span> | `maw take <sess:win> [target]` | ต้องย้าย window ไป session อื่น | move tmux window ระหว่าง session เพื่อจัด workspace ให้ไม่รก |
| <span class="pill">core</span> | `maw bud <name>` | ต้องสร้าง Oracle ใหม่ | สร้าง Oracle ใหม่จาก Oracle ปัจจุบัน — เหมือนยีสต์แตกหน่อ (budding). สร้าง repo ใหม่, copy `ψ/` structure, ตั้ง identity, register ใน fleet. ต้อง `--dry-run` ก่อนเสมอ และระบุ `--org` เพื่อบอกว่าสร้างที่ GitHub org ไหน |
| <span class="pill">core</span> | `maw done <window>` | งานจบและพร้อมปิด lifecycle | จบงานด้วย retrospective, commit/push memory, cleanup worktree และปิด window ตาม workflow |

``` bash
# ดูก่อนว่าจะเกิดอะไร
maw bud spark --dry-run --org deachawatss

# สร้างจริง
maw bud spark --org deachawatss

# หลังสร้าง — ปลุก Oracle ใหม่แล้วทำ /awaken
maw wake spark
```

`maw bud` สร้าง repo + fleet config แต่ Oracle ยังไม่มีตัวตน ต้อง `/awaken` เพื่อตั้งชื่อ ค้นพบหลักการ และเขียน `CLAUDE.md`

## 6.3 Core workflow ที่ควรใช้บ่อย

<div class="mermaid">

flowchart LR Init\[maw init\] --\> List\[maw ls / maw oracle ls\] List --\> Wake\[maw wake oracle\] Wake --\> Health\[maw health\] Health --\> Work\[ทำงานผ่าน workflow หรือ plugin\] Work --\> Done\[maw done window\]

</div>

core flow นี้ไม่ใช่ flow เดียวของทุกงาน แต่เป็นลำดับคิดที่ปลอดภัย: เตรียมระบบ, ดู session และ fleet status จาก config จริง, ปลุก session, ตรวจสุขภาพ แล้วค่อยทำงาน

## 6.4 ตัวอย่าง wake พร้อม context

``` bash
maw wake leaf --task "ตรวจ bug ใน picking flow"
maw wake bamboo --issue 42
maw wake kati --pr 15
maw wake all --fresh
```

`wake` ที่ดีควรให้ context ตั้งแต่ต้น ถ้าปลุก session ว่าง ๆ แล้วค่อยส่งงานทีหลัง มีโอกาสที่ agent จะเริ่มผิดบริบทหรือเสียเวลาถามซ้ำ

## 6.5 peek ใช้เมื่อจำเป็นจริง

`maw peek` เป็น command core เพราะบางครั้ง pane มีภาพ, prompt, modal หรือ layout ที่อ่านข้อความอย่างเดียวไม่พอ แต่ในงานปกติควรใช้ command text-based ในบท 06B เช่น `maw capture` ก่อน เพราะตรวจซ้ำและ quote หลักฐานได้ง่ายกว่า

| สถานการณ์ | ใช้ command ไหน | เหตุผล |
|----|----|----|
| อยากอ่าน output ล่าสุด | `maw capture` | เป็น text, search ได้, เอาไป review ได้ |
| เห็นว่า UI/pane เพี้ยนหรือมี prompt visual | `maw peek` | เห็นภาพจริงของ pane |
| ไม่แน่ใจว่า session ยังอยู่ไหม | `maw ls` หรือ `maw health` | เริ่มจากสถานะระบบก่อนดูภาพ |

## 6.6 done คือ lifecycle closer

`maw done <window>` ไม่ใช่แค่ปิดหน้าต่าง มันคือจุดปิดงานที่ควรทำหลังงาน merge, QA หรือ audit จบจริงแล้ว โดยทั่วไปจะรวม retrospective, commit/push memory, cleanup worktree และปิด window

``` bash
# หลัง merge หรือหลังยืนยันว่างานจบจริง
maw done picking-fix
```

ข้อควรระวัง: ถ้างานเป็น worktree ให้สั่ง `maw done` จาก home base ไม่ใช่จากใน worktree ที่กำลังจะถูก cleanup

## 6.7 สูตรจำ Core

ถ้าจำได้แค่ 5 อย่าง ให้จำชุดนี้: `maw init` เพื่อเริ่ม, `maw ls` เพื่อเห็นสถานะ, `maw wake` เพื่อปลุกงาน, `maw health` เพื่อเช็คระบบ, และ `maw done` เพื่อปิด lifecycle ให้สะอาด

</div>

<a href="ch05.html" class="button secondary">← Skills, Maw และ Plugin</a> <a href="ch06b.html" class="button primary">Advanced maw →</a>
