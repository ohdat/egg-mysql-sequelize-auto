# egg-sequelize-auto
用命令行自动生成Egg.js框架SequelizeJS的model类 (只支持typescript)


## 安装
```
npm install @ohdat/egg-sequelize-auto --save-dev
```

## 命令行说明
```
egg-sequelize-auto -h <host> -d <database> -p [port] -u <user> -x [password] -o [./path/to/model] -t [tableName]

Options:
  -h, --host        IP/Hostname for the database.                       [string]
  -d, --database    Database name.                               [string] [required]
  -u, --user        Username for database.                       [string] [required]
  -x, --pass        Password for database.                       [string] [required]
  -p, --port        Port number for database. Ex: 3306                    [number]
  -o, --output      What directory to place the models.                 [string]
  -t, --tables      table name to import                         [string] [required]
  -f, --over-write  Force to overwrite existed model files                [boolean]
```

## 设置
### package.json [选项 1]

```json
"scripts": {
  "model": "egg-sequelize-auto -h 127.0.0.1 -d database_name -p 3306 -u user -x password -o './app/model' -t table_name"
}
```
#### 命令行
```
npm run model
```

---
### package.json [选项 2]
```json
"scripts": {
  "model": "egg-sequelize-auto -h 127.0.0.1 -d database_name -p 3306 -u user -x password -o './app/model'"
}
```
#### 命令行
```
npm run model -- --t=table_name
```

## 例子
### 生成[test_table]的sql脚本
```sql
CREATE TABLE `test_table` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `key` VARCHAR(30) NOT NULL COMMENT 'Article key',
  `title` VARCHAR(100) NOT NULL COMMENT 'Article title',
  `content` TEXT NOT NULL COMMENT 'Article content',
  `created_time` DATETIME NOT NULL COMMENT 'Created time',
  `updated_time` DATETIME NOT NULL COMMENT 'Updated time',
  `removed` TINYINT(1) NOT NULL DEFAULT 0 COMMENT 'Flag of soft deletion',
  PRIMARY KEY (`id`));
```

### 生成的Model文件
/model/TestTable.ts
```ts
import { Application } from 'egg';
import { TestTable } from './define/TestTable';

export default function(app: Application) {
  const { DataTypes } = app.Sequelize;

  const ExportModel = app.model.define<TestTable, any>('TestTable', 
    {
	id: {
		autoIncrement: true,
		primaryKey: true,
		allowNull: false,
		type: DataTypes.INTEGER,
		comment: ""
	},
	key: {
		allowNull: false,
		type: DataTypes.STRING(30),
		comment: "Article key"
	},
	title: {
		allowNull: false,
		type: DataTypes.STRING(100),
		comment: "Article title"
	},
	content: {
		allowNull: false,
		type: DataTypes.TEXT,
		comment: "Article content"
	},
	created_time: {
		allowNull: false,
		type: DataTypes.DATE,
		comment: "Created time"
	},
	updated_time: {
		allowNull: false,
		type: DataTypes.DATE,
		comment: "Updated time"
	},
	removed: {
		allowNull: false,
		type: DataTypes.TINYINT,
		defaultValue: 0,
		comment: "Flag of soft deletion"
	}
}
  , {
    timestamps: false, // 去除createAt updateAt
    freezeTableName: true, // 使用自定义表名
    tableName: 'test_table',
  });

  return class extends ExportModel<TestTable, any> {

  };

}
```
/model/define/TestTable.d.ts
```ts
import { Model } from 'sequelize';

interface TestTableAttributes
  {
	id?: number;
	key?: string;
	title?: string;
	content?: string;
	created_time?: Date;
	updated_time?: Date;
	removed?: number
}
  
class TestTable extends Model<TestTableAttributes, any> implements TestTableAttributes 
  {
	id?: number;
	key?: string;
	title?: string;
	content?: string;
	created_time?: Date;
	updated_time?: Date;
	removed?: number
}
  
export { TestTableAttributes, TestTable };
```
---

#### 私有库配置  
添加 .npmrc 文件内写入
```
	@ohdat:registry=https://npm.pkg.github.com/
```
使用npm login命令进行授权，注意用户名要全部小写，Token的输入是以密码的方式输入，不会显示。
```
npm login --registry=https://npm.pkg.github.com
> Username: USERNAME
> Password: TOKEN
> Email: PUBLIC-EMAIL-ADDRESS`
```
或者 在actions 中使用
```
npm config set @ohdat:registry https://npm.pkg.github.com/:_authToken=TOKEN
```