# Chrome issues

## WebGL

Run `chrome://gpu` and see if WebGL is disabled, and see Problem Detected section.

Run `chrome://version` see  current run flags.

### Flatpak installation

If

```shell
flatpak run com.google.Chrome --ozone-platform=x11
```

works, then set the env

```shell
flatpak override --user com.google.Chrome --env=OZONE_PLATFORM=x11
```

or disable Vulkan

```shell
flatpak override --user com.google.Chrome --env=CHROME_DISABLE_VULKAN=1
```
