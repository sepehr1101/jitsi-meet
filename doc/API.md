Jitsi Meet API
============

You can use Jitsi Meet API to create Jitsi Meet video conferences with custom GUI.

Installation
==========

To embed Jitsi Meet API in your application you need to add Jitsi Meet API library

```javascript
<script src="https://meet.jit.si/lib-jitsi-meet.js"></script>
```

Now you can access Jitsi Meet API trough the ```JitsiMeetJS``` global object.

Components
=========

Jitsi Meet API has the following components:

* JitsiMeetJS

* JitsiConnection

* JitsiConference

* JitsiTrack

Usage
======
JitsiMeetJS
----------
You can access the following methods and objects trough ```JitsiMeetJS``` object.


*  ```JitsiMeetJS.init(options)``` - this method initialized Jitsi Meet API.
The ```options``` parameter is JS object with the following properties:
    1. useIPv6 - boolean property
    2. desktopSharingChromeMethod - Desktop sharing method. Can be set to 'ext', 'webrtc' or false to disable.
    3. desktopSharingChromeExtId - The ID of the jidesha extension for Chrome or Firefox. Example: 'mbocklcggfhnbahlnepmldehdhpjfcjp'
    desktopSharingChromeSources - Array of strings with the media sources to use when using screen sharing with the Chrome extension. Example: ['screen', 'window']
    4. desktopSharingChromeMinExtVersion - Required version of Chrome extension. Example: '0.1'
    5. desktopSharingFirefoxExtId - The ID of the jidesha extension for Firefox. If null, we assume that no extension is required.
    6. desktopSharingFirefoxDisabled - Boolean. Whether desktop sharing should be disabled on Firefox. Example: false.
    7. desktopSharingFirefoxMaxVersionExtRequired - The maximum version of Firefox which requires a jidesha extension. Example: if set to 41, we will require the extension for Firefox versions up to and including 41. On Firefox 42 and higher, we will run without the extension. If set to -1, an extension will be required for all versions of Firefox.
    8. desktopSharingFirefoxExtensionURL - The URL to the Firefox extension for desktop sharing. "null" if no extension is required.
    9. disableAudioLevels - boolean property. Enables/disables audio levels.

* ```JitsiMeetJS.JitsiConnection``` - the ```JitsiConnection``` constructor. You can use that to create new server connection.

* ```JitsiMeetJS.setLogLevel``` - changes the log level for the library. For example to have only error messages you should do:
```
JitsiMeetJS.setLogLevel(JitsiMeetJS.logLevels.ERROR);
```

* ```JitsiMeetJS.createLocalTracks(options)``` - Creates the media tracks and returns them trough ```Promise``` object.
    - options - JS object with configuration options for the local media tracks. You can change the following properties there:
        1. devices - array with the devices - "desktop", "video" and "audio" that will be passed to GUM. If that property is not set GUM will try to get all available devices.
        2. resolution - the prefered resolution for the local video.
        3. cameraDeviceId - the deviceID for the video device that is going to be used
        4. micDeviceId - the deviceID for the audio device that is going to be used

* ```JitsiMeetJS.enumerateDevices(callback)``` - returns list of the available devices as a parameter to the callback function. Every device is a object with the following format:
    - label - the name of the device
    - kind - "audioinput" or "videoinput"
    - deviceId - the id of the device.

* ```JitsiMeetJS.isDeviceListAvailable()```- returns true if retrieving the device list is support and false - otherwise.

* ```JitsiMeetJS.isDesktopSharingEnabled()``` - returns true if desktop sharing is supported and false otherwise. NOTE: that method can be used after ```JitsiMeetJS.init(options)``` is completed otherwise the result will be always null.

* ```JitsiMeetJS.events``` - JS object that contains all events used by the API. You will need that JS object when you try to subscribe for connection or conference events.
    We have two event types - connection and conference. You can access the events with the following code ```JitsiMeetJS.events.<event_type>.<event_name>```.
    For example if you want to use the conference event that is fired when somebody leave conference you can use the following code - ```JitsiMeetJS.events.conference.USER_LEFT```.
    We support the following events:
    1. conference
        - TRACK_ADDED - stream received. (parameters - JitsiTrack)
        - TRACK_REMOVED - stream removed. (parameters - JitsiTrack)
        - TRACK_MUTE_CHANGED - JitsiTrack was muted or unmuted. (parameters - JitsiTrack)
        - DOMINANT_SPEAKER_CHANGED - the dominant speaker is changed. (parameters - id(string))
        - USER_JOINED - new user joined a conference. (parameters - id(string), user(JitsiParticipant))
        - USER_LEFT - a participant left conference. (parameters - id(string), user(JitsiParticipant))
        - MESSAGE_RECEIVED - new text message received. (parameters - id(string), text(string), ts(number))
        - DISPLAY_NAME_CHANGED - user has changed his display name. (parameters - id(string), displayName(string))
        - LAST_N_ENDPOINTS_CHANGED - last n set was changed (parameters - array of ids of users)
        - IN_LAST_N_CHANGED - passes boolean property that shows whether the local user is included in last n set of any other user or not. (parameters - boolean)
        - CONFERENCE_JOINED - notifies the local user that he joined the conference successfully. (no parameters)
        - CONFERENCE_LEFT - notifies the local user that he left the conference successfully. (no parameters)
        - DTMF_SUPPORT_CHANGED - notifies if at least one user supports DTMF. (parameters - supports(boolean))
        - USER_ROLE_CHANGED - notifies that role of some user changed. (parameters - id(string), role(string))
        - USER_STATUS_CHANGED - notifies that status of some user changed. (parameters - id(string), status(string))
        - CONFERENCE_FAILED - notifies that user failed to join the conference. (parameters - errorCode(JitsiMeetJS.errors.conference))
        - KICKED - notifies that user has been kicked from the conference.
        - START_MUTED_POLICY_CHANGED - notifies that all new participants will join with muted audio/video stream (parameters - JS object with 2 properties - audio(boolean), video(boolean))
        - STARTED_MUTED - notifies that the local user has started muted
        - FIREFOX_EXTENSION_NEEDED - notifies that browser extension must be installed to proceed with screen sharing (parameters - extension url(string))
        - AVAILABLE_DEVICES_CHANGED - notifies that available participant devices changed (camera or microphone was added or removed) (parameters - id(string), devices(JS object with 2 properties - audio(boolean), video(boolean)))

    2. connection
        - CONNECTION_FAILED - indicates that the server connection failed.
        - CONNECTION_ESTABLISHED - indicates that we have successfully established server connection.
        - CONNECTION_DISCONNECTED - indicates that we are disconnected.
        - WRONG_STATE - indicates that the user has performed action that can't be executed because the connection is in wrong state.

* ```JitsiMeetJS.errors``` - JS object that contains all errors used by the API. You can use that object to check the reported errors from the API
    We have two error types - connection and conference. You can access the events with the following code ```JitsiMeetJS.errors.<error_type>.<error_name>```.
    For example if you want to use the conference event that is fired when somebody leave conference you can use the following code - ```JitsiMeetJS.errors.conference.PASSWORD_REQUIRED```.
    We support the following events:
    1. conference
        - CONNECTION_ERROR - the connection with the conference is lost.
        - SETUP_FAILED - conference setup failed
        - AUTHENTICATION_REQUIRED - user must be authenticated to create this conference
        - PASSWORD_REQUIRED - that error can be passed when the connection to the conference failed. You should try to join the conference with password.
        - PASSWORD_NOT_SUPPORTED - indicates that conference cannot be locked
        - VIDEOBRIDGE_NOT_AVAILABLE - video bridge issues.
        - RESERVATION_ERROR - error in reservation system
        - GRACEFUL_SHUTDOWN - graceful shutdown
        - JINGLE_FATAL_ERROR - error in jingle
        - CONFERENCE_DESTROYED - conference has been destroyed
        - CHAT_ERROR - chat error happened
        - FOCUS_DISCONNECTED - focus error happened
    2. connection
        - PASSWORD_REQUIRED - passed when the connection to the server failed. You should try to authenticate with password.
        - CONNECTION_ERROR - indicates connection failures.
        - OTHER_ERROR - all other errors
* ```JitsiMeetJS.logLevels``` - object with the log levels:
    1. TRACE
    2. DEBUG
    3. INFO
    4. LOG
    5. WARN
    6. ERROR

JitsiConnection
------------
This objects represents the server connection. You can create new ```JitsiConnection``` object with the constructor ```JitsiMeetJS.JitsiConnection```. ```JitsiConnection``` has the following methods:


1. ```JitsiConnection(appID, token, options)``` - constructor. Creates the conference object.

    - appID - identification for the provider of Jitsi Meet video conferencing services. **NOTE: not implemented yet. You can safely pass ```null```**
    - token - secret generated by the provider of Jitsi Meet video conferencing services. The token will be send to the provider from the Jitsi Meet server deployment for authorization of the current client. **NOTE: not implemented yet. You can safely pass ```null```**
    - options - JS object with configuration options for the server connection. You can change the following properties there:
        1. bosh -
        2. hosts - JS Object
            - domain
            - muc
            - bridge
            - anonymousdomain
        3. useStunTurn -

2. connect(options) - establish server connection
    - options - JS Object with ```id``` and ```password``` properties.

3. disconnect() - destroys the server connection

4. initJitsiConference(name, options) - creates new ```JitsiConference``` object.
    - name - the name of the conference
    - options - JS object with configuration options for the conference. You can change the following properties there:
        1. openSctp - boolean property. Enables/disables datachannel support. **NOTE: we recommend to set that option to true**
        2. recordingType - the type of recording to be used
        3. jirecon
        4. callStatsID - callstats credentials
        5. callStatsSecret - callstats credentials
        6. disableThirdPartyRequests - if true - callstats will be disabled and
        the callstats API won't be included.
        **NOTE: if 4 and 5 are set the library is going to send events to callstats. Otherwise the callstats integration will be disabled.**

5. addEventListener(event, listener) - Subscribes the passed listener to the event.
    - event - one of the events from ```JitsiMeetJS.events.connection``` object.
    - listener - handler for the event.

6. removeEventListener(event, listener) - Removes event listener.
    - event - the event
    - listener - the listener that will be removed.

JitsiConference
-----------
The object represents a conference. We have the following methods to control the conference:


1. join(password) - Joins the conference
    - password - string of the password. This parameter is not mandatory.

2. leave() - leaves the conference

4. getLocalTracks() - Returns array with JitsiTrack objects for the local streams.

5. addEventListener(event, listener) - Subscribes the passed listener to the event.
    - event - one of the events from ```JitsiMeetJS.events.conference``` object.
    - listener - handler for the event.

6. removeEventListener(event, listener) - Removes event listener.
    - event - the event
    - listener - the listener that will be removed.

7. on(event, listener) - alias for addEventListener

8. off(event, listener) - alias for removeEventListener

9. sendTextMessage(text) - sends the given string to other participants in the conference.

10. setDisplayName(name) - changes the display name of the local participant.
    - name - the new display name

11. selectParticipant(participantID) - Elects the participant with the given id to be the selected participant or the speaker. You should use that method if you are using simulcast.


12. sendCommand(name, values) - sends user defined system command to the other participants
    - name - the name of the command.
    - values - JS object. The object has the following structure:


        ```
            {


                value: the_value_of_the_command,


                attributes: {},// map with keys the name of the attribute and values - the values of the attributes.


                children: [] // array with JS object with the same structure.
            }
        ```


    NOTE: When you use that method the passed object will be added in every system message that is sent to the other participants. It might be sent more than once.


13. sendCommandOnce(name, values) - Sends only one time a user defined system command to the other participants


14. removeCommand(name) - removes a command for the list of the commands that are sent to the ther participants
    - name - the name of the command

15. addCommandListener(command, handler) - adds listener
    - command - string for the name of the command
    - handler(values) - the listener that will be called when a command is received from another participant.

16. removeCommandListener(command) - removes the listeners for the specified command
    - command - the name of the command

17. addTrack(track) - Adds JitsiLocalTrack object to the conference.
    - track - the JitsiLocalTrack

18. removeTrack(track) - Removes JitsiLocalTrack object to the conference.
    - track - the JitsiLocalTrack

19. isDTMFSupported() - Check if at least one user supports DTMF.

20. getRole() - returns string with the local user role ("moderator" or "none")

21. isModerator() - checks if local user has "moderator" role

22. lock(password) - set password for the conference; returns Promise
    - password - string password

    Note: available only for moderator

23. unlock() - unset conference password; returns Promise

    Note: available only for moderator

24. kick(id) - Kick participant from the conference
    - id - string participant id

25. setStartMutedPolicy(policy) - make all new participants join with muted audio/video
    - policy - JS object with following properties
        - audio - boolean if audio stream should be muted
        - video - boolean if video stream should be muted

    Note: available only for moderator

26. getStartMutedPolicy() - returns the current policy with JS object:
    - policy - JS object with following properties
        - audio - boolean if audio stream should be muted
        - video - boolean if video stream should be muted

27. isStartAudioMuted() - check if audio is muted on join

28. isStartVideoMuted() - check if video is muted on join

29. sendFeedback(overallFeedback, detailedFeedback) - Sends the given feedback through CallStats if enabled.
    - overallFeedback an integer between 1 and 5 indicating the user feedback
    - detailedFeedback detailed feedback from the user. Not yet used

JitsiTrack
======
The object represents single track - video or audio. They can be remote tracks ( from the other participants in the call) or local tracks (from the devices of the local participant).
We have the following methods for controling the tracks:

1. getType() - returns string with the type of the track( "video" for the video tracks and "audio" for the audio tracks)


2. mute() - mutes the track.

   Note: This method is implemented only for the local tracks.


3. unmute() - unmutes the track.

   Note: This method is implemented only for the local tracks.

4. isMuted() - check if track is muted

5. attach(container) - attaches the track to the given container.

6. detach(container) - removes the track from the container.

7. stop() - stop sending the track to the other participants in the conference.

   Note: This method is implemented only for the local tracks.

8. getId() - returns unique string for the track.

9. getParticipantId() - returns id(string) of the track owner

   Note: This method is implemented only for the remote tracks.


Getting Started
==============

1. The first thing you must do in order to use Jitsi Meet API is to initialize ```JitsiMeetJS``` object:

```javascript
JitsiMeetJS.init();
```

2. Then you must create the connection object:


```javascript
var connection = new JitsiMeetJS.JitsiConnection(null, null, options);
```


3. Now we can attach some listeners to the connection object and establish the server connection:

```javascript
connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_ESTABLISHED, onConnectionSuccess);
connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_FAILED, onConnectionFailed);
connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_DISCONNECTED, disconnect);

connection.connect();
```

4. After you receive the ```CONNECTION_ESTABLISHED``` event you are to create the ```JitsiConference``` object and
also you may want to attach listeners for conference events (we are going to add handlers for remote track, conference joined, etc. ):


```javascript

room = connection.initJitsiConference("conference1", confOptions);
room.on(JitsiMeetJS.events.conference.TRACK_ADDED, onRemoteTrack);
room.on(JitsiMeetJS.events.conference.CONFERENCE_JOINED, onConferenceJoined);
```

5. You also may want to get your local tracks from the camera and microphone:
```javascript
JitsiMeetJS.createLocalTracks().then(onLocalTracks);
```

NOTE: Adding listeners and creating local streams are not mandatory steps.

6. Then you are ready to create / join a conference :

```javascript
room.join();
```

After that step you are in the conference. Now you can continue with adding some code that will handle the events and manage the conference.