### Basic Network Request Implementation Steps in HarmonyOS



#### I. Basic Configuration Preparation

Before initiating network requests, complete two core configurations:

1. ​Permission Declaration​
   Add network access permissions in module.json5:

   ```
   "requestPermissions": [  
     {  
       "name": "ohos.permission.INTERNET"  
     }  
   ]  
   ```

   For HTTP requests, add "cleartextTraffic": true

2. ​Module Import​
   Import the network module in JS/TS files:

   ```
   import http from '@ohos.net.http';  
   ```



#### II. GET Request Implementation Steps

Sample scenario: Fetching news list data

1. ​Create Request Object​

   ```
   let httpRequest = http.createHttp();  
   ```

2. ​Configure Request Parameters​

   ```
   const url = "https://api.example.com/news";  
   const options = {  
     method: http.RequestMethod.GET,  
     header: {  
       "Content-Type": "application/json",  
       "Authorization": "Bearer your_token" // Optional auth header  
     },  
     connectTimeout: 10000, // 10s timeout  
     readTimeout: 15000  
   };  
   ```

3. ​Send Request & Handle Response​

   ```
   try {  
     const response = await httpRequest.request(url, options);  
     if (response.responseCode === http.ResponseCode.OK) {  
       const newsData = JSON.parse(response.result as string);  
       console.log("News title:", newsData.title);  
     } else {  
       console.error("Server error code:", response.responseCode);  
     }  
   } catch (err) {  
     console.error("Request error:", (err as BusinessError).message);  
   } finally {  
     httpRequest.destroy(); // Must release resources  
   }  
   ```



#### III. POST Request Implementation Steps

Sample scenario: Submitting user comments

1. ​Construct Request Body​

   ```
   const postData = {  
     articleId: 123,  
     content: "HarmonyOS development is efficient!",  
     userId: "user_001"  
   };  
   ```

2. ​Send POST Request​

   ```
   try {  
     const response = await httpRequest.request("https://api.example.com/comment", {  
       method: http.RequestMethod.POST,  
       header: { "Content-Type": "application/json" },  
       extraData: JSON.stringify(postData) // Pass JSON data  
     });  
     if (response.responseCode === 201) { // 201 Created  
       showToast("Comment submitted successfully");  
     }  
   } catch (err) {  
     showError("Network error, please retry");  
   }  
   ```

​

#### IV. Key Configuration Details

| Configuration Item | Description                  | Required |
| :----------------- | :--------------------------- | :------- |
| method             | HTTP method (GET/POST, etc.) | Yes      |
| header             | Headers (auth, content type) | No       |
| extraData          | POST request body            | No       |
| timeout            | Timeout (milliseconds)       | No       |
| responseType       | Response type (JSON/TEXT)    | No       |

​**​*

#### V. Less Common Requests

1. ​File Upload​

   ```
   const file = await fileio.readFile("/data/photo.jpg");  
   const uploadResponse = await httpRequest.request(  
     "https://api.example.com/upload",  
     {  
       method: http.RequestMethod.POST,  
       header: { "Content-Type": "multipart/form-data" },  
       extraData: file  
     }  
   );  
   ```

2. ​Streaming Response Handling​

   ```
   const stream = await httpRequest.request(  
     "https://example.com/large-video.mp4",  
     { responseType: http.HttpDataType.STREAM }  
   );  
   const buffer = await stream.readBuffer();  
   ```

​

#### VI. Error Handling

```
try {  
  // Request code...  
} catch (err) {  
  if ((err as BusinessError).code === 'ECONNABORTED') {  
    showError("Request timeout, check network");  
  } else if (err instanceof NetworkError) {  
    showError("Network connection failed");  
  } else {  
    showError("Server internal error");  
  }  
}  
```

​**​*

#### VII. Performance Optimization

1. ​Connection Reuse​

   ```
   // Create singleton during app initialization  
   const globalHttp = http.createHttp();  
   ```

2. ​Cache Strategy​

   ```
   const options = {  
     usingCache: true, // Enable cache  
     cacheMode: http.CacheMode.REQUEST_NETWORK // Prefer network data  
   };  
   ```

3. ​Concurrency Control​

   ```
   const maxConcurrent = 5; // Max concurrent requests  
   let currentCount = 0;  
   ```

​

#### VIII. Debugging

1. ​Packet Sniffing​
   Configure proxy with Charles/Fiddler:

   ```
   import network from '@ohos.net.network';  
   network.setProxy("192.168.1.100", 8888);  
   ```

2. ​Log Output​

   ```
   import log from '@ohos.hilog';  
   log.info("NET", "Request URL: %{public}s", url);  
   ```

​

#### IX. Full Code Example

```
@Entry  
@Component  
struct NewsPage {  
  private newsList: Array<{ title: string }> = [];  
  
  aboutToAppear() {  
    this.fetchNews();  
  }  
  
  async fetchNews() {  
    try {  
      let httpRequest = http.createHttp();  
      const response = await httpRequest.request(  
        "https://api.example.com/news",  
        {  
          method: http.RequestMethod.GET,  
          header: { "Content-Type": "application/json" }  
        }  
      );  
      if (response.responseCode === 200) {  
        this.newsList = JSON.parse(response.result as string);  
      }  
    } finally {  
      httpRequest?.destroy();  
    }  
  }  
  
  build() {  
    Column() {  
      ForEach(this.newsList, (item) => {  
        Text(item.title)  
          .fontSize(20)  
          .padding(16)  
      })  
    }  
  }  
}  
```

