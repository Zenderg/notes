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
## debounceTime
## distinct
## distinctUntilChanged
## distinctUntilKeyChanged
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
