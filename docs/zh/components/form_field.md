# FormField 表单字段组件

FormField 是用于配置表单字段的核心组件，支持多种输入类型、数据处理和验证功能。

配置在AdminModel的form_fields和add_form_fields中

## 示例

```
python

add_form_fields = [
        FormField("name", label="作者名称", required=True),
        FormField("avatar", label="头像", field_type=DisplayType.FILE_UPLOAD,upload_path="static/avatars", accept="image/*", max_size=1024*1024*10),
        FormField(
            'biography', label='简介', field_type=DisplayType.TEXT, 
            processor=lambda x: get_biography(x)
        )
    ]
```

## 基本参数

- `name`: 字段名称
    - 类型: `str`
    - 说明: 对应模型的字段名，用于表单提交

- `label`: 显示标签
    - 类型: `str`
    - 说明: 表单字段的显示名称

- `field_type`: 字段类型
    - 类型: `DisplayType`
    - 可选值:
        - `TEXT`: 文本输入框
        - `EMAIL`: 邮箱输入框
        - `PASSWORD`: 密码输入框
        - `DATE`: 日期选择器
        - `DATETIME`: 日期时间选择器
        - `SELECT`: 下拉选择框
        - `SWITCH`: 开关组件
        - `FILE_UPLOAD`: 文件上传
    - 说明: 决定表单字段的输入类型

- `upload_path`: 文件上传路径str
    - 类型: `str`
    - 默认值: None
    - 说明: 文件图片上传的路径

- `max_size`: 文件上传最大大小
    - 类型: `int`
    - 默认值: None
    - 说明: 文件图片上传的最大大小(单位字节)


## 高级配置

### 选择器配置

用于配置下拉选择框的选项：

- `choices`: 选项配置
    - 类型: `Dict[str, Any]`
    - 说明: 定义选择框的选项和对应的值

示例：
```python
FormField(
    "is_active",
    label="状态",
    field_type=DisplayType.SELECT,
    choices={"正常": True, "禁用": False}
)
```

### 数据处理器

用于在表单提交时处理字段值：

- `processor`: 处理函数
    - 类型: `Callable[[Any], Any]`
    - 说明: 在保存数据前对字段值进行处理

示例：
```python
FormField(
    "password", 
    label="密码", 
    field_type=DisplayType.PASSWORD,
    required=True,
    processor=lambda x: AdminUser.hash_password(x)  # 密码加密处理
)
```

## 字段类型示例

### 文本输入框
```python
FormField(
    "username",
    label="用户名",
    required=True
)
```

### 邮箱输入框
```python
FormField(
    "email",
    label="邮箱",
    field_type=DisplayType.EMAIL,
    required=True
)
```

### 密码输入框
```python
FormField(
    "password",
    label="密码",
    field_type=DisplayType.PASSWORD,
    required=True
)
```

### 日期选择器
```python
FormField(
    'create_at',
    label="出生日期",
    field_type=DisplayType.DATE
)
```

### 下拉选择框
```python
FormField(
    "status",
    label="状态",
    field_type=DisplayType.SELECT,
    choices={
        "启用": True,
        "禁用": False
    }
)
```

## 完整示例

```python
form_fields = [
    # 基本文本字段
    FormField(
        "username",
        label="用户名",
        required=True
    ),
    
    # 邮箱字段
    FormField(
        "email",
        label="邮箱",
        field_type=DisplayType.EMAIL,
        required=True
    ),
    
    # 密码字段（带处理器）
    FormField(
        "password", 
        label="密码", 
        field_type=DisplayType.PASSWORD,
        required=True,
        processor=lambda x: AdminUser.hash_password(x)
    ),
    
    # 电话字段
    FormField(
        "phone",
        label="电话",
        field_type=DisplayType.TEXT,
        required=True
    ),
    
    # 状态选择字段
    FormField(
        "is_active",
        label="状态",
        field_type=DisplayType.SELECT,
        choices={"正常": True, "禁用": False}
    ),
    
    # 日期字段
    FormField(
        'create_at',
        label="出生日期",
        field_type=DisplayType.DATE
    )
]
```

## 注意事项

1. 字段类型
    - 选择合适的 `field_type` 可以获得相应的输入验证
    - `EMAIL` 类型会自动验证邮箱格式
    - `DATE` 和 `DATETIME` 会提供日期选择器

2. 数据处理
    - `processor` 函数在数据保存前执行
    - 确保处理函数返回正确的数据类型

3. 必填字段
    - 设置 `required=True` 的字段会在前端���行验证
    - 表单提交时会检查必填字段是否填写 