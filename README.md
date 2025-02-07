![Bodega Logo](Images/logo.jpg)

### A simple store for all your basic needs, accepting cache money my friend 🐱

Is this library the best caching library? Absolutely not. But as a born and raised New Yorker I can attest that nobody thinks a [bodega](https://en.wikipedia.org/wiki/Bodega_(store) is the best store either, yet we appreciate 'em and what they do for us. Like a bodega this library is there for you when you need something simple and you want it to work.

Bodega is a straightforward actor-based library for writing files to disk with an incredibly simple API. Bodega is fully usable and useful on it's own, but it's also the foundation of [Boutique](https://github.com/mergesort/Boutique). You can find a reference implementation of an app built atop the Model View Controller Store architecture in this [repo](https://github.com/mergesort/MVCS) which shows you how to make an offline-ready realtime updating SwiftUI app in only a few lines of code. You can read more about the thinking behind the architecture in this blog post exploring the [MVCS architecture](https://build.ms/drafts/model-view-controller-store).

---

* [Getting Started](#getting-started)
* [DiskStorage](#diskstorage)
* [ObjectStorage](#objectstorage)
* [Further Exploration](#further-exploration)

---

### Getting Started

Bodega provides two kinds of storage for you, `DiskStorage` and `ObjectStorage`. `DiskStorage` is for writing `Data` to disk, and `ObjectStorage` builds upon `DiskStorage` allowing you to write any `Codable` object to disk using a very similar API.

Both `DiskStorage` and `ObjectStorage` are implemented as actors which means they take care of properly synchronizing disk reads and writes. Until Swift implements [custom executors](https://forums.swift.org/t/support-custom-executors-in-swift-concurrency/44425) there will probably be a small performance penalty when using `ObjectStorage` since one actor has to talk to another, but in limited usage and performance profiling I have noticed no performance issues. No promises though!

---

### DiskStorage

```swift
// Initialize a DiskStorage object
let storage = DiskStorage(
    storagePath: FileManager.default
        .urls(for: .documentDirectory, in: .userDomainMask)
        .first!
        .appendingPathComponent("Quotes")
)

// CacheKeys can be generated from a String or URL.
// URLs will be reformatted into a file system safe format before writing to disk.
let url = URL(string: "https://redpanda.club/dope-quotes/dolly-parton")
let cacheKey = CacheKey(url: url)
let data = Data("Find out who you are. And do it on purpose. - Dolly Parton".utf8)

// Write data to disk
try await storage.write(data, key: cacheKey)

// Read data from disk
let readData = await storage.read(key: cacheKey)

// Remove data from disk
try await storage.remove(key: Self.testCacheKey)
```

---

### ObjectStorage

`ObjectStorage` has a very similar API to `DiskStorage`, but with slight naming deviations to be more explicit that you're working with objects and not data.

```swift
// Initialize a DiskStorage object
let storage = ObjectStorage(
    storagePath: FileManager.default
        .urls(for: .documentDirectory, in: .userDomainMask)
        .first!
        .appendingPathComponent("Quotes")
)

let cacheKey = CacheKey("churchill-optimisim")

let quote = Quote(
    id: "winston-churchill-1",
    text: "I am an optimist. It does not seem too much use being anything else.",
    author: "Winston Churchill",
    url: URL(string: "https://redpanda.club/dope-quotes/winston-churchill")
)

// Store an object to disk
try await storage.store(Self.testObject, forKey: cacheKey)

// Read an object from disk
let readObject: CodableObject? = await storage.object(forKey: cacheKey)

// Grab all the keys, which at this point will be one key, `cacheKey`.
let allKeys = await storage.allKeys()

// Verify by calling `keyCount`, both key-related methods are also available on `DiskStorage`.
await storage.keyCount()

// Remove an object from disk
try await storage.removeObject(forKey: cacheKey)
```

---

### Further Exploration

Bodega is very useful on it's own for saving files and objects to disk, but it's made even more powerful by using [Boutique](https://github.com/mergesort/Boutique). Boutique is a `Store` and serves as the foundation of a Model View Controller Store architecture I've developed. MVCS brings together the familiarity and simplicity of the [MVC architecture](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html) you know and love with the power of a `Store`, to give your app a simple but well-defined state management and data architecture.

If you'd like to learn more about how it works you can read about the philosophy in a [blog post](https://build.ms/drafts/model-view-controller-store) where I explore MVCS for SwiftUI, and you can find a reference implementation of an offline-ready realtime updating MVCS app powered by Boutique in this [repo](https://github.com/mergesort/MVCS).

---

### Requirements

- iOS 13.0+
- macOS 11.0
- Xcode 13.2+

### Installation

#### Swift Package Manager

The [Swift Package Manager](https://www.swift.org/package-manager) is a tool for automating the distribution of Swift code and is integrated into the swift compiler.

Once you have your Swift package set up, adding Bodega as a dependency is as easy as adding it to the dependencies value of your `Package.swift`.

```swift
dependencies: [
    .package(url: "https://github.com/mergesort/Bodega.git", .upToNextMajor(from: "1.0.0"))
]
```

#### Manually

If you prefer not to use SPM, you can integrate Bodega into your project manually by copying the files in.

---

## About me

Hi, I'm [Joe](http://fabisevi.ch) everywhere on the web, but especially on [Twitter](https://twitter.com/mergesort).

## License

See the [license](LICENSE) for more information about how you can use Bodega.
