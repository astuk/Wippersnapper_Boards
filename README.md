# Wippersnapper_Boards
Hardware specification and boards for Wippersnapper.

This repository, its contents, and the Wippersnapper hardware specification is a ***work in progress and subject to change***.

# Introduction
This README.md specifies the hardware definition model for internet-of-things hardware compatibility with Wippersnapper. 

# Repository Contents

`definition`: Contains hardware definition models.
`boards.json`: Index of hardware within `definitions/` containing hardware USB vendor ID (vid) and product ID (pid).

# Hardware definition

The hardware definition describes the contents (hardware information, components) of a physical piece of hardware or project.

## Information

Information related to the hardware including the hardware's name, definition and unique identifiers.

| Property    | Required | Data Type | description                                             |
|-------------|----------|-----------|---------------------------------------------------------|
| boardName   | Yes      | String    | Hardware name                                           |
| mcuName     | Yes      | String    | Microcontroller name                                    |
| VID         | Yes      | int16     | USB Vendor ID                                           |
| PID         | Yes      | int16     | USB Product ID                                          |
| displayName | Yes      | String    | Adafruit IO Device name                                 |
| definition  | Yes      | String    | Device definition                                       |
| lastUpdated | Yes      | Time      | Last time the board definition was updated, in ISO-8601 |


## Components

Hardware components are digital pins, ADC pins, sensors, servos, or motors. These components are defined within the `components` array. 

\
Each hardware component is defined by adding the following to the `.json` definition file:

| Property       | Required | Data Type | description                                                                            |
|----------------|----------|-----------|----------------------------------------------------------------------------------------|
| name           | yes      | string    | Component type. Components connected to hardware externally are prefixed by `external_`|
| displayName    | no       | string    | Human-readable display name for Adafruit IO                                            |
| dataType       | yes      | string    | Expected data type from component                                                      |
| max_resolution | no       | int16     | Max resolution of an analog component, in bits                                         |
| readable       | yes      | boolean   | Whether the component is input only. Default is True, read/write.                      |


The following properties are set by the Wippersnapper web application. **You do not need to define these values**:

| Property       | Required | Data Type | description                                                                            |
|----------------|----------|-----------|----------------------------------------------------------------------------------------|
| mode  | no       | int16     | Component mode. See `mode` for type definitions.                              |
| direction      | no       | bool      | Defines the direction of a component, either input (`0`) or output (`1`).              |
| pull           | no       | bool      | Defines the pull direction of a component, either up (`0`) or down (`1`).              |
| period         | no       | int32     | Number of milliseconds between measurements. Defaults to -1, no active measurements.   |

### `period` Measurement Period

Stores the number of milliseconds between measurements as a signed 32-bit int. Defining a value of `-1` stops a measurement. This field is **zero** by default.

### `mode` Component mode

Defines the component's mode. Currently can either be analog (`1`), or digital (`2`).
* [Associated protocol buffer message](https://github.com/adafruit/Wippersnapper_Protobuf/blob/master/proto/pin/v1/pin.proto#L28)


## Draft
The following information is a draft and not currently implemented by Adafruit Wippersnapper.

#### Sensor Component Types

Sensors are special component types that are defined by attaching a component to the device in user-code. These components should append the following properties onto the component structure above:

| Property     | Required | Data Type | description                                                                       |
|--------------|----------|-----------|-----------------------------------------------------------------------------------|
| sensorID     | Yes      |   int32   | Sensor instance number. Must be unique (no two conflicting sensorIDs per definition) |
| max_val      | No       |   float   |  Maximum value of this sensor's value in SI units.                                   |
| min_val      | No       |   float   |  Minimum value of this sensor's value in SI units.                                   |



#### Properties and Units
The component's `propertyName`, `type`, and `unit` describe component's the data type and SI unit. The table below mirrors the [Sensor Properties and Units defined in the CircuitPython API documentation.](https://circuitpython.readthedocs.io/en/latest/docs/design_guide.html#sensor-properties-and-units).

| Property name         | Python type           | Units                                                                   |
| --------------------- | --------------------- | ----------------------------------------------------------------------- |
| ``acceleration``      | (float, float, float) | x, y, z meter per second per second                                     |
| ``magnetic``          | (float, float, float) | x, y, z micro-Tesla (uT)                                                |
| ``orientation``       | (float, float, float) | x, y, z degrees                                                         |
| ``gyro``              | (float, float, float) | x, y, z radians per second                                              |
| ``temperature``       | float                 | degrees centigrade                                                      |
| ``eCO2``              | float                 | equivalent CO2 in ppm                                                   |
| ``TVOC``              | float                 | Total Volatile Organic Compounds in ppb                                 |
| ``distance``          | float                 | centimeters                                                             |
| ``light``             | float                 | non-unit-specific light levels (should be monotonic but is not lux)     |
| ``lux``               | float                 | SI lux                                                                  |
| ``pressure``          | float                 | hectopascal (hPa)                                                       |
| ``relative_humidity`` | float                 | percent                                                                 |
| ``current``           | float                 | milliamps (mA)                                                          |
| ``voltage``           | float                 | volts (V)                                                               |
| ``color``             | int                   | RGB, eight bits per channel (0xff0000 is red)                           |
| ``alarm``             | (time.struct, str)    | Sample alarm time and string to characterize frequency such as "hourly" |
| ``datetime``          | time.struct           | date and time                                                           |
| ``duty_cycle``        | int                   | 16-bit PWM duty cycle (regardless of output resolution)                 |
| ``frequency``         | int                   | Hertz                                                                   |
| ``value``             | bool                  | Digital logic                                                           |
| ``value``             | int                   | 16-bit Analog value, unit-less                                          |
| ``weight``            | float                 | grams (g)                                                               |


# Examples

Example hardware definitions can be found in the `definitions/` directory.

# Limitations
* Wippersnapper currently only supports WiFi, Cellular and Ethernet connectivity.

# Contributing
If you do not see the board you want to use with Wippersnapper, adding support for a board is simple and we welcome all contributions:
* Fork this repository and checkout a new branch.
* Make a new directory in `definitions/YOUR_BOARD_NAME`
* Add your hardware definition, `YOUR_BOARD_NAME.json`, to `definitions/YOUR_BOARD_NAME`.
* Add a new board to `index.json`. 
  * The `board` key value should match the name of the directory and definition file you created.
  * This file is sorted by Vendor ID (VID) first.
    * If you are contributing hardware not designed by Adafruit - you will need to create a new Array and append an object containing the Product ID (`PID`) and `board`.
* Create a pull request on this repository
