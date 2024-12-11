# Batch Delete Hook Function

## Overview

The batch delete hook function allows you to customize the batch delete operation logic in your model admin class. By overriding the `handle_batch_delete` method, you can:

- Implement batch soft delete
- Add pre-delete validation
- Handle related data in batch
- Record batch deletion logs

## Basic Usage

Override the `handle_batch_delete` method in your model admin class:

```python
class YourModelAdmin(ModelAdmin):
    async def handle_batch_delete(self, request: Request, ids: list) -> tuple[bool, str, int]:
        """
        Handle batch delete operation hook method
        Args:
            request: Request object
            ids: List of record IDs to delete
        Returns:
            tuple[bool, str, int]: (success status, message, deleted count)
        """
        try:
            deleted_count = 0
            for id in ids:
                try:
                    obj = await self.get_object(id)
                    if obj:
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
                        
                        deleted_count += 1
                except Exception as e:
                    print(f"Failed to delete record {id}: {str(e)}")
                    continue
                    
            if deleted_count > 0:
                return True, f"Successfully deleted {deleted_count} records", deleted_count
            return False, "No records were deleted", 0
            
        except Exception as e:
            print(f"Batch delete processing error: {str(e)}")
            return False, f"Batch delete failed: {str(e)}", 0
```

## Parameter Description

- `request`: Robyn's Request object, containing all information about the current request
- `ids`: List of record IDs to be deleted

## Return Value

The method should return a tuple containing:

- `bool`: Indicates whether the operation was successful
- `str`: Operation result message
- `int`: Number of successfully deleted records 