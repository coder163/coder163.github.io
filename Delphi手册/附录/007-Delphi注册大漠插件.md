

官方首页介绍：大漠综合插件 (dm.dll)采用vc6.0编写采用COM接口编写，适用于所有语言调用.

参考网址：https://www.swissdelphicenter.ch/en/showcode.php?id=1281

>此外代码参考四哥例子，在此拜谢大神

编译环境

- 操作系统：Windows10
- 操作工具：Delphi 10.2.2

## 导入dm.dll

Component->Import Component->默认Import a Type Library，点击Next->找到Dm.dll（若未注册，点击add添加）选中，点击Next->设置下Unit Dir Name，点击Next->点击Finish。

此时会在设置的路径下自动生成Dm_TLB.pas。

## COM DLL注册大漠

从网上查询com dll的注册方法，最后注册 、卸载成功


### 第一种方式

代码清单：

```pascal
uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, Dm_TLB, ComObj, ActiveX, StdCtrls, Buttons, ToolWin, ComCtrls;

function OLERegister(sOleFileName: string; OleAction: Byte): Boolean;
const
  RegisterOle = 1; //注册
  UnRegisterOle = 0; //卸载
type
  TOleRegisterFunction = function: HResult; //注册或卸载函数原型
var
  hLibraryHandle: THandle; //由LoadLibray返回的DLL或OCX句柄
  hFunctionAddress: TFarProc; //DLL或OCX中的函数句柄，由GetProAddress返回
  RegFunction: TOleRegisterFunction; //注册或卸载函数指针
begin
  Result := False;

  //打开文件，返回DLL或OCX句柄
  hLibraryHandle := LoadLibrary(PChar(sOleFileName));
  if (hLibraryHandle > 0) then //DLLakg OCX句柄正确
  try
    //返回注册或卸载函数指针
    if (OleAction = RegisterOle) then  //返回注册函数指针
    begin

      hFunctionAddress := GetProcAddress(hLibraryHandle, PAnsiChar('DllRegisterServer'))

    end
    else //返回卸载函数指针
      hFunctionAddress := GetProcAddress(hLibraryHandle, PAnsiChar('DllUnRegisterServer'));

    if (hFunctionAddress <> nil) then //判断注册或卸载函数是否存在
    begin

      RegFunction := TOleRegisterFunction(hFunctionAddress); //获取操作函数的指针

      if RegFunction >= 0 then  //执行注册或卸载操作，返回值>=0表示执行成功
      begin

        Result := True;
      end;

    end;
  finally
    FreeLibrary(hLibraryHandle); //关闭已打开的文件
  end;
end;

procedure TForm1.Button1Click(Sender: TObject);
var
  mydm: Idmsoft;
  ole: Boolean;
begin
  ole := OLERegister('dm.dll', 1);
  if ole then begin
    mydm := CreateOleObject('dm.dmsoft') as Idmsoft;
    ShowMessage('初始化成功 插件版本:' + mydm.Ver);
  end;

end;

```

### 第二种方式

```pascal
uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, Dm_TLB, ComObj, ActiveX, StdCtrls, Buttons, ToolWin, ComCtrls;

function CreateComObjectFromDll(CLSID: TGUID; DllHandle: THandle): IUnknown;

implementation

{$R *.dfm}

function CreateComObjectFromDll(CLSID: TGUID; DllHandle: THandle): IUnknown;
var
  Factory: IClassFactory;
  hr: HRESULT;
  DllGetClassObject: function(const CLSID, IID: TGUID; var Obj): HResult; stdcall;
begin
  DllGetClassObject := GetProcAddress(DllHandle, 'DllGetClassObject');
  if Assigned(DllGetClassObject) then begin
    hr := DllGetClassObject(CLSID, IClassFactory, Factory);
    if hr = S_OK then begin
      Factory.CreateInstance(nil, IUnknown, Result);
      //Factory._Release;
    end;
  end;
end;

procedure TForm1.Button2Click(Sender: TObject);
var
  dmhdl: THandle;
  mydm: Idmsoft;
begin
  dmhdl := LoadLibrary('dm.dll');
  if dmhdl < 32 then
    ShowMessage('初始化成功'#13'请检测运行目录中是否含有dm.dll文件')
  else begin
    mydm := CreateComObjectFromDll(CLASS_dmsoft, dmhdl) as Idmsoft;
    ShowMessage('初始化成功 插件版本:' + mydm.Ver);
    mydm := nil;
  end;
end;
```