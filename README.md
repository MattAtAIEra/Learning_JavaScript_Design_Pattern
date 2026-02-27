# Learning JavaScript Design Patterns

一套以**分層架構**組織的 JavaScript / TypeScript 設計模式教學系列，從模組基礎到團隊協作共 13 份 SKILL.MD，涵蓋 GoF 經典模式與現代前後端實務。

## 目錄結構

```
skills/
├── 00-overview-architect-decision-flow.md   # 全局地圖：分層架構 & 閱讀順序
├── 01-foundation-modules-and-namespaces.md  # 模組、命名空間、專案佈局
├── 02-object-creation-layer.md              # Factory, Builder, Singleton, Prototype
├── 03-shared-utilities-and-functional-core.md # Filter, Accumulator, Immutable
├── 04-io-and-infrastructure-adapters.md     # Adapter, Facade, Proxy
├── 05-cross-cutting-concerns-aop-and-decorators.md # Decorator, AOP, Middleware
├── 06-dependency-injection-and-ioc-container.md    # DI / IoC 容器
├── 07-inter-component-communication.md      # Observer, Mediator, PubSub, Command
├── 08-state-management-and-business-logic.md # State, Memento, Visitor, Template
├── 09-application-layer-mvc-mvp-mvvm.md     # MVC / MVP / MVVM
├── 10-async-concurrency-and-resilience.md   # Promise, async/await, Web Workers
├── 11-testing-strategy-across-layers.md     # AAA, Mock, Spy, Stub
└── 12-team-framework-integration.md         # 團隊慣例與 onboarding
```

## 如何讓 AI Agent 在開發時參考這些設計模式

本專案最大的用途之一，是作為 **AI 輔助開發時的設計模式知識庫**。以下是幾種實際整合方式：

### 方式一：Claude Code — 寫入 `CLAUDE.md`

在你的專案根目錄的 `CLAUDE.md` 中加入引用指示：

```markdown
## Design Pattern 指導原則

在撰寫或重構程式碼時，請參考以下設計模式知識庫作為架構決策依據：
https://github.com/MattAtAIEra/Learning_JavaScript_Design_Pattern

關鍵原則：
- 物件建立使用 Factory / Builder，避免裸 new（參考 skills/02）
- 外部服務一律透過 Adapter 隔離（參考 skills/04）
- 跨層溝通使用 Observer / Mediator，禁止直接耦合（參考 skills/07）
- 商業邏輯集中於 Domain Layer，使用 State Pattern 管理狀態（參考 skills/08）
- 依賴注入優先，由 Composition Root 統一組裝（參考 skills/06）
```

Claude Code 每次對話都會載入 `CLAUDE.md`，因此 Agent 在寫程式時會自動遵循這些原則。

### 方式二：Claude Code — 作為自訂 Slash Command

將 skill 檔案設為可查閱的 slash command。在專案中建立 `.claude/commands/design-pattern.md`：

```markdown
請根據以下設計模式指引，審查目前的程式架構並提出改善建議：

$ARGUMENTS

參考知識庫：
- 分層架構總覽：skills/00-overview-architect-decision-flow.md
- 物件建立層：skills/02-object-creation-layer.md
- 通訊層：skills/07-inter-component-communication.md
- 狀態管理：skills/08-state-management-and-business-logic.md
```

使用時輸入 `/design-pattern 請檢查 src/services/ 的依賴關係是否合理`。

### 方式三：Cursor / Windsurf — Rules 檔案

在 Cursor 的 `.cursor/rules/design-patterns.mdc` 或 Windsurf 的 `.windsurfrules` 中：

```markdown
---
description: 設計模式架構決策指引
globs: ["src/**/*.ts", "src/**/*.js"]
---

# Design Pattern 指導原則

架構決策請參考 skills/ 目錄下的設計模式教學：

1. **模組邊界**（skills/01）：每個模組職責單一，透過明確介面暴露功能
2. **物件建立**（skills/02）：複雜物件使用 Builder，家族物件使用 Abstract Factory
3. **基礎設施隔離**（skills/04）：DB、HTTP、檔案系統一律透過 Adapter 抽象
4. **橫切關注點**（skills/05）：logging、認證、快取使用 Decorator 或 AOP
5. **依賴注入**（skills/06）：避免 service locator，在 Composition Root 統一組裝
6. **元件通訊**（skills/07）：跨模組使用 Event Bus，複雜協調使用 Mediator
7. **狀態管理**（skills/08）：有限狀態用 State Pattern，歷程追蹤用 Memento
8. **非同步處理**（skills/10）：統一錯誤處理，實作 retry + circuit breaker
```

### 方式四：GitHub Copilot — 自訂指令

在 `.github/copilot-instructions.md` 中引用：

```markdown
## 架構原則

本專案遵循分層架構設計模式，詳見 skills/ 目錄。
程式碼審查與生成時請遵守以下規範：

- 不允許在 Domain Layer 直接呼叫外部 API（違反 skills/04 Adapter 原則）
- 所有狀態轉換必須透過 State Pattern 明確定義（skills/08）
- 新增模組必須可透過 DI 注入，不可硬編碼依賴（skills/06）
```

### 方式五：直接 Clone 到專案中作為子目錄

```bash
# 作為 git submodule
git submodule add https://github.com/MattAtAIEra/Learning_JavaScript_Design_Pattern.git docs/design-patterns

# 或直接複製 skills/ 到專案
cp -r Learning_JavaScript_Design_Pattern/skills/ your-project/docs/design-patterns/
```

然後在 AI Agent 的設定檔中指向該目錄，讓 Agent 在需要時自行查閱。

## 閱讀順序

```
00 全局地圖
 │
 ├── 01 ~ 05  基礎層（可平行閱讀）
 │
 └── 06 依賴注入（需先讀完 01-05）
      │
      ├── 07 ~ 11  應用層（可平行閱讀）
      │
      └── 12 團隊整合（需讀完全部）
```

## 授權

本專案中的 SKILL.MD 教學內容為原創整理。設計模式的程式範例參考自 *Mastering JavaScript Design Patterns, Second Edition*（Packt），書籍原始碼與 PDF 不包含在本 repo 中。
