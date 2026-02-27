# Przeglądarki a Selenium

Link do dostępnych przeglądarek: https://googlechromelabs.github.io/chrome-for-testing/#stable

ChromeDriver (dla manualnego definiowania: https://developer.chrome.com/docs/chromedriver/downloads?hl=pl

<aside>
💡

Od wersji 115 Chrome i Driver zostały ze sobą zintegrowane pod kątem wydań: [https://developer.chrome.com/blog/chrome-for-testing?hl=pl](https://developer.chrome.com/blog/chrome-for-testing?hl=pl) 

</aside>

Firefox: https://www.mozilla.org/en-US/firefox/releases/

Geckodriver: https://github.com/mozilla/geckodriver/releases

![image.png](Przegl%C4%85darki%20a%20Selenium/image.png)

Wyłączenie automatycznej aktualizacji przeglądarek nie jest możliwe oficjalnymi sposobami. Często w testach automatycznych zależy nam, żeby przeglądarka nie aktualizowała się samoczynnie – pozwala to zachować spójność środowiska. Oto jak można to zrobić dla najpopularniejszych przeglądarek:

**Google Chrome:** Chrome nie pozwala wyłączyć aktualizacji przez zwykłe ustawienia, ale są na to sposoby:

- **Windows:**
    - Otwórz `services.msc` i znajdź usługi `Google Update Service (gupdate)` oraz `gupdatem`. Ustaw je na „Wyłączony”.
    - Możesz też edytować rejestr (`regedit`) lub, jeśli jesteś administratorem, użyć zasad grupy (GPO).
- **Działa tylko wtedy gdy Chrome był instalowany tylko dla bieżącego użytkownika:** Usługa Google Update może nie być zainstalowana jako systemowa usługa Windows i nie pojawi się na liście w services.msc.

**Co zrobić, jeśli nie ma usługi Google Update?**

Możesz spróbować innych metod blokowania automatycznych aktualizacji Chrome:

- **Zmień nazwę folderu „Update”:**
    
    Przejdź do katalogu instalacyjnego Chrome, np. **`C:\Program Files (x86)\Google\Update`** i zmień nazwę folderu „Update” na inną (np. „Update_bak”). To uniemożliwi Chrome uruchomienie mechanizmu aktualizacji[7](https://stackoverflow.com/questions/71268342/disable-google-chrome-auto-update-in-windows-11)[9](https://kingpinbrowser.com/blog/chrome-disable-autoupdates/).
    
- **Wyłącz zadania w Harmonogramie zadań:**
    
    Otwórz Harmonogram zadań (**`taskschd.msc`**) i znajdź zadania:
    
    - GoogleUpdateTaskMachineCore
    - GoogleUpdateTaskMachineUA
        
        Wyłącz je, klikając prawym i wybierając „Wyłącz”[8](https://superuser.com/questions/974766/turn-off-chrome-updates).
        
- **Edytuj rejestr:**
    
    Otwórz **`regedit`** i przejdź do: **`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Update`** Jeśli nie ma takiego klucza, utwórz go. Następnie dodaj wartość DWORD o nazwie **`UpdateDefault`** i ustaw ją na **`0`**.
    
- **Zablokuj aktualizacje przez firewall:** Możesz zablokować dostęp Chrome do serwerów aktualizacji przez reguły zapory sieciowej.
- **macOS:** Zablokowanie aktualizacji wymaga edycji plików systemowych lub ustawienia flagi przez `com.google.Keystone`. Przykład: w Terminalu wpisz `defaults write com.google.Keystone.Agent checkInterval 0`

**Mozilla Firefox**

Tutaj jest łatwiej – wszystko zrobisz w ustawieniach zaawansowanych:

- Wejdź na stronę `about:config`
- Ustaw: `app.update.enabled` → `false /// app.update.auto` → `false`

**Microsoft Edge, Safari, Opera**

W tych przeglądarkach wyłączenie aktualizacji jest trudniejsze, bo są one mocno powiązane z systemem operacyjnym. Edge i Safari aktualizują się razem z systemem, a Opera nie daje prostych opcji blokady. **W testach najlepiej korzystać ze specjalnych wersji lub środowisk testowych**.

---

**Czy można mieć kilka wersji przeglądarki na jednym komputerze?**

**Google Chrome – Channel Builds**

Możesz zainstalować równolegle różne wersje Chrome:

| Wersja | Przeznaczenie | Link |
| --- | --- | --- |
| Stable | Wersja produkcyjna | [Chrome Stable](https://www.google.com/chrome) |
| Beta | Testy kolejnej wersji | [Chrome Beta](https://www.google.com/chrome/beta) |
| Dev | Wersja deweloperska | [Chrome Dev](https://www.google.com/chrome/dev) |
| Canary | Najnowsza, niestabilna | [Chrome Canary](https://www.google.com/chrome/canary) |

Każda z nich instaluje się osobno i działa niezależnie.

**Firefox**

- **Firefox ESR (Extended Support Release):** Stabilna wersja z dłuższym wsparciem, rzadziej aktualizowana.
- **Firefox Developer Edition:** Wersja dla deweloperów, można mieć równolegle ze stabilną.
- **Firefox Nightly:** Najnowsza, testowa wersja – dla odważnych.
- **Wersje portable:** Możesz korzystać z przenośnych wersji Firefoksa, które nie wymagają instalacji.

---

**Narzędzia do testowania przeglądarek**

Jeśli nie chcesz instalować wielu wersji przeglądarek lokalnie, możesz skorzystać z narzędzi do testowania w chmurze lub własnych środowisk:

- **Selenium Grid**
Pozwala uruchamiać testy na wielu maszynach i przeglądarkach w Twojej własnej infrastrukturze.
- **Docker + Selenium**
Szybko uruchomisz dowolną wersję przeglądarki w kontenerze Docker – idealne do testów automatycznych.

---

**Podsumowanie**

| Cel | Rozwiązanie |
| --- | --- |
| Wyłączyć aktualizacje | Najłatwiej w Firefox, w Chrome wymaga obejścia, Edge/Safari – trudne |
| Kilka wersji przeglądarki | Chrome: Stable/Beta/Dev/Canary; Firefox: ESR, Developer, Nightly, portable |
| Środowiska testowe | BrowserStack, Sauce Labs, LambdaTest, Selenium Grid, Docker + Selenium |

---

**Wskazówka:**
W testach automatycznych najlepiej korzystać z dedykowanych środowisk i narzędzi – oszczędza to czas i minimalizuje ryzyko problemów przy aktualizacjach przeglądarek!

<aside>
💡

Uwaga! Przeglądarki w Selenium Grid nie są automatycznie aktualizowane, dzięki czemu unikamy “przypadkowego” upgrade, który zepsuje testy. 

</aside>