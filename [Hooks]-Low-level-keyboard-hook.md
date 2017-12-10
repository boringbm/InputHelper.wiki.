>### NOTE: This page is a work in progress! ###

**Page contents:**  
 - [Creating a low-level keyboard hook](#creating-a-low-level-keyboard-hook)
 - [Removing the hook](#removing-the-hook)
 - [Recommended structure](#recommended-structure)
 - [Responding to events](#responding-to-events)
    - [Adding an event handler](#responding-to-events--adding-an-event-handler)
    - [Event Args](#responding-to-events--event-args)

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
'Removing the hook manually.
KeyboardHook.Dispose()


'Example of a hook getting removed automatically.
Private Sub Button1_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
    Dim KeyboardHook As New InputHelper.Hooks.KeyboardHook()

    'Stuff happens here...

End Sub 'Once code execution reaches this point the hook variable goes out of scope and will be removed automatically.
```

<br/>

## Recommended structure ##

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

## Responding to events ##

The keyboard hook includes two events:

 - `KeyDown`
 - `KeyUp`

These are responsible for notifying the programmer when a keystroke occurs.

Like the name suggests the `KeyDown` event is raised whenever a key is pressed or held down on the keyboard. Likewise the `KeyUp` event is raised whenever a key is released.

<br/>

### Responding to events > Adding an event handler ###

Adding an event handler is done either with the [**`AddHandler` statement**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/addhandler-statement) or using a combination of the [**`WithEvents` keyword**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/modifiers/withevents) and [**`Handles` clause**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/handles-clause).

**Using `AddHandler`:**

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

**Using `WithEvents` and the `Handles` clause:**

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

**NOTE:** It is recommended to use the `AddHandler` solution to ensure that it works 

<br/>

### Responding to events > Event Args ###

_Event Arguments_ (or _Event Args_ for short) are a set of properties passed along with an event that contains information and data about the event.