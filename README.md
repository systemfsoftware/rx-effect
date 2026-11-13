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

`@systemfsoftware` packages are not published to npm. They come from nix.systemfsoftware.com, the package registry for `@systemfsoftware` packages.

1. Add this repository as a flake input:

   ```nix
   inputs.rx-effect.url = "github:systemfsoftware/rx-effect";
   ```

2. Substitute from the cache. This repository's `flake.nix` sets the same keys:

   ```nix
   nixConfig = {
     extra-substituters = "https://nix.systemfsoftware.com";
     extra-trusted-public-keys = "nix-cache-1:wXA8I4DayNW9q6enUyQA/P/nT38pGSTLt374t3gsn7M=";
   };
   ```

3. Take the package tarball through the consumer route in [pnpm-release-management](https://github.com/systemfsoftware/pnpm-release-management). That repository provides `lib.mkProducerTarballs`, the call this repository's `flake.nix` makes for its own producers, and `lib.mkPnpmConsumerStore`.

4. Install the peers from npm. You bring `effect` and `rxjs` yourself:

   ```bash
   pnpm add effect@^4 rxjs@^7
   ```

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
