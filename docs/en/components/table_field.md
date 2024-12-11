# TableField Component

TableField is the core component for configuring table columns, supporting various display types, related queries, and formatting features.

Example:
```python
TableField(
    "Trademark_mark_name",  # Use ModelName_fieldName format
    label="Trademark",
    display_type=DisplayType.TEXT,
    related_model=Trademark,  # Related Tortoise-ORM model class
    related_key="trademark_id",  # Foreign key field name
    sortable=True
)
```

## Basic Parameters

- `name`: Field name
    - Type: `str`
    - Description: Corresponds to model field name, use double underscore syntax for related fields

- `label`: Display label
    - Type: `str`
    - Description: Column header text shown in the table

- `display_type`: Display type
    - Type: `DisplayType`
    - Available values:
        - `TEXT`: Text type
        - `DATETIME`: Date and time type
        - `LINK`: Link type
        - `SWITCH`: Switch type
        - `IMAGE`: Image type
        - `SELECT`: Select type
    - Description: Determines how the field is displayed in the table

- `sortable`: Sortable
    - Type: `bool`
    - Default: `False`
    - Description: Whether to allow sorting by clicking the column header

- `editable`: Editable
    - Type: `bool`
    - Default: `False`
    - Description: Whether to allow direct editing in the table

- `hidden`: Hidden
    - Type: `bool`
    - Default: `False`
    - Description: Whether to hide this column in the table

## Related Field Configuration

For configuring the display of related model fields:

- `related_model`: Related model
    - Type: `Type[Model]`
    - Description: The related Tortoise ORM model class

- `related_key`: Related key
    - Type: `str`
    - Description: Foreign key field name in the current model



## Formatting Configuration

### formatter Function

For customizing field display format: can pass HTML or format database return values

```python
TableField(
    "create_mail_date",
    label="Created At",
    display_type=DisplayType.DATETIME,
    formatter=lambda x: x.strftime('%Y-%m-%d') if x else ''
)
TableField(
    "document_link", 
    label="Document Link", 
    display_type=DisplayType.LINK,
    sortable=True,
    formatter=lambda x: f'<a href="{x}" target="_blank">View File</a>' if x else ''
)
```

## Switch Component Configuration

For displaying boolean fields as switches:

- `labels`: Display text configuration
    - Type: `Dict[bool, str]`
    - Description: Define text display for different switch states

- `choices`: Value mapping configuration
    - Type: `Dict[bool, Any]`
    - Description: Define actual values corresponding to switch states

Example:
```python
TableField(
    "is_active",
    label="Status",
    display_type=DisplayType.SWITCH,
    editable=True,
    labels={True: "Active", False: "Inactive"},
    choices={True: True, False: False}
)
```

## Complete Example

```python
table_fields = [
    # Primary key field (usually hidden)
    TableField(
        "id", 
        label="ID", 
        display_type=DisplayType.TEXT, 
        editable=True, 
        hidden=True
    ),
    
    # Related field
    TableField(
        "Trademark_mark_name",
        label="Trademark",
        related_model=Trademark,
        related_key="trademark_id",
        sortable=True
    ),
    
    # Regular text field
    TableField(
        "description", 
        label="Description", 
        display_type=DisplayType.TEXT, 
        sortable=True, 
        formatter=lambda x: x
    ),
    
    # DateTime field
    TableField(
        "created_at", 
        label="Created At", 
        display_type=DisplayType.DATETIME, 
        sortable=True, 
        formatter=lambda x: x.strftime('%Y-%m-%d') if x else ''
    ),
    
    # Link field
    TableField(
        "document_link", 
        label="Document Link", 
        display_type=DisplayType.LINK,
        sortable=True,
        formatter=lambda x: f'<a href="{x}" target="_blank">View File</a>' if x else ''
    ),
    
    # Switch field
    TableField(
        "is_active", 
        label="Status",
        display_type=DisplayType.SWITCH,
        editable=True,
        labels={True: "Active", False: "Inactive"},
        choices={True: True, False: False}
    )
]
``` 