# 📘 Notatka z komend Bash

> Kompletny przewodnik po najważniejszych komendach systemu Linux/Bash

---

## 📂 Nawigacja i zarządzanie katalogami

#### `cd`

To wbudowana komenda służąca do **zmiany bieżącego katalogu roboczego**.

```bash
cd lub cd ~        # wróć do katalogu domowego
cd ..              # wróć o jeden katalog wyżej
cd ../..           # wróć o dwa katalogi wyżej
cd folder          # wejdź do katalogu folder
cd /ścieżka/do/katalogu   # przejście do konkretnego katalogu
cd -               # wróć do poprzedniego katalogu
```

---

#### `pwd`

To wbudowana komenda, która wyświetla **pełną ścieżkę do bieżącego katalogu roboczego**.

```bash
pwd            # wyświetla ścieżkę do katalogu
pwd -P         # pokazuje rzeczywistą ścieżkę, rozwiązując linki symboliczne
pwd -L         # pokazuje ścieżkę uwzględniającą linki
ls $(pwd)      # przekazanie ścieżki do innej komendy
```

---

#### `ls`

Komenda `ls` w Bashu służy do **wyświetlania zawartości katalogu** (listowania plików i folderów).

```bash
ls -l           # wyświetla szczegółową listę (uprawnienia, właściciel, rozmiar, data modyfikacji)
ls -a           # pokazuje wszystkie pliki, w tym ukryte (zaczynające się od `.`)
ls -t           # sortuje pliki według czasu modyfikacji (najnowsze na górze)
ls -r           # odwrotna kolejność sortowania
ls -R           # rekurencyjnie pokazuje zawartość podkatalogów
ls -h           # wyświetla rozmiary w czytelnym formacie (KB, MB, GB)
ls -lah         # połączenie: szczegóły + ukryte + czytelne rozmiary
ls --color      # koloruje wyniki (katalogi, pliki wykonywalne itd.)
ls *.txt        # wyświetlanie tylko plików z określonym rozszerzeniem
ls -S           # sortuje według rozmiaru pliku (największe na górze)
ls -1           # wyświetla po jednym pliku na linię
```

---

#### `touch`

Komenda **`touch`** służy głównie do **tworzenia pustych plików** lub **aktualizacji znacznika czasu**.

```bash
touch <nazwa_pliku>                  # tworzenie pustego pliku
touch plik1.txt plik2.txt            # tworzenie wielu plików jednocześnie
touch -a                             # aktualizuje tylko czas ostatniego dostępu
touch -m                             # aktualizuje tylko czas modyfikacji
touch -c                             # nie tworzy pliku, jeśli nie istnieje
touch -t YYYYMMDDHHMM.SS             # ręczne ustawienie czasu (np. 202405201530.00)
touch -r plik_wzor.txt nowy_plik.txt # kopiuje znacznik czasu z innego pliku
```

---

#### `mkdir` i `rmdir`

`mkdir` – tworzy nowy katalog.

```bash
mkdir <nazwa_katalogu>    # tworzy nowy katalog
mkdir -p kat1/kat2/kat3   # tworzy całą ścieżkę katalogów (łącznie z nadrzędnymi)
mkdir -m 755 katalog      # ustawia uprawnienia podczas tworzenia
mkdir -v katalog          # wyświetla komunikat o utworzeniu katalogu
mkdir {styczeń,luty,marzec}  # tworzenie wielu katalogów jednocześnie
```

`rmdir` – usuwa **pusty** katalog.

```bash
rmdir <nazwa_katalogu>    # usuwa pusty katalog
rmdir -p kat1/kat2/kat3   # usuwa całą ścieżkę pustych katalogów
rmdir -v katalog          # wyświetla komunikat o usunięciu
```

`rm` – jeśli katalog nie jest pusty.

```bash
rm -r <nazwa_katalogu>    # usuwa rekurencyjnie (katalog i jego zawartość)
rm -rf <nazwa_katalogu>   # usuwa bez pytania (UWAGA: nieodwracalne!)
rm -ri <nazwa_katalogu>   # usuwa z potwierdzeniem każdego pliku
```

---

## 📄 Praca z plikami

#### `cp`

Komenda **`cp`** służy do **kopiowania plików i katalogów**.

```bash
cp plik.txt kopia.txt          # kopiuje plik do nowej nazwy
cp plik.txt /ścieżka/do/celu/  # kopiuje plik do innego katalogu
cp -r katalog/ kopia_katalogu/ # kopiuje katalog rekurencyjnie
cp -i plik.txt cel/            # pyta przed nadpisaniem istniejącego pliku
cp -u plik.txt cel/            # kopiuje tylko jeśli plik źródłowy jest nowszy
cp -v plik.txt cel/            # wyświetla co jest kopiowane (verbose)
cp -p plik.txt cel/            # zachowuje uprawnienia, właściciela i czas modyfikacji
cp *.txt /katalog/docelowy/    # kopiuje wszystkie pliki .txt
```

---

#### `mv`

Komenda **`mv`** służy do **przenoszenia lub zmiany nazwy** plików i katalogów.

```bash
mv plik.txt nowa_nazwa.txt         # zmiana nazwy pliku
mv plik.txt /ścieżka/do/katalogu/  # przeniesienie pliku
mv katalog/ /nowa/lokalizacja/     # przeniesienie katalogu
mv -i plik.txt cel/                # pyta przed nadpisaniem
mv -u plik.txt cel/                # przenosi tylko jeśli plik jest nowszy
mv -v plik.txt cel/                # wyświetla co jest przenoszone
mv *.txt /katalog/docelowy/        # przeniesienie wielu plików jednocześnie
```

---

#### `rm`

Komenda **`rm`** służy do **usuwania plików i katalogów**. Operacja jest nieodwracalna.

```bash
rm plik.txt               # usuwa plik
rm plik1.txt plik2.txt    # usuwa wiele plików
rm -i plik.txt            # pyta przed usunięciem
rm -f plik.txt            # usuwa bez pytania (force)
rm -r katalog/            # usuwa katalog rekurencyjnie
rm -rf katalog/           # usuwa katalog bez pytania (NIEBEZPIECZNE!)
rm *.log                  # usuwa wszystkie pliki .log
rm -v plik.txt            # wyświetla co zostało usunięte
```

> ⚠️ **UWAGA:** `rm -rf /` lub `rm -rf *` może usunąć cały system! Zawsze sprawdzaj co usuwasz.

---

#### `cat`

Komenda **`cat`** (concatenate) służy do **wyświetlania zawartości pliku**, łączenia plików lub tworzenia nowych.

```bash
cat plik.txt                     # wyświetla zawartość pliku
cat plik1.txt plik2.txt          # wyświetla oba pliki po kolei
cat plik1.txt plik2.txt > nowy.txt  # łączy pliki w jeden
cat -n plik.txt                  # wyświetla z numerami linii
cat -A plik.txt                  # pokazuje znaki specjalne (np. końce linii)
cat -b plik.txt                  # numeruje tylko niepuste linie
cat > nowy_plik.txt              # tworzy nowy plik (Ctrl+D aby zakończyć)
cat >> plik.txt                  # dopisuje do istniejącego pliku
```

---

#### `less` i `more`

Służą do **stronicowanego przeglądania** długich plików.

```bash
less plik.txt         # przeglądanie pliku (q = wyjście, / = szukaj, n = następne)
less +F plik.txt      # śledzenie pliku na żywo (jak tail -f)
more plik.txt         # starszy pager, przewijanie tylko w dół
less -N plik.txt      # wyświetla z numerami linii
less -S plik.txt      # nie zawija długich linii
```

---

#### `head` i `tail`

Wyświetlają **początekk lub koniec** pliku.

```bash
head plik.txt          # wyświetla pierwsze 10 linii
head -n 20 plik.txt    # wyświetla pierwsze 20 linii
head -c 100 plik.txt   # wyświetla pierwsze 100 bajtów

tail plik.txt          # wyświetla ostatnie 10 linii
tail -n 20 plik.txt    # wyświetla ostatnie 20 linii
tail -f plik.txt       # śledzi plik na żywo (np. logi – bardzo przydatne!)
tail -c 100 plik.txt   # wyświetla ostatnie 100 bajtów
```

---

#### `nano`, `vim`, `gedit`

Edytory tekstu w terminalu.

```bash
nano plik.txt     # prosty edytor (Ctrl+S = zapisz, Ctrl+X = wyjście)
vim plik.txt      # zaawansowany edytor (i = tryb edycji, :wq = zapisz i wyjdź, :q! = wyjdź bez zapisu)
gedit plik.txt    # graficzny edytor tekstowy (jeśli dostępne GUI)
```

---

## 🔍 Wyszukiwanie

#### `find`

Komenda **`find`** służy do **wyszukiwania plików i katalogów** w systemie plików.

```bash
find . -name "plik.txt"          # szuka pliku o podanej nazwie
find / -name "*.log"             # szuka wszystkich plików .log od korzenia
find . -type f                   # szuka tylko plików (nie katalogów)
find . -type d                   # szuka tylko katalogów
find . -size +10M                # szuka plików większych niż 10 MB
find . -size -1k                 # szuka plików mniejszych niż 1 KB
find . -mtime -7                 # pliki modyfikowane w ciągu ostatnich 7 dni
find . -name "*.txt" -delete     # znajduje i usuwa pliki .txt
find . -name "*.sh" -exec chmod +x {} \;  # wykonuje komendę na każdym znalezionym pliku
find . -empty                    # szuka pustych plików i katalogów
find . -perm 644                 # szuka plików o konkretnych uprawnieniach
find . -user jan                 # szuka plików należących do użytkownika jan
```

---

#### `grep`

Komenda **`grep`** służy do **przeszukiwania tekstu** w plikach (Global Regular Expression Print).

```bash
grep "szukany_tekst" plik.txt         # szuka tekstu w pliku
grep -i "tekst" plik.txt              # szuka bez rozróżniania wielkości liter
grep -r "tekst" /katalog/            # szuka rekurencyjnie w katalogu
grep -n "tekst" plik.txt              # wyświetla numery linii
grep -v "tekst" plik.txt              # wyświetla linie NIE zawierające tekstu
grep -c "tekst" plik.txt              # liczy liczbę pasujących linii
grep -l "tekst" *.txt                 # wyświetla tylko nazwy plików z dopasowaniem
grep -w "słowo" plik.txt              # szuka całego słowa (nie fragmentu)
grep -A 3 "tekst" plik.txt            # wyświetla 3 linie po dopasowaniu
grep -B 3 "tekst" plik.txt            # wyświetla 3 linie przed dopasowaniem
grep -E "wzorzec1|wzorzec2" plik.txt  # grep z wyrażeniami regularnymi (rozszerzonymi)
grep "^start" plik.txt                # linie zaczynające się od "start"
grep "koniec$" plik.txt               # linie kończące się na "koniec"
```

---

#### `locate`

Szybkie wyszukiwanie pliku na podstawie **bazy danych nazw plików**.

```bash
locate plik.txt          # szybkie wyszukiwanie pliku
locate -i PLIK.TXT       # bez rozróżniania wielkości liter
locate "*.pdf"           # wyszukuje wszystkich PDF-ów
sudo updatedb            # aktualizacja bazy danych (wymagane przed locate)
```

---

#### `which` i `whereis`

Służą do **lokalizowania programów**.

```bash
which python3        # pokazuje pełną ścieżkę do pliku wykonywalnego
which -a python3     # pokazuje wszystkie lokalizacje
whereis bash         # pokazuje lokalizację binarki, źródeł i manuala
```

---

## 📊 Informacje o systemie

#### `uname`

Wyświetla **informacje o systemie operacyjnym**.

```bash
uname -a     # wszystkie informacje o systemie
uname -s     # nazwa systemu operacyjnego
uname -r     # wersja jądra (kernel)
uname -m     # architektura systemu (np. x86_64)
uname -n     # nazwa hosta
```

---

#### `df`

Wyświetla **informacje o zajętości dysków** (disk free).

```bash
df             # wyświetla zajętość systemów plików
df -h          # w czytelnym formacie (KB, MB, GB)
df -H          # w formacie z potęgami 10 (1 KB = 1000 B)
df -T          # wyświetla typ systemu plików
df /home       # info tylko dla konkretnego katalogu
df -i          # wyświetla zajętość i-węzłów (inodes)
```

---

#### `du`

Wyświetla **rozmiar zajmowany przez pliki i katalogi** (disk usage).

```bash
du katalog/           # rozmiar katalogu
du -h katalog/        # w czytelnym formacie
du -s katalog/        # tylko łączny rozmiar (summary)
du -sh *              # rozmiary wszystkich elementów w bieżącym katalogu
du -ah katalog/       # rozmiar wszystkich plików i katalogów
du --max-depth=1 /    # rozmiar katalogów tylko jeden poziom w głąb
```

---

#### `top` i `htop`

Wyświetlają **aktywne procesy i zasoby systemowe** w czasie rzeczywistym.

```bash
top              # standardowy monitor procesów (q = wyjście)
htop             # ulepszona wersja top z kolorami i UI (może wymagać instalacji)
top -u jan       # procesy tylko dla użytkownika jan
top -p 1234      # monitoruje konkretny proces o PID 1234
```

---

#### `ps`

Wyświetla **migawkę aktywnych procesów** (process status).

```bash
ps               # procesy bieżącej sesji
ps aux           # wszystkie procesy systemu (szczegółowo)
ps -ef           # wszystkie procesy w formacie pełnym
ps aux | grep nginx   # szuka konkretnego procesu
ps -p 1234       # info o procesie o danym PID
ps --sort=-%cpu  # sortuje według użycia CPU
```

---

#### `free`

Wyświetla **informacje o pamięci RAM**.

```bash
free             # wyświetla pamięć w bajtach
free -h          # w czytelnym formacie (MB, GB)
free -m          # w megabajtach
free -s 2        # odświeża co 2 sekundy
```

---

#### `uptime`

Wyświetla **jak długo system jest uruchomiony** oraz obciążenie.

```bash
uptime           # czas pracy systemu i load average
uptime -p        # czytelny format (np. "up 2 hours, 30 minutes")
uptime -s        # data i godzina uruchomienia systemu
```

---

#### `hostname`

Wyświetla lub ustawia **nazwę hosta** systemu.

```bash
hostname              # wyświetla nazwę hosta
hostname -I           # wyświetla adresy IP
hostname -f           # wyświetla pełną kwalifikowaną nazwę domeny (FQDN)
sudo hostname nowa_nazwa   # tymczasowo zmienia nazwę hosta
```

---

## 👥 Użytkownicy i uprawnienia

#### `whoami` i `id`

```bash
whoami         # wyświetla bieżącą nazwę użytkownika
id             # wyświetla UID, GID i grupy użytkownika
id jan         # info o konkretnym użytkowniku
who            # pokazuje zalogowanych użytkowników
w              # pokazuje zalogowanych użytkowników i ich aktywność
```

---

#### `chmod`

Komenda **`chmod`** zmienia **uprawnienia dostępu** do pliku lub katalogu.

```bash
# Tryb symboliczny
chmod u+x plik.sh         # daje prawo wykonywania właścicielowi
chmod g-w plik.txt        # odbiera prawo zapisu grupie
chmod o+r plik.txt        # daje prawo odczytu innym
chmod a+x skrypt.sh       # daje prawo wykonywania wszystkim (u+g+o)
chmod u=rwx,g=rx,o= plik  # ustawia dokładne uprawnienia

# Tryb numeryczny (oktalny)
chmod 755 plik.sh         # rwxr-xr-x (właściciel pełne, reszta odczyt+wykonanie)
chmod 644 plik.txt        # rw-r--r-- (właściciel odczyt+zapis, reszta odczyt)
chmod 700 katalog/        # rwx------ (tylko właściciel ma dostęp)
chmod 600 klucz.pem       # rw------- (plik prywatny, np. klucz SSH)
chmod -R 755 katalog/     # zmienia uprawnienia rekurencyjnie
```

> **Tabela uprawnień:**
> | Liczba | Uprawnienia | Opis |
> |--------|-------------|------|
> | 7 | rwx | odczyt + zapis + wykonanie |
> | 6 | rw- | odczyt + zapis |
> | 5 | r-x | odczyt + wykonanie |
> | 4 | r-- | tylko odczyt |
> | 0 | --- | brak uprawnień |

---

#### `chown`

Komenda **`chown`** zmienia **właściciela** pliku lub katalogu.

```bash
chown jan plik.txt              # zmienia właściciela na jan
chown jan:staff plik.txt        # zmienia właściciela i grupę
chown :staff plik.txt           # zmienia tylko grupę
chown -R jan:staff katalog/     # zmienia rekurencyjnie
sudo chown root plik.txt        # zmienia właściciela na root
```

---

#### `sudo` i `su`

```bash
sudo komenda              # wykonuje komendę jako superużytkownik (root)
sudo -u jan komenda       # wykonuje komendę jako inny użytkownik
sudo -i                   # otwiera powłokę root
sudo !!                   # powtarza ostatnią komendę jako sudo
su                        # przełącza na konto root
su jan                    # przełącza na konto użytkownika jan
su - jan                  # przełącza i ładuje środowisko użytkownika
```

---

#### `passwd`

Zmiana **hasła użytkownika**.

```bash
passwd              # zmienia hasło bieżącego użytkownika
sudo passwd jan     # zmienia hasło użytkownika jan (jako root)
sudo passwd -l jan  # blokuje konto użytkownika
sudo passwd -u jan  # odblokowuje konto użytkownika
```

---

## 🌐 Sieć

#### `ping`

Sprawdza **połączenie sieciowe** z hostem.

```bash
ping google.com          # wysyła pakiety ICMP (Ctrl+C aby przerwać)
ping -c 4 google.com     # wysyła 4 pakiety i kończy
ping -i 2 google.com     # wysyła co 2 sekundy
ping -s 100 google.com   # wysyła pakiety o rozmiarze 100 bajtów
```

---

#### `curl`

Komenda **`curl`** służy do **pobierania lub wysyłania danych** przez protokoły sieciowe.

```bash
curl https://example.com                  # pobiera zawartość URL
curl -o plik.html https://example.com     # zapisuje do pliku
curl -O https://example.com/plik.zip      # pobiera zachowując oryginalną nazwę
curl -L https://example.com              # podąża za przekierowaniami
curl -I https://example.com              # pobiera tylko nagłówki HTTP
curl -X POST -d "dane" https://api.com   # wysyła żądanie POST z danymi
curl -H "Authorization: Bearer TOKEN" https://api.com  # dodaje nagłówek
curl -u login:hasło https://example.com  # uwierzytelnianie podstawowe
curl --progress-bar -O https://example.com/duzy_plik.zip  # pasek postępu
```

---

#### `wget`

Służy do **pobierania plików** z sieci.

```bash
wget https://example.com/plik.zip           # pobiera plik
wget -O nazwa.zip https://example.com/plik  # pobiera z własną nazwą
wget -c https://example.com/plik.zip        # wznawia przerwanego pobieranie
wget -q https://example.com/plik.zip        # tryb cichy (quiet)
wget --limit-rate=500k https://...          # ogranicza prędkość pobierania
wget -r https://example.com/               # pobiera stronę rekurencyjnie
```

---

#### `ssh`

Komenda **`ssh`** umożliwia **zdalne logowanie** do serwera.

```bash
ssh user@host                          # łączy z hostem jako user
ssh -p 2222 user@host                  # łączy przez niestandardowy port
ssh -i klucz.pem user@host            # łączy używając klucza prywatnego
ssh -L 8080:localhost:80 user@host    # tunelowanie portów (local forwarding)
ssh-keygen -t rsa -b 4096             # generuje parę kluczy SSH
ssh-copy-id user@host                 # kopiuje klucz publiczny na serwer
```

---

#### `scp`

Komenda **`scp`** służy do **bezpiecznego kopiowania plików** przez SSH.

```bash
scp plik.txt user@host:/ścieżka/       # kopiuje plik na serwer
scp user@host:/ścieżka/plik.txt ./    # kopiuje plik z serwera
scp -r katalog/ user@host:/ścieżka/   # kopiuje katalog rekurencyjnie
scp -P 2222 plik.txt user@host:/ścieżka/  # przez niestandardowy port
```

---

#### `netstat` i `ss`

Wyświetlają **informacje o połączeniach sieciowych**.

```bash
netstat -tuln        # pokazuje nasłuchujące porty TCP i UDP
netstat -an          # wszystkie połączenia
ss -tuln             # nowszy zamiennik netstat, szybszy
ss -s                # podsumowanie połączeń
```

---

#### `ifconfig` i `ip`

```bash
ifconfig                  # wyświetla konfigurację interfejsów sieciowych
ip addr                   # nowszy zamiennik ifconfig
ip addr show eth0         # info o konkretnym interfejsie
ip route                  # wyświetla tablicę routingu
ip link set eth0 up/down  # włącza/wyłącza interfejs sieciowy
```

---

## 📦 Zarządzanie pakietami

#### `apt` (Debian/Ubuntu)

```bash
sudo apt update              # aktualizuje listę pakietów
sudo apt upgrade             # aktualizuje wszystkie zainstalowane pakiety
sudo apt install nazwa       # instaluje pakiet
sudo apt remove nazwa        # usuwa pakiet
sudo apt purge nazwa         # usuwa pakiet wraz z konfiguracją
sudo apt autoremove          # usuwa zbędne zależności
sudo apt search słowo        # szuka pakietu
apt list --installed         # lista zainstalowanych pakietów
sudo apt-cache show nazwa    # szczegóły pakietu
```

---

#### `yum` / `dnf` (RHEL/CentOS/Fedora)

```bash
sudo yum install nazwa        # instaluje pakiet
sudo dnf install nazwa        # nowszy zamiennik yum
sudo yum update               # aktualizuje wszystko
sudo yum remove nazwa         # usuwa pakiet
sudo yum search słowo         # szuka pakietu
```

---

## 🔧 Procesy i zadania

#### `kill` i `killall`

```bash
kill 1234                  # wysyła sygnał TERM do procesu o PID 1234
kill -9 1234               # wymuś zakończenie procesu (SIGKILL)
kill -l                    # lista wszystkich sygnałów
killall nazwa_procesu      # kończy wszystkie procesy o danej nazwie
killall -9 firefox         # wymuś zamknięcie wszystkich procesów firefox
pkill -u jan               # kończy wszystkie procesy użytkownika jan
```

---

#### `jobs`, `bg`, `fg`

Zarządzanie **zadaniami w tle**.

```bash
komenda &           # uruchamia komendę w tle
Ctrl+Z              # wstrzymuje bieżące zadanie
jobs                # lista zadań w tle
bg %1               # wznawia zadanie nr 1 w tle
fg %1               # przenosi zadanie nr 1 na pierwszy plan
disown %1           # odłącza zadanie od terminala
```

---

#### `nohup`

Pozwala na **uruchomienie procesu** niezależnego od terminala.

```bash
nohup komenda &               # uruchamia komendę niezależnie od sesji
nohup skrypt.sh > log.txt &   # z przekierowaniem wyjścia do pliku
```

---

#### `cron` i `crontab`

Harmonogram **automatycznego wykonywania** komend.

```bash
crontab -e         # edytuje crontab bieżącego użytkownika
crontab -l         # wyświetla crontab
crontab -r         # usuwa crontab
sudo crontab -e    # crontab dla root

# Format: minuta godzina dzień_miesiąca miesiąc dzień_tygodnia komenda
# Przykłady:
# 0 2 * * * /skrypty/backup.sh       # codziennie o 2:00 w nocy
# */15 * * * * /skrypty/check.sh     # co 15 minut
# 0 9 * * 1 /skrypty/raport.sh      # w każdy poniedziałek o 9:00
```

---

## 📝 Przetwarzanie tekstu

#### `echo`

Wyświetla **tekst lub zmienne** w terminalu.

```bash
echo "Witaj, świecie!"        # wyświetla tekst
echo $HOME                    # wyświetla wartość zmiennej
echo -n "Tekst"               # bez nowego wiersza na końcu
echo -e "Linia1\nLinia2"      # interpretuje sekwencje ucieczki (\n = nowa linia)
echo "tekst" >> plik.txt      # dopisuje do pliku
echo "tekst" > plik.txt       # nadpisuje plik
```

---

#### `sort`

Komenda **`sort`** służy do **sortowania zawartości** pliku lub strumienia.

```bash
sort plik.txt             # sortuje alfabetycznie
sort -r plik.txt          # sortuje odwrotnie
sort -n plik.txt          # sortuje numerycznie
sort -k 2 plik.txt        # sortuje według drugiej kolumny
sort -u plik.txt          # sortuje i usuwa duplikaty
sort -t: -k3 -n /etc/passwd  # sortuje według 3. pola z separatorem ":"
```

---

#### `uniq`

Usuwa lub wyświetla **powtarzające się linie** (działa na posortowanych danych).

```bash
uniq plik.txt          # usuwa duplikaty sąsiadujących linii
uniq -c plik.txt       # liczy wystąpienia każdej linii
uniq -d plik.txt       # pokazuje tylko duplikaty
uniq -u plik.txt       # pokazuje tylko unikalne linie
sort plik.txt | uniq   # pełne usunięcie duplikatów
```

---

#### `wc`

Komenda **`wc`** (word count) liczy **linie, słowa i znaki**.

```bash
wc plik.txt        # linie, słowa, bajty
wc -l plik.txt     # tylko liczba linii
wc -w plik.txt     # tylko liczba słów
wc -c plik.txt     # tylko liczba bajtów
wc -m plik.txt     # liczba znaków (z uwzględnieniem multibyte)
ls | wc -l         # liczy pliki w katalogu
```

---

#### `cut`

Wycina **kolumny lub pola** z tekstu.

```bash
cut -d: -f1 /etc/passwd        # pobiera 1. pole z separatorem ":"
cut -c1-10 plik.txt            # pobiera znaki od 1 do 10
cut -d, -f2,4 dane.csv         # pobiera pola 2 i 4 z pliku CSV
cut -d" " -f1 plik.txt         # pobiera pierwsze słowo z każdej linii
```

---

#### `awk`

**`awk`** to zaawansowane narzędzie do **przetwarzania tekstu** i wyodrębniania danych.

```bash
awk '{print $1}' plik.txt          # wyświetla pierwsze pole (domyślnie spacja)
awk -F: '{print $1}' /etc/passwd   # wyświetla 1. pole z separatorem ":"
awk '{print NR": "$0}' plik.txt    # dodaje numery linii
awk '$3 > 100' plik.txt            # wyświetla linie gdzie 3. pole > 100
awk '{sum += $1} END {print sum}' plik.txt  # sumuje wartości z 1. kolumny
awk '{print $NF}' plik.txt         # wyświetla ostatnie pole w linii
```

---

#### `sed`

Komenda **`sed`** (stream editor) służy do **transformacji tekstu**.

```bash
sed 's/stare/nowe/' plik.txt           # zamienia pierwsze wystąpienie w linii
sed 's/stare/nowe/g' plik.txt          # zamienia wszystkie wystąpienia (global)
sed -i 's/stare/nowe/g' plik.txt       # modyfikuje plik w miejscu
sed -n '5,10p' plik.txt                # wyświetla linie 5-10
sed '/wzorzec/d' plik.txt             # usuwa linie pasujące do wzorca
sed 's/  */ /g' plik.txt              # zamienia wielokrotne spacje na jedną
sed -i 's/^/# /' plik.txt             # dodaje "# " na początku każdej linii
```

---

## 📁 Archiwizacja i kompresja

#### `tar`

Komenda **`tar`** służy do **tworzenia i rozpakowywania archiwów**.

```bash
# Tworzenie archiwum
tar -cvf archiwum.tar katalog/         # tworzy archiwum .tar
tar -czvf archiwum.tar.gz katalog/    # tworzy archiwum .tar.gz (skompresowane gzip)
tar -cjvf archiwum.tar.bz2 katalog/  # tworzy archiwum .tar.bz2 (skompresowane bzip2)

# Rozpakowywanie
tar -xvf archiwum.tar                  # rozpakowuje .tar
tar -xzvf archiwum.tar.gz             # rozpakowuje .tar.gz
tar -xjvf archiwum.tar.bz2           # rozpakowuje .tar.bz2
tar -xvf archiwum.tar -C /katalog/   # rozpakowuje do wskazanego katalogu

# Podgląd zawartości
tar -tvf archiwum.tar                  # wyświetla zawartość bez rozpakowywania

# Przełączniki:
# -c = create (utwórz), -x = extract (rozpakuj)
# -v = verbose (szczegóły), -f = file (nazwa pliku)
# -z = gzip, -j = bzip2
```

---

#### `zip` i `unzip`

```bash
zip archiwum.zip plik1.txt plik2.txt   # pakuje pliki do .zip
zip -r archiwum.zip katalog/           # pakuje katalog rekurencyjnie
zip -e archiwum.zip plik.txt          # tworzy zaszyfrowane archiwum
unzip archiwum.zip                     # rozpakowuje .zip
unzip archiwum.zip -d /katalog/       # rozpakowuje do wskazanego katalogu
unzip -l archiwum.zip                  # lista zawartości bez rozpakowywania
```

---

#### `gzip` i `gunzip`

```bash
gzip plik.txt          # kompresuje plik (tworzy plik.txt.gz, usuwa oryginał)
gzip -k plik.txt       # kompresuje zachowując oryginał
gzip -d plik.txt.gz    # dekompresuje (odpowiednik gunzip)
gunzip plik.txt.gz     # dekompresuje
gzip -9 plik.txt       # maksymalny poziom kompresji
```

---

## 🔗 Przekierowania i potoki

#### Przekierowanie wyjścia

```bash
komenda > plik.txt       # przekierowanie stdout do pliku (nadpisuje)
komenda >> plik.txt      # przekierowanie stdout (dopisuje)
komenda 2> error.txt     # przekierowanie stderr do pliku
komenda 2>&1             # przekierowanie stderr do stdout
komenda &> plik.txt      # przekierowanie zarówno stdout jak i stderr
komenda > /dev/null      # wyrzuca wyjście (cisza)
komenda > /dev/null 2>&1 # wyrzuca całe wyjście (stdout i stderr)
```

---

#### Potok `|`

```bash
komenda1 | komenda2          # wyjście komendy1 jako wejście komendy2
ls -la | grep ".txt"         # filtruje wyniki ls
ps aux | grep nginx          # szuka procesu nginx
cat plik.txt | wc -l         # liczy linie w pliku
cat plik.txt | sort | uniq   # sortuje i usuwa duplikaty
history | tail -20           # ostatnie 20 komend historii
```

---

#### `tee`

Zapisuje dane **jednocześnie do pliku i stdout**.

```bash
komenda | tee plik.txt           # wyświetla i zapisuje do pliku
komenda | tee -a plik.txt        # wyświetla i dopisuje do pliku
komenda | tee plik1.txt plik2.txt  # zapisuje do wielu plików jednocześnie
```

---

## 📜 Historia i skróty

#### `history`

```bash
history                  # wyświetla historię komend
history 20               # ostatnie 20 komend
history -c               # czyści historię
!42                      # wykonuje komendę nr 42 z historii
!!                       # powtarza ostatnią komendę
!wget                    # powtarza ostatnią komendę zaczynającą się od "wget"
Ctrl+R                   # interaktywne przeszukiwanie historii
```

---

#### `alias`

Tworzy **skróty** do komend.

```bash
alias ll='ls -lah'                  # tworzy skrót ll
alias update='sudo apt update && sudo apt upgrade'  # złożony alias
alias rm='rm -i'                    # zawsze pyta przed usunięciem
alias ..='cd ..'                    # skrót nawigacji
unalias ll                          # usuwa alias
alias                               # wyświetla wszystkie aliasy
```

> 💡 Aby aliasy były trwałe, dodaj je do pliku `~/.bashrc` lub `~/.bash_aliases`.

---

## 📋 Zmienne i środowisko

#### `export` i zmienne środowiskowe

```bash
ZMIENNA="wartość"             # tworzy zmienną lokalną
export ZMIENNA="wartość"      # tworzy zmienną środowiskową
echo $ZMIENNA                 # wyświetla wartość zmiennej
printenv                      # wyświetla wszystkie zmienne środowiskowe
printenv PATH                 # wyświetla konkretną zmienną
env                           # wyświetla środowisko lub uruchamia komendę ze zmodyfikowanym środowiskiem
unset ZMIENNA                 # usuwa zmienną

# Ważne zmienne systemowe:
echo $HOME       # katalog domowy użytkownika
echo $PATH       # ścieżki wyszukiwania programów
echo $USER       # nazwa bieżącego użytkownika
echo $SHELL      # bieżąca powłoka
echo $PWD        # bieżący katalog (jak pwd)
echo $LANG       # język systemu
```

---

## 🔐 Linki symboliczne i twarde

#### `ln`

Komenda **`ln`** tworzy **dowiązania** (linki) do plików.

```bash
ln plik.txt twardy_link.txt        # tworzy twardy link
ln -s plik.txt link_symboliczny    # tworzy link symboliczny (symlink)
ln -s /ścieżka/do/katalogu link/   # symlink do katalogu
ls -la                             # wyświetla linki symboliczne (-> wskazanie)
readlink link_symboliczny          # pokazuje cel linku symbolicznego
```

---

## 🧩 Przydatne komendy różne

#### `date`

```bash
date                    # bieżąca data i godzina
date +"%Y-%m-%d"        # format: 2024-05-20
date +"%H:%M:%S"        # tylko godzina
date -d "tomorrow"      # jutrzejsza data
date -d "2 days ago"    # 2 dni temu
```

---

#### `cal`

```bash
cal               # wyświetla kalendarz bieżącego miesiąca
cal 2024          # kalendarz całego roku
cal 5 2024        # kalendarz maja 2024
```

---

#### `diff`

Porównuje **zawartość dwóch plików**.

```bash
diff plik1.txt plik2.txt           # porównuje pliki
diff -u plik1.txt plik2.txt        # format unified (czytelniejszy)
diff -i plik1.txt plik2.txt        # ignoruje wielkość liter
diff -r katalog1/ katalog2/        # porównuje katalogi rekurencyjnie
diff --color plik1.txt plik2.txt   # koloruje różnice
```

---

#### `xargs`

Przekazuje **wyjście jako argumenty** do innej komendy.

```bash
find . -name "*.txt" | xargs rm          # usuwa wszystkie znalezione pliki .txt
echo "plik1.txt plik2.txt" | xargs cat  # wyświetla zawartość podanych plików
cat lista.txt | xargs mkdir             # tworzy katalogi z listy
find . -name "*.log" | xargs grep "ERROR"  # szuka w wielu plikach
```

---

#### `tput` i `clear`

```bash
clear          # czyści ekran terminala
tput clear     # alternatywa dla clear
reset          # resetuje terminal (przydatne po wyjściu ze złych programów)
Ctrl+L         # skrót klawiszowy do czyszczenia ekranu
```

---

#### `man`

Wyświetla **podręcznik** (manual) komendy.

```bash
man ls              # manual komendy ls
man -k słowo        # szuka w manualach po słowie kluczowym
man 5 crontab       # konkretna sekcja manuala
info ls             # alternatywny system dokumentacji
ls --help           # krótka pomoc inline
```

---

#### `type`

Określa **typ komendy** (alias, wbudowana, plik wykonywalny).

```bash
type ls       # pokazuje czy ls jest wbudowaną, aliasem czy programem
type -a ls    # wszystkie lokalizacje komendy ls
```

---

## 💡 Skróty klawiszowe w Bash

| Skrót | Opis |
|-------|------|
| `Ctrl+C` | Przerywa bieżący proces |
| `Ctrl+Z` | Wstrzymuje bieżący proces |
| `Ctrl+D` | Kończy sesję / wysyła EOF |
| `Ctrl+L` | Czyści ekran (jak `clear`) |
| `Ctrl+R` | Przeszukuje historię komend |
| `Ctrl+A` | Przeskakuje na początek linii |
| `Ctrl+E` | Przeskakuje na koniec linii |
| `Ctrl+U` | Usuwa tekst od kursora do początku linii |
| `Ctrl+K` | Usuwa tekst od kursora do końca linii |
| `Ctrl+W` | Usuwa słowo przed kursorem |
| `Tab` | Autouzupełnianie komend i ścieżek |
| `Tab Tab` | Wyświetla wszystkie możliwe uzupełnienia |
| `↑ / ↓` | Nawigacja w historii komend |
| `!!` | Powtarza poprzednią komendę |
| `!$` | Ostatni argument poprzedniej komendy |

---

*Notatka wygenerowana jako szybki przewodnik po komendach Bash. Użyj `man <komenda>` aby uzyskać pełną dokumentację.*