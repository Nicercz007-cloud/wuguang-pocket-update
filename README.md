# 物光口袋 · 更新源

这个仓库是「物光口袋」的更新发放点，挂在国内静态托管平台
[帽子云](https://www.maoziyun.com/) 上（GitHub 推送即自动部署）。

App 里的「我的 → 软件更新」填的就是这里的 `android.json`：

```
https://<你的子域>.maoziyun.com/android.json
```

## 仓库里有什么

| 文件 | 作用 |
|---|---|
| `index.html` | 给人看的下载页（帽子云要求根目录有它） |
| `android.json` | **给 App 看的更新清单** —— 版本号、更新说明、安装包文件名 |
| `wuguang-pocket-2.0.1.apk` | 安装包本体 |

## 以后发新版，只需要换两个文件

1. 把新的 `wuguang-pocket-2.0.1.apk` 放进来（旧的可以留着，也可以删）
2. 改 `android.json`：

```json
{
  "versionCode": 33,
  "versionName": "2.0.1",
  "notes": "这一版改了什么…（支持 \n 换行）",
  "apk": "wuguang-pocket-2.0.1.apk",
  "size": 3198189
}
```

然后 `git push`。帽子云会自动重新部署，用户下次打开 App 就会冒小红点。

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
