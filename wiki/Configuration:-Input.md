### Overview

In this section you can configure input devices like keyboard and mouse, and some input-related options.

There's a section for each device type: `keyboard`, `touchpad`, `mouse`, `trackpoint`, `tablet`, `touch`.
Settings in those sections will apply to every device of that type.
Currently, there's no way to configure specific devices individually (but that is planned).

All settings at a glance:

```kdl
input {
    keyboard {
        xkb {
            // layout "us"
            // variant "colemak_dh_ortho"
            // options "compose:ralt,ctrl:nocaps"
            // model ""
            // rules ""
            // file "~/.config/keymap.xkb"
        }

        // repeat-delay 600
        // repeat-rate 25
        // track-layout "global"
    }

    touchpad {
        // off
        tap
        // dwt
        // dwtp
        // drag false
        // drag-lock
        natural-scroll
        // accel-speed 0.2
        // accel-profile "flat"
        // scroll-factor 1.0
        // scroll-method "two-finger"
        // scroll-button 273
        // tap-button-map "left-middle-right"
        // click-method "clickfinger"
        // left-handed
        // disabled-on-external-mouse
        // middle-emulation
    }

    mouse {
        // off
        // natural-scroll
        // accel-speed 0.2
        // accel-profile "flat"
        // scroll-factor 1.0
        // scroll-method "no-scroll"
        // scroll-button 273
        // left-handed
        // middle-emulation
    }

    trackpoint {
        // off
        // natural-scroll
        // accel-speed 0.2
        // accel-profile "flat"
        // scroll-method "on-button-down"
        // scroll-button 273
        // left-handed
        // middle-emulation
    }

    trackball {
        // off
        // natural-scroll
        // accel-speed 0.2
        // accel-profile "flat"
        // scroll-method "on-button-down"
        // scroll-button 273
        // left-handed
        // middle-emulation
    }

    tablet {
        // off
        map-to-output "eDP-1"
        // left-handed
        // calibration-matrix 1.0 0.0 0.0 0.0 1.0 0.0
    }

    touch {
        // off
        map-to-output "eDP-1"
    }

    // disable-power-key-handling
    // warp-mouse-to-focus
    // focus-follows-mouse max-scroll-amount="0%"
    // workspace-auto-back-and-forth
}
```

### Keyboard

#### Layout

In the `xkb` section, you can set layout, variant, options, model and rules.
These are passed directly to libxkbcommon, which is also used by most other Wayland compositors.
See the `xkeyboard-config(7)` manual for more information.

```kdl
input {
    keyboard {
        xkb {
            layout "us"
            variant "colemak_dh_ortho"
            options "compose:ralt,ctrl:nocaps"
        }
    }
}
```

> [!TIP]
>
> <sup>Since: 25.02</sup>
>
> Alternatively, you can directly set a path to a .xkb file containing an xkb keymap.
> This overrides all other xkb settings.
>
> ```kdl
> input {
>     keyboard {
>         xkb {
>             file "~/.config/keymap.xkb"
>         }
>     }
> }
> ```

When using multiple layouts, niri can remember the current layout globally (the default) or per-window.
You can control this with the `track-layout` option.

- `global`: layout change is global for all windows.
- `window`: layout is tracked for each window individually.

```kdl
input {
    keyboard {
        track-layout "global"
    }
}
```

#### Repeat

Delay is in milliseconds before the keyboard repeat starts.
Rate is in characters per second.

```kdl
input {
    keyboard {
        repeat-delay 600
        repeat-rate 25
    }
}
```

### Pointing Devices

Most settings for the pointing devices are passed directly to libinput.
Other Wayland compositors also use libinput, so it's likely you will find the same settings there.
For flags like `tap`, omit them or comment them out to disable the setting.

A few settings are common between input devices:

- `off`: if set, no events will be sent from this device.

A few settings are common between `touchpad`, `mouse`, `trackpoint`, and `trackball`:

- `natural-scroll`: if set, inverts the scrolling direction.
- `accel-speed`: pointer acceleration speed, valid values are from `-1.0` to `1.0` where the default is `0.0`.
- `accel-profile`: can be `adaptive` (the default) or `flat` (disables pointer acceleration).
- `scroll-method`: when to generate scroll events instead of pointer motion events, can be `no-scroll`, `two-finger`, `edge`, or `on-button-down`.
  The default and supported methods vary depending on the device type.
- `scroll-button`: <sup>Since: 0.1.10</sup> the button code used for the `on-button-down` scroll method. You can find it in `libinput debug-events`.
- `left-handed`: if set, changes the device to left-handed mode.
- `middle-emulation`: emulate a middle mouse click by pressing left and right mouse buttons at once.

Settings specific to `touchpad`s:

- `tap`: tap-to-click.
- `dwt`: disable-when-typing.
- `dwtp`: disable-when-trackpointing.
- `drag`: can be `true` or `false`, controls if tap-and-drag is enabled.
- `drag-lock`: <sup>Since: 25.02</sup> if set, lifting the finger off for a short time while dragging will not drop the dragged item. See the [libinput documentation](https://wayland.freedesktop.org/libinput/doc/latest/tapping.html#tap-and-drag).
- `tap-button-map`: can be `left-right-middle` or `left-middle-right`, controls which button corresponds to a two-finger tap and a three-finger tap.
- `click-method`: can be `button-areas` or `clickfinger`, changes the [click method](https://wayland.freedesktop.org/libinput/doc/latest/clickpad-softbuttons.html).
- `disabled-on-external-mouse`: do not send events while external pointer device is plugged in.

Settings specific to `touchpad` and `mouse`:

- `scroll-factor`: <sup>Since: 0.1.10</sup> scales the scrolling speed by this value.

Settings specific to `tablet`s:

- `calibration-matrix`: <sup>Since: 25.02</sup> set to six floating point numbers to change the calibration matrix. See the [`LIBINPUT_CALIBRATION_MATRIX` documentation](https://wayland.freedesktop.org/libinput/doc/latest/device-configuration-via-udev.html) for examples.

Tablets and touchscreens are absolute pointing devices that can be mapped to a specific output like so:

```kdl
input {
    tablet {
        map-to-output "eDP-1"
    }

    touch {
        map-to-output "eDP-1"
    }
}
```

Valid output names are the same as the ones used for output configuration.

<sup>Since: 0.1.7</sup> When a tablet is not mapped to any output, it will map to the union of all connected outputs, without aspect ratio correction.

### General Settings

These settings are not specific to a particular input device.

#### `disable-power-key-handling`

By default, niri will take over the power button to make it sleep instead of power off.
Set this if you would like to configure the power button elsewhere (i.e. `logind.conf`).

```kdl
input {
    disable-power-key-handling
}
```

#### `warp-mouse-to-focus`

Makes the mouse warp to newly focused windows.

X and Y coordinates are computed separately, i.e. if moving the mouse only horizontally is enough to put it inside the newly focused window, then it will move only horizontally.

```kdl
input {
    warp-mouse-to-focus
}
```

#### `focus-follows-mouse`

Focuses windows and outputs automatically when moving the mouse over them.

```kdl
input {
    focus-follows-mouse
}
```

<sup>Since: 0.1.8</sup> You can optionally set `max-scroll-amount`.
Then, focus-follows-mouse won't focus a window if it will result in the view scrolling more than the set amount.
The value is a percentage of the working area width.

```kdl
input {
    // Allow focus-follows-mouse when it results in scrolling at most 10% of the screen.
    focus-follows-mouse max-scroll-amount="10%"
}
```

```kdl
input {
    // Allow focus-follows-mouse only when it will not scroll the view.
    focus-follows-mouse max-scroll-amount="0%"
}
```

#### `workspace-auto-back-and-forth`

Normally, switching to the same workspace by index twice will do nothing (since you're already on that workspace).
If this flag is enabled, switching to the same workspace by index twice will switch back to the previous workspace.

Niri will correctly switch to the workspace you came from, even if workspaces were reordered in the meantime.

```kdl
input {
    workspace-auto-back-and-forth
}
```
