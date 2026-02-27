# SQL

Dokumentacja online: [https://www.w3schools.com/sql/default.asp](https://www.w3schools.com/sql/default.asp)

**SQL (Structured Query Language)** – ustandaryzowany język programowania służący do komunikacji z relacyjnymi bazami danych. Powstał w latach 70., obecnie jest standardem w pracy z bazami (MySQL, PostgreSQL, Oracle, MS SQL Server). SQL **nie jest językiem ogólnego programowania**, tylko specjalistycznym językiem **do zarządzania danymi**.

---

SQL pozwala na:

1. **Tworzenie struktur baz danych - d**efiniowanie tabel, kolumn, typów danych, tworzenie relacji między tabelami.
2. **Modyfikację danych - d**odawanie nowych rekordów (`INSERT`), aktualizowanie istniejących (`UPDATE`), usuwanie rekordów (`DELETE`), p**obieranie danych (zapytania), w**yszukiwanie informacji za pomocą `SELECT,` filtrowanie (`WHERE`), sortowanie (`ORDER BY`), grupowanie (`GROUP BY`), łączenie tabel (`JOIN`).
3. **Zarządzanie dostępem i bezpieczeństwem - n**adawanie uprawnień użytkownikom (`GRANT`, `REVOKE`).
4. **Administrację bazą - t**worzenie widoków (`VIEW`), procedur, indeksów, kopii zapasowe i optymalizacja zapytań.

**Kluczowe terminy:** 

**Klucz główny** - Kolumna (lub zestaw kolumn) jednoznacznie identyfikująca każdy rekord w tabeli. **Cechy - k**ażdy rekord musi mieć unikalną wartość w kolumnie klucza głównego. Nie może przyjmować wartości `NULL`. W jednej tabeli może istnieć tylko **jeden** klucz główny.

**Przykład**:

```sql
CREATE TABLE Pracownicy (
    ID INT PRIMARY KEY,
    Imie VARCHAR(50),
    Nazwisko VARCHAR(50)
);
```

Klucz obcy - Kolumna w jednej tabeli wskazująca na klucz główny w innej tabeli. **Cel**: Zapewnienie **spójności danych** i relacji między tabelami. 

**Cechy**: Może przyjmować wartości `NULL` (jeśli nie określono inaczej). Może wskazywać na klucz główny w innej tabeli. W jednej tabeli można mieć wiele kluczy obcych.

**Przykład**:

```sql
CREATE TABLE Zamowienia (
    ZamowienieID INT PRIMARY KEY,
    KlientID INT,
    FOREIGN KEY (KlientID) REFERENCES Klienci(ID)
);

```

**Widok**

Widok (VIEW) w SQL to wirtualna tabela, której zawartość jest zdefiniowana przez zapytanie SELECT. Nie przechowuje danych fizycznie (z wyjątkiem widoków materializowanych), tylko przechowuje definicję zapytania. Zachowuje się jak tabela, zawsze zwraca **aktualne dane** z tabel bazowych, może być używane w `SELECT`, `JOIN`, `WHERE`, itd.

```sql
CREATE VIEW ActiveCustomers AS
SELECT CustomerID, FirstName, LastName, Email
FROM Customers
WHERE IsActive = 1;
```

## **CREATE VIEW**

Tworzy nowy widok (logiczne "okno" na dane, oparte na zapytaniu `SELECT`).

```sql
CREATE VIEW ActiveCustomers AS
SELECT CustomerID, FirstName, LastName, Email
FROM Customers
WHERE IsActive = 1;
```

---

## **ALTER VIEW**

Modyfikuje istniejący widok (np. zmienia zapytanie, które go definiuje).

```sql
ALTER VIEW ActiveCustomers AS
SELECT CustomerID, FirstName, LastName, Email, Phone
FROM Customers
WHERE IsActive = 1;
```

---

Usuwa widok z bazy danych.

```sql
DROP VIEW ActiveCustomers;

```

## Typy danych w SQL

| Kategoria | Typ danych | Opis |
| --- | --- | --- |
| Typy liczbowe | INT | Liczba całkowita |
| Typy liczbowe | BIGINT | Duża liczba całkowita |
| Typy liczbowe | SMALLINT | Mała liczba całkowita |
| Typy liczbowe | DECIMAL(p,s) / NUMERIC(p,s) | Liczby dziesiętne z określoną precyzją |
| Typy liczbowe | FLOAT / REAL | Liczby zmiennoprzecinkowe |
| Typy znakowe | CHAR(n) | Stała długość tekstu (np. kod pocztowy) |
| Typy znakowe | VARCHAR(n) | Zmienna długość tekstu (np. imię, nazwisko) |
| Typy znakowe | TEXT | Długi tekst (opis, komentarz) |
| Typy daty i czasu | DATE | Tylko data (YYYY-MM-DD) |
| Typy daty i czasu | TIME | Tylko czas (HH:MM:SS) |
| Typy daty i czasu | DATETIME | Data i czas |
| Typy daty i czasu | TIMESTAMP | Data i czas z automatyczną aktualizacją |
| Typy daty i czasu | YEAR | Rok |
| Typy logiczne i inne | BOOLEAN | Wartość logiczna (TRUE / FALSE) |
| Typy logiczne i inne | BLOB | Dane binarne (np. obrazy, pliki) |

# Główne zapytania SQL

| Kategoria |  | Przykład |
| --- | --- | --- |
| Bazy danych |  |  |
| **`CREATE DATABASE** <nazwa>` | Tworzymy nową bazę danych |  |
| **`DROP DATABASE** Database_Name` | Usuwamy bazę danych |  |
| **`RENAME DATABASE** old_database_name **TO** new_database_name;` | Zmiana nazwy bazy danych |  |
| `USE <database>` | Uzywamy wskazanej bazy danych |  |
| Tabele |  |  |
| `CREATE TABLE <nazwa>` |  |  |
| `DROP TABLE table_name;` |  |  |
| `DELETE **FROM** table_name [**WHERE** condition];`  | Jeśli jednak nie określisz warunku WHERE, wszystkie wiersze zostaną usunięte z tabeli. |  |
| `TRUNCATE **TABLE** table_name;` | Funkcja TRUNCATE TABLE działa szybciej i zużywa mniej zasobów niż polecenie DELETE TABLE. Polecenie DROP TABLE może być również użyte do usunięcia całej tabeli, ale usuwa ono również strukturę tabeli. Funkcja TRUNCATE TABLE nie usuwa struktury tabeli. |  |
| `ALTER TABLE table_name ADD (column_Name1 column-definition, column_Name2 column-definition, ..... column_NameN column-definition);` | Instrukcja ALTER TABLE  pozwala dodawać, modyfikować i usuwać kolumny istniejącej tabeli. Instrukcja ta pozwala również użytkownikom bazy danych dodawać i usuwać różne ograniczenia SQL w istniejących tabelach. |  |
| Inne: `COPY, TEMP, RENAME` |  |  |
| **DQL** |  |  |
| `SELECT * FROM <nazwa tabeli>` 
`SELECT <nazwy kolumn>, <nazwy kolumn> FROM tabela` | Pobieramy wartości z tabeli, `*` = wszystkie lub konkretne wskazane po nazwie |  |
| `SELECT **DISTINCT**` | Pobieraj tylko różne lub unikalne dane | `SELECT **DISTINCT** column_name ,column_name **FROM** table_name;` |
| `COUNT`  | `Count` pozwala zliczyć rekordy. `AS` pozwala nadać własną nazwę zmodyfikowanej kolumnie. | `SELECT COUNT (Bike_Color) **AS** TotalBikeColor **FROM** Bikes ;` |
| `SELECT TOP 3` / `FIRST` / `LAST` / `RANDOM` | `TOP` = pokazuje ograniczoną liczbę rekordów lub wierszy z tabeli bazy danych. Reszta raczej czytelna. | `SELECT TOP 3 Car_Name, Car_Color FROM Cars;` |
| `SELECT … IN …` | `IN` to operator używany w zapytaniach SQL, który pomaga ograniczyć konieczność stosowania wielu warunków „OR” w języku SQL. Jest on używany w instrukcjach `SELECT, INSERT, UPDATE lub DELETE`. | `SELECT * **FROM** marks **WHERE** roll_no IN (001, 023, 024);` |
| `SELECT DATE` |  | `SELECT * **FROM** **tablename** **WHERE** your **datecolumn** >= '2013-12-12'` `SELECT * **FROM** table_name **WHERE** yourdate BETWEEN '2012-12-12' and '2013-12-12'` |
| `SELECT SUM (expression)`  |  | `SELECT SUM (salary) **AS** "Total Salary" **FROM** employees **WHERE** salary > 20000;` |
| `SELECT ... **WHERE** MARKS **IS** NULL` / `NOT NULL` | Zwraca komórki o wartości null lub wykluczamy te które posiadają null | `SELECT SIR_NAME, **NAME**, MARKS **FROM** STUDENTS **WHERE** MARKS **IS** NULL` |
| `WHERE` / `AND` / `OR`  | `WHERE` Pozwala warunkowo filtrować dane. `AND` pozwala łączyć warunki. `OR` = lub.  | **`SELECT** ***FROM** emp **WHERE** Department = "IT" AND Location = "Mumbai";` |
| `WITH` | Klauzula SQL WITH służy do tworzenia bloku podzapytania, do którego można odwoływać się w kilku miejscach głównego zapytania SQL. | `WITH <alias_name> **AS** (sql_sub-query_statement) **SELECT** column_list **FROM** <alias_name> [**table** **name**] [**WHERE** <join_condition>]` |
| `AS` | `AS` służy do tymczasowego przypisania nowej nazwy kolumnie tabeli lub nawet całej tabeli. | `SELECT Column_Name1 **AS** New_Column_Name, Column_Name2 **As** New_Column_Name **FROM** Table_Name;` |
| `HAVING` | Klauzula `HAVING` umieszcza warunek w grupach zdefiniowanych przez klauzulę `GROUP BY` w instrukcji `SELECT.` | `SELECT column_Name1, column_Name2, ....., column_NameN aggregate_function_name(column_Name) **FROM** table_name **GROUP** **BY** column_Name1 **HAVING** condition;` |
| `ORDER BY` / `ASC` / `DESC` | Definiujemy sortowanie tabeli.  |  |
| **`JOIN (INNER / LEFT / RIGHT / FULL)`** |  | `SELECT columns **FROM** table1 JOIN table2 **ON** table1.common_column = table2.common_column;` |
|  |  |  |

## Język manipulacji danymi (DML)

- **INSERT**: Dodaje nowe rekordy do tabeli
- **UPDATE**: Modyfikuje istniejące rekordy w tabeli
- **DELETE**: Usuwa rekordy z tabeli

```sql
-- Insert a single row
INSERT INTO employees (first_name, last_name, department_id, salary)
VALUES ('Alex', 'Rivera', 20, 72000);

-- Insert multiple rows
INSERT INTO departments (department_id, name)
VALUES (30, 'Marketing'), (40, 'Finance');
```

```sql
-- Give a 5% raise to Sales department
UPDATE employees
SET salary = salary * 1.05
WHERE department_id = 10;
```

```sql
-- Delete contractors who ended before 2024-01-01
DELETE contractors
WHERE end_date < DATE '2024-01-01';
```

## Język definicji danych (DDL)

- **CREATE**: Tworzy nowe obiekty bazy danych (tabele, widoki, procedury itp.)
- **ALTER**: Zmienia strukturę istniejących obiektów bazy danych
- **DROP**: Usuwa obiekty bazy danych
- **TRUNCATE**: Usuwa wszystkie rekordy z tabeli, pozostawiając jej strukturę

```sql
CREATE TABLE Klienci (
      ID INT PRIMARY KEY,
      Imie VARCHAR(50),
      Nazwisko VARCHAR(50)
  );
  
ALTER TABLE Klienci ADD Email VARCHAR(100);

DROP TABLE Klienci;

TRUNCATE TABLE Klienci;

```

## Język kontroli dostępu (DCL)

- **GRANT**: Nadaje użytkownikom określone uprawnienia
- **REVOKE**: Odbiera użytkownikom uprawnienia

- **UNION / UNION ALL** – łączenie wyników wielu zapytań
- **INTERSECT** – część wspólna wyników
- **EXCEPT / MINUS** – różnica wyników
- **OFFSET** – pomijanie określonej liczby rekordów (np. paginacja)

## Operatory i wyrażenia

- **LIKE / NOT LIKE** – dopasowanie wzorca
    - `%` – dowolna liczba znaków
    - `_` – jeden znak
- **IN / NOT IN** – sprawdzanie wartości w liście
- **BETWEEN / NOT BETWEEN** – sprawdzanie przedziałów
- **EXISTS** – sprawdzanie, czy podzapytanie zwraca wynik
- **CASE** – warunki w zapytaniach
- **CONCAT / ||** – łączenie tekstów (zależnie od bazy)

## Operatory matematyczne

- `+` dodawanie
- `-` odejmowanie
- `*` mnożenie
- `/` dzielenie
- `%` modulo

## Operatory porównań

- `=` – równe
- `<>` lub `!=` – różne
- `<`, `>`, `<=`, `>=` – porównania

```sql
 SELECT Imie, Nazwisko, Miasto
  FROM Klienci
  WHERE Miasto LIKE 'W%'
  ORDER BY Nazwisko ASC;
```

```sql
SELECT Imie, Nazwisko
FROM Klienci
WHERE Miasto IN ('Warszawa', 'Kraków', 'Gdańsk')
ORDER BY Nazwisko
OFFSET 5 ROWS FETCH NEXT 5 ROWS ONLY;
```

```sql
SELECT k.Miasto, SUM(z.Kwota) AS LacznaKwota
FROM Klienci k
JOIN Zamowienia z ON k.ID = z.KlientID
GROUP BY k.Miasto
HAVING SUM(z.Kwota) > 10000;
```

```sql
SELECT Imie, Nazwisko,
       CASE 
           WHEN Miasto = 'Warszawa' THEN 'Stolica'
           ELSE 'Inne'
       END AS Region
FROM Klienci
ORDER BY Region DESC, Nazwisko;

```

```sql
SELECT Imie, Nazwisko
FROM Klienci k
WHERE EXISTS (
    SELECT 1
    FROM Zamowienia z
    WHERE z.KlientID = k.ID
);
```

```sql
SELECT k.Imie, k.Nazwisko, z.DataZamowienia
FROM Klienci k
JOIN Zamowienia z ON k.ID = z.KlientID
WHERE z.DataZamowienia BETWEEN '2025-01-01' AND '2025-12-31';
```

```sql
SELECT Imie, Nazwisko, 'Klient' AS Typ
FROM Klienci
UNION
SELECT Imie, Nazwisko, 'Pracownik' AS Typ
FROM Pracownicy
ORDER BY Nazwisko;

```

```sql
-- Inner join employees with departments
SELECT [e.id](http://e.id), e.first_name, e.last_name, [d.name](http://d.name) AS department
FROM employees e
JOIN departments d ON d.department_id = e.department_id;

-- Left join to include employees without a manager
SELECT [e.id](http://e.id), e.first_name, m.first_name AS manager_first
FROM employees e
LEFT JOIN managers m ON [m.id](http://m.id) = e.manager_id;

-- Full outer join example (if supported by your DBMS)
SELECT COALESCE([e.id](http://e.id), 0) AS employee_id, [d.name](http://d.name) AS department
FROM employees e
FULL OUTER JOIN departments d ON d.department_id = e.department_id;
```

```sql
-- Employees who work in locations in the EU
SELECT id, first_name, last_name
FROM employees
WHERE department_id IN (
  SELECT department_id
  FROM departments
  WHERE location IN ('DE', 'FR', 'PL', 'ES', 'IT')
);
```

### Transakcje

```sql
-- Example transaction
BEGIN;
  UPDATE accounts SET balance = balance - 100.00 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100.00 WHERE id = 2;
COMMIT;  -- or ROLLBACK;
```

### DDL

```sql
-- Create table
CREATE TABLE projects (
  id SERIAL PRIMARY KEY,        -- use INTEGER AUTOINCREMENT in SQLite
  name VARCHAR(200) NOT NULL,
  start_date DATE,
  end_date DATE
);

-- Alter table
ALTER TABLE projects ADD COLUMN status VARCHAR(20) DEFAULT 'planned';

-- Drop table
DROP TABLE IF EXISTS old_logs;
```

```sql
### INNER JOIN
Zwraca tylko rekordy, które mają dopasowanie w obu tabelach.  
```sql
SELECT k.ID, k.Imie, z.ZamowienieID
FROM Klienci k
INNER JOIN Zamowienia z ON k.ID = z.KlientID;
```

```sql
SELECT k.ID, k.Imie, z.ZamowienieID
FROM Klienci k
LEFT JOIN Zamowienia z ON k.ID = z.KlientID;

SELECT k.ID, k.Imie, z.ZamowienieID
FROM Klienci k
RIGHT JOIN Zamowienia z ON k.ID = z.KlientID;
```

```sql
SELECT k.ID, k.Imie, z.ZamowienieID
FROM Klienci k
FULL JOIN Zamowienia z ON k.ID = z.KlientID;
```