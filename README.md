# Lab 5: Static Code Analysis - Hospital Patient System

A hospital patient records and admission manager. It admits patients into wards, processes discharges, calculates treatment costs, and exports an admission ledger.

This repository contains the cleaned version of `hospital_patient_system.py` after analysis with **Pylint**, **Bandit** and **Flake8**.

## Results

| Tool | Before | After |
|------|--------|-------|
| Pylint | 5.19/10 | **10.00/10** |
| Bandit | 5 issues (2 High, 1 Medium, 2 Low) | **0 issues** |
| Flake8 | 11 findings | **0 findings** |

## Repository contents

| File | Description |
|------|-------------|
| `hospital_patient_system.py` | Cleaned and fixed program |
| `issues.md` | Table of every issue found, its type, line(s), description and fix |
| `reflection.md` | Answers to the lab reflection questions |
| `reports/before/` | `pylint_report.txt`, `bandit_report.txt`, `flake8_report.txt` from the original code |
| `reports/after/` | The same three reports after the fixes |

## Setup and usage

```bash
python3 -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install pylint bandit flake8
```

Run the program:

```bash
python hospital_patient_system.py
```

Optionally set the nurse access key (it is no longer hardcoded in the source):

```bash
export NURSE_ACCESS_KEY="your-key"     # Windows: set NURSE_ACCESS_KEY=your-key
```

Re-run the analysis:

```bash
pylint hospital_patient_system.py
bandit -r hospital_patient_system.py
flake8 hospital_patient_system.py
```

## What was fixed

The work was done in three commits, one per tool.

**1. Bandit (security)**
- Removed `os.system` with string concatenation (shell command injection, B605, High).
- Replaced MD5 with SHA-256 (B324, High).
- Removed `eval()` on caller-supplied input (B307, Medium).
- Replaced `random` with `secrets` for ward tokens (B311, Low).
- Replaced the bare `except: pass` with `except KeyError` and a visible message (B110, Low).
- Moved the hardcoded access key to the `NURSE_ACCESS_KEY` environment variable and stopped printing it.

**2. Pylint (quality)**
- Added module and function docstrings.
- Renamed `admitPatient` to `admit_patient` (snake_case).
- Fixed the mutable default argument `intake_log=[]` (default `None`).
- Switched to f-strings and `with open(..., encoding="utf-8")`.
- Simplified redundant if/else logic, removed unused variables and the unused `math` import, and stopped shadowing the global `patient_records`.
- Added an `if __name__ == "__main__":` guard.

**3. Flake8 (style)**
- Wrapped lines longer than 79 characters.
- Added the missing newline at end of file.

## Bugs found by running the program

These were not reported by any tool:
- Cost printed as `1344.0000000000002`; it is now rounded to 2 decimal places (`1344.0`).
- `main()` ran on import; it is now guarded.
- The "master key" was printed to the console.

## Behaviour changes

- `calculate_treatment_cost(p_id, rate)` now takes a numeric multiplier (e.g. `1.12`) instead of an expression string (e.g. `"base_bill * 1.12"`). This is the only safe replacement for `eval`.
- `discharge_patient` now returns `True` on success and `False` for an unknown patient ID.
- Patient hashes are now SHA-256, so they differ from the original MD5 values.

## Expected output

```
Is P103 admitted: True
Cost calculated: 1344.0
Patient Hash: 063b8ff279d9e5d56585b4bc8cee04e3f29907409f86cfba9da3fa0794e62710
Ward Token: <random 4-digit number>
Patient ledger exported to: patient_records.txt
Active admits: 2
Access key configured: False
```
