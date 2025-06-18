

## I. Core Styling Property System

### 1. Dimension Control

```
// Three unit systems comparison
Text('Title')
  .width(120)          // Absolute unit (vp)
  .height('50%')       // Relative unit (parent percentage)
  .minHeight('100vp')  // Minimum height

// Responsive best practices
Column({ space: 20 }) {
  Text()
    .width('100%')    // Full width
    .height(44)       // Fixed height
    .padding({ left: 16, right: 16 }) // Padding
}
```

​Development Recommendation:​​

* Prefer vp units (1vp=1px@360dpi)

* Combine percentage + adaptive layouts for complex structures

### 2. Color System Deep Dive

```
// Triple background definition
View()
  .backgroundColor('#FF0000')        // Hex
  .backgroundColor(rgb(0, 125, 255)) // RGB
  .backgroundColor(Color.Primary)    // System theme color

// Gradient background
Image()
  .backgroundImage($r('app.media.gradient'))
  .backgroundImageSize(ImageSize.Cover) // Cover mode
```

### 3. Border & Corner Control

```
Button('Submit')
  .border({ 
    width: 2, 
    color: '#FFFFFF',
    style: BorderStyle.Dashed  // Dashed border
  })
  .borderRadius(22)  // Corner radius
```

## II. Layout Control Essentials

### 1. Linear Layout Parameters

```
Column({ 
  justifyContent: FlexAlign.Center,  // Main axis alignment
  alignItems: ItemAlign.Stretch      // Cross axis alignment
}) {
  Text()
    .fontSize(18)
    .fontWeight(FontWeight.Bold)
    .textAlign(TextAlign.Center)  // Text centering
}
```

### 2. Flexbox Advanced

```
Flex({ direction: FlexDirection.Row }) {
  Text()
    .layoutWeight(1)  // Flex weight
    .fontSize(16)
  
  Image()
    .aspectRatio(1)   // Aspect ratio lock
    .objectFit(ImageFit.Cover)  // Cover mode
}
```

## III. Interaction State Management

### 1. State Property Matrix

| Property   | Usage Scenario | Typical Values      |
| :--------- | :------------- | :------------------ |
| disabled   | Interaction    | true/false          |
| focusable  | Focus control  | true/false          |
| opacity    | Transparency   | 0~1                 |
| visibility | Display states | Visible/Hidden/None |

### 2. Focus State Handling

```
@Entry
@Component
struct InputDemo {
  @State isFocused = false

  build() {
    TextInput()
      .isFocused(this.isFocused)
      .onFocus(() => this.isFocused = true)
      .onBlur(() => this.isFocused = false)
      .borderColor(this.isFocused ? Color.Blue : Color.Gray)
  }
}
```

## IV. Style Reuse Best Practices

### 1. @Styles Decorator

```
@Styles commonStyle() {
  .width(200)
  .height(44)
  .backgroundColor(Color.Primary)
  .borderRadius(8)
}

@Component
struct ButtonDemo {
  build() {
    Button('Submit')
      .commonStyle()  // Style reuse
      .onClick(() => {})
  }
}
```

### 2. Dynamic Styling

```
@Entry
@Component
struct DynamicDemo {
  @State theme = 'dark'

  build() {
    View()
      .backgroundColor(theme === 'dark' ? Color.Black : Color.White)
      .fontColor(theme === 'dark' ? Color.White : Color.Black)
  }
}
```

## V. Performance Optimization

1. ​Overdraw Prevention​

   ```
   // ❌ Bad practice: Multiple background layers
   View()
     .backgroundColor(Color.Red)
     .backgroundImage($r('app.media.bg'))

   // ✅ Good practice: Single background layer
   View()
     .backgroundImage($r('app.media.bg'))
     .backgroundImagePosition(Alignment.Center)
   ```

2. ​Smart Preloading​

   ```
   // List preloading configuration
   List() {
     LazyForEach(data, (item) => {
       ListItem()
     }, (item) => item.id)
     .cachedCount(5)  // Preload ±5 items
   }
   ```

## VI. Debugging Techniques

1. ​Live Style Inspection​

   ```
   # Launch layout inspector
   devtools inspect --layout
   ```

2. ​Style Snapshot​

   ```
   // Add debug marker in component
   @Component
   struct DebugDemo {
     build() {
       View()
         .debugStyle('critical-button')  // Debug tagging
     }
   }
   ```

​Learning Roadmap:​​

1. Master basic size/color/margin properties

2. Proficiency in Flex layout system

3. Master state-property interactivity

4. Advanced style reuse techniques

5. Performance optimization specialization

