# **Make the codebase accessible from a `tests` folder**

Fixes the code imports issues when the tests are not within the codebase


<br><br>


---
**Contents**
- [**Make the codebase accessible from a `tests` folder**](#make-the-codebase-accessible-from-a-tests-folder)
  - [**The conftest file**](#the-conftest-file)


---

## **The conftest file**

Directly under `tests/`, create a file `conftest.py`  

Add:  
```py
import sys
from pathlib import Path


THIS_DIR = Path(__file__).parent
TESTS_DIR_PARENT = (THIS_DIR / "..").resolve()

sys.path.insert(0, str(TESTS_DIR_PARENT))

# Optional, used to refer to fixtures in tests/fixtures/xxx:
pytest_plugins = [
    "tests.fixtures.xxx"
]
```

