

## I. Core Understanding of Atomic Services

Atomic Services (formerly known as Atomicized Services) are lightweight service forms unique to HarmonyOS, characterized by three core features:

1. ​Zero Installation: Users can launch services instantly without downloading packages

2. ​Multi-device Adaptability: Supports 18+ devices including smartphones, tablets, and smart screens

3. ​Card-based Interaction: Exposes services through Smart Cards for quick access

Comparison with traditional apps:

| Aspect         | Atomic Services                                               | Traditional Apps       |
| :------------- | :------------------------------------------------------------ | :--------------------- |
| Package Size   | ≤10MB total                                                   | ≥100MB                 |
| Launch Methods | 6+ entry points (e.g., Smart Screen search, voice activation) | App drawer/icon only   |
| Development    | ArkTS language + Stage model                                  | Full-stack development |

## II. Development Environment Setup

### 1. Tool Preparation

```
# Install DevEco Studio 4.0+
Download: https://developer.harmonyos.com/cn/develop/deveco-studio

# Configure HarmonyOS SDK
Select API 11 (NEXT Developer Preview1) or higher
```

### 2. Project Creation

```
// Project creation workflow
File → New → Create Project → Atomic Service → Empty Ability
```

Key configurations:

* Package naming: com.example.service

* Device template: Select Phone/Tablet

* Enable distributed capabilities: Check "Distributed Data"

## III. Core Feature Implementation

### 1. Home Screen Development (index.ets)

```
@Entry
@Component
struct SmartTool {
  @State inputText: string = ""
  @State result: string = ""

  build() {
    Column.create()
      .child(SearchBar({
        placeholder: "Enter query...",
        onInput: (val) => this.inputText = val
      }))
      .child(Button("Search").onClick(() => this.executeQuery()))
      .child(ResultDisplay(this.result))
      .width('100%')
      .height('100%')
  }

  private async executeQuery() {
    if(this.inputText.trim() === "") return
    this.result = await fetch(`https://api.example.com?q=${encodeURIComponent(this.inputText)}`)
  }
}
```

### 2. Data Interaction Implementation

```
// Network request encapsulation
class ApiService {
  static async get(url: string): Promise<any> {
    const response = await fetch(url, {
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + auth.getToken()
      }
    })
    return response.json()
  }
}

// Usage example
const data = await ApiService.get('/weather?city=beijing')
```

## IV. Service Card Development

### 1. Static Card Configuration (widget.ets)

```
@Entry
@Component
struct WeatherCard {
  @State temperature: number = 22
  @State condition: string = "Sunny"

  build() {
    GridContainer() {
      Image($r("app.media.cloud"))
        .width(40)
        .height(40)
      
      Text(this.temperature + "℃")
        .fontSize(24)
        .fontWeight(FontWeight.Bold)
      
      Text(this.condition)
        .fontSize(18)
        .fontColor(Color.Blue)
    }
    .width('100%')
    .height(120)
    .padding(8)
  }
}
```

### 2. Dynamic Card Interaction

```
// Click event handling
@Entry
@Component
struct InteractiveCard {
  private isExpanded = false

  build() {
    Column.create()
      .child(Button("Expand").onClick(() => this.isExpanded = !this.isExpanded))
      .child(ConditionalRender(this.isExpanded, () => DetailView()))
      .height(this.isExpanded ? '100%' : 120)
  }
}
```

## V. Debugging & Deployment

### 1. Device Debugging Configuration

```
# Generate debug signature
./deveco-cli signing:generate --type debug

# Deploy to device
./deveco-cli run --device phone --profile debug
```

### 2. AppGallery Submission

1. ​Certificate Preparation:

   * Create AGC project ID

   * Generate .p12 keystore (≥8 characters password)

   * Upload CSR certificate

2. ​Build Configuration:

   ```
   // build-profile.json5
   {
     "signingConfigs": {
       "release": {
         "certificatePath": "keystore.p12",
         "certificatePassword": "123456",
         "alias": "my_alias"
       }
     }
   }
   ```

3. ​Submission Materials:

   * Privacy policy (publicly accessible URL)

   * App icon (1024x1024px SVG)

   * Service documentation

## VI. Performance Optimization

1. ​Resource Management:

   ```
   // Lazy image loading example
   Image($r("app.media.large_bg"))
     .loadingPolicy(ImageLoadingPolicy.Lazy)
   ```

2. ​Memory Control:

   * ≤150MB per-page memory usage

   * Use WeakRef for temporary objects

   ```
   let cache = new WeakRef({})
   ```

3. ​Launch Acceleration:

   * Preload critical resources

   ```
   // preload.json
   {
     "preloads": ["/resources/fonts/main.ttf"]
   }
   ```

> Recommendation: Use DevEco Studio's ​Performance Analyzer​ to monitor FPS and memory usage. Test card rendering with ​Device Preview​ mode. Ensure no local storage dependencies during debugging to maintain zero-install compliance.

This practical guide provides essential skills for HarmonyOS Atomic Service development. Start with simple tools and gradually master distributed data management and cross-device interaction. For advanced implementations, refer to Huawei's Atomic Service Development Specifications.
