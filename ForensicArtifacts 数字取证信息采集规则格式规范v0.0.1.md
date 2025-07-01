# OpenForensicRules: 数字取证信息采集规则格式规范



## 1. 简介

ForensicArtifacts 是一套标准化的数字取证与应急响应信息采集规则格式规范。它旨在提供一个通用的配置框架，使取证专业人员能够：

- 定义可跨平台复用的取证工件采集规则
- 在不同的取证工具和框架中使用相同的规则集
- 构建和分享标准化的证据采集方法

本项目受到 [ForensicArtifacts/artifacts](https://github.com/ForensicArtifacts/artifacts) 项目的启发，并在其基础上进行了改进和扩展。



## 2. 规则示例

规则使用 YAML 格式定义，每个配置的基本单元是一个 Artifact。以下是两个简单示例：

```yaml
name: 'WindowsSystemEventLogEvtx'
doc: 'Windows System Event log for Vista or later systems.''
author: 'ForensicArtifacts'
version: 0.0.1
sources:
  - type: 'FILE'
    attributes: 
      paths: ['%%environ_systemroot%%\System32\winevt\Logs\System.evtx']
    supported_os: 'Windows'
urls: ['https://artifacts-kb.readthedocs.io/en/latest/sources/windows/EventLog.html']
```

```yaml
name: 'LinuxLog'
doc: 'Linux 平台上日志文件收集'
author: 'NOPTeam'
version: '0.0.1'
sources:
  - type: 'PATH'
    supported_os: 'Linux'
    attributes:
      paths:
        - '/var/log/'
```

> 规则整体层级为 YAML文件 -> Artifact -> Source -> 具体类型的配置



## 3. Artifact 定义规范

### 3.1 核心字段

| 字段    | 描述               | 是否为必填项 |
| :------ | ------------------ | :----------: |
| name    | 工件名称           |      ✓       |
| doc     | 工件的描述信息     |      ✗       |
| author  | 工件作者           |      ✗       |
| sources | 信息采集的数据源   |      ✓       |
| urls    | 工件相关的参考链接 |      ✗       |
| version | 规则版本           |      ✓       |



### 3.2 字段详细说明

#### name

工件的唯一标识符，应清晰表达该工件的用途。

**格式要求**：

- 长度：2-255个字符
- 字符限制：仅支持英文字母和数字
- 命名风格：首字母大写的驼峰式命名

```yaml
name: 'WindowsSystemEventLogEvtx'
```



#### doc

工件的描述信息，说明其用途、重要性或注意事项。

**格式建议**：

- 简明扼要，通常为单行描述
- 需要多行时使用 YAML 多行文本格式（`|`）

```yaml
doc: |
  Windows 系统事件日志，适用于 Vista 及更高版本系统。
  包含系统启动、关机、服务启停等重要事件信息。
```



#### author

工件的作者信息。

```yaml
author: 'NOPTeam'
```



#### urls

相关参考资料的链接列表。

```yaml
urls:
  - 'https://artifacts-kb.readthedocs.io/en/latest/sources/windows/EventLog.html'
  - 'https://forensics.wiki/windows_event_log/'
```



#### version

规则版本号，当前规范版本为 0.0.1。

```yaml
version: '0.0.1'
```



#### sources

工件的核心组成部分，定义了实际的数据采集源。每个工件可包含多个 source 对象。

```yaml
sources:
  - type: COMMAND
    attributes:
      args: ['-ano']
      cmd: netstat
    supported_os: "windows"
  
  - type: FILE
    attributes:
      paths: ['%%environ_systemroot%%\System32\winevt\Logs\System.evtx']
    supported_os: "windows"
```



## 4. Source 定义规范

每个 source 对象表示特定操作系统上的一种数据源类型。

### 4.1 核心字段

| 字段         | 描述           | 是否为必填项 |
| ------------ | -------------- | :----------: |
| type         | 数据源类型     |      ✓       |
| attributes   | 数据源属性     |      ✓       |
| supported_os | 支持的操作系统 |      ✓       |



### 4.2 Source 类型

#### COMMAND

执行特定命令并收集其输出。

| 属性 | 描述           | 是否为必填项 |
| ---- | -------------- | :----------: |
| cmd  | 命令路径或名称 |      ✓       |
| args | 命令参数列表   |      ✓       |

```yaml
type: 'COMMAND'
attributes:
  cmd: 'netstat'
  args: ['-ano']
supported_os: 'windows'
```



#### FILE

收集特定文件。

| 属性  | 描述         | 是否为必填项 |
| ----- | ------------ | :----------: |
| paths | 文件路径列表 |      ✓       |

```yaml
type: 'FILE'
attributes:
  paths: 
    - '%%environ_systemroot%%\System32\winevt\Logs\System.evtx'
    - 'C:\Windows\System32\drivers\etc\hosts'
supported_os: 'windows'
```



#### PATH

收集特定目录。

| 属性  | 描述         | 是否为必填项 |
| ----- | ------------ | :----------: |
| paths | 目录路径列表 |      ✓       |

```yaml
type: 'PATH'
attributes:
  paths:
    - '%%environ_systemroot%%\System32\winevt\Logs\'
    - 'C:\Windows\Temp'
supported_os: 'windows'
```



#### REGISTRY_KEY

收集 Windows 注册表键。

| 属性 | 描述             | 是否为必填项 |
| ---- | ---------------- | :----------: |
| keys | 注册表键路径列表 |      ✓       |

```yaml
type: 'REGISTRY_KEY'
attributes:
  keys:
    - 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'
    - 'HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU'
supported_os: 'windows'
```



#### REGISTRY_VALUE

收集 Windows 注册表特定键的值。

| 属性            | 描述       | 是否为必填项 |
| --------------- | ---------- | :----------: |
| key_value_pairs | 键值对列表 |      ✓       |

```yaml
type: 'REGISTRY_VALUE'
attributes:
  key_value_pairs:
    - {key: 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run', value: 'SecurityHealth'}
    - {key: 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Reliability', value: 'LastAliveStamp'}
supported_os: 'windows'
```

特别说明：在 Windows 注册表中，value 的概念是 ValueName + ValueType + Data 的集合。本规则采用 Windows 中的 ValueName 作为 value 的值。



#### WMI

通过 WMI 查询收集 Windows 系统信息。

| 属性        | 描述              | 是否为必填项 |
| ----------- | ----------------- | :----------: |
| base_object | WMI 命名空间      |      ✗       |
| query       | WMI 查询语句(WQL) |      ✓       |

```yaml
type: 'WMI'
attributes:
  base_object: 'winmgmts:\root\cimv2'
  query: 'SELECT * FROM Win32_Process'
supported_os: 'windows'
```



### 4.3 支持的操作系统

`supported_os` 字段定义了数据源适用的操作系统：

| 值      | 描述                |
| ------- | ------------------- |
| windows | Windows 操作系统    |
| linux   | Linux 操作系统      |
| darwin  | macOS/OS X 操作系统 |
| android | Android 操作系统    |
| ios     | iOS 操作系统        |



## 5. 内置变量与通配符

规则支持多种内置变量，用于指定不同环境下的标准路径。在路径中可以使用通配符 `*` 和以下内置变量。

### 5.1 POSIX 用户变量

| 变量名                     | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `%%users_homedir%%`        | 系统所有用户的主目录 (`MacOS: /Users/*` 、`Linux: /home/*` 以及 `/root`) |
| `%%current_user_homedir%%` | 当前用户的主目录                                             |



### 5.2 Windows 环境变量

| 变量名                        | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| `%%environ_allusersappdata%%` | 环境变量 => `%AllUsersAppData%` 不存在时等于 `%ProgramData%` 环境变量 |
| `%%environ_allusersprofile%%` | 环境变量 => `%AllUsersProfile%`                              |
| `%%environ_programdata%%`     | 环境变量 => `%ProgramData%` 不存在时等于 `%AllUsersAppData%` 或 `%AllUsersProfile%\Application Data` |
| `%%environ_programfiles%%`    | 环境变量 => `%ProgramFiles%`                                 |
| `%%environ_programfilesx86%%` | 环境变量 => `%ProgramFiles(x86)%`                            |
| `%%environ_systemdrive%%`     | 环境变量 => `%SystemDrive%` （例如C:）                       |
| `%%environ_systemroot%%`      | 环境变量 => `%SystemRoot%` （例如C:\Windows）                |
| `%%environ_windir%%`          | 环境变量 => `%WinDir%` （例如C:\Windows）                    |



### 5.3 Windows 用户变量

#### 所有用户变量

这些变量会展开为系统上所有用户的对应路径：

| 变量名                   | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `%%users_appdata%%`      | 所有用户的环境变量 => `%AppData%` 默认 Vista+: `%%users_userprofile%%\AppData\Roaming` 旧版: `%%users_userprofile%%\Application Data` |
| `%%users_localappdata%%` | 所有用户的环境变量 => `%LocalAppData%` Vista+默认:`%%users_userprofile%%\AppData\Local` 旧版默认：`%%users_userprofile%%\Local Settings\Application Data` |
| `%%users_sid%%`          | 所有用户的安全标识符 (SID)                                   |
| `%%users_temp%%`         | 所有用户的环境变量 => `%TEMP%` 或 `%TMP%` 即所有用户的临时目录 |
| `%%users_username%%`     | 所有用户的环境变量 => `%USERNAME%` 即所有用户的名称          |
| `%%users_userprofile%%`  | 所有用户的环境变量 => `%USERPROFILE%` 即用户配置文件目录: Vista+默认: `C:\Users\用户名` 旧版默认: `C:\Documents and Settings\用户名` |



#### 当前用户变量

这些变量仅展开为执行取证工具的当前用户路径：

| 变量名                          | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `%%current_user_appdata%%`      | 当前用户的环境变量 => `%AppData%` 默认 Vista+: `%USERPROFILE%\AppData\Roaming` 旧版: `%USERPROFILE%\Application Data` |
| `%%current_user_localappdata%%` | 当前用户的环境变量 => `%LocalAppData%` Vista+默认:`%USERPROFILE%\AppData\Local` 旧版默认：`%USERPROFILE%\Local Settings\Application Data` |
| `%%current_user_sid%%`          | 当前用户的安全标识符 (SID)                                   |
| `%%current_user_temp%%`         | 当前用户的环境变量 => `%TEMP%` 或 `%TMP%` 即当前用户的临时目录 |
| `%%current_user_username%%`     | 当前用户的环境变量 => `%USERNAME%` 即当前用户的名称          |
| `%%current_user_userprofile%%`  | 当前用户的环境变量 => `%USERPROFILE%` 即当前用户配置文件目录: Vista+默认: `C:\Users\当前用户名` 旧版默认: `C:\Documents and Settings\当前用户名` |



## 6. 最佳实践

### 6.1 用户变量使用指南

- **完整取证场景**：使用 `%%users_xxx%%` 变量收集所有用户的数据

- **针对性调查**：使用 `%%current_user_xxx%%` 变量仅收集当前用户数据

- 权限考虑：

    - `%%users_xxx%%` 变量通常需要管理员/root权限才能完全访问
- `%%current_user_xxx%%` 变量在标准用户权限下也可以使用，但仅限于当前用户上下文



### 6.2 规则设计原则

1. **Artifact 归类准确，命名清晰**：在考虑创建 Artifact 时，其内部囊括的 source 应均与该 Artifact 主题强相关，命名 Artifact 时应清晰明了，使用户能够快速理解其内容。
2. **谨慎使用通配符**：滥用通配符可能使框架程序过度收集信息。
3. **文档完善**：提供足够的描述和参考链接，帮助使用者理解规则的用途。
4. **跨平台考虑**：尽可能设计能在多平台使用的规则。



### 6.3 性能与安全考量

1. **考虑资源消耗**：避免定义可能导致过度资源消耗的规则。
2. **注意隐私影响**：确保规则遵循相关法规和隐私保护要求。



## 7. 注意事项

### 7.1 多 Artifact 定义

在 YAML 文件格式中，可以使用 `---` 分隔符包含多个 Artifact 定义，这是 YAML 规则决定的，开发者应该考虑这一点，当然可以对其进行自定义规定。

例如：

```yaml
name: TempDir
doc: 收集临时目录
version: 0.0.1
author: 'NOPTeam'
sources:
  - type: PATH
    attributes:
      paths: ['/tmp/']
    supported_os: linux

---

name: RunningProcesses
doc: 收集进程列表
version: 0.0.1
author: 'NOPTeam'
sources:
  - type: COMMAND
    attributes:
      cmd: ps
      args: ['-aux']
    supported_os: linux
```



### 7.2 变量展开行为

- `%%users_xxx%%` 变量将展开为系统上所有用户的相应路径（可能生成多个路径）
- `%%current_user_xxx%%` 变量仅展开为执行取证工具的当前用户路径（生成单个路径）
- 使用变量时应考虑工具执行权限对变量展开的影响



### 7.3 规则验证

建议实施以下验证措施：

- 使用 YAML 验证工具确保语法正确
- 在多种目标环境中测试规则有效性
- 评估规则的性能影响和数据采集量



## 8. 贡献指南

我们欢迎社区贡献新的取证规则或改进现有规则。请遵循以下步骤：

1. Fork 项目仓库
2. 创建功能分支
3. 提交您的规则或修改
4. 创建 Pull Request



或联系微信 `just_hack_for_fun` 反馈

所有贡献者都应遵循本文档中定义的格式规范。



## 9. 许可证

本项目采用 Apache 2.0 协议









![image-20250630232330441](http://mweb-tc.oss-cn-beijing.aliyuncs.com/2025-06-30-152331.png)