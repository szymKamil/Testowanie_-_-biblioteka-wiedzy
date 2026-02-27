# Testy wydajnościowe

[Jmetter](Testy%20wydajno%C5%9Bciowe/Jmetter%203043f0d8989e8096b6affb6a8b6e2435.md)

[k6](Testy%20wydajno%C5%9Bciowe/k6%203043f0d8989e8045b78bfb853bd024af.md)

![image.png](Testy%20wydajno%C5%9Bciowe/image.png)

# Testy Wydajnościowe – Kompendium Mid-Level

> Dokument obejmuje teorię i praktykę potrzebną na rozmowę kwalifikacyjną i codzienną pracę na poziomie mid.
> 

---

## 1. Wielka Czwórka: Typy testów wydajnościowych

| Typ testu | Cel | Metafora |
| --- | --- | --- |
| **Load Testing** | Sprawdzenie, jak system radzi sobie z **oczekiwanym** ruchem (np. 1000 użytkowników naraz). | Standardowy dzień w restauracji przy pełnych stolikach. |
| **Stress Testing** | Szukanie punktu, w którym system **pęknie**. Sprawdzamy, co się stanie przy 5× większym ruchu niż norma. | Wpuszczasz 500 osób do małej kawiarni i patrzysz, czy najpierw padnie ekspres, czy kelner. |
| **Soak (Endurance) Testing** | Sprawdzanie stabilności w **długim czasie** (np. 24h pod stałym obciążeniem). Wykrywa wycieki pamięci (memory leaks). | Czy restauracja po 3 dniach bez przerwy nadal ma czyste talerze i energię? |
| **Spike Testing** | Nagły, drastyczny skok ruchu i równie nagły spadek. | „Kup teraz" podczas Black Friday lub wrzucenie linku na główną stronę Reddita. |

---

## 2. Kluczowe metryki

### Response Time (Czas odpowiedzi)

**Nigdy nie używaj średniej (Average)!** Średnia kłamie. Jeśli 9 osób dostanie odpowiedź w 1s, a jedna w 11s, średnia to 2s – ta jedna osoba jest wściekła, a Ty myślisz, że jest OK.

**Percentyle (P90, P95, P99)** to Złoty Standard. P95 = 500ms oznacza, że 95% użytkowników czekało pół sekundy lub krócej. Te 5% to właśnie „ogonek" (tail latency), który boli najbardziej w produkcji.

### Throughput (Przepustowość)

Mierzona w **RPS** (Requests Per Second) lub **TPS** (Transactions Per Second). Mówi, ile pracy system jest w stanie wykonać w jednostce czasu.

### Error Rate (Wskaźnik błędów)

Procent żądań zakończonych kodem innym niż 2xx (np. 500, 504). W zdrowym teście Load powinien wynosić **0%**.

### Saturation (Nasycenie)

Wykorzystanie zasobów: CPU, RAM, I/O dysku, przepustowość sieci. Jeśli CPU dobija do 100%, żadna optymalizacja kodu nie pomoże – potrzebujesz więcej mocy lub skalowania poziomego.

### Apdex (Application Performance Index)

Standard przemysłowy mierzący satysfakcję użytkownika. Dzieli odpowiedzi na trzy grupy:

1. **Satisfied** (np. < 500ms) – użytkownik jest zadowolony.
2. **Tolerating** (np. 500ms – 2s) – użytkownik widzi opóźnienie, ale nie wychodzi.
3. **Frustrated** (> 2s lub błąd) – użytkownik porzuca stronę.

```
Apdex = (Satisfied + Tolerating / 2) / Total Samples
```

Wynik od 0 do 1. Wartość > 0.85 uważana jest za akceptowalną.

### Little's Law (Prawo Little'a)

Matematyczna podstawa testów:

```
L = λ × W
```

- `L` – średnia liczba użytkowników/żądań w systemie (VUs)
- `λ` – przepustowość (Arrival Rate, RPS)
- `W` – średni czas odpowiedzi (sekundy)

**Praktyczne zastosowanie:** chcesz osiągnąć 100 RPS przy średnim czasie odpowiedzi 0.5s? Potrzebujesz 50 VUs (`L = 100 × 0.5`).

---

## 3. Architektura k6

### Dlaczego k6 to nie „po prostu JS"?

k6 jest napisane w **Go**, a skrypty piszesz w JavaScript, ale **nie działa na Node.js**. Używa interpretera **goja** – każdy Virtual User (VU) to osobna instancja interpretera JS uruchomiona jako goroutine (lekki proces Go).

Dzięki temu:

- z jednej maszyny wyciągniesz tysiące VUs (w JMeterze wymagałoby to klastra),
- zużycie RAM jest minimalne w porównaniu z modelem Thread-per-user (np. JMeter, Gatling w trybie blokującym).

> **Ważne:** nie używaj ciężkich bibliotek npm (np. `lodash`, `axios`). k6 ma własne API HTTP i wbudowane moduły. Im lżejszy skrypt, tym więcej VUs z jednej maszyny.
> 

### Anatomia skryptu k6

```
init code           ← wykonywany raz na starcie (import, odczyt plików)
     │
setup()             ← wykonywany raz przed testem (logowanie, dane startowe)
     │
default function()  ← pętla każdego VU (właściwy scenariusz)
     │
teardown()          ← wykonywany raz po zakończeniu testu
```

---

## 4. Podstawowy skrypt k6

```jsx
import http from 'k6/http';
import { sleep, check } from 'k6';

// Opcje testu
export const options = {
  vus: 50,           // liczba wirtualnych użytkowników
  duration: '1m',    // czas trwania

  thresholds: {
    http_req_duration: ['p(95)<500'], // P95 poniżej 500ms
    http_req_failed: ['rate<0.01'],   // błędy poniżej 1%
  },
};

export default function () {
  const res = http.get('https://api.example.com/users');

  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1); // Think Time – symulacja prawdziwego użytkownika
}
```

### Checks vs Thresholds – kluczowa różnica

|  | Checks | Thresholds |
| --- | --- | --- |
| Co robi? | Weryfikuje wartość (asercja) | Decyduje o wyniku testu (Pass/Fail) |
| Przerywa test? | ❌ Nie | ✅ Tak (opcjonalnie: `abortOnFail`) |
| Zastosowanie | Debug, walidacja odpowiedzi | CI/CD, Quality Gates |

---

## 5. Payload i Params – różnica

**Payload** to dane wysyłane w **ciele żądania HTTP**. Występuje w metodach: `POST`, `PUT`, `PATCH`.

**Params** to **obiekt konfiguracyjny requestu** – nie są to dane biznesowe. Zawierają m.in.: headers, cookies, tags, timeout, redirects, compression, auth.

```jsx
import http from 'k6/http';

export default function () {
  // Payload – dane biznesowe (ciało żądania)
  const payload = JSON.stringify({
    username: 'jan.kowalski@example.com',
    password: 'haslo123',
  });

  // Params – konfiguracja requestu
  const params = {
    headers: { 'Content-Type': 'application/json' },
    tags: { name: 'login' },
    timeout: '10s',
  };

  const res = http.post('https://api.example.com/login', payload, params);
}
```

---

## 6. Wbudowane metryki HTTP w k6 (http_req_*)

To wiedza, która oddziela mid od juniora. Każde żądanie HTTP jest rozkładane na fazy – k6 mierzy każdą z nich osobno.

```
┌──────────────────────────────────────────────────────────────────────┐
│  http_req_blocked      │ Czekanie na wolne połączenie z puli (TCP)   │
├──────────────────────────────────────────────────────────────────────┤
│  http_req_connecting   │ Nawiązywanie połączenia TCP (3-way handshake)│
├──────────────────────────────────────────────────────────────────────┤
│  http_req_tls_handshaking │ Negocjacja TLS/SSL (tylko HTTPS)         │
├──────────────────────────────────────────────────────────────────────┤
│  http_req_sending      │ Wysyłanie danych żądania do serwera         │
├──────────────────────────────────────────────────────────────────────┤
│  http_req_waiting      │ Czekanie na pierwszy bajt odpowiedzi (TTFB) │
├──────────────────────────────────────────────────────────────────────┤
│  http_req_receiving    │ Odbieranie danych odpowiedzi                │
├──────────────────────────────────────────────────────────────────────┤
│  http_req_duration     │ SUMA: sending + waiting + receiving         │
└──────────────────────────────────────────────────────────────────────┘
```

**Jak to wykorzystać w diagnostyce:**

- Wysoki `http_req_blocked` → wyczerpana pula połączeń TCP, brakuje keep-alive.
- Wysoki `http_req_tls_handshaking` → TLS Session Resumption wyłączony, zbyt dużo nowych handshake'ów.
- Wysoki `http_req_waiting` (TTFB) → wolny backend, baza danych, logika biznesowa.
- Wysoki `http_req_receiving` → duże payloady, sieć jest wąskim gardłem.

```jsx
// Możesz ustawiać Thresholds na poszczególne fazy
thresholds: {
  http_req_waiting: ['p(95)<300'],    // TTFB < 300ms dla 95% żądań
  http_req_duration: ['p(95)<500'],   // całkowity czas < 500ms
},
```

---

## 7. Modelowanie ruchu: Closed vs. Open Loop

### Closed Loop (Model Zamknięty) – domyślny w k6

Liczba VUs jest stała. Nowe żądanie wysyłane jest dopiero po zakończeniu poprzedniego.

**Problem:** jeśli system zaczyna zwalniać, VU też zwalnia → wysyłasz mniej requestów → system wygląda stabilnie. To **Coordinated Omission** – błąd, który sprawia, że wyniki są lepsze niż w rzeczywistości.

### Open Loop (Model Otwarty / Arrival Rate) – `ramping-arrival-rate`

Definiujesz, ile żądań na sekundę ma trafiać do systemu, **niezależnie od szybkości odpowiedzi**. To symuluje prawdziwy internet – użytkownicy nie czekają, aż serwer odetchnie.

```jsx
export const options = {
  scenarios: {
    open_model: {
      executor: 'ramping-arrival-rate',
      startRate: 10,        // start: 10 RPS
      timeUnit: '1s',
      preAllocatedVUs: 50,  // pula gotowych VUs
      maxVUs: 200,          // maksymalna pula przy przeciążeniu

      stages: [
        { target: 50, duration: '2m' },   // ramp up do 50 RPS
        { target: 50, duration: '5m' },   // utrzymaj 50 RPS
        { target: 0,  duration: '1m' },   // ramp down
      ],
    },
  },
};
```

---

## 8. Scenariusze w k6

k6 pozwala miksować różne typy executorów w jednym teście, np. jednocześnie symulować użytkowników przeglądających stronę i roboty indeksujące API.

| Executor | Zastosowanie |
| --- | --- |
| `shared-iterations` | Pula N iteracji rozdzielona między VUs. Dobry do testów funkcjonalnych. |
| `per-vu-iterations` | Każdy VU wykonuje dokładnie X powtórzeń. |
| `constant-vus` | Stała liczba VUs przez cały czas. Closed loop. |
| `ramping-vus` | Zmiana liczby VUs w czasie (stages). Klasyczny load test. |
| `constant-arrival-rate` | Stałe RPS. Open loop. |
| `ramping-arrival-rate` | Zmienne RPS w czasie (stages). Open loop. |

---

## 9. Korelacja i dynamiczne dane (Data Parameterization)

### Dlaczego to ważne?

Testowanie na jednym loginie `test/test` to błąd. Bazy danych keszują wyniki dla tego samego ID – wyniki będą przekłamane i nie wykryjesz problemów z Connection Pooling czy Database Locks.

### Wczytywanie danych z pliku CSV

```jsx
import { SharedArray } from 'k6/data';
import papaparse from 'https://jslib.k6.io/papaparse/5.1.1/index.js';

// SharedArray – dane wczytywane raz, współdzielone między VUs (oszczędność RAM)
const users = new SharedArray('users', function () {
  return papaparse.parse(open('./users.csv'), { header: true }).data;
});

export default function () {
  const user = users[__VU % users.length]; // każdy VU bierze inny rekord

  const payload = JSON.stringify({
    email: user.email,
    password: user.password,
  });

  http.post('https://api.example.com/login', payload, {
    headers: { 'Content-Type': 'application/json' },
  });
}
```

### Korelacja – wyciąganie tokenów z odpowiedzi

Realistyczny scenariusz: zaloguj się → wyciągnij token → użyj w kolejnych żądaniach.

```jsx
export default function () {
  // Krok 1: logowanie
  const loginRes = http.post('https://api.example.com/login',
    JSON.stringify({ email: 'user@test.com', password: 'secret' }),
    { headers: { 'Content-Type': 'application/json' } }
  );

  check(loginRes, { 'login successful': (r) => r.status === 200 });

  // Korelacja – wyciąganie tokenu z odpowiedzi
  const token = loginRes.json('access_token'); // lub: JSON.parse(loginRes.body).token

  // Krok 2: żądanie uwierzytelnione
  const profileRes = http.get('https://api.example.com/profile', {
    headers: { Authorization: `Bearer ${token}` },
  });

  check(profileRes, { 'profile loaded': (r) => r.status === 200 });

  sleep(2);
}
```

---

## 10. Grupy (Groups) i Tagi (Tags)

### Groups – logiczne grupowanie kroków

```jsx
import { group } from 'k6';

export default function () {
  group('User Journey: Shopping', () => {
    group('Browse', () => {
      http.get('https://shop.example.com/products');
      sleep(1);
    });

    group('Add to Cart', () => {
      http.post('https://shop.example.com/cart', JSON.stringify({ productId: 42 }));
      sleep(1);
    });

    group('Checkout', () => {
      http.post('https://shop.example.com/checkout', JSON.stringify({ paymentMethod: 'card' }));
    });
  });
}
```

### Tags – filtrowanie metryk w Grafanie

```jsx
const res = http.get('https://api.example.com/products', {
  tags: {
    name: 'product_list',  // Thresholds możesz ustawiać per tag!
    team: 'backend',
  },
});
```

```jsx
// Threshold tylko dla żądań oznaczonych tagiem
thresholds: {
  'http_req_duration{name:product_list}': ['p(95)<300'],
},
```

---

## 11. Wizualizacja wyników: Grafana + InfluxDB

Domyślne wyjście k6 (terminal) to za mało do analizy. W praktyce pipeline wygląda tak:

```
k6 → InfluxDB (baza metryk) → Grafana (dashboardy)
```

### Uruchomienie z eksportem do InfluxDB

```bash
k6 run --out influxdb=http://localhost:8086/k6 script.js
```

### Docker Compose – szybki setup lokalny

```yaml
version: '3'
services:
  influxdb:
    image: influxdb:1.8
    ports:
      - "8086:8086"
    environment:
      - INFLUXDB_DB=k6

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_AUTH_ANONYMOUS_ENABLED=true
    volumes:
      - grafana-storage:/var/lib/grafana

volumes:
  grafana-storage:
```

W Grafanie importujesz gotowy dashboard k6 (ID: **2587** z grafana.com).

### Inne wyjścia (outputs) k6

| Output | Zastosowanie |
| --- | --- |
| `--out influxdb` | Lokalne testy, Grafana |
| `--out cloud` | k6 Cloud (SaaS, płatny) |
| `--out json` | Surowe dane do własnej analizy |
| `--out statsd` | Integracja z Datadog, Telegraf |
| `--out prometheus-rw` | Prometheus + Grafana (bez InfluxDB) |

---

## 12. Integracja z CI/CD (Shift-Left)

W nowoczesnym DevOps testy wydajnościowe nie są robione raz na kwartał – są częścią każdego Pull Requestu.

### GitLab CI/CD – przykład

```yaml
performance_test:
  stage: test
  image: grafana/k6
  script:
    - k6 run --vus 50 --duration 2m tests/performance/api.js
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
```

### Thresholds jako Quality Gate

Jeśli nowy kod spowalnia P95 o więcej niż 10%, k6 zwraca kod wyjścia `1` → pipeline świeci na czerwono. To zapobiega **pełzającej degradacji wydajności** (performance creep).

```jsx
thresholds: {
  http_req_duration: [
    { threshold: 'p(95)<500', abortOnFail: true }, // przerwie test przy przekroczeniu
  ],
  http_req_failed: ['rate<0.01'],
},
```

---

## 13. Cykl życia testów wydajnościowych

1. **Analiza wymagań (NFRs – Non-Functional Requirements):** pytasz biznesu: „Ile osób ma tu wchodzić?" i „Jaki czas odpowiedzi jest akceptowalny?".
2. **Przygotowanie środowiska:** testuj na **stagingu** (kopia produkcji), nigdy bezpośrednio na produkcji. Środowisko powinno mieć zbliżone zasoby do produkcji.
3. **Tworzenie skryptów:** pamiętaj o **Think Time** (`sleep()`) i parametryzacji danych (różne loginy, produkty, wyszukiwania).
4. **Rozgrzewka (Warm-up):** przed pomiarem daj aplikacji 1–2 minuty na wypełnienie cache'u i nawiązanie połączeń z bazą danych.
5. **Wykonanie testu i zbieranie metryk.**
6. **Analiza i raportowanie:** szukaj korelacji – czy wzrost Response Time pokrywa się ze skokiem CPU na bazie danych?
7. **Optymalizacja i ponowny test:** zidentyfikuj wąskie gardło, napraw, uruchom test ponownie.

---

## 14. Analiza wąskich gardeł (Bottleneck Analysis)

Gdy test „padnie", musisz wiedzieć *gdzie*. Sprawdzasz kolejno:

**Database Locks** – czy baza nie blokuje tabel przy zbyt wielu równoległych zapisach? Szukaj w logach bazy (MySQL: `SHOW PROCESSLIST`, PostgreSQL: `pg_stat_activity`).

**Connection Pooling** – czy aplikacja nie ma limitu połączeń do bazy (np. tylko 100), przez co reszta żądań stoi w kolejce? Objaw: `http_req_blocked` rośnie liniowo z liczbą VUs.

**Garbage Collection (GC)** – szczególnie w Java/Go/Node.js. System staje co chwilę na 50–200ms przy „sprzątaniu" pamięci. Widoczne jako charakterystyczne „piły" na wykresie czasu odpowiedzi.

**Network Saturation** – czy nie wysyłasz tak dużych payloadów (ciężkie JSONy, brakujące paginacje), że zapchałeś kartę sieciową? Sprawdź `http_req_receiving`.

**CDN Cache** – jeśli testujesz URL-e za Cloudflare, możesz testować wydajność Cloudflare, nie swojej aplikacji. Testuj endpointy bezpośrednio (z pominięciem warstwy cache) lub używaj nagłówka `Cache-Control: no-cache`.

---

## 15. Coordinated Omission (Skoordynowane Pominięcie)

Temat na poziomie seniora, który powinien znać każdy mid.

**Problem:** jeśli narzędzie wysyła żądanie i czeka na odpowiedź przed wysłaniem kolejnego (Closed Loop), to gdy system zwalnia – samo narzędzie zwalnia. Wyniki wyglądają lepiej niż w rzeczywistości, bo narzędzie „dopasowało się" do ślimaczego tempa serwera.

**Rozwiązanie:** `ramping-arrival-rate` (Open Loop) – k6 wysyła żądania zgodnie z zadanym harmonogramem, niezależnie od szybkości odpowiedzi. Jeśli serwer nie wyrabia, kolejka się wydłuża i to jest widoczne w wynikach.

---

## 16. Pułapki, na których można poległeś

**Keszowanie po stronie CDN** – opisane powyżej w sekcji Bottleneck Analysis.

**Brak rozgrzewki** – pierwsze żądania są zawsze wolne (kompilacja JIT, nawiązywanie połączeń). Daj skryptowi 1–2 minuty „rozbiegu" przed mierzeniem.

**Zbyt mała maszyna testowa** – jeśli CPU maszyny z k6 dobija do 100%, wyniki są przekłamane, bo samo k6 nie wyrabia z wysyłaniem żądań. Monitor maszyny k6 tak samo jak testowanego systemu.

**Brak Think Time** – scenariusz bez `sleep()` to nie użytkownik, to bot. Realny użytkownik czyta stronę, wypełnia formularze. Brak think time powoduje nienaturalnie wysokie RPS i przekłamuje wyniki.

**Testowanie tylko „happy path"** – w produkcji użytkownicy robią błędy (404, walidacje formularzy). Uwzględnij je w skryptach, bo 404 też obciążają serwer.

**Jeden URL zamiast scenariusza użytkownika** – test pojedynczego endpointu `/api/products` to mikrobenchmark, nie test wydajnościowy. Symuluj realne przepływy (logowanie → przeglądanie → koszyk → checkout).

---

## 17. Słowniczek (szybka ściągawka)

| Termin | Znaczenie |
| --- | --- |
| VU (Virtual User) | Symulowany użytkownik w k6. |
| RPS / TPS | Requests/Transactions Per Second – przepustowość. |
| TTFB | Time To First Byte – czas do pierwszego bajtu odpowiedzi (`http_req_waiting`). |
| P95 / P99 | 95. / 99. percentyl czasu odpowiedzi. |
| Threshold | Próg Pass/Fail w k6 – serce integracji z CI/CD. |
| Think Time | Przerwa między akcjami użytkownika (`sleep()`). |
| Warm-up | Rozgrzewka systemu przed właściwym pomiarem. |
| Arrival Rate | Liczba żądań na sekundę (Open Loop). |
| Coordinated Omission | Błąd pomiaru w Closed Loop przy wolnym systemie. |
| Bottleneck | Wąskie gardło – element systemu ograniczający przepustowość. |
| Apdex | Wskaźnik satysfakcji użytkownika (0–1). |
| Little's Law | `L = λ × W` – matematyczna podstawa testów. |
| Staging | Środowisko testowe będące kopią produkcji. |
| NFR | Non-Functional Requirements – wymagania wydajnościowe od biznesu. |
| Connection Pool | Pula gotowych połączeń do bazy danych. |
| GC (Garbage Collection) | Automatyczne zwalnianie pamięci w środowiskach zarządzanych (Java, Go, Node). |