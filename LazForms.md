# Lazarus Forms

## InitShortCuts

```pascal
uses
  LCLType;
  
procedure InitShortCuts;
begin
{$IFDEF UNIX}
  actFileExit.Caption:= '&Quit';
  actFileExit.ShortCut := KeyToShortCut(VK_Q, [ssCtrl]);
{$ENDIF}
{$IFDEF WINDOWS}
  actFileExit.Caption:= 'E&xit';
  actFileExit.ShortCut := KeyToShortCut(VK_F4, [ssAlt]);
{$ENDIF}
end
```

## Translation

```pascal
uses
  {...}DefaultTranslator{...}
```

## Status Bar Hint

```pascal
interface
type
  MyForm: class(TForm)
    sbmain: TStatusBar;
  //...
  private
    procedure DisplayHint(Sender: TObject);
  public
    procedure FormCreate(Sender: Tobject);
implementation

procedure MyForm.DisplayHint(Sender: Tobject);
begin
  sbMain.SimpleText:= GetShortHint(Application.Hint);
end;

procedure MyForm.FormCreate(Sender: Tobject);
begin
  Application.OnHint:=@DisplayHint;
end;
```

## SetPropertyStorageActive

```pascal
procedure TfrmMain.SetPropertyStorageActive;
begin
{$IFDEF WINDOWS}
  if not DirectoryExists(GetAppConfigDir(False)) then
  begin
    ForceDirectories(GetAppConfigDir(False));
  end;
{$ENDIF}
  JSONPropStorage.JSONFileName:= GetAppConfigFile(False);
  JSONPropStorage.Active:= True;
end;
```
