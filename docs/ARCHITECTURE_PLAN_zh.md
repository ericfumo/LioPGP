````markdown
# 🏗️ LioPGP 架構規劃 / LioPGP 架构规划

**最後更新 / 最后更新**: 2026-05-28  
**狀態 / 状态**: 草案 / 草案  
**作者 / 作者**: @ericfumo

---

## 📐 核心項目分析 / 核心项目分析

### 🔵 ProtonMail/gopenpgp (Go)

**倉庫信息 / 仓库信息**:
- 語言 / 语言: Go
- 版本 / 版本: V3 (RFC9580 支援)
- Stars / Stars: 1,224
- 開源協議 / 开源协议: MIT License
- 網站 / 网站: https://gopenpgp.org

**主要特性 / 主要特性**:
- ✅ 高階 OpenPGP API (RFC4880 + RFC9580)
- ✅ PQC 預留支援 (ML-KEM, ML-DSA)
- ✅ Go Mobile 編譯支援 (gomobile)
- ✅ 流式加密/解密
- ✅ 多收件人加密
- ✅ 隱藏收件人支援
- ✅ 分離式簽名

**密鑰生成支援 / 密钥生成支持**:
```go
// RFC9580 Profile
- Curve25519 v6 (推薦 / 推荐)
- Curve448 v6
- ML-KEM-1024 (PQC)
- ML-DSA-65 (PQC 簽名 / PQC 签名)
```

### 🟠 open-keychain/open-keychain (Java/Android)

**倉庫信息 / 仓库信息**:
- 語言 / 语言: Java/Kotlin
- 開源協議 / 开源协议: GPLv3
- Stars / Stars: 2,563
- Forks / Forks: 524
- 狀態 / 状态: ⚠️ 停止積極開發（接受安全修補）
- 網站 / 网站: https://www.openkeychain.org

**核心實現 / 核心实现**:
- Bouncy Castle 分支 (含 PGP 擴展)
- Material Design v2
- OpenPGP API (第三方應用集成 / 第三方应用集成)
- SSH 身份認證 API
- 完整密鑰管理 UI

---

## 🏛️ LioPGP 整合架構 / LioPGP 集成架构

### 架構圖 / 架构图

```
┌─────────────────────────────────────────────────────────────────┐
│                    LioPGP Android App                           │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Layer 1: UI 層 (Jetpack Compose + Material Design 3)           │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ Screens:                                                    ││
│  │  • KeyListScreen          - 密鑰列表 / 密钥列表             ││
│  │  • KeyDetailScreen        - 密鑰詳情 / 密钥详情             ││
│  │  • EncryptScreen          - 加密介面 / 加密界面             ││
│  │  • DecryptScreen          - 解密介面 / 解密界面             ││
│  │  • PQCSettingsScreen      - PQC 設定 / PQC 设置             ││
│  │                                                              ││
│  │ Components:                                                 ││
│  │  • KeyCard                - 密鑰卡片 / 密钥卡片             ││
│  │  • AlgorithmBadge         - 演算法徽章 / 算法徽章           ││
│  │  • StatusIndicator        - 狀態指示 / 状态指示             ││
│  │  • FingerprintDisplay     - 指紋顯示 / 指纹显示             ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Layer 2: 業務邏輯層 (Kotlin)                                   │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ ViewModels:                                                 ││
│  │  • KeyListViewModel       - 密鑰列表邏輯 / 密钥列表逻辑     ││
│  │  • CryptoViewModel        - 加密操作邏輯 / 加密操作逻辑     ││
│  │  • PQCViewModel           - PQC 管理邏輯 / PQC 管理逻辑     ││
│  │                                                              ││
│  │ Repositories:                                               ││
│  │  • KeyRepository          - 密鑰數據層 / 密钥数据层         ││
│  │  • CryptoRepository       - 加密操作層 / 加密操作层         ││
│  │                                                              ││
│  │ Use Cases:                                                  ││
│  │  • GenerateKeyUseCase     - 生成密鑰 / 生成密钥             ││
│  │  • EncryptUseCase         - 加密用例 / 加密用例             ││
│  │  • DecryptUseCase         - 解密用例 / 解密用例             ││
│  │  • DetectPQCUseCase       - 偵測 PQC / 侦测 PQC             ││
│  │  • ImportKeyUseCase       - 匯入密鑰 / 汇入密钥             ││
│  │  • ExportKeyUseCase       - 匯出密鑰 / 汇出密钥             ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Layer 3: JNI 橋接層 (Kotlin/Java)                              │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ GopenPGPBridge.kt:                                          ││
│  │  ✓ initCrypto()                                             ││
│  │  ✓ generateKey(name, email, passphrase, algorithm)          ││
│  │  ✓ encryptData(data, publicKey, withSignature)              ││
│  │  ✓ decryptData(data, privateKey, passphrase)                ││
│  │  ✓ signData(data, privateKey, passphrase)                   ││
│  │  ✓ verifySignature(data, signature, publicKey)              ││
│  │  ✓ getPQCInfo() -> PQCInfo                                  ││
│  │  ✓ getKeyFingerprint(armored) -> String                     ││
│  │                                                              ││
│  │ Error Handling:                                             ││
│  │  • CryptoException         - 加密異常 / 加密异常             ││
│  │  • KeyException            - 密鑰異常 / 密钥异常             ││
│  │  • PQCNotSupportedException - PQC 不支援 / PQC 不支持       ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Layer 4: Go Crypto 層 (gopenpgp v3)                            │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ gopenpgp_wrapper.go (gomobile 編譯 / 编译):                 ││
│  │                                                              ││
│  │ PQC Key Generation / PQC 密钥生成:                          ││
│  │  • GeneratePQCKey(name, email, passphrase)                  ││
│  │  • GenerateRSAKey(...)                                      ││
│  │  • GenerateECCKey(...)                                      ││
│  │  • GenerateHybridKey(...) [RSA + ML-KEM]                    ││
│  │                                                              ││
│  │ Encryption/Decryption / 加密/解密:                          ││
│  │  • EncryptAndSign(plaintext, recipients, signer)            ││
│  │  • DecryptAndVerify(ciphertext, privateKey, verifiers)      ││
│  │  • StreamEncrypt(reader, recipients, signer)                ││
│  │  • StreamDecrypt(reader, privateKey, verifiers)             ││
│  │                                                              ││
│  │ Fingerprint Handling / 指纹处理:                            ││
│  │  • GetFingerprintInfo(armored) -> FingerprintInfo           ││
│  │  • CompareFingerprintLength(key1, key2)                     ││
│  │  • HashFingerprint(armored) -> String                       ││
│  │                                                              ││
│  │ PQC Support Detection / PQC 支持侦测:                       ││
│  │  • GetPQCInfo() -> PQCInfo                                  ││
│  │  • IsPQCSupported() -> bool                                 ││
│  │  • GetSupportedAlgorithms() -> []string                     ││
│  │                                                              ││
│  │ Dependencies:                                               ││
│  │  ✓ github.com/ProtonMail/gopenpgp/v3/crypto                ││
│  │  ✓ github.com/ProtonMail/gopenpgp/v3/profile               ││
│  │  ✓ github.com/ProtonMail/gopenpgp/v3/constants             ││
│  │  ✓ github.com/ProtonMail/go-crypto (RSA, ECC, ML-KEM)      ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  Layer 5: Data Persistence (Room Database)                      │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ Entities:                                                   ││
│  │  • KeyEntity               - 密鑰記錄 / 密钥记录             ││
│  │  • CertificateMetadata     - 證書元數據 / 证书元数据         ││
│  │  • OperationLog            - 操作日誌 / 操作日志             ││
│  │                                                              ││
│  │ DAOs:                                                       ││
│  │  • KeyDao                  - 密鑰 CRUD / 密钥 CRUD          ││
│  │  • MetadataDao             - 元數據 / 元数据                 ││
│  │                                                              ││
│  │ Database:                                                   ││
│  │  • LioPGPDatabase (Room)   - 本地 SQLite / 本地 SQLite     ││
│  └─────────────────────────────────────────────────────────────┘│
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 檔案結構 / 文件结构

```
LioPGP/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/ericfumo/liopgp/
│   │   │   │   ├── crypto/
│   │   │   │   │   ├── GopenPGPBridge.kt          # JNI 橋接
│   │   │   │   │   ├── PQCManager.kt              # PQC 管理
│   │   │   │   │   ├── CryptoException.kt         # 例外定義
│   │   │   │   │   └── PQCInfo.kt                 # PQC 信息模型
│   │   │   │   ├── data/
│   │   │   │   │   ├── model/
│   │   │   │   │   │   ├── PgpKey.kt
│   │   │   │   │   │   ├── CertificateMetadata.kt
│   │   │   │   │   │   └── OperationResult.kt
│   │   │   │   │   ├── database/
│   │   │   │   │   │   ├── LioPGPDatabase.kt
│   │   │   │   │   │   ├── KeyEntity.kt
│   │   │   │   │   │   └── KeyDao.kt
│   │   │   │   │   └── repository/
│   │   │   │   │       ├── KeyRepository.kt
│   │   │   │   │       └── CryptoRepository.kt
│   │   │   │   ├── domain/
│   │   │   │   │   ├── usecase/
│   │   │   │   │   │   ├── GenerateKeyUseCase.kt
│   │   │   │   │   │   ├── EncryptUseCase.kt
│   │   │   │   │   │   ├── DecryptUseCase.kt
│   │   │   │   │   │   ├── DetectPQCUseCase.kt
│   │   │   │   │   │   ├── ImportKeyUseCase.kt
│   │   │   │   │   │   └── ExportKeyUseCase.kt
│   │   │   │   │   └── model/
│   │   │   │   │       └── DomainModels.kt
│   │   │   │   ├── presentation/
│   │   │   │   │   ├── ui/
│   │   │   │   │   │   ├── screens/
│   │   │   │   │   │   │   ├── KeyListScreen.kt
│   │   │   │   │   │   │   ├── KeyDetailScreen.kt
│   │   │   │   │   │   │   ├── EncryptScreen.kt
│   │   │   │   │   │   │   ├── DecryptScreen.kt
│   │   │   │   │   │   │   └── PQCSettingsScreen.kt
│   │   │   │   │   │   ├── components/
│   │   │   │   │   │   │   ├── KeyCard.kt
│   │   │   │   │   │   │   ├── AlgorithmBadge.kt
│   │   │   │   │   │   │   ├── StatusIndicator.kt
│   │   │   │   │   │   │   └── FingerprintDisplay.kt
│   │   │   │   │   │   └── theme/
│   │   │   │   │   │       ├── Color.kt
│   │   │   │   │   │       ├── Typography.kt
│   │   │   │   │   │       ├── Shape.kt
│   │   │   │   │   │       └── Theme.kt
│   │   │   │   │   ├── viewmodel/
│   │   │   │   │   │   ├── KeyListViewModel.kt
│   │   │   │   │   │   ├── CryptoViewModel.kt
│   │   │   │   │   │   └── PQCViewModel.kt
│   │   │   │   │   └── MainActivity.kt
│   │   │   │   ├── di/
│   │   │   │   │   ├── RepositoryModule.kt        # Hilt 配置
│   │   │   │   │   └── DatabaseModule.kt
│   │   │   │   └── MyApplication.kt
│   │   │   └── res/
│   │   │       ├── values/
│   │   │       ├── values-zh-rTW/
│   │   │       └── values-zh-rCN/
│   ├── libs/
│   │   └── gopenpgp.aar                            # Go 編譯輸出
│   └── build.gradle.kts
│
├── android/
│   ├── gopenpgp_wrapper.go                         # Go wrapper for Android
│   ├── gopenpgp_wrapper_test.go
│   └── build.sh                                    # gomobile 編譯腳本
│
├── ios/
│   ├── gopenpgp_wrapper.go                         # Go wrapper for iOS
│   └── build.sh
│
├── docs/
│   ├── ARCHITECTURE_PLAN_zh.md                     # 本文件
│   ├── DESIGN_SYSTEM.md
│   ├── DESIGN_SYSTEM_zh.md
│   ├── PQC_COMPATIBILITY.md
│   ├── API_DESIGN.md
│   └── SECURITY_GUIDELINES.md
│
└── build.gradle.kts (root)
```

---

## 🔄 與 OpenKeychain 對標 / 与 OpenKeychain 对标

| 功能 / 功能 | OpenKeychain | LioPGP | 優勢 / 优势 |
|----------|--------------|--------|----------|
| **Cryptography Library** | Bouncy Castle (Java) | gopenpgp v3 (Go) | ✅ 輕量級、模塊化 |
| **UI Framework** | Material Design v2 | Material Design v3 | ✅ 現代化、動態主題 |
| **PQC 支援** | ❌ 無 | ✅ ML-KEM/ML-DSA | ✅ 後量子安全 |
| **RFC9580** | ❌ 無 | ✅ 完全支援 | ✅ 最新標準 |
| **指紋長度** | 40 字元 (固定) | 可變 (64+ for PQC) | ✅ 適應算法 |
| **維護狀態** | ⚠️ 停止開發 | 🆕 活躍開發 | ✅ 長期支援 |
| **語言** | Java (難以優化) | Kotlin UI + Go Crypto | ✅ 性能更好 |
| **Go Mobile** | ❌ 不支持 | ✅ 完全支援 | ✅ 跨平台 |
| **社區貢獻** | 524 Forks | 新項目 | 🔄 建立中 |

---

## 📊 密鑰演算法支援矩陣 / 密钥算法支持矩阵

### RFC4880 (傳統 / 传统)
```
✓ RSA-3072 (不推薦 / 不推荐)
✓ RSA-4096 (現有系統 / 现有系统)
✓ Curve25519 v4 (推薦 / 推荐)
✓ Curve448 v4 (高強度 / 高强度)
```

### RFC9580 (Crypto Refresh)
```
✓ Curve25519 v6 (推薦 / 推荐)
✓ Curve448 v6 (高強度 / 高强度)
✓ Ed25519 v6 (簽名 / 签名)
✓ Ed448 v6 (簽名 / 签名)
```

### PQC (後量子 / 后量子)
```
✓ ML-KEM-512 (中等強度 / 中等强度)
✓ ML-KEM-1024 (推薦 / 推荐)
✓ ML-DSA-44 (簽名 / 签名)
✓ ML-DSA-65 (推薦 / 推荐)

Planned / 計劃:
⊘ SLH-DSA-SHA2-128s
⊘ SLH-DSA-SHA2-256f
⊘ Kyber (CRYSTALS - 前 NIST 標準)
```

### 混合密鑰 (Hybrid / 混合)
```
Supported by gopenpgp / gopenpgp 支持:
✓ (RSA-4096 + ML-KEM-1024)
✓ (Curve25519 + ML-KEM-1024)
✓ (Curve25519 v6 + ML-KEM-1024)
```

---

## 🔐 PQC 相容性考量 / PQC 相容性考量

### 指紋長度變化 / 指纹长度变化

**傳統 RSA-4096 / 传统 RSA-4096**:
- 指紋: 40 個十六進位字元 (160 bits / 20 bytes)
- 格式: `ABC1 2345 678A 90BC DEF0 1234 5678 90AB CDEF 0123`

**PQC ML-KEM-1024 / PQC ML-KEM-1024**:
- 指紋: 64 個十六進位字元 (256 bits / 32 bytes)
- 格式: `ABC1 2345 678A 90BC DEF0 1234 5678 90AB CDEF 0123 4567 89AB CDEF 0123 4567 89AB`

**UI 影響 / UI 影响**:
- [ ] 指紋卡片需要自動換行 / 自动换行
- [ ] QR 碼編碼密度增加 / 编码密度增加
- [ ] 複製/分享功能需要優化 / 优化
- [ ] 數據庫欄位須擴大 (VARCHAR(512)) / 扩大

### 密鑰材料大小 / 密钥材料大小

| 演算法 / 算法 | 公鑰 / 公钥 | 私鑰 / 私钥 | 簽名 / 签名 | 加密 / 加密 |
|-----------|---------|---------|---------|---------|
| RSA-4096 | ~550 B | ~3.2 KB | 512 B | 512 B |
| ECC-P256 | 65 B | 32 B | 64 B | 32 B |
| ML-KEM-1024 | 1568 B | 3168 B | N/A | 1088 B |
| ML-DSA-65 | 1952 B | 4032 B | 4595 B | N/A |

**存儲影響 / 存储影响**:
- 密鑰存儲: +3-6x (需要優化 / 优化)
- 傳輸頻寬: +3-6x (考慮壓縮 / 压缩)
- 堆記憶體: 需要基準測試 / 基准测试

---

## 🛠️ 開發工具鏈 / 开发工具链

### 必需工具 / 必需工具

```bash
# Android SDK
- compileSdkVersion: 34
- minSdkVersion: 24 (Android 7.0+)
- targetSdkVersion: 34

# Go Development
- Go 1.21+
- GoMobile (gomobile)
  $ go install golang.org/x/mobile/cmd/gomobile@latest
  $ gomobile init

# Kotlin/Java
- Android Studio 2024.1+
- JDK 17+

# Build Tools
- Gradle 8.2+
- Android Gradle Plugin 8.1+
```

### 編譯 gopenpgp / 编译 gopenpgp

```bash
# 構建 Android AAR / 构建 Android AAR
$ cd android/
$ sh build.sh

# 構建 iOS Framework / 构建 iOS Framework
$ cd ios/
$ sh build.sh
```

---

## 📈 實施時程 / 实施时程

### Phase 1: 基礎架構 (第 1-2 週 / 第 1-2 周)

**目標 / 目标**: 建立 JNI 橋接與數據庫層

- [ ] 設置 Go 開發環境 / 设置
- [ ] 編譯 gopenpgp AAR / 编译
- [ ] 實現 GopenPGPBridge.kt / 实现
- [ ] 建立 Room 數據庫架構 / 建立
- [ ] 基礎密鑰生成 (RSA + ECC) / 基础
- [ ] 單元測試框架 / 单元测试框架

**交付物 / 交付物**:
- ✓ `gopenpgp.aar` (gomobile 編譯)
- ✓ `GopenPGPBridge.kt`
- ✓ `LioPGPDatabase` + `KeyEntity`

### Phase 2: PQC 整合 (第 3-5 週 / 第 3-5 周)

**目標 / 目标**: 實現 PQC 密鑰生成與相容性

- [ ] PQC 密鑰生成 (ML-KEM-1024 + ML-DSA-65) / 实现
- [ ] 指紋長度適應 / 适应
- [ ] PQC 信息檢測 / 检测
- [ ] RFC9580 相容性測試 / 测试
- [ ] 混合密鑰支援 / 支持
- [ ] 集成測試 / 集成测试

**交付物 / 交付物**:
- ✓ `PQCManager.kt`
- ✓ `GeneratePQCKey()` Go 函數
- ✓ PQC 相容性測試套件

### Phase 3: UI 實現 (第 6-7 週 / 第 6-7 周)

**目標 / 目标**: Material Design 3 完整 UI

- [ ] 密鑰列表界面 / 界面
- [ ] 密鑰詳情視圖 / 视图
- [ ] 加密/解密工作流 / 工作流
- [ ] PQC 設定界面 / 界面
- [ ] 狀態指示器 & 算法徽章 / 徽章
- [ ] UI 測試 (Compose Testing) / 测试

**交付物 / 交付物**:
- ✓ 完整 Material Design 3 實現
- ✓ 所有屏幕組件
- ✓ UI 測試覆蓋

### Phase 4: 優化與安全稽核 (第 8 週 / 第 8 周)

**目標 / 目标**: 效能優化與安全加固

- [ ] 效能基準測試 / 基准测试
- [ ] 記憶體洩漏檢查 / 检查
- [ ] 安全程式碼稽核 / 稽核
- [ ] 防止副信道攻擊 / 攻击
- [ ] Proguard/R8 難化配置 / 配置
- [ ] 測試覆蓋率 > 80% / 80%

**交付物 / 交付物**:
- ✓ 效能報告
- ✓ 安全稽核報告
- ✓ Release APK

---

## 🚀 版本路線圖 / 版本路线图

```
v0.1 (Beta)         - PQC 支援 Proof-of-Concept / PoC
├─ 基礎密鑰管理
├─ PQC 密鑰生成
└─ 簡單的加密/解密

v0.5 (Alpha)        - 完整功能
├─ Material Design 3 UI
├─ 混合密鑰支援
├─ RFC9580 完全相容
└─ 基本 OpenPGP API

v1.0 (Release)      - 生產就緒
├─ 完整文檔
├─ 安全稽核通過
├─ F-Droid & Play Store
└─ 社區支援

v2.0 (Future)       - 進階特性
├─ SSH 身份驗證 API
├─ OpenPGP API
├─ 更多 PQC 算法
└─ 硬體安全模塊 (SE)
```

---

## 📚 參考資源 / 参考资源

### gopenpgp 文檔 / gopenpgp 文档
- [GopenPGP API Docs](https://pkg.go.dev/github.com/ProtonMail/gopenpgp/v3)
- [go-crypto Module](https://github.com/ProtonMail/go-crypto)
- [RFC9580 - OpenPGP Crypto Refresh](https://www.rfc-editor.org/rfc/rfc9580.html)

### OpenKeychain 參考 / OpenKeychain 参考
- [GitHub Repository](https://github.com/open-keychain/open-keychain)
- [Architecture Documentation](https://github.com/open-keychain/open-keychain/wiki)
- [Material Design Implementation](https://www.openkeychain.org/)

### PQC 標準 / PQC 标准
- [NIST PQC Standardization](https://csrc.nist.gov/projects/post-quantum-cryptography)
- [ML-KEM Specification](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf)
- [ML-DSA Specification](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.204.pdf)

### Android 開發 / Android 开发
- [Jetpack Compose Documentation](https://developer.android.com/jetpack/compose)
- [Material Design 3 Spec](https://m3.material.io/)
- [Android Security Best Practices](https://developer.android.com/training/articles/security-best-practices)

---

## 📝 相關文檔 / 相关文档

- 📄 [DESIGN_SYSTEM.md](./DESIGN_SYSTEM.md) - Material Design 3 詳細指南
- 📄 [PQC_COMPATIBILITY.md](./PQC_COMPATIBILITY.md) - PQC 相容性研究
- 📄 [API_DESIGN.md](./API_DESIGN.md) - JNI/FFI API 設計
- 📄 [SECURITY_GUIDELINES.md](./SECURITY_GUIDELINES.md) - 安全指南

---

**最後更新 / 最后更新**: 2026-05-28  
**狀態 / 状态**: ✍️ 草案 / 草案 (等待社區反饋 / 等待社区反馈)

**下一步 / 下一步**:
1. 社區評審 / 社区评审
2. 建立子任務 GitHub Issues
3. 開始 Phase 1 開發 / 开始
````
