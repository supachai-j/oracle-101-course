---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch10.html
ingested_at: 2026-05-09
title: Troubleshooting
language: th
---

<div class="chapter-header">

# Troubleshooting

Decision tree และวิธีแก้ปัญหา Oracle แยกตามชั้นระบบ

</div>

<div class="section chapter-content">

บทนี้เป็นคู่มือแก้ปัญหา Oracle 101 สำหรับคนที่ใช้งานจริง ไม่ต้องจำทุก repo และไม่ต้องเดาว่าปัญหาอยู่ตรงไหน ให้เริ่มจากการแยกชั้นของระบบก่อน แล้วค่อยตรวจทีละจุด

เนื้อหานี้อ้างอิงจาก source ต้นน้ำของอาจารย์ Nat ซึ่งแบ่ง Oracle ออกเป็นหลายชั้นหลัก:

- `arra-oracle-v3` คือชั้นความจำ มี MCP, HTTP API, SQLite, FTS5, LanceDB, และ `psi`
- `maw-js` คือชั้นจัดการ agent ใช้ tmux, fleet config, CLI, API, websocket, และ plugin
- `arra-oracle-skills-cli` คือชั้นติดตั้ง skill ให้ agent เช่น Claude Code และ Codex
- `maw-ui` และ `ui-oracle` คือชั้นหน้าจอ เป็น lens ที่อ่านข้อมูลจาก API
- book repos เป็นแหล่งความรู้ ไม่ใช่ runtime dependency

## 10.1 หลักคิดก่อนแก้ปัญหา

เวลา Oracle เสีย อย่าเริ่มจากการ reinstall ทั้งหมด ให้ถามก่อนว่า "เสียที่ชั้นไหน"

แบ่งเป็น 5 ชั้น:

1.  Agent หรือ Skill - คำสั่งหาย, skill ไม่ขึ้น, MCP tool ไม่มา
2.  Oracle Memory - search ไม่เจอ, learn ไม่เข้า, database ว่าง
3.  Oracle HTTP API - UI ต่อไม่ได้, `curl` ไม่ตอบ, port `47778` ไม่เปิด
4.  Maw Orchestration - `maw ls` ไม่เห็น agent, ส่งข้อความไม่ได้, tmux pane ตาย
5.  UI - หน้าเว็บเปิดได้แต่ข้อมูลไม่มา, host ผิด, CORS หรือ API ผิดตัว

กฎง่ายๆ:

- ถ้า agent ใช้ tool ไม่ได้ ให้ตรวจ MCP และ skill ก่อน
- ถ้า UI ไม่มีข้อมูล ให้ตรวจ HTTP API ก่อน
- ถ้าส่งงานหา agent ไม่ได้ ให้ตรวจ `maw-js`, tmux, และ fleet config
- ถ้า search ไม่มีผลลัพธ์ ให้ตรวจ `ORACLE_DATA_DIR`, SQLite, FTS, และ vector index
- ถ้าทุกอย่างดูถูกแต่ยังเสีย ให้ดู log ของ process ที่รันจริง

## 10.2 คำสั่งตรวจสุขภาพเร็ว

ใช้ชุดนี้ก่อนเสมอ เพื่อรู้ว่าระบบยังหายใจอยู่หรือไม่

``` bash
date
pwd
echo "$ORACLE_DATA_DIR"
ss -ltnp | rg ':47778|:3456'
```

ตรวจ Oracle HTTP API:

``` bash
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=5"
curl -s http://localhost:47778/swagger | head
```

ตรวจ Oracle data:

``` bash
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}"
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db"
sqlite3 "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db" "select count(*) from oracle_documents;"
```

ตรวจ maw และ fleet:

``` bash
maw ls
maw oracle ls
tmux ls
maw capture gale --lines 20
```

ตรวจ MCP ฝั่ง Codex:

``` bash
codex mcp list
codex mcp get arra-oracle-v3
```

ชื่อ MCP อาจต่างกันในแต่ละเครื่อง ถ้า `arra-oracle-v3` ไม่เจอ ให้ดูชื่อจริงจาก `codex mcp list`

## 10.3 Decision Tree หลัก: เริ่มตรงไหนดี

แผนภาพนี้เป็นภาพรวมสำหรับคนเริ่มแก้ปัญหา

<div class="mermaid main-decision-tree">

flowchart TD A\[เริ่ม: Oracle ใช้งานไม่ได้\] --\> B{เสียตรงไหน} B --\>\|คำสั่งใน agent หาย\| C\[ตรวจ skill และ MCP\] B --\>\|UI ไม่มีข้อมูล\| D\[ตรวจ HTTP API :47778\] B --\>\|ส่งหา agent ไม่ได้\| E\[ตรวจ maw, tmux, fleet\] B --\>\|search ไม่เจอข้อมูล\| F\[ตรวจ data dir, SQLite, FTS, vector\] B --\>\|เปิดเว็บไม่ได้\| G\[ตรวจ port, host, proxy\] C --\> C1\[ls ~/.codex/skills หรือ ~/.claude/skills\] C --\> C2\[codex mcp list / get\] C2 --\> H{MCP ตอบไหม} H --\>\|ไม่\| H1\[แก้ command, env, path, restart agent\] H --\>\|ใช่\| H2\[ทดสอบ tool read-only\] D --\> D1\[curl /api/stats\] D1 --\> I{API ตอบไหม} I --\>\|ไม่\| I1\[start server หรือดู log\] I --\>\|ใช่\| I2\[ตรวจ query, data, CORS\] E --\> E1\[maw ls และ tmux ls\] E1 --\> J{session ยังอยู่ไหม} J --\>\|ไม่\| J1\[maw wake หรือสร้าง session ใหม่\] J --\>\|ใช่\| J2\[maw capture ดู pane ล่าสุด\] F --\> F1\[echo ORACLE_DATA_DIR\] F1 --\> K{ชี้ data ถูกไหม} K --\>\|ไม่\| K1\[ตั้ง ORACLE_DATA_DIR ให้ตรง canonical store\] K --\>\|ใช่\| K2\[ตรวจ oracle.db และ reindex ถ้าจำเป็น\] G --\> G1\[ss -ltnp ตรวจ port\] G1 --\> L{port เปิดไหม} L --\>\|ไม่\| L1\[start maw serve หรือ Oracle server\] L --\>\|ใช่\| L2\[ตรวจ host param และ API URL\]

</div>

## 10.4 ปัญหา: Agent ไม่มี skill หรือคำสั่งหาย

อาการที่เห็นได้:

- พิมพ์ `/learn`, `/project`, `/rrr` หรือ skill อื่นแล้วไม่มีคำสั่ง
- Codex เห็น skill ไม่ครบ แต่ Claude เห็น
- skill version ไม่ตรงกันระหว่าง agent
- command stub อยู่ผิดโฟลเดอร์

สาเหตุที่พบบ่อย:

- ติดตั้ง skill ไปผิด agent
- ใช้ profile ผิด เช่นติดตั้ง minimal แต่ต้องการ full หรือ lab
- agent ยังไม่ได้ restart หลังติดตั้ง
- มี skill เก่าค้างและบัง version ใหม่

ตรวจแบบเร็ว:

``` bash
ls -la ~/.codex/skills | head
ls -la ~/.claude/skills | head
ls -la ~/.codex/prompts | head
arra-oracle-skills --help
```

วิธีแก้ทั่วไป:

- ติดตั้ง skill ใหม่ไปยัง agent ที่ถูกต้อง
- ใช้ profile ที่ต้องการจริง เช่น full หรือ lab
- ลบหรือย้าย skill เก่าที่ conflict ออกไปไว้ที่ backup path
- restart agent session หลังติดตั้ง

ยืนยันผล:

``` bash
ls ~/.codex/skills
ls ~/.claude/skills
codex mcp list
```

ถ้าคำสั่งยังไม่มา ให้ตรวจว่า agent อ่าน skill จาก path ไหน ไม่ควรเดาจาก path ของ agent อื่น

## 10.5 ปัญหา: MCP ต่อไม่ได้

MCP คือทางที่ agent ใช้คุยกับ Oracle memory ผ่าน stdio ใน source ต้นน้ำของอาจารย์ Nat ระบุว่า entrypoint ฝั่ง MCP คือ `arra-oracle-v3/src/index.ts` หรือ wrapper script เช่น `start-mcp.sh`

อาการที่เห็นได้:

- agent ไม่มี Oracle tool
- tool เรียกแล้ว timeout
- tool บอกหา database ไม่เจอ
- MCP process ขึ้นแล้วตายทันที

ตรวจ config:

``` bash
codex mcp list
codex mcp get arra-oracle-v3
```

ถ้าชื่อไม่ตรง ให้ใช้ชื่อจริงจาก list:

``` bash
codex mcp list
```

ตรวจ path และ env:

``` bash
ls -la <ghq-root>/arra-oracle-v3/start-mcp.sh
echo "$ORACLE_DATA_DIR"
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}"
```

วิธีแก้ทั่วไป:

- ให้ MCP command ชี้ script ที่มีอยู่จริง
- ให้ `ORACLE_DATA_DIR` ชี้ data store ที่ถูกต้อง
- ตรวจว่า script รันได้และมี permission execute
- restart agent หลังแก้ MCP config

ยืนยันผล:

``` bash
codex mcp list
codex mcp get arra-oracle-v3
```

จากนั้นลองเรียก tool แบบ read-only เช่น stats, search, list หรือ concepts ถ้า tool มีให้ใช้ใน agent นั้น

## 10.6 ปัญหา: HTTP API ไม่ตอบ

HTTP API ของ `arra-oracle-v3` ใช้ port ปกติคือ `47778` UI หลายตัว เช่น `ui-oracle` จะคุยกับ API นี้ ไม่ใช่ MCP

อาการที่เห็นได้:

- UI เปิดได้แต่ข้อมูลว่าง
- `curl http://localhost:47778/api/stats` ไม่ตอบ
- browser ขึ้น network error
- Swagger เปิดไม่ได้

Decision tree:

<div class="mermaid">

flowchart TD A\[UI หรือ curl ต่อ Oracle API ไม่ได้\] --\> B\[curl /api/stats\] B --\> C{มี response ไหม} C --\>\|ไม่มี\| D\[ตรวจ port 47778\] D --\> E\["ss -ltnp \| rg ':47778'"\] E --\> F{มี process ฟัง port ไหม} F --\>\|ไม่มี\| G\[start Oracle HTTP server\] F --\>\|มี\| H\[ดู log process ที่ถือ port\] C --\>\|มี\| I\[API ยังอยู่\] I --\> J{search มีข้อมูลไหม} J --\>\|ไม่มี\| K\[ตรวจ data dir และ database\] J --\>\|มี\| L\[ปัญหาอยู่ที่ UI host, proxy, หรือ CORS\]

</div>

ตรวจ:

``` bash
curl -i http://localhost:47778/api/stats
curl -i "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=5"
ss -ltnp | rg ':47778'
```

วิธีแก้ทั่วไป:

- start หรือ restart Oracle HTTP server
- ตรวจ env `ORACLE_PORT` ถ้าไม่ได้ใช้ `47778`
- ตรวจว่า UI ชี้ API host ถูกตัว
- ถ้า API ตอบ แต่ UI ไม่เห็นข้อมูล ให้ดู browser network tab หรือ proxy config

ยืนยันผล:

``` bash
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=test&mode=hybrid&limit=3"
```

## 10.7 ปัญหา: Search ไม่เจอข้อมูล

Search ใน `arra-oracle-v3` ใช้ FTS5 และ vector search ร่วมกัน ถ้า vector ล้ม ระบบควรยังคืนผลจาก FTS ได้ ดังนั้น search ว่างไม่ได้แปลว่า vector เสียอย่างเดียว อาจเป็น data dir ผิด หรือ database ว่าง

อาการที่เห็นได้:

- `/api/search` ตอบ `[]`
- MCP search ไม่มีผลลัพธ์
- stats นับเอกสารน้อยผิดปกติ
- search เจอเฉพาะบาง repo

Decision tree:

<div class="mermaid">

flowchart TD A\[Search ไม่เจอข้อมูล\] --\> B\[ตรวจ ORACLE_DATA_DIR\] B --\> C{ชี้ canonical store ไหม} C --\>\|ไม่\| D\[แก้ ORACLE_DATA_DIR\] C --\>\|ใช่\| E\[ตรวจ oracle.db\] E --\> F{database มีเอกสารไหม} F --\>\|ไม่มี\| G\[run index/import ตามคู่มือ repo\] F --\>\|มี\| H\[ทดสอบ FTS query ง่ายๆ\] H --\> I{FTS เจอไหม} I --\>\|ไม่\| J\[ตรวจ FTS table หรือ reindex\] I --\>\|ใช่\| K\[ตรวจ vector health ถ้า hybrid แปลก\] K --\> L{vector fail ไหม} L --\>\|ใช่\| M\[ตรวจ Ollama, embedding model, LanceDB path\] L --\>\|ไม่\| N\[ตรวจคำค้น, filter, project scope\]

</div>

ตรวจ data path:

``` bash
echo "$ORACLE_DATA_DIR"
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}"
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db"
```

ตรวจจำนวนเอกสาร:

``` bash
sqlite3 "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db" "select count(*) from oracle_documents;"
sqlite3 "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db" ".tables"
```

ตรวจผ่าน API:

``` bash
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=5"
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=5"
```

วิธีแก้ทั่วไป:

- ให้ MCP และ HTTP ใช้ `ORACLE_DATA_DIR` เดียวกัน
- ถ้า database ว่าง ให้ run index/import ใหม่ตาม command ของ repo
- ถ้า FTS ใช้ได้แต่ hybrid แปลก ให้ตรวจ LanceDB, Ollama, และ embedding model
- ถ้าใช้หลาย model เช่น `nomic`, `qwen3`, `bge-m3` ให้จำว่าแต่ละ model มี collection แยกกัน

ยืนยันผล:

``` bash
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=3"
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=3"
```

## 10.8 ปัญหา: Maw ส่งข้อความหา agent ไม่ได้

`maw-js` ไม่ได้สร้าง worker ล่องหน มันควบคุม session จริงใน tmux ดังนั้นถ้า maw ส่งข้อความไม่ได้ ให้ตรวจ tmux และ fleet ก่อน

อาการที่เห็นได้:

- `maw hey gale "..."` ไม่ถึง
- `maw capture gale` ไม่มี output
- `maw ls` ไม่เห็น session ที่ควรมี
- `maw oracle ls` แสดงสถานะผิด
- UI ของ maw เห็น agent แต่ส่งคำสั่งไม่ได้

Decision tree:

<div class="mermaid">

flowchart TD A\[maw ส่งข้อความไม่ได้\] --\> B\[maw ls\] B --\> C{เห็น session ไหม} C --\>\|ไม่\| D\[ตรวจ fleet config\] D --\> E\[maw oracle ls\] E --\> F{oracle อยู่ใน fleet ไหม} F --\>\|ไม่\| G\[เพิ่มหรือแก้ fleet config\] F --\>\|ใช่\| H\[maw wake หรือเปิด session\] C --\>\|ใช่\| I\[tmux ls\] I --\> J{tmux session ยังอยู่ไหม} J --\>\|ไม่\| H J --\>\|ใช่\| K\[maw capture oracle --lines 20\] K --\> L{pane responsive ไหม} L --\>\|ไม่\| M\[restart session หรือแก้ process ใน pane\] L --\>\|ใช่\| N\[ตรวจ command, target name, plugin\]

</div>

ตรวจ:

``` bash
maw ls
maw oracle ls
tmux ls
maw capture gale --lines 20
```

วิธีแก้ทั่วไป:

- ใช้ชื่อ oracle ให้ตรงกับ fleet เช่น `gale`, `leaf`, `luna`
- ถ้า tmux session หาย ให้ wake หรือสร้างใหม่ด้วย maw
- ถ้า pane ค้าง ให้ capture log ก่อน แล้วค่อย restart session
- ถ้า command มาจาก plugin ให้ตรวจว่า plugin install และ enable แล้ว

ยืนยันผล:

``` bash
maw hey gale "[test] hello from troubleshooting check"
maw capture gale --lines 20
```

## 10.9 ปัญหา: maw serve หรือ maw-ui ไม่ขึ้น

`maw serve` ใช้ port ปกติคือ `3456` และ `maw-ui` เป็น lens ที่อ่าน API/websocket จาก maw server

อาการที่เห็นได้:

- dashboard เปิดไม่ได้
- websocket ตัด
- UI เห็นข้อมูลเก่า
- `?host=` ชี้ผิด node

ตรวจ:

``` bash
ss -ltnp | rg ':3456'
curl -s http://localhost:3456/api/config
curl -s http://localhost:3456/api/sessions
```

วิธีแก้ทั่วไป:

- start `maw serve`
- ตรวจ `MAW_PORT` หรือ `maw.config.json.port`
- ตรวจ `MAW_HOME` ถ้าใช้ instance แยก
- ถ้าใช้ hosted UI ให้ตรวจ `?host=` ว่าชี้ maw instance ที่ต้องการ
- ถ้า websocket fail ให้ดู log ของ `maw serve`

ยืนยันผล:

``` bash
curl -s http://localhost:3456/api/sessions
maw ls
```

## 10.10 ปัญหา: UI เปิดได้ แต่ข้อมูลผิด

จาก source ต้นน้ำของอาจารย์ Nat, `ui-oracle` เป็น active UI monorepo ส่วน standalone UI เก่า เช่น `ui-studio-oracle-studio`, `ui-vector-oracle-studio`, และ `ui-canvas-oracle-studio` เป็น predecessor ไม่ใช่ deployment source หลัก

อาการที่เห็นได้:

- หน้าเว็บโหลด แต่ข้อมูลไม่ตรงกับเครื่องนี้
- search ใน UI ต่างจาก search ใน terminal
- UI ต่อ API ของคนละเครื่อง
- browser จำ host เก่าไว้

ตรวจ:

``` bash
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=3"
```

ใน browser ให้ดู:

- Network request ยิงไป host ไหน
- URL มี `?host=` หรือไม่
- local storage เก็บ API host เก่าหรือไม่
- console มี CORS หรือ mixed content error หรือไม่

วิธีแก้ทั่วไป:

- ตั้ง UI host ให้ตรงกับ Oracle API จริง
- ล้าง host override ใน local storage ถ้ามี
- อย่าแก้ standalone UI เก่า ถ้า production ใช้ `ui-oracle`
- ถ้า API ตอบถูก แต่ UI ผิด ให้แก้ที่ host helper หรือ proxy ของ UI

ยืนยันผล:

``` bash
curl -s http://localhost:47778/api/stats
```

แล้ว reload UI และดูว่า request ไป API เดียวกัน

## 10.11 ปัญหา: Plugin ใช้ไม่ได้

ระบบ plugin ของ maw มี runtime directory หลักคือ `~/.maw/plugins` และ plugin ต้องมี `plugin.json` maw จะสแกน, validate, sort ด้วย weight, แล้ว expose command/API ตาม manifest

อาการที่เห็นได้:

- `maw <plugin-command>` ไม่เจอ
- plugin อยู่ใน repo แต่ runtime ไม่เห็น
- API `/api/<plugin>` ไม่ตอบ
- plugin ใช้ได้ใน dev แต่ไม่ได้ใน runtime

ตรวจ:

``` bash
ls -la ~/.maw/plugins
find ~/.maw/plugins -maxdepth 2 -name plugin.json -print
maw plugin list
```

ถ้า `maw plugin list` ไม่มีใน version นั้น ให้ตรวจจาก directory และ log ของ `maw serve`

วิธีแก้ทั่วไป:

- ติดตั้ง plugin ลง `~/.maw/plugins`
- ตรวจ `plugin.json` ว่ามี command/API surface ที่ต้องการ
- ตรวจว่า plugin ไม่ถูก disable ใน config
- restart `maw serve` หลังเพิ่ม plugin

ยืนยันผล:

``` bash
maw ls
curl -s http://localhost:3456/api/plugins
```

## 10.12 ปัญหา: ข้อมูลเขียนเข้าผิดที่

ปัญหานี้สำคัญมาก เพราะ Oracle อาจ "ทำงานได้" แต่เขียน memory ไปคนละ store

อาการที่เห็นได้:

- learn แล้วอีก agent ไม่เห็น
- Codex เห็นเอกสารน้อยกว่า Claude
- HTTP stats กับ MCP stats ไม่เท่ากัน
- inbox หรือ handoff ไปอยู่ผิด repo

สาเหตุที่พบบ่อย:

- `ORACLE_DATA_DIR` ไม่เหมือนกันระหว่าง process
- `ORACLE_REPO_ROOT` override ไป path อื่น
- ใช้ data default จาก source ต้นน้ำของอาจารย์ Nat `~/.arra-oracle-v2` แทน canonical fleet store
- agent MCP config มี env คนละชุดกับ HTTP server

ตรวจ:

``` bash
echo "$ORACLE_DATA_DIR"
echo "$ORACLE_REPO_ROOT"
ls -la ~/.oracle
ls -la ~/.arra-oracle-v2
curl -s http://localhost:47778/api/stats
```

ตรวจ DB ที่ process ควรใช้:

``` bash
sqlite3 "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db" "select count(*) from oracle_documents;"
```

วิธีแก้ทั่วไป:

- เลือก canonical store หนึ่งตัว
- ตั้ง `ORACLE_DATA_DIR` ให้เหมือนกันใน MCP, HTTP, shell, service manager
- ตั้ง `ORACLE_REPO_ROOT` เฉพาะเมื่อจำเป็น และต้องรู้ว่า write path จะเปลี่ยน
- restart process หลังแก้ env

ยืนยันผล:

``` bash
curl -s http://localhost:47778/api/stats
codex mcp list
```

จากนั้นลอง search ผ่านทั้ง HTTP และ MCP ว่าเห็นข้อมูลชุดเดียวกัน

## 10.13 ปัญหา: Vector หรือ embedding เสีย

Vector search ใช้ LanceDB และ embedding provider เช่น Ollama ตาม source ต้นน้ำของอาจารย์ Nat ระบุว่า model หลักมี `nomic`, `qwen3`, และ `bge-m3`

อาการที่เห็นได้:

- hybrid search ช้า
- hybrid search log มี vector error
- FTS search ได้ แต่ vector search ไม่ได้
- model หนึ่งมีผลลัพธ์ แต่อีก model ไม่มี

ตรวจ FTS ก่อน:

``` bash
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=5"
```

ตรวจ hybrid:

``` bash
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=5"
```

ตรวจ vector storage:

``` bash
ls -la "${ORACLE_DATA_DIR:-$HOME/.oracle}/lancedb"
```

ตรวจ Ollama ถ้าใช้ local embedding:

``` bash
ollama list
```

วิธีแก้ทั่วไป:

- ถ้า FTS ใช้ได้ ให้ระบบยังพอใช้งานได้ระหว่างแก้ vector
- ตรวจว่า model ที่ตั้งไว้มีอยู่จริง
- ตรวจว่า LanceDB path อยู่ใต้ data dir ที่ถูก
- reindex vector เมื่อเปลี่ยน model หรือ collection

ยืนยันผล:

``` bash
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=3"
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=3"
```

## 10.14 ปัญหา: Book หรือ docs ใช้ผิด repo เป็น runtime

ใน source ต้นน้ำของอาจารย์ Nat ระบุชัดว่า book repos เช่น `multi-agent-orchestration-book`, `agents-that-remember`, และ `the-agent-bus` เป็น source material ไม่ใช่ runtime dependency

อาการที่เห็นได้:

- พยายาม start service จาก book repo
- แก้ docs แล้วคาดว่า Oracle runtime จะเปลี่ยน
- หาคำสั่ง server ใน repo หนังสือไม่เจอ

วิธีคิดที่ถูก:

- ถ้าจะแก้ memory runtime ให้ดู `arra-oracle-v3`
- ถ้าจะแก้ agent orchestration ให้ดู `maw-js`
- ถ้าจะแก้ UI active ให้ดู `ui-oracle` หรือ `maw-ui`
- ถ้าจะอ่านแนวคิดหรือเขียนคู่มือ ให้ใช้ book repos

ยืนยัน repo:

``` bash
pwd
git remote -v
ls
```

## 10.15 Checklists แยกตามอาการ

### A. UI ไม่มีข้อมูล

``` bash
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&mode=hybrid&limit=3"
ss -ltnp | rg ':47778'
```

ถ้า API ไม่ตอบ ให้ start Oracle HTTP server ถ้า API ตอบ ให้ตรวจ UI host, proxy, CORS, และ browser storage

### B. Agent ไม่มี Oracle tools

``` bash
codex mcp list
codex mcp get arra-oracle-v3
ls -la ~/.codex/skills
```

ถ้า MCP ไม่อยู่ ให้เพิ่ม config ถ้า MCP อยู่แต่ใช้ไม่ได้ ให้ตรวจ command, env, path, และ restart agent

### C. Search ว่าง

``` bash
echo "$ORACLE_DATA_DIR"
sqlite3 "${ORACLE_DATA_DIR:-$HOME/.oracle}/oracle.db" "select count(*) from oracle_documents;"
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=5"
```

ถ้า count เป็น 0 ให้ index/import ใหม่ ถ้า FTS มีผล แต่ hybrid ไม่มี ให้ตรวจ vector ถ้าทั้งสองว่าง ให้ตรวจ data dir หรือ query scope

### D. maw ส่งข้อความไม่ได้

``` bash
maw ls
maw oracle ls
tmux ls
maw capture gale --lines 20
```

ถ้า session ไม่มี ให้ wake หรือสร้าง session ถ้า session มีแต่ pane ค้าง ให้ capture log แล้ว restart เฉพาะ session นั้น ถ้าชื่อ oracle ผิด ให้ใช้ชื่อจาก `maw oracle ls`

### E. Plugin ไม่ขึ้น

``` bash
ls -la ~/.maw/plugins
find ~/.maw/plugins -maxdepth 2 -name plugin.json -print
curl -s http://localhost:3456/api/plugins
```

ถ้า plugin ไม่มีใน runtime ให้ install หรือ symlink ให้ถูก ถ้า plugin มีแต่ command ไม่ขึ้น ให้ตรวจ manifest surface และ disabled config

## 10.16 Decision Tree สำหรับบทสรุปหน้าเดียว

<div class="mermaid">

flowchart LR A\[อาการ\] --\> B{เกี่ยวกับอะไร} B --\>\|Agent command\| C\[Skill/MCP\] B --\>\|Memory/Search\| D\[Oracle data\] B --\>\|Web/API\| E\[HTTP :47778\] B --\>\|Fleet/Message\| F\[Maw/Tmux\] B --\>\|Dashboard\| G\[UI/Host\] C --\> C1\[ตรวจ ~/.codex/skills, ~/.claude/skills\] C --\> C2\[ตรวจ codex mcp list\] C2 --\> C3\[แก้ MCP command/env แล้ว restart agent\] D --\> D1\[ตรวจ ORACLE_DATA_DIR\] D --\> D2\[นับ documents ใน oracle.db\] D2 --\> D3\[reindex ถ้าข้อมูลว่าง\] E --\> E1\[curl /api/stats\] E --\> E2\[ss -ltnp :47778\] E2 --\> E3\[start/restart Oracle HTTP\] F --\> F1\[maw ls / tmux ls\] F --\> F2\[maw capture\] F2 --\> F3\[wake/restart เฉพาะ session\] G --\> G1\[ตรวจ Network tab\] G --\> G2\[ตรวจ ?host= และ local storage\] G2 --\> G3\[ชี้ UI ไป API ที่ถูก\]

</div>

## 10.17 ข้อควรระวัง

- อย่าแก้หลายชั้นพร้อมกัน ถ้ายังไม่รู้ว่าชั้นไหนเสีย
- อย่าใช้ repo หนังสือเป็น runtime
- อย่า copy config จาก agent หนึ่งไปอีก agent โดยไม่ตรวจ path
- อย่า assume ว่า MCP กับ HTTP ใช้ process เดียวกัน
- อย่า assume ว่า UI เป็น source of truth UI เป็นแค่ lens
- อย่าเปลี่ยน data dir โดยไม่จด เพราะ memory อาจแยกเป็นสองโลก
- อย่า restart fleet ทั้งหมด ถ้าเสียแค่ session เดียว

## 10.18 สูตรแก้ปัญหาแบบสั้น

ถ้าจำได้แค่ชุดเดียว ให้ใช้ชุดนี้:

``` bash
echo "$ORACLE_DATA_DIR"
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&mode=fts&limit=3"
codex mcp list
maw ls
maw oracle ls
tmux ls
ss -ltnp | rg ':47778|:3456'
```

อ่านผลแบบง่าย:

- `curl /api/stats` ไม่ตอบ = HTTP API ยังไม่พร้อม
- `api/stats` ตอบแต่ search ว่าง = data/index/query มีปัญหา
- MCP list ไม่มี Oracle = agent ยังไม่ได้ต่อ memory
- `maw ls` ไม่เห็น session = orchestration หรือ tmux มีปัญหา
- port `47778` ไม่มี = Oracle HTTP ไม่ได้รัน
- port `3456` ไม่มี = maw server ไม่ได้รัน

เป้าหมายของ troubleshooting ไม่ใช่แก้ให้เร็วที่สุด แต่คือแก้ให้ถูกชั้นที่สุด เมื่อรู้ว่าปัญหาอยู่ชั้นไหน งานแก้จะเล็กลงมาก

</div>

<a href="ch09.html" class="button secondary">← Autonomous Ops</a> <a href="index.html" class="button primary">กลับสารบัญ</a>
