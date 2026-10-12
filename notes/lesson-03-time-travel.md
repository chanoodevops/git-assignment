# ⏪ Lesson 3: Git Time Travel (Revert & Reset) လက်တွေ့လေ့ကျင့်ခန်း

ဤလေ့ကျင့်ခန်းတွင် Git ဖြင့် အတိတ်သို့ ပြန်သွားခြင်း၊ အမှားများကို ပြန်လည်ပြင်ဆင်ခြင်းနှင့် Commit များကို ပယ်ဖျက်ခြင်း နည်းလမ်းများဖြစ်သည့် **Revert** နှင့် **Reset (`--soft`, `--mixed`, `--hard`)** တို့ကို လက်တွေ့လေ့ကျင့်ပါမည်။

---

## 🎯 ရည်ရွယ်ချက် (Objectives)
- `git revert` နှင့် `git reset` ၏ အခြေခံ ကွာခြားချက်ကို သဘောပေါက်စေရန်
- `reset` ၏ options များဖြစ်သော `--soft`၊ `--mixed` နှင့် `--hard` တို့၏ အလုပ်လုပ်ပုံကို လက်တွေ့ မျက်မြင်စစ်ဆေးနိုင်ရန်
- မှားယွင်းစွာ ဖျက်မိပါက `git reflog` ဖြင့် အရေးပေါ် ပြန်လည် ကယ်တင်နိုင်စေရန်

---

## 📝 အဆင့်ဆင့် လက်တွေ့လေ့ကျင့်ခန်းများ

### အဆင့် (၁) - လက်ရှိ Commit History ကို စစ်ဆေးခြင်း
လေ့ကျင့်ခန်း စတင်ရန် လက်ရှိ commit history ကို ကြည့်ပါ။

```bash
git log --oneline -n 5
```

---

### နည်းလမ်း (၁) - Git Revert (လုံခြုံစိတ်ချရသော ပြန်ပြင်နည်း)

`git revert` သည် အရင် Commit အား ဖျက်ဆီးပစ်ခြင်း မဟုတ်ဘဲ၊ ထို commit တွင် ပြင်ဆင်ခဲ့သမျှကို ဆန့်ကျင်ဘက် ပြန်လည်ပြင်ဆင်သည့် **Commit အသစ်တစ်ခု** ဖန်တီးပေးခြင်း ဖြစ်ပါသည်။ ထို့ကြောင့် Public/Shared Branch များတွင် အမြဲ အသုံးပြုသင့်ပါသည်။

```bash
# ၁။ စမ်းသပ်ရန် မှားယွင်းသော commit တစ်ခု ဖန်တီးပါ
echo "Wrong buggy code" > bug.txt
git add bug.txt
git commit -m "feat: add buggy code"

# ၂။ Commit hash ကို ကြည့်ပါ
git log --oneline -n 2
# (ဥပမာ output: "a1b2c3d feat: add buggy code")

# ၃။ Revert ပြုလုပ်ပါ (နောက်ဆုံး commit ကို revert လုပ်မည်)
git revert HEAD --no-edit

# ၄။ ရလဒ်ကို စစ်ဆေးပါ
git log --oneline -n 3
ls
```

*ရလဒ်:* `Revert "feat: add buggy code"` ဟူသော commit အသစ်တစ်ခု ထွက်ပေါ်လာပြီး `bug.txt` ဖိုင်လည်း working directory ထဲမှ အလိုအလျောက် ပယ်ဖျက်သွားသည်ကို တွေ့ရပါမည်။

---

### နည်းလမ်း (၂) - Git Reset --soft (Commit ကိုသာ ပယ်ဖျက်ခြင်း)

`--soft` သည် နောက်ဆုံး commit ကို ပယ်ဖျက်ပေးသော်လည်း ရေးသားထားသော ကုဒ်များကို **Staging Area (`git add` လုပ်ထားပြီးသား အခြေအနေ)** တွင် ဆက်လက် ထိန်းသိမ်းထားပေးပါသည်။ Commit message ပြင်ချင်သည့်အခါ သို့မဟုတ် ဖိုင်များ ထပ်ဖြည့်ချင်သည့်အခါ သုံးပါသည်။

```bash
# ၁။ စမ်းသပ်ရန် commit တစ်ခု လုပ်ပါ
echo "Temporary notes" > notes.txt
git add notes.txt
git commit -m "temp commit to test soft reset"

# ၂။ Soft reset ဖြင့် နောက်ဆုံး ၁ ခုကို ပြန်ဖြုတ်ပါ
git reset --soft HEAD~1

# ၃။ Status ကို စစ်ဆေးပါ
git status
```

*ရလဒ်:* `Changes to be committed:` အောက်တွင် `notes.txt` သည် အစိမ်းရောင်ဖြင့် Staged ဖြစ်နေဆဲကို တွေ့ရပါမည်။

---

### နည်းလမ်း (၃) - Git Reset --mixed (Default Reset)

`--mixed` သည် Commit ကို ပယ်ဖျက်သည့်အပြင် Staging Area မှလည်း ပြန်ဖြုတ်ပေးပါသည်။ သို့သော် သင်ရေးသားထားသော ကုဒ်များသည် **Working Directory ထဲတွင် မပျက်စီးဘဲ** ဆက်လက် တည်ရှိနေပါသည်။

```bash
# ၁။ အထက်ပါ Staged ဖြစ်နေသော notes.txt ကို commit ပြန်တင်ပါ
git commit -m "temp commit to test mixed reset"

# ၂။ Mixed reset လုပ်ပါ (Flag မထည့်ပါက default ဖြစ်သည်)
git reset HEAD~1

# ၃။ Status ကို စစ်ဆေးပါ
git status
```

*ရလဒ်:* `Untracked files:` အောက်တွင် `notes.txt` သည် အနီရောင်ဖြင့် ရှိနေဆဲ ဖြစ်သည်ကို တွေ့ရပါမည်။

---

### နည်းလမ်း (၄) - Git Reset --hard (အကြွင်းမဲ့ အပြီးပယ်ဖျက်ခြင်း)

`--hard` သည် Commit ကော၊ Staged Area ကော၊ Working Directory ထဲရှိ ရေးထားသမျှ ကုဒ်များပါ **လုံးဝ အပြီးပိုင် ပယ်ဖျက်ပစ်ခြင်း** ဖြစ်ပါသည်။ သတိကြီးစွာ ထား၍ သုံးရပါမည်။

```bash
# ၁။ စမ်းသပ်ရန် ဖိုင်အသစ် ရေးပြီး commit တင်ပါ
echo "Secret dangerous data" > secret.txt
git add secret.txt
git commit -m "bad commit to be destroyed"

# ၂။ Hard reset ဖြင့် အပြီးဖျက်ပစ်ပါ
git reset --hard HEAD~1

# ၃။ ဖိုင်ကို စစ်ဆေးပါ
ls
git status
```

*ရလဒ်:* `secret.txt` ဖိုင်သည် သင်၏ ကွန်ပျူတာထဲမှ လုံးဝ ပျောက်ကွယ်သွားပါမည်။

---

### 💡 Bonus အရေးပေါ် ကယ်တင်နည်း (`git reflog`)

အကယ်၍ `git reset --hard` ကြောင့် အရေးကြီးသော Commit များ အမှားအယွင်းကြောင့် ပျောက်ဆုံးသွားခဲ့လျှင်:

```bash
# ၁။ Git ၏ လုပ်ဆောင်မှု မှတ်တမ်းအကုန်လုံးကို ပြန်ကြည့်ပါ
git reflog

# ၂။ ဖျက်လိုက်သော commit မတိုင်မီ အခြေအနေ (ဥပမာ HEAD@{1}) ကို ရှာပြီး ပြန်လည် ခေါ်ယူပါ
git reset --hard HEAD@{1}
```

---

## 📊 Revert vs Reset နှိုင်းယှဉ်ချက်

| Command | Commit History | ကုဒ်များ (Working Directory) | ဘယ်နေရာမှာ သုံးမလဲ |
| :--- | :--- | :--- | :--- |
| `git revert <hash>` | Commit အသစ်တစ်ခု ထပ်တိုးသည် | အပြောင်းအလဲများကို ပြောင်းပြန်ပြင်သည် | **Public / Main / Shared Branch များ** |
| `git reset --soft` | Commit ပျက်သည် | Staged အဖြစ် ဆက်ရှိသည် | Commit message / staging ပြန်ပြင်လိုချိန် |
| `git reset --mixed` | Commit ပျက်သည် | Unstaged အဖြစ် ဆက်ရှိသည် | Code ကို ပြန်လည် rework လုပ်လိုချိန် |
| `git reset --hard` | Commit ပျက်သည် | **အကုန် ပျက်စီးသွားသည်** | စမ်းသပ်ချက်များကို အကုန်စွန့်ပစ်လိုချိန် |

---

👉 **နောက်တစ်ဆင့်:** [Lesson 4: Rebase vs Merge](./lesson-04-rebase-vs-merge.md) တွင် သန့်ရှင်းသော Linear History ဖန်တီးနည်းကို ဆက်လက် လေ့ကျင့်ပါမည်။
