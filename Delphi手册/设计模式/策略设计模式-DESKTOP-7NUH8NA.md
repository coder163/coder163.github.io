
策略模式一般情况下应用于当一个对象有某个行为，但是在不同的场景有不同的实现方式，例如，旅游的出行方式，我们可以选择骑自行车、也可以选择坐汽车、飞机、轮船等等，每一种旅行方式都是一个策略

> 如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。

## 优点

- 算法可以自由切换。

- 避免使用多重条件判断。

- 扩展性良好。


## 缺点

- 策略类会增多。

- 所有策略类都需要对外暴露。




本文中的代码在Delphi10.4中测试通过，有的朋友版本可能略低，只需要把变量的声明方式改变一下即可

**uStrategy：策略模式的核心代码**

```pascal
unit uStrategy;
​
interface
​
type
    {策略接口}
    IStrategy = interface(IUnKnown)
        ['{E919FE8C-EEF8-4B86-846B-B48D65797DDF}']
        //制定策略
        function doOperation(num1, num2: Integer): Integer;
    end;
​
    //策略的具体实现方式一
    TOperationAdd = class(TInterfacedObject, IStrategy)
    public
        function doOperation(num1, num2: Integer): Integer;
    end;
​
    //策略的具体实现方式二
    TOperationSubtract = class(TInterfacedObject, IStrategy)
    public
        function doOperation(num1, num2: Integer): Integer;
    end;
    //真正使用策略的类
    TOperation = class(TObject)
    public
        //Strategy:策略的类型需要使用接口类型，这样传任意的策略接口实现类即可
        function ExecuteStrategy(num1, num2: Integer; Strategy: IStrategy): Integer;
    end;
​
implementation
{ TOperationAdd }
​
function TOperationAdd.doOperation(num1, num2: Integer): Integer;
begin
​
    Result := num1 + num2;
end;
{ TOperationSubtract }
​
function TOperationSubtract.doOperation(num1, num2: Integer): Integer;
begin
    Result := num1 - num2;
end;
{ TOperation }
function TOperation.ExecuteStrategy(num1, num2: Integer; Strategy: IStrategy): Integer;
begin
    Result := Strategy.doOperation(num1, num2);
​
end;
​
end.
```



**测试代码单元**

```pascal

program ProjecttTategy;
​
{$APPTYPE CONSOLE}
​
{$R *.res}
​
uses
    System.SysUtils,
    uStrategy in 'uStrategy.pas',
    UnitBack in 'UnitBack.pas';
​
begin
    var Operation := TOperation.Create();
    //我们只需要更换不同的策略实现对象，即可完成不同的运算，而TOperation类中的代码不需要做任何更改
    var res := Operation.ExecuteStrategy(1, 2, TOperationAdd.Create());
​
    writeln(res.ToString);
    readln;
end.
```



> 这个例子我以加法和减法运算作为不同的实现的方式，目的是降低设计模式的学习难度，本来写了个结合泛型来实现数组排序的，但是会让策略模式变的难以理解，所以被我废弃了，后面有机会再和大家分享

## 开放封闭原则

我们在编写代码的时候应该尽量遵循面向对象程序设计的五大原则，而开放封闭就是其中之一。软件设计本身所追求的目标就是封装变化、降低耦合，而开放封闭原则正是对这一目标的最直接体现

开放封闭原则指的是一个模块在扩展性方面应该是开放的而在更改性方面应该是封闭的，就我们上面的案例即接口和实现类为扩展性的代码，TOperation类为非扩展的，所以我们一般情况下应该保证在不修改TOperation它代码的前提下实现功能的扩展
