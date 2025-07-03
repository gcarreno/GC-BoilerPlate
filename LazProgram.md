# Lazarus Program

## GetApplicationName

```pascal
program MyProgram;
uses
  {...}
  SysUtils,
  {...}

function GetApplicationName: String;
begin
  Result:= 'ApplicationName';
end;

begin
  OnGetApplicationName:= @GetApplicationName;
  {...}
end.
```
