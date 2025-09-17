# Daily Client SDK

## Getting started

If you are developing a video application and targeting Android apps, we think Daily's Android client SDK is a great choice! Below you'll find all you need to know to get started.

### Adding the dependency

To depend on the Daily Client package, you can add this package via Maven, for example in a `build.gradle` file, like the example below:

```groovy
dependencies {
    implementation 'co.daily:client:0.34.0'
    // ... other dependencies
}
```

Optionally, to use the camera video processor (for background blur and replacement), also add the following dependency:

```groovy
dependencies {
    // ...
    implementation 'co.daily:client-videoprocessor-plugin:0.1.1'
}
```

## About this repository

This repository has no actual *code* in it... what's up with that?

We felt a github repository would be the natural place for people to look for information about the library. The library's code isn't stored here; this repository only holds installation notes and the changelog.

## Learn more

Find the full official documentation [here](https://docs.daily.co/guides/products/mobile/android).
