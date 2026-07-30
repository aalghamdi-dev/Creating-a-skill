# Testing 3 Skills: What Happens When We Use Them

## Test 1: `pdf` Skill

**Input given:** "Here is a 5 page PDF report. Pull out the text and the one table inside it."

**What the skill does:**
- Opens the file
- Reads each page
- Finds the table and turns it into rows and columns
- Saves the text in a plain file, and the table in a new small file (like a mini spreadsheet)

**Output:**
- A text file with all words from the 5 pages
- A table with 3 columns: `Name`, `Cost`, `Date`
- Time to finish: fast (few seconds), since it's just code running on the file

**How it acts:** Quiet and simple. It just does the job on the file. No back and forth talk needed.

---

## Test 2: `prompt-engineering` Skill

**Input given:** "My prompt is: 'write me something about dogs.' Help me make it better."

**What the skill does:**
- Looks at the weak prompt
- Adds missing pieces: what kind of writing, how long, what tone
- Gives 2-3 better versions to pick from

**Output:**
> "Write a 200 word fun story about a dog who gets lost in a big city and finds its way home. Use a warm, hopeful tone. End with the dog reunited with its owner."

**How it acts:** Talks a lot. Explains *why* each change helps. Feels like getting advice from a person, not a machine doing file work.

---

## Test 3: `webapp-testing` Skill

**Input given:** "I built a login page. Please check if it works."

**What the skill does:**
- Opens the web page like a real user
- Types a fake username and password
- Clicks the login button
- Checks if the next page loads
- Tries a wrong password too, to see if it shows an error

**Output:**
- Login with right info: ✅ works, goes to home page
- Login with wrong info: ✅ shows "wrong password" message
- One small bug found: page takes 3 seconds to load (a bit slow)

**How it acts:** Very active. It "clicks" and "types" like a person testing an app. Takes longer than the other two because it must wait for pages to load.

---

## Side by Side Compare

| | `pdf` | `prompt-engineering` | `webapp-testing` |
|---|---|---|---|
| What it touches | A file | Words / text | A live app |
| Speed | Very fast | Fast | Slower (waits on pages) |
| Talks a lot? | No, just does the job | Yes, gives tips | A little, reports what it found |
| Needs the app running? | No | No | Yes |
| Main output | New file (text, table, etc.) | Better prompt text | Pass/fail list + bugs found |

---

## Simple Takeaway

- `pdf` is like a worker with a machine: put a file in, get a new file out.
- `prompt-engineering` is like a coach: it talks with you and gives better wording.
- `webapp-testing` is like a tester poking a real app: it clicks around and tells you what broke.

Each one is built for a very different kind of job, so they act nothing alike.
