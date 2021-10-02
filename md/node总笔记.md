# mysql
### mysql的安装
下载
[官方地址](https://dev.mysql.com/downloads/mysql/)
[腾讯下载源](https://pc.qq.com/detail/3/detail_1303.html)
安装
选server only  next
execute   next
next
next next next
设置密码 123123
next next execute finish

电脑右击菜单 运行 services.msc  
找到mysql 属性 设为自动启动

加环境变量
复制这个路径  C:\Program Files\MySQL\MySQL Server 8.0\bin
计算机 属性 高级系统设置 环境变量 
在系统的环境变量 找到path 加上那个地址
运行mysql检测是否设置成功

### 使用
进入mysql交互命令 （相当于运行node命令可以写代码运行代码在命令行）
命令 mysql -uroot -p
   -p表示使用密码登录
   -uroot表示超级管理员

运行这串代码 show variables like 'character\_set\_%';
查看当前数据库字符编码

utf8一个字三个字节
utf8mb4 一个字四个字节

把数据库编码改成utf8mb4
找到目录 C:\ProgramData\MySQL\MySQL Server 8.0
programData是一个隐藏文件夹
修改my.ini文件中的默认字符编码
设置
default-character-set=utf8mb4
character-set-server=utf8mb4
设置成功后把文件复制到安装目录
C:\Program Files\MySQL\MySQL Server 8.0
搞好后需要重新启动mysql服务
net stop mysql80 停止服务
net start mysql80 启动服务
电脑右击菜单 运行 services.msc   用界面进行重启

show databases;  查看当前拥有的数据库
exit 退出

使用navicat进行数据库管理 网上找破解版
https://www.jb51.net/article/199525.htm
破解工具 密码 3y6d
https://pan.baidu.com/s/11PH7oGOaegG00e8x-bx_aQ

https://www.yuque.com/docs/share/50c484aa-4a93-4deb-9781-d9264a133c0b


### 数据库设计
SQL 结构化查询语言
 - DDL 操作数据库对象 库表，视图，存储过程
 - DML 数据操控语言，操作数据库中的记录
 - DCL 数据控制语句  操作用户权限
管理库
create database name 创建一个数据库
use name 使用哪个数据库
drop database name 删除数据库
管理表

表记录的增删改查 DML
```sql
-- 增加记录
INSERT INTO `student`(stuno,`name`, birthday,phone,classid)
VALUES('500', '成哥', '1900-1-1', '13344445555', 2);

INSERT INTO `student`(stuno,`name`, birthday,phone,classid)
VALUES('500', '成哥', '1900-1-1', '13344445555', 2),
('501', '邓哥', '1900-1-2', '13344445556', 2);

-- 修改记录
UPDATE student SET `name`='邓旭名' 
WHERE id=12;

-- 删除记录
DELETE FROM student
WHERE `name`='袁哥';
```
单表的基本查询
```sql
SELECT id, loginid, loginpwd, 'abc' as '额外的一列' from `user`;
-- 取出user表某些属性的列

SELECT ismale '性别' from `employee`;
-- 不用as，写在后面就是别名

SELECT *, 'abc' as 'extra' from `employee`;

SELECT id, `name`, 
case ismale
when 1 then '男'
else '女'
end sex, 
salary 
FROM employee;
-- 把ismale列里的1改为男，0改为女

SELECT id, `name`, 
case
when ismale = 1 then '男'
else '女'
end sex, 
case
when salary>=10000 then '高'
when salary>=5000 then '中'
else '低'
end `level`,
salary
FROM employee;
-- 多条件查询，相当于if，else if，else

SELECT * FROM employee
WHERE ismale = 1;

SELECT * FROM department
WHERE companyId in (1, 2);
-- companyId为1或2的
SELECT * from employee
WHERE location is not null;
-- location的值不为null（空）

SELECT * from employee
WHERE location is null;
-- location的值为null（空）

SELECT * from employee
WHERE salary>=10000;

SELECT * from employee
WHERE salary BETWEEN 10000 and 12000;
-- 在。。。之间

SELECT * from employee
WHERE `name` like '%袁%';
-- 包含袁

SELECT * from employee
WHERE `name` like '袁_';
-- 以袁开头，并且只有两个字符

SELECT * from employee
WHERE `name` like '张%' and ismale=0 and salary>=12000;
-- and并且
SELECT * from employee
WHERE `name` like '张%' and (ismale=0 and salary>=12000
or
birthday>='1996-1-1');
-- or或者，满足一个则为真

SELECT *, case ismale
when 1 then '男'
else '女'
end sex from employee
ORDER BY sex asc, salary desc;
-- order by 排序 ，asc升序  desc降序

SELECT * from employee
limit 2,3;
-- 去掉前两个再选三个

select DISTINCT location from employee;
-- distinct 去重
```
联表查询
```sql
SELECT * 
from department as d left join employee as e
on d.id = e.deptId;
-- on后面是限制条件   
-- left join  左边的开头

SELECT * 
from employee as e right join department as d 
on d.id = e.deptId;
-- right join 右边的开头

SELECT e.`name` as empname, d.`name` as dptname, c.`name` as companyname
from employee as e 
inner join department as d on d.id = e.deptId 
inner join company c on d.companyId = c.id;
-- inner join 按顺序链接
```
### 函数和分组
**函数**
- 数字函数
ABS(x)   返回x的绝对值
CEILING(x)   返回大于x的最小整数值
FLOOR(x)   返回小于x的最大整数值
MOD(x,y)    返回x/y的模（余数）
PI() 返回pi的值（圆周率）
RAND() 返回０到１内的随机值
ROUND(x,y) 返回参数x的四舍五入的有y位小数的值
TRUNCATE(x,y)  返回数字x截短为y位小数的结果
```sql
SELECT ABS(-1);
SELECT CEIL(1.4);
SELECT ROUND(3.1415926, 3);
SELECT TRUNCATE(3.1415926,3);

SELECT TRUNCATE(salary,0)
FROM employee
```
- 聚合函数
AVG(col) 返回指定列的平均值
COUNT(col) 返回指定列中非NULL值的个数
MIN(col) 返回指定列的最小值
MAX(col) 返回指定列的最大值
SUM(col) 返回指定列的所有值之和
```sql
SELECT AVG(salary) as `avg`, id
FROM employee;

-- 查询员工数量
SELECT COUNT(id)
FROM employee;

SELECT count(id) as 员工数量,
avg(salary) as 平均薪资,
sum(salary) as 总薪资,
min(salary) as 最小薪资
FROM employee;
```
- 字符和日期函数
CONCAT(s1,s2...,sn) 将s1,s2...,sn连接成字符串
CONCAT_WS(sep,s1,s2...,sn) 将s1,s2...,sn连接成字符串，并用sep字符间隔
TRIM(str) 去除字符串首部和尾部的所有空格
LTRIM(str) 从字符串str中切掉开头的空格
RTRIM(str) 返回字符串str尾部的空格

CURDATE()或CURRENT_DATE() 返回当前的日期
CURTIME()或CURRENT_TIME() 返回当前的时间
TIMESTAMPDIFF(part,  date1,date2) 返回date1到date2之间相隔的part值，part是用于指定的相隔的年或月或日等
	MICROSECOND
	SECOND
	MINUTE
	HOUR
	DAY
	WEEK
	MONTH
	QUARTER
	YEAR
```sql
SELECT CONCAT_WS('@', `name`,salary)
FROM employee;

SELECT CURDATE();

SELECT CURTIME();

SELECT TIMESTAMPDIFF(DAY,'2010-1-1 11:11:11','2010-1-2 11:11:12');

SELECT *, 
TIMESTAMPDIFF(YEAR, birthday, CURDATE()) as age
from employee
ORDER BY age;
```
**分组**
运行顺序
	from
	join ... on ...
	where
	group by
	select
	having
	order by
	limit
分组后，只能查询分组的列和聚合列
```sql
-- 查询员工分布的居住地，以及每个居住地有多少名员工
-- 天府三街 3
SELECT location, count(id) as empnumber
FROM employee
GROUP BY location
HAVING empnumber>=40

-- 查询所有薪水在10000以上的员工的分布的居住地，然后仅得到聚集地大于30的结果
SELECT location, count(id) as empnumber
FROM employee
WHERE salary>=10000
GROUP BY location
HAVING count(id)>=30

```
**练习题**
```sql
-- 1. 查询渡一每个部门的员工数量

SELECT d.`name`, COUNT(e.id) as number
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE c.`name` like '%渡一%'
GROUP BY d.id, d.`name`;

-- 2. 查询每个公司的员工数量

SELECT c.`name`, COUNT(e.id) as number
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
GROUP BY c.id, c.`name`

-- 3. 查询所有公司5年内入职的居住在万家湾的女员工数量

SELECT c.`name`, CASE WHEN r.number is NULL THEN 0 ELSE r.number END as number
FROM company c LEFT JOIN (SELECT c.id, c.`name`, COUNT(e.id) as number
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE TIMESTAMPDIFF(YEAR,e.joinDate,CURDATE())<=5
AND e.location like '%万家湾%'
GROUP BY c.id, c.`name`) as r on c.id = r.id

-- 4. 查询渡一所有员工分布在哪些居住地，每个居住地的数量

SELECT e.location, count(e.id) as empnumber
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE c.`name` LIKE '%渡一%'
GROUP BY e.location

-- 5. 查询员工人数大于200的公司信息

SELECT * FROM company
WHERE id in (
SELECT c.id
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
GROUP BY c.id, c.`name`
HAVING count(e.id)>=200
)

-- 6. 查询渡一公司里比它平均工资高的员工

SELECT e.* 
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE c.`name` LIKE '%渡一%' AND
e.salary>(
-- 查询渡一的平均薪资
SELECT avg(e.salary)
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE c.`name` LIKE '%渡一%'
)

-- 7. 查询渡一所有名字为两个字和三个字的员工对应人数

SELECT CHAR_LENGTH(e.`name`) as 姓名长度, COUNT(E.ID) as 员工数量
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
WHERE c.`name` LIKE '%渡一%'
GROUP BY CHAR_LENGTH(e.`name`)
HAVING 姓名长度 in (2,3)

-- 8. 查询每个公司每个月的总支出薪水，并按照从低到高排序

SELECT c.`name`, SUM(e.salary) as sumofsalary
FROM company as c INNER JOIN department as d on c.id = d.companyId
INNER JOIN employee as e on d.id = e.deptId
GROUP BY c.id, c.`name`
ORDER BY sumofsalary
```

# 数据驱动和ORM
### mysql驱动程序
[mysql2的使用](https://github.com/sidorares/node-mysql2#readme)
`npm install --save mysql2`安装
```js
const mysql = require("mysql2");

// 创建一个数据库连接
const connection = mysql.createConnection({
  host: "localhost",
  user: "root",
  password: "ybybdwyJ42.",
  database: "companydb",
});

// simple query
// connection.query("SELECT * FROM `company`;", function (err, results) {
//   //err错误
//   //result查询结果
//   console.log(results); // results contains rows returned by server
// });

// connection.query(
//   "insert into company(`name`,location,buildDate) values('abbc', '阿萨德', curdate());",
//   (err, result) => {
//     console.log(result);
//   }
// );

// connection.query(
//   "update company set `name`='bcd' where id=4",
//   (err, result) => {
//     console.log(result);
//   }
// );

connection.query(
    "delete from company where id=4",
    (err, result) => {
      console.log(result);
    }
  );
```
promise用法
```js
const mysql = require("mysql2/promise");

async function test() {
  // 创建一个数据库连接
  const connection = await mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "ybybdwyJ42.",
    database: "companydb",
  });
//   console.log(connection);
  const [results] = await connection.query("SELECT * FROM `company`;");
  console.log(results);
  connection.end();
}
test();

```

```js
const mysql = require("mysql2/promise");
const pool = mysql.createPool({
  host: "localhost",
  user: "root",
  password: "ybybdwyJ42.",
  database: "companydb",
  multipleStatements: true,//开启执行多了sql语句，默认只能执行一个
  waitForConnections: true,//是否等待
  connectionLimit: 10,//链接限制数量
});

async function test(id) {
  // 创建一个数据库连接
  const sql = `select * from employee where \`name\` like concat('%', ?, '%') ;`;
  const [results] = await pool.execute(sql, [id]);
  console.log(results);
}
test("袁");
```
防止sql注入
	sql注入
		用户通过注入sql语句到最终查询中，导致了整个sql与预期行为不符
	mysql支持变量
		变量的内容不作为任何sql关键字
### Sequelize简介
[Sequelize简介](https://github.com/demopark/sequelize-docs-Zh-CN)

Object Relational Mapping 对象关系映射（ORM）
通过ORM框架，可以自动的把程序中的对象和数据库关联
ORM框架会隐藏具体的数据库底层细节，让开发者使用同样的数据操作接口，完成对不同数据库的操作
![OPM原理图](./assets/ORM原理图.jpg)
ORM的优势
	开发者不用关心数据库，仅需关心对象
	可轻易的完成数据库的移植
	无须拼接复杂的sql语句即可完成精确查询
### 模型定义和同步
安装sequelize
[sequelize](https://github.com/demopark/sequelize-docs-Zh-CN)
必须手动安装驱动
选择以下之一:
```
$ npm install --save pg pg-hstore # Postgres
$ npm install --save mysql2
$ npm install --save mariadb
$ npm install --save sqlite3
$ npm install --save tedious # Microsoft SQL Server
```
models/db.js
```js
const { Sequelize } = require("sequelize");

const sequelize = new Sequelize("myschooldb", "root", "ybybdwyJ42.", {
  host: "localhost",
  dialect: "mysql",
  logging: null
});

module.exports = sequelize;
```
models/Admin.js
```js
const sequelize = require("./db");
const { DataTypes } = require("sequelize");
//创建一个模型对象
const Admin = sequelize.define(
  "Admin",
  {
    loginId: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    loginPwd: {
      type: DataTypes.STRING,
      allowNull: false,
    },
  },
  {
    createdAt: false,
    updatedAt: false,
    paranoid: true //从此以后，该表的数据不会真正的删除，而是增加一列deletedAt，记录删除的时间
  }
);

module.exports = Admin;

```
models/Book.js
```js
const sequelize = require("./db");
const { DataTypes } = require("sequelize");

module.exports = sequelize.define(
  "Book",
  {
    name: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    imgurl: {
      type: DataTypes.STRING
    },
    publishDate: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    author: {
      type: DataTypes.STRING,
      allowNull: false,
    }
  },
  {
    paranoid: true,
  }
);

```
models/Class.js
```js
const sequelize = require("./db");
const { DataTypes } = require("sequelize");
const Student = require("./Student");
const Class = sequelize.define(
  "Class",
  {
    name: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    openDate: {
      type: DataTypes.DATE,
      allowNull: false,
    },
  },
  {
    createdAt: false,
    updatedAt: false,
    paranoid: true,
  }
);

Class.hasMany(Student);
//建立关系
module.exports = Class;

```
models/aync.js
```js
// 同步所有模型
require("./Admin");
require("./Book");
require("./Class");
require("./Student");
const sequelize = require("./db");
sequelize.sync({ alter: true }).then(() => {
  console.log("所有模型同步完成");
});

```
### 模型的增删改
![后端的三层框架（node）](./assets/三层架构.jpg)
[sequelize](https://github.com/demopark/sequelize-docs-Zh-CN)
index.js(样例)
```js
// require("./models/sync");

// 增加
const Admin = require("./models/Admin");
// const ins = Admin.build({
//   loginId: "abc",
//   loginPwd: "123",
// }); //同步方法，构建一个模型实例
// ins.loginId = "bcd";
// ins.save().then(() => {
//   console.log("新建管理员成功");
// });

Admin.create({
  loginId: "admin",
  loginPwd: "123456",
}).then((ins) => {
  console.log(ins.id, ins.loginId, ins.loginPwd);
});
```
每个数据库表建立一个service
services/adminServices.js
```js
// 管理员初始化
// 判断数据库中是否有管理员，如果没有，自动添加一个默认管理员
const Admin = require("../models/Admin");
exports.addAdmin = async function (adminObj) {
  // 应该判断adminObj的各种属性是否合理，以及账号是否已存在
  const ins = await Admin.create(adminObj);
  return ins.toJSON();
};

exports.deleteAdmin = async function (adminId) {
  // 方式1
  //   // 1.得到实例
  //   const ins = await Admin.findByPk(adminId);
  //   //   console.log(ins);
  //   // 2.删除实例
  //   if (ins) {
  //     await ins.destroy();
  //   }
  // 方式2
  const result = await Admin.destroy({
    where: {
      id: adminId,
    },
  });
  return result;
};

exports.updateAdmin = async function (id, adminObj) {
  // 方式1
  // // 1. 得到实例
  // const ins = await Admin.findByPk(id);
  // ins.loginId = adminObj.loginId;
  // // 2. 保存
  // ins.save();

  // 方式2
  const result = await Admin.update(adminObj, {
    where: {
      id,
    },
  });
  return result;
};
```
services/bookService.js
```js
const Book = require("../models/Book");
exports.addBook = async function (obj) {
  const ins = await Book.create(obj);
  return ins.toJSON();
};

exports.deleteBook = async function (id) {
  return await Book.destroy({
    where: {
      id,
    },
  });
};

exports.updateBook = async function (id, obj) {
  return await Book.update(obj, {
    where: {
      id,
    },
  });
};

```
services/classService.js
```js
const Class = require("../models/Class");
exports.addClass = async function (obj) {
  const ins = await Class.create(obj);
  return ins.toJSON();
};

exports.deleteClass = async function (id) {
  return await Class.destroy({
    where: {
      id,
    },
  });
};

exports.updateClass = async function (id, obj) {
  return await Class.update(obj, {
    where: {
      id,
    },
  });
};

```
index.js
```js
const adminServ = require("./services/adminService");
// adminServ.addAdmin({
//   loginId: "asdfasdfasdfssa",
//   loginPwd: "adsfasdfd",
// });

adminServ.deleteAdmin(4).then((r) => {
  console.log(r);
});

// adminServ
//   .updateAdmin(4, {
//     loginId: "aaaaaa",
//   })
//   .then((r) => {
//     console.log(r);
//   });

```
### 数据模拟
[mockjs](http://mockjs.com/)
安装mockjs
mock/mockClass.js
```js
const Mock = require("mockjs");
const result = Mock.mock({
  "datas|16": [
    {
      "id|+1": 1,
      name: "前端第 @id 期",
      openDate: "@date",
    },
  ],
}).datas;

const Class = require("../models/Class");
Class.bulkCreate(result);

```
mock/mockStudent.js
```js
const Mock = require("mockjs");
const result = Mock.mock({
  "datas|500-700": [
    {
      name: "@cname",
      birthdady: "@date",
      "sex|1-2": true,
      mobile: /1\d{10}/,
      //   location: "@city(true)",
      "ClassId|1-16": 0,
    },
  ],
}).datas;
console.log(result);
const Student = require("../models/Student");
Student.bulkCreate(result);
//将模拟的数据同步到数据库
```
### 数据抓取
[axios](https://github.com/axios/axios)
axios
	发送一个http请求，得到服务器的响应结果
	客户端和服务器通用
[cheerio](https://github.com/cheeriojs/cheerio#readme)
cheerio
	Jquery的核心库
	与dom无关
安装 axios和cheerio
spider/fetchBooks.js
```js
// 抓取豆瓣读书中的数据信息
const axios = require("axios").default;
const cheerio = require("cheerio");
const Book = require("../models/Book");
/**
 * 获取豆瓣读书网页的源代码
 */
async function getBooksHTML() {
  const resp = await axios.get("https://book.douban.com/latest");
  return resp.data;
}

/**
 * 从豆瓣读书中得到一个完整的网页，并从网页中分析出书籍的基本信息，然后得到一个书籍的详情页链接数组
 */
async function getBookLinks() {
  const html = await getBooksHTML();
  const $ = cheerio.load(html);
  const achorElements = $("#content .grid-12-12 li a.cover");
  const links = achorElements
    .map((i, ele) => {
      const href = ele.attribs["href"];
      return href;
    })
    .get();
  return links;
}

/**
 * 根据书籍详情页的地址，得到该书籍的详细信息
 * @param {*} detailUrl
 */
async function getBookDetail(detailUrl) {
  const resp = await axios.get(detailUrl);
  const $ = cheerio.load(resp.data);
  const name = $("h1").text().trim();
  const imgurl = $("#mainpic .nbg img").attr("src");
  const spans = $("#info span.pl");
  const authorSpan = spans.filter((i, ele) => {
    return $(ele).text().includes("作者");
  });
  const author = authorSpan.next("a").text();
  const publishSpan = spans.filter((i, ele) => {
    return $(ele).text().includes("出版年");
  });
  const publishDate = publishSpan[0].nextSibling.nodeValue.trim();
  return {
    name,
    imgurl,
    publishDate,
    author,
  };
}

/**
 * 获取所有的书籍信息
 */
async function fetchAll() {
  const links = await getBookLinks(); //得到书籍的详情页地址
  const proms = links.map((link) => {
    return getBookDetail(link);
  });
  return Promise.all(proms);
}

/**
 * 得到书籍信息，然后保存到数据库
 */
async function saveToDB() {
  const books = await fetchAll();
  await Book.bulkCreate(books);
  console.log("抓取数据并保存到了数据库");
}

saveToDB();

```
### 数据查询
[sequelize](https://github.com/demopark/sequelize-docs-Zh-CN)
3-7. 数据查询
	查询单个数据：findOne
	按照主键查询单个数据：findByPK
	查询多个数据：findAll
	查询数量：count
	包含关系：include


models/relation.js
```js
// 设置模型关系
const Class = require("./Class");
const Student = require("./Student");
Class.hasMany(Student);
Student.belongsTo(Class);
```
services/adminService.js
```js
// 管理员初始化
// 判断数据库中是否有管理员，如果没有，自动添加一个默认管理员
const Admin = require("../models/Admin");


exports.getAdminById = async function (id) {
  const result = await Admin.findByPk(id);
  if (result) {
    return result.toJSON();
  }
  return null;
};

exports.login = async function (loginId, loginPwd) {
  loginPwd = md5(loginPwd)
  const result = await Admin.findOne({
    where: {
      loginId,
      loginPwd,
    },
  });
  if (result && result.loginId === loginId) {
    return result.toJSON();
  }
  return null;
};
```
index.js
```js
require("./models/relation");
// require("./spider/fetchBooks");
const adminServ = require("./services/adminService");
// adminServ.login("bcd", "abc").then((r) => {
//   console.log(r);
// });
adminServ.getAdminById(1).then((r) => {
  console.log(r);
});
```
services/studentService.js
```js
const Student = require("../models/Student");
const { Op } = require("sequelize");
const Class = require("../models/Class");
exports.addStudent = async function (stuObj) {
  const ins = await Student.create(stuObj);
  return ins.toJSON();
};



exports.getStudentById = async function (id) {
  const result = await Student.findByPk(id);
  if (result) {
    return result.toJSON();
  }
  return null;
};

exports.getStudents = async function (
  page = 1,
  limit = 10,
  sex = -1,
  name = ""
) {
  // const results = await Student.findAll({
  //   offset: (page - 1) * limit,
  //   limit: +limit,
  // });
  // const total = await Student.count()
  // const datas = JSON.parse(JSON.stringify(results));
  // return {
  //   total,
  //   datas
  // }
  const where = {};
  if (sex !== -1) {
    where.sex = !!sex;
  }
  if (name) {
    where.name = {
      [Op.like]: `%${name}%`,
    };
  }

  const result = await Student.findAndCountAll({
    attributes: ["id", "name", "sex", "birthdady"],
    where,
    include: [Class],
    offset: (page - 1) * limit,
    limit: +limit,
  });
  return {
    total: result.count,
    datas: JSON.parse(JSON.stringify(result.rows)),
  };
};
```
index.js
```js
require("./models/relation");
// require("./spider/fetchBooks");
const stuServ = require("./services/studentService");
stuServ.getStudents(1, 10, false, "秀").then((r) => {
  console.log(r);
});
```
### MD5加密
单向加密，加密成固定长度，hash算法的一种
[github地址](https://www.npmjs.com/package/md5)
services/adminService.js
```js
const md5 = require("md5");
const Admin = require("../models/Admin");


exports.login = async function (loginId, loginPwd) {
  loginPwd = md5(loginPwd)
  const result = await Admin.findOne({
    where: {
      loginId,
      loginPwd,
    },
  });
  if (result && result.loginId === loginId) {
    return result.toJSON();
  }
  return null;
};


exports.getAdmins = async function () {
  const result = await Admin.findAll();
  return JSON.parse(JSON.stringify(result));
};
```
### moment
![时间处理模式](./assets/时间处理模式.jpg)
[官网](https://momentjs.com/)
[民间官网](http://momentjs.cn/)
```js
const moment = require("moment");
moment.locale("zh-cn");
//得到当前时间，moment对象
// console.log(moment().toString());
// console.log(moment.utc().toString());

//得到当前时间戳
// console.log(moment().valueOf(), +moment());
// console.log(moment.utc().valueOf(), +moment.utc());

//根据指定的时间格式得到时间，时间格式：xxxx-xx-xx xxxx/xx/xx  时间戳
// console.log(moment(0).toString(), +moment(0));
// console.log(moment.utc(0).toString(), +moment.utc(0));
// const value = "1970-01-01 00:00:00";
// console.log(moment(value).toString(), +moment(value));
// console.log(moment.utc(value).toString(), +moment.utc(value));

//使用日期令牌转换
//令牌是一个格式化的字符串，例如： "YYYY-MM-DD HH:mm:ss"
const formats = ["YYYY-MM-DD HH:mm:ss", "YYYY-M-D H:m:s", "x"];
// console.log(moment.utc("1970-01-01 00:00:00", formats, true).toString());
// console.log(moment.utc("1970-1-1 0:0:0", formats, true).toString());
// console.log(moment.utc(0, formats, true).toString());
// const invalidMoment = moment.utc(
//   "Thu Jan 01 1970 00:00:00 GMT+0000",
//   formats,
//   true
// );
// console.log(invalidMoment.isValid()); // false
// console.log(invalidMoment.toString());
// console.log(+invalidMoment);

// 显示（发生在客户端）
// const m = moment.utc("2015-1-5 23:00:01", formats, true);
// console.log(m.local().format("YYYY年MM月DD日 HH点mm分ss秒"));

// const m = moment("2015-1-5 23:00:01", formats, true);
// const toServer = m.utc().format("YYYY-MM-DD HH:mm:ss");
// console.log(toServer);

const m = moment.utc("2020-4-14 9:00:01", formats, true);
console.log(m.local().fromNow());
```
### 数据验证
相关库
[validator](https://github.com/validatorjs/validator.js)
		用于验证某个字符串是否满足某个规则
[validate.js](http://validatejs.org/)
		用于验证某个对象的树形是否满足某些规则

models/relation.js
```js
// 设置模型关系
const Class = require("./Class");
const Student = require("./Student");
Class.hasMany(Student);
Student.belongsTo(Class);
```
models/sync.js
```js
// 同步所有模型
require("./Admin");
require("./Book");
require("./Class");
require("./Student");
const sequelize = require("./db");
sequelize.sync({ alter: true }).then(() => {
  console.log("所有模型同步完成");
});

```
models/init.js
```js
require("./relation");
require("./sync");
```
services/init.js
```js
const validate = require("validate.js");
const moment = require("moment");

validate.extend(validate.validators.datetime, {
  /**
   * 该函数会自动用于日期格式转换
   * 它会在验证时自动触发，它需要将任何数据转换为时间戳返回
   * 如果无法转换，返回NaN
   * @param {*} value 传入要转换的值
   * @param {*} options 针对某个属性的验证配置
   */
  parse(value, options) {
    let formats = ["YYYY-MM-DD HH:mm:ss", "YYYY-M-D H:m:s", "x"];
    if (options.dateOnly) {
      formats = ["YYYY-MM-DD", "YYYY-M-D", "x"];
    }
    return +moment.utc(value, formats, true);
  },
  /**
   * 用户显示错误消息时，使用的显示字符串
   */
  format(value, options) {
    let format = "YYYY-MM-DD";
    if (!options.dateOnly) {
      format += " HH:mm:ss";
    }
    return moment.utc(value).format(format);
  },
});
```
init.js
```js
require("./models/init");
require("./services/init");
```
index.js
```js
require("./init");

const stuServ = require("./services/studentService");
stuServ
  .addStudent({
    name: "adfd",
    birthday: "2010-3-5",
    sex: true,
    mobile: "15454545444",
    ClassId: 3,
    deletedAt: "2010-1-1",
    a: 3,
    b: 4,
  })
  .catch((err) => {
    console.log(err);
  });
```
### 访问器和虚拟字段
sequelize  get
models/Student.js
```js
const sequelize = require("./db");
const { DataTypes } = require("sequelize");
const moment = require("moment");

module.exports = sequelize.define(
  "Student",
  {
    name: {
      type: DataTypes.STRING,
      allowNull: false,
    },
    birthday: {
      type: DataTypes.DATE,
      allowNull: false,
      get() {
        return this.getDataValue("birthday").getTime();
      },
    },
    age: {
      type: DataTypes.VIRTUAL,
      get() {
        const now = moment.utc();
        const birth = moment.utc(this.birthday);
        return now.diff(birth, "y"); //得到两个日期的年份的差异
      },
    },
    sex: {
      type: DataTypes.BOOLEAN,
      allowNull: false,
    },
    mobile: {
      type: DataTypes.STRING(11),
      allowNull: false,
    },
  },
  {
    createdAt: false,
    updatedAt: false,
    paranoid: true,
  }
);
```
services/studentService.js
```js
const Student = require("../models/Student");
const { Op } = require("sequelize");
const Class = require("../models/Class");
const validate = require("validate.js");
const moment = require("moment");
const { pick } = require("../util/propertyHelper");
exports.addStudent = async function (stuObj) {
  stuObj = pick(stuObj, "name", "birthday", "sex", "mobile", "ClassId");
  console.log(stuObj);
  validate.validators.classExits = async function (value) {
    const c = await Class.findByPk(value);
    if (c) {
      return;
    }
    return "is not exist";
  };

  const rule = {
    //验证规则
    name: {
      presence: {
        allowEmpty: false,
      },
      type: "string",
      length: {
        minimum: 1,
        maximum: 10,
      },
    },
    birthday: {
      presence: {
        allowEmpty: false,
      },
      datetime: {
        dateOnly: true,
        earliest: +moment.utc().subtract(100, "y"),
        latest: +moment.utc().subtract(5, "y"),
      },
    },
    sex: {
      presence: true,
      type: "boolean",
    },
    mobile: {
      presence: {
        allowEmpty: false,
      },
      format: /1\d{10}/,
    },
    ClassId: {
      presence: true,
      numericality: {
        onlyInteger: true,
        strict: false,
      },
      classExits: true,
    },
  };
  await validate.async(stuObj, rule);
  const ins = await Student.create(stuObj);
  return ins.toJSON();
};

exports.deleteStudent = async function (id) {
  return await Student.destroy({
    where: {
      id,
    },
  });
};

exports.updateStudent = async function (id, obj) {
  return await Student.update(obj, {
    where: {
      id,
    },
  });
};

exports.getStudentById = async function (id) {
  const result = await Student.findByPk(id);
  if (result) {
    return result.toJSON();
  }
  return null;
};

exports.getStudents = async function (
  page = 1,
  limit = 10,
  sex = -1,
  name = ""
) {
  // const results = await Student.findAll({
  //   offset: (page - 1) * limit,
  //   limit: +limit,
  // });
  // const total = await Student.count()
  // const datas = JSON.parse(JSON.stringify(results));
  // return {
  //   total,
  //   datas
  // }
  const where = {};
  if (sex !== -1) {
    where.sex = !!sex;
  }
  if (name) {
    where.name = {
      [Op.like]: `%${name}%`,
    };
  }

  const result = await Student.findAndCountAll({
    attributes: ["id", "name", "sex", "birthday", "age"],
    where,
    include: [Class],
    offset: (page - 1) * limit,
    limit: +limit,
  });
  return {
    total: result.count,
    datas: JSON.parse(JSON.stringify(result.rows)),
  };
};

```
### 日志记录
[log4js](https://log4js-node.github.io/log4js-node/)
概念
	level：日志级别
		例如调试日志、信息日志、错误日志等等
		见源码中的示意图
	category：日志分类
		例如：sql日志、请求日志等等
	appender：日志出口
		应该把日志写到哪？
		日志的书写格式是什么（layouts）
![log等级](./assets/log等级.jpg)
models/db.js
```js
const { Sequelize } = require("sequelize");
const { sqlLogger } = require("../logger");
const sequelize = new Sequelize("lby", "root", "123123", {
  host: "localhost",
  dialect: "mysql",
  logging: (msg) => {
    sqlLogger.debug(msg);
  },
});

module.exports = sequelize;
```
loggle.js
```js
const log4js = require("log4js");
const path = require("path");

log4js.configure({
  appenders: {
    sql: {
      //定义一个sql日志出口
      type: "dateFile",
      filename: path.resolve(__dirname, "logs", "sql", "logging.log"),
      maxLogSize: 1024 * 1024, //配置文件的最大字节数
      keepFileExt: true,
      layout: {
        type: "pattern",
        pattern: "%c [%d{yyyy-MM-dd hh:mm:ss}] [%p]: %m%n",
      },
    },
    default: {
      type: "stdout",
    },
  },
  categories: {
    sql: {
      appenders: ["sql"], //该分类使用出口sql的配置写入日志
      level: "all",
    },
    default: {
      appenders: ["default"],
      level: "all",
    },
  },
});

process.on("exit", () => {
  log4js.shutdown();
});

const sqlLogger = log4js.getLogger("sql");
const defaultLogger = log4js.getLogger();

exports.sqlLogger = sqlLogger;
exports.logger = defaultLogger;
```

# express
### express的基本使用
[express官网](http://expressjs.com/)
[express民间网站](https://www.expressjs.com.cn/)

```js
const express = require("express");
const http = require("http");
const app = express(); //创建一个express应用
// app实际上是一个函数，用于处理请求的函数
const server = http.createServer(app);
const port = 5008;
server.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```
```js
require("./init");
const express = require("express");
const app = express(); //创建一个express应用

// 配置一个请求映射，如果请求方法和请求路径均满足匹配，交给处理函数进行处理
// app.请求方法("请求路径", 处理函数)

app.get("/news/:id", (req, res) => {
  // req 和 res 是被express封装过后的对象
  // 获取请求信息
  console.log("请求头", req.headers); //获取请求头，对象
  console.log("请求路径", req.path);
  console.log("query", req.query);
  console.log("params", req.params);

  // 响应
  //   res.send({
  //     id: 123,
  //     name: "成哥",
  //     age: 18,
  //   });
  //   res.setHeader("a", "123");
  //   res.send([2, 3, 4]);
  //   res.status(302).header("location", "https://duyi.ke.qq.com").end();
  //   res.status(302).location("https://duyi.ke.qq.com").end();
  res.redirect(302, "https://duyi.ke.qq.com");
});

// 匹配任何get请求
app.get("*", (req, res) => {
  console.log("abc");
});

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```
### nodemon
[nodemon](https://github.com/remy/nodemon#nodemon)
	nodemon是一个监视器，用于监控工程中的文件变化，如果发现文件有变化，可以执行一段脚本
npm i -D nodemon 安装
nodemon index.js执行程序
nodemon -x npm run serve 执行脚本语言
配置文件
nodemon.json
```json
{
  "env": {
    "NODE_ENV": "development"
  },
  "watch": ["*.js", "*.json"],
  "ignore": ["package*.json", "nodemon.json", "node_modules", "public"]
}
```
### express中间件
![中间件示意图](./assets/中间件示意图.jpg)
express中间件
- 当匹配到了请求后
		交给第一个处理函数处理
		函数中需要手动的交给后续中间件处理
- 中间件处理的细节
	- 如果后续已经没有了中间件
			express发现如果响应没有结束
			express会响应404
	- 如果中间件发生了错误
			不会停止服务器
			相当于调用了 next(错误对象)
			寻找后续的错误处理中间件
				如果没有，则响应500
routes/errorMiddleware.js
```js
// 处理错误的中间件

module.exports = (err, req, res, next) => {
  if (err) {
    const errObj = {
      code: 500,
      msg: err instanceof Error ? err.message : err,
    };
    //发生了错误
    res.status(500).send(errObj);
  } else {
    next();
  }
};

```
routes/staticMiddleware.js
```js
module.exports = (req, res, next) => {
  if (req.path.startsWith("/api")) {
    // 说明你请求的是 api 接口
    next();
  } else {
    // 说明你想要的是静态资源
    if (true) {
      res.send("静态资源");
    } else {
      next();
    }
  }
};
```
routes/init.js
```js
const express = require("express");
const app = express(); //创建一个express应用

app.use(require("./staticMiddleware"));

app.get("/news/abc", (req, res, next) => {
  console.log("handler1");
  // throw new Error("abc")
  // 相当于 next(new Error("abc"))
  next(new Error("abc"));
  //   next();
});

//能匹配  /news  /news/abc   /news/123   /news/ab/adfs
//不能匹配  /n   /a   /   /newsabc
app.use("/news", require("./errorMiddleware"));

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});
```
### 常用中间件
-	express.static()
-	express.json()
  注意：除了错误级别的中间件，其他的中间件，必须在路由之前进行配置
  通过 express.json() 这个中间件，解析表单中的 JSON 格式的数据
-	express.urlencoded()
  通过 express.urlencoded() 这个中间件，来解析表单中的 url-encoded 格式的数据
```js
const express = require("express");
const app = express(); //创建一个express应用
const path = require("path");
const staticRoot = path.resolve(__dirname, "../public");

/**
 * 下面这段代码的作用：
 * 当请求时，会根据请求路径(req.path)，从指定的目录中寻找是否存在该文件，如果存在，直接响应文件内容，而不再移交给后续的中间件
 * 如果不存在文件，则直接移交给后续的中间件处理
 * 默认情况下，如果映射的结果是一个目录，则会自动使用index.html文件
 */
app.use(express.static(staticRoot));
// app.use("/static", (req, res) => {
//   console.log(req.baseUrl, req.path);
// });

app.use(express.urlencoded({ extended: true }));
app.use(express.json());
// app.use(require("./myUrlEncoded"));

app.post("/api/student", (req, res) => {
  console.log(req.body);
});

app.use(require("./errorMiddleware"));

const port = 5008;
app.listen(port, () => {
  console.log(`server listen on ${port}`);
});

```

### express路由
routes/getSendResults.js
```js
exports.getErr = function (err = "server internal error", errCode = 500) {
  return {
    code: errCode,
    msg: err,
  };
};

exports.getResult = function (result) {
  return {
    code: 0,
    msg: "",
    data: result,
  };
};

exports.asyncHandler = (handler) => {
  return async (req, res, next) => {
    try {
      const result = await handler(req, res, next);
      res.send(exports.getResult(result));
    } catch (err) {
      next(err);
    }
  };
};
```

routes/api/student.js
```js
const express = require("express");
const router = express.Router();
const stuServ = require("../../services/studentService");
const { asyncHandler } = require("../getSendResult");

router.get(
  "/",
  asyncHandler(async (req, res) => {
    const page = req.query.page || 1;
    const limit = req.query.limit || 10;
    const sex = req.query.sex || -1;
    const name = req.query.name || "";
    return await stuServ.getStudents(page, limit, sex, name);
  })
);

router.get(
  "/:id",
  asyncHandler(async (req, res) => {
    return await stuServ.getStudentById(req.params.id);
  })
);

router.post(
  "/",
  asyncHandler(async (req, res, next) => {
    return await stuServ.addStudent(req.body);
  })
);

router.delete(
  "/:id",
  asyncHandler(async (req, res, next) => {
    return await stuServ.deleteStudent(req.params.id);
  })
);

router.put(
  "/:id",
  asyncHandler(async (req, res, next) => {
    return await stuServ.updateStudent(req.params.id, req.body);
  })
);
module.exports = router;
```

### cookie的基本概念
#### 一个不大不小的问题

假设服务器有一个接口，通过请求这个接口，可以添加一个管理员

但是，不是任何人都有权力做这种操作的

那么服务器如何知道请求接口的人是有权力的呢？

答案是：只有登录过的管理员才能做这种操作

可问题是，客户端和服务器的传输使用的是http协议，http协议是无状态的，什么叫无状态，就是**服务器不知道这一次请求的人，跟之前登录请求成功的人是不是同一个人**

![](http://mdrs.yuanjin.tech/img/image-20200417161014030.png)

![](http://mdrs.yuanjin.tech/img/image-20200417161244373.png)

由于http协议的无状态，服务器**忘记**了之前的所有请求，它无法确定这一次请求的客户端，就是之前登录成功的那个客户端。

> 你可以把服务器想象成有着严重脸盲症的东哥，他没有办法分清楚跟他说话的人之前做过什么

于是，服务器想了一个办法

它按照下面的流程来认证客户端的身份

1. 客户端登录成功后，服务器会给客户端一个出入证（令牌 token）
2. 后续客户端的每次请求，都必须要附带这个出入证（令牌 token）

![](http://mdrs.yuanjin.tech/img/image-20200417161950450.png)

服务器发扬了认证不认人的优良传统，就可以很轻松的识别身份了。

但是，用户不可能只在一个网站登录，于是客户端会收到来自各个网站的出入证，因此，就要求客户端要有一个类似于卡包的东西，能够具备下面的功能：

1. **能够存放多个出入证**。这些出入证来自不同的网站，也可能是一个网站有多个出入证，分别用于出入不同的地方
2. **能够自动出示出入证**。客户端在访问不同的网站时，能够自动的把对应的出入证附带请求发送出去。
3. **正确的出示出入证**。客户端不能将肯德基的出入证发送给麦当劳。
4. **管理出入证的有效期**。客户端要能够自动的发现那些已经过期的出入证，并把它从卡包内移除。

能够满足上面所有要求的，就是cookie

cookie类似于一个卡包，专门用于存放各种出入证，并有着一套机制来自动管理这些证件。

卡包内的每一张卡片，称之为**一个cookie**。

#### cookie的组成

cookie是浏览器中特有的一个概念，它就像浏览器的专属卡包，管理着各个网站的身份信息。

每个cookie就相当于是属于某个网站的一个卡片，它记录了下面的信息：

- key：键，比如「身份编号」
- value：值，比如袁小进的身份编号「14563D1550F2F76D69ECBF4DD54ABC95」，这有点像卡片的条形码，当然，它可以是任何信息
- domain：域，表达这个cookie是属于哪个网站的，比如`yuanjin.tech`，表示这个cookie是属于`yuanjin.tech`这个网站的
- path：路径，表达这个cookie是属于该网站的哪个基路径的，就好比是同一家公司不同部门会颁发不同的出入证。比如`/news`，表示这个cookie属于`/news`这个路径的。（后续详细解释）
- secure：是否使用安全传输（后续详细解释）
- expire：过期时间，表示该cookie在什么时候过期

当浏览器向服务器发送一个请求的时候，它会瞄一眼自己的卡包，看看哪些卡片适合附带捎给服务器

如果一个cookie**同时满足**以下条件，则这个cookie会被附带到请求中

- cookie没有过期
- cookie中的域和这次请求的域是匹配的
  - 比如cookie中的域是`yuanjin.tech`，则可以匹配的请求域是`yuanjin.tech`、`www.yuanjin.tech`、`blogs.yuanjin.tech`等等
  - 比如cookie中的域是`www.yuanjin.tech`，则只能匹配`www.yuanjin.tech`这样的请求域
  - cookie是不在乎端口的，只要域匹配即可
- cookie中的path和这次请求的path是匹配的
  - 比如cookie中的path是`/news`，则可以匹配的请求路径可以是`/news`、`/news/detail`、`/news/a/b/c`等等，但不能匹配`/blogs`
  - 如果cookie的path是`/`，可以想象，能够匹配所有的路径
- 验证cookie的安全传输
  - 如果cookie的secure属性是true，则请求协议必须是`https`，否则不会发送该cookie
  - 如果cookie的secure属性是false，则请求协议可以是`http`，也可以是`https`

如果一个cookie满足了上述的所有条件，则浏览器会把它自动加入到这次请求中

具体加入的方式是，**浏览器会将符合条件的cookie，自动放置到请求头中**，例如，当我在浏览器中访问百度的时候，它在请求头中附带了下面的cookie：

![](http://mdrs.yuanjin.tech/img/image-20200417170328584.png)

看到打马赛克的地方了吗？这部分就是通过请求头`cookie`发送到服务器的，它的格式是`键=值; 键=值; 键=值; ...`，每一个键值对就是一个符合条件的cookie。

**cookie中包含了重要的身份信息，永远不要把你的cookie泄露给别人！！！**否则，他人就拿到了你的证件，有了证件，就具备了为所欲为的可能性。

#### 如何设置cookie

由于cookie是保存在浏览器端的，同时，很多证件又是服务器颁发的

所以，cookie的设置有两种模式：

- 服务器响应：这种模式是非常普遍的，当服务器决定给客户端颁发一个证件时，它会在响应的消息中包含cookie，浏览器会自动的把cookie保存到卡包中
- 客户端自行设置：这种模式少见一些，不过也有可能会发生，比如用户关闭了某个广告，并选择了「以后不要再弹出」，此时就可以把这种小信息直接通过浏览器的JS代码保存到cookie中。后续请求服务器时，服务器会看到客户端不想要再次弹出广告的cookie，于是就不会再发送广告过来了。

##### 服务器端设置cookie

服务器可以通过设置响应头，来告诉浏览器应该如何设置cookie

响应头按照下面的格式设置：

```yaml
set-cookie: cookie1
set-cookie: cookie2
set-cookie: cookie3
...
```

通过这种模式，就可以在一次响应中设置多个cookie了，具体设置多少个cookie，设置什么cookie，根据你的需要自行处理

其中，每个cookie的格式如下：

```
键=值; path=?; domain=?; expire=?; max-age=?; secure; httponly
```

每个cookie除了键值对是必须要设置的，其他的属性都是可选的，并且顺序不限

当这样的响应头到达客户端后，**浏览器会自动的将cookie保存到卡包中，如果卡包中已经存在一模一样的卡片（其他key、path、domain相同），则会自动的覆盖之前的设置**。

下面，依次说明每个属性值：

- **path**：设置cookie的路径。如果不设置，浏览器会将其自动设置为当前请求的路径。比如，浏览器请求的地址是`/login`，服务器响应了一个`set-cookie: a=1`，浏览器会将该cookie的path设置为请求的路径`/login`
- **domain**：设置cookie的域。如果不设置，浏览器会自动将其设置为当前的请求域，比如，浏览器请求的地址是`http://www.yuanjin.tech`，服务器响应了一个`set-cookie: a=1`，浏览器会将该cookie的domain设置为请求的域`www.yuanjin.tech`
  - 这里值得注意的是，如果服务器响应了一个无效的域，浏览器是不认的
  - 什么是无效的域？就是响应的域连根域都不一样。比如，浏览器请求的域是`yuanjin.tech`，服务器响应的cookie是`set-cookie: a=1; domain=baidu.com`，这样的域浏览器是不认的。
  - 如果浏览器连这样的情况都允许，就意味着张三的服务器，有权利给用户一个cookie，用于访问李四的服务器，这会造成很多安全性的问题
- **expire**：设置cookie的过期时间。这里必须是一个有效的GMT时间，即格林威治标准时间字符串，比如`Fri, 17 Apr 2020 09:35:59 GMT`，表示格林威治时间的`2020-04-17 09:35:59`，即北京时间的`2020-04-17 17:35:59`。当客户端的时间达到这个时间点后，会自动销毁该cookie。
- **max-age**：设置cookie的相对有效期。expire和max-age通常仅设置一个即可。比如设置`max-age`为`1000`，浏览器在添加cookie时，会自动设置它的`expire`为当前时间加上1000秒，作为过期时间。
  - 如果不设置expire，又没有设置max-age，则表示会话结束后过期。
  - 对于大部分浏览器而言，关闭所有浏览器窗口意味着会话结束。
- **secure**：设置cookie是否是安全连接。如果设置了该值，则表示该cookie后续只能随着`https`请求发送。如果不设置，则表示该cookie会随着所有请求发送。
- **httponly**：设置cookie是否仅能用于传输。如果设置了该值，表示该cookie仅能用于传输，而不允许在客户端通过JS获取，这对防止跨站脚本攻击（XSS）会很有用。 
  - 关于如何通过JS获取，后续会讲解
  - 关于什么是XSS，不在本文讨论范围

下面来一个例子，客户端通过`post`请求服务器`http://yuanjin.tech/login`，并在消息体中给予了账号和密码，服务器验证登录成功后，在响应头中加入了以下内容：

```
set-cookie: token=123456; path=/; max-age=3600; httponly
```

当该响应到达浏览器后，浏览器会创建下面的cookie：

```yaml
key: token
value: 123456
domain: yuanjin.tech
path: /
expire: 2020-04-17 18:55:00 #假设当前时间是2020-04-17 17:55:00
secure: false  #任何请求都可以附带这个cookie，只要满足其他要求
httponly: true #不允许JS获取该cookie
```

于是，随着浏览器后续对服务器的请求，只要满足要求，这个cookie就会被附带到请求头中传给服务器：

```yaml
cookie: token=123456; 其他cookie...
```

现在，还剩下最后一个问题，就是如何删除浏览器的一个cookie呢？

如果要删除浏览器的cookie，只需要让服务器响应一个同样的域、同样的路径、同样的key，只是时间过期的cookie即可

**所以，删除cookie其实就是修改cookie**

下面的响应会让浏览器删除`token`

```yaml
cookie: token=; domain=yuanjin.tech; path=/; max-age=-1
```

浏览器按照要求修改了cookie后，会发现cookie已经过期，于是自然就会删除了。

> 无论是修改还是删除，都要注意cookie的域和路径，因为完全可能存在域或路径不同，但key相同的cookie
>
> 因此无法仅通过key确定是哪一个cookie

##### 客户端设置cookie

既然cookie是存放在浏览器端的，所以浏览器向JS公开了接口，让其可以设置cookie

```js
document.cookie = "键=值; path=?; domain=?; expire=?; max-age=?; secure";
```

可以看出，在客户端设置cookie，和服务器设置cookie的格式一样，只是有下面的不同

- 没有httponly。因为httponly本来就是为了限制在客户端访问的，既然你是在客户端配置，自然失去了限制的意义。
- path的默认值。在服务器端设置cookie时，如果没有写path，使用的是请求的path。而在客户端设置cookie时，也许根本没有请求发生。因此，path在客户端设置时的默认值是当前网页的path
- domain的默认值。和path同理，客户端设置时的默认值是当前网页的domain
- 其他：一样
- 删除cookie：和服务器也一样，修改cookie的过期时间即可

#### 总结

以上，就是cookie原理部分的内容。

如果把它用于登录场景，就是如下的流程：

**登录请求**

1. 浏览器发送请求到服务器，附带账号密码
2. 服务器验证账号密码是否正确，如果不正确，响应错误，如果正确，在响应头中设置cookie，附带登录认证信息（至于登录认证信息是设么样的，如何设计，要考虑哪些问题，就是另一个话题了，可以百度 jwt）
3. 客户端收到cookie，浏览器自动记录下来



**后续请求**

1. 浏览器发送请求到服务器，希望添加一个管理员，并将cookie自动附带到请求中
2. 服务器先获取cookie，验证cookie中的信息是否正确，如果不正确，不予以操作，如果正确，完成正常的业务流程

routes/api/admin.js
```js
const express = require("express");
const router = express.Router();
const adminServ = require("../../services/adminService");
const { asyncHandler } = require("../getSendResult");

router.post(
  "/login",
  asyncHandler(async (req, res) => {
    const result = await adminServ.login(req.body.loginId, req.body.loginPwd);
    if(result){
        //登录成功
        res.header("set-cookie", `token=${result.id}; path=/; domain=localhost; max-age=3600; httponly`);
    }
    return result;
  })
);

module.exports = router;
```
### 断点调试
### 跨域之JSONP
服务端
```js
const result = await stuServ.getStudents(page, limit, sex, name);
  const json = JSON.stringify(result);
  const script = `callback(${json})`;
  res.header("content-type", "application/javascript").send(script);
```
客户端
```js
function jsonp(url) {
  const script = document.createElement("script");
  script.src = url;
  document.body.appendChild(script);
  script.onload = function () {
    script.remove();
  };
}

function callback(data) {
  console.log(data);
}

jsonp("http://localhost:5008/api/student");
```
### 跨域之CORS
> 阅读本文，你需要首先知道：
>
> 1. 浏览器的同源策略
> 2. 跨域问题
> 3. JSONP原理
> 4. cookie原理

JSONP并不是一个好的跨域解决方案，它至少有着下面两个严重问题：

1. **会打乱服务器的消息格式**：JSONP要求服务器响应一段JS代码，但在非跨域的情况下，服务器又需要响应一个正常的JSON格式
2. **只能完成GET请求**：JSONP的原理会要求浏览器端生成一个`script`元素，而`script`元素发出的请求只能是`get`请求

所以，CORS是一种更好的跨域解决方案。

#### 概述

`CORS`是基于`http1.1`的一种跨域解决方案，它的全称是**C**ross-**O**rigin **R**esource **S**haring，跨域资源共享。

它的总体思路是：**如果浏览器要跨域访问服务器的资源，需要获得服务器的允许**

![image-20200421152122793](http://mdrs.yuanjin.tech/img/image-20200421152122793.png)

而要知道，一个请求可以附带很多信息，从而会对服务器造成不同程度的影响

比如有的请求只是获取一些新闻，有的请求会改动服务器的数据

针对不同的请求，CORS规定了三种不同的交互模式，分别是：

- **简单请求**
- **需要预检的请求**
- **附带身份凭证的请求**

这三种模式从上到下层层递进，请求可以做的事越来越多，要求也越来越严格。

下面分别说明三种请求模式的具体规范。

#### 简单请求

当浏览器端运行了一段ajax代码（无论是使用XMLHttpRequest还是fetch api），浏览器会首先判断它属于哪一种请求模式

##### 简单请求的判定

当请求**同时满足**以下条件时，浏览器会认为它是一个简单请求：

1. **请求方法属于下面的一种：**
   - get
   - post
   - head
2. **请求头仅包含安全的字段，常见的安全字段如下：**
   - `Accept`
   - `Accept-Language`
   - `Content-Language`
   - `Content-Type`
   - `DPR`
   - `Downlink`
   - `Save-Data`
   - `Viewport-Width`
   - `Width`

3. **请求头如果包含`Content-Type`，仅限下面的值之一：**
   - `text/plain`
   - `multipart/form-data`
   - `application/x-www-form-urlencoded`

如果以上三个条件同时满足，浏览器判定为简单请求。

下面是一些例子：

```js
// 简单请求
fetch("http://crossdomain.com/api/news");

// 请求方法不满足要求，不是简单请求
fetch("http://crossdomain.com/api/news", {
  method:"PUT"
})

// 加入了额外的请求头，不是简单请求
fetch("http://crossdomain.com/api/news", {
  headers:{
    a: 1
  }
})

// 简单请求
fetch("http://crossdomain.com/api/news", {
  method: "post"
})

// content-type不满足要求，不是简单请求
fetch("http://crossdomain.com/api/news", {
  method: "post",
  headers: {
    "content-type": "application/json"
  }
})
```

##### 简单请求的交互规范

当浏览器判定某个**ajax跨域请求**是**简单请求**时，会发生以下的事情

1. **请求头中会自动添加`Origin`字段**

比如，在页面`http://my.com/index.html`中有以下代码造成了跨域

```js
// 简单请求
fetch("http://crossdomain.com/api/news");
```

请求发出后，请求头会是下面的格式：

```
GET /api/news/ HTTP/1.1
Host: crossdomain.com
Connection: keep-alive
...
Referer: http://my.com/index.html
Origin: http://my.com
```

看到最后一行没，`Origin`字段会告诉服务器，是哪个源地址在跨域请求

2. **服务器响应头中应包含`Access-Control-Allow-Origin`**

当服务器收到请求后，如果允许该请求跨域访问，需要在响应头中添加`Access-Control-Allow-Origin`字段

该字段的值可以是：

- *：表示我很开放，什么人我都允许访问
- 具体的源：比如`http://my.com`，表示我就允许你访问

> 实际上，这两个值对于客户端`http://my.com`而言，都一样，因为客户端才不会管其他源服务器允不允许，就关心自己是否被允许
>
> 当然，服务器也可以维护一个可被允许的源列表，如果请求的`Origin`命中该列表，才响应`*`或具体的源
>
> **为了避免后续的麻烦，强烈推荐响应具体的源**

假设服务器做出了以下的响应：

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
...

消息体中的数据
```

当浏览器看到服务器允许自己访问后，高兴的像一个两百斤的孩子，于是，它就把响应顺利的交给js，以完成后续的操作

下图简述了整个交互过程

![image-20200421162846480](http://mdrs.yuanjin.tech/img/image-20200421162846480.png)

#### 需要预检的请求

简单的请求对服务器的威胁不大，所以允许使用上述的简单交互即可完成。

但是，如果浏览器不认为这是一种简单请求，就会按照下面的流程进行：

1. **浏览器发送预检请求，询问服务器是否允许**
2. **服务器允许**
3. **浏览器发送真实请求**
4. **服务器完成真实的响应**

比如，在页面`http://my.com/index.html`中有以下代码造成了跨域

```js
// 需要预检的请求
fetch("http://crossdomain.com/api/user", {
  method:"POST", // post 请求
  headers:{  // 设置请求头
    a: 1,
    b: 2,
    "content-type": "application/json"
  },
  body: JSON.stringify({ name: "袁小进", age: 18 }) // 设置请求体
});
```

浏览器发现它不是一个简单请求，则会按照下面的流程与服务器交互

1. **浏览器发送预检请求，询问服务器是否允许**

```
OPTIONS /api/user HTTP/1.1
Host: crossdomain.com
...
Origin: http://my.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: a, b, content-type
```

可以看出，这并非我们想要发出的真实请求，请求中不包含我们的响应头，也没有消息体。

这是一个预检请求，它的目的是询问服务器，是否允许后续的真实请求。

预检请求**没有请求体**，它包含了后续真实请求要做的事情

预检请求有以下特征：

- 请求方法为`OPTIONS`
- 没有请求体
- 请求头中包含
  - `Origin`：请求的源，和简单请求的含义一致
  - `Access-Control-Request-Method`：后续的真实请求将使用的请求方法
  - `Access-Control-Request-Headers`：后续的真实请求会改动的请求头

2. **服务器允许**

服务器收到预检请求后，可以检查预检请求中包含的信息，如果允许这样的请求，需要响应下面的消息格式

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: a, b, content-type
Access-Control-Max-Age: 86400
...
```

对于预检请求，不需要响应任何的消息体，只需要在响应头中添加：

- `Access-Control-Allow-Origin`：和简单请求一样，表示允许的源
- `Access-Control-Allow-Methods`：表示允许的后续真实的请求方法
- `Access-Control-Allow-Headers`：表示允许改动的请求头
- `Access-Control-Max-Age`：告诉浏览器，多少秒内，对于同样的请求源、方法、头，都不需要再发送预检请求了

3. **浏览器发送真实请求**

预检被服务器允许后，浏览器就会发送真实请求了，上面的代码会发生下面的请求数据

```
POST /api/user HTTP/1.1
Host: crossdomain.com
Connection: keep-alive
...
Referer: http://my.com/index.html
Origin: http://my.com

{"name": "袁小进", "age": 18 }
```

4. **服务器响应真实请求**

```
HTTP/1.1 200 OK
Date: Tue, 21 Apr 2020 08:03:35 GMT
...
Access-Control-Allow-Origin: http://my.com
...

添加用户成功
```



可以看出，当完成预检之后，后续的处理与简单请求相同

下图简述了整个交互过程

![image-20200421165913320](http://mdrs.yuanjin.tech/img/image-20200421165913320.png)

#### 附带身份凭证的请求

默认情况下，ajax的跨域请求并不会附带cookie，这样一来，某些需要权限的操作就无法进行

不过可以通过简单的配置就可以实现附带cookie

```js
// xhr
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;

// fetch api
fetch(url, {
  credentials: "include"
})
```

这样一来，该跨域的ajax请求就是一个*附带身份凭证的请求*

当一个请求需要附带cookie时，无论它是简单请求，还是预检请求，都会在请求头中添加`cookie`字段

而服务器响应时，需要明确告知客户端：服务器允许这样的凭据

告知的方式也非常的简单，只需要在响应头中添加：`Access-Control-Allow-Credentials: true`即可

对于一个附带身份凭证的请求，若服务器没有明确告知，浏览器仍然视为跨域被拒绝。

另外要特别注意的是：**对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin 的值为*`**。这就是为什么不推荐使用*的原因

#### 一个额外的补充

在跨域访问时，JS只能拿到一些最基本的响应头，如：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。

`Access-Control-Expose-Headers`头让服务器把允许浏览器访问的头放入白名单，例如：

```
Access-Control-Expose-Headers: authorization, a, b
```

这样JS就能够访问指定的响应头了。

*安装利用cors库*
### session
下载安装`express-session`
![session详解](./assets/session原理示意图.jpg)
```js
// 加入cookie-parser 中间件
// 加入之后，会在req对象中注入cookies属性，用于获取所有请求传递过来的cookie
// 加入之后，会在res对象中注入cookie方法，用于设置cookie
const cookieParser = require("cookie-parser");
app.use(cookieParser());
```

### jwt
随着前后端分离的发展，以及数据中心的建立，越来越多的公司会创建一个中心服务器，服务于各种产品线。

而这些产品线上的产品，它们可能有着各种终端设备，包括但不仅限于浏览器、桌面应用、移动端应用、平板应用、甚至智能家居

![image-20200422163727151](http://mdrs.yuanjin.tech/img/image-20200422163727151.png)

> 实际上，不同的产品线通常有自己的服务器，产品内部的数据一般和自己的服务器交互。
>
> 但中心服务器仍然有必要存在，因为同一家公司的产品总是会存在共享的数据，比如用户数据

这些设备与中心服务器之间会进行http通信

一般来说，中心服务器至少承担着认证和授权的功能，例如登录：各种设备发送消息到中心服务器，然后中心服务器响应一个身份令牌（参见[cookie原理详解](http://www.yuanjin.tech/article/98)）

当这种结构出现后，就出现一个问题：它们之间还能使用传统的cookie方式传递令牌信息吗？

其实，也是可以的🐶，因为cookie在传输中无非是一个消息头而已，只不过浏览器对这个消息头有特殊处理罢了。

但浏览器之外的设备肯定不喜欢cookie，因为浏览器有着对cookie完善的管理机制，但是在其他设备上，就需要开发者自己手动处理了

jwt的出现就是为了解决这个问题

#### 概述

jwt全称`Json Web Token`，强行翻译过来就是`json格式的互联网令牌`（算了，还是不要强行翻译了🐷）

它要解决的问题，就是为多种终端设备，提供**统一的、安全的**令牌格式

![image-20200422165350268](http://mdrs.yuanjin.tech/img/image-20200422165350268.png)

因此，jwt只是一个令牌格式而已，你可以把它存储到cookie，也可以存储到localstorage，没有任何限制！

同样的，对于传输，你可以使用任何传输方式来传输jwt，一般来说，我们会使用消息头来传输它

比如，当登录成功后，服务器可以给客户端响应一个jwt：

```
HTTP/1.1 200 OK
...
set-cookie:token=jwt令牌
authorization:jwt令牌
...

{..., token:jwt令牌}
```

可以看到，jwt令牌可以出现在响应的任何一个地方，客户端和服务器自行约定即可。

> 当然，它也可以出现在响应的多个地方，比如为了充分利用浏览器的cookie，同时为了照顾其他设备，也可以让jwt出现在`set-cookie`和`authorization或body`中，尽管这会增加额外的传输量。

当客户端拿到令牌后，它要做的只有一件事：存储它。

你可以存储到任何位置，比如手机文件、PC文件、localstorage、cookie

当后续请求发生时，你只需要将它作为请求的一部分发送到服务器即可。

虽然jwt没有明确要求应该如何附带到请求中，但通常我们会使用如下的格式：

```
GET /api/resources HTTP/1.1
...
authorization: bearer jwt令牌
...
```

> 这种格式是OAuth2附带token的一种规范格式
>
> 至于什么是OAuth2，那是另一个话题了

这样一来，服务器就能够收到这个令牌了，通过对令牌的验证，即可知道该令牌是否有效。

它们的完整交互流程是非常简单清晰的

![image-20200422172837190](http://mdrs.yuanjin.tech/img/image-20200422172837190.png)

#### 令牌的组成

为了保证令牌的安全性，jwt令牌由三个部分组成，分别是：

1. header：令牌头部，记录了整个令牌的类型和签名算法
2. payload：令牌负荷，记录了保存的主体信息，比如你要保存的用户信息就可以放到这里
3. signature：令牌签名，按照头部固定的签名算法对整个令牌进行签名，该签名的作用是：保证令牌不被伪造和篡改

它们组合而成的完整格式是：`header.payload.signature`

比如，一个完整的jwt令牌如下：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9.BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc
```

它各个部分的值分别是：

- `header：eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`
- `payload：eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`
- `signature: BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc`

下面分别对每个部分进行说明

##### header

它是令牌头部，记录了整个令牌的类型和签名算法

它的格式是一个`json`对象，如下：

```json
{
  "alg":"HS256",
  "typ":"JWT"
}
```

该对象记录了：

- alg：signature部分使用的签名算法，通常可以取两个值
  - HS256：一种对称加密算法，使用同一个秘钥对signature加密解密
  - RS256：一种非对称加密算法，使用私钥加密，公钥解密
- typ：整个令牌的类型，固定写`JWT`即可

设置好了`header`之后，就可以生成`header`部分了

具体的生成方式及其简单，就是把`header`部分使用`base64 url`编码即可

> `base64 url`不是一个加密算法，而是一种编码方式，它是在`base64`算法的基础上对`+`、`=`、`/`三个字符做出特殊处理的算法
>
> 而`base64`是使用64个可打印字符来表示一个二进制数据，具体的做法参考[百度百科](https://baike.baidu.com/item/base64/8545775?fr=aladdin)

浏览器提供了`btoa`函数，可以完成这个操作：

```js
window.btoa(JSON.stringify({
  "alg":"HS256",
  "typ":"JWT"
}))
// 得到字符串：eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

同样的，浏览器也提供了`atob`函数，可以对其进行解码：

```js
window.atob("eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9")
// 得到字符串：{"alg":"HS256","typ":"JWT"}
```

> nodejs中没有提供这两个函数，可以安装第三方库`atob`和`bota`搞定
>
> 或者，手动搞定

##### payload

这部分是jwt的主体信息，它仍然是一个JSON对象，它可以包含以下内容：

```json
{
  "ss"："发行者",
	"iat"："发布时间",
	"exp"："到期时间",
	"sub"："主题",
	"aud"："听众",
	"nbf"："在此之前不可用",
  "jti"："JWT ID"
}
```

以上属性可以全写，也可以一个都不写，它只是一个规范，就算写了，也需要你在将来验证这个jwt令牌时手动处理才能发挥作用

上述属性表达的含义分别是：

- ss：发行该jwt的是谁，可以写公司名字，也可以写服务名称
- iat：该jwt的发放时间，通常写当前时间的时间戳
- exp：该jwt的到期时间，通常写时间戳
- sub：该jwt是用于干嘛的
- aud：该jwt是发放给哪个终端的，可以是终端类型，也可以是用户名称，随意一点
- nbf：一个时间点，在该时间点到达之前，这个令牌是不可用的
- jti：jwt的唯一编号，设置此项的目的，主要是为了防止重放攻击（重放攻击是在某些场景下，用户使用之前的令牌发送到服务器，被服务器正确的识别，从而导致不可预期的行为发生）

可是到现在，看了半天，没有出现我想要写入的数据啊😂

当用户登陆成功之后，我可能需要把用户的一些信息写入到jwt令牌中，比如用户id、账号等等（密码就算了😳）

其实很简单，payload这一部分只是一个json对象而已，你可以向对象中加入任何想要加入的信息

比如，下面的json对象仍然是一个有效的payload

```json
{
  "foo":"bar",
  "iat":1587548215
}
```

`foo: bar`是我们自定义的信息，`iat: 1587548215`是jwt规范中的信息

最终，payload部分和header一样，需要通过`base64 url`编码得到：

```js
window.btoa(JSON.stringify({
  "foo":"bar",
  "iat":1587548215
}))
// 得到字符串：eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9
```

##### signature

这一部分是jwt的签名，正是它的存在，保证了整个jwt不被篡改

这部分的生成，是对前面两个部分的编码结果，按照头部指定的方式进行加密

比如：头部指定的加密方法是`HS256`，前面两部分的编码结果是`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`

则第三部分就是用对称加密算法`HS256`对字符串`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`进行加密，当然你得指定一个秘钥，比如`shhhhh`

```js
HS256(`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9`, "shhhhh")
// 得到：BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc
```

最终，将三部分组合在一起，就得到了完整的jwt

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmb28iOiJiYXIiLCJpYXQiOjE1ODc1NDgyMTV9.BCwUy3jnUQ_E6TqCayc7rCHkx-vxxdagUwPOWqwYCFc
```

由于签名使用的秘钥保存在服务器，这样一来，客户端就无法伪造出签名，因为它拿不到秘钥。

换句话说，之所以说无法伪造jwt，就是因为第三部分的存在。

而前面两部分并没有加密，只是一个编码结果而已，可以认为几乎是明文传输

> 这不会造成太大的问题，因为既然用户登陆成功了，它当然有权力查看自己的用户信息
>
> 甚至在某些网站，用户的基本信息可以被任何人查看
>
> 你要保证的，是不要把敏感的信息存放到jwt中，比如密码

jwt的`signature`可以保证令牌不被伪造，那如何保证令牌不被篡改呢？

比如，某个用户登陆成功了，获得了jwt，但他人为的篡改了`payload`，比如把自己的账户余额修改为原来的两倍，然后重新编码出`payload`发送到服务器，服务器如何得知这些信息被篡改过了呢？

这就要说到令牌的验证了

#### 令牌的验证

![image-20200422172837190](http://mdrs.yuanjin.tech/img/image-20200422172837190.png)

令牌在服务器组装完成后，会以任意的方式发送到客户端

客户端会把令牌保存起来，后续的请求会将令牌发送给服务器

而服务器需要验证令牌是否正确，如何验证呢？

首先，服务器要验证这个令牌是否被篡改过，验证方式非常简单，就是对`header+payload`用同样的秘钥和加密算法进行重新加密

然后把加密的结果和传入jwt的`signature`进行对比，如果完全相同，则表示前面两部分没有动过，就是自己颁发的，如果不同，肯定是被篡改过了。

```
传入的header.传入的payload.传入的signature
新的signature = header中的加密算法(传入的header.传入的payload, 秘钥)
验证：新的signature == 传入的signature
```

当令牌验证为没有被篡改后，服务器可以进行其他验证：比如是否过期、听众是否满足要求等等，这些就视情况而定了

注意：这些验证都需要服务器手动完成，没有哪个服务器会给你进行自动验证，当然，你可以借助第三方库来完成这些操作

#### 总结

最后，总结一下jwt的特点：

- jwt本质上是一种令牌格式。它和终端设备无关，同样和服务器无关，甚至与如何传输无关，它只是规范了令牌的格式而已
- jwt由三部分组成：header、payload、signature。主体信息在payload
- jwt难以被篡改和伪造。这是因为有第三部分的签名存在。

### 登录和认证-服务器开发
/routes/jwt.js
```js
const secrect = "yuanjin";
const cookieKey = "token";
const jwt = require("jsonwebtoken");

//颁发jwt
exports.publish = function (res, maxAge = 3600 * 24, info = {}) {
  const token = jwt.sign(info, secrect, {
    expiresIn: maxAge,
  });
  //添加到cookie
  res.cookie(cookieKey, token, {
    maxAge: maxAge * 1000,
    path: "/",
  });
  //添加其他传输
  res.header("authorization", token);
};

exports.verify = function (req) {
  let token;
  //尝试从cookie中获取
  token = req.cookies[cookieKey]; //cookie中没有
  if (!token) {
    //尝试中header中
    token = req.headers.authorization;
    if (!token) {
      //没有token
      return null;
    }
    // authorization: bearer token
    token = token.split(" ");
    token = token.length === 1 ? token[0] : token[1];
  }
  try {
    const result = jwt.verify(token, secrect);
    return result;
  } catch(err) {
    return null;
  }
};
```
/routes/api/admin.js
```js
const express = require('express');
const router = express.Router();
const {login,getAdminById} = require('../../services/adminService')
const {asyncHandler} = require('../getSendResult')
const jwt = require('../jwt')
router.post('/login',asyncHandler(async (req,res)=>{
        const result = await login(req.body.loginId, req.body.loginPwd)
        if(result){
            
            const id = result.id;
            
            jwt.publish(res,undefined,{id})
        }
        return result;
    })
)
router.get("/whoami", asyncHandler(async (req, res) => {
    return await getAdminById(req.userId)
  }))
module.exports = router;
```
### 登录和认证-客户端开发

### 场景-日志记录
/logger.js
```js
const log4js = require("log4js");
const path = require("path");

function getCommonAppender(pathSeg) {
  return {
    //定义一个sql日志出口
    type: "dateFile",
    filename: path.resolve(__dirname, "logs", pathSeg, "logging.log"),
    maxLogSize: 1024 * 1024, //配置文件的最大字节数
    keepFileExt: true,
    daysToKeep: 3,
    layout: {
      type: "pattern",
      pattern: "%c [%d{yyyy-MM-dd hh:mm:ss}] [%p]: %m%n",
    },
  };
}

log4js.configure({
  appenders: {
    sql: getCommonAppender("sql"),
    default: {
      type: "stdout",
    },
    api: getCommonAppender("api"),
  },
  categories: {
    sql: {
      appenders: ["sql"], //该分类使用出口sql的配置写入日志
      level: "all",
    },
    default: {
      appenders: ["default"],
      level: "all",
    },
    api: {
      appenders: ["api"],
      level: "all",
    },
  },
});

process.on("exit", () => {
  log4js.shutdown();
});

exports.sqlLogger = log4js.getLogger("sql");
exports.logger = log4js.getLogger();
exports.apiLogger = log4js.getLogger("api");
```
/routes/apiLoggerMid.js
```js
const { apiLogger } = require("../logger.js");
const log4js = require("log4js");

// module.exports = (req, res, next) => {
//   next();
//   apiLogger.debug(`${req.method} ${req.path} ${req.ip}`);
// };

module.exports = log4js.connectLogger(apiLogger, {
  level: "auto",
});
```
在/routes/init.js中使用这个中间件

### 场景-文件上传
/routes/api/upload.js
```js
const express = require("express");
const router = express.Router();
const multer = require("multer");
const path = require("path");
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, path.resolve(__dirname, "../../public/upload"));
  },
  filename: function (req, file, cb) {
    // 时间戳-6位随机字符.文件后缀
    const timeStamp = Date.now();
    const ramdomStr = Math.random().toString(36).slice(-6);
    const ext = path.extname(file.originalname);
    const filename = `${timeStamp}-${ramdomStr}${ext}`;
    cb(null, filename);
  },
});

const upload = multer({
  storage,
  limits: {
    // fileSize: 150 * 1024,
  },
  fileFilter(req, file, cb) {
    //验证文件后缀名
    const extname = path.extname(file.originalname);
    const whitelist = [".jpg", ".gif", "png"];
    if (whitelist.includes(extname)) {
      cb(null, true);
    } else {
      cb(new Error(`your ext name of ${extname} is not support`));
    }
  },
});

router.post("/", upload.single("img"), (req, res) => {
  const url = `/upload/${req.file.filename}`;
  
  res.send({
    code: 0,
    msg: "",
    data: url,
  });
});

module.exports = router;
```
/public/index.html
```html
 <form action="/api/upload" method="POST" enctype="multipart/form-data">
        <p>
          <input type="text" name="a" />
        </p>
        <p>
          <input type="file" name="img" />
        </p>
        <p>
          <button>提交</button>
        </p>
 </form>
```
### 场景-文件下载
/routes/api/download.js
```js
const express = require("express");
const path = require("path");
const router = express.Router();

router.get("/:filename", (req, res) => {
  const absPath = path.resolve(
    __dirname,
    "../../resources",
    req.params.filename
  );
  res.download(absPath, req.params.filename);
});

module.exports = router;
```
/public/index.html
```html
 <a resrole="thunder" href="/res/picture.jpg">迅雷下载</a>
    <a href="/res/picture.jpg">普通下载</a>
    <!-- 迅雷下载协议
    把完整的下载地址得到
    AA地址ZZ
    base64编码
    thunder://base64编码
    -->
    <script>
      const a = document.querySelector("a[resrole=thunder]");
      let thunderLink = `AA${a.href}ZZ`;
      thunderLink = btoa(thunderLink);
      thunderLink = "thunder://" + thunderLink;
      a.href = thunderLink;
    </script>
```
### 场景-图片水印
```js
const path = require("path");
const jimp = require("jimp");

// 给一张图片加水印
async function mark(
  waterFile,
  originFile,
  targetFile,
  proportion = 5,
  marginProportion = 0.01
) {
  const [water, origin] = await Promise.all([
    jimp.read(waterFile),
    jimp.read(originFile),
  ]);

  // 对水印图片进行缩放
  const curProportion = origin.bitmap.width / water.bitmap.width;
  water.scale(curProportion / proportion);

  // 计算位置
  const right = origin.bitmap.width * marginProportion;
  const bottom = origin.bitmap.height * marginProportion;
  const x = origin.bitmap.width - right - water.bitmap.width;
  const y = origin.bitmap.height - bottom - water.bitmap.height;

  // 写入水印
  origin.composite(water, x, y, {
    mode: jimp.BLEND_SOURCE_OVER,
    opacitySource: 0.3,
  });

  await origin.write(targetFile);
}

async function test() {
  const waterPath = path.resolve(__dirname, "./water.jpg");
  const originPath = path.resolve(__dirname, "./origin.jpg");
  const targetPath = path.resolve(__dirname, "./new.jpg");
  mark(waterPath, originPath, targetPath);
}

test();
```
### 场景-图片防盗链
/routes/imgProtectMid.js
```js
const url = require("url");
const path = require("path");
module.exports = (req, res, next) => {
  const host = req.headers.host; //获取本网站的主机名（包括端口号）
  let referer = req.headers.referer;

  // 只处理图片
  const extname = path.extname(req.path);
  if (![".jpg", ".jpeg", ".png", ".gif"].includes(extname)) {
    next();
    return;
  }
  if (referer) {
    referer = url.parse(referer).host;
  }
  if (referer && host !== referer) {
    req.url = "/img/logo.jpg"; // url rewrite
  }
  next();
};
```
### 重要场景-代理
![代理服务器](./assets/代理服务器.jpg)
/routes/proxyMid.js(手写)
```js
// /data/api/xxxxx   --->    http://yuanjin.tech:5100/api/xxxx
const http = require("http");
module.exports = (req, res, next) => {
  const context = "/data";
  if (!req.path.startsWith(context)) {
    //不需要代理
    next();
    return;
  }
  // 需要代理
  const path = req.path.substr(context.length);
  // 创建代理请求对象 request
  const request = http.request(
    {
      host: "yuanjin.tech",
      port: 5100,
      path: path,
      method: req.method,
      headers: req.headers,
    },
    (response) => {
      // 代理响应对象 response
      res.status(response.statusCode);
      for (const key in response.headers) {
        res.setHeader(key, response.headers[key]);
      }
      response.pipe(res);
    }
  );
  req.pipe(request); //把请求体写入到代理请求对象的请求体中
};

```
/routes/proxyMid.js(第三方库)
```js
const { createProxyMiddleware } = require("http-proxy-middleware");
const context = "/data";
module.exports = createProxyMiddleware(context, {
  target: "http://yuanjin.tech:5100",
  pathRewrite: function (path, req) {
    console.log(path.substr(context.length));
    return path.substr(context.length);
  },
});

```
### 扩展场景-模板引擎
![两种渲染方式](./assets/两种渲染方式.jpg)
```js
const ejs = require("ejs");

ejs
  .renderFile("./test.ejs", {
    number: Math.random(),
  })
  .then((result) => {
    console.log(result);
  });

const str = `
生成的数字是：<%= number %>
`;

const result = ejs.render(str, {
  number: Math.random(),
});

console.log(result);
```
test.ejs
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>
      数字： <%= number %>
    </h1>
  </body>
</html>
```
/routes/controller/student.js
```js
const express = require("express");
const router = express.Router();
const stuServ = require("../../services/studentService");

router.get("/", async (req, res) => {
  const page = req.query.page || 1;
  const limit = req.query.limit || 10;
  const sex = req.query.sex || -1;
  const name = req.query.name || "";
  // total   datas
  const result = await stuServ.getStudents(page, limit, sex, name);
  res.render("./students.ejs", {
    ...result,
    page,
    limit,
  });
});

module.exports = router;

```
/routes/views/students.ejs
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>学生列表页</h1>
    <p>
      学生总数： <%= total %> , 当前第<%= page %>页，每页<%= limit %>条数据
    </p>
    <ul>
      <% datas.forEach(stu => { %>
      <li>
        <%= stu.name %>
      </li>
      <% }) %>
    </ul>
    <a href="?page=<%=+page-1%>">上一页</a>
    <a href="?page=<%=+page+1%>">下一页</a>
  </body>
</html>

```
/routes/init.js
```js
// 模板引擎
app.set("views", path.resolve(__dirname, "./views"));
app.use("/student", require("./controller/student"));

```
### 场景-生成二维码
```js
const QRCode = require("qrcode");
QRCode.toDataURL("https://duyi.ke.qq.com/?tuin=a5d48d54", (err, url) => {
  if (err) {
    console.log(err);
  } else {
    console.log(url);
    //返回base64格式图片
  }
});
```
### 场景-生成验证码
/routes/captchaMid.js
```js
const express = require("express");
const router = express.Router();
var svgCaptcha = require("svg-captcha");

router.get("/captcha", (req, res) => {
  var captcha = svgCaptcha.create({
    size: 6,
    ignoreChars: "iIl10oO",
    noise: 6,
    color: true,
  });
  req.session.captcha = captcha.text.toLowerCase(); //把验证码中的文本存放到session中

  res.type("svg");
  res.status(200).send(captcha.data);
});

function validateCaptcha(req, res, next) {
  const reqCaptcha = req.body.captcha ? req.body.captcha.toLowerCase() : ""; //用户传递的验证码
  if (reqCaptcha !== req.session.captcha) {
    //验证码有问题
    res.send({
      code: 401,
      msg: "验证码有问题",
    });
  } else {
    next();
  }

  req.session.captcha = "";
}

function captchaHandler(req, res, next) {
  if (!req.session.records) {
    // 如果session中没有访问记录
    req.session.records = [];
  }
  const now = new Date().getTime();
  req.session.records.push(now); // 把这一次请求的访问时间记录下来

  // 如果在一小段时间中请求达到了一定的数量，就可能是机器
  const duration = 10000;
  const repeat = 3;

  req.session.records = req.session.records.filter(
    (time) => now - time <= duration
  );

  if (req.session.records.length >= repeat || "captcha" in req.body) {
    // 验证验证码
    validateCaptcha(req, res, next);
  } else {
    next();
  }
}

router.post("*", captchaHandler);
router.put("*", captchaHandler);

module.exports = router;

```
### 场景-客户端缓存
#### 缓存的基本原理

在一个C/S结构中，最基本的缓存分为两种：

- 客户端缓存
- 服务器缓存

**本文仅讨论客户端缓存**

所谓**客户端缓存**，顾名思义，是将某一次的响应结果保存在客户端（比如浏览器）中，而后续的请求仅需要从缓存中读取即可，极大的降低了服务器的处理压力。

客户端缓存的原理如下：

<img src="http://mdrs.yuanjin.tech/img/image-20200430202446870.png" alt="image-20200430202446870" style="zoom: 33%;" />

> 这只是一个简易的原理图，实际情况可能有差异

这里就设计到一个缓存策略的问题，这些问题包括：

- 哪些资源需要加入到缓存，哪些不需要？
- 缓存的时间是多久呢？
- 如果服务器的资源有改动，客户端如何更新缓存呢？
- 如果缓存过期了，可是服务器上的资源并没有发生变动，又该如何处理呢？
- .......

要回答这些问题，就必须要清楚`http`中关于缓存的协议

理解了http的缓存协议，自然就能回答上面的问题了。

#### 来自服务器的缓存指令

当客户端发出一个`get`请求到服务器，服务器可能有以下的内心活动：「你请求的这个资源，我很少会改动它，干脆你把它缓存起来吧，以后就不要来烦我了」

为了表达这个美好的愿望，服务器在**响应头**中加入了以下内容：

```
Cache-Control: max-age=3600
ETag: W/"121-171ca289ebf"
Date: Thu, 30 Apr 2020 12:39:56 GMT
Last-Modified: Thu, 30 Apr 2020 08:16:31 GMT
```

这个响应头表达了下面的信息：

- `Cache-Control: max-age=3600`，我希望你把这个资源缓存起来，缓存时间是3600秒（1小时）
- `ETag: W/"121-171ca289ebf"`，这个资源的编号是`W/"121-171ca289ebf"`
- `Date: Thu, 30 Apr 2020 12:39:56 GMT`，我给你响应这个资源的服务器时间是格林威治时间`2020-04-30 12:39:56`
- `Last-Modified: Thu, 30 Apr 2020 08:16:31 GMT`，这个资源的上一次修改时间是格林威治时间`2020-04-30 08:16:31`

这个美好的缓存愿望，就这样通过响应头传递给客户端了

如果客户端是其他应用程序，可能并不会理会服务器的愿望，也就是说，可能根本不会缓存任何东西。

但是凑巧客户端是一个浏览器，它和服务器一直以来都是相亲相爱的小伙伴，当它看到服务器的这个响应头表达的美好愿望后，立即忙起来：

- 浏览器把这次请求得到的响应体缓存到本地文件中
- 浏览器标记这次请求的请求方法和请求路径
- 浏览器标记这次缓存的时间是3600秒
- 浏览器记录服务器的响应时间是格林威治时间`2020-04-30 12:39:56`
- 浏览器记录服务器给予的资源编号`W/"121-171ca289ebf"`
- 浏览器记录资源的上一次修改时间是格林威治时间`2020-04-30 08:16:31`

这一次的记录非常重要，它为以后浏览器要不要去请求服务器提供了各种依据。

<img src="http://mdrs.yuanjin.tech/img/image-20200430210430455.png" alt="image-20200430210430455" style="zoom:33%;" />

#### 来自客户端的缓存指令

当客户端收拾好行李，准备再次请求`GET /index.js`时，它突然想起了一件事：我需要的东西在不在缓存里呢？

此时，客户端会到缓存中去寻找是否有缓存的资源

寻找的过程如下：

1. 缓存中是否有匹配的请求方法和路径？
2. 如果有，该缓存资源是否还有效呢？

以上两个验证会导致浏览器产生不同的行为

<img src="http://mdrs.yuanjin.tech/img/image-20200430212052228.png" alt="image-20200430212052228" style="zoom:50%;" />

<img src="http://mdrs.yuanjin.tech/img/image-20200430214301507.png" alt="image-20200430214301507" style="zoom:33%;" />

要验证是否有匹配的缓存非常简单，只需要验证当前的请求方法`GET`和当前的请求路径`/index.js`是否有对应的缓存存在即可

如果没有，就直接请求服务器，就和第一次请求服务器时一样，这种情况没有什么好讨论的

关键在于验证缓存是否有效

如何验证呢？

非常简单，就是把`max-age + Date`，得到一个过期时间，看看这个过期时间是否大于当前时间，如果是，则表示缓存还没有过期，仍然有效，如果不是，则表示缓存失效。

##### 缓存有效

当浏览器发现缓存有效时，完全不会请求服务器，直接使用缓存即可得到结果

此时，如果你断开网络，会发现资源仍然可用

这种情况会极大的降低服务器压力，但当服务器更改了资源后，浏览器是不知道的，只要缓存有效，它就会直接使用缓存

##### 缓存无效

当浏览器发现缓存已经过期，它**并不会简单的把缓存删除**，而是抱着一丝希望，想问问服务器，我**这个缓存还能继续使用吗**？

于是，浏览器向服务器发出了一个**带缓存的请求**

所谓带缓存的请求，无非就是加入了以下的请求头：

```
If-Modified-Since: Thu, 30 Apr 2020 08:16:31 GMT
If-None-Match: W/"121-171ca289ebf"
```

它们表达了下面的信息：

- `If-Modified-Since: Thu, 30 Apr 2020 08:16:31 GMT`，亲，你曾经告诉我，这个资源的上一次修改时间是格林威治时间`2020-04-30 08:16:31`，请问这个资源在这个时间之后有发生变动吗？
- `If-None-Match: W/"121-171ca289ebf"`，亲，你曾经告诉我，这个资源的编号是`W/"121-171ca289ebf`，请问这个资源的编号发生变动了吗？

其实，这两个问题可以合并为一个问题：快说！资源到底变了没有！

之所以要发两个信息，是为了兼容不同的服务器，因为有些服务器只认`If-Modified-Since`，有些服务器只认`If-None-Match`，有些服务器两个都认

> 目前的很多服务器，只要发现`If-None-Match`存在，就不会去看``If-Modified-Since`
>
> `If-Modified-Since`是`http1.0`版本的规范，`If-None-Match`是`http1.1`的规范

此时，问题又抛给了服务器，接下来，就是服务器的表演时间了

服务器可能会产生两个情况：

- 缓存已经失效
- 缓存仍然有效

如果是第一种情况——**缓存已经失效**，那么非常简单，服务器再次给予一个正常的响应（响应码`200` 带响应体），同时可以附带上新的缓存指令，这就回到了上一节——来自服务器的缓存指令

这样一来，客户端就会重新缓存新的内容

但如果服务器觉得**缓存仍然有效**，它可以通过一种极其简单的方式告诉客户端：

- 响应码为`304 Not Modified`
- 无响应体
- 响应头带上新的缓存指令，见上一节——来自服务器的缓存指令

这样一来，就相当于告诉客户端：「你的缓存资源仍然可用，我给你一个新的缓存时间，你那边更新一下就可以了」

于是，客户端就继续happy的使用缓存了

这样一来，可以最大程度的减少网络传输，因为如果资源还有效，服务器就不会传输消息体

它们完整的交互过程如下：

<img src="http://mdrs.yuanjin.tech/img/image-20200430225326001.png" alt="image-20200430225326001" style="zoom:33%;" />

#### 细节

上面描述了客户端缓存的基本概念和过程

但其中仍然有不少细节值得我们注意

##### Cache-Control

在上述的讲解中，`Cache-Control`是服务器向客户端响应的一个消息头，它提供了一个`max-age`用于指定缓存时间。

实际上，`Cache-Control`还可以设置下面一个或多个值：

- `public`：指示服务器资源是公开的。比如有一个页面资源，所有人看到的都是一样的。这个值对于浏览器而言没有什么意义，但可能在某些场景可能有用。本着「我告知，你随意」的原则，`http`协议中很多时候都是客户端或服务器告诉另一端详细的信息，至于另一端用不用，完全看它自己。
- `private`：指示服务器资源是私有的。比如有一个页面资源，每个用户看到的都不一样。这个值对于浏览器而言没有什么意义，但可能在某些场景可能有用。本着「我告知，你随意」的原则，`http`协议中很多时候都是客户端或服务器告诉另一端详细的信息，至于另一端用不用，完全看它自己。
- `no-cache`：告知客户端，你可以缓存这个资源，但是不要**直接**使用它。当你缓存之后，后续的每一次请求都需要附带缓存指令，让服务器告诉你这个资源有没有过期。见：「来自客户端的缓存指令 -  缓存无效」
- `no-store`：告知客户端，不要对这个资源做任何的缓存，之后的每一次请求都按照正常的普通请求进行。若设置了这个值，浏览器将不会对该资源做出任何的缓存处理。
- `max-age`：不再赘述

比如，`Cache-Control: public, max-age=3600`表示这是一个公开资源，请缓存1个小时。

##### Expire

在`http1.0`版本中，是通过`Expire`响应头来指定过期时间点的，例如：

```
Expire: Thu, 30 Apr 2020 23:38:38 GMT
```

到了`http1.1`版本，已更改为通过`Cache-Control`的`max-age`来记录了。

##### 记录缓存时的有效期

浏览器会按照服务器响应头的要求，自动记录缓存到本地文件，并设置各种相关信息

在这些信息中，**有效期**尤为关键，它决定了这个缓存可以使用多久

浏览器会根据服务器不同的响应情况，设置不同的有效期

具体的有效期设置，按照下面的流程进行：

<img src="http://mdrs.yuanjin.tech/img/image-20200501075337464.png" alt="image-20200501075337464" style="zoom:33%;" />

例如，当`max-age`设置为0时，缓存立即过期

虽然立即过期，但缓存仍然被记录下来，后续的请求通过缓存指令发送到服务器，来确认资源是否被更改。

因此，`Cache-Control: max-age=0`类似于`Cache-Control: no-cache`

##### Pragma

这是`http1.0`版本的消息头

当该消息头出现在请求中时，是向服务器表达：不要考虑任何缓存，给我一个正常的结果。

在`http1.1`版本中，可以在**请求头**中加入`Cache-Control: no-cache`实现同样的含义。

> 是的，`Cache-Control`可以出现在请求头中

在`Chrome`浏览器中调试时，如果勾选了`Disable cache`，则发送的请求中会附带该信息

![image-20200501080330131](http://mdrs.yuanjin.tech/img/image-20200501080330131.png)

##### Vary

有的时候，是否有缓存，不仅仅是判断请求方法和请求路径是否匹配，可能还要判断头部信息是否匹配。

此时，就可以使用`Vary`字段来指定要区分的消息头

比如，当使用`GET /personal.html`请求服务器时，请求头中`cookie`的值不一样，得到的页面也不一样

如果还按照之前的做法，仅仅匹配请求方法和请求路径，如果`cookie`变动，你可能得到的仍然是之前的页面。

正确的做法如下：

<img src="http://mdrs.yuanjin.tech/img/image-20200501082103089.png" alt="image-20200501082103089" style="zoom:33%;" />

##### 使用版本号或hash

如果你是一个前端工程师，使用过`vue`或其他基于`webpack`搭建的工程

你会发现打包的结果中很多文件名类似于这样：

```
app.68297cd8.css
```

文件的中间部分使用了`hash`值

这样做的好处是，可以让客户端大胆的、长时间的缓存该文件，减轻服务器的压力

当文件改动后，它的文件`hash`值也会随之而变，比如变成了`app.446fccb8.css`

这样一来，客户端要请求新的文件时，就会发现路径从`/app.68297cd8.css`变成了`app.446fccb8.css`，由于之前的缓存路径无法匹配到，因此就会发送新的请求来获取新资源了。

以上是现代流行的做法。

而在古老的年代，还没有构建工具出现时，人们使用的办法是在资源路径后面加入版本号来获取新版本的文件

比如，页面中引入了一个css资源`app.css`，它可能的引入方式是：

```html
<link href="/app.css?v=1.0.0">
```

这样一来，缓存的路径是`/app.css?v=1.0.0`

当服务器的版本发生变化时，可以给予新的版本号，让html中的路径发生变动

```html
<link href="/app.css?v=1.0.1">
```

由于新的路径无法命中缓存，于是浏览器就会发送新的普通请求来获取这个资源

#### 总结

最后，通过客户端和服务器两位大佬的视角，来总结一下以上内容

##### 服务器视角

服务器无法知道客户端到底有没有像浏览器那样缓存文件，它只管根据请求的情况来决定如何响应

![image-20200501083702987](http://mdrs.yuanjin.tech/img/image-20200501083702987.png)

很多后端语言搭建的服务器都会自带自己的默认缓存规则，当然也支持不同程度的修改

##### 浏览器视角

浏览器在发出请求时会判断要不要使用缓存

<img src="http://mdrs.yuanjin.tech/img/image-20200501084258712.png" alt="image-20200501084258712" style="zoom:50%;" />

当收到服务器响应时，会自动根据缓存指令进行处理

<img src="http://mdrs.yuanjin.tech/img/image-20200501084559394.png" alt="image-20200501084559394" style="zoom:50%;" />

### 场景-富文本框
```html
<div id="div1"></div>
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/wangeditor@latest/dist/wangEditor.min.js"></script>
    <script type="text/javascript">
        const E = window.wangEditor
        const editor = new E("#div1")
        // 或者 const editor = new E(document.getElementById('div1'))
        editor.create()
```
### websocket原理
//长连接，比如股票，没必要每次请求都建立新的连接
在websocket的http握手阶段，服务器响应头中需要包含如下内容：

```
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: [key]
```

其中，`Sec-WebSocket-Accept`的值来自于以下算法：

```js
base64(sha1(Sec-WebSocket-Key) + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11") 
```
在`node`中可以使用以下代码获得：
```js
const crypto = require("crypto");
const hash = crypto.createHash("sha1");
hash.update(requestKey + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11");
const key = hash.digest("base64");
```
其中，`requestKey`来自于请求头中的`Sec-WebSocket-Key`

服务端
```js
const net = require("net");

const server = net.createServer((socket) => {
  console.log("收到客户端的连接");
  socket.once("data", (chunk) => {
    const httpContent = chunk.toString("utf-8");
    let parts = httpContent.split("\r\n");
    parts.shift();
    parts = parts
      .filter((s) => s)
      .map((s) => {
        const i = s.indexOf(":");
        return [s.substr(0, i), s.substr(i + 1).trim()];
      });
    const headers = Object.fromEntries(parts);
    const crypto = require("crypto");
    const hash = crypto.createHash("sha1");
    hash.update(
      headers["Sec-WebSocket-Key"] + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"
    );
    const key = hash.digest("base64");
    // 响应
    socket.write(`HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: ${key}

`);
    socket.on("data", (chunk) => {
      console.log(chunk);
    });
  });
});

server.listen(5008);
```
客户端
```html
<button>发送数据到服务器</button>
    <script>
      // 客户端（浏览器）websocket
      const ws = new WebSocket("ws://localhost:5008"); // 创建一个websocket，同时，发送连接到服务器
      ws.onopen = function () {
        // http握手完成
        console.log("连接已建立");
      };

      ws.onmessage = function (e) {
        console.log("来自服务器的数据", e.data);
      };

      ws.onclose = function () {
        console.log("通道关闭");
      };

      document.querySelector("button").onclick = function () {
        ws.send("123");
      };
      //   ws.close(); //客户端主动断开连接
    </script>
```
### socket.io
```html
<button>发送数据到服务器</button>
    <script src="https://cdn.bootcdn.net/ajax/libs/socket.io/2.3.0/socket.io.js"></script>
    <script>
      const socket = io.connect();
      document.querySelector("button").onclick = function () {
        socket.emit("msg", "msg from client");
      };

      socket.on("test", (chunk) => {
        console.log(chunk);
      });

      socket.on("disconnect", () => {
        console.log("closed");
      });
</script>
```
### CSRF攻击和防御
#### CSRF 特点和原理

CSRF：Cross Site Request Forgery，跨站请求伪造

本质是：恶意网站把**正常用户**作为**媒介**，通过模拟正常用户的操作，攻击其**登录过**的站点。

<img src="http://mdrs.yuanjin.tech/img/image-20200508122744169.png" alt="image-20200508122744169" style="zoom:50%;" />

它的原理如下：

1. 用户访问正常站点，登录后，获取到了正常站点的令牌，以cookie的形式保存

<img src="http://mdrs.yuanjin.tech/img/image-20200508123116104.png" alt="image-20200508123116104" style="zoom:50%;" />

2. 用户访问恶意站点，恶意站点通过某种形式去请求了正常站点（请求伪造），迫使正常用户把令牌传递到正常站点，完成攻击

<img src="http://mdrs.yuanjin.tech/img/image-20200508123401591.png" alt="image-20200508123401591" style="zoom:50%;" />

#### 防御

##### cookie的SameSite

现在很多浏览器都支持**禁止跨域附带的cookie**，只需要把cookie设置的`SameSite`设置为`Strict`即可

`SameSite`有以下取值：

- Strict：严格，所有跨站请求都不附带cookie，有时会导致用户体验不好
- Lax：宽松，所有跨站的超链接、GET请求的表单、预加载连接时会发送cookie，其他情况不发送
- None：无限制

这种方法非常简单，极其有效，但前提条件是：用户不能使用太旧的浏览器

##### 验证referer和Origin

页面中的二次请求都会附带referer或Origin请求头，向服务器表示该请求来自于哪个源或页面，服务器可以通过这个头进行验证

但某些浏览器的referer是可以被用户禁止的，尽管这种情况极少

##### 使用非cookie令牌

这种做法是要求每次请求需要在请求体或请求头中附带token



请求的时候：authorization: token



##### 验证码

这种做法是要求每个要防止CSRF的请求都必须要附带验证码

不好的地方是容易把正常的用户逼疯

##### 表单随机数

这种做法是服务端渲染时，生成一个随机数，客户端提交时要提交这个随机数，然后服务器端进行对比

该随机数是一次性的



流程：

1. 客户端请求服务器，请求添加学生的页面，传递cookie
2. 服务器
   1. 生成一个随机数，放到session中
   2. 生成页面时，表单中加入一个隐藏的表单域`<input type="hidden" name="hash" value="<%=session['key'] %>">`
3. 填写好信息后，提交表单，会自动提交隐藏的随机数
4. 服务器
   1. 先拿到cookie，判断是否登录过
   2. 对比提交过来的随机数和之前的随机数是否一致
   3. 清除掉session中的随机数



##### 二次验证

当做出敏感操作时，进行二次验证

### XSS攻击和防御

XSS：Cross Site Scripting 跨站脚本攻击



#### 存储型XSS

1. 恶意用户提交了恶意内容到服务器
2. 服务器没有识别，保存了恶意内容到数据库



1. 正常用户访问服务器
2. 服务器在不知情的情况下，给予了之前的恶意内容，让正常用户遭到攻击



#### 反射型

1. 恶意用户分享了一个正常网站的链接，链接中带有恶意内容
2. 正常用户点击了该链接
3. 服务器在不知情的情况，把链接的恶意内容读取了出来，放进了页面中，让正常用户遭到攻击



#### DOM型

1. 恶意用户通过任何方式，向服务器中注入了一些dom元素，从而影响了服务器的dom结构
2. 普通用户访问时，运行的是服务器的正常js代码

### nodejs的组成原理
![](http://mdrs.yuanjin.tech/img/node组成原理图.jpg)

1. 用户代码

JS代码，开发者编写的

2. 第三方库

大部分仍然是JS代码，由其他开发者编写

3. 本地模块代码

JS代码

4. V8引擎

c/c++代码，作用：把JS代码解释成为机器码

可以通过v8引擎的某种机制，扩展其功能
V8引擎的扩展和对扩展的编译，是通过一个工具：gyp工具

某些第三方库需要使用`node-gyp`工具进行构建，因此需要先安装`node-gyp`

### 进程和线程
#### 进程

一个应用程序，总是通过操作系统启动的，当操作系统启动一个应用程序时，会给其分配一个进程

一个进程拥有**独立的、可伸缩的**内存空间，原则上不受其他进程干扰

进程之间是可以通信的，只要两个进程双方遵守一定的协议，比如ipc

**CPU在不同的进程之间切换执行**



虽然一个应用程序在启动时只有一个进程，但它在运行的过程中，可以开启新的进程，进程之间仍然保持相对独立

如果一个进程是直接由操作系统开启，则它叫做主进程

如果一个进程B是由进程A开启，则A是B的父进程，B是A的子进程，子进程会继承父进程的一些信息，但仍然保持相对独立



```js
// nodejs 中开启子进程
const childProcess = require("child_process"); // 导入内置模块

childProcess.exec(在子进程运行的命令, (err, out, stdErr) => {
  // 回调函数中可以获取子进程的标准输出，这种数据交互是通过IPC完成的，nodejs已经帮你完成了处理
  // err：开启进程过程中发生的错误
  // out: 子进程输出的正常内容
  // stdErr: 子进程输出的错误内容
  // 子进程发生任何的错误，绝不会影响到父进程，它们的内存空间是完全隔离的
});

// 过去，nodejs没有提供给用户创建线程的接口，只能使用进程的方式
// 过去，nodejs还提供了cluster模块，通过另一种模式来创建进程
// 现在，nodejs已经提供了线程模块，对进程的操作已经很少使用了
```

#### 线程

操作系统启动一个进程（无论是主进程，还是子进程），都会自动为它分配一个线程，称之为主线程

**程序一定在线程上运行！！**

主线程在运行的过程中，可以创建多个线程，这些线程称之为子线程

当操作系统命令CPU去执行一个进程时，实际上，是在该进程的多个线程中切换执行

线程和进程很相似，它们都是独立运行，最大的区别在于：**线程的内存空间没有隔离**，共享进程的内存空间，线程之间的数据不用遵守任何协议，可以随意使用



什么时候要使用线程？

使用线程的主要目的，是为了充分使用多核cpu。线程执行过程中，尽量的不要阻塞。

最理想的线程效果：

1. 线程数等于cpu的核数
2. 线程永不阻塞
   1. 没有io
   2. 只存在大量运算
3. 线程相对独立，几乎不使用共享数据

线程一般处理cpu密集型操作（运算操作），而io密集型操作不适合使用线程，而适合使用异步



为了避免线程执行过程中共享数据产生的麻烦，nodejs使用独特的线程机制来尽力规避：

```js
// 创建子线程的父线程
const { Worker } = require("worker_threads");
const worker = new Worker(线程执行的入口文件, {
  workerData: 开启线程时向其传递的数据,
}); // worker是子线程实例

// 通过EventEmitter监听子线程的事件
worker.on("exit", () => {
  // 当子线程退出时运行的事件
});
worker.on("message", (msg) => {
  // 收到子线程发送的消息时运行的事件
});
worker.postMessage(任意消息); // 父线程向子线程发送任意消息
worker.terminate(); // 退出子线程
```



```js
const {
  isMainThread, // 是否是主线程
  parentPort, // 用于与父线程通信的端口
  workerData, // 获取线程启动时传递的数据
  threadId, // 获取线程的唯一编号
} = require("worker_threads");

parentPort.on("message", (msg) => {
  // 当收到父线程发送的消息时，触发的事件
});
parentPort.postMessage(workerData); // 向父线程发送消息
```

