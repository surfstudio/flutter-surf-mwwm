# Surf MWWM

[![Build Status](https://shields.io/github/actions/workflow/status/surfstudio/flutter-surf-mwwm/workflow.yaml?logo=github&logoColor=white)](https://github.com/surfstudio/SurfGear/tree/main/packages/surf_mwwm)
[![Coverage Status](https://img.shields.io/codecov/c/github/surfstudio/SurfGear?flag=surf_mwwm&logo=codecov&logoColor=white)](https://codecov.io/gh/surfstudio/SurfGear)
[![Pub Version](https://img.shields.io/pub/v/surf_mwwm?logo=dart&logoColor=white)](https://pub.dev/packages/surf_mwwm)
[![Pub Likes](https://badgen.net/pub/likes/surf_mwwm)](https://pub.dev/packages/surf_mwwm)
[![Pub popularity](https://badgen.net/pub/popularity/surf_mwwm)](https://pub.dev/packages/surf_mwwm/score)
![Flutter Platform](https://badgen.net/pub/flutter-platform/surf_mwwm)

This package made by [Surf](https://surf.ru/).

## Description

Reflection of widget in a single entity

## Installation

Add `surf_mwwm` to your `pubspec.yaml` file:

```yaml
dependencies:
  surf_mwwm: ^1.0.0
```

You can use both `stable` and `dev` versions of the package listed above in the badges bar.

## Example

1. Create WM class

```dart
class CounterWidgetModel extends WidgetModel {
  CounterWidgetModel(
    WidgetModelDependencies dependencies,
    this.navigator,
    this._key,
  ) : super(dependencies);

  final NavigatorState navigator;
  final GlobalKey<ScaffoldState> _key;

  StreamedState<int> counterState = StreamedState(0);

  final incrementAction = VoidAction();
  final showInit = StreamedAction<int>();

  @override
  void onLoad() {
    _listenToActions();
    super.onLoad();
  }

  void _listenToActions() {
    incrementAction.bind((_) {
      counterState.accept(counterState.value + 1);
    }).listenOn(
      this,
      onValue: (_) {},
    );

    showInit.bind((_) {
      ScaffoldMessenger.of(_key.currentContext!).showSnackBar(
        const SnackBar(
          content: Text('init'),
        ),
      );
    }).listenOn(this, onValue: (_) {});

    counterState.stream.where((c) => c.isEven).skip(1).listenOn(
      this,
      onValue: (c) {
        navigator.push(
          MaterialPageRoute<void>(
            builder: (ctx) => Scaffold(
              body: Column(
                children: [
                  TextField(
                    autofocus: true,
                    onChanged: (_) {},
                  ),
                ],
              ),
            ),
          ),
        );
      },
    );
  }
}
```

2. Create Screen

```dart
class CounterScreen extends MwwmWidget<CounterComponent> {
  CounterScreen({Key? key})
      : super(
          key: key,
          dependenciesBuilder: (context) =>
              CounterComponent(Navigator.of(context)),
          widgetStateBuilder: () => _CounterScreenState(),
          widgetModelBuilder: createCounterModel,
        );
}

class _CounterScreenState extends OldWidgetState<CounterWidgetModel> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      key: context.getComponent<CounterComponent>().scaffoldKey,
      appBar: AppBar(
        title: const Text('Counter Demo'),
      ),
      body: StreamBuilder(
        stream: wm.counterState.stream,
        initialData: 0,
        builder: (context, snapshot) {
          return Center(
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                const Text('You have pushed the this many times:'),
                Text(
                  '${snapshot.data}',
                  style: Theme.of(context).textTheme.caption,
                ),
                TextField(
                  autofocus: true,
                  onChanged: (_) {},
                ),
              ],
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: wm.incrementAction,
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

3. Create Route

```dart
class CounterScreenRoute extends MaterialPageRoute<void> {
  CounterScreenRoute() : super(builder: (ctx) => CounterScreen());
}
```

## Changelog

All notable changes to this project will be documented in [this file](./CHANGELOG.md).

## Issues

For issues, file directly in the Issues section.

## Contribute

If you would like to contribute to the package (e.g. by improving the documentation, solving a bug or adding a cool new feature), please review our [contribution guide](../../CONTRIBUTING.md) first and send us your pull request.

Your PRs are always welcome.

## How to reach us

Please feel free to ask any questions about this package. Join our community chat on Telegram. We speak English and Russian.

[![Telegram](https://img.shields.io/badge/chat-on%20Telegram-blue.svg)](https://t.me/SurfGear)

## License

[Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)
