# Flutter Riverpod 3（NotifierProvider + ConsumerWidget）

## 简介

用 Riverpod 3 的 **`Notifier` + `NotifierProvider`** 做计数器，`ProviderScope` 包住应用根，`ConsumerWidget` 在 `build` 里拿到 `WidgetRef`，用 `watch`/`read` 订阅或触发更新。

## 快速开始

### 环境要求

Flutter SDK（本项目使用 Riverpod 3.x API）。

### 运行

```bash
flutter pub get
flutter run
```

## 概念讲解

### 第一部分：`NotifierProvider` 与 `Notifier`

`CounterNotifier` 继承 `Notifier<int>`，`build()` 返回初始值，`increment` 里改 `state`。Provider 用 `CounterNotifier.new` 注册工厂，由 Riverpod 管理生命周期。

### 第二部分：为什么在 UI 用 `ConsumerWidget`

旧写法在 `StatelessWidget.build` 里没有 `ref`，按钮里无法 `read`。`ConsumerWidget` 的 `build(BuildContext, WidgetRef ref)` 同时给上下文和 `ref`，`watch(counterProvider)` 重建，`read(...).increment()` 发动作。

```dart
final counterProvider = NotifierProvider<CounterNotifier, int>(CounterNotifier.new);

class CounterNotifier extends Notifier<int> {
  @override
  int build() => 0;
  void increment() => state++;
}
```

## 完整示例

见 `lib/main.dart`：`ProviderScope`、`MaterialApp`、标题栏与 FAB 均已接好。

## 注意事项

- Riverpod 2 的 `StateNotifierProvider` 等与 3.x 不兼容，请以本 Demo 的写法为准对照官方迁移说明。
- 大项目里可把 provider 拆到单独文件，再配合 `keepAlive`、异步 `AsyncNotifier` 等。

## 完整讲解（中文）

Riverpod 的核心吸引力是：**依赖关系像图一样声明清楚，测试时也好替换**。`Notifier` 把「最小业务状态 + 修改入口」捆在一起，`ConsumerWidget` 把「谁要看这份状态」写在明面上。比起到处传 `BuildContext`、到处 `findAncestorStateOfType`，可读性通常更好。计数器虽然小，但模式与「用户信息、购物车、主题」等大状态一致，属于值得背下来的样板。
