---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch08.html
ingested_at: 2026-05-09
title: Workflow UseCase
language: th
---

<div class="chapter-header">

# Workflow UseCase

เส้นทางทำงานตั้งแต่รับโจทย์ ค้นบริบท แบ่งงาน QA จนถึง handoff

</div>

<div class="section chapter-content">

## 8.1 Workflow มาตรฐานแบบเต็ม

Oracle workflow ที่ดีไม่ใช่ให้ AI ทำทุกอย่างเอง แต่คือการทำให้บริบทไหลจากคน ไป Discord/Forum ไป worktree ไป PR ไป QA แล้วกลับมาเป็นผลลัพธ์ที่ตรวจสอบได้

<div class="mermaid compact-diagram">

flowchart TB Wind\[Wind request\] --\> Gale\[Gale triage\] Gale --\> Forum\[Discord Forum\<br/\>REQ / CR / BUG\] Forum --\> Brief\[Task Brief\<br/\>scope + acceptance\] Brief --\> Route{ส่งให้ใคร} Route --\>\|กลุ่มโปรเจคโรงงาน\| Leaf\[Leaf\] Route --\>\|software ทั่วไป\| Bamboo\[Bamboo\] Leaf --\> WT\["maw workon\<br/\>branch + tmux window"\] Bamboo --\> WT WT --\> PR\[maw pr\] PR --\> Docs\[Update docs\<br/\>SRS / SDD / UAT / RTM\] Docs --\> Kati\[Kati QA\] Kati --\> Gate{PASS?} Gate --\>\|FAIL\| WT Gate --\>\|PASS\| Merge\[Merge decision\] Merge --\> Deploy\[Deploy / release note\] Deploy --\> Done\["maw done\<br/\>Discord DONE\<br/\>handoff / rrr"\]

</div>

## 8.2 เริ่มงานด้วย context

ก่อนแก้งาน ให้ตอบ 5 คำถาม:

1.  นี่คือ requirement ใหม่, change request, bug, QA, deploy หรือ doc update?
2.  project อยู่กลุ่มโปรเจคโรงงานหรือ software ทั่วไป?
3.  source of truth อยู่ที่ repo, Discord forum, docs, memory หรือ service ไหน?
4.  ต้องมี PR, QA, doc update หรือ deploy หรือไม่?
5.  definition of done คืออะไร: PR สร้างแล้ว, Kati PASS, merge แล้ว, deploy แล้ว หรือ `maw done` แล้ว?

## 8.3 Worktree policy 3 ระดับ

ไม่ใช่ทุก repo มี policy เท่ากัน แต่กฎพื้นฐานคือให้เลือก flow ที่รักษา review, QA, lifecycle closer และ traceability ได้พอ

| ระดับ | กลุ่ม repo | push main | maw workon |
|----|----|----|----|
| **Strict** | กลุ่มโปรเจคโรงงาน / warehouse / production workflow | ปิดด้วย hook | บังคับ |
| **Preferred** | software ทั่วไป เช่น diary, webhook, project management | อนุญาตได้ แต่ควรมี warning | แนะนำอย่างแรง |
| **Unrestricted** | Oracle repos, tooling, docs, playground ที่ risk ต่ำ | อนุญาตตามบริบท | ใช้เมื่อมีงานยาวหรืออยากได้ lifecycle |

บทเรียนสำคัญ: “hook ไม่ block” ไม่ได้แปลว่า “ทางนี้ถูกต้อง” การข้าม `maw workon` ทำให้เสีย PR review, QA, lifecycle closer, `psi` sync และ traceability ได้ง่าย

## 8.4 Worktree-first workflow

งาน code ที่ไม่ใช่ trivial ควรเริ่มด้วย worktree เพื่อแยก branch, แยก tmux window และทำให้ cleanup ชัดเจน

| ขั้น | ทำอะไร | เหตุผล |
|----|----|----|
| 1 | `maw workon <repo> <slug>` | สร้าง worktree + branch + tmux window สำหรับงานนั้น |
| 2 | ทำงานใน worktree | ไม่แตะ main โดยตรง และไม่ชนงานของ Oracle อื่น |
| 3 | commit + verify | ผลลัพธ์ต้องมีหลักฐาน เช่น test, build, screenshot หรือ curl |
| 4 | `maw pr` | เปิด PR เป็นจุด review กลาง |
| 5 | ส่ง Kati QA โดยตรง | Gale monitor/merge ไม่ใช่ relay ทุกครั้ง |
| 6 | แก้ FAIL ใน branch/PR เดิม | ไม่แตก PR ใหม่จน trace ขาด |
| 7 | หลัง merge: `maw done <window>` | ปิด window, cleanup worktree, เขียน lifecycle event |

``` bash
maw workon <repo> <slug>
# ทำงาน + commit + verify
maw pr
maw hey kati "QA: PR #<N> on <repo>. Check: <acceptance criteria>."
maw hey gale "[leaf|bamboo] DONE: PR #<N> — sent to Kati."
# หลัง merge จาก home base
maw done <window-name>
```

`maw done` ต้องสั่งจาก home base ไม่ใช่จากใน worktree เพราะ self-target อาจ deadlock หรือถูก preflight ปฏิเสธได้

Anti-pattern ที่ควรถูก hook block ในกลุ่ม Strict:

``` bash
cd warehouse-app
git push origin main
```

ข้อความที่ถูกต้องควรประมาณว่า “project กลุ่มโรงงานต้องใช้ PR flow” แล้วให้กลับไปเริ่มด้วย `maw workon`

## 8.5 Full Pipeline 13 steps

1.  Wind request
2.  Gale parse ว่าเป็น REQ, CR หรือ BUG
3.  สร้าง GitHub issue ถ้าต้อง trace ระยะยาว
4.  สร้าง Discord forum post พร้อม tag `REQ`, `CR` หรือ `BUG`
5.  เขียน Task Brief พร้อม scope, acceptance criteria และ doc mandate
6.  `maw hey` dev Oracle ที่ถูก domain
7.  dev run `maw workon repo slug --prompt "task"` ใน home base ของตัวเอง
8.  ทำงาน, test, commit ใน feature branch
9.  เปิด PR ด้วย `maw pr`
10. dev auto-route ไป Kati QA โดยตรง
11. PASS/FAIL loop จนผ่าน หรือ escalate ถ้า fail เกิน 3 รอบ
12. merge ตาม policy ของ project
13. หลัง merge: `maw done`, update Discord เป็น `DEPLOY`/`DONE`, และเขียน handoff/rrr ถ้ามีบทเรียน

## 8.6 Discord SDLC pipeline

Discord เป็น tracking surface ของ SDLC ไม่ใช่ที่คุยเล่นเฉย ๆ แต่ละ project มี forum channel ของตัวเอง และใช้ tag เพื่อบอกสถานะของงาน

| Tag      | ใช้เมื่อ                          |
|----------|--------------------------------|
| `REQ`    | requirement หรือ user story ใหม่ |
| `SRS`    | อัปเดต requirement spec         |
| `SDD`    | อัปเดต design / architecture    |
| `DEV`    | กำลังพัฒนา                       |
| `CR`     | change request                 |
| `BUG`    | bug report หรือ QA fail         |
| `QA`     | ผลตรวจของ Kati                 |
| `E2E`    | ผลทดสอบ end-to-end             |
| `DOC`    | เอกสารที่เกี่ยวข้อง                 |
| `DEPLOY` | พร้อม deploy หรือ deploy แล้ว     |
| `DONE`   | เสร็จครบ loop แล้ว               |

<div class="mermaid">

sequenceDiagram participant W as Wind participant G as Gale participant D as Discord Forum participant Dev as Leaf / Bamboo participant K as Kati W-\>\>G: request / bug / change G-\>\>D: create forum post with REQ/CR/BUG G-\>\>D: post Task Brief + SRS/SDD notes G-\>\>Dev: delegate with worktree-first prompt Dev-\>\>D: update DEV / DOC when useful Dev-\>\>K: PR ready for QA K-\>\>D: post QA result K--\>\>Dev: FAIL loop if needed G-\>\>D: DEPLOY then DONE after merge

</div>

## 8.7 Project lifecycle

การเพิ่มหรือลบ project ไม่ควรเป็นแค่สร้าง repo เพราะ SDLC surface มีหลายชิ้น: repo, Discord channel, docs, channel map, worktree และ memory

| คำสั่ง | หน้าที่ | ข้อควรจำ |
|----|----|----|
| `project-lifecycle.sh create` | สร้าง repo + docs + Discord/forum mapping | ควรจบด้วย channel map ที่ค้นได้ |
| `project-lifecycle.sh audit` | ตรวจว่า repo, docs, channel, map ยังตรงกัน | ใช้ก่อนแก้ระบบ lifecycle |
| `project-lifecycle.sh remove` | ถอด project ออกจาก surface ที่เกี่ยวข้อง | ควรย้ายไป backup path เช่น `/tmp` ไม่ใช้ `rm -rf` |

หลักคิดคือ “ลบให้น้อยที่สุด เก็บหลักฐานให้มากที่สุด” เพราะ project ที่ดูเหมือนจบแล้วอาจยังมี trace, docs หรือ decision ที่ต้องอ้างถึงภายหลัง

## 8.8 Safety hooks

`arra-safety-hooks` เป็น guardrail จริง ไม่ใช่คำแนะนำ ถ้า hook block คำสั่ง แปลว่า command นั้นไม่ควรผ่านจนกว่าจะเปลี่ยนวิธีทำ

| กลุ่ม rule | ตัวอย่างที่ควรถูก block | เหตุผล |
|----|----|----|
| Destructive shell | `rm -rf`, ลบ path กว้างเกินไป | กันข้อมูลหายถาวร |
| Git history | `git reset --hard`, force push, amend | กัน rewrite ประวัติและทับงานคนอื่น |
| Bypass | `--no-verify` | ถ้า hook fail ต้องแก้เหตุ ไม่ใช่ข้าม guardrail |
| Main branch | push ตรงเข้า main ของ project repo | บังคับให้ผ่าน PR + QA |
| Guard ต้นน้ำจาก อาจารย์ Nat | สร้าง PR/comment ไป repo นอกขอบเขต | กันแตะ repo ต้นน้ำจาก อาจารย์ Nat หรือสื่อสารผิดที่ |

ชุด rule จริงมีประมาณ 12 ข้อและไม่มี override แบบ “ข้ามไปก่อน” ถ้า hook กับเอกสารไม่ตรงกัน ให้เชื่อ hook ก่อน แล้วค่อยอัปเดตเอกสารให้ตรงความจริง

## 8.9 Heartbeat protocol

งานที่ถูก delegate ต้องรายงานกลับ ไม่ใช่หายไปใน pane เงียบ ๆ โดยเฉพาะงาน Tier 3 หรือ worktree ที่รันนาน

| เวลา | สิ่งที่ควรเกิด | ตัวอย่าง message |
|----|----|----|
| ทุก 5 นาที | Progress heartbeat | `maw hey gale "[leaf] PROGRESS: finished API route"` |
| ติดขัดทันที | STUCK พร้อมหลักฐาน | `maw hey gale "[bamboo] STUCK: build fails at ..."` |
| เงียบ 10 นาที | Lead peek/capture | `maw capture <window> --lines 40` |
| เงียบ 20 นาที | Escalate ให้ Wind หรือ restart ตาม runbook | ใช้เมื่อ capture แล้วไม่ตอบจริง |
| เสร็จงาน | DONE พร้อม artifact | `maw hey gale "[kati] DONE: QA PASS PR #N"` |

## 8.10 QA Feedback Loop

Kati QA ไม่ใช่ขั้นตอนท้ายที่ทำครั้งเดียว ถ้า FAIL ต้องวนกลับ branch เดิม PR เดิม worktree เดิม เพื่อให้ trace ไม่แตก

<div class="mermaid">

flowchart LR PR\[Dev creates PR\] --\> Kati\[Kati QA\] Kati --\> Gate{PASS?} Gate --\>\|PASS\| Merge\[Merge\] Gate --\>\|FAIL\| Bug\[Gale post BUG in forum\] Bug --\> Fix\[maw hey dev fix same branch\] Fix --\> Push\[Dev push same PR\] Push --\> Retest\[Kati retest\] Retest --\> Gate Gate --\>\|FAIL 3 times\| Escalate\[Escalate Wind\]

</div>

Auto-route rule: เมื่อ dev เปิด PR แล้ว ต้องส่ง Kati โดยตรง ไม่ต้องรอ Gale relay

## 8.11 Test-One-Before-Batch

เวลาต้อง migrate หลายไฟล์ อย่าทำทั้งชุดก่อนค่อย test ให้ทำหนึ่งตัวอย่างก่อน ตรวจให้ผ่าน แล้วค่อย batch N ที่เหลือ

<div class="mermaid">

flowchart LR One\[เลือก 1 file\] --\> Migrate\[migrate\] Migrate --\> Verify\[verify build/test\] Verify --\> Gate{ผ่านไหม} Gate --\>\|ไม่\| Fix\[แก้ pattern\] Fix --\> Migrate Gate --\>\|ผ่าน\| Batch\[batch migrate N files\] Batch --\> VerifyAll\[verify ทั้งชุด\]

</div>

## 8.12 CMMI อยู่ตรงไหนใน workflow

งาน user-facing ต้อง update เอกสารใน PR เดียวกัน แต่รายละเอียด SRS, SDD, UAT, RTM, doc conversion และ traceability แยกไปที่ [บท 08B: CMMI & Compliance](ch08b.html) เพื่อไม่ให้บท workflow ยาวเกินไป

## 8.13 Handoff และ retrospective

งานที่จบควรมีร่องรอยอย่างน้อยหนึ่งอย่าง:

- Discord post/tag ถ้าเป็น project lifecycle
- PR link ถ้าเป็น code change
- QA PASS/FAIL ถ้างานต้องตรวจ
- `maw done` ถ้ามี worktree/window
- handoff ถ้างานยังต้องมี session ต่อ
- retrospective ถ้ามีบทเรียนหรือ failure mode

## 8.14 จุดจบบท

Workflow ที่ดีทำให้ agent ไม่ใช่แค่ตอบคำถาม แต่เป็นระบบงานที่อ่านต่อได้ ตรวจซ้ำได้ และส่งต่อได้ งานจบจริงเมื่อ trace ครบ: request, branch, PR, QA, merge, deploy, done และ memory

</div>

<a href="ch07.html" class="button secondary">← Orchestration</a> <a href="ch08b.html" class="button primary">CMMI &amp; Compliance →</a>
