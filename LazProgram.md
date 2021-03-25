# GetApplicationName

```pascal
uses
  {...}
  SysUtils,
  {...}

function GetApplicationName: String;
begin
  Result:= 'ApplicationNane';
end;

begin
  OnGetApplicationName:= @GetApplicationName;
  {...}
end.
```
