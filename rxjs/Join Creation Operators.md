# Порождающие новые обзерваблы из нескольких обзерваблов

### Содержание

* [combineLatest](#combinelatest)
* [concat | concatWith](#concat)
* [forkJoin](#forkjoin)
* [merge](#merge)
* [partition](#partition)
* [race](#race)
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

## partition

## race

## zip
