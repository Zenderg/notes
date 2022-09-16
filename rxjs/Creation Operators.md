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

> Официальная дока: https://rxjs.dev/api/ajax/ajax

## bindCallback

## bindNodeCallback

## defer

## empty

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