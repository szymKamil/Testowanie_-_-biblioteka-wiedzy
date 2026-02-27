# maven

# Maven

Maven jest popularnym narzędziem do automatyzacji budowy projektów Java, które znacznie ułatwia zarządzanie zależnościami, kompilację, testowanie i wdrażanie aplikacji. Oto kluczowe zalety i sens korzystania z Mavena:

1. Automatyzacja procesu budowy
Maven automatyzuje i upraszcza budowę projektów. Dzięki standaryzacji procesu budowy, wystarczy odpowiednio skonfigurować plik pom.xml, aby jednym poleceniem można było skompilować kod, uruchomić testy, spakować artefakty, a nawet wdrożyć aplikację.
2. Zarządzanie zależnościami
Maven pozwala na łatwe zarządzanie zależnościami zewnętrznymi. Wystarczy dodać odpowiednią deklarację zależności w pliku pom.xml, a Maven automatycznie pobierze odpowiednie biblioteki z repozytoriów Maven Central lub innych repozytoriów. To eliminuje konieczność ręcznego dodawania plików JAR do projektu.
3. Standaryzacja struktury projektu
Maven wprowadza standardową strukturę katalogów, co ułatwia pracę zespołową oraz przejrzystość projektu. Dzięki temu osoby pracujące w różnych projektach mogą szybko zrozumieć strukturę kodu, co przyspiesza proces wdrażania nowych osób do zespołu.
4. Łatwość integracji z narzędziami CI/CD
Maven dobrze współpracuje z narzędziami do ciągłej integracji (CI) i wdrażania (CD), takimi jak Jenkins, GitLab CI/CD czy GitHub Actions. Automatyczny proces budowy i testowania umożliwia sprawne wdrażanie zmian, co zwiększa efektywność i jakość oprogramowania.
5. Wsparcie dla różnych środowisk
Maven wspiera profile budowy, które pozwalają na konfigurowanie projektu dla różnych środowisk (np. deweloperskiego, testowego, produkcyjnego). Dzięki temu można łatwo zmieniać ustawienia aplikacji w zależności od środowiska, co przyspiesza i upraszcza proces wdrażania.
6. Wsparcie dla wielu języków programowania
Choć Maven jest najczęściej używany w projektach Java, wspiera również inne języki JVM (np. Groovy, Scala) oraz projekty poza JVM, co czyni go narzędziem elastycznym.
7. Obsługa pluginów
Maven posiada bogaty ekosystem wtyczek, które umożliwiają rozszerzenie jego funkcjonalności. Można dodawać wtyczki do generowania dokumentacji, analizowania jakości kodu, testowania, pakowania aplikacji i wielu innych zadań.
8. Reproducible Builds
Dzięki zarządzaniu zależnościami i automatyzacji procesu budowy Maven pozwala na powtarzalne budowy – ten sam kod i konfiguracja dadzą ten sam wynik, niezależnie od maszyny, na której przeprowadzana jest kompilacja.
9. Wsparcie dla wersjonowania zależności
Maven pozwala na kontrolowanie wersji zależności w projekcie, co minimalizuje ryzyko konfliktów między bibliotekami. Można także ustawić zakres zależności, co pozwala na lepsze zarządzanie ich dostępnością.

# Metody

| **Metoda** | **Opis** |
| --- | --- |
| `mvn validate` | Waliduje projekt – sprawdza, czy wszystkie wymagane informacje o projekcie i zależnościach są dostępne. |
| `mvn compile` | Kompiluje kod źródłowy projektu do kodu bajtowego. |
| `mvn test` | Uruchamia testy jednostkowe, jeśli są skonfigurowane w projekcie. |
| `mvn package` | Pakietuje projekt, tworząc plik JAR lub WAR, gotowy do wdrożenia. |
| `mvn install` | Instaluje spakowany projekt do lokalnego repozytorium Mavena, umożliwiając użycie go jako zależności. |
| `mvn deploy` | Wysyła projekt do zdalnego repozytorium, aby był dostępny dla innych użytkowników i projektów. |
| `mvn clean` | Usuwa wygenerowane pliki z katalogu `target`, umożliwiając “czystą” budowę projektu. |
| `mvn site` | Generuje dokumentację projektu na podstawie konfiguracji zawartej w `pom.xml`. |
| `mvn dependency:resolve` | Pobiera i rozwiązuje wszystkie zależności projektu, pobierając potrzebne pliki z repozytoriów. |
| `mvn dependency:tree` | Wyświetla hierarchię zależności, co jest przydatne do diagnozowania konfliktów wersji. |
| `mvn clean install` | Wykonuje pełny proces: usuwa stare pliki, kompiluje, pakuje i instaluje projekt w repozytorium lokalnym. |
| `mvn help:describe -Dcmd=<command>` | Wyświetla szczegółowe informacje o danym poleceniu Mavena, np. `mvn help:describe -Dcmd=install`. |
| `mvn help:evaluate -Dexpression=<expression>` | Pozwala wyświetlić określone wartości z `pom.xml`, np. nazwę wersji projektu. |