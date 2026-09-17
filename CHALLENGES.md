# Git & GitHub Workshop: finishing the challenges

We ran out of time in the room after round 1. The rest works exactly the same way, from anywhere, on your own schedule.

**How it works:** open **ccgit.vercel.app**. Your screen shows your team's current round with the exact commands. Every minute the site checks your team's GitHub repo; when the round is complete, it sets up the next round in your repo and your screen moves on. Nobody has to press anything.

**Teammates not around?** Every round can be done by one person. Do all the parts yourself, one branch each.

**Can't get in?** If the site asks you to check in again, use the **same entry number and the same GitHub username** as in the room and you land back in your team.

---

## 0. Setup (once per laptop)

Terminal: Windows → **Git Bash** from the Start menu (not PowerShell). Mac → Terminal.

```
git --version
```
No version → Windows: https://git-scm.com/download/win, Next on everything. Mac: click Install on the popup.

```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

**Accept the invitation.** Open your team repo (the link on your screen). Yellow banner at the top → **Accept**. No banner → the bell icon top right. Without this every `git push` fails with 403.

**Clone it** (once):
```
git clone https://github.com/CodingClub-IITD-AD/coding-club-pool-XXXX.git
cd coding-club-pool-XXXX
git status
```

**Signing in on the first push.** Windows: a browser window opens, sign in, Authorize, done. Mac: the terminal asks for a username and password; **GitHub does not accept your password here.** Make a token: github.com → your photo → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token → tick `repo` → copy it → paste it as the password (nothing shows while pasting). Anything else failing: repo page → green **Code** → **Codespaces** → Create. That's VS Code in the browser, already signed in.

---

## Round 1 · Spacecraft repair (branches + pull requests)

`mission_control.json` has engine, shields, communications OFFLINE. One system each (your screen says which). Alone: do all three, one branch each.

```
git switch main
git pull
git switch -c repair-engine
```
Branch names must be **exactly** `repair-engine`, `repair-shields`, `repair-comms`.

Open `mission_control.json`, change only your line `"OFFLINE"` → `"ONLINE"`. Keep every `{ } " ,` as it is. Save.

```
git diff
git add mission_control.json
git commit -m "Bring engine online"
git push -u origin repair-engine
```
Git prints a link ending `/pull/new/repair-engine`. Open it (or the yellow **Compare & pull request** button on the repo page) → **Create pull request** → **Merge pull request** → **Confirm merge**.

Then: `git switch main && git pull`.

**Complete when** all three systems are ONLINE on main and the three `repair-*` pull requests are merged. The site moves your team to round 2 within a minute.

**Common problems**
- 403 on push → invitation not accepted.
- Edited while on `main` → `git switch -c repair-engine` now, the edit comes along.
- Pushed a wrong branch name → `git switch -c repair-engine`, push that, open the PR from it.
- File on main is broken (a missing `{`) → someone deleted a character; fix it on a branch, PR, merge. Git merged exactly what was written.
- The site didn't move on → check the repo page: all three lines ONLINE? all three PRs merged with the exact names?

## Round 2 · The escape route (a conflict, on purpose)

Two people change the **same line** differently. Your screen gives roles: planner A (ROOFTOP), planner B (TUNNEL), Commander. Alone: play all three.

Both planners, from the same main:
```
git switch main && git pull
git switch -c route-rooftop            # B: route-tunnel
```
Change `"escape_route": "UNDECIDED"` → `"ROOFTOP"` (B: `"TUNNEL"`). Save.
```
git add mission_control.json
git commit -m "Choose escape route"
git push -u origin route-rooftop       # B: route-tunnel
```
Both open pull requests. **Merge rooftop first.** The tunnel PR now says *This branch has conflicts that must be resolved*. Git is not broken: two humans disagreed, git refuses to guess.

**Commander:** on the tunnel PR → **Resolve conflicts**. Delete the `<<<<<<<`, `=======`, `>>>>>>>` lines and the ROOFTOP line, keep `"escape_route": "TUNNEL",` → **Mark as resolved** → **Commit merge** → **Merge pull request**. The rooftop was unsafe.

**Complete when** main says TUNNEL and both `route-*` PRs are merged. The site then commits a secret into your repo and moves you to round 3.

## Round 3 · The time machine (reading history)

The site added a file `challenge/mission_access.env` and then replaced its code with `REMOVED`. It is still in the history.

```
git switch main
git pull
cat challenge/mission_access.env       # REMOVED
git log --oneline                      # find "Store temporary mission admin key"
git show <that commit id>              # the code is right there
```
Type the code into the box on your screen. One correct answer moves the team on. Nothing to commit.

## Round 4 · Mad Lib (the whole loop, alone)

On your screen: type an adjective, a noun, a verb → **Download** your file (`madlib/submissions/<yourusername>.json`). Put it in your clone's `madlib/submissions/` folder. Then, on your own:

```
git switch main && git pull
git switch -c madlib-yourusername
git add madlib/submissions/yourusername.json
git commit -m "Add my Mad Lib words"
git push -u origin madlib-yourusername
```
Pull request → Merge. Only your own file in your PR.

**Complete when** at least one credited file is on main. The projector page then writes your team's story with your names.

## Round 5 · Command cards

On your screen: a repo state and a hand of full git commands. Play the right one; the picture moves. Three rounds. One teammate finishing completes the team.

---

## Check you're done

`git log --oneline` on main: your three repair merges, two route merges, the secret's two commits, your Mad Lib merges. That log is the whole workshop.

## Cheat sheet

| I want to… | Command |
|---|---|
| see what's going on | `git status` |
| get the team's latest main | `git switch main && git pull` |
| start my own lane | `git switch -c name` |
| see what I changed | `git diff` |
| pick what goes in the save point | `git add file` |
| take the save point | `git commit -m "what changed"` |
| send my lane to GitHub | `git push -u origin name` |
| see the history | `git log --oneline` |
| open one save point | `git show <id>` |
| which lane am I on | `git branch` |

Stuck? Screenshot the terminal into the Coding Club group. The error text is the useful part.
