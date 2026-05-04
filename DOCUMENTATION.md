# CMITS Documentation

**Contributor Management and Issue Tracking System**

---

## Variables Used Throughout the Program

Before anything runs, two simple variables are created at the very top. These are used everywhere in the program just to make the printed output look clean and organized.

| Variable | Value | What it's for |
|----------|-------|---------------|
| `line` | `70 * "="` | A long line of equal signs. Used as a header separator when printing to the terminal. |
| `dash` | `70 * "-"` | A long line of dashes. Used as a section divider between printed blocks. |

---

## Part 1 — Helper Functions

I made three small functions whose only job is to check if the user's input is acceptable. Instead of writing the same checking logic over and over, I put it in these functions and just call them whenever I need them.

---

### `get_validated_input(prompt, valid_options)`

This function is for fields that only accept specific words — like "Bug" or "Feature", or "Open", "In Progress", "Resolved". It takes the question to ask (`prompt`) and a list of accepted answers (`valid_options`).

It keeps asking the same question until the user gives one of the accepted answers. It also fixes capitalization automatically, so typing "bug" or "BUG" both count as "Bug".

**Variables inside this function:**

| Variable | What it holds |
|----------|--------------|
| `prompt` | The question shown to the user (passed in when the function is called) |
| `valid_options` | The list of accepted answers (e.g., `["Bug", "Feature"]`) |
| `raw` | What the user actually typed, with extra spaces removed |
| `formatted` | The same input but with proper capitalization applied |

**Used when collecting:** issue type, issue priority, issue status.

---

### `get_validated_alpha(prompt, field_name)`

This function is for text fields like names, roles, or countries. It checks that the input is not empty and contains only letters and spaces — no numbers, no symbols.

If the input passes, it returns the value with proper capitalization (so "marie" becomes "Marie", "new zealand" becomes "New Zealand"). If it fails, it shows a message and asks again.

**Variables inside this function:**

| Variable | What it holds |
|----------|--------------|
| `prompt` | The question shown to the user |
| `field_name` | The name of the field, used in the error message (e.g., "Name", "Role") |
| `value` | What the user typed, cleaned up with `.strip()` |

**Used when collecting:** contributor name, role, language, country — and issue title and reporter name.

---

### `get_validated_int(prompt)`

This function is just for the commits field. It keeps asking until the user types a whole number that is zero or greater. It uses `.isdigit()` to check, which automatically rejects anything like "-5", "3.5", or "many".

**Variables inside this function:**

| Variable | What it holds |
|----------|--------------|
| `prompt` | The question shown to the user |
| `raw` | What the user typed, cleaned up with `.strip()` |

**Used when collecting:** number of commits per contributor.

---

## Part 2 — Collecting Contributors

### Project Tuple

Before asking for contributors, the program stores the project's fixed details in a tuple called `project`.

```
project = ("CMITS", 1, 2026, "Python", "Marie Criz Zaragoza")
```

A tuple was chosen here because this information should never change while the program is running. Tuples in Python do not allow changes after they are created, which makes them a safe choice for fixed data like this.

| Index | Value | Meaning |
|-------|-------|---------|
| `project[0]` | `"CMITS"` | Project name |
| `project[1]` | `1` | Version number |
| `project[2]` | `2026` | Year started |
| `project[3]` | `"Python"` | Main language |
| `project[4]` | `"Marie Criz Zaragoza"` | Project lead |

There is also a `contributors` variable — it starts as an empty list and gets filled as each contributor is registered.

---

### `get_contributor()`

This function collects all the details for one contributor. It asks five questions and uses the helper functions to make sure each answer is valid before accepting it. Once all five answers are collected, it returns them together as a dictionary.

The program calls this function inside a `while` loop that keeps going until exactly 4 contributors have been added. If the user presses Ctrl+C to stop early, the loop exits without crashing.

**Variables inside this function:**

| Variable | What it holds | Where it comes from |
|----------|--------------|---------------------|
| `name` | Contributor's full name | `get_validated_alpha()` |
| `role` | Their role on the project | `get_validated_alpha()` |
| `language` | Programming language they use | `get_validated_alpha()` |
| `commits` | Number of code submissions | `get_validated_int()` |
| `country` | Their country | `get_validated_alpha()` |

**Variables in the loop that calls it:**

| Variable | What it holds |
|----------|--------------|
| `contributors` | The growing list of contributor dictionaries |
| `contributor` | One contributor's data, returned from `get_contributor()` |

After the loop finishes, the program does three more things with the contributor data:

- Builds a `names` list by pulling just the names out of `contributors`, then sorts it alphabetically.
- Loops through `contributors` and adds `"status": "Active"` to every contributor using `.update()`.
- Makes a copy of the first contributor's data using `.copy()` and stores it in `backup` — just in case the original gets changed later.

---

## Part 3 — Collecting Issues

### `get_issue()`

This works the same way as `get_contributor()` but for issues. It asks six questions, validates each one, and returns everything as a dictionary. The program calls it inside a loop until exactly 5 issues are logged.

**Variables inside this function:**

| Variable | What it holds | Where it comes from |
|----------|--------------|---------------------|
| `issue_id` | A unique ID for the issue (e.g., ISS-001) | Plain `input()` — no validation |
| `title` | Short description of the issue | `get_validated_alpha()` |
| `issue_type` | "Bug" or "Feature" | `get_validated_input()` |
| `priority` | "Critical", "High", "Medium", or "Low" | `get_validated_input()` |
| `reporter` | Who reported it | `get_validated_alpha()` |
| `status` | "Open", "In Progress", or "Resolved" | `get_validated_input()` |

**Variables in the loop that calls it:**

| Variable | What it holds |
|----------|--------------|
| `issues` | The growing list of issue dictionaries |
| `issue` | One issue's data, returned from `get_issue()` |

---

### Analysis — What Happens After Issues Are Collected

Once all 5 issues are in the `issues` list, the program automatically runs some analysis. No input is needed from the user at this point.

**Variables created during analysis:**

| Variable | What it holds | How it's built |
|----------|--------------|----------------|
| `open_count` | Number of issues with status "Open" | A loop that counts matching issues |
| `reporters` | A set of unique reporter names | A loop that adds each reporter to a `set()` — duplicates are ignored automatically |
| `tech_stack` | A set of unique languages used by contributors | A loop through `contributors`, plus manually added "Rust" and removed "Cobol" using `.add()` and `.discard()` |
| `all_items` | Everything in both sets combined | `reporters.union(tech_stack)` |
| `common` | Names that appear in both sets | `reporters.intersection(tech_stack)` |
| `diff` | Names only in reporters, not in tech_stack | `reporters.difference(tech_stack)` |
| `all_priorities` | Set of all priority levels used | A loop through `issues` adding each priority |
| `priority_count` | Dictionary counting how many issues per priority | A loop that adds to a counter for each priority key |
| `status_groups` | Dictionary grouping issue titles by their status | A loop that groups titles under each status as a list |
| `reporter_count` | Dictionary counting issues per reporter | A loop tracking how many issues each person reported |
| `top_reporter` | Name of the person with the most issues reported | A manual loop comparing counts — no `max()` used |
| `top_count` | How many issues the top reporter submitted | Tracked alongside `top_reporter` in the same loop |

The program also directly changes `issues[0]["priority"]` to "Critical" to simulate an urgent escalation — showing how a specific record can be updated by its index.

---

## Part 4 — Saving and Reading Files

### Creating the Folder

The program creates a folder to store all output files. The folder name comes from the project name in the tuple, converted to lowercase.

| Variable | What it holds |
|----------|--------------|
| `folder_name` | `"cmits"` — built from `project[0].lower()` |

It checks if the folder already exists using `os.path.exists()` before creating it, so it never overwrites anything.

---

### Saving `project_report.txt`

| Variable | What it holds |
|----------|--------------|
| `report_path` | Full file path — built with `os.path.join(folder_name, "project_report.txt")` |
| `priority_str` | A single string summarizing priority counts — built from `priority_count.items()` |

The file is opened in write mode (`"w"`) and the entire project summary is written into it: project info, all contributors, all issues, analysis results, and status groups. The whole thing is wrapped in `try/except IOError` so if saving fails for any reason, it shows a clear error instead of crashing.

---

### Saving `issues.csv`

| Variable | What it holds |
|----------|--------------|
| `csv_path` | Full file path — built with `os.path.join(folder_name, "issues.csv")` |
| `writer` | The CSV writer object from Python's built-in `csv` module |

The first row written is the header (`id, title, priority, reporter, status`). Then each issue becomes one row. This file can be opened directly in Excel or Google Sheets.

---

### Reading the Files Back

After saving, the program reads the report file back three different ways to confirm everything saved properly:

| Method | What it does |
|--------|-------------|
| `f.read()` | Loads and prints the entire file at once into a variable called `content` |
| `f.readline()` | Reads one line at a time — called twice to get `first_line` and `second_line` |
| `f.readlines()` | Loads all lines into a list called `all_lines`, then loops through to find and print only lines that mention "Critical" or "High" |

A variable called `critical_high_count` tracks how many of those flagged lines were found.

All reading is wrapped in `try/except FileNotFoundError` in case the file somehow doesn't exist when the program tries to open it.

---

## Bonus — Urgent Issues

### List Comprehension

```python
urgent = [issue["title"] for issue in issues if issue["priority"] in ("Critical", "High")]
```

| Variable | What it holds |
|----------|--------------|
| `urgent` | A list of issue titles where priority is Critical or High — built in one line |

This is called a list comprehension. It's a shorter way to write a filter loop. It goes through every issue and picks the title only if the priority matches. The result is a ready-to-use list of urgent issue titles.

---

### Appending to the Report

The program opens the existing report file again, but this time in append mode (`"a"`). This means it adds to the bottom without touching anything already saved. It writes a new "URGENT ISSUES" section with each urgent title listed underneath.

After appending, it reads the last 6 lines of the file using `all_lines[-6:]` and prints them to confirm the section was added correctly.

---

## Input Rules

| Field | What's accepted |
|-------|----------------|
| Names, roles, countries, titles, reporters | Letters and spaces only |
| Commits | Whole numbers, zero or above |
| Issue type | "Bug" or "Feature" only |
| Priority | "Critical", "High", "Medium", or "Low" only |
| Status | "Open", "In Progress", or "Resolved" only |

If the wrong thing is typed, the program asks again. It does not crash.

---

## Output Files

| File | What's in it |
|------|-------------|
| `cmits/project_report.txt` | Full report — project info, contributors, issues, analysis, and urgent issues |
| `cmits/issues.csv` | All 5 issues in spreadsheet format, ready to open in Excel |
