# Smart Title Plugin - 3-Part Model Format Support

## 🎯 修改目标

支持 NVIDIA 等提供商使用的 3 段式模型格式：`provider/namespace/model`

## ✅ 完成的修改

### 1. **lib/model-selector.ts** - 核心逻辑修改

#### 修改前：
```typescript
const parts = configModel.split('/')
if (parts.length !== 2) {
    // 只支持 2 段式
}
const [providerID, modelID] = parts
```

#### 修改后：
```typescript
const parts = configModel.split('/')
if (parts.length < 2 || parts.length > 3) {
    // 支持 2-3 段式
}
const providerID = parts[0]
const modelID = parts.slice(1).join('/')  // 重新组合 namespace/model
```

### 2. **FALLBACK_MODELS** - 添加 NVIDIA 支持

```typescript
export const FALLBACK_MODELS: Record<string, string> = {
    // ... 其他提供商
    opencode: 'gpt-5-nano',  // 从 big-pickle 改为更快的 nano
    nvidia: 'nvidia/nvidia-nemotron-nano-9b-v2'  // 新增
};
```

### 3. **PROVIDER_PRIORITY** - 优化优先级

```typescript
const PROVIDER_PRIORITY = [
    'opencode',    // 提升到第一优先级（快速、免费）
    'openai',
    'anthropic',
    'google',
    'nvidia',      // 新增
    // ...
];
```

### 4. **用户配置** - 恢复 NVIDIA 模型

`~/.config/opencode/smart-title.jsonc`:
```jsonc
{
  "model": "nvidia/nvidia/nvidia-nemotron-nano-9b-v2"
}
```

## 📊 支持的格式

| 格式 | 示例 | 提供商 |
|------|------|--------|
| 2-part | `opencode/gpt-5-nano` | OpenCode, OpenAI, Anthropic, Google, etc. |
| 3-part | `nvidia/nvidia/nvidia-nemotron-nano-9b-v2` | NVIDIA |
| 3-part | `nvidia/meta/llama-3.3-70b-instruct` | NVIDIA (Meta models) |

## 🧪 测试结果

```
✅ opencode/gpt-5-nano          → Provider: opencode, Model: gpt-5-nano
✅ nvidia/nvidia/nvidia-nemotron-nano-9b-v2 → Provider: nvidia, Model: nvidia/nvidia-nemotron-nano-9b-v2
✅ quotio/claude-haiku-4.5      → Provider: quotio, Model: claude-haiku-4.5
❌ invalid                       → Invalid format
❌ too/many/parts/here          → Invalid format
```

## 🚀 部署步骤

```bash
cd /Users/w/opencode-smart-title
npm run build
npm link
```

## 📝 预期日志变化

### 修改前：
```json
{"level":"WARN","message":"✗ Invalid config model format, expected \"provider/model\""}
{"level":"INFO","message":"✓ Successfully using fallback model","data":{"providerID":"opencode","modelID":"big-pickle"}}
```

### 修改后：
```json
{"level":"DEBUG","message":"Attempting to use config-specified model","data":{"providerID":"nvidia","modelID":"nvidia/nvidia-nemotron-nano-9b-v2","format":"3-part"}}
{"level":"INFO","message":"✓ Successfully using config-specified model","data":{"providerID":"nvidia","modelID":"nvidia/nvidia-nemotron-nano-9b-v2"}}
```

## 🎉 优势

1. **支持更多模型** - NVIDIA 提供的 80+ 模型现在都可用
2. **无需回退** - 直接使用配置的模型，节省 1.5 秒
3. **向后兼容** - 2 段式格式仍然完全支持
4. **更好的回退** - OpenCode 优先，速度更快

## 📚 可用的 NVIDIA 模型示例

```jsonc
// 轻量级模型（适合标题生成）
"model": "nvidia/nvidia/nvidia-nemotron-nano-9b-v2"

// 代码专用模型
"model": "nvidia/qwen/qwen2.5-coder-7b-instruct"

// 推理模型
"model": "nvidia/deepseek-ai/deepseek-r1"

// Meta Llama 系列
"model": "nvidia/meta/llama-3.3-70b-instruct"
```
