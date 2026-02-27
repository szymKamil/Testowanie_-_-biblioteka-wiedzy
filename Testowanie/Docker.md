# Docker

[Problemy](Docker/Problemy%20.md)

[Komendy](Docker/Komendy%20.md)

Kontener vs obrazy

Obrazy to template dla konetnerów. Zawierają informacje nt. specyfikacji. Kontenery to uruchomione grupy procesów. Możemy mieć wiele kontenerów utworzonych na bazie obrazu. 

## Dockerfile

Typowy minimalny Dockerfile (np. dla aplikacji w Node/Java/Python) składa się z kilku kroków:

- **`FROM`** – wybór bazowego obrazu (np. **`node:20-alpine`**, **`openjdk:17-jdk-slim`**, **`python:3.11`**).
- **`WORKDIR`** – ustawienie katalogu roboczego wewnątrz kontenera.
- **`COPY`** – skopiowanie plików projektu do obrazu.
- **`RUN`** – instalacja zależności (np. **`mvn package`**, **`npm install`**, **`pip install -r requirements.txt`**), tj. uruchomienie komend np. basha.
- **`EXPOSE`** – udokumentowanie portu, na którym słucha aplikacja (np. 8080).
- **`CMD`** lub **`ENTRYPOINT`** – komenda startowa, która rusza aplikację po uruchomieniu kontenera (np. **`["java","-jar","app.jar"]`**).

Podstawowe sekcje:

- **FROM** – wybór bazowego obrazu (np. **`node:20-alpine`**, **`openjdk:17`**, **`python:3.11`**), od którego startujesz budowę own obrazu.
- **WORKDIR** – katalog roboczy wewnątrz kontenera, gdzie będą wykonywane polecenia i kopiowane pliki.
- **COPY** – kopiuje pliki z Twojego katalogu lokalnego do obrazu.
- **RUN** – wykonuje polecenia podczas budowy obrazu, zwykle instalacja zależności (np. **`apt-get install`**, **`npm install`**).
- **ENV** – ustawienie zmiennych środowiskowych, które będą dostępne w kontenerze.
- **EXPOSE** – informuje o porcie, na którym aplikacja będzie nasłuchiwać (dokumentacyjne).
- **CMD** lub **ENTRYPOINT** – komenda uruchamiana po starcie kontenera, np. start serwera aplikacji.

# Compose

Docker Compose to narzędzie i plik **`docker-compose.yml`**, w którym opisujesz *jak uruchomić* kilka kontenerów jako jedną aplikację (np. backend, frontend, baza, Redis).

Zamiast odpalać wszystko ręcznie komendami **`docker run`**, piszesz konfigurację w YAML, a potem robisz jedno **`docker compose up`** i całe środowisko wstaje.

W pliku compose definiujesz:

- **`services:`** – każdy service = konfiguracja jednego kontenera (image, build, porty, zmienne środowiskowe, volumes…).
- **`build:`** – skąd zbudować obraz (ścieżka do katalogu z Dockerfile; Compose sam użyje **`docker build`**).
- **`image:`** – gotowy obraz do pobrania z registry (np. **`postgres:16`**).
- **`ports:`** – mapowanie portów (np. **`"8080:8080"`**).
- **`environment:`** – zmienne środowiskowe (hasła DB, URL‑e, feature flagi).
- **`volumes:`** – trwałe dane (np. katalog z danymi bazy).
- `env_file:`  - podpinamy plik .env w kórym możemy przechowywać dane jak np. login i hasło.

Compose ogarnia też: tworzenie sieci między kontenerami, prostą orkiestrację (zależności, healthchecki), skalowanie ilości replik (**`docker compose up --scale api=3`**).

Sekcje:

- **version** – wersja składni pliku compose (np. **`'3'`**, **`'3.8'`**).
- **services:** – kluczowa sekcja, w której definiujesz poszczególne kontenery/usługi. Każda usługa ma swoją nazwę i pod nią konfigurację, np. **`web`**, **`db`**.

Dla każdej usługi w **services** możesz mieć:

- **build:** – ścieżka do katalogu z Dockerfile lub więcej parametrów build (np. **`context`** i **`dockerfile`**).
- **image:** – nazwa obrazu do uruchomienia (jeśli nie budujesz samodzielnie).
- container_name: - Warto jednak pamiętać, że ustawienie **`container_name`** jest przydatne, gdy chcemy mieć stałą, przewidywalną nazwę kontenera, ale może ograniczać elastyczność np. przy skalowaniu wielu instancji tej samej usługi (bo nazwa musi być unikalna).
- **ports:** – mapowanie portów hosta na kontener (**`"8080:80"`**).
- **environment:** – zmienne środowiskowe przekazywane do kontenera (np. hasła, ustawienia).
- **volumes:** – mapowanie katalogów lub wolumenów z hosta do kontenera (np. **`./data:/var/lib/mysql`**).
- **depends_on:** – określa zależności między usługami (np. **`web`** zależy od **`db`**).
- **networks:** – określanie sieci, do których usługa ma być podłączona.

Dodatkowo na poziomie pliku są sekcje:

- **volumes:** – definicje trwałych wolumenów, które mogą być współdzielone przez usługi.
- **networks:** – definicje sieci Docker do separacji i komunikacji między kontenerami.

# Kopiowanie plików z kontenera - cp

### **Kopiowanie katalogu z kontenera → host**

```bash
docker cp <container_name_or_id>:/sciezka/wewnatrz/kontenera /sciezka/na/hoscie
Przykład:
docker cp my-container:/var/lib/mysql ./backup_mysql

```

Działa nawet wtedy, gdy kontener jest *wystartowany* lub *zatrzymany*.

---

# **Backup całego systemu plików kontenera (tar)**

Możesz utworzyć archiwum TAR bezpośrednio z wnętrza kontenera.

### **Wejście do kontenera**

```bash
docker exec -it <container> bash
```

Następnie:

```bash
tar czvf /tmp/backup.tar.gz /sciezka/do/danych
```

Potem na hoście:

```bash
docker cp <container>:/tmp/backup.tar.gz .
```

---

# **3. Tworzenie obrazu z kontenera (zachowanie stanu dysku)**

**Uwaga:** to nie zapisze danych poza warstwą writable, ale jeśli dane są wewnątrz kontenera — tak, uratujesz je.

```bash
docker commit <container_id> my-backup-image

```

Potem możesz uruchomić nowy kontener:

```bash
docker run -it my-backup-image
```

**Nie jest to idealne dla danych aplikacyjnych**, ale stanowi awaryjną strategię, jeśli nie masz dostępu do ścieżek.

---

# **4. Podłączenie volume "na żywo" (raczej awaryjne)**

Możesz podłączyć zewnętrzny volume i przenieść na niego dane:

```bash
docker volume create rescue-volume
```

Start pomocniczego kontenera:

```bash
docker run -it --name rescue -v rescue-volume:/data ubuntu
```

W źródłowym kontenerze:

```bash
docker cp <container>:/sciezka/z/danymi <rescue_container>:/data
```