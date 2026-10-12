# 🔀 Lesson 2: Git Merge Strategies လက်တွေ့လေ့ကျင့်ခန်း

ဤလေ့ကျင့်ခန်းတွင် Git ၏ အဓိက Merge Strategies (၃) မျိုးဖြစ်သော **Fast-Forward Merge**၊ **3-Way Merge (No-FF)** နှင့် **Squash Merge** တို့ကို လက်တွေ့ ရိုက်ထည့် စမ်းသပ်ပါမည်။

---

## 🎯 ရည်ရွယ်ချက် (Objectives)
- Fast-Forward၊ 3-Way Merge (`--no-ff`) နှင့် Squash Merge တို့ မည်သို့ ကွာခြားသည်ကို လက်တွေ့ မြင်တွေ့စေရန်
- `git log --graph --oneline --all` ကို အသုံးပြု၍ commit history ပုံစံများကို ကြည့်ရှုတတ်စေရန်
- အခြေအနေအလိုက် သင့်တော်သော Merge နည်းလမ်းကို ရွေးချယ်တတ်စေရန်

---

## 📝 အဆင့်ဆင့် လက်တွေ့လေ့ကျင့်ခန်းများ

### နည်းလမ်း (၁) - Fast-Forward Merge

Fast-Forward ဆိုသည်မှာ Feature branch ခွဲထွက်သွားပြီးနောက် `main` branch တွင် မည်သည့် အပြောင်းအလဲ အသစ်မျှ မရှိသေးသည့်အခါ Git သည် `main` ၏ pointer ကို Feature ၏ နောက်ဆုံး commit ဆီသို့ တိုက်ရိုက် ရွှေ့ပေးလိုက်ခြင်း ဖြစ်ပါသည်။

#### လက်တွေ့စမ်းသပ်ရန် အဆင့်များ:
```bash
# ၁။ Main branch ပေါ်တွင် ရောက်နေကြောင်း သေချာပါစေ
git checkout main

# ၂။ Lesson 1 တွင် ပြုလုပ်ခဲ့သော feature/login ကို fast-forward merge လုပ်မည်
git merge feature/login
```

*ရလဒ် စစ်ဆေးရန်:*
```bash
git log --oneline --graph
```
*ရလဒ်:* `Fast-forward` ဟု ဖော်ပြပြီး Merge Commit အသစ် မထွက်ဘဲ Linear History အဖြစ် ဖြောင့်တန်းစွာ ပေါင်းစပ်သွားသည်ကို တွေ့ရပါမည်။

---

### နည်းလမ်း (၂) - 3-Way Merge (`--no-ff`)

`main` branch မှာရော Feature branch မှာပါ အပြောင်းအလဲများ အသီးသီး ရှိနေချိန်တွင်ဖြစ်စေ၊ သို့မဟုတ် Feature တစ်ခုလုံး၏ သမိုင်းကြောင်းကို ပေါ်လွင်စေချင်သည့်အခါဖြစ်စေ Merge Commit အသစ်တစ်ခု ထုတ်လုပ်၍ ပေါင်းစပ်ခြင်း ဖြစ်ပါသည်။

#### လက်တွေ့စမ်းသပ်ရန် အဆင့်များ:

```bash
# ၁။ Branch အသစ် ဖန်တီးပြီး ပြောင်းပါ
git checkout -b feature/signup

# ၂။ Main branch သို့ ပြန်သွားပြီး commit အသစ်တစ်ခု လုပ်ပါ (Divergence ဖြစ်စေရန်)
git checkout main
echo "Updated main" >> index.html
git add index.html
git commit -m "Update index on main"

# ၃။ Feature branch သို့ ပြန်သွားပြီး code ရေးကာ commit လုပ်ပါ
git checkout feature/signup
echo "Signup Page" > signup.html
git add signup.html
git commit -m "feat: add signup feature"

# ၄။ Main branch သို့ ပြန်လာပါ
git checkout main

# ၅။ 3-Way Merge (Explicit Merge Commit) ဖြင့် ပေါင်းစပ်ပါ
git merge --no-ff feature/signup -m "Merge branch 'feature/signup' into main"
```

*ရလဒ် စစ်ဆေးရန်:*
```bash
git log --oneline --graph -n 6
```
*ရလဒ်:* အောက်ပါအတိုင်း Branch ခွဲထွက်သွားပြီး ပြန်လည် ပေါင်းဆုံကာ Merge Commit အသစ် ထွက်လာသည့် Graph ပုံစံကို တွေ့ရပါမည်-
```text
*   Merge branch 'feature/signup' into main
|\  
| * feat: add signup feature
* | Update index on main
|/  
* feat: add login feature
```

---

### နည်းလမ်း (၃) - Squash Merge

Feature branch တစ်ခုတွင် အသေးစိတ် commit များစွာ (ဥပမာ- "WIP", "fix typo", "update css") ရှိနေသော်လည်း `main` branch သို့ ရောက်သည့်အခါ commit တစ်ခုတည်းအဖြစ် သန့်ရှင်းစွာ ပေါင်းထည့်လိုသည့်အခါ အသုံးပြုပါသည်။

#### လက်တွေ့စမ်းသပ်ရန် အဆင့်များ:

```bash
# ၁။ Branch အသစ် ဖန်တီးပါ
git checkout -b feature/profile

# ၂။ ပထမ commit တစ်ခု ပြုလုပ်ပါ
echo "Profile v1" > profile.html
git add profile.html
git commit -m "WIP: start profile page"

# ၃။ ဒုတိယ commit တစ်ခု ထပ်မံပြုလုပ်ပါ
echo "Profile v2: Add Avatar" >> profile.html
git add profile.html
git commit -m "feat: add avatar to profile"

# ၄။ Main branch သို့ ပြန်သွားပါ
git checkout main

# ၅။ Squash Merge ပြုလုပ်ပါ (Commit မဖြစ်သေးဘဲ Staging Area သို့သာ ရောက်ပါမည်)
git merge --squash feature/profile

# ၆။ Commit တစ်ခုတည်းအဖြစ် တင်ပါ
git commit -m "feat: complete user profile feature"
```

*ရလဒ် စစ်ဆေးရန်:*
```bash
git log --oneline -n 3
```
*ရလဒ်:* Feature branch ထဲရှိ "WIP", "feat: add avatar" စသည့် commit အသေးစား ၂ ခု မပါလာတော့ဘဲ "feat: complete user profile feature" ဟူသော Commit တစ်ခုတည်းသာ `main` history ထဲသို့ သန့်ရှင်းစွာ ရောက်ရှိသွားသည်ကို တွေ့ရပါမည်။

---

## 📊 Merge Strategies နှိုင်းယှဉ်ချက် အကျဉ်း

| Strategy | Merge Commit အသစ် | History ပုံစံ | အသုံးများသည့်နေရာ |
| :--- | :---: | :--- | :--- |
| **Fast-Forward** | မထွက်ပါ | ဖြောင့်တန်းသည် (Linear) | Local updates, Simple task branches |
| **3-Way Merge (`--no-ff`)** | ထွက်သည် | Branch သမိုင်းကြောင်း အတိအကျ ပေါ်သည် | Team Releases, Production merges |
| **Squash Merge** | မထွက်ပါ (Commit အသစ် ၁ ခုသာရ) | သန့်ရှင်းပြီး ရိုးရှင်းသည် | Feature Branch PRs, Code Review ပြီးချိန် |

---

👉 **နောက်တစ်ဆင့်:** [Lesson 3: Time Travel (Revert & Reset)](./lesson-03-time-travel.md) တွင် အမှားများကို ပြန်လည်ပြင်ဆင်ခြင်းနှင့် အတိတ်သို့ ပြန်သွားခြင်းကို ဆက်လက် လေ့ကျင့်ပါမည်။
