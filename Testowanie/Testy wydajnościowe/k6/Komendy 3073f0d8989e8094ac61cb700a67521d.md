# Komendy

| Komenda | Opis |
| --- | --- |
| `k6 run <nazwa pliku>` | Lokalne uruchomienie ksryptu |
| `cat .\my-first-test.ts | docker run --rm -i grafana/k6 run -` |  |
| `k6 cloud login -token <API_TOKEN> -stack <STACK_SLUG>` |  |
| `k6 help` | Wyświetla listę dostępnych komend. |

| Command | Description | Usage |
| --- | --- | --- |
| `help` | Displays all possible commands | `k6 help` |
| `run` | Executes a k6 script | `k6 run test.js` |
| `version` | Displays installed k6 version | `k6 version` |

Flagi to ustawienia dodawane do poleceń w celu zmiany części konfiguracji.

| Flag | Opis | Użycie |
| --- | --- | --- |
| `--help` | Displays possible flags for the given command | `k6 run --help` |
| `--vus` or `-u` | Sets number of virtual users | `k6 run test.js --vus 10 --duration 30s` |
| `--duration` | Sets the duration of the test | `k6 run test.js --duration 10m` |
| `--iterations` or `-i` | Instructs k6 to iterate the default function a number of times | `k6 run test.js -i 3` |
| `-e` | Sets an environment variable to pass to the script | `k6 run test.js -e DOMAIN=test.k6.io` |

`cat .\my-first-test.ts`

- wypisuje zawartość pliku na stdout
- działa jak: „pobierz kod testu jako czysty tekst”.

### 2. `|`

- pipe – przekazuje wynik poprzedniej komendy jako wejście do następnej.

### 3. `docker run --rm -i grafana/k6 run -`

| Element | Znaczenie |
| --- | --- |
| `docker run` | uruchom kontener |
| `grafana/k6` | oficjalny obraz k6 |
| `--rm` | usuń kontener po zakończeniu |
| `-i` | czytaj dane ze stdin |
| `run -` | uruchom skrypt przekazany na stdin (`-` = zamiast pliku) |

---

## ✅ Efekt

➡ k6 dostaje kod testu bezpośrednio z pipe’a

➡ nie potrzeba `-v` ani kopiowania plików

➡ jednorazowe, „czyste” uruchomienie