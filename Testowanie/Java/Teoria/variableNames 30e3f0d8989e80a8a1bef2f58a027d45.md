# variableNames

# Konwencja nazewnictwa

| Typ Identyfikatora | Zasady Nazewnictwa | Przykłady |
| --- | --- | --- |
| **Klasa** | - Nazwa powinna zaczynać się od wielkiej litery.  - Powinna być rzeczownikiem, takim jak `Color`, `Button`, `System`, `Thread`.  - Należy używać odpowiednich słów zamiast akronimów. | `java<br>public class Employee<br>{<br>// fragment kodu<br>}<br>` |
| **Interfejs** | - Nazwa powinna zaczynać się od wielkiej litery.  - Powinna być przymiotnikiem, takim jak `Runnable`, `Remote`, `ActionListener`.  - Należy używać odpowiednich słów zamiast akronimów. | `java<br>interface Printable<br>{<br>// fragment kodu<br>}<br>` |
| **Metoda** | - Nazwa powinna zaczynać się od małej litery.  - Powinna być czasownikiem, takim jak `main()`, `print()`, `println()`.  - Jeśli nazwa zawiera wiele słów, każde kolejne słowo powinno zaczynać się od wielkiej litery, np. `actionPerformed()`. | `java<br>class Employee<br>{<br>// metoda<br>void draw()<br>{<br>// fragment kodu<br>}<br>}<br>` |
| **Zmienna** | - Nazwa powinna zaczynać się od małej litery, np. `id`, `name`.  - Nie powinna zaczynać się od specjalnych znaków, takich jak `&`, `$`, `_`.  - Jeśli nazwa zawiera wiele słów, każde kolejne słowo powinno zaczynać się od wielkiej litery, np. `firstName`, `lastName`.  - Unikaj jednowyrazowych nazw, takich jak `x`, `y`, `z`. | `java<br>class Employee<br>{<br>// zmienna<br>int id;<br>// fragment kodu<br>}<br>` |
| **Pakiet** | - Nazwa powinna być zapisana małymi literami, np. `java`, `lang`.  - Jeśli nazwa zawiera wiele słów, powinna być oddzielona kropkami, np. `java.util`, `java.lang`. | `java<br>// pakiet<br>package com.javatpoint;<br>class Employee<br>{<br>// fragment kodu<br>}<br>` |
| **Stała** | - Nazwa powinna być zapisana wielkimi literami, np. `RED`, `YELLOW`.  - Jeśli nazwa zawiera wiele słów, powinna być oddzielona podkreśleniem `_`, np. `MAX_PRIORITY`.  - Może zawierać cyfry, ale nie na początku nazwy. | `java<br>class Employee<br>{<br>// stała<br>static final int MIN_AGE = 18;<br>// fragment kodu<br>}<br>` |