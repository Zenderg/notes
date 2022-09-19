# Порождающие новые обзерваблы из нескольких обзерваблов

### Содержание

* [combineLatest](#combinelatest)
* [concat](#concat)
* [forkJoin](#forkjoin)
* [merge](#merge)
* [partition](#partition)
* [race](#race)
* [zip](#zip)

## combineLatest

Комбинирует несколько обзерваблов, переданных в него параметром как массив или объект. Когда в каждый из обзерваблов придет хотя бы одно значение, тогда он у будет отправлять ивенты каждый раз, когда любой из обзерваблов примет в себя новое значение.

* Если передать туда пустой массив, то он сразу завершится.
* Если завершить один из обзерваблов, тогда combineLatest, в котором он находился, тоже завершится.
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

## forkJoin

## merge

## partition

## race

## zip
