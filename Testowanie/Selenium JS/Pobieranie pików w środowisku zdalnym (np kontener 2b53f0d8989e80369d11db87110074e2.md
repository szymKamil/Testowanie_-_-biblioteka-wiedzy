# Pobieranie pików w środowisku zdalnym (np. kontener Dockera na serwerze).

Kluczowy problem: Kod testu (Java/Host) i Przeglądarka (Docker/Node) działają w **odseparowanych systemach plików**.

## 1. Strategia "Kurier" (zalecane dla Selenium 4)

Najnowsze i najczystsze podejście. Grid sam przesyła plik z kontenera do kodu testu. Nie wymaga konfiguracji Docker Volumes.

- **Działanie:** Plik ląduje w kontenerze -> `downloadFile` pobiera go po sieci -> Zapisuje na dysku lokalnym (Host).
- **Zastosowanie:** Jenkins, CI/CD, Grid bez dostępu do plików.

```java

// 1. Konfiguracja (ChromeOptions)` 
options.setCapability("se:downloadsEnabled", true);
//Możemy to też zrobić poprzez:
ChromeOptions options = getDefaultChromeOptions();
    options.setEnableDownloads(true);
    driver = new RemoteWebDriver(gridUrl, options);
// Konieczne jest dodanie flagi: --enable-managed-downloads true

// 2. W teście (Pobieranie i zapis na Host)
Path localPath = Path.of(System.getProperty("user.dir"), "target", "downloads");
((HasDownloads) driver).downloadFile("nazwa_pliku_w_kontenerze.pdf", localPath);

// 3. Weryfikacja/Usuwanie (Standardowa Java)
Files.exists(localPath.resolve("nazwa_pliku_w_kontenerze.pdf"));

```

Należy pamiętać, że Selenium nie czeka na zakończenie pobierania plików, więc lista stanowi natychmiastowy zrzut ekranu zawierający nazwy plików znajdujących się obecnie w katalogu dla danej sesji.

```java
    List<String> files = ((HasDownloads) driver).getDownloadableFiles();
```

**Ważne:** Plik nie pojawia się w metodzie `.getDownloadableFiles()` natychmiast. Konieczne jest użycie mechanizmu **polling** (oczekiwania), aż plik pojawi się na liście przed próbą jego pobrania.

```java
// Przykład bezpiecznego pobierania (pseudokod):
FluentWait<WebDriver> wait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(10))
    .pollingEvery(Duration.ofMillis(500));

// Czekaj aż lista plików w kontenerze nie będzie pusta
String remoteFileName = wait.until(d -> {
    List<String> files = ((HasDownloads) d).getDownloadableFiles();
    return files.isEmpty() ? null : files.get(0);
});

// Dopiero teraz pobierz
((HasDownloads) driver).downloadFile(remoteFileName, localPath);
```

Domyślnie katalog pobierania jest usuwany po zakończeniu danej sesji, ale można również usunąć wszystkie pliki podczas sesji.

```java
    ((HasDownloads) driver).deleteDownloadableFiles();
```

---

## 2. Strategia "Wspólny Dysk" (Volume Mapping)

Fizyczne połączenie folderu w kontenerze z folderem na hoście.

- **Działanie:** Docker "montuje" folder Hosta wewnątrz Kontenera. Zmiany są widoczne natychmiast w obu miejscach.
- **Wymaga:** Edycji `docker-compose.yml`.

```yaml
volumes:
  - ./lokalny_folder_hosta:/home/seluser/Downloads
```

**Kod Java:**

```java
// 1. Opcje Drivera (Ścieżka Linuxowa - Kontener)
prefs.put("download.default_directory", "/home/seluser/Downloads");
//Alternatywnie:
prefs.put("download.default_directory", downloadPath.toString());
prefs.put("download.prompt_for_download", false);
prefs.put("download.directory_upgrade", true);
prefs.put("safebrowsing.enabled", true);

// 2. Weryfikacja/Usuwanie (Ścieżka Windowsowa - Host)
Path hostPath = Path.of(System.getProperty("user.dir"), "lokalny_folder_hosta");
Files.list(hostPath).forEach(Files::delete);
```

### Problem: "Permission Denied" na Linuxie (Strategy 2 "Wspólny Dysk")

To klasyk przy pracy z Jenkinsem/Linuxem. Docker często tworzy pliki jako `root`, a Jenkins (jako user `jenkins`) nie może ich potem usunąć (`Files.delete` rzuci wyjątek).

**Dopisz do Strategii 2:**

> Uwaga CI/CD: Na systemach Linux pliki tworzone przez Docker mogą mieć właściciela root. Aby uniknąć błędu AccessDeniedException przy czyszczeniu folderu przez Javę, należy w docker-compose.yml wymusić użytkownika:
> 

```java
services:
  chrome:
    user: "${UID}:${GID}" # Uruchamia proces w kontenerze jako bieżący użytkownik hosta
```

---

## 3. Strategia "Izolacja" (Tylko w kontenerze)

Plik zostaje w kontenerze. Host go nie widzi.

- **Zastosowanie:** Gdy nie chcemy zaśmiecać dysku lub nie mamy uprawnień do mapowania wolumenów.
- **Wymaga:** Użycia `docker exec` do weryfikacji i sprzątania, a tym samym konieczny jest dostęp kodu do kontenera dockera.

**Kod Java (Opcje):**

```java
// Ścieżka Linuxowa
prefs.put("download.default_directory", "/home/seluser/Downloads");`

**Weryfikacja (Hack):**

// Sprawdzenie czy przeglądarka widzi plik
driver.get("file:///home/seluser/Downloads/plik.pdf");
Assert.assertFalse(driver.getPageSource().contains("ERR_FILE_NOT_FOUND"));`
```

**Sprzątanie poprzez shell:**

```java
String[] cmd = {
    "docker", "exec", "-u", "seluser", "nazwa_kontenera",
    "rm", "/home/seluser/Downloads/plik.pdf"
};
Runtime.getRuntime().exec(cmd);

```

**Zastosowanie:** brak uprawnień do wolumenów, testy czysto izolowane per kontener.

# 4. Plik zapisywany lokalnie

### 🧩 Snippet: Solidna weryfikacja lokalna (Java NIO)

Po pobraniu pliku na dysk lokalny (Host), nie sprawdzaj tylko czy istnieje. Sprawdź, czy ma zawartość (nie jest pusty) i czy masz do niego dostęp.

### 1. Weryfikacja pobranego pliku (Asercje)

Ten fragment sprawdza plik **już po przesłaniu go na maszynę lokalną** (Hosta).

```java
// Zdefiniuj docelową ścieżkę na dysku lokalnym (gdzie test jest uruchamiany)
Path targetFile = Path.of(System.getProperty("user.dir"), "target", "downloads", "faktura.pdf");

// KROK 1: Transfer (Tylko dla Dockera - dla Local nic nie zmienia, jeśli plik już tam jest)
if (isRunningInDocker) {
    // "faktura_w_kontenerze.pdf" to nazwa, pod jaką plik zapisał się w /home/seluser/Downloads
    ((HasDownloads) driver).downloadFile("faktura_w_kontenerze.pdf", targetFile);
}

// KROK 2: Weryfikacja fizyczna (Java NIO)
// Sprawdź czy plik fizycznie istnieje na dysku Hosta
if (!Files.exists(targetFile)) {
    throw new AssertionError("Błąd krytyczny! Plik nie został odnaleziony: " + targetFile.toAbsolutePath());
}

// Sprawdź czy plik nie jest pusty (0 bajtów = błąd sieci/zapisu)
try {
    long fileSize = Files.size(targetFile);
    if (fileSize == 0) {
        throw new AssertionError("Błąd! Pobrany plik jest pusty (0 bajtów).");
    }
    System.out.println("Sukces! Plik pobrany poprawnie. Rozmiar: " + fileSize + " bajtów.");
} catch (IOException e) {
    throw new RuntimeException("Błąd odczytu atrybutów pliku: " + e.getMessage());
}
```

### 2. Inteligentne czyszczenie folderu (`clearDownloadFolder`)

Metoda automatycznie wykrywa środowisko i czyści odpowiednie zasoby:

- **Lokalnie:** Usuwa pliki fizycznie z dysku.
- **Docker:** Czyści tymczasowe pliki w kontenerze (przez API Selenium), aby nie zapchać pamięci Noda przy długich testach.

```java
public static void clearDownloadFolderFromFiles() throws IOException {
    boolean isDocker = testIsRunningInDocker(); // Twoja metoda detekcji

    if (!isDocker) {
        // --- ŚCIEŻKA LOKALNA ---
        // Czyścimy fizyczny folder na dysku komputera
        Path downloadFolder = getDownloadDirectory();
        System.out.println("Tryb LOCAL: Czyszczę folder: " + downloadFolder.toAbsolutePath());

        if (Files.exists(downloadFolder) && Files.isDirectory(downloadFolder)) {
            // Używamy try-with-resources dla strumienia, aby uniknąć wycieków pamięci
            try (var files = Files.list(downloadFolder)) {
                files.forEach(f -> {
                    try {
                        Files.deleteIfExists(f);
                    } catch (IOException e) {
                        // Logujemy błąd, ale nie przerywamy pętli dla innych plików
                        System.err.println("Ostrzeżenie: Nie udało się usunąć pliku: " + f);
                    }
                });
            }
        }
    } else {
        // --- ŚCIEŻKA DOCKER (Selenium 4) ---
        // Czyścimy pliki tymczasowe wewnątrz kontenera Noda
        System.out.println("Tryb DOCKER: Usuwam pliki z sesji przeglądarki.");
        ((HasDownloads) DriverFactory.getDriver()).deleteDownloadableFiles();
    }
    
    System.out.println("Operacja czyszczenia folderu zakończona.");
}
```

### 3. Wstrzykiwanie ścieżki do konfiguracji (Dynamic Path Injection)

Podmieniamy placeholder `${DownloadPath}` w pliku konfiguracyjnym (np. `.properties` lub profil przeglądarki) na faktyczną ścieżkę absolutną.

**Ważne usprawnienie:** Obsługa separatorów systemowych. Windows używa `\`, który w plikach konfiguracyjnych często musi być podwojony `\\` lub zamieniony na `/`.

```java
// 1. Ustalanie ścieżki (Java Path)
Path downloadPath = null;
if (!dockerEnv) {
    // Lokalnie: absolutna ścieżka do folderu w projekcie
    downloadPath = Path.of("DownloadFolder").toAbsolutePath();
    System.out.println("Lokalny folder pobierania: " + downloadPath);
} else {
    // Docker: Ścieżka wewnętrzna (Linux)
    // Tę wartość wstrzykujemy, jeśli test leci w Dockerze, a nie używamy domyślnych ustawień
    downloadPath = Path.of("/home/seluser/Downloads");
}

// 2. Podmiana w pliku (String Replacement)
// download.default_directory=${DownloadPath} <- Linia w pliku
while ((optionLine = bufferedReader.readLine()) != null) {
    if (optionLine.contains("${DownloadPath}")) {
        // Zabezpieczenie przed NullPointerException
        String pathString = (downloadPath != null) ? downloadPath.toString() : "";
        
        // FIX NA WINDOWS: Zamieniamy backslashe na forward slashe, 
        // co jest bezpieczniejsze w plikach konfiguracyjnych (Chrome to zrozumie)
        pathString = pathString.replace("\\", "/");

        optionLine = optionLine.replace("${DownloadPath}", pathString);
    }
    // ... zapisz linię ...
}
```

---