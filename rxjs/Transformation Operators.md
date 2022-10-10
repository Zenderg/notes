# Трансформация

### Содержание

* [buffer](#buffer)
* [bufferCount](#buffercount)
* [bufferTime](#buffertime)
* [bufferToggle](#buffertoggle)
* [bufferWhen](#bufferwhen)
* [concatMap](#concatmap)
* [concatMapTo](#concatmapto)
* [exhaust | exhaustAll](#exhaust)
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

Переименовали в `exhaustAll`, юзайте его.

Как я понял он оперирует ивентами, которые являются обзерваблами. Причем ивенты из изначального обзервабла будут игнорироваться, если предыдуший ивент (который является обзерваблом) еще не завершился.

```js
const clicks = fromEvent(document, 'click');
const higherOrder = clicks.pipe(
  map(() => interval(1000).pipe(take(5)))
);
const result = higherOrder.pipe(exhaustAll());

result.subscribe(x => console.log(x));
```

В данном кейсе, при первом клике ивент будет преобразован в обзервабл (`interval`), который каждую секунду будет отправлять 5 ивентов, а потом завершится. Если он еще не завершился, а ивенты кликов будут приходить, то он будет их все игноирировать, пока не завершить предыдущий обзервабл (`interval`).

> ~~https://rxjs.dev/api/operators/exhaust~~

> https://rxjs.dev/api/operators/exhaustAll

## exhaustMap

В общем... он похож на [`exhaust`](#exhaust), делает все то же самое. За тем лишь исключением, что используется в конкретном месте.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(
  exhaustMap(() => interval(1000).pipe(take(5)))
);

result.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/exhaustMap

## expand

Должен возвращать обзервабл. При каждом отправлении ивента из возвращаемого обзервабла, предыдущий ивент из него складывается рекурсивно с текущим. Звучит странно, но примерно так я и понял.

```js
const clicks = fromEvent(document, 'click');

const powersOfTwo = clicks.pipe(
  map(() => 1),
  expand(x => of(2 * x).pipe(delay(1000))),
  take(10)
);

powersOfTwo.subscribe(x => console.log(x));

// 1
// 2
// 4
// 8
// 16
// 32
// 64
// 128
// 256
// 512
```

За более детальный объяснением можно сходить вот сюда: https://ncjamieson.com/understanding-expand/

> https://rxjs.dev/api/operators/expand

## groupBy

Оператор, который делает группировку ивентов по заданному ключу. Принимает в себя функцию, которая должна возвращать ключ для каждого элемента. После завершения обзервабла `groupBy` отправляет ивенты (которые будут обзерваблами), для каждого ключа будет отправляться свой ивент, который содержит в себе все элементы собранные по одному и тому же ключу.

```js
of(
  { id: 1, name: 'JavaScript' },
  { id: 2, name: 'Parcel' },
  { id: 2, name: 'webpack' },
  { id: 1, name: 'TypeScript' },
  { id: 3, name: 'TSLint' }
).pipe(
  groupBy(p => p.id),
  mergeMap(group$ => group$.pipe(reduce((acc, cur) => [...acc, cur], [])))
)
.subscribe(p => console.log(p));
 
// displays:
// [{ id: 1, name: 'JavaScript' }, { id: 1, name: 'TypeScript'}]
// [{ id: 2, name: 'Parcel' }, { id: 2, name: 'webpack'}]
// [{ id: 3, name: 'TSLint' }]
```

> https://rxjs.dev/api/operators/groupBy

## map

Работает аналогично методу в джсе. Трансформирует значение для каждого пришедшего ивента и передает его дальше по пайпу.

```js
const obs = new Subject()

obs.pipe(
  map(x => x * 10)
)
.subscribe(p => console.log(p));

obs.next(1)
obs.next(2)
obs.next(3)

// 10
// 20
// 30
```

> https://rxjs.dev/api/operators/map

## mapTo

Задеприкейчено. Говорят лучше просто [`map`](#map) юзать. `mapTo` делает то же самое, но возвращает всегда только одно и то же значение для каждого ивента.

> ~~https://rxjs.dev/api/operators/mapTo~~

## mergeMap

Возвращает обзервабл для каждого из ивентов. При этом от обзервабла, созданного предыдущем ивентом, оно не отписыватеся (это важно).

```js
const letters = of('a', 'b', 'c');
const result = letters.pipe(
  tap(() => console.log("first tap")),
  mergeMap(x => interval(1000).pipe(map(i => x + i)))
);
 
result.subscribe(x => console.log(x));
// first tap
// a0
// b0
// c0
// a1
// b1
// c1
// ...
// будет бесконечно каждую секунду отправлять abc 
```

> https://rxjs.dev/api/operators/mergeMap

## mergeMapTo

Задеприкейчено. Лучше просто использовать [`mergeMap`](#mergemap). Делает то же самое, но возвращает для каждого ивента один и тот же обзервабл.

> ~~https://rxjs.dev/api/operators/mergeMapTo~~

## mergeScan

Работает также как `reduce` в джсе. Тоже есть аккамулятор. Срабатывает на каждый пришедший ивент и должен возвращаться обзервабл. В подписку попадает значение из аккамулятора.

```js
const click$ = fromEvent(document, 'click').pipe(map(() => 1));

const count$ = click$.pipe(
  mergeScan((acc, one) => of(acc + one), 0)
);
 
count$.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/mergeScan

## pairwise

Объединяет предыдущий ивент и текущий в массив из двух элементов. Работает только после получения двух ивентов.

```js
const clicks = fromEvent(document, 'click').pipe(pairwise());
 
clicks.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/pairwise

## pluck

Задеприкейчено. Юзайте просто [`map`](#map) и не парьтесь.

> ~~https://rxjs.dev/api/operators/pluck~~

## scan

Работает аналогично `reduce` в джсе. Отправляет аккамулятор на каждый пришедший ивент.

```js
const numbers$ = of(1, 2, 3);
 
numbers$
  .pipe(
    scan((total, n) => total + n)
  )
  .subscribe(console.log);
  
// 1
// 3
// 6
```

> https://rxjs.dev/api/operators/scan

## switchScan

## switchMap

## switchMapTo

## window

## windowCount

## windowTime

## windowToggle

## windowWhen
