# Git Complete Cheatsheet & Guide 🚀

Git ကို ကျွမ်းကျင်စွာ အသုံးပြုနိုင်ရန်အတွက် **Merge Strategies**၊ **Branching**၊ **Time Travel** နှင့် **Rebase vs Merge** စသည့် အဓိက အယူအဆ (Concept) များကို ရှင်းလင်းချက်၊ ဥပမာ Command များ၊ Best Practices များနှင့်တကွ စနစ်တကျ စုစည်းဖော်ပြပေးထားပါသည်။

---

## မာတိကာ (Table of Contents)
1. [Merge Strategies အမျိုးအစားများ ကွာခြားချက်](#1-merge-strategies-အမျိုးအစားများ-ကွာခြားချက်)
2. [Git Branching Cheatsheet](#2-git-branching-cheatsheet)
3. [Git Time Travel (အတိတ်သို့ ပြန်သွားခြင်းနှင့် ပြင်ဆင်ခြင်း)](#3-git-time-travel-အတိတ်သို့-ပြန်သွားခြင်းနှင့်-ပြင်ဆင်ခြင်း)
4. [Git Rebase vs Merge](#4-git-rebase-vs-merge)
5. [Git Best Practices အကြံပြုချက်များ](#5-git-best-practices-အကြံပြုချက်များ)
6. [လက်တွေ့လေ့ကျင့်ရန် သင်ခန်းစာမှတ်စုများ (Hands-on Practice Notes)](#6-လက်တွေ့လေ့ကျင့်ရန်-သင်ခန်းစာမှတ်စုများ-hands-on-practice-notes)
7. [DevOps & Platform Engineering လက်တွေ့ Scenarios](#7-devops--platform-engineering-လက်တွေ့-scenarios)

---

## 1. Merge Strategies အမျိုးအစားများ ကွာခြားချက်

Git တွင် Branch များကို ပေါင်းစပ် (Merge) သည့်အခါ အဓိက Strategy (၃) မျိုးရှိပါသည်။

| Merge Strategy | လုပ်ဆောင်ပုံ | အားသာချက် | သင့်တော်သည့် နေရာ |
| :--- | :--- | :--- | :--- |
| **Fast-Forward Merge** | `main` branch တွင် commit အသစ်မရှိပါက pointer ကို ရှေ့သို့ တိုက်ရိုက်ရွှေ့ပေးခြင်း | History သန့်ရှင်းပြီး ဖြောင့်တန်းသည် (Linear) | Solo project သို့မဟုတ် Branch အသေးစားများ |
| **3-Way Merge (`--no-ff`)** | အပြောင်းအလဲများကို တွက်ချက်ပြီး Merge Commit အသစ်တစ်ခု ဖန်တီး၍ ပေါင်းစပ်ခြင်း | Feature တစ်ခုလုံး၏ စတင်ခြင်း/ပြီးဆုံးခြင်း သမိုင်းကြောင်းကို ရှင်းလင်းစွာ မြင်နိုင်သည် | Team project များ၊ Production/Release branches |
| **Squash Merge** | Feature branch ရှိ commit များစွာကို commit တစ်ခုတည်းအဖြစ် ပေါင်းစည်း၍ merge လုပ်ခြင်း | Main history ကို commit အသေးစားများ မရှုပ်ထွေးစေဘဲ သန့်ရှင်းစေသည် | Pull Requests (PRs), Feature-driven development |

### 💻 Example Commands

```bash
# 1. Fast-Forward Merge (Default အကယ်၍ main တွင် commit အသစ်မရှိပါက)
git checkout main
git merge feature-login

# 2. 3-Way Merge (Explicit Merge Commit အသစ် အမြဲဖန်တီးစေရန်)
git checkout main
git merge --no-ff feature-login -m "Merge feature-login into main"

# 3. Squash Merge (Commit အားလုံးကို တစ်ခုတည်းအဖြစ် ပေါင်းစည်းမည်)
git checkout main
git merge --squash feature-login
git commit -m "feat: complete user login authentication system"
```

> [!TIP]
> **ဘယ်အချိန်မှာ ဘာသုံးသင့်သလဲ?**
> - Feature branch ၏ commit တစ်ခုချင်းစီ၏ သမိုင်းကြောင်းအသေးစိတ် လိုအပ်ပါက `--no-ff` ကို သုံးပါ။
> - "WIP", "fix typo" စသည့် commit အသေးစားလေးများ မပါစေချင်ဘဲ feature အပြည့်အစုံကို commit တစ်ခုတည်းအဖြစ် main သို့ သွင်းချင်ပါက `--squash` ကို သုံးပါ။

---

## 2. Git Branching Cheatsheet

Branch များကို စနစ်တကျ ဖန်တီးခြင်း၊ ကူးပြောင်းခြင်း၊ စစ်ဆေးခြင်းနှင့် စီမံခန့်ခွဲခြင်းအတွက် အသုံးများသော Command များ ဖြစ်ပါသည်။

```bash
# Branch အားလုံးကို စစ်ဆေးကြည့်ရှုရန် (Local + Remote)
git branch -a

# လက်ရှိ ရောက်နေသော Branch ကို စစ်ဆေးရန်
git branch --show-current

# Branch အသစ် စတင်ဖန်တီးရန်
git branch feature/payment

# Branch အသစ် ဖန်တီးပြီး တစ်ခါတည်း switch လုပ်ရန် (Traditional)
git checkout -b feature/payment

# Branch အသစ် ဖန်တီးပြီး switch လုပ်ရန် (Modern Git recommended)
git switch -c feature/payment

# ရှိပြီးသား Branch သို့ ကူးပြောင်းရန်
git switch feature/payment
# သို့မဟုတ်
git checkout feature/payment

# Branch အမည် ပြောင်းလဲရန် (Rename)
git branch -m old-branch-name new-branch-name

# Branch အဟောင်းအား ဖျက်ပစ်ရန် (Merged ဖြစ်ပြီးသား Safe Delete)
git branch -d feature/payment

# Branch အား အတင်းအဓမ္မ ဖျက်ပစ်ရန် (Force Delete - မပေါင်းရသေးသည့် commit များရှိပါက)
git branch -D feature/payment

# Remote server ပေါ်ရှိ branch အား ဖျက်ပစ်ရန်
git push origin --delete feature/payment
```

---

## 3. Git Time Travel (အတိတ်သို့ ပြန်သွားခြင်းနှင့် ပြင်ဆင်ခြင်း)

အမှားများကို အရင် Commit အဟောင်းသို့ ပြန်သွား၍ ပြင်ဆင်ရန် သို့မဟုတ် မလိုလားအပ်သော အပြောင်းအလဲများကို ဖျက်ပစ်ရန် အသုံးပြုပါသည်။

### အဓိက ကွာခြားချက်များ
- `git checkout <hash>` / `git switch --detach <hash>`: မူလ Code အား ဖျက်ပစ်ခြင်းမရှိဘဲ ခေတ္တ အတိတ်သို့ သွားရောက်ကြည့်ရှုခြင်း (Read-only / Detached HEAD mode)။
- `git revert <hash>`: **အလွန်ဘေးကင်းသော နည်းလမ်းဖြစ်ပါသည်။** အရင် Commit အား ဖျက်မပစ်ဘဲ ၎င်း၏ ပြောင်းလဲမှုကို ပြန်ပြင်သည့် Commit အသစ်တစ်ခု ထပ်မံဖန်တီးပေးသည်။
- `git reset <hash>`: အတိတ်သို့ လုံးဝ ပြန်သွားခြင်းဖြစ်ပြီး သတိထားသုံးစွဲရန် လိုအပ်သည်။
  - `--soft`: Commit ကိုသာ ပယ်ဖျက်ပြီး ပြင်ဆင်ထားသော ဖိုင်များအား Staged Area တွင် ဆက်လက် ထိန်းသိမ်းထားသည်။
  - `--mixed` (Default): Commit နှင့် Staging Area ကို ပယ်ဖျက်ပြီး ပြင်ဆင်ထားသော ဖိုင်များကို Working Directory တွင် ဆက်လက် ထားရှိသည်။
  - `--hard`: Commit ကော ပြင်ဆင်ထားသမျှ အပြောင်းအလဲအားလုံးပါ လုံးဝ အပြီးအပိုင် ပယ်ဖျက်လိုက်သည်။

### 💻 Example Commands

```bash
# Commit History ကို တိုတိုတုတ်တုတ် ကြည့်ရှုရန်
git log --oneline -n 10

# 1. ခေတ္တ အတိတ်သို့ သွားရောက် စစ်ဆေးကြည့်ရှုခြင်း (Detached HEAD)
git checkout a1b2c3d
# ပြန်လည် ထွက်ခွာပြီး main သို့ ရောက်ရှိရန်
git switch main

# 2. အန္တရာယ်ကင်းစွာ ပြန်ပြင်သည့် Commit အသစ်ထုတ်ခြင်း (Revert)
# (Public / Shared Branch များတွင် အမြဲ Revert ကိုသာ အသုံးပြုသင့်သည်)
git revert a1b2c3d

# 3. Reset အသုံးပြုနည်းများ
# နောက်ဆုံး commit ၁ ခုကို ဖျက်မည်၊ သို့သော် ဖိုင်ပြင်ဆင်ချက်များကို Staged အဖြစ် ထားမည်
git reset --soft HEAD~1

# နောက်ဆုံး commit ၁ ခုကို ဖျက်မည်၊ ဖိုင်ပြင်ဆင်ချက်များကို Unstaged အဖြစ် ထားမည်
git reset --mixed HEAD~1

# Commit အဟောင်းများအားလုံးနှင့် ပြင်ဆင်ချက်များကို အပြီးအပိုင် ဖျက်ပစ်မည် (Hard Reset)
git reset --hard a1b2c3d
```

> [!WARNING]
> `git reset --hard` ကို Public/Shared branch များတွင် မသုံးသင့်ပါ။ သင်၏ အပြောင်းအလဲများကို အခြားသူများနှင့် မျှဝေထားပြီးပါက အမြဲ `git revert` ကိုသာ အသုံးပြုပါ။

> [!TIP]
> **အရေးပေါ် ကယ်တင်နည်း (`git reflog`)**:
> အကယ်၍ `git reset --hard` ကြောင့် အရေးကြီးသော Commit များ ပျောက်ဆုံးသွားခဲ့လျှင် Git သည် local history ကို မှတ်သားထားဆဲ ဖြစ်ပါသည်။
> ```bash
> git reflog
> # သင် လိုချင်သော commit hash ကို ရှာဖွေပြီး ပြန်လည်ရယူနိုင်ပါသည်
> git reset --hard HEAD@{1}
> ```

---

## 4. Git Rebase vs Merge

`Merge` နှင့် `Rebase` နှစ်မျိုးစလုံးသည် Branch နှစ်ခုကို ပေါင်းစပ်ရန် အသုံးပြုသော်လည်း Git History ပုံစံ လုံးဝ ကွာခြားသွားပါသည်။

| အချက်အလက် | Git Merge | Git Rebase |
| :--- | :--- | :--- |
| **History ပုံစံ** | Branch ခွဲများ ပါဝင်သော သဘာဝကျသည့် History (Non-linear) | လိုင်းတစ်လိုင်းတည်းဖြစ်သော သန့်ရှင်းသည့် History (Linear) |
| **Commit ပြောင်းလဲမှု** | Merge Commit အသစ်တစ်ခု ဖန်တီးသည်။ မူလ Commit များ မပြောင်းလဲပါ။ | Commit များကို Base အသစ်ပေါ်သို့ ပြန်လည် ရေးသားသည် (Re-write)။ |
| **Traceability** | Branch ဘယ်အချိန်ခွဲခဲ့ပြီး ဘယ်အချိန် ပြန်ပေါင်းခဲ့သည်ကို အတိအကျ ပြန်ကြည့်နိုင်သည်။ | Commit hash အသစ်များ ဖြစ်သွားသဖြင့် မူလ timeline အစစ်အမှန် မဟုတ်တော့ပါ။ |
| **အသုံးပြုရန် သင့်တော်သည့်နေရာ** | Public / Shared Branch များ (ဥပမာ- `main`, `staging`) | မိမိတစ်ဦးတည်း လုပ်ဆောင်နေသော Private Feature Branch များ |

### 💻 Workflow ပုံစံ ဥပမာများ

#### နည်းလမ်း (၁): Git Merge Workflow
```bash
git checkout main
git merge feature-branch
```
*ရလဒ်:* `main` နှင့် `feature-branch` တို့၏ သမိုင်းကြောင်းကို ထိန်းသိမ်းထားသည့် Merge Commit တစ်ခု ထွက်ပေါ်လာမည်။

#### နည်းလမ်း (၂): Git Rebase Workflow
```bash
# 1. Feature branch ပေါ်သို့ ပြောင်းပါ
git switch feature-branch

# 2. Main ၏ နောက်ဆုံး အပြောင်းအလဲများပေါ်သို့ feature commits များကို ပြန်လည် တင်ပါ
git rebase main

# (Conflict များ ရှိလာပါက ဖိုင်များကို ဖြေရှင်းပြီး အောက်ပါ command ကို သုံးပါ)
# git add <resolved-file>
# git rebase --continue
# (Rebase ကို ဖျက်သိမ်းပြီး မူလအခြေအနေ ပြန်သွားလိုပါက: git rebase --abort)

# 3. Main branch သို့ သွားပြီး Fast-Forward ဖြင့် လွယ်ကူစွာ ပေါင်းပါ
git switch main
git merge feature-branch
```

> [!IMPORTANT]
> **The Golden Rule of Rebase:**
> မည်သည့်အခါမျှ အများသုံး Public/Shared Branch (ဥပမာ- `main`, `master`, `develop`) ပေါ်တွင် `rebase` မလုပ်ပါနှင့်။ အခြား developer များနှင့် ပေါင်းစပ်လုပ်ဆောင်နေသော branch များတွင် commit history ကို rewrite လုပ်မိပါက merge conflict ကြီးမားစွာ ဖြစ်ပေါ်စေနိုင်ပါသည်။

---

## 5. Git Best Practices အကြံပြုချက်များ

1. **Commit မကြာခဏ ပြုလုပ်ပါ (Commit Early, Commit Often):** အပြောင်းအလဲ သေးငယ်ပြီး logical unit တစ်ခု ပြီးမြောက်တိုင်း commit ပြုလုပ်ပါ။
2. **ရှင်းလင်းသော Commit Message ရေးပါ:** `git commit -m "feat: implement JWT token refresh mechanism"` စသဖြင့် ဘာလုပ်ခဲ့သည်ကို တိကျစွာ ရေးသားပါ။
3. **Private Branch များတွင် Rebase သုံးပါ:** အခြားသူများနှင့် မပူးပေါင်းမီ မိမိ၏ local branch history ကို သန့်ရှင်းစေရန် rebase ပြုလုပ်ပါ။
4. **Shared/Main Branch များတွင် Merge သုံးပါ:** အဖွဲ့လိုက် သမိုင်းကြောင်းကို မပျောက်ပျက်စေရန် အလုံခြုံဆုံး နည်းလမ်း ဖြစ်ပါသည်။
5. **Pull မလုပ်မီ Status ကို စစ်ဆေးပါ:** `git status` နှင့် `git diff` တို့ကို အမြဲ အသုံးပြု၍ သေချာစစ်ဆေးပြီးမှ commit/push ပြုလုပ်ပါ။

---

## 6. လက်တွေ့လေ့ကျင့်ရန် သင်ခန်းစာမှတ်စုများ (Hands-on Practice Notes)

တစ်ဆင့်ချင်းစီ အသေးစိတ် လက်တွေ့ လေ့ကျင့်နိုင်ရန်အတွက် အောက်ပါ သီးသန့် သင်ခန်းစာ မှတ်စုများကို လေ့လာနိုင်ပါသည်-

- 🚀 **Lesson 1:** [Git Branching လက်တွေ့လေ့ကျင့်ခန်း](./notes/lesson-01-branching.md)
  - *Branch ဖန်တီးခြင်း၊ ကူးပြောင်းခြင်း၊ စစ်ဆေးခြင်းနှင့် ဖျက်ခြင်း*
- 🔀 **Lesson 2:** [Git Merge Strategies လက်တွေ့လေ့ကျင့်ခန်း](./notes/lesson-02-merge-strategies.md)
  - *Fast-Forward Merge, 3-Way Merge (`--no-ff`), Squash Merge*
- ⏪ **Lesson 3:** [Git Time Travel (Revert & Reset) လက်တွေ့လေ့ကျင့်ခန်း](./notes/lesson-03-time-travel.md)
  - *Git Revert, Reset (`--soft`, `--mixed`, `--hard`), `git reflog` ဖြင့် အရေးပေါ် ကယ်တင်ခြင်း*
- 🔀 **Lesson 4:** [Git Rebase vs Merge လက်တွေ့လေ့ကျင့်ခန်း](./notes/lesson-04-rebase-vs-merge.md)
  - *Linear History ဖန်တီးခြင်း၊ Rebase Conflicts ဖြေရှင်းခြင်း၊ The Golden Rule of Rebase*

---

## 7. DevOps & Platform Engineering လက်တွေ့ Scenarios

DevOps / Platform Engineer များ လုပ်ငန်းခွင်တွင် နေ့စဉ် ကြုံတွေ့ရလေ့ရှိသော အခြေအနေများအတွက် အထူးပြု လက်တွေ့လေ့လာရန် မှတ်စုများ-

- 🚨 **Scenario 1:** [Production Incident Hotfix & Safe Rollback](./notes/scenario-01-production-hotfix-rollback.md)
  - *GitOps / CI/CD ပျက်စီးချိန်တွင် `git revert` ဖြင့် Safe Production Rollback ပြုလုပ်ခြင်းနှင့် `hotfix/` branch အား `--no-ff` ဖြင့် Audit Trail မပျက်အောင် ပေါင်းစပ်ခြင်း*
- 🏗️ **Scenario 2:** [GitOps & Terraform Infrastructure PR Workflow](./notes/scenario-02-gitops-infra-pr-rebase-squash.md)
  - *Terraform / Helm module များအား `git rebase` ဖြင့် sync လုပ်ခြင်း၊ conflict ဖြေရှင်းခြင်းနှင့် Pull Request တွင် `Squash Merge` ဖြင့် သန့်ရှင်းသော Semantic Commit ထုတ်လုပ်ခြင်း*
- 🔐 **Scenario 3:** [Accidental Leaked Secret Removal from Remote Repo](./notes/scenario-03-leaked-secret-key-removal.md)
  - *လွန်ခဲ့သော Commit ၄ ခုတွင် ပေါက်ကြားသွားသော Secret Key / Credentials အား Interactive Rebase (`git rebase -i HEAD~5`) နှင့် `git-filter-repo` ဖြင့် Git History အစအဆုံးမှ လုံးဝ ဖျက်ထုတ်ပစ်ခြင်း*
- 🍒 **Scenario 4:** [Selective Hotfix Deployment with Git Cherry-Pick](./notes/scenario-04-selective-hotfix-cherry-pick.md)
  - *`develop` branch တွင် မပြီးသေးသော WIP features များကို ချန်ထားခဲ့ပြီး အရေးပေါ် CVE Patch commit တစ်ခုတည်းကိုသာ `git cherry-pick` ဖြင့် Production သို့ ဆွဲထုတ် deploy လုပ်ခြင်း*
- 🔍 **Scenario 5:** [Automated Incident Root Cause Analysis with Git Bisect](./notes/scenario-05-automated-debugging-git-bisect.md)
  - *Commit ပေါင်း ၄၀ ကြားတွင် ပျက်စီးသွားသော CI/CD build regression ကို `git bisect run` ဖြင့် ၅ ကြိမ်အတွင်း အလိုအလျောက် တိကျစွာ ရှာဖွေဖော်ထုတ်ခြင်း*


