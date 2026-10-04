# 物光口袋 · 更新源

这个仓库是「物光口袋」的更新发放点，挂在国内静态托管平台
[帽子云](https://www.maoziyun.com/) 上（GitHub 推送即自动部署）。

App 里的「我的 → 软件更新」填的就是这里的 `android.json`（App 出厂默认值）：

```
https://cdn.jsdelivr.net/gh/Nicercz007-cloud/wuguang-pocket-update@main/android.json
```

## 仓库里有什么

| 文件 | 作用 |
|---|---|
| `index.html` | 给人看的下载页（帽子云要求根目录有它） |
| `android.json` | **给 App 看的更新清单** —— 版本号、更新说明、安装包文件名 |
| `wuguang-pocket-2.3.9.apk` | 安装包本体（当前版） |
| `README.md` | 本文件 |

## 以后发新版：跑一个脚本，推四个文件

```bash
node outputs/publish-vNNN.cjs --dry   # 先干跑：本地自检 + 读线上现状，不推
node outputs/publish-vNNN.cjs         # 正式推
```

推送顺序是 **APK → index.html → README.md → android.json**。
清单是"开关"，**最后推** —— 推到就是全好，不会出现"清单已经指向一个还不存在的包"。
推完脚本会自己从线上回读一遍（比字节数、关键字段），再 purge 四个文件的 jsDelivr 缓存。

⚠ 2026-09-27 之前不是这样：清单和 APK 由一个脚本推，下载页和 README 另有两个"附脚本"。
结果是**主脚本每版都跑、附脚本一忙就忘** —— 清单和 APK 一路正常推到 2.2.1，
而下载页的兜底和 README 从 2.1.6 起就没再动过：
**App 能收到更新，人打开下载页看到的却还是两个月前的版本。**

## 推送是怎么走的（别被本地 git 骗了）

推送走 **GitHub Contents API 直推**（`PUT /repos/:owner/:repo/contents/:path`），
**不经过本地 `git commit`**。所以下面三条**全是假信号**：

- 本地 `git log` 停在很早的版本
- 本地 `git status` 里 `android.json` / 包名显示 `M`
- 本地目录里没有最新的包

**判"有没有推上去"只认线上**：Contents API 的内容、目录 API 的字节数、jsDelivr CDN。
（2026-09-27 就是因为只看本地 git，误判成"v43 以后一次都没推过"。）

## 字段说明

| 字段 | 必填 | 说明 |
|---|---|---|
| `versionCode` | ✅ | **判断有没有新版只看它**。必须比已装的包大，否则不提示 |
| `versionName` | ✅ | 给人看的版本号，显示在红点和提示里 |
| `apk` | ✅ | 安装包地址。写文件名即可（会按清单所在目录自动补全），也可以写完整 URL |
| `notes` | | 更新说明，App 的更新弹层里会原样显示 |
| `size` | | 字节数，填了会在按钮上显示「（3.0 MB）」 |

`versionCode` 与 `versionName` 必须和打包时 `app/build.gradle` 里的一致 ——
写错了会出现「提示 4.1.0、装上去还是 4.0.0」这类最难解释的问题。

## 下载页那段兜底

`index.html` 里写死着**一段兜底**：清单一时取不到时（例如用 `file://` 直接打开）
显示的就是它。它是写死的，所以**发新版时也必须跟到同一版**。

正常路径仍只认 `android.json` —— 版本号、按钮指向、体积、更新说明全部从清单读。

不用靠人记得：`outputs/_verify-index-page-v222.cjs` 的 **D 态**会拿 `android.json`
和这段兜底**逐条比**（条数、每条文字、按钮指向的包名），对不上就报红。
`publish-vNNN.cjs` 推之前也会再比一次，不一致直接中止。

## 出错了怎么办

清单格式坏了、或者服务器有问题，可以让 App 显示一句人话：

```json
{ "error": "服务器在维护，稍后再试" }
```
