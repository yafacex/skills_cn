---
name: webapp-testing
description: 使用 Playwright 与本地 Web 应用交互并进行测试的工具包。支持验证前端功能、调试 UI 行为、捕获浏览器截图以及查看浏览器日志。
license: Complete terms in LICENSE.txt
---

# Web 应用测试

测试本地 Web 应用时，编写原生 Python Playwright 脚本。

**可用辅助脚本：**
- `scripts/with_server.py` - Manages server lifecycle (supports multiple servers)

**务必先用 `--help` 运行脚本**以查看用法。除非先尝试运行后发现确实需要定制化方案，否则不要阅读源码。这些脚本可能很大，会污染上下文窗口；它们被设计为黑盒直接调用，而不是读入上下文。

## 决策树：选择你的方式

```
User task → Is it static HTML?
    ├─ Yes → Read HTML file directly to identify selectors
    │         ├─ Success → Write Playwright script using selectors
    │         └─ Fails/Incomplete → Treat as dynamic (below)
    │
    └─ No (dynamic webapp) → Is the server already running?
        ├─ No → Run: python scripts/with_server.py --help
        │        Then use the helper + write simplified Playwright script
        │
        └─ Yes → Reconnaissance-then-action:
            1. Navigate and wait for networkidle
            2. Take screenshot or inspect DOM
            3. Identify selectors from rendered state
            4. Execute actions with discovered selectors
```

## 示例：使用 with_server.py

启动服务时先运行 `--help`，再使用该辅助脚本：

**Single server:**
```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_automation.py
```

**Multiple servers (e.g., backend + frontend):**
```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_automation.py
```

编写自动化脚本时，只包含 Playwright 逻辑（服务生命周期由脚本自动管理）：
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True) # Always launch chromium in headless mode
    page = browser.new_page()
    page.goto('http://localhost:5173') # Server already running and ready
    page.wait_for_load_state('networkidle') # CRITICAL: Wait for JS to execute
    # ... your automation logic
    browser.close()
```

## 先侦察再行动（Reconnaissance-Then-Action）模式

1. **Inspect rendered DOM**:
   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. 从检查结果中**确定 selector**

3. 使用已确定的 selector **执行操作**

## 常见坑

❌ **Don't** inspect the DOM before waiting for `networkidle` on dynamic apps
✅ **Do** wait for `page.wait_for_load_state('networkidle')` before inspection

## 最佳实践

- **把内置脚本当黑盒使用**：完成任务前先考虑 `scripts/` 里是否有脚本能帮忙。这些脚本能可靠处理常见复杂流程而不污染上下文。先用 `--help` 看用法，再直接调用。
- 同步脚本使用 `sync_playwright()`
- 结束时务必关闭浏览器
- 使用可读性更强的 selector：`text=`、`role=`、CSS selector 或 ID
- 加上合适的等待：`page.wait_for_selector()` 或 `page.wait_for_timeout()`

## 参考文件

- **examples/** - Examples showing common patterns:
  - `element_discovery.py` - Discovering buttons, links, and inputs on a page
  - `static_html_automation.py` - Using file:// URLs for local HTML
  - `console_logging.py` - Capturing console logs during automation
