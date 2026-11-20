# STM32 Base Project
This repo explains the process of setting up a STM32 vscode project for a windows computer. It demostratest the process of using CubeMX to generate the initial boilerplate code, opening it in vscode, and modfiying `CMakeLists.txt` for build & flash. 

In addition, it demostrates how unit testing can be implemented by using the host PC as the runner.

- [STM32 Base Project](#stm32-base-project)
  - [Requirements](#requirements)
  - [CubeMX Setup](#cubemx-setup)
    - [Key point for CubeMX Generation](#key-point-for-cubemx-generation)
  - [Opening with Vscode](#opening-with-vscode)
  - [Build, Flash](#build-flash)
  - [Unit Testing (WIP)](#unit-testing-wip)


## Requirements
- [MSYS2](https://www.msys2.org/) installed, with gcc, arm-none-eabi-gcc, cmake and ctest available
- STM32 CubeMX & STM32 Flash Programmer installed
- [STM32 VS code Extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension) installed

## CubeMX Setup
CubeMX is a graphical IDE for generating projects. Using it, you can enable or disable various peripherals, with the peripheral startup code in `main.c`. 

I've found [this wiki](https://wiki.st.com/stm32mcu/index.php?title=Category:Getting_started_with_STM32L4&sfr=stm32mcu) by ST to be a useful despite it being for STM32L4. Step 1 and step 2 are relevant for initial setup. 

For those that want FreeRTOS configured, you can use [this tutorial](https://controllerstech.com/stm32-freertos-tutorial-cubemx-led-example/) by controllerstech. 

### Key point for CubeMX Generation
Before generating code, in the Project Manager, the Toolchain/IDE selected should be CMake. 

When done, click on "Open Folder"

## Opening with Vscode
Next, right click on folder and select open with vscode. Follow the prompts and wait for it to be configured. Its generally quite straightforward. the configured Preset I selected was Debug as this would allow me to use the inbuilt debugger if necessary. 

This was generally quite straightforward. The reference documentation I used was [this link](https://community.st.com/stm32-mcus-60/how-to-create-a-project-with-stm32-vs-code-extension-v2-0-0-or-newer-162256) on the ST community.

## Build, Flash
This was the point I remember being confused. I knew how to build by pressing the build button at the end, but I wasn't sure how to flash. Perhaps its because I'm not good with CMake. But here's the process.

Building:
```bash
cmake --build build/Debug
```

Flashing depends on the method. I'm assuming you use the STM32 Cube CLI programmer. Ensure it is on path by calling `STM32_Programmer_CLI --version`. Then add this to the bottom of `CMakeLists.txt` This ensures a build is correct and its the same as the build button. 

To flash

```cmake
add_custom_target(flash
    COMMAND STM32_Programmer_CLI -c port=SWD -w ${CMAKE_PROJECT_NAME}.elf -v -rst
    DEPENDS ${CMAKE_PROJECT_NAME}
    COMMENT "Flashing with STM32CubeProgrammer..."
)
```

Next run 
```bash
cmake --build build/Debug/ --target flash
```

This points cmake to the build/Debug folder, and executes the flash command. At this point your STM32 should be flashed with your new code.

## Unit Testing (WIP)