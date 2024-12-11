# Add Hook Function

## Overview

The add hook function allows you to customize the add operation logic in your model admin class. By overriding the `handle_add` method, you can:

- Add custom business logic before and after saving
- Modify data before saving
- Set additional fields
- Implement custom validation logic

## Basic Usage

Override the `handle_add` method in your model admin class:

```python
class YourModelAdmin(ModelAdmin):
    async def handle_add(self, request: Request, data: dict) -> tuple[bool, str]:
        """
        Handle add operation hook method
        Args:
            request: Request object
            data: Data to be added
        Returns:
            tuple[bool, str]: (success status, message)
        """
        try:
            # Process form data
            processed_data = await self.process_form_data(data)
            
            # You can add custom logic here
            # Example: Add creation time
            processed_data['created_at'] = datetime.now()
            
            # Create object
            obj = await self.model.create(**processed_data)
            
            # You can perform other operations here, such as:
            # - Record operation logs
            # - Send notifications
            # - Trigger other business processes
            
            return True, "Create successful"
            
        except Exception as e:
            print(f"Add processing error: {str(e)}")
            return False, f"Create failed: {str(e)}"
```

## Parameter Description

- `request`: Robyn's Request object, containing all information about the current request
- `data`: Dictionary containing data to be added, where keys are field names and values are field values

## Return Value

The method should return a tuple containing:

- `bool`: Indicates whether the operation was successful
- `str`: Operation result message 