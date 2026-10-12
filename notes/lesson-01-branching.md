# 🚀 Lesson 1: Git Branching လက်တွေ့လေ့ကျင့်ခန်း

ဤလေ့ကျင့်ခန်းတွင် Git Branch အသစ်များ ဖန်တီးခြင်း၊ Branch အချင်းချင်း ကူးပြောင်းခြင်း၊ စစ်ဆေးခြင်းနှင့် မလိုအပ်တော့သော Branch များကို ဖျက်ခြင်းတို့ကို လက်တွေ့လေ့ကျင့်ပါမည်။

---

## 🎯 ရည်ရွယ်ချက် (Objectives)
- Branch များ၏ သဘောတရားနှင့် အလုပ်လုပ်ပုံကို နားလည်စေရန်
- `git branch`၊ `git checkout -b`၊ `git switch -c` command များကို ကျွမ်းကျင်စွာ အသုံးပြုနိုင်ရန်
- Safe Delete (`-d`) နှင့် Force Delete (`-D`) ကွာခြားချက်ကို သိရှိစေရန်

---

## 🛠️ ကြိုတင်ပြင်ဆင်ခြင်း (Prerequisites & Setup)

လက်တွေ့လေ့ကျင့်ရန်အတွက် သီးသန့် practice folder အသစ်တစ်ခု ဆောက်လုပ်ပါမည်။

```bash
# ၁။ Folder အသစ်ဆောက်ပြီး ထို folder ထဲသို့ ဝင်ပါ
mkdir git-practice
cd git-practice

# ၂။ Git Repository အသစ် စတင်ပါ
git init

# ၃။ Initial commit အတွက် index.html ဖိုင်ဆောက်ပြီး commit တင်ပါ
echo "Hello Git" > index.html
git add index.html
git commit -m "Initial commit: add index.html"
```

> [!NOTE]
> သင်၏ ပင်မ branch သည် Git version ပေါ်မူတည်၍ `master` သို့မဟုတ် `main` ဖြစ်နေနိုင်ပါသည်။ လက်ရှိ branch အမည်ကို `git branch --show-current` ဖြင့် စစ်ဆေးနိုင်ပါသည်။

---

## 📝 အဆင့်ဆင့် လက်တွေ့လေ့ကျင့်ခန်းများ (Step-by-Step Practice)

### အဆင့် (၁) - လက်ရှိ Branch များကို စစ်ဆေးခြင်း
```bash
git branch
```
*ရလဒ်:* `* main` (သို့မဟုတ် `* master`) ကို အစိမ်းရောင် သို့မဟုတ် ကြယ်ပွင့်ဖြင့် တွေ့ရပါမည်။

---

### အဆင့် (၂) - Branch အသစ်ဆောက်ပြီး တစ်ခါတည်း ကူးပြောင်းခြင်း
Login feature အတွက် branch အသစ်တစ်ခု ဆောက်လုပ်ပါမည်။

```bash
# နည်းလမ်း (က) - Traditional Command
git checkout -b feature/login

# သို့မဟုတ် နည်းလမ်း (ခ) - Modern Git Command (အကြံပြုထားသော နည်းလမ်း)
# git switch -c feature/login
```

*စစ်ဆေးရန်:*
```bash
git branch --show-current
```
*မျှော်လင့်ထားသည့် ရလဒ်:* `feature/login` ပေါ်သို့ ရောက်ရှိနေရပါမည်။

---

### အဆင့် (၃) - Feature Branch တွင် Code အပြောင်းအလဲ ပြုလုပ်၍ Commit တင်ခြင်း
```bash
# login.html ဖိုင်အသစ် ဖန်တီးမည်
echo "Login Page" > login.html

# Staged area သို့ ထည့်သွင်းမည်
git add login.html

# Commit တင်မည်
git commit -m "feat: add login feature"
```

*စစ်ဆေးရန်:*
```bash
git log --oneline -n 2
```
*ရလဒ်:* `Initial commit` အပေါ်တွင် `feat: add login feature` commit အသစ် ရောက်ရှိနေသည်ကို မြင်တွေ့ရပါမည်။

---

### အဆင့် (၄) - Main Branch သို့ ပြန်လည်ကူးပြောင်းခြင်း
```bash
git checkout main
# (သို့မဟုတ်: git switch main)
```

*စစ်ဆေးရန်:*
```bash
# Folder ထဲရှိ ဖိုင်များကို ကြည့်ပါ
ls
# (သို့မဟုတ် Windows PowerShell တွင်: dir)
```
> [!IMPORTANT]
> `main` branch ပေါ်သို့ ပြန်ရောက်သွားသည့်အခါ ခုနက ဆောက်ခဲ့သော `login.html` ဖိုင် ပျောက်ကွယ်သွားသည်ကို တွေ့ရပါမည်။ အဘယ်ကြောင့်ဆိုသော် ထိုဖိုင်သည် `feature/login` branch ပေါ်တွင်သာ ရှိနေသေးသောကြောင့် ဖြစ်ပါသည်။

---

### အဆင့် (၅) - စမ်းသပ်ရန် Branch အသစ်တစ်ခု ထပ်ဆောက်ပြီး ဖျက်ပစ်ခြင်း
မလိုလားအပ်သော သို့မဟုတ် အလုပ်ပြီးမြောက်သွားသော branch များကို ဖျက်ပစ်ပုံကို စမ်းသပ်ပါမည်။

```bash
# ၁။ စမ်းသပ်ရန် branch အသစ်ဆောက်ပါ
git branch test-branch

# ၂။ Branch စာရင်းကို ကြည့်ပါ
git branch

# ၃။ Branch ကို ဖျက်ပစ်ပါ (Safe delete)
git branch -d test-branch
```

> [!WARNING]
> မိမိ လက်ရှိ ရောက်နေသော (Active ဖြစ်နေသော) branch ကို ဖျက်၍ မရပါ။ အခြား branch တစ်ခုသို့ အရင်ကူးပြောင်း (switch) ပြီးမှသာ ဖျက်နိုင်ပါသည်။

---

## 📊 Summary of Commands

| Command | ဖော်ပြချက် |
| :--- | :--- |
| `git branch` | ရှိပြီးသား Local branch များကို ကြည့်ရှုခြင်း |
| `git branch <name>` | Branch အသစ် ဖန်တီးခြင်း (ကူးမပြောင်းသေးပါ) |
| `git checkout -b <name>` / `git switch -c <name>` | Branch အသစ် ဖန်တီးပြီး တစ်ခါတည်း switch လုပ်ခြင်း |
| `git switch <name>` | ရှိပြီးသား branch သို့ ကူးပြောင်းခြင်း |
| `git branch -d <name>` | Merged ဖြစ်ပြီးသား branch ကို ဘေးကင်းစွာ ဖျက်ခြင်း |
| `git branch -D <name>` | မပေါင်းရသေးသည့် branch ကို အတင်းအဓမ္မ ဖျက်ခြင်း (Force delete) |

---

👉 **နောက်တစ်ဆင့်:** `feature/login` branch ကို မဖျက်ပါနှင့်။ [Lesson 2: Merge Strategies](./lesson-02-merge-strategies.md) တွင် Fast-Forward Merge ပြုလုပ်ရန် ဆက်လက် အသုံးပြုပါမည်။
