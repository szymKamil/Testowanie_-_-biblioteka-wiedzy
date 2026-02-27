# Big O

Big O to notacja używana do analizy wydajności algorytmów, w tym także w Javie. Określa, jak zmienia się czas wykonania (lub zajętość pamięci) algorytmu w zależności od rozmiaru wejścia, zwłaszcza dla dużych danych. W skrócie, Big O mierzy, jak bardzo czas wykonania rośnie wraz ze wzrostem liczby elementów wejściowych.

Jak Big O jest używane w Javie?
Podczas pisania kodu w Javie analizujemy złożoność algorytmów i struktur danych, by ocenić ich efektywność. Przykłady złożoności:

O(1) – Złożoność stała: operacja zajmuje stałą ilość czasu niezależnie od rozmiaru wejścia. Przykład: dostęp do elementu w tablicy na podstawie indeksu.
O(n) – Złożoność liniowa: czas wykonania rośnie proporcjonalnie do liczby elementów. Przykład: iteracja przez listę.
O(log n) – Złożoność logarytmiczna: czas wykonania rośnie logarytmicznie w stosunku do liczby elementów. Przykład: wyszukiwanie binarne.
O(n^2) – Złożoność kwadratowa: czas wykonania rośnie kwadratowo wraz ze wzrostem liczby elementów. Przykład: sortowanie przez wstawianie lub sortowanie bąbelkowe.
Przykład
W przypadku klasy ArrayList w Javie:

Dostęp do elementu ma złożoność O(1), ponieważ elementy są przechowywane w tablicy.
Usuwanie elementu ma złożoność O(n), ponieważ usunięcie może wymagać przesunięcia pozostałych elementów.
Big O pomaga w wyborze odpowiednich struktur danych i algorytmów dla określonego problemu, aby osiągnąć najlepszą wydajność w Javie.