**Content:**  
 - [Creating a low-level keyboard hook](#creating-a-low-level-keyboard-hook)
 - [Removing the hook](#removing-the-hook)

<br/>

## Creating a low-level keyboard hook ##

To create a low-level keyboard hook you just need to instantiate an instance of the `InputHelper.Hooks.KeyboardHook` class. 

```vb.net
    Dim KeyboardHook As New InputHelper.Hooks.KeyboardHook()
```

<br/>

## Removing the hook ##

To remove the hook you just have to call its `Dispose()` method. The hook will also be removed automatically if it goes out of scope.

```vb.net
    KeyboardHook.Dispose()
```

It is recommended that you keep the hook variable on class-level, and dispose it when you no longer need it.

```vb.net
    Dim KeyboardHook As InputHelper.Hooks.KeyboardHook

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        KeyboardHook = New InputHelper.Hooks.KeyboardHook()
    End Sub

    Private Sub StopButton_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
        KeyboardHook.Dispose()
    End Sub
```