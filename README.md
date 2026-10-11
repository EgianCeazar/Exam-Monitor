# Exam Monitor

A simple anti-cheat helper for Google Form exams. It **detects and logs** when a student leaves the exam window, and shows everything live on a teacher dashboard.

> **Important:** a web page can't block other apps. This tool *detects* and *reports*. It does not lock the computer. Treat flags as a reason to review, not as proof of cheating.

## What it does

- Wraps a Google Form in a page that watches for tab switches and window changes (opening Word, PDFs, notes, other websites, ChatGPT, a second monitor, etc.)
- Shows the student a warning each time and counts it
- Teacher dashboard updates instantly, beeps, and sends a browser notification on each violation
- Flags students who reach 3 violations (changeable)
- Download the full log as a CSV file
- No accounts, no scripts, no spreadsheets, no passwords to set up

## How it works

```
Student  ->  exam.html  --(events)-->  ntfy.sh  -->  dashboard.html  <-  Teacher
```

`exam.html` holds the Google Form inside it (a "wrapper") and watches the window. Each event is sent to a private, random session ID on [ntfy.sh](https://ntfy.sh), a free public message service. The dashboard listens to the same session ID and shows events live.

## Files

| File | What it is |
|------|-----------|
| `index.html` | Home page that links to the setup page |
| `setup.html` | Paste your Form link, get the student and teacher links |
| `exam.html` | The page students open. Embeds the Form and detects violations |
| `dashboard.html` | The teacher's live monitor |

## Setup (about 2 minutes)

### 1. Make the Google Form
1. Go to [forms.google.com](https://forms.google.com) and create your exam.
2. Add a required **Full name** question as the first question.
3. Click **Publish**, then click the **link icon** to copy the responder link.

### 2. Open the setup page
**Option A: Use the published website**
Go to [https://peppy-klepon-14bb1b.netlify.app/setup.html](https://examonitor.netlify.app/)

**Option B: Host it yourself**
- **GitHub Pages:** push this repo, go to **Settings > Pages**, pick `main` and `/ (root)`, then **Save**. Your setup page is at `https://USERNAME.github.io/REPO-NAME/setup.html`
- **Netlify:** drag the folder into [app.netlify.com/drop](https://app.netlify.com/drop). Your setup page is at `https://yoursite.netlify.app/setup.html`

### 3. Generate your links
1. Paste your Form link and click **Generate my links**.
2. Copy the **student link** (share this) and open the **teacher link** (keep this private).

### 4. Test it
1. Open the **teacher link**, then click **Enable alerts**.
2. Open the **student link** in a private window and enter a fake name.
3. Switch tabs. A warning should appear, and the dashboard should beep and show 1 violation right away.

## Using it on exam day

1. Generate fresh links for each exam. A new link means a clean, empty session.
2. Open the dashboard before the exam and keep the tab open.
3. Share **only** the student link. Don't share the raw Form link.
4. Tell students to type their name the same way in the popup and in the Form.
5. After the exam, click **Download CSV** to save the log.

## Settings

| What | Where | How |
|------|-------|-----|
| Flag limit | `dashboard.html` | Change `const FLAG_AT = 3;` and the "Flagged (3+)" label |
| Warning text | `exam.html` | Edit the text inside the `#warn` card |
| Message server | `exam.html` and `dashboard.html` | Change `const NTFY = "https://ntfy.sh";` to your own [ntfy server](https://docs.ntfy.sh/install/) |

## Troubleshooting

- **Dashboard says "Reconnecting...":** check your internet. If your school network blocks ntfy.sh, use your own ntfy server (see Settings).
- **No beep:** click **Enable alerts** and make sure the tab isn't muted.
- **Dashboard is empty after a refresh:** open the teacher link again. Messages are kept on the server for about 12 hours, so very old exams won't reload. Download the CSV after each exam.
- **Form is blank inside the student page:** turn off "Limit to 1 response" in the Form settings.
- **"Exam link is not set up correctly":** the student opened the page without the generated link. Give them the student link from `setup.html`.

## Limitations

- It detects, it doesn't block. Students can still open other things, but it gets logged.
- It can't see a second device, like a phone.
- It can only see whether the exam window is active. It can't see inside the Form.
- Students could open the raw Form link and skip the monitor page. Only share the student link.
- The student link contains the session ID, so a technical student could read or send fake events. Treat the log as a flag, not proof.
- The public ntfy.sh server has per-network rate limits. If a whole class shares one network, test it first with several devices, or use your own ntfy server.
- Student names pass through the public ntfy.sh server and are kept for about 12 hours. If that matters for your class, use student IDs, or self-host ntfy.
- For high-stakes exams, combine it with [Safe Exam Browser](https://safeexambrowser.org) or a proctor.

## Tech used

HTML, CSS, JavaScript, Google Forms, ntfy (server-sent events)
