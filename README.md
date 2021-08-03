# False lint MissingPermission when Platform 31 MissingPermission installed

Bug report : https://issuetracker.google.com/issues/195334540

## Given

The current project has :
```
    classpath "com.android.tools.build:gradle:4.2.2"

    ...
    compileSdk 30

    defaultConfig {
        minSdk 23
        targetSdk 30
    }
```

The following code :
```
    private fun isBluetoothHeadsetConnected(): Boolean {
        val mBluetoothAdapter = BluetoothAdapter.getDefaultAdapter()
        return mBluetoothAdapter != null && mBluetoothAdapter.isEnabled
                && mBluetoothAdapter.getProfileConnectionState(BluetoothHeadset.HEADSET) == BluetoothHeadset.STATE_CONNECTED;
    }
```

Requires the permission `<uses-permission android:name="android.permission.BLUETOOTH" />` which is available since a low API.



# Problem

When running `app:lint`, the following error occurs :
```
MainActivity.kt:17: Error: Missing permissions required by BluetoothAdapter.getProfileConnectionState: android.permission.BLUETOOTH_CONNECT [MissingPermission]
                  && mBluetoothAdapter.getProfileConnectionState(BluetoothHeadset.HEADSET) == BluetoothHeadset.STATE_CONNECTED;
                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

But `android.permission.BLUETOOTH_CONNECT` has been added in API 31.

# Explanation

This error occurs only when :

- AGP is in version < 7.0.0
- Platform-31 (Android 12 Preview) is installed 

Using AGP 7.0, or uninstalling Platform 31 will make the task app:lint works without issue.

This is a false positive as the MissingPermission shouldn't be an issue as we are compiling/targeting against API 30 (while the permission has been added in API 31)

