# ModelAdmin Class

ModelAdmin is the core class for configuring how models are displayed and managed in the admin interface. By inheriting and configuring ModelAdmin, you can customize display fields, filters, search options, and other features.

## Basic Usage

```python
from robyn_admin.core import ModelAdmin, TableField, DisplayType

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
    
    # 表格字段配置
    table_fields = [
        # need to insert pk field to
        TableField(
            name="id", label="ID", display_type=DisplayType.TEXT, editable=False, hidden=True
        ),
        TableField(
            "mark_name", label="商标名", display_type=DisplayType.TEXT, sortable=True, formatter=lambda x: str(x)
        ),
        TableField(
            "registration_number", label="注册号", display_type=DisplayType.TEXT, sortable=True, formatter=lambda x:  str(x) if x else None
        ),
        TableField(
            'status', label='状态', display_type=DisplayType.TEXT, sortable=True, formatter=lambda x: str(x)
        )
    ]
    # 默认排序
    default_ordering = ["-created_at"]

...
# Mark is tortoise-orm model
admin_site.register_model(Mark, MarkAdmin)
```

## Properties

### Basic Properties

- `verbose_name`: Display name
    - Type: `str`
    - Description: Model name displayed in the admin interface

- `menu_group`: Menu group
    - Type: `str`
    - Description: Group name where the model appears in the admin menu

- `menu_icon`: Menu icon
    - Type: `str`
    - Description: Bootstrap Icons class name

- `menu_order`: Menu order
    - Type: `int`
    - Description: Position in the menu group, smaller numbers appear first

### Feature Controls

- `enable_edit`: Enable editing
    - Type: `bool`
    - Default: `True`
    - Description: Whether to allow record editing

- `allow_add`: Allow adding
    - Type: `bool`
    - Default: `True`
    - Description: Whether to show the add button

- `allow_delete`: Allow deletion
    - Type: `bool`
    - Default: `True`
    - Description: Whether to allow record deletion

- `allow_export`: Allow export
    - Type: `bool`
    - Default: `True`
    - Description: Whether to show the export button

### Field Configuration

- `table_fields`: Table fields
    - Type: `List[TableField]`
    - Description: Define fields and their configuration in the table, more details reading: [TableField](/en/components/table_field/)

- `form_fields`: Form fields
    - Type: `List[FormField]`
    - Description: Define fields and their configuration in the edit form, more details reading: [FormField](/en/components/form_field/)

- `search_fields`: Search fields
    - Type: `List[SearchField]`
    - Description: Define searchable fields, more details reading: [SearchField](/en/components/search_field/)

- `filter_fields`: Filter fields
    - Type: `List[FilterField]`
    - Description: Define filter fields, more details reading: [FilterField](/en/components/filter_field/)

## Advanced Configuration

### Custom Queryset

```python
async def get_queryset(self, request: Request, params: dict) -> QuerySet:
    """Custom queryset method"""
    queryset = self.model.all()
    
    # Add custom filters
    queryset = queryset.filter(is_active=True)
    
    # Handle related queries
    queryset = queryset.select_related("department")
    
    return queryset
```

### Custom Serialization

```python
async def serialize_object(self, obj: Model, for_display: bool = True) -> dict:
    """Custom serialization method"""
    result = await super().serialize_object(obj, for_display)
    
    # Add custom field
    result['custom_field'] = await self.get_custom_value(obj)
    
    return result
```

## Usage Example

```python
from robyn_admin.core import ModelAdmin, TableField, SearchField, SelectFilter
from your_models import User

class UserAdmin(ModelAdmin):
    verbose_name = "User Management"
    menu_group = "System"
    menu_icon = "bi bi-people"
    
    table_fields = [
        TableField("username", label="Username", sortable=True),
        TableField("email", label="Email", sortable=True),
        TableField(
            "is_active", 
            label="Status",
            display_type=DisplayType.SWITCH,
            editable=True,
            labels={True: "Active", False: "Inactive"}
        )
    ]
    
    search_fields = [
        SearchField("username", label="Username"),
        SearchField("email", label="Email")
    ]
    
    filter_fields = [
        SelectFilter(
            "is_active",
            label="Status",
            choices={True: "Active", False: "Inactive"}
        )
    ]
    
    default_ordering = ["-created_at"]
```
