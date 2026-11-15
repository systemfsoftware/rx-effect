# @systemfsoftware/rx-effect

Bridge [RxJS](https://rxjs.dev) and [Effect](https://effect.website).

`fromObservable` turns an `Observable<A>` into a typed `Stream<A, E>` — values, errors, and completion map onto Effect's channels, with backpressure and proper interruption (unsubscribing the source when the stream is interrupted).

```ts
import { fromObservable } from '@systemfsoftware/rx-effect'
import { Stream } from 'effect'
import { interval } from 'rxjs'

const stream = fromObservable(interval(1000)) // Stream<number>
```

## Install

```bash
pnpm add @systemfsoftware/rx-effect effect@^4 rxjs@^7
```

> [!NOTE]
> `effect` and `rxjs` (v7) are peer dependencies — you bring your own.
