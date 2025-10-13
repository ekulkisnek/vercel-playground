# Agent Notes for vercel-playground

These notes are for any automation/agent starting work on this repo. Read this first to ensure consistent deployments to Vercel.

## Project context
- Repo: https://github.com/ekulkisnek/vercel-playground
- Vercel project: https://vercel.com/lukes-projects-fe2e76bf/vercel-playground
- Production URL: https://vercel-playground.vercel.app
- teamId: team_WuBeQ3hxIx8UTXBYNadA0wjO
- projectId: prj_5Fl7lyRCUIVMXlWu6tbV7r2u7qEi
- Stable image URL (reference only): https://raw.githubusercontent.com/ekulkisnek/vercel-playground/main/images/trump-dog.png

## Purpose
Ensure the homepage renders the local image reliably in production and provide a repeatable deploy workflow.

## Agent checklist (do this on start)
1. Open `index.html` and confirm it renders the local image:
   - Expected: `<img src="/images/trump-dog.png" ... />`
   - A clickable caption/link to `/images/trump-dog.png` should be present.
   - Optionally include a separate visible link to the stable GitHub image for reference.
2. Do not use remote raw GitHub URLs as the `<img src>`; use the local `/images/trump-dog.png` path to avoid hotlinking or CORS/CDN flakiness.
3. Keep `vercel.json` headers intact for predictable caching.
4. When changes are made, push, open a PR to `main`, and merge to deploy.

## Standard change flow
- Branch naming: prefer `cursor/*` or `agent/*` prefixes, e.g. `agent/add-agent-notes`.
- Commit messages: focus on the "why" in 1–2 sentences.

### Commands
```bash
# Show status and recent history
git status -s
git log --oneline -n 10

# Stage and commit
git add -A
git commit -m "$(cat <<'EOF'
Add AGENT_NOTES.md and document deployment flow

Provides agent startup checklist and Vercel deployment instructions for consistency.
EOF
)"

# Push branch (replace with your branch name)
git push -u origin $(git rev-parse --abbrev-ref HEAD)

# Open PR to main (requires GitHub CLI auth)
gh pr create \
  --title "docs: add AGENT_NOTES.md for agent workflow" \
  --body "$(cat <<'EOF'
## Summary
- Add AGENT_NOTES.md with agent checklist and Vercel deploy steps
- Ensure homepage uses /images/trump-dog.png and provides direct/stable links

## Test plan
- Open index.html locally; verify image and links render
- Merge to main and confirm production at https://vercel-playground.vercel.app
EOF
)" \
  --base main

# Merge PR (optionally auto-merge if checks exist)
# Replace <PR_URL_OR_NUMBER> if not in PR context
gh pr merge --merge --delete-branch --auto <PR_URL_OR_NUMBER>
```

## Direct production deploy (optional)
If you need to deploy without a PR, use Vercel CLI with a token (preferred to keep PR flow for auditability):

```bash
export VERCEL_ORG_ID=team_WuBeQ3hxIx8UTXBYNadA0wjO
export VERCEL_PROJECT_ID=prj_5Fl7lyRCUIVMXlWu6tbV7r2u7qEi
# Requires VERCEL_TOKEN to be set in the environment
vercel --prod --confirm --token "$VERCEL_TOKEN"
```

## Expectations for homepage
- Title: "Vercel Playground".
- Heading shows a variant of "Hello, Vercel!".
- Local image renders from `/images/trump-dog.png` with an accessible `alt`.
- Under the image, provide:
  - A visible direct link to `/images/trump-dog.png`.
  - A separate visible link to the stable GitHub image URL (reference only).

## Do/Don't
- Do: keep edits minimal and rationale-focused.
- Do: prefer local assets and relative paths for images.
- Don't: run long-lived processes.
- Don't: change repo/Vercel settings unless requested.

## Troubleshooting
- If production still shows an old version, ensure the branch merged into `main` and that the Vercel production deployment succeeded. Re-run a production deploy if needed (see direct deploy commands above).
