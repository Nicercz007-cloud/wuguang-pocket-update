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
| `wuguang-pocket-2.1.3.apk` | 安装包本体（当前版） |

## 以后发新版，只需要换两个文件

1. 把新的 `wuguang-pocket-2.1.3.apk` 放进来（旧的可以留着，也可以删）
2. 改 `android.json`：

```json
{
  "versionCode": 47,
  "versionName": "2.1.3",
  "notes": "这一版改了什么…（支持 \n 换行）",
  "apk": "wuguang-pocket-2.1.3.apk",
  "size": 0
}
```

然后 `git push`。帽子云会自动重新部署，用户下次打开 App 就会冒小红点。

**`index.html` 正常情况下不用动** —— 版本号、按钮指向、体积、更新说明全部从 `android.json` 读。
（2026-09-22 之前不是这样：更新说明是**写死**的，于是安装包发到 2.0.10 了，
这一页还挂着 2.0.2 的说明 —— 打开看着就像"没上架"。）

⚠ 但页面里还留了**一段兜底**：清单一时取不到时（例如用 `file://` 直接打开）显示的就是它，
它是写死的，所以**发新版时要顺手把它也改成同一版**。
不用靠人记得 —— `outputs/_verify-index-page-v212.cjs` 的 **D 态**会拿 `android.json`
和兜底逐条比，对不上就报红。

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

## 出错了怎么办

清单格式坏了、或者服务器有问题，可以让 App 显示一句人话：

```json
{ "error": "服务器在维护，稍后再试" }
```
