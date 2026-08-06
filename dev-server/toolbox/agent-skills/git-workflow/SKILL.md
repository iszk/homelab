---
name: git-workflow
description: Git and GitHub workflow for Codex. Use when the user asks to commit changes, create a branch, inspect git status, push a branch, create or update a pull request, prepare PR descriptions, or otherwise manage repository Git/GitHub workflow using the user's preferred conventions.
---

# Git Workflow

Use this skill to handle Git and GitHub workflow for the user.

## Core Rules

- Speak Japanese by default.
- Keep the workflow explicit and concise.
- default branch では作業しない。
- default branch にいる場合は topic branch を作成してから作業する。
- default branch 以外にいる場合は、その branch で作業してよい。
- Before making Git state changes, inspect the current repository state.
- Never discard or overwrite user changes unless the user explicitly asks.
- `git config` を勝手に変更しない。変更が必要な場合は、理由と変更内容を説明してユーザーに確認する。
- Stage only files relevant to the current task. `git add .` は使わない。
- `--force` や `--force-with-lease` は、ユーザー確認なしで使わない。
- Prefer non-interactive Git commands.
- If authentication or permission fails, stop and report the required permission.

## Request Interpretation

Treat short requests as follows:

- `commit して`: inspect changes, confirm the intended commit scope when unclear, stage relevant files, and create a Conventional Commit.
- `push して`: check the current branch and push it to `origin`.
- `PR つくって`: ensure changes are committed and pushed, then create a pull request targeting the default branch.
- `commit して push して PR つくって`: perform the full branch-to-PR workflow after verification.

Better request forms:

- `この変更を feat(scope): 説明 で commit して`
- `今の branch を push して`
- `main 向けに PR を作って。未対応事項は X と書いて`
- `この差分だけ commit/push/PR して`

## Workflow

1. Inspect state with `git status --short --branch`.
2. Inspect relevant diffs before staging.
3. Identify the repository default branch.
4. If on the default branch, create a topic branch before making changes or committing.
5. If already on a non-default branch, continue on the current branch unless the user asks otherwise.
6. Choose branch names by intent:
   - Feature: `feature/short-description`
   - Bug fix: `fix/short-description`
   - Refactor/chore: `chore/short-description`
7. Run relevant verification before commit.
8. Review the commit scope before staging.
9. Stage only relevant files. Do not use `git add .`.
10. Before committing, self-review the staged diff with `git diff --cached`:
    - Confirm the staged changes match the requested intent.
    - Check for unintended edits, secrets, generated noise, or unrelated formatting churn.
    - Confirm verification results and note any skipped checks.
    - If the review finds a concrete issue, fix it before committing.
11. Commit with Conventional Commits:
   - Format: `type(scope): 日本語の説明`
   - Keep the description concise and in Japanese.
12. Push with `git push origin <branch-name>`.
13. Create a PR to the repository default branch.

## Commit Safety

- 差分が複数の目的に分かれている場合は、勝手にまとめず、commit の分け方をユーザーに確認する。
- untracked file がある場合は、必要なものだけを確認して stage する。
- secret、credential、`.env` 系ファイル、token、password、private key が差分に含まれていないか確認する。
- commit 対象が不明確な場合は、対象ファイルと意図をユーザーに確認する。

## Verification

- For TypeScript changes, run `npm run typecheck` and targeted tests.
- Run `npm test` when the change has broad impact or no targeted test is obvious.
- Do not use Docker or Docker Compose for ordinary development verification.
- If required services or credentials are unavailable, report what was skipped and why.
- test、typecheck、lint などの検証が失敗した場合は、原則 commit しない。
- ユーザーが明示的に続行を指示した場合だけ、失敗内容を commit message または PR description に記録して進める。

## Push

- 通常は `git push origin <branch-name>` を使う。
- upstream が未設定の場合は `git push -u origin <branch-name>` を使う。
- `--force` や `--force-with-lease` が必要に見える場合は、理由、対象 branch、想定される影響を説明してユーザーに確認する。
- push が認証や権限で失敗した場合は、追加の変更をせず、エラーの要点と必要な権限を報告する。

## Pull Request

Use the primary commit message as the PR title.

Write the PR description in Japanese and include:

- どのような変更が行われたか
- なぜその変更が行われたか
- 重要な実装上の詳細
- 今回あえて対応しなかった内容

If GitHub MCP tools are available, use them for PR creation. If GitHub MCP tools are unavailable, fall back to `gh`.

Fallback order:

1. GitHub MCP tools
2. `gh pr create`
3. If neither is available, stop and provide the PR title, PR body, base branch, and head branch for the user to create manually.

## Safety

- If there are unrelated user changes, leave them untouched.
- If unrelated changes are in files that must be edited, inspect carefully and work around them.
- If a merge or rebase conflict is obvious, resolve it; if unsure, stop and ask.
- If GitHub returns `401` or `403`, report that `Contents: Write` and `Pull Requests: Write` may be required.
