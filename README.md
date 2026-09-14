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
```
