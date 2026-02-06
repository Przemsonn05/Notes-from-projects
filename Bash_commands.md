
#### `cd`

 To wbudowana komenda służąca do **zmiany bieżącego katalogu roboczego**

```bash
cd lub cd~  #wróć do katalogu domowego
cd ..   #Wróć o jeden katalog wyżej
cd folder   #wejdź do katalogu folder
cd /ścieżka/do/katalogu   #przejście do konkretnego katalogu
```



#### `pwd`

To wbudowana komenda, która wyświetla **pełną ścieżkę do bieżącego katalogu roboczego**.

```bash
pwd   #wyświetla ściezkę do katalogu
pwd -P   #pokazuje rzeczywistą ścieżkę, rozwiązując linki symboliczne
pwd -L   #pokazuje ścieżkę uwzględniającą linki
ls $(pwd)   #przekazanie ścieżki do innej komendy
```



#### `ls`

Komenda `ls` w Bashu służy do **wyświetlania zawartości katalogu** (listowania plików i folderów). Jest jedną z najczęściej używanych komend w terminalu.

```bash
ls -l   #Wyświetla szczegółową listę (uprawnienia, właściciel, rozmiar, data modyfikacji)
ls -a   #Pokazuje wszystkie pliki, w tym ukryte (zaczynające się od `.`)
ls -t   #Sortuje pliki według czasu modyfikacji (najnowsze na górze)
ls -r   #Odwrotna kolejność sortowania
ls -R   #Rekurencyjnie pokazuje zawartość podkatalogów
ls --color   #Koloruje wyniki (katalogi, pliki wykonywalne itd.)
ls *.txt   #wyświetlanie tylko plików z określonym rozszerzeniem
```



#### `touch`

Komenda **`touch`** w Bashu służy głównie do **tworzenia pustych plików** lub **aktualizacji znacznika czasu** (daty modyfikacji/dostępu) istniejących plików.

```bash
touch <nazwa_pliku>
touch -a   #aktualizuje tylko czas ostatniego dostępu
touch -m   #aktualizuje tylko czas modyfikacji
touch -c   #Nie tworzy pliku, jeśli nie istnieje, unika przypadkowego tworzenia
touch -t YYYYMMDDHHMM.SS   #ręczne ustawienie czasu (np. `-t 202405201530.00` dla 20 maja 2024, 15:30:00)
```



#### `mkdir` i `rmdir`

`mkdir` - tworzy nowy katalog
```bash
mkdir <nazwa_katalogu>
mkdir -p   #tworzy całą ścieżkę katalogów
mkdir -m   #ustawia uprawnienia podczas tworzenia
mkdir -v   #wyświetla komunikat o utworzeniu katalogu
```

`rmdir` - usuwa pusty katalog
```bash
rmdir <nazwa_katalogu>
rmdir -p   #usuwa całą ścieżkę katalogów
rmdir -v   #wyświetla komunikat o usunięciu
```

`rm` - jeśli katalog nie jest pusty
```bash
rm -r <nazwa_katalogu>   #usuwa rekurencyjnie
```
