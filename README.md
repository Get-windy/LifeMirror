# LifeMirror

**Your Digital Life Twin / 你的数字人生孪生**

LifeMirror helps you build a complete digital replica of your life. It securely stores events, relationships, media, and stories, then weaves them into an interactive timeline, a family tree, and a rich gallery – all under your full control. Designed with privacy, family heritage, and AI‑powered intelligence at its core.

LifeMirror 帮助你构建完整的数字人生孪生。它安全地存储你一生中的事件、关系、影像和故事，并将其编织成交互式时间线、家族树和丰富的多媒体画廊——所有数据都由你完全掌控。其核心设计围绕隐私、家族传承和 AI 智能展开。

---

## ✨ Features / 特性

- **Life Timeline / 人生时间线** – Record events with rich text, location, and media attachments. Navigate through years, search, and filter.  
  **人生时间线**：用富文本、地点和媒体附件记录事件。可按年份浏览、搜索和过滤。

- **Family Tree & Kinship / 家族树与亲属关系** – Build multi‑generational trees with complex Chinese kinship relations (adoption, uxorilocal marriage, joint heir, etc.). Compute *wufu* (五服) relationships automatically.  
  **家族树与亲属关系**：构建多代家族树，支持中国宗族复杂的亲属关系（过继、入赘、兼祧等）。自动计算五服关系。

- **Multimedia Vault / 多媒体库** – Import photos, videos, audio, and documents. AI automatically tags faces, transcribes speech, and extracts scenes.  
  **多媒体库**：导入照片、视频、音频和文档。AI 自动标注人脸、转录语音和识别场景。

- **Privacy First / 隐私至上** – All data is encrypted locally with hardware‑backed keys (Android Keystore, iOS Keychain, TPM). You control who sees what, even after death (time‑locked secrets).  
  **隐私至上**：所有数据均使用硬件级密钥（Android Keystore、iOS Keychain、TPM）在本地加密。你可以控制谁能看到什么，甚至死后也能通过时间锁保护敏感资料。

- **Offline‑First / 离线优先** – No cloud dependency. Your data stays on your devices. Sync manually if you wish.  
  **离线优先**：无需依赖云端。数据只保留在你的设备上。可按需手动同步。

- **AI‑Powered / AI 赋能** – Local AI models (ONNX Runtime) analyze your content: face recognition, sentiment analysis, speech‑to‑text, and more – without sending data anywhere.  
  **AI 赋能**：本地 AI 模型（ONNX Runtime）分析你的内容：人脸识别、情感分析、语音转文字等——数据无需离开你的设备。

- **Legacy & Inheritance / 数字遗产与传承** – Define time locks for sensitive items, split keys among trusted contacts, and prepare your digital heritage for future generations.  
  **数字遗产与传承**：为敏感资料设置时间锁，将密钥分发给可信联系人，为后代准备你的数字遗产。

---

## 🏗️ Architecture / 架构

LifeMirror is built as a **Rust core** with platform‑specific UIs (Flutter for mobile/desktop/web). All storage is local and encrypted.

LifeMirror 采用 **Rust 核心** 加跨平台 UI（Flutter 支持移动/桌面/Web）的架构。所有存储均为本地加密。

```
┌─────────────────────────────────────────────┐
│              Frontend (Flutter)              │
│  Timeline · Family Tree · Gallery · Maps     │
└───────────────────────┬─────────────────────┘
                        │ dart:ffi
┌───────────────────────▼─────────────────────┐
│           Rust Core (lifemirror_core)        │
│  • Data models (Event, Person, Relation)     │
│  • Encryption engine (Tink + hardware keys)  │
│  • Graph engine (Neo4j / SQLite)             │
│  • Media index (MinIO)                        │
│  • AI proxy (ONNX Runtime)                    │
│  • Time‑lock & export                          │
└───────────────────────┬─────────────────────┘
                        │
┌───────────────────────▼─────────────────────┐
│               Local Storage                   │
│  ┌──────────┐  ┌────────┐  ┌────────────┐   │
│  │ SQLCipher│  │ MinIO  │  │ Neo4j (opt)│   │
│  │ (events, │  │(photos,│  │ (graph)    │   │
│  │  people) │  │ videos)│  │            │   │
│  └──────────┘  └────────┘  └────────────┘   │
└─────────────────────────────────────────────┘
```

- **Rust core** ensures memory safety, high performance, and easy cross‑platform integration.  
  **Rust 核心**：保证内存安全、高性能且易于跨平台集成。
- **SQLCipher** transparently encrypts all structured data.  
  **SQLCipher**：透明加密所有结构化数据。
- **MinIO** stores media files, each encrypted with a per‑file key.  
  **MinIO**：存储媒体文件，每个文件使用独立密钥加密。
- **Neo4j** (optional) powers advanced kinship queries on desktop; mobile uses a simplified relational schema.  
  **Neo4j**（可选）：在桌面端支持高级亲属关系查询；移动端使用简化关系模式。
- **Tink** provides standardised, audited cryptography.  
  **Tink**：提供标准化、经过审计的加密算法。
- **ONNX Runtime** runs AI models locally – no data ever leaves your device.  
  **ONNX Runtime**：本地运行 AI 模型，数据永不离开设备。

---

## 🚀 Quick Start / 快速开始

### Prerequisites / 前置要求
- Rust 1.70+ (for core / 用于核心库)
- Flutter 3.x (for UI / 用于 UI)
- CMake, Ninja (for building some dependencies / 用于构建部分依赖)

### Build the Rust core / 构建 Rust 核心
```bash
cd lifemirror_core
cargo build --release
```

### Build the Flutter app / 构建 Flutter 应用
```bash
cd flutter_app
flutter pub get
flutter run
```

> Detailed build instructions for each platform can be found in [BUILD.md](BUILD.md).  
> 各平台的详细构建说明请参阅 [BUILD.md](BUILD.md)。

---

## 🧱 Project Structure / 项目结构

```
.
├── lifemirror_core/          # Rust core library / Rust 核心库
│   ├── src/
│   │   ├── models/           # Data structures (Event, Person, …) / 数据结构
│   │   ├── crypto/            # Encryption (Tink + platform keys) / 加密
│   │   ├── graph/             # Neo4j / SQLite graph store / 图谱存储
│   │   ├── media/             # MinIO client, thumbnailing / 媒体客户端
│   │   ├── ai/                 # ONNX Runtime integration / AI 集成
│   │   └── time_lock/         # Shamir secret sharing, timestamps / 时间锁
│   └── Cargo.toml
├── flutter_app/               # Flutter UI
│   ├── lib/
│   │   ├── screens/           # Timeline, family tree, gallery, maps / 界面
│   │   ├── widgets/           # Reusable UI components / 可复用组件
│   │   └── ffi/               # Dart FFI bindings to Rust core / FFI 绑定
│   └── pubspec.yaml
├── docs/                       # Additional documentation / 额外文档
└── README.md
```

---

## 🔐 Privacy & Security / 隐私与安全

- **Hardware‑isolated keys** – Master key never leaves secure hardware (TPM, Secure Enclave, etc.).  
  **硬件隔离密钥**：主密钥永不离开安全硬件（TPM、安全隔区等）。
- **Per‑file encryption** – Each photo, video, and document gets its own random key, itself encrypted by the master key.  
  **文件级加密**：每个照片、视频和文档使用独立随机密钥，该密钥再由主密钥加密。
- **Biometric authentication** – Optionally require fingerprint / face ID for sensitive operations.  
  **生物认证**：可为敏感操作选择要求指纹/面容 ID。
- **Time‑locked secrets** – Split a secret among trusted contacts so it can only be recovered after a certain date or condition (e.g., after death).  
  **时间锁秘密**：将秘密拆分成多份分发给可信联系人，只有在特定日期或条件（如去世后）才能恢复。
- **Local‑first** – No cloud account, no central server. You own your data.  
  **本地优先**：无云账户，无中心服务器。你拥有你的数据。

---

## 🤝 Contributing / 贡献

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

我们欢迎各种形式的贡献！请参阅 [CONTRIBUTING.md](CONTRIBUTING.md) 了解贡献指南。

- **Report bugs** – Open an issue with a clear description and reproduction steps.  
  **报告 Bug**：提交 issue，附上清晰描述和重现步骤。
- **Suggest features** – Use the "enhancement" label.  
  **建议功能**：使用 "enhancement" 标签。
- **Submit pull requests** – For bug fixes, new features, or improvements.  
  **提交 Pull Request**：用于修复 bug、添加新功能或改进。

---

## 📄 License / 许可证

LifeMirror is licensed under the **Apache 2.0 License**.  
See [LICENSE](LICENSE) for details.

LifeMirror 采用 **Apache 2.0 许可证**。  
详情请参阅 [LICENSE](LICENSE)。

---

## 📬 Contact / 联系方式

- GitHub Issues – for questions and discussions / 用于提问和讨论
- Email: [team@lifemirror.org](mailto:team@lifemirror.org) (placeholder / 暂定)

---

**LifeMirror – mirror your life, preserve your legacy.**  
**LifeMirror – 镜像人生，传承记忆。**