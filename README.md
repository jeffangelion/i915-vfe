# How to compile patched `i915` driver
Patched `i915` driver fixes [this][bug] issue, you can download it [here][releases]
## Download kernel sources
```bash
git clone https://github.com/torvalds/linux
cd linux
git checkout bbf5c97 # Linux 5.9
pamac install linux-latest-headers
cp /usr/lib/modules$(uname -r)/build/.config ./
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
sudo make -j$(getconf _NPROCESSORS_ONLN) modules_prepare
sudo make -j$(getconf _NPROCESSORS_ONLN) M=./drivers/gpu/drm/i915 modules
```

### Checksums:
Driver |Kernel version|SHA1
:-----:|:------------:|:--------------------------------------:
i915.ko|5.9.0         |f738ec02bf108cf124b30f31668909d1052ba034

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