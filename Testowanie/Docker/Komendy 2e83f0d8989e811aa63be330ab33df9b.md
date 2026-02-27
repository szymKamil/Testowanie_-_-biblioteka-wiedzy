# Komendy

[https://docs.docker.com/reference/cli/docker/](https://docs.docker.com/reference/cli/docker/)

[https://www.jenkins.io/doc/book/pipeline/docker/](https://www.jenkins.io/doc/book/pipeline/docker/)

[https://docs.docker.com/reference/cli/docker/buildx/build/](https://docs.docker.com/reference/cli/docker/buildx/build/)

| Komenda (Polecenie)          | Opis (Co robi?)                                                                                         | Przykład użycia                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Zarządzanie Kontenerami**  |                                                                                                         |                                                                                       |
| `docker run [opcje] [obraz]` | Uruchamia nowy kontener na podstawie podanego obrazu. Jest to najważniejsza komenda.                    | `docker run -d -p 4444:4444 --name selenium-chrome selenium/standalone-chrome:latest` |
| `docker ps`                  | Wyświetla listę wszystkich **aktualnie uruchomionych** kontenerów.                                      | `docker ps`                                                                           |
| `docker ps -a`               | Wyświetla listę **wszystkich** kontenerów, również tych zatrzymanych (`-a` od _all_).                   | `docker ps -a`                                                                        |
| `docker stop [nazwa/ID]`     | Zatrzymuje działający kontener. Kontener wciąż istnieje, ale jest wyłączony.                            | `docker stop selenium-chrome`                                                         |
| `docker start [nazwa/ID]`    | Uruchamia wcześniej zatrzymany kontener.                                                                | `docker start selenium-chrome`                                                        |
| `docker rm [nazwa/ID]`       | Usuwa **zatrzymany** kontener. Aby usunąć działający, trzeba go najpierw zatrzymać lub użyć flagi `-f`. | `docker rm selenium-chrome`                                                           |
| `docker info`                | Wyświetla informacje o systemie dockera.                                                                |                                                                                       |

| `docker network disconnect -f test-env_grid jenkins-docker`
| Odłączenie kontenera od sieci <nazwa> | |
| **Zarządzanie Obrazami** | | |
| `docker images` | Wyświetla listę wszystkich obrazów pobranych na Twoją maszynę. | `docker images` |
| `docker image ls` | Lista obrazów. | |
| `docker pull [obraz]` | Pobiera najnowszą wersję obrazu z repozytorium (domyślnie z Docker Hub). | `docker pull selenium/standalone-chrome:latest` |
| `docker rmi [nazwa/ID]` | Usuwa obraz z Twojej maszyny. Nie można usunąć obrazu, jeśli jest używany przez jakikolwiek kontener (nawet zatrzymany). | `docker rmi selenium/standalone-chrome` |
| `docker network connect <nazwa_sieci> <nazwa_kontenera>`
| Dodanie konetenera do sieci. | `docker network connect selenium-network selenium-hub`
|
| `docker network create my_network`
| Tworzenie sieci. | |
| **Diagnostyka i Interakcja** | | |
| `docker logs [nazwa/ID]` | Wyświetla logi (standardowe wyjście) z działania kontenera. Kluczowe do diagnozowania problemów. | `docker logs -f selenium-chrome` |
| `docker exec -it [nazwa/ID] sh` | Uruchamia interaktywną powłokę (terminal) wewnątrz **działającego** kontenera. Niezastąpione do zaawansowanego debugowania. | `docker exec -it selenium-chrome sh` |
| `docker inspect [nazwa/ID]` | Wyświetla szczegółowe informacje o kontenerze w formacie JSON, w tym jego konfigurację sieciową i wolumeny. | `docker inspect selenium-chrome` |
| `docker network ls` | Wyświetla istniejące sieci | |
| | | |
| **Czyszczenie Środowiska** | | |
| `docker rm $(docker ps -aq)` | **Komenda-pro-tip:** Usuwa **wszystkie** kontenery (zarówno działające, jak i zatrzymane) z Twojego systemu[. | `docker rm -f $(docker ps -aq)` (z flagą `-f` aby wymusić usunięcie działających) |
| `docker image prune` | Usuwa "zawieszone" obrazy (tzw. _dangling images_), które nie są już powiązane z żadnym tagiem. Oszczędza miejsce na dysku. | `docker image prune` |
| **Compose** | | |
| `docker compose -f selenium_grid_env.yml up` | Uruchamia kopozyty z kontenerami dokera. | `docker compose -f selenium_grid_env.yml up` |
| `docker compose -f selenium_grid_env.yml down --rmi all --volumes --remove-orphans`
| Usunięcie całego katalogu compose. | |
| **Dockerfile** | | |
| `docker build -f <nazwa_dockerfile>` | | |
| | | |

| Flaga                 | Opis                                                                 |
| --------------------- | -------------------------------------------------------------------- |
| `-d`                  | Uruchamia kontener w tle (detached).                                 |
| `-p <host:container>` | Mapowanie portów, np. `-p 8080:80`.                                  |
| `-P`                  | Publikuje wszystkie EXPOSE na losowe porty hosta.                    |
| `-e KEY=VALUE`        | Ustawia zmienne środowiskowe w kontenerze.                           |
| `--env-file <plik>`   | Wczytuje wiele zmiennych z pliku `.env`.                             |
| `-v <host:container>` | Montuje wolumen, bind-mount lub named volume.                        |
| `--rm`                | Usuwa kontener po zakończeniu działania.                             |
| `--name <nazwa>`      | Nadaje nazwę kontenerowi.                                            |
| `--network <nazwa>`   | Podłącza kontener do konkretnej sieci.                               |
| `--restart <policy>`  | Polityka restartu (`no`, `always`, `on-failure`).                    |
| `-it`                 | Interaktywny terminal (`-i` + `-t`).                                 |
| `--entrypoint <cmd>`  | Nadpisuje ENTRYPOINT z obrazu.                                       |
| `--privileged`        | Podnosi uprawnienia kontenera (uważnie!).                            |
| `--cpus n`            | Limit CPU, np. `--cpus="0.5"`.                                       |
| `--memory <size>`     | Limit RAM, np. `--memory=512m`.                                      |
| -t <tag>              | Nadaje tag obrazowi, np. -t myapp:latest.                            |
| -f <Dockerfile>       | Wskazuje inny Dockerfile.                                            |
| --no-cache            | Buduje bez użycia cache.                                             |
| --pull                | Wymusza pobranie najnowszej wersji bazowego obrazu.                  |
| --build-arg KEY=VALUE | Przekazuje argumenty ARG do builda.                                  |
| -a                    | Pokazuje wszystkie kontenery (również zatrzymane).                   |
| -q                    | Wyświetla tylko ID.                                                  |
| --filter              | Filtrowanie, np. --filter "status=exited".                           |
| `-v`                  | Mountowanie folderu. Lokalny folder staje się widoczny w kontenerze. |

# Mapowanie portów

Domyślnie kontenery są izolowane od hosta. Tworząc kontenery trzeba nadać im port flagą `-p`.

# Mount types

**1. Bind mount**

Montuje **konkretny folder lub plik z hosta** do kontenera.

✔️ pełna kontrola, idealne do dev

❗ ryzyko popsucia danych w kontenerze (piszesz bezpośrednio na hosta)

**Przykład:**

```bash
docker run -v /host/logs:/app/logs nginx
```

**Bind mount to jedynie „podpięcie” tego folderu/plików do środka kontenera.**

### 📦 Jak wygląda w kontenerze?

- Podany katalog hosta jest widoczny jako **katalog w kontenerze**.
- Działają jak _shared folder_ — każda zmiana jednej strony jest od razu widoczna po drugiej.

### 📌 Kto zarządza?

✔️ **Ty (host)** — Docker niczego nie kontroluje, jedynie montuje.

❌ Docker nie robi backupów, nie czyści, nie zarządza uprawnieniami.

---

**2. Volume**

Zarządzane przez Dockera – przechowywane w `/var/lib/docker/volumes`.

✔️ idealne do produkcji

✔️ bezpieczne, Docker sam zarządza lokalizacją

✔️ łatwe backupy

❗ nie są widoczne jako normalne foldery hosta (chyba że je sprawdzisz)
📍 Gdzie fizycznie leżą dane?

➡️ W specjalnym katalogu Dockera:

**Przykład:**

```bash
docker run -v mydata:/var/lib/mysql mysql
```

**Uwaga:** Docker _sam_ decyduje o fizycznej lokalizacji i zarządzaniu.

### 📦 Jak wygląda w kontenerze?

- Podpięte tam, gdzie wskażesz, np. `/var/lib/mysql`.
- Zmiany są trwałe i nie znikają po usunięciu kontenera.

### 📌 Kto zarządza?

✔️ **Docker** – tworzy, przechowuje, czyści, przypisuje uprawnienia.

✔️ Idealne do baz danych, persistent storage.

---

**3. tmpfs mount**

### 📍 Gdzie fizycznie leżą dane?

➡️ **W pamięci RAM hosta.**

Nie zapisuje się **NIC na dysk**.

To tak jakby kontener miał katalog „ultra szybki”, ale ulotny.

Dane trzymane **w RAM** – znikają po zatrzymaniu kontenera.

✔️ bardzo szybkie

✔️ dobre dla danych tymczasowych

❗ brak trwałości

**Przykład:**

```bash
docker run --tmpfs /app/cache nginx
```

| Typ            | Gdzie są dane?                      | Trwałość        | Kto zarządza? | Zastosowania              |
| -------------- | ----------------------------------- | --------------- | ------------- | ------------------------- |
| **Bind mount** | Dokładnie w katalogu hosta          | trwałe          | użytkownik    | dev, logi, hot-reload     |
| **Volume**     | `/var/lib/docker/volumes/.../_data` | trwałe          | Docker        | produkcja, bazy danych    |
| **tmpfs**      | RAM                                 | znikają po stop | OS            | cache, secrets, temp data |

```powershell
docker network ls
docker network inspect grid-network

docker ps -a --filter "ancestor=selenium/hub" --format "Name: {{.Names}}"
```

Zmiana sieci:

`docker inspect <jenkins-container-name-or-id> | Select-String "NetworkMode"`

```powershell
docker network create grid-network # Utwórz sieć, jeśli jeszcze nie istnieje
docker network connect grid-network <jenkins-container-name-or-id>

//Weryfikacja połaczenia:
docker network inspect grid-network

```

docker build -t jenkins-docker -f jenkins.Dockerfile .
