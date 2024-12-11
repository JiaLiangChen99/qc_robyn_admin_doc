# Delete Hook Function

## Overview

The delete hook function allows you to customize the delete operation logic in your model admin class. By overriding the `handle_delete` method, you can:

- Implement soft delete
- Add pre-delete validation
- Handle related data
- Record deletion logs

## Basic Usage

Override the `handle_delete` method in your model admin class:

```python
class YourModelAdmin(ModelAdmin):
    async def handle_delete(self, request: Request, object_id: str) -> tuple[bool, str]:
        """
        Handle delete operation hook method
        Args:
            request: Request object
            object_id: Record ID
        Returns:
            tuple[bool, str]: (success status, message)
        """
        try:
            obj = await self.get_object(object_id)
            if not obj:
                return False, "Record not found"
                
            # You can add custom logic here
            # Example: Soft delete
            obj.is_deleted = True
            await obj.save()
            
            # Or perform physical deletion
            # await obj.delete()
            
            # You can perform other operations here, such as:
            # - Check related data
            # - Record deletion logs
            # - Send notifications
            
            return True, "Delete successful"
            
        except Exception as e:
            print(f"Delete processing error: {str(e)}")
            return False, f"Delete failed: {str(e)}"
```

## Parameter Description

- `request`: Robyn's Request object, containing all information about the current request
- `object_id`: ID of the record to be deleted

## Return Value

The method should return a tuple containing:

- `bool`: Indicates whether the operation was successful
- `str`: Operation result message 