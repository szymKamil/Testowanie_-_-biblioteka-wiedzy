# encapsulation

# Encapsulation (kapsułkowanie)

Encapsulation, czyli enkapsulacja, to jeden z fundamentalnych konceptów programowania obiektowego. Polega na ukrywaniu wewnętrznych szczegółów implementacji obiektów oraz na udostępnianiu tylko wybranych metod (interfejsu) do interakcji z tymi obiektami. Dzięki temu osiągamy kilka korzyści:

Ukrywanie szczegółów: Użytkownik klasy nie musi znać wewnętrznej struktury obiektu, co upraszcza jego użycie.

Zwiększenie bezpieczeństwa: Dostęp do danych obiektu można kontrolować, co pozwala na zapobieganie nieautoryzowanym modyfikacjom.

Łatwiejsza konserwacja: Zmiany w implementacji klasy nie wpływają na kod, który z tej klasy korzysta, o ile interfejs pozostaje niezmieniony.

Lepsza organizacja kodu: Enkapsulacja pozwala na grupowanie danych i metod, co ułatwia zarządzanie kodem.

```java
public class Osoba {
    private String imie;  // pole prywatne

    public String getImie() {  // getter
        return imie;
    }

    public void setImie(String imie) {  // setter
        this.imie = imie;
    }
}
```