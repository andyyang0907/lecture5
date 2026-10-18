# 生命周期日志：钩子顺序 + r/R 行为对照记录

项目：`counter_deep`　打点文件：`lib/main.dart`（`_MyHomePageState`）

## 1. 打点位置

在 `_MyHomePageState` 的 7 个钩子和 `build` 中各加一行 `print`，统一前缀 `[lifecycle]`：

| 钩子 | 触发时机 |
|---|---|
| `initState` | State 对象创建后仅一次 |
| `didChangeDependencies` | `initState` 之后；依赖的 InheritedWidget 变化时 |
| `didUpdateWidget` | 父组件重建并传入同类型新 Widget 配置时 |
| `reassemble` | 热重载（hot reload）时 |
| `deactivate` | State 从组件树中临时/永久移除时 |
| `dispose` | State 永久销毁前 |
| `build` | 每次需要重绘 UI 时 |

## 2. 预期钩子顺序（理论）

| 操作 | 预期日志序列 | State 是否保留 |
|---|---|---|
| 首次启动 | `initState` → `didChangeDependencies` → `build` | 新建 |
| 点 + 按钮（`setState`） | 仅 `build` | 保留（`_counter` 累加） |
| 热重载（控制台按 `r`） | `reassemble` → `build` | **保留**（`_counter`、`_step` 不变） |
| 热重启（控制台按 `R`） | `initState` → `didChangeDependencies` → `build`（重新走完整创建流程） | **丢失**（`_counter` 归零、`_step` 复位为 1） |

补充说明：

- `didUpdateWidget` 在本 Demo 中一般观察不到：父组件传给 `MyHomePage` 的 `title` 从未变化。
- 热重启不保证走正常卸载流程，`deactivate` / `dispose` 是否打印以实际观察为准。
- 退出应用时进程直接结束，`dispose` 的日志可能来不及输出。

## 3. 实际观察记录（运行后填写）

运行方式：项目根目录执行 `flutter run -d macos`（或 `-d chrome`，或 IDE 直接运行），在运行控制台按 `r` = 热重载、`R` = 热重启。

| 操作 | 控制台实际输出（按顺序粘贴） | State 是否保留（计数器变化） | 与预期是否一致 |
|---|---|---|---|
| 首次启动 | | | |
| 点 + 按钮 3 次 | | | |
| 按 `r`（热重载） | | | |
| 按 `R`（热重启） | | | |
| （选做）改步长为 5 后按 `r` | | | |

## 4. 结论（根据实际观察总结）

- 热重载能保留状态的原因：
- 热重启会重置状态的原因：
- 从日志顺序看 `build` 与其他钩子的关系：
