# luci-app-ddns-go (hahaher123 fork)

> **引用来源**：本项目基于 [sirpdboy/luci-app-ddns-go](https://github.com/sirpdboy/luci-app-ddns-go) 修改，版权与上游一致（GPL-3.0），感谢原作者 sirpdboy。上游用法与功能说明请看原仓库 README。
>
> **自用修改**（仅满足本人 https 反代环境，不提交回上游）：
> 1. LuCI「操作台」内嵌页改为同源反代相对路径 `/ddns-go/`，废弃 https 拒绝嵌入分支——反代配置由本包自带 `/etc/nginx/conf.d/luci-app-ddns-go.locations` 提供，http/https 自适应，不再被浏览器混合内容策略拦截；
> 2. 「Base Setting」页 Open Web Interface 按钮同步改为 `/ddns-go/`；
> 3. CI 改为单 job 构建 noarch apk（含 luci-i18n-ddns-go-zh-cn），Release 出 `SNAPSHOT-noarch.tar.gz`；
> 4. ddns-go 内核不在本仓库构建，请配合 [openwrt-ddns-go](https://github.com/hahaher123/openwrt-ddns-go)（UCI 段 `basic` 契约与上游一致）。
