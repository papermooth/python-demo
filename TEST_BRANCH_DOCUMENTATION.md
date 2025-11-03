# Test分支项目详细说明文档

## 1. 分支信息

当前分支: **test**

## 2. 项目概述

这是一个基于Django框架开发的博客系统测试分支，支持文章的创建、查看和编辑功能。Test分支针对测试环境进行了特定配置，简化了部署流程。

## 3. 技术栈

- **后端框架**: Django 2.2.10（注意：比dev分支的2.1.8版本更新）
- **数据库**: MySQL (使用PyMySQL 0.9.3驱动)
- **WSGI服务器**: uWSGI 2.0.18
- **Web服务器**: Nginx
- **容器化**: Docker (支持但简化了配置)

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
├── manage.py                 # Django管理脚本
├── myblog/                   # 项目配置目录
│   ├── __init__.py
│   ├── settings.py           # 项目设置
│   ├── urls.py               # 主路由配置
│   └── wsgi.py               # WSGI配置
├── requirements.txt          # 依赖列表
├── Dockerfile                # Docker构建文件
├── Dockerfile-base           # 基础Docker构建文件
├── Dockerfile-optimized      # 优化的Docker构建文件
├── myblog.conf               # Nginx配置文件
├── run.sh                    # 启动脚本
├── test.py                   # 简单测试文件
├── uwsgi.ini                 # uWSGI配置
├── DEV_BRANCH_DOCUMENTATION.md  # dev分支文档
└── README.md                 # 项目基本文档
```

## 5. Test分支特点

### 5.1 与Dev分支的主要区别

1. **缺少CI/CD配置**：
   - 没有Jenkinsfile
   - 没有SonarQube配置文件
   - 没有Kubernetes部署目录（deploy/）

2. **简化的启动方式**：
   - run.sh脚本直接启动uWSGI和Nginx

3. **数据库配置差异**：
   - 数据库连接信息直接硬编码在settings.py中
   - 数据库主机固定为`192.168.136.128`

4. **Django版本更新**：
   - 使用Django 2.2.10（dev分支使用2.1.8）

5. **简单测试文件**：
   - 包含test.py文件用于简单测试

### 5.2 核心功能模块

**数据模型 (models.py)**：
```python
class Article(models.Model):
    title = models.CharField(max_length=32, default='')  # 文章标题
    content = models.TextField(null=True)  # 文章内容

    def __str__(self):
        return self.title
```

**视图函数 (views.py)**：
- index: 显示所有文章列表
- article_page: 显示单篇文章详情
- article_edit_page: 创建或编辑文章的页面
- article_edit_page_action: 处理文章的保存操作

**路由配置**：与其他分支保持一致

## 6. 数据库配置

Test分支使用固定的数据库配置：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'myblog',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST': '192.168.136.128',  # 固定的测试环境数据库服务器
        'PORT': '3306'
    }
}
```

## 7. 部署与运行

### 7.1 本地测试运行

1. 安装依赖：
   ```bash
   pip install -r requirements.txt
   ```

2. 运行数据库迁移：
   ```bash
   python manage.py migrate
   ```

3. 启动应用：
   ```bash
   ./run.sh
   ```
   或直接运行：
   ```bash
   python manage.py runserver
   ```

### 7.2 Docker容器化运行

1. 构建镜像：
   ```bash
   docker build -t myblog-test .
   ```

2. 运行容器：
   ```bash
   docker run -p 8002:8002 myblog-test
   ```

## 8. 测试文件说明

Test分支包含一个简单的测试文件`test.py`，内容如下：

```python
print("hello world")
print("hello world")
```

这个文件可以用于验证Python环境和基本执行权限。

## 9. 注意事项

1. **数据库连接**：测试环境的数据库服务器固定为`192.168.136.128`，请确保该服务器可访问且MySQL服务正常运行。

2. **Django版本兼容性**：使用的Django 2.2.10版本与其他分支不同，请注意API差异。

3. **安全性**：测试环境中的数据库凭据直接硬编码在配置文件中，仅适用于测试环境，生产环境请勿使用。

4. **简化配置**：Test分支移除了CI/CD和复杂的容器编排配置，适合快速测试和开发。

## 10. 分支管理建议

1. 功能开发应在dev分支进行
2. 测试验证在test分支进行
3. 稳定版本合并到main分支

## 11. 环境变量与配置

测试环境中几乎不使用环境变量，配置大多是硬编码的，这简化了测试流程但降低了灵活性。

## 12. 启动流程说明

当执行`./run.sh`时，脚本会：
1. 启动uWSGI服务，加载uwsgi.ini配置
2. 启动Nginx服务，使用myblog.conf配置