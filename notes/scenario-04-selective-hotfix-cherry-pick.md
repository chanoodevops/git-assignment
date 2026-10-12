# 🍒 Scenario 4: Selective Hotfix Deployment with Git Cherry-Pick (DevOps / Platform)

## 🏢 Scenario Background (လက်တွေ့ အခြေအနေ)
သင်၏အဖွဲ့သည် Multi-Environment Pipeline (`develop` ➔ `staging` ➔ `production`) စနစ်ကို အသုံးပြုနေသည်။ လက်ရှိတွင် `develop` branch ပေါ်တွင် လာမည့်လ Release အတွက် Developer များ ရေးသားထားသော Feature အသစ်ပေါင်း (၁၅) ခု ရောက်ရှိနေပြီး QA အဖွဲ့မှ စမ်းသပ်စစ်ဆေးနေဆဲ (WIP - Work in Progress) ဖြစ်သည်။

---

## 💥 The Incident (ကြုံတွေ့ရသော ပြဿနာ)
Security Scan မှ လက်ရှိ Production တွင် ပြင်းထန်သော Security Vulnerability (CVE Patch) တစ်ခု ချက်ချင်း လိုအပ်နေကြောင်း တွေ့ရှိရသည်။
Developer တစ်ဦးက ထို bug ကို ပြင်ဆင်ပြီး `develop` branch ပေါ်သို့ commit တင်လိုက်သည် (Commit: `c8d7e6f fix: patch critical open-ssl vulnerability`).

**စိန်ခေါ်မှု:**
- အကယ်၍ `develop` branch ကို `main` (Production) သို့ တိုက်ရိုက် merge လုပ်လိုက်ပါက စမ်းသပ်ဆဲ မပြီးပြတ်သေးသော Feature ၁၅ ခုပါ Production သို့ အတူပါသွားပြီး System Crash ဖြစ်သွားနိုင်သည်။
- **လိုအပ်ချက်:** မပြီးသေးသော feature များကို ချန်ထားခဲ့ပြီး ထို CVE Patch commit **တစ်ခုတည်း** ကိုသာ Production သို့ အမြန်ဆုံး ဆွဲထုတ်ယူပြီး Deploy လုပ်ရမည်။

---

## 🛠️ Step-by-Step Resolution (Cherry-Pick ဖြင့် အဆင့်ဆင့် ဖြေရှင်းပုံ)

### အဆင့် (၁) - Commit Hash ကို ရှာဖွေစစ်ဆေးခြင်း
`develop` branch ပေါ်ရှိ လိုအပ်သော fix commit hash ကို အတိအကျ ရှာဖွေပါမည်။

```bash
git log develop --oneline -n 5
# ရလဒ်:
# a1b2c3d feat: wip payment gateway v2 (မယူရ)
# e4f5g6h feat: wip new dashboard layout (မယူရ)
# c8d7e6f fix: patch critical open-ssl vulnerability <--- ဤ commit တစ်ခုသာ လိုအပ်သည်!
```

---

### အဆင့် (၂) - Hotfix Branch ဆောက်ပြီး Cherry-Pick ပြုလုပ်ခြင်း

Production (`main`) မှ branch အသစ်ခွဲပြီး အဆိုပါ commit တစ်ခုတည်းကိုသာ ရွေးထုတ်ကူးယူ (Cherry-pick) ပါမည်။

```bash
# ၁။ Main branch ၏ နောက်ဆုံး အခြေအနေမှ hotfix branch ခွဲထွက်ပါ
git checkout main
git pull origin main
git checkout -b hotfix/cve-patch

# ၂။ CVE patch commit တစ်ခုတည်းကိုသာ ဆွဲယူပါ
git cherry-pick c8d7e6f
```

*ရလဒ် စစ်ဆေးရန်:*
```bash
git log --oneline -n 1
```
*ရလဒ်:* `fix: patch critical open-ssl vulnerability` ဟူသော commit တစ်ခုတည်းသာ `hotfix/cve-patch` ပေါ်သို့ ရောက်ရှိလာပြီး ကျန် WIP features များ လုံးဝ ပါမလာသည်ကို တွေ့ရပါမည်။

---

### အဆင့် (၃) - Cherry-Pick Conflict ဖြစ်ပေါ်ပါက ဖြေရှင်းနည်း (အကယ်၍ ဖြစ်ပေါ်ပါက)

အကယ်၍ ထို commit ပြင်ဆင်ခဲ့သော ဖိုင်သည် `main` တွင် ကွဲလွဲနေပါက Cherry-Pick pause ဖြစ်သွားပါမည်။

```bash
# ၁။ Status စစ်ဆေးပါ
git status

# ၂။ Conflict ဖြစ်နေသော ဖိုင်ကို ဖွင့်ပြင်ပြီး stage လုပ်ပါ
git add <conflict-file>

# ၃။ Cherry-pick ကို ဆက်လက် လုပ်ဆောင်ပါ
git cherry-pick --continue
```

---

### အဆင့် (၄) - Production သို့ ပေါင်းစပ်၍ Deploy ပြုလုပ်ခြင်း

```bash
# ၁။ Main branch သို့ ပြန်သွားပါ
git checkout main

# ၂။ Hotfix ကို ပေါင်းစပ်ပါ
git merge --no-ff hotfix/cve-patch -m "merge: emergency cve patch via cherry-pick"

# ၃။ Production သို့ push တင်ပါ (CI/CD မှ Production cluster ပေါ်သို့ ချက်ချင်း deploy လုပ်ပါမည်)
git push origin main

# ၄။ Patch Version Tag ထိုးပါ
git tag -a v2.1.1 -m "Hotfix release: OpenSSL CVE patch"
git push origin v2.1.1

# ၅။ Hotfix branch ကို ဖျက်ပါ
git branch -d hotfix/cve-patch
```

---

## 💡 DevOps Key Takeaways & Best Practices

1. **Selective Deployment:**
   - Multi-branch pipeline များတွင် branch တစ်ခုလုံးကို merge ရန် မဖြစ်နိုင်သော အရေးပေါ် အခြေအနေများတွင် `git cherry-pick` သည် အကောင်းဆုံး ဖြေရှင်းနည်း ဖြစ်ပါသည်။
2. **Duplicate Commits Awareness:**
   - Cherry-pick လုပ်လိုက်သောအခါ commit hash အသစ်တစ်ခု ထွက်ပေါ်လာမည်ဖြစ်သဖြင့် နောင်တစ်ချိန် `develop` ကို `main` သို့ merge သည့်အခါ Git သည် ပြင်ဆင်ချက်တူနေကြောင်း အလိုအလျောက် သိရှိပြီး conflict မဖြစ်အောင် ညှိပေးပါသည်။
3. **Keep Commits Atomic:**
   - Feature များနှင့် Bug fix များကို commit တစ်ခုတည်းတွင် ရောမရေးပါနှင့်။ Atomic Commit (အကြောင်းအရာ တစ်ခုလျှင် commit တစ်ခု) ခွဲရေးထားမှသာ လိုအပ်သည့် bug fix ကို အလွယ်တကူ cherry-pick လုပ်နိုင်မည် ဖြစ်ပါသည်။
