# Трансформация

### Содержание

* [buffer](#buffer)
* [bufferCount](#buffercount)
* [bufferTime](#buffertime)
* [bufferToggle](#buffertoggle)
* [bufferWhen](#bufferwhen)
* [concatMap](#concatmap)
* [concatMapTo](#concatmapto)
* [exhaust](#exhaust)
* [exhaustMap](#exhaustmap)
* [expand](#expand)
* [groupBy](#groupby)
* [map](#map)
* [mapTo](#mapto)
* [mergeMap](#mergemap)
* [mergeMapTo](#mergemapto)
* [mergeScan](#mergescan)
* [pairwise](#pairwise)
* [partition](#partition)
* [pluck](#pluck)
* [scan](#scan)
* [switchScan](#switchscan)
* [switchMap](#switchmap)
* [switchMapTo](#switchmapto)
* [window](#window)
* [windowCount](#windowcount)
* [windowTime](#windowtime)
* [windowToggle](#windowToggle)
* [windowWhen](#windowWhen)

## buffer

Принимает в себя обзервабл. Отправляет ивент со всеми накопленными ивентами дальше, когда в переданный обзервабл прилетает тригер ивент.

```js
const clicks = fromEvent(document, 'click');
const intervalEvents = interval(1000);
const buffered = intervalEvents.pipe(buffer(clicks));

buffered.subscribe(x => console.log(x));
```
(в примере выше интервал будет отправлять каждую секунду новый ивент. Каждый такой ивент будет накапливаться в массиве в буфере. Массив с накопленными ивентами будет отправлен в подписку, когда юзер тыкнет на документ)

> https://rxjs.dev/api/operators/buffer

## bufferCount

Принимает в себя 2 параметра. Первый отвечает за размер буфера. Второй отвечает за кол-во ивентов, которые будут прилетать в буфер перед тем, как он отправит их дальше (если этот параметр больше первого, то буфер отправит дальше по пайпу только последние ивенты, которые он может в себя вместить). Как только буфер будет заполнен он отправит массив ивентов дальше по пайпу, и затем обнулит его (только если у вас не выставлен специфичный второй параметр).

```js
const clicks = fromEvent(document, 'click');
const buffered = clicks.pipe(bufferCount(2));

buffered.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/bufferCount

## bufferTime

Принимает в себя время в мс и каждый заданный интервал отправляет массив из предыдущих элементов дальше по пайпу.

```js
const clicks = fromEvent(document, 'click');
const buffered = clicks.pipe(bufferTime(1000));

buffered.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/bufferTime

## bufferToggle

Оч сложна чот...

> https://rxjs.dev/api/operators/bufferToggle

## bufferWhen

Принимает в себя функцию, которая должна возвращать обзервабл. Когда ивент прилетит в возвращаемый обзервабл, тогда закроется буфер и пропустит все ивенты дальше по пайпу.

```js
const clicks = fromEvent(document, 'click');
const buffered = clicks.pipe(
  bufferWhen(() => interval(1000 + Math.random() * 4000))
);
buffered.subscribe(x => console.log(x));
```

В доке пишут, что это хрень отвечает за логику того, когда отправлять ивенты в буфер, когда закрывать его, обнулять и пр. Но передавать функцию в него можно только на закрытие буфера. В общем странно как то.

> https://rxjs.dev/api/operators/bufferWhen

## concatMap

Принимает в себя фунцию, которая должна возвращать обзервабл, значение которого пройдет дальше по пайпу. Причем новые ивенты не будут попадать в этот оператор пока обзервабл, который он возвращает, не завершится.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(
  concatMap(ev => interval(1000).pipe(take(4)))
);

result.subscribe(x => console.log(x));
```

Из примера выше, если юзер кликнул по странице, то в подписку отправится 4 ивента из интервала в течении секунды. Если в это время еще кликать, то последующие ивенты попадут в `concatMap` только если до конца отправятся 4 ивента из интервала. Т.е. они как бы попадут в очередь и будут обработаны только через 4 секунды.

> https://rxjs.dev/api/operators/concatMap

## concatMapTo

В доке говорят это хрень какая то, будет задеприкейчено, поэтому юзайте просто [`concatMap`](#concatmap).

> ~~https://rxjs.dev/api/operators/concatMapTo~~

## exhaust

## exhaustMap

## expand

## groupBy

## map

## mapTo

## mergeMap

## mergeMapTo

## mergeScan

## pairwise

## partition

## pluck

## scan

## switchScan

## switchMap

## switchMapTo

## window

## windowCount

## windowTime

## windowToggle

## windowWhen
