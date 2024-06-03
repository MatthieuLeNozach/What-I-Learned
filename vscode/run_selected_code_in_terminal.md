# **Run selected code in the active terminal**

Works with SQL in duckdb, psql, with bash...



<br><br>


---
**Contents**
- [**Run selected code in the active terminal**](#run-selected-code-in-the-active-terminal)
  - [**Edit the `keybindings.json` file**](#edit-the-keybindingsjson-file)
  - [**Use the shortcut**](#use-the-shortcut)


---

## **Edit the `keybindings.json` file**

In VScode, type `ctrl+maj+p`, and enter `open Keyboard Shortcuts (JSON)

add these lines:  

```json
    {
        "key": "ctrl+alt+k",
        "command": "workbench.action.terminal.runSelectedText",
    }
```


## **Use the shortcut**

Select some code (sql, terminal commands)
Type `ctrl+alt+k`