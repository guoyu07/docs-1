# 查询构建器

查询构建器就是平时开发框架中的 `from()->select()` 等方法，其具体作用是自动构建 SQL 查询语句，方便开发，提高开发效率，但是构建器不支持复杂查询，具体复杂查询建议使用自己手写 SQL 语句进行查询，因为查询构建器无法保证高复杂度的查询效率。

**注意: 查询构建器是一个独立，不依赖于数据库连接驱动的独立对象，因此生成出来的语句，需要传递到连接驱动中才可以真正发挥作用。**

```php
FastD\Database\Query\Mysql::singleton();
```

获取 `Mysql` 查询构建器，其他方法 `createQueryBuilder` 中，原理也是通过 `Mysql::singleton` 获取查询构建器，因此通过 `Mysql::singleton` 即可获取最原始的构建器。

### ＃获取查询构建器

```php
/**
 * @Route("/builder", name="database.builder")
 *
 * @return Response|string
 */
public function queryBuilderAction()
{
    $queryBuilder = $this->getDriver('read')->createQueryBuilder();

    return $this->render('database/drivers.twig', [
        'sql' => $sql,
    ]);
}
```

查询构建器通过 `select`，`insert`，`update` 来获取生成语句。此处没有添加 `delete` 操作，因为再业务开发中，不应该使用物理删除操作，所以框架也不提供删除语句构建。

##### ＃from($table, $alias = null)

```php
$queryBuilder->from('test'); // FROM `test`
$queryBuilder->from('test', 't'); // FROM `test` AS `t`
```

`from` 方法接受两个参数，第二个是别名(`AS`)。

##### ＃select

```php
$queryBuilder->from('test')->select(); // select * from `test`;
```

##### ＃insert(array $data)

```php
$queryBuilder->from('test')->insert(['name' => 'janhuang']);
// INSERT INTO `test` (`name`) values ('janhuang');
```

##### ＃update(array $data, array $where = [])

```php
$queryBuilder->from('test')->update(['name' => 'janhuang']);
// UPDATE `test` SET `name` = 'janhuang';
```

如果需要添加更新条件

```php
$queryBuilder->from('test')->update(['name' => 'janhuang'], ['id' => 1]);
// UPDATE `test` SET `name` = 'janhuang' WHERE `id` = 1;
```

##### ＃fields(array $fields)

```php
$queryBuilder->fields(['id', 'name'])->from('test')->select();
```

如果需要设置别名的话，则需要配置数组 `value` 值，该值就是字段名的别名.

##### ＃where(array $where)

普通条件查询

```php
$queryBuilder->where(['id' => 1])->from('test')->select();
// SELECT * FROM `test` WHERE `id` = 1;
```

条件查询

```php
$queryBuilder->where(['[>]id' => 1])->from('test')->select();
// SELECT * FROM `base` WHERE `id`>'1';
```

**AND/OR**

```php
$queryBuilder->where([
    'AND' => [
        'id' => 1,
        'name' => 'jan'
    ]
])->from('test')->select();
// SELECT * FROM `test` WHERE `id` = 1 AND `name` = 'jan';
```

##### ＃limit($limit, $offset = null)

```php
$queryBuilder->from('test')->limit(5)->select(); 
// SELECT * FROM `test` LIMIT 5;
$queryBuilder->from('test')->limit(5,5)->select(); 
// SELECT * FROM `test` LIMIT 5, 5;
```

##### ＃groupBy(array $groupBy)

查询分组

```php
$queryBuilder->from('test')->groupBy(['name'])->select();
// SELECT * FROM `base` GROUP BY `name`;
```

##### ＃orderBy(array $orderBy)

查询排序

```php
$queryBuilder->from('test')->orderBy([
    'name' => 'DESC',
    'age' => 'DESC'
])->select();
// SELECT * FROM `base` ORDER BY `name` DESC,`age` DESC;
```

##### ＃like(array $like)

模糊匹配查询

```php
$queryBuilder->from('test')->like([
    'name' => '%jan%'
])->select();
// SELECT * FROM `base` WHERE `name` LIKE '%jan%';
```

##### ＃notLike(array $like)

模糊匹配查询

```php
$queryBuilder->from('test')->notLike([
    'name' => '%jan%'
])->select();
// SELECT * FROM `base` WHERE `name` NOT LIKE '%jan%';
```

##### ＃having(array $having)

```php
$queryBuilder->from('test')->having(['[>]age' => 10])->select();
// SELECT * FROM `base` HAVING `age`>'10';
```

##### ＃join($table, $on, $type = 'LEFT')

```php

```