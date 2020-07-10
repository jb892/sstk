SSTK: SmartScenes ToolKit
============

The (S)STK provides:

1. Web-based framework for viewing models and scenes.
1. Various web-based annotation interfaces for annotating models and scenes
1. Batch processing component for doing analysis on scenes and offscreen rendering (see [ssc/README.md](ssc/README.md))
1. Server-side rendering

# Getting Started

0. The SSTK can be used on Linux, MacOS and Windows systems.

1. Install [node.js](https://nodejs.org/).  Using the Node Version Manager ([nvm](https://github.com/creationix/nvm)) is the easiest way to install node.js on most systems.
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
source ~/.bashrc
nvm install v10.13.0
```
If you use zsh instead of bash, replace all instances of bash with zsh.
Confirm above works using `node -v` at the terminal.

2. Build and run server
```
  cd stk
  ./build.sh
  cd server
  ./run.sh
```
There are some prerequisites for the build process in Ubuntu, they can be installed using `sudo apt-get install build-essential libxi-dev libglu1-mesa-dev libglew-dev libvips`.

3. Visit http://localhost:8010 in your browser (Chrome is recommended)!

## MySQL database setup guide
1. Install MySQL database on your PC (example OS: Ubuntu 16.04)
```
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation # Configure db security
```
2. Create database and tables
```
sudo mysql -p
mysql> CREATE DATABASE scannet;
mysql> USE scannet;
mysql> SOURCE /PATH_TO_SSTK/scripts/db/create_annotation_cleaned.sql
mysql> SOURCE /PATH_TO_SSTK/scripts/db/create_annotation_tables.sql
mysql> SOURCE /PATH_TO_SSTK/scripts/db/create_part_annotation_tables.sql
```

## NYUv2 Example setup guide
1. Execute the getexamples script:
```
# Under sstk/ folder
./getexamples.sh
```
2. Edit server config file:
```
vim sstk/server/config/index.js
vim> config.annDb = { host: 'localhost', user: 'root', password: 'PASSWORD', database: 'scannet' };
```
Change the user name and password to yours.

3. Run server:
```
cd SSTK/server/
./run.sh
```
4. Go to link: http://localhost:8010/scans/nyuv2

## Assets
To use the STK, you will need to get yourself some assets.  There are several open-source datasets that
you can use with the STK.  Many of these datasets require agreeing to a license and terms of use, 
so please go to their respective websites to download them.

1. 3D Models
  - [ShapeNet](www.shapenet.org) is a large dataset of 3D models.    
2. Synthetic Scenes
  - [Stanford Scene Database](http://graphics.stanford.edu/projects/scenesynth/) consist of 150 synthetic scenes.
  - [SUNCG](suncg.cs.princeton.edu) is a large indoor dataset with over 45K houses.
3. Reconstructions
  - [ScanNet](http://www.scan-net.org/) 
  - [Matterport3D](https://github.com/niessner/Matterport)
  - [SceneNN](http://people.sutd.edu.sg/~saikit/projects/sceneNN/)
  - [2D-3D-S](http://buildingparser.stanford.edu/dataset.html)

The STK has been developed to be able to easily view and annotate 3D assets.  
Specifically, parts of ShapeNet, SUNCG, ScanNet, and Matterport3D were all developed using the STK.

## Entry Points
- `model-viewer` : Model viewing interface
- `model-categorizer.html` : Model categorization interface
- `scene-viewer.html` : Scene Viewer

## Development To-Do
- Add Draco Compression support
  - [ ] Write a script that supports converting a batch of `.ply` models to `.drc` format.
  - [ ] Add Draco decording support on server side and feed the 3D data into annotation pipeline.

Advanced Build Instructions
==================

You will need to build the client side assets that the server will serve before connecting to any entry point with your browser. To do this run the following in the root repository directory:
* `cd client`
* Type `npm install` to get base dependencies
* Run `npm run build` to package the stk source files
  * Use `NODE_ENV=dev npm run build` to build source maps and have webpack watch for changes.
  * Use `NODE_ENV=prod npm run build` to optimize (including minify) the JS assets.

For convenience a `build.sh` script is provided that will run the two steps above.
You will need to repeat the build step every time the client source files are changed or use `NODE_ENV=dev npm run build` to watch and rebuild as you develop!

Running the server
==================
Once you have built the client source files as described above, you need to start the server (see [server/README.md](server/README.md) for details, including how to deploy a new instance).
Do the following from the root repository directory:
```
cd server
npm install
npm start
```  
Again, a `server/run.sh` script is provided that will run the above steps together.

Development Workflow
======================
For routine local development, here are the usual steps:
* Start webpack build process in watch mode by calling `NODE_ENV=dev npm run build` in repository root.  If new dependencies have been introduced, you may get an unresolved module error and will need to run `npm install` first.  You need to build the client code everytime there is a code change -- watch mode is a convenience for avoiding manual `npm run build` calls all the time.
* In a separate terminal, start the server process by calling `./run.sh` inside the `server/` directory.
* Go to a browser window and pull up `localhost:8010/index.html` or any other entry point (such as `scene-viewer.html`).
* If you just need a one-time build of the toolkit, copy out the `client/build/STK.bundle.js` (after `npm run build`) to the `vendor/assets/javascripts` directory of SceneStudio.

Versioning conventions:
- The `master` branch contains latest mainstream (with potential bug fixes over latest release)
- The `dev` branch contains large (potentially breaking) changes
- The `v0.5.x` branch (and similar future versioned branches) contain the latest release of that form
Versioning workflow: develop on `master` for small bug fixes or on `dev` for large changes. When ready to release branch, make sure to update appropriate `v0.5.x` or similar latest release branch, and also tag with exact version number (e.g., `v0.5.3`).

Building the documentation
==========================
- `cd client` from the repository root
- run `npm run jsdoc`
- Open the generated `jsdoc/index.html` page with a browser

Changelog
=========

Refer to [CHANGELOG.md](CHANGELOG.md) for a record of notable changes.

