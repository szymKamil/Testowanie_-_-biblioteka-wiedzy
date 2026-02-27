# comparator

# Comparator

Comparable to interfejs w Javie, który jest używany do definiowania naturalnego porządku dla obiektów. Służy do tego, aby obiekty danej klasy mogły być porównywane względem siebie, co umożliwia sortowanie ich w kolekcjach, takich jak List lub Set. Interfejs Comparable zawiera jedną metodę, compareTo, którą trzeba zaimplementować w klasie, która go implementuje.

```java
public interface Comparable<T>{

    int compareTo(T o);
}
```

to parametr typu (generyk), co oznacza, że Comparable może działać z dowolnym typem T. Dzięki temu typ T można dopasować do klasy, która implementuje ten interfejs, a w rezultacie porównywać tylko obiekty tego samego typu. Przykładowo, jeśli klasa Student implementuje Comparable, to tylko obiekty klasy Student będą mogły być porównywane ze sobą przy użyciu compareTo.

| Wynik `int` | Znaczenie                                                                                              | Przykład             |
| ----------- | ------------------------------------------------------------------------------------------------------ | -------------------- |
| `0`         | Obiekty `o1` i `o2` są równe według tego kryterium porównania.                                         | `o1` jest równy `o2` |
| `< 0`       | Obiekt `o1` jest mniejszy niż `o2`, więc `o1` powinien znajdować się przed `o2` w porządku sortowania. | `o1 < o2` (np. -1)   |
| `> 0`       | Obiekt `o1` jest większy niż `o2`, więc `o1` powinien znajdować się po `o2` w porządku sortowania.     | `o1 > o2` (np. 1)    |

Comparator nie może być używany do sortowania obiektów, jeśli nie nadpiszemy klasy.
Załóżmy, że mamy klasę Student, która implementuje interfejs Comparable i sortujemy studentów po ich wieku.

Implementując Comparable, klasa sama określa, jak chce być sortowana.

```java
public class Student implements Comparable<Student> {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.age, other.age);  // sortowanie po wieku
    }

    @Override
    public int compareTo(Object o) {
        Student other = (Student) o;
        return name.compareTo(other.name);  // sortowanie po imieniu
    }

    @Override
    public String toString() {
        return name + " (" + age + ")";
    }
}
```

```java
List<Student> students = new ArrayList<>();
students.add(new Student("Alice", 22));
students.add(new Student("Bob", 20));
students.add(new Student("Charlie", 21));

Collections.sort(students);

System.out.println(students);
```

Comparable i Comparator to dwa interfejsy w Javie, które służą do porównywania obiektów, ale mają różne zastosowania i sposoby implementacji.

| Kryterium                 | `Comparable`                                                                                                        | `Comparator`                                                                                    |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Cel**                   | Określa naturalny porządek dla obiektów klasy.                                                                      | Umożliwia definiowanie różnych sposobów sortowania.                                             |
| **Miejsce implementacji** | W klasie, której obiekty mają być porównywane.                                                                      | W oddzielnej klasie lub w postaci wyrażenia lambda.                                             |
| **Metoda**                | `compareTo(T o)`                                                                                                    | `compare(T o1, T o2)`                                                                           |
| **Zestaw argumentów**     | Jeden argument - obiekt tego samego typu.                                                                           | Dwa argumenty - obiekty tego samego typu.                                                       |
| **Kiedy stosować**        | Gdy istnieje jedno domyślne kryterium porównania, które ma sens dla wszystkich przypadków.                          | Gdy chcemy porównać obiekty według różnych kryteriów lub nie możemy zmodyfikować klasy obiektu. |
| **Przykład użycia**       | `Collections.sort(List<T> list)` - sortowanie z użyciem naturalnego porządku (np. alfabetycznego lub numerycznego). | `Collections.sort(List<T> list, Comparator<T> c)` - sortowanie z użyciem dodatkowego kryterium. |

Kiedy używać Comparable a kiedy Comparator?
Comparable jest najlepszy, gdy istnieje naturalny porządek, który powinien być stosowany wszędzie, np. dla liczb lub nazw.
Comparator jest bardziej elastyczny i sprawdza się, gdy klasa wymaga wielu kryteriów sortowania lub kiedy klasy są nieedytowalne (np. klasy zewnętrzne, do których nie mamy dostępu).

```java
class StudentComparator implements Comparator<Student> {

    @Override
    public int compare(Student s1, Student s2) {
        return Integer.compare(s1.getAge(), s2.getAge());
       // return s1.getAge() - s2.getAge();
       // return s2.getAge() - s1.getAge();
    }

}

Student[] students2 = new Student[]{new Student("Zach", 30), new Student("Tim", 21), new Student("Ann", 55)};
        Arrays.sort(students2, compar.reversed()); //odwarcamy kolejność
        System.out.println(Arrays.toString(students2) + " test students2");
```

## Wywoływanie comparatora zależnie od parametrów:

```java
public static class EmployeeComparator <T extends Employee>
            implements Comparator<Employee> {

        private String sortType;

        public EmployeeComparator() {
            this("name");
        }

        public EmployeeComparator(String sortType) {
            this.sortType = sortType;
        }

        @Override
        public int compare(Employee o1, Employee o2) {

            if (sortType.equalsIgnoreCase("yearStarted")) {
                return o1.yearStarted - o2.yearStarted;
            }

            return o1.name.compareTo(o2.name);
        }
```

## Podwójny comparator

```java
// podwójny comparator
        var comparatorMixed = new SecondLevelComparator<Person>() {
            @Override
            public int compare(Person o1, Person o2) {
                int result = o1.name().compareTo(o2.name());
                return result == 0 ? secondLvL(o1, o2) : result;
            }

            @Override
            public int secondLvL(Person o1, Person o2) {
                return o1.surname().compareTo(o2.surname());
            }
        };
        people.sort(comparatorMixed);
        System.out.println(people);
```
