### Vendor GPS HAL example

<b><u><font size=3 face="Roboto" color=#1d3557>EC25 GPS</font></u></b>

using AOSP default [android.hardware.gnss@1.0-service](https://android.googlesource.com/platform/hardware/interfaces/+/refs/heads/android10-mainline-release/gnss/1.0/default/) as the server of default impl. [android.hardware.gnss@1.0-impl](https://android.googlesource.com/platform/hardware/interfaces/+/refs/heads/android10-mainline-release/gnss/1.0/default/) to provide [android.hardware.gnss@1.0](https://android.googlesource.com/platform/hardware/interfaces/+/refs/heads/android10-mainline-release/gnss/1.0) HIDL interface implementation.

HIDL Passthrough mode

<img src="https://github.com/tingkts/Andorid-GPS/blob/main/assets/GPS%20HIDL%20HAL%20HW%20-%20Passthrough%20mode.png" style="zoom:80%" />



<b><u><font size=3 face="Roboto" color=#1d3557>u-blox GPS</font></u></b>

The vendor has proprietary implementation [android.hardware.gnss@2.0-service-ubx]() as the server of [android.hardware.gnss@2.0-impl-ubx]() to provide [android.hardware.gnss@2.0](https://android.googlesource.com/platform/hardware/interfaces/+/refs/heads/android10-mainline-release/gnss/1.0) HIDL interface implementation.

HIDL Binderized mode

<img src="https://github.com/tingkts/Andorid-GPS/blob/main/assets/GPS%20HIDL%20HAL%20HW%20-%20Binderized%20mode.png" style="zoom:80%" />



<b><u><font size=3 face="Roboto" color=#1d3557>Case Study</font></u></b>
<b><font size=3 face="Roboto" color=#1d3557> : How does android system choose a HIDL service, if there is more than one service in the system ?</font></b>

Android will first choose a higher-level HIDL service/implementation.

For EC25 GPS and u-blox GPS coexistence case, there are android.hardware.gnss@1.0-service and android.hardware.gnss@2.0-service-ubx HIDL implementation coexistence, android will use 2.0 interface, in the other words, EC25 GPS will not use.


[android/frameworks/base/services/core/jni/com_android_server_location_GnssLocationProvider.cpp](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/services/core/jni/com_android_server_location_GnssLocationProvider.cpp?q=com_android_server_location_GnssLocationProvider.cpp&ss=android)
```cpp
sp<IGnss_V1_0> gnssHal = nullptr;
sp<IGnss_V1_1> gnssHal_V1_1 = nullptr;
sp<IGnss_V2_0> gnssHal_V2_0 = nullptr;

/* Initializes the GNSS service handle. */
static void android_location_GnssLocationProvider_set_gps_service_handle() {
    gnssHal_V2_0 = IGnss_V2_0::getService();
    if (gnssHal_V2_0 != nullptr) {
        gnssHal = gnssHal_V2_0;
        gnssHal_V1_1 = gnssHal_V2_0;
        return;
    }

    ALOGD("gnssHal 2.0 was null, trying 1.1");
    gnssHal_V1_1 = IGnss_V1_1::getService();
    if (gnssHal_V1_1 != nullptr) {
        gnssHal = gnssHal_V1_1;
        return;
    }

    ALOGD("gnssHal 1.1 was null, trying 1.0");
    gnssHal = IGnss_V1_0::getService();
}
```


</br>

### AOSP HIDL source

- [android/hardware/interfaces](https://android.googlesource.com/platform/hardware/interfaces/)
- [android/system/libhidl](https://android.googlesource.com/platform/system/libhidl/)
- [android/system/tools/hidl](https://android.googlesource.com/platform/system/tools/hidl/)
- [android/system/hwservicemanager/](https://android.googlesource.com/platform/system/hwservicemanager/)

</br>
</br>
</br>

<p align="right">>= android 8</p>

