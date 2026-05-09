---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch02.html
ingested_at: 2026-05-09
title: สถาปัตยกรรม
language: th
---

<div class="chapter-header">

# สถาปัตยกรรม

แผนที่ระบบ เส้นทาง config โครงสร้าง ψ และบทบาทของ repo หลัก

</div>

<div class="section chapter-content">

## 2.1 แผนที่สถาปัตยกรรม

Oracle แยกเป็นหลายชั้นเพื่อให้ระบบตรวจสอบง่ายและไม่ผูกกับ UI หรือ agent ตัวเดียว แผนภาพนี้ย่อจาก เอกสารสถาปัตยกรรมต้นน้ำจาก อาจารย์ Nat ให้เห็นทางเดินหลักของข้อมูล

<div class="inline-diagram" role="img" aria-label="Oracle system paths diagram">

![](data:image/svg+xml;base64,PHN2ZyB2aWV3Ym94PSIwIDAgMTA0MCA1MjAiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+CiAgPGRlZnM+CiAgICA8bGluZWFyZ3JhZGllbnQgaWQ9ImciIHgxPSIwIiB4Mj0iMSIgeTE9IjAiIHkyPSIxIj4KICAgICAgPHN0b3Agb2Zmc2V0PSIwIiBzdG9wLWNvbG9yPSIjNGY0NmU1Ij48L3N0b3A+CiAgICAgIDxzdG9wIG9mZnNldD0iMSIgc3RvcC1jb2xvcj0iIzMxMmU4MSI+PC9zdG9wPgogICAgPC9saW5lYXJncmFkaWVudD4KICAgIDxtYXJrZXIgaWQ9ImFycm93IiBtYXJrZXJ3aWR0aD0iMTIiIG1hcmtlcmhlaWdodD0iMTIiIHJlZng9IjEwIiByZWZ5PSI2IiBvcmllbnQ9ImF1dG8iPgogICAgICA8cGF0aCBkPSJNMiwyIEwxMCw2IEwyLDEwIFoiIGZpbGw9IiM0ZjQ2ZTUiIC8+CiAgICA8L21hcmtlcj4KICA8L2RlZnM+CiAgPHJlY3QgeD0iMTYiIHk9IjE2IiB3aWR0aD0iMTAwOCIgaGVpZ2h0PSI0ODgiIHJ4PSIxOCIgZmlsbD0iI2ZmZmZmZiIgc3Ryb2tlPSIjY2JkNWUxIiAvPgogIDxjaXJjbGUgY3g9Ijk0IiBjeT0iMTAwIiByPSI1NCIgZmlsbD0iIzMxMmU4MSI+PC9jaXJjbGU+PHRleHQgeD0iOTQiIHk9IjExOSIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSI2NCIgZm9udC13ZWlnaHQ9IjkwMCIgZmlsbD0iI2ZmZmZmZiI+z4g8L3RleHQ+CiAgPGcgZm9udC1zaXplPSIxOCIgZm9udC13ZWlnaHQ9IjgwMCIgZmlsbD0iIzBmMTcyYSI+CiAgICA8cmVjdCB4PSIxOTAiIHk9IjU4IiB3aWR0aD0iMTc2IiBoZWlnaHQ9Ijg0IiByeD0iOCIgZmlsbD0iI2VlZjJmZiIgc3Ryb2tlPSIjNGY0NmU1IiAvPjx0ZXh0IHg9IjI3OCIgeT0iOTQiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkFJIEFnZW50czwvdGV4dD48dGV4dCB4PSIyNzgiIHk9IjEyMCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSIxMyI+Q2xhdWRlIC8gQ29kZXggLyBDdXJzb3I8L3RleHQ+CiAgICA8cmVjdCB4PSI0MzAiIHk9IjU4IiB3aWR0aD0iMTg2IiBoZWlnaHQ9Ijg0IiByeD0iOCIgZmlsbD0iI2Y4ZmFmYyIgc3Ryb2tlPSIjNGY0NmU1IiAvPjx0ZXh0IHg9IjUyMyIgeT0iOTQiIHRleHQtYW5jaG9yPSJtaWRkbGUiPlNraWxscyBDTEk8L3RleHQ+PHRleHQgeD0iNTIzIiB5PSIxMjAiIHRleHQtYW5jaG9yPSJtaWRkbGUiIGZvbnQtc2l6ZT0iMTMiPmluc3RhbGwgd29ya2Zsb3c8L3RleHQ+CiAgICA8cmVjdCB4PSI2ODAiIHk9IjU4IiB3aWR0aD0iMjA0IiBoZWlnaHQ9Ijg0IiByeD0iOCIgZmlsbD0iI2Y4ZmFmYyIgc3Ryb2tlPSIjNGY0NmU1IiAvPjx0ZXh0IHg9Ijc4MiIgeT0iOTQiIHRleHQtYW5jaG9yPSJtaWRkbGUiPk1DUCBzdGRpbzwvdGV4dD48dGV4dCB4PSI3ODIiIHk9IjEyMCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSIxMyI+YWdlbnQgdG9vbHM8L3RleHQ+CiAgICA8cmVjdCB4PSIxOTAiIHk9IjIyNCIgd2lkdGg9IjIyMCIgaGVpZ2h0PSI5MCIgcng9IjgiIGZpbGw9IiNmOGZhZmMiIHN0cm9rZT0iIzRmNDZlNSIgLz48dGV4dCB4PSIzMDAiIHk9IjI2MCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+bWF3LWpzPC90ZXh0Pjx0ZXh0IHg9IjMwMCIgeT0iMjg2IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBmb250LXNpemU9IjEzIj50bXV4IC8gZmxlZXQgLyBwbHVnaW5zPC90ZXh0PgogICAgPHJlY3QgeD0iNTAwIiB5PSIyMTQiIHdpZHRoPSIyMjAiIGhlaWdodD0iMTEwIiByeD0iOCIgZmlsbD0idXJsKCNnKSIgc3Ryb2tlPSIjMzEyZTgxIiAvPjx0ZXh0IHg9IjYxMCIgeT0iMjU2IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBmaWxsPSIjZmZmZmZmIj5hcnJhLW9yYWNsZS12MzwvdGV4dD48dGV4dCB4PSI2MTAiIHk9IjI4NCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSIxMyIgZmlsbD0iI2ZmZmZmZiI+SFRUUCA6NDc3NzggKyBNZW1vcnk8L3RleHQ+CiAgICA8cmVjdCB4PSI3OTIiIHk9IjIxMCIgd2lkdGg9IjE3NiIgaGVpZ2h0PSIxMTYiIHJ4PSI4IiBmaWxsPSIjZjhmYWZjIiBzdHJva2U9IiM0ZjQ2ZTUiIC8+PHRleHQgeD0iODgwIiB5PSIyNTIiIHRleHQtYW5jaG9yPSJtaWRkbGUiPlVJIExlbnM8L3RleHQ+PHRleHQgeD0iODgwIiB5PSIyODAiIHRleHQtYW5jaG9yPSJtaWRkbGUiIGZvbnQtc2l6ZT0iMTMiPm1hdy11aSAvIHVpLW9yYWNsZTwvdGV4dD4KICAgIDxyZWN0IHg9IjE4MCIgeT0iMzk4IiB3aWR0aD0iMTg4IiBoZWlnaHQ9IjcyIiByeD0iOCIgZmlsbD0iI2VlZjJmZiIgc3Ryb2tlPSIjNGY0NmU1IiAvPjx0ZXh0IHg9IjI3NCIgeT0iNDI4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5TUUxpdGUgKyBGVFM1PC90ZXh0Pjx0ZXh0IHg9IjI3NCIgeT0iNDUwIiB0ZXh0LWFuY2hvcj0ibWlkZGxlIiBmb250LXNpemU9IjEzIj5vcmFjbGUuZGI8L3RleHQ+CiAgICA8cmVjdCB4PSI0MjYiIHk9IjM5OCIgd2lkdGg9IjE4OCIgaGVpZ2h0PSI3MiIgcng9IjgiIGZpbGw9IiNlZWYyZmYiIHN0cm9rZT0iIzRmNDZlNSIgLz48dGV4dCB4PSI1MjAiIHk9IjQyOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+TGFuY2VEQjwvdGV4dD48dGV4dCB4PSI1MjAiIHk9IjQ1MCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSIxMyI+dmVjdG9yczwvdGV4dD4KICAgIDxyZWN0IHg9IjY3MiIgeT0iMzk4IiB3aWR0aD0iMTg4IiBoZWlnaHQ9IjcyIiByeD0iOCIgZmlsbD0iI2VlZjJmZiIgc3Ryb2tlPSIjNGY0NmU1IiAvPjx0ZXh0IHg9Ijc2NiIgeT0iNDI4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj7PiCB2YXVsdDwvdGV4dD48dGV4dCB4PSI3NjYiIHk9IjQ1MCIgdGV4dC1hbmNob3I9Im1pZGRsZSIgZm9udC1zaXplPSIxMyI+ZmlsZXMgLyBpbmJveCAvIG1lbW9yeTwvdGV4dD4KICA8L2c+CiAgPGcgc3Ryb2tlPSIjNGY0NmU1IiBzdHJva2Utd2lkdGg9IjMiIGZpbGw9Im5vbmUiIG1hcmtlci1lbmQ9InVybCgjYXJyb3cpIj4KICAgIDxwYXRoIGQ9Ik0xNDggMTAwIEgxOTAiIC8+PHBhdGggZD0iTTM2NiAxMDAgSDQzMCIgLz48cGF0aCBkPSJNNjE2IDEwMCBINjgwIiAvPjxwYXRoIGQ9Ik03ODIgMTQyIFYyMTQgSDcyMCIgLz4KICAgIDxwYXRoIGQ9Ik0yNzggMTQyIFYyMjQiIC8+PHBhdGggZD0iTTQxMCAyNjkgSDUwMCIgLz48cGF0aCBkPSJNNzIwIDI2OSBINzkyIiAvPgogICAgPHBhdGggZD0iTTYxMCAzMjQgVjM3MiBIMjc0IFYzOTgiIC8+PHBhdGggZD0iTTYxMCAzMjQgVjM5OCIgLz48cGF0aCBkPSJNNjEwIDMyNCBWMzcyIEg3NjYgVjM5OCIgLz4KICA8L2c+Cjwvc3ZnPg==)

</div>

## 2.2 Skills table

Skill คือ workflow ที่ agent เรียกใช้ได้ซ้ำ ส่วน plugin คือความสามารถที่ระบบ maw โหลดได้หลาย surface เช่น CLI, API, peer transport หรือ cron

| กลุ่ม | ตัวอย่าง | หน้าที่ | เก็บที่ไหน |
|----|----|----|----|
| Session awareness | `recap`, `where-we-are` | สรุปบริบทก่อนเริ่มงาน | skill folder ของ agent |
| Codebase discovery | `learn`, `trace`, `dig` | อ่าน repo ค้นประวัติ และเก็บหลักฐาน | skills + Oracle memory |
| Memory capture | `rrr`, `forward`, `inbox` | บันทึกบทเรียนและส่งต่อ session ถัดไป | `psi` และ `oracle.db` |
| Maw lifecycle | `wake`, `sleep`, `stop`, `done` | คุม agent pane และสถานะงาน | `maw-core-plugins` |
| Plugin migration | `maw-learn`, `maw-project` | ย้าย workflow จาก skill ไปเป็น runtime plugin | `~/.maw/plugins` |

## 2.3 โครงสร้าง ψ

    ψ/
    ├── inbox/          # handoff, ข้อความเข้า
    │   └── handoff/
    ├── memory/
    │   ├── learnings/       # บทเรียนที่เรียนรู้
    │   ├── retrospectives/  # สรุป session
    │   └── resonance/       # ตัวตน, หลักการ
    ├── writing/        # ร่าง
    ├── learn/          # repo ที่ clone มาศึกษา
    ├── archive/        # งานเสร็จแล้ว
    ├── outbox/         # ข้อความออก
    └── metrics/        # telemetry data

โครงสร้างนี้ตั้งใจให้คนอ่านได้ด้วยตา ไม่ใช่ข้อมูลลับในกล่องดำ ถ้า search แปลกหรือ UI แสดงผลผิด ให้เปิดไฟล์ใน `ψ` ตรวจสอบได้ทันที

**หมายเหตุ:** โครงสร้าง `ψ` อาจต่างกันในแต่ละ Oracle — default จากต้นน้ำของอาจารย์ Nat สร้างจาก `/awaken`

## 2.4 Config map

<div class="config-list">

<div class="config-item">

`ORACLE_DATA_DIR`ตัวชี้ data store หลัก เช่น `~/.oracle` มี `oracle.db`, `feed.log`, `lancedb/` และเนื้อหา `psi`

</div>

<div class="config-item">

`ORACLE_DB_PATH`override path ของ SQLite เมื่อไม่ต้องการใช้ค่า default ใต้ data dir

</div>

<div class="config-item">

`ORACLE_REPO_ROOT`root ที่ MCP ใช้อ่านเขียน learning, inbox, trace และ handoff

</div>

<div class="config-item">

`ORACLE_PORT`port ของ HTTP API ค่า default จากต้นน้ำของอาจารย์ Nat คือ `47778`

</div>

<div class="config-item">

`MAW_HOME`แยก instance ของ maw เช่น config, plugin และ UI dist

</div>

<div class="config-item">

`MAW_CONFIG_DIR`ตำแหน่ง `maw.config.json` และ fleet config ใต้ `fleet/*.json`

</div>

<div class="config-item">

`MAW_PORT`port ของ `maw serve` ค่า default คือ `3456`

</div>

<div class="config-item">

`MAW_PLUGIN_HOME`ตำแหน่ง runtime plugin packages เช่น `~/.maw/plugins`

</div>

</div>

## 2.5 กฎจำง่าย

<div class="callout-grid">

<div class="callout">

**MCP สำหรับ agent**Codex และ Claude เรียกเครื่องมือผ่าน stdio MCP เพื่อ search, learn, trace และ handoff

</div>

<div class="callout">

**HTTP สำหรับ UI**Studio, Vector, Feed, Forum และ UI bridge อ่านข้อมูลผ่าน endpoint เช่น `/api/search` และ `/api/stats`

</div>

<div class="callout">

**maw สำหรับทีม**งาน orchestration ใช้ tmux, fleet config, API, websocket และ plugin runtime

</div>

<div class="callout">

**books เป็น consumer**หนังสือและ guide ใช้อธิบายระบบ ไม่ใช่ runtime dependency ที่ต้องติดตั้งก่อน

</div>

</div>

</div>

<a href="ch01.html" class="button secondary">← Oracle คืออะไร</a> <a href="ch03.html" class="button primary">ติดตั้งจาก 0 →</a>
