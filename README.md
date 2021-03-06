# AudioPlayer

A Flutter audio plugin (ObjC/Java) to play remote or local audio files 

## Features

- [x] Android & iOS
  - [x] play (remote file)
  - [x] stop
  - [x] pause
  - [x] onComplete
  - [x] onDuration / onCurrentPosition
  - [x] seek
  - [x] mute
- [x] Android ONLY
    - [x] Native Notification Controls
    - [x] music session with controls (50% done)
        - [x] music session creation
        - [x] play, pause, stop, seek controls
        - [ ] metadata broadcasting*
        - [ ] next, previous, queue management*
        - [ ] mediaBrowser for car/wear os library*
    - [x] audioFocus management
        - [x] requesting focus
        - [x] focus loss management

## Usage

[Example](https://github.com/moda20/audioplayer/blob/master/example/lib/main.dart)

Also used in [TuneIn](https://github.com/moda20/flutter-tunein)

To use this plugin :

- Add the dependency to your [pubspec.yaml](https://github.com/moda20/audioplayer/blob/master/example/pubspec.yaml) file.

```yaml
  dependencies:
    flutter:
      sdk: flutter
    audioplayer:
```

- Instantiate an AudioPlayer instance

```dart
//...
/// The useAndroidMediaControlNotifications argument sets whether to show the notification or hide it completely
/// The onlyShowNotificationWhenPlaying argument sets whether to show the notification or hide it based on the current state pf play : show only if playing
AudioPlayer audioPlugin = new AudioPlayer({bool useAndroidMediaControlNotifications=false, bool onlyShowNotificationWhenPlaying=false});
//...
```

### Player Controls

```dart
/// Will play the item given in url section, if metaData is passed it will also show on the native
/// android media controls 
audioPlayer.play(url, {title, author, albumArt, album});

/// Will pause the audio Play
audioPlayer.pause();

/// Will stop the audio Play
await audioPlayer.stop();

/// Will set the metadata of the song if given, without playing the media passed in the url
audioPlayer.setItem({String title,String author,String albumArt,String album, String uri})

/// Will set whether media notification is shown or not, even if you call showNotification manually
/// The arguments are the same as the Constructor arguments
Future<void> useNotificationMediaControls(bool value, bool onlyShowWhenPlaying);

/// Will show the media Controls, will update it if it is shown already
Future<void> showNotificationMediaControls();

/// Will hide the media Controls if it is shown instantly even if it is set to be used
Future<void> hideNotificationMediaControls();

```

### Status and current position

The dart part of the plugin listen for platform calls :

```dart
//...
_positionSubscription = audioPlayer.onAudioPositionChanged.listen(
  (p) => setState(() => position = p)
);

_audioPlayerStateSubscription = audioPlayer.onPlayerStateChanged.listen((s) {
  if (s == AudioPlayerState.PLAYING) {
    setState(() => duration = audioPlayer.duration);
  } else if (s == AudioPlayerState.STOPPED) {
    onComplete();
    setState(() {
      position = duration;
    });
  }
}, onError: (msg) {
  setState(() {
    playerState = PlayerState.stopped;
    duration = new Duration(seconds: 0);
    position = new Duration(seconds: 0);
  });
});
```

### Playback Keys (Android Only)

The dart part of the plugin that listen for playback keys ( bluetooth/cable headphones, remotes, ...) :

```dart

_audioPlaybackKeysSubscription = audioPlayer.onPlaybackKeyEvent.listen((s) {
  switch(s){
    case PlayBackKeys.PAUSE_KEY):
      //Handle the pause key event    
      break;
    case PlayBackKeys.PLAY_KEY:
      //Handle the play key, this can also be triggered by the play pause key
      break;
    case PlayBackKeys.PAUSE_KEY:
      //Handle the pause key, this can also be triggered by the play pause key
      break;
    case PlayBackKeys.NEXT_KEY:
      //Handle the next key event 
      break;
    case PlayBackKeys.PREV_KEY:
      //Handle the previous key event
      break;
    case PlayBackKeys.FAST_FORWARD_KEY:
      //Handle the fast forward key event
      break;
    case PlayBackKeys.REWIND_KEY:
      //Handle the rewind key event
      break;
    case PlayBackKeys.SEEK_KEY:
      //Handle the seek key event, (not yet implemented correctly)
      break;
    case PlayBackKeys.STOP_KEY:
      //Handle the stop key event
      break;
    }
}, onError: (msg) {
  //Error handling
});
```

### Audio Focus status (Android Only)

The dart part of the plugin that listens for the audio focus status change:

```dart
//...
_audioFocusSubscription = audioPlayer.onAudioFocusChange.listen((s) {
  switch(s){
    case AudioFocus.AUDIO_FOCUS_GAINED:{
      //Audio Focus has been gained
      break;
    }
    case AudioFocus.AUDIO_FOCUS_LOST:{
      //Audio Focus has been lost
      break;
    }
    case AudioFocus.NO_AUDIO_FOCUS:{
      //No Audio Focus has been issued (neutral state)
      break;
    }    
  }
}, onError: (msg) {
  //Error handling
});
```

Do not forget to cancel all the subscriptions when the widget is disposed.

## iOS

## :warning: iOS App Transport Security

By default iOS forbids loading from non-https url. To cancel this restriction edit your .plist and add :

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
## Troubleshooting

- If you get a MissingPluginException, try to `flutter build apk` on Android, or `flutter build ios`
- to use the plugin in a ObjC iOS project, add 'use_frameworks!' to your podfile cf. [example](https://github.com/moda20/blob/master/example/ios/Podfile)

## Getting Started

For help getting started with Flutter, view our online
[documentation](http://flutter.io/).

For help on editing plugin code, view the [documentation](https://flutter.io/platform-plugins/#edit-code).
