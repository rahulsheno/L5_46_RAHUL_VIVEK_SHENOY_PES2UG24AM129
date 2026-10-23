# Reflection

**1. Which issues were easiest and which were hardest to fix?**
Style issues (E501, W292, unused imports, `== None`) were mechanical and quick. The hardest was `eval()` in `calculate_treatment_cost`: it could not simply be swapped for `ast.literal_eval`, because the input is a formula, not a literal. I had to change the function's design to accept a numeric rate, which also changed how it is called.

**2. Did any tool report false positives, or miss real problems?**
Nothing was strictly a false positive, but severity needs judgement. B311 (`random`) is Low and harmless for a random demo number, yet it matters for a token meant to be hard to guess. The tools also missed things: the hardcoded key that was printed to the console, the floating-point output `1344.0000000000002`, and `main()` running on import. I found those by running and reading the program.

**3. How would you integrate static analysis into a real workflow?**
Run Flake8, Pylint and Bandit in a pre-commit hook and again in CI on every pull request, failing the build on High/Medium Bandit findings and on a Pylint score below a threshold (e.g. 9.5). Style tools can auto-format on save, so reviewers can focus on logic and design.

**4. What improvements did you see in quality, security and maintainability?**
Pylint went from 5.19 to 10.00, Bandit from 5 issues (2 High) to 0, and Flake8 from 11 findings to 0. The code no longer has command injection or arbitrary code execution paths, no hardcoded secret, and no silent error swallowing. Docstrings, PEP 8 naming and `with` blocks make it easier to read and safer to change. Static analysis is not a substitute for testing, though: the tools passed the original code's logic bugs, which running it exposed.
