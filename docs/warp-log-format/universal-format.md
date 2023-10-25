# 数据交换格式 - SRGF格式

星穹铁道抽卡记录格式（Star Rail Gachalog Format, **SRGF**），为[UIGF组织](https://uigf.org/zh/)所提出的一种用于各《崩坏：星穹铁道》第三方跃迁记录管理工具进行数据导出与交换的通用记录格式标准。

SRGF在设计上与由同一组织维护的“原神统一可交换祈愿记录标准”（UIGF标准）相似，并根据其经验教训以及《崩坏：星穹铁道》官方跃迁记录API给出信息的变化做出了改进。

SRGF为星穹铁道跃迁观测工具当前所声明支持的唯一导入与导出格式。

!> 星穹铁道跃迁观测工具保留在未来提供XLSX格式导出文件的可能性，但仅用于数据展示，请勿用于数据交换。<br/>本工具将不会对任何基于工作簿格式的跃迁记录文件提供导入支持。

!> 虽然结构相似，UIGF.J格式仅支持《原神》，SRGF格式仅支持《崩坏：星穹铁道》。请勿尝试交叉导入。

## 格式介绍

请前往UIGF组织的官方网站查看[SRGF标准原文](https://uigf.org/zh/standards/SRGF.html)。

SRGF格式根对象下包含`info`与`list`两个字段，前者为一个保持该记录文件基本信息的对象，后者用于存放所有跃迁记录项的一个数组。

UIGF组织提供了SRGF格式的JSON Schema。

文件格式大致如下：

```json
{
  "info": {
    "uid": "100000000",
    "lang": "zh-cn",
    "region_time_zone": 8,
    "export_timestamp": 1684124992,
    "export_app": "xxxxx",
    "export_app_version": "xxxxx",
    "srgf_version": "v1.0"
  },
  "list": [
    {
      "gacha_id": "1001",
      "gacha_type": "1",
      "item_id": "10000000",
      "count": "1",
      "time": "1970-01-01 00:00:00",
      "name": "xxx",
      "item_type": "光锥",
      "rank_type": "4",
      "id": "1000000000000000000"
    }
  ]
}
```

!> 以下所用名词并非UIGF组织在SRGF标准中定义的标准名称。

### 文件基本信息

`info`用于储存该通用祈愿记录文件所具有的一些基本信息，包括该记录的用户信息、导出信息、以及其他的扩展信息。

#### 用户信息

跃迁记录工具需要通过用户的`uid`以确定该记录的归属，而`lang`则标记了跃迁记录项的语言。

鉴于单个SRGF文件仅会包含一名用户的祈愿记录，而标准规定文件内所有记录项应当为同一语言，这两项可以被分类为“用户信息”，并从记录项中提取出来、置于文件基本信息部分。

此外，《崩坏：星穹铁道》的跃迁历史记录API中提供了该账号所属服务器的时区信息`region_time_zone`。

| 字段   | SRGF规定  | 星穹铁道跃迁观测工具 - 导入  | 星穹铁道跃迁观测工具 - 导出    |
| ------ | -------- | -------------------------- | ----------- |
| `uid`  | 以字符串形式保存 | SRGF模式下必要 | SRGF模式下保留     |
| `lang` | 以字符串形式保存，与官方API提供的原始值相同，<br/>即由ISO 639-1两位语言代码+ISO 3166-1两位国家或地区代码组成 | 仅支持`zh-cn` | 仅支持`zh-cn`|
| `region_time_zone` | 整数，与官方API提供的原始值相同 | 必要 | 必要 |

#### 导出信息

跃迁记录工具需要在SRGF文件中记录一些额外的信息，标识该文件遵循的标准版本、导出程序以及导出时间信息，以便其他工具能够对该记录文件进行正确处理。

| 字段          | 含义   | SRGF规定     | 星穹铁道跃迁观测工具 - 导入 | 星穹铁道跃迁观测工具 - 导出         |
| ------------- | ----------------------------------------- | ------------------- | --------------------------- | --------------------------- |
|`export_timestamp`|文件生成的UNIX时间戳|精确到秒的时间戳，以整数形式保存|可选，作为字符串读取<br/>兼容精确到毫秒的时间戳|必要，作为整数或字符串导出<br/>精确到秒的时间戳|
| `export_app`  | 文件创建工具标识      | 由各工具自行定义 | 非必要 | `DodocoTales.StarRail` |
|`export_app_version`|工具版本标识|由各工具自行定义|非必要|程序版本号|
|`srgf_version`|遵循的SRGF版本号| 已定义的SRGF版本号|必须存在，用于识别SRGF文件<br/>对具体值无要求|`v1.0`|


#### 扩展信息

!> 以下字段并非SRGF标准的一部分。

扩展信息为一系列星穹铁道跃迁观测工具或其他祈愿工具所自定义的字段，其并非标准的一部分。

| 字段 | 含义 | 取值 | 星穹铁道跃迁观测工具 - 导入 | 星穹铁道跃迁观测工具 - 导出 |
| ---- | ---- | --- | ------------------------- | --------------------------- |
| `export_time` | 文件生成时间 | 格式应为`yyyy-MM-dd hh:mm:ss` | 可选，优先读取<br/>若缺失则使用`export_timestamp` | 格式为`yyyy-MM-dd hh:mm:ss` |
| `game` | 游戏名 | `Honkai_Star_Rail` | 非必要 | `Honkai_Star_Rail` |
| `game_biz` | 游戏客户端类型 | `hkrpg_cn`（国服）<br/>`hkrpg_global`（国际服） | 非必要<br/>若缺失则在导入时手动选择 | `hkrpg_cn`（国服）<br/> `hkrpg_global`（国际服） |

### 跃迁记录项

`list`数组用于存放用户的跃迁记录项。祈愿记录项中应当包含足够的必要信息，且需尽可能反应从米哈游官方的跃迁记录API所获取到的原始记录的样貌。

跃迁记录项中应当包含以下原始信息，且全部以字符串形式保存：

| 字段 | 含义 |
| --- | ---- |
| `gacha_id` | 该跃迁记录所属卡池的编号 |
| `gacha_type` | 该跃迁记录所属的类型 |
| `item_id` | 该次跃迁获得的单位的编号 |
| `count` | 数量，当前固定为`"1"`|
| `time` | 该跃迁记录的服务器时间，属于`region_time_zone`时区 |
| `name` | 该次跃迁获得的单位在`lang`语言中的名称 |
| `item_type` | 该次跃迁获得的单位在`lang`语言下的类型名 |
| `rank_type` | 该次跃迁获得的单位的星级 |
| `id` | 该跃迁记录项的编号 |

跃迁记录项中暂不注入`srgf_gacha_type`字段。

## 支持现状

!> 以下支持列表并不完整，欢迎补充

当前已知的支持或已声明支持SRGF格式的工具，及其与星穹铁道跃迁观测工具间的兼容性如下（顺序无意义）：

!> 以下测试结果仅对`zh-cn`语言有效，未测试其他语言的跃迁记录导入与导出

| 工具          | 平台 | `export_app`            |  导入支持 | 从Starwo导入 | 导出支持 | 向Starwo导入 |  
| ---------- | ---- | ----------------------- | ---------| -------------| ------------ | --- |
| 星穹铁道跃迁观测工具<br/>Star Rail Warp Observe <br/> [TremblingMoeNew/StarRailWarpObserve](https://github.com/TremblingMoeNew/StarRailWarpObserve) | Windows | `DodocoTales.StarRail` | :heavy_check_mark:  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| 崩坏:星穹铁道跃迁记录导出工具<br/>Star Rail Warp History Exporter <br/>[biuuu/star-rail-warp-export](https://github.com/biuuu/star-rail-warp-export) | Windows | `star-rail-warp-export` | :heavy_check_mark:  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| 崩坏:星穹铁道抽卡导出工具<br/> Star Rail Gacha Exporter<br/> [DancingSnow0517/StarRail-gacha](https://github.com/DancingSnow0517/StarRail-gacha) | Windows | `star-rail-gacha` | :x:  | :x: | :heavy_exclamation_mark: | :x: |  
| 崩坏:星穹铁道跃迁记录导出工具<br/>Star Rail Tools<br/>[cntvc/star-rail-tools](https://github.com/cntvc/star-rail-tools) | Windows | `StarRailTools` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| 《崩坏：星穹铁道》工具箱<br/>Honkai Star Rail Toolkit<br/>[LittleNyima/honkai-starrail-toolkit](https://github.com/LittleNyima/honkai-starrail-toolkit) | Windows | `StarRailToolkit` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| 星铁披萨小助手<br/>HSR Pizza Helper<br/>[pizza-studio/hsrpizzahelper](https://github.com/pizza-studio/hsrpizzahelper)| iOS<br/>macOS<br/>iPadOS | | :x: | :x: | :x: | :x: |  
| Asta<br/>[AuroraZiling/star-rail-asta](https://github.com/AuroraZiling/star-rail-asta) | Windows | `asta` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| SRCat<br/>[BoxCatTeam/SRCat](https://github.com/BoxCatTeam/SRCat) | Windows | `SRCat` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| 「崩坏 · 星穹铁道」 跃迁记录导出脚本<br/>star-rail-gacha-export<br/> [vikiboss/star-rail-gacha-export](https://github.com/vikiboss/star-rail-gacha-export) | Windows | `star-rail-gacha-export` | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: |  
| HoYo.Gacha<br/>[lgou2w/HoYo.Gacha](https://github.com/lgou2w/HoYo.Gacha) | Windows | `com.lgou2w.hoyo.gacha` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| Starward<br/>[Scighost/Starward](https://github.com/Scighost/Starward) | Windows | `Starward` | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  
| PamGram<br/>[PaiGramTeam/PamGram](https://github.com/PaiGramTeam/PamGram) | Telegram Bot | `PaiGram` | :white_check_mark: | :grey_question: | :white_check_mark: | :grey_question: |  
| 星轨工具箱<br/>[JamXi233/SRTools](https://github.com/JamXi233/SRTools) | Windows | `SRTools` | :white_check_mark: | :grey_question: | :white_check_mark: | :grey_question: |  
| Miao-Yunzai<br/>[yoimiya-kokomi/Miao-Yunzai](https://github.com/yoimiya-kokomi/Miao-Yunzai) | QQ Bot | `Miao-Yunzai` | :grey_question: | :grey_question: | :grey_question: | :grey_question: |  
| 咸鱼的崩铁助理<br/>非开源工具 | 微信小程序 | `咸鱼的崩铁助理` | :white_check_mark: | :grey_question: |  :white_check_mark: | :grey_question: |  
| 非小酋<br/>Feixiaoqiu<br/>非开源工具 | 网页 | | :x: | :x: | :x: | :x: |  
