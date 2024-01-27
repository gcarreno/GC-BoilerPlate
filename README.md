# Boiler Plate files for Gustavo Carreno

## QEMU.md

Instruction on how to install `QEMU` and setting it up.

## Laravel.md

Some instruction about creating a new `Laravel` application.

## GitHub.md

- [Badges](GitHub.md#badges)

## CONTRIBUTING.md

Contributing guidelines.

## CommonSyntaxHilight.md

My list of syntax hilighting examples.

## Snipets.md

A list of useful code snippets.

## Object Pascal

### Time difference
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

### File size
```pas
function FileSizeToString(const ASize: UInt64; const oneKB: Integer = {$IFDEF MSWINDOWS}1024{$ELSE}1000{$ENDIF}): string;
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

## LazProgram.md

- [GetApplicationName](LazProgram.md#getapplicationname)

## LazForms.md

- [InitShortCuts](LazForms.md#initshortcuts)
- [SetPropertyStorageActive](LazForms.md#setpropertystorageactive)

## FPCupDeluxe.md

Contains information regarding [fpcupdeluxe](https://github.com/newpascal/fpcupdeluxe).

## FPDOC.md

Scripts for `fpdoc`, documentation generator.

## GITIgnore.md

GIT ignore for various project types.

## GITAttributes.md

GIT attributes for various project types.

## CakePHP.md

Step to get a functional `CakePHP` app with Authentication and Authorisation.

## MySQL.md

Some useful commands for `MySQL`.
