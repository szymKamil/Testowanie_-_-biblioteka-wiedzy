# Testowanie---biblioteka-wiedzy

Kilka słów od autora (●'◡'●) _trochę wyszło długo, ale może ktoś przeczyta_
Repozytorium zawiera zbiór notatek/wiedzy związanej z najwazniejszycmi aspektami pracy testera/testera automatyzującego. Notaki te zbieram od jakiegoś czasu, z prozaicznego powodu - jest tego wiele, a czasem coś się zapomni, czasem warto sobie coś przypomnieć (pamięć jest zawodna).
Genezą tego były notatki dotyczące testów automatycznych Selenium + frameworków, które powstawały w trakcie mojej nauki tego narzędzia. Jest tego moim zdaniem dośc sporo, a to tylko niewielki fragment wiedzy dotyczacej automatyzacji czy ogólnie testowania. Wraz z rozwojem zawodowym dostrzegłem, że tego rodzaju notaki warto rozbudowywać o inne obszary, stąd nieco bardziej rozbudowany spis treści.

Mam ambitny pomysł rozbudowywać po godzinach ten zasób wiedzy w formę rozbudowanej biblioteki, kompednium wiedzy potrzebnej każdemu testerowi, szczególnie automatyzującemu. Aktualnie informacji dostępnych w sieci jest aż zanadto, szczeóglnie że wiele z niej można uzyskać od dowolnego AI (Gemini, ChatGPT, Claude). Samemu kożystam także z tych źródeł, jednak fundamenty pozostają te same - trzeba wiedzieć o co pytać, a tego rodzaju pytania rodzą się czesto z praktyki. I na tej praktyce i codziennych wyzwaniach buduję te fundamenty.
Dodatkowo, wiele infromacji można pozysać z tak archaicznych źródeł, jak książki ☜(ﾟヮﾟ☜) dlatego biblioteka ta będzie kompilacją różnych źródeł, co może być właśnie jej istotną zaletą.

Niestety, z racji, że rozwijam to po godzinach, wielu rzeczy wciąż brakuje, niektóre tematy są tylko zarysowane w spisie treści czy podstronach do uzupełnienia. Dlatego pozwolę sobie orientacyjnie wypisać (za pomocą AI), co na ten moment zostało zrobione:

## Zakres materiału 29.03.2026 

---

## 📊 Tematy gotowe do pełnego wykorzystania

Te notatki są dobrze opracowane, zawierają solidną wiedzę i praktyczne przykłady:

### ⭐⭐⭐ NAJLEPIEJ OPRACOWANE

- **[Selenium JS](Testowanie/Selenium%20JS.md)** – Kompletna biblioteka (2910 linii)
  - Obejmuje historię, architekturę, WebDriver, Grid, IDE
  - Materiały online, metodologia, praktyki kodowania
  - Problemy techniczne i Cucumber
  - **Warte przeczytania dla każdego testera automatyzującego**

- **[Teoria dot. testów automatycznych](Testowanie/Teoria%20dot%20testów%20automatycznych.md)** – Solidne fundamenty (537 linii)
  - Poziomy testów (unit, integracyjne, systemowe, E2E, akceptacyjne)
  - Piramida testów, rodzaje testowania (funkcjonalne, niefunkcjonalne)
  - Metodyki testowania
  - **Obowiązkowa lektura dla zrozumienia strategii testowania**

- **[Testy wydajnościowe](Testowanie/Testy%20wydajnościowe.md)** – Kompendium praktyczne (525 linii)
  - Cztery typy testów (Load, Stress, Soak, Spike)
  - Metryki: Response Time, Throughput, Error Rate, Apdex
  - Prawo Little'a i architektura k6
  - Podfoldery: JMeter i k6

  - **[Playwright TS](Testowanie/Playwright%20TS.md)** – Solidne fundamenty (>4000 linii)
  - Kompletna ściągawka pracy z Playwright w najwazniejszych aspektach narzędzia.
  - _Status: Gotowe, możliwe, że do rozwinięcia w przyszłości o nowe treści.
  - **Warte przeczytania dla każdego testera automatyzującego**

### ⭐⭐ DOBRZE OPRACOWANE

- **[Java](Testowanie/Java.md)** – Kompleksowe podstawy
  - 17 podtematów (Zmienne, Obiekty, Stringi, Kolekcje, Metody, Stream, Threads, itp.)
  - Dokumentowanie kodu, Maven
  - Każdy temat ma własny plik z przykładami
  - **Idealnie dla testerów piszących automat w Javie**

- **[TypeScript](Testowanie/Typescript.md)** – Kompletny przewodnik (1500+ linii)
  - 11 sekcji fundamentów (Typy, Funkcje, Obiekty, Klasy, Generyki, Utility Types, Moduły)
  - Praktyczne zastosowania w Playwright
  - **NOWE:** 3 rozdziały testowania (Async & Błędy, Unit Testing, Praktyka testowa)
  - Dobre praktyki
  - **Pełny kompendium dla automatyzacji w JS/TS**

- **[Docker](Testowanie/Docker.md)** – Praktyczne (147 linii + podfoldery)
  - Dobrze wyjaśniona różnica między obrazami a kontenerami
  - Dockerfile i Docker Compose na przykładach
  - Operacje na kontenerach (cp, backup, tar)
  - Podfoldery: Komendy i Problemy
  - **Essential dla każdego nowoczesnego testera**

- **[SQL](Testowanie/SQL.md)** – Praktyczny przewodnik (369 linii)
  - Podstawowe koncepcje (klucze główne, klucze obce, widoki)
  - Typy danych
  - Główne zapytania (CREATE, SELECT, JOIN, WHERE, itp.)
  - DML (INSERT, UPDATE, DELETE)
  - **Wystarczy jako szybki reference dla testerów**

- **[Jenkins](Testowanie/Jenkins.md)** – Praktyczne wdrożenie (377 linii)
  - Budowa obrazu Dockera
  - Konfiguracja z GitHubem
  - Jenkinsfile – struktura pipeline'u
  - **Potrzebne do CI/CD automatyzacji**

- **[API](Testowanie/API.md)** – Komprehensywne (418 linii)
  - Wyjaśnienie API jako koncepcji
  - Style komunikacji (REST, GraphQL, WebSockets, Webhooks, SOAP, gRPC, MQTT)
  - Endpointy i metody HTTP
  - **Świetny wstęp do testowania API**

---

## 🚧 Tematy do uzupełnienia

Te notatki zostały rozpoczęte, ale wymagają dodatkowej pracy:


- **[Git](Testowanie/Git.md)** – Brak treści
  - _Status: Do uzupełnienia_

- **[Testy manualne](Testowanie/Testy%20manualne.md)** – Bardzo krótkie
  - Zawiera tylko kilka linii i pytania rekrutacyjne
  - _Status: Do rozszerzenia_

- **[Książki](Testowanie/Książki%20dotyczące%20testów%20automatycznych.md)** – Zaczęte
  - Lista rekomendacjach jest niekompletna
  - _Status: W trakcie uzupełniania_

---

## 💡 Jak korzystać z tej biblioteki

1. **Dla początkujących:** Zacznij od [Teorii testów automatycznych](Testowanie/Teoria%20dot%20test%C3%B3w%20automatycznych%20.md), a następnie przejdź do narzędź zależy od Twoich potrzeb (Selenium, Playwright)

2. **Dla testerów Java:** [Java](Testowanie/Java%20.md) → [Selenium JS](Testowanie/Selenium%20JS%20.md) (wraz z konwersją na Java)

3. **Dla testerów JS/TS:** [TypeScript](Testowanie/Typescript%20.md) → [Selenium JS](Testowanie/Selenium%20JS%20.md) lub przyczekaj na [Playwright TS](Testowanie/Playwright%20TS%20.md)

4. **Dla CI/CD:** [Docker](Testowanie/Docker%20.md) → [Jenkins](Testowanie/Jenkins%20.md)

5. **Dla testów wydajnościowych:** [Testy wydajnościowe](Testowanie/Testy%20wydajno%C5%9Bciowe%20.md)

6. **Quick reference:** [SQL](Testowanie/SQL%20.md), [API](Testowanie/API%20.md)
