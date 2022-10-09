Haki zdarzeń
============

Pygame Zero automatycznie wyłapie i będzie wywoływać zdefiniowane przez Ciebie haki zdarzeń.
To podejście oszczędza Ci potrzeby implementowania maszynerii pętli zdarzeń samemu.

Haki pętli gry
--------------

Typowa pętla gry wygląda mniej więcej tak::

    while gra_nie_skończona():
        przetwarzaj_wejście()
        aktualizuj()
        rysuj()

Przetwarzanie wejścia jest trochę bardziej skomplikowane, ale Pygame Zero pozwala Ci
łatwo zdefiniować funkcje ``update()`` (*„aktualizuj”*) i ``draw()`` (*„rysuj”*)
w swoim module gry.

.. function:: draw()

    Wywoływana przez Pygame Zero kiedy potrzebuje odrysować zawartość okna gry.

    ``draw()`` nie może przyjmować żadnych argumentów.

    Pygame Zero próbuje ustalić, kiedy trzeba odrysować ekran gry, żeby uniknąć
    rysowania jeśli nic się nie zmieniło. W każdym kroku pętli gry ekran zostanie
    odrysowany w następujących sytuacjach:

    * Gdy masz zdefiniowaną funkcję ``update()`` (zobacz niżej).
    * Gdy zegar uruchomi jakieś zdarzenie.
    * Gdy nastąpi zdarzenie wejścia.

    Możesz się na to naciąć, jeśli spróbujesz zmienić stan gry albo
    uruchomić jakąś animację wewnątrz funkcji ``draw``. Na przykład, ten
    kod jest błędny: nie ma gwarancji, że kosmita będzie się przesuwał
    po ekranie::

        def draw():
            alien.left += 1
            alien.draw()

    Prawidłowy kod używa ``update()`` by zmieniać albo animować obiekty,
    a ``draw`` tylko do rysowania zawartości ekranu::

        def draw():
            alien.draw()

        def update():
            alien.left += 1

.. function:: update() albo update(dt)

    Wywoływane przez Pygame Zero jako krok w logice Twojej gry. Ta funkcja będzie
    wywoływana w kółko, 60 razy an sekundę.

    Są dwa podejścia do pisania funkcji ``update``.

    W prostych grach możesz przyjąć, że pomiędzy wywołaniami ``update`` upływa
    bardzo krótki czas (ułamek sekundy). Może nawet nie interesować Cię, jaki
    to jest czas: możesz po prostu przesuwać obiekty o określoną liczbę pikseli co
    klatkę (albo przyspieszać je w określonym tempie, itd.).

    Bardziej zaawansowanym podejściem jest bazowanie ruchu i obliczeń fizycznych
    na odstępie czasu który faktycznie upłynął pomiędzy wywołaniami. To może
    dać płynniejszą animację, ale obliczenia robią się przez to trudniejsze,
    i musisz bardziej uważać, by unikać nieprzewidywalnych zachowań gdy
    odtępy czasu stają się większe.

    Aby użyć podejścia opartego na czasie, możesz zmienić funkcję ``update``
    tak by przyjmowała jeden parametr. Jeśli Twoja funkcja ``update`` przyjmuje
    argument, Pygame Zero przekaże do niej upływ czasu w sekundach. Możesz go użyć
    w swoich obliczeniach ruchu.


Haki obsługi zdarzeń
--------------------

Podobnie do haków pętli gry, Twój program w Pygame Zero może reagować na zdarzenia
wejścia, definiując funkcje o specjalnych nazwach.

Trochę podobnie jak w przypadku ``update()``, Pygame Zero przyjrzy się Twoim funkcjom
obsługi zdarzeń, by dowiedzieć się, jak je wywoływać. Nie muszą więc one przyjmować
argumentów. Na przykład, Pygame Zero bez problemu wywoła każdą z tych wersji funkcji
``on_mouse_down``::

    def on_mouse_down():
        print("Kliknięto przcisk myszy")

    def on_mouse_down(pos):
        print("Kliknięto przycisk myszy w punkcie", pos)

    def on_mouse_down(button):
        print("Kliknięto przycisk myszy", button)

    def on_mouse_down(pos, button):
        print("Kliknięto przycisk myszy", button, "w punkcie", pos)

Robi to, sprawdzając nazwy parametrów, dlatego muszą być one nazwane dokładnie
tak jak powyżej. Każde zdarzenie ma inny zestaw parametrów którego możesz użyć,
zgodnie z poniższym opisem.

.. function:: on_mouse_down([pos], [button])

    Wywoływane po wciśnięciu przycisku myszy.

    :param pos: Zestaw (x, y) wskazujący lokalizację wskaźnika myszy w momencie
                wciśnięcia przycisku.
    :param button: Jedna z wartości wyliczenia :class:`mouse`, wskazująca,
                   który przycisk został wciśnięty.

.. function:: on_mouse_up([pos], [button])

    Wywoływane po puszczeniu przycisku myszy.

    :param pos: Zestaw (x, y) wskazujący lokalizację wskaźnika myszy w momencie
                puszczenia przycisku.
    :param button: Jedna z wartości wyliczenia :class:`mouse`, wskazująca,
                   który przycisk został puszczony.

.. function:: on_mouse_move([pos], [rel], [buttons])

    Wywoływane podczas ruchu myszy.

    :param pos: Zestaw (x, y) wskazujący lokalizację, do której został przesunięty
                wskaźnik myszy.
    :param rel: Zestaw (delta_x, delta_y) reprezentujący zmianę pozycji
                wskaźnika myszy.
    :param buttons: Zestaw wartości wyliczenia :class:`mouse`, wskazujący przyciski
                    wciśnięte podczas ruchu.


Aby obsłużyć przeciąganie myszą, użyj kodu takiego jak ten::

    def on_mouse_move(rel, buttons):
        if mouse.LEFT in buttons:
            # nastąpiło przeciąganie myszą, zrób coś z `rel`
            ...


.. function:: on_key_down([key], [mod], [unicode])

    Wywoływane po wciśnięciu klawisza.

    :param key: Liczba oznaczająca wciśnięty klawisz (zob.
                :ref:`niżej <buttons-and-keys>`).
    :param unicode: Tam gdzie to stosowne — znak który został wpisany. Nie wszystkie
                    klawisze dają w rezultacie widoczne znaki — wiele może być
                    znakami kontrolnymi. W przypadku, gdy klawisz nie odpowiada
                    znakowi Unicode, ten parametr będzie pustym napisem.
    :param mod: Mapa bitowa wciśniętych klawiszy modyfikujących.

.. function:: on_key_up([key], [mod])

    Wywoływane po puszczeniu klawisza.

    :param key: Liczba oznaczająca puszczony klawisz (zob.
                :ref:`niżej <buttons-and-keys>`).
    :param mod: Mapa bitowa wciśniętych klawiszy modyfikujących.


.. function:: on_music_end()

    Wywoływane po zakończeniu :ref:`ścieżki z muzyką <music>`.

    Zwróć uwagę, że ta funkcja nie będzie wywołana, jeśli muzyka jest ustawiona
    jako zapętlona.


.. _buttons-and-keys:

Przyciski i klawisze
''''''''''''''''''''

Wbudowane obiekty ``mouse`` (*„mysz”*) i ``keys`` (*„klawisze”*) służą do ustalania,
które przyciski lub klawisze były wciśnięte podczas powyższych zdarzeń.

Zwróć uwagę, że zdarzenia kręcenia kółkiem myszy pojawiają się jako wciśnięcia przycisku
zdefiniowanych niżej stałymi ``WHEEL_UP`` (*„kółko w górę”*)
i ``WHEEL_DOWN`` (*„kółko w dół”*).

.. class:: mouse

    Wbudowane wyliczenie przycisków, które można odbierać funkcjami obsługi
    ``on_mouse_*``.

    .. attribute:: LEFT
    .. attribute:: MIDDLE
    .. attribute:: RIGHT
    .. attribute:: WHEEL_UP
    .. attribute:: WHEEL_DOWN

.. class:: keys

    Wbudowane wyliczenie klawiszy, które można odbierać funkcjami obsługi ``on_key_*``.

    .. attribute:: BACKSPACE
    .. attribute:: TAB
    .. attribute:: CLEAR
    .. attribute:: RETURN
    .. attribute:: PAUSE
    .. attribute:: ESCAPE
    .. attribute:: SPACE
    .. attribute:: EXCLAIM
    .. attribute:: QUOTEDBL
    .. attribute:: HASH
    .. attribute:: DOLLAR
    .. attribute:: AMPERSAND
    .. attribute:: QUOTE
    .. attribute:: LEFTPAREN
    .. attribute:: RIGHTPAREN
    .. attribute:: ASTERISK
    .. attribute:: PLUS
    .. attribute:: COMMA
    .. attribute:: MINUS
    .. attribute:: PERIOD
    .. attribute:: SLASH
    .. attribute:: K_0
    .. attribute:: K_1
    .. attribute:: K_2
    .. attribute:: K_3
    .. attribute:: K_4
    .. attribute:: K_5
    .. attribute:: K_6
    .. attribute:: K_7
    .. attribute:: K_8
    .. attribute:: K_9
    .. attribute:: COLON
    .. attribute:: SEMICOLON
    .. attribute:: LESS
    .. attribute:: EQUALS
    .. attribute:: GREATER
    .. attribute:: QUESTION
    .. attribute:: AT
    .. attribute:: LEFTBRACKET
    .. attribute:: BACKSLASH
    .. attribute:: RIGHTBRACKET
    .. attribute:: CARET
    .. attribute:: UNDERSCORE
    .. attribute:: BACKQUOTE
    .. attribute:: A
    .. attribute:: B
    .. attribute:: C
    .. attribute:: D
    .. attribute:: E
    .. attribute:: F
    .. attribute:: G
    .. attribute:: H
    .. attribute:: I
    .. attribute:: J
    .. attribute:: K
    .. attribute:: L
    .. attribute:: M
    .. attribute:: N
    .. attribute:: O
    .. attribute:: P
    .. attribute:: Q
    .. attribute:: R
    .. attribute:: S
    .. attribute:: T
    .. attribute:: U
    .. attribute:: V
    .. attribute:: W
    .. attribute:: X
    .. attribute:: Y
    .. attribute:: Z
    .. attribute:: DELETE
    .. attribute:: KP0
    .. attribute:: KP1
    .. attribute:: KP2
    .. attribute:: KP3
    .. attribute:: KP4
    .. attribute:: KP5
    .. attribute:: KP6
    .. attribute:: KP7
    .. attribute:: KP8
    .. attribute:: KP9
    .. attribute:: KP_PERIOD
    .. attribute:: KP_DIVIDE
    .. attribute:: KP_MULTIPLY
    .. attribute:: KP_MINUS
    .. attribute:: KP_PLUS
    .. attribute:: KP_ENTER
    .. attribute:: KP_EQUALS
    .. attribute:: UP
    .. attribute:: DOWN
    .. attribute:: RIGHT
    .. attribute:: LEFT
    .. attribute:: INSERT
    .. attribute:: HOME
    .. attribute:: END
    .. attribute:: PAGEUP
    .. attribute:: PAGEDOWN
    .. attribute:: F1
    .. attribute:: F2
    .. attribute:: F3
    .. attribute:: F4
    .. attribute:: F5
    .. attribute:: F6
    .. attribute:: F7
    .. attribute:: F8
    .. attribute:: F9
    .. attribute:: F10
    .. attribute:: F11
    .. attribute:: F12
    .. attribute:: F13
    .. attribute:: F14
    .. attribute:: F15
    .. attribute:: NUMLOCK
    .. attribute:: CAPSLOCK
    .. attribute:: SCROLLOCK
    .. attribute:: RSHIFT
    .. attribute:: LSHIFT
    .. attribute:: RCTRL
    .. attribute:: LCTRL
    .. attribute:: RALT
    .. attribute:: LALT
    .. attribute:: RMETA
    .. attribute:: LMETA
    .. attribute:: LSUPER
    .. attribute:: RSUPER
    .. attribute:: MODE
    .. attribute:: HELP
    .. attribute:: PRINT
    .. attribute:: SYSREQ
    .. attribute:: BREAK
    .. attribute:: MENU
    .. attribute:: POWER
    .. attribute:: EURO
    .. attribute:: LAST

Dodatkowo, masz dostęp do zestawu stałych reprezentujących klawisze modyfikujące:

.. class:: keymods

    Stałe reprezentujące klawisze modyfikujące, które mogą być wciśnięte
    podczas zdarzeń ``on_key_up``/``on_key_down``.

    .. attribute:: LSHIFT
    .. attribute:: RSHIFT
    .. attribute:: SHIFT
    .. attribute:: LCTRL
    .. attribute:: RCTRL
    .. attribute:: CTRL
    .. attribute:: LALT
    .. attribute:: RALT
    .. attribute:: ALT
    .. attribute:: LMETA
    .. attribute:: RMETA
    .. attribute:: META
    .. attribute:: NUM
    .. attribute:: CAPS
    .. attribute:: MODE
