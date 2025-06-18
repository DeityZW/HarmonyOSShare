

## I. Core Architecture Principles

ArkTS implements a layered state management system through decorators, featuring:

1. ​Reactive Updates​
   Automatic UI re-rendering on state changes

2. ​Unidirectional Data Flow​
   Strict parent→child data propagation

3. ​Cross-level Communication​
   Multi-tier component state synchronization

## II. Core Decorators Explained

### 1. @State (Component Internal State)

​Features:​​

* Local component state management

* Automatic UI synchronization

* Supports primitives and object arrays

​Code Example:​​

```
@Entry
@Component
struct Counter {
  @State count: number = 0

  build() {
    Column() {
      Text(this.count.toString())
      Button("Increment").onClick(() => this.count += 1)
    }
  }
}
```

​Debug Command:​​
hdc logcat --tag=ArkTS_State

### 2. @Prop (Parent→Child Propagation)

​Features:​​

* Immutable data flow

* Strict type checking

* Parent-driven updates

​Code Example:​​

```
@Component
struct Child {
  @Prop readonly title: string
  build() { Text(this.title) }
}

@Entry
@Component
struct Parent {
  private msg = "Parent Message"
  build() {
    Child({ title: this.msg })
  }
}
```

### 3. @Link (Bidirectional Binding)

​Features:​​

* Two-way synchronization

* Requires $ prefix

* Limited to primitive types

​Code Example:​​

```
@Component
struct InputComponent {
  @Link text: string
  build() {
    TextInput({ value: $text })
  }
}

@Entry
@Component
struct App {
  @State input = ""
  build() {
    Column() {
      InputComponent({ text: this.input })
      Text(this.input)
    }
  }
}
```

### 4. @Provide / @Consume (Cross-level Sharing)

​Features:​​

* Global state management

* Complex object support

* Hierarchy traversal

​Code Example:​​

```
// State Provider
@Entry
@Component
struct AppState {
  @Provide user = { name: "Zhang San", age: 18 }
}

// State Consumer
@Component
struct Profile {
  @Consume user!: { name: string; age: number }
  build() {
    Text(this.user.name)
  }
}
```

## III. Decorator Comparison Matrix

| Decorator | Scope            | Data Flow      | Mutability | Use Case              |
| :-------- | :--------------- | :------------- | :--------- | :-------------------- |
| @State    | Component        | Self-managed   | Mutable    | Local UI state        |
| @Prop     | Parent→Child     | Unidirectional | Immutable  | Simple data passing   |
| @Link     | Parent↔Child     | Bidirectional  | Mutable    | Form inputs           |
| @Provide  | Global/Hierarchy | Multi-level    | Mutable    | Themes, user settings |

## IV. Practical Implementation

### 1. Form State Management

```
@Entry
@Component
struct LoginForm {
  @State formState = {
    username: "",
    password: "",
    remember: false
  }

  validate() {
    return this.formState.username.length > 3
  }

  build() {
    Column() {
      TextInput({ value: $formState.username })
      Checkbox(this.formState.remember)
      Button("Submit").onClick(() => {
        if(this.validate()) {
          // Submission logic
        }
      })
    }
  }
}
```

### 2. Global Theme Switching

```
// Theme Service
@Entry
@Component
struct ThemeProvider {
  @Provide currentTheme = "dark"
  toggleTheme() {
    this.currentTheme = this.currentTheme === "dark" ? "light" : "dark"
  }
}

// Consumer Component
@Component
struct AppHeader {
  @Consume currentTheme!: string
  build() {
    Flex({ direction: FlexDirection.Row }) {
      Text("Theme: " + this.currentTheme)
      Button("Toggle").onClick(() => {
        // Theme toggle logic
      })
    }
  }
}
```

## V. Performance Optimization

### 1. State Segmentation

```
@State userInfo = { ... }
@State settings = { ... }
```

### 2. Batch Updates

```
$state = {
  ...this.state,
  field1: newValue,
  field2: newValue2
}
```

### 3. Memory Management

```
@Dispose
onDispose() {
  // Cleanup timers/subscriptions
}
```

## VI. Debugging Techniques

### 1. State Snapshot

```
deveco-cli state-snapshot
```

### 2. Change Tracking

```
@State count: number = 0
  .onChange((newVal) => {
    console.log(`count changed to ${newVal}`)
  })
```

​Best Practices:​​

* Use granular state management

* Prefer @Provide/@Consume for cross-module

* Implement proper state immutability

* Utilize batch updates for complex objects

This comprehensive guide provides essential patterns for effective state management in ArkTS applications. By combining appropriate decorators with performance optimization strategies, developers can build robust and maintainable HarmonyOS applications.
