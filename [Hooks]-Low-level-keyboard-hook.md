>### NOTE: This page is a work in progress! ###

**Page contents:**  
 - [Creating a low-level keyboard hook](#creating-a-low-level-keyboard-hook)
 - [Removing the hook](#removing-the-hook)
 - [Responding to events](#responding-to-events)
    - [Adding an event handler](#adding-an-event-handler)
    - [Event Args](#event-args)

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

**NOTE:** It is recommended that you keep the hook variable at class-level, and dispose it when you no longer need it.

```vb.net
Dim KeyboardHook As InputHelper.Hooks.KeyboardHook

Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
    KeyboardHook = New InputHelper.Hooks.KeyboardHook()
End Sub

Private Sub StopButton_Click(sender As System.Object, e As System.EventArgs) Handles StopButton.Click
    KeyboardHook.Dispose()
End Sub
```

<br/>

## Responding to events ##

The keyboard hook includes two events:

 - `KeyDown`
 - `KeyUp`

These are responsible for notifying the programmer when a keystroke occurs.

Like the name suggests the `KeyDown` event is raised whenever a key is pressed or held down on the keyboard. Likewise the `KeyUp` event is raised whenever a key is released.

### Adding an event handler ###

Adding an event handler is done either with the [**`AddHandler` statement**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/addhandler-statement) or a combination of the [**`WithEvents` keyword**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/modifiers/withevents) and [**`Handles` clause**](https://docs.microsoft.com/en-us/dotnet/visual-basic/language-reference/statements/handles-clause).

### Event Args ###

_Event Arguments_ (or _Event Args_ for short) are a set of properties passed along with an event that contains information and data about the event.