

## I. Event Architecture

### 1.1 Event Propagation Model

HarmonyOS adopts a three-phase event propagation mechanism (Capture-Target-Bubble), optimized for device characteristics while maintaining Web standards compatibility:

```
graph LR  
A[Device Input] --> B{ArkUI Event Dispatcher}  
B --> C[Target Component]  
C --> D{Bubble Phase}  
D --> E[Parent Component]  
```

* ​Mouse Events: Left-click, movement, hover (Right-click handled separately)

* ​Keyboard Events: Compound key recognition and preprocessing

### 1.2 Core Event Types

| Event Category    | Typical Callback Method               | Data Object |
| :---------------- | :------------------------------------ | :---------- |
| Mouse Hover       | onHover((isHover) => void)            | HoverEvent  |
| Mouse Interaction | onMouse((event: MouseEvent) => void)  | MouseEvent  |
| Keyboard Input    | onKeyEvent((event: KeyEvent) => void) | KeyEvent    |
| Focus Changes     | onFocus/Blur                          | FocusEvent  |

## II. Complex Scenario Solutions

### 2.1 Precision Event Targeting

​Problem Scenario: Nested List Button requiring independent mouse event handling

```
List({ space: 20 }) {  
  ForEach(items, (item, index) => {  
    ListItem() {  
      Button("Action")  
        .onMouse((e) => {  
          if(e.button === MouseButton.Left) {  
            this.handleItemClick(index); // Left-click handling  
          }  
        })  
        .onHover((isHover) => {  
          this.updateHoverState(index, isHover); // Independent hover state  
        })  
    }  
  })  
}  
```

### 2.2 Z-Shaped Focus Navigation

​Implementation: Cross-hierarchy focus transition logic

```
Column() {  
  @State activeTab = 0  
  Tabs() {  
    TabContent() {  
      List() {  
        ForEach(data, (_, i) => {  
          ListItem()  
            .focusable(true)  
            .tabIndex(i === 0 ? 1 : -1)  
            .onKeyEvent((e) => {  
              if(e.type === KeyType.Down && e.keyCode === KeyCode.Tab) {  
                this.activeTab = (this.activeTab + 1) % 3;  
                nextTabItem.requestFocus();  
                return true;  
              }  
            })  
        })  
      }  
    }  
  }  
}  
```

### 2.3 Compound Key Handling

​Shortcut Implementation:

```
// Global shortcut registration  
@Entry  
@Component  
struct ShortcutDemo {  
  build() {  
    WindowEventBus.on(KeyEvent, (event) => {  
      if(event.isCtrlDown() && event.keyCode === KeyCode.S) {  
        this.handleSave();  
      }  
    })  
  }  
}  

// Component-level handling  
Button("Save")  
  .onKeyEvent((e) => {  
    if(e.isCtrlDown() && e.keyCode === KeyCode.Enter) {  
      e.stopPropagation();  
      this.saveData();  
    }  
  })  
```

## III. Event Styling Control

### 3.1 Hover Visual Feedback

```
Button("Hover Effect")  
  .hoverEffect(HoverEffect.Default)  
  .onHover((isHover) => {  
    this.hoverStyle = isHover ? {  
      backgroundColor: Color.LightBlue,  
      shadow: new BoxShadow({  
        color: Color.Gray,  
        radius: 4,  
        offsetX: 2,  
        offsetY: 2  
      })  
    } : {};  
  })  
```

### 3.2 Focus State Indication

Dynamic focus border:

```
TextInput()  
  .focusable(true)  
  .onFocus(() => this.showFocusRing = true)  
  .onBlur(() => this.showFocusRing = false)  
  .focusStyle({  
    border: {  
      color: this.showFocusRing ? Color.Blue : Color.Transparent,  
      width: 2  
    },  
    shadow: this.showFocusRing ? {  
      color: Color.BlueAlpha(0.3),  
      radius: 4,  
      offsetX: 2,  
      offsetY: 2  
    } : undefined  
  })  
```

## IV. Performance Optimization

### 4.1 Event Throttling

```
// Scroll event debouncing  
let scrollTimeout: number;  
Scroll() {  
  .onScroll((e) => {  
    clearTimeout(scrollTimeout);  
    scrollTimeout = setTimeout(() => {  
      this.processScrollData(e);  
    }, 100);  
  })  
}  
```

### 4.2 Virtual Event Handling

Large list optimization:

```
List({ space: 20 }) {  
  Scroll() {  
    VirtualList({  
      count: 1000,  
      itemHeight: 80  
    }) {  
      ForEach(visibleData, (item) => {  
        ListItem()  
          .onMouse((e) => this.handleVirtualItemHover(e))  
      })  
    }  
  }  
}  
```

## V. Debugging

### 5.1 Event Tracking

```
// Enable event logging  
setGlobalEventDebug(true, {  
  filter: ['mouse', 'key'],  
  logLevel: DebugLevel.Verbose  
});  

// Sample output:  
[Event] 2025-06-23 14:30:00.123:  
  Type: MouseEvent  
  Target: Button#0x7f8d9c0  
  Position: (120, 240)  
  Buttons: Left  
  Action: Press  
```

### 5.2 Common Issues

| Symptom              | Likely Cause            | Solution                    |
| :------------------- | :---------------------- | :-------------------------- |
| Event not triggering | Missing focusable prop  | Add .focusable(true)        |
| Focus mismanagement  | Improper event bubbling | Use event.stopPropagation() |
| Style not applying   | Style scope conflict    | Use @style qualifier        |
| Shortcut not working | Global listener missing | Register via WindowEventBus |

## VI. Implementation Scenarios

### Scenario 1: Data Table Interaction

```
Table() {  
  ForEach(headers, (header) => {  
    TableHeader(header)  
  })  
  ForEach(rows, (row) => {  
    TableRow() {  
      ForEach(row.cells, (cell) => {  
        TableCell()  
          .onMouse((e) => {  
            if(e.action === MouseAction.DoubleClick) {  
              this.editCell(cell);  
            }  
          })  
      })  
    }  
  })  
}  
```

### Scenario 2: Cross-Device Control

​Mobile sender:

```
@Entry  
@Component  
struct MobileController {  
  @Link remoteComponent: RemoteControlComponent  
  
  build() {  
    Column() {  
      Button("Fullscreen")  
        .onClick(() => {  
          this.remoteComponent.postEvent(  
            new KeyEvent({  
              type: KeyType.Down,  
              keyCode: KeyCode.F11  
            })  
          );  
        })  
    }  
  }  
}  
```

​Desktop receiver:

```
@Component  
struct DesktopReceiver {  
  onKeyEvent(event: KeyEvent) {  
    if(event.type === KeyType.Down && event.keyCode === KeyCode.F11) {  
      this.toggleFullScreen();  
    }  
  }  
}  
```

## VII. Development Techniques

### 7.1 Custom Gesture Recognition

```
GestureContainer() {  
  @State startPos: Point = { x: 0, y: 0 }  
  
  onMove((e) => {  
    if(e.touches.length === 3) {  
      const deltaX = e.touches[0].x - this.startPos.x;  
      const deltaY = e.touches[0].y - this.startPos.y;  
      this.handleThreeFingerSwipe(deltaX, deltaY);  
    }  
  })  
}  
```

### 7.2 Hardware Acceleration

```
// Force GPU rendering  
Component.setRenderMode(RenderMode.Hardware);  
  
// Canvas optimization  
Canvas()  
  .enableHardwareAcceleration(true)  
  .render(() => {  
    drawComplexPath();  
  });  
```

## VIII. Best Practices

1. ​Event Delegation: Delegate high-frequency events to parent components

2. ​State Isolation: Use @Link for bidirectional parent-child state sync

3. ​Device Adaptation:

   ```
   if(DeviceType.mobile) {  
     this.disableHoverEffects();  
   }  
   ```

4. ​Accessibility:

   ```
   Button("Submit")  
     .accessibilityLabel("Confirm order submission")  
     .accessibilityHint("Double-tap to complete")  
   ```

