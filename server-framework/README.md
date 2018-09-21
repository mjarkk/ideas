# An idea for a new server framework

## Server & database
server: Golang  
database: mongodb

## Some demo sets
- `someName[` means a database  
- `Link: {` is not to link data to an object but more to shows if there are object copied to make it easier to eddit later
- `Link -> HOST` is the main document
- `premissions: {` works mostly the same as links but instaid of a link it shows the groups and users that can access that object

```json
Chats[
  {
    between: [
      {
        name: "Mark",
        username: "testuser",
        email: "some-email@gmail.com",
        premissions: [
          "admin",
          "user"
        ]
      }, {
        ...
      }
    ],
    chat: [
      {
        message: "Test"
        username: "testuser"
      }
    ],
    link: {
      between: "Users",
      chat: {username: "Users/username"}
    },
    premissions: {
      view: {
        HOST: [
          "user:testuser"
          "admin"
        ]
      },
      eddit: {
        HOST: [
          "user:testuser"
          "admin"
        ]
      },
      remove: {
        HOST: [
          "admin"
        ]
      }
    }
  }
]

Users[
  {
    name: "Mark",
    username: "testuser",
    email: "some-email@gmail.com",
    premissions: [
      "admin",
      "user"
    ]
    link: {
      HOST: "Chats/between"
      username: "Chats/chat/username"
    }
  }
]
```

## Security
Document are secured by the `premissions` object inside all documents  
If that object doesn't contain premissions from the user it won't allow the user to eddit, remove, etc

## Routes
My idea is to generate close to all routes automaticly out of the databases  

```
// get everthing as not loged in user
GET: /Chats (no auth)
Res: [
  empty
]

// get everthing as loged in user
GET: /chats (with auth "admin")
Res: [
  {
    between: [
      {
        name: "Mark",
        username: "testuser",
        email: "some-email@gmail.com",
        premissions: [
          "admin",
          "user"
        ]
      }, {
        ...
      }
    ],
    chat: [
      {
        message: "Test"
        username: "testuser"
      }
    ]
  }
]

// Request only ... (based on graphql so only filtering on the user side)
POST /chats/first
POSTDATA: {
  what: `
    between {
      username
    }
  `
}
RES: [
  {
    between: [
      {
        username: "testuser"
      }, {
        username: ...
      }
    ]
  }
]
```

## Auth
Because the user may want to inplement there own version of auth i want them to to add / remove / edit easly.  

## Real time data
Based on the user session ID it will send rests of data and updates (maybe also predictable requesting of data)

## Sending extra information
An example
```
// because of the previous addition it doesn't return the username
// the username will be added again on the user's side
POST /chats/first
POSTDATA: {
  what: `
    between {
      username,
      email
    }
  `,
  previous: [
    `
      between {
        email
      }
    `
  ]
}
RES: [
  {
    between: [
      {
        email: "some-email@gmail.com"
      }, {
        email: ...
      }
    ]
  }
]
```
