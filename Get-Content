# 🔍 PowerShell — Codebase Searching Command

> **উদ্দেশ্য:** একটি নির্দিষ্ট ফাইলের ভেতর থেকে নির্দিষ্ট CSS class / keyword গুলো খুঁজে বের করা।

---

## ✅ Full Command

```powershell
Get-Content "src\app\[store]\dashboard\inventory\inventory-content.tsx" 
  | Select-String "overflow|min-w|sm:max-w|sm:text-3xl|flex-col"
```

---

## 🔬 Command Anatomy (অংশভিত্তিক বিশ্লেষণ)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   Get-Content   src\app\[store]\dashboard\inventory\inventory-content.tsx   │
│   └──────────┘  └──────────────────────────────────────────────────────┘   │
│    [১] Cmdlet              [২] File Path                                    │
│                                                                             │
│        |  (pipe)                                                            │
│        ↓                                                                    │
│                                                                             │
│   Select-String  "overflow|min-w|sm:max-w|sm:text-3xl|flex-col"            │
│   └────────────┘  └──────────────────────────────────────────────┘         │
│    [৩] Cmdlet              [৪] Search Pattern (Regex)                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 📖 প্রতিটি অংশের বিস্তারিত ব্যাখ্যা

### [১] `Get-Content`
| বিষয় | বিবরণ |
|-------|--------|
| ধরন | PowerShell Built-in Cmdlet |
| কাজ | নির্দিষ্ট ফাইলটি পড়ে প্রতিটি **লাইন** আলাদাভাবে output করে |
| Linux equivalent | `cat` |

---

### [২] `src\app\[store]\dashboard\inventory\inventory-content.tsx`
| বিষয় | বিবরণ |
|-------|--------|
| ধরন | File Path (relative) |
| অর্থ | প্রোজেক্টের `src/app/[store]/dashboard/inventory/` ফোল্ডারের ভেতরের `inventory-content.tsx` ফাইল |
| `[store]` | Next.js Dynamic Route Segment — ফোল্ডারের নাম আক্ষরিকভাবেই `[store]` |
| `\` (backslash) | Windows-এ path separator হিসেবে ব্যবহার হয় |

---

### [৩] `|` (Pipe Operator)
| বিষয় | বিবরণ |
|-------|--------|
| কাজ | `Get-Content`-এর output কে `Select-String`-এর input হিসেবে পাঠায় |
| অর্থ | "এক command-এর result পরের command-এ দাও" |

---

### [৪] `Select-String "overflow|min-w|sm:max-w|sm:text-3xl|flex-col"`
| বিষয় | বিবরণ |
|-------|--------|
| ধরন | PowerShell Built-in Cmdlet |
| কাজ | প্রতিটি লাইনে Pattern খোঁজে, যে লাইনে match পায় সেটি print করে |
| Linux equivalent | `grep` |
| `"..."` | Search pattern (double quote-এর ভেতরে) |
| `\|` (pipe in pattern) | Regex OR operator — "এটা **অথবা** ওটা" মানে |

---

### 🎯 Search Pattern বিশ্লেষণ

```
"overflow|min-w|sm:max-w|sm:text-3xl|flex-col"
    ↑         ↑       ↑          ↑         ↑
    ①         ②       ③          ④         ⑤
```

| # | Pattern | কী খোঁজে |
|---|---------|-----------|
| ① | `overflow` | `overflow-hidden`, `overflow-auto`, `overflow-x-scroll` ইত্যাদি |
| ② | `min-w` | `min-w-0`, `min-w-full`, `min-w-[200px]` ইত্যাদি |
| ③ | `sm:max-w` | `sm:max-w-sm`, `sm:max-w-[90%]` ইত্যাদি |
| ④ | `sm:text-3xl` | Tailwind responsive text size class |
| ⑤ | `flex-col` | `flex-col`, `sm:flex-col` ইত্যাদি |

> **নোট:** `|` হলো Regex OR — মানে এই ৫টির যেকোনো **একটি** যে লাইনে আছে, সেই লাইন output-এ আসবে।

---

## 📤 Output কেমন দেখায়?

```
src\app\[store]\dashboard\inventory\inventory-content.tsx:12:  <div className="flex flex-col overflow-hidden">
src\app\[store]\dashboard\inventory\inventory-content.tsx:34:  <h1 className="sm:text-3xl text-xl font-bold">
src\app\[store]\dashboard\inventory\inventory-content.tsx:57:  <div className="min-w-0 flex-1">
```

**Format:**  `ফাইল পাথ : লাইন নম্বর : লাইনের content`

---

## ⚡ দরকারি Variations

### শুধু লাইন নম্বর দেখাতে চাইলে
```powershell
Get-Content src\app\[store]\dashboard\inventory\inventory-content.tsx `
  | Select-String "overflow|min-w|sm:max-w|sm:text-3xl|flex-col" `
  | Select-Object LineNumber, Line
```

### Case-sensitive search করতে চাইলে
```powershell
Get-Content src\app\[store]\dashboard\inventory\inventory-content.tsx `
  | Select-String "overflow|flex-col" -CaseSensitive
```

### একাধিক ফাইলে একসাথে খুঁজতে চাইলে
```powershell
Get-ChildItem src\app\[store]\dashboard\ -Recurse -Filter "*.tsx" `
  | Select-String "overflow|min-w|sm:max-w|sm:text-3xl|flex-col"
```

---

## 🗺️ Visual Flow

```
inventory-content.tsx
        │
        │  Get-Content (লাইন বাই লাইন পড়ে)
        ↓
┌───────────────────┐
│  line 1           │
│  line 2           │  ──→  Select-String  ──→  matched লাইনগুলো print
│  line 3  ✅ match │              ↑
│  line 4           │     "overflow|min-w|..."
│  ...              │
└───────────────────┘
```

---

> **💡 মনে রাখো:** এই command টি essentially PowerShell-এর `grep` — Linux/macOS-এ একই কাজ করতে লিখতে হতো:
> ```bash
> grep -n "overflow\|min-w\|sm:max-w\|sm:text-3xl\|flex-col" src/app/\[store\]/dashboard/inventory/inventory-content.tsx
> ```
