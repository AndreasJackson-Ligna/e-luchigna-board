# jiva
Code for and board files for Power unit tempearure sensor

## Links
This repo [Jiva-Board](https://github.com/arvidblaser/jiva-board)  
Test application [Jiva-BlinkySleepUarty](https://github.com/arvidblaser/jiva-blinkySleepyUarty)  
BLE (main) application [Jiva-ble](https://github.com/arvidblaser/jiva-ble)  

## Configuration

create folder C:\git
and then in git bash cmd
```
cd /c/git
git clone https://github.com/arvidblaser/jiva-board.git
git clone https://github.com/arvidblaser/jiva-blinkySleepyUarty.git
git clone https://github.com/arvidblaser/jiva-ble.git
```
Check that the chosen SDK and toolchain is 2.6.1 (other versions may work, but not tested. This should be preconfigugured in the .vscode-folder in the settings.json. This is also the place where I have preconfigured where the NRF-plugin should look for custom boards with the "nrf-connect.boardRoots"-parameter)

Open the full folder for the project you want in vs code. Easiest way:
```
cd /c/git/jiva-blinkySleepyUarty
code .
```
Add build configurations for the projects by choosing jiva board (should exist a custom boards choice with it) and keep the default on the oher sections.

Test to build and flash the blinkySleepyUarty - example. It should demonstrante that led blinks and that deep sleep and Uart works as expected.

If that works build the Jiva-ble project.

Note that the projects have different jiva.overlay files as more pins are enabled in full Jiva Project.



## Onetime steps to do for similar projects
### How to create a new board
* Only need to this once for a new PCB layout
* Create board (only need to select which MCU usead really)
* Configure DTS file for all pins that is connected and always used by the board (all this can be added in overlay file, but you only need to do it once here and makes the overlay file less cluttered)
* In the Kconfig.defconfig usage of the DC-DC shlould be added (if used). (Placement of thiss semms to have be changed since Ben project so not 100% sure)
```
config BOARD_ENABLE_DCDC
	bool "Enable DCDC mode"
	select SOC_DCDC_NRF52X
	default y
```
*  Create overlay file for optional pin connections depending on setup
(nrf connect tab -> actions -> build generate devicetree/overlay) 

## Other design practices

for less clutter i prefer option one when refrerring to pins in devictree / overlay
url: https://docs.zephyrproject.org/latest/build/dts/howtos.html 
```
/ {
        soc {
                serial0: serial@40002000 {
                        status = "okay";
                        current-speed = <115200>;
                        /* ... */
                };
        };

        aliases {
                my-serial = &serial0;
        };

        chosen {
                zephyr,console = &serial0;
        };
};
```

```
/* Option 1: by node label */
#define MY_SERIAL DT_NODELABEL(serial0)

/* Option 2: by alias */
#define MY_SERIAL DT_ALIAS(my_serial)

/* Option 3: by chosen node */
#define MY_SERIAL DT_CHOSEN(zephyr_console)

/* Option 4: by path */
#define MY_SERIAL DT_PATH(soc, serial_40002000)
```

