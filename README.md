# FreeRTOS Development Containers

這是一個專為 FreeRTOS 開發設計的 Docker 容器化專案，提供多種架構的開發環境，讓開發者能夠在統一的容器環境中進行 FreeRTOS 相關的開發工作。

## 支援的架構

- **ARM Cortex-M**: 適用於 STM32、NXP LPC、Microchip SAM 等
- **RISC-V**: 適用於 RISC-V 微控制器
- **ESP32**: 適用於 ESP32、ESP32-S2、ESP32-S3 等
- **POSIX**: 在 Linux 環境下模擬 FreeRTOS

## 快速開始

> **注意**: 如果映像檔是私有的，你需要先登入 GitHub Container Registry：
> ```bash
> echo $GITHUB_TOKEN | docker login ghcr.io -u YOUR_USERNAME --password-stdin
> ```
> 其中 `GITHUB_TOKEN` 是你的 GitHub Personal Access Token。

### 1. 拉取預建映像檔

```bash
# ARM 開發環境
docker pull ghcr.io/wiceec/freertos:arm-latest

# RISC-V 開發環境
docker pull ghcr.io/wiceec/freertos:riscv-latest

# ESP32 開發環境
docker pull ghcr.io/wiceec/freertos:xtensa-esp32-latest

# POSIX 模擬環境
docker pull ghcr.io/wiceec/freertos:posix-latest
```

### 2. 啟動開發容器

```bash
# ARM 開發環境（將當前目錄掛載到容器的 /workspace）
docker run -it -v ${PWD}:/workspace -w /workspace ghcr.io/wiceec/freertos:arm-latest bash

# Windows PowerShell 使用者
docker run -it -v ${pwd}:/workspace -w /workspace ghcr.io/wiceec/freertos:arm-latest bash

# 或使用絕對路徑
docker run -it -v /path/to/your/project:/workspace -w /workspace ghcr.io/wiceec/freertos:arm-latest bash
```

### 3. 在容器內編譯專案

```bash
# 進入容器後，你可以使用預裝的工具
cd /workspace

# 使用 CMake 建置專案
mkdir build && cd build
cmake ..
make

# 或使用 Ninja
cmake -G Ninja ..
ninja

# 檢查工具鏈版本
arm-none-eabi-gcc --version
```

## 映像檔說明

### Base 映像檔
- **映像檔**: `ghcr.io/wiceec/freertos:base-latest`
- **用途**: 所有其他映像檔的基礎
- **內容**: Debian stable-slim + 基本開發工具

### 架構特定映像檔

#### ARM Cortex-M
```bash
docker run -it -v ${PWD}:/workspace ghcr.io/wiceec/freertos:arm-latest bash
```
- **工具鏈**: `gcc-arm-none-eabi`
- **適用**: STM32, NXP LPC, Microchip SAM, Nordic nRF

#### RISC-V
```bash
docker run -it -v ${PWD}:/workspace ghcr.io/wiceec/freertos:riscv-latest bash
```
- **工具鏈**: `riscv-none-elf-gcc`
- **適用**: SiFive, Nuclei, GigaDevice GD32V

#### ESP32
```bash
docker run -it -v ${PWD}:/workspace ghcr.io/wiceec/freertos:xtensa-esp32-latest bash
```
- **工具鏈**: `xtensa-esp32-elf-gcc`
- **適用**: ESP32, ESP32-S2, ESP32-S3, ESP32-C3

#### POSIX 模擬
```bash
docker run -it -v ${PWD}:/workspace ghcr.io/wiceec/freertos:posix-latest bash
```
- **工具鏈**: `gcc-multilib`
- **適用**: FreeRTOS POSIX 模擬器

## 預裝工具

所有容器都包含以下開發工具：

- **編譯器**: 對應架構的 GCC 工具鏈
- **建置系統**: CMake, Make, Ninja
- **開發工具**: Git, Python3, pip
- **除錯工具**: GDB (架構對應版本)

## 使用範例

### 編譯 FreeRTOS 範例專案

```bash
# 1. 啟動容器
docker run -it -v ${PWD}:/workspace -w /workspace ghcr.io/wiceec/freertos:arm-latest bash

# 2. 下載 FreeRTOS 原始碼
git clone https://github.com/FreeRTOS/FreeRTOS.git
cd FreeRTOS

# 3. 編譯範例專案（以 STM32 為例）
cd FreeRTOS/Demo/CORTEX_STM32F103_Keil
make

# 4. 檢查產生的二進位檔案
ls -la *.bin *.elf *.hex
```

### 使用 Docker Compose

創建 `docker-compose.yml`：

```yaml
version: '3.8'
services:
  freertos-dev:
    image: ghcr.io/wiceec/freertos:arm-latest
    volumes:
      - .:/workspace
    working_dir: /workspace
    stdin_open: true
    tty: true
    command: bash
```

啟動：
```bash
docker-compose run freertos-dev
```

## 本地建置

如果你需要自訂映像檔：

```bash
# 建置基礎映像檔
docker build -t freertos:base ./freertos-base/

# 建置 ARM 環境
docker build -t freertos:arm ./freertos/ \
  --build-arg BASE_IMAGE=freertos:base \
  --build-arg TOOLCHAIN_ARCH=arm-none-eabi

# 建置 RISC-V 環境
docker build -t freertos:riscv ./freertos/ \
  --build-arg BASE_IMAGE=freertos:base \
  --build-arg TOOLCHAIN_ARCH=riscv-none-elf

# 建置 ESP32 環境
docker build -t freertos:xtensa-esp32 ./freertos/ \
  --build-arg BASE_IMAGE=freertos:base \
  --build-arg TOOLCHAIN_ARCH=xtensa-esp32-elf

# 建置 POSIX 環境
docker build -t freertos:posix ./freertos-posix/ \
  --build-arg BASE_IMAGE=freertos:base
```

## CI/CD 整合

這些容器特別適合在 CI/CD 管道中使用：

```yaml
# GitHub Actions 範例
jobs:
  build:
    runs-on: ubuntu-latest
    container:
      image: ghcr.io/wiceec/freertos:arm-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build FreeRTOS project
        run: |
          mkdir build && cd build
          cmake ..
          make
```

## 常見問題

### Q: 如何在 Windows 上使用？
A: 確保 Docker Desktop 已安裝並啟動，然後使用 PowerShell 或 CMD 執行 Docker 命令。

### Q: 如何在容器中使用 USB 裝置？
A: 使用 `--device` 參數：
```bash
docker run -it --device=/dev/ttyUSB0 -v ${PWD}:/workspace ghcr.io/wiceec/freertos:arm-latest bash
```

### Q: 如何保持容器中的檔案？
A: 使用 volume 掛載：
```bash
docker run -it -v freertos-data:/data -v ${PWD}:/workspace ghcr.io/wiceec/freertos:arm-latest bash
```

## 貢獻

歡迎提交 Issue 和 Pull Request 來改善這個專案。

## 授權

請參考 LICENSE 檔案。
