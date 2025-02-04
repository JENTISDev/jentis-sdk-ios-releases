
# JentisSDK Documentation

## Overview
JentisSDK is a robust iOS SDK designed to facilitate app tracking to Jentis. This documentation provides details on installation, initialization, and usage of the SDK, including advanced configuration options.

---

## Table of Contents
1. [Installation](#installation)
2. [Initialization](#initialization)
3. [Usage](#usage)
   - [Setting User Consents](#setting-user-consents)
   - [Tracking Custom Events](#tracking-custom-events)
   - [Submitting Data](#submitting-data)
   - [Adding Enrichments](#adding-enrichments)
   - [Adding Custom Enrichments](#adding-custom-enrichments)
   - [Add to Cart](#add-to-cart)
   - [Configuring Log Level](#configuring-log-level)
4. [TrackConfig Details](#trackconfig-details)
4. [Offline Mode](#offline-mode)
5. [License](#license)

---

## Installation

### Swift Package Manager
Add `JentisSDK` as a dependency in your `Package.swift` file:
```swift
dependencies: [
    .package(url: "https://github.com/JENTISDev/jentis-sdk-ios-releases.git", from: "1.0.0")
]
```

### Manual Integration
Download `JentisSDK.xcframework` and add it to your project dependencies.

---

## Initialization

Before using the SDK, configure it with a `TrackConfig` instance. This should be done in the app’s startup code, typically in `AppDelegate` or the SwiftUI `@main` struct.

### Example:
```swift
import JentisSDK

@main
struct MyApp: App {
    init() {
        let config = TrackConfig(
            trackDomain: "tracking.yourdomain.com",
            container: "your-container",
            environment: .stage, // live or stage
            version: "1",
            debugCode: "optional-debug-code",
            authorizationToken: "your-authorization-token",
            customProtocol: "https" // Optional custom protocol
        )
        JentisService.configure(with: config)
    }
}
```

---

## Usage

### Setting User Consents
Set user consents for different vendors:
```swift
TrackingService.shared.setConsents([
    "googleanalytics": .allow,
    "facebook": .deny
])
```

### Tracking Custom Events
Track custom events using `push`:
```swift
TrackingService.shared.push([
    "track": "pageview",
    "title": "Home Page",
    "url": "https://example.com"
])
```

### Submitting Data
Submit all stored events to the server:
```swift
try await TrackingService.shared.submit()
```

### Adding Enrichments
Add enrichments to tracking payloads:
```swift
TrackingService.shared.addEnrichment(
    pluginId: "userSession",
    arguments: [
        "sessionStart": "2024-11-21T10:00:00Z",
        "isPremiumUser": true
    ],
    variables: ["sessionId", "userType"]
)
```

### Adding Custom Enrichments
Add custom enrichments to tracking payloads:
```swift
TrackingService.shared.addCustomEnrichment(
   pluginId: "enrichment_xxxlprodfeed",
   arguments: [
       "account": "JENTIS TEST ACCOUNT",
       "page_title": "MY PAGE TITLE",
       "productId": ["123", "ABC", "3"],
       "baseProductId": ["1"]
   ],
   variables: ["enrichment_product_variant"]
)
```

### Add to Cart
Push an "Add to Cart" action and submit the data:
```swift
try await TrackingService.shared.addToCart([
    "product_id": "12345",
    "quantity": "2",
    "price": "49.99"
])
```

### Configuring Log Level
Configure the log level for the SDK to control the verbosity of logs:
```swift
JentisService.setLogLevel(.debug)
```

Available log levels:
- `.debug`: Detailed information for debugging.
- `.info`: Informational messages.
- `.warning`: Warnings about potential issues.
- `.error`: Errors that occurred during execution.
- `.critical`: Critical errors.
- `.none`: Disables all logging.

---

## TrackConfig Details
The `TrackConfig` class manages the configuration of the SDK. Below are its properties:

| Property              | Type            | Description                          | Default     |
|-----------------------|-----------------|--------------------------------------|-------------|
| `trackDomain`         | `String`        | The destination domain for requests | Required    |
| `container`           | `String`        | Container value from JENTIS DCP     | Required    |
| `environment`         | `.live/.stage`  | Tracking environment                | `.stage`    |
| `version`             | `String?`       | Optional version for debug          | `nil`       |
| `debugCode`           | `String?`       | Optional debug code                 | `nil`       |
| `authorizationToken`  | `String`        | Token for authorization             | `""`        |
| `customProtocol`      | `String?`       | Custom protocol (e.g., `http`)      | `"https"`   |

---

### Offline Mode

The SDK supports offline tracking, allowing events to be cached locally and sent to the server once network connectivity is restored.

#### Configuration

To enable offline tracking, use the `enableOfflineTracking` property in the `TrackConfig`. Additionally, you can specify the maximum duration (in seconds) for which events should remain cached using the `offlineTrackingTimeout` property.

#### Example

```swift
let config = TrackConfig(
    trackDomain: "tracking.yourdomain.com",
    container: "your-container",
    environment: .live,
    enableOfflineTracking: true, // Enable offline tracking
    offlineTrackingTimeout: 7200 // Set timeout to 2 hours (in seconds)
)
JentisService.configure(with: config)
```

#### Properties

- `enableOfflineTracking`: Set this to `true` to enable offline event caching. When enabled, events will be stored locally if the network is unavailable.
- `offlineTrackingTimeout`: Specify the maximum time (in seconds) for events to remain in the local cache. Events older than this duration will be discarded.

#### Behavior

1. Events are stored locally if there is no network connection.
2. Stored events are automatically sent to the server when the network is restored.
3. Cached events exceeding the specified timeout will be discarded.

This configuration helps maintain reliable tracking even in scenarios with intermittent connectivity.


## License
This SDK is licensed under the MIT License.