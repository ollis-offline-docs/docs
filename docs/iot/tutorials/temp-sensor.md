---
title: Read environmental conditions from a sensor
description: Learn how to read temperature, barometric pressure, and humidity with the .NET IoT Libraries.
author: camsoper
ms.date: 07/31/2024
ms.topic: tutorial
---

# Read environmental conditions from a sensor

One of the most common scenarios for IoT devices is detection of environmental conditions. A variety of sensors are available to monitor temperature, humidity, barometric pressure, and more.

In this topic, you will use .NET to read environmental conditions from a sensor.

> [!VIDEO https://learn-video.azurefd.net/vod/player?show=dotnet-iot-for-beginners&ep=iot-sensors-and-displays-with-i²c-and-dotnet-dotnet-iot-for-beginners]

## Prerequisites

- [!INCLUDE [prereq-sbc](../includes/prereq-sbc.md)]
- [BME280](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout) humidity/barometric pressure/temperature sensor breakout
- Jumper wires
- Breadboard (optional)
- Raspberry Pi GPIO breakout board (optional)
- [!INCLUDE [tutorial-prereq-dotnet](../includes/tutorial-prereq-dotnet.md)]

[!INCLUDE [rpi-note](../includes/rpi-note.md)]

> [!IMPORTANT]
> There are many manufacturers of BME280 breakouts. Most designs are similar, and the manufacturer shouldn't make any difference to the functionality. This tutorial attempts to account for variations. Ensure your BME280 breakout includes an Inter-Integrated Circuit (I<sup>2</sup>C) interface.
>
> Components like BME280 breakouts are often sold with unsoldered pin headers. If you're uncomfortable with soldering, look for a BME280 breakout board with a pre-soldered header or a different connector. If you want, consider learning how to solder! [Here's a good beginner's guide to soldering](https://learn.adafruit.com/adafruit-guide-excellent-soldering).

[!INCLUDE [prepare-pi-i2c](../includes/prepare-pi-i2c.md)]

## Prepare the hardware

Use the hardware components to build the circuit as depicted in the following diagram:

:::image type="content" source="../media/rpi-bmp280_i2c-thumb.png" alt-text="A Fritzing diagram showing the connection from Raspberry Pi to BME280 breakout board" lightbox="../media/rpi-bmp280_i2c.png":::

The following are the connections from the Raspberry Pi to the BME280 breakout. Note that pin labels differ on various BME280 breakouts.

| Raspberry Pi | BME280 Breakout | Color  |
|--------------|-----------------|--------|
| 3.3V         | VIN/3V3         | red    |
| Ground       | GND             | black  |
| SDA (GPIO 2) | SDI/SDA         | blue   |
| SCL (GPIO 3) | SCK/SCL         | orange |

[!INCLUDE [tutorial-rpi-gpio](../includes/tutorial-rpi-gpio.md)]

[!INCLUDE [gpio-breakout](../includes/gpio-breakout.md)]

## Create the app

Complete the following steps in your preferred development environment:

1. Create a new .NET Console App using either the [.NET CLI](../../core/tools/dotnet-new.md) or [Visual Studio](../../core/tutorials/with-visual-studio.md). Name it *SensorTutorial*.

    ```dotnetcli
    dotnet new console -o SensorTutorial
    cd SensorTutorial
    ```

1. [!INCLUDE [tutorial-add-packages](../includes/tutorial-add-iot-package.md)]
1. Replace the contents of *Program.cs* with the following code:

    :::code language="csharp" source="~/iot-samples/tutorials/SensorTutorial/Program.cs" :::

    In the preceding code:

    - `i2cSettings` is set to a new instance of `I2cConnectionSettings`. The constructor sets the `busId` parameter to 1 and the `deviceAddress` parameter to `Bme280.DefaultI2cAddress`.

        > [!IMPORTANT]
        > Some BME280 breakout manufacturers use the secondary address value. For those devices, use `Bme280.SecondaryI2cAddress`.

    - A [using declaration](../../csharp/language-reference/statements/using.md) creates an instance of `I2cDevice` by calling `I2cDevice.Create` and passing in `i2cSettings`. This `I2cDevice` represents the I<sup>2</sup>C bus. The `using` declaration ensures the object is disposed and hardware resources are released properly.
    - Another `using` declaration creates an instance of `Bme280` to represent the sensor. The `I2cDevice` is passed in the constructor.
    - The time required for the chip to take measurements with the chip's current (default) settings is retrieved by calling `GetMeasurementDuration`.
    - A `while` loop runs indefinitely. Each iteration:
        1. Clears the console.
        1. Sets the power mode to `Bmx280PowerMode.Forced`. This forces the chip to perform one measurement, store the results, and then sleep.
        1. Reads the values for temperature, pressure, humidity, and altitude.

            > [!NOTE]
            > Altitude is calculated by the device binding. This overload of `TryReadAltitude` uses mean sea level pressure to generate an estimate.

        1. Writes the current environmental conditions to the console.
        1. Sleeps 1000 ms.

1. [!INCLUDE [tutorial-build](../includes/tutorial-build.md)]
1. [!INCLUDE [tutorial-deploy](../includes/tutorial-deploy.md)]
1. Run the app on the Raspberry Pi by switching to the deployment directory and running the executable.

    ```bash
    ./SensorTutorial
    ```

    Observe the sensor output in the console.

1. Terminate the program by pressing <kbd>Ctrl+C</kbd>.

Congratulations! You've used I<sup>2</sup>C to read values from a temperature/humidity/barometric pressure sensor!

## Get the source code

The source for this tutorial is [available on GitHub](https://github.com/MicrosoftDocs/dotnet-iot-assets/tree/main/tutorials/SensorTutorial).

## Next steps

> [!div class="nextstepaction"]
> [Learn how to display text on an LCD](../tutorials/lcd-display.md)
