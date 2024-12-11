# SearchField 搜索字段组件

SearchField 是用于配置数据列表搜索条件的组件，支持普通字段和关联模型字段的搜索。

配置在AdminModel的search_fields中。

## 基本用法

### 普通字段搜索

用于对当前模型字段进行搜索：

```python
search_fields = [
    SearchField(
        name="username",           # 字段名
    )
]
```

### 关联模型字段搜索

用于对关联模型的字段进行搜索：

```python
search_fields = [
    SearchField(
        name="Category_name",      # 格式为: 关联模型名_字段名
        related_model=Category,    # 关联的模型类
        related_key="category_id"  # 外键字段名
    )
]
```

## 完整示例

```python
class TrademarkRecordAdmin(AdminModel):
    search_fields = [
        # 关联模型字段搜索
        SearchField(
            name="US_Trademark_mark_name",     # 搜索商标名称
            related_model=US_Trademark,
            related_key="trademark_id",
        ),
        SearchField(
            name="US_Trademark_serial_number", # 搜索商标编号
            related_model=US_Trademark,
            related_key="trademark_id",
        )
    ]
```

## 重要参数说明

### 基础参数

- `name`: 搜索字段名称
    - 类型: `str`
    - 说明: 对应模型的字段名，关联模型格式为"模型名_字段名"

### 关联模型参数

- `related_model`: 关联的模型类
    - 类型: `Type[Model]`
    - 说明: 指定关联的模型类

- `related_key`: 关联的外键字段
    - 类型: `str`
    - 说明: 指定关联模型的外键字段名

## 注意事项

1. 关联模型搜索
    - 字段名必须符合"模型名_字段名"的格式
    - 需要同时配置related_model和related_key参数

2. 搜索行为
    - 默认使用模糊匹配（icontains）进行搜索
    - 多个搜索字段之间是OR关系 