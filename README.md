# Vulkan Device Chooser Layer

This is a quick and dirty implementation of a Vulkan layer to force a specific physical device to be used. This is useful for Vulkan games which do not provide an option to choose the device themselves.

Compiling requires `vulkan/vulkan.h`, `vulkan/vk_layer.h` and `vulkan/vk_layer_dispatch_table.h`.
On Debian based systems, you can install these with:
```bash
sudo apt-get install libvulkan-dev vulkan-validationlayers-dev
```
On Fedora, you can install these with:
```bash
sudo dnf install vulkan-headers vulkan-validation-layers-devel
```

Build and install with:
```bash
meson builddir --prefix=/usr
meson compile -C builddir
sudo meson install -C builddir
```

This will install to the system's Vulkan layer directory, `/usr/share/vulkan/implicit_layer.d/`.

To run a Vulkan application forcing a specific device to be used, launch it with these environment variables:
```
ENABLE_DEVICE_CHOOSER_LAYER=1 VULKAN_DEVICE_INDEX=<device index>
```
Replace `<device index>` with the "GPU id" for the desired device as reported by `vulkaninfo` (without the layer enabled).

For example:
```bash
$ ENABLE_DEVICE_CHOOSER_LAYER=1 VULKAN_DEVICE_INDEX=1 vulkaninfo
```
should give info for the device which had GPU id 1 when running `vulkaninfo` without the environment variable set.

The layer can be used with Steam games by setting their launch options to:
```
ENABLE_DEVICE_CHOOSER_LAYER=1 VULKAN_DEVICE_INDEX=<device index> %command%
```

If you prefer not to use indexes, you can set VULKAN_DEVICE_INDEX to `name:<GPU name fragment>`, where <GPU name fragment> is a part of the gpu name. This will make the program use the last gpu that has a name that contains <GPU name fragment>.
For example, if you have a gpu named `AMD Radeon RX 5700 XT`, and set VULKAN_DEVICE_INDEX to `name:5700 XT`, then this gpu will be selected among others.

You can also set VULKAN_DEVICE_INDEX to `letmechoose` and a small screen will allow you to choose the gpu to use from a list.

Some games create a lot of processes that have vulkan environment, so they may ask repeatedly what gpu to use, to prevent being asked too much, you can also set VULKAN_DEVICE_INDEX to `letmechoose:<some name>` and all vulkan instances will use the same device after you choose once, the setting will be save to `/tmp/vkdevicechooser/<some name>`.