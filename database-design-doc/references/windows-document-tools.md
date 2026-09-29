# Windows 文档工具注意事项

工具位置按当前环境发现，不把本次机器绝对路径或插件版本写死到可复用技能。使用 OfficeCLI 前通过 `--version`、`--help` 或子命令帮助核对支持能力。

## OfficeCLI 读取与缓存

常用读法为 `officecli view <docx> text --start N --end M`、`view outline`、`get <path>`、`query <selector>`、`raw /styles`。实际参数以帮助为准，某些版本的 text 不支持将输出写入 `--out`，可用 PowerShell UTF-8 重定向保存读取结果。

OfficeCLI resident 保留内存文档。外部程序改写同一路径后，旧 resident 可能读取旧版本；旧 resident 的 close/save 还可能将旧内容写回。外部修改后的校验应复制到从未使用的新任务路径再 validate，避免用关闭旧缓存来“刷新”外部编辑的文件。OfficeCLI 自己编辑之后，要在外部渲染前 save/close 确保文件已落盘。

## 格式与字段

OOXML 属性子元素具有顺序要求。结构校验出现 unexpected child 时，核对实际节点顺序；不要为消除错误删除必要内容。不要盲目复制模板 styleId，先映射对应角色及继承关系，处理直接格式对样式的覆盖。

模板页边距变化后，检查表格列网格、单元格宽度、图像两处尺寸和目录右对齐制表位。行网格与 snapToGrid 会影响行距，需对照模板实际 docGrid，不能仅检查 spacing 数值。

目录更新优先使用当前可用的 Word 字段刷新方式。不可用时，按文档技能设置打开刷新标记，并在确有稳定页码证据时窄幅更新缓存；核对正文实际标签，移除正文已删除的陈旧缓存项，不补回章节。更新之后重新验证分页。不要用 LibreOffice 另存整个 DOCX 来替代模板保真字段更新。

## 渲染与安装

通过依赖发现工具定位 Python、Node 等运行时，通过文档技能定位渲染脚本；缺少渲染器时先检查可用 Word、LibreOffice 和现有用户级工具。需要安装时选择当前用户可读可执行的位置，安装前检查 ACL，安装后运行无副作用的版本或帮助命令。

优先渲染副本到任务目录，保持源 DOCX 和模板不变。中文 Python 输出必要时设置 PYTHONIOENCODING=utf-8。中间数据留在 work，正式成果放在当前会话约定的交付目录。
