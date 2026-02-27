# API

**API (Application Programming Interface)** to zestaw zasad, reguł i narzędzi, które umożliwiają komunikację między różnymi programami lub systemami.

Można je traktować jako **most** łączący aplikacje, dzięki któremu mogą wymieniać się danymi i funkcjonalnościami.

**Jak to działa?**

- Aplikacja **A** wysyła żądanie do API.
- API przekazuje to żądanie do aplikacji **B** (lub systemu).
- Aplikacja **B** zwraca odpowiedź poprzez API.

Czyli **API jest pośrednikiem** pomiędzy dwoma aplikacjami.

**Przykłady użycia:**

- **Mapy Google** – aplikacje mogą korzystać z API Google Maps, aby wyświetlać mapy i wskazówki dojazdu.
- **Płatności online** – sklepy internetowe korzystają z API np. PayU, Stripe czy PayPal, aby obsługiwać transakcje.
- **Pogoda** – aplikacja pogodowa korzysta z API serwisów meteorologicznych, aby pobierać aktualne dane o temperaturze i prognozie.

**Cechy API**

- Umożliwia **współdzielenie funkcjonalności** między aplikacjami.
- Może być **otwarte (publiczne)** – dostępne dla wszystkich, lub **zamknięte (prywatne)** – dostępne tylko wewnątrz organizacji.
- API może być zrealizowane w różnych stylach, np. **REST**, **SOAP**, **GraphQL**.

---

✅ **Podsumowanie:**

API to interfejs, który pozwala programom **dogadywać się ze sobą** bez konieczności zaglądania do ich wewnętrznego kodu.

# Style komunikacji API

## 🌐 REST (Representational State Transfer)

- **Czym jest**: Styl architektoniczny oparty o HTTP. Najczęściej spotykany w API webowych.
- **Zasada**: Operujesz na zasobach (`/users`, `/products`) przy pomocy standardowych metod HTTP (`GET`, `POST`, `PUT`, `DELETE`).
- **Zalety**:
    - Prosty i intuicyjny (używa HTTP).
    - Łatwy w debugowaniu (Postman, curl, przeglądarka).
    - Szerokie wsparcie w narzędziach i bibliotekach.
- **Wady**:
    - Nadmiarowe dane (czasem dostajesz więcej, niż chcesz).
    - Brak standardowego mechanizmu „subskrypcji” (tylko request-response).

---

## 📊 GraphQL

- **Czym jest**: Język zapytań do API stworzony przez Facebooka.
- **Zasada**: Klient sam określa, jakie dane chce otrzymać w odpowiedzi (np. z kilku tabel w jednym zapytaniu).
- **Zalety**:
    - Unikasz nadmiarowych danych (pobierasz dokładnie to, czego potrzebujesz).
    - Jedno zapytanie może zastąpić kilka endpointów REST.
- **Wady**:
    - Większa złożoność implementacji po stronie serwera.
    - Trudniejsze cachowanie na poziomie HTTP (bo wszystkie zapytania idą pod jeden endpoint, np. `/graphql`).

---

## 🔄 WebSockets

- **Czym jest**: Protokół dwukierunkowej komunikacji w czasie rzeczywistym nad TCP.
- **Zasada**: Po nawiązaniu połączenia klient i serwer mogą wysyłać wiadomości w obie strony (bez czekania na request).
- **Zalety**:
    - Idealne do aplikacji realtime (chaty, giełdy, multiplayer).
    - Szybki i oszczędny (brak narzutu wielu requestów HTTP).
- **Wady**:
    - Większa złożoność (utrzymanie połączenia, skalowanie).
    - Nie zawsze działa w infrastrukturze z restrykcyjnymi firewallami/proxy.

---

## 📩 Webhooks

- **Czym jest**: Mechanizm „push” oparty o HTTP. Serwer *sam wysyła requesty* do aplikacji klienta, gdy coś się wydarzy.
- **Zasada**: Klient rejestruje URL (endpoint) → serwer wywołuje go w razie zdarzenia (np. płatność zakończona).
- **Zalety**:
    - Proste do wdrożenia.
    - Oszczędza polling (klient nie musi co sekundę pytać „czy już?”).
- **Wady**:
    - Klient musi mieć publiczny endpoint dostępny z internetu.
    - Trudności z bezpieczeństwem (trzeba weryfikować podpisy/sekrety).

---

## 🧩 SOAP (Simple Object Access Protocol)

- **Czym jest**: Starszy protokół komunikacji oparty o XML, zwykle nad HTTP, ale może działać też np. przez SMTP.
- **Zasada**: Wymiana ściśle zdefiniowanych komunikatów XML zgodnie z WSDL (Web Services Description Language).
- **Zalety**:
    - Silne typowanie i formalne kontrakty (dobrze pasuje w środowiskach enterprise).
    - Obsługuje transakcje, bezpieczeństwo (WS-Security), routing.
- **Wady**:
    - Bardzo rozbudowany, ciężki i mało elastyczny.
    - XML zamiast JSON (więcej narzutu).

---

## ⚡ gRPC (Google Remote Procedure Call)

- **Czym jest**: Framework RPC od Google, oparty o HTTP/2 i Protocol Buffers (protobuf).
- **Zasada**: Definiujesz kontrakty usług (`service`, `method`) w plikach `.proto`, a gRPC generuje klienta i serwer w różnych językach.
- **Zalety**:
    - Bardzo szybki i lekki (protobuf).
    - Obsługuje streaming dwukierunkowy.
    - Łatwe generowanie klientów w wielu językach.
- **Wady**:
    - Trudniejszy do testowania ręcznego (nie wystarczy przeglądarka czy Postman).
    - Wymaga HTTP/2 i znajomości protobuf.

---

## 📡 MQTT (Message Queuing Telemetry Transport)

- **Czym jest**: Bardzo lekki protokół publikacji/subskrypcji (publish/subscribe), zaprojektowany dla IoT.
- **Zasada**: Urządzenia publikują wiadomości do „brokera”, inni subskrybują odpowiednie tematy (`topic`).
- **Zalety**:
    - Minimalne zużycie pasma (działa nawet przy słabym internecie).
    - Idealny dla IoT, sensorów, małych urządzeń.
- **Wady**:
    - Wymaga dodatkowego serwera (brokera).
    - Nie nadaje się jako zamiennik klasycznego API webowego.

# Endpointy w API

**Endpoint** to konkretny adres URL w API, który wskazuje na **zasób** (np. użytkownik, zadanie, produkt) lub **usługę** (np. logowanie, pobieranie danych pogodowych).

To właśnie na ten adres wysyłane jest żądanie HTTP (np. **GET, POST, PUT, DELETE**) w celu pobrania lub modyfikacji danych.

**Jak to działa?**

- Endpoint to **punkt dostępu** do funkcji API.
- Każdy endpoint jest częścią większej struktury API.
- Endpoint zwykle odpowiada jednej konkretnej operacji lub zbiorowi danych.

**Przykłady**

- `https://myurl.com/tasks` → zwraca wszystkie zadania.
- `https://myurl.com/tasks/1` → zwraca tylko zadanie o **ID = 1**.
- `https://api.weather.com/city/London` → zwraca prognozę pogody dla Londynu.

**Cechy endpointów**

- Organizowane według **typu zasobu** (np. `/users`, `/products`).
- Mają **przewidywalne wzorce**, co ułatwia korzystanie z API.
- Mogą wspierać różne metody HTTP:
    
    # Najczęściej używane metody
    
    - **GET** – pobiera dane (np. lista użytkowników, szczegóły produktu).
    - **POST** – tworzy nowy zasób (np. dodanie nowego użytkownika).
    - **PUT** – aktualizuje istniejący zasób **w całości**.
    - **PATCH** – aktualizuje istniejący zasób **częściowo** (np. tylko nazwę).
    - **DELETE** – usuwa wskazany zasób.
    
    ## Inne, rzadziej spotykane
    
    - **HEAD** – zwraca tylko nagłówki odpowiedzi (bez treści).
    - **OPTIONS** – zwraca informacje o tym, jakie metody są dozwolone dla danego endpointu.
    - **TRACE** – służy do debugowania, odsyła otrzymane żądanie w odpowiedzi.
    - **CONNECT** – nawiązuje tunel do serwera (używane np. w HTTPS, proxy).

---

# Kody HTTP

| Kod | Nazwa (EN) | Co to znaczy / kiedy się pojawia |
| --- | --- | --- |
| 100 | Continue | Klient może kontynuować wysyłanie treści żądania (używane przy dużych payloadach). |
| 101 | Switching Protocols | Serwer akceptuje przełączenie protokołu (np. na WebSocket). |
| 102 | Processing (WebDAV) | Serwer przyjął żądanie i przetwarza je (dla długich operacji WebDAV). |
| 103 | Early Hints | Wczesne wskazówki (np. `Link: preload`) — przydatne do preładowania zasobów. |

| Kod | Nazwa | Zastosowanie |
| --- | --- | --- |
| 200 | OK | Standardowy sukces (GET, PUT w wielu przypadkach). |
| 201 | Created | Zasób utworzony (zwykle po POST). |
| 202 | Accepted | Przyjęto do przetworzenia asynchronicznie. |
| 203 | Non-Authoritative Information | Zasób zwrócony z innego źródła (rzadko używane). |
| 204 | No Content | Sukces bez treści (np. DELETE, lub PUT bez odpowiedzi). |
| 205 | Reset Content | Prośba o zresetowanie formularza/stanów po stronie klienta. |
| 206 | Partial Content | Odpowiedź częściowa (Range requests). |
| 207 | Multi-Status (WebDAV) | Wyniki wieloczęściowe (WebDAV). |
| 208 | Already Reported (WebDAV) | Już zgłoszono (WebDAV, kolejne odpowiedzi wieloczęściowe). |
| 226 | IM Used | Zastosowano delta encoding (RFC 3229). |

| Kod | Nazwa | Zastosowanie |
| --- | --- | --- |
| 300 | Multiple Choices | Kilka możliwych zasobów (rzadko używane). |
| 301 | Moved Permanently | Stałe przekierowanie — zmień URL. |
| 302 | Found | Tymczasowe przekierowanie (historycznie „Moved Temporarily”). |
| 303 | See Other | Przekieruj klienta, użyj GET na innym URL (po POST). |
| 304 | Not Modified | Zasób nieuaktualniony — klient może użyć cache. |
| 305 | Use Proxy | (Deprecated) — użyj proxy. |
| 306 | (Unused) | Kiedyś wykorzystywane, teraz nieużywane. |
| 307 | Temporary Redirect | Tymczasowe przekierowanie, metoda nie zmienia się. |
| 308 | Permanent Redirect | Stałe przekierowanie, metoda nie zmienia się. |

| Kod | Nazwa | Zastosowanie |
| --- | --- | --- |
| 400 | Bad Request | Niepoprawne żądanie / walidacja. |
| 401 | Unauthorized | Brak lub złe uwierzytelnienie (nieautoryzowany). |
| 402 | Payment Required | Zarezerwowany / rzadko używany (płatności). |
| 403 | Forbidden | Brak uprawnień mimo uwierzytelnienia. |
| 404 | Not Found | Zasób nie istnieje. |
| 405 | Method Not Allowed | Metoda HTTP nie jest dozwolona dla zasobu. |
| 406 | Not Acceptable | Negocjacja treści nie pozwala na odpowiedź. |
| 407 | Proxy Authentication Required | Autoryzacja proxy wymagana. |
| 408 | Request Timeout | Żądanie przekroczyło czas. |
| 409 | Conflict | Konflikt stanu (np. wersjonowanie zasobów). |
| 410 | Gone | Zasób usunięty trwale. |
| 411 | Length Required | Brak nagłówka Content-Length. |
| 412 | Precondition Failed | Warunki (If-*) nie spełnione. |
| 413 | Payload Too Large | Treść żądania za duża. |
| 414 | URI Too Long | URI/URL zbyt długi. |
| 415 | Unsupported Media Type | Typ treści nieobsługiwany. |
| 416 | Range Not Satisfiable | Żądanie zakresu nie do spełnienia. |
| 417 | Expectation Failed | Nagłówek Expect nie może być spełniony. |
| 418 | I'm a teapot | (RFC 2324 — żart aprillowoy; czasem spotykany). |
| 421 | Misdirected Request | Żądanie skierowane do serwera niezdolnego do obsługi. |
| 422 | Unprocessable Entity (WebDAV) | Walidacja semantyczna nie przeszła. |
| 423 | Locked (WebDAV) | Zasób zablokowany. |
| 424 | Failed Dependency (WebDAV) | Zależność wcześniejsza nie powiodła się. |
| 425 | Too Early | Serwer odrzuca powtórne lub zbyt wczesne przetworzenie. |
| 426 | Upgrade Required | Wymagana inna wersja/protokół (Upgrade). |
| 428 | Precondition Required | Wymagana precondition (zapobiega race conditions). |
| 429 | Too Many Requests | Limit rate-limit przekroczony. |
| 431 | Request Header Fields Too Large | Nagłówki żądania zbyt duże. |
| 451 | Unavailable For Legal Reasons | Niedostępne z powodów prawnych (np. censorship). |

| Kod | Nazwa | Zastosowanie |
| --- | --- | --- |
| 500 | Internal Server Error | Ogólny błąd serwera. |
| 501 | Not Implemented | Serwer nie obsługuje funkcji wymaganej. |
| 502 | Bad Gateway | Serwer działa jako bramka i otrzymał błędną odpowiedź. |
| 503 | Service Unavailable | Serwis tymczasowo niedostępny (maintenance / overload). |
| 504 | Gateway Timeout | Bramka/podmiot upstream przekroczył czas oczekiwania. |
| 505 | HTTP Version Not Supported | Wersja HTTP nie obsługiwana. |
| 506 | Variant Also Negotiates | Problem z negotiation (negotiation loop). |
| 507 | Insufficient Storage (WebDAV) | Brak miejsca/zasobów do wykonania żądania. |
| 508 | Loop Detected (WebDAV) | Pętla wykryta w przetwarzaniu. |
| 510 | Not Extended | Oczekiwane rozszerzenia nie dostarczone. |
| 511 | Network Authentication Required | Wymagana autoryzacja sieciowa (np. captive portal). |

**Dodatkowe uwagi praktyczne**

- **Specyfikacja vs. praktyka**: powyższe to *konwencje zdefiniowane w RFC* i rozszerzeniach (WebDAV, itp.). Technicznie możesz zwrócić dowolny numer, ale to **łamanie konwencji** i powoduje problemy z interoperacyjnością.
- **1xx rzadko w API REST**: Informacyjne (1xx) rzadko są widziane w API opartych na REST — częściej w niskopoziomowych implementacjach HTTP, WebSocket handshake czy przy obsłudze dużych uploadów. 103 (Early Hints) zyskuje zastosowanie przy optymalizacji ładowania stron (preload).

# Protokół – Host – Ciało (HTTP)

**1. Protokół**

- Określa sposób komunikacji: `http://` (port 80), `https://` (port 443).

**2. Host**

- Domena/adres IP serwera.
- W URL: `https://example.com/users` → `example.com`.
- W nagłówku: `Host: example.com`.

**3. Ciało (body)**

- Dane przesyłane w żądaniu/odpowiedzi.
- Występuje głównie w `POST/PUT/PATCH`.
- Format: JSON, XML, pliki, HTML.

**Struktura komunikatu HTTP**

1. Linia startowa (np. `GET /users HTTP/1.1` albo `HTTP/1.1 200 OK`).
2. Nagłówki (`Host`, `Content-Type`).
3. Ciało (opcjonalne, np. dane JSON).
4. 

**Query parameters** to część adresu URL, która służy do przekazywania dodatkowych danych w żądaniu HTTP.

Składnia (syntax)

- Parametry zapytań pojawiają się po znaku **`?`** w URL.
- Kilka parametrów łączy się znakiem **`&`**.
- Każdy parametr to **`klucz=wartość`**.

```bash
https://example.com/search?query=java&sort=asc&page=2
```

- `?query=java` → pierwszy parametr (`query=java`)
- `&sort=asc` → drugi parametr (`sort=asc`)
- `&page=2` → trzeci parametr (`page=2`)

# 🔐 Uwierzytelnianie i autoryzacja (Authentication vs Authorization)

- **Uwierzytelnianie (Authentication)**
    
    – proces potwierdzania **kim jesteś**.
    
    – przykłady: login/hasło, token, certyfikat.
    
- **Autoryzacja (Authorization)**
    
    – proces sprawdzania **co możesz zrobić**.
    
    – przykłady: użytkownik „admin” może usunąć dane, zwykły user – nie.
    

**Popularne mechanizmy:**

- **Basic Auth** – login i hasło zakodowane w Base64, np.
    
    `Authorization: Basic dXNlcjpwYXNzd29yZA==`
    
    ➝ proste, ale niebezpieczne bez HTTPS.
    
- **Bearer Token (JWT)** – klient wysyła token JWT w nagłówku:
    
    `Authorization: Bearer <token>`
    
    ➝ popularne w API REST, łatwe do użycia w testach.
    
- **OAuth 2.0** – standard autoryzacji z „tokenami dostępu”.
    
    ➝ często spotykany w integracjach (Google, Facebook, PayPal).
    

---

# 📑 Nagłówki HTTP – praktyczne

**Najczęściej używane nagłówki:**

- `Content-Type` – typ danych w body (np. `application/json`).
- `Accept` – format danych, jakie klient akceptuje (np. `Accept: application/xml`).
- `Authorization` – dane uwierzytelniające (`Bearer`, `Basic`).
- `User-Agent` – identyfikator klienta (np. przeglądarka, narzędzie testowe).
- `Host` – adres hosta (np. `Host: api.example.com`).
- `Cache-Control` – kontrola cache (np. `no-cache`, `max-age=60`).
- `ETag` – identyfikator wersji zasobu, przydatny do sprawdzania czy coś się zmieniło.
- `Location` – nagłówek w odpowiedzi 201 lub 3xx – wskazuje nowy adres zasobu.

**Przykład requesta z nagłówkami:**

```
POST /login HTTP/1.1
Host: api.example.com
Content-Type: application/json
Accept: application/json
Authorization: Bearer eyJhbGciOi...
User-Agent: PostmanRuntime/7.39.0

{
  "username": "test",
  "password": "secret"
}

```

---

# 📦 Formaty danych w API

**1. JSON (JavaScript Object Notation)**

- Najpopularniejszy format.
- Lekki, łatwy do odczytu przez człowieka i maszynę.

```json
{
  "id": 1,
  "name": "Alice",
  "roles": ["admin", "editor"]
}
```

**2. XML (Extensible Markup Language)**

- Częsty w SOAP, starszych API.
- Cięższy, ale bardziej formalny.

```xml
<User>
  <Id>1</Id>
  <Name>Alice</Name>
  <Roles>
    <Role>admin</Role>
    <Role>editor</Role>
  </Roles>
</User>
```

**3. Form Data (multipart/form-data)**

- Do wysyłania plików, formularzy.

```
POST /upload
Content-Type: multipart/form-data; boundary=----xyz

------xyz
Content-Disposition: form-data; name="file"; filename="image.png"
Content-Type: image/png

<binary data>
------xyz--

```

**4. x-www-form-urlencoded**

- Klasyczny format formularzy HTML.

```
POST /login
Content-Type: application/x-www-form-urlencoded

username=test&password=secret
```