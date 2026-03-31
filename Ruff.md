Jest to jednocześnie linter oraz formatter kodu napisany w Rust, przez co jest bardzo szybki.
Formatter dba o estetykę kodu i sprawia aby ta estetyka była ustandaryzowana. Linter dba o zachowanie dobrych praktyk w kodzie, aby był jak najbardziej sensowny.

Linter
``` bash
ruff check path/to/file - sprawdza dany plik

ruff check . - wszystkie pliki
```

naprawia możliwe do naprawy błędy
```
ruff check --fix .
```

tryb ciągły sprawdzania błędów podczas kodowania
```
ruff check --watch .
```

Formatter
```
ruff format .
```

Formatowanie przy zapisywaniu w VS Code
editor.formatOnSave: true - w ustawieniach

Konfiguracja Ruff w pyproject.toml
```
[tool.ruff]
line-length = 88  
target-version = "py313"

[tool.ruff.lint]
# E - errors of proper spacing (PEP 8 errors)
# F - pyflake logical errors in code
# I - isort; imports are sorted
# B - flake8 bugbear; not obvious situations that lead to na error
# UP - pyupgrade; code style and behaviours adequate to current Python version
# N - naming; proper naming of functions (snake_case), classes (PascalCase), consts (SCREAMING_SNAKE_CASE)
# A - Python's names are not allowed in the code (id, list, sum etc.)
# PT - pytest standards are beeing keept
# C4 - optimise building of lists and dictionaries of comprehensions
# PTH - do not allow using os.path
# E501 - line is too long
select = ["E", "F", "I", "B", "UP", "N", "A", "PT", "C4", "PTH"]
ignore = ["E501"]
```