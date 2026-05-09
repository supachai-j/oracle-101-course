---
source_type: doc
source_url: https://github.com/supachai-j/oracle-ebook/blob/main/website/ch03.html
ingested_at: 2026-05-09
title: ติดตั้งจาก 0
language: th
---

<div class="chapter-header">

# ติดตั้งจาก 0

ลำดับติดตั้งจากเครื่องว่างจนมี Memory, Skills, Maw และ UI ใช้งานได้

</div>

<div class="section chapter-content">

## 3.1 ก่อนเริ่ม

การติดตั้ง Oracle จากศูนย์ควรทำตามลำดับ dependency ไม่ใช่สุ่ม clone ทุก repo เพราะแต่ละชั้นมีหน้าที่ต่างกัน

| ต้องมี | ใช้ทำอะไร |
|----|----|
| Git และ GitHub CLI | clone repo, auth, อ่าน source |
| Bun หรือ Node runtime | รัน `arra-oracle-v3`, `maw-js`, skills CLI |
| `tmux` | ให้ maw คุม agent pane จริง |
| `ghq` | เก็บ repo เป็นระบบตาม owner/repo |
| `sqlite3` | ตรวจ `oracle.db` ตอน debug |
| Ollama หรือ embedding provider | ใช้ vector search ถ้าต้องการ hybrid search เต็มรูปแบบ |

## 3.2 Clone หรือ install Oracle memory

เริ่มจาก runtime หลักคือ `arra-oracle-v3` เพราะ agent และ UI ต้องพึ่ง memory layer

``` bash
export ORACLE_SOURCE_ORG=Soul-Brews-Studio  # org ของอาจารย์ Nat
ghq get "$ORACLE_SOURCE_ORG/arra-oracle-v3"
cd "$HOME/ghq/github.com/$ORACLE_SOURCE_ORG/arra-oracle-v3"
bun install
```

ตั้ง `ORACLE_SOURCE_ORG` เป็น org หรือ fork ที่ทีมใช้จริง โดยต้นน้ำหลักคือ `Soul-Brews-Studio` (org ของอาจารย์ Nat) ถ้าเครื่องนี้ใช้ fork ส่วนตัว ให้ clone fork ได้ แต่ต้องจำว่าระบบจริงคือ memory layer ไม่ใช่หนังสือหรือ UI

## 3.3 ตั้ง data dir

เลือก data dir กลางหนึ่งที่ agent ทุกตัวใช้ร่วมกัน เช่น `~/.oracle`

``` bash
export ORACLE_DATA_DIR="$HOME/.oracle"
mkdir -p "$ORACLE_DATA_DIR"
```

fleet หนึ่งควรมี data store กลางเดียว ไม่อย่างนั้น Codex, Claude และ UI จะเห็นจำนวนเอกสารไม่เท่ากัน

## 3.4 Start HTTP API และตรวจสุขภาพ

หลัง install แล้วให้เปิด HTTP API ก่อน เพื่อให้ UI และ maw ตรวจได้

``` bash
ORACLE_DATA_DIR="$HOME/.oracle" bun run src/server.ts
curl -s http://localhost:47778/api/stats
curl -s "http://localhost:47778/api/search?q=oracle&limit=5"
```

ถ้า API ตอบ แต่ search ว่าง ให้แยกปัญหาเป็นเรื่อง index/data ไม่ใช่เรื่อง network

## 3.5 Register MCP ให้ agent

MCP คือทางที่ agent ใช้เรียก Oracle tools ผ่าน stdio ตัวอย่าง Codex ควรชี้ไปที่ wrapper script ของ repo ที่ติดตั้งจริง และส่ง env `ORACLE_DATA_DIR` ให้ถูก

``` bash
codex mcp list
codex mcp get arra-oracle-v3
```

ถ้ายังไม่ได้ register ให้เพิ่มผ่าน config ของ agent นั้น แล้ว restart session เพื่อให้ tool list โหลดใหม่

## 3.6 ติดตั้ง skills

ใช้ `arra-oracle-skills-cli` เพื่อให้ agent หลายตัวมี command และ workflow เดียวกัน

``` bash
bun add -g "github:$ORACLE_SOURCE_ORG/arra-oracle-skills-cli"
arra-oracle-skills install -g --profile lab
```

profile คือชุดของ skill ที่ติดตั้งให้ agent ใช้ งานจริงควรลงให้ครบ เพราะ skill เป็นไฟล์ markdown ในเครื่อง ไม่ได้เป็น process ที่กิน CPU หรือ RAM ตลอดเวลา

| Profile | จำนวน skill | เหมาะกับ | คำสั่ง |
|----|----|----|----|
| `seed` | ~15 | เริ่มต้น, Oracle ใหม่ที่ยังไม่รู้จะใช้อะไร | `arra-oracle-skills install -g --profile seed` |
| `standard` | ~30 | ใช้งานทั่วไป, มี session management + trace + learn | `arra-oracle-skills install -g --profile standard` |
| `full` / `lab` | 47+ | แนะนำ — ครบทุก skill, ใช้งานจริงใน production | `arra-oracle-skills install -g --profile lab` |

**แนะนำ:** ลง `lab` ให้ครบ เพราะ skill ที่ไม่ได้ใช้ไม่เปลือง resource มันแค่เป็นไฟล์ใน `~/.claude/skills/` หรือ `~/.codex/skills/`

คำสั่ง switch profile:

``` bash
# ดู profile ปัจจุบัน
arra-oracle-skills list -g

# เปลี่ยน profile (ใช้ /go skill ใน Claude Code)
/go lab          # เปลี่ยนเป็น lab profile
/go full         # เปลี่ยนเป็น full profile
/go standard     # ลดลงเป็น standard
```

ติดตั้งให้หลาย agent พร้อมกัน:

``` bash
# Claude Code
arra-oracle-skills install -g --agent claude --profile lab

# Codex
arra-oracle-skills install -g --agent codex --profile lab

# ตรวจสอบ
ls ~/.claude/skills/ | wc -l    # ควรได้ 47+
ls ~/.codex/skills/ | wc -l     # ควรได้ 47+
```

หลังติดตั้งให้ตรวจว่า path ของ agent นั้นมี skill จริง เช่น `~/.codex/skills` และ `~/.claude/skills`

## 3.7 ติดตั้ง maw-js

เมื่อต้องคุมหลาย agent ให้ติดตั้ง `maw-js`

``` bash
bun add -g "github:$ORACLE_SOURCE_ORG/maw-js"
maw --help
maw ls
```

ตั้งค่า maw ให้รู้จัก Oracle HTTP API โดยแก้ไฟล์ config ของ instance นั้นโดยตรง

``` bash
mkdir -p ~/.config/maw
$EDITOR ~/.config/maw/maw.config.json
# ตั้ง oracleUrl = http://localhost:47778
```

## 3.8 Start maw server และ UI

`maw serve` เปิด API, websocket และ packed UI บน port default `3456`

``` bash
maw serve
curl -s http://localhost:3456/api/config
```

ถ้าใช้ UI แยก ให้ `maw-ui` อ่านจาก maw API ไม่ใช่ถือ state เอง

## 3.9 ติดตั้ง plugin

Core plugins เช่น wake, sleep, stop, done ควรถูก bootstrap ก่อน ส่วน optional plugins ติดตั้งลง `~/.maw/plugins`

``` bash
maw plugin list
maw plugin install <plugin-name>
```

plugin ที่ดีควรมี `plugin.json`, handler, manifest surface และ version/hash ที่ตรวจสอบได้

## 3.10 Verify end-to-end

| จุดตรวจ     | คำสั่ง                 | ผ่านเมื่อ                      |
|------------|----------------------|-----------------------------|
| Oracle API | `curl /api/stats`    | ได้ JSON และจำนวน documents  |
| MCP        | `codex mcp list`     | เห็น Oracle MCP server       |
| Skills     | `ls ~/.codex/skills` | มี skill profile ที่ติดตั้ง       |
| Maw        | `maw ls`             | เห็น session หรือไม่ error     |
| Fleet      | `maw oracle ls`      | เห็นรายชื่อ Oracle ที่ config ไว้ |
| UI         | เปิด `maw serve`      | dashboard อ่าน config/API ได้ |

## 3.11 ข้อควรระวัง

- อย่าใช้ book repo เป็น runtime dependency
- อย่าให้ Codex กับ Claude ชี้คนละ `ORACLE_DATA_DIR`
- อย่า debug UI ก่อนตรวจ HTTP API
- อย่าลบ `psi` เพื่อแก้ปัญหา search ก่อน backup
- ถ้าเปลี่ยน env ของ service ที่รันค้าง ต้อง restart process นั้น

</div>

<a href="ch02.html" class="button secondary">← สถาปัตยกรรม</a> <a href="ch04.html" class="button primary">Oracle Brain →</a>
