# Dev分支项目详细说明文档

## 1. 分支信息

当前分支: **dev**

## 2. 项目概述

这是一个基于Django框架开发的博客系统，支持文章的创建、查看和编辑功能。Dev分支包含了完整的开发、构建和部署配置。

## 3. 技术栈

- **后端框架**: Django 2.1.8
- **数据库**: MySQL (使用PyMySQL 0.9.3驱动)
- **WSGI服务器**: uWSGI 2.0.18
- **Web服务器**: Nginx 1.13.7
- **容器化**: Docker
- **CI/CD**: Jenkins
- **容器编排**: Kubernetes
- **代码质量**: SonarQube
- **测试**: Robot Framework

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
├── deploy/                   # Kubernetes部署配置
│   ├── configmap.yaml        # 配置映射
│   ├── deploy-myblog.yaml    # 应用部署配置
│   ├── ingress.yaml          # 入口配置
│   ├── secret.yaml           # 密钥配置
│   └── svc-myblog.yaml       # 服务配置
├── log/                      # 日志目录
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
├── Jenkinsfile               # Jenkins CI/CD流水线
├── myblog.conf               # Nginx配置文件
├── nginx-1.13.7.tar.gz       # Nginx源码包
├── run.sh                    # 启动脚本
├── sonar-project.properties  # SonarQube配置
├── test-case.robot           # Robot Framework测试用例
└── uwsgi.ini                 # uWSGI配置
```

## 5. 核心功能模块

### 5.1 数据模型 (models.py)

```python
class Article(models.Model):
    title = models.CharField(max_length=32, default='')  # 文章标题
    content = models.TextField(null=True)  # 文章内容

    def __str__(self):
        return self.title
```

### 5.2 视图函数 (views.py)

- **index**: 显示所有文章列表
- **article_page**: 显示单篇文章详情
- **article_edit_page**: 创建或编辑文章的页面
- **article_edit_page_action**: 处理文章的保存操作

### 5.3 路由配置

**主路由 (myblog/urls.py)**: 将所有 `/blog/` 开头的请求转发到blog应用

**应用路由 (blog/blog_urls.py)**: 
- `/blog/index/`: 文章列表页面
- `/blog/article/<id>/`: 文章详情页面
- `/blog/article/edit/<id>/`: 文章编辑页面
- `/blog/article/edit/action`: 保存文章的处理接口

### 5.4 页面模板

- **index.html**: 显示文章列表，提供写博客入口
- **article_page.html**: 显示文章详情，提供编辑入口
- **article_edit_page.html**: 文章创建和编辑表单

## 6. 数据库配置

项目使用MySQL数据库，配置通过环境变量动态设置：

- 数据库名: `myblog`
- 用户名: 通过环境变量`MYSQL_USER`配置
- 密码: 通过环境变量`MYSQL_PASSWD`配置
- 主机: 通过环境变量`MYSQL_HOST`配置
- 端口: 通过环境变量`MYSQL_PORT`配置

## 7. 部署配置

### 7.1 Docker容器化

Dockerfile基于CentOS 7.5.1804，包含以下组件：
- Python 3.6
- Nginx 1.13.7
- 所需的Python依赖包

构建命令: `docker build -t myblog .`

### 7.2 Kubernetes部署

#### 主要资源配置

- **Deployment**: 管理Pod副本，设置了健康检查和就绪探针
- **Service**: 提供稳定的访问端点
- **Ingress**: 配置外部访问路由
- **ConfigMap**: 存储非敏感配置信息
- **Secret**: 存储敏感信息如数据库凭据

#### 部署关键配置

- 容器镜像: `192.168.136.128:60080/demo/myblog`
- 端口映射: 8002
- 资源限制: 内存100Mi-500Mi, CPU 50m-100m
- 健康检查: 通过访问`/blog/index/`验证服务状态

### 7.3 CI/CD流程 (Jenkinsfile)

Jenkins流水线包含以下阶段：

1. **git-log**: 记录Git提交信息
2. **checkout**: 从代码仓库检出代码
3. **CI**: 包含并行的单元测试和代码扫描
   - 单元测试 (当前跳过)
   - 代码扫描 (使用SonarQube)

完整流水线还包括构建镜像、推送镜像和部署到Kubernetes等阶段。

## 8. 开发环境设置

### 8.1 本地开发

1. 安装依赖: `pip install -r requirements.txt`
2. 运行数据库迁移: `python manage.py migrate`
3. 启动开发服务器: `python manage.py runserver`

### 8.2 容器化开发

1. 构建镜像: `docker build -t myblog .`
2. 运行容器: `docker run -p 8002:8002 myblog`

## 9. 注意事项

- Dev分支包含了完整的CI/CD和容器化配置
- 生产环境部署时请修改以下配置：
  - `settings.py`中的`SECRET_KEY`
  - `DEBUG = False`
  - 数据库连接信息
- Kubernetes部署需要确保集群中有`demo`命名空间
- 镜像仓库地址`192.168.136.128:60080`需要根据实际环境修改

## 10. 测试

项目包含Robot Framework测试用例 (test-case.robot)，可用于自动化测试。

## 11. 监控与日志

- 容器日志可通过Kubernetes日志系统查看
- 应用日志存储在`log/`目录下

## 12. 与Main分支的主要区别

- Dev分支包含完整的CI/CD配置
- 使用`deploy/`目录而非`resources/`目录
- 增加了Docker相关配置文件
- 包含Jenkins流水线和SonarQube配置
- 包含Robot Framework测试用例