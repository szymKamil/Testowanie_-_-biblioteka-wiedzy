# IOfilesWork

# Files

Czym są IO, NIO i NIO.2 w Javie?

| Technologia | Wersja Javy | Pełna nazwa | Opis skrótowy |
| --- | --- | --- | --- |
| IO | Java 1.0 | Input/Output | Tradycyjne, blokujące I/O |
| NIO | Java 1.4 | New Input/Output | Buforowane, nieblokujące I/O |
| NIO.2 | Java 7 | New Input/Output 2 | Rozszerzenie NIO – obsługa plików, systemu plików, kanałów asynchronicznych |

Podejście: Strumieniowe, blokujące – operacja (np. odczyt pliku) blokuje wątek, dopóki się nie zakończy.
API: InputStream, OutputStream, Reader, Writer
Zalety: Prosty model programowania.
Wady: Mało wydajne przy dużej ilości danych lub wielu połączeniach (np. serwery sieciowe).

```java
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
String line = reader.readLine();
```

NIO (New IO)
Podejście: Buforowane, możliwość nieblokujących operacji.
API: ByteBuffer, CharBuffer, FileChannel, Selector, SocketChannel
Zalety: Lepsze dla dużych aplikacji sieciowych – obsługuje wiele kanałów jednym wątkiem.
Wady: Trudniejsze w implementacji, bardziej niskopoziomowe.

```java
ByteBuffer buffer = ByteBuffer.allocate(1024);
FileChannel channel = FileChannel.open(Paths.get("file.txt"), StandardOpenOption.READ);
channel.read(buffer);
```

NIO.2 (Java 7+)
Path, Files – nowoczesna obsługa plików.
WatchService – nasłuchiwanie zmian w systemie plików.
AsynchronousFileChannel, AsynchronousSocketChannel – asynchroniczne I/O.
Zalety: Wygodniejsze API do pracy z plikami i folderami, lepsza skalowalność dla aplikacji asynchronicznych.

```java
Path path = Paths.get("file.txt");
List<String> lines = Files.readAllLines(path);
```

| Cecha | IO | NIO | NIO.2 |
| --- | --- | --- | --- |
| Typ operacji | Blokujące | Buforowane, potencjalnie nieblokujące | Buforowane + asynchroniczne |
| API | Strumienie | Kanały, Bufory | Path, Files, kanały async |
| Obsługa plików | Tak, ale stara | Podstawowa (`FileChannel`) | Nowoczesna i bogata (`Files`) |
| Sieć (Sockets) | Blokujące gniazda | Nieblokujące `SocketChannel` | Asynchroniczne kanały |
| Reaktywność / Skalowalność | Słaba | Dobra | Bardzo dobra |

## Exeptions IOExeption

2 Metody:

| Cecha | LBYL | EAFP |
| --- | --- | --- |
| Styl | Proaktywny | Reaktywny |
| Czytelność | Lepsza w prostych przypadkach | Lepsza, gdy ryzyko jest niskie |
| Wydajność | Może być bardziej kosztowny | Może być szybszy (mniej warunków) |
| Przykład użycia | Sprawdzanie `null`, zakresów | Praca na plikach, parsowanie liczb |

LBYL:

```java
if (str != null) {
    System.out.println(str.length());
} else {
    System.out.println("String jest nullem!");
}
```

Zastosowanie:
Używane tam, gdzie łatwo i tanio jest sprawdzić warunek przed wykonaniem operacji.
Czytelniejsze, gdy możliwy wyjątek jest rzadki i łatwy do przewidzenia.

EAFP:

```java
try {
    System.out.println(str.length());
} catch (NullPointerException e) {
    System.out.println("String był nullem!");
}
```

Zastosowanie:
Używane, gdy trudno lub nieefektywnie jest wcześniej sprawdzić wszystkie warunki.
Typowe w programowaniu zorientowanym na wyjątki (np. operacje I/O, parsowanie).

# Files

Java oferuje bogaty zestaw klas i interfejsów do obsługi plików i katalogów. Poniższe notatki przedstawiają kompleksowe omówienie najważniejszych aspektów pracy z plikami w języku Java, od podstawowych operacji do zaawansowanych technik.

## File

Klasa `File` z pakietu `java.io` stanowi abstrakcyjną reprezentację plików i katalogów w systemie plików.

### Główne cechy klasy File

- Reprezentuje ścieżkę do pliku lub katalogu w abstrakcyjny sposób[^11]
- Umożliwia wykonywanie operacji na plikach i katalogach bez dostępu do ich zawartości[^13]
- Dostępna od Java 1.0 (1996) i nadal szeroko używana[^4]

### Konstruktory klasy File

```java
File(String pathname)                    // Tworzy obiekt File na podstawie ścieżki
File(String parent, String child)        // Tworzy obiekt File z katalogu nadrzędnego i podrzędnego
File(File parent, String child)          // Tworzy obiekt File z obiektu File i podkatalogu
File(URI uri)                            // Tworzy obiekt File z URI
```

### Najważniejsze metody klasy File

- **Sprawdzanie właściwości:**
    - `boolean exists()` - sprawdza, czy plik/katalog istnieje[^17]
    - `boolean isFile()` - sprawdza, czy jest to plik[^17]
    - `boolean isDirectory()` - sprawdza, czy jest to katalog[^17]
    - `boolean canRead()` - sprawdza, czy plik jest czytelny[^17]
    - `boolean canWrite()` - sprawdza, czy plik jest zapisywalny[^17]
    - `long length()` - zwraca rozmiar pliku w bajtach[^17]
- **Pobieranie informacji:**
    - `String getName()` - zwraca nazwę pliku/katalogu[^17]
    - `String getAbsolutePath()` - zwraca bezwzględną ścieżkę[^17]
    - `String getPath()` - zwraca ścieżkę pliku[^17]
    - `String[] list()` - zwraca tablicę plików w katalogu[^17]
- **Operacje na plikach:**
    - `boolean createNewFile()` - tworzy nowy pusty plik[^17][^11]
    - `boolean delete()` - usuwa plik/katalog[^17]
    - `boolean mkdir()` - tworzy katalog[^17]
    - `boolean mkdirs()` - tworzy katalog wraz z nieistniejącymi katalogami nadrzędnymi[^17]

### Przykład użycia klasy File

```java
import java.io.File;
import java.io.IOException;

public class FileExample {
    public static void main(String[] args) {
        try {
            File file = new File("plik.txt");
            if (file.exists()) {
                System.out.println("Plik istnieje: " + file.getName());
            } else {
                if (file.createNewFile()) {
                    System.out.println("Utworzono nowy plik");
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Path

Interfejs `Path` z pakietu `java.nio.file` został wprowadzony w Java 7 jako nowoczesna alternatywa dla klasy `File`.

### Główne cechy interfejsu Path

- Reprezentuje ścieżkę w systemie plików, wskazującą na plik lub katalog[^2]
- Ścieżka może być bezwzględna (zawiera pełną ścieżkę od korzenia) lub względna[^2]
- Oferuje bardziej szczegółowe komunikaty o błędach niż klasa File[^4]
- Umożliwia lepszą obsługę linków symbolicznych i metadanych plików[^4]

### Tworzenie obiektu Path

Obiekty `Path` tworzy się za pomocą statycznej metody `Paths.get()`:

```java
import java.nio.file.Path;
import java.nio.file.Paths;

Path path1 = Paths.get("C:\\dane\\plik.txt");               // Dla Windows
Path path2 = Paths.get("/home/user/plik.txt");              // Dla Unix/Linux
Path path3 = Paths.get("C:", "dane", "plik.txt");           // Z oddzielnych elementów
```

### Operacje na obiektach Path

- Uzyskiwanie informacji o ścieżce (nazwa pliku, katalog nadrzędny)
- Normalizacja ścieżek
- Łączenie ścieżek
- Rozwiązywanie ścieżek względnych
- Konwersja do/z obiektów File[^2]

## Files

Klasa `Files` z pakietu `java.nio.file` zawiera statyczne metody do operacji na plikach i katalogach, pracujące głównie z obiektami `Path`.

### Najważniejsze metody klasy Files

- **Tworzenie:**
    - `Path createFile(Path path, FileAttribute<?>... attrs)` - tworzy nowy pusty plik[^3]
    - `Path createDirectory(Path dir, FileAttribute<?>... attrs)` - tworzy nowy katalog[^3]
    - `Path createDirectories(Path dir, FileAttribute<?>... attrs)` - tworzy katalogi wraz z nieistniejącymi katalogami nadrzędnymi[^3]
- **Kopiowanie i przenoszenie:**
    - `Path copy(Path source, Path target, CopyOption... options)` - kopiuje plik[^3]
    - `long copy(InputStream in, Path target, CopyOption... options)` - kopiuje strumień do pliku[^3]
    - `long copy(Path source, OutputStream out)` - kopiuje plik do strumienia[^3]
- **Odczyt i zapis:**
    - `byte[] readAllBytes(Path path)` - odczytuje wszystkie bajty z pliku
    - `List<String> readAllLines(Path path, Charset cs)` - odczytuje wszystkie linie z pliku
    - `Path write(Path path, byte[] bytes, OpenOption... options)` - zapisuje bajty do pliku
- **Usuwanie:**
    - `boolean deleteIfExists(Path path)` - usuwa plik, jeśli istnieje

### Przykład użycia klasy Files

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.io.IOException;

public class FilesExample {
    public static void main(String[] args) {
        try {
            Path path = Paths.get("plik.txt");
            if (Files.exists(path)) {
                String content = Files.readString(path);
                System.out.println(content);
            } else {
                Files.writeString(path, "Przykładowa zawartość pliku");
                System.out.println("Utworzono nowy plik");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Walk File Tree

Java oferuje kilka sposobów przechodzenia przez drzewo katalogów, z których każdy ma swoje specyficzne zastosowania.

### Files.walkFileTree()

Metoda `walkFileTree()` implementuje wzorzec projektowy Visitor, umożliwiając przechodzenie przez drzewo plików i katalogów[^15]:

```java
public static Path walkFileTree(Path start, FileVisitor<? super Path> visitor) throws IOException
public static Path walkFileTree(Path start, Set<FileVisitOption> options, int maxDepth,
                                FileVisitor<? super Path> visitor) throws IOException
```

- Przechodzi przez drzewo plików, zaczynając od określonego katalogu[^15]
- Wywołuje metody obiektu `FileVisitor` dla każdego napotkanego pliku[^15]
- Można określić maksymalną głębokość przeszukiwania i opcje odwiedzania[^15]

### Files.list() i Files.walk()

- `Files.list(Path dir)` - zwraca strumień plików tylko z pierwszego poziomu katalogu[^6]
- `Files.walk(Path start, int maxDepth)` - przechodzi przez drzewo plików do określonej głębokości[^6]

### Różnice między metodami

- `list(dir)` zwraca strumień plików tylko z danego katalogu[^6]
- `walk(dir)` i `walkFileTree(dir)` przechodzą przez całe drzewo, włączając katalog początkowy[^6]
- `walkFileTree` używa interfejsu `FileVisitor`, natomiast `walk` zwraca `Stream<Path>`[^6]

### Przykład użycia Files.walkFileTree()

```java
Files.walkFileTree(directory, new SimpleFileVisitor<Path>() {
    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
        System.out.println("Plik: " + file.getFileName());
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) throws IOException {
        System.out.println("Katalog: " + dir.getFileName());
        return FileVisitResult.CONTINUE;
    }
});
```

## Odczyt z dysku

Java oferuje różne sposoby odczytu danych z plików na dysku, każdy z własnymi zaletami i ograniczeniami.

### Metody z klasy Files (Java NIO)

- `byte[] readAllBytes(Path path)` - odczytuje wszystkie bajty z pliku
- `List<String> readAllLines(Path path, Charset cs)` - odczytuje wszystkie linie z pliku
- `BufferedReader newBufferedReader(Path path, Charset cs)` - tworzy buforowany czytnik

### RandomAccessFile

Klasa `RandomAccessFile` umożliwia losowy dostęp do plików:

- Pozwala na odczyt i zapis danych w dowolnym miejscu pliku[^5][^14]
- Pracuje z plikiem jako z dużą tablicą bajtów i kursorem do poruszania się po pliku[^5]
- Oferuje tryby dostępu: “r” (tylko odczyt), “rw” (odczyt i zapis), “rwd” i “rws” (synchronizowany zapis)[^14]

```java
RandomAccessFile file = new RandomAccessFile("plik.txt", "rw");
file.seek(5);    // Przejście do 5. bajtu
byte[] buffer = new byte[^10];
file.read(buffer);
file.close();
```

## Input Stream

Klasa `InputStream` to abstrakcyjna klasa bazowa dla wszystkich strumieni wejściowych w Java IO.

### Główne cechy klasy InputStream

- Reprezentuje uporządkowany strumień bajtów[^8]
- Jest klasą abstrakcyjną, która musi być rozszerzona przez konkretne implementacje[^16]
- Oferuje metody do odczytu danych, zamykania strumienia i obsługi znaczników[^16]

### Podklasy InputStream

- `FileInputStream` - do odczytu bajtów z plików
- `ByteArrayInputStream` - do odczytu bajtów z tablicy bajtów
- `BufferedInputStream` - buforowany strumień dla lepszej wydajności
- `DataInputStream` - do odczytu typów prostych Java
- `ObjectInputStream` - do odczytu obiektów Java

### Podstawowe metody InputStream

- `int read()` - odczytuje pojedynczy bajt (zwraca -1 na końcu strumienia)[^16]
- `int read(byte[] b)` - odczytuje bajty do tablicy[^16]
- `void close()` - zamyka strumień[^16]
- `long skip(long n)` - pomija określoną liczbę bajtów[^16]

### Przykład użycia FileInputStream

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class InputStreamExample {
    public static void main(String[] args) {
        try (InputStream inputStream = new FileInputStream("plik.txt")) {
            int data = inputStream.read();
            while (data != -1) {
                System.out.print((char) data);
                data = inputStream.read();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## FileReader vs BufferedReader

Porównanie dwóch popularnych klas do odczytu plików tekstowych w Java.

### FileReader

- Odczytuje znaki bezpośrednio z pliku[^9]
- Nie jest buforowany, co oznacza, że każde wywołanie `read()` powoduje operację I/O[^9]
- Odczytuje za każdym razem 2 bajty (16 bitów)[^9]
- Wolniejszy w porównaniu do BufferedReader[^9]
- Nie obsługuje metod `mark()` i `reset()`[^9]

### BufferedReader

- Odczytuje znaki z innego czytnika (np. FileReader)[^9]
- Jest buforowany - wczytuje większe porcje danych do bufora wewnętrznego[^9]
- Gdy `read()` jest wywoływane, dane są głównie odczytywane z bufora, a nie z pliku[^9]
- Znacznie szybszy niż FileReader dzięki zmniejszeniu liczby operacji I/O[^9]
- Obsługuje metody `mark()` i `reset()`[^9]
- Udostępnia wygodną metodę `readLine()` do odczytu całych linii tekstu

### Przykład porównawczy

```java
// Użycie FileReader
try (FileReader fr = new FileReader("plik.txt")) {
    int znak;
    while ((znak = fr.read()) != -1) {
        System.out.print((char) znak);
    }
}

// Użycie BufferedReader
try (BufferedReader br = new BufferedReader(new FileReader("plik.txt"))) {
    String linia;
    while ((linia = br.readLine()) != null) {
        System.out.println(linia);
    }
}
```

## Character Set i Character Encodings

Zestawy znaków i kodowania są kluczowe dla właściwej obsługi tekstu w różnych językach.

### Character Set (Zestaw znaków)

- Zbiór znaków, które można używać w danym kontekście[^10]
- Java obsługuje wiele zestawów znaków, m.in. US-ASCII, ISO-8859-1, UTF-8, UTF-16[^10]
- JVM określa domyślny zestaw znaków podczas uruchamiania[^10]
- Domyślny zestaw znaków zależy od lokalizacji i systemu operacyjnego[^10]

### Character Encodings (Kodowania znaków)

- Sposób reprezentacji znaków w postaci bajtów[^10]
- Różne kodowania obsługują różne zestawy znaków
- Od Java 18 domyślnym kodowaniem jest UTF-8[^10]
- W starszych wersjach Java można wymusić UTF-8 za pomocą parametru `Dfile.encoding=UTF-8`[^10]

### UTF-8

- Najbardziej rozpowszechnione kodowanie na świecie[^10]
- Kodowanie o zmiennej długości (1-4 bajty na znak)
- Obsługuje wszystkie znaki Unicode
- Kompatybilne wstecz z ASCII (znaki ASCII zajmują 1 bajt)
- Używane domyślnie przez wiele API Java, np. `java.nio.Files`[^10]

### Przykład określania kodowania

```java
// Odczyt pliku z określonym kodowaniem
try (BufferedReader reader = Files.newBufferedReader(Paths.get("plik.txt"),
                                                    StandardCharsets.UTF_8)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
}

// Zapis pliku z określonym kodowaniem
try (BufferedWriter writer = Files.newBufferedWriter(Paths.get("plik.txt"),
                                                    StandardCharsets.UTF_8)) {
    writer.write("Tekst z polskimi znakami: ąśćńłóżź");
}
```

## Dodatkowe istotne elementy

### Try-with-resources

Od Java 7 dostępna jest konstrukcja try-with-resources, która automatycznie zamyka zasoby:

```java
try (InputStream is = new FileInputStream("plik.txt");
     OutputStream os = new FileOutputStream("kopia.txt")) {
    // Kod korzystający z zasobów
    byte[] buffer = new byte[^1024];
    int length;
    while ((length = is.read(buffer)) > 0) {
        os.write(buffer, 0, length);
    }
} // Zasoby zostaną automatycznie zamknięte
```

### DirectoryStream

`DirectoryStream` umożliwia iterację po zawartości katalogu:

```java
try (DirectoryStream<Path> stream = Files.newDirectoryStream(Paths.get("."))) {
    for (Path entry : stream) {
        System.out.println(entry.getFileName());
    }
}
```

### Wirtualne systemy plików

Java NIO.2 umożliwia tworzenie wirtualnych systemów plików w pamięci:

```java
try (FileSystem fs = Jimfs.newFileSystem(Configuration.unix())) {
    Path path = fs.getPath("/plik.txt");
    Files.writeString(path, "Zawartość pliku w pamięci");
    System.out.println(Files.readString(path));
}
```

### Wątki i synchronizacja

Przy pracy z plikami w środowisku wielowątkowym należy pamiętać o:

- Synchronizacji dostępu do współdzielonych plików
- Używaniu klas thread-safe lub własnej synchronizacji
- Stosowaniu blokad na pliki przy współbieżnym dostępie

## Podsumowanie

Java oferuje bogaty ekosystem klas i interfejsów do pracy z plikami, od prostych operacji odczytu/zapisu po zaawansowane techniki. Wybór odpowiednich narzędzi zależy od konkretnych wymagań aplikacji, takich jak wydajność, złożoność operacji czy zgodność z różnymi platformami.

Od Java 7 zaleca się korzystanie z API NIO.2 (Path, Files), które oferuje więcej funkcjonalności i lepszą obsługę błędów w porównaniu do starszego API (File). Przy pracy z tekstem zawsze należy pamiętać o właściwym określeniu kodowania znaków, najlepiej używając UTF-8.