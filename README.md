# PowerShell: Codebase Searching Command

## কমান্ড

```powershell
Get-ChildItem -Recurse -Path src/ -Include *.tsx,*.ts | Select-String "Nazim Interprise"
```

---

## অংশ ১ — `Get-ChildItem`

```powershell
Get-ChildItem -Recurse -Path src/ -Include *.tsx,*.ts
```

> **সহজ কথায়** — file খোঁজো (`ls` এর মতো)

| Flag | মানে |
|------|------|
| `Get-ChildItem` | folder এর ভেতরের items দেখাও |
| `-Recurse` | সব sub-folder এও ঢুকে খোঁজো |
| `-Path src/` | শুধু `src/` folder এ খোঁজো |
| `-Include *.tsx,*.ts` | শুধু `.tsx` আর `.ts` files নাও |

**Output:** `src/` এর ভেতরের সব `.ts` আর `.tsx` files এর list

---

## অংশ ২ — `|` (Pipe)

```powershell
|
```

আগের command এর **output** কে পরের command এর **input** হিসেবে পাঠায়।

```
Get-ChildItem এর file list  →  Select-String এ যাচ্ছে
```

---

## অংশ ৩ — `Select-String`

```powershell
Select-String "Nazim Interprise"
```

> **সহজ কথায়** — text খোঁজো (`grep` এর মতো)

প্রতিটা file এর ভেতরে `"Nazim Interprise"` text টা আছে কিনা দেখো।

**Output:**
```
filename : line number : matching line
```

---

## পুরো flow একসাথে

```
src/ folder এর সব .ts/.tsx files নাও
              ↓
প্রতিটা file এ "Nazim Interprise" খোঁজো
              ↓
যেখানে পাওয়া গেছে সেই file + line number দেখাও
```

---

## Linux/Mac এ equivalent

```bash
grep -r "Nazim Interprise" src/
```

> PowerShell এ `grep` নেই, তাই `Get-ChildItem` + `Select-String` ব্যবহার করতে হয়।

---

## তুলনা

| | Linux/Mac | Windows (PowerShell) |
|---|---|---|
| **Command** | `grep` | `Get-ChildItem` + `Select-String` |
| **Recursive flag** | `-r` | `-Recurse` |
| **File filter** | `--include="*.ts"` | `-Include *.tsx,*.ts` |
| **সংক্ষিপ্ততা** | ছোট, এক লাইন | বড়, দুই অংশ |
| **কাজ** | একই | একই |
