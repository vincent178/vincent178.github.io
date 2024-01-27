---
layout: post
title: "记一个 GORM 奇怪报错"
date: 2021-11-08T23:36:17+08:00
draft: false
---

<!--more-->

业务伪代码是
```go
s := findStruct()
tx := db.Save(&s)
if tx.Error != nil {
    log.Fatal(tx.Error)
}
```
打印出来的错误是`WHERE conditions required`。

产生 bug 的原因是 `findStruct` 返回的是指向 struct 的指针，调用保存的参数 `&s` 变成了双指针。

`save` 的代码在 `gorm@v1.22.2/finisher_api.go`:
```go
// Save update value in database, if the value doesn't have primary key, will insert it
func (db *DB) Save(value interface{}) (tx *DB) {
	tx = db.getInstance()
	tx.Statement.Dest = value

	reflectValue := reflect.Indirect(reflect.ValueOf(value))
	switch reflectValue.Kind() {
	case reflect.Slice, reflect.Array:
        // ...
	case reflect.Struct:
        // ...
	default:
        // ...
		tx = tx.callbacks.Update().Execute(tx)
        // ...
	}

	return
}
```
因为是双指针，`reflectValue.Kind()` 就不是 `reflect.Struct`，就会执行 `default` 代码段中的 `update` 逻辑。

`Update` 的代码在 `gorm@v1.22.2/callbacks/update.go`:
```go
func Update(config *Config) func(db *gorm.DB) {
	supportReturning := utils.Contains(config.UpdateClauses, "RETURNING")

	return func(db *gorm.DB) {
        // ...
		if db.Statement.SQL.String() == "" {
			db.Statement.SQL.Grow(180)
			db.Statement.AddClauseIfNotExists(clause.Update{})
			if set := ConvertToAssignments(db.Statement); len(set) != 0 {
				db.Statement.AddClause(set)
			} else {
				return
			}
			db.Statement.Build(db.Statement.BuildClauses...)
		}

		if _, ok := db.Statement.Clauses["WHERE"]; !db.AllowGlobalUpdate && !ok {
			db.AddError(gorm.ErrMissingWhereClause) // 报错消息
			return
		}

        // ...
	}
}
```
可以看到因为 Statement 中没有声明 Where 条件，所以就抛了 `gorm.ErrMissingWhereClause` 错误。

至此就找到了产生这个奇怪的错误消息的地方。
