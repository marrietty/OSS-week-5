# Documentation — CMITS

**Contributor Management and Issue Tracking System**  
A plain-language guide to understanding how this program works.

---

## What Is This Program?

Imagine you're managing a small software team. You need to keep track of **who is working on the project** and **what problems need to be solved**. CMITS is a simple tool that helps you do exactly that — through a series of questions it asks you in the terminal.

At the end, it saves everything into organized files you can read or share.

---

## How the Program Is Organized

The program is divided into three main parts, plus a bonus section.

---

### Part 1 — Launch Your Project

**What it does:** Sets up the project and collects information about your team.

The program starts by storing some fixed project details — the name, version, year it started, the programming language used, and the project lead's name. Think of this like a project ID card that never changes.

Then it asks you to enter information for **4 contributors** — one at a time. For each contributor, you provide:
- Their **name**
- Their **role** (e.g., Developer, Designer)
- The **programming language** they use
- How many **commits** (code submissions) they've made
- Their **country**

The program checks every answer as you type. If you enter something invalid — like a number where a name should be — it will ask you again until you get it right.

Once all 4 contributors are entered, it prints a sorted list of their names and marks everyone as "Active."

---

### Part 2 — Track and Analyse Issues

**What it does:** Collects problem reports and runs basic analysis on them.

Next, the program asks you to log **5 issues** — these are problems or feature requests for the project. For each issue, you provide:
- An **Issue ID** (like a ticket number)
- A **title** describing the problem
- The **type** — either Bug (something broken) or Feature (something new to add)
- A **priority** — Critical, High, Medium, or Low
- The **reporter** — who flagged this issue
- The **status** — Open, In Progress, or Resolved

After all issues are entered, the program automatically:
- Counts how many issues are still **Open**
- Updates the first issue's priority to **Critical** (to simulate an urgent change)
- Lists all unique **reporters** and **programming languages** used on the team
- Groups issues by their **status**
- Finds the **top reporter** — whoever submitted the most issues

---

### Part 3 — Save, Read, and Report

**What it does:** Saves everything to files and reads them back.

Once the data is collected, the program creates a folder called `cmits/` and saves two files inside it:

**project_report.txt** — A full text summary of the project, including all contributors, all issues, and the analysis results. Think of it as a printed report you'd hand to a manager.

**issues.csv** — A spreadsheet-style file listing all issues in a simple format. This can be opened in Excel or Google Sheets.

The program then reads the report back and prints it on screen — first the whole file, then just the first two lines, then only the lines that mention "Critical" or "High" priority.

---

### Bonus — Urgent Issues

**What it does:** Finds and highlights the most critical issues.

As a final step, the program uses a single line of code to pull out all issues labelled Critical or High — these are the ones that need immediate attention. It then adds a special "URGENT ISSUES" section to the bottom of the report file and confirms by printing the last 6 lines of the file.

---

## Input Rules

The program is strict about what it accepts to keep the data clean:

| Field | Rule |
|-------|------|
| Names, roles, countries | Letters and spaces only — no numbers |
| Commits | Whole numbers only (0 or more) |
| Issue type | Must be "Bug" or "Feature" |
| Priority | Must be "Critical", "High", "Medium", or "Low" |
| Status | Must be "Open", "In Progress", or "Resolved" |

If you enter something that doesn't match, the program will simply ask again — it won't crash.

---

## Output Summary

By the end of the program, you will have:

- A printed **final summary** on screen
- A **project_report.txt** file with all details
- An **issues.csv** file ready to be opened in a spreadsheet

---

## In Simple Terms

This program is like a digital form that collects team and issue information, organizes it, and saves it into files — all without needing a database or an internet connection. It's self-contained and runs entirely from the command line.
