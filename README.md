# STM32 Nucleo64 C031C6 with Wokwi Simulation

[![Build and Simulate in Wokwi](https://github.com/wokwi/stm32-hello-wokwi/actions/workflows/ci.yml/badge.svg)](https://github.com/wokwi/stm32-hello-wokwi/actions/workflows/ci.yml)

A simple "Hello World" example showing how to run an STM32 project in [Wokwi for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=wokwi.wokwi-vscode).

## Building

We recommend using the [STM32 VS Code extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension) to build the project. You will also need to install [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html#get-software).

You can also build the project from the command line:

```bash
make
```

Make sure you have the [Arm GNU Toolchain](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads) installed.

## Simulation

To simulate this project, install [Wokwi for VS Code](https://marketplace.visualstudio.com/items?itemName=wokwi.wokwi-vscode). Open the project directory in Visual Studio Code, press **F1** and select "Wokwi: Start Simulator".

If wokwi complains about a missing firmware, you may need to tell cmake to generate a debug build. Press **F1**, select "CMake: Select Configure Preset", and choose "debug".

Once the simulation is running, you should see the text "Hello, Wowki!" in the Serial monitor.

### Debugging

You can also debug the simulated project using the built-in debugger in Visual Studio Code. To do that, follow these steps:

1. Configure cmake to generate a debug build by pressing **F1**, selecting "CMake: Select Configure Preset", and choosing "debug".
2. Press **F1** again and select "Wokwi: Start Simulator and Wait for Debugger".
3. Press **F5** to start the debugger.

The debug configuration is already defined in the [.vscode/launch.json](.vscode/launch.json) file. For more information, see the [Wokwi for VS Code documentation](https://docs.wokwi.com/vscode/debugging).

## Problems
> There is a conflict for build directory. Wokwi simulation is looking for something different. So there are apporaches we can use.

> Here is one approach for platformio projects. It is from [Leonid's guide for wokwi](https://blog.leon0399.ru/wokwi-platformio-github-actions?showSharer=true)
``` sh
[wokwi]
version = 1
firmware = "../../.pio/build/lucidgloves-prototype3/firmware.bin"
elf = "../../.pio/build/lucidgloves-prototype3/firmware.elf"
gdbServerPort=3333
```

> Here is my adaptation for STM32CUBECLT. I build project by CMakeTools extention on vscode. Do not forget to create directory and files as shown.
```sh
[wokwi]
version = 1
elf = "build/debug/build/stm32-hello-wokwi.elf"
firmware = "build/debug/build/stm32-hello-wokwi.hex"
gdbServerPort = 3333
```

> For now i depend on this toolchhain. In future i will run all in vscode and ubuntu WSL(again ubuntu in github CI) with ARM GCC toolchain or Rust. To be able to build and test platform indepently.`Vendor dependency is going to end`

