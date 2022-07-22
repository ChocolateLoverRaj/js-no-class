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
