## Next Redux TOOLKIT Wrapper along with Redux Persist (& redux-thunk too) - A ready boilerplate for NEXT JS projects with persistent global state ✨

# Note: This is a fork from https://github.com/fazlulkarimweb/with-next-redux-wrapper-redux-persist remodelled to work with redux toolkit.#

I just needed a simple persistent global state for my Next.js website. This is a redux boilerplate and I wrote it for myself at first to speed up my future project. Now I added extensive comment to help people understanding the whole implementation.

Using a persistent global state in a Next js website is much more daunting than I thought! See my angry [tweet](https://twitter.com/fazlulkarimweb/status/1266463218265812992) 😂. You have to have a solid understanding of client-side and server-side rendering & how Next js is dealing with it! Obviously don't forget about the persistency feature and some performance drawbacks in the case of server-side rendering. So I created this boilerplate containing a classic counter app example.

![Counter Example](https://github.com/fazlulkarimweb/with-next-redux-wrapper-redux-persist/blob/master/public/Homepage.gif?raw=true)

## Why this boileplate? 😰

Copy and pasting redux boilerplate code without understanding seems a norm to people who are relatively new to the redux ecosystem. If you are new to redux, you may use this boilerplate to copy and paste and attain the functionality right away. Okay, you can do this. But it will be more helpful if you understand the code and implement it by yourself. You may take help from this boilerplate code. I didn't find a boilerplate like this in the next js example section. Besides I used hook every time in my redux code. The example in the next js repo weren't written with the hooks. Hooks in redux are elegant and powerful.😍

So I decided to write one for myself!

## Enough Talk, Show me the code

What should be the structure of your redux store?

```javascript
// ./store/store
import { configureStore, getDefaultMiddleware } from '@reduxjs/toolkit'

import { combineReducers } from 'redux'
import { createWrapper, HYDRATE } from 'next-redux-wrapper'

import {
  persistStore,
  persistReducer,
  FLUSH,
  REHYDRATE,
  PAUSE,
  PERSIST,
  PURGE,
  REGISTER,
} from 'redux-persist'
import storage from 'redux-persist/lib/storage'

import counter from "./counter/reducer";

//COMBINING ALL REDUCERS

const rootReducer = combineReducers({
  list,
  // other reducers here
})

const makeStore = ({ isServer }) => {
  if (isServer) {
    //If it's on server side, create a store
    return configureStore({
      reducer: rootReducer,
    })
  } else {
    //If it's on client side, create a store which will persist
    // const storage = storage.default

    const persistConfig = {
      key: 'root',
      version: 1,
      blacklist: [],
      storage,
    }

    const persistedReducer = persistReducer(persistConfig, rootReducer) // Create a new reducer with our existing reducer

    const store = configureStore({
      reducer: persistedReducer,
      middleware: getDefaultMiddleware({
        serializableCheck: {
          ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
        },
      }),
    })

    store.__persistor = persistStore(store) // This creates a persistor object & push that persisted object to .__persistor, so that we can avail the persistability feature

    return store
  }
}
// Export the wrapper & wrap the pages/_app.js with this wrapper only

export const wrapper = createWrapper(makeStore)
```

View the file structure along with the code

![Code with the file structure](https://github.com/fazlulkarimweb/with-next-redux-wrapper-redux-persist/blob/master/public/_appjs.png?raw=true)

## How you should wrap the \_app js file

Screenshot for understanding the file structure along with the code.

![Code with the file structure](https://github.com/fazlulkarimweb/with-next-redux-wrapper-redux-persist/blob/master/public/next%20app%20wrapper.png?raw=true)

## External API call & NEXT RUN BUILD

I tried to add some external API call to see the hydration and rehydration process. Have a look into the SSG, Static & SSR rendering of the pages in this boilerplate. Seems good to me.

![Code with the file structure](https://github.com/fazlulkarimweb/with-next-redux-wrapper-redux-persist/blob/master/public/Next_Build.png?raw=true)

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

[MIT](https://choosealicense.com/licenses/mit/)
