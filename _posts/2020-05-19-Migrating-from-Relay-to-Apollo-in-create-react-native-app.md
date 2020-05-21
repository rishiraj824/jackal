---
title: Migrating from Relay to Apollo in react native app
desc: Quick steps for Migrating to Apollo from a Relay app
---

Hi, there! I am one of you who was a victim of using an old client of relay on a react native app. This could definitely apply to you if you are using any React Environment. This article covers the steps in detail.
**First Step:**

Easy peasy! Delete all the relay dependencies from your package.json as you won’t need them.
```
dependencies: {
 “react-relay”: “0.0.0-experimental-895a6fe0”,
 “relay-runtime”: “9.1.0”,
},
devDependencies: {
 “relay-compiler”: “9.1.0”,
 “relay-compiler-language-typescript”: “12.0.1”,
}
```

**Second Step:**

Remove all the `*.graphql.js` files, or if you’re using typescript then,`*.graphql.ts` files from the codebase. I have a small script to do it for you. Be careful while executing it though.
```
find . -type f \( -iname ‘*.graphql.ts’ \) -not -wholename ‘*node_modules*’ -exec sh -c ‘rm -rf “$1”’ _ {} \;
```

Also, delete __generated__ directories if you have them.
```
find . -type d \( -iname ‘__generated__’ \) -not -wholename ‘*node_modules*’ -exec sh -c ‘rm -rf “$1”’ _ {} \;
```
**Third Step:**

Install all the apollo libraries which are needed.
Let me save time for you!
```
npm install @apollo/react-hooks apollo-client graphql apollo-client-preset graphql-tag --save
```

**Fourth Step:**

For all the components using `QueryRenderer` you can replace them with `Query`.
`import { Query } from "react-apollo";`

**Fifth Step:**

We will now replace the following lines with appropriate functions that apollo provides us with.
```
import {fetchQuery, graphql, commitMutation} from 'relay-runtime';
```

For **queries**, we use the `useQuery` hook directly for the functional components by importing it from the library we installed earlier, i.e., `@apollo/react-hooks`. An example to use the same is demonstrated in the official docs here. This hook can be replaced with the fetchQuery provided by Relay Modern given that we are using it inside functional components.

For **mutations**, we can use the hook `useMutation` provided by the library and replace all imports with commitMutation directly in our app.

You can also proceed the following way as I mention it below if you think it might help you. Here, we try to create a utility file to use our Apollo Client from one single file.
To do this first,

Add a new `environment.tsx` file as follows inside a folder, call it `config/`

```
import * as SecureStore from "expo-secure-store";
import ApolloClient from "./apollo";
class ApolloClientAPI {
 constructor() {
  const token = SecureStore.getItemAsync("jwt");
  this.client = ApolloClient(token);
 }
 mutate(mutation, variables) {
  return this.client.mutate({ mutation, variables });
 }
 
 query(query, variables) {
  return this.client.query({ query, variables });
 }
}
export default ApolloClientAPI;
```
**Note:** This is a utility file that helps me create new instances of **ApolloClientAPI** to make a mutation or query from inside my components. Feel free to modify the same.
Now we will create the file called applo.tsx which I imported above in `environement.tsx`
```
import { ApolloClient } from "apollo-client";
import { HttpLink, InMemoryCache } from "apollo-client-preset";
/* 
 
The host where my expo app is running is "192.168.1.6:8000", make sure to check this before you paste the code
*/
const API_URL = "http://192.168.1.6:8000/graphql";
const makeApolloClient = (token) => {
 const headers = { 
  headers: `Authorization: Bearer ${token}`,
 };
// create an apollo link instance, a network interface for apollo client
 const link = new HttpLink({
  uri: API_URL,
  ...headers
 });
// create an inmemory cache instance for caching graphql data
const cache = new InMemoryCache();
// instantiate apollo client with apollo link instance and cache instance
const client = new ApolloClient({
 link,
 cache,
});
 return client;
};
export default makeApolloClient;
```

Note: I have installed `apollo-client-preset` package for my project, it is useful to use it for making use of in-memory cache if you don’t need it just remove the cache parameter being passed in the ApolloClient class and it should work fine.

Here’s an example of how you can use the above functions.

```
import gql from "graphql-tag";
import React from "react";
import ApolloClientAPI from "../../config/environment";
// Note: This is just an example query!
const registrationMutation = gql`
  mutation SignupMutation($registrationInput: registerUserInput!) {
    registerUser(input: $registrationInput) {
      viewer {
        id
        jwt
        email
      }
    }
  }
`;
class Signup extends React.Component {
  
 state = {
    email: "",
    username: "",
    password: ""
  };
createAccount = () => {
   
 const { email, username, password } = this.state;
 const client = new ApolloClientAPI();
 client.mutate(registrationMutation, {
        registrationInput: { user: { email: email, password: password }},
  })
  .then((res) => {
       // do something, redirect and store token?
  }).catch((err)=> {
       // handle error
  })
 render() {
     // your components
 }
}
```

That’s it, folks! Your app now uses the latest Apollo Client. Thanks for reading!!