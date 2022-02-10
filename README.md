## How to optimize large react codebase

-   If you need to optimize the first page load (the bundle size), use the webpack-bundle-analyzer npm package to get a snapshot of your dependencies sizes. Check if the biggest dependencies are imported unnecessarily. The network panel in dev tools will also help you see if some request are slowing down page load. You can use code splitting if you bundle is too large with dynamic `import` You can also use `React.Suspense` combine with `React.lazy` to lazy-load some part of the application. You can reduce your images sizes with a tool like compressor.io

-   If you need to optimize the performance of the application itself the best tool is the React Profiler (in the dev tools) that will help you pinpoint bottlenecks, What components are slow to render and how many children component they in turn render. Most performance issues come from unnecessary re-renders. You can usually fix those issues using `useCallback`, `useMemo`, `memo`. If the app is rendering large list of items you can try virtualising it (react-window npm package). For performance gain you should also consider server-side rendering with frameworks such as NextJs (can also help with SEO).

### During development, use:

-   [why-did-you-render](https://github.com/welldone-software/why-did-you-render) to avoid unnecessary renders, try to fix them with `useCallback`, `useMemo`, `memo`.
-   [redux-logger](https://www.npmjs.com/package/redux-logger) to keep track of all redux actions dispatched
-   Use [Lighthouse](https://developers.google.com/web/tools/lighthouse) in dev tools for performance monitoring
-   Use the [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost) to keep track of your dependencies sizes.
-   Use end-to-end testing (e.g. [Cypress](https://www.cypress.io/)) to keep track of he performance of the page load.
-   Keep your components small so the application is more maintanable, fixable if issues arise (90% of my files are under 100 lines of code, even with styling included alongside the markup).

## What strategies do you use to keep your CSS maintainable? How does that approach compare to others?

Back in the days we all used to write big CSS files that were indeed a challenge to maintain (bloated files, cascading issues, unexpected inheritance,...), pretty much the worst part of web application development. The came React with a new philosophy: "self-contained" components with logic placed alongside the markup. And then a trend to also include the styling in there. I personally embrace it so I like to use [Styled Components](https://styled-components.com/). You can see straight away what style applies to a particuar component, with no cascading/inheritance issues. The idea is that if you need to re-use the same style in various places in the application it probably means that most of the time you need to actually re-use this specific component so it makes sense to keep markup + style together. Then for global style I like to use a UI library such as [Material UI](https://mui.com/) that makes it easy to define `themes` that you call from the root of the app. I have never had any real CSS headache since, and I actually enjoy styling my components now. CSS modules also is a good approach but Styled Components feels more "react" idiomatic rather than having classes that also can make your markup less readable. With tools like [SASS](https://sass-lang.com/) you can get smaller files than with pure CSS but you end up with variables that can then be overwritten, you need to import other files, also there is an additional compiling step... My experience is, as always with software: better to keep it simple and thereore more readable/maintanable.

## Please explain the latest feature that you worked on and how you implemented it.

-   Video to GPT3 fine-tuning (Not latest feature but more interesting):

    1. User uploading a new video in Google storage triggers a cloud function that extracts the audio from the video ([fluent-ffmpeg](https://www.npmjs.com/package/fluent-ffmpeg)).
    2. A cloud function then transcript the speech to text ([Goolge cloud Speech-to-text](https://cloud.google.com/speech-to-text)) and stores the result for this particular user.
    3. On request all the aggregated text is sent to our server ([Ngrok](https://ngrok.com/)).
    4. A python api ([Flask](https://flask.palletsprojects.com/en/2.0.x/)) get the requests and starts a GPT3 fine-tuning on it to create a modelisation of the user speech.
    5. The firestore collection for this user is updated when model is created and the front-end is notified (through a [firestore subscription](https://firebase.google.com/docs/firestore/query-data/listen)).

-   [Torus Login](https://toruswallet.io/)
    1. Read some of the documentation to figure out what part of the API you need for you use case, and to understand how the technology works under the hood.
    2. Look if integration examples are provided for your use case ([React](https://github.com/torusresearch/torus-embed/tree/master/examples/react-basic-app)).
    3. Make it work in a sample app to see what the flow is like, to pinpoint any issues, what the api is returning.
    4. Write a service as a wrapper around the api.
    5. Write a small test ([Testing Library](https://testing-library.com/docs/react-testing-library/intro/)) while mocking your service api wrapper ([Jest](https://jestjs.io/)) and implement the feature in the real iteratively.
