---
title: Belongs To
layout: page
---

## Belongs To

`belongs to` 会与另一个模型建立了一对一的连接。 这种模型的每一个实例都“属于”另一个模型的一个实例。

例如，您的应用包含 user 和 company，并且每个 user 能且只能被分配给一个 company。下面的类型就表示这种关系。 注意，在 `User` 对象中，有一个和 `Company` 一样的 `CompanyID`。 默认情况下， `CompanyID` 被隐含地用来在 `User` 和 `Company` 之间创建一个外键关系， 因此必须包含在 `User` 结构体中才能填充 `Company` 内部结构体。

```go
// `User` 属于 `Company`，`CompanyID` 是外键
type User struct {
  gorm.Model
  Name      string
  CompanyID int
  Company   Company
}

type Company struct {
  ID   int
  Name string
}
```

请参阅 [[预加载](belongs_to.html#预加载)](belongs_to.html#Eager-Loading) 以了解内部结构的详细信息。

## 重写外键

要定义一个 belongs to 关系，数据库的表中必须存在外键。默认情况下，外键的名字，使用拥有者的类型名称加上表的主键的字段名字

例如，定义一个User实体属于Company实体，那么外键的名字一般使用CompanyID。

GORM同时提供自定义外键名字的方式，如下例所示。

```go
type User struct {
  gorm.Model
  Name         string
  CompanyRefer int
  Company      Company `gorm:"foreignKey:CompanyRefer"`
  // 使用 CompanyRefer 作为外键
}

type Company struct {
  ID   int
  Name string
}
```

## 重写引用

对于 belongs to 关系，GORM 通常使用数据库表，主表（拥有者）的主键值作为外键参考。 正如上面的例子，我们使用主表Company中的主键字段ID作为外键的参考值。

如果在Company实体中设置了User实体，那么GORM会自动把Company中的ID属性保存到User的CompanyID属性中。

同样的，您也可以使用标签 `references` 来更改它，例如：

```go
type User struct {
  gorm.Model
  Name      string
  CompanyID string
  Company   Company `gorm:"references:Code"` // 使用 Code 作为引用
}

type Company struct {
  ID   int
  Code string
  Name string
}
```

{% note warn %}
**NOTE** GORM usually guess the relationship as `has one` if override foreign key name already exists in owner's type, we need to specify `references` in the `belongs to` relationship.
{% endnote %}

```go
type User struct {
  gorm.Model
  Name      string
  CompanyID string
  Company   Company `gorm:"references:CompanyID"` // use Company.CompanyID as references
}

type Company struct {
  CompanyID   int
  Code        string
  Name        string
}
```

## Belongs to 的 CRUD

Please checkout [Association Mode](associations.html#Association-Mode) for working with belongs to relations

## 预加载

GORM allows eager loading belongs to associations with `Preload` or `Joins`, refer [Preloading (Eager loading)](preload.html) for details

## 外键约束

You can setup `OnUpdate`, `OnDelete` constraints with tag `constraint`, it will be created when migrating with GORM, for example:

```go
type User struct {
  gorm.Model
  Name      string
  CompanyID int
  Company   Company `gorm:"constraint:OnUpdate:CASCADE,OnDelete:SET NULL;"`
}

type Company struct {
  ID   int
  Name string
}
```
