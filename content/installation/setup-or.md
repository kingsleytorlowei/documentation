---
title: "Setup OpenReplay"
metaTitle: "Setup OpenReplay"
metaDescription: "How to quickly setup OpenReplay and start recording sessions and metrics."
---

Getting started with OpenReplay is straightforward. There are 2 ways you can get OpenReplay up and running: using the script or the npm package.

## Using Script

Copy/paste the code snippet directly into the `</head>` of your web app. The script can be found in OpenReplay dashboard under 'Preferences > Projects' and is unique to each project.

If your website supports Google Tag Manager, then you may follow the [GTM step-by-step guide](/integrations/google-tag-manager).

## Using NPM

First install the npm package.

```bash
npm i @openreplay/tracker
```

Then, initialize the package from your codebase entry point and start the tracker. You must set the `projectKey` option in the constructor. Its value can can be found in your OpenReplay dashboard under 'Preferences > Projects'.

### If your website is a Single Page Application (SPA)

```js
import OpenReplay from '@openreplay/tracker';
//...
const tracker = new OpenReplay({
  projectKey: PROJECT_KEY
});
tracker.start();
```

### If your web app is Server-Side-Rendered (SSR)

Follow the below snippet if your web app is Server-Side-Rendered (SSR) (i.e. NextJS, NuxtJS). Ensure `tracker.start()` is called once the app is started (in `useEffect` or `componentDidMount`).

```js
import OpenReplay from '@openreplay/tracker/cjs';
//...
const tracker = new OpenReplay({
      projectKey: PROJECT_KEY,
      onStart: () => { tracker.setUserID('MY_USER_ID'); } // optional
});
//...
function MyApp() {
  useEffect(() => { // use componentDidMount in case of React Class Component
    tracker.start();
  }, []);
}
```

If you would like to go further in configuring the tracker, check [JavaScript SDK](/installation/javascript-sdk#options) for the list of available options.

## Next Steps

Now that you have installed the tracker and deployed your web app, session recordings should appear within few minutes. Well done!

In the meantime, you can further in the setup process to get the most out of OpenReplay. The more you capture the better for quickly reproducing and fixing issues:
- [Identify a User](/installation/identify-a-user): Associate your internal user ID with the session being recorded.
- [Metadata](/installation/metadata): Also referred to as traits or user variables, metadata provides additional information about users while recording sessions.
- [Custom Events](/installation/custom-events): Add more intelligence by recording domain-specific events alongside session replays.
- [Upload Source-Maps](/installation/upload-sourcemaps): See the source code context obtained from stack traces in their original form.
- [Plugins](/plugins): Understand what's happening under the hood by capturing your application's store and monitoring your HTTP and GraphQL queries.
- [Integrations](/integrations): Plug OpenReplay with your logging tools and sync backend errors with session replays.
