# personal

## view

the site is live at

```url
https://froggycroak.github.io/ProfileHQ/
```

## jekyll

```cmd
bundle exec jekyll serve
```

## 图床

- 软件工具
  - picgo
  - ossutil

## cautions

_includes\head.html

```html
<link rel="stylesheet" href="{{ 'assets/main.css' | relative_url }}">
```

注意使用单引号，避免vscode的formatOnSave擅自在双引号内添加空格(%20)
