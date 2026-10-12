# 🔍 Scenario 5: Automated Incident Root Cause Analysis with Git Bisect (DevOps / Platform)

## 🏢 Scenario Background (လက်တွေ့ အခြေအနေ)
သင်သည် Platform / SRE (Site Reliability Engineer) တစ်ဦး ဖြစ်သည်။ Microservices Repo တစ်ခုတွင် Developer များ ပူးပေါင်း၍ တစ်ပတ်အတွင်း commit ပေါင်း ၄၀ ခန့်ကို `main` branch သို့ merge လုပ်ခဲ့ကြသည်။
Repository တွင် Docker container build နှင့် integration test များကို စစ်ဆေးသည့် Automated CI Pipeline ရှိသည်။

---

## 💥 The Incident (ကြုံတွေ့ရသော ပြဿနာ)
Nightly Performance Test တွင် Container Image Build Time သည် မူလ **၃ မိနစ်** မှ **၂၅ မိနစ်** သို့ ထိုးတက်သွားပြီး Production deployment pipeline တစ်ခုလုံး အဆမတန် နှေးကွေးသွားသည်။

**စိန်ခေါ်မှု:**
- လွန်ခဲ့သော ၇ ရက်အတွင်း commit ပေါင်း (၄၀) merge လုပ်ခဲ့သည်။
- Commit ၄၀ လုံးကို တစ်ခုချင်းစီ checkout လုပ်ပြီး build စမ်းသပ်ရန် နာရီပေါင်းများစွာ အချိန်ကုန်မည်။
- မည်သည့် commit က ဤ performance regression ကို ဖြစ်စေခဲ့သည်ကို အမြန်ဆုံး ဖော်ထုတ်ရမည်။

---

## 🛠️ Step-by-Step Resolution (Git Bisect Binary Search ဖြင့် အဆင့်ဆင့် ရှာဖွေပုံ)

`git bisect` သည် သင်္ချာ၏ **Binary Search (နှစ်ခြမ်းခွဲ ရှာဖွေခြင်း)** algorithm ကို အသုံးပြု၍ အပြစ်ရှိသော commit (Bad Commit) ကို အဆင့် အနည်းငယ် (ဥပမာ- commit ၄၀ ကို ၅ ကြိမ် စမ်းသပ်ရုံဖြင့်) တိကျစွာ ရှာဖွေပေးနိုင်သော စွမ်းအားမြင့် tool ဖြစ်ပါသည်။

### နည်းလမ်း (၁) - အလိုအလျောက် Script ဖြင့် ရှာဖွေခြင်း (`git bisect run`) (အကြံပြုထားသော နည်းလမ်း)

စစ်ဆေးပေးမည့် Test Script (ဥပမာ `test-build.sh`) တစ်ခု ရေးပါ:
```bash
#!/bin/bash
# Docker build time သို့မဟုတ် configuration ကို စစ်ဆေးသည့် script
# Exit code 0 ဖြစ်ပါက အောင်မြင်သည် (Good)
# Exit code 1 ဖြစ်ပါက ကျရှုံးသည် (Bad)
./mvnw clean package -DskipTests
```

Script အဆင်သင့်ဖြစ်ပါက Git ကို အလိုအလျောက် ရှာဖွေခိုင်းပါမည်:
```bash
# ၁။ Bisect စတင်ပါ
git bisect start

# ၂။ လက်ရှိ အခြေအနေသည် ပျက်စီးနေသော (Bad) အခြေအနေ ဖြစ်သည်
git bisect bad HEAD

# ၃။ လွန်ခဲ့သော အပတ်က release tag v2.0.0 သည် ကောင်းမွန်သော (Good) အခြေအနေ ဖြစ်သည်
git bisect good v2.0.0

# ၄။ Script ဖြင့် အလိုအလျောက် binary search run ပါ
git bisect run ./test-build.sh
```

---

### နည်းလမ်း (၂) - လက်ဖြင့် တစ်ဆင့်ချင်းစီ စစ်ဆေးခြင်း (Manual Bisect)

အကယ်၍ test script မရှိပါက Terminal မှ လက်ဖြင့် စစ်ဆေးနိုင်ပါသည်:

```bash
# ၁။ Bisect စတင်ပါ
git bisect start
git bisect bad HEAD
git bisect good v2.0.0

# Git သည် အလယ်တည့်တည့်ရှိ commit သို့ အလိုအလျောက် checkout လုပ်ပေးပါမည်
# "Bisecting: 20 revisions left to test after this (roughly 5 steps)"

# ၂။ Build စမ်းသပ်ကြည့်ပါ (သို့မဟုတ် test run ပါ)
docker build -t test-app .

# ၃။ အကယ်၍ build မြန်ဆန်နေဆဲဆိုလျှင် 'good' ဟု ရိုက်ပါ
git bisect good

# ၄။ အကယ်၍ build နှေးကွေးနေလျှင် 'bad' ဟု ရိုက်ပါ
git bisect bad

# Git သည် ကျန်ရှိသော commit များကို ထပ်မံ ၂ ခြမ်းခွဲပေးပါမည်။ 
# အဆင့် ၅ ဆင့်ခန့် အပြီးတွင် အောက်ပါအတိုင်း တရားခံ commit ကို ဖော်ထုတ်ပြသပါမည်-
```

#### 🎯 တွေ့ရှိသည့် ရလဒ်:
```text
7c8d9e0f is the first bad commit
Author: dev-junior <dev@company.com>
Date:   Wed Oct 7 14:20:00 2026
    build: add uncompressed dependencies into docker context
```

---

### အဆင့် (နောက်ဆုံး) - ပြဿနာကို ဖြေရှင်းပြီး Bisect ပိတ်သိမ်းခြင်း

တရားခံ commit ကို သိရှိသွားပြီဖြစ်၍ အဆိုပါ commit ကို revert လုပ်ပြီး bisect mode မှ ထွက်ခွာပါမည်:

```bash
# ၁။ Bisect mode မှ ပုံမှန် အခြေအနေသို့ ပြန်သွားပါ
git bisect reset

# ၂။ တရားခံ commit ကို ချက်ချင်း revert ပြုလုပ်ပါ
git revert 7c8d9e0f --no-edit

# ၃။ Pipeline ပြန်လည် run စေရန် push တင်ပါ
git push origin main
```

---

## 💡 DevOps Key Takeaways & Best Practices

1. **Massive Time Saver:**
   - Commit ၁၀၀ ရှိနေလျှင်တောင်မှ `git bisect` သည် ၇ ကြိမ်သာ စစ်ဆေးရသဖြင့် နာရီပေါင်းများစွာ ကြာမည့် debugging ကို ၅ မိနစ်အတွင်း ပြီးစီးစေပါသည်။
2. **Automate with `git bisect run`:**
   - Unit test သို့မဟုတ် integration test script တစ်ခု ရှိရုံဖြင့် ညဘက် incident ဖြစ်ပွားချိန်တွင် အလိုအလျောက် root cause ရှာဖွေနိုင်ပါသည်။
3. **Always tag stable releases:**
   - `git bisect good <tag>` အလွယ်တကူ သတ်မှတ်နိုင်ရန်အတွက် Production release တိုင်းကို `git tag` ထိုးထားသည့် အလေ့အထ ရှိရပါမည်။
