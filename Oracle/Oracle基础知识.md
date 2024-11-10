# Oracle基础知识

## 1. 数据库的启动和关闭

### 1.1 启动数据库

```sql
-- 以系统管理员身份连接
sqlplus / as sysdba

-- 启动数据库的三个步骤
startup nomount;     -- 启动实例，读取参数文件，启动SGA和后台进程
alter database mount;     -- 装载数据库，读取控制文件
alter database open;     -- 打开数据库，打开数据文件和重做日志文件
```

### 1.2 关闭数据库

```sql
shutdown immediate;     -- 立即关闭数据库，回滚未提交事务
shutdown normal;       -- 正常关闭，等待所有用户断开连接
shutdown abort;        -- 强制关闭，不推荐使用
```

## 2. 用户和权限管理

### 2.1 创建和管理用户

```sql
-- 创建用户
CREATE USER username 
IDENTIFIED BY password
DEFAULT TABLESPACE users
TEMPORARY TABLESPACE temp;

-- 修改用户密码
ALTER USER username IDENTIFIED BY new_password;

-- 删除用户
DROP USER username CASCADE;  -- CASCADE会删除该用户的所有对象
```

### 2.2 授予和撤销权限

```sql
-- 授予系统权限
GRANT CREATE SESSION, CREATE TABLE TO username;
GRANT DBA TO username;  -- 授予DBA角色

-- 授予对象权限
GRANT SELECT, INSERT ON schema.table_name TO username;

-- 撤销权限
REVOKE CREATE TABLE FROM username;
```

## 3. 表空间管理

### 3.1 创建和管理表空间

```sql
-- 创建表空间
CREATE TABLESPACE tablespace_name
DATAFILE '/path/to/datafile.dbf'
SIZE 100M
AUTOEXTEND ON NEXT 10M
MAXSIZE 500M;

-- 设置表空间离线/在线
ALTER TABLESPACE tablespace_name OFFLINE;
ALTER TABLESPACE tablespace_name ONLINE;

-- 删除表空间
DROP TABLESPACE tablespace_name INCLUDING CONTENTS AND DATAFILES;
```

## 4. 基本SQL操作

### 4.1 表操作

```sql
-- 创建表
CREATE TABLE employees (
    emp_id NUMBER PRIMARY KEY,
    emp_name VARCHAR2(100),
    salary NUMBER(10,2),
    dept_id NUMBER,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) 
    REFERENCES departments(dept_id)
);

-- 修改表结构
ALTER TABLE employees ADD (email VARCHAR2(100));
ALTER TABLE employees MODIFY (emp_name VARCHAR2(150));
ALTER TABLE employees DROP COLUMN email;

-- 创建索引
CREATE INDEX idx_emp_name ON employees(emp_name);
```

### 4.2 数据操作

```sql
-- 插入数据
INSERT INTO employees (emp_id, emp_name, salary)
VALUES (1, '张三', 5000);

-- 批量插入
INSERT INTO employees (emp_id, emp_name, salary)
SELECT emp_id, emp_name, salary FROM temp_employees;

-- 更新数据
UPDATE employees 
SET salary = salary * 1.1
WHERE dept_id = 10;

-- 删除数据
DELETE FROM employees WHERE emp_id = 1;
TRUNCATE TABLE employees;  -- 快速删除表中所有数据
```

## 5. 事务控制

```sql
-- 事务控制命令
COMMIT;      -- 提交事务
ROLLBACK;    -- 回滚事务
SAVEPOINT sp1;    -- 创建保存点
ROLLBACK TO sp1;  -- 回滚到保存点

-- 设置事务隔离级别
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

## 6. 性能优化基础

### 6.1 查看执行计划

```sql
-- 查看SQL执行计划
EXPLAIN PLAN FOR
SELECT * FROM employees WHERE salary > 5000;

-- 显示执行计划
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
```

### 6.2 统计信息收集

```sql
-- 收集表的统计信息
ANALYZE TABLE employees COMPUTE STATISTICS;
-- 或使用推荐的包
EXEC DBMS_STATS.GATHER_TABLE_STATS('SCHEMA_NAME', 'EMPLOYEES');
```