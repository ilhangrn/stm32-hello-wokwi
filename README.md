# STM32 Nucleo64 C031C6 Wokwi Simulation with embedded ci/cd flows

[![Build and Simulate in Wokwi](https://github.com/wokwi/stm32-hello-wokwi/actions/workflows/ci.yml/badge.svg)](https://github.com/wokwi/stm32-hello-wokwi/actions/workflows/ci.yml)

A simple "Hello World" example (it is not simple anymore) showing how to run an STM32 project in [Wokwi for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=wokwi.wokwi-vscode).

## Building

We recommend using the [STM32 VS Code extension](https://marketplace.visualstudio.com/items?itemName=stmicroelectronics.stm32-vscode-extension) to build the project. You will also need to install [STM32CubeCLT](https://www.st.com/en/development-tools/stm32cubeclt.html#get-software).

You can also build the project from the command line. make command is always a problem in windows. I suggest using `ubuntu/wsl` or `CMaketools extention in vscode`

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

## Github actions and local actions
> For both of them we need to get token from Wokwi. Because we pass our binary files to their server to run on our simulations and test it. You need to visit [Wokwi ci dashboard](https://wokwi.com/dashboard/ci).

```sh
 $env:WOKWI_CLI_TOKEN="<TOKEN>"
```

> After getting `TOKEN` from them. We can use it in github actions and local calls. Principles are same.

## Local use
> We can run a scenario(which refers a test routine) with a `serial.test.yaml` file in our repository, like this
```yaml
name: Serial test
version: 1
author: ilhan

steps:
  - wait-serial: "Hello"
```

```sh
wokwi-cli --scenario serial.test.yaml
```

> We can also have a direct call with line below. Having abc.test.yaml files helps better for future steps.
```sh
wokwi-cli --expect-text "Hello" --timeout 600
```

> It is same in github actions. We just add it to our `github/workflows/ci.yml` like this:
```yml
name: Build and test

on:
  workflow_dispatch:
  push:

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install ARM GCC toolchain
        uses: carlosperate/arm-none-eabi-gcc-action@v1
        with:
          release: '12.2.Rel1'
      
      - name: Build project
        run: make
 
      - name: Simulate and test with Wokwi
        uses: wokwi/wokwi-ci-action@v1
        with:
          token: ${{ secrets.WOKWI_CLI_TOKEN }}
          path: /    # directory with wokwi.toml, relative to repo's root
          timeout: 600
          scenario: 'serial.test.yaml'
```

> As you can see there is a detail as `secrets.WOKWI_CLI_TOKEN`. It is not that big secret. It is a secure-encrpty way of passing tokens to servers. To use it you need to add the token given by wokwi.

> To do this:
- visit https://github.com/**your_user_name**/**your_repo_name**/settings/secrets/actions

- Below you will see a section for your tokens. Probably you have nothing there. 

- Click `add new repository secret`, give it a name as `WOKWI_CLI_TOKEN` and then paste the your token from wokwi in textbox.

> Now you are ready, when you push(by default action settings) a commit it will run our action and give result for tests running on simulation.

### We are just beginning
> Our current wokwi-cli version is v0.14.5, imagine v1.0 release. Imagine releasing production code without touching board.

> Just imagine!