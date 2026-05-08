Deploy the latest index.html from the working directory to the edge-journal repo, then commit and push to GitHub so Vercel picks it up.

Steps:
1. Copy `C:\Users\Kory\Projects\index.html` to `C:\projects\edge-journal\index.html`, overwriting it.
2. Change into `C:\projects\edge-journal`.
3. Run `git diff --stat` to show what changed (line count diff is fine).
4. Run `git add index.html`.
5. Write a concise commit message that describes what changed in this deploy. If the user passed arguments via `$ARGUMENTS`, use that as the commit message. Otherwise, infer the message from the diff (e.g. "Update hero particle animation", "Add terminal aesthetic CSS", "Fix trade log bug").
6. Commit and push to `origin main`.
7. Report the commit hash and confirm: "Deployed — Vercel will be live in ~30 seconds."

Do not push any file other than `index.html` unless the user explicitly asks.
