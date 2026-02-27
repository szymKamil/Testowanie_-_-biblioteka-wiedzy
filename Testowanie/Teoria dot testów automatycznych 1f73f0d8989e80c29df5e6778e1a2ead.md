# Teoria dot. testów automatycznych

### `TODO: Automatyzacja – teoria`

`Masz technikalia Selenium, ale brakuje:`

- `kiedy **automatyzować**, a kiedy nie`
- `piramida testów`
- `ROI automatyzacji`
- `różnice: testy E2E vs API vs unit`

### `Testy niefunkcjonalne – przykłady`

`Np.:`

- `czym różni się test wydajnościowy od obciążeniowego`
- `przykłady metryk (TPS, response time, SLA)`

# Poziomy testów

Testowanie oprogramowania (lub po prostu testowanie) polega na dynamicznej ocenie fragmentu oprogramowania, zwanego System Under Test (SUT), poprzez skończony zestaw przypadków testowych (lub po prostu testów), wydając werdykt na jego temat. Testowanie oznacza wykonanie SUT przy użyciu określonych wartości wejściowych w celu oceny wyniku lub oczekiwanego zachowania. Na pierwszy rzut oka wyróżniamy dwie odrębne kategorie testowania oprogramowania: ręczne i automatyczne. Z jednej strony, w testowaniu ręcznym, osoba (zazwyczaj inżynier oprogramowania lub użytkownik końcowy) ocenia SUT. Z drugiej strony, w testach automatycznych używamy określonych narzędzi programowych do opracowywania testów i kontrolowania ich wykonywania w odniesieniu do SUT. Zautomatyzowane testy pozwalają na wczesne wykrycie defektów (zwykle nazywanych błędami) w SUT, zapewniając jednocześnie wiele dodatkowych korzyści (np. oszczędność kosztów, szybka informacja zwrotna, pokrycie testowe lub możliwość ponownego wykorzystania, by wymienić tylko kilka). Testowanie manualne może być również wartościowym podejściem w niektórych przypadkach, na przykład w testach eksploracyjnych (tj. testerzy swobodnie badają i oceniają SUT).

W zależności od rozmiaru SUT, możemy zdefiniować różne poziomy testowania. Poziomy te definiują kilka kategorii, w których zespoły programistyczne dzielą swoje wysiłki testowe. W tej książce proponuję układ piętrowy reprezentujący różne poziomy. Niższe poziomy tej struktury reprezentują testy mające na celu weryfikację małych fragmentów oprogramowania (zwanych jednostkami). W miarę wznoszenia się w stosie znajdujemy inne poziomy (np. integracja, system itp.), w których SUT integruje coraz więcej komponentów.

![obraz.png](Teoria%20dot%20test%C3%B3w%20automatycznych/obraz.png)

Najniższym poziomem tego stosu jest testowanie jednostkowe. Na tym poziomie oceniamy poszczególne jednostki oprogramowania. Jednostka to konkretny obserwowalny element zachowania. Na przykład, jednostki są zazwyczaj metodami lub klasami w programowaniu obiektowym i funkcjami w programowaniu funkcjonalnym. Testy jednostkowe mają na celu sprawdzenie, czy każda jednostka zachowuje się zgodnie z oczekiwaniami. Zautomatyzowane testy jednostkowe zwykle działają bardzo szybko, ponieważ każdy test wykonuje niewielką ilość kodu w izolacji. Aby osiągnąć tę izolację, możemy użyć podwójnych testów, fragmentów oprogramowania, które zastępują zależne komponenty danej jednostki. Na przykład, popularnym typem podwójnego testu w programowaniu obiektowym jest obiekt mock. Obiekt pozorny naśladuje rzeczywisty obiekt przy użyciu zaprogramowanego zachowania. Kolejnym poziomem  jest testowanie integracyjne. Na tym poziomie różne jednostki są komponowane w celu utworzenia komponentów złożonych. Testowanie integracyjne ma na celu ocenę interakcji między zaangażowanymi jednostkami i ujawnienie defektów w ich interfejsach. Następnie, na poziomie testowania systemu i end-to-end (E2E), testujemy system oprogramowania jako całość. Musimy wdrożyć SUT i zweryfikować jego funkcje wysokiego poziomu, aby wykonać te poziomy. Różnica między testami systemowymi/end-to-end a testami integracyjnymi polega na tym, że te pierwsze obejmują wszystkie komponenty systemu i użytkownika końcowego (zazwyczaj podszywającego się pod niego). Innymi słowy, testy systemowe i end-to-end oceniają SUT poprzez interfejs użytkownika (UI). Interfejs użytkownika może być graficzny (GUI) lub niegraficzny (np. tekstowy lub innego typu).

> Piramida Testów to klasyczna reprezentacja poziomów testowania. Mike Cohn po raz pierwszy ukuł tę koncepcję w 2009 roku. W swojej pierwotnej koncepcji Cohn zalecał dużą liczbę testów jednostkowych jako podstawę działań testowych. Kolejne poziomy (np. testy integracyjne) są mniej liczne na każdym etapie, ale zazwyczaj droższe (pod względem wysiłku włożonego w rozwój i utrzymanie) oraz wolniejsze (pod względem czasu wykonania). Ta propozycja może być niepraktyczna dla wielu projektów, ponieważ testy jednostkowe nie zawsze tworzą kompleksowy zestaw testów. Z tego powodu inni autorzy definiują różne kształty dla poziomów testowania, takie jak "trofeum testowe" (w którym warstwa pośrednia, tj. testy integracyjne, jest największa). Ponieważ znaczenie różnych kategorii testów może się różnić w zależności od projektu, używam podstawowej struktury stosu do reprezentowania różnych poziomów testowania.
> 

![obraz.png](Teoria%20dot%20test%C3%B3w%20automatycznych/obraz%201.png)

Testy akceptacyjne to najwyższy poziom prezentowanego stosu. Na tym poziomie użytkownik końcowy uczestniczy w procesie testowania. Celem testów akceptacyjnych jest podjęcie decyzji czy system oprogramowania spełnia oczekiwania użytkownika końcowego. Podobnie jak w przypadku testów kompleksowych, testy akceptacyjne sprawdzają cały system i jego zależności. Dlatego testy akceptacyjne wykorzystują również interfejs użytkownika do przeprowadzenia walidacji SUT. walidacji.

> Głównym celem Selenium WebDriver jest implementacja testów end-to-end. Niemniej jednak, możemy użyć WebDrivera do przeprowadzenia testów systemowych podczas wykonywania wywołań backendowych wykonywanych przez testowaną stronę internetową. Co więcej, możemy użyć Selenium WebDriver w połączeniu z narzędziem Behavior-Driven Development (BDD) do implementacji testów akceptacyjnych
> 

> **Weryfikacja i walidacja**
> 

> Niższe poziomy stosu testów, które omówiliśmy (testy jednostkowe, integracyjne, systemowe i end-to-end), należą do testowania rozwojowego. Testowanie rozwojowe to proces prowadzony przez zespół tworzący system oprogramowania (czyli programistów, testerów itp.) podczas fazy konstrukcji w cyklu życia oprogramowania. Testowanie rozwojowe jest rodzajem **weryfikacji**, ponieważ oceniamy, czy oprogramowanie spełnia określone wymagania funkcjonalne i niefunkcjonalne (czyli jego specyfikację). Zgodnie z klasyczną definicją Barry’ego Boehma z 1984 roku, weryfikacja pozwala odpowiedzieć na pytanie: **„Czy budujemy produkt właściwie?”** Najwyższy poziom stosu testów przedstawionego na rysunku 1-5 (czyli testy akceptacyjne) należy do **testowania użytkownika**, ponieważ angażuje końcowego użytkownika w proces testowania. Testy akceptacyjne są rodzajem **walidacji**, ponieważ ich celem jest wykazanie, że system spełnia oczekiwania użytkownika końcowego. Walidacja to proces bardziej ogólny niż weryfikacja, ponieważ specyfikacja systemu nie zawsze odzwierciedla rzeczywiste życzenia lub potrzeby użytkownika. Zatem zgodnie z Boehmem, walidacja pozwala odpowiedzieć na pytanie: **„Czy budujemy właściwy produkt?”**
> 

### **Rodzaje testowania**

W zależności od strategii projektowania przypadków testowych możemy wdrażać różne typy testów. Dwa główne typy testowania to:

**Testowanie funkcjonalne (znane również jako testowanie behawioralne lub „zamknięta skrzynka” – *closed-box testing*)**

Ocena zgodności oprogramowania z oczekiwanym zachowaniem, czyli wymaganiami funkcjonalnymi.

**Testowanie strukturalne (znane również jako testowanie „przezroczystej skrzynki” – *clear-box testing*)**

Określa, czy struktura kodu programu zawiera błędy. W tym celu testerzy muszą znać wewnętrzną logikę danego fragmentu oprogramowania.

Różnica między tymi typami testowania polega na tym, że testy funkcjonalne opierają się na **odpowiedzialności**, a testy strukturalne na **implementacji**. Oba typy można stosować na dowolnym poziomie testowania (jednostkowym, integracyjnym, systemowym, end-to-end lub akceptacyjnym). Jednak testy strukturalne są najczęściej przeprowadzane na poziomie jednostkowym lub integracyjnym, ponieważ te poziomy umożliwiają większą kontrolę nad przebiegiem wykonania kodu.

Określenia **black-box** i **white-box** są innymi nazwami testowania funkcjonalnego i strukturalnego. Niemniej jednak, te nazwy nie są już zalecane, ponieważ branża technologiczna dąży do przyjęcia bardziej **inkluzywnej terminologii** i unikania potencjalnie krzywdzącego języka.

**Odmiany testowania funkcjonalnego:**

- **Testowanie interfejsu użytkownika (UI)** – znane jako testowanie GUI, gdy interfejs jest graficzny. Sprawdza, czy elementy wizualne aplikacji działają zgodnie z oczekiwaniami. Różni się od testowania systemowego i end-to-end, ponieważ UI testuje sam interfejs, a systemowe testy oceniają cały system poprzez UI.
- **Testowanie negatywne** – sprawdza zachowanie systemu w warunkach nieoczekiwanych (np. wyjątki). Jest przeciwieństwem testowania pozytywnego, które bada typowe scenariusze (tzw. *happy path*).
- **Testowanie międzyprzeglądarkowe (cross-browser testing)** – specyficzne dla aplikacji webowych, ma na celu sprawdzenie zgodności aplikacji z różnymi przeglądarkami, ich wersjami i systemami operacyjnymi.

---

**Testowanie niefunkcjonalne**

Obejmuje strategie oceniające atrybuty jakościowe systemu (czyli wymagania niefunkcjonalne). Przykłady:

- **Testowanie wydajności** – ocenia metryki, takie jak czas odpowiedzi, stabilność, niezawodność i skalowalność. Celem nie jest znalezienie błędów, ale wąskich gardeł systemu.
    - **Testowanie obciążeniowe (load testing)** – symuluje wielu użytkowników jednocześnie.
    - **Testowanie przeciążeniowe (stress testing)** – sprawdza granice wydolności systemu.
- **Testowanie bezpieczeństwa** – ocenia aspekty takie jak:
    - *Poufność* – ochrona przed nieautoryzowanym ujawnieniem danych.
    - *Uwierzytelnianie* – potwierdzenie tożsamości użytkownika.
    - *Autoryzacja* – ustalenie uprawnień użytkownika.
- **Testowanie użyteczności (UX)** – ocenia, jak przyjazna jest aplikacja dla użytkownika. Podtypem jest:
    - **Testowanie A/B** – porównanie różnych wersji aplikacji w celu sprawdzenia, która jest lepsza dla użytkowników.
- **Testowanie dostępności** – ocenia, czy aplikacja jest dostępna dla osób z niepełnosprawnościami.

---

**Selenium WebDriver s**łuży głównie do **testów funkcjonalnych** – interakcja z interfejsem webowym w celu oceny zachowania aplikacji.

Choć nie jest to jego główne zastosowanie, WebDriver może być wykorzystywany także do **testów niefunkcjonalnych**, np.:

- testy wydajności,
- testy bezpieczeństwa,
- testy dostępności,
- testy lokalizacji (ocena zachowania aplikacji w różnych ustawieniach regionalnych).

---

### **Metodyki testowania**

Cykl życia oprogramowania (SDLC) to zestaw czynności potrzebnych do stworzenia systemu. Moment projektowania testów zależy od procesu wytwórczego (np. iteracyjny, waterfall, agile). Główne metodyki:

- **Test-Driven Development (TDD) -** Testy są tworzone **przed** kodem. Najpierw powstaje test (który początkowo nie przechodzi), potem kod, który pozwala testowi przejść, a na końcu następuje refaktoryzacja kodu. Popularne w metodykach zwinnych, takich jak Extreme Programming (XP).
- **Test Last Development (TLD) -** Testy są tworzone **po** implementacji systemu. Typowe dla tradycyjnych metod (waterfall, spiral, RUP).
- **Behavior-Driven Development (BDD) -** Wywodzi się z TDD, ale większy nacisk kładzie na rozmowy między użytkownikiem końcowym a zespołem projektowym.
    
    Testy akceptacyjne tworzy się w formie scenariuszy w strukturze:
    
    - **Given** – kontekst początkowy,
    - **When** – zdarzenie uruchamiające scenariusz,
    - **Then** – oczekiwany rezultat.
        
        Często używany z WebDriverem i Cucumberem.
        

TODO: 

`Brakuje opisu procesu:`

- `analiza wymagań`
- `planowanie testów`
- `projektowanie testów`
- `implementacja`
- `wykonanie testów`
- `raportowanie defektów`
- `zamknięcie testów`

`Dobrze byłoby:`

- `wskazać **wejścia / wyjścia** każdej fazy`
- `powiązać STLC z SDLC`

`TODO:` 

**`Funkcjonalne`**

- `testy funkcjonalne`
- `testy regresji`
- `testy dymne (smoke)`
- `testy sanity`

**`Niefunkcjonalne`**

- `wydajnościowe`
- `obciążeniowe`
- `stresowe`
- `użyteczności`
- `bezpieczeństwa`
- `kompatybilności`
- `niezawodności`

### `Techniki czarnoskrzynkowe`

- `klasy równoważności`
- `analiza wartości brzegowych`
- `tablice decyzyjne`
- `testowanie oparte na przypadkach użycia`

### `Techniki białoskrzynkowe`

- `pokrycie instrukcji`
- `pokrycie decyzji`
- `pokrycie ścieżek`

### `Techniki oparte na doświadczeniu`

- `testy eksploracyjne`
- `checklisty`
- `error guessing`

## DOM

**DOM (Document Object Model)** to wieloplatformowy interfejs umożliwiający reprezentację dokumentów podobnych do XML (np. stron internetowych opartych na HTML) w postaci struktury drzewiastej. Poniżej przedstawiono prostą stronę internetową, a odpowiadająca jej struktura DOM w pamięci jest zilustrowana na rysunku. Jak widać, każdy znacznik HTML (np. **`<html>`**, **`<head>`**, **`<body>`**, **`<a>`** itd.) generuje węzeł (lub element) w tym drzewie. Każdy standardowy atrybut HTML (np. **`charset`**, **`href`**) odpowiada równoważnej właściwości DOM. Ponadto, tekst zawarty wewnątrz znaczników HTML jest dostępny w strukturze drzewa.

Języki takie jak JavaScript wykorzystują metody DOM do dostępu i modyfikacji struktury drzewa. Dzięki temu strony internetowe stają się dynamiczne i mogą zmieniać swój układ oraz zawartość w odpowiedzi na zdarzenia użytkownika.

**Kluczowe terminy:**

- **Struktura drzewa** – hierarchiczna organizacja elementów strony.
- **Węzeł/element** – reprezentacja znacznika HTML w DOM.
- **Właściwości DOM** – odpowiedniki atrybutów HTML w modelu obiektowym.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>DOM example</title>
</head>
<body>
<h1>Heading text</h1>
<a href="#">Link text</a>
</body>
</html>
```

![image.png](Teoria%20dot%20test%C3%B3w%20automatycznych/image.png)

# **Wzorzec Page Object Model (POM)**

**Page Object Model (POM)** to wzorzec projektowy stosowany w automatyzacji testów interfejsu użytkownika. Jego celem jest odizolowanie logiki operacji na elementach UI od logiki samych testów. Dzięki temu każdy element strony (przycisk, pole tekstowe itp.) jest reprezentowany w dedykowanej klasie (tzw. „page class”), a testy wywołują metody tych klas zamiast bezpośrednio korzystać z selektorów. W praktyce oznacza to, że projektujemy aplikację (system pod testem – SUT) zgodnie z paradygmatem obiektowym: każdą stronę modelujemy jako obiekt z metodami reprezentującymi akcje użytkownika[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=POM%20bazuje%20na%20reprezentacji%20struktury,u%C5%BCytkownik%20mo%C5%BCe%20wykona%C4%87%20na%20stronie)[selenium.dev](https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/#:~:text=Page%20Object%20is%20a%20Design,are%20located%20in%20one%20place). Klasy stron zawierają lokalizatory elementów i metody interakcji, co sprawia, że kod staje się bardziej modułowy i czytelny [jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=Page%20Object%20Model%20,kt%C3%B3rych%20nast%C4%99pnie%20korzystamy%20w%20testach) [jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=POM%20bazuje%20na%20reprezentacji%20struktury,u%C5%BCytkownik%20mo%C5%BCe%20wykona%C4%87%20na%20stronie).

## **Zasada działania POM**

Zasadnicza zasada wzorca POM została opisana m.in. następującym cytatem (przetłumaczonym na język polski):

> „Zasadą działania wzorca projektowego POM jest oddzielenie logiki obsługi elementów interfejsu użytkownika do oddzielnych klas (zwanych klasami stron) od logiki testów. Innymi słowy, modelujemy wygląd i zachowanie naszego systemu pod testem zgodnie z paradygmatem obiektowym, czyli jako obiekty stron. Następnie te obiekty stron są używane przez testy Selenium WebDriver.”
> 

Oznacza to w praktyce, że każda strona aplikacji jest reprezentowana przez osobną klasę z metodami opisującymi możliwe działania (np. wypełnij formularz, kliknij przycisk)[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=POM%20bazuje%20na%20reprezentacji%20struktury,u%C5%BCytkownik%20mo%C5%BCe%20wykona%C4%87%20na%20stronie). Testy automatyczne zamiast bezpośrednio manipulować interfejsem korzystają z tych metod – przykładowo, zamiast pisać **`driver.findElement(...).click()`**, test wywołuje **`stronaLogowania.kliknijPrzyciskLogowania()`**. Taki podział pozwala na **czyste oddzielenie logiki testu od szczegółów implementacji UI**[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=Page%20Object%20Model%20pomaga%20oddzieli%C4%87,jedynie%20odpowiednie%20obiekty%20Page%20Object)[selenium.dev](https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/#:~:text=Page%20Object%20is%20a%20Design,are%20located%20in%20one%20place). Gdy interfejs się zmieni (np. zmienią się selektory elementów), modyfikujemy tylko kod klas POM, a testy pozostają bez zmian[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=Page%20Object%20Model%20pomaga%20oddzieli%C4%87,jedynie%20odpowiednie%20obiekty%20Page%20Object)[selenium.dev](https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/#:~:text=Page%20Object%20is%20a%20Design,are%20located%20in%20one%20place).

## **Wpływ na strukturę kodu testów**

Stosowanie POM narzuca przejrzystą strukturę kodu. Zwykle wydziela się w projekcie oddzielny katalog (np. **`pages`** lub **`pageobjects`**), w którym znajdują się klasy reprezentujące poszczególne strony lub widoki aplikacji. Każda taka klasa zawiera lokalizatory elementów i metody akcji. Testy umieszczamy w innych klasach (np. w katalogu **`tests`**), które importują obiekty stron i wywołują ich metody do przeprowadzania testów. Taki podział kodu (page classes vs. test classes) tworzy **czytelny i modułowy projekt**: elementy UI są zlokalizowane w jednym miejscu, a testy opisują tylko sekwencję kroków użytkownika [selenium.dev](https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/#:~:text=Page%20Object%20is%20a%20Design,are%20located%20in%20one%20place)[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=POM%20bazuje%20na%20reprezentacji%20struktury,u%C5%BCytkownik%20mo%C5%BCe%20wykona%C4%87%20na%20stronie). Przykładowo, zmiana ścieżki do przycisku wymaga aktualizacji tylko w jednej klasie strony, nie we wszystkich testach. W efekcie kod testów jest krótszy i bardziej wyrazisty – zamiast ciągów instrukcji używamy dobrze nazwanych metod z Page Object.

## **Zalety wzorca Page Object Model**

- **Oddzielenie logiki testów od logiki interfejsu:** POM pozwala utrzymać czysty kod testów, koncentrując testy na zachowaniu aplikacji, a wszystkie techniczne szczegóły (lokalizatory, metody interakcji) przenosi do klas stron[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=Page%20Object%20Model%20pomaga%20oddzieli%C4%87,jedynie%20odpowiednie%20obiekty%20Page%20Object)[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,czytelno%C5%9B%C4%87%20u%C5%82atwia%20tworzenie%20skutecznych%20test%C3%B3w). Dzięki temu testy stają się bardziej odporne na zmiany UI – modyfikujemy tylko kod w klasach POM[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=Page%20Object%20Model%20pomaga%20oddzieli%C4%87,jedynie%20odpowiednie%20obiekty%20Page%20Object).
- **Modułowość i reużywalność:** Każda strona jest osobnym modułem (klasą), co zwiększa **modularność** kodu. Tę samą klasę strony można używać w wielu testach, unikając powtarzania kodu[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,czytelno%C5%9B%C4%87%20u%C5%82atwia%20tworzenie%20skutecznych%20test%C3%B3w)[lambdatest.com](https://www.lambdatest.com/blog/page-object-model-in-selenium-python/#:~:text=This%20makes%20the%20code%20more,test%20scripts%20are%20stored%20separately). Centralizacja elementów interfejsu w jednym miejscu pozwala łatwo ponownie wykorzystać funkcjonalności (np. logowanie) w wielu scenariuszach[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,czytelno%C5%9B%C4%87%20u%C5%82atwia%20tworzenie%20skutecznych%20test%C3%B3w)[qatouch.com](https://www.qatouch.com/blog/page-object-model-in-selenium/#:~:text=1,focusing%20on%20the%20technical%20details).
- **Czytelność i zrozumiałość:** POM sprawia, że testy są zapisywane w bardziej zrozumiałej formie: zamiast „surowych” wywołań WebDrivera mamy opisowe metody („kliknijZaloguj”, „wpiszHasło” itp.). Dzięki temu testy wyraźnie pokazują kroki użytkownika, a nie szczegóły implementacyjne[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,czytelno%C5%9B%C4%87%20u%C5%82atwia%20tworzenie%20skutecznych%20test%C3%B3w)[qatouch.com](https://www.qatouch.com/blog/page-object-model-in-selenium/#:~:text=2,in%20knowledge%20transfer%20within%20teams). Takie podejście ułatwia czytanie i przegląd kodu testów przez cały zespół.
- **Redukcja duplikacji kodu:** Wszystkie lokalizatory i metody są zgrupowane w klasach POM, więc unika się powielania tych samych wywołań w wielu miejscach[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,czytelno%C5%9B%C4%87%20u%C5%82atwia%20tworzenie%20skutecznych%20test%C3%B3w). Jeśli ten sam element występuje na różnych stronach, można go umieścić w jednym obiekcie i używać globalnie. To znacznie upraszcza utrzymanie – zmiana selektora w jednym miejscu aktualizuje wszystkie testy.
- **Skalowalność projektu:** POM ułatwia rozwój testów w miarę rozbudowy aplikacji. W nowych wersjach aplikacji dodaje się kolejne strony czy funkcje, co po prostu skutkuje tworzeniem nowych klas POM. Istniejące testy pozostają niezmienione, co sprawia, że framework testowy jest łatwo skalowalny[qatouch.com](https://www.qatouch.com/blog/page-object-model-in-selenium/#:~:text=3,test%20suite%20expands%20over%20time).

## **Ograniczenia i wady POM**

- **Początkowy nakład i złożoność:** Wprowadzenie POM wymaga dodatkowej pracy przy planowaniu i implementacji. Trzeba zaprojektować i stworzyć osobne klasy dla każdej strony oraz metody do interakcji. To wiąże się z większym czasem na początkowe przygotowanie i umiejętnościami programistycznymi zespołu[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,wi%C4%99kszej%20uwagi%20na%20etapie%20planowania)[linkedin.com](https://www.linkedin.com/advice/1/what-benefits-challenges-using-page-object-model#:~:text=The%20Page%20Object%20Model%20,handling%20web%20elements%20and%20actions). W małych lub prostych projektach może się to wydawać nadmiarowe.
- **Wyższe wymagania kompetencyjne:** POM zakłada, że członkowie zespołu znają koncepcję programowania obiektowego i potrafią dobrze organizować kod. Trudniejsze elementy (np. paginacja, dynamiczne tabele, okna modalne) muszą być opisane w metodach klas POM, co wymaga doświadczenia[jaktestowac.pl](https://jaktestowac.pl/lesson/pw1s03l02/#:~:text=,wi%C4%99kszej%20uwagi%20na%20etapie%20planowania)[linkedin.com](https://www.linkedin.com/advice/1/what-benefits-challenges-using-page-object-model#:~:text=The%20Page%20Object%20Model%20,handling%20web%20elements%20and%20actions).
- **Dodatkowa warstwa abstrakcji:** Modelowanie każdej strony jako osobnego obiektu wprowadza kolejny poziom abstrakcji. Może to utrudnić debugowanie, gdy test nie przechodzi – trzeba prześledzić zarówno kod testu, jak i kod odpowiedniej klasy POM. Jeśli klasy stron nie są dobrze zaprojektowane, mogą stać się zbyt rozbudowane i skomplikowane[linkedin.com](https://www.linkedin.com/advice/1/what-benefits-challenges-using-page-object-model#:~:text=The%20Page%20Object%20Model%20,handling%20web%20elements%20and%20actions)[linkedin.com](https://www.linkedin.com/advice/1/what-benefits-challenges-using-page-object-model#:~:text=While%20POM%20offers%20a%20lot,processes%2C%20leading%20to%20bloated%20code).
- **Nadmiar kodu w niesprzyjających scenariuszach:** W niektórych sytuacjach (np. gdy testujemy pojedyncze małe interakcje) korzyści POM mogą nie przeważać nad kosztem jego utrzymania. Dodatkowo, POM nie rozwiązuje wszystkich problemów testowych – np. kwestie synchronizacji (czekania na elementy) czy obsługi wielu okien należy nadal obsłużyć oddzielnie.

## **Narzędzia i języki programowania**

Wzorzec Page Object Model jest **językowo niezależny** (ang. *language-agnostic*), ale najczęściej stosuje się go w ramach testów z użyciem Selenium WebDriver lub podobnych frameworków. Typowe narzędzia i konteksty to:

- **Selenium WebDriver**: To najpopularniejsza biblioteka do automatyzacji testów UI. POM jest powszechnie używany w projektach Selenium we wszystkich obsługiwanych językach – Java, C#, Python, JavaScript (np. Node.js), Ruby itp.[lambdatest.com](https://www.lambdatest.com/blog/page-object-model-tutorial-selenium-csharp/#:~:text=Page%20Object%20Model%20is%20language,languages%20like%20Python%2C%20Java%2C%20etc)[testingbot.com](https://testingbot.com/resources/articles/page-object-model-appium#:~:text=The%20Page%20Object%20Model%2C%20also,and%20reduces%20overall%20code%20duplication). Dzięki temu niezależnie od technologii backendowej można korzystać z tego wzorca.
- **Appium**: W automatyzacji testów mobilnych (Android/iOS) Appium używa tych samych założeń co Selenium. POM jest także popularny przy testach aplikacji mobilnych – umożliwia abstrakcję ekranów aplikacji do klas Page Object.
- **Inne frameworki webowe:** W nowoczesnych narzędziach JavaScript (np. Playwright, Cypress, WebdriverIO) można również implementować POM, chociaż nie wszystkie przykładają do tego tak dużą wagę jak Selenium. Zwykle tworzy się w nich osobne moduły lub klasy opisujące elementy stron. Nawet jeśli dany framework oferuje inne mechanizmy (np. *page objects* w Playwright), zasada rozdzielenia logiki pozostaje podobna.

Podsumowując, wzorzec POM jest najczęściej stosowany w projektach z Selenium (i jego pochodnych), w dowolnym języku programowania, a także w automatyzacji mobilnej (Appium). Jego elastyczność i szerokie wsparcie narzędziowe sprawiają, że jest jednym z podstawowych sposobów organizacji kodu testów UI.

## Page Objects – Reprezentacja funkcji aplikacji

**Page Object** to instancja klasy strony (ang. *page class*) używana w konkretnych przypadkach testowych. Klasy te pełnią rolę pojedynczego repozytorium operacji dostępnych na danej stronie. Oznacza to, że:

- zawierają **lokatory elementów** (np. przycisków, pól tekstowych),
- implementują **metody interakcji** (np. kliknięcia, wypełnianie formularzy),
- **kapsułkują funkcje**, jakie aplikacja udostępnia z punktu widzenia użytkownika.

Takie podejście **pozwala oddzielić kod interfejsu (UI)** od samej logiki testów i **unikać powielania kodu**. Instancje klas stron (czyli właśnie page objects) są tworzone i używane w różnych przypadkach testowych, a testy wykorzystują metody tych obiektów, zamiast bezpośrednio manipulować elementami interfejsu.

W rezultacie:

- testy stają się bardziej czytelne i wyrażają *co* ma być testowane, a nie *jak*,
- łatwo aktualizować interfejs (zmiany w selektorach wymagają zmian tylko w klasie strony),
- można implementować złożone scenariusze end-to-end jako zestaw wywołań metod tych obiektów.

## Robust Page Objects – wzmocnienie struktury POM

Aby zwiększyć **odporność i skalowalność** wzorca Page Object, stosuje się tzw. **Robust Page Objects** – solidniejsze, bardziej zaawansowane wersje klas stron. Kluczowe techniki to:

- **Dziedziczenie i klasa bazowa (Base Page):**
    
    Jeśli aplikacja posiada wiele stron, często mają one wspólne elementy (np. pasek nawigacyjny, komunikaty błędów, przyciski „Zaloguj”/„Wyloguj”). Dobrym podejściem jest stworzenie **klasy bazowej**, która zawiera wspólną logikę i z której dziedziczą wszystkie inne klasy stron. Taka klasa bazowa może zawierać np.:
    
    - mechanizmy oczekiwania na elementy (waits),
    - metody logowania użytkownika,
    - obsługę błędów i wyjątków,
    - logikę nawigacji.
- **Kapsułkowanie operacji:**
    
    Wszystkie akcje użytkownika na stronie (np. „zaloguj się”, „wyszukaj produkt”) powinny być **ujednolicone i zamknięte w metodach**. Testy nie powinny sięgać do wnętrza obiektu strony, tylko korzystać z metod interfejsu tej klasy.
    
- **Eliminacja duplikacji:**
    
    Dzięki wspólnej klasie bazowej i dobremu podziałowi metod, możliwe jest unikanie powtórzeń kodu. Utrzymanie kodu testów staje się prostsze, ponieważ logika znajduje się w jednym miejscu.
    
- **Obsługa dynamicznych elementów i złożonych komponentów:**
    
    W bardziej rozbudowanych wersjach klas POM można również umieszczać obsługę modalnych okienek, ramek (iframes), dynamicznych list czy elementów AJAX.
    

## Domain Specific Language (DSL) – testy w formie czytelnego „języka”

Po zbudowaniu dobrze zaprojektowanych klas Page Object, możemy pójść o krok dalej i potraktować metody tych klas jako **język dziedzinowy (DSL)**, który opisuje operacje na aplikacji w sposób zbliżony do języka naturalnego.

**Czym jest DSL?**

DSL (Domain Specific Language) to specjalizowany język programistyczny służący do opisu operacji w konkretnej dziedzinie (tu: testowania interfejsu użytkownika). DSL w kontekście POM polega na tym, że:

- metody klas stron są na tyle czytelne i dobrze nazwane, że test wygląda jak **opis scenariusza biznesowego**,
- testy nie zawierają niskopoziomowych komend Selenium, a jedynie operacje udostępnione przez klasy stron.

**Korzyści z podejścia DSL:**

- testy są **czytelne dla osób nietechnicznych** (np. analityków biznesowych),
- zmniejsza się zależność testów od detali implementacyjnych (np. lokalizatorów),
- ułatwia to **utrzymanie** i **rozwijanie** testów przy zmianach UI.

**Jak to działa?**

Page Object zawiera metody, które „ukrywają” wszystkie szczegóły działania Selenium, a testy wywołują tylko te metody. Przykładowo, zamiast pisać:

```java
Przykładowo, zamiast pisać:
driver.findElement(By.id("username")).sendKeys("user");
//Używamy:
loginPage.enterUsername("user");
```

## Page Factory – automatyczna inicjalizacja elementów w Page Object

**Page Factory** to mechanizm wbudowany w Selenium WebDriver, który **upraszcza tworzenie klas Page Object**, automatycznie inicjalizując pola reprezentujące elementy strony.

### Główne cechy Page Factory

- Pozwala **zdefiniować elementy strony za pomocą adnotacji** (ang. *annotations*).
- Automatycznie **inicjalizuje pola klasy**, dzięki czemu nie trzeba pisać kodu `driver.findElement(...)` dla każdego elementu.
- Może **poprawić czytelność** i **utrzymanie** klas Page Object, szczególnie w większych projektach.

---

## Najważniejsze adnotacje i klasy

| Element | Opis |
| --- | --- |
| `@FindBy` | Adnotacja używana do wskazania, jak znaleźć element (np. po `id`, `name`, `xpath`). Przypisuje znaleziony element do pola klasy. |
| `@FindAll` | Umożliwia zdefiniowanie **alternatywnych lokalizatorów** – Selenium spróbuje znaleźć element, używając dowolnego z nich. |
| `PageFactory.initElements()` | Metoda statyczna, która **inicjalizuje wszystkie pola oznaczone `@FindBy` lub `@FindAll`**, czyli sprawia, że są gotowe do użycia w testach. |
| `@CacheLookup` | **Buforuje (cache'uje)** element po pierwszym odnalezieniu, co **przyspiesza testy** na statycznych stronach, ale **może powodować błędy** w dynamicznych aplikacjach, gdy elementy się zmieniają. |

---

### Jak działa Page Factory?

1. W klasie Page Object oznaczasz pola adnotacjami:
    
    ```java
    @FindBy(id = "username")
    WebElement usernameInput;
    ```
    
2. W konstruktorze lub metodzie inicjalizujesz klasę:
    
    ```java
    PageFactory.initElements(driver, this);
    ```
    
3. Od tej pory możesz używać elementu bez ręcznego szukania:
    
    ```java
    usernameInput.sendKeys("admin");
    ```
    

---

### Uwaga na `@CacheLookup`

- Działa dobrze w **statycznych aplikacjach**, gdzie DOM się nie zmienia.
- Może powodować błędy (np. `StaleElementReferenceException`), jeśli element zniknie lub zostanie przeładowany – wtedy Selenium nadal próbuje użyć starego, już nieistniejącego elementu.
- W dynamicznych stronach (np. z AJAX) raczej **nie zaleca się go używać**.

TODO: 
1. Najważniejsza luka: **teoria automatyzacji testów**

Masz dużo *jak pisać testy*, ale mało *dlaczego i jak je projektować*.

### 1.1. Piramida testów (z perspektywy automatyzacji)

Powinna się pojawić obowiązkowo:

- Unit tests (najtańsze, najszybsze)
- Integration / API tests
- UI / E2E tests (najdroższe, najmniej stabilne)

Warto dopisać:

- dlaczego **UI ≠ podstawa automatyzacji**
- koszty utrzymania testów UI
- gdzie Selenium / Playwright ma sens, a gdzie nie

---

## 2. Architektura testów automatycznych – duży brak

Masz PageFactory, ale to **tylko warstwa UI**.

### 2.1. Wzorce projektowe w automatyzacji

Rekomendowane sekcje:

- Page Object Model (POM)
- Screenplay Pattern (kiedy POM przestaje wystarczać)
- Fluent Interface
- Factory / Builder (np. do danych testowych)
- Separation of concerns (test ≠ logika ≠ selektory)

👉 To bardzo podnosi „senior-level” notatek.

---

## 3. Dane testowe – praktycznie nieobecne

Automatyzacja bez danych = niestabilność.

Warto dodać:

- statyczne vs dynamiczne dane testowe
- generatory danych (faker)
- test data builders
- izolacja danych testowych
- sprzątanie danych po teście

---

## 4. Stabilność testów (flaky tests)

Tego tematu **nie ma**, a jest kluczowy.

### 4.1. Przyczyny niestabilności

- synchronizacja (waits)
- dynamiczny DOM
- zależności między testami
- dane współdzielone
- środowisko

### 4.2. Jak z nimi walczyć

- explicit waits vs implicit
- retry – kiedy TAK, kiedy NIE
- idempotentne testy
- testy niezależne

---

## 5. Synchronizacja i asynchroniczność (UI & backend)

Masz DOM, ale nie masz:

- różnicy między presence / visibility / clickability
- polling vs hard sleep
- synchronizacji z backendem (API calls, network idle)
- event-based waiting (Playwright / BiDi)

To naturalne rozszerzenie obecnych notatek.

---

## 6. Automatyzacja poza UI (ważne)

Jeśli to ma być strona „automatyzacja”, UI nie może być jedynym filarem.

### 6.1. Testy API

Choćby skrótowo:

- po co automatyzować API
- testy kontraktowe
- schematy JSON
- mocki vs real backend

### 6.2. Kontrakty i integracje

- consumer-driven contracts
- mock server (WireMock / MockServer)
- kiedy mockować, a kiedy nie

---

## 7. CI/CD i uruchamianie testów

Tego brakuje całkowicie, a jest kluczowe.

Sekcja do dodania:

- local vs CI execution
- headless vs headed
- równoległość testów
- artefakty testowe (raporty, logi, screeny)
- flaki w CI ≠ flaki lokalnie

**Continuous Integration (CI)**

CI to praktyka, w której członkowie zespołu **ciągle budują, testują i integrują** zmiany.

Pojęcie CI zostało wprowadzone przez Grady’ego Boocha w 1991 roku. Dziś to powszechna strategia.

**Etapy CI:**

1. **Repozytorium kodu źródłowego** – zazwyczaj zarządzane przez system kontroli wersji (VCS), np. Git (najpopularniejszy), CVS lub SVN. Platformy takie jak GitHub, GitLab, Bitbucket ułatwiają współpracę.
2. **Commity i synchronizacja** – programiści wprowadzają zmiany lokalnie, a następnie „commitują” je do repozytorium. Każdy commit uruchamia budowanie i testowanie.
3. **Testy regresji** – zestaw testów sprawdzających, czy nowe zmiany nie zepsuły działania. Może zawierać testy jednostkowe, integracyjne, end-to-end itp.

Gdy liczba testów jest zbyt duża:

- wybieramy tylko niektóre – np. **smoke testing** (krytyczna funkcjonalność) lub **sanity testing** (podstawowe działanie),
- cała paczka testów może być wykonywana np. co noc.

Do wdrożenia CI potrzebna jest infrastruktura serwera budującego (build server). W przypadku niepowodzenia testów regresji, deweloper otrzymuje raport.

---

**Rozszerzenie CI – Continuous Delivery i Deployment**

1. **Continuous Delivery (CD)** – po CI, oprogramowanie jest wdrażane na środowisku staging (testowym), gdzie wykonywane są automatyczne testy akceptacyjne.
2. **Continuous Deployment** – ostateczny krok: wersja trafia bezpośrednio na środowisko produkcyjne.

![image.png](Teoria%20dot%20test%C3%B3w%20automatycznych/image%201.png)

---

## 8. Observability testów

Masz logi, ale warto uzupełnić:

- sensowny logging w testach
- zrzuty ekranu vs wideo
- network logs
- trace / HAR / Playwright trace

---

## 9. Antywzorce w automatyzacji (bardzo wartościowe)

Świetna sekcja na „dojrzałość” notatek:

- testy zależne od kolejności
- logika biznesowa w testach
- nadmierne użycie sleep
- testy „klikaj-wszystko”
- Page Object jako dump selektorów

<aside>
💡

Informacje na temat wersjonowania:  [https://semver.org/](https://semver.org/)

</aside>