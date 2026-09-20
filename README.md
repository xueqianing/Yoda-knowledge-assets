# Yoda-knowledge-assets

Yoda 个人知识库（本地语义检索）所需的**嵌入模型**与**本地推理运行时**。公开只读，供客户端按需下载。

## 目录结构

```
models/bge-small-zh-v1.5/              嵌入模型（transformers.js ONNX 版）
├─ config.json
├─ tokenizer.json
├─ tokenizer_config.json
├─ special_tokens_map.json
└─ onnx/model_quantized.onnx           23MB（客户端写死 dtype:q8，只加载这一份）

runtimes/embedding-runtime/v1/         本地推理运行时（按平台分发）
├─ darwin-arm64.tar.gz                 11MB  macOS (Apple Silicon)
├─ win32-x64.tar.gz                    27MB  Windows x64
└─ manifest.json                       各平台 sha256 与字节数
```

## 说明

- **为什么运行时要单独下载**：跑 ONNX 模型需要原生推理引擎（mac 是 `.dylib`、win 是 `.dll`），
  全平台合计 200MB+，不能打进安装包，故按平台分发、首次使用语义检索时下载。
- **为什么用分支 + raw URL 而不是 Release 资产**：模型文件名带斜杠（`onnx/model_quantized.onnx`），
  Release 资产会扁平化文件名；raw URL 保留路径，模型与运行时包得以共用同一套「前缀 + 文件」约定。
- **校验**：客户端内置各文件 sha256；`runtimes/.../manifest.json` 记录运行时包哈希。
- **构建可复现**：运行时包在归一化 mtime 后用 `gzip -n` 打包，同样内容产出稳定哈希。
- 国内直连 `raw.githubusercontent.com` 可能较慢；客户端会优先走内网 HTTP 源。

## 内容版本

| 组件 | 版本 |
|---|---|
| @huggingface/transformers | 4.2.0 |
| onnxruntime | 1.24.3 |
| bge-small-zh-v1.5 | Xenova ONNX（q8 量化） |
