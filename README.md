# rx-effect

`fromObservable` turns an RxJS `Observable` into an Effect `Stream`. Values, errors and completion map onto the stream's elements, typed failure and end, and the source is unsubscribed when the stream stops early.

Use it when RxJS code feeds an Effect pipeline.

## Quick start

```ts
import { fromObservable } from '@systemfsoftware/rx-effect'
import { Effect, Stream } from 'effect'
import { interval } from 'rxjs'

const ticks = fromObservable((error) => String(error))(interval(10)).pipe(Stream.take(2))

Effect.runPromise(Stream.runCollect(ticks)).then(console.log)
// [ 0, 1 ]
```

`fromObservable` takes the error mapper first and returns a function that takes the Observable. The mapper runs only when the Observable errors, and its result becomes the stream's typed failure.

## Install

```bash
pnpm add @systemfsoftware/rx-effect effect@^4 rxjs@^7
```

`effect` and `rxjs` are peer dependencies: this package declares them but does not install them, so one copy is shared with the rest of your project.

## API

```ts
fromObservable<E>(onError: (error: unknown) => E): <A>(observable: Observable<A>) => Stream<A, E>
```

The whole implementation is in [`FromObservable.ts`](packages/rx-effect/src/FromObservable.ts).

- Each `next` value becomes a stream element, in the order it arrives.
- `complete` ends the stream. An Observable that completes without emitting gives an empty stream.
- `error` fails the stream with the value `onError` returns.
- Stopping the stream early, for example with `Stream.take`, unsubscribes the source once.

The bridge does not apply backpressure. It never pauses the Observable: values the stream has not read yet wait in an unbounded queue. A synchronous source that emits 10,000 values before the reader starts delivers all 10,000.

## Contributing

Development setup and workflow are in [CONTRIBUTING.md](CONTRIBUTING.md).

## Support

Report bugs and ask questions in the [issue tracker](https://github.com/systemfsoftware/rx-effect/issues).

## License

[Apache-2.0](packages/rx-effect/LICENSE)
