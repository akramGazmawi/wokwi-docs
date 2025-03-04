---
title: Logic Analyzer Guide
sidebar_label: Logic Analyzer
---

The [Logic Analyzer](../parts/wokwi-logic-analyzer) records the values of digital signals in your project. It's a powerful debugging tool, and can help you diagnose issues in your circuit and code. It's also very useful as a learning aid.

Some use cases include:

- Observe the PWM signal generated by the [analogWrite() function](https://www.arduino.cc/reference/en/language/functions/analog-io/analogwrite/)
- Look at the [Servo](../parts/wokwi-servo) control signal
- Develop and debug PIO machines for the [Raspberry Pi Pico](../parts/wokwi-pi-pico)
- Learn about the I2C protocol by looking at the SCL/SDA signals

## Features

The Wokwi Logic Analyzer has 8 digital channels, called D0 to D7. It has a sample rate of 1GHz, which should be more than enough for most applications.

Each channel has an activity LED that turns green whenever there is an activity on the channel. The activity LEDs provide an easy way to see that the signals are connected
correctly.

The Logic analyzer has a small display. It shows the number of samples captured since
the simulation started.

## Using the Logic Analyzer

First, add the Logic Analyzer to your project. You can do this by clicking the purple Plus button, then selecting "Logic Analyzer (8 channels)".

Next, connect the signals you want to record to the Logic Analyzer pins. For instance, if you want to record the I2C communication between an Arduino Uno and some peripheral (such as the [DS1307 RTC](../parts/wokwi-ds1307)), you can connect pins A4/A5 (which carry the SDA/SCL signals) to pins D0/D1 of the Logic Analyzer.

Start the simulation. You should see the green activity LEDs blink as the signals are coming in, and the number of samples show go up.

To view the capture, stop the simulation. This will download a file called "wokwi-logic.vcd" to your computer. The file contains the recorded signals in [Value Change Dump](https://en.wikipedia.org/wiki/Value_change_dump) format.

You can use software such as PulseView or GTKWave to open and view the capture files. These software also include powerful protocol analyzers that can decode many common protocols such as UART, I2C, WS2812, and many others.

### Viewing the data in PulseView

PulseView an open source Logic Analyzer GUI. It runs on Linux, Windows, Mac OS X, and there's even an Android version. You can visit their [downloads page](https://sigrok.org/wiki/Downloads) to get the latest version.

Load the recorded signal (VCD) file by clicking on the small arrow next to the "Open" button, and choosing: "Import Value Change Dump data..."

![PulseView: Import Value Change Dump data](logic-analyzer-pulseview-1.png)

You'll see a dialog where you can choose the VCD file to import. Once you selected a file, there'll be another dialog with import options:

![PulseView: Compress idle periods](logic-analyzer-pulseview-2.png)

Unfortunately, the default options usually cause PulseView to consume a lot of RAM and become sluggish. You can reduce the memory usage by setting **Downsampling factor**. A value of **50** should work for most use cases. The following table lists some common values:

| Downsampling factor | Sample rate | When to use?                                          |
| ------------------- | ----------- | ----------------------------------------------------- |
| 1000                | 1MHz        | Low frequency signals / long recordings (10+ minutes) |
| **50**              | **20MHz**   | Common signals (UART, I2C, LED PWM, WS2812, etc.)     |
| 10                  | 100MHz      | High speed signals (10MHz+)                           |
| 1                   | 1GHz        | Very high speed signals (50MHz+)                      |

After confirming the import options, you should see the imported signals on the screen. The signal names will be "logic.D0", "logic.D1", etc.

To learn more about using PulseView, please consult the [PulseView user manual](https://sigrok.org/doc/pulseview/unstable/manual.html).

#### Configuring Windows to open VCD files in PulseView

You can configure Windows to automatically open VCD files in PulseView. Open a command prompt (cmd) window **as administrator**, and type the following commands:

```
ftype vcdfile="%ProgramFiles%\sigrok\PulseView\pulseview.exe" "-I" "vcd:downsample=50" "%1"
assoc .vcd=vcdfile
```

Note that these commands don't work in powershell. The only work in cmd with Administrator privileges. The command sets the Downsampling factor to 50, but you can choose a different value that fits your needs (see table above).
