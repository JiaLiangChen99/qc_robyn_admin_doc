# AdminSite 站点注册

AdminSite 是 QC Robyn Admin 的核心类，用于初始化和配置整个后台管理系统。

## 基本用法 

```python
from robyn import Robyn
from robyn_admin.core import AdminSite

app = Robyn()
admin_site = AdminSite(
    app,
    title="QC Robyn Admin",
    prefix="admin",
    copyright="© 2024 公司名称 版权所有",
    db_url="postgresql://user:password@localhost:5432/dbname",
    modules={
        "models": ["model.table", "robyn_admin.models"]
    },
    default_language="zh_CN",
    generate_schemas=True
)
```

## 参数说明

### 必需参数

- `app`: Robyn 应用实例
    - 类型: `Robyn`
    - 说明: 用于注册路由和处理器的 Robyn 应用实例

### 可选参数

- `title`: 后台系统标题
    - 类型: `str`
    - 默认值: 'Robyn Admin'
    - 说明: 显示在后台界面顶部的系统名称

- `prefix`: 路由前缀
    - 类型: `str`
    - 默认值: 'admin'
    - 说明: 后台管理系统的 URL 前缀，如 '/admin'

- `copyright`: 版权信息
    - 类型: `str`
    - 默认值: None
    - 说明: 显示在登陆页面底部的版权信息

- `db_url`: 数据库连接 URL
    - 类型: `str`
    - 默认值: None
    - 说明: 数据库连接字符串，如果为 None 则尝试复用已有配置

- `modules`: 模型模块配置
    - 类型: `Dict[str, List[Union[str, ModuleType]]]`
    - 默认值: None
    - 说明: 需要加载的模型模块配置

- `default_language`: 默认语言
    - 类型: `str`
    - 默认值: 'en_US'
    - 说明: 系统默认语言，支持 'zh_CN' 和 'en_US'

- `generate_schemas`: 是否自动生成数据库表
    - 类型: `bool`
    - 默认值: False
    - 说明: 是否在启动时自动创建数据库表结构

## 主要方法

### register_model

注册模型到后台管理系统。

```python
admin_site.register_model(YourModel, YourModelAdmin)
```

参数:
- `YourModel`: tortoise 模型类
- `YourModelAdmin`: 对应的管理类

### register_menu

注册自定义菜单项。

```python
from robyn_admin.core import MenuItem

admin_site.register_menu(MenuItem(
    name="系统管理",
    icon="bi bi-gear",
    order=1
))
```

参数:
- `menu_item`: MenuItem 实例，包含菜单的名称、图标等信息

## 使用示例

```python
from robyn import Robyn
from robyn_admin.core import AdminSite, ModelAdmin, MenuItem
from your_models import YourModel

app = Robyn()

admin_site = AdminSite(
    app,
    title="后台管理系统",
    prefix="admin",
    copyright="© 2024 qc_robyn_admin",
    default_language="zh_CN"
)

admin_site.register_menu(MenuItem(
    name="业务管理",
    icon="bi bi-briefcase",
    order=1
))

admin_site.register_model(YourModel, YourModelAdmin)
```

## 注意事项

1. 数据库配置
    - 如果未提供 `db_url`，系统会尝试复用已有的数据库配置
    - 建议在生产环境中明确提供数据库配置

2. 权限控制
    - 系统会自动创建默认的超级管理员账号（用户名: admin，密码: admin）
    - 建议在部署后立即修改超级管理员密码

3. 路由前缀
    - 确保 `prefix` 不要与其他路由冲突
    - 建议使用有意义的前缀，如 'admin'、'manage' 等