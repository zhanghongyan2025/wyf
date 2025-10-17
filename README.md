# 民宿业务Web自动化测试项目

## 项目概述
本项目是一个基于Playwright和Pytest的民宿业务Web自动化测试框架，专注于民宿业务场景的自动化测试验证。框架采用页面对象模型（Page Object Model）设计模式，实现测试逻辑与页面操作的分离，覆盖民宿注册、房间备案、房间管理等核心业务模块，支持多维度测试验证和专业测试报告生成。

## 项目结构
```
wyf/
├── .gitattributes                # Git属性配置文件
├── pytest.ini                    # Pytest配置文件
├── requirements.txt              # 项目依赖清单
├── tests/                        # 测试相关目录
│   ├── data/                     # 测试数据文件目录
│   │   ├── evidence_files/       # 产权/租赁证明文件
│   │   ├── id_card_files/        # 身份证相关文件
│   │   ├── fire_safety_files/    # 消防安全证明文件
│   │   ├── bedroom_files/        # 卧室相关文件
│   │   ├── livingroom_files/     # 客厅相关文件
│   │   ├── kitchen_files/        # 厨房相关文件
│   │   └── bathroom_files/       # 浴室相关文件
│   ├── pages/                    # 页面对象目录
│   │   └── fd/                   # 房东相关页面
│   │       ├── filing_room_page.py  # 房间备案页面
│   │       ├── ft_manage_page.py    # 房东管理页面
│   │       ├── add_new_minsu.py     # 新增民宿页面
│   │       ├── minsu_management_page.py  # 民宿管理页面
│   │       └── louyu_management.py  # 楼宇管理页面
│   ├── prepare/                  # 测试准备脚本目录
│   │   ├── delete_ms.py          # 民宿数据删除脚本
│   │   ├── bedroom_files/        # 卧室准备文件
│   │   └── bathroom_files/       # 浴室准备文件
│   ├── test_suites/              # 测试套件目录
│   │   └── fd/
│   │       └── test_filing_room.py  # 房间备案测试用例
│   └── utils/                    # 工具类目录
│       ├── page_utils.py         # 页面操作工具
│       └── validator.py          # 验证工具
└── conf/                         # 配置目录
    ├── __pycache__/              # 编译缓存目录
    ├── config.py                 # 项目常量配置
    └── logging_config.py         # 日志配置
```

## 技术栈与依赖
### 核心技术
- **自动化框架**：Playwright (~=1.55.0) - 用于Web页面自动化操作，支持多浏览器
- **测试框架**：Pytest (~=8.4.0) - 测试用例组织、执行与断言

### 报告工具
- pytest-html (~=4.1.1) - 生成HTML格式测试报告
- allure-pytest (~=2.13.2) - 生成Allure可视化测试报告

### 数据与工具
- Faker (~=37.3.0) - 生成模拟测试数据
- paramiko (~=3.5.1) - SSH连接（用于日志获取等）
- pytest-instafail (~=0.5.0) - 实时显示测试失败信息
- mysql-connector-python (~=8.4.0) - 数据库交互

## 核心功能模块

### 1. 房间备案测试（`test_filing_room.py`）
#### 基础字段验证
- 验证房间名称、民宿名称、楼层等基础信息的非空校验
- 验证卧室数量、客厅数量、厨房数量等数值字段的合法性
- 验证车位、阳台、窗户等设施选择的必选校验
- 验证产权类型（自有/租赁/共有）与对应证明文件的关联校验

#### 文件上传验证
- 支持产权证明、消防安全证明、公安登记表等文件的上传验证
- 验证文件大小限制（≤10MB）
- 验证文件格式限制（仅支持pdf/jpg/jpeg/png）
- 验证文件数量限制（单文件上传）

### 2. 页面操作模块
- **房间备案页面（`filing_room_page.py`）**：提供房间信息填写、文件上传、表单提交等核心操作方法
- **民宿管理页面（`minsu_management_page.py`）**：实现民宿查询、提交备案等操作
- **楼宇管理页面（`louyu_management.py`）**：支持楼宇查询、修改等功能
- **新增民宿页面（`add_new_minsu.py`）**：提供民宿基本信息填写功能

### 3. 测试数据管理
- 测试文件分类存储于`tests/data`目录，包括各类证明文件模板
- 通过`conf/config.py`统一管理文件路径常量
- 提供房间、民宿公共参数配置，减少测试数据冗余

### 4. 数据库操作
- 提供`delete_ms.py`脚本，支持多数据库中指定民宿记录的查询与删除
- 实现删除前数据预览和二次确认机制，确保操作安全性

## 环境配置

### 前置条件
- Python 3.8+
- 浏览器：Chrome、Firefox、WebKit（Playwright会自动管理驱动）

### 安装步骤
1. 克隆仓库
   ```bash
   git clone <仓库地址>
   cd wyf
   ```

2. 安装依赖包
   ```bash
   pip install -r requirements.txt
   ```

3. 安装Playwright浏览器驱动
   ```bash
   playwright install
   ```

## 测试执行

### 基本命令
```bash
# 执行所有测试用例
pytest

# 执行指定测试套件（如房间备案测试）
pytest tests/test_suites/fd/test_filing_room.py

# 生成HTML报告
pytest --html=report.html

# 生成Allure报告（需先安装Allure）
pytest --alluredir=allure-results
allure serve allure-results
```

### 配置说明（`pytest.ini`）
```ini
[pytest]
log_cli = true                   # 启用控制台日志输出
log_cli_level = INFO             # 控制台日志级别为INFO
log_cli_format = %(asctime)s [%(levelname)s] %(name)s: %(message)s  # 日志格式
log_cli_date_format = %H:%M:%S   # 日志时间格式
addopts = --instafail            # 测试失败时立即显示错误信息
```

## 扩展指南
- **新增页面**：在`tests/pages`目录下创建页面对象类，实现页面元素定位和操作方法
- **新增测试用例**：在`tests/test_suites`目录下按模块组织测试类，使用Pytest参数化实现多场景覆盖
- **工具扩展**：在`tests/utils`目录下添加通用工具方法，提高代码复用性

## 注意事项
- 执行文件上传测试时，确保`tests/data`目录下存在对应格式和大小的测试文件
- 数据库相关操作需提前配置正确的连接信息（`delete_ms.py`）
- 测试环境需保持稳定，部分测试依赖特定业务场景的前置条件

## 维护与支持
- 定期更新依赖包版本，确保兼容性
- 新增业务功能时，同步更新对应页面对象和测试用例
- 测试数据定期清理，可通过`prepare`目录下的脚本执行
