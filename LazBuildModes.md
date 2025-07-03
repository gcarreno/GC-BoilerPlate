# Lazarus Build Modes

## All build modes

> [!NOTE]
> This may be needed if your source code has non `AnsiString` characters in it.

Include the following on all build modes at Project Options->Compiler Options->Custom Options:
```
-FcUTF8
```
You can achieve that with the button `Add -FcUTF8`, if present.

## Debug build mode

> [!NOTE]
> This will allow debug code to be ran, somewhat, at a global level, without the need to change constants in multiple files.

Add this to globally trigger debug code enclosed in `{$IFDEF DEBUG}..{$ENDIF}`:
1. Navigate to Project Options->Compiler Options->Custom Options.
2. Click on the `Defines...` button.
3. Add a new define called `DEBUG`.
4. On the Debug build mode, check the box to activate it.