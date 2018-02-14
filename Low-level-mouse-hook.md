Contents:

 - [Creating a low-level mouse hook](#creating-a-low-level-mouse-hook)
 - [Removing the hook](#removing-the-hook)
 - [Recommended structure](#recommended-structure)
 - [Responding to events](#responding-to-events)
    - [Adding event handlers](#adding-event-handlers)
    - [Event Args](#event-args)

# **Creating a low-level mouse hook** #

To create a low-level mouse hook with InputHelper you simply have to instantiate an instance of the `InputHelper.Hooks.KeyboardHook` class.

```vb.net
Dim MouseHook As New InputHelper.Hooks.MouseHook()
```

The hook is now created and will be listening for mouse events.

<br/>

# **Removing the hook** #

The mouse hook can be removed manually by calling its `Dispose()` method.

```vb.net
'Removing the hook manually.
MouseHook.Dispose()
```

However, the hook will also be removed automatically if the variable goes out of scope:

```vb.net
'Example of a hook getting removed automatically.
Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
    Dim MouseHook As New InputHelper.Hooks.MouseHook()

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
    Dim MouseHook As InputHelper.Hooks.MouseHook

    Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        MouseHook = New InputHelper.Hooks.MouseHook() 'Start the hook when the form opens.
    End Sub

    Private Sub StartButton_Click(sender As System.Object, e As System.EventArgs) Handles StartButton.Click
        If MouseHook IsNot Nothing Then MouseHook.Dispose() 'Remove the old hook before creating a new one.
        MouseHook = New InputHelper.Hooks.MouseHook()
    End Sub

    Private Sub StopButton_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
         If MouseHook IsNot Nothing Then MouseHook.Dispose()
    End Sub
End Class
```

<br/>

# **Responding to events** #

The mouse hook includes four events. They are responsible for notifying the programmer when a mouse event occurs.

 - `MouseDown` - Occurs when a mouse button is pressed or held down.
 - `MouseMove` - Occurs when the mouse moves.
 - `MouseUp` - Occurs when a mouse button is released.
 - `MouseWheel` - Occurs when the mouse wheel is scrolled.

<br/>

### Adding event handlers ###

Adding an event handler to one of the `MouseHook`'s events is done either with the [**`AddHandler` statement**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/addhandler-statement) or using a combination of the [**`WithEvents` keyword**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/modifiers/withevents) and [**`Handles` clause**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/handles-clause).

Using `AddHandler`:

```vb.net
Dim MouseHook As InputHelper.Hooks.MouseHook

Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
    MouseHook = New InputHelper.Hooks.MouseHook()

    'Adding event handlers to the MouseHook events.
    AddHandler MouseHook.MouseDown, AddressOf MouseHook_MouseDown
    AddHandler MouseHook.MouseMove, AddressOf MouseHook_MouseMove
    AddHandler MouseHook.MouseUp, AddressOf MouseHook_MouseUp
    AddHandler MouseHook.MouseWheel, AddressOf MouseHook_MouseWheel
End Sub

Private Sub MouseHook_MouseDown(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    'MouseDown event.
End Sub

Private Sub MouseHook_MouseMove(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    'MouseMove event.
End Sub

Private Sub MouseHook_MouseUp(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    'MouseUp event.
End Sub

Private Sub MouseHook_MouseWheel(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    'MouseWheel event.
End Sub
```

Using `WithEvents` and the `Handles` clause:

```vb.net
'When using WithEvents and the Handles clause the hook must be initialized immediately at class-level.
Dim WithEvents MouseHook As New InputHelper.Hooks.MouseHook()

Private Sub MouseHook_MouseDown(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs) Handles MouseHook.MouseDown
    'MouseDown event.
End Sub

Private Sub MouseHook_MouseMove(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs) Handles MouseHook.MouseMove
    'MouseMove event.
End Sub

Private Sub MouseHook_MouseUp(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs) Handles MouseHook.MouseUp
    'MouseUp event.
End Sub

Private Sub MouseHook_MouseWheel(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs) Handles MouseHook.MouseWheel
    'MouseWheel event.
End Sub
```

**NOTE:** It is recommended to use the `AddHandler` solution if the hook may be disposed/reinstantiated multiple times during the lifetime of the form.

<br/>

### Event Args ###

_Event Arguments_ (or _Event Args_ for short) are a set of properties passed along with an event that contains information and data from, or about, the event.

InputHelper's event arguments can be accessed through the **`e`** parameter of the event.

```vb.net
Private Sub MouseHook_MouseDown(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    MessageBox.Show("Mouse button clicked: " & e.Button.ToString() & ", Double-click: " & e.DoubleClick.ToString())
End Sub

Private Sub MouseHook_MouseMove(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    TextBox1.AppendText("Mouse moved to (X: " & e.Location.X & ", Y: " & e.Location.Y & ")")
End Sub

Private Sub MouseHook_MouseWheel(sender As Object, e As InputHelperLib.InputHelper.Hooks.MouseHookEventArgs)
    MessageBox.Show("Mouse scroll: " & e.ScrollDirection.ToString() & ", Amount: " & e.Delta)
End Sub
```

Available properties are:

| Property        | Access      | Description |
| --------------- | ----------- | ----------- |
| Block           | Read, Write | Whether the mouse input should be blocked from reaching any windows.<br/>**CAUTION:** Blocked mouse input will not be handled by any application, nor Windows itself! Any problems caused by using this property is _**your own responsibility!**_ _(Any event)_
| Button          | Read        | The mouse button that was pressed or released _(MouseDown or MouseUp events only)_.
| ButtonState     | Read        | The state of the button that generated the event (Down or Up) _(MouseDown and MouseUp events only)_.
| Delta           | Read        | The amount that the mouse wheel has scrolled. This is positive if the mouse wheel is rotated upwards (away from the user) or negative if the mouse wheel is rotated in a downwards (toward the user) _(MouseWheel event only)_.
| DoubleClick     | Read        | Whether the clicked/released button was caused by a double-click _(MouseDown event only)_.
| Location        | Read        | The current location of the mouse (in screen coordinates) _(Any event)_.
| ScrollDirection | Read        | In which direction the mouse wheel was scrolled (vertically or horizontally) _(MouseWheel event only)_.