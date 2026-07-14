### Device specific configuration to build AOSP Android 17 for Orange Pi 5 series boards with rk3588s

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
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/manifest_rk_opi.xml --create-dirs
```

Or optionally, you can reduce download size by creating a shallow clone and removing unneeded projects:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1 --depth=1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/manifest_rk_opi.xml --create-dirs
curl -o .repo/local_manifests/remove_projects.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/remove_projects.xml
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
lunch aosp_opi5_pro-cp2a-userdebug
```
```
lunch aosp_opi5_pro_tv-cp2a-userdebug
```
```
lunch aosp_opi5_pro_car-cp2a-userdebug
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

- Currently, this Android-17 support is only for Rockchip's rk3588 SoC, Would be releasing for rk3566 in near future. For AOSP 16 on rk3566, Please use the android-16.0 branch of this project. 

***


Also look into [Linux kernel build instructions](https://github.com/dvab-sarma/android_kernel_manifest/tree/android-16.0).

The rockchip drm was patched in this kernel attached in this github.  So, it is recommended to use the kernel in this github.
***

### Issues:
- Camera doesn't work
- 3.5 mm port doesn't work.
- Ethernet port isn't working.
- [Android](https://github.com/dvab-sarma/android_local_manifest/issues)
- [Linux kernel](https://github.com/dvab-sarma/android_kernel_manifest/issues)

***
### Prebuilt Images
- You can download the android 15 / 16 / 17 prebuilt images for various boards from this google drive link. [Here](https://drive.google.com/drive/folders/1d5ifTQ6-efLuzAD8wl57woRwLUpaYbc-)

***

### Wiki:

- [Main Wiki](https://github.com/dvab-sarma/android_local_manifest/wiki)
- [Mesa3D](https://github.com/dvab-sarma/android_local_manifest/wiki/Mesa3D)



### TODO:
- Camera drivers need to be developed for rockchip based devices.

***
**Credits:**
- The android userspace code is based on KonstaKang's raspberry-vanilla aosp project. A huge thanks to KonstaKang and raspberry-vanilla team.
- A huge thanks to Masayuki Araki ([Misaka](https://github.com/misakazip)) for his contribution in developing and testing  this build for Orange Pi 5.

