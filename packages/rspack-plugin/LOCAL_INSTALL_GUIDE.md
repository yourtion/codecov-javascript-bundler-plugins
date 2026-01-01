# 本地安装测试指南

## 方法一：使用 pnpm link（推荐）

### 1. 在 rspack-plugin 目录创建全局链接

```bash
cd /Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/rspack-plugin
pnpm link --global
```

### 2. 同时链接 bundler-plugin-core（重要！）

```bash
cd /Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/bundler-plugin-core
pnpm link --global
```

### 3. 在目标项目中链接

```bash
cd /path/to/your/test-project
pnpm link --global @codecov/rspack-plugin
pnpm link --global @codecov/bundler-plugin-core
```

## 方法二：使用 file: 协议（需要同时链接两个包）

**注意**：由于 rspack-plugin 依赖 bundler-plugin-core，你需要同时链接两个包。

在你想要测试的项目的 `package.json` 中添加：

```json
{
  "devDependencies": {
    "@codecov/bundler-plugin-core": "file:/Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/bundler-plugin-core",
    "@codecov/rspack-plugin": "file:/Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/rspack-plugin"
  }
}
```

然后运行：

```bash
pnpm install
```

## 方法三：在 workspace 中测试（如果是 monorepo）

如果你的测试项目也是这个 monorepo 的一部分，可以在根目录的 `package.json` 中添加：

```json
{
  "pnpm": {
    "overrides": {
      "@codecov/rspack-plugin": "workspace:*"
    }
  }
}
```

## 验证安装

安装后，你可以运行以下命令验证：

```bash
# 在测试项目中
pnpm list @codecov/rspack-plugin
```

应该会显示类似：

```
@codecov/rspack-plugin 1.0.0 -> symlink:/Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/rspack-plugin
```

## 使用示例

在你的 Rspack 配置文件中：

```javascript
// rspack.config.js
const { codecovRspackPlugin } = require("@codecov/rspack-plugin");

module.exports = {
  plugins: [
    codecovRspackPlugin({
      enableBundleAnalysis: true,
      bundleName: "test-bundle",
      dryRun: true, // 本地测试建议开启 dryRun
      debug: true,
    }),
  ],
};
```

## 注意事项

1. **修改后重新构建**：每次修改 rspack-plugin 的代码后，需要重新构建：

   ```bash
   cd /Users/yourtion/codes/open/codecov-javascript-bundler-plugins/packages/rspack-plugin
   pnpm build
   ```

2. **热重载**：使用 file: 协议链接的包通常不支持热重载，需要重启测试项目的 dev server

3. **调试**：开启 `debug: true` 和 `dryRun: true` 选项可以方便本地调试
