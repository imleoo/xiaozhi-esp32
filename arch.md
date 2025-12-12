# 小智架构文档

## 项目简介

小智是一个基于ESP32的开源AI聊天机器人项目，支持离线语音唤醒、流式ASR+LLM+TTS架构、表情显示、设备控制等功能。项目采用MIT许可证，支持超过70种不同的开源硬件。

### 版本说明
- **v2版本**：当前主分支，使用新的分区表布局（4MB flash）
- **v1版本**：稳定版本1.9.2，维护至2026年2月（`git checkout v1`）

## 核心特性

### 语音交互
- **离线语音唤醒**：基于ESP-SR，支持自定义唤醒词
- **音频编解码**：采用OPUS编码，16kHz采样率
- **语音活动检测**：实时VAD，智能检测说话停顿
- **声纹识别**：基于3D Speaker模型识别说话人
- **流式处理**：实时语音流处理，低延迟交互

### 网络通信
- **双网络支持**：Wi-Fi + ML307 Cat.1 4G
- **通信协议**：
  - WebSocket协议（推荐）：低延迟实时通信
  - MQTT+UDP混合协议：兼容性好，支持弱网环境
- **自动重连**：网络断线自动重连机制

### 显示系统
- **显示支持**：OLED/LCD/TFT多种显示屏
- **LVGL图形库**：支持复杂的UI和动画效果
- **表情系统**：
  - 内置多种表情（PNG/JPG格式）
  - 支持GIF动画
  - 自定义表情包
- **多语言字体**：内置20+语言支持

### 设备控制（MCP协议）
- **GPIO控制**：数字输入/输出控制
- **PWM输出**：舵机、LED调光控制
- **I2C设备**：传感器、扩展板控制
- **SPI设备**：显示屏、存储设备控制
- **ADC采集**：模拟信号读取
- **摄像头支持**：图像采集和处理

## 目录结构详解

```
xiaozhi-esp32/
├── main/                          # 主程序代码
│   ├── application.cc/.h          # 应用程序主控制器
│   ├── device_state_machine.cc/.h # 设备状态机管理
│   ├── ota.cc/.h                  # OTA固件升级
│   ├── settings.cc/.h             # 系统配置管理
│   ├── system_info.cc/.h          # 系统信息获取
│   ├── assets.cc/.h               # 资源文件管理
│   ├── mcp_server.cc/.h           # MCP协议服务端实现
│   │
│   ├── audio/                     # 音频处理模块
│   │   ├── audio_service.cc/.h     # 音频服务主控制器
│   │   ├── audio_codec.cc/.h       # 音频编解码接口
│   │   ├── wake_word.h             # 唤醒词接口定义
│   │   ├── processors/             # 音频处理器
│   │   │   ├── afe_audio_processor # AFE音频前端处理
│   │   │   └── audio_debugger      # 音频调试工具
│   │   ├── codecs/                 # 音频编解码器实现
│   │   │   ├── es8311_audio_codec  # ES8311编解码芯片
│   │   │   ├── es8374_audio_codec  # ES8374编解码芯片
│   │   │   ├── es8388_audio_codec  # ES8388编解码芯片
│   │   │   └── box_audio_codec     # ESP-BOX专用编解码
│   │   └── wake_words/             # 唤醒词实现
│   │       ├── afe_wake_word       # AFE唤醒词检测
│   │       ├── esp_wake_word       # ESP-SR唤醒词
│   │       └── custom_wake_word    # 自定义唤醒词
│   │
│   ├── boards/                     # 硬件板级支持包
│   │   ├── common/                 # 通用组件
│   │   │   ├── board.cc/.h         # Board基类
│   │   │   ├── wifi_board.cc/.h    # Wi-Fi板级基类
│   │   │   ├── dual_network_board.cc/.h # 双网络板基类
│   │   │   ├── button.cc/.h        # 按键处理
│   │   │   ├── knob.cc/.h          # 旋钮编码器
│   │   │   ├── backlight.cc/.h     # 背光控制
│   │   │   ├── adc_battery_monitor.cc/.h # ADC电池监测
│   │   │   ├── axp2101.cc/.h        # AXP2101电源管理
│   │   │   ├── sy6970.cc/.h         # SY6970充电管理
│   │   │   ├── esp32_camera.cc/.h  # ESP32摄像头驱动
│   │   │   ├── power_save_timer.cc/.h # 定时休眠
│   │   │   ├── sleep_timer.cc/.h    # 休眠定时器
│   │   │   └── system_reset.cc/.h   # 系统复位
│   │   └── [具体板型]/              # 70+种具体硬件支持
│   │       ├── esp-box-3/          # ESP32-S3-BOX3
│   │       ├── lichuang-dev/       # 立创开发板
│   │       ├── m5stack-core-s3/    # M5Stack CoreS3
│   │       └── ...                 # 更多硬件支持
│   │
│   ├── display/                    # 显示模块
│   │   ├── display.cc/.h           # 显示接口基类
│   │   ├── oled_display.cc/.h      # OLED显示实现
│   │   ├── lcd_display.cc/.h       # LCD显示实现
│   │   ├── emote_display.cc/.h     # 表情显示管理
│   │   └── lvgl_display/           # LVGL显示实现
│   │       ├── lvgl_display.cc/.h  # LVGL主控制器
│   │       ├── lvgl_font.cc/.h     # 字体管理
│   │       ├── lvgl_theme.cc/.h    # 主题管理
│   │       ├── lvgl_image.cc/.h    # 图片管理
│   │       ├── emoji_collection.cc/.h # 表情集合
│   │       ├── gif/                # GIF动画支持
│   │       └── jpg/                # JPG图片支持
│   │
│   ├── led/                        # LED控制模块
│   │   ├── led_strip.cc/.h         # LED灯带控制
│   │   ├── single_led.cc/.h        # 单LED控制
│   │   ├── circular_strip.cc/.h    # 环形LED灯带
│   │   └── gpio_led.cc/.h          # GPIO LED控制
│   │
│   ├── protocols/                  # 通信协议
│   │   ├── protocol.cc/.h          # 协议基类
│   │   ├── websocket_protocol.cc/.h # WebSocket实现
│   │   └── mqtt_protocol.cc/.h      # MQTT实现
│   │
│   └── main.cc                     # 程序入口
│
├── managed_components/             # ESP-IDF组件依赖
│   ├── 78__esp-opus/              # OPUS音频编解码
│   ├── 78__esp-ml307/             # ML307 4G模组驱动
│   ├── espressif__esp-sr/         # ESP-SR语音识别
│   ├── xiaozhi-fonts/             # 自定义字体组件
│   └── ...                       # 其他组件
│
├── scripts/                        # 构建和工具脚本
│   ├── release.py                 # 发布构建脚本
│   ├── build_default_assets.py    # 默认资源构建
│   ├── gen_lang.py                # 语言配置生成
│   ├── audio_debug_server.py      # 音频调试服务器
│   ├── mp3_to_ogg.sh              # 音频格式转换
│   └── versions.py                # 版本管理
│
├── docs/                          # 项目文档
│   ├── v1/                        # v1版本文档
│   ├── websocket.md               # WebSocket协议文档
│   ├── mqtt-udp.md                # MQTT协议文档
│   ├── mcp-usage.md               # MCP使用说明
│   ├── mcp-protocol.md            # MCP协议实现
│   └── custom-board.md            # 自定义开发板指南
│
├── partitions/                    # 分区表定义
│   ├── v1/                        # v1版本分区表
│   └── v2/                        # v2版本分区表（4MB）
│
├── assets/                        # 资源文件
│   ├── locales/                   # 多语言资源
│   │   ├── zh-CN/                 # 简体中文
│   │   ├── en-US/                 # 英语
│   │   └── ja-JP/                 # 日语
│   └── common/                    # 通用音频资源
│
└── tests/                         # 测试代码
```

## 架构设计详解

### 1. 应用程序架构

#### 事件驱动模型
```cpp
// 主事件定义
#define MAIN_EVENT_SCHEDULE             (1 << 0)  // 调度事件
#define MAIN_EVENT_SEND_AUDIO           (1 << 1)  // 发送音频
#define MAIN_EVENT_WAKE_WORD_DETECTED   (1 << 2)  // 唤醒词检测
#define MAIN_EVENT_VAD_CHANGE           (1 << 3)  // VAD状态变化
#define MAIN_EVENT_ERROR                (1 << 4)  // 错误事件
#define MAIN_EVENT_NETWORK_CONNECTED    (1 << 7)  // 网络连接
#define MAIN_EVENT_TOGGLE_CHAT          (1 << 9)  // 切换聊天状态
```

#### 应用程序生命周期
1. **初始化阶段**（Application::Initialize）
   - NVS初始化
   - 显示屏初始化
   - 音频系统初始化
   - 网络连接启动
   - MCP服务启动

2. **运行阶段**（Application::Run）
   - 事件循环处理
   - 状态机调度
   - 音频数据处理
   - 网络消息处理

### 2. 设备状态机

```cpp
enum DeviceState {
    kDeviceStateIdle,          // 空闲
    kDeviceStateListening,     // 监听中
    kDeviceStateSpeaking,      // 播放中
    kDeviceStateUpgrading,     // 升级中
    kDeviceStateAlert,         // 提醒状态
    kDeviceStateSleeping,      // 休眠
};
```

状态转换规则：
- Idle → Listening：检测到唤醒词或手动触发
- Listening → Speaking：接收到服务器响应
- Listening → Idle：超时或取消
- Speaking → Idle：播放完成
- Any → Alert：系统提醒
- Any → Upgrading：OTA升级

### 3. MCP协议架构

#### MCP工具注册机制
```cpp
// 注册示例
auto& mcp_server = MCPToolRegistry::GetInstance();
mcp_server.RegisterTool("set_gpio", "控制GPIO输出",
    {Property("pin", kPropertyTypeInteger),
     Property("value", kPropertyTypeBoolean)},
    [](const cJSON* params) -> ReturnValue {
        // 实现逻辑
    });
```

#### 支持的MCP功能
- **GPIO控制**：输入/输出配置，电平控制
- **PWM输出**：舵机控制，LED调光
- **I2C通信**：读写I2C设备
- **SPI通信**：读写SPI设备
- **ADC采集**：模拟电压读取
- **系统控制**：重启、关机、休眠
- **音频控制**：音量调节，播放控制
- **显示控制**：显示文字，切换表情

### 4. 音频处理流水线

```
麦克风 → AudioProcessor → VAD检测 → 唤醒词检测 → OPUS编码 → 网络发送
                                                            ↓
网络接收 ← JSON解析 ← TTS播放 ← 语音合成 ← LLM处理 ← ASR识别 ← OPUS解码
```

#### 音频参数配置
- 采样率：16kHz
- 通道数：单声道
- 编码格式：OPUS（6kbps）
- 帧长度：20ms
- VAD灵敏度：可调节

## 开发环境配置

### 1. 环境准备

```bash
# 1. 安装ESP-IDF
git clone https://github.com/espressif/esp-idf.git
cd esp-idf
git checkout v5.5  # 推荐版本
./install.sh
source ./export.sh

# 2. 克隆项目
git clone https://github.com/78/xiaozhi-esp32.git
cd xiaozhi-esp32

# 3. 安装依赖
idf.py reconfigure
```

### 2. 编译命令

```bash
# 配置项目
idf.py menuconfig
# 配置项：
# - Serial flasher config → Flash size → 4MB
# - Component config → XiaoZhi → Board Type → 选择对应硬件

# 清理构建
idf.py fullclean

# 编译项目
idf.py build

# 烧录固件
idf.py flash

# 监控日志
idf.py monitor

# 合并bin文件（用于发布）
idf.py merge-bin

# OTA升级
idf.py ota
```

### 3. 调试工具

#### 音频调试
```bash
# 启动音频调试服务器
python scripts/audio_debug_server.py

# 功能：
# - 实时查看音频波形
# - VAD状态监控
# - 唤醒词测试
# - 音频参数调节
```

#### 网络调试
- WebSocket测试工具：wscat
- MQTT调试工具：MQTT Explorer
- 串口监控：idf.py monitor 或 minicom

## 板级定制指南

### 1. 创建新板级支持

```cpp
// 在 main/boards/my_board/board.h
#ifndef BOARD_H
#define BOARD_H

#include "../common/board.h"

class MyBoard : public Board {
public:
    MyBoard();
    void Init() override;
    std::string GetBoardName() override { return "my-board"; }

    // 实现必需的接口
    AudioCodec* CreateAudioCodec() override;
    Display* CreateDisplay() override;
    // ...
};

#endif
```

### 2. 配置硬件参数

```cpp
// 在 main/boards/my_board/config.h
#pragma once

// GPIO定义
#define BOARD_I2C_SDA  5
#define BOARD_I2C_SCL  6
#define BOARD_SPEAKER_IO  7
#define BOARD_MICROPHONE_IO  8

// 硬件特性
#define BOARD_HAS_BATTERY    1
#define BOARD_HAS_CHARGING   1
#define BOARD_BACKLIGHT_PIN  9
```

### 3. 添加到构建系统

```cmake
# 在 main/CMakeLists.txt 中添加
elseif(CONFIG_BOARD_TYPE_MY_BOARD)
    set(BOARD_TYPE "my-board")
    set(BUILTIN_TEXT_FONT font_puhui_20_4)
    set(DEFAULT_EMOJI_COLLECTION twemoji_64)
```

## 资源管理

### 1. 资源文件组织

```
assets/
├── locales/              # 多语言资源
│   ├── zh-CN/           # 简体中文
│   │   ├── language.json
│   │   ├── wake_up.ogg
│   │   └── sleep.ogg
│   ├── en-US/           # 英语
│   └── ja-JP/           # 日语
└── common/              # 通用资源
    ├── notification.ogg
    └── error.ogg
```

### 2. 资源打包

资源文件通过SPIFFS文件系统打包：
- 默认分区大小：1.5MB（4MB flash）
- 支持热更新资源
- 支持自定义资源包

### 3. 字体系统

支持多种字体格式：
- `.bdf`：位图字体
- `.ttf`：矢量字体
- 自定义格式：压缩字体

内置字体：
- `font_puhui_14_1`：普通话14px
- `font_awesome_14_1`：FontAwesome图标14px
- `font_puhui_basic_20_4`：基础20px

## OTA升级

### 1. 升级流程

```mermaid
graph LR
    A[检查版本] --> B[下载固件]
    B --> C[验证固件]
    C --> D[写入OTA分区]
    D --> E[重启设备]
    E --> F[验证启动]
```

### 2. OTA配置

```cpp
// 在 sdkconfig 中配置
CONFIG_BOOTLOADER_APP_ROLLBACK_ENABLE=y
CONFIG_PARTITION_TABLE_SINGLE_APP=y
CONFIG_PARTITION_TABLE_OFFSET=0x8000
```

### 3. 升级方式

- **HTTP升级**：从URL下载固件
- **MQTT推送**：通过MQTT推送固件数据
- **本地升级**：从SD卡或SPIFFS升级

## 性能优化

### 1. 内存管理

- 使用静态内存池减少碎片
- 音频缓冲区使用PSRAM
- 图片懒加载机制

### 2. CPU优化

- 使用ESP32-S3的向量指令
- 音频处理使用硬件加速
- 双核任务分离

### 3. 功耗优化

- 自动休眠机制
- 动态频率调整
- 外设按需上电

## 常见问题

### 1. 编译错误

**错误**：fatal error: esp_opus.h: No such file or directory
**解决**：确保已安装esp-opus组件
```bash
idf.py reconfigure
```

### 2. 烧录失败

**错误**：Failed to write to target RAM
**解决**：
- 检查USB线质量
- 降低波特率：`idf.py -p /dev/ttyUSB0 -b 115200 flash`
- 按住BOOT键再按RST键进入下载模式

### 3. Wi-Fi连接失败

**排查步骤**：
1. 检查ssid和密码是否正确
2. 查看日志中的错误代码
3. 尝试重启路由器
4. 检查天线连接

### 4. 音频问题

**没有声音**：
- 检查 speaker_io 配置
- 验证功放芯片型号
- 调节音量设置

**唤醒不灵敏**：
- 调节VAD阈值
- 重新训练唤醒词
- 检查麦克风连接

## 测试

### 1. 单元测试

项目使用ESP-IDF的测试框架：
```bash
# 运行所有测试
idf.py test

# 运行特定测试
idf.py test [test_name]
```

### 2. 集成测试

使用GitHub Actions进行CI/CD：
- 多板型自动构建
- 自动化测试
- 发布自动化

### 3. 压力测试

- 长时间运行测试
- 内存泄漏检测
- 网络断连恢复测试

## 贡献指南

1. Fork项目到你的GitHub
2. 创建特性分支：`git checkout -b feature/xxx`
3. 提交代码：`git commit -am 'Add xxx feature'`
4. 推送分支：`git push origin feature/xxx`
5. 创建Pull Request

### 代码规范

- 遵循Google C++代码风格
- 使用clang-format格式化代码
- 添加必要的注释和文档
- 确保通过所有测试

## 许可证

本项目采用MIT许可证，详见[LICENSE](LICENSE)文件。