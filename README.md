# Python Demo 项目说明文档

## 1. Git分支信息

### 本地分支
- `main` (当前分支)
- `dev`
- `test`

### 远程分支
- `origin/main`
- `origin/dev`
- `origin/test`

## 2. 项目概述

这是一个基于Django框架开发的简单博客系统，支持文章的创建、查看和编辑功能。

## 3. 技术栈

- **框架**: Django 2.1.8
- **数据库**: MySQL (使用PyMySQL 0.9.3驱动)
- **WSGI服务器**: uWSGI 2.0.18
- **部署**: 支持Kubernetes部署

## 4. 项目结构

```
python-demo/
├── blog/                     # 主应用目录
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── blog_urls.py          # 应用路由配置
│   ├── migrations/           # 数据库迁移文件
│   ├── models.py             # 数据模型
│   ├── templates/            # HTML模板
│   │   ├── article_edit_page.html  # 文章编辑页面
│   │   ├── article_page.html       # 文章详情页面
│   │   └── index.html              # 首页
│   ├── tests.py
│   └── views.py              # 视图函数
├── log/                      # 日志目录
├── manage.py                 # Django管理脚本
├── myblog/                   # 项目配置目录
│   ├── __init__.py
│   ├── settings.py           # 项目设置
│   ├── urls.py               # 主路由配置
│   └── wsgi.py               # WSGI配置
├── requirements.txt          # 依赖列表
├── resources/                # Kubernetes部署配置
│   ├── myblog/               # 应用部署配置
│   ├── mysql/                # MySQL部署配置
│   └── secret.yaml           # 密钥配置
├── static/                   # 静态资源
├── uwsgi.ini                 # uWSGI配置
└── run.sh                    # 启动脚本
```

## 5. 核心功能

### 5.1 数据模型
- **Article**: 文章模型，包含标题(title)和内容(content)字段

### 5.2 主要功能
- **文章列表**: 展示所有文章
- **文章详情**: 查看单篇文章内容
- **文章编辑**: 创建和修改文章

### 5.3 路由配置
- `/index/`: 文章列表页面
- `/article/<id>/`: 文章详情页面
- `/article/edit/<id>/`: 文章编辑页面
- `/article/edit/action`: 保存文章的处理接口

## 6. 数据库配置

项目使用MySQL数据库，配置如下：
- 数据库名: `myblog`
- 用户名: 默认为`root` (可通过环境变量`MYSQL_USER`配置)
- 密码: 默认为`123456` (可通过环境变量`MYSQL_PASSWD`配置)
- 主机: 默认为`127.0.0.1` (可通过环境变量`MYSQL_HOST`配置)
- 端口: 默认为`3306` (可通过环境变量`MYSQL_PORT`配置)

## 7. 部署说明

### 7.1 本地开发
1. 安装依赖: `pip install -r requirements.txt`
2. 运行数据库迁移: `python manage.py migrate`
3. 启动开发服务器: `python manage.py runserver`

### 7.2 Kubernetes部署
项目包含完整的Kubernetes部署配置，位于`resources/`目录下：
- 应用部署: `resources/myblog/deploy.yaml`
- 服务配置: `resources/myblog/service.yaml`
- 入口配置: `resources/myblog/ingress.yaml`
- MySQL部署: `resources/mysql/`

## 8. 注意事项

- 项目使用的是Django 2.1.8版本，请注意兼容性
- 生产环境中请修改`settings.py`中的`SECRET_KEY`并设置`DEBUG = False`
- 请根据实际环境配置数据库连接信息