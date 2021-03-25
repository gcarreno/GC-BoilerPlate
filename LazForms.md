# InitShortCuts

```pascal
uses
  LCLType;
  
procedure InitShortCuts;
begin
{$IFDEF LINUX}
  actFileExit.ShortCut := KeyToShortCut(VK_Q, [ssCtrl]);
{$ENDIF}
{$IFDEF WINDOWS}
  actFileExit.ShortCut := KeyToShortCut(VK_X, [ssAlt]);
{$ENDIF}
end
```

# SetPropertyStorageActive

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
