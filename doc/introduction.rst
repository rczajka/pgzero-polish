Wprowadzenie do Pygame Zero
===========================

.. highlight:: python
    :linenothreshold: 5

Tworzenie okna
--------------

Na początek utwórz pusty plik o nazwie ``intro.py``.

Sprawdź, że to działa i że pojawia się wtedy puste okno, uruchamiając ::

    pgzrun intro.py

Wszystko w Pygame Zero jest opcjonalne; pusty plik to działający skrypt Pygame Zero!

Możesz opuścić grę, klikając przycisk zamykania okna, albo naciskając
``Ctrl-Q`` (``⌘-Q`` na Maku). Jeśli gra z jakiekolwiek powodu przestanie reagować
możesz zakończyć ją, naciskając ``Ctrl-C`` w oknie Terminala.


Malowanie tła
-------------

Następnie, dodajmy funkcję :func:`draw` (*„rysuj”*) i ustawmy wymiary okna. Pygame Zero
będzie wywoływać tę funkcję zawsze, gdy będzie potrzebować narysować zawartość ekranu.

W pliku ``intro.py`` dodaj następującą treść::

    WIDTH = 300
    HEIGHT = 300

    def draw():
        screen.fill((128, 0, 0))

Uruchom ``pgzrun intro.py`` jeszcze raz — na ekranie powinien być czerwonawy kwadrat!

Co robi ten kod?

``WIDTH`` i ``HEIGHT`` kontrolują szerokość i wysokość Twojego okna. Kod
ustawia wielkość okna na 300 pikseli w obu wymiarach.

``screen`` to wbudowana zmienna oznaczająca zawartość okna. Posiada ona
:ref:`zestaw metod do rysowania duszków i kształtów <screen>`. Metoda
``screen.fill()`` wypełnia ekran jednolitym kolorem, określonym
jako zestaw kolorów ``(czerwony, zielony, niebieski)``. ``(128, 0, 0)`` to będzie
ciemno-czerwony. Spróbuj pozmieniać te wartości, używając liczb od 0 do 255,
i zobacz jakie możesz tworzyć kolory. Możesz też użyć jednej z
:doc:`wbudowanych nazw kolorów <colors_ref>`.

Teraz zrobimy sobie duszka, który będziemy mogli poruszać.


Rysowanie duszka
----------------

Zanim cokolwiek narysujemy, potrzebujemy obrazka kosmity, którego będziemy używać.
Możesz kliknąć ten poniżej i zapisać („Zapisz obraz jako…” lub coś podobnego).

.. image:: _static/alien.png

(Ten obrazek ma przezroczyste elementy (tzw. „kanał alfa”), co jest świetne w grach!

Ale najlepiej wygląda na ciemnym tle, więc hełm kosmiczny tego kosmity może być
widoczny dopiero, gdy umieścisz go w grze).

.. tip::

    Mnóstwo duszków do swobodnego użytku, w tym i ten, znajdziesz na `kenney.nl
    <https://kenney.nl/assets?q=2d>`_. Ten pochodzi z zestawu
    `Platformer Art Deluxe pack
    <https://kenney.nl/assets/platformer-art-deluxe>`_.

Trzeba zapisać plik z obrazkiem we właściwym miejscu, tak by Pygame Zero
mógł go znaleźć. Utwórz katalog o nazwie ``images`` i w nim zapisz obrazek jako
``alien.png``. Wszystko to musi być małymi literami. Inaczej Pygame Zero będzie
narzekać, by ostrzec Cię przed możliwymi problemami ze zgodnością z innymi platformami.

Gdy już to zrobisz, Twój projekt powinien wyglądać tak:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    └── intro.py

``images/`` to standardowy katalog, w którym Pygame Zero będzie szukać Twoich obrazków.

Do przedstawienia grafiki, którą będziesz rysował na ekranie, możesz użyć
wbudowanej klasy :class:`Actor`.

Zdefiniujmy teraz takiego aktora. Zmieć zawartość pliku ``intro.py`` na taką::

    alien = Actor('alien')
    alien.pos = 100, 56

    WIDTH = 500
    HEIGHT = alien.height + 20

    def draw():
        screen.clear()
        alien.draw()

Twój kosmita powinien teraz pojawić się na ekranie! Przekazanie napisu ``'alien'``
do klasy ``Actor`` powoduje automatyczne załadowanie obrazka, wraz z atrybutami
takimi jak pozycja i rozmiar. Dzięki temu możemy ustawić ``HEIGHT`` (wysokość okna)
zależnie od wysokości kosmity.

Metoda ``alien.draw()`` rysuje duszka na ekranie w jego aktualnym położeniu.


Poruszanie kosmitą
------------------

Ustawmy kosmitę poza ekranem; zamień linię z ``alien.pos`` na taką::

    alien.topright = 0, 10

Zwróć uwagę, że możesz przypisać wartości do atrybutu ``topright`` (*„góra-prawo”*),
by poruszać kosmicznym aktorem za jego prawy góry róg. Jeśli prawa krawędź kosmity
jest w pozycji ``0``, to znaczy że kosmita znajduje się tuż za lewą krawędzią ekranu.
A teraz niech się porusza. Dodaj poniższy kod na dole pliku::

    def update():
        alien.left += 2
        if alien.left > WIDTH:
            alien.right = 0

Pygame Zero będzie wywoływał Twoją funkcję :func:`update` (*„aktualizuj”*)
przed każdą klatką gry. Poruszanie kosmity o kilka pikseli przy każdej klatce
sprawi, że będzie się on przesuwał po ekranie. Kiedy wysunie się za prawą krawędź
ekranu, ustawiamy go z powrotem po lewej stronie.

Twoje funkcje ``draw()`` i ``update()`` działają podobnie, ale służą do dwóch różnych
celów. Funkcja ``draw()`` (*„rysuj”*) rysuje aktualną pozycję kosmity, podczas gdy
funkcja ``update()`` (*„aktualizuj”*) sprawia że kosmita przesuwa się po ekranie.


Obsługa klików
--------------

Sprawmy, żeby gra robiła coś, gdy klikniemy na kosmitę. W tym celu
potrzebujemy zdefiniować funkcję o nazwie :func:`on_mouse_down` (*„gdy mysz naciśnięta”*).
Dodaj to do kodu źródłowego::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            print("Aj!")
        else:
            print("Pudło!")

Uruchom grę i spróbuj klikać w kosmitę i obok niego.

Pygame Zero wywołuje Twoje funkcje w sprytny sposób. Jeśli nie określisz,
że Twoja funkcja przyjmuje parametr ``pos``, Pygame Zero będzie ją wywoływać
bez pozycji. Istnieje też parametr ``button`` (*„przycisk”*) funkcji ``on_mouse_down``.
Mogliśmy więc napisać::

    def on_mouse_down():
        print("Klik!")

albo::

    def on_mouse_down(pos, button):
        if button == mouse.LEFT and alien.collidepoint(pos):
            print("Aj!")



Dźwięki i obrazy
----------------

Teraz sprawmy, że kosmita będzie wyglądał na urażonego. Zapisz te pliki:

* `alien_hurt.png <_static/alien_hurt.png>`_ - zapisz to jako ``alien_hurt.png``
  w katalogu ``images``.
* `eep.wav <_static/eep.wav>`_ - utwórz katalog o nazwie ``sounds``
  i w nim zapisz to jako ``eep.wav``.

Teraz Twój projekt powinien wyglądać tak:

.. code-block:: none

    .
    ├── images/
    │   └── alien.png
    │   └── alien_hurt.png
    ├── sounds/
    │   └── eep.wav
    └── intro.py

``sounds/`` (*„dźwięki”*) to standardowy katalog, w którym Pygame Zero będzie szukać
Twoich plików dźwiękowych.

Teraz zmieńmy funkcję ``on_mouse_down`` by wykorzystać te nowe pliki::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            alien.image = 'alien_hurt'
            sounds.eep.play()

Teraz, gdy klikniesz kosmitę, powinien wydawać dźwięk, a jego obrazek zmieni się
w niezadowolnego kosmitę.

W tej grze jest jednak problem; kosmita nigdy nie zmienia się z powrotem
w szczęśliwego kosmitę (ale dźwięk słychać przy każdym kliknięciu). Naprawmy to.


Zegar
-----

Jeśli znasz się już trochę z Pythonem poza programowaniem gier, być może
znasz funkcję ``time.sleep()``, która ustawia opóźnienie. Może Cię kusić,
by napisać tego rodzaju kod::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            alien.image = 'alien_hurt'
            sounds.eep.play()
            time.sleep(1)
            alien.image = 'alien'

Niestety, to się w ogóle nie nadaje do użycia w grze. ``time.sleep()``
blokuje całą aktywność — a my chcemy, żeby gra nadal działała i ruszała się.
Musimy pozwolić, by funkcja ``on_mouse_down`` zakończyła się, i sprawić, by gra
wiedziała, kiedy przestawić kosmitę z powrotem, podczas swojego normalnego
działania, gdy cały czas w międzyczasie będzie uruchamiać Twoje funkcje
``draw()`` i ``update()``.

Nie jest to trudne z Pygame Zero, ponieważ ma wbudowany obiekt
:class:`Clock` (*„zegar”*), który pozwala ustawiać funkcje do wywołania
na później.

Najpierw zróbmy „refaktoryzację” (tzn. przeorganizujmy kod). Możemy utworzyć
funkcje, które ustawiają kosmitę jako urażonego i zmieniają go z powrotem
do normalnego stanu::

    def on_mouse_down(pos):
        if alien.collidepoint(pos):
            set_alien_hurt()


    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()


    def set_alien_normal():
        alien.image = 'alien'

To jeszcze nie robi nic inaczej niż dotąd. Funkcja ``set_alien_normal()`` nigdzie nie jest
wywoływana. Zmieńmy ``set_alien_hurt()`` używając zegara, tak by funkcja
``set_alien_normal()`` została wywołana chwilę później. ::

    def set_alien_hurt():
        alien.image = 'alien_hurt'
        sounds.eep.play()
        clock.schedule_unique(set_alien_normal, 0.5)

``clock.schedule_unique()`` sprawi, że ``set_alien_normal()`` będzie wywołane
po upływie pół sekundy. ``schedule_unique()`` (*„zaplanuj bez powtórzeń”*) zapewnia
przy tym, że ta sama funkcja może być w tym samym czasie zaplanowana do wykonania
tylko raz — nawet gdy będziesz klikać bardzo szybko.

Spróbuj — zobaczysz jak kosmita wraca do stanu normalnego po upływie pół sekundy.
Spróbuj klikać bardzo szybko i zweryfikuj, że kosmita wraca po normalnej postaci
dopiero pół sekundy po ostatnim kliknięciu.

``clock.schedule_unique()`` akceptuje zarówno liczby całkowite, jak i ułamkowe,
jako określenie długości czasu. W tym wprowadzeniu pokazujemy przykład użycia
z liczbą ułamkową, ale śmiało popróbuj różnych liczb, by zobaczyć różne efekty
wywoływane przez różne wartości.


Podsumowanie
------------

Zobaczyliśmy jak ładować i rysować duszki, grać dźwięki, obsługiwać
zdarzenia wejścia, i używać wbudowanego zegara.

Być może chcesz rozbudować tę grę, dodając do niej jakąś punktację, albo
tak by kosmita poruszał się w bardziej skomplikowany sposób.

Istnieje znacznie więcej wbudowanych funkcji, ułatwiających używanie Pygame Zero.
Przejrzyj :doc:`wbudowane obiekty <builtins>` by nauczyć się używać reszty API.
