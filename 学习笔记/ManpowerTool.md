# 人力资源管理工具 日志

### 1. 数据库和后端设计

#### 1.1 简介

数据库分为 employee, projects, Employee Skill Set, Project Schedule, Project Skill Set 五个基本表

employee 中包含employee_id, firstname, lastname, division四个字段

employee skillset中包含 employee_id (foreign key), carpentry , ironwork, plumbing, framing, cfrp, carpentry, finish, operator 九个字段

project 中包含project_id, project_begin_time, project_end_time, project_leader, project Field Manager 六个字段

project skillset中包含 project_id, carpentry , ironwork, plumbing, framing, cfrp, carpentry, finish, operator 九个字段

其中：

id为long类型，为唯一主键

skillset类型为 float，用来衡量一个技工的熟练度。0代表完全不会，大于1的值表示日常工作产出大于一人

project skillset 表示一个项目需要的技工数量

Project schedule 使用唯一id作为标识，employee_id，project_id， start_date, end_date四个键

#### 1.2 需要写的CRUD

基本CRUD：

1. 根据employee表查询某个员工，修改某个员工，以及增加/删减某个员工
2. 根据project表查询某个project，增删改查
3. 根据从前端选取的employee（employee_id）查询employee_skillset表，进行skill的增改删查
4. 根据从前端选取的project（project_id）查询project_skillset表，进行skillset的增删改查
5. 根据前端的设置对Project Schedule进行增删改查
6. 根据Project表查询某段时间employee的工作状态
7. Project表具有三个相同的表，分别为主表，和两个备份表。用户选择保存数据库表时将覆盖表

#### 1.3 自动分配算法

使用二维图来实现employee的自动分配



### 2. 前端设计

前端使用 Vue+Element UI

首要目标：

1. 制作数据库增改删查的操作界面
2. 制作甘特图
3. 制作以日期为单位的显示板





