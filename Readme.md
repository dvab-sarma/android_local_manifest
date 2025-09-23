### Device specific configuration to build AOSP Android 16 for Orange Pi 5 series boards with rk3588s and Orange Pi 3b v2.1(rk3566s)

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
repo init -u https://android.googlesource.com/platform/manifest -b android-16.0.0_r1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0/manifest_rk_opi.xml --create-dirs
```

Or optionally, you can reduce download size by creating a shallow clone and removing unneeded projects:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-16.0.0_r1 --depth=1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0/manifest_rk_opi.xml --create-dirs
curl -o .repo/local_manifests/remove_projects.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-16.0/remove_projects.xml
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
lunch aosp_opi5_pro-bp2a-userdebug
```
```
lunch aosp_opi5_pro_tv-bp2a-userdebug
```
```
lunch aosp_opi5_pro_car-bp2a-userdebug
```

for (`opi 3b`): 
```
lunch aosp_opi3b-bp2a-userdebug
```
```
lunch aosp_opi3b_tv-bp2a-userdebug
```
```
lunch aosp_opi3b_car-bp2a-userdebug
```

7. Compile:

```
make bootimage systemimage vendorimage -j$(nproc)
```

8. Make flashable image for the device
 (`opi5_pro`): 

```
./opi5_pro-mkimg.sh
```
(`opi3b`): 
```
./opi3b-mkimg.sh
```

9. ONLY FOR Orangepi 3b boards - 

The current build has a bug where it causes kernel panic in opi 3b boards after burning the image. It is necessary to follow these steps to get rid of the kernel panic - 

Solution 1 - 
a. after generating the .img file using `./opi3b-mkimg.sh` command, execute this command to mount the .img with write access - 
```
gnome-disk-image-mounter --writable OrangePiAOSP-20250730-opi3b.img
```
Note - replace `OrangePiAOSP-20250730-opi3b.img` with the latest generated image.

b. after mounting it as writable image, open the boot partition of the .img in file explorer and copy and paste the `Image` file (kernel image) from `device/opi/opi3b-kernel` into the boot partition.

c. safely eject the .img file and burn the image onto an sd card and boot the board.

Solution 2 -
a. If solution 1 doesn't work, burn the .img file and insert the sd card in pc.
b. open the boot partition of the sd card, manually copy and paste the `Image` file (kernel image) from `device/opi/opi3b-kernel` into the boot partition.
c. Safely eject the SD card and power on the board with sd card in it.


Also look into [Linux kernel build instructions](https://github.com/dvab-sarma/android_kernel_manifest/tree/android-16.0).

The kernel version is 6.15, which is not the recommended kernel for android 15 or 16. It should work fine since most of the patches for rk3588 were upstreamed to kernel version 6.14 and the recommended kernel 6.12 for android 16 was missing the patches. 

The rockchip drm was patched in this kernel attached in this github.  So, it is recommended to use the kernel in this github.
***

### Issues:
- Camera doesn't work, since the mainline devicetree doesn't have the camera. Will be fixed in the future.
- HDMI audio and 3.5 mm port doesn't work.
- HDMI port near to the mic doesn't work.
- USB 3.0 doesn't work.
- [Android](https://github.com/dvab-sarma/android_local_manifest/issues)
- [Linux kernel](https://github.com/dvab-sarma/android_kernel_manifest/issues)

***
### Prebuilt Images
- You can download the android 15 / 16 prebuilt images for various boards from this google drive link. [Here](https://drive.google.com/drive/folders/1d5ifTQ6-efLuzAD8wl57woRwLUpaYbc-)

***

### Wiki:

- [Mesa3D](https://github.com/dvab-sarma/android_local_manifest/wiki/Mesa3D)


### TODO:
- Camera drivers need to be developed for rockchip based devices.
- Audio needs to be configured properly.

***
**Credits:**
- The android userspace code is based on KonstaKang's raspberry-vanilla aosp project. A huge thanks to KonstaKang and raspberry-vanilla team.
- A huge thanks to Masayuki Araki ([Misaka](https://github.com/misakazip)) for his contribution in developing and testing  this build for Orange Pi 5.
