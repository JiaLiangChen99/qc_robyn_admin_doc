# TableField 表格字段组件

TableField 是用于配置表格列的核心组件，支持多种显示类型、关联查询、格式化等功能。

## 基本参数

- `name`: 字段名称
    - 类型: `str`
    - 说明: 对应模型的字段名，关联字段使用双下划线语法

- `label`: 显示标签
    - 类型: `str`
    - 说明: 表格列头显示的名称

- `display_type`: 显示类型
    - 类型: `DisplayType`
    - 可选值:
        - `TEXT`: 文本类型
        - `DATETIME`: 日期时间类型
        - `LINK`: 链接类型
        - `SWITCH`: 开关类型
        - `IMAGE`: 图片类型
        - `SELECT`: 选择类型
    - 说明: 决定字段在表格中的显示方式

- `sortable`: 是否可排序
    - 类型: `bool`
    - 默认值: `False`
    - 说明: 是否允许点击列头进行排序

- `editable`: 是否可编辑
    - 类型: `bool`
    - 默认值: `False`
    - 说明: 是否允许直接在表格中编辑

- `hidden`: 是否隐藏
    - 类型: `bool`
    - 默认值: `False`
    - 说明: 是否在表格中隐藏该列

## 关联字段配置

用于配置关联模型的字段显示：

- `related_model`: 关联模型
    - 类型: `Type[Model]`
    - 说明: 关联的 Tortoise ORM 模型类

- `related_key`: 关联键
    - 类型: `str`
    - 说明: 当前模型中的外键字段名

示例：
```python
TableField(
    "Trademark_mark_name",  # 使用 模型名_字段名 的格式
    label="商标",
    related_model=Trademark,  # 关联的tortoise-orm模型类
    related_key="trademark_id",  # 外键字段名
    sortable=True
)
```

## 格式化配置

### formatter 函数

用于自定义字段的显示格式：可以传入html或者对数据库返回值进行格式化等操作

```python
TableField(
    "create_mail_date",
    label="创建时间",
    display_type=DisplayType.DATETIME,
    formatter=lambda x: x.strftime('%Y-%m-%d') if x else ''
)
TableField(
    "document_link", 
    label="文档链接", 
    display_type=DisplayType.LINK,
    sortable=True,
    formatter=lambda x: f'<a href="{x}" target="_blank">查看文件</a>' if x else ''
)
```

## 开关组件配置

用于布尔值字段的开关显示：

- `labels`: 显示文本配置
    - 类型: `Dict[bool, str]`
    - 说明: 定义开关不同状态显示的文本

- `choices`: 值映射配置
    - 类型: `Dict[bool, Any]`
    - 说明: 定义开关状态对应的实际值

示例：
```python
TableField(
    "push_to_customer_done",
    label="处理情况",
    display_type=DisplayType.SWITCH,
    editable=True,
    labels={True: "已处理", False: "未处理"},
    choices={True: True, False: False}
)
```

## 完整示例

```python
table_fields = [
    # 主键字段（通常隐藏）
    TableField(
        "id", 
        label="ID", 
        display_type=DisplayType.TEXT, 
        editable=True, 
        hidden=True
    ),
    
    # 关联字段
    TableField(
        "US_Trademark_mark_name",
        label="商标",
        related_model=US_Trademark,
        related_key="trademark_id",
        sortable=True
    ),
    
    # 普通文本字段
    TableField(
        "document_description", 
        label="文档描述", 
        display_type=DisplayType.TEXT, 
        sortable=True, 
        formatter=lambda x: x
    ),
    
    # 日期时间字段
    TableField(
        "create_mail_date", 
        label="创建时间", 
        display_type=DisplayType.DATETIME, 
        sortable=True, 
        formatter=lambda x: x.strftime('%Y-%m-%d') if x else ''
    ),
    
    # 链接字段
    TableField(
        "document_link", 
        label="文档链接", 
        display_type=DisplayType.LINK,
        formatter=lambda x: f'<a href="{x}" target="_blank">查看文件</a>' if x else ''
    ),
    
    # 开关字段
    TableField(
        "push_to_customer_done", 
        label="处理情况",
        display_type=DisplayType.SWITCH,
        editable=True,
        labels={True: "已处理", False: "未处理"},
        choices={True: True, False: False}
    )
]
```
