# ModelAdmin 模型管理类

ModelAdmin 是用于配置模型在后台的展示和管理方式的核心类。通过继承和配置 ModelAdmin，可以自定义模型的显示字段、过滤器、搜索项等功能。

## 基本用法

```python
from qc_robyn_admin.core import ModelAdmin, TableField, DisplayType

class MarkAdmin(ModelAdmin):
    # 显示名称
    verbose_name = "商标查询"
    
    # 菜单配置
    menu_group = "商标查询"    # 所属菜单组
    menu_icon = "bi bi-people" # Bootstrap 图标
    menu_order = 1            # 菜单排序
    
    # 功能开关
    enable_edit = True       # 允许编辑
    allow_add = True        # 允许添加
    allow_delete = True     # 允许删除
    allow_export = True     # 允许导出
    allow_import = True    # 允许导入

    # 表格字段配置
    table_fields = [
        TableField(
            name="id", label="ID", display_type=DisplayType.TEXT, editable=False, hidden=True
        ),
        TableField(
            "xxx", label="xxx", display_type=DisplayType.TEXT, sortable=True, formatter=lambda x: str(x)
        ),
    ]
    # 默认排序
    default_ordering = ["-created_at"]

    import_fields = [
        "name",
        "email",
        "biography"
    ]
...
# Mark is tortoise-orm model
admin_site.register_model(Mark, MarkAdmin)

```

## 属性说明

### 基础属性

- `verbose_name`: 显示名称
    - 类型: `str`
    - 说明: 在后台界面菜单栏展示的名称

- `menu_group`: 菜单组
    - 类型: `str`
    - 说明: 模型在后台菜单中所属的组

- `menu_icon`: 菜单图标
    - 类型: `str`
    - 说明: 使用 Bootstrap Icons 的图标类名

- `menu_order`: 菜单排序
    - 类型: `int`
    - 说明: 在同组菜单中的排序位置，数字越小越靠前

### 功能控制

- `enable_edit`: 启用编辑
    - 类型: `bool`
    - 默认值: `True`
    - 说明: 是否允许编辑表格记录

- `allow_add`: 允许添加
    - 类型: `bool`
    - 默认值: `True`
    - 说明: 是否显示添加按钮，需要搭配 add_form参数使用

- `allow_delete`: 允许删除
    - 类型: `bool`
    - 默认值: `True`
    - 说明: 是否允许删除记录

- `allow_export`: 允许导出
    - 类型: `bool`
    - 默认值: `True`
    - 说明: 是否显示导出按钮

- `allow_import`: 允许导入
    - 类型: `bool`
    - 默认值: `True`
    - 说明: 是否显示导入按钮

### 字段配置

- `table_fields`: 表格字段
    - 类型: `List[TableField]`
    - 说明: 定义表格中显示的字段及其配置, 更多参数阅读: [TableField](/zh/components/table_field/)

- `form_fields`: 表单字段
    - 类型: `List[FormField]`
    - 说明: 定义编辑表单中的字段及其配置, 更多参数阅读: [FormField](/zh/components/from_field/)

- `search_fields`: 搜索字段
    - 类型: `List[SearchField]`
    - 说明: 定义可搜索的字段, 更多参数阅读: [SearchField](/zh/components/search_field/)

- `filter_fields`: 过滤字段
    - 类型: `List[FilterField]`
    - 说明: 定义过滤器字段, 更多参数阅读: [FilterField](/zh/components/filter_field/)

- `import_fields`: 导入字段
    - 类型: `List[str]`
    - 说明: 定义导入时可导入的字段

## 高级配置

### 自定义查询集

```python
async def get_queryset(self, request: Request, params: dict) -> QuerySet:
    """自定义查询集"""
    queryset = self.model.all()
    
    # 添加自定义过滤条件
    queryset = queryset.filter(is_active=True)
    
    # 处理关联查询
    queryset = queryset.select_related("department")
    
    return queryset
```

### 自定义序列化

```python
async def serialize_object(self, obj: Model, for_display: bool = True) -> dict:
    """自定义序列化方法"""
    result = await super().serialize_object(obj, for_display)
    
    # 添加自定义字段
    result['custom_field'] = await self.get_custom_value(obj)
    
    return result
```

## 使用示例

```python
from qc_robyn_admin.core import ModelAdmin, TableField, SearchField, SelectFilter
from your_models import User

class UserAdmin(ModelAdmin):
    verbose_name = "用户管理"
    menu_group = "系统管理"
    menu_icon = "bi bi-people"
    
    table_fields = [
        TableField("username", label="用户名", sortable=True),
        TableField("email", label="邮箱", sortable=True),
        TableField(
            "is_active", 
            label="状态",
            display_type=DisplayType.SWITCH,
            editable=True,
            labels={True: "启用", False: "禁用"}
        )
    ]
    
    search_fields = [
        SearchField("username", label="用户名"),
        SearchField("email", label="邮箱")
    ]
    
    filter_fields = [
        SelectFilter(
            "is_active",
            label="状态",
            choices={True: "启用", False: "禁用"}
        )
    ]
    
    default_ordering = ["-created_at"]
```

