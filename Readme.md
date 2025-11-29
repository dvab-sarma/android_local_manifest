### Device specific configuration to build AOSP Android 16 for Orange Pi 5 series boards with rk3588s

***

### How to build (Ubuntu 24.04 LTS):

1. Establish [Android build environment](https://source.android.com/docs/setup/start/requirements).

2. Make sure to run the below commands to install some dependencies

```
sudo apt-get install dosfstools e2fsprogs fdisk kpartx mtools rsync
sudo pip3 install meson mako jinja2 ply pyyaml dataclasses
```

3. Initialize repo:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-16.0.0_r3
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0_r3/manifest_rk_opi.xml --create-dirs
```

Or optionally, you can reduce download size by creating a shallow clone and removing unneeded projects:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-16.0.0_r3 --depth=1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0_r3/manifest_rk_opi.xml --create-dirs
curl -o .repo/local_manifests/remove_projects.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0_r3/remove_projects.xml
```

4. Sync source code:

```
repo sync -j$(nproc)
```

5. Setup Android build environment:

```
. build/envsetup.sh
```

6. Select the device (`opi5_pro` ) and build target (tablet UI, `tv` for Android TV, or `car` for Android Automotive):


```
lunch aosp_opi5_pro-bp3a-userdebug
```
```
lunch aosp_opi5_pro_tv-bp3a-userdebug
```
```
lunch aosp_opi5_pro_car-bp3a-userdebug
```


7. Compile:

```
make bootimage systemimage vendorimage -j$(nproc)
```

8. Make flashable image for the device
 (`opi5 series boards`): 

```
./opi5_pro-mkimg.sh
```
***
### UPDATE FOR RK3566 (Orange pi 3b v2.1)

- Currently, this update supports for Rockchip's rk3588 SoC, Would be releasing for rk3566 in near future. 

***


Also look into [Linux kernel build instructions](https://github.com/dvab-sarma/android_kernel_manifest/tree/android-16.0).

The kernel version is 6.15, which is not the recommended kernel for android 15 or 16. It should work fine since most of the patches for rk3588 were upstreamed to kernel version 6.14 and the recommended kernel 6.12 for android 16 was missing the patches. 

The rockchip drm was patched in this kernel attached in this github.  So, it is recommended to use the kernel in this github.
***

### Issues:
- Camera doesn't work, since the mainline devicetree doesn't have the camera. Will be fixed in the future.
- 3.5 mm port doesn't work.
- HDMI port near to the mic doesn't work.
- USB 3.0 doesn't work.
- [Android](https://github.com/dvab-sarma/android_local_manifest/issues)
- [Linux kernel](https://github.com/dvab-sarma/android_kernel_manifest/issues)

***
### Prebuilt Images
- You can download the android 15 / 16 prebuilt images for various boards from this google drive link. [Here](https://drive.google.com/drive/folders/1d5ifTQ6-efLuzAD8wl57woRwLUpaYbc-)

***

### Wiki:

- [Main Wiki](https://github.com/dvab-sarma/android_local_manifest/wiki)
- [Mesa3D](https://github.com/dvab-sarma/android_local_manifest/wiki/Mesa3D)



### TODO:
- Camera drivers need to be developed for rockchip based devices.
- Audio needs to be configured properly.

***
**Credits:**
- The android userspace code is based on KonstaKang's raspberry-vanilla aosp project. A huge thanks to KonstaKang and raspberry-vanilla team.
- A huge thanks to Masayuki Araki ([Misaka](https://github.com/misakazip)) for his contribution in developing and testing  this build for Orange Pi 5.

