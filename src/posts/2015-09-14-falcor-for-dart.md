---
title: Falcor for dart
lang: en
aliases: [2015/09/14/falcor-for-dart]
---

[Rasmus](https://rasmus.eneman.eu) and I have for a while been working on to port the
[FalcorJS router](http://netflix.github.io/falcor) built by Netflix to Dart.

We have been working hard to get all the FalcorJS tests to pass in Dart and we are finally
proud to announce that the Dart version is ready and it is available at
[pub](https://pub.dartlang.org/packages/falcor_dart). But keep in mind, as the Falcor team states
in their repository: **This release is a developer preview**.

For those who are interested at helping out or looking at the source it is available at [Github](https://github.com/Pajn/falcor_dart).

It also provides a binding to shelf which is optional to use but easy to get started with.

Example using shelf:
```dart

import 'package:shelf/shelf.dart' as shelf;
import 'package:shelf/shelf_io.dart' as io;

import 'package:falcor_dart/router.dart';
import 'package:falcor_dart/shelf.dart';

/// Example using shelf binding
main() async {
  final todoService = new TodosService();

  final falcorHandler = await createFalcorHandler((_) {
    return new Router([
      route('todos.name', get: (_) async {
        var todoList = await todoService.getTodoList();

        return {
          'path': ['todos', 'name'],
          'value': todoList.map((todo) => todo['name']),
        };
      }),
      route('todos.length', get: (_) async {
        var todoList = await todoService.getTodoList();

        return {
          'path': ['todos', 'length'],
          'value': todoList.length,
        };
      })
    ]);
  });

  var handler = const shelf.Pipeline()
      .addMiddleware(shelf.logRequests())
      .addHandler(falcorHandler);

  var server = await io.serve(handler, 'localhost', 8080);

  print('Serving at http://${server.address.host}:${server.port}');
}

class TodosService {
  getTodoList() async {
    return [
      {'name': 'Create Router'},
      {'name': 'Create request handler'},
      {'name': 'Send requests'},
    ];
  }
}
```

And making the following request attempts to retrieve the name and length of the todos list:

`http://localhost:8080/?paths=[["todos", "name"]]&method=get`

The following route will return this response:
```dart
{
  "jsonGraph": {
    "todos": {
      "name": [
        "Create Router",
        "Create request handler",
        "Send requests"
      ]
    }
  }
}
```

If we want to get the length we can make this request:
`http://localhost:8080/?paths=[["todos", "length"]]`

Which will return:
```dart
{
  "jsonGraph": {
    "todos": {
      "length": 3
    }
  }
}
```

And if we want to match both we can make the following request:

`http://localhost:8080/?paths=[["todos", ["name", "length"]]]`

It will result in this response:
```dart
{
  "jsonGraph": {
    "todos": {
      "name": [
        "Create Router",
        "Create request handler",
        "Send requests"
      ],
      "length": 3
    }
  }
}
```

Pretty easy, huh? Just grab it and try it yourselves and I hope you like using falcor router!
