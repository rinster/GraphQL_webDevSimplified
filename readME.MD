# Tutorial [Learn GraphQL in 40 Minutes](https://www.youtube.com/watch?v=ZQL7tL2S0oQ&ab_channel=WebDevSimplified)  :link:

**Author: WebDevSimplified**


GraphQL is the hottest technology when it comes to server side APIs for good reason. It is incredibly fast to develop with, and it makes building a front-end painlessly easy. In this video I am going to be teaching you what GraphQL is, why it is important, why it is better than REST, and then we will be walking through an entire Node.js and Express GraphQL API for books and authors. By the end of this video you will have a complete understanding of what GraphQL is and how to use it.

**What is GraphQL?**

GraphQL is a query language and runtime that makes it easier to work with APIs (Application Programming Interfaces). Let's break it down into simpler terms.

Imagine you have a web application that needs to get data from a server. Traditionally, you would use REST APIs, where you have specific endpoints for different types of data. For example, if you wanted to get information about a user, you might have an endpoint like /users/:id.

Now, GraphQL takes a different approach. Instead of having multiple endpoints, you have a single endpoint called the GraphQL server. This server understands your requests and gives you the data you need.

With GraphQL, you define a schema that describes the structure of your data. The schema acts like a blueprint for the types of data available and the operations you can perform. For example, you might have a type called "User" with fields like "name" and "age".

When you want to get data, you send a query to the GraphQL server. In this query, you specify the fields you need and the server responds with exactly that data. This means you can avoid getting extra data that you don't need, which can make your application more efficient.

GraphQL also supports mutations, which are used to modify data on the server. For example, you can have a mutation to create a new user or update an existing user. Mutations are similar to queries but are used for changing data instead of just reading it.

Additionally, GraphQL has a feature called subscriptions. Subscriptions allow you to get real-time updates from the server. For example, if you have a chat application, you can subscribe to receive new messages as they come in.

To use GraphQL, you need a GraphQL server on the backend that understands GraphQL queries, mutations, and subscriptions. On the frontend, you can use libraries or frameworks that help you interact with the GraphQL server and manage your data.

Overall, GraphQL simplifies the process of getting data from a server by providing a single endpoint and allowing you to specify exactly what data you need. It also supports mutations for modifying data and subscriptions for real-time updates. It's a powerful tool that can make your application more efficient and flexible.

--- 

## Basic Setup

1. In your base folder run:
`npm init`

2. Then install the necessary dependencies with
`npm i express express-graphql graphql`

3. Install `nodemon` to watch changes and restart the server
`npm i --save-dev nodemon`

4. Change your package.json file
```
...
"main": "server.js",
  "scripts": {
    "devStart": "nodemon server.js"
  },
...
```
5. Add a `server.js` file in your base folder with the code below:
```
const express = require('express');
const expressGraphQL = require('express-graphql').graphqlHTTP;
const {
    GraphQLSchema,
    GraphQLObjectType,
    GraphQLString
} = require('graphql')
const app = express();

const schema = new GraphQLSchema({
    query: new GraphQLObjectType({
        name: 'HelloWorld',
        fields: () => ({
            message: {
                 type: GraphQLString,
                 resolve: () => 'Hello World' 
            }
        })
    })
})

app.use('/graphql', expressGraphQL({
    schema: schema,
    graphiql: true
}))

app.listen(3000, () => {console.log('Server running')});
``` 

**Running your App**
1. To start your app, in the command line run:
`npm run devStart`

2. Open your browser to the specified port `3000`
`http://localhost:3000/graphql`

3. Voila! This should open up a browser tab to handle your GraphSchema with **GraphiQL** interface

---
## Query with Data

Here we simulate a database with a hardcoded JSON Object
Import the GraphQL methods in the header
```
const {
    GraphQLSchema,
    GraphQLObjectType,
    GraphQLString,
    GraphQLList,
    GraphQLNonNull,
    GraphQLInt
} = require('graphql')
```

Add the JSON data
```
const authors = [
    { id: 1, name: 'J. K. Rowling' },
    { id: 2, name: 'J. R. R. Tolkien' },
    { id: 3, name: 'Brent Weeks' }
]

const books = [
    { id: 1, name: 'Harry Potter and the Chamber of Secrets', authorId: 1 },
    { id: 2, name: 'Harry Potter and the Prisoner of Azkaban', authorId: 1 },
    { id: 3, name: 'Harry Potter and the Goblet of Fire', authorId: 1 },
    { id: 4, name: 'The Fellowship of the Ring', authorId: 2 },
    { id: 5, name: 'The Two Towers', authorId: 2 },
    { id: 6, name: 'The Return of the King', authorId: 2 },
    { id: 7, name: 'The Way of Shadows', authorId: 3 },
    { id: 8, name: 'Beyond the Shadows', authorId: 3 }
]
```

Define your books, authors and root query query
```
// Define the BookType Query
const BookType = new GraphQLObjectType({
    name: 'Book',
    description: 'This represents a book written by an author',
    fields: () => ({
        id: { type: GraphQLNonNull(GraphQLInt) },
        name: { type: GraphQLNonNull(GraphQLString) },
        authorId: { type: GraphQLNonNull(GraphQLInt) },
        author: {
            type: AuthorType,
            resolve: (book) => {
                return authors.find(author => author.id === book.authorId)
            }
        }
    })
})

// Define the AuthorType Query
const AuthorType = new GraphQLObjectType({
    name: 'Author',
    description: 'This represents an author of a book',
    fields: () => ({
        id: { type: GraphQLNonNull(GraphQLInt) },
        name: { type: GraphQLNonNull(GraphQLString) },
    })
})


// Root Query  - Allows us to query from different types of data 
const RootQueryType = new GraphQLObjectType({
    name: 'Query',
    description: 'Root Query',
    fields: () => ({
        books: {
            type: new GraphQLList(BookType),
            description: 'List of All Books',
            resolve: () => books
        }
    })
})
```
Rewrite your base schema to receive your root query, place this near the bottom of the file
```
const schema = new GraphQLSchema({
    query: RootQueryType
})

app.use('/graphql', expressGraphQL({
    schema: schema,
    graphiql: true
}))

app.listen(3000, () => {console.log('Server running')});
```

### How to Query Only Authors

You'll need to add authors to the root query like so
```
// Root Query  - Allows us to query from different types of data 
const RootQueryType = new GraphQLObjectType({
    name: 'Query',
    description: 'Root Query',
    fields: () => ({
        books: {
            type: new GraphQLList(BookType),
            description: 'List of All Books',
            resolve: () => books
        },
        authors: {
            type: new GraphQLList(AuthorType),
            description: 'List of All Authors',
            resolve: () => authors
        }
    })
})
```
### How to Query a Single Book
Update your root query for a single book
```
const RootQueryType = new GraphQLObjectType({
    name: 'Query',
    description: 'Root Query',
    fields: () => ({
        book: {
            type: BookType,
            description: 'A Single Book',
            args: {
                id: { type: GraphQLInt }
            },
            resolve: (parent, args) => books.find(book => book.id === args.id)
        },
        books: {
            type: new GraphQLList(BookType),
            description: 'List of All Books',
            resolve: () => books
        },
        authors: {
            type: new GraphQLList(AuthorType),
            description: 'List of All Authors',
            resolve: () => authors
        }
    })
})
```

---

## GraphQL Query Syntax
### Query all book names 
```
{
  books {
    name
    id
  }
}
```
Will return: 
```
{
  "data": {
    "books": [
      {
        "name": "Harry Potter and the Chamber of Secrets",
        "id": 1
      },
      {
        "name": "Harry Potter and the Prisoner of Azkaban",
        "id": 2
      },
    ]
  }
}
```

### Query all authors through the books
```
{
  books {
    name
    id
    author {
      name
    }
  }
}
```
Will return
```
{
  "data": {
    "books": [
      {
        "name": "Harry Potter and the Chamber of Secrets",
        "id": 1,
        "author": {
          "name": "J. K. Rowling"
        }
      },
      {
        "name": "Harry Potter and the Prisoner of Azkaban",
        "id": 2,
        "author": {
          "name": "J. K. Rowling"
        }
      },
      {
        "name": "Harry Potter and the Goblet of Fire",
        "id": 3,
        "author": {
          "name": "J. K. Rowling"
        }
      },
    ]
  }
}
```

### Querying only author data 
Ensure that you have added authors to the root query

```
const RootQueryType = new GraphQLObjectType({
    name: 'Query',
    description: 'Root Query',
    fields: () => ({
        books: {
            type: new GraphQLList(BookType),
            description: 'List of All Books',
            resolve: () => books
        },
        authors: {
            type: new GraphQLList(AuthorType),
            description: 'List of All Authors',
            resolve: () => authors
        }
    })
})
```

So this:

```
{
    authors {
      name
    }
}

```
Returns this
```
{
  "data": {
    "authors": [
      {
        "name": "J. K. Rowling"
      },
      {
        "name": "J. R. R. Tolkien"
      },
      {
        "name": "Brent Weeks"
      }
    ]
  }
}

```
### Query a single book by ID
```
{
 book(id: 1) {
		name
	} 
}

```
will return this:
```
{
  "data": {
    "book": {
      "name": "Harry Potter and the Chamber of Secrets"
    }
  }
}
```