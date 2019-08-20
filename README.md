## Use
Best use is to make changes in the version of the master branch and then compile it by running `npm run build` in scratch-gui folder. 
After building, the website can be run from the build subfolder in the scratch-gui folder using the index file.

## Setup 
I run this on Windows 10, in Git Bash

Follow these steps (for more info see: [Scratch modification guide](https://scratch.mit.edu/discuss/topic/289503/?page=1))

1. Clone this repo to you local machine. 

`git clone https://github.com/Sannevw/RobotLearningScratch.git`

`cd scratch-gui`

`npm install`

`cd ..`

`cd scratch-vm`

`npm install`

`npm link`

`cd ..`

`cd scratch-gui`

`npm link scratch-vm`

`cd ..`

`cd scratch-blocks`

`npm run prepublish`

`cd ..`

`cd scratch-gui`

`npm run build`

`npm start`

Runing `npm start` from the scratch-gui folder will host the website locally to test.

Follow these instructions here to install from the beginning if anything is wrong: [Scratch modification guide](https://scratch.mit.edu/discuss/topic/289503/?page=1)


## Set the default project

[UPDATE]
Better way to do it, is to change the hardcoded default project id in this file:
scratch-gui\src\reducers\project-state.js

line 28 
```javascript
const defaultProjectId = 'YOUR_PROJECT_ID'; // hardcoded id of default project
```

One way to do this is to change in `scratch-gui\src\lib\project-fetcher-hoc.jsx` line 75-91. Initialize projectId specifically to your project's id, which can be found in the Scratch URL https://scratch.mit.edu/projects/ID_OF_YOUR_PROJECT/editor/

```javascript
fetchProject (projectId, loadingState) {
            projectId = 'INSERT_YOUR_PROJECTS_ID';
            return storage
                .load(storage.AssetType.Project, projectId, storage.DataFormat.JSON)
                .then(projectAsset => {
                    if (projectAsset) {
                        this.props.onFetchedProjectData(projectAsset.data, loadingState);
                    } else {
                        // Treat failure to load as an error
                        // Throw to be caught by catch later on
                        throw new Error('Could not find project');
                    }
                })
                .catch(err => {
                    this.props.onError(err);
                    log.error(err);
                });
        }
```

## Make sprites unclickable
scratch-gui/src/containers/sprite-selector-item.jsx   
comment out this.props.onClick(this.props.id);

```javascript
  handleClick (e) {
        e.preventDefault();
        if (!this.noClick) {
            //this.props.onClick(this.props.id);
        }
    }
  ```
       
## in the canvas
scratch-gui/src/containers/stage.jsx
Comment out this.props.vm.setEditingTarget(targetId);

```javascript
    handleDoubleClick (e) {
        const {x, y} = getEventXY(e);
        // Set editing target from cursor position, if clicking on a sprite.
        const mousePosition = [x - this.rect.left, y - this.rect.top];
        const drawableId = this.renderer.pick(mousePosition[0], mousePosition[1]);
        if (drawableId === null) return;
        const targetId = this.props.vm.getTargetIdForDrawableId(drawableId);
        if (targetId === null) return;
        //this.props.vm.setEditingTarget(targetId);
    }
```

## make the sprites undraggable

scratch-gui/src/containers/stage.jsx
change in 
```javascript
onStartDrag (x, y) {  }
       if (!(this.props.useEditorDragStyle || target.draggable)) return;
       ```
to
```javascript
       if (!(this.props.useEditorDragStyle && target.draggable)) return;
 ```
 
 And in the json of the project, set draggable=False

## Change the name of the project from "Scratch Project" to "Robot Programming" in the interface
in .\scratch-gui\src\containers\gui.jsx

```javascript
const messages = defineMessages({
    defaultProjectTitle: {
        id: 'Robot Programming',
        description: 'Default title for project',
        defaultMessage: 'Robot Programming'
    }
});
```
