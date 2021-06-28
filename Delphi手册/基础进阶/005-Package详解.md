## 概述

Delphi中的包是Delphi程序间通讯与模块化的一个方法，其中包可分为设计包（比如控件都有一个设计包）和运行包，包的本质和Win32下的DLL并无本质区别，可以理解为Borland专有的一个DLL，只不过这个“DLL”只能用Delphi开发的程序中。

这有点类似于VC下的可以导出整个类的DLL--这种DLL只能被使用MFC编程的程序使用。Delphi运行包的好处与VC中的专有DLL有相同之处：使用十分方便，开发的程序可以很容易模块化，便于合作开发。

### 优缺点

优点

- 应用程序可以被高度的模块化，而且可以逐渐交付完成的功能给客户
- 维护方便，可以只更新单一的模块功能
- 提升程序的载入速度

缺点

- 有些情形下使用Package只能间接參考的方式取得资料(变量, 类 …).
- Package Name 不能重复.
- Contains 中的 Unit Name 不能在**所有**的Package中重复出现，只能出现一次
- PackageA有使用到PackageB必需要在Requires中引用 但是PackageA及PackageB**不能彼此循环引用.**

### 分类

当用户运行应用程序时，运行时程序包提供功能。 设计时程序包用于在IDE中安装组件并为自定义组件创建特殊的属性编辑器。 单个包可以在设计时和运行时均起作用，并且设计时包经常通过在其require子句中引用运行时包来工作。

- 设计期包（Designtime only） -用来在DELPHI的IDE环境安装控件和为控件建立特殊的属性编辑器。设计期包允许包含控件、属性和控件编辑器等等

- 运行期包（Runtime only）-当运行程序时提供VCL和库函数的支持，操作上很类似标准的动态链接库。Install按钮无法使用。

- 设计和运行期包（Designtime and Runtime ）：设计与运行时都能用

![](_v_images/20210609193003852_9862.png =772x)


### 文件说明


BPL 英文全称 Borland Package library ，是一种特殊的DLL文件，用于代码重用和减少可执行文件。编译bpl时，仅需要添加相应功能的pas文件，如果有窗体，则需要添加dfm文件。既然是DLL文件，那就是在运行时所需要的文件。BPL相当于C++中的DLL

DCP 英文全称：delphi compiled package，是 package 编译时跟 bpl 一起产生出来的，记录着 package 中公开的 class、procedure、function、variable、const.... 等等的名称和相对位置。如果 某个控件包 A 引用了 控件包 B，当 控件包 A 编译时，需要 控件包 B.dcp，若 控件包 B 有修改，更改了公开的界面，则 控件包 A 必须在 控件包 B 编译之后重新编译，以引用新的 B.dcp。否则，当 控件包 A 执行时，执行到引用自 控件包 B 的内容时，就会出现错误。DCP相当于C++中的Lib，编译时需要。


DCU 英文全称：Delphi Compiled Unit File，是delphi单元文件.pas文件编译后产生的文件，感觉没有太大用处。


## 加载方式

Package中的代码

```pascal
unit Unit2;

interface

uses Vcl.Dialogs;

//函数案例
function add(Num1, Num2: Integer): Integer; stdcall;

//过程案例
procedure ShowMsg(Str: String); stdcall;

type
//类的案例
  TUser = class
  public
    function ShowString(): string;
  end;

  // 需要像DLL一样声明导出函数的列表，如果是静态导入此项可以省略
exports add, ShowMsg;

implementation

procedure ShowMsg(Str: String);
begin
  showmessage(Str);
end;

function add(Num1, Num2: Integer): Integer;
begin
  Result := Num1 + Num2;
end;

{ TUser }

function TUser.ShowString: string;
begin

  Result := 'HelloWorld';
end;

end.
```

### 静态加载

一般大家在用Delphi时都是使用『静态载入』, 像VCL的Package就是这种方式, 这种方式的好处是设计者不用去理会Package 的载入和释放, 其实设计者根本感觉不到设用这项技术; 当然也可以手动将Package加入到项目中『project->Options->Packages->Build with runtime packages中加入Package Name彼此的分隔符是分号』

![](_v_images/20210609193222920_5273.png =757x)


![](_v_images/20210609193242388_17262.png =757x)


> 代码实现

基本上是无痛使用，只要路径配置没有问题，基本上和使用普通单元没有区别

```pascal
implementation

uses  Unit2, Unit3;
{$R *.dfm}

procedure TForm1.Button1Click(Sender: TObject);

begin
  showmessage(TUser.create().showString());
  var
  From3 := TForm3.create(nil);
  From3.visible := true;

end;
```

### 动态加载

动态加载和静态加载相反，无论是载入还是释放都要自己来处理，看起来好像是动态载入,这种方式个人感觉相当麻烦，虽然本质上和dll的动态加载一样，但是因为在导入的元素中多了类的概念，所以还需要使用反射的方式创建类的对象才能实现类成员的引用


```pascal
implementation

uses rtti, System.StrUtils;
{$R *.dfm}

procedure TForm1.Button2Click(Sender: TObject);
var
  // 声明和Package导出列表中一致结构的过程
  add01: procedure(Msg: String); stdcall;
  // 声明和Package导出列表中一致结构的函数
  add02: function(Num1: Integer; Num2: Integer): Integer; stdcall;
begin
  // 载入bpl格式的Package
  var
  PackageHandle := LoadPackage('Package1.bpl');

  if PackageHandle <> 0 then
  begin
    // 载入成功之后获取对应函数、过程的指针
    @add01 := GetProcAddress(PackageHandle, 'ShowMsg');
    @add02 := GetProcAddress(PackageHandle, 'add');
    if @add01 <> nil then
    begin
      // 调用
      add01('HelloWorld');
      showmessage(add02(1, 2).Tostring);
    end;

  end;
  // 对于类我们需要先创建类的对象然后才可以实现类中函数的调用
  var
    // 创建运行期上下问对象
  rc := TRttiContext.create;
  var
    // 载入对应单元中的类，注意此处需要写单元名+类名
  ClassType := rc.FindType('Unit2.TUser');

  var
    // 获取元类实例(对象)
  Instance := ClassType.AsInstance;
  var
    // 获取该实例的元信息类型
  QRClass := Instance.MetaclassType;
  var
    // 获取用于创建TUser类型的构造方法
  CreateMethod := Instance.GetMethod('Create');
  var
    // 利用获取到的构造方法对象，创建TUser类对象
  User := CreateMethod.Invoke(QRClass, []);

  var
    // 函数调用
  rs := ClassType.GetMethod('ShowString').Invoke(User, []);
  // 显示返回值
  showmessage(rs.asstring);
  //卸载包
  UnloadPackage(PackageHandle);
end;
```

> 从上面动态加载的代码可以看出涉及到反射相关的知识，个人感觉这种方式在使用起来不太方便，当然如果对反射比较熟悉的话那就没问题了

我在搜索Package相关内容的使用看到下面这段代码，它也可以实现创建类的对象，只是中间出现的类型的强制转换，个人不是特别推荐，只是记录一下作为笔记参考

```pascal
function CreateFormByClassName(ClassName: string): integer;
var
  AClass: TPersistentClass;
  AForm: TCustomForm;
begin
  Result := mrNone;
  AClass := GetClass(ClassName);
  if AClass <> nil then
  begin
    AForm := TComponentClass(AClass).Create(Application) as TCustomForm;
    Result := AForm.ShowModal;
  end;
```

## 官方参考文档

官方文档是英文的，我也是翻看+翻译读了很久挑了几篇有用的

- http://docwiki.embarcadero.com/RADStudio/Sydney/en/Packages_(Delphi)
- http://docwiki.embarcadero.com/RADStudio/Sydney/en/Compiling_Packages
- http://docwiki.embarcadero.com/RADStudio/Sydney/en/Loading_Packages_in_an_Application
- http://docwiki.embarcadero.com/RADStudio/Sydney/en/Add_Runtime_Package