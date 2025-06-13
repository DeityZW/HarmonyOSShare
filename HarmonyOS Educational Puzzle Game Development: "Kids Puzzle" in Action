
> Note: This is a technical implementation guide. For comprehensive learning, please refer to the official documentation.

***

## I. Project Overview

We developed an animal-themed puzzle game for children aged 3-6, featuring:

* 3x3 puzzle layout (with adjustable 2x2 easy mode)

* Wildlife illustrations (panda/rabbit/giraffe)

* Drag-and-drop interaction with sound effects

* Timed challenges and achievement system

* Adaptive screen resolution

## II. Development Environment Setup

```
# Create new project (DevEco Studio)  
File → New → New Project → Empty Ability → ArkTS  
```

## III. Core Implementation

### 1. UI Layout (main_page.ets)

```
@Entry  
@Component  
struct PuzzleGame {  
  private puzzlePieces: Array<Image> = []  
  private emptyPos = {x:2, y:2} // Initial empty position  
  private timer: number = 0  
  private steps = 0  

  build() {  
    Column.create()  
      .child(Stack({ alignContent: Alignment.End })  
        .child(TimerDisplay(this.timer))  
        .child(StepCounter(this.steps))  
      )  
      .child(PuzzleGrid(this.puzzlePieces, this.emptyPos))  
      .child(Button("Restart").onClick(() => this.resetGame()))  
      .width('100%')  
      .height('100%')  
  }  
}  
```

### 2. Puzzle Component (puzzle_grid.ts)

```
@Component  
struct PuzzleGrid {  
  private pieces: Array<Image>  
  private emptyPos: {x:number, y:number}  

  build() {  
    Grid({ columnsTemplate: "repeat(3, 1fr)", rowsTemplate: "repeat(3, 1fr)" }) {  
      ForEach(this.pieces, (piece, index) => {  
        Image(piece)  
          .width(100)  
          .height(100)  
          .draggable(true)  
          .onDragEnd((e) => this.handleDrop(e, index))  
      }, (item) => item.key)  
    }  
    .onClick(() => this.shufflePieces())  
  }  

  private handleDrop(event: DragEvent, index: number) {  
    let targetPos = {x: Math.floor(index/3), y: index%3}  
    if(this.isAdjacent(targetPos, this.emptyPos)) {  
      this.swapPieces(index, this.emptyPos)  
      this.emptyPos = targetPos  
      this.steps++  
    }  
  }  
}  
```

### 3. Animation Implementation (animation.ts)

```
// Puzzle piece movement animation  
function slideAnimation(component: Image, startPos: {x:number,y:number}, endPos: {x:number,y:number}) {  
  let dx = endPos.x - startPos.x  
  let dy = endPos.y - startPos.y  
  component.animateTo(  
    { transform: `translate(${dx*100}px, ${dy*100}px)` },  
    { duration: 300, curve: Curve.EaseOut }  
  )  
}  

// Timer implementation  
class GameTimer {  
  private timerId: number = 0  
  private isRunning = false  

  start(callback: (time:number) => void) {  
    this.timerId = setInterval(() => {  
      this.isRunning && callback(++this.time)  
    }, 1000)  
  }  

  stop() {  
    clearInterval(this.timerId)  
  }  
}  
```

## IV. Technical Insights

### 1. Drag Interaction Implementation

Enable drag functionality with draggable property and handle placement logic:

```
private isAdjacent(posA: {x,y}, posB: {x,y}): boolean {  
  return Math.abs(posA.x-posB.x) + Math.abs(posA.y-posB.y) === 1  
}  
```

### 2. Asset Management

Recommended vector graphics for crisp visuals:

```
# Resource directory structure  
resources/  
├── images/  
│   ├── puzzle_background.png  
│   ├── animals/  
│   │   ├── panda.svg  
│   │   ├── rabbit.svg  
│   │   └── giraffe.svg  
```

### 3. Responsive Layout

Flex layout implementation for multi-device support:

```
Column.create()  
  .width('100%')  
  .height('100%')  
  .child(Grid({   
    columnsTemplate: "repeat(3, 1fr)",  
    rowsTemplate: "repeat(3, 1fr)",  
    alignContent: Alignment.Stretch  
  }))  
```

> ​Development Tips: Preload images on launch, use @Link for component communication, and implement @State for two-way data binding. Test touch responsiveness across devices.

This case study demonstrates core HarmonyOS development workflows including UI construction, interaction logic implementation, and animation integration.
