# Git & GitHub Workshop: the challenges

Finish these with your team, at your own pace. Everything runs on your own laptop plus your team's GitHub repo. Nothing else is needed.

Your team repo is the link on ccgit.vercel.app (open the site, you're still logged in) or in the attendance list. It looks like `https://github.com/CodingClub-IITD-AD/coding-club-pool-…`.

---

## 0. Setup (once)

**Git installed?** Open a terminal (Windows: **Git Bash** from the Start menu, not PowerShell. Mac: Terminal) and type:

```
git --version
```

No version → Windows: https://git-scm.com/download/win, run the installer, Next on everything. Mac: click "Install" when the popup appears.

**Tell git who you are (once per laptop):**

```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

**Accept the invitation.** Open your team repo on GitHub. There is a yellow banner at the top: *You've been invited to collaborate* → **Accept**. No banner? Click the bell icon (top right) → the invitation is there. Without this, every `git push` fails with 403.

**Clone it:**

```
git clone https://github.com/CodingClub-IITD-AD/coding-club-pool-XXXX.git
cd coding-club-pool-XXXX
git status
```

`git status` should say `On branch main … up to date with 'origin/main'`.

**Signing in on push.** The first `git push` asks you to sign in.
- Windows: a browser window opens → sign in → Authorize. Done forever.
- Mac: the terminal asks for a username and password. **GitHub does not accept your account password here.** Make a token: github.com → your photo → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token → tick `repo` → Generate → copy it. Paste the token where it asks for the password (nothing appears while you paste, that's normal).
- Anything else failing: on the repo page click the green **Code** button → **Codespaces** → **Create codespace on main**. That's VS Code in the browser with git already signed in. Do everything there.

---

## 1. Spacecraft repair (branches and pull requests)

`mission_control.json` in your repo:

```json
{
  "engine": "OFFLINE",
  "shields": "OFFLINE",
  "communications": "OFFLINE",
  "escape_route": "UNDECIDED"
}
```

Three systems, three people. Decide who takes which: **engine**, **shields**, **communications**. Each person, on their own laptop:

```
git switch main
git pull
git switch -c repair-engine
```

Branch names must be exactly: `repair-engine`, `repair-shields`, `repair-comms`.

Open `mission_control.json` in any editor. Change **only your line**: `"OFFLINE"` → `"ONLINE"`. Keep every `{`, `}`, `"` and `,` exactly as they are. Save.

```
git diff
git add mission_control.json
git commit -m "Bring engine online"
git push -u origin repair-engine
```

Git prints a link ending in `/pull/new/repair-engine`. Open it (or go to the repo page: there's a yellow **Compare & pull request** button). **Create pull request** → **Merge pull request** → **Confirm merge**.

Then everyone:

```
git switch main
git pull
cat mission_control.json
```

**Done when** all three lines say ONLINE on main and you each merged your own pull request.

What you learned: a branch is your own lane. Nobody pushes to `main` directly (it is locked). The pull request is how work gets in, and someone can read the change before it does. Three people changed three different lines, so all three merged without a fight.

**It broke:**
- `push` rejected / 403 → you never accepted the invitation. Accept it, push again.
- You edited the file while still on `main` → run `git switch -c repair-engine` now; the edit comes with you. Carry on.
- You pushed with the wrong branch name → `git switch -c repair-engine`, `git push -u origin repair-engine`, open the pull request from that one. The old branch can stay.
- The file on main looks broken (a missing `{`) → someone deleted a character. Fix it on a branch, pull request, merge. Git merged exactly what was written; that's the point.

---

## 2. The escape route (a merge conflict, on purpose)

Same file, same line, two people, different answers. Roles: **A** and **B** edit, **C** is Mission Commander and decides.

Everyone first:

```
git switch main
git pull
```

**A:**
```
git switch -c route-rooftop
```
change `"escape_route": "UNDECIDED"` → `"escape_route": "ROOFTOP"`, save.
```
git add mission_control.json
git commit -m "Choose the rooftop route"
git push -u origin route-rooftop
```

**B, at the same time, from the same main:**
```
git switch -c route-tunnel
```
change the same line to `"TUNNEL"`, save.
```
git add mission_control.json
git commit -m "Choose the tunnel route"
git push -u origin route-tunnel
```

**A** opens a pull request and merges it. Main now says ROOFTOP.

**B** opens a pull request. GitHub says: **This branch has conflicts that must be resolved.** Git is not broken. Two humans changed the same line differently, and git refuses to guess.

**C (Mission Commander)** resolves it: on B's pull request click **Resolve conflicts**. You see:

```
<<<<<<< route-tunnel
  "escape_route": "TUNNEL",
=======
  "escape_route": "ROOFTOP",
>>>>>>> main
```

The rooftop is unsafe. Delete the marker lines and the ROOFTOP line so only `"escape_route": "TUNNEL",` remains. **Mark as resolved** → **Commit merge** → **Merge pull request**.

Everyone: `git switch main && git pull`. Main says TUNNEL.

**Done when** `escape_route` is TUNNEL on main and both pull requests are merged.

---

## 3. The time machine (reading history)

Something was committed, then removed. It is still in the history. One teammate hides it, the other two find it.

**The hider:**
```
git switch main && git pull
git switch -c mission-key
```
Create a file `challenge/mission_access.env` containing:
```
# Synthetic workshop value; never a real credential.
MISSION_ADMIN_KEY=ORBIT-7731
```
(pick your own code). Then:
```
git add challenge/mission_access.env
git commit -m "Store temporary mission admin key"
```
Now edit the file: replace the code with `REMOVED`. Then:
```
git add challenge/mission_access.env
git commit -m "Remove temporary mission admin key from current config"
git push -u origin mission-key
```
Open the pull request, merge it. Main now has the file with `REMOVED` in it, and two commits behind it.

**The finders:**
```
git switch main
git pull
cat challenge/mission_access.env        # says REMOVED
git log --oneline                        # find "Store temporary mission admin key", note its id
git show <that id>                       # the old contents are right there
```

Tell the hider the code. No commit needed.

**Done when** both finders read the code out of `git show`.

What you learned: nothing committed is gone. `git log` lists every save point; `git show` opens one.

---

## 4. Mad Lib (the whole loop, solo)

Each of you adds one file, alone, using everything above.

Create `madlib/submissions/<your-github-username>.json` (lowercase username):

```json
{
  "contributor": {"name": "Your Name", "github_username": "yourusername"},
  "words": {"adjective": "sleepy", "noun": "toaster", "verb": "juggles"}
}
```

Branch → add → commit → push → pull request → merge. Branch name: `madlib-<yourusername>`. Only your own file in your pull request.

**Done when** all three files are on main. If the workshop site is on the Mad Lib stage, it reads them and writes your team's story on the projector with your names.

---

## 5. Check you're done

On main, after `git pull`:

```
git log --oneline
```

You should see your three repair merges, the two route merges (one with a conflict resolution), the mission-key merge, and three Mad Lib merges. That log is the whole workshop.

```
git branch
```

lists every branch you made. They can stay.

---

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

Stuck? Message the Coding Club group with a screenshot of the terminal. The error text is the useful part.
