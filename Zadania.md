### Przygotowanie środowiska

Do pierwszego uruchomienia bazy danych niezbędne jest ściągnięcie pliku `adventure_works_2014_OLTP_script.zip` zawierającego zrzut danych.

Plik można ściągnąć spod adresu: https://drive.google.com/file/d/1tzZoSdOYLMRNEmpLtM2ad8oDx65bLLgl/view?usp=sharing

Ściągnięty plik należy skopiować do katalogu z projektem.

### Uruchomienie bazy danych

Wystarczy wydać polecenie: `docker-compose up`

### Podłączenie się do bazy danych

Niezbędne dane dostępowe:

| Property  | Value           |
|-----------|-----------------|
| host      | localhost       |
| port      | 5432            |
| user      | postgres        |
| password  | postgres        |
| database  | Adventureworks  |

### Organizacja bazy danych

Tabele w bazie rozdzielone są pomiędzy 13 schematów, dlatego też podczas wykonywania zapytań należy zawsze poprzedzić nazwę tabeli odpowiednim prefixem.

Przykład:

`SELECT * FROM production.product`  

(gdyż tabela `product` znajduje się w schemacie `production`)

### Graficzny schemat bazy danych

Nie do końca aktualna (ale w miarę dobra) wersja dostępna jest pod adresem: https://drive.google.com/file/d/1gpgAu_4xEsru58JrPGfaBrI3_XMSCTGQ/view?usp=sharing

### Analiza planów zapytań

Aby otrzymać plan zapytania dla danego query korzystamy z polecenia `EXPLAIN`. 

W najprostszym formacie wygląda ono tak:

`EXPLAIN SELECT * FROM production.product`

W wyniku otrzymamy wtedy plan zapytania wraz z teoretycznym kosztem jego wykonania

By otrzymać faktyczny koszt zapytania należy skorzystać z rozszerzonej wersji polecenia - tj. `EXPLAIN ANALYZE`:

`EXPLAIN ANALYZE SELECT * FROM production.product`

**UWAGA**: `EXPLAIN ANALYZE` dokonuje faktycznego uruchomienia zapytania, dlatego należy używać go rozważnie w przypadku zapytań modyfikujących stan bazy !!!

### Przydatne linki

https://www.postgresql.org/docs/current/sql-explain.html - dokumentacja referencyjna polecenia `EXPLAIN`

https://tatiyants.com/pev - graficzny wizualizator planu zapytania

Aby z niego skorzystać niezbędne jest otrzymanie planu zapytania w formacie JSON:

`EXPLAIN (FORMAT JSON, ANALYZE) SELECT * FROM production.product`

https://thoughtbot.com/blog/reading-an-explain-analyze-query-plan - opis jak czytać plan zapytania

https://thoughtbot.com/blog/advanced-postgres-performance-tips - tips & tricks w temacie optymalizacji

https://pganalyze.com/docs/explain - użyteczne informacje na temat tego co zwróci plan zapytania (np. typy złączeń)

### Zadania

Zadaniem wszystkich grup jest analiza zapytań z użyciem polecenia `EXPLAIN`

#### Grupa 1

Za pomocą polecenia `EXPLAIN` odczytać plan zapytania dla następującego query:

`SELECT * FROM production.product`

Sprawdzić jaki wpływ na plan zapytania i jego koszt ma:

- zmiana listy kolumn we klauzuli `SELECT` (projekcja)
- użycie klauzuli `DISTINCT`
- użycie klauzuli `ORDER BY`
- użycie w klauzuli `SELECT` wyłącznie tej kolumny / kolumn, na której / których jest założony indeks (należy go uprzednio stworzyć)

#### Grupa 2

Za pomocą polecenia `EXPLAIN` odczytać plan zapytania dla następującego query:

`SELECT * FROM person.person`

Sprawdzić jaki wpływ na plan zapytania i jego koszt ma:

- zawężenie wyników zapytania za pomocą klauzuli `WHERE` na jednej lub więcej kolumnach
- użycie w klauzuli `WHERE` operatorów `OR` oraz `IN`
- użycie w klauzuli `WHERE` warunków dotyczących kolumny / kolumn, na której / których jest założony indeks (należy go uprzednio stworzyć). Czy w takim przypadku kolejność kolumn w indeksie ma znaczenie ?

### Grupa 3

Za pomocą polecenia `EXPLAIN` odczytać plan zapytania dla następującego query:

```sql
SELECT *
FROM production.product p
JOIN production.transactionhistory t
ON p.productid = t.productid
```

Sprawdzić jaki wpływ na plan zapytania i jego koszt ma:
- zawężenie wyników zapytania za pomocą klauzuli WHERE dla jednej / obu tabel. 
- zmiana kolejności tabel we klauzuli `JOIN`
- zastąpienie `JOIN` przez `OUTER JOIN`
- złączenie wyłącznie za pomocą klauzuli `WHERE` (bez klauzuli `JOIN`)
- użycie iloczynu kartezjańskiego tabel (bez klauzuli `WHERE`)
- posortowanie wyniku po jednej z kolumn biorących udział w złączeniu

UWAGA: Powyższe pomysły zmian to tylko sugestie. Zdecydowanie jestem otwarty na inne koncepcje wychodzące z grup :)