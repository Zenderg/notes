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

## fromEvent

## fromEventPattern

## generate

## interval

## of

## range

## throwError

## timer

## iif