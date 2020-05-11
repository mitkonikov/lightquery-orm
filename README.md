# LightQuery

World's first library that allows client side database query without writing back-end resolvers. This library works on top of the typed repositories from TypeORM. It's developed by SchoolNet. For security concerns read [here](#security).

## Features

Write query from the client side using nothing but a simple JSON syntax.
 * Select
 * Where
   - `like` statement
   - `limit`

## Example syntax on the client side:

```js
let body = {
    word: {
        select: ["Word", "Type"],
        where: { ID: 3 }
    },
    generated_word: {
        select: ["Word"]
    }
}
```

This is an example JSON syntax for the body of the request to Light. `"word"` is the name of the repository in the switch function (see below). `select` is required, while `where` is not. 


## Switch function

The switch function is the only function that you need to implement in your backend in order to tell LightQuery which repositories can be accessed directly from the client side.

```js
let switchCallback = (key) => {
    let repository: any;
    let failed: boolean;

    switch(key.toLowerCase()) {
        case "word": // this is the name of the repository
            repository = Words; // you give access to this repository
            break;
        case "generated_word":
            repository = WordGenerated;
            break;
        case "word_of_the_day":
            repository = WordDay;
            break;
        default:
            // you set failed to true
            // when the key doesn't match a repository
            failed = true;
            break;
    }

    return {
        repository,
        failed
    }
}
```

### Implementation

If you want to see real-world implementation of this library, check out the [ZBORAPI.ts](https://github.com/mitkonikov/SchoolNet/blob/master/src/ZBORAPI.ts) file.

### Details

#### Like statement

If you want to introduce a like statement in any query you would do it like this.

```js
let body = {
    word: {
        select: ["Word", "Type"],
        where: { Word: "%thing%" }
    }
}
```

#### Limit

For limiting the number of queries you insert a limit variable in the where clause.

```js
let body = {
    word: {
        select: ["Word", "Type"],
        where: { Word: "something", limit: 3 }
    }
}
```

### Security

This library sits on top of TypeORM, so the only repositories it has contact with are these you give access to with the switch function. The only thing that the client can do right now, is to SELECT rows from specific tables. It is not possible to INSERT, UPDATE or DELETE anything in any of the tables.

> In conclusion: you have nothing to worry about.

### Contribute

Everyone is welcomed with their knowledge and time to contribute to this remarkable library.

### Future

We are looking to make it available for MySQL without the need of TypeORM.
