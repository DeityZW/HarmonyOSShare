
## I. Architectural Evolution of State Management

HarmonyOS has established a complete decorator system for ArkTS state management, characterized by:

​V1 to V2 Paradigm Shift​

* Transition from single @State management to hierarchical state architecture

​Bidirectional Binding Revolution​

* @Link enables two-way data flow between components

​Deep Observation Breakthrough​

* @ObservedV2 + @Trace enable nested object tracking

## II. Core Decorator Matrix

### 1. Fundamental State Management Triad

​Parent Component​

```
@Component
struct Parent {
  @State count: number = 0  // Component internal state
  @Prop readonly title: string  // Unidirectional data flow

  build() {
    Column() {
      Child({ title: this.title })  // Parent→Child propagation
      Counter({ count: this.count })  // State elevation
    }
  }
}
```

​Child Component​

```
@Component
struct Child {
  @Prop title: string  // Immutable data flow
  build() { Text(this.title) }
}
```

​Bidirectional Component​

```
@Component
struct Counter {
  @Link count: number  // Two-way binding
  build() {
    TextInput({ value: $count })
    Text(this.count)
  }
}
```

### 2. Cross-level Communication

​State Service​

```
@Entry
@ComponentV2
struct Store {
  @Provide()  // Global provision
  user = { name: "Zhang San", settings: { theme: "dark" } }
}
```

​Consumer Component​

```
@Component
struct Profile {
  @Consume()  // Automatic injection
  user!: { name: string; settings: { theme: string } }
  build() {
    Text(this.user.settings.theme)
  }
}
```

### 3. Complex Object Observation

```
@ObservedV2  // Deep observation marker
class Address {
  @Trace  // Property tracing
  street: string = "Beijing Haidian"
  
  @Trace
  city: string = "Beijing"
}

@ComponentV2
struct Location {
  @Local address = new Address()  // Component internal state
  build() {
    TextInput({ value: $address.city })  // Observable modification
  }
}
```

​**​*

## III. Six Practical Patterns

### 1. Form State Management (Flux Pattern)

​State Container​

```
@Entry
@ComponentV2
struct FormStore {
  @State formData = {
    username: "",
    password: "",
    remember: false
  }
  
  @Monitor('formData')  // Deep listener
  onFormChange(monitor: IMonitor) {
    console.log("Form changes:", monitor)
  }
}
```

​Form Component​

```
@Component
struct LoginForm {
  @Link formData: FormStore['formData']
  build() {
    Column() {
      TextInput({ value: $formData.username })
      Checkbox(this.formData.remember)
    }
  }
}
```

### 2. Theme System (Observer Pattern)

​Theme Service​

```
@Entry
@ComponentV2
struct ThemeProvider {
  @Provide currentTheme = "dark"
  toggle() {
    this.currentTheme = this.currentTheme === "dark" ? "light" : "dark"
  }
}
```

​Consumer Component​

```
@Component
struct AppHeader {
  @Consume currentTheme!: string
  build() {
    Flex({ direction: FlexDirection.Row }) {
      Text("Current Theme: " + this.currentTheme)
      Button("Toggle").onClick(() => {})
    }
  }
}
```

### 3. Shopping Cart System (CQRS Pattern)

​State Management​

```
@Entry
@ComponentV2
struct CartStore {
  @State items: Product[] = []
  @Computed get totalPrice() {
    return this.items.reduce((sum, item) => sum + item.price, 0)
  }
  
  @Action
  add(item: Product) {
    this.items = [...this.items, item]
  }
}
```

​Product Component​

```
@Component
struct ProductCard {
  @Link product: Product
  build() {
    Button("Add to Cart").onClick(() => {
      cartStore.add(this.product)
    })
  }
}
```

​**​*

## IV. Advanced Features

### 1. State Promotion Pattern

​Child Component​

```
@Component
struct ChildPicker {
  @Prop @Link selected: string  // Bidirectional promotion
  build() {
    Picker({ value: $selected })
  }
}
```

​Parent Component​

```
@Component
struct Parent {
  @State selectedDate: string = "2025-01-01"
  build() {
    ChildPicker({ selected: this.selectedDate })
  }
}
```

### 2. Lazy-loaded State Management

```
const createLazyStore = () => {
  @ComponentV2
  struct LazyStore {
    @State data: any = null
    async load() {
      this.data = await fetchData()
    }
  }
  return lazyStore
}

const lazyStore = createLazyStore()
lazyStore.load()
```

### 3. State Snapshot Pattern

```
@Entry
@ComponentV2
struct AppState {
  @State settings = {
    theme: "system",
    language: "zh-CN"
  }
  aboutToAppear() {
    const snapshot = JSON.stringify(this.settings)
    localStorage.setItem("appState", snapshot)
  }
}
```

​**​*

## V. Performance Optimization

### 1. Rendering Optimization

```
@Component
struct ExpensiveComponent {
  @State data: number[] = []
  @Memo
  get filteredData() {
    return this.data.filter(/* Complex calculation */)
  }
}
```

### 2. Memory Management

```
@Dispose
onDispose() {
  this.ws?.close()
  this.timer?.clear()
}
```

### 3. Batch Updates

```
const batchUpdate = () => {
  this.$batch(() => {
    this.count++
    this.items.push(newItem)
  })
}
```

​**​*

## VI. Debugging & Monitoring

### 1. State Tracking

```
# Start state monitoring
deveco-cli state-track --component=Counter
```

### 2. Change Tracking

```
@State count: number = 0
  .onChange((newVal) => {
    console.log(`[${new Date()}] count changed to ${newVal}`)
  })
```

## Implementation Recommendations

1. ​Single Responsibility Principle​
   Each state module manages independent business domains

2. ​State Granularity Control​
   Avoid excessive centralization (e.g., split forms into sub-states)

3. ​Type Safety Standards​

```
interface UserState {
  name: string
  age: number
  preferences: {
    theme: 'dark' | 'light'
    notifications: boolean
  }
}
```

4. ​Migration Strategy​

   * Gradually replace legacy @State components with @Local + @ObservedV2

   * Implement phased migration for complex components

This comprehensive guide provides essential patterns for effective state management in ArkTS applications. Developers should focus on decorator composition while leveraging HarmonyOS' unique distributed capabilities for enhanced user experiences.
