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

Берет определенный по индексу ивент. Соответственно принимает в себя номер индекса. Вторым параметром можно еще прокинуть дефолтное значение.

```js 
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(elementAt(2));

result.subscribe(x => console.log(x));

// Results in:
// click 1 = nothing
// click 2 = nothing
// click 3 = MouseEvent object logged to console
```

> https://rxjs.dev/api/operators/elementAt

## filter

Работает как обычный фильтр в джсе. Пропускает дальше по пайпу только те ивенты, которые прошли описанное условие.

```js
const div = document.createElement('div');
div.style.cssText = 'width: 200px; height: 200px; background: #09c;';
document.body.appendChild(div);

const clicks = fromEvent(document, 'click');
const clicksOnDivs = clicks.pipe(filter(ev => (<HTMLElement>ev.target).tagName === 'DIV'));

clicksOnDivs.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/filter

## first

Берет первый ивент и завершает подписку, если вызван без параметров. Первым параметром можно передать функцию, которая должна возвращать булево значение для каждого элемента, если `true`, тогда значение пройдет дальше по пайпу. Вторым параметром можно передать дефолтное значение. Я так понял оно нужно, когда выкидывается ошибка из исходного обзервабла, или когда ивент, совпадающий по описанному условию так и не пришел, а обзервабл уже завершился.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(first());

result.subscribe(x => console.log(x));

// Отправит один ивент клика, а затем завершится
```

> https://rxjs.dev/api/operators/first

## ignoreElements

Игнорирует любые ивенты и не пускает их дальше по пайпу. За исключением завершения обзервабла или ошибки.

```js
of('you', 'talking', 'to', 'me')
  .pipe(ignoreElements())
  .subscribe({
    next: word => console.log(word),
    error: err => console.log('error:', err),
    complete: () => console.log('the end'),
  });
 
// result:
// 'the end'
```

> https://rxjs.dev/api/operators/ignoreElements

## last

Работает как и [`first`](#first). Принимает в себя те же параметры (функцию кондишн и дефолтное значение). Но отправляет ивент дальше по пайпу только тогда, когда обзервабл завершится.

```js
const source = from(['x', 'y', 'z']);
const result = source.pipe(last());

result.subscribe(value => console.log(`Last alphabet: ${ value }`));

// Outputs
// Last alphabet: z
```

> https://rxjs.dev/api/operators/last

## sample

Отправляет последний пришедший ивент из исходного обзервабла дальше по пайпу только тогда, когда в обзервабл, который передается параметром в этот оператор, придет ивент.

```js
const seconds = interval(1000);
const clicks = fromEvent(document, 'click');
const result = seconds.pipe(sample(clicks));

result.subscribe(x => console.log(x));

// При клике будет отправлять кол-во секунд
// Которые прошли с момента старта подписки
```

> https://rxjs.dev/api/operators/sample

## sampleTime

Отправляет последний пришедший ивент из исходного обзервабла дальше по пайпу через заданное кол-во мс.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(sampleTime(1000));

result.subscribe(x => console.log(x));

// Кажду секунду отправляет последний совершенный клик
```

> https://rxjs.dev/api/operators/sampleTime

## single

Отправляет один ивент по завершению обзервабла, если функция кондишн не указывается (если придет два ивента, то выбросит ошибку). Если функция указана, то по завершению обзервабла отправит дальше по пайпу ивент, который попадает под описанное условие. Если таких ивентов два или их нет в принципе, то выдаст ошибку.

```js
const source1 = of(
 { name: 'Ben' },
 { name: 'Tracy' },
 { name: 'Laney' },
 { name: 'Lily' }
);
 
source1
  .pipe(single(x => x.name.startsWith('B')))
  .subscribe(x => console.log(x));
  
// Emits 'Ben'
```

> https://rxjs.dev/api/operators/single

## skip

Игнорирует заданное кол-во ивентов.

```js
const source = interval(500);
const result = source.pipe(skip(10));

result.subscribe(value => console.log(value));
// output: 10...11...12...13...
```

> https://rxjs.dev/api/operators/skip

## skipLast

Игнорирует заданное кол-во последних ивентов.

```js
const seconds = interval(1000);
const result = seconds.pipe(skipLast(2));

result.subscribe(x => console.log(x));

// Начнет отправлять ивенты через 2 секунды
```

> https://rxjs.dev/api/operators/skipLast

## skipUntil

Принимает в себя обзервабл. Будет скипать ивенты из исходного обзервабла до тех пор, пока в передаваемый не придет хотя бы один ивент.

```js
const intervalObservable = interval(1000);
const click = fromEvent(document, 'click');

const emitAfterClick = intervalObservable.pipe(
  skipUntil(click)
);

// clicked at 4.6s. output: 5...6...7...8........
emitAfterClick.subscribe(value => console.log(value));
```

> https://rxjs.dev/api/operators/skipUntil

## skipWhile

Принимает в себя функцию, которая должна возвращать булево значение для каждого элемента. Пока функция возвращает `true` все ивенты из сходного обзервабла будут игнорироваться. Если один раз вернет `false`, то игнорирование прекращается.

```js
const source = from(['Green Arrow', 'SuperMan', 'Flash', 'SuperGirl', 'Black Canary'])
const example = source.pipe(skipWhile(hero => hero !== 'SuperGirl'));

example.subscribe(femaleHero => console.log(femaleHero));

// output: SuperGirl, Black Canary
```

> https://rxjs.dev/api/operators/skipWhile

## take

Пропускает по пайпу заданное кол-во первых ивентов, а затем завершает подписку.

```js
const intervalCount = interval(1000);
const takeFive = intervalCount.pipe(take(5));

takeFive.subscribe(x => console.log(x));
 
// Logs:
// 0
// 1
// 2
// 3
// 4
```

> https://rxjs.dev/api/operators/take

## takeLast

Пропускает по пайпу заданное кол-во последних ивентов. Не будет работать, если исходный обзервабл никогда не завершится.

```js
const many = range(1, 100);
const lastThree = many.pipe(takeLast(3));

lastThree.subscribe(x => console.log(x));

// 98
// 99
// 100
```

> https://rxjs.dev/api/operators/takeLast

## takeUntil

Принимает в себя обзервабл. Пропускает значения дальше по пайпу до тех пор, пока в передаваемый обзервабл не придет хотя бы один ивент. После этого завершает подписку там, где находится этот оператор.

```js
const source = interval(1000);
const clicks = fromEvent(document, 'click');
const result = source.pipe(takeUntil(clicks));

result.subscribe(x => console.log(x));
```

Будет отправлять ивенты каждую секунду, пока кто нибудь не кликнет.

> https://rxjs.dev/api/operators/takeUntil

## takeWhile

Принимает в себя функцию, которая должна возвращать булево значение для каждого элемента. Пока функция возвращает `true` все ивенты из сходного обзервабла будут проходить дальше. Если один раз вернет `false`, то подписка завершается.

```js
const clicks = fromEvent<PointerEvent>(document, 'click');
const result = clicks.pipe(takeWhile(ev => ev.clientX > 200));

result.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/takeWhile

## throttle

Принимает в себя обзервабл. Из сурс обзервабла принимает только первый ивент, остальные будет игнорировать, пока в передаваемый обзервабл не придет какой то ивент. Короче то же самое что и [`audit`](#audit), только работает не с последним ивентом, а с первым.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(throttle(() => interval(1000)));

result.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/throttle

## throttleTime

То же самое что и обычный [`throttle`](#throttle), только можно указывать сразу кол-во мс.

```js
const clicks = fromEvent(document, 'click');
const result = clicks.pipe(throttleTime(1000));

result.subscribe(x => console.log(x));
```

> https://rxjs.dev/api/operators/throttleTime
