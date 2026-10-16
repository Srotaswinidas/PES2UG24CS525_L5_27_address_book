# Lab 5: Static Code Analysis - Issues Found

**Program:** `address_book.py` (Contact & Address Book Manager)
**Tools:** Pylint, Bandit, Flake8

## Summary

| Tool | Before | After |
|------|--------|-------|
| Pylint | 5.47/10 (29 messages) | 10.00/10 |
| Bandit | 4 findings (1 High, 1 Medium, 2 Low) | 0 |
| Flake8 | 4 findings | 0 |

## Issue table

Sorted by severity (High and Medium first). Line numbers refer to the **original** file.

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| Weak MD5 hash (Bandit B324, **High**) | Security | 19 | `hashlib.md5` is cryptographically broken, and the id is cut to 6 characters, which makes collisions likely | Use `hashlib.sha256` |
| Unsafe `pickle.load` (Bandit B301, **Medium**) | Security | 67 | Unpickling a tampered backup file can execute arbitrary code (CWE-502) | Store the backup as JSON with `json.dump` / `json.load` |
| `pickle` import (Bandit B403, Low) | Security | 3 | Importing pickle flags a possible deserialization risk | Remove the import and use `json` |
| Hardcoded password (Bandit B105, Low) | Security | 6 | `SYNC_PASSWORD = "contacts-demo"` is a secret in source code and is never used | Delete the unused constant |
| Overly broad exception (Pylint W0718) | Bug | 34 | `except Exception` hides unrelated errors, not just a missing contact | Catch `KeyError` only |
| `open()` without encoding (Pylint W1514) | Bug | 54 | CSV encoding depends on the platform, so output can differ between machines | Add `encoding="utf-8"` (also on the JSON backup files) |
| `range(len(...))` loop (Pylint C0200) | Style | 49 | Index-based loop is not Pythonic and is harder to read | Use `enumerate(sorted(contacts), 1)` |
| camelCase function and argument names (Pylint C0103) | Style | 10, 18, 22, 30, 39, 47, 53, 60, 65 | Names such as `addContact` and `fullName` break PEP 8 snake_case | Rename to `add_contact`, `full_name`, etc., and update all calls |
| Missing module and function docstrings (Pylint C0114, C0116) | Style | 1, 10, 18, 22, 30, 39, 47, 53, 60, 65, 70 | No documentation of what the module and functions do | Add docstrings to the module and every function |
| Missing whitespace around operator (Flake8 E225) | Style | 23 | `found=[]` | Change to `found = []` |
| Too many blank lines (Flake8 E303) | Style | 47 | 3 blank lines before `printDirectory` | Use exactly 2 blank lines |
| Missing whitespace after comma (Flake8 E231) | Style | 84 | `"\| first:",sorted(...)` | Add a space after the comma |
| Line too long (Flake8 E501) | Style | 85 | 86 characters, limit is 79 | Split the `print` call over several lines |
| `main()` runs on import | Design | 88 | Importing the module runs the demo as a side effect | Wrap the call in `if __name__ == "__main__":` |

## Notes

- All four Bandit findings, all four Flake8 findings and all Pylint messages were fixed, in three commits (one per tool).
- Replacing MD5 with SHA-256 changes the contact id printed for Neha Sharma (`ecc177` to `8c1a8a`). All other output is unchanged.
