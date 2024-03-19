# MMM-SmartWebDisplay

`MMM-SmartWebDisplay` is a module for MagicMirror². It allows displaying any web content on your [MagicMirror](https://github.com/MichMich/MagicMirror) and interacting with it through notifications or with [MMM-TelegramBot](https://github.com/eouia/MMM-TelegramBot).

This module is a major evolution of [MMM-iFrame-Ping](https://github.com/AgP42/MMM-iFrame-Ping)

## Main functionalities of MMM-SmartWebDisplay module:

- Periodically refresh the URL
- Rotating URLs in a list
- Receive notification/Telegram messages for the following actions:
  - Change URLs and update refresh rotating timer value
  - Go to the next/previous URL of the list
  - Play(or restart)/Pause/Stop the update and rotation of URLs

These notifications can be sent by several other MM modules and also (thanks to [MMM-RemoteControl](https://github.com/Jopyth/MMM-Remote-Control)) by external HTTP request, like for example IFTTT or Tasker (Android). You can also send notifications easily by using this fork of MMM-RemoteControl that adds specific controls: [MMM-RemoteControl for SmartWebDisplay](https://github.com/AgP42/MMM-Remote-Control)

- If a PIR-sensor using MMM-PIR-Sensor module is used, the display will not be updated during screen off (this behavior works also with all other modules that send the notification "USER_PRESENCE") and will be refreshed with the screen on.
- If the MMM-SmartWebDisplay module is hidden (by REMOTE-CONTROL or any Carousel module for example), the URL display will not be updated. As soon as one MMM-SmartWebDisplay module is displayed on the screen, an update will be requested.
- Possibility to display the date and time of the last update request (configurable)
- Possibility to declare several instances (but so far the notification will address all the instances together)

Known issues:
- If several instances of this module are declared, the notifications/Telegram messages sent will apply to each instance.

Some screenshots:

Displaying YouTube (displayLastUpdate: true):

![MMM-SmartWebDisplay](https://github.com/AgP42/MMM-iFrame-Ping/blob/master/screenshot/MMM-iFrame-Ping_youtube_update.png)

Displaying TRENDnet snapshot (displayLastUpdate: false):

![MMM-SmartWebDisplay](https://github.com/AgP42/MMM-iFrame-Ping/blob/master/screenshot/MMM-iFrame-Ping.png)

MMM-RemoteControl menu for SmartWebDisplay:

![MMM-RemoteControl](https://github.com/AgP42/MMM-Remote-Control/raw/master/.github/RemoteSWD_2.png)

## Installation

Git clone this repo into ~/MagicMirror/modules directory:
```
cd ~/MagicMirror/modules
git clone https://github.com/AgP42/MMM-SmartWebDisplay.git
```
and add the configuration section in your MagicMirror config file

## Update
```
cd ~/MagicMirror/modules/MMM-SmartWebDisplay
git pull
```

## Module configuration

To use this module, add it to the modules array in the `config/config.js` file:

```javascript
{
  module: 'MMM-SmartWebDisplay',
  position: 'middle_center',
  config: {
    // See 'Configuration options' for more information.
    logDebug: false, // set to true to get detailed debug logs. To see them: "Ctrl+Shift+i"
    height:"100%", // height
    width:"100%", // width
    updateInterval: 0, // in minuttes. 0 for no refresh
    NextURLInterval: 0.5, // in minuttes, 0 to not auto jump to next
    displayLastUpdate: true, // display the last update time
    displayLastUpdateFormat: 'ddd - HH:mm:ss', // format of the date and time to display
    url: ["http://magicmirror.builders/", "https://www.youtube.com/embed/Qwc2Eq6YXTQ?autoplay=1"], // URL to be displayed
    scrolling: "no", // whether or not to allow scrolling
    shutoffDelay: 10000 // delay in milliseconds to video shut-off while using together with MMM-PIR-Sensor 
  }
},
```

## Configuration options

The following properties can be configured:

| Option                | Description                                                                                                                                                                            |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `url`                 | List of the URL(s) to display. Default value: `["http://magicmirror.builders/", "https://www.youtube.com/embed/Qwc2Eq6YXTQ?autoplay=1"]`                                            |
| `width`               | the width of the iFrame. Default value: `"100%"`                                                                                                                                       |
| `height`              | the width of the iFrame. Default value: `"100px"`                                                                                                                                      |
| `scrolling`           | Allow a scroll bar or not. Default value: `"no"`                                                                                                                                       |
| `updateInterval`      | the update internal to refresh the display, in minutes. Set it to 0 to avoid refresh (case of videos). Default value: `0.5`                                                           |
| `NextURLInterval`     | The delay between 2 URL rotation, in minutes. Set it to 0 to avoid automatic rotation. If only 1 URL declared, it will be refresh with this delay (if not set to 0). Default value: `0.5` |
| `displayLastUpdate`   | If true, this will display the last update time at the end of the task list. See screenshot below. Default value: `false`                                                            |
| `displayLastUpdateFormat` | Format to use for the time display if displayLastUpdate is true. Default value: `'ddd - HH:mm:ss'`                                                                                    |
| `logDebug`            | Set to true to have all log infos on the console. Default value: `false`                                                                                                               |
| `shutoffDelay`        | The delay between getting USER_PRESENCE notification from the PIR sensor. Default value: `10000`                                                                                      |

## Notifications

Notifications you can send to this module:

| Command   | Description                                            | Payload                                                                    |
|-----------|--------------------------------------------------------|----------------------------------------------------------------------------|
| SWD_URL   | Change the URL displayed on MagicMirror.               | `{url:["https://magicmirror.builders/"]}`                                 |
| SWD_NEXT  | Move to the next URL in rotation.                      | -                                                                          |
| SWD_PREV  | Move to the previous URL in rotation.                  | -                                                                          |
| SWD_PLAY  | Resume URL rotation.                                   | -                                                                          |
| SWD_PAUSE | Pause URL rotation.                                    | -                                                                          |
| SWD_STOP  | Stop URL rotation.                                     | -                                                                          |

Example of HTTP request using MMM-RemoteControl:

- `http://192.168.xx.xx:8080/remote?action=NOTIFICATION&notification=SWD_URL&payload={"url":["https://magicmirror.builders/"]}`
- `http://192.168.xx.xx:8080/remote?action=NOTIFICATION&notification=SWD_NEXT`

Warning: "play/pause/stop" applies to URL rotation, not to the video itself. Interaction with the video itself is not possible.

## Use case examples

### Nest Camera streaming
As of right now, Nest Camera only supports streaming to iFrame when the camera feed is set to public. When you set it to public, you'll get a live URL and an iFrame embedded URL (should look like https://video.nest.com/embedded/live/wSbs3mRsOF?autoplay=1). For more info, check out this thread https://nestdevelopers.io/t/is-there-a-way-to-get-nest-camera-streams-in-an-iframe/813. 

### Youtube streaming
Just go to the video you want (see below for more details). Click share -> embed and pull out the URL. Then add the autoplay parameter (eg. https://www.youtube.com/embed/pcmjht0Hqvw?autoplay=1).

#### Youtube playlist
You can stream any public playlist or make a playlist (this requires a YouTube account). 
With your own playlist streaming on the mirror, you can change the contents directly on YouTube (adding videos, removing others, adding live channels and broadcasts..etc) using your mobile or desktop, without changing anything to the mirror and it'll be updated automatically 🥇

To do so: firstly, go to the first video in any playlist, right-click and choose Copy embed code as seen here:
![MMM-iFrame-Ping](https://github.com/AgP42/MMM-iFrame-Ping/blob/master/screenshot/youtubeplaylist.jpg)

now paste it on any notepad. It should look like this:
```
<iframe width="853" height="480" src="https://www.youtube.com/embed/XMIc4uTAMh0?list=PLbIZ6k-SE9ShGEZ_wuvG3hatiC6jWJgVm" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
```

Copy the `src` part and add `autoplay=1` at the end so the video auto starts, like this:
```
https://www.youtube.com/embed/XMIc4uTAMh0?list=PLbIZ6k-SE9ShGEZ_wuvG3hatiC6jWJgVm&autoplay=1
```

you can add multiple tags like link&TAG1&TAG2&TAG3
to enable list repeating add a tag

    loop=1

to remove the YouTube logo from the control bar add

    modestbranding=1

to disable video annotations add

    iv_load_policy=3

Small hint: you can test your link in the browser easily: just open a new tab and paste it to see how it will be exactly on the mirror

so we'll edit our link to make it auto playable and disable annoying annotations and remove the YouTube logo
```
https://www.youtube.com/embed/XMIc4uTAMh0?list=PLbIZ6k-SE9ShGEZ_wuvG3hatiC6jWJgVm&autoplay=1&modestbranding=1&iv_load_policy=3
```

wanna edit the YouTube player more? here is the full list of tags in The Official YouTube API page under Supported Parameters table:
to disable keyboard inputs to the player, add

    disablekb=1

to disable the player controls completely for a cleaner look, add

    controls=0

**Thanks to [theramez](https://github.com/AgP42/MMM-iFrame-Ping/issues/4) for this method**


The MIT License (MIT)
=====================

Copyright © 2019 Agathe Pinel

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

**The software is provided “as is”, without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software.**
