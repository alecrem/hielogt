# Phaser Next.js Template

This is a Phaser 3 project template that uses the Next.js framework. It includes a bridge for React to Phaser game communication, hot-reloading for quick development workflow and scripts to generate production-ready builds.

## Requirements

[Node.js](https://nodejs.org) is required to install dependencies and run scripts via `pnpm`.

## Available Commands

| Command          | Description                                    |
| ---------------- | ---------------------------------------------- |
| `pnpm install`   | Install project dependencies                   |
| `pnpm run dev`   | Launch a development web server                |
| `pnpm run build` | Create a production build in the `dist` folder |

## Writing Code

After cloning the repo, run `pnpm install` from your project directory. Then, you can start the local development server by running `pnpm run dev`.

The local development server runs on `http://localhost:8080` by default. Please see the Next.js documentation if you wish to change this, or add SSL support.

Once the server is running you can edit any of the files in the `src` folder. Next.js will automatically recompile your code and then reload the browser.

## Template Project Structure

We have provided a default project structure to get you started. This is as follows:

| Path                      | Description                                                                                                              |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `src/pages/_document.tsx` | A basic Next.js component entry point. It is used to define the `<html>` and `<body>` tags and other globally shared UI. |
| `src`                     | Contains the Next.js client source code.                                                                                 |
| `src/styles/globals.css`  | Some simple global CSS rules to help with page layout. You can enable Tailwind CSS here.                                 |
| `src/page/_app.tsx`       | The main Next.js component.                                                                                              |
| `src/App.tsx`             | Middleware component used to run Phaser in client mode.                                                                  |
| `src/PhaserGame.tsx`      | The React component that initializes the Phaser Game and serves as a bridge between React and Phaser.                    |
| `src/game/EventBus.ts`    | A simple event bus to communicate between React and Phaser.                                                              |
| `src/game`                | Contains the game source code.                                                                                           |
| `src/game/main.tsx`       | The main **game** entry point. This contains the game configuration and starts the game.                                 |
| `src/game/scenes/`        | The Phaser Scenes are in this folder.                                                                                    |
| `public/favicon.png`      | The default favicon for the project.                                                                                     |
| `public/assets`           | Contains the static assets used by the game.                                                                             |

## React Bridge

The `PhaserGame.tsx` component is the bridge between React and Phaser. It initializes the Phaser game and passes events between the two.

To communicate between React and Phaser, you can use the **EventBus.js** file. This is a simple event bus that allows you to emit and listen for events from both React and Phaser.

```js
// In React
import { EventBus } from "./EventBus";

// Emit an event
EventBus.emit("event-name", data);

// In Phaser
// Listen for an event
EventBus.on("event-name", (data) => {
  // Do something with the data
});
```

In addition to this, the `PhaserGame` component exposes the Phaser game instance along with the most recently active Phaser Scene using React forwardRef.

Once exposed, you can access them like any regular react reference.

## Phaser Scene Handling

In Phaser, the Scene is the lifeblood of your game. It is where you sprites, game logic and all of the Phaser systems live. You can also have multiple scenes running at the same time. This template provides a way to obtain the current active scene from React.

You can get the current Phaser Scene from the component event `"current-active-scene"`. In order to do this, you need to emit the event `"current-scene-ready"` from the Phaser Scene class. This event should be emitted when the scene is ready to be used. You can see this done in all of the Scenes in our template.

**Important**: When you add a new Scene to your game, make sure you expose to React by emitting the `"current-scene-ready"` event via the `EventBus`, like this:

```ts
class MyScene extends Phaser.Scene {
  constructor() {
    super("MyScene");
  }

  create() {
    // Your Game Objects and logic here

    // At the end of create method:
    EventBus.emit("current-scene-ready", this);
  }
}
```

You don't have to emit this event if you don't need to access the specific scene from React. Also, you don't have to emit it at the end of `create`, you can emit it at any point. For example, should your Scene be waiting for a network request or API call to complete, it could emit the event once that data is ready.

### React Component Example

Here's an example of how to access Phaser data for use in a React Component:

```ts
import { useRef } from 'react';
import { IRefPhaserGame } from "./game/PhaserGame";

// In a parent component
const ReactComponent = () => {

    const phaserRef = useRef<IRefPhaserGame>(); // you can access to this ref from phaserRef.current

    const onCurrentActiveScene = (scene: Phaser.Scene) => {

        // This is invoked

    }

    return (
        ...
        <PhaserGame ref={phaserRef} currentActiveScene={onCurrentActiveScene} />
        ...
    );

}
```

In the code above, you can get a reference to the current Phaser Game instance and the current Scene by creating a reference with `useRef()` and assign to PhaserGame component.

From this state reference, the game instance is available via `phaserRef.current.game` and the most recently active Scene via `phaserRef.current.scene`.

The `onCurrentActiveScene` callback will also be invoked whenever the the Phaser Scene changes, as long as you emit the event via the EventBus, as outlined above.

## Handling Assets

To load your static games files such as audio files, images, videos, etc place them into the `public/assets` folder. Then you can use this path in the Loader calls within Phaser:

```js
preload();
{
  //  This is an example of loading a static image
  //  from the public/assets folder:
  this.load.image("background", "assets/bg.png");
}
```

When you issue the `pnpm run build` command, all static assets are automatically copied to the `dist/assets` folder.

## Deploying to Production

After you run the `pnpm run build` command, your code will be built into a single bundle and saved to the `dist` folder, along with any other assets your project imported, or stored in the public assets folder.

In order to deploy your game, you will need to upload _all_ of the contents of the `dist` folder to a public facing web server.

## Customizing the Template

### Next.js

If you want to customize your build, such as adding plugin (i.e. for loading CSS or fonts), you can modify the `next.config.mjs` file for cross-project changes, or you can modify and/or create new configuration files and target them in specific npm tasks inside of `package.json`. Please see the [Next.js documentation](https://nextjs.org/docs) for more information.

## Join the Phaser Community!

We love to see what developers like you create with Phaser! It really motivates us to keep improving. So please join our community and show-off your work 😄

**Visit:** The [Phaser website](https://phaser.io) and follow on [Phaser Twitter](https://twitter.com/phaser_)<br />
**Play:** Some of the amazing games [#madewithphaser](https://twitter.com/search?q=%23madewithphaser&src=typed_query&f=live)<br />
**Learn:** [API Docs](https://newdocs.phaser.io), [Support Forum](https://phaser.discourse.group/) and [StackOverflow](https://stackoverflow.com/questions/tagged/phaser-framework)<br />
**Discord:** Join us on [Discord](https://discord.gg/phaser)<br />
**Code:** 2000+ [Examples](https://labs.phaser.io)<br />
**Read:** The [Phaser World](https://phaser.io/community/newsletter) Newsletter<br />

Created by [Phaser Studio](mailto:support@phaser.io). Powered by coffee, anime, pixels and love.

The Phaser logo and characters are &copy; 2011 - 2025 Phaser Studio Inc.

All rights reserved.
