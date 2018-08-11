Contents:

 - [Creating a low-level keyboard hook](#creating-a-low-level-keyboard-hook)
 - [Removing the hook](#removing-the-hook)
 - [Recommended structure](#recommended-structure)
 - [Responding to events](#responding-to-events)
    - [Adding an event handler](#adding-an-event-handler)
    - [Event Args](#event-args)
 - [Modifier keys](#modifier-keys)

# **Creating a low-level keyboard hook** #

To create a low-level keyboard hook with InputHelper you simply have to instantiate an instance of the `InputHelper.Hooks.KeyboardHook` class.

```vb.net
Dim KeyboardHook As New InputHelper.Hooks.KeyboardHook()
```

The hook is now created and will be listening for keyboard events.

<br/>

# **Removing the hook** #

The hook can be removed manually by calling its `Dispose()` method.

```vb.net
'Removing the hook manually.
KeyboardHook.Dispose()
```

However, the hook will also be removed automatically if the variable goes out of scope:

```vb.net
'Example of a hook getting removed automatically.
Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
    Dim KeyboardHook As New InputHelper.Hooks.KeyboardHook()

    'Stuff happens here...

End Sub 'Once code execution reaches this point the hook variable goes out of scope and will be removed automatically.
```

<br/>

# **Recommended structure** #

It is recommended that you keep the hook variable at class-level, and dispose it when you no longer need it. This is because it gives you better control over the hook and decreases the risk of it accidentally going out of scope.

Below is an example of using the hook in a form with a **Start** and **Stop** button, where the hook is started automatically when the form opens:

```vb.net
Imports InputHelperLib

Public Class Form1
    Dim KeyboardHook As InputHelper.Hooks.KeyboardHook

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        KeyboardHook = New InputHelper.Hooks.KeyboardHook() 'Start the hook when the form opens.
    End Sub

    Private Sub StartButton_Click(sender As System.Object, e As System.EventArgs) Handles StartButton.Click
        If KeyboardHook IsNot Nothing Then KeyboardHook.Dispose() 'Remove the old hook before creating a new one.
        KeyboardHook = New InputHelper.Hooks.KeyboardHook()
    End Sub

    Private Sub StopButton_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
         If KeyboardHook IsNot Nothing Then KeyboardHook.Dispose()
    End Sub
End Class
```

<br/>

# **Responding to events** #

The keyboard hook includes two events:

 - `KeyDown`
 - `KeyUp`

These are responsible for notifying the programmer when a keystroke occurs.

Like the name suggests the `KeyDown` event is raised whenever a key is pressed or held down on the keyboard. Likewise the `KeyUp` event is raised whenever a key is released.

<br/>

### Adding an event handler ###

Adding an event handler is done either with the [**`AddHandler` statement**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/addhandler-statement) or using a combination of the [**`WithEvents` keyword**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/modifiers/withevents) and [**`Handles` clause**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/handles-clause).

Using `AddHandler`:

```vb.net
Dim KeyboardHook As InputHelper.Hooks.KeyboardHook

Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
    KeyboardHook = New InputHelper.Hooks.KeyboardHook()

    'Adding event handlers to the KeyDown and KeyUp events.
    AddHandler KeyboardHook.KeyDown, AddressOf KeyboardHook_KeyDown
    AddHandler KeyboardHook.KeyUp, AddressOf KeyboardHook_KeyUp
End Sub

Private Sub KeyboardHook_KeyDown(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs)
    'KeyDown event.
End Sub

Private Sub KeyboardHook_KeyUp(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs)
    'KeyUp event.
End Sub
```

Using `WithEvents` and the `Handles` clause:

```vb.net
'When using WithEvents and the Handles clause the hook must be initialized immediately at class-level.
Dim WithEvents KeyboardHook As New InputHelper.Hooks.KeyboardHook()

Private Sub KeyboardHook_KeyDown(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs) Handles KeyboardHook.KeyDown
    'KeyDown event.
End Sub

Private Sub KeyboardHook_KeyUp(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs) Handles KeyboardHook.KeyUp
    'KeyUp event.
End Sub
```

**NOTE:** It is recommended to use the `AddHandler` solution if the hook may be disposed/reinstantiated multiple times during the lifetime of the form.

<br/>

### Event Args ###

_Event Arguments_ (or _Event Args_ for short) are a set of properties passed along with an event that contains information and data about the event.

InputHelper's event arguments can be accessed through the **`e`** parameter of the event.

```vb.net
Private Sub KeyboardHook_KeyDown(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs) Handles KeyboardHook.KeyDown
    MessageBox.Show("Key: " & e.KeyCode.ToString() & ", Modifiers: " & e.Modifiers.ToString())
End Sub
```

Available properties are:

| Property  | Access      | Description |
| --------- | ----------- | ----------- |
| Block     | Read, Write | Whether the keystroke should be blocked from reaching any windows.<br/>**CAUTION:** Blocked keystrokes will not be handled by any application, nor Windows itself (apart from `CTRL + ALT + DEL`). Whatever problems caused by using this property is _**your own responsibility**_.
| Extended  | Read        | Whether the keystroke message originated from one of the additional keys on the enhanced keyboard.<br/>\- **Read more**: [**Keystroke Message Flags - About Keyboard Input - MSDN**](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646267(v=vs.85).aspx#_win32_Keystroke_Message_Flags)).
| KeyCode   | Read        | The keyboard code of the key that generated the keystroke.
| KeyState  | Read        | The current state of the key that generated the keystroke (down or up).
| Modifiers | Read        | The modifier keys that was pressed in combination with the keystroke.<br/>\- **Valid modifiers:** `Control`, `Shift`, `Alt`, `Windows`
| ScanCode  | Read        | The hardware scan code of the key that generated the keystroke.<br/>\- **Read more:** [**Scancode - Wikipedia**](https://en.wikipedia.org/wiki/Scancode).

<br/>

# **Modifier keys** #

Checking if a modifier has been pressed during a `KeyDown` or `KeyUp` event can be done via the `e.Modifiers` property.

```vb.net
Private Sub KeyboardHook_KeyDown(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs) Handles KeyboardHook.KeyDown
    'Checks for the key combination CTRL + A.
    If e.Modifiers = InputHelper.ModifierKeys.Control AndAlso e.KeyCode = Keys.A Then
        MessageBox.Show("CTRL + A was pressed.")
    End If
End Sub
```

Checking for multiple modifiers at the same time can be done by comparing the `e.Modifiers` property with a [**bitwise-or**](https://en.wikipedia.org/wiki/Bitwise_operation) combination of any of the `InputHelper.ModifierKeys` values.

```vb.net
Private Sub KeyboardHook_KeyDown(sender As System.Object, e As InputHelperLib.InputHelper.Hooks.KeyboardHookEventArgs) Handles KeyboardHook.KeyDown
    'Checks for the key combination CTRL + SHIFT + S.
    If e.Modifiers = (InputHelper.ModifierKeys.Control Or InputHelper.ModifierKeys.Shift) AndAlso e.KeyCode = Keys.S Then
        MessageBox.Show("CTRL + SHIFT + S was pressed.")
    End If
End Sub
```