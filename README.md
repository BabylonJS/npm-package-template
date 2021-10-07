# Babylon.js NPM Package Template

## Quick Start Guide

1. Use GitHub to create `your-new-repo` from this template
2. In the directory in which you want to develop, clone `your-new-repo`
3. `cd your-new-repo`
4. `npm install`: NPM will install required dependencies for all projects
5. `npm run dev`: NPM will build all projects in watch mode and launch a 
test page

## About This Repository

This repository is intended to be a "jumping off point" for moving
ideas from prototyping into active development. The tools supplied and 
practices established by this repository are focused on setting up 
a simple, versatile, and reliable workflow for creating Babylon.js 
experiences which can subsequently be integrated in other projects for 
circulation. For more information, check out the
[docs page](https://TODO-make-a-docs-page-for-this).

## Repository Structure

This repository (and any new repository built using it as a template) has
three principle sections: the `app_package`, the `test_package`, and 
the `root`.

### The `app_package`

The `app_package`, consisting of the "app_package" directory and everything
in it, is the core of this repository which everything else exists to 
support. It is an NPM package which is intended to encapsulate the 
Babylon.js experience -- and *only* the Babylon.js experience -- for which 
this repository was created. It shouldexpose a minimal outward-facing 
contract (i.e., as few public methodsas possible) so that it can be as easy 
as possible to add to Web apps, PWAs, and whatever other platforms will be 
used to circulate this experience. Of the three sections of this 
repository, this is the only one designed to be exported as a separate NPM 
package for integration intosubsequent projects.

### The `test_package`

The `test_package`, consisting of the "test_package" directory and 
everything in it, exists exclusively as a test harness for the 
`app_package`. Essentially `test_package` is a minimalistic standalone Web 
app which consumes `app_package` through NPM in the same way a real 
shipping experience might. This allows the usage, integration, and 
functionality of `app_package` to be tested in isolation, decoupling the 
development of the 3D experience itself from the development of the Web app/
PWA/etc. in which it will eventually be circulated.

### The `root`

The `root`, consisting of the repository's root directory and everything in 
it, exists only to provide "glue" and development shortcuts making the 
`app_package` and the `test_package` easier to use.

- **`npm install`**: Running this command in the root directory will 
recursively install all NPM dependencies for the root, `app_package`, 
and `test_package`.
- **`npm run dev`**: Running this command in the root directory is a 
shortcut for running the same command in both `app_package` and 
`test_package`. This will cause both packages to be built in watch mode, 
launching a browser showing the `test_package` experience which will be 
updated and redeployed whenever the files in `app_package` and 
`test_package` are changed.
- **`npm run build`**: Running this command in the root directory is a
shortcut for running the same command in `app_package` and `test_package`
sequentially. This will cause both packages to be built and a standalone
single-page Web app to be output in the "docs" directory, which can be 
set up to be hosted as a 
[GitHub Pages](https://guides.github.com/features/pages/) site.

### The Missing Piece: Asset Hosting

Notably missing from this repository is any provision for hosting 
assets -- 3D models, textures, sound files, and so on which are used by
`app_package` as part of the Babylon.js experience. The reason for this
is that asset development for 3D experiences generally follows a very
different workflow from software development, often requiring different
source control, access, licensing, deployment, and many other 
considerations. For this reason, we recommend keeping asset 
development/hosting and code development/hosting separate, especially 
during experience maturation, to allow these dissimiliar tasks to be 
addressed independently with task-appropriate solutions. Consequently,
asset hosting is excluded from this repository's intended responsibilities
and is instead left to its sibling repository, the 
[asset-host-template](https://github.com/BabylonJS/asset-host-template).

## Getting Started

All steps assume a git- and NPM-enabled command line or terminal. For a 
more in-depth tutorial, please consult the [Golden Path tutorial in the 
docs](https://TODO-make-a-docs-page-for-this)

### Bootstrapping

1. Create a new repository using this repo as a template repository. In 
the following steps, this new repository will be called `your-new-repo`.
2. Clone `your-new-repo` locally.
3. `cd` to the root of your clone.
4. Run `npm install` to install all the NPM dependencies for 
`your-new-repo`.
5. Run `npm run dev` to run a dev build. After a moment, your default 
browser should open to http://localhost:8080, which will display the
`test_package` experience.

### Changing the Experience

1. In your preferred code editor, open ./app_package/src/Playground/
playground.ts. This file contains the default experience source code.
2. In playground.ts, change the line that sets `sphere.position.y = 1` to 
set it to `3` instead.
3. Save your changes. After a moment, the browser page which launched 
showing the `test_package` should automatically reload, showing the effect 
of your changes.
4. If you are starting from code you explored in the TypeScript Playground,
you can try copy-pasting your Playground code to replace the `Playground` 
class in playground.ts. **NOTE**: Depending on what features your code 
uses, this may not work out-of-the-box if the features you're using aren't
imported in playground.ts by default.

### Moving Beyond playground.ts

Playground.ts exists to provide a familiar "landing site" for developers
progressing their Babylon.js ideas from exploration in (for example) 
the TypeScript Playground to maturation in the npm-package-template. 
However, for these ideas to mature into a clean, robust, and maintainable
codebase, the code should move beyond Playground-specific practices such
as the use of the `BABYLON` namespace and `import "*"`. For this reason, it 
is recommended that, soon after repo creation, all functionality should be
moved out of playground.ts into independent files, whereupon Playground.ts
should be deleted. To illustrate this, the following example shows how one
might port over the 
[Simple Sinusoid Bouncing Ball](https://playground.babylonjs.com/#8WLKPR) 
Playground.

```ts
class Playground {
    public static CreateScene(engine: BABYLON.Engine, canvas: HTMLCanvasElement): BABYLON.Scene {
        const scene = new BABYLON.Scene(engine);
        const sphere = BABYLON.Mesh.CreateSphere("sphere", 16, 2, scene);
        sphere.position.y = 1;
        BABYLON.Mesh.CreateGround("ground1", 6, 6, 2, scene);
        scene.createDefaultCameraOrLight();

        scene.onBeforeRenderObservable.runCoroutineAsync(function* () {
            for (let frameCount = 0; true; ++frameCount) {
                sphere.position.y = 1 + 2 * Math.abs(Math.sin(frameCount / 16));
                yield;
            }
        }());

        return scene;
    }
}
```

1. After finishing step 4 in the prior section, the above Playground code 
should have been pasted over the Playground code in playground.ts, leaving
the imports at the top and the export at the bottom of that file intact. 
The bouncing ball should already be visible in the `test_package` page.
2. Rather than create our scene from a static method in an empty class, we
wish to create a scene type to represent this specific scene. To do this,
we create a new file called bouncingBallScene.ts adjacent to 
playgroundRunner.ts, filling it with the following contents adapted from 
the original Playground code:
```ts
import { Engine, Mesh, Scene } from "@babylonjs/core";

export class BouncingBallScene extends Scene {
    constructor (engine: Engine) {
        super(engine);
        const sphere = Mesh.CreateSphere("sphere", 16, 2, this);
        sphere.position.y = 1;
        Mesh.CreateGround("ground1", 6, 6, 2, this);
        this.createDefaultCameraOrLight();

        this.onBeforeRenderObservable.runCoroutineAsync(function* () {
            for (let frameCount = 0; true; ++frameCount) {
                sphere.position.y = 1 + 2 * Math.abs(Math.sin(frameCount / 16));
                yield;
            }
        }());
    }
}

```
3. We may also choose to create a bouncingBall.ts file to factor out 
functionality further, modifying bouncingBallScene.ts to use it.
```ts
import { Mesh, Scene, TransformNode } from "@babylonjs/core";

export class BouncingBall extends TransformNode {
    constructor (scene: Scene) {
        super("bouncingBall", scene);
        const sphere = Mesh.CreateSphere("sphere", 16, 2, scene);
        sphere.setParent(this);

        this.position.y = 1;

        scene.onBeforeRenderObservable.runCoroutineAsync(this._bounceCoroutine());
    }

    private *_bounceCoroutine() {
        for (let frameCount = 0; true; ++frameCount) {
            sphere.position.y = 1 + 2 * Math.abs(Math.sin(frameCount / 16));
            yield;
        }
    }
}
```

```ts
import { Engine, Scene } from "@babylonjs/core";
import { BouncingBall } from "./bouncingBall";

export class BouncingBallScene extends Scene {
    constructor (engine: Engine) {
        super(engine);
        new BouncingBall(this);
        Mesh.CreateGround("ground1", 6, 6, 2, this);
        this.createDefaultCameraOrLight();
    }
}

```
4. We can now change playgroundRunner.ts create the `BouncingBallScene` 
intead of calling into Playground code.
```ts
    ...
    const canvas = options.canvas;
    const engine = new Engine(canvas);
    let scene = new BouncingBallScene(engine);
    ...
```
5. Now that all references to playground.ts are removed, we can rename
playgroundRunner.ts to runtime.ts (or something similar to signify that it
no longer runs Playground code) and delete the Playground folder 
altogether.
6. With these steps done, the code from our Playground-based exploration 
has been neatly refactored into multiple clean TypeScript files, ready to
continue maturing into a robust, maintainable, and sustainable codebase.

## Licensing

This template repository is intended to be used as a starting point for
other projects, so its own license is not expected to persist into those
projects. By default, NPM packages automatically adopt the ISC license,
so that default choice is left intact in the three package.json files
present in this template.
