# Documentation — CMITS

**Contributor Management and Issue Tracking System**
A plain-language guide to every function and section in `main.py`, including the full code for each.

---

## Overview

CMITS is a Python program that runs in the terminal. It asks you questions, collects your answers, organizes the data, and saves everything into files. There are no buttons or screens — just typed responses.

The program is built from **3 helper functions**, **2 data-collection functions**, and **4 sections** of logic that process and save everything.

---

## Shared Variables

Before any functions are defined, two reusable separator lines are created. These are just visual dividers used throughout the program when printing to the terminal — a long line of `=` signs and a long line of `-` signs.

```python
line = 70 * "="
dash = 70 * "-"
```

---

## Helper Functions

These are small, reusable tools built to solve one specific problem: making sure bad data never gets into the system. They are called repeatedly throughout the program whenever user input is needed.

---

### `get_validated_input(prompt, valid_options)`

```python
def get_validated_input(prompt, valid_options):
    while True:
        try:
            raw = input(prompt).strip()
            formatted = raw.title()

            if formatted not in valid_options:
                raise ValueError(f"Invalid input. Please choose from: {valid_options}")

            return formatted
        except ValueError as e:
            print(f"Error: {e}")
```

**What it does:**
Asks the user a question and only accepts one of the pre-approved answers. It strips extra spaces from what the user typed, normalizes the capitalization (so "bug" and "BUG" are both treated as "Bug"), then checks if the answer is in the allowed list. If it isn't, it shows an error and loops back to ask again — it never moves forward with a wrong answer.

**Why it was created:**
Some fields only make sense with specific values. For example, an issue's priority can only be "Critical", "High", "Medium", or "Low" — anything else would break the system's analysis. This function enforces that rule in one place so it doesn't have to be rewritten every time.

**Used for:** Issue type (Bug/Feature), priority level (Critical/High/Medium/Low), and issue status (Open/In Progress/Resolved).

---

### `get_validated_alpha(prompt, field_name)`

```python
def get_validated_alpha(prompt, field_name):
    while True:
        value = input(prompt).strip()
        if value and value.replace(' ', '').isalpha():
            return value.title()
        print(f'  [!] {field_name} cannot be empty or contain numbers/special characters. Try again.')
```

**What it does:**
Asks the user for a text answer — like a name, role, or country — and rejects anything that contains numbers or special characters. It also rejects blank answers. Spaces are allowed (so "New Zealand" or "Team Lead" work fine). If the input passes, it's returned with proper capitalization (e.g., "jane doe" becomes "Jane Doe").

**Why it was created:**
Names and roles should only contain letters. Allowing numbers or symbols in a contributor's name field (e.g., "Dev123" or "!Admin") would produce messy, unprofessional reports. This function keeps all text fields clean automatically.

**Used for:** Contributor name, role, language, country, and issue title and reporter.

---

### `get_validated_int(prompt)`

```python
def get_validated_int(prompt):
    while True:
        raw = input(prompt).strip()
        if raw.isdigit():
            return int(raw)
        print('  [!] Commits must be a non-negative integer. Try again.')
```

**What it does:**
Asks the user to enter a number and keeps asking until they provide a valid whole number that is zero or greater. It uses Python's built-in `.isdigit()` check, which naturally rejects decimals, negative numbers, and any text. Once a valid number is entered, it converts it from text to an actual integer and returns it.

**Why it was created:**
The number of commits a contributor has made must be a real, countable number — not "many" or "-5" or "3.5". This function ensures the commits field always contains a usable integer that the program can work with.

**Used for:** Number of commits per contributor.

---

## Section 1 — Launch Your Project

### Project Data (Tuple)

```python
project = ("CMITS", 1, 2026, "Python", "Marie Criz Zaragoza")
contributors = []
```

The project's fixed details are stored in a tuple — an ordered, unchangeable list. Tuples are used here because this information (name, version, year, language, lead) should never be modified once set. The `contributors` list starts empty and gets filled as users are registered.

---

### `get_contributor()`

```python
def get_contributor():
    print("\n--- New Contributor Entry ---")
    name     = get_validated_alpha("Enter contributor name: ", "Name")
    role     = get_validated_alpha("Enter your role: ", "Role")
    language = get_validated_alpha("Enter primary language: ", "Language")
    commits  = get_validated_int("Enter number of commits: ")
    country  = get_validated_alpha("Enter country: ", "Country")

    return {
        "name": name,
        "role": role,
        "language": language,
        "commits": commits,
        "country": country
    }

# Main execution — register exactly 4 contributors
while len(contributors) < 4:
    try:
        contributor = get_contributor()
        contributors.append(contributor)
        print(f"Contributor {len(contributors)}/4 added successfully.")
    except KeyboardInterrupt:
        print("\nProcess interrupted by user.")
        break
```

**What it does:**
Collects all the information needed for one contributor by asking five questions in order: name, role, programming language, number of commits, and country. Each answer is validated using the helper functions. Once all five answers pass validation, they are packaged together into a dictionary (a labeled collection of data) and returned. The `while` loop calls this function repeatedly until exactly 4 contributors are registered.

**Why it was created:**
Instead of writing the same five questions out four separate times, this function groups them into one reusable block. It also handles the case where the user interrupts the program midway (pressing Ctrl+C), so it exits cleanly instead of crashing.

**Fields collected:**

| Field | What it means |
|-------|--------------|
| `name` | The contributor's full name |
| `role` | Their job on the project (e.g., Developer, Designer) |
| `language` | The programming language they use |
| `commits` | How many times they've submitted code |
| `country` | Where they are from |

---

### Banner and List Operations

After all contributors are registered, the program prints a welcome banner using the project tuple, then performs several list operations on the contributor data:

```python
# Sort contributor names alphabetically
names = []
for c in contributors:
    names.append(c["name"])
names.sort()

# Mark every contributor as Active
for c in contributors:
    c.update({"status": "Active"})

# Back up the first contributor's data
backup = contributors[0].copy()
```

These steps demonstrate sorting, dictionary updating with `update()`, and safe copying with `copy()` — all standard tools for managing lists of records.

---

## Section 2 — Track and Analyse Issues

### `get_issue()`

```python
def get_issue():
    print("\n--- New Issue Entry ---")
    issue_id = input("Enter Issue ID: ").strip()
    title    = get_validated_alpha("Enter Issue title: ", "Issue title")

    issue_type = get_validated_input(
        "Enter issue type (Bug/Feature): ",
        ["Bug", "Feature"]
    )
    priority = get_validated_input(
        "Enter issue priority (Critical/High/Medium/Low): ",
        ["Critical", "High", "Medium", "Low"]
    )

    reporter = get_validated_alpha("Enter issue reporter: ", "Reporter")

    status = get_validated_input(
        "Enter issue status (Open/In Progress/Resolved): ",
        ["Open", "In Progress", "Resolved"]
    )

    return {
        "id": issue_id,
        "title": title,
        "type": issue_type,
        "priority": priority,
        "reporter": reporter,
        "status": status
    }

# Collect exactly 5 issues
while len(issues) < 5:
    try:
        issue = get_issue()
        issues.append(issue)
        print(f"Issue {len(issues)}/5 added successfully.")
    except KeyboardInterrupt:
        print("\nProcess interrupted by user.")
        break
```

**What it does:**
Collects all the information needed to log one issue by asking six questions: the issue ID, title, type, priority, reporter, and status. The free-text fields (title, reporter) go through `get_validated_alpha()`, and the choice fields (type, priority, status) go through `get_validated_input()`. Once all six answers pass, they are packaged into a dictionary and returned. The `while` loop calls this five times to collect five issues.

**Why it was created:**
For the same reason as `get_contributor()` — to avoid repetition and keep the input process consistent and safe. Grouping all six questions into one function also makes the code easier to read and maintain.

**Fields collected:**

| Field | What it means |
|-------|--------------|
| `id` | A unique identifier for the issue (e.g., ISS-001) |
| `title` | A short description of the problem or request |
| `type` | Whether it's a Bug (broken) or Feature (new addition) |
| `priority` | How urgent it is — Critical, High, Medium, or Low |
| `reporter` | Who flagged this issue |
| `status` | Whether it's Open, In Progress, or Resolved |

---

### Analysis Operations

After all issues are collected, the program runs analysis automatically — no user input needed.

```python
# Count Open issues
open_count = 0
for issue in issues:
    if issue["status"] == "Open":
        open_count += 1

# Escalate the first issue to Critical
issues[0]["priority"] = "Critical"

# Build a set of all unique reporters
reporters = set()
for issue in issues:
    reporters.add(issue["reporter"])

# Build a set of all technologies used
tech_stack = set()
for c in contributors:
    tech_stack.add(c["language"])
tech_stack.add("Rust")
tech_stack.discard("Cobol")

# Set operations
all_items = reporters.union(tech_stack)
common    = reporters.intersection(tech_stack)
diff      = reporters.difference(tech_stack)

# Group issues by status
status_groups = {}
for issue in issues:
    s = issue["status"]
    if s in status_groups:
        status_groups[s].append(issue["title"])
    else:
        status_groups[s] = [issue["title"]]

# Find the top reporter without using max()
reporter_count = {}
for issue in issues:
    r = issue["reporter"]
    reporter_count[r] = reporter_count.get(r, 0) + 1

top_reporter = ""
top_count = 0
for r, count in reporter_count.items():
    if count > top_count:
        top_count = count
        top_reporter = r
```

**What each part does:**
- **Open count** — loops through all issues and tallies how many are still unresolved
- **Priority escalation** — directly updates the first issue's priority to show how records can be changed by index
- **Reporters set** — collects all unique reporter names; sets automatically remove duplicates
- **Tech stack set** — collects all unique languages from contributors, then demonstrates `add()` and `discard()` to manually adjust the set
- **Set operations** — `union` combines both sets, `intersection` finds what they share, `difference` finds what's only in reporters
- **Status groups** — builds a dictionary where each key is a status and its value is a list of issue titles under that status
- **Top reporter** — manually loops through reporter counts to find who submitted the most, without relying on Python's `max()` shortcut

---

## Section 3 — Save, Read and Report

### Create Project Folder

```python
import os

folder_name = project[0].lower().replace(" ", "_")

if not os.path.exists(folder_name):
    os.makedirs(folder_name)
    print(f"Folder created : {folder_name}/")
else:
    print(f"Folder already exists : {folder_name}/")
```

**What it does:** Takes the project name from the tuple, converts it to lowercase, and creates a folder with that name (`cmits/`). It checks first whether the folder already exists so it never accidentally overwrites an existing one.

---

### Save `project_report.txt`

```python
report_path  = os.path.join(folder_name, "project_report.txt")
priority_str = " ".join([f"{k}:{v}" for k, v in priority_count.items()])

try:
    with open(report_path, "w") as f:
        f.write(f"{line}\n")
        f.write(f" {project[0]} — PROJECT REPORT\n")
        f.write(f"{line}\n\n")
        f.write(f"Project  : {project[0]}\n")
        f.write(f"Version  : {project[1]}\n")
        f.write(f"Year     : {project[2]}\n")
        f.write(f"Language : {project[3]}\n")
        f.write(f"Lead     : {project[4]}\n\n")
        f.write(f"--- Contributors ({len(contributors)}) ---\n")
        for c in contributors:
            f.write(f"  {c['name']} | {c['role']} | {c['language']} | {c['commits']} commits | {c['country']}\n")
        f.write(f"\n--- Issues ({len(issues)}) ---\n")
        for issue in issues:
            f.write(f"  {issue['id']} | {issue['title']} | {issue['priority']} | {issue['reporter']} | {issue['status']}\n")
        f.write(f"\n--- Analysis ---\n")
        f.write(f"Open issues  : {open_count}\n")
        f.write(f"Reporters    : {reporters}\n")
        f.write(f"Tech Stack   : {tech_stack}\n")
        f.write(f"Top Reporter : {top_reporter} ({top_count} issues)\n")
        f.write(f"Priority     : {priority_str}\n\n")
        f.write(f"--- Status Groups ---\n")
        for s, titles in status_groups.items():
            f.write(f"  {s} : {titles}\n")
        f.write(f"{line}\n")
    print(f"Report saved : {report_path}")
except IOError as e:
    print(f"IOError writing report: {e}")
```

**What it does:** Opens a new text file and writes the entire project summary into it — project info, all contributors, all issues, and the analysis results. It's wrapped in a `try/except` block so that if something goes wrong during saving (e.g., the disk is full), the program prints a helpful error instead of crashing.

---

### Save `issues.csv`

```python
import csv

csv_path = os.path.join(folder_name, "issues.csv")

try:
    with open(csv_path, "w", newline="") as f:
        writer = csv.writer(f)
        writer.writerow(["id", "title", "priority", "reporter", "status"])
        for issue in issues:
            writer.writerow([
                issue["id"],
                issue["title"],
                issue["priority"],
                issue["reporter"],
                issue["status"]
            ])
    print(f"CSV saved : {csv_path}")
except IOError as e:
    print(f"IOError writing CSV: {e}")
```

**What it does:** Creates a spreadsheet-compatible `.csv` file. The first row is the header (column names), and each issue becomes one data row beneath it. This file can be opened directly in Excel or Google Sheets without any extra steps.

---

### Reading Files Back

```python
try:
    # read() — load and print the full file
    with open(report_path, "r") as f:
        content = f.read()
    print(content)

    # readline() — read only the first two lines
    with open(report_path, "r") as f:
        first_line  = f.readline().rstrip()
        second_line = f.readline().rstrip()
    print(f"Line 1 : {first_line}")
    print(f"Line 2 : {second_line}")

    # readlines() — read all lines, filter for Critical or High
    with open(report_path, "r") as f:
        all_lines = f.readlines()
    total_lines = len(all_lines)
    critical_high_count = 0
    for l in all_lines:
        if "Critical" in l or "High" in l:
            critical_high_count += 1
            print(f"  {l.rstrip()}")
    print(f"Total lines : {total_lines} Critical/High lines : {critical_high_count}")

except FileNotFoundError as e:
    print(f"FileNotFoundError: {e}")
```

**What it does:** After saving, the program reads the report back in three different ways to confirm the file was written correctly. `read()` loads the entire file at once. `readline()` reads one line at a time — here it's called twice to get the first two lines. `readlines()` loads every line into a list, then the program loops through and prints only the lines that mention "Critical" or "High".

---

## Bonus Section — Urgent Issue Detection

### Part 1: List Comprehension Filter

```python
urgent = [issue["title"] for issue in issues if issue["priority"] in ("Critical", "High")]

print(f"Urgent issues : {urgent}")
print(f"Count : {len(urgent)}\"")
```

**What it does:** Uses a single line called a list comprehension to scan all five issues and collect only the titles of those marked Critical or High. It reads almost like plain English: *"give me the title of every issue where the priority is Critical or High."*

**Why this approach:** It's a compact and efficient way to filter data without writing a full multi-line loop. The result is a clean list of urgent issue titles ready to be used immediately.

---

### Part 2: Append Urgent Issues to Report

```python
try:
    with open(report_path, "a") as f:
        f.write(f"\n--- URGENT ISSUES ---\n")
        for title in urgent:
            f.write(f"  - {title}\n")
        f.write(f"{line}\n")
    print(f"Urgent section appended to {report_path}")
except IOError as e:
    print(f"IOError appending to report: {e}")

# Confirm by printing the last 6 lines
try:
    with open(report_path, "r") as f:
        all_lines = f.readlines()
    print(f"\n--- Last 6 lines ---")
    for l in all_lines[-6:]:
        print(l.rstrip())
except FileNotFoundError as e:
    print(f"FileNotFoundError: {e}")
```

**What it does:** Opens the existing report file in append mode (`"a"`) — this means it adds to the bottom of the file without erasing anything already saved. It writes a new "URGENT ISSUES" section listing every critical or high-priority issue title. Then it reads the last 6 lines of the file and prints them to confirm the addition worked correctly.

---

## Input Rules Summary

| Field | Rule |
|-------|------|
| Names, roles, countries, titles, reporters | Letters and spaces only — no numbers or symbols |
| Commits | Whole numbers only, zero or greater |
| Issue type | Must be exactly "Bug" or "Feature" |
| Priority | Must be "Critical", "High", "Medium", or "Low" |
| Status | Must be "Open", "In Progress", or "Resolved" |

If any rule is broken, the program asks again. It does not crash.

---

## Output Files

| File | Location | Contents |
|------|----------|----------|
| `project_report.txt` | `cmits/` folder | Full report: project info, contributors, issues, analysis, urgent flags |
| `issues.csv` | `cmits/` folder | Spreadsheet of all 5 issues — ready to open in Excel |
