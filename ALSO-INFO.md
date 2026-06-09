# PowerShell: Codebase এ কী কী খোঁজা যায়

> `Select-String` শুধু text খোঁজে। কিন্তু PowerShell এ আরো অনেক কিছু খোঁজার command আছে।

---

## ১. Text / String খোঁজা — `Select-String`

```powershell
# সাধারণ text খোঁজা
Select-String "pattern" file.ts

# একাধিক pattern (Regex)
Select-String -Pattern "useState|useEffect" file.tsx

# exact text (regex ছাড়া)
Select-String -SimpleMatch "exact text" file.ts

# case sensitive খোঁজা
Select-String -CaseSensitive "MyComponent" file.tsx

# সব .ts/.tsx file এ খোঁজা
Get-ChildItem -Recurse -Include *.tsx,*.ts | Select-String "Nazim Interprise"
```

| Flag | কাজ |
|------|-----|
| `-Pattern` | Regex pattern দিয়ে খোঁজে |
| `-SimpleMatch` | Exact text match করে (regex নয়) |
| `-CaseSensitive` | বড়/ছোট হাতের পার্থক্য করে |
| `-NotMatch` | pattern নেই এমন lines দেখায় |

**Output:** `filename : line number : matching line`

---

## ২. File / Folder খোঁজা — `Get-ChildItem`

```powershell
# extension অনুযায়ী খোঁজা
Get-ChildItem -Recurse -Filter *.tsx

# শুধু file নাম দেখানো
Get-ChildItem -Name

# শুধু folder দেখানো
Get-ChildItem -Directory

# নির্দিষ্ট নামের file খোঁজা
Get-ChildItem -Recurse -Filter "page.tsx"

# একাধিক extension
Get-ChildItem -Recurse -Include *.ts,*.tsx,*.json
```

| Flag | কাজ |
|------|-----|
| `-Recurse` | sub-folder এও খোঁজে |
| `-Filter` | একটি pattern দিয়ে ফিল্টার করে |
| `-Include` | একাধিক extension নেয় |
| `-Directory` | শুধু folder দেখায় |
| `-Name` | শুধু নাম দেখায় (full path নয়) |

---

## ৩. File Size / Date অনুযায়ী খোঁজা — `Where-Object`

```powershell
# 10KB এর বড় files
Get-ChildItem -Recurse | Where-Object { $_.Length -gt 10KB }

# নির্দিষ্ট date এর পরে modified
Get-ChildItem -Recurse | Where-Object { $_.LastWriteTime -gt "2024-01-01" }

# 0 byte (empty) files
Get-ChildItem -Recurse | Where-Object { $_.Length -eq 0 }

# size এবং extension একসাথে
Get-ChildItem -Recurse -Include *.ts | Where-Object { $_.Length -gt 5KB }
```

| Operator | মানে |
|----------|------|
| `-gt` | greater than (বড়) |
| `-lt` | less than (ছোট) |
| `-eq` | equal (সমান) |
| `-ne` | not equal (সমান নয়) |
| `-ge` | greater than or equal |
| `-le` | less than or equal |

---

## ৪. File এর Property / Metadata — `Select-Object`

```powershell
# নির্দিষ্ট property দেখানো
Get-ChildItem | Select-Object Name, Length, LastWriteTime

# সব property একসাথে
Get-Item file.ts | Select-Object *

# শুধু নাম আর extension
Get-ChildItem -Recurse | Select-Object Name, Extension

# size MB তে দেখানো
Get-ChildItem -Recurse | Select-Object Name,
  @{Name="SizeMB"; Expression={[math]::Round($_.Length / 1MB, 2)}}
```

**সাধারণ Properties:**

| Property | মানে |
|----------|------|
| `Name` | file এর নাম |
| `Length` | size (bytes এ) |
| `Extension` | `.ts`, `.tsx` ইত্যাদি |
| `LastWriteTime` | সর্বশেষ modify হওয়ার time |
| `CreationTime` | তৈরি হওয়ার time |
| `FullName` | পুরো path |

---

## ৫. File এর Content / Line Count — `Get-Content` + `Measure-Object`

```powershell
# file এর পুরো content পড়া
Get-Content file.ts

# line count করা
Get-Content file.ts | Measure-Object -Line

# word count করা
Get-Content file.ts | Measure-Object -Word

# character count করা
Get-Content file.ts | Measure-Object -Character

# সব .ts file এর মোট line count
Get-ChildItem -Recurse -Include *.ts |
  Get-Content | Measure-Object -Line
```

| Flag | কাজ |
|------|-----|
| `-Line` | কত lines আছে গোনে |
| `-Word` | কত words আছে গোনে |
| `-Character` | কত characters আছে গোনে |

---

## ৬. Duplicate / Group / Sort — `Group-Object` + `Sort-Object`

```powershell
# extension অনুযায়ী group করা
Get-ChildItem -Recurse | Group-Object Extension

# size অনুযায়ী sort (ছোট থেকে বড়)
Get-ChildItem -Recurse | Sort-Object Length

# size অনুযায়ী sort (বড় থেকে ছোট)
Get-ChildItem -Recurse | Sort-Object Length -Descending

# সবচেয়ে বড় ৫টা file
Get-ChildItem -Recurse | Sort-Object Length -Descending | Select-Object -First 5

# duplicate নাম খোঁজা
Get-ChildItem -Recurse | Group-Object Name | Where-Object { $_.Count -gt 1 }
```

---

## একসাথে তুলনা

| কী খুঁজবে | Command | Linux/Mac equivalent |
|-----------|---------|----------------------|
| Text/keyword | `Select-String` | `grep` |
| File/folder নাম | `Get-ChildItem` | `find` |
| Size বা date | `Where-Object` | `find -size`, `find -newer` |
| File metadata | `Select-Object` | `ls -la` |
| Line/word count | `Get-Content` + `Measure-Object` | `wc -l`, `wc -w` |
| Group / sort | `Group-Object`, `Sort-Object` | `sort`, `uniq -c` |

---

## Pipe দিয়ে একসাথে ব্যবহার

সব command ই `|` দিয়ে chain করা যায়:

```powershell
# সবচেয়ে বড় .ts file খুঁজে তার line count দেখানো
Get-ChildItem -Recurse -Include *.ts |
  Sort-Object Length -Descending |
  Select-Object -First 1 |
  Get-Content | Measure-Object -Line

# "TODO" আছে এমন files এর নাম ও line number
Get-ChildItem -Recurse -Include *.tsx,*.ts |
  Select-String "TODO" |
  Select-Object Filename, LineNumber, Line
```
