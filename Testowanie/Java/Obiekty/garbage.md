# garbage

# Garbage

Java wykorzystuje technikę garbage collection do usuwania obiektów, które nie są już używane. Tzw. Java Grim Reaper. Śledzi on obiekty i analizuje ich wykorzystanie w kodzie. Gdy wszystkie odołania zostaną usunięte, obiekt zostanie usunięty z pamięci.

“Java Grim Reaper to narzędzie do analizy i monitorowania aplikacji Java, które pomaga w zarządzaniu zasobami, identyfikacji wycieków pamięci oraz analizie wydajności. Umożliwia deweloperom śledzenie wątków, zrozumienie użycia pamięci i diagnozowanie problemów z aplikacjami działającymi w środowisku Java. To narzędzie jest szczególnie przydatne w przypadku złożonych aplikacji, gdzie trudniej jest zidentyfikować problemy bez odpowiednich narzędzi analitycznych.”
Można ręcznie wywołać Grim Reapera poprzez `System.gc()`
Oto kilka rzeczy, które warto wiedzieć o System.gc():

Sugerowanie, a nie wymuszenie: Wywołanie System.gc() jest tylko sugestią, a JVM może zignorować tę prośbę.

Zbieranie śmieci: Jeśli JVM zdecyduje się na przeprowadzenie zbierania śmieci, może to spowodować przerwy w działaniu aplikacji, więc nie jest zalecane wywoływanie tej metody w krytycznych częściach kodu.

Optymalizacja: W większości przypadków lepiej jest polegać na automatycznym zarządzaniu pamięcią, które zapewnia JVM, niż ręcznie wywoływać System.gc().