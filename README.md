README


Apple WLOC 定位修改
修改 Apple 网络定位服务 (WiFi/基站) 返回的坐标，实现 iOS 网络定位虚拟定位。打开在线选点页面选位置即可生效，无需手动填经纬度。

订阅地址
Surge: https://raw.githubusercontent.com/Yu9191/wloc/refs/heads/main/modules/wloc.sgmodule

Quantumult X: https://raw.githubusercontent.com/Yu9191/wloc/refs/heads/main/modules/wloc.conf

Loon: https://raw.githubusercontent.com/Yu9191/wloc/refs/heads/main/modules/wloc.lpx

Stash: https://raw.githubusercontent.com/Yu9191/wloc/refs/heads/main/modules/wloc.stoverride

Shadowrocket(小火箭): https://raw.githubusercontent.com/Yu9191/wloc/refs/heads/main/modules/wloc.module

Egern 可直接使用 Surge 模块 Stash 请直接订阅上面的 .stoverride，无需用 Script Hub 转换

快捷指令（推荐，最方便）
直接用快捷指令切换 / 清除定位，无需打开选点页面：

wloc 设置地理位置：https://www.icloud.com/shortcuts/a82717d8fdad4e6280866fcf911173f7
wloc 清理恢复位置：https://www.icloud.com/shortcuts/f42632d406504f24a2cd163af4fe012f
用法

设置位置： 在地图 App 里选好位置（长按地图选点）→ 共享 → 选「wloc 设置地理位置」即可切换。
苹果地图：选点 → 共享 → 「wloc 设置地理位置」
高德地图：选点 → 分享 → 更多 → 「wloc 设置地理位置」
清理位置： 点「wloc 清理恢复位置」即可恢复真实定位。
支持苹果地图、高德（含短链，自动跟跳转 + GCJ-02→WGS84 坐标换算）。

前提：代理已开 + 模块已启用 + 信任 gs-loc.apple.com。选点页面（Worker / Pages）方案仍保留，见下方。

关于地图链接解析（worker）
为了让苹果地图和高德走同一条流程，链接统一发给 wloc-spoofer.wloc.workers.dev/api/parse 解析：

高德：分享出来是短链，真实坐标只藏在 302 跳转的 Location 头里，且是 GCJ-02 偏移坐标。快捷指令既读不到跳转头、也难做坐标换算，所以由 worker 跟跳转 → 抠坐标 → GCJ-02→WGS84 → 返回经纬度。
苹果地图：链接里直接带 coordinate=纬度,经度，但在中国大陆同样是 GCJ-02 偏移坐标，所以和高德一样由 worker 做 GCJ-02→WGS84 换算后返回；境外坐标会自动跳过换算（out_of_china 判断）原样返回。除了统一坐标系，走同一接口也方便统一处理短链、文本夹链接、名称解码等。
隐私： /api/parse 是纯转发解析——收到链接 → 跟跳转 → 解析坐标 → 返回 JSON，全程不写任何存储、不记日志、不缓存，处理完即丢。

不放心可自行部署： worker 源码完全开源，可自己部署一份替换上面的地址：

解析逻辑：worker/src/parse.js，路由：worker/src/index.js
部署后把快捷指令里的 wloc-spoofer.wloc.workers.dev 换成你自己的 worker 域名即可。
使用方法
工作原理
参数配置
取消虚拟定位 / 恢复真实定位
收藏位置功能
自部署 Worker（推荐）
公共选点页面有请求上限，建议部署自己的实例：

Workers: https://wloc-spoofer.wloc.workers.dev/
Pages: https://wloc-pages.pages.dev/
一键部署（Workers）：

Deploy to Cloudflare Workers

一键部署仅支持 Workers 模式，点击按钮后按提示授权即可完成部署。

手动部署（Workers）：

# 1. 克隆仓库
git clone https://github.com/Yu9191/wloc.git
cd wloc/worker

# 2. 安装依赖
npm install

# 3. 登录 Cloudflare（首次需要）
npx wrangler login

# 4. 部署
npm run deploy
部署成功后会得到你自己的 Worker 地址（如 https://wloc-spoofer.<你的子域名>.workers.dev），用这个地址选点即可。

免费账户每天 10 万次请求，个人使用完全够用。

高级：Pages 部署
注意事项
致谢
proxypin-wloc-spoofer - 原始 WLOC 定位修改思路 by FFF686868
NSNanoCat/Util - 跨平台脚本工具框架
