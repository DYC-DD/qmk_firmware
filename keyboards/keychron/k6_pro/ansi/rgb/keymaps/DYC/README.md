# MacOS 注音輸入半形英文字母（失敗）

![Keychron K6 Pro](https://github.com/Keychron/ProductImage/blob/main/K_Pro/k6_pro.jpg?raw=true)

## 功能目標

在 macOS 的 注音輸入法下，當使用者按下 `右側 Shift + A~Z` 系統預設會輸出對應的 全形大寫英文字母（Full-width Capital Letters），例如輸入 Shift + A 會變成 Ａ（Unicode U+FF21），而非預期的 A（U+0041）；這對需要在注音輸入法下快速輸入英文字母造成困擾。

> **目前無法透過 QMK 達成的行為**
>
> -   QMK 僅負責輸出「掃描碼」（如 HID Usage ID 0x04 = A 鍵）
> -   macOS 的注音輸入法會自行攔截 Shift + A~Z 並轉換成全形字元
> -   無法透過 QMK 韌體繞過作業系統輸入法的行為層

---

## 安裝清單 (MacOS)

1.  **安裝 QMK CLI 工具（Python 套件）**

    ```bash
    python3 -m pip install --user qmk
    ```

    -   安裝好後請把 `~/.local/bin` 或 `~/Library/Python/3.x/bin` 加入 PATH
        ```bash
        echo 'export PATH="$HOME/Library/Python/3.9/bin:$PATH"' >> ~/.zshrc
        source ~/.zshrc
        ```

2.  **安裝 Homebrew**

    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

    -   加入環境變數（Apple Silicon 用）
        ```bash
        echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
        eval "$(/opt/homebrew/bin/brew shellenv)"
        ```

3.  **安裝 QMK 所需編譯與燒錄工具**

    ```bash
    brew tap osx-cross/avr
    brew install avr-gcc arm-none-eabi-gcc dfu-util dfu-programmer
    ```

4.  **確認安裝**
    ```bash
    qmk --version
    arm-none-eabi-gcc -v
    dfu-util --version
    ```
    -   確認 QMK CLI 安裝成功
    -   確認 ARM 工具鏈可用
    -   確認 DFU 燒錄工具可用

---

## 步驟 (MacOS)

1. clone 專案到目錄
2. 告訴 QMK CLI：「我的 QMK 原始碼就是在 `~/qmk_firmware`」
    ```bash
    qmk setup -H /Users/deng/Code/qmk_firmware
    ```
3. 安裝 Python 依賴套件

    ```bash
    /opt/homebrew/Cellar/qmk/1.1.6/libexec/bin/python -m pip install -r /Users/deng/Code/qmk_firmware/requirements.txt
    ```

4. 編譯

    ```bash
    qmk compile -kb keychron/k6_pro/ansi/rgb -km DYC
    ```

5. 進入 DFU 模式 [Keychron](https://www.keychron.uk/blogs/archived/k6-pro-factory-reset-and-firmware-flash?srsltid=AfmBOooRQfLoAgbd0JqLW5CneisuLtaeEJEag9DGBkd4P5ZLt8p3L92e&utm_source=chatgpt.com)

    1. 拔除電源線：從鍵盤上拔下 USB Type-C 電源線。
    2. 取下空格鍵帽：使用鍵帽拔取器或小工具，輕輕取下空格鍵帽。
    3. 找到復位按鈕：在空格鍵下方的 PCB 板上，靠近空格鍵開關的左側，可以找到一個小型的復位按鈕。
    4. 切換到有線模式：將鍵盤側面的模式切換開關撥到「Cable」位置，確保鍵盤處於有線模式。
    5. 按住復位按鈕並連接電源：按住復位按鈕不放，同時插入 USB Type-C 電源線，然後鬆開復位按鈕。
    6. 確認進入 DFU 模式：此時，鍵盤應該已進入 DFU 模式。

6. 燒錄
    ```bash
    qmk flash -kb keychron/k6_pro/ansi/rgb -km DYC
    ```
