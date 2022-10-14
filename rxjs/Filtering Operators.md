# Фильтрация

### Содержание

* [audit](#audit)
* [auditTime](#audittime)
* [debounce](#debounce)
* [debounceTime](#debouncetime)
* [distinct](#distinct)
* [distinctUntilChanged](#distinctuntilchanged)
* [distinctUntilKeyChanged](#distinctuntilkeychanged)
* [elementAt](#elementat)
* [filter](#filter)
* [first](#first)
* [ignoreElements](#ignoreelements)
* [last](#last)
* [sample](#sample)
* [sampleTime](#sampletime)
* [single](#single)
* [skip](#skip)
* [skipLast](#skiplast)
* [skipUntil](#skipuntil)
* [skipWhile](#skipwhile)
* [take](#take)
* [takeLast](#takelast)
* [takeUntil](#takeuntil)
* [takeWhile](#takewhile)
* [throttle](#throttle)
* [throttleTime](#throttletime)

## audit

Пропускает только последний ивент из сурс обзервабла. Принимает в себя обзервабл. Активирует подписку на него только тогда, когда приходит ивент из сурс обзервабла. Ивенты не будут проходить дальше по пайпу, пока в передаваемый обзервабл не придет ивент.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(audit(ev => interval(1000)));

result.subscribe(x => console.log(x));
```

 Пример выше будет обрабатывать только последний ивент клика за 1 секунду.

> https://rxjs.dev/api/operators/audit

## auditTime

То же самое что и [`audit`](#audit), только принимает время в мс вместо обзервабла.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(auditTime(1000));

result.subscribe(x => console.log(x));
```

Пример выше будет обрабатывать только последний ивент клика за 1 секунду.

> https://rxjs.dev/api/operators/auditTime

## debounce

Пропускает только последний ивент из сурс обзервабла. Принимает в себя обзервабл. Активирует подписку на него только тогда, когда приходит ивент из сурс обзервабла. Ивенты не будут проходить дальше по пайпу, пока в передаваемый обзервабл не придет ивент.

При этом, когда приходит ивент из сурс обзервабла, `debounce` завершает подписку на свой обзервабл, который был порожден предыдущем ивентом, и делает новую. 

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(
  scan(i => ++i, 1),
  debounce(i => interval(200 * i))
);

result.subscribe(x => console.log(x));
```

Пример выше будет обрабатывать только последний ивент, если в течении заданного времени не поступало новых.

> https://rxjs.dev/api/operators/debounce

## debounceTime

То же самое что и [`debounce`](#debounce), только принимает время в мс вместо обзервабла.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(debounceTime(1000));

result.subscribe(x => console.log(x));
```

Пример выше будет обрабатывать только последний ивент, если в течении заданного времени не поступало новых.

> https://rxjs.dev/api/operators/debounceTime

## distinct

Служит фильтром для ВСЕХ (важно) ивентов. Принимает в себя функцию, которая должна возвращать уникальное значение для каждого ивента. Если функцию не передавать, то она будет сравнивать сами ивенты (может быть юзабельно, если приходят просто числа какие нибудь, а не объекты).

```js
of(
  { age: 4, name: 'Foo'},
  { age: 7, name: 'Bar'},
  { age: 5, name: 'Foo'}
)
.pipe(distinct(({ name }) => name))
.subscribe(x => console.log(x));

// Outputs
// { age: 4, name: 'Foo' }
// { age: 7, name: 'Bar' }
```

> https://rxjs.dev/api/operators/distinct

## distinctUntilChanged

Служит фильтром для уникальных ивентов. Сравнивает предыдущий и текущий ивенты между собой. Самый простой пример:

```js
of(1, 1, 1, 2, 2, 2, 1, 1, 3, 3)
  .pipe(distinctUntilChanged())
  .subscribe(console.log);
  
// Logs: 1, 2, 1, 3
```

Еще внутрь можно передать свой кастомный компаратор. Если он возвращает `true`, значит изменений между двумя ивентами нет и он скипает текущий:

```js
const totallyDifferentBuilds$ = of(
  { engineVersion: '1.1.0', transmissionVersion: '1.2.0' },
  { engineVersion: '1.1.0', transmissionVersion: '1.4.0' },
  { engineVersion: '1.3.0', transmissionVersion: '1.4.0' },
  { engineVersion: '1.3.0', transmissionVersion: '1.5.0' },
  { engineVersion: '2.0.0', transmissionVersion: '1.5.0' }
).pipe(
  distinctUntilChanged((prev, curr) => {
    return (
      prev.engineVersion === curr.engineVersion ||
      prev.transmissionVersion === curr.transmissionVersion
    );
  })
);
 
totallyDifferentBuilds$.subscribe(console.log);
 
// Logs:
// { engineVersion: '1.1.0', transmissionVersion: '1.2.0' }
// { engineVersion: '1.3.0', transmissionVersion: '1.4.0' }
// { engineVersion: '2.0.0', transmissionVersion: '1.5.0' }
```

> https://rxjs.dev/api/operators/distinctUntilChanged

## distinctUntilKeyChanged

Также фильтрует уникальные ивенты. Почти тоже самое что и [`distinctUntilChanged`](#distinctuntilchanged), только с сахаром. На этот раз предназначено именно для ивентов в виде объекта. Принимает в себя строку - ключ в объекте. По этому ключу оно будет брать значения из предыдущего ивента и следующего и сравнивать их. Допом вторым параметром можно еще кастомный компаратор запихать.

```js
of(
  { age: 4, name: 'Foo' },
  { age: 7, name: 'Bar' },
  { age: 5, name: 'Foo' },
  { age: 6, name: 'Foo' }
).pipe(
  distinctUntilKeyChanged('name')
)
.subscribe(x => console.log(x));
 
// displays:
// { age: 4, name: 'Foo' }
// { age: 7, name: 'Bar' }
// { age: 5, name: 'Foo' }
```

> https://rxjs.dev/api/operators/distinctUntilKeyChanged

## elementAt
## filter
## first
## ignoreElements
## last
## sample
## sampleTime
## single
## skip
## skipLast
## skipUntil
## skipWhile
## take
## takeLast
## takeUntil
## takeWhile
## throttle
## throttleTime
