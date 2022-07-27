# MurphPad ZMK Config

## Instructions

1. [Fork this repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo) so you can edit it.
2. Navigate to the **Actions** tab and click the "I understand my workflows, go ahead and run them" button to enable builds.
   ![Actions tab with "I understand my workflows" button](https://aws1.discourse-cdn.com/github/original/2X/8/8a28c79db26e3c2d82f2d0694ae0762b2ef7763b.png)
3. Edit the [config/murphpad.conf](config/murphpad.conf) to enable/disable features. Edit [config/murphpad.keymap](config/murphpad.keymap) to change the keymap. Lastly, make sure the [build.yaml](build.yaml) file has your board in the "boards" list.
4. After committing your changes, your firmware will begin compiling. Assuming there are no typos or other problems, it will eventually be [downloadable from the Actions tab](https://zmk.dev/docs/user-setup#installing-the-firmware).
5. [Flash the firmware](https://zmk.dev/docs/user-setup#flashing-uf2-files) that matches the board you're using, e.g. `murphpad-nice_nano_v2-zmk.uf2` for a nice!nano v2.

## Common Problems

### There was an error and the build didn't finish.

#### There is probably an error in your keymap.

Carefully double-check the last changes you made. ZMK syntax is very different from QMK syntax. Note that when editing keymaps, each ZMK code is generally preceded by a reference categorizing what that code does.

For example: the letter "Z" is categorized as a **k**ey **p**ress. To add the letter "Z" to a keymap, it must be written `&kp Z`.

A few more examples below:

| QMK | ZMK |
| --- | --- |
| `KC_A` | `&kp A` |
| `QK_REBOOT` | `&reset` |
| `LT(1, KC_SPACE)` | `&lt 1 SPACE` |

There are many, many more differences. Don't forget to [check the ZMK docs](https://zmk.dev/docs/features/keymaps).

### The OLED/RGB/encoder(s) don't work.

#### Double-check the features in the [config/murphpad.conf](config/murphpad.conf) file.

And double-check the Kconfig file in the build results to make sure what you've enabled is really enabled. As of mid-July 2022, [there are also some issues regarding default board/shield configurations taking precedence over user configurations](https://github.com/zmkfirmware/zmk/issues/1382).

#### The OLED can be a bit finicky.

- As of mid-July 2022, [there is a known issue with OLEDs not being re-initialized after power off](https://github.com/zmkfirmware/zmk/issues/674).

Try the following:

1. Wait at least one minute and press the physical reset button on the board once.
2. If `CONFIG_ZMK_RGB_UNDERGLOW_EXT_POWER` is set to `y` in [config/murphpad.conf](config/murphpad.conf), turn on RGB. Then try step 1.
3. Add `&ext_power EP_ON` to your keymap and press the key to *make sure* external power is on. Then try step 2.

### How do I turn off the RGB without turning the OLED off?

The RGB LEDs and OLED are powered by the same pin, `VCC`. So if power is cut to that pin, both will be turned off. There are two ways to resolve this:

#### Double-check your configuration.

1. Make sure `CONFIG_ZMK_RGB_UNDERGLOW_EXT_POWER` is set to `n` in [config/murphpad.conf](config/murphpad.conf). This will allow you to turn the RGB LEDs off without cutting power to the board's `VCC` pin.
2. Add `&ext_power EP_ON` to your keymap and press the key to *make sure* external power is on. Before removing this binding, wait at least one minute so the powered on status is saved to flash memory.

#### Connect OLED VCC to a different power source.

If the board powering your MurphPad has an alternate power pin (often labeled `RAW`), connecting `VCC` on the OLED to it will prevent the display from being affected when board `VCC` is toggled off.

Later revisions of the MurphPad add jumpers to simplify this process for boards with alternate power pins [in the same place as nice!nano `RAW`](https://nicekeyboards.com/docs/nice-nano/pinout-schematic), but this can be done on all known versions.

:warning: If using a nice!nano v2 without a battery, its `RAW` pin does not always deliver consistent voltage. This can result in the OLED turning off immediately after boot, or failing to turn on. Refer to [step 1 of this section](#the-oled-can-be-a-bit-finicky) for troubleshooting steps.

---

<details>
    <summary>Instructions for MurphPad v3.1</summary>

![photo by Tokolist#9634](https://cdn.discordapp.com/attachments/754060816122249352/929437921948344350/unknown.png)

1. Disconnect OLED `VCC` from LED8 by cutting the trace on the PCB.
2. Solder a wire connecting from the `RAW` pin on the PCB to the OLED `VCC` pin.
   - [@tokolist](https://github.com/tokolist) demonstrates an elegant solution by wiring `RAW` to the unused LED strip connection, but the wire can be connected directly.

:warning: If the LED strip is in use, you will also need to cut the trace between its `5V` and OLED `VCC`, and rewire the strip to board `VCC`.
</details>

<details>
    <summary>Instructions for MurphPad Rev2 v3.1</summary>

![photo by @honorless](https://cdn.discordapp.com/attachments/837441710698004531/1001305218979479682/PXL_20220726_0117007153.jpg)

1. Disconnect OLED `VCC` from LED8 by cutting the J1 jumper.
2. Connect OLED `VCC` to board `RAW` by closing the J2 jumper.

:warning: If your board's additional power pin is not in the same place, you will need to wire that yourself instead of closing J2.
</details>

---

### The flash worked, but I'm having inexplicable issues/can't get anything to pair.

#### Try flashing the `settings_reset` firmware for your board.

## Further Troubleshooting Resources

- [ZMK's troubleshooting page](https://zmk.dev/docs/troubleshooting)
- Stop by #keyboard-help in [MechWild's Discord](https://discord.gg/nfxHnsm)
- Ask the ZMK experts in [ZMK's Discord](https://zmk.dev/community/discord/invite)
