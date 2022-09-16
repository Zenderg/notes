# Порождающие новые обзерваблы

### Содержание

* [ajax](#ajax)
* [bindCallback](#bindcallback)
* [bindNodeCallback](#bindnodecallback)
* [defer](#defer)
* [empty](#empty)
* [from](#from)
* [fromEvent](#fromevent)
* [fromEventPattern](#fromeventpattern)
* [generate](#generate)
* [interval](#interval)
* [of](#of)
* [range](#range)
* [throwError](#throwerror)
* [timer](#timer)
* [iif](#iif)

## ajax

Создает обзервабл из аджакс запроса.

Самое простое использование:
```js
ajax("https://api.github.com/users?per_page=5").subscribe((users) => {
  console.log(users)
})
```

 Если хочеца сразу получить данные из запроса в подписку, тогда:
 ```js
ajax.getJSON("https://api.github.com/users?per_page=5").subscribe((users) => {
  console.log(users)
})
 ```

 А еще его можно вот так вызывать, если нужна детализация к запросу:

 ```js
ajax({
  url: 'https://httpbin.org/delay/2',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'rxjs-custom-header': 'Rxjs'
  },
  body: {
    rxjs: 'Hello World!'
  }
})
 ```

> https://rxjs.dev/api/ajax/ajax

## bindCallback

???

> https://rxjs.dev/api/index/function/bindCallback

## bindNodeCallback

???

> https://rxjs.dev/api/index/function/bindNodeCallback

## defer

Принимает в себя параметром фабрику (по сути обычную функцию). На каждую подписку на defer использует эту фабрику (которая должна возвращать обзервабл) и генерирует обзервабл. Причем на каждую подписку оно генерирует отдельный обзервабл.

Используется как то так:
```js
const fromOrInterval = defer(() => {
  return Math.random() > 0.5
    ? from([1,2,3])
    : interval(1000);
});

fromOrInterval.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/index/function/defer


## empty

Создает пустой обзервабл. Он ничего не отправляет, а сразу завершается. Может быть полезно, когда, например, нужно вернуть обзервабл, и не важно какой.

В 8 версии вроде как будет задеприкейчено. Поэтому они советуют использовать вместо него обычную константу EMPTY.

```js
EMPTY.subscribe({
  next: () => console.log('Next'),
  complete: () => console.log('Complete!')
});

// Outputs
// Complete!
```

> ~~https://rxjs.dev/api/index/function/empty~~

> https://rxjs.dev/api/index/const/EMPTY

## from

Создает обзервабл из массивов, промисов, итерируемых и обзерваблоподобных (хз что это значит) объектов. Но в основном используется для массивов и генераторов.

Like this:
```js
const array = [10, 20, 30];
const result = from(array);

result.subscribe(x => console.log(x));

// Logs:
// 10
// 20
// 30
```

А еще в него можно передать вторым параметром кастомный скедулар.

> https://rxjs.dev/api/index/function/from

## fromEvent

Создает обзервабл на:
* DOM EventTarget - объект у которого есть методы `addEventListener` и `removeEventListener`.
* Node.js EventEmitter - у которого методы `addListener` и `removeListener`
* Джейквери стайл - методы `on` и `off`
* Node List - список дом элементов, которые получаются например из `document.querySelectorAll` или `Node.childNodes`
* HTML Collection - хз, по сути как то, что выше. Только видимо работает не с нодами из дома, а с хтмл коллекциями. Думаю это когда у тебя не объект возвращается какой то, а полноценный тег.

Как видно из обрабатываемых ивентов, главное чтобы у объекта было 2 заранее заданных метода на регистрацию и удаление обработчика.

При любой подписке на него регистрирует обработчик на заданный ивент, при отписке убирает обработчик соответственно. Если источник это несолько объектов (например `NodeList`), тогда он регистрирует обработчики на каждый объект по очереди, при отписке также по очереди убирает их.

Тип того:
```js
const clicks = fromEvent(document, 'click');
clicks.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/index/function/fromEvent
## fromEventPattern

Порождает ивенты из кастомных обработчиков. По сути нужен если `fromEvent` недостаточно и хочется более гибкой логики.

При подписке тригерит функцию регистратора ивента (на каждую подписку свою), которая передается первым параметром. При отписке тригерит функцию удаления обработчика, если такая была передана вторым параметром.

```js
function addClickHandler(handler) {
  document.addEventListener('click', handler);
}
 
function removeClickHandler(handler) {
  document.removeEventListener('click', handler);
}
 
const clicks = fromEventPattern(
  addClickHandler,
  removeClickHandler
);

clicks.subscribe(x => console.log(x));
```

Параметр `handler` в кастомных функция это функция в `subscribe`.

> https://rxjs.dev/api/index/function/fromEventPattern
## generate

Создает обзервабл, который может генерировать все что угодно. Просто сразу отправляет кучу ивентов по определенным условиям.

Очень похож на обычный `for` в джсе. Принимает в себя 5 параметров.
1. Начальное значение
2. Функцию условие. Эта функция должна возвращать булево значение. Вызываться и проверяться будет на каждой итерации.
3. Функцию степа. Должна возвращать новое значение для следующей итерации.
4. Опционально еще результирующую функцию, которая будет вызываться перед отправкой в подписки. Должна возвращать новое какое то значение.
5. Ну еще опционально скедулар кастомный, но это не столь важно.

```js
const result = generate(
  0,
  x => x < 3,
  x => x + 1,
  x => x
);

result.subscribe(x => console.log(x));

// Logs:
// 0
// 1
// 2
```

А еще можно вот в таком виде все это делать:
```js
generate({
  initialState: 0,
  condition(value) { return value < 3; },
  iterate(value) { return value + 1; },
  resultSelector(value) { return value * 1000; }
})
```

> https://rxjs.dev/api/index/function/generate

## interval

Создает обзервабл, который отправляет бесконечную последовательность (которая начинается с 0 и каждый раз увеличивается на 1) с задержкой в заданных миллисекундах.

По дефолту использует `asyncScheduler`.

```js
const numbers = interval(1000);
 
numbers.subscribe(x => console.log(x));
 
// Logs:
// 0
// 1
// 2
// 3
// ...
```

> https://rxjs.dev/api/index/function/interval

## of

Может создавать обзервабл и отправлять в него очередь из своих параметров и сразу завершаться.

```js
of(10, 20, 30).subscribe({
    next: value => console.log('next:', value),
    complete: () => console.log('the end'),
  });
 
// Outputs
// next: 10
// next: 20
// next: 30
// the end
```

Вообще написано, что в 8 версии будет задипрекейчено, поэтому лучше использовать `scheduled`... Но там в него надо еще обязательно свой скедулар прокидывать, как то не особо удобно. Думаю все таки лучше будет использовать альтернативу [from](#from).

> ~~https://rxjs.dev/api/index/function/of~~

> https://rxjs.dev/api/index/function/scheduled

## range

Создает обзервабл, который порождает последовательность из в заднном диапазоне и после этого завершается.

```js
const numbers = range(1, 3);
 
numbers.subscribe({
  next: value => console.log(value),
  complete: () => console.log('Complete!')
});
 
// Logs:
// 1
// 2
// 3
// 'Complete!'
```

> https://rxjs.dev/api/index/function/range

## throwError

На сколько я понял, оно генерирует обзервабл, который сразу порождают ошибку при подписке.

```js
const errorWithTimestamp$ = throwError(() => new Error(`This is error`));
 
errorWithTimestamp$.subscribe({
  error: err => console.log(err.message)
});
```

Не особо понятно зачем оно надо, даже в доке я никакой инфы не нашел по этому вопросу. Первое, что пришло в голову это выбрасывать ошибки в тех случаях, когда надо вернуть обзервабл:
```js
const delays$ = of(1000, 2000, Infinity, 3000);
 
delays$.pipe(
  concatMap(ms => {
    if (ms < 10000) {
      return timer(ms);
    } else {
      // This is probably overkill.
      return throwError(() => new Error(`Invalid time ${ ms }`));
    }
  })
)
.subscribe({
  next: console.log,
  error: console.error
});
```

Но они говорят, что так делать хуево, поэтому можно просто вместо `throwError` написать:
```js
throw new Error(`Invalid time ${ ms }`);
```

И результат будет такой же.

> https://rxjs.dev/api/index/function/throwError

## timer

Создает обзервабл, который отправляет ивент (значение ивента будет 0, но его можно кастомизировать при необходимости) через заданное кол-во миллисекунд.

```js
const source = of(1, 2, 3);

timer(3000)
  .pipe(concatMap(() => source))
  .subscribe(console.log);
  
// Output
// 1
// 2
// 3
```

> https://rxjs.dev/api/index/function/timer

## iif

Принимает в себя 3 параметра:
1. Функция условие. Должна возвращать булево значние.
2. Обзервабл если условие `true`
3. Обзервабл если условие `false`

Если подписаться на `iif`, то сначала выполнится функция условие. В зависимости от вернувшегося значения, эта штука принимает решение какой ей обзервабл вернуть.
```js
let subscribeToFirst;

const firstOrSecond = iif(
  () => subscribeToFirst,
  of('first'),
  of('second')
);
 
subscribeToFirst = true;
firstOrSecond.subscribe(value => console.log(value));
 
// Logs:
// 'first'
 
subscribeToFirst = false;
firstOrSecond.subscribe(value => console.log(value));
 
// Logs:
// 'second'
```

> https://rxjs.dev/api/index/function/iif
