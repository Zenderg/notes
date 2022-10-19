# Объединяющие несколько обзерваблов

### Содержание

* [combineLatestAll](#combinelatestall)
* [concatAll](#concatall)
* [exhaustAll](#exhaustall)
* [mergeAll](#mergeall)
* [switchAll](#switchall)
* [startWith](#startWith)
* [withLatestFrom](#withlatestfrom)

## combineLatestAll

Работает с ивентами, которые являются обзерваблами. После завершения исходного обзервабла оборачивает все обзерваблы, которые пришли из ивентов, в [`combineLatest`](https://github.com/Zenderg/notes/blob/main/rxjs/Join%20Creation%20Operators.md#combinelatest).
Значения в подписку на исходный обзервабл попадают также, как если бы там был [`combineLatest`](https://github.com/Zenderg/notes/blob/main/rxjs/Join%20Creation%20Operators.md#combinelatest).

```js
const clicks = fromEvent(document, 'click');
const higherOrder = clicks.pipe(
  map(() => of(1,2,3)),
  take(2)
);
const result = higherOrder.pipe(combineLatestAll());

result.subscribe(x => console.log(x));

// [3, 1]
// [3, 2]
// [3, 3]
```

Каждый клик в примере выше порождает обзервабл через [`of`](https://github.com/Zenderg/notes/blob/main/rxjs/Creation%20Operators.md#of).
Затем, через 2 клика, `higherOrder` обзервабл завершится и 2 обзервабла попадут в `combineLatestAll`.
Он в свою очередь обернет их в [`combineLatest`](https://github.com/Zenderg/notes/blob/main/rxjs/Join%20Creation%20Operators.md#combinelatest) и получится примерно вот такая конструкция:

```js
combineLatest([
of(1,2,3),
of(1,2,3)
])
```

На которую уже создается подписка.

В первом элементе возвращаемого из [`combineLatest`](https://github.com/Zenderg/notes/blob/main/rxjs/Join%20Creation%20Operators.md#combinelatest) массива всегда будет 3, потому что [`combineLatest`](https://github.com/Zenderg/notes/blob/main/rxjs/Join%20Creation%20Operators.md#combinelatest) ждет, пока во все передаваемые обзерваблы прилетит ивент.
А т.к. создание обзервабла через [`of`](https://github.com/Zenderg/notes/blob/main/rxjs/Creation%20Operators.md#of) это всегда синхронная операция, то сначала отработает до конца первый обзервабл, а только потом начнет обрабатываться второй.

Можно сделать ее асинхронной через `scheduled`:

```js
const clicks = fromEvent(document, 'click');
const higherOrder = clicks.pipe(
  map(() => scheduled([1,2,3], asyncScheduler)),
  take(2)
);
const result = higherOrder.pipe(combineLatestAll());

result.subscribe(x => console.log(x));

// [1, 1]
// [2, 1]
// [2, 2]
// [3, 2]
// [3, 3]
```

> https://rxjs.dev/api/operators/combineLatestAll

## concatAll
## exhaustAll
## mergeAll
## switchAll
## startWith
## withLatestFrom
