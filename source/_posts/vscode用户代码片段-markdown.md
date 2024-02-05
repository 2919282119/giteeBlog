---
tilte: vscode用户代码片段——markdown
tags: markdown
---
### 1.设置——打开settings.json——在settings.json中修改配置允许markdown自动提示
```json
 "[markdown]": {
        "editor.quickSuggestions": {
            "comments": "on",
            "strings": "on",
            "other": "on"
        }
    }
```
### 2.用户代码片段——markdown.json中增加代码段
```json
	"markdown头部模板": {
		"prefix": "md",
		"body": [
			"---",
			"tilte: $1",
			"tags: $0"
			"---"
		]
	},
	"多行代码段": {
	    "prefix": "bl",
		"body": [
			"```$1",
			"$0"
			"```"
		]
	}
```