# FilterField 过滤字段组件

FilterField 是用于配置数据列表过滤条件的组件，支持多种过滤类型和数据处理功能。

配置在AdminModel的filter_fields中。

## 基本过滤器类型

### InputFilter 输入框过滤器

用于文本搜索过滤：

```python
InputFilter(
    name="username",           # 字段名
    label="用户名",            # 显示标签
    placeholder="请输入用户名", # 占位文本
    operator="icontains"       # 过滤操作符
)
```

### SelectFilter 下拉选择过滤器

用于选项过滤：

```python
SelectFilter(
    name="status",
    label="状态",
    choices={                  # 选项配置
        True: "启用",
        False: "禁用"
    },
)
```

### DateRangeFilter 日期范围过滤器

用于日期范围过滤：

```python
DateRangeFilter(
    name="create_time",
    label="创建时间"
)
```

### NumberRangeFilter 数字范围过滤器

用于数值范围过滤：

```python
NumberRangeFilter(
    name="price",
    label="价格"
)
```

### BooleanFilter 布尔过滤器

用于布尔值过滤：

```python
BooleanFilter(
    name="is_active",
    label="是否激活"
)
```

## 关联模型过滤

支持对关联模型字段进行过滤：

```python
InputFilter(
    name="Category_name",      # 格式为: 关联模型名_字段名
    label="分类名称",
    related_model=Category,    # 关联的模型类
    related_key="category_id"  # 外键字段名
)
```

## 完整示例

```python
filter_fields = [
    # 文本输入过滤
    InputFilter(
        name="username",
        label="用户名",
        placeholder="请输入用户名搜索"
    ),
    
    # 选择过滤
    SelectFilter(
        name="status",
        label="状态",
        choices={
            1: "正常",
            0: "禁用",
            -1: "删除"
        }
    ),
    
    # 日期范围过滤
    DateRangeFilter(
        name="create_time",
        label="创建时间"
    ),
    
    # 数字范围过滤
    NumberRangeFilter(
        name="price",
        label="价格区间"
    ),
    
    # 布尔过滤
    BooleanFilter(
        name="is_active",
        label="激活状态"
    ),
    
    # 关联模型过滤
    InputFilter(
        name="Category_name",
        label="分类名称",
        related_model=Category,
        related_key="category_id"
    )
]
```

## 重要参数说明

### 基础参数

- `name`: 过滤字段名称
    - 类型: `str`
    - 说明: 对应模型的字段名，关联模型格式为"模型名_字段名"

- `label`: 显示标签
    - 类型: `str`
    - 说明: 过滤器的显示名称

- `filter_type`: 过滤器类型
    - 类型: `FilterType`
    - 可选值:
        - `INPUT`: 输入框
        - `SELECT`: 下拉选择
        - `DATE_RANGE`: 日期范围
        - `NUMBER_RANGE`: 数字范围
        - `BOOLEAN`: 布尔选择

### 高级参数

- `choices`: 选项配置（用于SelectFilter）
    - 类型: `Dict[Any, str]`
    - 说明: 定义选择框的选项和对应的值


- `placeholder`: 占位提示文本
    - 类型: `str`
    - 说明: 输入框的提示文本

- `operator`: 过滤操作符
    - 类型: `str`
    - 默认值: `icontains`
    - 说明: 定义过滤条件的比较方式

## 注意事项

1. 关联模型过滤
    - 字段名必须符合"模型名_字段名"的格式
    - 需要正确配置related_model和related_key

2. 选择过滤器
    - choices字典的key为实际值，value为显示文本

3. 范围过滤器
    - DateRangeFilter提供日期选择器
    - NumberRangeFilter提供数值输入框 

## 高级用法

### 动态过滤选项

在某些场景下，我们需要从数据库中动态获取过滤选项，比如状态列表。这时可以通过重写 `get_filter_fields` 方法来实现：

```python
class TrademarkAdmin(AdminModel):
    async def get_status_choices(self) -> Dict[str, str]:
        """获取状态选项"""
        # 从数据库中获取所有不重复的状态值
        status_choices = await US_Trademark.all().values_list('status', flat=True)
        # 转换为选项字典
        return {status: status for status in status_choices if status}
    
    async def get_filter_fields(self) -> List[FilterField]:
        """获取过滤字段配置"""
        # 动态获取状态选项
        status_choices = await self.get_status_choices()        
        
        filters = [
            # 普通文本搜索过滤器
            InputFilter(
                "mark_name", 
                label="商标名", 
                placeholder="请输入商标名",
                operator="icontains"
            ),
            # 动态选项的下拉框过滤器
            SelectFilter(
                "status", 
                label="状态",  
                choices=status_choices,
            )
        ]
        return filters
```

这种方式特别适用于以下场景：
- 需要从数据库动态获取过滤选项
- 过滤选项需要根据用户权限动态生成
- 过滤器配置需要依赖其他数据

通过重写 `get_filter_fields` 方法，我们可以：
1. 实现动态的过滤选项
2. 根据条件返回不同的过滤器配置
3. 在过滤器配置中使用异步操作