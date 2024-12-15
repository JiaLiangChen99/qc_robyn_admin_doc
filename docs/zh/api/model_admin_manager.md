# 权限管理

QC Robyn Admin 提供了简单的路由管理模块，包括用户管理、角色管理和权限控制。

## 初始账号

系统初始化时会自动创建超级管理员账号：
- 用户名：admin
- 密码：admin

建议首次登录后立即修改密码。

tips：

qc_robyn_admin通过配置角色的方式来控制路由权限，需要对角色做好允许路由访问后，才可以进行路由限制，否则用户可以访问所有页面

![角色管理界面](../images/role_manager.png)
![角色管理设置](../images/role_set_path.png)

添加一个新的用户

![添加角色](../images/add_authormanage_user.png)

设置用户所属角色

![设置用户所属角色](../images/set_user_role.png)

## 用户管理

在用户管理界面，可以进行以下操作：
- 创建新用户
- 编辑用户信息（用户名、密码、邮箱等）
- 设置用户状态（启用/禁用）
- 分配用户角色

![用户管理界面](../images/user_manager.png)


## 角色管理

角色是权限的集合，在角色管理界面可以：
- 创建新角色
- 编辑角色信息
- 配置角色可访问路由

![角色管理界面](../images/role_manager.png)

## 用户角色配置

对用户绑定角色

![权限配置界面](../images/user_for_role.png)

