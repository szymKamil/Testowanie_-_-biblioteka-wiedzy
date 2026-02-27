# Testowanie---biblioteka-wiedzy

Kilka słów od autora (●'◡'●) _trochę wyszło długo, ale może ktoś przeczyta_
Repozytorium zawiera zbiór notatek/wiedzy związanej z najwazniejszycmi aspektami pracy testera/testera automatyzującego. Notaki te zbieram od jakiegoś czasu, z prozaicznego powodu - jest tego wiele, a czasem coś się zapomni, czasem warto sobie coś przypomnieć (pamięć jest zawodna).
Genezą tego były notatki dotyczące testów automatycznych Selenium + frameworków, które powstawały w trakcie mojej nauki tego narzędzia. Jest tego moim zdaniem dośc sporo, a to tylko niewielki fragment wiedzy dotyczacej automatyzacji czy ogólnie testowania. Wraz z rozwojem zawodowym dostrzegłem, że tego rodzaju notaki warto rozbudowywać o inne obszary, stąd nieco bardziej rozbudowany spis treści.

Mam ambitny pomysł rozbudowywać po godzinach ten zasób wiedzy w formę rozbudowanej biblioteki, kompednium wiedzy potrzebnej każdemu testerowi, szczególnie automatyzującemu. Aktualnie informacji dostępnych w sieci jest aż zanadto, szczeóglnie że wiele z niej można uzyskać od dowolnego AI (Gemini, ChatGPT, Claude). Samemu kożystam także z tych źródeł, jednak fundamenty pozostają te same - trzeba wiedzieć o co pytać, a tego rodzaju pytania rodzą się czesto z praktyki. I na tej praktyce i codziennych wyzwaniach buduję te fundamenty.
Dodatkowo, wiele infromacji można pozysać z tak archaicznych źródeł, jak książki ☜(ﾟヮﾟ☜) dlatego biblioteka ta będzie kompilacją różnych źródeł, co może być właśnie jej istotną zaletą.

Niestety, z racji, że rozwijam to po godzinach, wielu rzeczy wciąż brakuje, niektóre tematy są tylko zarysowane w spisie treści czy podstronach do uzupełnienia. Dlatego pozwolę sobie orientacyjnie wypisać (za pomocą AI), co na ten moment zostało zrobione:

## Zakres materiału 27.02.2026

---

## 📊 Tematy gotowe do pełnego wykorzystania

Te notatki są dobrze opracowane, zawierają solidną wiedzę i praktyczne przykłady:

### ⭐⭐⭐ NAJLEPIEJ OPRACOWANE

- **[Selenium JS](Testowanie/Selenium%20JS%201ef3f0d8989e808798bac94c52351f99.md)** – Kompletna biblioteka (2910 linii)
  - Obejmuje historię, architekturę, WebDriver, Grid, IDE
  - Materiały online, metodologia, praktyki kodowania
  - Problemy techniczne i Cucumber
  - **Warte przeczytania dla każdego testera automatyzującego**

- **[Teoria dot. testów automatycznych](Testowanie/Teoria%20dot%20test%C3%B3w%20automatycznych%201f73f0d8989e80c29df5e6778e1a2ead.md)** – Solidne fundamenty (537 linii)
  - Poziomy testów (unit, integracyjne, systemowe, E2E, akceptacyjne)
  - Piramida testów, rodzaje testowania (funkcjonalne, niefunkcjonalne)
  - Metodyki testowania
  - **Obowiązkowa lektura dla zrozumienia strategii testowania**

- **[Testy wydajnościowe](Testowanie/Testy%20wydajno%C5%9Bciowe%203043f0d8989e80a29156c5dd2d9c1795.md)** – Kompendium praktyczne (525 linii)
  - Cztery typy testów (Load, Stress, Soak, Spike)
  - Metryki: Response Time, Throughput, Error Rate, Apdex
  - Prawo Little'a i architektura k6
  - Podfoldery: JMeter i k6

### ⭐⭐ DOBRZE OPRACOWANE

- **[Java](Testowanie/Java%202603f0d8989e801a8926e275cfbbd806.md)** – Kompleksowe podstawy
  - 17 podtematów (Zmienne, Obiekty, Stringi, Kolekcje, Metody, Stream, Threads, itp.)
  - Dokumentowanie kodu, Maven
  - Każdy temat ma własny plik z przykładami
  - **Idealnie dla testerów piszących automat w Javie**

- **[TypeScript](Testowanie/Typescript%2030e3f0d8989e80df9a6fe47b5f4713aa.md)** – Kompletny przewodnik (1500+ linii)
  - 11 sekcji fundamentów (Typy, Funkcje, Obiekty, Klasy, Generyki, Utility Types, Moduły)
  - Praktyczne zastosowania w Playwright
  - **NOWE:** 3 rozdziały testowania (Async & Błędy, Unit Testing, Praktyka testowa)
  - Dobre praktyki
  - **Pełny kompendium dla automatyzacji w JS/TS**

- **[Docker](Testowanie/Docker%202e83f0d8989e80b2911dd454177d17b1.md)** – Praktyczne (147 linii + podfoldery)
  - Dobrze wyjaśniona różnica między obrazami a kontenerami
  - Dockerfile i Docker Compose na przykładach
  - Operacje na kontenerach (cp, backup, tar)
  - Podfoldery: Komendy i Problemy
  - **Essential dla każdego nowoczesnego testera**

- **[SQL](Testowanie/SQL%2027b3f0d8989e8084a048ebdf06f70b1d.md)** – Praktyczny przewodnik (369 linii)
  - Podstawowe koncepcje (klucze główne, klucze obce, widoki)
  - Typy danych
  - Główne zapytania (CREATE, SELECT, JOIN, WHERE, itp.)
  - DML (INSERT, UPDATE, DELETE)
  - **Wystarczy jako szybki reference dla testerów**

- **[Jenkins](Testowanie/Jenkins%202813f0d8989e803c86dec515218506f8.md)** – Praktyczne wdrożenie (377 linii)
  - Budowa obrazu Dockera
  - Konfiguracja z GitHubem
  - Jenkinsfile – struktura pipeline'u
  - **Potrzebne do CI/CD automatyzacji**

- **[API](Testowanie/API%2027e3f0d8989e8093b428c08ad39606ed.md)** – Komprehensywne (418 linii)
  - Wyjaśnienie API jako koncepcji
  - Style komunikacji (REST, GraphQL, WebSockets, Webhooks, SOAP, gRPC, MQTT)
  - Endpointy i metody HTTP
  - **Świetny wstęp do testowania API**

---

## 🚧 Tematy do uzupełnienia

Te notatki zostały rozpoczęte, ale wymagają dodatkowej pracy:

- **[Playwright TS](Testowanie/Playwright%20TS%2030e3f0d8989e801081b7c9d58c75e9c3.md)** – Zaledwie stub
  - Wymaga pełnego opracowania analogicznego do Selenium JS
  - _Status: Do uzupełnienia_

- **[Git](Testowanie/Git%2030f3f0d8989e80cbbcb8cb6d3bed70d9.md)** – Brak treści
  - _Status: Do uzupełnienia_

- **[Testy manualne](Testowanie/Testy%20manualne%203063f0d8989e80739bf6fa896da264ef.md)** – Bardzo krótkie
  - Zawiera tylko kilka linii i pytania rekrutacyjne
  - _Status: Do rozszerzenia_

- **[Książki](Testowanie/Ksi%C4%85%C5%BCki%20dotycz%C4%85ce%20test%C3%B3w%20automatycznych%201ef3f0d8989e80718ca4d97143143b77.md)** – Zaczęte
  - Lista rekomendacjach jest niekompletna
  - _Status: W trakcie uzupełniania_

---

## 💡 Jak korzystać z tej biblioteki

1. **Dla początkujących:** Zacznij od [Teorii testów automatycznych](Testowanie/Teoria%20dot%20test%C3%B3w%20automatycznych%201f73f0d8989e80c29df5e6778e1a2ead.md), a następnie przejdź do narzędź zależy od Twoich potrzeb (Selenium, Playwright)

2. **Dla testerów Java:** [Java](Testowanie/Java%202603f0d8989e801a8926e275cfbbd806.md) → [Selenium JS](Testowanie/Selenium%20JS%201ef3f0d8989e808798bac94c52351f99.md) (wraz z konwersją na Java)

3. **Dla testerów JS/TS:** [TypeScript](Testowanie/Typescript%2030e3f0d8989e80df9a6fe47b5f4713aa.md) → [Selenium JS](Testowanie/Selenium%20JS%201ef3f0d8989e808798bac94c52351f99.md) lub przyczekaj na [Playwright TS](Testowanie/Playwright%20TS%2030e3f0d8989e801081b7c9d58c75e9c3.md)

4. **Dla CI/CD:** [Docker](Testowanie/Docker%202e83f0d8989e80b2911dd454177d17b1.md) → [Jenkins](Testowanie/Jenkins%202813f0d8989e803c86dec515218506f8.md)

5. **Dla testów wydajnościowych:** [Testy wydajnościowe](Testowanie/Testy%20wydajno%C5%9Bciowe%203043f0d8989e80a29156c5dd2d9c1795.md)

6. **Quick reference:** [SQL](Testowanie/SQL%2027b3f0d8989e8084a048ebdf06f70b1d.md), [API](Testowanie/API%2027e3f0d8989e8093b428c08ad39606ed.md)
