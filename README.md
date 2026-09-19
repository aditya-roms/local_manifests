# Local Manifests for Redmi Note 14 5G (beryl)

This repository contains the local manifest and setup instructions to build Android 16 (LineageOS / crDroid) for the **Redmi Note 14 5G (`beryl`)**.

---

## Method 1: Using Local Manifest (Recommended)

In your ROM source root directory, run:

```bash
# Clone local manifest
git clone https://github.com/aditya-roms/local_manifests.git -b main .repo/local_manifests

# Sync all device repositories
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
```

---

## Method 2: Manual Git Clone

If you prefer to clone the required repositories manually without using local manifests, run the following commands from your ROM source root:

```bash
# 1. Device Tree
git clone https://github.com/aditya-roms/device_xiaomi_beryl.git -b lineage-23.2-6.12 device/xiaomi/beryl

# 2. Kernel & Prebuilt Modules
git clone https://github.com/aditya-roms/device_xiaomi_beryl-kernel.git -b lineage-23.2-6.12 device/xiaomi/beryl-kernel

# 3. Proprietary Vendor Blobs
git clone https://github.com/aditya-roms/vendor_xiaomi_beryl.git -b lineage-23.2-6.12 vendor/xiaomi/beryl

# 4. MediaTek Sepolicy Vendor
git clone https://github.com/aditya-roms/device_mediatek_sepolicy_vndr.git -b 16.2-rebase device/mediatek/sepolicy_vndr

# 5. MediaTek IMS
git clone https://github.com/techyminati/android_vendor_mediatek_ims.git -b android-16-qpr2 vendor/mediatek/ims

# 6. Hardware Dependencies
git clone https://github.com/aditya-roms/hardware_xiaomi.git -b 16.2-rebase hardware/xiaomi
git clone https://github.com/aditya-roms/hardware_mediatek.git -b 16.2-rebase hardware/mediatek

# 7. FM Radio App (with 64-bit MTK fix)
git clone https://github.com/aditya-roms/packages_apps_RevampedFMRadio.git -b mtk packages/apps/RevampedFMRadio
```

---

## 🚀 Building the ROM

Once the sources are synced:

```bash
# Set up build environment
source build/envsetup.sh

# Lunch target (Android 16 3-part syntax)
lunch lineage_beryl-bp4a-user        # for user build (official)
# or:
lunch lineage_beryl-bp4a-userdebug   # for userdebug build

# Start the build
m bacon -j$(nproc --all)
```

---

## 🛠️ What to Use Instead of `lunch` (If `lunch` Doesn't Work)

If `lunch` fails, throws `Don't have a product spec for: 'lineage_beryl'`, or fails to resolve dependencies, use either of these alternatives:

### Alternative 1: Use `breakfast` (Recommended)
LineageOS / crDroid / MistOS provides the `breakfast` command which automatically discovers and configures the device:

```bash
source build/envsetup.sh
breakfast beryl user             # for official user build
# or:
breakfast beryl userdebug        # for userdebug build

m bacon -j$(nproc --all)
```

### Alternative 2: Use `brunch` (All-in-one command)
`brunch` combines `breakfast` and `m bacon` in a single command:

```bash
source build/envsetup.sh
brunch beryl user
```

### Alternative 3: Direct Environment Variables (Bypass `lunch` completely)
You can completely bypass `lunch` by exporting the build environment variables directly in your terminal:

```bash
source build/envsetup.sh

# Directly export target environment variables
export TARGET_PRODUCT=lineage_beryl
export TARGET_RELEASE=bp4a
export TARGET_BUILD_VARIANT=user      # or userdebug
export LINEAGE_BUILD=beryl

# Start compilation directly
m bacon -j$(nproc --all)
```

---

## 🔧 Troubleshooting

### Duplicate module error during `soong_build` (`protobuf_vendorcompat`)
If you encounter:
```
error: hardware/lineage/compat/Android.bp: module "prebuilt_libprotobuf-cpp-full-*-vendorcompat" already defined
       prebuilts/misc/protobuf_vendorcompat/Android.bp: <-- previous definition here
```
**Fix:** Update your device tree to the latest commit:
```bash
git -C device/xiaomi/beryl pull origin lineage-23.2-6.12
```
The device tree automatically adds `PRODUCT_SOURCE_ROOT_DIRS += -prebuilts/misc/protobuf_vendorcompat` in `device.mk`, instructing Soong to skip the conflicting AOSP prebuilts.

Alternatively, you can quickly remove the duplicate AOSP file in your source:
```bash
rm -f prebuilts/misc/protobuf_vendorcompat/Android.bp
```

### Qualcomm HAL dependency errors during `soong_build` (`snapalloc` / `libvmmem_headers`)
If you encounter:
```
error: hardware/qcom-caf/sm8750/display/core/snapalloc/Android.bp: "vendor.qti.hardware.display.snapalloc-impl" depends on undefined module "libvmmem_headers"
```
**Why it happens:** Redmi Note 14 5G (`beryl`) is a **MediaTek (MT6855)** device and does not use Qualcomm CAF hardware components. Some ROM trees contain mismatched or broken Qualcomm HAL namespaces that fail Blueprint dependency resolution.

**Fix:** Update your device tree to the latest commit:
```bash
# For Lineage / crDroid:
git -C device/xiaomi/beryl pull origin lineage-23.2-6.12

# For MistOS:
git -C device/xiaomi/beryl pull origin mistos-16.2-6.12
```
The device tree automatically adds `PRODUCT_SOURCE_ROOT_DIRS += -hardware/qcom -hardware/qcom-caf` in `device.mk`, instructing Soong to skip all unnecessary Qualcomm HALs during compilation.

Alternatively, on the build server you can remove the broken Qualcomm folders:
```bash
rm -rf hardware/qcom-caf/sm8750 hardware/qcom-caf/sm8450-6.6
```

