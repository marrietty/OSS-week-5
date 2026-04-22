# CMITS — Contributor Management and Issue Tracking System

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![Code Quality](https://img.shields.io/badge/Code%20Quality-flake8-yellow)
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Week](https://img.shields.io/badge/OSS-Week%206-blueviolet)

A Python command-line application for managing project contributors and tracking issues — built as a course project for Introduction to Open Source Software.

---

## Table of Contents

- [About](#about)
- [Features](#features)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [How to Use](#how-to-use)
- [Output Files](#output-files)
- [GitHub Actions](#github-actions)
- [Contributing](#contributing)
- [License](#license)

---

## About

CMITS is a simple, interactive system that lets you register contributors, log issues, and generate project reports — all from the terminal. It demonstrates core Python concepts including tuples, lists, sets, dictionaries, file I/O, and input validation.

---

## Features

-  Register up to **4 contributors** with name, role, language, commits, and country
-  Log up to **5 issues** with type, priority, reporter, and status
-  Robust **input validation** — rejects invalid entries and re-prompts cleanly
-  Automatic **project report** generation (`.txt`)
-  Automatic **issues export** (`.csv`)
-  **Urgent issue detection** using list comprehension
-  **Final summary** printed to the terminal

---

## Project Structure

```
OSS-week-6/
├── main.py
├── CODE_OF_CONDUCT.md      # Community standards
├── CONTRIBUTING.md         # How to contribute
├── DOCUMENTATION.md        # Plain-language code explanation
├── LICENSE                 # MIT License
└── README.md               # You are here
```

---

## Requirements

- Python 3.x
- No external libraries — uses only built-ins (`os`, `csv`)

---

## How to Use

**1. Clone the repository**
```bash
git clone https://github.com/marrietty/OSS-week-6.git
cd OSS-week-6
```

**2. Run the program**
```bash
python src/main.py
```

**3. Follow the prompts**

The program guides you through two stages:

**Stage 1 — Contributors** (4 entries)
```
Enter contributor name: Jane Doe
Enter your role: Developer
Enter primary language: Python
Enter number of commits: 42
Enter country: Philippines
```

**Stage 2 — Issues** (5 entries)
```
Enter Issue ID: ISS-001
Enter Issue title: Login page crashes on mobile
Enter issue type (Bug/Feature): Bug
Enter issue priority (Critical/High/Medium/Low): High
Enter issue reporter: Jane Doe
Enter issue status (Open/In Progress/Resolved): Open
```

---


## Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](./CONTRIBUTING.md) before submitting a pull request, and follow the [Code of Conduct](./CODE_OF_CONDUCT.md).

---

## License

This project is licensed under the [MIT License](./LICENSE).

---

**Marie Criz Zaragoza** · Student ID: 26040022 · Introduction to Open Source Software
