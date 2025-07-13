# IFW-Android-Mass
How to use **IFW (Intent Firewall)** to block specific activities without breaking app functionality. Boot script and instructions to mass produce. All users listed are from **[forums.jtechforums.org](forums.jtechforums.org)**

**SCROLL TO THE BOTTOM FOR BOOT SCRIPT - CREDIT @chatzie**

> **Intent Firewall (Quoted from below app developers page**  
> Intent Firewall was introduced in Android 4.4.2 (API 19) and is still effective in the latest Android systems. It is integrated into the Android Framework to filter the intents sent by applications or systems.  
>  
> Intent Firewall, indeed it is a firewall, it has no impact on component status. The application detects the component is on, but it just cannot start the component.


Thanks **[@TripleU](https://github.com/TripleU613)** for mentioning IFW recently, I looked into it because of that.

Stop trying to modify apps, do this instead.

This guide will teach you how to block specific activities using **IFW (Intent Firewall)** by either using a root app, or building it into a rom. The app is helpful regardless for creating the files you'll need.

**For example, the Google Meet tab in gmail:**
*VIDEO*
https://drive.google.com/file/d/1IBNZ7Vfl8_R4S5OYk_NqVMj4qwTBPfWt/view?usp=drive_link

As you can see, the meet tab is disabled without affecting the rest of the gmail app. The meet tab activity name is
```com.google.android.libraries.communications.conference.ui.largescreen.HomeActivity```
which pm commands will not work on.

**Download this app**

[lihenggui/blocker: Utilize an integrated firewall to manage application components.](https://github.com/lihenggui/blocker)

Open it, search for your app and find the activities you want to block. Toggle them off. They are now blocked.

If you want to build it into a rom (script to place it and give correct ownership and permissions on bottom of this page), go to the settings page from the top right (3 dots), choose a backup folder and export your configuration. Your output will be xml files.

**Example XML file**
```
# Google Meet tab in Gmail - com.google.android.gm.xml

<rules>
   <activity block="true" log="false">
      <component-filter name="com.google.android.gm/com.google.android.libraries.communications.conference.ui.largescreen.HomeActivity" />
   </activity>
   <broadcast block="true" log="false" />
   <service block="true" log="false" />
</rules>
```

This needs to be placed in ```/data/system/ifw``` with correct ownership and permissions.

***Bonus:*** This system of blocking with IFW can block Google Maps on a phone but allow it to to work with Android Auto.

# BOOT SCRIPT (NO ROOT NECESSARY)
```bash
# /system/etc/init/ifw.rc

on post-fs-data

# Remove any existing IFW directory or symlink (safe: won't affect user data)
    exec -- /system/bin/rm -rf /data/system/ifw

# Create a symlink from system IFW rules to expected location
    symlink /system/etc/ifw /data/system/ifw

# Set permissions and restore SELinux context for system_server access
    exec -- /system/bin/sh -c 'chmod 0644 /system/etc/ifw/*.xml || true'
    restorecon -R /system/etc/ifw
    restorecon -R /data/system/ifw
```

Put the rules (xml files) in **```/system/etc/ifw```**

Build super and reflash, no need to erase userdata.
