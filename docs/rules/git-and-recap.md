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

## Commit message format (enforced)

5. Messages follow [Conventional Commits](https://www.conventionalcommits.org/): `type(scope)!: description`
   - **type**: `feat` `fix` `docs` `style` `refactor` `perf` `test` `build` `ci` `chore` `revert`
   - **scope** (optional, lowercase): e.g. `engine`, `server`, `client`, `steps`
   - **!** (optional): marks a breaking change
   - **description**: imperative, lowercase start ("add", not "added"); the whole first line is **≤ 100 characters**
   - Examples: `docs: add step 04 on latency`, `feat(engine): add limit order matching`
6. Checked by `.githooks/commit-msg` on every commit (the commit is refused if the message is wrong), and again by the **Commit messages** GitHub Actions workflow on every push.
7. One-time setup after cloning: `git config core.hooksPath .githooks`
