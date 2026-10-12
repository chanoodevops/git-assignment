# 🔐 Scenario 3: Accidental Leaked Secret Removal from Remote Repo (DevOps / Platform)

## 🏢 Scenario Background (လက်တွေ့ အခြေအနေ)
Developer သို့မဟုတ် Platform Engineer တစ်ဦးသည် Terraform သို့မဟုတ် Cloud Provider SDK ဖြင့် စမ်းသပ်ရင်း Production AWS Access Key / Database Password ပါဝင်သော `.env` သို့မဟုတ် `credentials.json` ဖိုင်ကို မတော်တဆ git commit လုပ်မိခဲ့သည်။ ထိုသို့ commit ပြုလုပ်ပြီးနောက် commit အသစ် (၄) ခု ထပ်မံပြုလုပ်ခဲ့ပြီး Remote GitHub/GitLab Repository သို့လည်း `git push` တင်ပြီးဖြစ်သွားသည်။

---

## 💥 The Incident (ကြုံတွေ့ရသော ပြဿနာ)
Security Team သို့မဟုတ် Git Secret Scanner (GitGuardian / GitHub Secret Scanning) မှ Alert တက်လာသည်-
> **CRITICAL SECURITY ALERT:** Production AWS Secret Access Key leaked in commit `HEAD~4` on remote `main` branch!

ဖိုင်အသစ်တစ်ခုဆောက်ပြီး သော့ကို ဖျက်လိုက်ရုံ (Delete & Commit) ဖြင့် မလုံလောက်ပါ။ အဘယ်ကြောင့်ဆိုသော် **Git History ထဲတွင် အဆိုပါ commit ဟောင်းသည် အမြဲ ကျန်ရစ်နေမည်** ဖြစ်ပြီး မည်သူမဆို commit log ကို ကြည့်ရှု၍ Key ကို ခိုးယူနိုင်သောကြောင့် ဖြစ်သည်။

---

## 🚨 အရေးပေါ် ပထမဆုံး လုပ်ဆောင်ရမည့်အဆင့် (Step 0: Key Rotation)

> [!CAUTION]
> **အလွန်အရေးကြီးပါသည်:** Git History ကို မရှင်းလင်းမီ **ပေါက်ကြားသွားသော Secret Key / Password ကို AWS Console / Cloud Provider ထဲတွင် ချက်ချင်း REVOKE (Deactivate / Delete) ပြုလုပ်ပြီး Key အသစ် လဲလှယ် (Rotate) ရပါမည်!**
> အဘယ်ကြောင့်ဆိုသော် Key သည် Remote သို့ ရောက်ရှိပြီးဖြစ်၍ တိုက်ခိုက်သူများ ရယူပြီး ဖြစ်နိုင်သောကြောင့် ဖြစ်သည်။

---

## 🛠️ Step-by-Step Resolution (Git History မှ လုံးဝ အပြီးတိုင် ဖျက်ထုတ်ပုံ)

### နည်းလမ်း (က) - Interactive Rebase ဖြင့် Commit ၄ ခု အကွာမှ Secret ဖျက်ထုတ်ခြင်း (အသေးစား အပြောင်းအလဲများအတွက်)

လက်ရှိ HEAD မှ ၄ ခုအကွာ (HEAD~4) ဖြစ်သောကြောင့် လွန်ခဲ့သော ၅ ခုမြောက် commit သို့ Interactive Rebase ပြုလုပ်ပါမည်။

```bash
# ၁။ လွန်ခဲ့သော commit ၅ ခုကို Interactive Rebase ခေါ်ပါ
git rebase -i HEAD~5
```

Terminal တွင် Editor (Vim သို့မဟုတ် Nano) ပွင့်လာပါမည်:
```text
pick a1b2c3d feat: add initial terraform config
pick e4f5g6h feat: add aws credentials (SECRET LEAK HERE!) <--- ဤလိုင်းကို ပြင်မည်
pick 7h8i9j0 feat: update vpc module
pick 1k2l3m4 feat: add security groups
pick 5n6o7p8 feat: configure load balancer
```

#### ပြင်ဆင်ပုံ:
Secret ပါနေသော commit (`e4f5g6h`) ၏ ရှေ့မှ `pick` ကို `edit` (သို့မဟုတ် `e`) ဟု ပြောင်းပြီး Save & Exit လုပ်ပါ:
```text
edit e4f5g6h feat: add aws credentials (SECRET LEAK HERE!)
```

Git သည် ထို commit နေရာတွင် ခေတ္တ ရပ်တန့်ပေးမည်ဖြစ်သည်:
```bash
# ၂။ Secret ပါနေသော ဖိုင်ကို ဖျက်ပစ်ပါ (သို့မဟုတ် သော့နေရာတွင် environment variable ပြောင်းပါ)
rm credentials.json
# (သို့မဟုတ်: git rm --cached credentials.json)

# ၃။ .gitignore ထဲသို့ ထည့်သွင်းပါ
echo "credentials.json" >> .gitignore
echo "*.env" >> .gitignore
git add .gitignore

# ၄။ Commit ကို ပြန်လည်ပြင်ဆင်ပါ (Amend)
git commit --amend --no-edit

# ၅။ ကျန်ရှိသော commit များကို ဆက်လက် run စေပါ
git rebase --continue
```

---

### နည်းလမ်း (ခ) - `git-filter-repo` (သို့မဟုတ် BFG Repo-Cleaner) ဖြင့် History တစ်ခုလုံးမှ အပြီးတိုင် ချေမှုန်းခြင်း (Recommended for Enterprise)

အကယ်၍ commit ဘယ်နှစ်ခုမှန်း မသေချာလျှင် သို့မဟုတ် branch များစွာတွင် ပျံ့နှံ့နေပါက Git မှ တရားဝင် အကြံပြုထားသော `git-filter-repo` ကို အသုံးပြုနိုင်ပါသည်။

```bash
# ၁။ git-filter-repo ကို install လုပ်ပါ (Python tool)
pip install git-filter-repo

# ၂။ credentials.json ဖိုင်ကို git history အစအဆုံးမှ လုံးဝ ဖျက်ထုတ်ပစ်ပါ
git filter-repo --path credentials.json --invert-paths
```

---

### အဆင့် (နောက်ဆုံး) - Remote Repository သို့ လုံခြုံစွာ Force Push လုပ်ခြင်း

Local history တွင် Secret လုံးဝ ပျောက်ကွယ်သွားပြီ ဖြစ်သဖြင့် Remote repo သို့ Force Push လုပ်ရပါမည်။

```bash
# Remote သို့ Force with Lease ဖြင့် Push တင်ပါ
git push --force-with-lease origin main
```

*စစ်ဆေးရန်:*
```bash
# Secret ပါမပါ ရှာဖွေစစ်ဆေးပါ
git log -S "AKIA" --all
```
*ရလဒ်:* မည်သည့် commit history တွင်မျှ Secret Key ရှာမတွေ့တော့ရပါမည်။

---

## 🛡️ DevOps / Platform Best Practices (နောင်တွင် မဖြစ်စေရန် ကာကွယ်နည်း)

1. **Pre-commit Hooks (Talisman / detect-secrets / ggshield):**
   - Developer စက်ထဲတွင် commit မတင်မီ Secret များကို ကြိုတင် စစ်ဆေးတားဆီးသည့် `pre-commit` hook သွင်းထားပါ။
   - ဥပမာ: `pre-commit install`
2. **CI/CD Pipeline Secret Scanning:**
   - GitHub Secret Scanning သို့မဟုတ် TruffleHog ကို CI pipeline တွင် ထည့်သွင်းထားပါ။
3. **Never store static credentials in Git:**
   - AWS IAM Roles for Service Accounts (IRSA), HashiCorp Vault သို့မဟုတ် Cloud Secrets Manager များကိုသာ အသုံးပြုပါ။
