# chinastudyfree-lzcapp

[聪聪学堂 ChinaStudyFree](https://github.com/tanle-mtr/ChinaStudyFree) 的懒猫微服打包（源码构建模式，仓库 [lazycat-contrib/chinastudyfree-lzcapp](https://github.com/lazycat-contrib/chinastudyfree-lzcapp)，仅发布喵喵商店）。

## 说明

- 源码构建：仓库只存 `UPSTREAM.conf`（固定上游 commit）与 `build.sh`，构建时拉取上游源码（不入库），用改编的上游 Dockerfile 构建单容器（Next.js 静态前端 + Express AI 后端，端口 8787）。
- 在线 AI 能力（出题/讲解/故事/配图/TTS）为可选：安装时填入自己的 API Key 启用，留空自动降级为离线预生成内容。
- 应用无账号体系，无需免密登录注入；已接入懒猫文件选择器拦截（存档导入/导出、分享卡片保存）。
- 升级：改 `UPSTREAM.conf` 的 COMMIT + `package.yml` version 后手动 dispatch workflow。
