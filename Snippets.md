# Code Snippets

## FPC

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
