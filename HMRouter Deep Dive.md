> This is currently used in my project and works great. Highly recommended!

## I. Implementation Principles & Architecture

HMRouter leverages HarmonyOS's componentized navigation architecture with a three-tier encapsulation for efficient routing management:

```
Application Layer (@HMRouter Annotations) ↔ Routing Management Layer (RouteTable) ↔ System Layer (Navigation Containers)  
```

### 1. Annotation-Driven Routing Table

* Automatically generates routing mappings via compile-time annotation processors

* Enables type-safe navigation with metadata collection (page paths, parameter validation rules, lifecycle callbacks)

### 2. Dynamic Route Stack Management

* Built on NavPathStack for intelligent routing maintenance

* Supports:

  * Nested routing (sub-navigation within containers)

  * Cross-module navigation (requires module dependency configuration)

  * Route state persistence (stack restoration after app restart)

### 3. Enhanced Lifecycle

* Extended lifecycle states:

  ```
  export enum PageLifecycle {  
    onCreate = 'onCreate',  
    onShow = 'onShow',  
    onHide = 'onHide',  
    onDestroy = 'onDestroy'  
  }  
  ```

## II. Core Usage

### 2.1 Environment Configuration

```
// hvigor-config.json  
{  
  "dependencies": {  
    "@hadss/hmrouter": "^1.0.0-rc.6",  
    "@hadss/hmrouter-transitions": "^1.0.0-rc.6"  
  }  
}  
```

Requires compilation plugin configuration in each module's hvigorfile.ts

### 2.2 Route Declaration

```
// Page route definition  
@HMRouter({  
  pageUrl: "LoginActivity",  
  interceptors: [AuthInterceptor] // Global interceptors  
})  
@Component  
export struct LoginPage {  
  // Parameter validation  
  @Validate({  
    name: { type: 'string', required: true },  
    age: { type: 'number', min: 18 }  
  })  
  static routeParams: Record<string, Validator> = {}  
}  
```

### 2.3 Navigation Implementation

```
// Basic navigation  
HMRouter.push({  
  pageUrl: "DetailPage",  
  params: { id: 123 },  
  onResult: (res) => {  
    console.log('Returned data:', res)  
  }  
})  
  
// Interceptor-enabled navigation  
HMRouter.withInterceptor(AuthInterceptor).push("ProfilePage")  
```

## III. Technical Advantages

### 3.1 Modular Development Support

* Compile-time checks for unregistered routes

* Dependency isolation via module.json declarations

* Dynamic loading with @LazyRoute

### 3.2 Advanced Features

| Feature               | Implementation                                                             |
| :-------------------- | :------------------------------------------------------------------------- |
| Transition Animations | JSON-configurable scaling/fade/ shared-element animations                  |
| Service Routes        | @ServiceRoute for cross-component access                                   |
| Permission Checks     | Annotation-based authorization (e.g., @RequirePermission("READ_CONTACTS")) |

### 3.3 Performance Metrics

* Cold startup routing resolution latency: <20ms (vs. 50ms for native Navigation)

* Unlimited theoretical stack depth (memory-dependent)

* 40% regex validation optimization

## IV. Comparison with Alternatives

### 4.1 vs. Native Navigation

| Feature                 | HMRouter               | Native Navigation |
| :---------------------- | :--------------------- | :---------------- |
| Route Interception      | ✔️ Global/local        | ❌ None            |
| Transition Animations   | ✔️ Custom JSON         | ✔️ Basic only     |
| Compile-time Validation | ✔️ Type safety         | ❌ Runtime only    |
| Cross-module Navigation | ✔️ Dependency required | ❌ Not supported   |

### 4.2 vs. TheRouter

| Feature           | HMRouter              | TheRouter                      |
| :---------------- | :-------------------- | :----------------------------- |
| Routing Protocol  | Annotation-based      | Annotation + code registration |
| Cross-platform    | HarmonyOS only        | Android/iOS/Harmony            |
| Dynamic Routing   | ❌ Full rebuild        | ✔️ Hot-update                  |
| Lifecycle Control | ✔️ Extended callbacks | ✔️ Basic only                  |

## V. Best Practices

### 5.1 E-commerce Routing Scheme

```
// Cart page configuration  
@HMRouter({  
  pageUrl: "CartPage",  
  interceptors: [LoginInterceptor, CartValidationInterceptor],  
  transitions: {  
    enter: { type: 'slide', direction: 'right' },  
    exit: { type: 'fade' }  
  }  
})  
@Component  
export struct CartPage {  
  // Automatic parameter validation  
  static validateParams(params: Record<string, any>) {  
    return params.items.every(item => item.id > 0)  
  }  
}  
  
// Validation on navigation  
HMRouter.push("CartPage", { items: [101,102] })  
  .catch(err => console.error('Validation failed:', err))  
```

### 5.2 Multi-module Architecture

```
app-root/  
├── entry-module/       # Main entry  
│   └── src/  
│       └── main/ets/  
│           └── pages/  
│               └── MainActivity.ets  
├── feature-module/     # Feature modules  
│   ├── build.gradle  
│   └── src/  
│       └── main/ets/  
│           └── routes.json  # Module route declarations  
└── shared-module/      # Shared services  
    └── src/  
        └── main/ets/  
            └── services/    # Shared service routes  
```

