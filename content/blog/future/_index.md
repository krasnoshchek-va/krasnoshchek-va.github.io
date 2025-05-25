---
date: '2025-05-25T21:38:55+10:00'
draft: false 
title: 'Dart: Future'
summary: "В отличие от синхронной операции, асинхронная не может предоставить результат немедленно. Асинхронной операции может потребоваться дождаться выполнения других внешних операций (Например, выполнения сетевого запроса). Вместо блокировки потока, асинхронная операция немедленно возвращает объект типа **Future**, который представляет собой результат этой операции."
---

![Ожидание](./aren-nagulyan-48Uy6kNxctw-unsplash.jpg)
Photo by [Aren Nagulyan](https://unsplash.com/@arennagulyan_photo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) on [Unsplash](https://unsplash.com/photos/a-man-carrying-a-brown-briefcase-on-a-train-track-48Uy6kNxctw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)


В отличие от синхронной операции, асинхронная не может предоставить результат немедленно. Асинхронной операции может потребоваться дождаться выполнения других внешних операций (Например, выполнения сетевого запроса). Вместо блокировки потока, асинхронная операция немедленно возвращает объект типа **Future**, который представляет собой результат этой операции. Он может иметь два состояния: завершенное и незавершенное.

Незавершенный future ожидает завершения асинхронной операции или выдачи ошибки.

При успехе future типа Future\<T\> завершается со значением типа T. Если future не возвращает значения для дальнейшего использования, то ее тип Future\<void\>.
Если асинхронная операция завершается неудачей, то future завершается с ошибкой.

Если нужно получить результат Future, то для этого есть два пути:
- Использовать Future API
- async / await

# Future API
Для каждого состояния Future можно зарегистрировать обратные вызовы `then(onValue)` для обработки значения и `catchError()`, для обработки ошибок, возникающих во время выполнения операции и в `then()`.

```Dart
myAsyncFunc().then(processValue).catchError(handleError);
```  

Цепочка из `then()` и `catchError()` может рассматриваться как грубый эквивалент `try-catch`

Future может иметь больше одной пары зарегистрированных обратных вызовов. Каждый последующий объект обрабатывается независимо и так, как если бы он был единственным.

С помощью `catcError()` можно обрабатывать специфичные ошибки. Для этого есть необязательный именованный параметр `test`, где можно выполнить проверку на определенный тип ошибки

```Dart
  myAsyncFunc()
      .then((_) => ...)
      .catchError(handleExceptionOne, test: (e) => e is ExceptionOne)
      .catchError(handleExceptionTwo, test: (e) => e is ExceptionTwo);
```

Помимо `catchError()` можно зарегистрировать обратный вызов `onError()`, но в отличие от первого, он обрабатывает ошибки, возникающие в `then()`

Так же можно определить обратный вызов `whenComplete()`, это эквивалент `finally` в `try-catch-finally`. Он вызывается, когда завершается его приемник, независимо от того, делает он это со значением или ошибкой.

```Dart
myAsyncFunc()
	.then(processValue)
	.catchError(handleError)
	.whenComplete(() => print("I work in any case"));
```

Если в `whenСomplete()` не появляется ошибка, то его Future завершается так же, как Future, на котором он был вызван.

## async и await
Ключевые слова `async` и `await` предоставляют декларативный способ определения асинхронных функций и использования их результатов. Есть два основных правила:
- Чтобы определить асинхронную функцию, нужно добавить `async` перед телом функции.
- Ключевое слово `await` работает только в асинхронных функциях.

Результатов функции, помеченной `async` является Future.

```Dart
Future<String> getAsyncGreeting() async {
	await Future.delayed(Duration(seconds: 2));
	return "Hello, World!";
}
```

Асинхронная функция выполняется синхронно до первого ключевого слова `await`. Это означает, что в теле асинхронной функции весь синхронный код до первого ключевого слова `await` выполняется немедленно.

```Dart
Future<void> printAsyncGreeting() async {
	print("Hi!");
	await Future.delayed(Duration(seconds: 2), () => print("Hola!"));
	print("Hallo!");
}
```


Отлавливать ошибки можно с помощью привычного `try-catch-finally`.
```Dart
try {
  greeting = await getAsyncGreeting();
  priint(greeting);
} catch (e) {
  // handle error
}
```

На самом деле `async` и `await` являются надстройками над Feature API.

Чтобы выявить распространенные ошибки, возникающие при работе с асинхронностью то можно включить следующие проверки линтера:
- [discarded_futures](https://dart.dev/tools/linter-rules/discarded_futures)
- [unawaited_futures](https://dart.dev/tools/linter-rules/unawaited_futures)
