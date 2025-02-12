# Nvidia Shield TV Controller
This project is what I used to consildate all of my TV remotes down to just the Nvidia Shield TV Remote. I have an `Nvidia Shield TV Pro (2019)` connected to my LG TV via HDMI 1, a `Playstation 4` on HDMI 2, and a `Nintendo Switch` on HDMI 3. With this application (3 applications) installed on my tv, I can select each application `Nvidia Shield TV Pro`, `Playstation 4`, or `Nintendo Switch` and it will automatically switch to the associated input for the given device. I then can press on the Netflix button to return to the shield at any time. The Netflix button works like normal, while the Nvidia Shield is the selected input.

## Use:
Once set up, this will create app icons on the Nvidia Shield to control whatever inputs you'd like on your TV. You could also use this to create app icons to perform any action and run anything. It should be easily extensible and ultra and simple lightweight application with the support of intercepting key press events from controllers.
This simplicity is intentional to ensure that the security and validity of the accessibility service is easy to follow and doesn't harbor anything malicious. (I'm generally paranoid about any app that requests accessibility permissions, since they grant such broad control of a device)

From here on out, I will assume you are on an Nvidia Shield TV. Other Android TV setup steps may vary slightly, but likely will work as well.

Currently, it is set up so that pressing the `Netflix` button when the shield input isn't selected, will select the shield input.
When the Nvidia Shield input is selected, pressing the `Netflix` button will perform the normal action (default launches Netflix).

If for some reason you become stuck and pressing the Netflix button doesn't take you back (e.g. internal state got messed up) just press and hold on the `Netflix` button for 2 seconds or more and then release, and it will take you back to the Nvidia Shield input.

## Screenshots:

![Home Screen Shortcuts](./screenshots/home_screen_shortcuts.webp?raw=true "Home Screen Shortcuts")

![All Apps](./screenshots/all_apps.webp?raw=true "All Apps")


## Setup:

Configuration:

1. Turn on LG IP Control Mode: 

    To enable IP Control Mode, turn on the LG TV.
    Hold down on the settings button (gear icon) on your remote for over 5 seconds. In the top left-hand corner, the channel information should appear.
    Quickly press the key combination 828[OK] on the remote. If that doesn't work, try navigating to "Network" or "Connections" and pressing 82888. I read that [here](https://github.com/WesSouza/lgtv-ip-control#setting-up-the-tv).
    A dialog should appear titled IP CONTROL SETUP
    Set the Network IP Control option to `ON`
    Below this, you will see the TV IP address. You can use this for step 2.

2. Set config values:

    Edit the `app/build.gradle` configuration values to match your specific inputs. The Nvidia Shield is the main controller, so it contains the accessibility service and must be included. All others are optional.
    If you have an LG TV, primarily this just means entering the correct input values to match the given input for each device. 

    Options are:
        
    ```
    [dtv / atv / cadtv / catv / avav1 / component1 / hdmi1 / hdmi2 / hdmi3 / hdmi4]
    ```

    You'll also want to enter the IP address of your LG TV. This should probably be set to a static value on your router to prevent it from changing.

3. Enable developer mode on the Nvidia Shield TV:

    Open the following menu tree:
    Settings → Device Preferences → About → Build
    Click on `Build` 8 times consecutively. 
    Developer mode should be enabled

    Open the following menu tree:
    
    Settings → Device Preferences → Developer options (at the bottom)
    
    Enable the `Network debugging` option.
    
    Once enabled, the IP address should appear as an option.
 
4. Download and Install Android Studio 

5. Run the `NvidiaShieldTVController` project for each *Debug* variant to install

    Open the`NvidiaShieldTVController` project with android studio
    At some point, when Android Studio loads, it hopefully will detect the Nvidia Shield TV. When an option to enable debugging appears, allow your computer.
    Once the project loads, select the variants you want on the TV and run each one:
    
    In the bottom left corner of the Android Studio window is a build variants drawer.
    Select this and using the drop-down you can pick each variant. Likely you'll want to run the Debug variant.
    Release variant requires a signing config file, which you'd have to create. It's something for distributing versions of the app.
    Then, at the top of Android Studio, there should be a green run button.
    Run that. When complete, it should flash on your TV.
    If a different input will be selected for a given variant, when you run it from Android Studio, the TV input should also change.

6. Enable Accessibility service:

    You must enable the accessibility service associated with the Shield variant app for the Netflix button to work and bring you back to the shield when on another input. See `Use` section at the top of the file for more details (Default name: "Nvidia Shield")
    
    Open the following menu tree:
    
    Settings → Device Preferences → Accessibility → Shield TV Control
    
    Use Shield TV Control? OK
    
7. Disable Developer Options when everything is working

    You don't need to leave developer options enabled once you have everything working. To disable them once you have everything working and tested, follow these steps:
    
    Open the following menu tree:

    Settings → Device Preferences → Developer options (at the bottom)
    
    Turn off the Developer Options setting. Leave the developer options menu, and they should no longer be visible at the bottom of the menu. You should be permanently good to go.


Note:
    One of the reasons I made and am open sourcing this project is because I generally distrust any application with accessibility privileges enabled. So this allows any individual to run this setup, but to also audit the code. The accessibility service code can be found in {NvidiaShieldTVController/app/src/shield/java/com/ashbreeze/shield_tv_controller/NavButtonService.kt}.
    This project also allows me to have slightly more intelligent controls between the inputs than a normal shortcut remapper would allow. Also, I ensured that the remote functions entirely normally with this enabled. The Netflix button even works like normal when on the shield. 
    When I first added the remapping accessibility service, it changed how the regular controls worked in strange ways. It took some experimentation to find the combination of flags that would allow everything to function normally.  This from what I could tell has 0 affect, but it's also why I chose to remap just the Netflix button. The other buttons were harder to try to replicate their functionality (e.g. home button)
    Originally, I tried to do multiple buttons, but realized getting their functionality to match perfectly wasn't straight forward. launching Netflix however, was straight forward :)

Another thing to mention about secruity:
    I highly recommend you fully trust your network security before doing this, since the television is now open to being logged into by anyone on your local network. Who knows what vulnerabilities that could expose.


## Notes / Debugging
You should be able to telnet into the TV and change inputs manually
If you've enabled the IP Control Mode, you should be able to do some simple debugging to test things before getting everything else going. Just telnet into the TV:
```
telnet ${TV_IP_ADDRESS} 9761
```
Then run the following command to select hdmi1
```
INPUT_SELECT hdmi1
```
`NG` means failure, `OK` means success

Other input options:

```
[dtv / atv / cadtv / catv / avav1 / component1 / hdmi1 / hdmi2 / hdmi3 / hdmi4]
```


Feel free to reach out to me if you have any questions, I haven't really productionized this as I don't feel confident with these steps that it's really a truly consumer-friendly experience, but I believe something along these lines would be the future if all the TV companies could get their shit together and decide on a network protocol for all the TVs and devices to communicate. If that were to happen, this device could do this without any of these shenanigans.

## Support:

Obviously, I don't offer any guarantees of any kind around this project, but I'm very happy to help as much as possible. If you encounter a problem, or would like help to set this up with something other than an LG TV (e.g. onkyo receiver or Samsung TV) feel free to reach out to open a new issue on GitHub, and I'd be happy to work with you to get this setup and working for whatever devices you have at home.


## Credit:

I loosely referenced several projects when making this, so always nice to see the breadcrumbs.

For the accessibility service, I used this:

https://github.com/shuhaowu/NavButtonRemap

The primary issue I encountered with this was one of the lines in the service_config.xml

`android:accessibilityFeedbackType="feedbackGeneric"`

With this flag enabled, whenever I would do special actions like try to move an app icon (Long press → move), it would display a visual on-screen display of some sort with visible controls, but it also just wouldn't function correctly with the remote. Materially, the icons wouldn't move with the arrow keys like normal. Once I removed this, that issue went away.


For the telnet service, I found this as a starting point:
https://github.com/hkdsun/Android-Telnet-Client
