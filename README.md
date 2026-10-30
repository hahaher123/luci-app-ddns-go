# luci-app-ddns-go (hahaher123 fork)

> **引用来源**：本项目基于 [sirpdboy/luci-app-ddns-go](https://github.com/sirpdboy/luci-app-ddns-go) 修改，版权与上游一致（GPL-3.0），感谢原作者 sirpdboy。上游用法与功能说明请看原仓库 README。
>
> **自用修改**（仅满足本人 https 反代环境，不提交回上游）：
> 1. LuCI「操作台」内嵌页改为同源反代相对路径 `/ddns-go/`，废弃 https 拒绝嵌入分支——反代配置由本包自带 `/etc/nginx/conf.d/luci-app-ddns-go.locations` 提供，http/https 自适应，不再被浏览器混合内容策略拦截；
> 2. 「Base Setting」页 Open Web Interface 按钮同步改为 `/ddns-go/`；
> 3. CI 改为单 job 构建 noarch apk（含 luci-i18n-ddns-go-zh-cn），Release 出 `SNAPSHOT-noarch.tar.gz`；
> 4. ddns-go 内核不在本仓库构建，请配合 [openwrt-ddns-go](https://github.com/hahaher123/openwrt-ddns-go)（UCI 段 `basic` 契约与上游一致）。

## HTTPS 环境说明

完整功能 = LuCI 内嵌面板在 http/https 下都能直接用，依赖 **nginx 链路**：

- **前置**：`nginx-ssl` + `luci-nginx`（LuCI 由 nginx 提供；443 监听与自签证书 `_lan.crt/_lan.key` 由 nginx 安装时自动生成）。
- **本包自带反代**：`/etc/nginx/conf.d/luci-app-ddns-go.locations` 把 `/ddns-go/` 反代到 `127.0.0.1:9876`（含 `proxy_redirect` 修正 ddns-go 登录时的相对 302）。文件被 `_lan` server 的 `include conf.d/*.locations` 引入，80 / 443 同份生效；uci-defaults 在安装/升级后自动 restart nginx，装上即用。
- **前端**：内嵌 iframe 与「打开网页」按钮固定走相对路径 `/ddns-go/`，自动继承当前页面协议，https 下不再触发浏览器混合内容拦截；直连 `http://<路由器>:9876` 仍可用。
- **uhttpd 链路**（LuCI 未走 nginx 时）：反代不存在，内嵌页会 404——此时要么自行按同源反代方式配置，要么不装本包用上游版直连 `http://<路由器>:9876`。
