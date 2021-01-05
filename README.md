# How to compile patched `i915` driver
Patched `i915` driver fixes [this][bug] issue, you can download it [here][releases]
## Download kernel sources
```bash
git clone https://github.com/torvalds/linux
cd linux
git checkout 2c85ebc # Linux 5.10
# also install linux-headers w/ your respective package manager
cp /usr/lib/modules/$(uname -r)/build/.config ./
cp /usr/lib/modules/$(uname -r)/build/Module.symvers ./
```

## Patch driver
```bash
curl https://patchwork.freedesktop.org/patch/395580/raw/ --output drm-i915-gt-Limit-VFE-threads-based-on-GT.patch
patch -p1 < drm-i915-gt-Limit-VFE-threads-based-on-GT.patch
```

## Compile driver
```bash
make olddefconfig
make -j$(getconf _NPROCESSORS_ONLN) modules_prepare
make -j$(getconf _NPROCESSORS_ONLN) M=./drivers/gpu/drm/i915 modules
# output file: linux/drivers/gpu/drm/i915/i915.ko
```

### Checksums:
|Kernel|SHA1                                    |
|:----:|:--------------------------------------:|
|5.9.0 |f738ec02bf108cf124b30f31668909d1052ba034|
|5.10.0|593b14dc3c08a10057d1568c3f0b59da4b7f6edc|

## Install driver:
```bash
sudo mkdir -p /lib/modules/$(uname -r)/extramodules/gpu/drm/i915
cp i915.ko /lib/modules/$(uname -r)/extramodules/gpu/drm/i915
echo "search extradrivers" | sudo tee /etc/depmod.d/00-extra.conf
sudo depmod && sudo mkinitcpio -P
```

<!--Links-->
[bug]: https://bugzilla.redhat.com/show_bug.cgi?id=1843274
[releases]: https://github.com/jeffangelion/i915-vfe/releases
