# http

# URI i URL

| Cecha | `URI` (`java.net.URI`) | `URL` (`java.net.URL`) |
| --- | --- | --- |
| **Znaczenie** | Jednolity identyfikator zasobu (ang. *Uniform Resource Identifier*) – identyfikuje zasób w sposób ogólny | Jednolity lokalizator zasobu (ang. *Uniform Resource Locator*) – wskazuje lokalizację zasobu |
| **Zakres** | Może zawierać tylko nazwę (np. `mailto:someone@example.com`) | Zawsze wskazuje pełną lokalizację (np. `http://example.com`) |
| **Często zawiera** | Schemat, ścieżkę, port, zapytania, fragmenty | Schemat, host, port, ścieżkę, zapytania |
| **Sprawdza istnienie zasobu?** | ❌ Nie – to tylko identyfikator | ✅ Może wskazywać na rzeczywisty zasób w sieci lub systemie |
| **Obsługa połączenia** | ❌ Nie pozwala otwierać połączenia | ✅ Można z niego otworzyć połączenie do zasobu (`openConnection()`) |
| **Relacja między nimi** | URI może zostać przekonwertowany na URL (jeśli jest poprawny) | URL można przekonwertować na URI (`toURI()`) |
| **Wyjątki konstrukcyjne** | `URISyntaxException` | `MalformedURLException` |

URI to uniwersalny identyfikator zasobów – używany tam, gdzie nie jest konieczne fizyczne połączenie z zasobem.

URL to lokalizator zasobów – służy do uzyskiwania dostępu do danych w sieci lub systemie.

## Metody HTTP

| Metoda HTTP | Opis |
| --- | --- |
| `GET` | Pobieranie zasobu |
| `POST` | Tworzenie zasobu |
| `PUT` | Nadpisanie zasobu |
| `DELETE` | Usunięcie zasobu |
| `PATCH` | Częściowa aktualizacja |
| `HEAD` | Tylko nagłówki (bez body) |
| `OPTIONS` | Dostępne metody |

# Komunikaty o błędach

🔴 4xx – Błędy po stronie klienta

| Kod | Nazwa | Znaczenie |
| --- | --- | --- |
| 400 | **Bad Request** | Złe żądanie – np. nieprawidłowy format danych |
| 401 | **Unauthorized** | Brak autoryzacji – wymagane logowanie lub token |
| 403 | **Forbidden** | Dostęp zabroniony – użytkownik nie ma uprawnień |
| 404 | **Not Found** | Nie znaleziono – zasób nie istnieje |
| 405 | **Method Not Allowed** | Metoda (np. PUT) niedozwolona dla tego zasobu |
| 408 | **Request Timeout** | Czas oczekiwania na żądanie minął |
| 409 | **Conflict** | Konflikt danych (np. przy modyfikacji tego samego zasobu) |
| 429 | **Too Many Requests** | Zbyt wiele żądań – limit rate limiting został przekroczony |

🔴 5xx – Błędy po stronie serwera

| Kod | Nazwa | Znaczenie |
| --- | --- | --- |
| 500 | **Internal Server Error** | Ogólny błąd serwera |
| 501 | **Not Implemented** | Serwer nie obsługuje żądanej metody |
| 502 | **Bad Gateway** | Serwer pośredniczący dostał złą odpowiedź od innego serwera |
| 503 | **Service Unavailable** | Usługa niedostępna – np. przeciążenie, prace serwisowe |
| 504 | **Gateway Timeout** | Przekroczony czas odpowiedzi od serwera pośredniego |

ℹ️ Przykładowe inne kategorie

| Zakres | Znaczenie |
| --- | --- |
| 1xx | Informacyjne (rzadko używane) |
| 2xx | Sukces (np. 200 OK, 201 Created) |
| 3xx | Przekierowania (np. 301, 302) |

Synch send i Asynch send

Callback