# 版本号与命名规则

适用于 `maple-days` 下的所有 mod 仓库。

## 一、jar 文件名

统一格式：**`<archive_name>-<version>.jar`**

| 仓库 | modid（不变） | archive_name |
|---|---|---|
| `MFnAbout` | `mfnabout` | `MFnAbout` |
| `TipsExtension` | `tipsext` | `TipsExtension` |
| `SimuLightBlock` | `simulightblock` | `SimuLightBlock` |
| `Slashblade-Chixiao` | `chixiao` | `Slashblade-Chixiao` |

实现方式：`gradle.properties` 里 `archive_name` 与 `mod_id` 解耦，`build.gradle` 用
`archivesName.set(propertyString('archive_name'))`。

> **`mod_id` 永不跟随仓库名改动。** 它决定物品注册名与存档兼容性，
> 改名会让存档里的物品变成「未知物品」。

## 二、版本号 `a.b.c`

采用 SemVer，含义按 mod 的实际影响来判定：

| 位 | 什么时候 +1 | 具体判定 |
|---|---|---|
| **a**（major） | 破坏性变更 | modid 或注册名改动、配置键重命名且不提供迁移、存档有损坏风险、核心行为不兼容 |
| **b**（minor） | 新增功能 | 新增可配置项、新增界面/命令/物品、扩展了原有能力的边界，且向后兼容 |
| **c**（patch） | 修复问题 | 只修 bug、修显示错误、修兼容性，不改变任何已声明的行为 |

补充规则：

- 纯文档改动（README、注释）**不 bump 版本**；跟随下一次实质改动一起发布
- `0.x.y` 用于首个稳定版之前，规则可放宽
- 预发布用后缀 `-beta.1`、`-rc.1`，**不推送**到 CurseForge / Modrinth
- 不使用 `+build` 元数据：`+` 在文件系统和 URL 里都容易出问题

## 三、版本号的单一来源

`gradle.properties` 的 `mod_version` 是唯一来源。

**例外：`Slashblade-Chixiao`** 使用 `use_tags = false` + 手写 `Tags.java`，
版本号同时存在于 `gradle.properties` 与 `Tags.java` 的 `VERSION` 常量，**升版本时必须改两处**。

## 四、CHANGELOG

每个仓库维护 `CHANGELOG.md`，采用 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 格式：

```markdown
## [1.2.0] - 2026-11-22

### Added
- ...

### Fixed
- ...
```

## 五、发布检查清单

- [ ] `mod_version` 按规则自增（Chixiao 还要改 `Tags.VERSION`）
- [ ] `CHANGELOG.md` 补齐本节
- [ ] `./gradlew build` 通过，产物名符合 `<archive_name>-<version>.jar`
- [ ] jar 的 `META-INF/MANIFEST.MF` 里没有指向不存在文件的属性
      （`MixinConfigs`、`FMLAT`、`FMLCorePlugin` 都要核对）
- [ ] 覆盖 jar 前先退出会占用文件的程序：**Bandizip**（shell 扩展会一直攥着 jar）、
      Everything、HMCL/PCL 启动器、以及游戏本体。否则 `Copy-Item` 会静默失败或报共享冲突
- [ ] 在整合包里实测一次