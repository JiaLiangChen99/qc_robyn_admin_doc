# FormField Component

FormField is a core component for configuring form fields, supporting various input types, data processing, and validation functionalities.

Configuration is done in the form_fields and add_form_fields of AdminModel

## Basic Parameters

- `name`: Field name
    - Type: `str`
    - Description: Corresponds to the model field name, used for form submission

- `label`: Display label
    - Type: `str`
    - Description: Display name for the form field

- `field_type`: Field type
    - Type: `DisplayType`
    - Available values:
        - `TEXT`: Text input box
        - `EMAIL`: Email input box
        - `PASSWORD`: Password input box
        - `DATE`: Date picker
        - `DATETIME`: Date time picker
        - `SELECT`: Dropdown select box
        - `SWITCH`: Switch component
    - Description: Determines the input type of the form field

- `required`: Required field
    - Type: `bool`
    - Default value: `False`
    - Description: Whether the field is required

## Advanced Configuration

### Select Configuration

For configuring dropdown select options:

- `choices`: Options configuration
    - Type: `Dict[str, Any]`
    - Description: Defines the options and corresponding values for the select box

Example:
```python
FormField(
    "is_active",
    label="Status",
    field_type=DisplayType.SELECT,
    choices={"Active": True, "Disabled": False}
)
```

### Data Processor

For processing field values during form submission:

- `processor`: Processing function
    - Type: `Callable[[Any], Any]`
    - Description: Processes field value before saving

Example:
```python
FormField(
    "password", 
    label="Password", 
    field_type=DisplayType.PASSWORD,
    required=True,
    processor=lambda x: AdminUser.hash_password(x)  # Password encryption
)
```

## Field Type Examples

### Text Input
```python
FormField(
    "username",
    label="Username",
    required=True
)
```

### Email Input
```python
FormField(
    "email",
    label="Email",
    field_type=DisplayType.EMAIL,
    required=True
)
```

### Password Input
```python
FormField(
    "password",
    label="Password",
    field_type=DisplayType.PASSWORD,
    required=True
)
```

### Date Picker
```python
FormField(
    'create_at',
    label="Birth Date",
    field_type=DisplayType.DATE
)
```

### Dropdown Select
```python
FormField(
    "status",
    label="Status",
    field_type=DisplayType.SELECT,
    choices={
        "Enabled": True,
        "Disabled": False
    }
)
```

## Complete Example

```python
form_fields = [
    # Basic text field
    FormField(
        "username",
        label="Username",
        required=True
    ),
    
    # Email field
    FormField(
        "email",
        label="Email",
        field_type=DisplayType.EMAIL,
        required=True
    ),
    
    # Password field (with processor)
    FormField(
        "password", 
        label="Password", 
        field_type=DisplayType.PASSWORD,
        required=True,
        processor=lambda x: AdminUser.hash_password(x)
    ),
    
    # Phone field
    FormField(
        "phone",
        label="Phone",
        field_type=DisplayType.TEXT,
        required=True
    ),
    
    # Status select field
    FormField(
        "is_active",
        label="Status",
        field_type=DisplayType.SELECT,
        choices={"Active": True, "Disabled": False}
    ),
    
    # Date field
    FormField(
        'create_at',
        label="Birth Date",
        field_type=DisplayType.DATE
    )
]
```

## Important Notes

1. Field Types
    - Choosing the appropriate `field_type` provides corresponding input validation
    - `EMAIL` type automatically validates email format
    - `DATE` and `DATETIME` provide date pickers

2. Data Processing
    - `processor` functions execute before data saving
    - Ensure processing functions return the correct data type

3. Required Fields
    - Fields with `required=True` are validated on the frontend
    - Required fields are checked during form submission 