# 🔍 PowerShell — Codebase Searching Command

> **উদ্দেশ্য:** একটি নির্দিষ্ট ফাইলের ভেতর থেকে নির্দিষ্ট CSS class / keyword গুলো খুঁজে বের করা।

---

## 📌 দুটো Scenario

| Scenario | Path-এ কী আছে | Command |
|----------|---------------|---------|
| **Case 1** | `[]` নেই (normal path) | `Get-Content "path"` |
| **Case 2** | `[]` আছে (Next.js dynamic route) | `Get-Content -LiteralPath "path"` |

---

## ✅ Case 1 — Path-এ `[ ]` নেই

```powershell
PS C:\Users\Wasim\ims-project> Get-Content "src\components\ui\button.tsx" | Select-String "overflow|flex-col"
```

### 🔬 Command Prompt বিশ্লেষণ

```
PS C:\Users\Wasim\ims-project> Get-Content "src\components\ui\button.tsx" | Select-String "overflow|flex-col"
└──────────────────────────────┘ └──────────┘ └────────────────────────┘   └────────────┘ └────────────────┘
        [১] Prompt               [২] Cmdlet       [৩] File Path               [৪] Cmdlet    [৫] Pattern
```

| # | অংশ | বিবরণ |
|---|-----|--------|
| [১] | `PS C:\Users\Wasim\ims-project>` | PowerShell Prompt — বর্তমান directory দেখাচ্ছে |
| [২] | `Get-Content` | ফাইলটি পড়ে লাইন বাই লাইন output করে (`cat`-এর মতো) |
| [৩] | `"src\components\ui\button.tsx"` | Normal path — কোনো `[]` নেই, তাই সরাসরি quote-এ দেওয়াই যথেষ্ট |
| [৪] | `Select-String` | প্রতিটি লাইনে pattern খোঁজে (`grep`-এর মতো) |
| [৫] | `"overflow\|flex-col"` | Regex OR — `overflow` **অথবা** `flex-col` যে লাইনে আছে সেটি দেখাবে |

### 📤 Output

```
src\components\ui\button.tsx:8:   <div className="flex flex-col gap-2">
src\components\ui\button.tsx:15:  <span className="overflow-hidden text-ellipsis">
```

---

## ⚠️ Case 2 — Path-এ `[ ]` আছে (Next.js Dynamic Route)

### সমস্যাটা কী?

Next.js Dynamic Route ফোল্ডারের নাম হয় `[store]`, `[id]`, `[slug]` ইত্যাদি।
PowerShell-এ `[` এবং `]` হলো **wildcard character** — এগুলো দিয়ে character range বোঝায় (যেমন `[abc]` মানে a, b, বা c)।

ফলে শুধু quote দিলেও PowerShell path-কে সঠিকভাবে বোঝে না এবং error দেয়:

```powershell
# ❌ এটা কাজ করবে না
PS C:\Users\Wasim\ims-project> Get-Content "src\app\[store]\dashboard\sales\sales-content.tsx" | Select-String "overflow"

# Output:
# Get-Content: Cannot find path '...' because it does not exist.
```

### ✅ সমাধান: `-LiteralPath` Flag

```powershell
PS C:\Users\Wasim\ims-project> Get-Content -LiteralPath "src\app\[store]\dashboard\sales\sales-content.tsx" | Select-String "overflow|min-h|max-h|lg:flex-row"
```

### 🔬 Command Prompt বিশ্লেষণ

```
PS C:\Users\Wasim\ims-project> Get-Content -LiteralPath "src\app\[store]\dashboard\sales\sales-content.tsx" | Select-String "overflow|min-h|max-h|lg:flex-row"
└──────────────────────────────┘ └──────────┘ └──────────┘ └──────────────────────────────────────────────┘   └────────────┘ └────────────────────────────────┘
        [১] Prompt               [২] Cmdlet   [৩] Flag              [৪] File Path                              [৫] Cmdlet         [৬] Pattern
```

| # | অংশ | বিবরণ |
|---|-----|--------|
| [১] | `PS C:\Users\Wasim\ims-project>` | PowerShell Prompt — বর্তমান directory |
| [২] | `Get-Content` | ফাইলটি পড়ে লাইন বাই লাইন output করে |
| [৩] | `-LiteralPath` | **এই flag-টাই মূল সমাধান** — path-কে exactly literal হিসেবে নেয়, কোনো wildcard interpretation করে না |
| [৪] | `"src\app\[store]\dashboard\sales\sales-content.tsx"` | Path — `[store]` এখন আক্ষরিক ফোল্ডার নাম হিসেবে পড়া হচ্ছে |
| [৫] | `Select-String` | প্রতিটি লাইনে pattern খোঁজে |
| [৬] | `"overflow\|min-h\|max-h\|lg:flex-row"` | Regex OR — এই ৪টির যেকোনো একটি যে লাইনে আছে সেটি দেখাবে |

### 📤 Output

```
src\app\[store]\dashboard\sales\sales-content.tsx:11:  <div className="min-h-screen overflow-hidden">
src\app\[store]\dashboard\sales\sales-content.tsx:29:  <div className="lg:flex-row flex-col gap-4">
src\app\[store]\dashboard\sales\sales-content.tsx:45:  <section className="max-h-[400px] overflow-y-auto">
```

---

## 🆚 Case 1 vs Case 2 — পার্থক্য এক নজরে

```
Case 1 ([] নেই):
─────────────────────────────────────────────────────────────────────
  Get-Content  "src\components\ui\button.tsx"  |  Select-String "..."
  └──────────┘  └──────────────────────────┘
   Cmdlet         Normal path → quote-ই যথেষ্ট


Case 2 ([] আছে):
─────────────────────────────────────────────────────────────────────
  Get-Content  -LiteralPath  "src\app\[store]\dashboard\..."  |  Select-String "..."
  └──────────┘ └───────────┘  └────────────────────────────┘
   Cmdlet       Flag (জরুরি)    [] সহ path → LiteralPath লাগবেই
```

| | Case 1 | Case 2 |
|--|--------|--------|
| Path-এ `[]` | নেই | আছে |
| Extra flag লাগে? | ❌ না | ✅ হ্যাঁ (`-LiteralPath`) |
| Quote লাগে? | ✅ হ্যাঁ | ✅ হ্যাঁ |
| Error হওয়ার সম্ভাবনা | নেই | `-LiteralPath` না দিলে হবেই |

---

## ⚡ দরকারি Variations

### শুধু লাইন নম্বর দেখাতে চাইলে
```powershell
# [] নেই
PS C:\Users\Wasim\ims-project> Get-Content "src\components\ui\button.tsx" `
  | Select-String "overflow|flex-col" `
  | Select-Object LineNumber, Line

# [] আছে
PS C:\Users\Wasim\ims-project> Get-Content -LiteralPath "src\app\[store]\dashboard\sales\sales-content.tsx" `
  | Select-String "overflow|min-h|max-h|lg:flex-row" `
  | Select-Object LineNumber, Line
```

### Case-sensitive search করতে চাইলে
```powershell
PS C:\Users\Wasim\ims-project> Get-Content -LiteralPath "src\app\[store]\dashboard\sales\sales-content.tsx" `
  | Select-String "overflow|flex-col" -CaseSensitive
```

### একাধিক ফাইলে একসাথে খুঁজতে চাইলে
```powershell
PS C:\Users\Wasim\ims-project> Get-ChildItem -LiteralPath "src\app\[store]\dashboard\" -Recurse -Filter "*.tsx" `
  | Select-String "overflow|min-h|max-h|lg:flex-row"
```

---

## 🗺️ Visual Flow

```
                    ┌──────────────────────────────────────────────────────────┐
                    │                   PowerShell Terminal                    │
                    │                                                          │
  Path-এ [] নেই?   │  Get-Content "path"  ──pipe──→  Select-String "pattern"  │
                    │                                                          │
  Path-এ [] আছে?   │  Get-Content -LiteralPath "path"  ──pipe──→  Select-String "pattern" │
                    └──────────────────────────────────────────────────────────┘
                                          │
                                          ↓
                              matched লাইনগুলো print হয়
                         format: ফাইল পাথ : লাইন নম্বর : content
```

---

> **💡 মনে রাখো:** Next.js project-এ `[param]` ফোল্ডার থাকবেই — তাই PowerShell-এ সবসময় `-LiteralPath` ব্যবহার করাটা habit বানিয়ে নাও।
