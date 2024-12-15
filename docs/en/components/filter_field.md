# FilterField Component

FilterField is a component for configuring data list filtering conditions, supporting various filter types and data processing capabilities.

Configuration is done in the filter_fields of AdminModel.

## Basic Filter Types

### InputFilter

Used for text search filtering:

```python
InputFilter(
    name="username",           # Field name
    label="Username",          # Display label
    placeholder="Enter username", # Placeholder text
    operator="icontains"       # Filter operator
)
```

### SelectFilter

Used for option filtering:

```python
SelectFilter(
    name="status",
    label="Status",
    choices={                  # Options configuration
        True: "Enabled",
        False: "Disabled"
    },
)
```

### DateRangeFilter

Used for date range filtering:

```python
DateRangeFilter(
    name="create_time",
    label="Create Time"
)
```

### NumberRangeFilter

Used for numeric range filtering:

```python
NumberRangeFilter(
    name="price",
    label="Price"
)
```

### BooleanFilter

Used for boolean value filtering:

```python
BooleanFilter(
    name="is_active",
    label="Active Status"
)
```

## Related Model Filtering

Supports filtering on related model fields:

```python
InputFilter(
    name="Category_name",      # Format: RelatedModelName_fieldname
    label="Category Name",
    related_model=Category,    # Related model class
    related_key="category_id"  # Foreign key field name
)
```

## Complete Example

```python
filter_fields = [
    # Text input filter
    InputFilter(
        name="username",
        label="Username",
        placeholder="Search by username"
    ),
    
    # Select filter
    SelectFilter(
        name="status",
        label="Status",
        choices={
            1: "Normal",
            0: "Disabled",
            -1: "Deleted"
        }
    ),
    
    # Date range filter
    DateRangeFilter(
        name="create_time",
        label="Create Time"
    ),
    
    # Number range filter
    NumberRangeFilter(
        name="price",
        label="Price Range"
    ),
    
    # Boolean filter
    BooleanFilter(
        name="is_active",
        label="Active Status"
    ),
    
    # Related model filter
    InputFilter(
        name="Category_name",
        label="Category Name",
        related_model=Category,
        related_key="category_id"
    )
]
```

## Important Parameters

### Basic Parameters

- `name`: Filter field name
    - Type: `str`
    - Description: Corresponds to model field name, format for related models is "ModelName_fieldname"

- `label`: Display label
    - Type: `str`
    - Description: Display name for the filter

- `filter_type`: Filter type
    - Type: `FilterType`
    - Available values:
        - `INPUT`: Input box
        - `SELECT`: Dropdown select
        - `DATE_RANGE`: Date range
        - `NUMBER_RANGE`: Number range
        - `BOOLEAN`: Boolean select

### Advanced Parameters

- `choices`: Options configuration (for SelectFilter)
    - Type: `Dict[Any, str]`
    - Description: Defines the options and corresponding values for the select box

- `placeholder`: Placeholder text
    - Type: `str`
    - Description: Hint text for input box

- `operator`: Filter operator
    - Type: `str`
    - Default value: `icontains`
    - Description: Defines how the filter condition compares values

## Important Notes

1. Related Model Filtering
    - Field name must follow the "ModelName_fieldname" format
    - Correct configuration of related_model and related_key is required

2. Select Filter
    - In choices dictionary, key is the actual value, value is the display text
    - multiple=True enables multiple selection(to do)

3. Range Filters
    - DateRangeFilter provides date picker
    - NumberRangeFilter provides numeric input boxes 

## Advanced Usage

### Dynamic Filter Options

In some scenarios, we need to dynamically fetch filter options from the database, such as status lists. This can be achieved by overriding the `get_filter_fields` method:

```python
class TrademarkAdmin(AdminModel):
    async def get_status_choices(self) -> Dict[str, str]:
        """Get status options"""
        # Fetch all unique status values from database
        status_choices = await US_Trademark.all().values_list('status', flat=True)
        # Convert to options dictionary
        return {status: status for status in status_choices if status}
    
    async def get_filter_fields(self) -> List[FilterField]:
        """Get filter field configurations"""
        # Dynamically get status options
        status_choices = await self.get_status_choices()        
        
        filters = [
            # Regular text search filter
            InputFilter(
                "mark_name", 
                label="Trademark Name", 
                placeholder="Enter trademark name",
                operator="icontains"
            ),
            # Dropdown filter with dynamic options
            SelectFilter(
                "status", 
                label="Status",  
                choices=status_choices,
            )
        ]
        return filters
```

This approach is particularly useful in scenarios where:
- Filter options need to be dynamically fetched from database
- Filter options need to be generated based on user permissions
- Filter configurations depend on other data

By overriding the `get_filter_fields` method, we can:
1. Implement dynamic filter options
2. Return different filter configurations based on conditions
3. Use async operations in filter configurations