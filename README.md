> [!TIP]
> Write code in Rust instead. Rust avoids the problems here by having *traits*, which are very cool.

# Stop writing `class` in JavaScript
This writing is to try to convince you to stop doing this:
```js
class Person {
  constructor (name: string) {
    this.name = name  
  }
  
  sayHello () {
    return `Hello ${this.name}`
  }
  
  sayBye () {
    return `Bye ${this.name}`
  }
}
```
and do this instead:
```js
const sayHello = name => `Hello ${name}`

const sayBye = name =>  `Bye ${name}`
```

## Advantages To Switching
### Can have multiple files
`class`: In the example, the `class` is very small and simple. Real `class`s are often >100 lines long. It can get very messy and hard to understand what is going on.

No `class`: You can split your code into however many files you want. The file name can make it clear what that file's code is doing, and it's easier to understand when you see less lines of code.

Example:
`sayHello.js`
```js
// Say hello
```

`sayBye.js`
```js
// Say bye
```

### Code Splitting
When you write a `class`, sometimes not all of its methods are used. `class` is harder to do code splitting than functions. It really bothers me when I am using a library which has a `class` with a lot of methods that I'm not going to use:
```js
class Counter {
  set () {}
  reset () {}
  add () {}
  addOne () {}
  subtractOne () {}
  subtract () {}
}
```

### Can't extend >1 `class`
Here is an example of two usefull `class`s:
```js
class MultiKeyMap extends Map {
  // Methods overrided to use multiple keys 
  get () {}
  set () {}
  has () {}
  delete () {}
}
```
```js
class DefaultedMap {
  constructor (getDefault) {}
  
  // Calls `getDefault` if no key
  get () {}
}
```
What if you wanted a `DefaultedMultiKeyMap`? There is no way to do this that isn't messy. Here is a messy way, based on [this StackOverflow answer](https://stackoverflow.com/a/62778691/11145447):
```js
const getDefaultedMap = BaseMap => class extends BaseMap {
  // Defaulted map overrides
}

const DefaultedMultiKeyMap = getDefaultedMap(MultiKeyMap)
```
One problem with this is that it relies on the defaulted map to be a function which returns a custom `class`. If you're using a js library and it's a class, it probably doesn't have a way to be based off of two classes.

Here is what you can do using flexible functions:

`multiKeyMap/get.js`
```js
const get = (keys, internalGet) => {}
```
`defaultedMap/get.js`
```js
const get = (key, defaultFn, internalGet) => {}
```
`normalMap/get.js`
```js
const get = (map, key) => map.get(key)
```
Your code
```js
const normalMap = new Map()

getOrDefault(['key1', 'key2'], keys => getMultiKey(keys, keys => normalMapGet(normalMap, keys)))
```
With this way the only *data* you have to store is a normal `Map`!

## Using flexible functions
The examples will use TypeScript because it's easier to understand the data types.

### Emitter example
There are so many different emitters you can use in JavaScript. The most common emitters are [Node.js `EventEmitter`](https://nodejs.dev/learn/the-nodejs-event-emitter) and [`eventemitter3`](https://www.npmjs.com/package/eventemitter3). Both of these emitters are `class`. This example will show a very flexible emitter, which is emitter logic which you can wrap in anything.
```ts
type ForEachCallback<ItemData> = (itemData: ItemData) => void
type ForEach<ForEachData, ItemData> = (data: ForEachData, callback: ForEachCallback<ItemData>) => void

type Listener<T extends unknown[]> = (...inputs: T) => void
const emit = <ForEachData, T extends unknown[]>(
  forEachData: ForEachData, 
  forEach: ForEach<ForEachData, Listener<T>>
): void => {
  forEach(forEachData, listener => listener())
}
```
Things that make this emitter very flexible:
- No requirement of needing a `Set`, `Array`, or `Map` to store listeners. Instead of using `Array.prototype.forEach()`, it lets you customize the forEach function.
- No requirement of how you add / remove listeners

Example usages of `emit`:

With `Array`
```ts
const arrayForEach: ForEach<unknown[], unknown> = (arr, callback) => arr.forEach(callback)

emit(myArrayOfListeners, arrayForEach)
```

With `Set`
```ts
const setForEach: ForEach<Set<unknown>, unknown> = (set, callback) => set.forEach(callback)

emit(mySetOfListeners, arrayForEach)
```

## Contributing
### Adding a bad thing about not using `class`
Create an issue with a reason that is supporting `class`

### Adding another reason to not use `class`
Create an issue or pull request

### Talking about this topic
Open a discussion
