# Git & Recaps

## Only the student uses git

1. AI agents **never** run git commands that change the repository or the remote: no `git add`, `git commit`, `git push`, `git merge`, `git rebase`, `git reset`, `git tag`, or branch changes. **The student does all of that.**
2. Read-only commands (`git status`, `git diff`, `git log`) are fine for understanding the current state.

## Recap after every change

3. After creating or changing any file, finish with a **short recap**: 2–5 plain sentences on what was done, what changed, and why.
4. Then suggest the git commands for the student to run:

   ```bash
   git add <changed paths>
   git commit -m "docs: short summary of the change"
   ```

5. Commit message style: a prefix (`docs:`, `feat:`, `fix:`, `test:`, `chore:`, `refactor:`), then an imperative summary under ~72 characters, e.g. `docs: add step 04 on latency and data structures`.
