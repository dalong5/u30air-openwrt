# U30 Air OpenWrt 私有优化档案

设备：ZTE U30 Air / Unisoc UMS9620。系统：MU300 项目的 OpenWrt 25.12.5，内核 6.18.54。

这是本人设备的配置与系统恢复档案，不是适配任意 U30 Air 的官方固件。

## 已保存内容

- OpenClash v0.48.002、Mihomo v1.19.33 ARM64。
- anti-AD 与秋风 AWAvenue 规则提供者配置，24 小时更新。
- Adblock：AdGuard、跟踪器、CERT Polska、HaGeZi Pro，以及现有自定义黑白名单。
- BBR、5G/4G 自动选网与支持频段配置，Wi-Fi 中国地区配置。
- LuCI、OpenClash、Adblock、文件管理器等已安装界面；可编辑简体中文 PO 文件。
- 安装软件清单、配置模板、界面恢复脚本及离线 rootfs 个性化脚本。
- 完整系统 rootfs 恢复归档，通过 Release 附件保存，不放入 Git 历史。

配置和脚本见 `u30air-config-20261101.zip`，解压后查看 `templates/rootfs/`、`translations/`、`scripts/`、`docs/`。

## 使用前必须知道

完整 rootfs 保留软件和系统模块，但**不是 PAC、全盘镜像或通用 sysupgrade.bin**，不能直接上传到普通 OpenWrt 刷机页。它依赖原设备 MU300 启动环境与匹配的 6.18.54 内核，不包含基带、校准、IMEI、Android 全分区或存储分区内容。

没有执行任何刷机、重分区或实机恢复测试。当前提供的是可完整读取、经过脱敏的 rootfs 恢复材料；**不能把归档校验通过当作刷机启动验证通过**。

### 凭据已移除

不包含原管理密码、Wi-Fi 密码、SSH 私钥、代理节点、订阅、文件管理数据库/令牌或短信。root 默认锁定，OpenClash 与独立文件服务不自启；DNS 使用可直接解析的上游，避免未导入节点时形成依赖。

恢复前先在本地设置新密码：

```sh
# Linux / WSL，需要 Python 3.10+ 与 openssl
python3 scripts/personalize-rootfs.py \
  u30air-openwrt-25.12.5-k6.18.54-sanitized-rootfs-20261101.tar.gz \
  private-personalized-rootfs.tar.gz
```

生成的个性化包含新密码哈希与 Wi-Fi 密码，只保留在本地，不要上传。代理节点与订阅需自行重新导入，确认 DNS 和分流后再启用 OpenClash。

## 恢复界面

在相同版本的现有设备上解压配置包后，可运行 `sh scripts/restore-ui.sh`。脚本先备份当前界面，再只覆盖 `usr/` 与 `www/` 下的界面文件，不改网络、密码或代理订阅。配置模板在 `templates/rootfs/etc/`，不建议整包直接覆盖。

## 校验与来源

Release 的 `SHA256SUMS` 校验下载附件；配置包内 `FILES.sha256` 校验源文件。原项目和规则由各自作者维护，保留原文件的许可与版权声明。

- MU300 Linux：https://github.com/dikeckaan/mu300-linux
- OpenClash：https://github.com/vernesong/OpenClash
- Mihomo：https://github.com/MetaCubeX/mihomo
- anti-AD：https://github.com/privacy-protection-tools/anti-AD
- 秋风：https://github.com/TG-Twilight/AWAvenue-Ads-Rule
- HaGeZi：https://github.com/hagezi/dns-blocklists

已知限制：设备特定的内核与包源不一定匹配，禁止强装不匹配的内核模块。BBR 不作用于 UDP，也不能保证提高所有流量速度。域名规则不能保证去除所有视频内广告。插件更新可能覆盖界面定制。
