# 📊 Excel dla Data Analysta — Teoria od Basic do Advanced

> **Kompendium wiedzy** dla analityka danych: funkcje, formuły, wykresy, tabele, formatowanie i współpraca.
> Każda sekcja zawiera **składnię**, **przykłady** oraz **praktyczne zastosowania**.

---

## 📑 Spis treści

1. [Funkcje — przegląd najważniejszych grup](#1-funkcje--przegląd-najważniejszych-grup)
2. [Formuły logiczne i matematyczne](#2-formuły-logiczne-i-matematyczne)
3. [Funkcje statystyczne](#3-funkcje-statystyczne)
4. [Zaawansowana statystyka](#4-zaawansowana-statystyka)
5. [Funkcja RANK — ranking danych](#5-funkcja-rank--ranking-danych)
6. [Formuły tablicowe (Array Formulas)](#6-formuły-tablicowe-array-formulas)
7. [Funkcje wyszukiwania (Lookup Functions)](#7-funkcje-wyszukiwania-lookup-functions)
8. [Funkcje tekstowe](#8-funkcje-tekstowe)
9. [Funkcje dat i czasu](#9-funkcje-dat-i-czasu)
10. [Najważniejsze wykresy](#10-najważniejsze-wykresy)
11. [Tabele Excel (Excel Tables)](#11-tabele-excel-excel-tables)
12. [Formatowanie i współpraca](#12-formatowanie-i-współpraca)
13. [Skróty klawiszowe — must-know](#13-skróty-klawiszowe--must-know)
14. [Najczęstsze błędy i ich znaczenie](#14-najczęstsze-błędy-i-ich-znaczenie)
15. [Bonus: Power Query i Power Pivot](#15-bonus-power-query-i-power-pivot)

---

## 1. Funkcje — przegląd najważniejszych grup

### 🔍 1.1. Wyszukiwanie i odwołania (fundament)

To najważniejsza grupa funkcji — pozwala łączyć dane z różnych tabel.

| Funkcja (EN) | Funkcja (PL) | Kiedy używać |
|---|---|---|
| `XLOOKUP` | `X.WYSZUKAJ` | **Nowoczesny standard.** Zastępuje VLOOKUP i INDEX/MATCH. Odporny na zmiany kolumn, szuka w lewo i w prawo. |
| `VLOOKUP` | `WYSZUKAJ.PIONOWO` | Nadal powszechny w starszych firmach — warto znać, by rozumieć cudze arkusze. |
| `INDEX` / `MATCH` | `INDEKS` / `PODAJ.POZYCJĘ` | Potężny duet dla elastycznego wyszukiwania, gdy XLOOKUP jest niedostępny. |

### ⚖️ 1.2. Funkcje logiczne (podejmowanie decyzji)

- **`IF` (JEŻELI)** — podstawowy test logiczny
- **`IFS` (WARUNKI)** — wiele warunków bez zagnieżdżeń
- **`IFERROR` (JEŻELI.BŁĄD)** — kluczowa dla estetyki raportów; zamienia `#N/D!` na 0 lub tekst
- **`AND` / `OR` (ORAZ / LUB)** — rozszerzają możliwości IF

### 🧮 1.3. Funkcje agregujące (statystyka warunkowa)

> Analityk rzadko sumuje wszystko — zazwyczaj dane spełniające konkretne kryteria.

- **`SUMIFS`** — suma warunkowa (jeden lub wiele warunków)
- **`COUNTIFS`** — liczenie wystąpień
- **`AVERAGEIFS`** — średnia warunkowa
- **`AGGREGATE`** — „szwajcarski scyzoryk" (pomija ukryte wiersze i błędy)

### 🧹 1.4. Czyszczenie i przekształcanie danych

- **`TRIM`** — usuwa zbędne spacje (częsty problem przy imporcie)
- **`TEXTJOIN`** — łączy teksty z separatorem
- **`LEFT` / `RIGHT` / `MID`** — wycinanie fragmentów tekstu
- **`VALUE`** — zamienia tekst wyglądający jak liczba na prawdziwą liczbę

### ⚡ 1.5. Dynamiczne tablice (Modern Excel)

> Rewolucja ostatnich lat — funkcje zwracają wyniki do wielu komórek naraz (tzw. **spilling**).

- **`UNIQUE`** — lista unikalnych wartości
- **`SORT`** — dynamiczne sortowanie
- **`FILTER`** — wyciąga podzbiór danych (jak SQL wewnątrz Excela)

### 📅 1.6. Praca z datami

- **`EOMONTH`** — ostatni dzień miesiąca
- **`DATEDIF`** — różnica między datami
- **`NETWORKDAYS`** — dni robocze (bez weekendów i świąt)

---

## 2. Formuły logiczne i matematyczne

### 🧠 2.1. Budowanie „inteligencji" arkusza

#### `IF` oraz `IFS`

```excel
=IF(A1>100; "Duża"; "Mała")

=IFS(A1>90; "A"; A1>70; "B"; A1>50; "C"; TRUE; "F")
```

> ⚠️ **Unikaj zagnieżdżania 7+ funkcji IF** — to przepis na błąd, którego nikt (łącznie z Tobą za dwa tygodnie) nie naprawi.

#### `AND` i `OR` — łączniki warunków

```excel
=IF(AND(A1>18; B1="Warszawa"); "Dorosły z Warszawy"; "Inny")

=IF(OR(C1="karta"; C1="BLIK"); "Płatność cyfrowa"; "Gotówka")
```

#### `LET` — nowoczesna optymalizacja

Pozwala przypisać nazwę do wyniku obliczenia i użyć jej wielokrotnie w tej samej formule.

```excel
=LET(
    przychod; SUM(Sprzedaż[Kwota]);
    koszt;   SUM(Sprzedaż[Koszt]);
    zysk;    przychod - koszt;
    zysk / przychod
)
```

**Dlaczego to ważne?** Excel nie liczy tego samego dwa razy — przy 100 000 wierszy drastycznie przyspiesza działanie.

### 🔢 2.2. Formuły matematyczne — precyzja i agregacja

#### `SUMIFS` — podstawa raportowania

```excel
=SUMIFS(Sprzedaż[Kwota]; Sprzedaż[Produkt]; "Buty"; Sprzedaż[Miesiąc]; "Marzec")
```

> 💡 **Kolejność:** najpierw zakres do sumowania, potem pary `gdzie_szukać; czego_szukać`.

#### `SUMPRODUCT` — średnia ważona

Jedna z najpotężniejszych funkcji „starej szkoły". Mnoży odpowiadające sobie elementy w tablicach i sumuje wyniki.

Wzór na średnią ważoną:

$$\bar{x}_w = \frac{\sum_{i=1}^{n} (w_i \cdot x_i)}{\sum_{i=1}^{n} w_i}$$

```excel
=SUMPRODUCT(B2:B10; C2:C10) / SUM(B2:B10)
```

#### Zaokrąglanie: `ROUND`, `MROUND`, `CEILING`, `FLOOR`

| Funkcja | Działanie | Przykład |
|---|---|---|
| `ROUND(A1; 2)` | standardowo do 2 miejsc | 3,456 → 3,46 |
| `MROUND(A1; 50)` | do wielokrotności 50 | 127 → 150 |
| `CEILING(A1; 10)` | zawsze w górę do 10 | 41 → 50 |
| `FLOOR(A1; 10)` | zawsze w dół do 10 | 49 → 40 |
| `TRUNC(A1)` | ucina część dziesiętną | -3,7 → -3 |
| `INT(A1)` | zaokrągla **w dół** | -3,7 → -4 |

#### `ABS` i `MOD`

- **`ABS`** — wartość bezwzględna (kluczowa przy analizie odchyleń od celu)
- **`MOD`** — reszta z dzielenia (świetne do oznaczania co drugiego wiersza)

### 🏆 2.3. Złote zasady łączenia logiki z matematyką

**Obsługa błędów w dzieleniu:**

```excel
=IFERROR(Sprzedaż/Ilość; 0)
```

**Warunkowe flagowanie (Boolean Logic):**

Zamiast `IF(A1>100; 1; 0)` napisz krócej:

```excel
=(A1>100)*1
```

Excel traktuje `TRUE` jako 1, a `FALSE` jako 0 — ten trik pozwala budować ultra-szybkie modele.

---

## 3. Funkcje statystyczne

### 🔢 3.1. Rodzina COUNT — „ile tego właściwie jest?"

| Funkcja | Co liczy |
|---|---|
| `COUNT` (ILE.LICZB) | tylko liczby |
| `COUNTA` (ILE.NIEPUSTYCH) | wszystko niepuste (tekst, liczby, błędy) |
| `COUNTBLANK` (LICZ.PUSTE) | puste komórki — sprawdzanie „dziur" w danych |

### 🎯 3.2. Funkcje `IFS` — precyzyjne uderzenie

> Zawsze używaj wersji z literą **S** na końcu — obsługa wielu kryteriów i logiczniejsza składnia.

```excel
=SUMIFS(Sprzedaż[Kwota]; Sprzedaż[Region]; "Północ"; Sprzedaż[Produkt]; "Laptop")
=COUNTIFS(Klienci[Miasto]; "Warszawa"; Klienci[Dzień]; "Poniedziałek")
=AVERAGEIFS(Koszyki[Wartość]; Koszyki[Płatność]; "karta")
=MAXIFS(Sprzedaż[Cena]; Sprzedaż[Kwartał]; "Q4")
=MINIFS(Sprzedaż[Cena]; Sprzedaż[Kwartał]; "Q4")
```

### 📈 3.3. Statystyka opisowa — więcej niż średnia

> Sama średnia (AVERAGE) często kłamie — np. gdy jeden duży kontrakt zawyża wynik.

- **`MEDIAN`** — wartość środkowa (lepsza niż średnia dla zarobków)
- **`MODE.SNGL`** — dominanta (wartość występująca najczęściej)
- **`STDEV.P` / `STDEV.S`** — odchylenie standardowe (populacja / próbka)
- **`VAR.P` / `VAR.S`** — wariancja
- **`SKEW`** — skośność rozkładu
- **`KURT`** — kurtoza (spiczastość)

Wzór odchylenia standardowego populacji:

$$\sigma = \sqrt{\frac{\sum (x_i - \mu)^2}{N}}$$

**Interpretacja:**
- Małe odchylenie → stabilne procesy
- Duże odchylenie → duża nieprzewidywalność (ryzyko)

### 🏅 3.4. Pozycjonowanie i rankingi

- **`RANK.EQ`** — miejsce w rankingu (ties dostają tę samą pozycję)
- **`RANK.AVG`** — miejsce w rankingu (ties dostają średnią pozycję)
- **`LARGE(zakres; k)`** — k-ta największa wartość
- **`SMALL(zakres; k)`** — k-ta najmniejsza wartość
- **`PERCENTILE.INC`** — percentyl włącznie
- **`PERCENTRANK.INC`** — pozycja wartości jako percentyl

```excel
=LARGE(B2:B100; 3)            → trzeci najwyższy wynik
=PERCENTILE.INC(B2:B100; 0,9) → 90-ty percentyl
```

---

## 4. Zaawansowana statystyka

### 🔗 4.1. Analiza relacji (korelacja)

> **Kluczowe pytanie analityka:** Czy zmienna X ma wpływ na zmienną Y?

#### `CORREL` — współczynnik korelacji Pearsona

Wzór:

$$r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \cdot \sum (y_i - \bar{y})^2}}$$

| Wartość r | Interpretacja |
|---|---|
| ~ +1 | silna zależność dodatnia (reklama ↑ → sprzedaż ↑) |
| ~ 0 | brak zależności liniowej |
| ~ -1 | silna zależność ujemna (cena ↑ → sprzedaż ↓) |

```excel
=CORREL(Reklama[Wydatki]; Sprzedaż[Przychód])
```

- **`COVARIANCE.P`** — kowariancja (popularna w analizie ryzyka portfela)
- **`RSQ`** — współczynnik determinacji $R^2$

### 📊 4.2. Rozkłady prawdopodobieństwa

| Funkcja | Zastosowanie |
|---|---|
| `NORM.DIST` | rozkład normalny (np. prawdopodobieństwo czasu dostawy > 5 dni) |
| `NORM.INV` | odwrotny rozkład normalny |
| `BINOM.DIST` | wynik binarny (sukces/porażka) — np. konwersja |
| `POISSON.DIST` | zdarzenia w czasie (np. reklamacje na godzinę) |
| `EXPON.DIST` | rozkład wykładniczy (np. czas między zdarzeniami) |

### 🔮 4.3. Predykcja i trendy

- **`FORECAST.LINEAR`** — prognoza liniowa
- **`FORECAST.ETS`** — prognoza z uwzględnieniem sezonowości (triple exponential smoothing)
- **`TREND`** — wartości wzdłuż trendu liniowego
- **`GROWTH`** — wzrost wykładniczy
- **`SLOPE`** / **`INTERCEPT`** — współczynnik kierunkowy i wyraz wolny regresji liniowej

```excel
=FORECAST.LINEAR(13; B2:B13; A2:A13)   → sprzedaż w 13. miesiącu
=SLOPE(Y; X)                            → nachylenie regresji
=INTERCEPT(Y; X)                        → punkt przecięcia z osią Y
```

### 📐 4.4. Grupowanie i częstotliwość

- **`FREQUENCY`** — ile wartości wpada w dane „koszyki" (podstawa histogramu)
- **`QUARTILE.INC`** — kwartyle (Q1 = 25%, Q2 = mediana, Q3 = 75%)

### 🧪 4.5. Testowanie hipotez (wnioskowanie)

- **`T.TEST`** — test t-Studenta dla dwóch grup
- **`Z.TEST`** — test Z (duża próba, znana wariancja)
- **`CHISQ.TEST`** — test chi-kwadrat (zależność zmiennych kategorycznych)
- **`F.TEST`** — test F (równość wariancji)

> 💡 Jeśli **p-value < 0,05**, możesz z 95% pewnością powiedzieć, że różnica jest istotna statystycznie.

---

## 5. Funkcja RANK — ranking danych

### Składnia

```excel
=RANK.EQ(liczba; odwołanie; [kolejność])
```

| Argument | Opis |
|---|---|
| `liczba` | wartość, której pozycję sprawdzasz (zazwyczaj pojedyncza komórka) |
| `odwołanie` | cała lista — **zablokuj `$`**, np. `$A$1:$A$10` |
| `kolejność` | `0` lub pominięte → malejąco; `1` → rosnąco |

### Przykłady

```excel
=RANK.EQ(B2; $B$2:$B$100; 0)   → największa wartość = 1. miejsce (sprzedaż)
=RANK.EQ(B2; $B$2:$B$100; 1)   → najmniejsza wartość = 1. miejsce (czas biegu)
```

> ⚠️ **Pułapka `RANK.EQ`:** przy remisach (ties) obie wartości dostają tę samą pozycję, a następna jest pomijana. Dla średniej pozycji użyj `RANK.AVG`.

---

## 6. Formuły tablicowe (Array Formulas)

### 🚀 6.1. Wielka rewolucja — dynamiczne tablice

W starszym Excelu potrzebowałeś `Ctrl + Shift + Enter` (tzw. formuły CSE). **Microsoft 365 i Excel 2021+** wprowadzają **Dynamic Arrays** — formuły automatycznie „rozlewają się" na sąsiednie komórki.

- **Zjawisko Spill** — niebieska ramka obejmuje wszystkie wyniki
- **Błąd `#SPILL!`** — coś stoi na drodze rozlewającej się formuły

### 🛠️ 6.2. Kluczowe funkcje tablicowe

#### `FILTER`

```excel
=FILTER(A2:C100; B2:B100="Niemcy"; "Brak wyników")
```

#### `UNIQUE`

```excel
=UNIQUE(A2:A100)                     → lista unikalnych marek
=UNIQUE(A2:A100; FALSE; TRUE)        → tylko wartości występujące raz
```

#### `SORT` i `SORTBY`

```excel
=SORT(FILTER(A2:C100; C2:C100>1000); 3; -1)   → filtruj > 1000 i sortuj malejąco po 3. kolumnie
=SORTBY(A2:A100; B2:B100; -1)                 → sortuj A wg B malejąco
```

#### `SEQUENCE`

```excel
=SEQUENCE(10; 1; 100; 10)    → 100, 110, 120, ..., 190
=SEQUENCE(12; 1; DATE(2025;1;1); 1)   → 12 kolejnych dni od 2025-01-01
```

#### `RANDARRAY` — losowe tablice

```excel
=RANDARRAY(5; 3; 1; 100; TRUE)   → tablica 5×3 losowych liczb całkowitych 1-100
```

### 🔗 6.3. Operator `#` (Spill Range Operator)

> **Najważniejszy symbol w nowoczesnych tablicach.**

Jeśli w E1 masz `=UNIQUE(...)` z 50 wynikami, to `=E1#` zawsze obejmie cały „rozlany" zakres — nawet gdy jutro będzie 60 wierszy.

```excel
=COUNTA(E1#)        → zawsze aktualna liczba elementów
=SUM(FILTER(...)#)  → suma całego zakresu zwróconego przez FILTER
```

### 🔀 6.4. Logika tablicowa (Boolean Logic)

W tablicach `AND`/`OR` słabo radzą sobie — zamiast tego używamy arytmetyki:

| Operator | Działa jak | Przykład |
|---|---|---|
| `*` (mnożenie) | `AND` | `(Region="Północ") * (Sprzedaż>1000)` |
| `+` (dodawanie) | `OR` | `(Region="Północ") + (Region="Południe")` |

### 💡 6.5. Scenariusze — dlaczego to potężne

**Scenariusz A — dynamiczna lista rozwijana:**
W „Sprawdzaniu poprawności danych" wpisz źródło `=A1#` (gdzie A1 to funkcja UNIQUE) — lista sama się aktualizuje.

**Scenariusz B — obliczenia bez kolumn pomocniczych:**
Suma sprzedaży dla produktów na „A" i cenie > 50:

```excel
=SUM((LEFT(A2:A100;1)="A") * (B2:B100>50) * (C2:C100))
```

---

## 7. Funkcje wyszukiwania (Lookup Functions)

### 📜 7.1. `VLOOKUP` — klasyk z ograniczeniami

```excel
=VLOOKUP(szukana_wartość; tabela; numer_kolumny; [typ_wyszukiwania])
=VLOOKUP("A001"; Produkty; 3; FALSE)
```

**Wady:**
- ❌ Szuka **tylko w prawo**
- ❌ Podatna na błędy przy wstawianiu kolumn
- ❌ Domyślnie szuka **przybliżenia** (zawsze dodawaj `FALSE` lub `0`!)

### ⭐ 7.2. `XLOOKUP` — nowoczesny standard

```excel
=XLOOKUP(szukana; gdzie_szukać; co_zwrócić; [jeśli_brak]; [tryb_dopasowania]; [tryb_wyszukiwania])
=XLOOKUP(A2; Produkty[ID]; Produkty[Cena]; "Brak")
```

**Dlaczego jest lepsza?**
- ✅ Szuka w **dowolnym kierunku** (w lewo, w prawo, w górę, w dół)
- ✅ **Domyślnie dokładne dopasowanie** (nie trzeba dodawać `FALSE`)
- ✅ **Wbudowana obsługa błędów** (argument `jeśli_brak`)
- ✅ Może **zwrócić całe wiersze** danych naraz
- ✅ Obsługuje **wyszukiwanie od końca** (`tryb_wyszukiwania = -1`)

### 🎯 7.3. `INDEX` & `MATCH` — duet dla profesjonalistów

```excel
=INDEX(zakres_zwrotu; MATCH(szukana; zakres_szukania; 0))
=INDEX(Produkty[Cena]; MATCH("A001"; Produkty[ID]; 0))
```

**Jak to działa?**
1. `MATCH` znajduje numer wiersza z szukaną wartością
2. `INDEX` zwraca wartość z danego wiersza i kolumny

**Zalety:** bardziej wydajny niż VLOOKUP przy dużych zbiorach + szuka w obu kierunkach.

### 📐 7.4. `HLOOKUP` — wyszukiwanie poziome

Działa jak VLOOKUP, ale dla danych ułożonych poziomo. W analizie rzadziej spotykana, bo bazy danych mają strukturę pionową.

### 🎯 7.5. Kluczowe pojęcia

#### Dopasowanie dokładne vs przybliżone

- **Dokładne** — szukasz ID 123 i chcesz dokładnie tego ID
- **Przybliżone** — progi rabatowe (0 zł = 0%, 1000 zł = 5%, 5000 zł = 10%); klient wydał 3500 zł → dostaje 5%

> ⚠️ Dla przybliżonego dopasowania dane muszą być **posortowane rosnąco**!

#### Wildcards (symbole wieloznaczne)

| Symbol | Znaczenie |
|---|---|
| `*` | dowolny ciąg znaków |
| `?` | dokładnie jeden znak |
| `~*` lub `~?` | dosłowna gwiazdka / pytajnik |

```excel
=XLOOKUP("Dell*"; ...; ...; ; 2)   → "Dell Laptop", "Dell Monitor" itp.
```

> 💡 W `XLOOKUP` musisz ustawić `tryb_dopasowania = 2`, aby wildcards działały.

### 📊 7.6. Szybkie porównanie

| Cecha | `VLOOKUP` | `XLOOKUP` | `INDEX/MATCH` |
|---|---|---|---|
| Szuka w lewo | ❌ | ✅ | ✅ |
| Obsługa błędów | zewnętrzne `IFERROR` | wbudowane | zewnętrzne `IFERROR` |
| Odporność na dodawanie kolumn | ❌ | ✅ | ✅ |
| Wydajność (duże zbiory) | ⚠️ średnia | ✅ szybka | ✅ szybka |
| Dostępność | wszędzie | M365 / 2021+ | wszędzie |

---

## 8. Funkcje tekstowe

### ✂️ 8.1. Wycinanie i ekstrakcja

```excel
=LEFT("Warszawa"; 3)              → "War"
=RIGHT("+48 123 456"; 6)          → "23 456"
=MID("PL-2024-001"; 4; 4)         → "2024"
=LEN("PESEL12345678901")          → 16  (walidacja długości!)
```

### 🧽 8.2. Czyszczenie i normalizacja

| Funkcja | Działanie |
|---|---|
| `TRIM` | usuwa nadmiarowe spacje (początek, koniec, podwójne wewnątrz) |
| `CLEAN` | usuwa znaki niedrukowalne (częste z kopiowania z WWW) |
| `UPPER` | WIELKIE LITERY |
| `LOWER` | małe litery |
| `PROPER` | Format „Imię Nazwisko" |

> ⚠️ Bez normalizacji Excel traktuje `"Warszawa"` i `"warszawa"` jako **dwa różne** miasta!

### 🔍 8.3. Wyszukiwanie i zamiana

```excel
=SUBSTITUTE(A1; "-"; "")          → usuwa wszystkie myślniki
=SUBSTITUTE(A1; "-"; ""; 2)       → usuwa tylko drugi myślnik

=FIND("@"; "user@domain.com")     → 5   (rozróżnia wielkość liter)
=SEARCH("@"; "user@domain.com")   → 5   (ignoruje wielkość, obsługuje wildcards)

=REPLACE("2024-01-15"; 6; 2; "02") → "2024-02-15"  (zamiana po pozycji)
```

### ⭐ 8.4. Nowoczesne funkcje (M365) — game changers

```excel
=TEXTBEFORE("jan.kowalski@firma.pl"; "@")    → "jan.kowalski"
=TEXTAFTER("jan.kowalski@firma.pl"; "@")     → "firma.pl"
=TEXTSPLIT("a,b,c,d"; ",")                   → {"a","b","c","d"} (rozlewanie poziome)
=TEXTSPLIT("a,b;c,d"; ","; ";")              → tabela 2×2 (kolumny i wiersze)
```

### 🔗 8.5. Łączenie tekstów

```excel
=A1 & " " & B1                              → "Jan Kowalski"
=CONCAT(A1:A5)                              → łączy wszystkie komórki bez separatora
=TEXTJOIN(", "; TRUE; A1:A10)               → "a, b, c, d" (ignoruje puste)
```

### 🔄 8.6. Konwersja typów

```excel
=VALUE("1 234,56")                          → 1234,56 (liczba)
=TEXT(1234,56; "# ##0,00 zł")               → "1 234,56 zł"
=TEXT(TODAY(); "DDDD")                      → "poniedziałek"
=TEXT(0,85; "0,00%")                        → "85,00%"
```

### 🆕 8.7. Pozostałe przydatne

- **`CHAR(10)`** — znak nowej linii (do łamania tekstu w komórkach)
- **`UNICODE(A1)`** — kod Unicode znaku
- **`EXACT(A1; B1)`** — porównanie z uwzględnieniem wielkości liter
- **`REPT("-"; 20)`** — powtórzenie tekstu 20 razy

---

## 9. Funkcje dat i czasu

### ⏰ 9.1. Pobieranie bieżącego czasu

| Funkcja | Zwraca | Zastosowanie |
|---|---|---|
| `TODAY()` | dzisiejsza data | wiek faktury: `=TODAY()-Data_Faktury` |
| `NOW()` | data + godzina | logi systemowe, pomiar czasu |

> ⚠️ Obie funkcje są **zmienne (volatile)** — przeliczają się przy każdej zmianie arkusza.

### 🧩 9.2. Rozbijanie daty na części

```excel
=YEAR(A1)          → 2026
=MONTH(A1)         → 4
=DAY(A1)           → 20
=WEEKDAY(A1; 2)    → 1 (poniedziałek, gdy drugi argument = 2)
=ISOWEEKNUM(A1)    → numer tygodnia wg ISO (standard europejski)
```

> 💡 `WEEKDAY(data; 2)` — poniedziałek = 1, niedziela = 7 (standard europejski)

### ⏳ 9.3. Obliczanie różnic i terminów

#### `DATEDIF` — ukryta funkcja Excela

```excel
=DATEDIF(Data_Urodzenia; TODAY(); "Y")    → wiek w latach
=DATEDIF(Start; End; "M")                  → różnica w miesiącach
=DATEDIF(Start; End; "D")                  → różnica w dniach
=DATEDIF(Start; End; "YM")                 → miesiące bez lat
=DATEDIF(Start; End; "MD")                 → dni bez miesięcy
```

> ⚠️ `DATEDIF` nie pojawia się w podpowiedziach Excela — musisz wpisać ją z pamięci!

#### Dni robocze i przesunięcia

```excel
=NETWORKDAYS(Start; End; Święta)          → dni robocze (bez weekendów i świąt)
=NETWORKDAYS.INTL(Start; End; 11)         → tylko niedziele jako weekend (np. Izrael)
=WORKDAY(TODAY(); 10; Święta)             → data 10 dni roboczych od dziś
=EDATE(TODAY(); 3)                         → dziś + 3 miesiące
=EDATE(TODAY(); -6)                        → dziś - 6 miesięcy
=EOMONTH(TODAY(); 0)                       → ostatni dzień bieżącego miesiąca
=EOMONTH(TODAY(); 1)                       → ostatni dzień przyszłego miesiąca
=YEARFRAC(Start; End; 1)                   → różnica w latach jako ułamek (np. 1,25)
```

### ⏱️ 9.4. Praca z czasem

```excel
=HOUR(A1)       → godzina
=MINUTE(A1)     → minuta
=SECOND(A1)     → sekunda
=TIME(14; 30; 0)  → 14:30:00
```

**Trik — czas pracy przez północ:**
Jeśli start 22:00, koniec 06:00, to `koniec - start` da wynik ujemny. Użyj:

```excel
=MOD(Koniec - Start; 1)     → zawsze zwróci poprawny czas
```

### 🎨 9.5. Formatowanie dat dla raportów

```excel
=TEXT(A1; "MMMM")       → "styczeń"
=TEXT(A1; "DDDD")       → "poniedziałek"
=TEXT(A1; "RRRR-MM")    → "2024-01" (idealne do tabel przestawnych)
=TEXT(A1; "DD.MM.RRRR") → "15.01.2024"
=TEXT(A1; "Q0")         → "Q1" (własny kwartał)
```

**Wyznaczanie kwartału z daty:**

```excel
=ROUNDUP(MONTH(A1)/3; 0)   → 1, 2, 3 lub 4
="Q" & ROUNDUP(MONTH(A1)/3; 0)   → "Q1", "Q2", ...
```

---

## 10. Najważniejsze wykresy

### 📊 10.1. Wykresy statystyczne (analiza rozkładu)

- **Histogram** — rozkład częstotliwości danych w przedziałach (bins); sprawdza normalność i skośność
- **Box & Whisker (pudełkowy)** — mediana, kwartyle, outliery; porównanie rozkładów między grupami
- **Wykres Pareto** — słupkowy + skumulowany procent; analiza „80/20" (które 20% problemów generuje 80% skutków)

### 📈 10.2. Analiza relacji i trendów

- **Wykres punktowy (Scatter Plot)** — **najważniejszy** do korelacji dwóch zmiennych
  - Dodaj **linię trendu** (linear, exponential, polynomial) + wartość $R^2$
- **Wykres kaskadowy (Waterfall)** — zmiany wartości przez serie dodatnich/ujemnych kroków (P&L)
- **Funnel (lejek)** — etapy procesu (np. konwersja w lejku sprzedażowym)

### 🎯 10.3. Zaawansowane elementy wizualne

- **Słupki błędów (Error Bars)** — niepewność danych (odchylenie standardowe, błąd standardowy, %)
- **Sparklines** — mini-wykresy w pojedynczej komórce (szybka ocena trendu)
- **Wykres kombi (Combo)** — łączenie słupków z linią + oś pomocnicza

### 🧪 10.4. Analysis ToolPak (dodatek do analizy danych)

> **Aktywacja:** `Plik → Opcje → Dodatki → Analysis ToolPak → Przejdź → OK`

Daje dostęp do:

| Narzędzie | Zastosowanie |
|---|---|
| **Regresja** | szczegółowe raporty: p-value, współczynniki, reszty, $R^2$ |
| **ANOVA** (jednoczynnikowa / dwuczynnikowa) | porównanie średnich z wielu grup |
| **Korelacja** | macierz korelacji dla wielu zmiennych |
| **Statystyka opisowa** | średnia, mediana, odchylenie, skośność, kurtoza jednym kliknięciem |
| **Histogram** | automatyczna tabela częstotliwości + wykres |
| **Próbkowanie** | losowe lub periodyczne |
| **Test t**, **Test F**, **Test Z** | testy hipotez |

### 🗺️ 10.5. Mapy i hierarchie

- **Filled Map** — automatyczne mapowanie danych geograficznych (kraj, województwo)
- **Treemap** — hierarchia i proporcje (udziały kategorii w rynku) jako prostokąty
- **Sunburst** — hierarchia promieniście od środka (np. kategoria → podkategoria → produkt)

### 💡 10.6. Dobre praktyki wykresów

1. **Jeden wykres = jedna myśl.** Nie pakuj 5 serii danych w jeden wykres.
2. **Usuń zbędny „chart junk"** — gridlines, ramki, 3D effects, cienie.
3. **Sortuj dane** przed utworzeniem wykresu słupkowego (od największego do najmniejszego).
4. **Używaj kolorów z sensem** — jeden kolor dla neutralnych, inny dla podkreślenia.
5. **Zacznij oś Y od zera** w wykresach słupkowych (inaczej wprowadzasz w błąd).
6. **Tytuł wykresu = wniosek**, nie tylko nazwa kategorii. Zamiast „Sprzedaż 2024" napisz „Sprzedaż spadła o 12% w Q4".

---

## 11. Tabele Excel (Excel Tables)

> **Skrót aktywacji:** `Ctrl + T` na dowolnym zakresie danych.

### 🔄 11.1. Dynamiczny zakres

Najważniejsza cecha — zakres rośnie automatycznie przy dopisywaniu wierszy. Wykresy, tabele przestawne i formuły same „zauważają" nowe dane.

### 🧱 11.2. Odwołania strukturalne

Zapomnij o `=SUMA($B$2:$B$500)`. W tabelach używamy **nazw kolumn**:

```excel
=SUM(Sprzedaż[Przychód])
=SUMIFS(Sprzedaż[Kwota]; Sprzedaż[Region]; "Północ")
=Sprzedaż[@Cena] * Sprzedaż[@Ilość]    → @ oznacza „ten sam wiersz"
```

| Składnia | Znaczenie |
|---|---|
| `Tabela[Kolumna]` | cała kolumna (bez nagłówka) |
| `Tabela[@Kolumna]` | wartość z tego samego wiersza |
| `Tabela[#Nagłówki]` | sam nagłówek |
| `Tabela[#Razem]` | wiersz sumy |
| `Tabela[#Wszystko]` | cała tabela z nagłówkami |

### 🔁 11.3. Kolumny obliczane

Wpisujesz formułę **raz** — Excel kopiuje ją do wszystkich wierszy. Nowy wiersz → automatyczne wypełnienie formuły. Koniec z „zapomniałem przeciągnąć do końca".

### Σ 11.4. Wiersz sumy (Total Row)

W menu „Projektowanie tabeli" → ✅ Wiersz sumy. Rozwijane menu z: Suma, Średnia, Licznik, Max, Min, Odchylenie, Wariancja.

> 💡 Wiersz sumy używa funkcji `SUBTOTAL`/`AGGREGATE` — **reaguje na filtry** (liczy tylko widoczne wiersze).

### 🎛️ 11.5. Fragmentatory (Slicers)

Wizualne filtry z interaktywnymi przyciskami — idealne do prostych dashboardów.

**Aktywacja:** kliknij tabelę → Wstawianie → Fragmentator → wybierz kolumny.

### 🔌 11.6. Integracja z Power Query

Tabele to **jedyny zalecany format źródłowy** dla Power Query wewnątrz Excela. PQ automatycznie rozpoznaje nagłówki i typy danych — import i czyszczenie są bezbłędne.

### 🎨 11.7. Bonus: dobre praktyki nazewnicze

- Nazywaj tabele w „Projektowaniu tabeli" (zamiast `Tabela1`, `Tabela2`)
- Konwencja: `tab` + rzeczownik, np. `tabSprzedaż`, `tabKlienci`, `tabProdukty`
- Nazwy kolumn w formie przyjaznej (bez skrótów, jedno słowo lub krótka fraza)

---

## 12. Formatowanie i współpraca

### 🎨 12.1. Formatowanie niestandardowe (Custom Formatting)

> Zmienia wygląd komórki **bez zmiany wartości** — formuły nadal działają.

**Struktura formatu:** `dodatnie ; ujemne ; zero ; tekst`

| Kod formatu | Efekt |
|---|---|
| `0;-0;;@` | ukrywa zera |
| `# ##0 "kg"` | liczba z jednostką (bez psucia typu) |
| `[Zielony]# ##0;[Czerwony]-# ##0` | kolory bezpośrednio w formacie |
| `0,00%` | procenty |
| `# ##0,00 "zł"` | waluta z rozdzielaniem tysięcy |
| `[>1000000]0,0 "M";[>1000]0,0 "K";0` | skala: M / K / pełna |

### 🌡️ 12.2. Formatowanie warunkowe

- **Paski danych (Data Bars)** — mini-wykresy w komórkach
- **Skale kolorów (Heatmaps)** — „gorące" vs „zimne" punkty
- **Zestawy ikon** — strzałki, flagi, wskaźniki

#### Formuły w formatowaniu warunkowym

> Potężniejsze niż gotowe schematy Excela.

```excel
=$C2 < TODAY()            → podświetl cały wiersz gdy data w C przeterminowana
=MOD(ROW(); 2) = 0        → zebra striping (co drugi wiersz)
=COUNTIF($A$2:$A$100; A2) > 1   → podświetl duplikaty
```

> 💡 **Ważne:** W formule używaj odwołania do **pierwszej komórki** zakresu + mieszane blokowanie (`$C2`, nie `$C$2`).

### 🛡️ 12.3. Walidacja danych (Data Validation)

„Idiot-proofing" arkusza przed współpracą:

- **Listy rozwijane** — zamiast „W-wa", „Warszawa", „Waw" jedna konkretna opcja
- **Ograniczenia** — cena nie może być ujemna ani tekstem
- **Niestandardowe formuły** — np. `=ISNUMBER(A1)` albo `=LEN(A1)=11` (PESEL)
- **Komunikaty wprowadzania** i **komunikaty błędów** — instrukcje dla użytkownika

### 👥 12.4. Współpraca (Collaboration)

#### Widok arkusza (Sheet View)

W pliku z 10 osobami filtruj/sortuj bez psucia widoku innym — twój filtr widzisz **tylko Ty**.

#### Komentarze vs Notatki

| Funkcja | Zastosowanie |
|---|---|
| **Komentarze** | dyskusja (jak czat), oznaczanie osób `@Imię` |
| **Notatki** | techniczne wyjaśnienia („Dane pochodzą z raportu SAP") |

### 🔒 12.5. Ochrona i uprawnienia

- **Chroń arkusz** — zablokuj komórki z formułami, odblokuj pola danych
- **Zezwalaj na edycję zakresów** — marketing edytuje tylko A:C, sprzedaż D:F
- **Chroń skoroszyt** — blokada dodawania/usuwania arkuszy
- **Ukrywanie formuł** — atrybut „Ukryta" w formatowaniu komórki + ochrona arkusza

### 🕰️ 12.6. Historia wersji (Version History)

W M365 / Excel Online: `Plik → Informacje → Historia wersji` — przywracaj plik sprzed godziny / dnia / tygodnia. **Najskuteczniejszy sposób na unikanie katastrof.**

### 📦 12.7. Przekazywanie plików (Handoff)

> 🏆 **Złota zasada:** Twój plik musi być zrozumiały bez Twojej obecności.

1. **Arkusz „Readme"** — pierwsze zakładka z opisem:
   - Źródło danych
   - Co oznaczają kolory
   - Data ostatniej aktualizacji
   - Kontakt do autora
2. **Spójna kolorystyka:**
   - 🟡 żółty = ręczne wprowadzenie
   - ⬜ szary = formuła (nie dotykać!)
   - 🔵 niebieski = dane z innego źródła
3. **Nazwy zakresów** — zamiast `$B$2:$B$100` używaj nazwanego zakresu `Przychody`
4. **Sprawdzanie ułatwień dostępu** — `Recenzja → Sprawdź ułatwienia dostępu` (kontrast, alt-teksty)

---

## 13. Skróty klawiszowe — must-know

### ⚡ Najbardziej produktywne

| Skrót | Działanie |
|---|---|
| `Ctrl + T` | utwórz tabelę Excel |
| `Ctrl + Shift + L` | włącz/wyłącz filtr |
| `Ctrl + ;` | wstaw dzisiejszą datę (stałą) |
| `Ctrl + Shift + ;` | wstaw bieżącą godzinę (stałą) |
| `Ctrl + Strzałka` | skok do końca danych w kierunku |
| `Ctrl + Shift + Strzałka` | zaznacz do końca danych |
| `Ctrl + Shift + V` | wklej specjalnie (w nowszych wersjach) |
| `Alt + =` | autosuma |
| `F2` | edytuj komórkę |
| `F4` | zablokuj odwołanie (`A1` → `$A$1` → `A$1` → `$A1`) |
| `F9` | przelicz cały skoroszyt |
| `Ctrl + 1` | okno formatowania komórek |
| `Ctrl + PgUp` / `PgDn` | przełączanie między arkuszami |
| `Alt + Enter` | nowa linia wewnątrz komórki |
| `Ctrl + D` / `Ctrl + R` | wypełnij w dół / w prawo |
| `Ctrl + Z` / `Ctrl + Y` | cofnij / ponów |

### 🎯 Tabele przestawne i analiza

| Skrót | Działanie |
|---|---|
| `Alt + N + V` | wstaw tabelę przestawną |
| `Alt + F1` | szybki wykres z zaznaczenia |
| `F11` | wykres na nowym arkuszu |

---

## 14. Najczęstsze błędy i ich znaczenie

| Błąd | Co oznacza | Jak naprawić |
|---|---|---|
| `#DIV/0!` | dzielenie przez zero | `=IFERROR(A/B; 0)` |
| `#N/A` | nie znaleziono wartości | sprawdź dopasowanie, użyj `IFNA` lub argumentu w XLOOKUP |
| `#VALUE!` | zły typ danych (np. tekst zamiast liczby) | użyj `VALUE()`, sprawdź źródło |
| `#REF!` | uszkodzone odwołanie (usunięta komórka) | cofnij operację, napraw formułę |
| `#NAME?` | nierozpoznana nazwa funkcji/zakresu | sprawdź pisownię, język funkcji |
| `#NUM!` | nieprawidłowa liczba (np. pierwiastek z -1) | sprawdź argumenty funkcji |
| `#NULL!` | niepoprawne przecięcie zakresów | sprawdź operatory (spacja = przecięcie) |
| `#SPILL!` | tablica dynamiczna nie ma miejsca | usuń blokadę w zakresie rozlewania |
| `#CALC!` | błąd obliczeń (pusta tablica) | sprawdź logikę, obsłuż `IFERROR` |
| `######` | kolumna za wąska dla liczby/daty | poszerz kolumnę (nie jest prawdziwym błędem) |

### 🛠️ Funkcje do obsługi błędów

```excel
=IFERROR(formuła; "Brak danych")        → łapie wszystkie błędy
=IFNA(formuła; "Nie znaleziono")        → łapie tylko #N/A (bardziej precyzyjne)
=ISERROR(A1)                            → zwraca TRUE/FALSE
=ISBLANK(A1)                            → sprawdza czy pusta
=ISNUMBER(A1) / =ISTEXT(A1)             → sprawdza typ
```

---

## 15. Bonus: Power Query i Power Pivot

### 🔄 15.1. Power Query (Pobieranie i przekształcanie)

> **Najważniejsze narzędzie nowoczesnego analityka w Excelu.**

Pozwala na **powtarzalny ETL** (Extract, Transform, Load) bez pisania kodu.

**Typowy workflow:**
1. **Import** z dowolnego źródła (CSV, baza danych, WWW, folder, API)
2. **Transformacje** (UI drag-and-drop):
   - Usuwanie duplikatów, filtrowanie
   - Pivot / Unpivot (kolumny ↔ wiersze)
   - Łączenie zapytań (Merge = JOIN, Append = UNION)
   - Czyszczenie tekstów, dzielenie kolumn
3. **Załaduj** do tabeli, modelu danych lub tylko połączenia
4. **Odśwież** jednym kliknięciem — wszystkie kroki powtarzają się automatycznie

**Zaleta:** każdy krok jest zapisywany jako **lista instrukcji** — od dziś każde czyszczenie danych robisz raz.

### 📐 15.2. Power Pivot i model danych

- **Relacje między tabelami** — jak w bazie danych (klient_id ↔ zamówienia)
- **Język DAX** — potężniejsza wersja formuł Excela:

```dax
Sprzedaż YTD := TOTALYTD(SUM(Sprzedaż[Kwota]); Kalendarz[Data])
Udział % := DIVIDE([Sprzedaż]; CALCULATE([Sprzedaż]; ALL(Produkty)))
```

- **Miary (Measures)** vs **kolumny obliczane**
- **Hierarchie** (Rok → Kwartał → Miesiąc → Dzień)

> 💡 Power Pivot obsługuje **dziesiątki milionów wierszy** — zwykły Excel pada przy ~1 mln.

---

## 🎓 Podsumowanie — cheat sheet analityka

### Top 10 funkcji, które musisz znać na pamięć

1. `XLOOKUP` / `INDEX+MATCH` — łączenie danych
2. `SUMIFS` / `COUNTIFS` / `AVERAGEIFS` — agregacja warunkowa
3. `IFERROR` — obsługa błędów
4. `IF` / `IFS` — logika
5. `FILTER` / `UNIQUE` / `SORT` — dynamiczne tablice
6. `TEXT` / `VALUE` — konwersja typów
7. `TRIM` / `SUBSTITUTE` — czyszczenie tekstu
8. `DATEDIF` / `EOMONTH` / `NETWORKDAYS` — praca z datami
9. `LET` — optymalizacja złożonych formuł
10. `SUMPRODUCT` — średnia ważona i obliczenia tablicowe

### Kluczowe wzory matematyczne

**Średnia ważona:**
$$\bar{x}_w = \frac{\sum_{i=1}^{n} (w_i \cdot x_i)}{\sum_{i=1}^{n} w_i}$$

**Odchylenie standardowe populacji:**
$$\sigma = \sqrt{\frac{\sum (x_i - \mu)^2}{N}}$$

**Współczynnik korelacji Pearsona:**
$$r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \cdot \sum (y_i - \bar{y})^2}}$$



---