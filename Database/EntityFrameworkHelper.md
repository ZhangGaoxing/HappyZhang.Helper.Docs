<link href="https://cdn.bootcss.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet">


# EntityFrameworkHelper 类

<small>命名空间：[HappyZhang.Helper.Database](./)</small>

<small>NuGet 包引用：`Microsoft.EntityFrameworkCore`，`EFCore.BulkExtensions`，`Microsoft.AspNetCore.Http`</small>

Entity Framework Core 帮助类，提供使用 EF 框架操作数据库的一组方法。

```C#
public class EntityFrameworkHelper<TEntity> where TEntity : class
```

`TEntity`：要操作的实体。

<div style="display: block;position: relative;border-radius: 8px;padding: 1rem;background-color: #fff1cc;color: #664b00;margin: 10px">
    <p style="margin-top:0;font-weight: bold"><i class="fa fa-exclamation-triangle" aria-hidden="true"></i>&nbsp;&nbsp;注意</p>
    <p><span>使用此帮助类进行操作时，数据库中的表必须包含如下字段：</span></p>
    <div>
        <p>public string CreatorID { get; set; } // 创建人 ID</p>
        <p>public DateTime? CreateTime { get; set; } // 创建时间</p>
        <p>public string ModifierID { get; set; } // 修改人 ID</p>
        <p>public DateTime? ModifyTime { get; set; } // 修改时间</p>
        <p>public bool IsDeleted { get; set; } // 删除标记</p>
    </div>
</div>

## 构造函数

### EntityFrameworkHelper(DbContext, string)

使用数据库上下文和操作者 ID 实例化一个 Entity Framework Core 帮助类。

```C#
public EntityFrameworkHelper(DbContext context, string manipulatorID = null)
```

#### 参数

* **`context`** `DbContext`：数据库上下文。
* **`manipulatorID`** `string`：操作者 ID，可为空。

#### 用法

在控制器中使用依赖注入获取`DbContext`，或者直接实例化一个`DbContext`。

```C#
private readonly TestContext _context;
private readonly EntityFrameworkHelper<Test> _helper;

public TestController(TestContext context)
{
    _context = context;
    _helper = new EntityFrameworkHelper<Test>(context, SessionHelper.LoginerID);
}
```

## 方法
### Delete(object)

根据主键值软删除。

```C#
public bool Delete(object id);
```

#### 参数

* **`id`** `object`：主键值。

#### 返回

`bool`：是否软删除成功。

#### 用法

```C#
bool ret = _helper.Delete(id: 10);
```

### DeleteH(object)

根据主键值硬删除。

```C#
public bool DeleteH(object id);
```

#### 参数

* **`id`** `object`：主键值。

#### 返回

`bool`：是否硬删除成功。

### DeleteRange(IEnumerable<object>)

批量软删除。

```C#
public bool DeleteRange(IEnumerable<object> ids);
```

#### 参数

* **`ids`** `IEnumerable<object>`：主键值数组。

#### 返回

`bool`：是否软删除成功。

#### 用法

```C#
List<Test> tests = new List<Test>();

IEnumerable<object> ids = tests.Select(x => x.TestID).Cast<object>();
bool ret = _helper.DeleteRange(ids);
```

### DeleteHRange(IEnumerable<object>)

批量硬删除。

```C#
public bool DeleteRange(IEnumerable<object> ids);
```

#### 参数

* **`ids`** `IEnumerable<object>`：主键值数组。

#### 返回

`bool`：是否硬删除成功。

#### 用法

```C#
List<Test> tests = new List<Test>();

IEnumerable<object> ids = tests.Select(x => x.TestID).Cast<object>();
bool ret = _helper.DeleteRange(ids);
```

### GetBy(Expression<Func<TEntity, bool>>);

根据表达式获取实体对象。

```C#
public TEntity GetBy(Expression<Func<TEntity, bool>> whereExpression);
```

#### 参数

* **`whereExpression`** `Expression<Func<TEntity, bool>>`：查询条件。

#### 返回

`TEntity`：实体对象。

### GetBy(Expression<Func<TEntity, bool>>, Func<IQueryable<TEntity>, IIncludableQueryable<TEntity, object>>);

根据表达式获取实体对象。

```C#
public TEntity GetBy(Expression<Func<TEntity, bool>> whereExpression, Func<IQueryable<TEntity>, IIncludableQueryable<TEntity, object>> includeExpression);
```

#### 参数

* **`whereExpression`** `Expression<Func<TEntity, bool>>`：查询条件。
* **`includeExpression`** `Func<IQueryable<TEntity>, IIncludableQueryable<TEntity, object>>`：要包含的相关实体。

#### 返回

`TEntity`：实体对象。

#### 用法

```C#
Test test = helper.GetBy(whereExpression: t => t.TestID == 10, 
                includeExpression: t => t.Include(t => t.AnotherTest));
```