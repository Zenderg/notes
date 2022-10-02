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

## bufferTime

## bufferToggle

## bufferWhen

## concatMap

## concatMapTo

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
