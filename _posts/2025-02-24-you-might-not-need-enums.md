---
title: You might not need TypeScript enums
tags:
- javascript
- typescript
- enum
first-draft: 2025-02-24
tweet: 'You might not need TypeScript enums #typescript #javascript #enum'
#tootUrl: 'https://mastodon.social/@saji/TODO'
---
With Node.js learning to ignore TypeScript annotations[^1] and TypeScript getting an option that disallows runtime semantics[^2], let’s re–evaluate if we need the most commonly used TypeScript runtime features: **enums**.

[^1]: `--experimental-strip-types` flag introduced in Node.js 23.0.0 and [no flag needed since 23.6.0][node:st]
[^2]: [`enableSyntaxOnly` option introduced in TypeScript 5.8 beta][ts:so]
[node:st]: https://nodejs.org/en/blog/release/v23.6.0#unflagging---experimental-strip-types
[ts:so]: https://devblogs.microsoft.com/typescript/announcing-typescript-5-8-beta/#the---erasablesyntaxonly-option

_(Spoiler: no)_

Let’s look at a common example of an enum:

```typescript
enum Things {
  FOO = 'foo',
  BAR = 'bar',
  ZAR = 'zar',
}
```

Since values are human readable, we can use TypeScript union of strings
instead:

```typescript
type Thing = 'foo' | 'bar' | 'zar';
```

Then you can use strings for values:

```typescript
let someThing: Thing = 'foo';

function logThing (thing: Thing) {
  console.log(thing);
}

logThing('bar');
```

## List values

Sometimes it’s useful to have access to all possible values at
runtime. For this, we can list them in an array and use that as source
of truth for types[^3]:

[^3]: I’d put array definition in one location (e.g. `src/consts/…`) and
      types in another (e.g. `src/types/…`), but you do you.

```typescript
const THINGS = Object.freeze(['foo', 'bar', 'zar'] as const;)

type Thing = (typeof THINGS)[number];
// ↑ this evaluates to: type Thing = "foo" | "bar" | "zar"
```

- `as const` makes TypeScript to see it as immutable (instead of `Array<string>`)
- `Object.freeze` prevents from changing the array by accident at runtime

Now you can e.g. present user with a list of all options to choose from
(example using [`lit-html`][lit-html]):

[lit-html]: https://lit.dev/

```typescript
html`<select>
  ${THINGS.map(thing => html`<option>${thing}</option>`)}
</select>`
```

… or sanitise user input:

```typescript
function sanitise<Type>(
  dirtyValue: any,
  allowedValues: Readonly<Array<Type>>,
  fallbackValue: Type | undefined
): Type {
  if (allowedValues.includes(dirtyValue as Type)) {
    return dirtyValue as Type;
  } else {
    const error = new Error(`Unrecognised value: ${dirtyValue}`);
    if (fallbackValue === undefined) {
      throw error;
    } else {
      console.warn(error);
      return fallbackValue;
    }
  }
}

const dirtyThing = new URLSearchParams(window.location.search).get('thing');
const cleanThing = sanitise<Thing>(dirtyThing, THINGS, THINGS[0]);
```

## Mapping

To map values to something else, use `Record` to make sure all values
are included as keys (an no other keys are present):

```typescript
const thingToLabelMap: Record<Thing, string> = {
  foo: 'Felicity',
  bar: 'Bartholomew',
  zar: 'Zahary',
}
const label = thingToLabelMap[thing];
```

[Try it out in TypeScript playground][pg:mapping]

[pg:mapping]: https://www.typescriptlang.org/play/?#code/PTAEl4NxAndAVAnAnqAhgE1QSwHYHNQHs5Q4BTAW3wDds8SAbckrAFwGdQAzOfM0EjZgAsSRGAAkAkgDkA4gGUCRITRj4AMsgBG9ALLIADgFgAUCYDG+LK2axJshQF5QAeU0ArEmeYA6LiRIAXiQAFADaAOQc+PjhADSg4ZrIcHEJAcnhALoo7BZWzACUANwmzAj6JLCCNKBOwWUV+By20vIFoVgArmTacJklpsZ51qDKuABcVTVOkdHhJuaWI2M4qhradHr6kwBKnoSoADww1bjx1nA0AHy1oADeJqCc0ZPhAGL0GGYCCHGPoEk4K8AELJIT4Og8EgAdz+xie6SBCQAWshBMlfrETABfEwkAAe+kINmGNjoWnotxWawpmwMoRW-SAA

## Switch

To make sure all values are handled in a `switch` statement, you can
either rely on a
[EsLint switch-exhaustiveness-check rule][switch-exhaustiveness], or
include `default` case with `satisfies never`:

[switch-exhaustiveness]: https://typescript-eslint.io/rules/switch-exhaustiveness-check/

```typescript
switch (thing)
{
  case 'foo':
    console.debug('foo?');
    break;
  case 'bar':
    console.debug('bar.');
    break;
  case 'zar':
    console.debug('zar!');
    break;
  default:
    // This will raise a TS error, if we add a new value
    // without adding a case
    thing satisfies never;
}
```

[Try it out in TypeScript playground][pg:switch]

[pg:switch]: https://www.typescriptlang.org/play/?#code/PTAEl4NxAndAVAnAnqAhgE1QSwHYHNQHs5Q4BTAW3wDds8SAbckrAFwGdQAzOfM0EjZgAsSRGAAkAkgDkA4gGUCRVgHcBAY0GhWzZM0YsAsAChja-Fm2xJshQF5QAeQBGAKxJrmAOi4kSALxIACgBtAHIOfHxQgBpQUKdkOBi4v0TQgF0UdjMLZgBKAG5jZgQABxJYQRpQe0CS8vwOK2l5POCsAFcyJxF0opMjEFApfD0ALjjkdhgq3FDQLF9UElRYnrVkDtYK1SF8DuZQIV1YBSxE7mV2ZY5sAXMCJqEMdmZ8OIio03NLZ9wJmbVezhSLzKaVGjGYwqdSaOqzHB5YwAb2MoFAG22H1BYzR6IxP3wDE8yycHRwgRB+AA-KFCnj0U5SMgANb9dGYirxNK4oz4gkWIkkEkkMkU7lwTx09n4pkkVkyzkpHkMgWsIUisWU1JwACE0tVcoVeJumzozF5-KGgPYqjodGIyBeFWQpz4cG4cFiGCayhd6BQCxIylAlGQdA6JFVQ12gn2hzQmFwgc5qr+eFYuhetxI7EWlBE-QAvsYgA
