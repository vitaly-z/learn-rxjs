# zip

#### signature: `zip(observables: *): Observable`

## After all observables emit, emit values as an array

---

💡 Combined with [interval](../creation/interval.md) or
[timer](../creation/timer.md), zip can be used to time output from another
source!

---

### Why use zip?
This operator is ideal when you want to combine values from multiple observables in a pairwise fashion, like zipping together the teeth of a zipper. Imagine having two observables, where one emits values like "hot", "warm", "cold", and the other emits values like "coffee", "tea", "lemonade". Using the zip operator, you'd pair them together as "hot coffee", "warm tea", and "cold lemonade".

An everyday example is when you want to match information from two sources, like pairing names with their corresponding scores in a game. Picture an observable emitting player names and another emitting their scores. With zip, you can easily create pairs of `[player, score]`, ensuring each player is associated with the correct score.

Be mindful that zip will only emit a value when all input observables have emitted a corresponding value. This means if one observable has emitted more values than another, the unmatched values will be held back until the other observable emits its next value. In some cases, this could lead to unpaired values, making it important to ensure your observables are synchronized.

[![Ultimate RxJS](https://drive.google.com/uc?export=view&id=1qq2-q-eVe-F_-d0eSvTyqaGRjpfLDdJz 'Ultimate RxJS')](https://ultimatecourses.com/courses/rxjs?ref=4)

### Examples

##### Example 1: zip multiple observables emitting at alternate intervals

(
[StackBlitz](https://stackblitz.com/edit/typescript-5az27c?file=index.ts&devtoolsheight=100)
| [jsBin](http://jsbin.com/lireyisira/1/edit?js,console) |
[jsFiddle](https://jsfiddle.net/btroncone/ton462sg/) )

```js
// RxJS v6+
import { delay } from 'rxjs/operators';
import { of, zip } from 'rxjs';

const sourceOne = of('Hello');
const sourceTwo = of('World!');
const sourceThree = of('Goodbye');
const sourceFour = of('World!');
//wait until all observables have emitted a value then emit all as an array
const example = zip(
  sourceOne,
  sourceTwo.pipe(delay(1000)),
  sourceThree.pipe(delay(2000)),
  sourceFour.pipe(delay(3000))
);
//output: ["Hello", "World!", "Goodbye", "World!"]
const subscribe = example.subscribe(val => console.log(val));
```

##### Example 2: zip when 1 observable completes

(
[StackBlitz](https://stackblitz.com/edit/typescript-f4qgry?file=index.ts&devtoolsheight=100)
| [jsBin](http://jsbin.com/fisitatesa/1/edit?js,console) |
[jsFiddle](https://jsfiddle.net/btroncone/oamyk3xr/) )

```js
// RxJS v6+
import { take } from 'rxjs/operators';
import { interval, zip } from 'rxjs';

//emit every 1s
const source = interval(1000);
//when one observable completes no more values will be emitted
const example = zip(source, source.pipe(take(2)));
//output: [0,0]...[1,1]
const subscribe = example.subscribe(val => console.log(val));
```

##### Example 3: get X/Y coordinates of drag start/finish (mouse down/up)

(
[StackBlitz](https://stackblitz.com/edit/rxjs-zip-mousedownup-coordinates?file=index.ts&devtoolsheight=50)
)

```js
// RxJS v6+
import { fromEvent, zip } from 'rxjs';
import { map } from 'rxjs/operators';

const documentEvent = eventName =>
  fromEvent(document, eventName).pipe(
    map((e: MouseEvent) => ({ x: e.clientX, y: e.clientY }))
  );

zip(documentEvent('mousedown'), documentEvent('mouseup')).subscribe(e =>
  console.log(JSON.stringify(e))
);
```

##### Example 4: mouse click duration

(
[StackBlitz](https://stackblitz.com/edit/rxjs-zip-mouseclickduration?file=index.ts&devtoolsheight=50)
)

```js
// RxJS v6+
import { fromEvent, zip } from 'rxjs';
import { map } from 'rxjs/operators';

const eventTime = eventName =>
  fromEvent(document, eventName).pipe(map(() => new Date()));

const mouseClickDuration = zip(
  eventTime('mousedown'),
  eventTime('mouseup')
).pipe(map(([start, end]) => Math.abs(start.getTime() - end.getTime())));

mouseClickDuration.subscribe(console.log);
```

### Additional Resources

- [zip](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#static-method-zip)
  📰 - Official docs
- [zip](https://indepth.dev/reference/rxjs/operators/zip) - In Depth Dev Reference
- [Combination operator: zip](https://egghead.io/lessons/rxjs-combination-operator-zip?course=rxjs-beyond-the-basics-operators-in-depth)
  🎥 💵 - André Staltz

---

> 📁 Source Code:
> [https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/zip.ts](https://github.com/ReactiveX/rxjs/blob/master/src/internal/operators/zip.ts)
