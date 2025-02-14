![image](https://github.com/user-attachments/assets/999f9e9a-ffe3-4374-b22c-b0c652576e1b)## Topics to be Covered

- **Git Remotes**
  - Push Command
  - Fetch Command
  - Pull Command
- **GitHub**
  - Forking a Repository
  - Making a Pull Request (PR)
- **Git Cherry-Picking**
---

## Git Remotes

### Introduction to Git Remotes

A **Git Remote** is essentially a **publicly accessible copy** of a Git repository that includes the entire codebase along with its version history. This remote repository serves as a centralized location where multiple developers can collaborate on the same project, ensuring that all team members can access the latest version of the project and contribute their changes.

### Push Command

#### Scenario: Collaborative Development

Imagine you are working on a project where several developers contribute code. For example, **Developer 1** and **Developer 2** are both working on different features of the same project. Developer 2 has completed their feature and needs to share their code with Developer 1 and the rest of the team.

![Developer's Local Work](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/151/original/2.png?1725097900)

To facilitate this, the project is hosted on a **centralized server** (e.g., a GitHub repository) that acts as a shared space for the codebase. The server ensures that everyone on the team is working with the most up-to-date code.

#### How Git Handles Code Pushes

To push code from your local machine to the centralized server, you need to add a remote repository and then push your changes. The command to add a remote is:

```bash
git remote add "origin" [email protected]:User/UserRepo.git
```

Here’s what happens when you execute this command:

- **Git** sets up a link between your local repository and the remote repository, creating a **read-only version** of the repository's history on your local machine. This means you can view the history of the remote repository but cannot directly make changes to it.
- The branches in the remote repository are now accessible with a prefix indicating the remote name. For example, if your remote is named `origin`, the branches will appear as `origin/main`, `origin/dev`, etc.
- These **read-only branches** are protected, meaning you cannot commit changes directly to them.

So, how do you work on this code?

- Git allows you to create a **writable copy** of a branch from the remote repository on your local machine. This writable branch is where you can make changes and commit new code.
- Once your work is done, you can **push** these changes back to the remote repository so that they are available to others.

![Push to Remote](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/152/original/3.png?1725097924)
*Left: Developer's local environment; Right: Changes being pushed to the remote repository.*


### Clone Command

When a developer joins a project for the first time, they need to download the entire project from the centralized server. This is achieved using the **clone command**.

#### Workflow of the Clone Command

The **clone command** essentially does two things:

1. **Creates a Read-Only Copy of the Version History:** The entire history of the project is downloaded, allowing the developer to access all past changes.
2. **Creates a Writable Copy of the Default Branch:** Typically, this is the `main` branch. This writable copy is where the developer will make their contributions.

The clone command ensures that the developer has everything they need to start working on the project immediately.

![Cloning the Repository](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/154/original/4.png?1725097963)

**Important Concepts:**

- **Read-Only Branches:** If a developer tries to check out a specific commit from a read-only branch using `git checkout origin/c8`, they won’t be able to make changes to that commit. However, this action will update the writable part by incorporating the changes from `c8` into the current branch.

### Fetch and Pull Commands

#### Scenario: Synchronizing Changes

Consider a scenario where both **Developer 1** and **Developer 2** have cloned the same repository and are working on their respective features. Developer 1 completes some changes and pushes them to the remote repository.

![Fetch and Pull Scenario](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/155/original/5.png?1725097985)

Now, Developer 2 needs to ensure that their local repository is up to date with the latest changes made by Developer 1.

**Question for Learners:** Will Developer 2’s local repository automatically reflect the changes pushed by Developer 1?
**Answer:** No, it won’t. Developer 2’s repository will only reflect the latest changes after they run specific commands to synchronize their local copy with the remote repository.

#### Git Commands to Synchronize Changes

1. **Fetch Command:** The `fetch` command is used to update the **read-only copy** of the remote branches on your local machine. This means it pulls the latest changes from the remote repository but doesn’t update your working branch (the writable part).
   
   - Example: Running `git fetch origin` will update the local references for `origin/main`, `origin/dev`, etc., but will not merge these changes into your current branch.

2. **Pull Command:** The `pull` command is more comprehensive. It not only updates the read-only branches but also merges the changes from the remote repository into your current branch. This ensures that your local branch is fully synchronized with the remote repository.

   - Example: Running `git pull origin main` will fetch the latest changes from `origin/main` and automatically merge them into your current branch.

Reference: [Github link](https://github.com/Naman-Bhalla/git_class_12_feb)

---


### **Steps to Create a New Branch from `UAT`, Make Changes, and Merge It Back in a Production Environment**  

---

### **1️⃣ Switch to the `UAT` Branch & Ensure It's Up-to-Date**  
Before creating a new branch, update your local copy of `UAT`.  
```sh
git checkout UAT   # Switch to UAT branch
git pull origin UAT  # Get the latest changes from remote
```
✅ Ensures you're working with the latest `UAT` code.

---

### **2️⃣ Create a New Branch for Your Changes**  
```sh
git checkout -b feature-branch  # Create and switch to a new branch
```
✅ The `feature-branch` is now based on the latest `UAT`.

---

### **3️⃣ Make Changes & Commit Them**  
Modify your files and then commit the changes.  
```sh
git add .  # Stage all changes
git commit -m "Added new feature to feature-branch"
```
✅ Saves your changes locally.

---

### **4️⃣ Push the New Branch to Remote (Optional, for Collaboration)**  
```sh
git push -u origin feature-branch  # Uploads the new branch to remote
```
✅ This allows others to review your changes.

---

### **5️⃣ Switch Back to `UAT` & Pull Latest Changes**  
Before merging, ensure `UAT` has the latest updates.  
```sh
git checkout UAT  # Switch back to UAT
git pull origin UAT  # Get latest changes from remote
```
✅ Ensures no conflicts when merging.

---

### **6️⃣ Merge `feature-branch` into `UAT`**  
```sh
git merge feature-branch  # Merge changes into UAT
```
✅ If there are **no conflicts**, the merge completes successfully.  
🚨 If conflicts occur, resolve them manually and run:
```sh
git add .  # Stage resolved files
git commit -m "Resolved merge conflicts"
```

---

### **7️⃣ Push Merged Changes to Remote `UAT`**  
```sh
git push origin UAT
```
✅ Now the merged changes are in the remote `UAT` branch.

---

### **8️⃣ (Optional) Delete the Feature Branch**  
Once merged, the feature branch is no longer needed.  
```sh
git branch -d feature-branch  # Delete locally
git push origin --delete feature-branch  # Delete from remote
```
✅ Cleans up unused branches.

---

### **Summary of Commands**
```sh
git checkout UAT
git pull origin UAT
git checkout -b feature-branch
# Make changes
git add .
git commit -m "Added new feature"
git push -u origin feature-branch
git checkout UAT
git pull origin UAT
git merge feature-branch
git push origin UAT
git branch -d feature-branch
git push origin --delete feature-branch
```

✅ **Best Practices**
- **Always pull before branching and merging** (`git pull origin <branch>`).  
- **Use `git status` to check changes before committing.**  
- **Resolve conflicts carefully during merging.**  

Would you like steps for handling merge conflicts in detail? 🚀


-------------------------------------


### **Real-World Example: `git rebase` vs `git revert` vs `git restore`**  

Let's say you're working on a **feature branch (`feature-branch`)** based on `main`.  

---

## **1️⃣ Scenario: Using `git rebase` to Keep a Clean History**  
**Problem:**  
Your feature branch has **diverged** from `main`, and you want to bring it up to date before merging.  

### **Before Rebase**
```
main:        A --- B --- C
                 \
feature-branch:   D --- E --- F
```

### **Solution: Rebase the Feature Branch onto `main`**
```sh
git checkout feature-branch
git rebase main
```

### **After Rebase**
```
main:        A --- B --- C
                          \
feature-branch:            D' --- E' --- F'
```
✅ **All commits (`D`, `E`, `F`) are now based on the latest `main`**  
✅ **History looks linear, making merging easier**  

🚨 **Warning:** If your feature branch is shared with others, **do not use `rebase`** because it rewrites commit history.

---

## **2️⃣ Scenario: Using `git revert` to Undo a Specific Commit**
**Problem:**  
You committed a bug (`commit F`) that broke production. Instead of deleting history, you want to create a **new commit that undoes it**.

### **Before Revert**
```
main: A --- B --- C --- D --- E --- F (Bug)
```

### **Solution: Revert `F`**
```sh
git revert F
```

### **After Revert**
```
main: A --- B --- C --- D --- E --- F --- G (Revert F)
```
✅ **Commit `F` is not removed**, but `G` is created to undo its effects  
✅ **Safe for shared branches**  

---

## **3️⃣ Scenario: Using `git restore` to Discard Uncommitted Changes**
**Problem:**  
You modified `file.txt` but want to **undo changes before committing**.

### **Solution: Restore the File**
```sh
git restore file.txt
```
✅ **Resets `file.txt` back to the last committed version**  

---

## **🚀 Summary**
| Command | Use Case | Safe for Shared Branches? | Removes History? |
|---------|---------|-----------------|----------------|
| **`git rebase`** | Rewrite history, clean commit structure | ❌ No | ✅ Yes |
| **`git revert`** | Undo a specific commit with a new commit | ✅ Yes | ❌ No |
| **`git restore`** | Discard uncommitted changes | ✅ Yes | ❌ No |

---

Would you like to see **how to handle merge conflicts during rebase**? 🚀


---------------------------

### **Handling Merge Conflicts During `git rebase` (Practical Example)**  

---

### **🛠 Scenario: Rebase with a Conflict**
You are working on a **feature branch (`feature-branch`)** based on `main`.  
However, while you were working, **someone else modified the same file in `main`**.  
Now, when you try to **rebase your branch onto `main`**, Git detects a **merge conflict**.

---

### **1️⃣ Initial State Before Rebase**
Your **Git history looks like this:**
```
main:        A --- B --- C
                 \
feature-branch:   D --- E --- F
```
- **Commit `C` (on `main`) modifies `file.txt`.**
- **Commit `D` (on `feature-branch`) also modifies `file.txt`.**

---

### **2️⃣ Start the Rebase**
Now, let's rebase `feature-branch` onto `main`:
```sh
git checkout feature-branch
git rebase main
```
✅ Git starts replaying `D`, `E`, and `F` **on top of `main`**.

---

### **3️⃣ Merge Conflict Occurs**
```
main:        A --- B --- C
                          \
feature-branch:            D' (Conflict!)
```
- Git tries to apply `D` on top of `C` but detects that **`file.txt` was changed in both branches**, causing a **conflict**.

Git will show an error:
```
CONFLICT (content): Merge conflict in file.txt
error: could not apply D...
```

---

### **4️⃣ Resolve the Conflict Manually**
1️⃣ Open the conflicted file (`file.txt`):
```txt
<<<<<<< HEAD (Current state from `main`)
Code from commit C
=======
Code from commit D
>>>>>>> feature-branch
```
2️⃣ **Manually edit the file** to keep the correct version:
```txt
Final version that includes both changes.
```
3️⃣ **Mark the conflict as resolved**:
```sh
git add file.txt
```

---

### **5️⃣ Continue the Rebase**
After resolving the conflict:
```sh
git rebase --continue
```
✅ Git continues replaying the remaining commits (`E`, `F`).  
✅ If more conflicts occur, repeat the **resolve → add → continue** process.

---

### **6️⃣ If You Want to Abort the Rebase**
If you want to **cancel the rebase** and return to the previous state:
```sh
git rebase --abort
```
✅ This **stops** the rebase and restores `feature-branch` to its original state.

---

### **7️⃣ Successfully Completed Rebase**
After resolving all conflicts, Git history is now **linear**:
```
main:        A --- B --- C
                          \
feature-branch:            D' --- E' --- F'
```
Now, **push the rebased branch**:
```sh
git push origin feature-branch --force
```
✅ `--force` is needed because rebase **rewrites history**.

---

## **🚀 Summary of Commands**
| Command | Purpose |
|---------|---------|
| `git rebase main` | Start rebase onto `main` |
| `git status` | Check conflicts |
| `git add <file>` | Mark conflict as resolved |
| `git rebase --continue` | Continue rebase after resolving |
| `git rebase --abort` | Cancel rebase if things go wrong |
| `git push origin feature-branch --force` | Push after rebasing |

---

## **✅ When to Use Rebase vs. Merge?**
| Feature | `git rebase` | `git merge` |
|---------|-------------|-------------|
| **History** | Linear & Clean | Preserves full commit history |
| **Usage** | Before merging a feature branch | Merging into shared branches |
| **Conflicts** | Must be resolved manually for each commit | All conflicts handled at once |

---

### **Would you like a hands-on exercise to practice this? 🚀**



---------------------------------------------------------

# **`git stash` - Real-World Example**  

### **🔹 Scenario: You Need to Switch Branches Without Losing Changes**
Imagine you're working on **feature-branch**, and you have **uncommitted changes** in your files.  
Suddenly, your manager asks you to **fix a critical bug on `main`**.  

✅ You **can’t commit** unfinished work.  
✅ You **can’t switch branches** without committing or discarding changes.  
✅ **Solution:** Use `git stash` to **temporarily save your changes**.

---

## **1️⃣ Before Using `git stash`**
Let's say you're working on `feature-branch` and made changes to `file.txt`:
```sh
git status
```
```
On branch feature-branch
Changes not staged for commit:
    modified: file.txt
```
🚨 **Problem:** If you try to switch to `main`, Git will block you:
```
error: Your local changes to the following files would be overwritten by checkout:
        file.txt
Please commit your changes or stash them before switching branches.
```

---

## **2️⃣ Stash Your Changes**
Use:
```sh
git stash
```
✅ This **saves your changes temporarily** and resets `file.txt` to its last committed state.  

Check your stash:
```sh
git stash list
```
```
stash@{0}: WIP on feature-branch: f3a2b1d Added new logic
```

---

## **3️⃣ Switch Branches & Work on `main`**
Now, you can **switch to `main` and fix the bug**:
```sh
git checkout main
git pull origin main  # Get the latest changes
# Fix the bug, commit & push
```

---

## **4️⃣ Return to `feature-branch` and Restore Your Work**
After fixing the bug, switch back:
```sh
git checkout feature-branch
git stash pop  # Apply stashed changes & remove from stash
```
✅ **Your changes are restored** and removed from the stash list.

---

## **5️⃣ (Optional) If You Want to Keep Stashed Changes**
If you want to **apply changes but keep them in the stash**, use:
```sh
git stash apply
```
✅ This **restores changes but does NOT delete the stash**.

To remove a specific stash manually:
```sh
git stash drop stash@{0}
```

---

## **6️⃣ (Optional) If You Need to Recover a Deleted Stash**
If you accidentally deleted a stash and need to recover it:
```sh
git fsck --lost-found
git show <stash-hash>  # Find lost stash and recover it
```

---

## **🚀 Summary of Commands**
| Command | Purpose |
|---------|---------|
| `git stash` | Stash uncommitted changes |
| `git stash list` | View saved stashes |
| `git stash pop` | Apply & remove the latest stash |
| `git stash apply` | Apply stash but keep it saved |
| `git stash drop stash@{0}` | Remove a specific stash |
| `git stash clear` | Delete all stashes |

---

## **✅ When to Use `git stash`?**
✔ **Switching branches temporarily** without committing work.  
✔ **Saving work in progress** to test something else.  
✔ **Keeping a clean commit history** without unnecessary commits.  

---

### **Would you like a scenario on `git stash apply` vs. `git stash pop`? 🚀**




## GitHub

### Forking a Repository

**Forking** is a critical concept in open-source development, where you might want to contribute to a popular project like **Linux**. Organizations like Google or Microsoft might create their own copies (forks) of Linux to tailor it to their needs.

![Forking Example](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/156/original/6.png?1725098014)

#### Understanding Forking

Here’s how forking works:

1. **Remote of the Original Project:** On your local machine, you might add a remote that points to the original Linux repository. This ensures that you can stay updated with any new changes made to the project.
   
2. **Your Working Copy:** Your local repository will have its own branches, like `main`, where you can make your changes.
   
3. **Limitations on Direct Changes:** Since the original Linux repository is owned by someone else, you **cannot** directly push changes to their `main` branch.

4. **Creating a Fork:** To contribute, you create a fork of the Linux repository in your GitHub account. This fork is a personal copy of the entire repository, where you have full control.

![Forking Process](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/157/original/7.png?1725098052)

### Making Your First Pull Request (PR)

Once you have forked a repository, you will clone your fork to your local machine to start working. However, to keep your fork updated with the latest changes from the original repository, you need to add an additional remote pointing to the original repository.

![Forking and PR Workflow](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/160/original/8.png?1725098072)

#### Workflow: Making a Pull Request

1. **Clone Your Fork:** After forking the repository, clone it to your local machine. This gives you a working copy of the project where you can make changes.

2. **Add an Upstream Remote:** Add a remote that points to the original repository, often named `upstream`. This remote allows you to fetch the latest changes from the original project.

   ```bash
   git remote add upstream https://github.com/original_user/original_repo.git
   ```

3. **Make Your Changes:** Work on the project, make improvements, fix bugs, or add features.

4. **Create a Pull Request (PR):** Once your changes are ready, push them to your fork and then go to GitHub to create a **Pull Request**. In this PR, you are asking the maintainers of the original repository to review and potentially merge your changes.

   - **Source Branch:** The branch in your fork where you made the changes.
   - **Target Branch:** The branch in the original repository where you want your changes to be merged, often the `main` branch.

![PR Process - Step 1](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/161/original/9.png?1725098106)
![PR Process - Step 2](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/088/162/original/10.png?1725098124)

