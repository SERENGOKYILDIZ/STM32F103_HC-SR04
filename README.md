# STM32F103C6T6A HC-SR04 Ultrasonic Distance Sensor Project

This project is an application that measures distance using the HC-SR04 ultrasonic distance sensor with the STM32F103C6T6A microcontroller.

## 📋 Table of Contents

- [Overview](#overview)
- [Hardware Requirements](#hardware-requirements)
- [Connection Diagram](#connection-diagram)
- [Features](#features)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Technical Details](#technical-details)
- [Development Environment](#development-environment)
- [License](#license)
- [Author](#author)

## 🔍 Overview

This project continuously measures distance from the HC-SR04 ultrasonic distance sensor using the STM32F103C6T6A microcontroller. Measurements are performed with high precision using the timer's input capture feature.

### Key Features

- HC-SR04 ultrasonic distance sensor integration
- High-precision measurement using Timer Input Capture mode
- Automatic distance measurement at 200ms intervals
- STM32 HAL library usage
- Project structure configured with STM32CubeMX

## 🔧 Hardware Requirements

- **Microcontroller**: STM32F103C6T6A (ARM Cortex-M3, 32KB Flash, 10KB RAM)
- **Sensor**: HC-SR04 Ultrasonic Distance Sensor
- **Oscillator**: 8MHz external crystal oscillator (HSE)
- **Power Supply**: 3.3V

## 🔌 Connection Diagram

### HC-SR04 Connections

| HC-SR04 Pin | STM32F103C6T6A Pin | Description |
|-------------|---------------------|-------------|
| VCC         | 5V                  | Power supply |
| GND         | GND                 | Ground |
| Trig        | PA9                 | Trigger pin (GPIO Output) |
| Echo        | PA8                 | Echo pin (TIM1_CH1 - Input Capture) |

### System Clock

- **HSE**: 8MHz external oscillator (PD0/PD1)
- **System Clock**: 72MHz (9x multiplication with PLL)

## ⚙️ Features

### Distance Measurement

- **Measurement Range**: 2cm - 400cm (according to HC-SR04 specifications)
- **Measurement Precision**: Microsecond-level with timer input capture
- **Measurement Frequency**: New measurement every 200ms
- **Distance Calculation**: `Distance = (Pulse_Width × 0.034) / 2` (in cm)

### Timer Configuration

- **Timer**: TIM1
- **Prescaler**: 71 (72MHz / 72 = 1MHz → 1µs resolution)
- **Period**: 65535 (maximum 65.535ms measurement range)
- **Mode**: Input Capture (Rising/Falling Edge Detection)

## 📁 Project Structure

```
STM32F103C6T6A_HC-SR04/
├── Core/
│   ├── Inc/                    # Header files
│   │   ├── main.h
│   │   ├── stm32f1xx_hal_conf.h
│   │   └── stm32f1xx_it.h
│   └── Src/                    # Source files
│       ├── main.c              # Main application code
│       ├── stm32f1xx_hal_msp.c
│       ├── stm32f1xx_it.c
│       └── system_stm32f1xx.c
├── Drivers/                    # HAL and CMSIS drivers
├── Debug/                      # Build outputs
├── STM32F103C6T6A_HC-SR04.ioc  # STM32CubeMX configuration file
└── README.md                   # This file
```

## 🚀 Installation

### Requirements

1. **STM32CubeIDE** or compatible development environment
2. **STM32CubeMX** (for project configuration)
3. **STM32F1xx HAL Driver** (included in project)
4. **ST-Link** or compatible programmer

### Steps

1. Clone or download the project:
   ```bash
   git clone https://github.com/SERENGOKYILDIZ/STM32F103_HC-SR04.git
   cd STM32F103_HC-SR04
   ```

2. Open the project in STM32CubeIDE:
   - File → Import → Existing Projects into Workspace
   - Select the project folder

3. Make hardware connections (according to the connection diagram above)

4. Build and upload the project:
   - Project → Build All (Ctrl+B)
   - Run → Debug (F11)

## 💻 Usage

### Basic Usage

When the project is running, the system automatically:

1. Initializes timers and GPIOs
2. Performs distance measurement from HC-SR04 sensor every 200ms
3. Stores the measured distance in the `Distance` variable (in cm)

### Code Structure

#### Main Functions

- `HCSR04_Read()`: Triggers the sensor and starts measurement
- `HAL_TIM_IC_CaptureCallback()`: Timer interrupt callback function - captures echo signal
- `delay()`: Microsecond-level delay function

#### Important Variables

```c
uint32_t IC_Val1;          // First captured timer value (rising edge)
uint32_t IC_Val2;          // Second captured timer value (falling edge)
uint32_t Difference;       // Difference between two values (microseconds)
uint8_t Distance;          // Calculated distance (cm)
uint8_t Is_First_Captured; // Indicates whether the first value is captured
```

## 🔬 Technical Details

### Distance Calculation

The HC-SR04 sensor sends an ultrasonic wave and receives the echo. The duration the Echo pin is HIGH is proportional to the distance:

```
Distance (cm) = (Echo Duration (µs) × Sound Speed (cm/µs)) / 2
```

Since sound speed is approximately 0.034 cm/µs:
```
Distance (cm) = (Echo Duration (µs) × 0.034) / 2
```

### Timer Input Capture Working Principle

1. **Rising Edge**: Timer value is captured when Echo pin goes HIGH (IC_Val1)
2. **Falling Edge**: Timer value is captured when Echo pin goes LOW (IC_Val2)
3. **Difference Calculation**: The difference between the two values gives the echo signal duration
4. **Distance Calculation**: Distance is calculated using the difference value

### System Clock Configuration

- **HSE**: 8MHz
- **PLL Multiplier**: 9x
- **PLL Output**: 72MHz
- **System Clock**: 72MHz
- **APB1 Prescaler**: /2 (36MHz)
- **APB1 Timer Clock**: 72MHz (2x multiplication after prescaler)
- **APB2**: 72MHz

## 🛠️ Development Environment

- **IDE**: STM32CubeIDE
- **Firmware Package**: STM32Cube FW_F1 V1.8.6
- **HAL Version**: STM32F1xx HAL Driver
- **Compiler**: ARM GCC
- **Debugger**: ST-Link

## 📝 Notes

- The distance value is stored in the `Distance` variable but is not currently written to serial port or any other output
- You can add your preferred output method (UART, LCD, etc.)
- Timer prescaler value (71) is set to provide 1µs resolution
- Maximum measurable distance is approximately 11 meters (65.535ms × 0.034cm/µs / 2)

## 🔄 Development Suggestions

- Print distance value to serial monitor via UART
- LED or buzzer control based on distance value
- Add averaging (filtering) to improve measurement stability
- Add minimum/maximum distance limits
- Multiple sensor support

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Semi Eren Gökyıldız**

- Email: gokyildizsemieren@gmail.com
- GitHub: [SERENGOKYILDIZ](https://github.com/SERENGOKYILDIZ)
- LinkedIn: [Semi Eren Gökyıldız](https://www.linkedin.com/in/semi-eren-gökyıldız)

---

**Note**: This project was created with STM32CubeMX and uses the STM32 HAL library.
