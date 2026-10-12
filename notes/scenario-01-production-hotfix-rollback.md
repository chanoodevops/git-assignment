# 🚨 Scenario 1: Production Incident Hotfix & Safe Rollback (DevOps / Platform)

## 🏢 Scenario Background (လက်တွေ့ အခြေအနေ)
သင်သည် E-Commerce Platform တစ်ခု၏ **DevOps / Platform Engineer** ဖြစ်သည်။ ကုမ္ပဏီသည် **GitOps (ArgoCD / Flux)** သို့မဟုတ် **Automated CI/CD Pipeline (GitHub Actions / GitLab CI)** ကို အသုံးပြု၍ `main` (သို့မဟုတ် `production`) branch သို့ commit ရောက်ရှိသည်နှင့် Production Kubernetes Cluster ပေါ်သို့ အလိုအလျောက် Deploy လုပ်သည့် စနစ်ကို ကျင့်သုံးနေသည်။

---

## 💥 The Incident (ကြုံတွေ့ရသော ပြဿနာ)
ည ၈ နာရီခန့်တွင် မကြာသေးမီက deploy လိုက်သော feature အသစ်ကြောင့် Production Database Connection Pool ပြည့်သွားပြီး API Server များ 500 Internal Server Error တက်ကာ Service ကျသွားသည်။ 
Monitoring Dashboard (Datadog / Prometheus) တွင် Alert များ အဆက်မပြတ် တက်လာသည်။

**ရည်မှန်းချက်:** 
1. Service ကို အမြန်ဆုံး ပုံမှန် ပြန်လည်လည်ပတ်နိုင်စေရန် (MTTR - Mean Time to Recovery နည်းစေရန်) အန္တရာယ်ကင်းစွာ **Rollback** လုပ်မည်။
2. အရေးပေါ် Bug Fix (Hotfix) ကို ပြုလုပ်ပြီး Production နှင့် Main branch နှစ်ခုစလုံးသို့ စနစ်တကျ ပြန်လည် ပေါင်းစပ်မည်။

---

## 🛠️ Step-by-Step Incident Resolution (အဆင့်ဆင့် ဖြေရှင်းပုံ)

### အဆင့် (၁) - အရေးပေါ် Production Rollback (Safe Revert)

DevOps လောကတွင် Shared / Production Branch ပေါ်တွင် `git reset --hard` ကို လုံးဝ မသုံးရပါ။ အဘယ်ကြောင့်ဆိုသော် GitOps စနစ်များ (ArgoCD) သည် Commit Hash အပြောင်းအလဲကို စောင့်ကြည့်နေပြီး၊ Commit History ဖျက်ပစ်ပါက Remote Sync Error တက်ကာ ပိုမိုဆိုးရွားစေနိုင်သောကြောင့် ဖြစ်ပါသည်။

```bash
# ၁။ Production (သို့မဟုတ် Main) branch ၏ နောက်ဆုံး အခြေအနေကို pull လုပ်ပါ
git checkout main
git pull origin main

# ၂။ ပြဿနာဖြစ်စေသော commit hash ကို ရှာဖွေပါ
git log --oneline -n 3
# ဥပမာ- 9f8a12b feat(api): increase connection pool timeout (BAD COMMIT)

# ၃။ Bad commit ကို ပြောင်းပြန်လှန်သည့် Safe Revert Commit ပြုလုပ်ပါ
git revert 9f8a12b --no-edit

# ၄။ CI/CD Pipeline ကို အလိုအလျောက် ပြန်လည် run စေရန် push လုပ်ပါ
git push origin main
```

> [!NOTE]
> `git revert` ပြုလုပ်လိုက်သောကြောင့် GitOps / CI/CD သည် Commit အသစ်တစ်ခုအဖြစ် မြင်တွေ့ပြီး ယခင် stable ဖြစ်ခဲ့သော configuration သို့ အလိုအလျောက် rollback လုပ်ကာ Production service ချက်ချင်း ပြန်လည် ကောင်းမွန်သွားမည်။

---

### အဆင့် (၂) - Hotfix Branch ဖန်တီး၍ အမြစ်ပြတ် ပြင်ဆင်ခြင်း

Rollback ကြောင့် Service ပုံမှန် ပြန်ဖြစ်သွားပြီဖြစ်၍ Bug ကို အေးအေးဆေးဆေး စစ်ဆေးပြင်ဆင်ရန် `hotfix/` branch ခွဲထွက်ပါမည်။

```bash
# ၁။ Main branch မှ hotfix branch ခွဲထွက်ပါ
git checkout -b hotfix/db-connection-pool

# ၂။ config ဖိုင်တွင် memory limit နှင့် pool size ကို ပြင်ဆင်ပါ
echo "pool_size: 20" > config/database.yaml
echo "timeout: 5s" >> config/database.yaml

# ၃။ ပြင်ဆင်ချက်ကို commit တင်ပါ
git add config/database.yaml
git commit -m "fix(db): optimize connection pool size and timeout for production"

# ၄။ Local စမ်းသပ်မှုနှင့် lint/test များ အောင်မြင်ကြောင်း စစ်ဆေးပါ
```

---

### အဆင့် (၃) - 3-Way Merge (`--no-ff`) ဖြင့် ပြန်လည်ပေါင်းစပ်ခြင်း

DevOps / Platform အဖွဲ့တွင် Hotfix သို့မဟုတ် Release များကို ပေါင်းစပ်သည့်အခါ **3-Way Merge (`--no-ff`)** ကို အသုံးပြုကြသည်။ အဘယ်ကြောင့်ဆိုသော် Post-Mortem (Incident Analysis) ပြုလုပ်သည့်အခါ ဘယ်အချိန်တွင် မည်သည့် Hotfix တင်ခဲ့သည်ကို Audit Trail အနေဖြင့် အတိအကျ ပြန်လည်စစ်ဆေးနိုင်ရန် ဖြစ်ပါသည်။

```bash
# ၁။ Main branch သို့ ပြောင်းပါ
git checkout main
git pull origin main

# ၂။ Explicit Merge Commit ဖြင့် ပေါင်းစပ်ပါ
git merge --no-ff hotfix/db-connection-pool -m "merge: hotfix/db-connection-pool into main (Incident #402)"

# ၃။ Production သို့ တိုက်ရိုက် Deploy လုပ်ရန် push တင်ပါ
git push origin main

# ၄။ အလုပ်ပြီးမြောက်သွားသော local hotfix branch ကို ဖျက်ပစ်ပါ
git branch -d hotfix/db-connection-pool
```

---

## 💡 DevOps Key Takeaways & Best Practices

1. **Revert vs Reset in Production:**
   - Public/Production branch များတွင် **အမြဲတမ်း `git revert` ကိုသာ အသုံးပြုပါ။** ၎င်းသည် GitOps Controller များ (ArgoCD/Flux) နှင့် အဆင်ပြေဆုံးဖြစ်ပြီး Audit Trail ကို ထိန်းသိမ်းပေးသည်။
   - `git reset --hard` ကို force push (`push -f`) လုပ်ခြင်းသည် အခြား DevOps အဖွဲ့သားများ၏ local repository များနှင့် CI/CD webhook များကို ပျက်စီးစေပါသည်။
2. **Auditability with `--no-ff`:**
   - Incident Hotfix များတွင် `--no-ff` merge commit သုံးခြင်းဖြင့် Incident ID (ဥပမာ `#402`) ကို သမိုင်းကြောင်းတွင် အတိအကျ မှတ်တမ်းတင်ထားနိုင်ပါသည်။
3. **Automated Tagging:**
   - Hotfix ပြီးတိုင်း `git tag -a v1.4.1 -m "Patch release: Fix DB connection pool"` ဖြင့် Semantic Versioning Tag ထိုးထားသင့်ပါသည်။
