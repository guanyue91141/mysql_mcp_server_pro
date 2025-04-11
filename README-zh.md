[![简体中文](https://img.shields.io/badge/简体中文-点击查看-orange)](README-zh.md)
[![English](https://img.shields.io/badge/English-Click-yellow)](README.md)

# mcp_mysql_server

## 介绍
mcp_mysql_server_pro 不仅止于mysql的增删改查功能，还包含了数据库异常分析能力，且便于开发者们进行个性化的工具扩展

- 支持 STDIO 方式 与 SSE 方式
- 支持 支持多sql执行，以“;”分隔。 
- 支持 根据表注释可以查询出对于的数据库表名，表字段
- 支持 sql执行计划分析
- 支持 中文字段转拼音.
- 支持 锁表分析
- 支持权限控制，只读（readonly）、读写（writer）、管理员（admin）
    ```
    "readonly": ["SELECT", "SHOW", "DESCRIBE", "EXPLAIN"],  # 只读权限
    "writer": ["SELECT", "SHOW", "DESCRIBE", "EXPLAIN", "INSERT", "UPDATE", "DELETE"],  # 读写权限
    "admin": ["SELECT", "SHOW", "DESCRIBE", "EXPLAIN", "INSERT", "UPDATE", "DELETE", 
             "CREATE", "ALTER", "DROP", "TRUNCATE"]  # 管理员权限
    ```

## 使用说明

### SSE 方式

- 使用 uv 启动服务

将以下内容添加到你的 mcp client 工具中，例如cursor、cline等

mcp json 如下
````
{
  "mcpServers": {
    "operateMysql": {
      "name": "operateMysql",
      "description": "",
      "isActive": true,
      "baseUrl": "http://localhost:9000/sse"
    }
  }
}
````

修改.env 文件内容,将数据库连接信息修改为你的数据库连接信息
```
# MySQL数据库配置
MYSQL_HOST=192.168.xxx.xxx
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=root
MYSQL_DATABASE=a_llm
MYSQL_ROLE=admin
```

启动命令
```
# 下载依赖
uv sync

# 启动
uv run server.py
```

### STDIO 方式 

将以下内容添加到你的 mcp client 工具中，例如cursor、cline等

mcp json 如下
```
{
  "mcpServers": {
      "operateMysql": {
        "isActive": true,
        "name": "operateMysql",
        "command": "uv",
        "args": [
          "--directory",
          "G:\\python\\mysql_mcp\\src",  # 这里需要替换为你的项目路径
          "run",
          "server.py",
          "--stdio"
        ],
        "env": {
          "MYSQL_HOST": "192.168.xxx.xxx",
          "MYSQL_PORT": "3306",
          "MYSQL_USER": "root",
          "MYSQL_PASSWORD": "root",
          "MYSQL_DATABASE": "a_llm",
          "MYSQL_ROLE": "admin"
       }
    }
  }
}    
```

## 个性扩展工具
1. 在handles包中新增工具类，继承BaseHandler，实现get_tool_description、run_tool方法

2. 在__init__.py中引入新工具即可在server中调用


## 示例
1. 创建新表以及插入数据 prompt格式如下
```
# 任务
   创建一张组织架构表，表结构如下：部门名称，部门编号，父部门，是否有效。
# 要求
 - 表名用t_admin_rms_zzjg,
 - 字段要求：字符串类型使用'varchar(255)'，整数类型使用'int',浮点数类型使用'float'，日期和时间类型使用'datetime'，布尔类型使用'boolean'，文本类型使用'text'，大文本类型使用'longtext'，大整数类型使用'bigint'，大浮点数类型使用'double。
 - 表头需要加入主键字段，序号 XH varchar(255)
 - 表最后需加入固定字段：创建人-CJR varchar(50)，创建时间-CJSJ datetime，修改人-XGR varchar(50)，修改时间-XGSJ datetime。
 - 字段命名使用工具返回内容作为字段命名
 - 常用字段需要添加索引
 - 每个字段需要添加注释，表注释也需要
 - 创建完成后生成5条真实数据
```

2. 根据表注释查询数据 prompt如下
```
查询用户信息表中张三的数据
```

3. 分析慢sql prompt如下
```
select * from t_jcsjzx_hjkq_cd_xsz_sk xsz
left join t_jcsjzx_hjkq_jcd jcd on jcd.cddm = xsz.cddm 
根据当前的索引情况，查看执行计划提出优化意见，以markdown格式输出，sql相关的表索引情况、执行情况，优化意见
```

4. 分析sql卡死问题 prompt如下
```
update t_admin_rms_zzjg set sfyx = '0' where xh = '1' 卡死了，请分析原因
```



