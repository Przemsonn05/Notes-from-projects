# Teoria PostgreSQL

### Lab1:

| Polecenie       | Znaczenie                             |
| --------------- | ------------------------------------- |
| \l              | wyświetla listę baz danych            |
| \dt             | wyświetla listę tabel w bieżącej BD   |
| \d nazwa_tabeli | wyświetla strukturę tabeli            |
| \\?             | wyświetla pomoc odnośnie poleceń psql |
| \h              | wyświetla pomoc odnośnie SQL          |
| \q              | kończy pracę z psql                   |
| \i nazwa_pliku  | wykonuje skrypt                       |
| \dn             | wyświetla nazwy schematów             |

```sql
select * from dane   --wybiera wszystkie dane z tabelki dane
```

CTRC + C - przerywa bieżące zapytanie, np. w wyniku błędu

```sql
set search_path to nazwa_schematu;   --ustawia bieżący schemat na pierwszy plan
set search_path to public;   --ustawia relacje na publiczną, czyli zamyka bieżące
show search_path;   --aby wyświetlić zawartość zmiennej środowiskowej
```


```sql
select * from klienci
where miejscowosc in ('Warszawa');   --klienci z Warszawy
where miejscowosc <> 'Warszawa';   --klienci nie, którzy nie są z Warszawy
where ulica like 'Sandomierska%';   --klienci mieszkaja na ulicy sandomierskiej
								   --% - zastępuje dowolny ciąg znaków, czyli moze                                          byc np. Sandomierska37
where odleglosc > 100;   -- odleglosc wieksza od 100km
```


### Lab2:

```sql
order by nazwa   -- sortowanie według danej nazwy
order by nazwa1 desc, nazwa2 asc   -- sortowanie malejące wg nazwa1 i rosnące wg                                         nazwa2
```

Działania matematyczne:

```sql
select 23 * 45 + 33;
select power(2, 20);
select sqrt(3);
```

```sql
where nazwa is not null   -- dana komórka nie jest pusta, wyświetla ją
where nazwa is null   -- wyswietla sie tam gdzie null
```

```sql
-- dodaje do mojej bazy widok występy
create view wystepy as
select *
from siatkowka.siatkarki natural join siatkowka.punkty;
```



### Lab3

##### Wyłuskanie przedział zamówień na podstawie konkretnej daty:

```sql
select * from zamowienia
where datazamowienia between '2025-12-01' and '2025-12-31'

----------------- 2 sposób

select * from zamowienia
where date_part('year', datarealizacji) = 2025
	and date_part('month', datarealizacji) = 12
```

##### Zamówienie w poszczególnych dniach miesiąca

```sql
select * from zamowienia
where date_part('day', datarealizacji) in (17, 18, 19)
```

##### Słowo zaczyna się na S i jakiś inny wyraz zaczyna się na m

```sql
select * from czekoladki
where nazwa ~ '^S' and nazwa ~ '\ym'
```

##### Słowo zaczyna się na litery od A do C

```sql
select * from czekoladki
where nazwa similar to '[A-C]%'
```


##### Jedno słowo (czyli nie zawiera spacji)

```sql
select * from czekoladki
where nazwa not like '% %'
```


##### Zamiana metodą replace

```sql
-- wyszukuje numery które mają 10 cyfr bez spacji) --
select nazwa, telefon from klienci
where replace(telefon, ' ', '') like '__________'
```


##### Metoda UNION - suma zbiorów

```sql
select idczekoladki, nazwa, masa, koszt from czekoladki
where (masa >= 15 and masa <= 24)
union
select idczekoladki, nazwa, masa, koszt from czekoladki
where (koszt >= 0.15 and koszt <= 0.24)
```


##### Metoda INTERSECT - część wspólna zbiorów

```sql
(select idczekoladki, nazwa, masa, koszt from czekoladki
where (masa >= 15 and masa <= 24)
intersect
select idczekoladki, nazwa, masa, koszt from czekoladki
where (koszt >= 0.15 and koszt <= 0.24))
union
(select idczekoladki, nazwa, masa, koszt from czekoladki
where (masa >= 25 and masa <= 35)
intersect
select idczekoladki, nazwa, masa, koszt from czekoladki
where (koszt >= 0.25 and koszt <= 0.35))
```


##### Metoda EXCEPT - różnica zbiorów

```sql
select idczekoladki, nazwa, masa, koszt from czekoladki
where (masa >= 25 and masa <= 35)
except
select idczekoladki, nazwa, masa, koszt from czekoladki
where (koszt >= 0.25 and koszt <= 0.35)
```


##### Wyszukiwanie osób, które nie złożyły żadnego zamówienia

```sql
select idklienta from klienci
except
select idzamowienia from zamowienia;
```


##### Sumowanie punktów z warunkiem

```sql
select 
	idmeczu, gospodarze, goscie,
	(select sum(punkty_gosp) from unnest(gospodarze) punkty_gosp) as punkty1,
	(select sum(punkty_gosc) from unnest(goscie) punkty_gosc) as punkty2
from 
	siatkowka.statystyki
where
	array_length(gospodarze, 1) = 5
	and 
	(
		gospodarze[5] > 15 or goscie[5] > 15
	)
order by
	idmeczu
```


##### Gdy suma punktów gospodarzy jest powyżej 100

```sql
select
	idmeczu,
	punkty1,
	punkty2
from (select
	idmeczu,
	(select sum(punkty_gosp) from unnest(gospodarze) punkty_gosp) as punkty1,
	(select sum(punkty_gosc) from unnest(goscie) punkty_gosc) as punkty2
	from
		siatkowka.statystyki
)
WHERE
    punkty1 > 100
ORDER BY
    idmeczu;
```


#### CASE WHEN - WHAT IF, struktura

```sql
select 
    nazwa,
    cena,
    case 
        when cena > 100 then 'Drogi'
        when cena > 50 then 'Średni'
        else 'Tani'
    end as kategoria_ceny
from produkty;
```


#### JOIN ON

```sql
select kolumny
from tabela1
join tabela2 on tabela1.kolumna = tabela2.kolumna
```

```sql
SELECT Pracownicy.Imie, Dzialy.Nazwa_dzialu
FROM Pracownicy
JOIN Dzialy ON Pracownicy.ID_dzialu = Dzialy.ID_dzialu;
```

INNER JOIN - zwraca rekordy, które mają pasujące wartości w obu tabelach
LEFT JOIN - zwraca wszystkie rekordy z lewej tabeli i pasujące z prawej
RIGHT JOIN - zwraca wszystkie rekordy z lewej tabeli i pasujące z prawej
FULL OUTER JOIN  - zwraca wszystkie rekordy z obu tabel


### Lab4

`NATURAL JOIN` automatycznie łączy dwie tabele na podstawie **wszystkich kolumn, które mają identyczne nazwy i zgodne typy danych w obu tabelach**. Łączy tabele automatycznie po kolumnach o tej samej nazwie.

```sql
select
	z.datarealizacji,
	z.idzamowienia,
	k.nazwa
from zamowienia z
join klienci k on z.idklienta = k.idklienta
where k.nazwa like '% Antoni'
```

zadanie z połączeniem najważniejszych operacji tj. join … on, where, like, itp.


```sql
datarealizacji >= (current_date - interval '15 years')
--złożenie zamówienia z datą realizacji nie starszą niż sprzed piętnastu lat--
```

```sql
extract(month from z.datarealizacji) = 11
--wyłuskanie miesiąca z daty--
```


Żeby użyć atrybutu danej tabeli można dostać się do niej używają paru join-ów: (jeśli mamy do wyświetlenia dane, które są z innej tabeli i nie mają z nią bezpośredniego połączenia)

```sql
select
	k.idklienta,
	k.nazwa,
	k.ulica,
	k.miejscowosc,
	p.nazwa
from klienci k
join zamowienia z on k.idklienta = z.idklienta
join artykuly a on a.idzamowienia = z.idzamowienia
join pudelka p on p.idpudelka = a.idpudelka
where p.nazwa in ('Kremowa fantazja', 'Kolekcja jesienna')
```


#### Exists

`EXISTS` służy do **sprawdzenia, czy podzapytanie zwraca jakikolwiek wynik** (czyli choć jeden wiersz).

- Jeśli podzapytanie zwróci **choć jeden rekord**, `EXISTS` → zwraca **TRUE**.
    
- Jeśli podzapytanie nie zwróci nic, `EXISTS` → zwraca **FALSE**.

```sql
SELECT kolumny
FROM tabela1 t1
WHERE EXISTS (
    SELECT 1
    FROM tabela2 t2
    WHERE warunek_powiązania
);
```


```sql
select distinct
...
...
...
--automatycznie usuwa duplikaty--
```

```sql
ILIKE 
--(PostgreSQL) = wyszukiwanie tekstu bez uwzględniania wielkości liter--
```


Przykład zadania z warunkiem w warunku:
```sql
select idczekoladki, nazwa 
from czekoladki
where koszt > (
	select koszt
	from czekoladki
	where idczekoladki = 'd08'
)
```


### Lab5

COUNT - służy do **liczenia liczby wierszy**, które spełniają określone kryteria.

```sql
count(*) -- liczy wszystkie wiersze
count(column_name) --liczy wiersze bez null
count(distinct column_name) --liczy unikalne wartości
```


Instrukcja `CASE WHEN` w SQL to **konstrukcja warunkowa**, która działa bardzo podobnie do `if ... then ... else` w językach programowania.

Pozwala ona na **tworzenie nowej wartości lub podejmowanie decyzji w locie** wewnątrz zapytania SQL, w zależności od tego, czy określone warunki są spełnione.

```postgresql
CASE
  WHEN warunek_1 THEN wynik_1
  WHEN warunek_2 THEN wynik_2
  ...
  ELSE wynik_domyslny
END
```


`SUM()` to **funkcja agregująca** w SQL, która służy do **obliczania sumy (całkowitej wartości) wszystkich liczb w danej kolumnie**.

Przykład kodu z wykorzystaniem funkcji:

```postgresql
select a.idpudelka
from artykuly a
group by a.idpudelka
having sum(
	case
		when a.sztuk is not null then a.sztuk
		else 0
	end
) = (
	select max(max_pudelko)
	from (
		select sum(
			case
				when a.sztuk is not null then a.sztuk
				else 0
			end
		) as max_pudelko
		from artykuly a
		group by a.idpudelka
	)
)
```


`extract` - to funkcja w SQL, która służy do **wyciągania określonej części (jak rok, miesiąc, dzień, godzina) z wartości typu data lub czas** (np. z kolumny typu `DATE`, `TIMESTAMP` lub `TIME`).

```postgresql
select extract(year from datarealizacji) as rok,
	   extract(month from datarealizacji) as miesiąc,
	   count(*) as ilosc
from zamowienia
group by rok, miesiąc
order by rok, miesiąc
```


Przykład z zadania:

```postgresql
SELECT 
    SUM(
        a.sztuk * (
            p.cena 
            -
            (
                SELECT SUM(z.sztuk * c.koszt)
                FROM zawartosc z
                JOIN czekoladki c ON z.idczekoladki = c.idczekoladki
                WHERE z.idpudelka = p.idpudelka
            )
        )
    ) AS zysk_calkowity
FROM artykuly a
JOIN pudelka p ON a.idpudelka = p.idpudelka;

```


Przykład zadania z połączeniem poznanej składni:

Zad.5.7.3
```postgresql
select s.imie, s.nazwisko, s.iddruzyny, sum(p.asy), sum(p.bloki) 
from siatkowka.siatkarki
join siatkowka.punkty p on s.iddruzyny = p.iddruzyny and s.numer = p.numer
group by s.numer, s.imie. s.nazwisko, s.iddruzyny
having sum(p.punkty) >= 150
order by (sum(p.asy), sum(p.bloki)) desc
```

Zad.5.7.4
```postgresql
select s.imie, s.nazwisko, s.iddruzyny, sum(p.punkty)
from siatkowka.siatkarki
join siatkowka.punkty p on s.iddruzyny = p.iddruzyny and s.numer = p.numer
group by s.numer, s.imie, s.nazwisko, s.iddruzyny
having sum(p.punkty) / 2 < sum(p.bloki)
order by s.imie, s.nazwisko, s.iddruzyny, sum(p.punkty)
```


`array_length` - w PostgreSQL służy do pobierania liczby elementów w określonym wymiarze tablicy.

```postgresql
-- Tablica liczbowa
SELECT array_length(ARRAY[10, 20, 30, 40], 1);
--w nawiasie -> tablica, numer wymiaru
```

Przykład zadania: zad.5.8.1:
```postgresql
select d.iddruzyny, d.nazwa, count(distinct s.idmeczu) as ile_meczy
from siatkowka.druzyny d
join siatkowka.statystyki s on d.iddruzyny = s.iddruzyny
where array_length(s.gospodarze, 1) = 5 or array_length(s.goscie, 1) = 5
group by d.iddruzyny, d.nazwa
order by ile_meczy desc;
```


### Lab6

W PostgreSQL dodajesz dane do tabeli używając polecenia `INSERT INTO`. Oto podstawowa składnia:

```postgresql
INSERT INTO nazwa_tabeli (kolumna1, kolumna2, kolumna3)
VALUES (wartość1, wartość2, wartość3);
```

Przykłady:
```postgresql
-- Dodanie jednego wiersza
INSERT INTO users (name, email, age)
VALUES ('Jan Kowalski', 'jan@example.com', 30);

-- Dodanie wielu wierszy naraz
INSERT INTO users (name, email, age)
VALUES 
    ('Anna Nowak', 'anna@example.com', 25),
    ('Piotr Wiśniewski', 'piotr@example.com', 35),
    ('Maria Zielińska', 'maria@example.com', 28);

-- Jeśli podajesz wartości dla wszystkich kolumn w kolejności, możesz pominąć nazwy kolumn
INSERT INTO users
VALUES (4, 'Tomasz Lewandowski', 'tomasz@example.com', 32);

-- Kolumny z wartościami domyślnymi lub NULL można pominąć
INSERT INTO users (name, email)
VALUES ('Katarzyna Dąbrowska', 'kasia@example.com');
```


Polecenie `DELETE` w PostgreSQL służy do usuwania wierszy z tabeli. Oto jak działa:

```postgresql
DELETE FROM nazwa_tabeli
WHERE warunek;
```

Przykłady:
```postgresql
-- Usunięcie konkretnego użytkownika po ID
DELETE FROM users
WHERE id = 5;

-- Usunięcie wielu wierszy spełniających warunek
DELETE FROM users
WHERE age < 18;

-- Usunięcie z wieloma warunkami
DELETE FROM users
WHERE age > 65 AND status = 'inactive';

-- Usunięcie z warunkiem OR
DELETE FROM users
WHERE email IS NULL OR email = '';

-- Usunięcie z użyciem LIKE
DELETE FROM products
WHERE name LIKE '%test%';

-- Usunięcie z podzapytaniem
DELETE FROM orders
WHERE user_id IN (SELECT id FROM users WHERE deleted = true);
```


Polecenie `UPDATE` w PostgreSQL służy do modyfikowania istniejących danych w tabeli. Oto jak działa:

```postgresql
UPDATE nazwa_tabeli
SET kolumna1 = nowa_wartość1, kolumna2 = nowa_wartość2
WHERE warunek;
```

Przykłady:
```postgresql
-- Aktualizacja jednej kolumny dla jednego wiersza
UPDATE users
SET email = 'nowy@example.com'
WHERE id = 5;

-- Aktualizacja wielu kolumn naraz
UPDATE users
SET name = 'Jan Nowak', age = 31, status = 'active'
WHERE id = 5;

-- Aktualizacja wielu wierszy
UPDATE products
SET price = price * 1.1
WHERE category = 'electronics';

-- Aktualizacja z obliczeniami
UPDATE employees
SET salary = salary + 500
WHERE department = 'IT';
```


RETURNING - zwracanie zaktualizowanych danych:

```postgresql
-- Zobacz co zostało zaktualizowane
UPDATE users
SET status = 'active'
WHERE last_login > NOW() - INTERVAL '30 days'
RETURNING *;

-- Zwróć tylko wybrane kolumny
UPDATE products
SET price = price * 0.9
WHERE stock > 100
RETURNING id, name, price;
```


Funkcja `RIGHT()` w PostgreSQL zwraca określoną liczbę znaków z **prawej strony** (końca) tekstu.

```postgresql
RIGHT(tekst, liczba_znaków)
```


COPY FROM — wczytywanie danych z pliku do tabeli
```postgresql
COPY nazwa_tabeli (kolumna1, kolumna2, ...)
FROM 'ścieżka/do/pliku.txt'
WITH (FORMAT text);
```

COPY TO — zapis danych z tabeli do pliku
```postgresql
COPY nazwa_tabeli (kolumna1, kolumna2, ...)
TO 'ścieżka/do/pliku.txt'
WITH (FORMAT text);
```



### Lab 9

`CREATE TABLE` - To podstawowa komenda służąca do **utworzenia nowej tabeli** w bazie danych. Definiujesz w niej nazwy kolumn oraz typy danych, jakie będą przechowywane (np. liczby, tekst, daty).

```postgreSQL
CREATE TABLE uzytkownicy ( 
	id SERIAL PRIMARY KEY, 
	imie VARCHAR(50), 
	wiek INT 
);
```


`REFERENCES` - Słowo kluczowe używane przy tworzeniu **klucza obcego (Foreign Key)**. Mówi bazie danych: _"Wartość w tej kolumnie musi istnieć w innej tabeli, do której się odnoszę"_. Służy do łączenia tabel relacjami.

```postgreSQL
CREATE TABLE zamowienia (
    id_zamowienia SERIAL PRIMARY KEY,
    id_uzytkownika INT REFERENCES uzytkownicy(id) -- Tutaj używamy REFERENCES
);
```


`ALTER TABLE` - Ta komenda służy do **modyfikowania już istniejącej tabeli**. Używasz jej, gdy struktura bazy jest gotowa, ale musisz coś zmienić (np. dodać nową kolumnę, zmienić typ danych, usunąć kolumnę).

```postgreSQL
ALTER TABLE uzytkownicy ADD COLUMN nazwisko VARCHAR(100);
```


`ADD CONSTRAINT` - Jest to podkomenda używana wewnątrz `ALTER TABLE`. Służy do **dodania nowej reguły do istniejącej tabeli**.

```postgreSQL
ALTER TABLE uzytkownicy
ADD CONSTRAINT sprawdz_wiek CHECK (wiek >= 18);
```


`ALTER TABLE ONLY` - To pojęcie jest specyficzne dla **PostgreSQL**, który obsługuje **dziedziczenie tabel** (table inheritance).

Normalnie, gdy zmieniasz tabelę-rodzica (np. dodajesz kolumnę), zmiana ta automatycznie propaguje się na wszystkie tabele-dzieci (tabele dziedziczące). Słowo kluczowe **ONLY** zapobiega temu zachowaniu. Oznacza: _"Zmień tylko tę konkretną tabelę, ale zostaw tabele dziedziczące w spokoju"_.

```postgreSQL
ALTER TABLE ONLY produkt ADD COLUMN data_waznosci DATE;
```


`ADD COLUMN` - Służy do wstawienia nowej kolumny do istniejącej tabeli.

```postgreSQL
ALTER TABLE pracownicy
ADD COLUMN telefon VARCHAR(15);
```


`DROP COLUMN` - Służy do całkowitego usunięcia kolumny z tabeli.

```postgreSQL
ALTER TABLE pracownicy
DROP COLUMN faks;
```


`RENAME COLUMN` - Służy do zmiany samej nazwy kolumny bez utraty danych, które się w niej znajdują. Często używane, gdy zrobisz literówkę przy tworzeniu tabeli lub gdy zmienia się kontekst biznesowy.

```postgreSQL
ALTER TABLE pracownicy
RENAME COLUMN telefon TO telefon_komorkowy;
```


`ALTER COLUMN ... SET/DROP DEFAULT` - Służy do zmiany wartości domyślnej dla **nowych** wierszy. Nie zmienia to danych, które już są w tabeli.

```postgreSQL
ALTER TABLE pracownicy
ALTER COLUMN status SET DEFAULT 'aktywny';
```


`ALTER COLUMN ... SET/DROP NOT NULL` - Służy do zmiany reguły, czy kolumna może być pusta, czy nie.

```postgreSQL
ALTER TABLE pracownicy
ALTER COLUMN nazwisko DROP NOT NULL;
```


`CREATE VIEW` - jeśli chcesz zmienić definicję widoku (np. dodać kolumnę do SELECT), używasz tej komendy. Działa ona jak "Zastąp, jeśli istnieje".

```postgreSQL
CREATE VIEW raport_sprzedazy AS
SELECT 
    k.nazwa_klienta, 
    p.nazwa_produktu, 
    z.data_zamowienia,
    z.kwota
FROM zamowienia z
JOIN klienci k ON z.klient_id = k.id
JOIN produkty p ON z.produkt_id = p.id;
```


`COPY ... FROM STDIN` - o polecenie, które mówi bazie danych: _"Nie szukaj pliku na dysku. Zaraz prześlę Ci dane bezpośrednio w tym samym strumieniu co polecenia, linijka po linijce."_

```postgreSQL
-- Krok 1: Wpisujesz komendę
COPY pracownicy (imie, nazwisko) FROM STDIN;

-- Krok 2: Baza czeka (prompt zmienia się), a Ty wpisujesz dane oddzielone tabulatorami
Jan	Kowalski
Anna	Nowak
Piotr	Zielinski

-- Krok 3: Kończysz wprowadzanie specjalnym znakiem
\.
```



### Lab10

`NATURAL JOIN` - To "inteligentne" (lub leniwe) łączenie tabel. Baza danych automatycznie szuka kolumn w obu tabelach, które mają **tę samą nazwę** i łączy je po nich.

```postgreSQL
-- Zamiast pisać:
SELECT * FROM pracownicy JOIN departamenty ON pracownicy.dep_id = departamenty.dep_id;

-- Piszesz:
SELECT * FROM pracownicy NATURAL JOIN departamenty;
```


`IN/NOT IN` - Służy do sprawdzenia, czy wartość znajduje się na podanej **liście** lub w wyniku podzapytania.

```postgreSQL
SELECT * FROM produkty 
WHERE kategoria IN ('Elektronika', 'Zabawki');
```


`EXISTS/NOT EXISTS` - To logiczne sprawdzenie **istnienia**. Służy do zbadania, czy podzapytanie zwraca **jakikolwiek wiersz**.

```postgreSQL
SELECT * FROM klienci k
WHERE EXISTS (
    SELECT 1 FROM zamowienia z 
    WHERE z.id_klienta = k.id
);
```


`ANY/ALL` - Operatory używane w połączeniu z porównaniami (`=`, `>`, `<`, `<>`) do pracy z listą wartości.

#### **ANY (lub SOME)**

Warunek jest spełniony, jeśli **przynajmniej jedna** wartość z listy pasuje.

- `> ANY (10, 20, 30)` oznacza: większe niż 10 (bo 10 to najmniejsza z opcji, wystarczy być większym od niej). To to samo co "większe od minimum".
    
- `= ANY (...)` działa identycznie jak `IN (...)`.
    

#### **ALL**

Warunek musi być spełniony dla **wszystkich** wartości z listy.

- `> ALL (10, 20, 30)` oznacza: większe niż 30 (musi być większe od 10 ORAZ 20 ORAZ 30). To to samo co "większe od maksimum".

```postgreSQL
SELECT * FROM pracownicy 
WHERE zarobki > ALL (SELECT zarobki FROM pracownicy WHERE dzial = 'IT');
```

Szukamy pracownika z innego działu, który zarabia **więcej niż ktokolwiek z IT**:
```postgreSQL
SELECT imie, zarobki 
FROM pracownicy 
WHERE dzial = 'HR' 
AND zarobki > ANY (SELECT zarobki FROM pracownicy WHERE dzial = 'IT');
```

Szukamy pracowników, których zarobki są **identyczne** jak kogoś w Marketingu:
```postgreSQL
SELECT imie, zarobki 
FROM pracownicy 
WHERE zarobki = ANY (SELECT zarobki FROM pracownicy WHERE dzial = 'Marketing');
```

Szukamy pracownika, który zarabia **więcej niż wszyscy w IT razem wzięci** (czyli więcej niż najlepiej zarabiający informatyk):
```postgreSQL
SELECT imie, zarobki 
FROM pracownicy 
WHERE dzial = 'HR' 
AND zarobki > ALL (SELECT zarobki FROM pracownicy WHERE dzial = 'IT');
```

Szukamy kogoś, kto zarabia mniej niż **każdy** pracownik Marketingu (czyli mniej niż najgorzej zarabiający w Marketingu).
```postgreSQL
SELECT imie, zarobki 
FROM pracownicy 
WHERE zarobki < ALL (SELECT zarobki FROM pracownicy WHERE dzial = 'Marketing');
```


`WITH` - Pozwala zdefiniować tymczasową tabelę (taki "bufor" lub nazwane podzapytanie), która istnieje tylko na czas trwania jednego zapytania.

```postgreSQL
WITH srednia_ogolna AS (
    -- Krok 1: Jedna liczba (średnia firmy)
    SELECT AVG(zarobki) as global_avg FROM pracownicy
),
srednie_dzialow AS (
    -- Krok 2: Tabela ze średnimi per dział
    SELECT dzial, AVG(zarobki) as dept_avg 
    FROM pracownicy 
    GROUP BY dzial
)
-- Krok 3: Główne zapytanie łączące klocki
SELECT d.dzial, d.dept_avg
FROM srednie_dzialow d, srednia_ogolna g
WHERE d.dept_avg > g.global_avg;
```



### Lab11

```postgreSQL
CREATE OR REPLACE FUNCTION nazwa_funkcji(parametry)
RETURNS typ_zwracany AS $$
DECLARE
    -- deklaracja zmiennych lokalnych (opcjonalna)
BEGIN
    -- ciało funkcji: logika, obliczenia, zapytania SQL
    RETURN wartosc; -- wartość, którą funkcja zwraca
END;
$$ LANGUAGE plpgsql;
```


#### **Co to jest `RECORD`**?

`RECORD` to **specjalny typ zmiennej**, która:

> potrafi przechowywać **CAŁY WIERSZ z zapytania SQL**  
> (czyli wiele kolumn naraz, a nie jedną wartość)

Czyli to jest **„pojemnik na rekord z tabeli / selecta”**.

#### Po co RECORD jest potrzebny?

Gdy:

- nie chcesz wypisywać każdej kolumny osobno
    
- nie wiesz dokładnie, ile kolumn zwróci SELECT
    
- chcesz operować na „całych wierszach”


#### `RETURNS SETOF RECORD` 

W PostgreSQL to deklaracja używana przy tworzeniu funkcji. Mówi ona bazie danych dwie rzeczy:

1. **SETOF:** Funkcja zwróci **zbiór wierszy** (może to być 0, 1 lub wiele wierszy), a nie pojedynczą wartość.
    
2. **RECORD:** Funkcja **nie wie z góry**, jakie kolumny (nazwy i typy danych) zostaną zwrócone. Struktura wiersza jest "anonimowa" w momencie definicji funkcji.


#### `RETURN NEXT` 

To polecenie w języku **PL/pgSQL** (język proceduralny PostgreSQL), które służy do **budowania wyniku wiersz po wierszu**.

Najlepiej wyobrazić to sobie jako **koszyk na zakupy**.

### Jak to działa (Analogia)

1. **Zwykły `RETURN`**: To jak wejście do sklepu, wzięcie jednej rzeczy, zapłacenie i natychmiastowe wyjście. Funkcja kończy działanie w tym momencie.
    
2. **`RETURN NEXT`**: To chodzenie z koszykiem. Bierzesz produkt i wkładasz go do koszyka (`RETURN NEXT`), ale **nie wychodzisz ze sklepu**. Idziesz dalej, bierzesz kolejny produkt, wkładasz do koszyka (`RETURN NEXT`). Dopiero gdy skończysz (pętla się skończy), idziesz do kasy z całym koszykiem.


#### **`RETURN QUERY`** 

To polecenie w PL/pgSQL, które służy do **hurtowego** zwracania wyników zapytania SQL.

Zamiast budować wynik wiersz po wierszu (jak w `RETURN NEXT`), `RETURN QUERY` wykonuje standardowe zapytanie `SELECT` i **cały jego wynik** dołącza do zbioru, który zwróci funkcja.

### Jak to działa (Analogia)

Wracając do naszej analogii ze sklepem:

- **`RETURN NEXT`** to branie produktów z półki pojedynczo ręką: _jabłko... gruszka... śliwka..._
    
- **`RETURN QUERY`** to wzięcie całego kartonu z owocami i wrzucenie go do wózka jednym ruchem.
    

### Przykład

Załóżmy, że masz tabelę `uzytkownicy`. Chcesz zwrócić wszystkich aktywnych użytkowników.

SQL

```postgreSQL
CREATE OR REPLACE FUNCTION aktywni_uzytkownicy()
RETURNS SETOF uzytkownicy AS $$
BEGIN
    -- To jedno polecenie "wrzuca" do wyniku wszystkie pasujące wiersze naraz
    RETURN QUERY 
    SELECT * FROM uzytkownicy WHERE status = 'aktywny';

    -- Funkcja idzie dalej (możemy coś jeszcze dorzucić!)
    
    RETURN; -- Koniec funkcji
END;
$$ LANGUAGE plpgsql;
```
