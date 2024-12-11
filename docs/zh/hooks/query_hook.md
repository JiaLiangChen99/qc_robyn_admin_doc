# 查询钩子函数

## 概述

查询钩子函数允许您在模型管理类中自定义数据查询的处理逻辑。通过重写`handle_query`方法,您可以:

- 自定义查询条件
- 添加数据过滤
- 自定义排序逻辑
- 实现数据权限控制

## 基本用法

在您的模型管理类中重写`handle_query`方法:

```python
class YourModelAdmin(ModelAdmin):
    async def handle_query(self, request: Request, params: dict) -> tuple[QuerySet, int]:
        """
        处理数据查询的钩子方法
        Args:
            request: Request对象
            params: 查询参数字典,包含:
                - limit: 每页记录数
                - offset: 偏移量
                - search: 搜索关键字
                - sort: 排序字段
                - order: 排序方式(asc/desc)
                - 其他过滤参数
        Returns:
            tuple[QuerySet, int]: (查询结果集, 总记录数)
        """
        try:
            # 获取基础查询集
            queryset = await self.get_queryset(request, params)
            
            # 在这里可以添加自定义逻辑
            # 例如：数据权限控制
            if not request.user.is_superuser:
                queryset = queryset.filter(owner=request.user)
            
            # 自定义过滤条件
            if params.get('status'):
                queryset = queryset.filter(status=params['status'])
            
            # 处理排序
            if params.get('sort'):
                order_by = f"{'-' if params['order'] == 'desc' else ''}{params['sort']}"
                queryset = queryset.order_by(order_by)
            elif self.default_ordering:
                queryset = queryset.order_by(*self.default_ordering)
                
            # 获取总记录数
            total = await queryset.count()
            
            # 分页
            queryset = queryset.offset(params['offset']).limit(params['limit'])
            
            return queryset, total
            
        except Exception as e:
            print(f"查询处理错误: {str(e)}")
            return self.model.all(), 0
```

## 参数说明

- `request`: Robyn的Request对象,包含当前请求的所有信息
- `params`: 查询参数字典,包含分页、排序、搜索等参数

## 返回值

该方法需要返回一个元组,包含:

- `QuerySet`: 查询结果集
- `int`: 总记录数 