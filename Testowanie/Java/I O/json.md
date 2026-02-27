# Zaawansowane przetwarzanie JSON w Javie - kompletny przewodnik

## Wprowadzenie do JSON w ekosystemie Java

JSON (JavaScript Object Notation) stał się dominującym formatem wymiany danych w aplikacjach webowych. Java oferuje kilka podejść do pracy z JSON-em, od niskopoziomowych parserów po wysokopoziomowe biblioteki obiektowego mapowania. Kluczowe technologie obejmują:

- **Jackson** - najpopularniejsza biblioteka o wysokiej wydajności
- **Gson** - rozwiązanie Google z naciskiem na prostotę
- **JSON-P (JSON Processing)** - oficjalny standard JSR 353
- **JSON-B (JSON Binding)** - standard JSR 367 dla mapowania obiektowego

```java
// Przykładowa struktura JSON
{
  "id": 123,
  "name": "Jan Kowalski",
  "email": "jan@example.com",
  "roles": ["ADMIN", "USER"],
  "metadata": {
    "created": "2025-05-31T12:34:56",
    "modified": "2025-05-31T15:00:00"
  }
}
```

## Jackson - zaawansowane techniki

### Konfiguracja i podstawowe użycie

```xml
<!-- Zależność Maven -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.17.0</version>
</dependency>
```

```java
ObjectMapper mapper = new ObjectMapper()
    .enable(SerializationFeature.INDENT_OUTPUT)
    .disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);

// Serializacja
String json = mapper.writeValueAsString(user);

// Deserializacja
User user = mapper.readValue(json, User.class);
```

### Niestandardowe serializatory

```java
public class CustomDateSerializer extends JsonSerializer<LocalDateTime> {
    private static final DateTimeFormatter formatter = 
        DateTimeFormatter.ISO_LOCAL_DATE_TIME;

    @Override
    public void serialize(LocalDateTime value, JsonGenerator gen,
        SerializerProvider provider) throws IOException {
        gen.writeString(formatter.format(value));
    }
}

@JsonSerialize(using = CustomDateSerializer.class)
private LocalDateTime createdDate;
```


### Przetwarzanie strumieniowe dla dużych danych

```java
try (JsonParser parser = mapper.getFactory().createParser(new File("large.json"))) {
    while (parser.nextToken() != JsonToken.END_ARRAY) {
        User user = mapper.readValue(parser, User.class);
        processUser(user);
    }
}
```


## Gson - alternatywne podejście Google

```java
Gson gson = new GsonBuilder()
    .setDateFormat("yyyy-MM-dd'T'HH:mm:ss")
    .excludeFieldsWithoutExposeAnnotation()
    .create();

// Serializacja z obsługą generyków
Type listType = new TypeToken<List<User>>(){}.getType();
String json = gson.toJson(users, listType);
```


## JSON-P (JSON Processing)

### Tworzenie i parsowanie dokumentów

```java
JsonObject jsonObject = Json.createObjectBuilder()
    .add("name", "Jan Kowalski")
    .add("age", 30)
    .add("active", true)
    .build();

JsonReader reader = Json.createReader(new StringReader(json));
JsonStructure structure = reader.read();
```


## JSON-B (JSON Binding)

```java
@JsonbPropertyOrder(PropertyOrderStrategy.REVERSE)
@JsonbNillable
public class User {
    @JsonbProperty("user-name")
    private String name;
    
    @JsonbDateFormat("dd.MM.yyyy")
    private LocalDate birthDate;
}

Jsonb jsonb = JsonbBuilder.create();
String json = jsonb.toJson(user);
```


## Zaawansowane zagadnienia

### Obsługa formatów daty i czasu

```java
// Jackson
ObjectMapper mapper = new ObjectMapper()
    .registerModule(new JavaTimeModule())
    .disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);

// Gson
Gson gson = new GsonBuilder()
    .registerTypeAdapter(LocalDateTime.class, new LocalDateTimeAdapter())
    .create();
```


### Optymalizacja wydajności

| Biblioteka | Serializacja (ops/s) | Deserializacja (ops/s) | Pamięć (MB) |
| :-- | :-- | :-- | :-- |
| Jackson | 1,450,000 | 1,200,000 | 85 |
| Gson | 980,000 | 850,000 | 120 |
| JSON-B | 750,000 | 600,000 | 150 |

*Dane oparte na testach dla dokumentów 1KB[^11]*

### Integracja z Java 8+

```java
// Streamowanie z Jackson
mapper.readValues(parser, User.class)
    .forEachRemaining(this::processUser);

// Lambda w obsłudze błędów
jsonParser.readObject().unwrap(JsonObject.class)
    .getJsonArray("items")
    .stream()
    .map(JsonValue::asJsonObject)
    .filter(obj -> obj.getInt("id") > 100)
    .collect(Collectors.toList());
```


## Bezpieczeństwo i najlepsze praktyki

1. **Walidacja schematu JSON**
```java
JsonSchemaFactory schemaFactory = JsonSchemaFactory.byDefault();
JsonSchema schema = schemaFactory.getSchema(
    new URL("https://example.com/schema.json"));
ProcessingReport report = schema.validate(json);
```

2. **Ochrona przed atakami DOS**
```java
JsonFactory factory = new JsonFactory();
factory.setStreamReadConstraints(StreamReadConstraints.builder()
    .maxStringLength(10000)
    .maxNumberLength(100)
    .build());
```

3. **Logowanie wrażliwych danych**
```java
@JsonIgnore
private String password;

@JsonSerialize(using = MaskedSerializer.class)
private String creditCardNumber;
```


## Porównanie technologii

| Cecha | Jackson | Gson | JSON-P | JSON-B |
| :-- | :-- | :-- | :-- | :-- |
| Wydajność | ★★★★★ | ★★★☆ | ★★☆☆ | ★★★☆ |
| Elastyczność | ★★★★★ | ★★★★☆ | ★★★☆☆ | ★★★☆☆ |
| Zgodność ze standardem | ★★★☆☆ | ★★☆☆☆ | ★★★★★ | ★★★★★ |
| Obsługa adnotacji | Tak | Tak | Nie | Tak |
| Integracja z JPA | Tak | Tak | Nie | Tak |

## Case study: System przetwarzający 1M+ operacji dziennie

**Architektura rozwiązania:**

- Warstwa wejściowa: JSON-P do wstępnej walidacji
- Przetwarzanie główne: Jackson dla transformacji danych
- Serializacja wyników: JSON-B dla zgodności z interfejsami systemowymi
- Buforowanie: Gson dla prostych obiektów w pamięci podręcznej

```java
// Przykładowy pipeline przetwarzania
try (JsonParser parser = Json.createParser(input)) {
    while (parser.hasNext()) {
        JsonObject obj = parser.getObject();
        if (validator.isValid(obj)) {
            User user = jsonb.fromJson(obj.toString(), User.class);
            User processed = processor.process(user);
            jacksonMapper.writeValue(output, processed);
        }
    }
}
```


## Podsumowanie i rekomendacje

Wybierz technologię w zależności od wymagań:

- **Jackson** - optymalny wybór dla wysokiej wydajności i zaawansowanych funkcji
- **Gson** - do prostych aplikacji i szybkiego prototypowania
- **JSON-P/JSON-B** - gdy wymagana jest pełna zgodność ze standardami Java EE

Przy pracy z dużymi zbiorami danych preferuj przetwarzanie strumieniowe. Dla systemów rozproszonych rozważ użycie schematów JSON Schema. Pamiętaj o aktualizacji bibliotek do najnowszych wersji ze względu na poprawki bezpieczeństwa.
