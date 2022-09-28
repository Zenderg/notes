# Порождающие новые обзерваблы из нескольких обзерваблов

### Содержание

* [combineLatest](#combinelatest)
* [concat | concatWith](#concat)
* [forkJoin](#forkjoin)
* [merge](#merge)
* [partition](#partition)
* [race | raceWith](#race)
* [zip](#zip)

## combineLatest

Комбинирует несколько обзерваблов, переданных в него параметром как массив или объект. Когда в каждый из обзерваблов придет хотя бы одно значение, тогда он у будет отправлять ивенты каждый раз, когда любой из обзерваблов примет в себя новое значение.

* Если передать туда пустой массив, то он сразу завершится.
* Если завершить один из обзерваблов, тогда combineLatest, в котором он находился, тоже завершится. (вот тут не уверен, возможно криво перевел)
* Если выкинуть ошибку в одном из обзерваблов, combineLatest тоже выкинет ошибку.

```js
const observable1 = new Subject();

const observable2 = new Subject();

combineLatest([observable1, observable2]).subscribe(console.log)

observable1.next(1);

observable2.next(1);
observable2.next(2);

```

> https://rxjs.dev/api/index/function/combineLatest

## concat

Задеприкейчен, теперь говорят вместо него использовать `concatWith`.

`concatWith` - пропускает через себя ивенты исходного обзервабла, после того как исходный закрывается (и только после этого), начинает передавать ивенты из переданного в себя обзервабла:
```js
const clicks$ = fromEvent(document, 'click');
const moves$ = fromEvent(document, 'mousemove');

clicks$.pipe(
  map(() => 'click'),
  take(1),
  concatWith(
    moves$.pipe(
      map(() => 'move')
    )
  )
)
.subscribe(x => console.log(x));

// 'click'
// 'move'
// 'move'
// 'move'
// ...
```

Оно может принимать в себя несколько обзерваблов `concatWith(obs1, obs2, ...)`, но ивенты будут браться из следующих, после того как предыдущий обзервабл будет завершен.

> ~~https://rxjs.dev/api/operators/concat~~

> https://rxjs.dev/api/operators/concatWith

## forkJoin

Принимает в себя массив (или объект, где значения будут обзерваблами) обзерваблов, ждет пока они завершатся и только после этого отправляет ивент в подписку с последними значениями этих обзерваблов.
```js
const observable = forkJoin({
  foo: of(1, 2, 3, 4),
  bar: Promise.resolve(8),
  baz: timer(4000)
});

// same shit
// const observable = forkJoin([
//   of(1, 2, 3, 4),
//   Promise.resolve(8),
//   timer(4000)
// ]);

observable.subscribe({
 next: value => console.log(value),
 complete: () => console.log('This is how it ends!'),
});

// Logs:
// { foo: 4, bar: 8, baz: 0 } after 4 seconds
// 'This is how it ends!' immediately after
```

Примечание: все обзерваблы в примере выше автоматически завершаются.

> https://rxjs.dev/api/index/function/forkJoin

## merge

Принимает в себя через запятую сколько угодно обзерваблов и склеивает их и порождает один, который отправляет ивент в подписку, когда в один из перечисленных обзерваблов ивент прилетает.

```js
const clicks = fromEvent(document, 'click');
const timer = interval(1000);

const clicksOrTimer = merge(clicks, timer);

clicksOrTimer.subscribe(x => console.log(x));
// Каждую секунду через интервал будут отправляться ивенты в подписку
// Также, если будут клики на странице, тогда ивент о клике тоже будет
// прилетать в подписку
```

А еще у него можно выставить конкурентность последним параметром. Т.е. какое максимальное кол-во обзерваблов будут прослушиваться. Переход на прослушку других в этом случае будет тогда, когда один из прослушиваемых завершится.

> https://rxjs.dev/api/index/function/merge

## partition

Разбивает один обзервабл на массив из 2 обзерваблов по определенному кондишену. В первом обзервабле будут трушные ивенты, во втором не оч.

По сути оно там просто делает 2 обзервабла и добавляет к ним оператор `filter`.

```js
const observableValues = of(1, 2, 3, 4, 5, 6);
const [evens$, odds$] = partition(observableValues, value => value % 2 === 0);
 
odds$.subscribe(x => console.log('odds', x));
evens$.subscribe(x => console.log('evens', x));
 
// Logs:
// odds 1
// odds 3
// odds 5
// evens 2
// evens 4
// evens 6
```

> https://rxjs.dev/api/index/function/partition

## race

Задеприкейтили, превратили в оператор `raceWith`.


> ~~https://rxjs.dev/api/index/function/race~~

> https://rxjs.dev/api/operators/raceWith

## zip
