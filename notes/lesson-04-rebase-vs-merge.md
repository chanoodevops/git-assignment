# 🔀 Lesson 4: Git Rebase vs Merge လက်တွေ့လေ့ကျင့်ခန်း

ဤလေ့ကျင့်ခန်းတွင် **Git Rebase** နှင့် **Git Merge** တို့ မည်သို့ ကွာခြားသည်ကို လက်တွေ့ စမ်းသပ်ကာ သန့်ရှင်းသော **Linear History (တစ်ဆက်တည်းဖြစ်သော သမိုင်းကြောင်း)** ဖန်တီးပုံကို လေ့ကျင့်ပါမည်။

---

## 🎯 ရည်ရွယ်ချက် (Objectives)
- Rebase လုပ်ဆောင်သည့်အခါ Git က Commit များကို မည်သို့ ပြန်လည် နေရာချထားသည်ကို မျက်မြင် နားလည်စေရန်
- Rebase လုပ်ပြီးနောက် Fast-Forward Merge ပြုလုပ်ပုံကို ကျွမ်းကျင်စွာ အသုံးပြုနိုင်ရန်
- Rebase Conflict ဖြေရှင်းခြင်း (`--continue` / `--abort`) အခြေခံကို နားလည်စေရန်
- The Golden Rule of Rebase ကို သိရှိလိုက်နာစေရန်

---

## 📝 အဆင့်ဆင့် လက်တွေ့လေ့ကျင့်ခန်းများ

### အဆင့် (၁) - Feature Branch အသစ် ဖန်တီး၍ Commit ပြုလုပ်ခြင်း

```bash
# ၁။ Feature branch အသစ်ဆောက်ပြီး ပြောင်းပါ
git checkout -b feature/settings

# ၂။ settings.html ဖိုင်ဆောက်ပြီး commit တင်ပါ
echo "Settings Page" > settings.html
git add settings.html
git commit -m "feat: add settings page"
```

---

### အဆင့် (၂) - Main Branch တွင် အပြောင်းအလဲ အသစ် ပြုလုပ်ခြင်း

လက်ရှိ Feature branch ခွဲထွက်သွားပြီးနောက် `main` branch တွင်လည်း အခြား developer တစ်ဦးမှ commit အသစ် တင်လိုက်သည့် အခြေအနေကို ဖန်တီးပါမည်။

```bash
# ၁။ Main branch သို့ ပြန်သွားပါ
git checkout main

# ၂။ index.html တွင် အပြောင်းအလဲ ပြုလုပ်ပါ
echo "Footer Information" >> index.html
git add index.html
git commit -m "feat: update footer in main"
```

*စစ်ဆေးရန်:*
```bash
git log --oneline --graph --all -n 4
```
*ရလဒ်:* `main` နှင့် `feature/settings` တို့သည် အသီးသီး ခွဲထွက်သွားသော Branch နှစ်ခု (Divergent branches) ဖြစ်နေသည်ကို မြင်တွေ့ရပါမည်။

---

### အဆင့် (၃) - Feature Branch ပေါ်တွင် Rebase စတင် ပြုလုပ်ခြင်း

Feature branch ရှိ commit များကို `main` ၏ နောက်ဆုံး commit ပေါ်သို့ Base ပြောင်း၍ ပြန်လည် တင်ပါမည်။

```bash
# ၁။ Feature branch ပေါ်သို့ အရင်ပြောင်းပါ
git checkout feature/settings

# ၂။ Main ၏ နောက်ဆုံး အပြောင်းအလဲများပေါ်သို့ Rebase လုပ်ပါ
git rebase main
```

*ရလဒ် စစ်ဆေးရန်:*
```bash
git log --oneline --graph -n 4
```
*ရလဒ်:* ယခုအခါ Branch ခွဲမရှိတော့ဘဲ `main` ၏ နောက်ဆုံး commit ဖြစ်သော "feat: update footer in main" ၏ အပေါ်တည့်တည့်သို့ "feat: add settings page" commit သည် ဖြောင့်တန်းစွာ ပြောင်းရွှေ့ရောက်ရှိသွားပါပြီ။

---

### အဆင့် (၄) - Main Branch သို့ ပြန်သွားပြီး Fast-Forward Merge ပြုလုပ်ခြင်း

Rebase လုပ်ပြီးသွားပြီဖြစ်သောကြောင့် `main` သို့ ပြန်သွား၍ Merge လုပ်ပါက Merge Commit အသစ် ထွက်စရာမလိုဘဲ Fast-Forward ဖြစ်သွားပါမည်။

```bash
# ၁။ Main သို့ ပြန်သွားပါ
git checkout main

# ၂။ Feature branch ကို ပေါင်းထည့်ပါ
git merge feature/settings
```

*နောက်ဆုံး History ကို စစ်ဆေးရန်:*
```bash
git log --oneline --graph -n 5
```
*မျှော်လင့်ထားသည့် ရလဒ်:*
```text
* feat: add settings page (HEAD -> main, feature/settings)
* feat: update footer in main
* feat: complete user profile feature
...
```
Branch အခွဲများ မရှိတော့ဘဲ လိုင်းတစ်လိုင်းတည်းဖြစ်သော **သန့်ရှင်းသည့် Linear History** အဖြစ် ပြီးမြောက်သွားပါပြီ။

---

## 🛠️ Rebase Conflict ဖြစ်ပေါ်လာပါက ဖြေရှင်းနည်း

အကယ်၍ `git rebase main` လုပ်နေစဉ် Conflict ဖြစ်ပေါ်လာပါက:

1. Conflict ဖြစ်နေသော ဖိုင်များကို ဖွင့်၍ အမှန်ပြင်ဆင်ပါ။
2. ပြင်ဆင်ပြီးသော ဖိုင်များကို Stage လုပ်ပါ:
   ```bash
   git add <filename>
   ```
3. Rebase ကို ဆက်လက် လုပ်ဆောင်ပါ (Commit မတင်ရပါ):
   ```bash
   git rebase --continue
   ```
4. အကယ်၍ အဆင်မပြေဘဲ Rebase မလုပ်မီ မူလအခြေအနေသို့ လုံးဝ ပြန်သွားလိုပါက:
   ```bash
   git rebase --abort
   ```

---

## ⚠️ The Golden Rule of Rebase (အရေးကြီး သတိပေးချက်)

> [!CAUTION]
> **အများသုံး Public / Shared Branch များတွင် `git rebase` လုံးဝ မလုပ်ပါနှင့်!**
> ဥပမာ- `main`, `master`, `staging` ကဲ့သို့သော အဖွဲ့သားများ မျှဝေသုံးစွဲနေသည့် branch များတွင် commit history ကို rewrite လုပ်မိပါက အခြား developer များအတွက် ကြီးမားသော conflict ပြဿနာများ ဖြစ်ပေါ်စေပါသည်။
> 
> - **Private Branch (မိမိ Local တွင်သာ):** `git rebase` သုံးပါ။
> - **Shared/Public Branch:** `git merge` သုံးပါ။

---

## 🏁 သင်ခန်းစာများ အပြီးသတ် အနှစ်ချုပ်

ဂုဏ်ယူပါသည်! သင်သည် Git ၏ အဓိက အယူအဆများဖြစ်သော:
1. [Branching နည်းပညာများ](./lesson-01-branching.md)
2. [Merge Strategies (Fast-Forward, 3-Way, Squash)](./lesson-02-merge-strategies.md)
3. [Time Travel (Revert, Soft/Mixed/Hard Reset, Reflog)](./lesson-03-time-travel.md)
4. [Rebase vs Merge Workflow](./lesson-04-rebase-vs-merge.md)

စသည်တို့ကို အောင်မြင်စွာ လက်တွေ့ လေ့ကျင့် ပြီးမြောက်သွားပြီ ဖြစ်ပါသည်။
