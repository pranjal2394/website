# Website

TODO: Rewrite .js files in .ts files

# Prerequesites

## Node and npm

`Google install node and npm for your system (windows, mac, linux)`

## Typescript

`npm install -g typescript`

## Webpack

`npm install -g webpack`
`npm install -g webpack-cli` (not sure if we need that or webpack includes it already)

## Yarn

`npm install -g yarn`

## Project's Dependencies

Run `yarn install` in root and `yarn install` in `frontend/`

# Development

- Go to `frontend/` folder and run `yarn webpack:watch` to create a bundle on each change
- Run typescript watcher via `yarn watch:ts` in root dir to transpile ts files under `src/` on each change
- Start development server via `yarn dev` in root dir


# Production

Order of steps is important
- Run `yarn webpack:bundle` to bundle the frontend for production
- Transpile backend's typescript via running `yarn transpile:ts` in root dir 
- Start production server via `yarn prod` in root dir, it would run on port 5000

## Container

We use Docker in Production as Container Engine. You can find the Dockerfile in the root-Directory.
If you want to run the production Container locally, run:
- `docker build -t <image_name>:<tag> .`
- `docker run -it -p <local_port>:5000 <image_name>:<tag>`
- The application is reachable under `localhost:<local_port>`
- To stop the container simply use `Ctrl-C`

# How it works

#### Backend

In `bin/www.js` (mostly autogenerated code) you could find the initial setup of the server that is highly dependent on `app.js` where we define routes, middleware etc.

The server code should go under `src/`. Running `yarn watch:ts` in the root folder will set a watcher on each typescript file under `src/`. The watched typescript files would be transpiled to `js` on each change. 

Running `yarn dev` would start the server that would self-restart on each change. The server would also restart on each frontend change, because the frontend bundle will change and the server has to serve the new one. 

#### Frontend

In dev mode, the frontend is bundled via webpack by running `yarn webpack:watch`. On each change, webpack will generate new bundle under `frontend/dist/`, the server will restart and reference the new bundle automatically.

The server uses two bundles.

The one webpack generates as a library - `bundle-server.js` - so that Node can import it via `const frontend = require('./frontend/dist/bundle-server');` to be able to server-render.

The second bundle is referenced in the server-rendered html via `<script type="text/javascript" src="dist/bundle.${buildhash}.js"></script></body>` so that ReactDOM can hydrate the server-side rendered markup.


> If you call ReactDOM.hydrate() on a node that already has this server-rendered markup, React will preserve it and only attach event handlers, allowing you to have a very performant first-load experience.

On top, a new hash of the bundle is generated on each change so that the name of the bundle changes to `bundle.${buildhash}.js` and the old (browser-side cached) bundle becomes obsolete.



