# Query Hook Function

## Overview

The query hook function allows you to customize the data query logic in your model admin class. By overriding the `handle_query` method, you can:

- Customize query conditions
- Add data filtering
- Customize sorting logic
- Implement data permission control

## Basic Usage

Override the `handle_query` method in your model admin class:

```python
class YourModelAdmin(ModelAdmin):
    async def handle_query(self, request: Request, params: dict) -> tuple[QuerySet, int]:
        """
        Handle data query hook method
        Args:
            request: Request object
            params: Query parameter dictionary, containing:
                - limit: Records per page
                - offset: Offset
                - search: Search keyword
                - sort: Sort field
                - order: Sort order (asc/desc)
                - Other filter parameters
        Returns:
            tuple[QuerySet, int]: (Query result set, Total count)
        """
        try:
            # Get base queryset
            queryset = await self.get_queryset(request, params)
            
            # You can add custom logic here
            # Example: Data permission control
            if not request.user.is_superuser:
                queryset = queryset.filter(owner=request.user)
            
            # Custom filter conditions
            if params.get('status'):
                queryset = queryset.filter(status=params['status'])
            
            # Handle sorting
            if params.get('sort'):
                order_by = f"{'-' if params['order'] == 'desc' else ''}{params['sort']}"
                queryset = queryset.order_by(order_by)
            elif self.default_ordering:
                queryset = queryset.order_by(*self.default_ordering)
                
            # Get total count
            total = await queryset.count()
            
            # Pagination
            queryset = queryset.offset(params['offset']).limit(params['limit'])
            
            return queryset, total
            
        except Exception as e:
            print(f"Query processing error: {str(e)}")
            return self.model.all(), 0
```

## Parameter Description

- `request`: Robyn's Request object, containing all information about the current request
- `params`: Query parameter dictionary, containing pagination, sorting, search parameters

## Return Value

The method should return a tuple containing:

- `QuerySet`: Query result set
- `int`: Total record count 