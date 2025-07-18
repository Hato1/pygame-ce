.. include:: common.txt

:mod:`pygame.event`
===================

.. module:: pygame.event
   :synopsis: pygame module for interacting with events and queues

| :sl:`pygame module for interacting with events and queues`

Pygame handles all its event messaging through an event queue. The routines in
this module help you manage that event queue. The input queue is heavily
dependent on the :mod:`pygame.display` module. If the display has not been
initialized and a video mode not set, the event queue may not work properly.

The event queue has an upper limit on the number of events it can hold. When
the queue becomes full new events are quietly dropped. To prevent lost events,
especially input events which signal a quit command, your program must handle
events every frame (with ``pygame.event.get()``, ``pygame.event.pump()``,
``pygame.event.wait()``, ``pygame.event.peek()`` or ``pygame.event.clear()``)
and process them. Not handling events may cause your system to decide your
program has locked up. To speed up queue processing use
:func:`pygame.event.set_blocked()` to limit which events get queued.

To get the state of various input devices, you can forego the event queue and
access the input devices directly with their appropriate modules:
:mod:`pygame.mouse`, :mod:`pygame.key`, and :mod:`pygame.joystick`. If you use
this method, remember that pygame requires some form of communication with the
system window manager and other parts of the platform. To keep pygame in sync
with the system, you will need to call :func:`pygame.event.pump()` to keep
everything current. Usually, this should be called once per game loop.
Note: Joysticks will not send any events until the device has been initialized.

The event queue contains :class:`pygame.event.Event` event objects.
There are a variety of ways to access the queued events, from simply
checking for the existence of events, to grabbing them directly off the stack.
The event queue also offers some simple filtering which can slightly help
performance by blocking certain event types from the queue. Use
:func:`pygame.event.set_allowed()` and :func:`pygame.event.set_blocked()` to
change this filtering. By default, all event types can be placed on the queue.

All :class:`pygame.event.Event` instances contain an event type identifier
and attributes specific to that event type. The event type identifier is
accessible as the :attr:`pygame.event.Event.type` property. Any of the
event specific attributes can be accessed through the
:attr:`pygame.event.Event.__dict__` attribute or directly as an attribute
of the event object (as member lookups are passed through to the object's
dictionary values). The event object has no method functions. Users can create
their own new events with the :func:`pygame.event.Event()` function.

The event type identifier is in between the values of ``NOEVENT`` and
``NUMEVENTS``. User defined events should have a value in the inclusive range
of ``USEREVENT`` to ``NUMEVENTS - 1``. User defined events can get a custom
event number with :func:`pygame.event.custom_type()`.
It is recommended all user events follow this system.

Events support equality and inequality comparisons. Two events are equal if
they are the same type and have identical attribute values.

While debugging and experimenting, you can print an event object for a quick
display of its type and members. The function :func:`pygame.event.event_name()`
can be used to get a string representing the name of the event type.

Events that come from the system will have a guaranteed set of member
attributes based on the type. The following is a list of event types with their
specific attributes.

::

    QUIT              none
    ACTIVEEVENT       gain, state
    KEYDOWN           key, mod, unicode, scancode
    KEYUP             key, mod, unicode, scancode
    MOUSEMOTION       pos, rel, buttons, touch
    MOUSEBUTTONUP     pos, button, touch
    MOUSEBUTTONDOWN   pos, button, touch
    JOYAXISMOTION     joy (deprecated), instance_id, axis, value
    JOYBALLMOTION     joy (deprecated), instance_id, ball, rel
    JOYHATMOTION      joy (deprecated), instance_id, hat, value
    JOYBUTTONUP       joy (deprecated), instance_id, button
    JOYBUTTONDOWN     joy (deprecated), instance_id, button
    VIDEORESIZE       size, w, h
    VIDEOEXPOSE       none
    USEREVENT         code

.. versionchangedold:: 2.0.0 The ``joy`` attribute was deprecated, ``instance_id`` was added.

.. versionchangedold:: 2.0.1 The ``unicode`` attribute was added to ``KEYUP`` event.

Note that ``ACTIVEEVENT``, ``VIDEORESIZE`` and ``VIDEOEXPOSE`` are considered
as "legacy" events, the use of pygame2 ``WINDOWEVENT`` API is recommended over
the use of this older API.

You can also find a list of constants for keyboard keys
:ref:`here <key-constants-label>`.

|

When compiled with SDL2, pygame has these additional events and their
attributes.

::

    AUDIODEVICEADDED   which, iscapture
    AUDIODEVICEREMOVED which, iscapture
    FINGERMOTION       touch_id, finger_id, x, y, dx, dy
    FINGERDOWN         touch_id, finger_id, x, y, dx, dy
    FINGERUP           touch_id, finger_id, x, y, dx, dy
    MOUSEWHEEL         which, flipped, x, y, touch, precise_x, precise_y
    MULTIGESTURE       touch_id, x, y, pinched, rotated, num_fingers
    TEXTEDITING        text, start, length
    TEXTINPUT          text

.. versionaddedold:: 1.9.5

.. versionchangedold:: 2.0.2 Fixed amount horizontal scroll (x, positive to the right and negative to the left).

.. versionchangedold:: 2.0.2 The ``touch`` attribute was added to all the ``MOUSE`` events.

The ``touch`` attribute of ``MOUSE`` events indicates whether or not the events were generated
by a touch input device, and not a real mouse. You might want to ignore such events, if your application
already handles ``FINGERMOTION``, ``FINGERDOWN`` and ``FINGERUP`` events.

.. versionadded:: 2.1.3 Added ``precise_x`` and ``precise_y`` to ``MOUSEWHEEL`` events

|

Many new events were introduced in pygame 2.

pygame can recognize text or files dropped in its window. If a file
is dropped, ``DROPFILE`` event will be sent, ``file`` will be its path.
The ``DROPTEXT`` event is only supported on X11.

``MIDIIN`` and ``MIDIOUT`` are events reserved for :mod:`pygame.midi` use.

pygame 2 also supports controller hot-plugging

::

   Event name               Attributes and notes

   DROPFILE                 file
   DROPBEGIN
   DROPCOMPLETE
   DROPTEXT
   MIDIIN
   MIDIOUT
   CONTROLLERDEVICEADDED    device_index
   JOYDEVICEADDED           device_index
   CONTROLLERDEVICEREMOVED  instance_id
   JOYDEVICEREMOVED         instance_id
   CONTROLLERDEVICEREMAPPED instance_id
   KEYMAPCHANGED
   CLIPBOARDUPDATE
   RENDER_TARGETS_RESET
   RENDER_DEVICE_RESET
   LOCALECHANGED

Also in this version, ``instance_id`` attributes were added to joystick events,
and the ``joy`` attribute was deprecated.

``KEYMAPCHANGED`` is a type of an event sent when keymap changes due to a
system event such as an input language or keyboard layout change.

``CLIPBOARDUPDATE`` is an event sent when clipboard changes. This can still
be considered as an experimental feature, some kinds of clipboard changes might
not trigger this event.

``LOCALECHANGED`` is an event sent when user locale changes

.. versionaddedold:: 2.0.0

.. versionadded:: 2.1.3 ``KEYMAPCHANGED``, ``CLIPBOARDUPDATE``,
   ``RENDER_TARGETS_RESET``, ``RENDER_DEVICE_RESET`` and ``LOCALECHANGED``

|

Since pygame 2.0.1, there are a new set of events, called window events.
Here is a list of all window events, along with a short description

::

   Event type                Short description

   WINDOWSHOWN            Window became shown
   WINDOWHIDDEN           Window became hidden
   WINDOWEXPOSED          Window got updated by some external event
   WINDOWMOVED            Window got moved
   WINDOWRESIZED          Window got resized
   WINDOWSIZECHANGED      Window changed its size
   WINDOWMINIMIZED        Window was minimized
   WINDOWMAXIMIZED        Window was maximized
   WINDOWRESTORED         Window was restored
   WINDOWENTER            Mouse entered the window
   WINDOWLEAVE            Mouse left the window
   WINDOWFOCUSGAINED      Window gained focus
   WINDOWFOCUSLOST        Window lost focus
   WINDOWCLOSE            Window was closed
   WINDOWTAKEFOCUS        Window was offered focus
   WINDOWHITTEST          Window has a special hit test
   WINDOWICCPROFCHANGED   Window ICC profile changed (SDL backend >= 2.0.18)
   WINDOWDISPLAYCHANGED   Window moved on a new display (SDL backend >= 2.0.18)


``WINDOWMOVED``, ``WINDOWRESIZED`` and ``WINDOWSIZECHANGED`` have ``x`` and
``y`` attributes, ``WINDOWDISPLAYCHANGED`` has a ``display_index`` attribute.
All windowevents have a ``window`` attribute.

.. versionaddedold:: 2.0.1

.. versionadded:: 2.1.3 ``WINDOWICCPROFCHANGED`` and ``WINDOWDISPLAYCHANGED``

|

On Android, the following events can be generated

::

   Event type                 Short description

   APP_TERMINATING           OS is terminating the application
   APP_LOWMEMORY             OS is low on memory, try to free memory if possible
   APP_WILLENTERBACKGROUND   Application is entering background
   APP_DIDENTERBACKGROUND    Application entered background
   APP_WILLENTERFOREGROUND   Application is entering foreground
   APP_DIDENTERFOREGROUND    Application entered foreground

.. versionadded:: 2.1.3

|

.. function:: pump

   | :sl:`internally process pygame event handlers`
   | :sg:`pump() -> None`

   For each frame of your game, you will need to make some sort of call to the
   event queue. This ensures your program can internally interact with the rest
   of the operating system. If you are not using other event functions in your
   game, you should call ``pygame.event.pump()`` to allow pygame to handle
   internal actions.

   This function is not necessary if your program is consistently processing
   events on the queue through the other :mod:`pygame.event` functions.

   There are important things that must be dealt with internally in the event
   queue. The main window may need to be repainted or respond to the system. If
   you fail to make a call to the event queue for too long, the system may
   decide your program has locked up.

   .. caution::
      This function should only be called in the thread that initialized :mod:`pygame.display`.

   .. ## pygame.event.pump ##

.. function:: get

   | :sl:`get events from the queue`
   | :sg:`get(eventtype=None) -> Eventlist`
   | :sg:`get(eventtype=None, pump=True) -> Eventlist`
   | :sg:`get(eventtype=None, pump=True, exclude=None) -> Eventlist`

   This will get all the messages and remove them from the queue. If a type or
   sequence of types is given only those messages will be removed from the
   queue and returned.

   If a type or sequence of types is passed in the ``exclude`` argument
   instead, then all only *other* messages will be removed from the queue. If
   an ``exclude`` parameter is passed, the ``eventtype`` parameter *must* be
   None.

   If you are only taking specific events from the queue, be aware that the
   queue could eventually fill up with the events you are not interested.

   If ``pump`` is ``True`` (the default), then :func:`pygame.event.pump()` will be called.

   .. versionchangedold:: 1.9.5 Added ``pump`` argument
   .. versionchangedold:: 2.0.2 Added ``exclude`` argument

   .. ## pygame.event.get ##

.. function:: poll

   | :sl:`get a single event from the queue`
   | :sg:`poll() -> Event instance`

   Returns a single event from the queue. If the event queue is empty an event
   of type ``pygame.NOEVENT`` will be returned immediately. The returned event
   is removed from the queue.

   .. caution::
      This function should only be called in the thread that initialized :mod:`pygame.display`.

   .. ## pygame.event.poll ##

.. function:: wait

   | :sl:`wait for a single event from the queue`
   | :sg:`wait() -> Event instance`
   | :sg:`wait(timeout) -> Event instance`

   Returns a single event from the queue. If the queue is empty this function
   will wait until one is created. From pygame 2.0.0, if a ``timeout`` argument
   is given, the function will return an event of type ``pygame.NOEVENT``
   if no events enter the queue in ``timeout`` milliseconds. The event is removed
   from the queue once it has been returned. While the program is waiting it will
   sleep in an idle state. This is important for programs that want to share the
   system with other applications.

   .. versionchangedold:: 2.0.0.dev13 Added ``timeout`` argument

   .. caution::
      This function should only be called in the thread that initialized :mod:`pygame.display`.

   .. ## pygame.event.wait ##

.. function:: peek

   | :sl:`test if event types are waiting on the queue`
   | :sg:`peek(eventtype=None) -> bool`
   | :sg:`peek(eventtype=None, pump=True) -> bool`

   Returns ``True`` if there are any events of the given type waiting on the
   queue. If a sequence of event types is passed, this will return ``True`` if
   any of those events are on the queue.

   When ``eventtype`` is not passed or ``None``, this function will return ``True`` if
   there's any event on the queue, and return ``False`` if the queue is empty.

   If ``pump`` is ``True`` (the default), then :func:`pygame.event.pump()` will be called.

   .. versionchangedold:: 1.9.5 Added ``pump`` argument

   .. versionchanged:: 2.5.3 no longer mistakenly returns an event when ``eventtype`` is None or not passed.

   .. ## pygame.event.peek ##

.. function:: clear

   | :sl:`remove all events from the queue`
   | :sg:`clear(eventtype=None) -> None`
   | :sg:`clear(eventtype=None, pump=True) -> None`

   Removes all events from the queue. If ``eventtype`` is given, removes the given event
   or sequence of events. This has the same effect as :func:`pygame.event.get()` except ``None``
   is returned. It can be slightly more efficient when clearing a full event queue.

   If ``pump`` is ``True`` (the default), then :func:`pygame.event.pump()` will be called.

   .. versionchangedold:: 1.9.5 Added ``pump`` argument

   .. ## pygame.event.clear ##

.. function:: event_name

   | :sl:`get the string name from an event id`
   | :sg:`event_name(type, /) -> string`

   Returns a string representing the name (in CapWords style) of the given
   event type.

   "UserEvent" is returned for all values in the user event id range.
   "Unknown" is returned when the event type does not exist.

   .. ## pygame.event.event_name ##

.. function:: set_blocked

   | :sl:`control which events are blocked on the queue`
   | :sg:`set_blocked(type, /) -> None`
   | :sg:`set_blocked(typelist, /) -> None`
   | :sg:`set_blocked(None) -> None`

   The given event types are not allowed to appear on the event queue. By
   default all events can be placed on the queue. It is safe to disable an
   event type multiple times.

   If ``None`` is passed as the argument, ALL of the event types are blocked
   from being placed on the queue.

   .. ## pygame.event.set_blocked ##

.. function:: set_allowed

   | :sl:`control which events are allowed on the queue`
   | :sg:`set_allowed(type, /) -> None`
   | :sg:`set_allowed(typelist, /) -> None`
   | :sg:`set_allowed(None) -> None`

   The given event types are allowed to appear on the event queue. By default,
   all event types can be placed on the queue. It is safe to enable an event
   type multiple times.

   If ``None`` is passed as the argument, ALL of the event types are allowed
   to be placed on the queue.

   .. ## pygame.event.set_allowed ##

.. function:: get_blocked

   | :sl:`test if a type of event is blocked from the queue`
   | :sg:`get_blocked(type, /) -> bool`
   | :sg:`get_blocked(typelist, /) -> bool`

   Returns ``True`` if the given event type is blocked from the queue. If a
   sequence of event types is passed, this will return ``True`` if any of those
   event types are blocked.

   .. ## pygame.event.get_blocked ##

.. function:: set_grab

   | :sl:`control the sharing of input devices with other applications`
   | :sg:`set_grab(bool, /) -> None`

   When your program runs in a windowed environment, it will share the mouse
   and keyboard devices with other applications that have focus. If your
   program sets the event grab to ``True``, it will lock all input into your
   program.

   It is best to not always grab the input, since it prevents the user from
   doing other things on their system.

   .. ## pygame.event.set_grab ##

.. function:: get_grab

   | :sl:`test if the program is sharing input devices`
   | :sg:`get_grab() -> bool`

   Returns ``True`` when the input events are grabbed for this application.

   .. ## pygame.event.get_grab ##

.. function:: post

   | :sl:`place a new event on the queue`
   | :sg:`post(event, /) -> bool`

   Places the given event at the end of the event queue.

   This is usually used for placing custom events on the event queue.
   Any type of event can be posted, and the events posted can have any attributes.

   When this event is received on the event queue, it will be a shallow copy of
   the event object posted by this function; the dict attribute of both events
   will be a reference to the same dict object in memory. Modifications on one
   dict can affect another, use deepcopy operations on the dict object if you
   don't want this behaviour.

   This returns a boolean on whether the event was posted or not. Blocked events
   cannot be posted, and this function returns ``False`` if you try to post them.

   .. versionchangedold:: 2.0.1 returns a boolean, previously returned ``None``

   .. ## pygame.event.post ##

.. function:: custom_type

   | :sl:`make custom user event type`
   | :sg:`custom_type() -> int`

   Reserves an event slot for use in a custom event ``userevent`` and returns the integer that the event slot is reserved to.

   If too many events are made a :exc:`pygame.error` is raised.

   .. versionaddedold:: 2.0.0.dev3

   .. ## pygame.event.custom_type ##

.. class:: Event

   | :sl:`pygame object for representing events`
   | :sg:`Event(type, dict) -> Event`
   | :sg:`Event(type, \**attributes) -> Event`

   A pygame object used for representing an event. ``Event`` instances
   support attribute assignment and deletion.

   When creating the object, the attributes may come from a dictionary
   argument with string keys or from keyword arguments.

   .. versionchanged:: 2.1.4 This class is also available through the ``pygame.Event``
      alias.

   .. note::
      From version 2.1.3 ``EventType`` is an alias for ``Event``. Beforehand,
      ``Event`` was a function that returned ``EventType`` instances. Use of
      ``Event`` is preferred over ``EventType`` wherever it is possible, as
      the latter could be deprecated in a future version.

   .. attribute:: type

      | :sl:`event type identifier.`
      | :sg:`type -> int`

      Read-only. The event type identifier. For user created event
      objects, this is the ``type`` argument passed to
      :class:`pygame.event.Event()`.

      For example, some predefined event identifiers are ``QUIT`` and
      ``MOUSEMOTION``.

      .. ## pygame.event.Event.type ##

   .. attribute:: __dict__

      | :sl:`event attribute dictionary`
      | :sg:`__dict__ -> dict`

      Read-only. The event type specific attributes of an event. The
      ``dict`` attribute is a synonym for backward compatibility.

      For example, the attributes of a ``KEYDOWN`` event would be ``unicode``,
      ``key``, and ``mod``

      .. ## pygame.event.Event.__dict__ ##

   .. versionaddedold:: 1.9.2 Mutable attributes.

   .. ## pygame.event.Event ##

.. ## pygame.event ##
