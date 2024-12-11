# SearchField Component

SearchField is a component for configuring data list search conditions, supporting both regular fields and related model fields search.

Configuration is done in the search_fields of AdminModel.

## Basic Usage

### Regular Field Search

Used for searching fields in the current model:

```python
search_fields = [
    SearchField(
        name="username",           # Field name
    )
]
```

### Related Model Field Search

Used for searching fields in related models:

```python
search_fields = [
    SearchField(
        name="Category_name",      # Format: RelatedModelName_fieldname
        related_model=Category,    # Related model class
        related_key="category_id"  # Foreign key field name
    )
]
```

## Complete Example

```python
class TrademarkRecordAdmin(AdminModel):
    search_fields = [
        # Related model field search
        SearchField(
            name="US_Trademark_mark_name",     # Search trademark name
            related_model=US_Trademark,
            related_key="trademark_id",
        ),
        SearchField(
            name="US_Trademark_serial_number", # Search trademark number
            related_model=US_Trademark,
            related_key="trademark_id",
        )
    ]
```

## Important Parameters

### Basic Parameters

- `name`: Search field name
    - Type: `str`
    - Description: Corresponds to model field name, format for related models is "ModelName_fieldname"

### Related Model Parameters

- `related_model`: Related model class
    - Type: `Type[Model]`
    - Description: Specifies the related model class

- `related_key`: Related foreign key field
    - Type: `str`
    - Description: Specifies the foreign key field name of the related model

## Important Notes

1. Related Model Search
    - Field name must follow the "ModelName_fieldname" format
    - Both related_model and related_key parameters must be configured

2. Search Behavior
    - Uses fuzzy matching (icontains) by default
    - Multiple search fields have OR relationship 