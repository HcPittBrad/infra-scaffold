# PLAN — How（灵魂：可验收的执行步骤）

> 一句话定位：**把 DESIGN 里的功能点，拆成 AI agent 能一步步执行并验收的 Phase。**
> Phase = 原子执行单元，每个 Phase 结束必须有人工或命令可验证的 checklist。
> 没有验收标准的 Phase 不是合格的 Phase。

---

## 前置条件

- [ ] [环境依赖1，如：Python 3.11+]
- [ ] [环境依赖2，如：Node.js 20+]
- [ ] `.env` 已配置（参考 `.env.example`）

---

## Phase 1：[名称]（覆盖功能点 #N）

> 目标：[一句话，这个 Phase 结束后能做到什么]

### 步骤

[做什么，具体到 agent 能直接执行的命令和文件]

```bash
# 关键命令
```

### 验收 ✅（主线——没过不进下一个）

```bash
# agent 可自闭环验证的命令
# 期望输出：[精确描述]
```

- [ ] [验收项1]
- [ ] [验收项2]

---

## Phase 2：[名称]（覆盖功能点 #N）

> 目标：[...]

### 步骤

[...]

### 验收 ✅

- [ ] [验收项]

---

## 测试策略

> 规则：Step A → Step C 由 agent 自闭环执行；Step B 仅针对无法在浏览器/CLI 模拟的系统级行为（全局快捷键、原生粘贴、系统托盘等）才需要人工。

### Step A：后端接口验证（agent 自闭环，Phase 1 后执行）

> **灵魂：模拟业务逻辑链路，不是挨个测接口。**
> 一条 curl 链 = 一个完整业务流程。关注的是"用户做了一件事之后，系统状态是否正确变化"，而不是"这个接口返回了 200"。

**写法原则：**
- 用 curl 串联多个接口，模拟一个完整的用户操作序列
- 每一步用 `python3 -c "assert ..."` 断言，任意一步失败则链路断掉
- 覆盖：正向流程 + 状态变化验证 + 至少一个边界情况

```bash
# ── 业务链路示例：[功能名] 完整流程 ──────────────────────────────
# Step 1：触发入口（如：创建资源、触发计算）
RESULT=$(curl -s -X POST http://localhost:[port]/[step1] \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}')
python3 -c "import sys,json; d=json.loads('$RESULT'); assert d['field'], 'step1 failed'"

# Step 2：验证状态变化（如：查询列表确认资源已入库）
curl -s http://localhost:[port]/[step2] \
  | python3 -c "import sys,json; d=json.load(sys.stdin); assert d['total'] > 0, 'not saved'"

# Step 3：执行操作（如：更新、判断、触发副作用）
curl -s -X POST http://localhost:[port]/[step3] \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}' \
  | python3 -c "import sys,json; d=json.load(sys.stdin); assert d['result_field'], 'step3 failed'"

# ── 边界情况（独立测，不串链路）────────────────────────────────
# 常见边界：空字符串 / 超长文本（>2000字） / 特殊字符 / 重复提交
curl -s -X POST http://localhost:[port]/[endpoint] \
  -H "Content-Type: application/json" \
  -d '{"key": ""}' \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print('边界ok:', list(d.keys()))"
```

**验收标准**：每条业务链路断言全部通过，状态变化符合预期，无 500 错误。

---

### Step B：人工验收（仅系统级行为，Phase 最后执行）

只有以下情况才需要人工点：

```
❌ 不可自动化（需真实 OS/native runtime）：
  - 全局快捷键（Cmd+Option+X）
  - paste_text 粘贴到外部 app
  - 系统托盘菜单
  - 原生文件对话框

✅ 其余都可以自动化（Step A / Step C）
```

人工验收清单模板（只写真正需要点的）：
```
[ ] 快捷键 [X] → 窗口出现
[ ] 回车后文字粘贴到 [外部 app] 光标处
[ ] 托盘菜单 → [功能] 正常
```

---

### Step C：自动化 UI 测试（agent 自闭环，前端 Phase 完成后执行）

> **灵魂：模拟用户操作路径，不是测组件能不能渲染。**
> 一个 test = 一个用户意图，从"用户做了什么"到"用户看到了什么"。关注的是交互流程是否正确，而不是元素是否存在。

**两层分工：**
- **Vitest 单元测**：mock API 层，测"用户操作 → 正确调用了哪个函数 → 组件状态正确变化"
  - 例：用户输入文字 + 按 Enter → `api.convert` 被调用 → 结果渲染在 UI 上
- **Playwright E2E 测**：真实 HTTP 调后端，测"用户完成一个完整交互流程 → 数据库/状态实际更新"
  - 例：用户在 Review 页面输入答案 → 点击提交 → 后端 SM-2 更新 → stats 页面 total 变化

**写法原则：**
```ts
// ✅ 好的测试：描述用户意图，验证可见结果
it("用户输入中英混合文字按 Enter 后，转换结果出现在屏幕上", async () => {
  await userEvent.type(input, "我想 book a meeting");
  await userEvent.keyboard("{Enter}");
  await waitFor(() => expect(screen.getByText(/I want to/)).toBeInTheDocument());
});

// ❌ 坏的测试：只测组件存在，不测交互
it("输入框存在", () => {
  expect(screen.getByPlaceholderText(/输入/)).toBeInTheDocument();
});
```

#### 安装

```bash
pnpm add -D vitest @testing-library/react @testing-library/user-event \
           @testing-library/jest-dom jsdom @playwright/test
pnpm exec playwright install chromium
```

#### 必须创建的三个文件

**`src/test/setup.ts`** — mock 所有 native/Tauri API：
```ts
import "@testing-library/jest-dom";
import { vi } from "vitest";

vi.mock("@tauri-apps/api/core", () => ({ invoke: vi.fn().mockResolvedValue(undefined) }));
vi.mock("@tauri-apps/api/window", () => ({
  getCurrentWindow: () => ({ hide: vi.fn(), show: vi.fn(), setFocus: vi.fn() }),
}));
vi.mock("@tauri-apps/api/event", () => ({
  listen: vi.fn().mockResolvedValue(() => {}),
  emit:   vi.fn().mockResolvedValue(undefined),
}));
// 其他 native 插件在此 mock（clipboard、global-shortcut 等）
```

**`playwright.config.ts`**（项目根 / ui/ 目录下）— 必须存在，否则 Playwright 加载 Vitest matchers 冲突崩溃：
```ts
import { defineConfig, devices } from "@playwright/test";
export default defineConfig({
  testDir: "./src/test/e2e",
  fullyParallel: false,
  workers: 1,
  reporter: "line",
  use: { baseURL: "http://localhost:[vite-port]", trace: "on-first-retry" },
  projects: [{ name: "chromium", use: { ...devices["Desktop Chrome"] } }],
});
```

**`vite.config.ts`** test 块——排除 e2e 目录（e2e 由 Playwright 跑，不能混入 Vitest）：
```ts
test: {
  globals: true,
  environment: "jsdom",
  setupFiles: ["./src/test/setup.ts"],
  exclude: [...configDefaults.exclude, "src/test/e2e/**"],
},
```

#### Tauri mock 写法：单元测试 vs E2E 差异

| 场景 | 方式 |
|------|------|
| Vitest 单元测试 | `vi.mock("@tauri-apps/api/core", ...)` 在 `setup.ts` |
| Playwright E2E | `page.addInitScript(() => { window.__TAURI_INTERNALS__ = { invoke: () => Promise.resolve() }; window.__TAURI__ = { ... } })` |

E2E stub 函数模板（每个 `test.beforeEach` 调用）：
```ts
async function stubTauriApis(page: Page) {
  await page.addInitScript(() => {
    // @ts-ignore
    window.__TAURI_INTERNALS__ = { invoke: () => Promise.resolve(), transformCallback: (cb) => cb };
    // @ts-ignore
    window.__TAURI__ = {
      invoke: () => Promise.resolve(),
      event:  { listen: () => Promise.resolve(() => {}), emit: () => Promise.resolve() },
      window: { getCurrent: () => ({ hide: () => Promise.resolve(), show: () => Promise.resolve() }) },
    };
  });
}
```

#### 已知踩坑

| 坑 | 现象 | 解法 |
|----|------|------|
| `playwright.config.ts` 不存在 | `TypeError: Cannot redefine property: Symbol($$jest-matchers-object)` 崩溃 | 必须创建，哪怕最简版本 |
| Playwright strict mode | `getByText` 匹配到多个元素直接报错 | 用更精确的前缀，如 `/共.*个词/` 而不是 `/个词/`；不用 `first()` 绕过 |
| E2E 里 Tauri invoke 未定义 | 组件挂载时报 `window.__TAURI_INTERNALS__ is not defined` | 用 `addInitScript` 注入 stub，且必须在 `goto` 之前调用 |
| selector 跨 span 文本 | `<div>共 <span>5</span> 个词</div>` → `getByText` 用 div 的 textContent 匹配，regex 可穿透 | 用 `/共.*个词/` 匹配整个 div 的 textContent |
| vite.config 未排除 e2e | Vitest 尝试跑 `.spec.ts` 文件，与 Playwright 格式不兼容 | `exclude: [...configDefaults.exclude, "src/test/e2e/**"]` |

#### 运行命令

```bash
pnpm vitest run                  # 单元测试（无需后端/dev server）
pnpm exec playwright test        # E2E（需后端 + vite dev server 均已启动）
```

**验收标准**：`vitest run` 全绿 + `playwright test` 全绿，只留 Step B 人工清单。

---

## 多端打包与部署（Tauri v2 + Vite）

> 同一份代码，按需选择输出端。Tauri v2 原生支持 Mac/Windows/Linux/iOS/Android；H5 是独立 Vite 构建。
> **PLAN 里必须标注每个功能在哪些端可用**，原生能力（全局快捷键、剪贴板模拟、系统托盘）只在桌面端存在。

### 功能 × 端 矩阵（每个项目填写）

| 功能 | Mac | Windows | iOS | Android | H5/浏览器 |
|------|-----|---------|-----|---------|-----------|
| [核心 AI 功能] | ✅ | ✅ | ✅ | ✅ | ✅ |
| 全局快捷键 | ✅ | ✅ | ❌ | ❌ | ❌ |
| 自动粘贴到光标 | ✅ | ✅ | ❌ | ❌ | ❌ |
| 系统托盘 | ✅ | ✅ | ❌ | ❌ | ❌ |

> 打 ❌ 的功能在该端直接隐藏入口，不做降级兜底。

### 构建命令

```bash
# 桌面端（Mac .app / Windows .msi / Linux AppImage）
PATH="$HOME/.cargo/bin:$PATH" pnpm tauri build

# 移动端（需先配置 iOS/Android toolchain）
pnpm tauri ios build
pnpm tauri android build

# H5 / 浏览器（纯静态，后端需单独部署到云）
pnpm build   # 输出 dist/，nginx 或 Vercel 直接托管
```

### 前置准备（首次运行前检查）

```bash
# 检查桌面端工具链
cargo --version   # 找不到则：curl https://sh.rustup.rs -sSf | sh
pnpm --version

# 检查移动端（按需）
xcode-select --version   # iOS
# Android：需 Android Studio + NDK，见 Tauri 官方文档

# 端口冲突处理（Vite 默认 1420）
kill $(lsof -ti:[port]) 2>/dev/null
```

### 已知踩坑

| 坑 | 现象 | 解法 |
|----|------|------|
| 端口被占（上次 dev server 未退出） | `Error: Port [x] is already in use` | `kill $(lsof -ti:[port])` 再重启 |
| cargo 不在 PATH | `command not found: cargo` | `PATH="$HOME/.cargo/bin:$PATH"` 前缀，或写入 `.zshrc` |
| macOS 辅助功能权限 | enigo 键盘模拟无效，粘贴不生效 | 系统设置 → 隐私与安全 → 辅助功能 → 添加 app |
| Tauri dev 和 Vite dev 冲突 | `pnpm tauri dev` 内部自己起 Vite，不要单独跑 `pnpm dev` | 只跑 `pnpm tauri dev`，不要同时跑 `pnpm dev` |

### 验收清单（按端）

```
桌面端：
[ ] pnpm tauri build 无报错，输出 .app / .msi
[ ] 全局快捷键正常触发
[ ] paste_text 粘贴到外部 app 光标处正常
[ ] 系统托盘菜单可点击

H5 端：
[ ] pnpm build 无报错，输出 dist/
[ ] 核心 AI 功能（translate/explain/review）在浏览器正常
[ ] 原生功能入口（快捷键/粘贴）在 H5 版本不显示
```

---

## 最终验收：README

> README 写清楚了 = 产品价值成立 = 可以给别人用了。

- [ ] 这是什么，解决什么痛点（对应 CONTEXT 核心痛点）
- [ ] 怎么安装
- [ ] 怎么用
- [ ] 怎么配置

README 是最终把关，不能通过则说明功能或文档有缺失。
