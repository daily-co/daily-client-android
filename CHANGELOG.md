# Changelog

All notable changes to the **daily-android** SDK will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## 0.8.0

### Changed

- The `streamingSettings` parameter in `updateRecording` is no longer optional.

  - Before:
  
    ```kotlin
    fun updateLiveStream(
        streamingSettings: StreamingUpdateSettings? = null,
        streamId: StreamId? = null,
        listener: RequestListener? = null
    )
    ```

  - After:

    ```kotlin
    fun updateLiveStream(
        streamingSettings: StreamingUpdateSettings,
        streamId: StreamId? = null,
        listener: RequestListener? = null
    )
    ```
    
- The timestamp fields in `CallConfiguration` are now of type `Long` rather than `Int`.

  - Before:

    ```kotlin
    val roomExpiration: Int? = null,
    val tokenExpiration: Int? = null,
    val roomNotBefore: Int? = null,
    val tokenNotBefore: Int? = null,
    ```

  - After:

    ```kotlin
    val roomExpiration: Long? = null,
    val tokenExpiration: Long? = null,
    val roomNotBefore: Long? = null,
    val tokenNotBefore: Long? = null,
    ```
    
- The `onLiveStreamStarted` callback now has a single `LiveStreamStatus` parameter, which contains
  the information that was previously provided as multiple parameters.

  - Before:
  
    ```kotlin
    fun onLiveStreamStarted(
        streamId: StreamId,
        startedBy: ParticipantId,
        layout: StreamingLayout?
    )
    ```
    
  - After:
  
    ```kotlin
    fun onLiveStreamStarted(status: LiveStreamStatus)
    ```

- The `VideoMaxQuality` enum has been split into two separate enums,
  `VideoSendSettingsMaxQuality` and `VideoReceiveSettingsMaxQuality`.

- The `facingMode` field of `VideoMediaTrackSettings` now has an enum type
  (`FacingMode`) rather than a `String` type.

  - Before:

    ```kotlin
    val facingMode: String
    ```

  - After:

    ```kotlin
    val facingMode: FacingMode
    ```

- Added two additional constants `left` and `right` to the `FacingMode` enum.

  - Before:

    ```kotlin
    enum class FacingMode {
      user,
      environment;
    }
    ```

  - After:

    ```kotlin
    enum class FacingMode {
      user,
      environment,
      left,
      right;
    }
    ```

## [0.7.1]

### Added

<!-- for new functionality -->

- `CallClient.callConfigFor()` -- request the call configuration for a given Daily room URL.

- A new `callConfiguration` field has been added to `CallClient`. This field is updated a call has been successfully joined or left.

- Support for specifying a meeting token when joining a call.

- Added `Builder` classes for several `Update` types (such as `InputSettingsUpdate.Builder`),
  to make instantiating those types easier in Java.

- Added simple media control convenience methods to `CallClient`:
  - `setInputEnabled()` and `setInputsEnabled()`
  - `setIsPublishing()`
  - `setSubscriptionState()`
  - `setSubscriptionProfile()`

### Changed

- Changed the order of arguments to `CallClient.startLiveStream()`:

  - Before:

    ```kotlin
    fun startLiveStream(
        endpoints: LiveStreamEndpoints,
        streamingSettings: StreamingSettings? = null,
        forceNew: StreamingStartMode = StreamingStartMode.onlyIfNotAlreadyStreaming,
        streamId: StreamId? = null,
        listener: RequestListenerWithData<StreamId>? = null
    )
    ```

  - After:
  
    ```kotlin
    fun startLiveStream(
        endpoints: LiveStreamEndpoints,
        streamingSettings: StreamingSettings? = null,
        streamId: StreamId? = null,
        forceNew: StreamingStartMode = StreamingStartMode.onlyIfNotAlreadyStreaming,
        listener: RequestListenerWithData<StreamId>? = null
    )
    ```

- Changed the order of arguments to `CallClient.startRecording()`.

  - Before:

    ```kotlin
    fun startRecording(
        streamingSettings: StreamingSettings? = null,
        forceNew: StreamingStartMode = StreamingStartMode.onlyIfNotAlreadyStreaming,
        streamId: StreamId? = null,
        listener: RequestListenerWithData<StreamId>? = null
    )
    ```

  - After:
  
    ```kotlin
    fun startRecording(
        streamingSettings: StreamingSettings? = null,
        streamId: StreamId? = null,
        forceNew: StreamingStartMode = StreamingStartMode.onlyIfNotAlreadyStreaming,
        listener: RequestListenerWithData<StreamId>? = null
    )
    ```

- Changed the order of arguments to `CallClient.updateRecording()`.

  - Before:

    ```kotlin
    fun updateRecording(
        streamId: StreamId? = null,
        streamingSettings: StreamingUpdateSettings? = null,
        listener: RequestListener? = null
    )
    ```

  - After:
  
    ```kotlin
    fun updateRecording(
        streamingSettings: StreamingUpdateSettings? = null,
        streamId: StreamId? = null,
        listener: RequestListener? = null
    )
    ```

### Removed

<!-- for removed functionality -->

- The `VideoView.release()` method has been removed, as cleanup is now automatic.

- The `MeetingSession.topology` field has been removed. 

## [0.7.0]

This library version was corrupted by a faulty build script and will crash unexpectedly, so it should not be used. Please use version 0.7.1 instead.

## [0.6.0]

### Added

- A new wrapper class `CallClientCoroutineWrapper` has been added, to allow the `CallClient`
  APIs to be used from Kotlin Coroutines. The `CallClient` APIs now notify listeners when
  asynchronous operations are completed, as detailed below.

#### APIs

- `CallClient.sendAppMessage()` -- this allows sending custom JSON messages either to all
  participants, or a participant of your choice.

- `CallClient.startRecording()` -- starts a recording.

- `CallClient.stopRecording()` -- stops a recording.

- `CallClient.updateRecording()` -- changes the configuration of an existing recording.

- A new `permissions` field has been added to the participant `Info` class. It describes the
  the participant's in-call permissions, and is non-null only upon joining. It's made up of:

  - `hasPresence`: a boolean describing whether the participant has presence (i.e. whether they
    show up in others' `participants()`)
  - `canSend`: a set describing which kinds of media a participant is allowed to send (`camera`,
    `microphone`, `screenVideo`, `screenAudio`, `customVideo`, and `customAudio`).

  When the local participant's `canSend` permission is updated by a meeting owner, they may end up
  losing permission to send a kind of media they were previously sending. When permission to send
  that media is restored, they will automatically resume sending it (assuming `inputs()` and
  `publishing()` are both still enabled for that media.)

- A new `offReasons` field has been added to `ParticipantAudioInfo` and `ParticipantVideoInfo`,
  describing why a participant's media might be in the `state` `MediaState.off`. There may be more
  than one of the following reasons:

  - `user`: The user explicitly wants the media to be off.
  - `bandwidth`: The media has been automatically stopped due to bandwidth concerns.
  - `sendPermission`: The media is off due to the sender lacking proper `canSend` permission.

- A new `CallClient.updateRemoteParticipants()` method that allows you to modify things about
  remote participants that you can control as a meeting owner (it fails if you're not the meeting
  owner). Today the only thing you can control about remote participants is their `permissions`
  (i.e. `hasPresence` and `canSend`). Any permission not specified will not be updated.

- `CallClient.startLiveStream()` -- starts a live stream to the specified endpoints.

- `CallClient.addLiveStreamingEndpoints()` -- adds new endpoints to an existing live stream.

- `CallClient.removeLiveStreamingEndpoints()` -- removes endpoints from an existing live stream.

- `CallClient.stopLiveStream()` -- stops an existing live stream.

- `CallClient.updateLiveStream()` -- alters the layout of an existing live stream.

#### Events

- `CallClientListener.onAppMessage()` -- this callback will be invoked whenever an app message
  is received from another participant.

- `CallClientListener.onRecordingStarted()` -- emitted when a recording starts.

- `CallClientListener.onRecordingStopped()` -- emitted when a recording stops.

- `CallClientListener.onRecordingError()` -- emitted when a recording error occurs.

- A new `reason` field has been added to `CallClientListener.onParticipantLeft()`. It can assume
  two values:

  - `hidden`: The participant has lost presence (i.e. their `hasPresence` permissions has been
    revoked).
  - `leftCall`: The participant has left the room.

- New event `onNetworkStatsUpdated` on `CallClientListener` which is emitted when a new batch of network statistics are calculated. This also comes with the getter `getNetworkStats` on `CallClient`.

```kotlin
  fun onNetworkStatsUpdated(newNetworkStatistics: NetworkStats)
```

- New event `onParticipantCounts` on `CallClientListener` which is emitted when the participant counts change. This also comes with the getter `participantCounts` on `CallClient`.

```kotlin
  fun onParticipantCounts(newParticipantCounts: ParticipantCounts)
```

- `CallClientListener.onLiveStreamStarted()` -- emitted when a live stream starts.

- `CallClientListener.onLiveStreamStopped()` -- emitted when a live stream stops.

- `CallClientListener.onLiveStreamError()` -- emitted when a live stream encounters an error.

- `CallClientListener.onLiveStreamWarning()` -- emitted when a live stream encounters a warning.

### Changed

- `CallState.new` has been renamed to `CallState.initialized`

- The `CallClient` has been modified so that all blocking operations can be performed asynchronously.

  - The following methods have an extra (optional) listener parameter, that can be used to handle
    the request completing or encountering an error:
    - `setUserName()`
    - `setAudioDevice()`
    - `updateInputs()`
    - `updateSubscriptions()`
    - `updateSubscriptionProfiles()`
    - `updatePublishing()`
    - `leave()`
  - The `join()` method now has a mandatory listener parameter (see below).
  - Methods in `CallClient` which were previously `suspend` are no longer `suspend`, but rather
    enqueue the operation to be completed in the background, and immediately return. The listener
    associated with each API call will be notified when the operation completes.
    - If you are using Kotlin Coroutines, a new wrapper class `CallClientCoroutineWrapper` has been
      added which exposes `suspend` functions rather than listeners. Depending on your preference,
      you may use this class instead of `CallClient`.

### Deprecated

<!-- for soon-to-be removed functionality -->

- n/a

### Removed

<!-- for removed functionality -->

- The `VideoView.release()` method has been removed, as cleanup is now automatic.

### Fixed

<!-- for fixed bugs -->

- n/a

### Performance

<!-- for performance-relevant changes -->

- n/a

### Security

<!-- for security-relevant changes -->

- n/a

### Other

<!-- for everything else -->

- n/a

## [0.5.0]

### Added

- Support for specifying a meeting token when joining a call.

### Changed

<!-- for changed functionality -->

- The `VideoView` now automatically releases its resources when it is detached from the window.

- The signature of the `CallClient.join()` method has changed to allow for a meeting token to be
  specified. As mentioned above, a listener parameter has also been added.

  - Before:

    ```kotlin
    suspend fun join(
        url: String,
        clientSettings: ClientSettingsUpdate? = null
    )
    ```

  - After:
    ```kotlin
    suspend fun join(
        url: String,
        meetingToken: MeetingToken? = null,
        clientSettings: ClientSettingsUpdate? = null,
        listener: RequestListenerWithData<CallJoinData>
    )
    ```

### Deprecated

<!-- for soon-to-be removed functionality -->

- n/a

### Removed

<!-- for removed functionality -->

- The `VideoView.release()` method has been removed, as cleanup is now automatic.

### Fixed

<!-- for fixed bugs -->

- n/a

### Performance

<!-- for performance-relevant changes -->

- n/a

### Security

<!-- for security-relevant changes -->

- n/a

### Other

<!-- for everything else -->

- n/a

## [0.4.0]

### Added

- New function on `CallClient` to allow set the participant username.

  ```kotlin
    fun setUserName(userName: String)
  ```

- Added support for working with audio input/output devices in order to set them up and query them, so now you can build a device picker:

  - The function `availableDevices` now also returns a list of available audio devices. The data returned can be used to change the audio device that are currently in use.
  - New function on `CallClient` to allow get the current audio device
    ```kotlin
      fun audioDevice()
    ```
  - New function on `CallClient` to allow sets the audio input/output device. It takes a deviceId as an argument that matches an id returned by availableDevices.
    ```kotlin
      fun setAudioDevice(deviceId)
    ```
  - New event `CallClientListener` which is emitted when the available devices change.
    ```kotlin
      fun onAvailableDevicesUpdated(availableDevices: AvailableDevices)
    ```

- Now is possible to include `VideoView` in an XML layout, with properties `daily_bring_to_front`
  and `daily_scale_mode`.

### Changed

<!-- for changed functionality -->

- Increased minimum Android SDK requirement to 24 (Android 7.0).

- The function `VideoView.bringVideoToFront()` has been replaced with a property,
  `VideoView.bringVideoToFront`.

- If a `VideoView` is brought to the front using `bringVideoToFront`, it now only sits on top of
  other `VideoView` instances, and will not occlude other `Views` drawn over it.

- The `Lifecycle` parameter for the `CallClient` constructor is now optional. If omitted, the
  `CallClient` should be manually cleaned up using `CallClient.release()`.

- It is now possible to specify a custom `Handler` when creating the `CallClient`. `CallClient`
  APIs should be called either from the main thread, or from the thread associated with your
  `Handler` (if you've specified one).

### Deprecated

<!-- for soon-to-be removed functionality -->

- n/a

### Removed

<!-- for removed functionality -->

- n/a

### Fixed

<!-- for fixed bugs -->

- n/a

### Performance

<!-- for performance-relevant changes -->

- n/a

### Security

<!-- for security-relevant changes -->

- n/a

### Other

<!-- for everything else -->

- n/a

## [0.3.1] - 2022-07-27

### Fixed

- Fixed an issue where the `onSubscriptionProfilesUpdated` method was always required to be overridden when adding a listener.

## [0.3.0] - 2022-07-22

### Added

- Support to video scale mode on `VideoView`:

  - Before, video view always worked scaling the video to fill all the screen. Now video view has the property `videoScaleMode`, which you can set one of these values:
    - `VideoScaleMode.FILL`: video frame is scaled to fill the size of the view by maintaining the aspect ratio. Some portion of the video frame may be clipped. This is the default value.
    - `VideoScaleMode.FIT`: video frame is scaled to fit the size of the view by maintaining the aspect ratio, this probably will make borders to be displayed.

- Support to receive screen share.

  - Now, `participant.media` has two new fields:
    - `screenAudio`: contains the participant's screen audio track and track state.
    - `screenVideo`: contains the participant's screen video track and track state.

- Add support to identify the active speaker.

  - New function on `CallClient` to check who is the active speaker

    ```kotlin
      fun activeSpeaker(): Participant?
    ```

  - New event `CallClientListener` which is emitted when the active speaker changes.

    ```kotlin
      fun onActiveSpeakerChanged(activeSpeaker: Participant?)
    ```

- Add support to update subscriptions

  - New function on `CallClient` to update subscriptions. Configure how (or if) to subscribe to remote media tracks.

    ```kotlin
      suspend fun updateSubscriptions(
        forParticipants: Map<ParticipantId, Update<SubscriptionSettingsUpdate>>? = null,
        forParticipantsWithProfiles: Map<SubscriptionProfile, Update<SubscriptionSettingsUpdate>>? = null
      ): Map<ParticipantId, SubscriptionSettings>
    ```

  - New event `CallClientListener` which is emitted when subscriptions change.

    ```kotlin
      fun onSubscriptionsUpdated(subscriptions: Map<ParticipantId, SubscriptionSettings>)
    ```

  - New function on `CallClient` to check the current subscriptions

    ```kotlin
      fun subscriptions(): Map<ParticipantId, SubscriptionSettings>
    ```

  - New function on `CallClient` to update subscription profiles. Configure how (or if) to subscribe to remote media tracks.

    ```kotlin
      suspend fun updateSubscriptionProfiles(
        subscriptionProfiles: Map<SubscriptionProfile, Update<SubscriptionProfileSettingsUpdate>>
      ): Map<SubscriptionProfile, SubscriptionProfileSettings>
    ```

  - New event `CallClientListener` which is emitted when subscriptions profile change.

    ```kotlin
      fun onSubscriptionProfilesUpdated(subscriptionProfiles: Map<SubscriptionProfile, SubscriptionProfileSettings>)
    ```

  - New function on `CallClient` to check the current subscription profiles.

    ```kotlin
      fun subscriptionProfiles(): Map<SubscriptionProfile, SubscriptionProfileSettings>
    ```

### Changed

- Renamed the enum class from `VideoSendSettingsMaxQuality` to `VideoMaxQuality`, this way the same class can be used for both send and receive settings.
- Renamed the enum class from `VideoSendSettingsMaxQualityUpdate` to `VideoMaxQualityUpdate`, this way the same class can be used for both send and receive settings.

  - Before:

    ```kotlin
      val publishingSettingsIntent = PublishingSettingsUpdate(
            camera = CameraPublishingSettingsUpdate(
                sendSettings = VideoSendSettingsUpdate(
                    encodings = VideoEncodingsSettingsUpdate(
                        settings = mapOf(
                            VideoSendSettingsMaxQualityUpdate.low to
                                    VideoEncodingSettingsUpdate(
                                        maxBitrate = BitRate(80000),
                                        maxFramerate = FrameRate(10),
                                        scaleResolutionDownBy = Scale(4F)
                                    ),
                            VideoSendSettingsMaxQualityUpdate.medium to
                                    VideoEncodingSettingsUpdate(
                                        maxBitrate = BitRate(680000),
                                        maxFramerate = FrameRate(30),
                                        scaleResolutionDownBy = Scale(1F)
                                    )
                        )
                    )
                )
            )
        )
    ```

  - After:

    ```kotlin
      val publishingSettingsIntent = PublishingSettingsUpdate(
            camera = CameraPublishingSettingsUpdate(
                sendSettings = VideoSendSettingsUpdate(
                    encodings = VideoEncodingsSettingsUpdate(
                        settings = mapOf(
                            VideoMaxQualityUpdate.low to
                                    VideoEncodingSettingsUpdate(
                                        maxBitrate = BitRate(80000),
                                        maxFramerate = FrameRate(10),
                                        scaleResolutionDownBy = Scale(4F)
                                    ),
                            VideoMaxQualityUpdate.medium to
                                    VideoEncodingSettingsUpdate(
                                        maxBitrate = BitRate(680000),
                                        maxFramerate = FrameRate(30),
                                        scaleResolutionDownBy = Scale(1F)
                                    )
                        )
                    )
                )
            )
        )
    ```

## [0.2.0] - 2022-06-14

### Changed

- Update publishing no longer takes input settings

  - Before:

    ```kotlin
      suspend fun updatePublishing(
          publishSettings: Update<PublishingSettingsUpdate>,
          inputSettings: Update<InputSettingsUpdate>? = null
      ): PublishingSettings
    ```

  - After:

    ```kotlin
        suspend fun updatePublishing(
            publishSettings: Update<PublishingSettingsUpdate>,
        ): PublishingSettings
    ```

- Making CallClient a lifecycle aware component.

  - Before:

    ```kotlin
      // Creating the CallClient inside your Activity.
      val callClient = CallClient(applicationContext)
      // Invoking to destroy the CallClient when your activity was destroyed
      callClient.onDestroy()
    ```

  - After:

    ```kotlin
      // Creating the CallClient inside your Activity.
      val callClient = CallClient(applicationContext, this.lifecycle)
      // CallClient is automatically destroyed and release the resources.
    ```

- Refactor how the classes are organized in each package

  - Before:

    ```plain
      └── co
        └── daily
            ├── CallClient.kt
            ├── CallClientListener.kt
            ├── DailyEvent.kt
            ├── UnknownCallClientError.kt
            ├── AvailableDevices.kt
            ├── CallJoinData.kt
            ├── MediaConstraints.kt
            ├── MediaDeviceInfo.kt
            ├── MediaStreamTrack.kt
            ├── MeetingSession.kt
            ├── Participant.kt
            ├── Participants.kt
            ├── ClientSettings.kt
            ├── ClientSettingsUpdate.kt
            ├── DailyAudioManager.kt
            ├── Serializers.kt
            ├── VideoView.kt
    ```

  - After:

    ```plain
      └── co
        └── daily
            ├── CallClient.kt
            ├── CallClientListener.kt
            ├── context
            │ └── CallClientSharedContext.kt
            ├── event
            │ └── DailyEvent.kt
            ├── exception
            │ ├── UnknownCallClientError.kt
            │ └── UnsupportedSerializeException.kt
            ├── model
            │ ├── AvailableDevices.kt
            │ ├── CallJoinData.kt
            │ ├── MediaConstraints.kt
            │ ├── MediaDeviceInfo.kt
            │ ├── MediaStreamTrack.kt
            │ ├── MeetingSession.kt
            │ ├── Participant.kt
            │ └── Participants.kt
            ├── settings
            │ ├── ClientSettings.kt
            │ └── ClientSettingsUpdate.kt
            ├── util
            │ ├── DailyAudioManager.kt
            │ └── Serializers.kt
            └── view
                └── VideoView.kt
    ```

### Fixed

- Fixed the issue that sometimes the app was crashing when leaving the meeting.
- Closed the video capturer when invoking `getUserMedia` with camera `isEnabled:false`

### Other

- `daily-android` can now be built using a pre-compiled `libwebrtc` for all platforms, allowing compiling daily-android even on Mac.
- Refactor the way we were handling the dependencies and packaging `daily-android`. Now the only libraries that are packaged together with `daily-android` are the jars from `libwebrtc`. All other dependencies are specified on `pom.xml` to be handled by Maven,

## [0.1.1] - 2022-04-14

Initial release.

