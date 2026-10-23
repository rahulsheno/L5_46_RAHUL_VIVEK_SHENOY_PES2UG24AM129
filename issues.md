# Issues Found and Fixed: `hospital_patient_system.py`

Baseline: Pylint **5.19/10**, Bandit **5 issues** (2 High, 1 Medium, 2 Low), Flake8 **11 findings**.
Final: Pylint **10.00/10**, Bandit **0 issues**, Flake8 **0 findings**.

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| `os.system` with string concatenation (B605, High) | Security | 56 | Filename was concatenated into a shell command, allowing command injection (e.g. `x; rm -rf ~`). | Removed the shell call and used `print()` to report the export. |
| Weak MD5 hash (B324, High) | Security | 45 | MD5 is broken and unsuitable for identifying patient data. | Switched to `hashlib.sha256`. |
| `eval()` on caller-supplied string (B307 Medium / W0123) | Security | 40 | Arbitrary code execution through `formula_expr`. | Replaced the expression string with a numeric `rate` parameter: `round(base_bill * float(rate), 2)`. |
| Insecure `random` for tokens (B311, Low) | Security | 49 | `random` is predictable, so ward tokens could be guessed. | Used `secrets.randbelow(9000) + 1000`. |
| Bare `except: pass` (B110 Low / E722 / W0702) | Bug / Security | 21-25 | Hid every error, so discharging an unknown patient silently did nothing. | Catch `KeyError`, report the unknown ID, and return `False` or `True`. |
| Hardcoded secret, printed to console (not flagged by tools) | Security | 62, 71 | Master key committed to source and echoed to output. | Read from the `NURSE_ACCESS_KEY` environment variable and no longer print it. |
| Mutable default argument (W0102) | Bug | 13 | `intake_log=[]` is shared across all calls. | Default to `None` and create a new list inside the function. |
| Floating-point output (found by running the program) | Bug | 39-41 | Cost printed `1344.0000000000002`. | Round currency to 2 decimal places. |
| `main()` runs on import (found by reading) | Bug | 80 | Importing the module triggers admissions, file writes and prints. | Added `if __name__ == "__main__":`. |
| Resource leak, no encoding (R1732 / W1514) | Quality | 53-55 | File not closed if `write` fails, and encoding is platform-dependent. | `with open(..., encoding="utf-8")`. |
| `== None` comparison (E711 / C0121) | Style | 30 | Should use identity for `None`. | `record is not None`. |
| Redundant if/else returning bool (R1703 / R1705) | Quality | 31-35 | Verbose branching. | Return the boolean expression directly. |
| Unused variables (F841 / W0612) | Quality | 29, 39, 61 | `bed_charge`, `base_bill` (eval-only) and loop var `p` unused. | Removed `bed_charge`; `p` removed by using `.values()`; `base_bill` now used. |
| Unused import `math` (F401 / W0611) | Quality | 4 | Dead import. | Removed. |
| `admitPatient` is camelCase (C0103) | Style | 13 | Violates PEP 8 naming. | Renamed to `admit_patient`. |
| Parameter shadows global (W0621) | Quality | 59 | `patient_records` argument hides the module-level dict. | Renamed the parameter to `records`. |
| `%` string formatting (C0209) | Style | 17 | Old-style formatting. | f-string. |
| Missing docstrings (C0114 / C0116) | Documentation | module, all functions | No documentation. | Added module and function docstrings. |
| Long lines (E501) | Style | 7-9, 16, 72 | Lines over 79 characters. | Wrapped dict literals and statements. |
| No newline at end of file (W292 / C0304) | Style | 80 | File ended without a newline. | Added a trailing newline. |

## Behaviour changes to be aware of
- `calculate_treatment_cost` now takes a numeric multiplier (`1.12`) instead of an expression string (`"base_bill * 1.12"`). This is the only way to remove `eval` safely.
- `discharge_patient` now returns `True` or `False` instead of `None`.
- Hashes and tokens differ from the original (SHA-256 instead of MD5, and tokens are random anyway).
