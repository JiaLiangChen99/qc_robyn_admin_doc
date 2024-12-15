# AdminSite Registration

AdminSite is the core class of QC Robyn Admin, used to initialize and configure the entire admin system.

## Basic Usage

```python
from robyn import Robyn
from qc_robyn_admin.core import AdminSite

app = Robyn()
admin_site = AdminSite(
    app,
    title="QC Robyn Admin",
    prefix="admin",
    copyright="© 2024 Company Name. All rights reserved",
    db_url="postgresql://user:password@localhost:5432/dbname",
    modules={
        "models": ["model.table", "qc_robyn_admin.models"]
    },
    default_language="en_US",
    generate_schemas=True
)
```

## Parameters

### Required Parameters

- `app`: Robyn application instance
    - Type: `Robyn`
    - Description: The Robyn application instance used for registering routes and handlers

### Optional Parameters

- `title`: Admin system title
    - Type: `str`
    - Default: 'QC Robyn Admin'
    - Description: System name displayed at the top of the admin interface

- `prefix`: Route prefix
    - Type: `str`
    - Default: 'admin'
    - Description: URL prefix for the admin system, e.g., '/admin'

- `copyright`: Copyright information
    - Type: `str`
    - Default: 'QC Robyn Admin'
    - Description: Copyright text displayed at the bottom of the page

- `db_url`: Database connection URL
    - Type: `str`
    - Default: None
    - Description: Database connection string, uses existing configuration if None

- `modules`: Model module configuration
    - Type: `Dict[str, List[Union[str, ModuleType]]]`
    - Default: None
    - Description: Configuration for model modules to be loaded

- `default_language`: Default language
    - Type: `str`
    - Default: 'en_US'
    - Description: System default language, supports 'zh_CN' and 'en_US'

- `generate_schemas`: Auto-generate database tables
    - Type: `bool`
    - Default: False
    - Description: Whether to automatically create database tables on startup

## Main Methods

### register_model

Register a model to the admin system.

```python
admin_site.register_model(YourModel, YourModelAdmin)
```

Parameters:
- `YourModel`: Tortoise ORM model class
- `YourModelAdmin`: Corresponding admin class

### register_menu

Register a custom menu item.

```python
from qc_robyn_admin.core import MenuItem

admin_site.register_menu(MenuItem(
    name="System Management",
    icon="bi bi-gear",
    order=1
))
```

Parameters:
- `menu_item`: MenuItem instance containing menu name, icon, and other information

## Usage Example

```python
from robyn import Robyn
from qc_robyn_admin.core import AdminSite, ModelAdmin, MenuItem
from your_models import YourModel

app = Robyn()

admin_site = AdminSite(
    app,
    title="Admin System",
    prefix="admin",
    copyright="© 2024 qc_robyn_admin",
    default_language="en_US"
)

admin_site.register_menu(MenuItem(
    name="Business Management",
    icon="bi bi-briefcase",
    order=1
))

admin_site.register_model(YourModel, YourModelAdmin)
```

## Important Notes

1. Database Configuration
    - If `db_url` is not provided, the system will try to use existing database configuration
    - It's recommended to explicitly provide database configuration in production

2. Permission Control
    - The system automatically creates a default superuser account (username: admin, password: admin)
    - It's recommended to change the superuser password immediately after deployment

3. Route Prefix
    - Ensure the `prefix` doesn't conflict with other routes
    - Use meaningful prefixes like 'admin', 'manage', etc. 