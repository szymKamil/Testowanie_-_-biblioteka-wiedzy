# java_sql

# Praca z SLQ poprzez JAVA

## Konfiguracja

### Wymagania i konfiguracja

- **JDK** (Java Development Kit) w wersji ≥ 8.
- **Maven** lub **Gradle** do zarządzania zależnościami.

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>
```

- Sterownik JDBC odpowiedni dla bazy (np. **MySQL**, **PostgreSQL**, **SQL Server**) - np. https://dev.mysql.com/downloads/connector/j/

### ✅ Dodanie sterownika MySQL Connector/J do IntelliJ (ręcznie, bez Mavena)

### 🔹 Krok 1: Pobierz i rozpakuj ZIP

- Przejdź na stronę: [https://dev.mysql.com/downloads/connector/j/](https://dev.mysql.com/downloads/connector/j/)
- Pobierz paczkę ZIP odpowiednią dla Twojego systemu.
- Wypakuj ją – znajdziesz tam plik:`mysql-connector-j-<wersja>.jar`
—

### 🔹 Krok 2: Dodaj `.jar` do projektu w IntelliJ

1. Kliknij **prawym przyciskiem myszy** na nazwę projektu → wybierz **Open Module Settings***(lub skrót: `Ctrl+Shift+Alt+S`)*
2. Przejdź do zakładki **Libraries**
3. Kliknij `+` → wybierz **Java**
4. Wskaż pobrany plik `.jar` z katalogu, gdzie go wypakowałeś

## Zatwierdź i zamknij okno

### 🔹 Krok 3: Upewnij się, że biblioteka jest dostępna w module

1. W oknie **Project Structure**:
2. Przejdź do zakładki **Modules**
3. Wybierz swój moduł (np. `main`)
4. Przejdź do zakładki **Dependencies**
5. Sprawdź, czy sterownik `mysql-connector-j-<wersja>.jar` znajduje się na liście

## 🗃️ Java – Łączenie z bazą danych MySQL

W Javie istnieją dwa podstawowe sposoby na połączenie się z bazą danych MySQL:

---

## 1️⃣ `DriverManager` (klasyczne podejście)

### ✅ Zalety:

- Proste i szybkie do zaimplementowania
- Dobre do testów i małych aplikacji

### ❌ Wady:

### 🔸 Brak connection poolingu

- Każde wywołanie `DriverManager.getConnection()` tworzy **nowe połączenie do bazy**.
- Tworzenie/zamykanie połączeń jest kosztowne czasowo i obciąża serwer.
- Brak mechanizmu do **ponownego użycia** już istniejących połączeń.
- W większych aplikacjach może to prowadzić do:
    - spadku wydajności
    - przekroczenia limitu połączeń do bazy danych

### 🔸 Hardkodowanie URL i danych logowania

- Dane połączeniowe (`host`, `port`, `nazwa bazy`, `user`, `hasło`) są wpisywane **na sztywno** w kodzie:
    
    ```java
    DriverManager.getConnection("jdbc:mysql://localhost:3306/db", "root", "1234");
    ```
    
- Trudno zmienić środowisko (np. z localhost na serwer produkcyjny).
- Trudniej zabezpieczyć dane (np. hasła), bo są zapisane w kodzie źródłowym.

### 🔸 Trudne zarządzanie konfiguracją w większych projektach

- Brakuje centralnego punktu, gdzie można ustawić parametry połączenia.
- Każda klasa lub moduł może mieć inne połączenia — brak kontroli i spójności.
- Trudno dostosować parametry (np. timeouty, encoding, SSL) bez rozbudowanej logiki.

### ➡️ W projektach produkcyjnych lepiej korzystać z rozwiązań takich jak:

- MysqlDataSource (z zewnętrzną konfiguracją),
- Connection Pooling (np. HikariCP, Apache DBCP, C3P0),
- Wstrzykiwanie zależności (np. Spring @Bean dla DataSource),
- Pliki konfiguracyjne .properties lub .yaml.

### 💡 Przykład:

```java
import java.sql.Connection;
import java.sql.DriverManager;

public class Main {
    public static void main(String[] args) throws Exception {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String user = "user";
        String password = "password";

        Connection conn = DriverManager.getConnection(url, user, password);
        System.out.println("Połączono!");
        conn.close();
    }
}
```

## MysqlDataSource (zalecane podejście)

Klasa z pakietu com.mysql.cj.jdbc.MysqlDataSource.

✅ Zalety:
- Obsługuje connection pooling
- Można przekazywać parametry oddzielnie (bez hardkodowania URL)
- Dobrze współpracuje z JNDI (np. w aplikacjach serwerowych)
- Większa kontrola nad konfiguracją połączenia

❌ Wady:
- Trochę więcej kodu na start
- Wymaga jawnego importu biblioteki MySQL Connector/J

```java
import com.mysql.cj.jdbc.MysqlDataSource;
import java.sql.Connection;

public class Main {
public static void main(String[] args) throws Exception {
MysqlDataSource dataSource = new MysqlDataSource();
dataSource.setServerName("localhost");
dataSource.setPortNumber(3306);
dataSource.setDatabaseName("mydb");
dataSource.setUser("user");
dataSource.setPassword("password");

        Connection conn = dataSource.getConnection();
        System.out.println("Połączono!");
        conn.close();
    }
}
```

| Cechy | `DriverManager` | `MysqlDataSource` |
| --- | --- | --- |
| **Poziom trudności** | Łatwy | Średni |
| **Obsługa connection poolingu** | ❌ Nie | ✅ Tak |
| **Hardkodowanie URL** | ✅ Tak | ❌ Nie |
| **Możliwość konfiguracji** | Ograniczona | Bardziej elastyczna |
| **Zastosowanie produkcyjne** | ❌ Odradzane | ✅ Zalecane |

📌 Rekomendacja
Do nauki lub małych projektów → DriverManager
Do większych aplikacji / produkcji → MysqlDataSource (+ pooling, np. HikariCP)

## 🌀 Czym jest connection pooling?

Connection pooling to technika zarządzania połączeniami z bazą danych, polegająca na utrzymywaniu puli (ang. pool) otwartych połączeń, które są wielokrotnie używane przez aplikację zamiast otwierać i zamykać nowe za każdym razem.

Jak to działa?
Gdy aplikacja się uruchamia, tworzona jest określona liczba połączeń do bazy danych (np. 10).
Te połączenia są przechowywane w “puli”.
Gdy jakiś fragment kodu potrzebuje połączenia:
Pobiera gotowe połączenie z puli.
Po zakończeniu operacji:
Połączenie nie jest zamykane — wraca z powrotem do puli, gotowe do ponownego użycia.

❌ Bez poolingu:

```java
Connection conn = DriverManager.getConnection(...);
// kosztowne, trwa kilkadziesiąt–kilkaset ms
conn.close();
```

✅ Z poolingiem (np. HikariCP, MysqlDataSource):

```java
Connection conn = dataSource.getConnection();
// błyskawiczne pobranie z gotowej puli
conn.close(); // tak naprawdę „zwraca” połączenie do puli
```

## 3. 🔗 Połączenie przez DriverManager

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DriverManagerExample {
    public static void main(String[] args) {
        String url      = "jdbc:mysql://localhost:3306/nazwabazy?useSSL=false&serverTimezone=UTC";
        String user     = "root";
        String password = "haslo";

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            System.out.println("✅ Połączono przez DriverManager");
        } catch (SQLException e) {
            System.err.println("❌ Błąd: " + e.getMessage());
        }
    }
}
```

- Metoda `getConnection(...)` zwraca instancję `java.sql.Connection`.
- Parametry URL różnią się w zależności od bazy (zob. rozdział 9).

---

## 4. 🌐 Połączenie przez DataSource

### 4.1. `MysqlDataSource`

```java
import com.mysql.cj.jdbc.MysqlDataSource;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Arrays;

public class DataSourceExample {
    public static void main(String[] args) {
        String user = "root";
        char[] pass = "tajneHaslo".toCharArray();

        MysqlDataSource ds = new MysqlDataSource();
        ds.setServerName("localhost");
        ds.setPort(3306);
        ds.setDatabaseName("music");

        try (Connection conn = ds.getConnection(user, String.valueOf(pass))) {
            System.out.println("✅ Połączono przez MysqlDataSource");
            Arrays.fill(pass, ' ');
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### 4.2. Zalety DataSource

- Obiektowe podejście — można go wstrzykiwać (DI).
- Łatwiejsza integracja z poolami (`HikariCP`, `Apache DBCP`).
- Konfigurowalność (timeout, SSL, unicode).

---

## 5. 📝 Wykonywanie zapytań SQL

### 5.1. `Statement`

```java
try (Statement stmt = conn.createStatement()) {
    String sql = "CREATE TABLE artists (id INT PRIMARY KEY, name VARCHAR(100));";
    stmt.execute(sql);
}
```

- Metody: `execute()`, `executeQuery()` (SELECT), `executeUpdate()` (INSERT/UPDATE/DELETE).
- Niechronione przed SQL Injection.

### 5.2. `PreparedStatement`

```java
String query = "INSERT INTO artists (id, name) VALUES (?, ?)";
try (PreparedStatement ps = conn.prepareStatement(query)) {
    ps.setInt(1, 1);
    ps.setString(2, "The Beatles");
    int rows = ps.executeUpdate();
    System.out.println(rows + " wiersze dodane");
}
```

- Parametryzacja zapytań — bezpieczne przed SQL Injection.
- Możliwość wielokrotnego wykonywania z różnymi zestawami parametrów.

### 5.3. `CallableStatement`

```java
try (CallableStatement cs = conn.prepareCall("{CALL get_artist_name(?, ?)}")) {
    cs.setInt(1, artistId);
    cs.registerOutParameter(2, Types.VARCHAR);
    cs.execute();
    String name = cs.getString(2);
}
```

- Wywoływanie procedur składowanych.

---

## 6. 📑 Przetwarzanie wyników (`ResultSet`)

```java
String select = "SELECT id, name FROM artists";
try (Statement stmt = conn.createStatement();
     ResultSet rs = stmt.executeQuery(select)) {
    while (rs.next()) {
        int id   = rs.getInt("id");
        String name = rs.getString("name");
        System.out.printf("ID:%d, Name:%s%n", id, name);
    }
}
```

- Metody: `next()`, `getInt()`, `getString()`, `getDate()`, itp.
- Możliwość dostępu przez indeks kolumny lub nazwę.

---

## 7. 🔄 Transakcje i Batch

### Transakcje

```java
conn.setAutoCommit(false);
try {
    // kilka operacji
    conn.commit();
} catch (SQLException e) {
    conn.rollback();
}
```

### Batch

```java
try (PreparedStatement ps = conn.prepareStatement("INSERT INTO ...")) {
    for (...) {
        // ustaw parametry
        ps.addBatch();
    }
    int[] results = ps.executeBatch();
}
```

- `setAutoCommit(false)` — ręczne zarządzanie commit/rollback.
- Batch pozwala na hurtowe wysyłanie zapytań.

---

## 8. 🛡️ Najlepsze praktyki

- **Try-with-resources** dla `Connection`, `Statement`, `ResultSet`.
- Używaj **PreparedStatement** dla zapytań z parametrami.
- Unikaj przechowywania haseł w kodzie źródłowym.
- Konfiguracja poprzez pliki properties (`.properties`, `.env`).
- **Connection pool** dla zwiększenia wydajności.
- Logowanie zapytań i błędów (SLF4J, Logback).

---

## 9. 📁 Struktura projektu (Maven)

```
project-root/
│
├── src/main/java/
│   └── com/example/db/
│       ├── DataSourceConfig.java
│       ├── DBConnectionManager.java
│       ├── ArtistDAO.java
│       └── Main.java
├── src/main/resources/
│   └── db.properties
└── pom.xml
```

---

1. 🔄 Zaawansowane zarządzanie połączeniami

1.1. Connection Pool vs DriverManager

DriverManager: prosty, ale przy każdorazowym wywołaniu getConnection() tworzy nowe połączenie — kosztowne.

DataSource + Pool: utrzymuje stały zbiór otwartych połączeń. Pozwala na:

konfigurację maksimum otwartych połączeń,

elastyczne timeouty,

detekcję i zwalnianie „wyciekłych” połączeń.

1.2. HikariCP – przykładowa konfiguracja

HikariConfig config = new HikariConfig();
config.setJdbcUrl(“jdbc:mysql://localhost:3306/music”);
config.setUsername(“root”);
config.setPassword(System.getenv(“DB_PASS”));
config.setMaximumPoolSize(10);
config.setConnectionTimeout(30000); // max czekania na połączenie
config.setIdleTimeout(600000); // timeout nieużywanych połączeń
config.setLeakDetectionThreshold(2000); // wykrywanie wycieków po 2s

HikariDataSource ds = new HikariDataSource(config);

1. 🔑 Pobieranie kluczy generowanych (auto-increment)

String sql = “INSERT INTO artists(name) VALUES(?)”;
try (PreparedStatement ps = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS)) {
ps.setString(1, “Queen”);
ps.executeUpdate();
try (ResultSet keys = ps.getGeneratedKeys()) {
if (keys.next()) {
long id = keys.getLong(1);
System.out.println(“Nowy ID:” + id);
}
}
}

1. 🔄 Mapowanie typów Java ↔︎ SQL

Java Type

JDBC Method

SQL Type

String

getString/setString

VARCHAR, TEXT

int/Integer

getInt/setInt

INT

long/Long

getLong/setLong

BIGINT

boolean/Boolean

getBoolean/setBoolean

BOOLEAN

LocalDate

getDate().toLocalDate()/setDate

DATE

LocalDateTime

getTimestamp()/setTimestamp

DATETIME, TIMESTAMP

InputStream

getBinaryStream()/setBinaryStream

BLOB

Uwaga: Konwersję typów daty zaleca się wykonywać przez java.time (z JDBC 4.2+).

1. 📋 Metadane bazy i wyników

4.1. DatabaseMetaData

DatabaseMetaData meta = conn.getMetaData();
System.out.println(meta.getDatabaseProductName() + ” ” + meta.getDatabaseProductVersion());
try (ResultSet tables = meta.getTables(null, null, “%”, null)) {
while (tables.next()) {
System.out.println(“Tabela:” + tables.getString(“TABLE_NAME”));
}
}

4.2. ResultSetMetaData

try (ResultSet rs = stmt.executeQuery(“SELECT * FROM artists”)) {
ResultSetMetaData rsMeta = rs.getMetaData();
int cols = rsMeta.getColumnCount();
for (int i = 1; i <= cols; i++) {
System.out.printf(“Kolumna %d: %s (%s)”,
i,
rsMeta.getColumnName(i),
rsMeta.getColumnTypeName(i)
);
}
}

1. 🔄 Transakcje zaawansowane

5.1. Poziomy izolacji

conn.setTransactionIsolation(Connection.TRANSACTION_REPEATABLE_READ);

NONE, READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE

5.2. Savepointy

conn.setAutoCommit(false);
Savepoint sp = conn.setSavepoint(“BeforeInsert”);
try {
// operacje SQL
conn.rollback(sp); // przywróć do punktu
conn.commit();
} catch (SQLException e) {
conn.rollback(); // całościowy rollback
}

1. 🛠 Batch, fetchSize i streaming

Batch: wysyłanie wielu INSERT/UPDATE jednym wywołaniem — ps.addBatch() + ps.executeBatch().

fetchSize: sugerowana liczba wierszy pobieranych na raz z serwera:

stmt.setFetchSize(100);

Streaming (duże zbiory):

stmt = conn.createStatement(ResultSet.TYPE_FORWARD_ONLY, ResultSet.CONCUR_READ_ONLY);
stmt.setFetchSize(Integer.MIN_VALUE); // dla MySQL streaming

1. 🧹 Efektywne zamykanie i detekcja wycieków

Korzystaj z try-with-resources dla każdego Connection, Statement, ResultSet.

Pooly SQL – konfiguruj leakDetectionThreshold (HikariCP) lub removeAbandoned (DBCP).

1. ⚠️ Obsługa wyjątków i kody SQLState

try {
// operacje
} catch (SQLException e) {
System.err.printf(“SQLState: %s, ErrorCode: %d, Message: %s”,
e.getSQLState(), e.getErrorCode(), e.getMessage());
Throwable cause = e.getCause();
}

SQLState pozwala zidentyfikować klasę błędu (np. 08 = problem z połączeniem).