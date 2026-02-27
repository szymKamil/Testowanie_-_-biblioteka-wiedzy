# statements

# Statements (instrukcje)

Instrukcje (statements) to podstawowe jednostki kodu w Javie, które wykonują określone zadania. Są odpowiedzialne za „magiczne” działanie programu, ponieważ to właśnie one określają, co program robi — od przypisywania wartości, przez wykonywanie operacji logicznych i arytmetycznych, po wywoływanie metod i sterowanie przepływem programu.

## Typy instrukcji w Javie

1. Instrukcje deklaracyjne
2. Instrukcje wywołania metody
3. Instrukcje przypisania
4. Instrukcje sterujące przepływem programu
    - Instrukcje warunkowe (`if`, `else`, `switch`)
    - Pętle (`for`, `while`, `do-while`)
5. Instrukcje sterujące przepływem pętli
    - `break`
    - `continue`
6. Instrukcje zwracające

Instrukcje deklaracyjne:

Służą do deklarowania i inicjalizacji zmiennych.
Przykład:

```
int x = 5;  // Deklaracja zmiennej typu int i przypisanie jej wartości 5
```

Instrukcje wywołania metody:

Wywołują metodę, co powoduje wykonanie określonych operacji lub zwrócenie wartości.
Przykład:

```
System.out.println("Hello, world!");  // Wywołanie metody, która wyświetla tekst
```

Instrukcje przypisania:

Przypisują wartość zmiennej.
Przykład:

```
x = 10;  // Przypisanie nowej wartości zmiennej x
```

Instrukcje sterujące przepływem programu:
Pozwalają kontrolować, które części kodu zostaną wykonane i w jakiej kolejności.
Przykłady:
Instrukcje warunkowe: if, else, switch

```
if (x > 0) {
    System.out.println("x jest dodatnie");
}
```

Pętle: for, while, do-while

```
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

Instrukcje sterujące przepływem pętli:
Pozwalają zarządzać przepływem w pętli, np. przerywać lub pomijać jej iteracje.
Przykłady:
break: przerywa pętlę
continue: przeskakuje do następnej iteracji pętli

Instrukcje zwracające:

Służą do zakończenia działania metody i ewentualnie zwrócenia wartości.
Przykład:

```
return x + y;  // Kończy metodę i zwraca wynik dodawania x i y
```