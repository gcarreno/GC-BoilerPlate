# Code Snippets

## FPC

### Time Difference
```pas
function FormatElapsed(const APrevious: TDateTime): String;
// Kindly pinched, with authorisation, from KodeZwerg
var
  diffSecs: Int64;
  days, hours, minutes, seconds: Integer;
begin
  diffSecs := SecondsBetween(Now, APrevious);
  days := diffSecs div SecsPerDay;
  diffSecs := diffSecs mod SecsPerDay;
  hours := diffSecs div SecsPerHour;
  diffSecs := diffSecs mod SecsPerHour;
  minutes := diffSecs div SecsPerMin;
  seconds := diffSecs mod SecsPerMin;
  Result := Format('%dd %dh %dm %ds', [days, hours, minutes, seconds]);
end;
```

### File Size
```pas
function FileSizeToString(const ASize: UInt64; const oneKB: Integer = {$IFDEF MSWINDOWS}1024{$ELSE}1000{$ENDIF}): String;
// Kindly pinched, with authorisation, from KodeZwerg
var
  Calc: Extended;
  Sign: String;
  SizeLength: Integer;
begin
  SizeLength := 0;
  Calc := ASize;
  while (Calc > oneKB) do
    begin
      Calc := Calc / oneKB;
      Inc(SizeLength);
    end;
  case SizeLength of
    0: Sign := ' byte';
    1: Sign := ' kb'; // Kilo Byte
    2: Sign := ' mb'; // Mega Byte
    3: Sign := ' gb'; // Giga Byte
    4: Sign := ' tb'; // Tera Byte
    5: Sign := ' pb'; // Peta Byte
    6: Sign := ' eb'; // Exa Byte
    7: Sign := ' zb'; // Zetta Byte
    8: Sign := ' yb'; // Yotta Byte
    9: Sign := ' rb'; // Ronna Byte
    10:Sign := ' qb'; // Quetta Byte
  else
    Sign :=' (' + IntToStr(SizeLength) + ')';
  end;
  Result := FormatFloat('#,##0.00', Calc) + Sign;
end;
```

## Lazarus

### `defines.inc`

```pas
{$IF Defined(DCC) or Defined(VER210) or Defined(VER200) or Defined(VER190) or Defined(VER185) or Defined(VER180) or Defined(VER170) or Defined(VER160) or Defined(VER150) or Defined(VER140) or Defined(VER130) or Defined(VER120) or Defined(VER100) or Defined(VER90) or Defined(VER80)}
  {$DEFINE Delphi} { Delphi }
{$IFEND}

{$IF Defined(DELPHI) and Declared(CompilerVersion) and (CompilerVersion >= 25)}
  {$LEGACYIFEND ON}
{$IFEND}

{$IF Defined(FPC)}
  {$DEFINE Lazarus} { Lazarus and Free Pascal }
{$IFEND}

{$IF Defined(DELPHI) and Declared(CompilerVersion) and (CompilerVersion >= 23)}
  {$DEFINE NameSpace} { Delphis NameSpace feature (eg Winapi.Windows instead of Windows) }
{$IFEND}

{$IF Defined(DELPHI) and Declared(CompilerVersion) and (CompilerVersion >= 20)}
  {$DEFINE UniCode} { Delphis UniCode support }
{$IFEND}

{$IF Defined(WIN32) or Defined(WIN64) or Defined(MSWindows)}
  {$DEFINE Windows} { We are on Windows }
{$IFEND Windows}

{$IF Defined(FPC) and Declared(FPC_VERSION) and (FPC_VERSION >= 3)}
  {$DEFINE UniCode} { FreePascal UniCode support }
{$IFEND}
```
