

## I. System Popup Classification

### 1.1 Modal Dialogs (Strong Interaction)

#### 1.1.1 Dialog

* ​Features: Full-screen overlay, blocks all underlying interactions

* ​Use Cases: Critical operations confirmation (delete/exit), form submission

* ​Official Example:

  ```
  // Show confirmation dialog  
  promptAction.showDialog({  
    title: 'Delete Confirmation',  
    message: 'Are you sure to delete this file?',  
    buttons: [Text('Cancel'), Text('Confirm')]  
  });  
  ```

* ​Advantages: Clear interaction flow

* ​Limitations: Frequent use degrades user experience

#### 1.1.2 Action Menu

* ​Features: Bottom-aligned list with multiple options

* ​Use Cases: Feature selection (share/settings)

* ​Implementation:

  ```
  promptAction.showActionMenu({  
    title: 'Select Action',  
    buttons: [Text('Share'), Text('Save')]  
  });  
  ```

* ​Advantages: Quick access to key functions

* ​Limitations: Max 5 options before pagination required

### 1.2 Non-modal Dialogs (Weak Interaction)

#### 1.2.1 Loading Dialog

* ​Features: Auto-centered with progress animation

* ​Use Cases: Network requests, data processing

* ​Usage:

  ```
  // Show loading  
  loadingDialog.show({ message: 'Saving...' });  
  // Hide  
  loadingDialog.hide();  
  ```

* ​Advantages: Unified loading state management

* ​Limitations: No custom animation support

#### 1.2.2 Tooltip

* ​Features: Anchor-based positioning, auto-aligns to components

* ​Use Cases: Icon explanations, operation guides

* ​Implementation:

  ```
  @Component  
  struct Tooltip {  
    build() {  
      Popup() {  
        Text('Click to save changes')  
      }  
      .target(anchorRef)  
      .placement(Placement.Bottom)  
    }  
  }  
  ```

* ​Advantages: Precise contextual guidance

* ​Limitations: Fixed display duration

## II. Advanced Implementation

### 2.1 Navigation Dialog

* ​Features: Page-routing based with transparent background

* ​Configuration:

  ```
  @Entry  
  @Component  
  struct MainView {  
    build() {  
      NavDestination() {  
        Column() {  
          Button("Show Dialog")  
            .onClick(() => this.showDialog())  
        }  
        .mode(NavDestinationMode.DIALOG)  
      }  
    }  
  }  
  ```

* ​Use Cases: Form filling, image preview

* ​Key Consideration: Back button handling required

### 2.2 Global Overlay

* ​Implementation:

  ```
  // Create global overlay  
  const overlay = OverlayManager.createComponent(  
    new ComponentContent().setComponent(alertComponent)  
  );  
  overlay.show();  
  ```

* ​Typical Applications: Floating customer service buttons

* ​Limitations: Manual lifecycle management

## III. Best Practices

### 3.1 Design Specifications

| Type    | Max Duration | Min Trigger Distance | Animation Duration |
| :------ | :----------- | :------------------- | :----------------- |
| Dialog  | Unlimited    | 0                    | 300ms              |
| Loading | 30s          | -                    | 200ms              |
| Tooltip | 5s           | 48dp                 | 250ms              |

### 3.2 Performance Optimization

1. Memory: Call dispose() promptly

2. Rendering: Enable willMount preloading for complex dialogs

3. Animations: Prefer system-native animations

### 3.3 Common Scenarios

#### Scenario 1: Form Validation

```
TextInput()  
  .onChange((text) => {  
    if(text.length < 6) formValidator.showWarning('Minimum 6 characters');  
  });  
```

#### Scenario 2: Multi-step Guide

```
@State currentStep = 0;  
build() {  
  Stack() {  
    MainContent()  
    if(currentStep === 1) {  
      GuideOverlay()  
        .content("Click top-right to complete")  
        .onClose(() => currentStep = 2);  
    }  
  }  
}  
```

## IV. Key Considerations

1. ​Permissions: System dialogs don't require special permissions. Custom dialogs need ohos.permission.SYSTEM_ALERT_WINDOW

2. ​Style Consistency: Use @style for global theming:

   ```
   @style GlobalDialogStyle {  
     backgroundColor: Color.Transparent;  
     borderRadius: 8;  
   }  
   ```

3. ​Accessibility: Mandatory accessibilityLabel property

4. ​Lifecycle: Avoid showing dialogs in onInit

