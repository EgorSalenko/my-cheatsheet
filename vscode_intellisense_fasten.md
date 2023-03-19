# Edit Quick Suggestion Setting in VSCode

1. Search for *settings.json* and add following lines:

```
  "editor.quickSuggestions": {
    "other": true,
    "comments": true,
    "strings": true
  },
  "editor.quickSuggestionsDelay": 10,
```

2. Next change the Microsoft C++ Extension Intellisense Engine to Tag Parser:

```
Extensions-> C/C++ -> Gear Icon -> Extension Settings
```

3. In Search bar tape `engine`

4. Change *C_pp Intellisense Engine* to `Tag Parser`
