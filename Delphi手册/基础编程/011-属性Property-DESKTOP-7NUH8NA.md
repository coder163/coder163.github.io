

Property是一个很有意思的语法特性，它使得方法具有了字段的调用特征，并赋予字段执行动作的能力。

A property, like a field, defines an attribute of an object. But while a field is merely a storage location whose contents can be examined and changed, a property associates specific actions with reading or modifying its data. Properties provide control over access to an object's attributes, and they allow attributes to be computed.

> 摘自官方wiki，谷歌翻译：属性（如字段）定义对象的属性。 但是，尽管字段只是可以检查和更改其内容的存储位置，但属性将特定操作与读取或修改其数据相关联。 属性提供对对对象属性的访问的控制，并且它们允许计算属性。


需要提醒大家的是属性和字段看着很像，但他们不是一回事儿

A field is like a variable that belongs to an object. Fields can be of any type, including class types. (That is, fields can hold object references.) Fields are usually private.

> 摘自官方wiki，谷歌翻译：字段就像属于对象的变量。 字段可以是任何类型，包括类类型。 （也就是说，字段可以保存对象引用。）字段通常是私有的。

说了这么多，那么字段和属性怎么定义呢？翠花，上代码

```pascal
TUserInfo = class
    private
        //此处的变量就是字段，又因为字段的单词是 Field，所以字段一般以 F 开头
        FName: String;
        FAge: Integer;

        procedure FSetAge(theAge: Integer);
    public
        //使用 property 定义属性
        property Name: string read FName;
        //
        property Age: Integer read FAge write FSetAge;
end
```


属性Property的基本格式如下：

```
property 属性名 : 属性值类型 read 属性读函数/属性值变量 write 属性写函数/属性值变量
```

- 属性的特征类似于字段，所以属性名就像字段名，属性值类型就像字段的值类型

- 属性读函数，是属性被“读取”时所执行的操作，这样在执行“取值”操作时，具备了执行其他动作的可能。另外，属性值变量，可以是Property所在类能够访问的任何变量，如果使用了属性值变量，则相当于属性值直接从值变量中获取，这和直接赋值是没有什么差别的

- 属性写函数，是属性被“写入”时所执行的操作，这样在执行属性“赋值”操作时，具备了执行其他动作的可能。比如：写入edit的Text属性时，窗口会同时执行界面更新操作。另外，属性值变量和【属性读函数】中所述类似，如果使用了，就相当于将传来的属性值直接赋值到对应的属性值变量


总结一下：

- 从代码也好、文字描述也罢，我们大致能够看出字段一般是一些定义了具体值的变量，而属性恰恰是改变这些字段值的操作

- 字段只能是变量，而属性可以是变量也可以是方法(包括过程和函数)


## 一点点疑惑

将字段和属性分离，然后通过属性对字段进行读写分离操作有必要这样做吗？

答案是肯定的，之前在群里看到有的朋友对这个就存在疑惑，感觉两者在使用方面没什么区别，其实不然。其中一个理由就是这种方式提供了更为严格权限控制，字段一般情况下是私有的，而私有的字段在不同单元下是无法访问的，属性一般是 public 这种权限就可以任意访问

其次就是我自己也想吐槽的，我定义一个字段，你再让我去写一个属性这不是增加了我的工作量吗？关于这一点我们可以通过Delphi工具帮我们自动生成，类似于我们在写了方法声明之后按下Ctrl+Shif+C可以自动补全方法实现一样，属性也可以自动生成。具体操作如下


![](_v_images/20210619183907604_19413.png)

在需要添加属性的类上右击就可以看到能够创建的选项

![](_v_images/20210619184030593_22183.png)


生成的代码如下

```
type
  TPersion = class
    private
      FName: String;
      procedure SetName(val: String);
    public
      property Name: String read FName write SetName;
  end;

implementation

procedure TPersion.SetName(val: String);
begin
  FName := val;
end;
```


## 参考资料

- 官方Wiki：[http://docwiki.embarcadero.com/RADStudio/Sydney/en/Properties_(Delphi)](http://docwiki.embarcadero.com/RADStudio/Sydney/en/Properties_(Delphi))

- 官方Wiki：[http://docwiki.embarcadero.com/RADStudio/Sydney/en/Fields_(Delphi)](http://docwiki.embarcadero.com/RADStudio/Sydney/en/Fields_(Delphi))










