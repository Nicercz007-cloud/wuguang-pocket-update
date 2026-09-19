# 物光口袋 · 更新分发

这个仓库只放三样东西，**不要往里加别的东西**（多一个 `index.html` 之外的同名入口，或者加一个 `package.json`，都可能让帽子云把站点当成需要构建的项目，从而部署失败）。

| 文件 | 作用 |
|---|---|
| `index.html` | 给人看的下载页（浏览器打开站点域名看到的就是它） |
| `android.json` | 给 App 看的更新清单 |
| `wuguang-pocket-*.apk` | 安装包本体 |

## 发新版怎么做

1. 把新 APK 放进这个仓库，**文件名带新版本号**（例如 `wuguang-pocket-v41.apk`），不要覆盖同名文件；
2. 改 `android.json`：

```json
{
  "versionCode": 30,
  "versionName": "4.1.0",
  "notes": "这一版改了什么（可以换行，用 \\n）",
  "apk": "wuguang-pocket-v41.apk",
  "size": 3142229
}
```

- `versionCode`：**必须比上一版大**，而且要和 APK 里真实的 versionCode 一致。App 只靠它判断有没有新版。
- `versionName`：给人看的，比如 `4.1.0`。
- `apk`：新 APK 的**文件名**（相对路径，写文件名就行）。
- `size`：新 APK 的字节数，只用于显示，写错不致命。

3. 提交。帽子云会自动重新部署，App 下次检查就能看到小红点。

> `versionCode` 忘了加一，用户就永远收不到更新提示 —— 这是最容易犯、也最难发现的错。
> 打包时请核对 `android.json` 的 `versionCode` 与 `aapt dump badging` 出来的 `versionCode` 是否相同。

## 数据说明

这个站点**只用于分发安装包**：访问它的 App 只会下载 `android.json` 和 APK 两个文件，不上传任何数据。
