# apachePOI

# Wprowadzenie do Apache POI

Apache POI to biblioteka Java umożliwiająca manipulację dokumentami Microsoft Office, w tym plikami Excel (XLS/XLSX). Składa się z dwóch głównych komponentów:

- **HSSF** (Horrible Spreadsheet Format) - obsługa starszego formatu XLS (Excel 97-2003)
- **XSSF** (XML Spreadsheet Format) - obsługa nowszego formatu XLSX (Excel 2007+)
- **SXSSF** (Streaming API) - optymalizacja pamięciowa dla dużych zbiorów danych [^2][^14]

```xml
<!-- Zależność Maven -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>5.2.3</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>5.2.3</version>
</dependency>
```

## Tworzenie i zapis plików Excel

### Podstawowa struktura dokumentu

```java
// Tworzenie nowego skoroszytu XLSX
XSSFWorkbook workbook = new XSSFWorkbook();
XSSFSheet sheet = workbook.createSheet("Dane");

// Nagłówki kolumn
Row headerRow = sheet.createRow(0);
headerRow.createCell(0).setCellValue("ID");
headerRow.createCell(1).setCellValue("Nazwa");

// Dodawanie danych
IntStream.range(1, 100).forEach(i -> {
    Row row = sheet.createRow(i);
    row.createCell(0).setCellValue(i);
    row.createCell(1).setCellValue("Rekord " + i);
});

// Autodopasowanie kolumn
sheet.autoSizeColumn(0);
sheet.autoSizeColumn(1);

// Zapis do pliku
try (FileOutputStream fos = new FileOutputStream("raport.xlsx")) {
    workbook.write(fos);
}
```

## Zaawansowane techniki odczytu

### Efektywne przetwarzanie dużych plików (SXSSF)

```java
// Konfiguracja SXSSF z oknem 100 wierszy
SXSSFWorkbook workbook = new SXSSFWorkbook(100);
SXSSFSheet sheet = workbook.createSheet("Duże dane");

// Generowanie 1 000 000 wierszy
for(int i=0; i<1_000_000; i++){
    Row row = sheet.createRow(i);
    row.createCell(0).setCellValue(i);
    row.createCell(1).setCellValue(Math.random()*1000);

    // Flush co 1000 wierszy
    if(i % 1000 == 0) {
        ((SXSSFSheet)sheet).flushRows(1000);
    }
}
```

### Odczyt z użyciem DataFormatter

```java
DataFormatter formatter = new DataFormatter();
try (XSSFWorkbook workbook = new XSSFWorkbook(new File("dane.xlsx"))) {
    Sheet sheet = workbook.getSheetAt(0);
    for (Row row : sheet) {
        for (Cell cell : row) {
            String cellValue = formatter.formatCellValue(cell);
            System.out.print(cellValue + "\t");
        }
        System.out.println();
    }
}
```

## Obsługa formuł i obliczeń

### Dodawanie i ewaluacja formuł

```java
Cell formulaCell = row.createCell(2);
formulaCell.setCellFormula("SUM(A2:B2)");

// Ewaluacja formuł
FormulaEvaluator evaluator = workbook.getCreationHelper().createFormulaEvaluator();
evaluator.evaluateFormulaCell(formulaCell);

// Aktualizacja wartości
if (formulaCell.getCellType() == CellType.NUMERIC) {
    System.out.println("Wynik sumy: " + formulaCell.getNumericCellValue());
}
```

### Obsługa formuł tablicowych

```java
CellRangeAddress range = new CellRangeAddress(0, 4, 0, 2);
sheet.addArrayFormula("SUM(A1:C5*D1:F5)", range);
```

## Zaawansowane formatowanie

### Style komórek i czcionki

```java
// Tworzenie stylu dla nagłówków
CellStyle headerStyle = workbook.createCellStyle();
Font headerFont = workbook.createFont();
headerFont.setBold(true);
headerFont.setColor(IndexedColors.WHITE.getIndex());
headerStyle.setFont(headerFont);
headerStyle.setFillForegroundColor(IndexedColors.DARK_BLUE.getIndex());
headerStyle.setFillPattern(FillPatternType.SOLID_FOREGROUND);
headerStyle.setBorderBottom(BorderStyle.THICK);
```

### Warunkowe formatowanie

```java
SheetConditionalFormatting sheetCF = sheet.getSheetConditionalFormatting();
ConditionalFormattingRule rule = sheetCF.createConditionalFormattingRule(
    ComparisonOperator.GT, "1000");

PatternFormatting fill = rule.createPatternFormatting();
fill.setFillBackgroundColor(IndexedColors.RED.getIndex());
fill.setFillPattern(PatternFormatting.SOLID_FOREGROUND);

CellRangeAddress[] regions = { CellRangeAddress.valueOf("B2:B100") };
sheetCF.addConditionalFormatting(regions, rule);
```

## Praca z wykresami

### Tworzenie wykresu kolumnowego

```java
XDDFChart chart = sheet.createChart();
XDDFChartLegend legend = chart.getOrAddLegend();
legend.setPosition(LegendPosition.TOP_RIGHT);

// Dane źródłowe
XDDFDataSource<Number> xs = XDDFDataSourcesFactory.fromNumericCellRange(
    sheet, new CellRangeAddress(0, 0, 0, 4));
XDDFNumericalDataSource<Number> ys = XDDFDataSourcesFactory.fromNumericCellRange(
    sheet, new CellRangeAddress(1, 1, 0, 4));

// Konfiguracja serii danych
XDDFBarChartData data = (XDDFBarChartData) chart.createData(
    ChartTypes.BAR, xs, ys);
data.setBarDirection(BarDirection.COL);

// Formatowanie wykresu
chart.plot(data);
chart.setTitleText("Przykładowy wykres");
chart.setTitleOverlay(false);
```

## Optymalizacja wydajności

### Event Model dla dużych plików

```java
OPCPackage pkg = OPCPackage.open(new File("duzy_plik.xlsx"));
XSSFReader reader = new XSSFReader(pkg);
XMLReader parser = SAXHelper.newXMLReader();
parser.setContentHandler(new XSSFSheetXMLHandler(
    reader.getStylesTable(),
    new SheetContentsHandler() {
        @Override
        public void cell(String cellReference, String formattedValue) {
            // Przetwarzanie komórek
        }
    },
    true  // Uwzględnij komórki z formułami
));
parser.parse(new InputSource(reader.getSheetsData().next()));
```

## Obsługa błędów i najlepsze praktyki

1. **Zarządzanie zasobami**: Zawsze zamykaj strumienie w bloku `try-with-resources`
2. **Buforowanie stylów**: Twórz style na poziomie skoroszytu i współdziel je między komórkami
3. **Optymalizacja pamięci**: Dla plików >50MB używaj SXSSF [^3][^14]
4. **Formatowanie dat**: Używaj `CreationHelper.createDataFormat()`
5. **Walidacja formuł**: Sprawdzaj poprawność przed ewaluacją

```java
try (SXSSFWorkbook workbook = new SXSSFWorkbook(100)) {
    // ... operacje na pliku ...
    workbook.dispose();  // Usuń pliki tymczasowe
}
```

## Integracja z nowoczesnymi API

### Generowanie Excel z użyciem strumieni Java 8+

```java
List<Product> products = productRepository.findAll();
try (SXSSFWorkbook workbook = new SXSSFWorkbook(100)) {
    SXSSFSheet sheet = workbook.createSheet("Produkty");

    // Nagłówki
    Row header = sheet.createRow(0);
    header.createCell(0).setCellValue("ID");
    header.createCell(1).setCellValue("Nazwa");
    header.createCell(2).setCellValue("Cena");

    // Dane
    AtomicInteger rowNum = new AtomicInteger(1);
    products.stream()
        .parallel()
        .forEach(product -> {
            Row row = sheet.createRow(rowNum.getAndIncrement());
            row.createCell(0).setCellValue(product.getId());
            row.createCell(1).setCellValue(product.getName());
            row.createCell(2).setCellValue(product.getPrice());
        });
}
```

## Aktualne trendy i rozwój

- Obsługa najnowszych funkcji Excel 365
- Integracja z formatem OpenXML 2.0
- Lepsza obsługa formuł dynamicznych (np. LAMBDA, LET)
- Optymalizacja pamięci dla arkuszy złożonych
- Wsparcie dla wielowątkowego przetwarzania

Podsumowując, Apache POI oferuje kompleksowe narzędzia do zaawansowanej manipulacji plikami Excel w aplikacjach Java. Wybór między HSSF, XSSF a SXSSF zależy od konkretnych wymagań dotyczących formatu plików i wydajności. Nowoczesne implementacje powinny preferować API strumieniowe (SXSSF) dla dużych zbiorów danych, zachowując tradycyjne podejście (XSSF) dla złożonych operacji formatowania i formuł.