# ベンゼン（Benzene） - ヘキサコプタ飛行制御システム

本リポジトリは、Raspberry Pi Picoと複数のセンサを使用して構築されたヘキサコプタ型ドローン「**ベンゼン**」の飛行制御プログラムを含んでいます。

---

## 📦 機体仕様

| 項目               | 内容                         |
|--------------------|------------------------------|
| **機体名**         | ベンゼン（Benzene）         |
| **形状**           | ヘキサコプタ（6モーター）   |
| **フライト制御**   | Raspberry Pi Pico            |
| **IMUセンサ**      | LSM9DS1（9軸センサ）         |
| **距離センサ**     | VL53L1X（ToF 高度センサ）   |
| **PID制御**        | 姿勢・角速度・高度制御に適用 |
| **LED**            | WS2812B（状態表示に使用）   |
| **通信**           | SBUS（RC受信機） + I2C + UART |

---

## 🔧 使用ライブラリ／依存

- [Pico SDK](https://github.com/raspberrypi/pico-sdk)
- Eigen（線形代数計算ライブラリ）
- LSM9DS1 / VL53L1X 向けCドライバ
- カスタムPWM/I2C制御ライブラリ（本リポジトリ内）

---

## 📐 ハードウェア構成図

```
          [ Raspberry Pi Pico ]
                   |
    ----------------------------------------
    |         |           |          |     |
  [IMU]    [距離センサ] [RC入力]  [LED] [ESC制御]
 (LSM9DS1)  (VL53L1X)   (SBUS)   (WS2812)   (PWM)
```

---

## 🧠 ソフトウェア構成図

```text
Main Loop (pico_copter.cpp)
  ├── センサ初期化: imu_mag_init(), initialize_Altitude()
  ├── 制御初期化: control_init(), Kalman_init()
  ├── マルチコア: angle_control() を core1 に割り当て
  └── 400Hz割り込み: loop_400Hz() にて rate_control()
                          ├── センサ読み取り (sensor.cpp)
                          ├── PID制御 (pid.cpp)
                          └── PWM出力 (pwm.cpp)
```

---

## 🎮 機能一覧

- IMU（加速度・ジャイロ・磁気）による姿勢推定（EKF使用）
- ToFセンサ（VL53L1X）による高度推定と制御
- PID制御による姿勢角・角速度の安定化
- RC送信機によるマニュアル／自動制御切替
- RGB LEDによる状態表示（ノーマル・ホバリング・赤外検出等）
- データロギング（飛行データ保存・出力機能）

---

## 🚀 ビルド方法（CMake）

```bash
cd your_project_directory
mkdir build && cd build
cmake ..
make -j4
```

---

## 📁 ディレクトリ構成

```
.
├── pico_copter.cpp        # メイン制御ループ
├── control.cpp            # PID・状態管理・高度制御
├── ekf.cpp                # 拡張カルマンフィルタ
├── sensor.cpp             # IMU / ToFセンサ読み取り
├── pwm.cpp                # PWM出力制御
├── pid.cpp                # PIDクラス
├── radio.cpp              # RC入力（SBUS受信）
├── rgbled.cpp             # WS2812 LED制御
├── i2c.cpp                # I2C通信（OpenMVなど）
├── CMakeLists.txt         # ビルド設定
└── ...
```
---
