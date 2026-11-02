# luci-app-ddns-go (hahaher123 fork)

> **引用来源**：本项目基于 [sirpdboy/luci-app-ddns-go](https://github.com/sirpdboy/luci-app-ddns-go) 修改，版权与上游一致（GPL-3.0），感谢原作者 sirpdboy。上游用法与功能说明请看原仓库 README。
>
> **自用修改**（仅满足本人 https 反代环境，不提交回上游）：
> 1. LuCI「操作台」内嵌页改为同源反代相对路径 `/ddns-go/`，废弃 https 拒绝嵌入分支——反代配置由本包自带 `/etc/nginx/conf.d/luci-app-ddns-go.locations` 提供，http/https 自适应，不再被浏览器混合内容策略拦截；
> 2. 「Base Setting」页 Open Web Interface 按钮同步改为 `/ddns-go/`；
> 3. **反代入口仅对内网开放**：`/ddns-go/` 自带 allow/deny（回环 + 私网段，末尾 `deny all`）。ddns-go 自带的「禁止公网访问」按对端 IP 判定，经反代后看到的永远是 `127.0.0.1`，那道闸对反代请求不生效，改由这一层兜住——不要删掉它，删掉即等于把面板放进公网；
> 4. 修 uci-defaults 里 `exit 0` 早于 nginx restart 的顺序问题（原写法让「装完自动 restart nginx」成为死代码，首次安装需手工 restart 才能用 `/ddns-go/`）；
> 5. CI 改为单 job 构建 noarch apk（含 luci-i18n-ddns-go-zh-cn），Release 出 `SNAPSHOT-noarch.tar.gz`；
> 6. ddns-go 内核不在本仓库构建，请配合 [openwrt-ddns-go](https://github.com/hahaher123/openwrt-ddns-go)（UCI 段 `basic` 契约与上游一致）。

## HTTPS 环境说明

完整功能 = LuCI 内嵌面板在 http/https 下都能直接用，依赖 **nginx 链路**：

- **前置**：`nginx-ssl` + `luci-nginx`（LuCI 由 nginx 提供；443 监听与自签证书 `_lan.crt/_lan.key` 由 nginx 安装时自动生成）。
- **本包自带反代**：`/etc/nginx/conf.d/luci-app-ddns-go.locations` 把 `/ddns-go/` 反代到 `127.0.0.1:9876`（含 `proxy_redirect` 修正 ddns-go 登录时的 307）。文件被 `_lan` server 的 `include conf.d/*.locations` 引入，80 / 443 同份生效；uci-defaults 在安装/升级后自动 restart nginx，装上即用。
- **访问范围**：`/ddns-go/` 只接受回环与私网来源（含 IPv6 ULA / 链路本地），其余一律 403。这与 `_lan` server 自带的 `restrict_locally` 是两道独立的闸——本文件不依赖那个 include，单独拿出去用也不会放开到公网。
- **前端**：内嵌 iframe 与「打开网页」按钮固定走相对路径 `/ddns-go/`，自动继承当前页面协议，https 下不再触发浏览器混合内容拦截；直连 `http://<路由器>:9876` 仍可用（该端口由防火墙 WAN 侧 REJECT 兜住）。
- **uhttpd 链路**（LuCI 未走 nginx 时）：反代不存在，内嵌页会 404——此时要么自行按同源反代方式配置，要么不装本包用上游版直连 `http://<路由器>:9876`。
