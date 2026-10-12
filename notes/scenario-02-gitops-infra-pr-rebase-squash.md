# 🏗️ Scenario 2: GitOps & Terraform Infrastructure PR Workflow (Rebase & Squash Merge)

## 🏢 Scenario Background (လက်တွေ့ အခြေအနေ)
သင်သည် Cloud & Platform Engineering Team တွင် လုပ်ကိုင်နေပြီး အဖွဲ့သားများနှင့်အတူ **Terraform (Infrastructure as Code)** နှင့် **Helm / Kubernetes Manifests** များကို Central Repository တစ်ခုတည်းတွင် ပူးပေါင်း ရေးသားနေသည်။
အဖွဲ့တွင် **Trunk-Based Development** နှင့် **Automated Terraform CI/CD (Atlantis / GitHub Actions)** ကို အသုံးပြုထားပြီး PR Merge လုပ်သည်နှင့် `terraform apply` ကို အလိုအလျောက် run စေသည်။

---

## 💥 The Challenge (ကြုံတွေ့ရသော စိန်ခေါ်မှု)
1. သင်သည် AWS EKS Kubernetes Cluster အသစ် ဆောက်လုပ်ရန် `feat/eks-cluster-v1.28` branch ခွဲပြီး ရက်အနည်းငယ်ကြာ လုပ်ဆောင်နေသည်။
2. ထိုအတောအတွင်း အခြား Platform Engineer တစ်ဦးက `main` branch ပေါ်တွင် Base IAM Roles နှင့် Security Groups မွမ်းမံမှုများကို Merge လုပ်လိုက်သည်။ သင်၏ feature branch သည် နောက်ကျကျန်ရစ်ခဲ့သည် (Out of sync)။
3. သင်၏ local feature branch တွင် "wip terraform", "fix typo in main.tf", "debug iam policy", "format terraform" စသည့် commit အသေးစား (၁၀) ခုကျော် ဖြစ်ပေါ်နေသည်။
4. **ပြဿနာ:** ဤအတိုင်း `main` သို့ တိုက်ရိုက် Merge လုပ်လိုက်ပါက:
   - Commit history များ ရှုပ်ပွသွားမည်။
   - Terraform State ပြောင်းလဲမှုများကို Audit လုပ်ရန် ခက်ခဲသွားမည်။
   - Base infrastructure အပြောင်းအလဲများနှင့် မကိုက်ညီတော့သဖြင့် CI/CD တွင် `terraform plan` fail ဖြစ်သွားနိုင်သည်။

---

## 🛠️ Step-by-Step Resolution (အဆင့်ဆင့် ဖြေရှင်းပုံ)

### အဆင့် (၁) - Feature Branch ကို Main နှင့် Rebase ပြုလုပ်၍ Synchronize လုပ်ခြင်း

မိမိ၏ Feature branch ကို `main` ၏ နောက်ဆုံး infrastructure code များပေါ်သို့ Base ပြောင်းတင်ရန် `git rebase` ကို အသုံးပြုပါမည်။

```bash
# ၁။ Remote main ၏ နောက်ဆုံး အပြောင်းအလဲများကို fetch လုပ်ပါ
git checkout main
git pull origin main

# ၂။ မိမိ၏ feature branch ပေါ်သို့ ကူးပြောင်းပါ
git checkout feat/eks-cluster-v1.28

# ၃။ Main ၏ အပေါ်သို့ rebase လုပ်ပါ
git rebase main
```

> [!TIP]
> Rebase လုပ်လိုက်ခြင်းကြောင့် `main` ပေါ်ရှိ အသစ်ရောက်လာသော IAM / Security Group code များကို သင်၏ feature branch က ချက်ချင်း ရရှိသွားပြီး Terraform module conflict များကို သင်၏ Local စက်ထဲတွင် ကြိုတင် ဖြေရှင်းနိုင်စေပါသည်။

---

### အဆင့် (၂) - Rebase Conflict များကို ဖြေရှင်းခြင်း (အကယ်၍ ဖြစ်ပေါ်ပါက)

အကယ်၍ `main.tf` တွင် နှစ်ဦးစလုံး တစ်ပြိုင်နက် ပြင်ဆင်မိထားပါက Rebase သည် ခေတ္တ ရပ်တန့်သွားပါမည်။

```bash
# ၁။ Conflict ဖြစ်နေသော ဖိုင်ကို စစ်ဆေးပါ
git status
# (ဥပမာ- both modified: modules/eks/main.tf)

# ၂။ ဖိုင်ကို ဖွင့်ပြီး မှန်ကန်သော configuration ကို ရွေးချယ် ပြင်ဆင်ပါ
# (Terraform validation စစ်ဆေးပါ: terraform validate)

# ၃။ ပြင်ဆင်ပြီးသော ဖိုင်ကို stage လုပ်ပါ
git add modules/eks/main.tf

# ၄။ Rebase ကို ဆက်လက် ဆောင်ရွက်ပါ
git rebase --continue
```

---

### အဆင့် (၃) - Feature Branch ကို Remote PR သို့ Push ပြုလုပ်ခြင်း

Rebase လုပ်ပြီးသောအခါ commit hashes များ ပြောင်းလဲသွားသဖြင့် remote branch သို့ ပုံမှန် push လုပ်၍ မရနိုင်ပါ။ ဘေးကင်းလုံခြုံသော `--force-with-lease` ကို အသုံးပြုရပါမည်။

```bash
# အခြားသူ ရေးထားသော code များကို မတော်တဆ overwrite မဖြစ်စေရန် safe force push သုံးပါ
git push --force-with-lease origin feat/eks-cluster-v1.28
```

---

### အဆင့် (၄) - Squash Merge ဖြင့် Main သို့ သန့်ရှင်းစွာ ပေါင်းထည့်ခြင်း

Pull Request (PR) တွင် Review အောင်မြင်ပြီး CI Test များ (`terraform fmt`, `terraform plan`) Pass ဖြစ်သွားသောအခါ Commit အသေးစား ၁၀ ခုလုံးကို Commit အသစ် **တစ်ခုတည်း** အဖြစ် ပေါင်းစည်း၍ Merge ပြုလုပ်ပါမည်။

#### (က) GitHub / GitLab PR UI မှ ပြုလုပ်ခြင်း:
- PR တွင် **"Squash and merge"** option ကို ရွေးချယ်ပြီး Semantic Commit Message တစ်ခုသာ သတ်မှတ်ပေးပါ:
  `feat(terraform): provision AWS EKS cluster v1.28 with security baseline (#105)`

#### (ခ) CLI မှ တိုက်ရိုက် ပြုလုပ်ခြင်း:
```bash
git checkout main
git pull origin main

# Squash Merge ပြုလုပ်ပါ
git merge --squash feat/eks-cluster-v1.28

# သန့်ရှင်းသော Semantic Commit Message ဖြင့် commit တင်ပါ
git commit -m "feat(terraform): provision AWS EKS cluster v1.28 with security baseline"

# Push တင်ပါ (Automated CI/CD မှ terraform apply စတင်ပါမည်)
git push origin main

# အလုပ်ပြီးသော branch ကို ဖျက်ပါ
git branch -d feat/eks-cluster-v1.28
git push origin --delete feat/eks-cluster-v1.28
```

---

## 💡 DevOps Key Takeaways & Best Practices

1. **Clean Terraform State & Audit Trail:**
   - Terraform / Infrastructure code များတွင် `git log` ကို ကြည့်ခြင်းဖြင့် ဘယ် resource ကို ဘယ် commit က ဖန်တီးခဲ့သည်ကို တစ်ချက်တည်း မြင်နိုင်ရပါမည်။ Squash Merge သည် commit ရှုပ်ထွေးမှုများကို ကာကွယ်ပေးသည်။
2. **Rebase Before PR:**
   - PR မဖွင့်မီ သို့မဟုတ် Review မယူမီ `git rebase main` အမြဲ လုပ်ထားခြင်းဖြင့် CI/CD Pipeline တွင် `terraform plan` conflict မတက်ဘဲ အဆင်ပြေချောမွေ့စွာ run နိုင်စေပါသည်။
3. **Always use `--force-with-lease`:**
   - Rebase ပြီးနောက် `git push -f` အစား `git push --force-with-lease` ကိုသာ အမြဲ အသုံးပြုပါ။ ၎င်းသည် အဖွဲ့သားတစ်ဦးဦးက ထို branch ပေါ်သို့ commit အသစ် တင်ထားပါက သတိပေးတားဆီးပေးသော safety feature ဖြစ်ပါသည်။
