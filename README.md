# Hotpaw Keyboard

Hotpaw is a handwired, **hot**swappable, 3D **p**rinted, **a**nd **w**ireless keyboard. It's a split board with 70 keys, powered by a pair of nice!nano v2 microcontrollers.

Because I made it for myself, it only has a Colemak layout for now.

## Use cloud build

With every push to master, Github will build the firmware. [Here they are.](https://github.com/jtebert/zmk-config/actions) Pick the most recent build, download `firmware` under the "Artifacts" section, and flash as described below.

## Build locally

[Set up a local toolchain for ZMK.](https://zmk.dev/docs/development/setup) Easiest: use VS Code and the provided docker container.

Clone this `zmk-config` repository.

Follow [these instructions](https://zmk.dev/docs/development/build-flash#building-from-zmk-config-folder) to set up building locally with the `zmk-config` folder.

From within the `zmk/app` directory & container (*not zmk-config*), build each half:

```shell
# Right half
west build -d build/right -p -b nice_nano_v2 -- -DSHIELD=hotpaw_right -DZMK_CONFIG="/workspaces/zmk-config/config"
# Left half
west build -d build/left -p -b nice_nano_v2 -- -DSHIELD=hotpaw_left -DZMK_CONFIG="/workspaces/zmk-config/config"
```

Find the name of the ZMK docker container with `docker container ls`. It will be some string like `2cca4ef867b8`. Copy the firmware for each half using this string:

```shell
# Right half
docker cp 2cca4ef867b8:/workspaces/zmk/app/build/right/zephyr/zmk.uf2 right/
# Left half
docker cp 2cca4ef867b8:/workspaces/zmk/app/build/left/zephyr/zmk.uf2 left/
```

## Flashing

Put **one** half of the keyboard into boot mode. Do this either by:
1. Shorting the `GND` and `RST` pins on the controller, or
2. Using the keyboard shortcut
   - For the left half: `Fn` + `Esc` (top left key)
   - For the right half: `Fn` + `-` (top right key)

The keyboard should show up as a folder in the explorer as `NICENANO`. Paste the corresponding `zmk.uf2` file to the device. You might get an error that it couldn't finish copying. [This is a red herring.](https://zmk.dev/docs/troubleshooting#file-transfer-error) The keyboard will auto-reboot and be ready to use.

## Troubleshooting

### Keyboard says that it's connected by bluetooth, but doesn't type anything.

[Relevant ZMK link](https://zmk.dev/docs/features/bluetooth#troubleshooting)

1. Check that the keyboard is prioritizing bluetooth connection. Use the keyboard shortcut to set this: `Fn` + `Alt`
2. Reset the bluetooth connection:
   1. In the computer's bluetooth settings, remove/forget the keyboard connection.
   2. Turn off bluetooth on the computer.
   3. On the keyboard, clear the bluetooth profiles with `Fn` + `Ctrl`
   4. Turn back on the computer's bluetooth and re-pair to the keyboard