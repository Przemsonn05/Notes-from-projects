# Notatka: Podstawy pracy z Cisco IOS - Laboratorium 000

## 1. Przygotowanie stanowiska pracy

### Połączenie fizyczne
- **Sprzęt**: 1 przełącznik Cisco + 1 komputer PC
- **Okablowanie**: niebieski kabel konsoli
- **Podłączenie**: wtyk konsoli → gniazdo CONSOLE przełącznika
  - Uwaga: czasem gniazdo jest z tyłu, wtedy używamy przedłużacza zakończonego czarnym łącznikiem

### Konfiguracja oprogramowania Putty
1. Uruchomić program Putty na PC
2. Wybrać tryb połączenia: **Serial**
3. Kliknąć **Open** → pojawi się puste okno CLI (Command Line Interface)

### Aktywacja CLI
- Wcisnąć kilka razy klawisz **ENTER**
- Konsola RS-232 nie kontroluje sprzętowo sesji (można przełączać bez zamykania okna)

---

## 2. Pierwsze uruchomienie - kreator konfiguracji

### Komunikat startowy
```
Would you like to enter the initial configuration dialog? [yes/no]:
```

### Akcja
- Odpowiedzieć **no** lub wcisnąć **CTRL-C** (zakończenie kreatora)

### Rezultat - pojawienie się karetki
```
Switch>
```
- `Switch` = hostname przełącznika (można zmienić)
- `>` = poziom uprawnień 1 (user level)

---

## 3. Poziomy uprawnień w Cisco IOS

### System uprawnień
- **16 poziomów**: 0...15
- Poziom 1 (user) → podstawowe komendy
- Poziom 15 (privileged) → pełne uprawnienia

### Przejście do trybu privileged
```
Switch>enable
```

### Nowa karetka
```
Switch#
```
- Tryb **exec CLI** - diagnostyka i zarządzanie (bez konfiguracji sieciowej)
- Odpowiednik komendy `su` w systemach Unix/Linux

---

## 4. Podstawowe komendy diagnostyczne

### Lista dostępnych komend
```
Switch# ?
```
- Rozwój listy: wielokrotne wciskanie **spacji**
- Przegląd przykładowych komend (np. `dir`)

### Komenda "show" - diagnostyka stanu urządzenia

#### Parametry pierwszego poziomu
```
Switch#show ?
```

#### Przykłady użycia
```
Switch#show ip ?
Switch#show vlan
```

---

## 5. Interfejsy w Cisco IOS

### Definicja interfejsu
**Interfejs** = encja wprowadzająca/wyprowadzająca dane z urządzenia

### Rodzaje interfejsów
- **Fizyczne**: odpowiadają gniazdom w obudowie (np. GigabitEthernet)
- **Logiczne**: ustanowione programowo (np. Tunnel, Dialer, Loopback, BVI)

### Sprawdzenie typów interfejsów IP
```
Switch#show ip interface ?
```

### Raport o interfejsach - najważniejsza komenda
```
Switch#show ip interface brief
```

#### Przykładowy wynik
```
Interface          IP-Address  OK? Method Status                Protocol
GigabitEthernet0/1 unassigned  YES manual down                  down
GigabitEthernet0/2 unassigned  YES manual down                  down
GigabitEthernet0/3 unassigned  YES manual down                  down
```

### Interpretacja statusów

#### Kolumna "Status" (stan fizyczny medium)
- **up** = sprawne, aktywne, włączone
- **down** = wyłączone, niesprawne, usunięte
- **administratively down** = wyłączone w konfiguracji przez administratora

#### Kolumna "Protocol" (stan protokołu)
- Kompatybilność ustawień z urządzeniem komunikującym się przez medium
- Gotowość do nawiązania komunikacji

### Test praktyczny
1. Podłączyć kabel Ethernet z PC do dowolnego portu przełącznika
2. Obserwować komunikaty na ekranie
3. Ponownie wykonać: `Switch#show ip interface brief`
4. Sprawdzić zmianę statusów portów

---

## 6. Skróty komend w Cisco IOS

### Zasada unikatowych przedrostków
- Nie trzeba wpisywać pełnych słów kluczowych
- Wystarczy unikatowy przedrostek

### Przykłady
```
show → sh
show ip interface brief → sh ip int br
```

### Auto-uzupełnianie - klawisz TAB
```
Switch#sho<TAB>
```
- Działa tylko gdy wcześniejsze człony są poprawne
- Pomaga w nauce komend i weryfikacji poprawności

---

## 7. Tryb konfiguracji

### Separacja funkcjonalności
- **Tryb exec** → diagnostyka i utrzymanie
- **Tryb config** → konfiguracja zachowania w sieci
- Powód: **bezpieczeństwo**

### Wejście do trybu konfiguracji
```
Switch#configure terminal
Switch(config)#
```

### Zmiana karetki
- `Switch#` → tryb exec
- `Switch(config)#` → tryb konfiguracji

### Lista zagadnień konfiguracyjnych
```
Switch(config)# ?
```
(rozwinąć spacją)

---

## 8. Konfiguracja interfejsu

### Przejście do konfiguracji konkretnego interfejsu
```
Switch(config)#interface GigabitEthernet0/5
Switch(config-if)#
```

### Zmiana karetki ponownie
- Nowa pula komend dostępna dla tego interfejsu

### Lista komend dla interfejsu
```
Switch(config-if)# ?
```

### Przykład: wyłączenie interfejsu
```
Switch(config-if)#shutdown
```
- Obserwować: sygnalizatory LED, komunikaty systemowe
- Zweryfikować: `show ip interface brief`

---

## 9. Wydawanie komend "show" z trybu konfiguracji

### Problem
- Komendy `show` są dostępne w trybie exec
- Z trybu config trzeba by wyjść, sprawdzić i wrócić

### Skomplikowana procedura
```
Switch(config-if)#exit
Switch(config)#exit
Switch#show ip interface brief
Switch#configure terminal
Switch(config)#interface GigabitEthernet0/5
Switch(config-if)#
```

### Rozwiązanie - przedrostek "do"
```
Switch(config-if)#do show ip interface brief
```
- Wykonanie komendy exec z poziomu trybu konfiguracji

---

## 10. Pliki konfiguracji

### Główny plik konfiguracji
- Wszystkie zmiany w **jednym spójnym zasobie**
- Format = składnia komend (identyczna!)
- Można używać konfiguracji jako komend (wklejanie) i odwrotnie

### Wyświetlenie aktywnej konfiguracji
```
Switch#show running-config
```

#### Przykładowy wynik (konfiguracja fabryczna)
```
Building configuration...
Current configuration : 971 bytes
!
version 14.1
ip cef
!
hostname Switch
!
spanning-tree mode pvst
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
(...)
interface Vlan1
 no ip address
 shutdown
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
end
```

### Znak "!" = komentarz (instrukcja pusta)

### Usuwanie wpisów - przedrostek "no"
```
Switch(config)#interface GigabitEthernet0/5
Switch(config-if)#no shutdown
```

---

## 11. Konfiguracja startowa (startup-config)

### Drugi plik konfiguracji
- **startup-config** = zapisana w pamięci trwałej
- Używana przy uruchamianiu przełącznika

### Zapisanie konfiguracji
```
Switch#copy running-config startup-config
```
- Obecna konfiguracja zostanie użyta przy następnym starcie

### Przywrócenie konfiguracji fabrycznej
```
Switch#write erase
```
- Kasowanie pamięci trwałej
- Konfiguracja fabryczna przy następnym uruchomieniu

---

## 12. Ergonomia CLI - skróty klawiszowe

### Nawigacja w linii
- **CTRL-A** → przeniesienie kursora na początek linii
- **CTRL-E** → przeniesienie kursora na koniec linii
- **CTRL-R** → odświeżenie linii (zniszczonej przez komunikaty systemowe)

### Historia komend
- **Strzałki w górę/dół** → przeglądanie historii komend

---

## 13. Ergonomia programu Putty

### Kopiowanie i wklejanie
- **Prawy przycisk myszy** → wklejenie:
  - Tekstu zaznaczonego w terminalu (podświetlony na zielono)
  - Zawartości schowka systemowego (jeśli nic nie zaznaczono)

### Zapisanie całej sesji
1. Menu kontekstowe Putty (ikona w lewym górnym rogu)
2. Opcja: **"Copy all to clipboard"**
3. Wkleić do pliku i zapisać
4. **Zastosowanie**: zabieranie historii eksperymentów po zajęciach

---

## Podsumowanie wszystkich komend

### Komendy podstawowe (tryb user level - Switch>)

| Komenda | Opis |
|---------|------|
| `enable` | Przejście z poziomu user (1) do privileged (15) |
| `ENTER` (kilka razy) | Aktywacja procesu obsługującego CLI |

---

### Komendy trybu privileged (Switch#)

#### Nawigacja i pomoc
| Komenda | Opis |
|---------|------|
| `?` | Wyświetlenie listy dostępnych komend |
| `<komenda> ?` | Wyświetlenie parametrów dla danej komendy |
| `<TAB>` | Auto-uzupełnianie komendy |

#### Komendy diagnostyczne "show"
| Komenda | Opis |
|---------|------|
| `show ?` | Lista wszystkich parametrów komendy show |
| `show ip ?` | Lista parametrów związanych z IP |
| `show ip interface ?` | Lista typów interfejsów IP |
| `show ip interface brief` | Raport o wszystkich interfejsach (najczęściej używana) |
| `show vlan` | Wyświetlenie informacji o VLAN-ach |
| `show running-config` | Wyświetlenie aktywnej konfiguracji (w pamięci RAM) |
| `show startup-config` | Wyświetlenie konfiguracji startowej (w pamięci trwałej) |

#### Komendy zarządzania konfiguracją
| Komenda | Opis |
|---------|------|
| `configure terminal` | Wejście do trybu konfiguracji globalnej |
| `copy running-config startup-config` | Zapisanie aktywnej konfiguracji do pamięci trwałej |
| `copy startup-config running-config` | Wczytanie konfiguracji startowej jako aktywnej |
| `write erase` | Usunięcie konfiguracji startowej (przywrócenie fabrycznej) |
| `reload` | Restart urządzenia |

#### Inne komendy trybu exec
| Komenda | Opis |
|---------|------|
| `dir` | Wyświetlenie zawartości systemu plików |
| `exit` | Wylogowanie z poziomu privileged (powrót do user level) |

---

### Komendy trybu konfiguracji globalnej (Switch(config)#)

#### Nawigacja w trybie konfiguracji
| Komenda | Opis |
|---------|------|
| `?` | Lista dostępnych opcji konfiguracyjnych |
| `exit` | Wyjście z trybu konfiguracji (powrót do trybu exec) |
| `CTRL-Z` | Szybkie wyjście z trybu konfiguracji (powrót do trybu exec) |
| `end` | Wyjście z trybu konfiguracji (powrót do trybu exec) |

#### Konfiguracja interfejsów
| Komenda | Opis |
|---------|------|
| `interface GigabitEthernet0/X` | Wejście do trybu konfiguracji interfejsu X |
| `interface range GigabitEthernet0/1-24` | Konfiguracja zakresu interfejsów jednocześnie |

#### Konfiguracja hostname
| Komenda | Opis |
|---------|------|
| `hostname <nazwa>` | Ustawienie nazwy urządzenia |
| `no hostname` | Przywrócenie domyślnej nazwy (Switch) |

#### Wykonywanie komend exec z trybu config
| Komenda | Opis |
|---------|------|
| `do <komenda_exec>` | Wykonanie komendy trybu exec bez wychodzenia z trybu config |
| `do show ip interface brief` | Przykład: sprawdzenie interfejsów z trybu konfiguracji |
| `do show running-config` | Przykład: podgląd konfiguracji z trybu config |

---

### Komendy trybu konfiguracji interfejsu (Switch(config-if)#)

#### Nawigacja
| Komenda | Opis |
|---------|------|
| `?` | Lista dostępnych komend dla interfejsu |
| `exit` | Wyjście do trybu konfiguracji globalnej |

#### Zarządzanie stanem interfejsu
| Komenda | Opis |
|---------|------|
| `shutdown` | Wyłączenie interfejsu (administratively down) |
| `no shutdown` | Włączenie interfejsu |

#### Inne komendy interfejsu
| Komenda | Opis |
|---------|------|
| `description <opis>` | Dodanie opisu interfejsu |
| `no description` | Usunięcie opisu interfejsu |

---

### Skróty klawiszowe CLI

| Skrót | Opis |
|-------|------|
| `CTRL-A` | Przeniesienie kursora na początek linii |
| `CTRL-E` | Przeniesienie kursora na koniec linii |
| `CTRL-R` | Odświeżenie linii wpisywanego tekstu |
| `CTRL-C` | Przerwanie bieżącej operacji (np. zamknięcie kreatora) |
| `CTRL-Z` | Wyjście z trybu konfiguracji do trybu exec |
| `Strzałka w górę` | Poprzednia komenda z historii |
| `Strzałka w dół` | Następna komenda z historii |
| `TAB` | Auto-uzupełnianie komendy |
| `Spacja` | Rozwinięcie pełnej listy (przy wyświetlaniu pomocy) |

---

### Skróty i aliasy komend (przykłady)

| Pełna komenda | Skrót | Opis |
|---------------|-------|------|
| `show` | `sh` | Diagnostyka |
| `show ip interface brief` | `sh ip int br` | Raport interfejsów (najkrótszy możliwy) |
| `configure terminal` | `conf t` | Wejście do trybu konfiguracji |
| `interface` | `int` | Konfiguracja interfejsu |
| `no shutdown` | `no shut` | Włączenie interfejsu |

---

### Operacje na przedrostkach komend

| Przedrostek | Zastosowanie | Przykład |
|-------------|--------------|----------|
| `no` | Negacja/usunięcie komendy | `no shutdown` (włączenie interfejsu) |
| | | `no ip address` (usunięcie adresu IP) |
| `do` | Wykonanie komendy exec z trybu config | `do show ip interface brief` |
| `default` | Przywrócenie domyślnej wartości | `default interface GigabitEthernet0/1` |

---

### Skróty Putty (klient terminala)

| Akcja | Skrót |
|-------|-------|
| Wklejenie zaznaczonego tekstu | Prawy przycisk myszy |
| Wklejenie ze schowka systemowego | Prawy przycisk myszy (bez zaznaczenia) |
| Skopiowanie całej sesji | Menu → "Copy all to clipboard" |

---

## Wzorce użycia komend

### Sprawdzanie stanu urządzenia
```bash
Switch#show ip interface brief
Switch#show running-config
Switch#show vlan
```

### Konfiguracja interfejsu - pełny proces
```bash
Switch#configure terminal
Switch(config)#interface GigabitEthernet0/5
Switch(config-if)#no shutdown
Switch(config-if)#description Polaczenie do PC
Switch(config-if)#do show ip interface brief
Switch(config-if)#exit
Switch(config)#exit
Switch#copy running-config startup-config
```

### Szybka weryfikacja z trybu konfiguracji
```bash
Switch(config)#do show ip interface brief
Switch(config)#do show running-config
```

### Resetowanie do ustawień fabrycznych
```bash
Switch#write erase
Switch#reload
```

---

## Wskazówki praktyczne

1. **Zawsze weryfikuj zmiany** komendą `show` (używaj `do` z trybu config)
2. **Zapisuj konfigurację** przed zakończeniem pracy (`copy run start`)
3. **Używaj auto-uzupełniania** (TAB) - oszczędza czas i zapobiega błędom
4. **Zaznaczaj ważne fragmenty** w Putty i kopiuj do notatek
5. **Na koniec zajęć**: "Copy all to clipboard" → zapisz w pliku
6. **Ucz się skrótów** - `sh ip int br` zamiast `show ip interface brief`
7. **Używaj `?` często** - najlepszy sposób na naukę CLI
8. **Historia komend** - strzałki góra/dół oszczędzają czas

---

## Typowe problemy i rozwiązania

| Problem | Przyczyna | Rozwiązanie |
|---------|-----------|-------------|
| Interfejs "administratively down" | Wyłączony w konfiguracji | `no shutdown` w trybie config-if |
| Nie można wydać komendy `show` | Jesteś w trybie config | Użyj `do show ...` lub wyjdź (`exit`) |
| Komenda nie działa | Błąd w składni | Użyj `?` i TAB do weryfikacji |
| Konfiguracja znika po restarcie | Nie zapisano do startup-config | `copy running-config startup-config` |
| Linia komend "zniszczona" przez komunikaty | Komunikaty systemowe | Naciśnij `CTRL-R` aby odświeżyć linię |

---

## Hierarchia trybów CLI

```
Switch>                           (User EXEC - poziom 1)
    |
    +-- enable
         |
         Switch#                  (Privileged EXEC - poziom 15)
              |
              +-- configure terminal
                   |
                   Switch(config)#              (Global Configuration)
                        |
                        +-- interface GigabitEthernet0/X
                             |
                             Switch(config-if)#   (Interface Configuration)
```

---

## Pliki konfiguracji - schemat

```
┌─────────────────────────────────────┐
│     PAMIĘĆ RAM (volatile)           │
│  running-config (aktywna config)    │
│  - wprowadzane zmiany               │
│  - edytowalna                       │
│  - gubi się po restarcie            │
└─────────────────────────────────────┘
            ↓ ↑
   copy run start / copy start run
            ↓ ↑
┌─────────────────────────────────────┐
│   PAMIĘĆ FLASH (non-volatile)       │
│  startup-config (config startowa)   │
│  - ładowana przy starcie            │
│  - przetrwa restart                 │
│  - usuwana przez: write erase       │
└─────────────────────────────────────┘
```

---

**Autor notatki**: Opracowanie na podstawie materiałów z Laboratorium 000 - Sieci Komputerowe  
**Data**: 2024  
**Wersja**: 1.0

---
---
---
---
---

# Notatka: VLAN, DTP i VTP w przełącznikach Cisco Catalyst - Laboratorium 021

## 1. Podstawowa konfiguracja przełącznika Cisco Catalyst

### Przygotowanie stanowiska
- **Sprzęt**: Przełącznik Cisco Catalyst (2950, 2960, 3550, 3560, 3750) + stacja PC
- **Połączenia**: 
  - Kabel Twisted Pair (Ethernet)
  - Kabel konsoli (zarządzanie)

### Wejście do trybu konfiguracji
```bash
Switch>enable
Switch#configure terminal
Switch(config)#
```

---

## 2. Koncepcja VLAN w przełącznikach warstwy 2

### Czym jest VLAN?
**VLAN** (Virtual LAN) = zbiór wybranych przez administratora portów przełącznika

### Kluczowe cechy VLAN
- Sieci VLAN są **numerowane**
- Poszczególne VLAN mogą być od siebie **izolowane**
- Komunikacja między portami z różnych VLAN **nie jest możliwa** (bez routera)
- **Domyślnie**: wszystkie porty należą do **VLAN 1**

### VLAN a adresacja IP w przełącznikach L2

#### Ważne zasady
- Adresy IP **nie są przypisywane** do portów fizycznych
- Adresy IP otrzymują **wirtualne interfejsy VLAN**
- Adresacja IP służy **tylko do celów administracyjnych** (telnet, HTTP, SSH)

#### Przełącznik L2 vs Router
| Przełącznik L2 | Router |
|----------------|--------|
| Adresy IP tylko dla zarządzania | Adresy IP na każdym porcie |
| IP przypisane do interfejsów VLAN | IP przypisane do portów fizycznych |
| Nie routuje między VLAN | Routuje między sieciami |

---

## 3. Konfiguracja interfejsu VLAN1

### Przypisanie adresu IP do VLAN1
```bash
Switch(config)#interface vlan1
Switch(config-if)#ip address 200.200.200.1 255.255.255.0
Switch(config-if)#no shutdown
```

### Przykładowa adresacja
| Urządzenie | Adres IP | Maska |
|------------|----------|-------|
| Przełącznik (VLAN1) | 200.200.200.1 | 255.255.255.0 |
| Stacja PC | 200.200.200.2 | 255.255.255.0 |

### Warunek aktywacji VLAN
⚠️ **WAŻNE**: VLAN nie uruchomi się, jeśli **żaden port fizyczny** przypisany do tego VLAN nie jest w stanie **Forwarding**

**Rozwiązanie**: Podłącz przynajmniej jedno urządzenie (np. PC) do portu w danym VLAN

---

## 4. Tryby pracy portów przełącznika

### Trzy tryby pracy portu

| Tryb | Przeznaczenie | Ramkowanie |
|------|---------------|------------|
| **access** | Port prowadzący do DTE (końcówka sieci) | IEEE 802.3 (standardowe) |
| **trunk** | Port prowadzący do DCE (inny przełącznik) | IEEE 802.1Q (tagowane) |
| **dynamic** | Tryb negocjowany automatycznie | Zależny od negocjacji |

### Konfiguracja trybu access
```bash
Switch(config)#interface fa0/5
Switch(config-if)#switchport mode access
```

### Identyfikatory portów w Catalyst

#### Fast Ethernet
```
fa 0/1 ... fa 0/n
```
- `fa` = Fast Ethernet
- `0` = port bezpośrednio w obudowie
- `1...n` = numer portu

#### Gigabit Ethernet
```
gig 1/0/1 ... gig 1/0/n
gig 0/1 ... gig 0/n
```

---

## 5. Dynamic Trunking Protocol (DTP)

### Czym jest DTP?
**DTP** = protokół Cisco do automatycznej identyfikacji rodzaju portu po drugiej stronie kabla

### Kiedy działa DTP?
- Tryb **dynamic** (domyślny)
- Można używać w trybie **access** i **trunk** (na wypadek zmiany na dynamic)

### Kiedy DTP NIE działa?

#### Problem: Połączenie przez przełącznik innego producenta
```
[Cisco] ←→ [Inny producent] ←→ [Cisco]
          (nie wspiera DTP)
```

**Skutek**: 
- Sprzeczne komunikaty DTP
- Niemożność ustalenia trybu pracy
- Port zostanie **wyłączony**

#### Komunikat błędu
```
%DTP-5-DOMAINMISMATCH: Unable to perform trunk negotiation on port Fa0/5
because of VTP domain mismatch.
```

### Wyłączanie DTP
```bash
Switch(config)#interface fa0/5
Switch(config-if)#switchport nonegotiate
```

**Zastosowanie**: 
- Połączenia przez przełączniki innych producentów
- Eliminacja problemów z negocjacją
- Gdy znamy docelowy tryb pracy portu

---

## 6. Weryfikacja konfiguracji interfejsów

### Sprawdzenie interfejsów IP
```bash
Switch#show ip interface brief
Switch#show ip interface vlan 1
```

### Z trybu konfiguracji (przedrostek "do")
```bash
Switch(config)#do show ip interface brief
Switch(config)#do show ip interface vlan 1
```

### Interpretacja statusów
- **up** = aktywny i sprawny
- **down** = nieaktywny lub niesprawny
- **administratively down** = wyłączony w konfiguracji

---

## 7. Tworzenie i zarządzanie VLAN

### Sprawdzenie aktualnego stanu VLAN
```bash
Switch#show vlan
```

### Tworzenie nowych VLAN
```bash
Switch#configure terminal
Switch(config)#vlan 20
Switch(config-vlan)#exit
Switch(config)#vlan 21
Switch(config-vlan)#exit
```

### Problem: Tryb VTP CLIENT

#### Komunikat błędu
```
VTP VLAN configuration not allowed when device is in CLIENT mode.
```

#### Rozwiązanie
```bash
Switch(config)#vtp mode transparent
```

---

## 8. Przypisywanie portów do VLAN

### Metoda 1: Pojedynczy port
```bash
Switch(config)#interface fa0/2
Switch(config-if)#no shutdown
Switch(config-if)#switchport mode access
Switch(config-if)#switchport access vlan 20
```

**Skutek**: Port zostaje **przeniesiony** z VLAN1 do VLAN20

### Metoda 2: Zakres portów (interface range)
```bash
Switch(config)#interface range fa0/15 - 17
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 20
```

⚠️ **UWAGA**: W wyrażeniu `15 - 17` **muszą być spacje**!

### Wyłączanie DTP dla portu (jeśli problemy)
```bash
Switch(config)#interface fa0/2
Switch(config-if)#switchport nonegotiate
```

---

## 9. Klasyczny tryb edycji VLAN (starsze przełączniki)

### Wejście do trybu edycji bazy VLAN
```bash
Switch#vlan database
Switch(vlan)#
```

**Uwaga**: Tryb pochodzący z systemu **CatalystOS** - może być niedostępny w nowszych przełącznikach

### Wyjście z trybu edycji VLAN
```bash
Switch(vlan)#exit
```

---

## 10. Test izolacji VLAN

### Procedura testowania

#### Krok 1: Przygotowanie
- Podłącz **dwie stacje PC** kablami TP do dwóch wybranych portów

#### Krok 2: Konfiguracja IP
- Skonfiguruj adresację IP obydwu stacji w **tej samej sieci IP**

#### Krok 3: Test komunikacji
- Wykonaj **ping** z jednej stacji do drugiej

### Scenariusze testowe

| Scenariusz | Porty w VLAN | Wynik ping |
|------------|--------------|------------|
| Test 1 | Oba w tym samym VLAN | ✅ Sukces |
| Test 2 | W różnych VLAN | ❌ Brak komunikacji |

**Wniosek**: VLAN skutecznie **izolują** porty należące do różnych sieci wirtualnych

---

## 11. VLAN Trunks - IEEE 802.1Q

### Koncepcja VLAN Trunk

**Trunk** = specjalne połączenie między przełącznikami umożliwiające przekazywanie ruchu **wielu VLAN jednocześnie**

### Zalety trunków
- System VLAN **obejmuje wiele przełączników**
- VLAN budowane z portów **rozproszonych** po różnych lokalizacjach
- **Jedno połączenie fizyczne** przekazuje ruch wielu izolowanych VLAN
- Wykorzystanie **tagowania IEEE 802.1Q**

### Topologia z trunkiem
```
[PC1] ←→ [Switch1] ←TRUNK→ [Switch2] ←→ [PC2]
         VLAN 10,20           VLAN 10,20
```

---

## 12. Konfiguracja VLAN Trunk

### Przygotowanie stanowiska
- **2 przełączniki** Cisco Catalyst
- **2 stacje PC** (po jednej na przełącznik)
- **Kabel TP RJ45** między przełącznikami (trunk)

### Konfiguracja portu jako trunk
```bash
Switch(config)#interface fa0/1
Switch(config-if)#no shutdown
Switch(config-if)#switchport mode trunk
```

### Enkapsulacja dot1q (niektóre modele, np. Catalyst 3550)
```bash
Switch(config-if)#switchport trunk encapsulation dot1q
```

⚠️ **UWAGA**: Niektóre przełączniki wymagają **jawnego wytypowania** enkapsulacji IEEE 802.1Q przed włączeniem trybu trunk

### Diagram zmiany trybu
```
Port w trybie dynamic (domyślnie)
         ↓
switchport mode trunk
         ↓
Port w trybie trunk (802.1Q)
```

---

## 13. Zarządzanie VLAN w trunk

### Zezwolenie na komunikację VLAN przez trunk
```bash
Switch(config-if)#switchport trunk allowed vlan 1-100
```

⚠️ **Opóźnienie**: Komenda działa z **kilkunastosekundowym** opóźnieniem

### Usuwanie zezwolenia dla konkretnego VLAN
```bash
Switch(config-if)#switchport trunk allowed vlan remove 10
```

### Dodawanie VLAN do trunk
```bash
Switch(config-if)#switchport trunk allowed vlan add 50
```

---

## 14. Weryfikacja konfiguracji trunk

### Podstawowe komendy weryfikacji
```bash
Switch#show running-config
Switch#show interface trunk
Switch#show interface fa0/1 switchport
Switch#show interface fa0/1 status
```

### Sprawdzenie przynależności do VLAN
```bash
Switch#show vlan
```

**Rezultat**: Port skonfigurowany jako trunk **zostaje usunięty** ze wszystkich VLAN (pełni funkcję specjalną: trunk zamiast access)

### Przykładowy wynik `show interface trunk`
```
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Fa0/1       1-100

Port        Vlans allowed and active in management domain
Fa0/1       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,20,30
```

---

## 15. Test rozproszonego VLAN

### Procedura testowania

#### Scenariusze
1. PC1 (VLAN 10, Switch1) ↔ PC2 (VLAN 10, Switch2) → **komunikacja OK**
2. PC1 (VLAN 10, Switch1) ↔ PC2 (VLAN 20, Switch2) → **brak komunikacji**
3. PC1 (VLAN 20, Switch1) ↔ PC2 (VLAN 20, Switch2) → **komunikacja OK**

### Wnioski
- VLAN działa **między przełącznikami** (dzięki trunk)
- Izolacja VLAN jest **zachowana** nawet w środowisku rozproszonym
- Trunk pozwala na **rozbudowę sieci VLAN** bez dodatkowych kabli

---

## 16. Native VLAN w trunk

### Definicja Native VLAN
**Native VLAN** = VLAN, dla którego ruch jest przekazywany przez trunk **bez enkapsulacji** IEEE 802.1Q (w trybie native)

### Domyślne ustawienie
- **Domyślnie**: VLAN 1 jest native VLAN

### Konfiguracja native VLAN
```bash
Switch(config)#interface fa0/1
Switch(config-if)#switchport trunk native vlan 10
```

### Zasada zgodności
⚠️ **KRYTYCZNE**: W **obydwu połączonych** przełącznikach konfiguracja native VLAN **musi być zgodna**!

```
Switch1: native vlan 10  ←→  Switch2: native vlan 10  ✅ OK
Switch1: native vlan 10  ←→  Switch2: native vlan 1   ❌ BŁĄD
```

### Weryfikacja native VLAN
```bash
Switch#show interface fa0/1 trunk
Switch#show interface fa0/1 switchport
```

### Dlaczego native VLAN?
- Kompatybilność wsteczna z urządzeniami nie obsługującymi 802.1Q
- Komunikacja z urządzeniami legacy
- Jeden VLAN może działać bez tagowania

---

## 17. VLAN Trunking Protocol (VTP) - Wprowadzenie

### Czym jest VTP?
**VTP** = protokół umożliwiający **automatyczną propagację** informacji o VLAN między przełącznikami

### Koncepcja domeny VTP
- Przełączniki grupowane w **domeny VTP**
- Domena identyfikowana przez **nazwę** (konfigurowalną)
- Informacja o VLAN propagowana **w ramach jednej domeny**

### Trzy role w domenie VTP

| Rola | Funkcja | Możliwość edycji VLAN |
|------|---------|----------------------|
| **Server** | Konfigurowany przez admina, serwuje info do klientów | ✅ Tak |
| **Client** | Konfiguruje VLAN na podstawie komunikatów serwera | ❌ Nie |
| **Transparent** | Przekazuje komunikaty VTP dalej, nie aktualizuje bazy | ✅ Tak (lokalnie) |

---

## 18. Wymagania i ograniczenia VTP

### Wymagania techniczne

#### 1. Trunk między przełącznikami
```bash
Switch(config-if)#switchport mode trunk
```
Weryfikacja:
```bash
Switch#show interface fa0/1 switchport
```

#### 2. Identyczna nazwa domeny
```bash
Switch(config)#vtp domain nazwa_domeny
```

⚠️ **UWAGA**: Nazwa domeny musi być **unikatowa** w środowisku laboratoryjnym!

#### 3. Brak innych przełączników na trasie
```
[Switch1] ←→ [Switch2]  ✅ OK

[Switch1] ←→ [Inny producent] ←→ [Switch2]  ❌ Nie zadziała
[Switch1] ←→ [Inna domena VTP] ←→ [Switch2]  ❌ Nie zadziała
```

### Ważne ostrzeżenie o dołączaniu do domeny

⚠️ **KRYTYCZNE**: Gdy przełącznik **dołącza do domeny VTP**, automatycznie pobiera bazę VLAN od istniejących przełączników (nawet jeśli będzie serwerem!)

**Skutek**: Poprzednia baza VLAN zostanie **UTRACONA**!

---

## 19. Zabezpieczenie VTP hasłem

### Konfiguracja hasła
```bash
Switch(config)#vtp password haslo
```

### Mechanizm zabezpieczenia
- Hasło jest podstawą do obliczenia **hash-kodu MD5**
- Hash MD5 służy do **zakodowania/rozkodowania** komunikatów VTP

### Problem: Niezgodne hasła

#### Objawy
```
MD5 digest checksum mismatch
```

#### Rozwiązanie
1. **Ujednolicić** hasła VTP w przełącznikach
2. **Usunąć** hasła ze wszystkich przełączników

### Usuwanie hasła
```bash
Switch(config)#no vtp password
```

---

## 20. Rozwiązywanie problemów VTP

### Problem 1: Niezgodność hash-kodów MD5

#### Rozwiązanie
Przeprowadzić klienta VTP: client → transparent → client
```bash
Switch(config)#vtp mode transparent
Switch(config)#vtp mode client
```

### Problem 2: Niezgodność VTP revision number

#### Objawy
- Brak aktualizacji VLAN
- Niemożność skasowania revision number

#### Rozwiązanie
Zmienić nazwę domeny VTP: nazwa1 → nazwa2 → nazwa1
```bash
Switch(config)#vtp domain tymczasowa_nazwa
Switch(config)#vtp domain oryginalna_nazwa
```

### Problem 3: Brak aktualizacji VLAN

#### Wymuszenie aktualizacji VTP
**Metoda**: Chwilowo założyć i usunąć VLAN w serwerze VTP
```bash
Switch(config)#vlan 999
Switch(config-vlan)#exit
Switch(config)#no vlan 999
```

⚠️ To **jedyna** metoda wymuszenia aktualizacji!

---

## 21. Konfiguracja VTP - Serwer

### Przygotowanie stanowiska
- **2 przełączniki** Cisco (2950, 2960, 3550, 3560, 3750)
- Połączenie: kabel **TP** lub **światłowód**
- Podział ról: jeden **serwer VTP**, drugi **klient VTP**

### Konfiguracja serwera VTP
```bash
Switch(config)#vtp mode server
```

**Uwaga**: Tryb **server** jest **domyślny**, ale zawsze należy zweryfikować!

### Definicja VLAN w serwerze
```bash
Switch(config)#vlan 20
Switch(config-vlan)#exit
Switch(config)#vlan 21
Switch(config-vlan)#exit
Switch(config)#vlan 22
Switch(config-vlan)#exit
```

⚠️ **WAŻNE**: Propagacja komunikatu VTP następuje **dopiero po wydaniu komendy `exit`**!

---

## 22. Konfiguracja VTP - Klient

### Konfiguracja klienta VTP
```bash
Switch(config)#vtp domain nazwa_domeny
Switch(config)#vtp mode client
```

**Nazwa domeny**: Musi być **identyczna** jak w serwerze!

### Weryfikacja propagacji VLAN
```bash
Switch#show vlan
```

#### Oczekiwany rezultat
- Pozycja **"Number of existing VLANs"** zwiększona z **5** (bazowa) o liczbę VLAN skonfigurowanych przez VTP
- Lista VLAN zawiera VLAN utworzone w serwerze

### Teraz możliwe jest
```bash
Switch(config)#interface fa0/5
Switch(config-if)#switchport access vlan 20
```
Przydzielanie portów do VLAN **otrzymanych od serwera VTP**

---

## 23. Tryb transparent VTP

### Przełączenie w tryb transparent
```bash
Switch(config)#vtp mode transparent
```

### Charakterystyka trybu transparent
- **Nie aktualizuje** swojej bazy VLAN
- **Przekazuje** komunikaty VTP do dalszych przełączników
- Pozwala na **lokalną edycję** VLAN

### Test działania
1. Przełączyć klienta w tryb transparent
2. Dokonać zmian w serwerze VTP (dodać VLAN)
3. Sprawdzić, czy klient **nie zaktualizował** swojej bazy

### Powrót do trybu client
```bash
Switch(config)#vtp mode client
```

**Rezultat**: Baza VLAN zostanie **zaktualizowana** zgodnie z serwerem

---

## 24. Diagnostyka VTP

### Status VTP
```bash
Switch#show vtp status
```

#### Przykładowy wynik
```
VTP Version capable             : 1 to 3
VTP version running             : 1
VTP Domain Name                 : domena
VTP Pruning Mode                : Disabled
VTP Traps Generation            : Disabled
Device ID                       : 0019.e86a.6a80
Configuration last modified by  : 192.168.1.1 at 3-1-93 00:15:12
Local updater ID is             : 192.168.1.1 on interface Vl1 (lowest numbered VLAN interface found)

Feature VLAN:
--------------
VTP Operating Mode              : Server
Maximum VLANs supported locally : 255
Number of existing VLANs        : 8
Configuration Revision          : 5
MD5 digest                      : 0x3F 0x21 0xA8 0x5E 0x9D 0x5C...
```

### Debugowanie VTP
```bash
Switch#debug sw-vlan vtp events
```

**Zastosowanie**: 
- Włączyć w przełączniku **klienta VTP**
- Uruchomić gdy VTP **nie działa**
- Obserwować komunikaty podczas propagacji

### Monitorowanie Revision Number
```bash
Switch#show vtp status
```

Obserwować pole: **Configuration Revision**
- Zaczyna od **0**
- **Rośnie** z każdą aktualizacją listy VLAN
- Pomaga śledzić synchronizację

---

## 25. Różnica: Interface VLAN vs VLAN

### VLAN (definicja sieci)
```bash
Switch(config)#vlan 20
Switch(config-vlan)#exit
```
**Efekt**: Tworzy VLAN 20 (definicja sieci wirtualnej)

### Interface VLAN (interfejs IP)
```bash
Switch(config)#interface vlan 20
Switch(config-if)#ip address 10.0.20.1 255.255.255.0
```
**Efekt**: Tworzy interfejs IP dla VLAN 20 (do zarządzania)

### Usuwanie błędnie utworzonego interfejsu VLAN
```bash
Switch(config)#no interface vlan 20
```

---

## Podsumowanie wszystkich komend

### Komendy podstawowe - Konfiguracja interfejsu VLAN1

| Komenda | Opis |
|---------|------|
| `interface vlan1` | Wejście do konfiguracji interfejsu VLAN1 |
| `ip address 200.200.200.1 255.255.255.0` | Przypisanie adresu IP do interfejsu VLAN |
| `no shutdown` | Aktywacja interfejsu VLAN |
| `show ip interface brief` | Raport o wszystkich interfejsach IP |
| `show ip interface vlan 1` | Szczegóły interfejsu VLAN1 |

---

### Komendy konfiguracji trybu portu

| Komenda | Opis |
|---------|------|
| `interface fa0/5` | Wejście do konfiguracji portu FastEthernet 0/5 |
| `interface gig0/1` | Wejście do konfiguracji portu GigabitEthernet 0/1 |
| `switchport mode access` | Ustawienie trybu portu: access (DTE) |
| `switchport mode trunk` | Ustawienie trybu portu: trunk (DCE) |
| `switchport mode dynamic` | Ustawienie trybu portu: dynamic (auto-negocjacja) |
| `switchport nonegotiate` | Wyłączenie protokołu DTP dla portu |

---

### Komendy zarządzania VLAN

#### Tworzenie i usuwanie VLAN
| Komenda | Opis |
|---------|------|
| `show vlan` | Wyświetlenie wszystkich VLAN w przełączniku |
| `vlan 20` | Utworzenie VLAN 20 (lub wejście do jego konfiguracji) |
| `vlan 21` | Utworzenie VLAN 21 |
| `no vlan 20` | Usunięcie VLAN 20 |
| `vlan database` | Wejście do klasycznego trybu edycji VLAN (starsze modele) |

#### Przypisywanie portów do VLAN
| Komenda | Opis |
|---------|------|
| `switchport access vlan 20` | Przypisanie portu do VLAN 20 (tryb access) |
| `interface range fa0/15 - 17` | Konfiguracja zakresu portów (15, 16, 17) |
| `switchport access vlan 20` (w trybie range) | Przypisanie wszystkich portów z zakresu do VLAN 20 |

---

### Komendy VTP (VLAN Trunking Protocol)

#### Tryby VTP
| Komenda | Opis |
|---------|------|
| `vtp mode server` | Ustawienie trybu VTP: serwer (domyślny) |
| `vtp mode client` | Ustawienie trybu VTP: klient |
| `vtp mode transparent` | Ustawienie trybu VTP: transparent |

#### Konfiguracja domeny i hasła
| Komenda | Opis |
|---------|------|
| `vtp domain nazwa_domeny` | Ustawienie nazwy domeny VTP (musi być unikalna!) |
| `vtp password haslo` | Ustawienie hasła dla domeny VTP |
| `no vtp password` | Usunięcie hasła VTP |

#### Diagnostyka VTP
| Komenda | Opis |
|---------|------|
| `show vtp status` | Wyświetlenie statusu VTP (tryb, domena, revision number) |
| `debug sw-vlan vtp events` | Włączenie debugowania zdarzeń VTP |
| `no debug sw-vlan vtp events` | Wyłączenie debugowania VTP |

---

### Komendy konfiguracji trunk

#### Podstawowa konfiguracja trunk
| Komenda | Opis |
|---------|------|
| `switchport mode trunk` | Zmiana portu na tryb trunk |
| `switchport trunk encapsulation dot1q` | Wymuszenie enkapsulacji 802.1Q (niektóre modele) |
| `switchport trunk allowed vlan 1-100` | Zezwolenie na VLAN 1-100 w trunk |
| `switchport trunk allowed vlan add 50` | Dodanie VLAN 50 do listy dozwolonych |
| `switchport trunk allowed vlan remove 10` | Usunięcie VLAN 10 z listy dozwolonych |

#### Native VLAN
| Komenda | Opis |
|---------|------|
| `switchport trunk native vlan 10` | Ustawienie VLAN 10 jako native VLAN |

#### Weryfikacja trunk
| Komenda | Opis |
|---------|------|
| `show interface trunk` | Raport o wszystkich portach trunk |
| `show interface fa0/1 switchport` | Szczegóły konfiguracji switchport dla fa0/1 |
| `show interface fa0/1 status` | Status portu fa0/1 |
| `show interface fa0/1 trunk` | Szczegóły trunk dla fa0/1 (native VLAN, allowed VLAN) |

---

### Komendy weryfikacji i diagnostyki

| Komenda | Opis |
|---------|------|
| `show running-config` | Wyświetlenie aktywnej konfiguracji |
| `show vlan` | Lista wszystkich VLAN i przypisanych portów |
| `show vlan brief` | Skrócona lista VLAN |
| `show interface trunk` | Lista portów trunk i ich konfiguracji |
| `show interface fa0/1 switchport` | Pełne informacje o porcie (tryb, VLAN, trunk) |
| `show vtp status` | Status VTP (tryb, domena, revision) |
| `do show vlan` | Wykonanie komendy show z trybu config |

---

### Komendy różnicowe i naprawcze

| Komenda | Opis |
|---------|------|
| `no shutdown` | Włączenie interfejsu/portu |
| `shutdown` | Wyłączenie interfejsu/portu |
| `no vlan 20` | Usunięcie VLAN 20 |
| `no interface vlan 20` | Usunięcie interfejsu IP VLAN 20 |
| `no switchport access vlan 20` | Przywrócenie portu do VLAN 1 (domyślny) |
| `no switchport nonegotiate` | Włączenie z powrotem DTP |
| `no vtp password` | Usunięcie hasła VTP |

---

### Skróty i aliasy komend

| Pełna komenda | Skrót | Uwagi |
|---------------|-------|-------|
| `show vlan` | `sh vlan` | Najczęściej używane |
| `interface fastethernet` | `int fa` | Port Fast Ethernet |
| `interface gigabitethernet` | `int gig` | Port Gigabit Ethernet |
| `interface range` | `int range` | Konfiguracja zakresu portów |
| `switchport` | `sw` | Komendy switchport |
| `configure terminal` | `conf t` | Wejście do trybu config |

---

### Typowe sekwencje komend - Wzorce użycia

#### Utworzenie VLAN i przypisanie portów
```bash
Switch#configure terminal
Switch(config)#vlan 20
Switch(config-vlan)#exit
Switch(config)#interface range fa0/10 - 15
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 20
Switch(config-if-range)#switchport nonegotiate
Switch(config-if-range)#exit
```

#### Konfiguracja trunk między dwoma przełącznikami
```bash
# Przełącznik 1 i 2:
Switch(config)#interface fa0/1
Switch(config-if)#no shutdown
Switch(config-if)#switchport mode trunk
Switch(config-if)#switchport trunk allowed vlan 1-100
Switch(config-if)#switchport trunk native vlan 10
```

#### Konfiguracja VTP - Serwer
```bash
Switch(config)#vtp mode server
Switch(config)#vtp domain moja_domena
Switch(config)#vtp password bezpieczne_haslo
Switch(config)#vlan 20
Switch(config-vlan)#exit
Switch(config)#vlan 21
Switch(config-vlan)#exit
```

#### Konfiguracja VTP - Klient
```bash
Switch(config)#vtp mode client
Switch(config)#vtp domain moja_domena
Switch(config)#vtp password bezpieczne_haslo
Switch(config)#interface fa0/1
Switch(config-if)#switchport mode trunk
```

#### Weryfikacja pełnej konfiguracji VLAN
```bash
Switch#show vlan
Switch#show interface trunk
Switch#show vtp status
Switch#show ip interface brief
```

---

## Schematy i diagramy

### Hierarchia trybów portu przełącznika

```
Port Ethernet (domyślnie: dynamic)
         |
         +-- switchport mode access → Ramki 802.3 (standardowe)
         |
         +-- switchport mode trunk → Ramki 802.1Q (tagowane)
         |
         +-- switchport mode dynamic → Auto-negocjacja (DTP)
```

### Propagacja VLAN przez VTP

```
┌────────────────────────┐
│   VTP SERVER           │
│   - edycja VLAN        │
│   - propagacja config  │
└───────────┬────────────┘
            │ Trunk (VTP messages)
            ↓
┌────────────────────────┐
│   VTP CLIENT           │
│   - odbiera VLAN       │
│   - brak edycji        │
└────────────────────────┘
```

### Trunk z tagowaniem 802.1Q

```
[Switch1]                      [Switch2]
VLAN 10,20,30                  VLAN 10,20,30
     |                              |
     +--- TRUNK (802.1Q) -----------+
           |                    |
        Tag VLAN 10          Tag VLAN 10
        Tag VLAN 20          Tag VLAN 20
        Tag VLAN 30          Tag VLAN 30
        Native VLAN 1        Native VLAN 1 (bez tagu)
```

### Izolacja VLAN - przed i po konfiguracji

#### Przed (wszystkie porty w VLAN 1)
```
┌──────────────────────────────────┐
│         VLAN 1 (default)         │
│  [Port1] [Port2] [Port3] [Port4] │
│     ↕       ↕       ↕       ↕    │
│    komunikacja możliwa wszędzie  │
└──────────────────────────────────┘
```

#### Po utworzeniu VLAN 10 i 20
```
┌──────────────────┐    ┌──────────────────┐
│     VLAN 10      │    │     VLAN 20      │
│  [Port1] [Port2] │    │  [Port3] [Port4] │
│     ↕       ↕    │    │     ↕       ↕    │
└──────────────────┘    └──────────────────┘
  Komunikacja OK          Komunikacja OK
  
  ✗ Brak komunikacji między VLAN 10 i VLAN 20
```

---

## Typowe problemy i rozwiązania

| Problem | Przyczyna | Rozwiązanie |
|---------|-----------|-------------|
| VLAN nie uruchamia się | Żaden port VLAN nie w stanie Forwarding | Podłączyć urządzenie do portu w tym VLAN |
| Port wyłączony (DTP error) | Kolizja VTP domain przez przełącznik innego producenta | `switchport nonegotiate` |
| Nie można tworzyć VLAN | Tryb VTP: CLIENT | `vtp mode transparent` lub `vtp mode server` |
| VTP nie propaguje VLAN | Różne nazwy domen VTP | Ujednolicić `vtp domain nazwa` |
| VTP MD5 digest mismatch | Różne hasła VTP | Ujednolicić lub usunąć hasła (`no vtp password`) |
| Trunk nie działa | Brak enkapsulacji dot1q (Catalyst 3550) | `switchport trunk encapsulation dot1q` |
| Native VLAN mismatch | Różne native VLAN po obu stronach | Ujednolicić konfigurację native VLAN |
| VLAN nie aktualizuje się | VTP revision number | Założyć i usunąć VLAN w serwerze (wymuszenie) |

---

## Najlepsze praktyki

### Bezpieczeństwo
1. **Nigdy** nie używać VLAN 1 dla danych użytkowników (użyć tylko dla zarządzania)
2. **Zawsze** zmieniać native VLAN z domyślnego VLAN 1
3. **Używać** unikalnych nazw domen VTP w laboratorium
4. **Zabezpieczać** VTP hasłem w środowiskach produkcyjnych
5. **Wyłączać DTP** (`switchport nonegotiate`) gdy połączenie nie wymaga negocjacji

### Konfiguracja
1. **Dokumentować** przypisanie portów do VLAN
2. **Weryfikować** konfigurację trunk po każdej zmianie (`show interface trunk`)
3. **Monitorować** VTP revision number w środowiskach z VTP
4. **Używać** opisów interfejsów (`description`) dla ważnych połączeń
5. **Testować** izolację VLAN przed wdrożeniem produkcyjnym

### Rozwiązywanie problemów
1. **Sprawdzać** logi i komunikaty debugowania
2. **Weryfikować** konfigurację trunk na obu końcach połączenia
3. **Upewnić się**, że allowed VLAN zawiera potrzebne VLAN
4. **Sprawdzać** zgodność native VLAN po obu stronach trunk
5. **Używać** `show` commands przed i po każdej zmianie

---

## Przydatne wskazówki

### Numery VLAN
- **VLAN 1**: Zarezerwowany, domyślny, najlepiej nie używać dla danych użytkowników
- **VLAN 2-1001**: Normalne VLAN (standard range)
- **VLAN 1002-1005**: Zarezerwowane dla Token Ring i FDDI
- **VLAN 1006-4094**: Extended range VLAN (nie wszystkie przełączniki)

### Priorytety DTP
```
Mode           Port A  ↔  Port B     Result
------------------------------------------
trunk              ↔   trunk        trunk
trunk              ↔   dynamic      trunk
dynamic            ↔   dynamic      access
access             ↔   access       access
```

### VTP Revision Number
- Zaczyna od **0**
- Zwiększa się o **1** przy każdej zmianie VLAN
- **Wyższy** revision number **wygrywa** przy propagacji
- Kasowanie: zmiana nazwy domeny lub trybu VTP

---

## Checklist konfiguracji

### VLAN podstawowy
- [ ] Utworzyć VLAN (`vlan 20`)
- [ ] Przypisać porty do VLAN (`switchport access vlan 20`)
- [ ] Ustawić tryb portu (`switchport mode access`)
- [ ] Wyłączyć DTP jeśli potrzeba (`switchport nonegotiate`)
- [ ] Zweryfikować (`show vlan`)

### Trunk między przełącznikami
- [ ] Skonfigurować enkapsulację dot1q (jeśli wymagane)
- [ ] Ustawić tryb trunk (`switchport mode trunk`)
- [ ] Skonfigurować allowed VLAN (`switchport trunk allowed vlan 1-100`)
- [ ] Ustawić native VLAN (`switchport trunk native vlan 10`)
- [ ] Zweryfikować na obu przełącznikach (`show interface trunk`)
- [ ] Sprawdzić zgodność native VLAN

### VTP Server i Client
- [ ] Skonfigurować trunk między przełącznikami
- [ ] Ustawić nazwę domeny VTP (identyczna w obu)
- [ ] Ustawić tryb: server w jednym, client w drugim
- [ ] Utworzyć VLAN w serwerze
- [ ] Zweryfikować propagację w kliencie (`show vlan`)
- [ ] Sprawdzić VTP status (`show vtp status`)

---

**Autor notatki**: Opracowanie na podstawie materiałów z Laboratorium 021 - Sieci Komputerowe  
**Tematyka**: VLAN, DTP, VTP, Trunking IEEE 802.1Q  
**Data**: 2024  
**Wersja**: 1.0

---
---
---
---
---

# Sieci Komputerowe – Lab 022
### Przełączniki Cisco Catalyst: STP, EtherChannel, zarządzanie

---

## Zadanie A – Kanały komunikacyjne i dostęp zdalny

### Konfiguracja adresu IP (interfejs VLAN1)
```
enable
configure terminal
interface vlan1
ip address <adres_IP> <maska>
no shutdown
```

### Sprawdzenie interfejsów
```
show ip interface brief
show ip interface vlan 1
```

### Konfiguracja Telnet (linie VTY)
```
line vty 0 15
password sieci
login
transport input telnet
```
> Jeśli włączony jest `aaa new-model`, należy go wyłączyć: `no aaa new-model`

### Hasło trybu uprzywilejowanego (exec)
```
enable password sieci
```

### Konfiguracja SSH
```
ip domain-name domena
hostname Mojhost
crypto key generate rsa          # klucz min. 512 bitów (wymagane przez PuTTY)
aaa new-model
username sieci priv 15 password 0 sieci
line vty 0 15
transport input ssh
```

### Ograniczenie dostępu przez ACL
```
access-list 55 permit 192.168.1.0 0.0.0.255
line vty 0 15
access-class 55 in
```
> Maska w ACL zapisana jest w **inwersji bitowej** (np. `0.0.0.255` = maska `255.255.255.0`)

---

## Zadanie B – Spanning Tree Protocol (STP)

### Sprawdzenie stanu STP
```
show spanning-tree
show spanning-tree detail
show spanning-tree vlan 1-10
show spanning-tree summary
show spanning-tree int fa 0/1 detail
```

### Zarządzanie root bridge
```
spanning-tree vlan 1 root primary      # ustawia bieżący przełącznik jako root
spanning-tree vlan 1 root secondary    # ustawia jako zapasowy root bridge
spanning-tree vlan 1 priority 16384    # bezpośrednie ustawienie priorytetu (wielokrotność 4096)
```
> Domyślna wartość priorytetu: **32768**

### Ochrona root bridge
```
spanning-tree guard root               # blokuje przejęcie statusu root bridge przez port
```

### Priorytet portu i koszt łącza
```
interface fa 0/1
spanning-tree port-priority 64        # domyślnie 128, granulacja 16
spanning-tree vlan 1 port-priority 64

spanning-tree cost 13                  # ręczne ustawienie kosztu
spanning-tree vlan 1 cost 13
```

> Domyślne koszty STP (16-bit): 10Mbps=100, 100Mbps=19, 1Gbps=4, 10Gbps=2

### Tryby STP
```
spanning-tree mode pvst                # Per-VLAN STP
spanning-tree mode rapid-pvst          # Rapid Per-VLAN STP
```

### Wyłączenie STP
```
spanning-tree portfast default         # wyłącza STP globalnie
spanning-tree portfast                 # wyłącza STP na porcie (tryb config-if)
```
> ⚠️ Wyłączenie STP przy aktywnej pętli może zablokować całą sieć!

### Diagnostyka STP
```
debug spanning-tree events
no debug spanning-tree events
```

---

## Zadanie C – Zarządzanie adresami MAC

### Przeglądanie tablicy MAC
```
show mac address-table
show mac-address-table
show mac-address-table interface fa 0/1
```

### Blokowanie stacji (statyczny wpis MAC + drop)
```
mac address-table static 0013.72b9.89fe vlan 1 drop
```

### Statyczny wpis MAC kierujący na port
```
mac-address-table static 1111.2222.3333 vlan 1 int fa0/5
```

---

## Zadanie D – EtherChannel (agregacja portów)

### Tworzenie interfejsu Port-Channel
```
interface Port-channel 1
switchport mode access
```

### Przypisywanie portów do channel-group
```
interface range FastEthernet 0/1 - 2
switchport mode access
channel-group 1 mode on
```
> Numer `channel-group` musi być taki sam jak numer `Port-channel`

### Sprawdzenie EtherChannel
```
show etherchannel summary              # statusy: D-down, P-port-channel, d-default
show spanning-tree                     # łącze widoczne jako Po1
```

### Diagnostyka EtherChannel
```
debug etherchannel
```

### Reset interfejsu Port-Channel (przy błędach)
```
int Po 1
shut
no shut
```

---

## Zadanie E – Ogólne czynności konfiguracyjne

| Komenda | Opis |
|---|---|
| `no ip domain-lookup` | Wyłącza automatyczne wyszukiwanie DNS przy literówkach |
| `hostname s0` | Zmiana nazwy przełącznika |
| `show running-config` | Wyświetla aktualną konfigurację |
| `write memory` | Zapisuje konfigurację do pamięci FLASH |
| `write erase` | Kasuje konfigurację (przywraca ustawienia fabryczne) |
| `exit` | Cofnięcie o jeden poziom w CLI |
| `end` | Powrót do trybu exec z dowolnego poziomu |
| `debug ip icmp` | Monitorowanie ruchu ICMP |
| `no debug all` | Wyłączenie wszystkich procesów debug |

---

## Kluczowe pojęcia

| Pojęcie | Znaczenie |
|---|---|
| **Root Bridge** | Wyróżniony przełącznik w STP, do którego liczone są ścieżki |
| **BID** | Bridge ID – identyfikator przełącznika w STP (priority + MAC) |
| **Root Port** | Port z najlepszą ścieżką do root bridge |
| **Designated Port** | Najlepszy port na danym segmencie sieci |
| **Alternative Port** | Zablokowany port zapasowy w STP |
| **BPDU** | Bridge Protocol Data Unit – ramka STP |
| **EtherChannel** | Logiczna agregacja portów fizycznych |
| **VTY** | Virtual Terminal Lines – linie dla zdalnego dostępu (Telnet/SSH) |
| **ACL** | Access Control List – lista reguł filtrowania ruchu |
| **Port-Channel** | Interfejs logiczny reprezentujący EtherChannel |

---
---
---
---
---

# 🖧 Sieci Komputerowe – Lab 070 – Notatka krok po kroku

## Topologia

```
[PC] ──── [R1 (fa0/0)] ── [R1 (fa0/1)] ──── [R2]
```

- R1 podłączony do PC (fa0/0) i do R2 (fa0/1)
- Konsola konfiguracyjna podłączona do R1

---

## ✅ Zadanie A – Przygotowanie rutera (wstępna konfiguracja)

### 1. Uruchomienie i wejście do trybu konfiguracji

```bash
# Po uruchomieniu rutera pojawia się karetka:
Router>

# Wejście w tryb uprzywilejowany:
Router> enable
Router#

# Wejście w tryb konfiguracji:
Router# configure terminal
Router(config)#
```

> **Skrót:** `conf t` zamiast `configure terminal`  
> **TAB** – autouzupełnianie, **?** – podpowiedzi

---

### 2. Wyłączenie uciążliwych funkcji

```bash
# Wyłącz automatyczne zapytania DNS (blokują CLI):
Router(config)# no ip domain-lookup

# Wyłącz pobieranie konfiguracji przez TFTP:
Router(config)# no service config

# Wyłącz stronicowanie długich wydruków (wpisz w trybie #):
Router# terminal length 0
```

---

### 3. Konfiguracja interfejsów (przykład dla fa0/1)

```bash
Router(config)# interface FastEthernet 0/1
Router(config-if)# ip address 192.168.123.100 255.255.255.0
Router(config-if)# no shutdown
```

> ⚠️ Większość interfejsów fizycznych jest domyślnie wyłączona (`administratively down`).  
> Zawsze kończ `no shutdown`!

---

### 4. Interfejs wirtualny Loopback

```bash
Router(config)# interface Loopback 0
Router(config-if)# ip address 10.0.0.1 255.255.255.0
Router(config-if)# no shutdown
```

---

### 5. Sprawdzanie stanu interfejsów

```bash
Router# show ip int fa 0/1
Router# show interfaces summary
Router# show interfaces description
Router# show interfaces accounting
Router# show interfaces fa 0/1
Router# show controllers fa 0/1
Router# show run
```

---

### 6. Zapis i kasowanie konfiguracji

```bash
# Zapis do NVRAM (użyj dopiero po zakończeniu eksperymentów):
Router# write mem
# lub:
Router# copy running-config startup-config

# Restart rutera (NIE TERAZ):
Router# reload

# Kasowanie konfiguracji z NVRAM po zakończeniu lab:
Router# write erase
```

> ⚠️ **Po zakończeniu laboratorium wymaż konfigurację z NVRAM** (`write erase`), żeby nie przeszkadzać kolejnym użytkownikom!

---

### 7. Nawigacja po CLI

```bash
exit        # wyjście o jeden poziom wyżej
end         # skok od razu na szczyt (do Router#)
Ctrl+Z      # to samo co end
```

---

## ✅ Zadanie B – Dalsze czynności konfiguracyjne

### 1. Nadanie nazwy ruterowi

```bash
Router(config)# hostname R1
R1(config)#
```

> Każdy ruter powinien mieć unikalną nazwę – łatwiej uniknąć pomyłek.

---

### 2. Sprawdzenie interfejsów IP

```bash
R1# show ip interface brief
R1# show interfaces description
```

---

### 3. Filtrowanie komunikatów systemowych (konsola)

```bash
R1(config)# logging console 2
```

> 8 poziomów szczegółowości (0 = najważniejsze, 7 = debug).

---

### 4. Sprawdzanie konfiguracji

```bash
R1# show running-config    # bieżąca konfiguracja (RAM)
R1# show startup-config    # konfiguracja w NVRAM
```

---

### 5. Sesje użytkowników

```bash
R1# show users
R1# show sessions
```

---

### 6. Diagnostyka – ping

```bash
# Seria 1000 pingów:
R1# ping 192.168.1.1 rep 1000

# Anulowanie: CTRL+SHIFT+6
```

---

### 7. Tablica ARP

```bash
R1# show arp
```

---

### 8. CDP – Cisco Discovery Protocol

CDP wykrywa sąsiadujące urządzenia Cisco.

```bash
# Podłącz interfejs R2 do interfejsu R1 (ta sama sieć IP!)

# Sprawdzenie CDP:
R1# show cdp
R1# show cdp neighbors
R1# show cdp neighbors detail

# Włącz / wyłącz CDP globalnie:
R1(config)# cdp run
R1(config)# no cdp run

# Wyłącz CDP na konkretnym interfejsie:
R1(config)# interface fa 0/1
R1(config-if)# no cdp enable

# Sprawdź CDP per interfejs:
R1# show cdp interface

# Wyczyść tablicę CDP:
R1# clear cdp table

# Zmiana timera i czasu ważności:
R1(config)# cdp timer 10
R1(config)# cdp holdtime 90
```

#### Test propagacji nazwy hosta przez CDP:
```bash
# Zmień nazwę w jednym ruterze i obserwuj, kiedy pojawi się w sąsiednim:
R1(config)# hostname inna
inna# show cdp neighbors
```

---

## ✅ Zadanie C – Serwer DHCP w ruterze

### Cel:
- R1 = serwer DHCP
- PC i R2 = klienci DHCP

---

### 1. Włącz usługę DHCP w R1

```bash
R1(config)# service dhcp
```

---

### 2. Skonfiguruj pulę DHCP

```bash
R1(config)# ip dhcp pool MOJA_PULA
R1(dhcp-config)# network 10.10.10.0 255.255.255.0
R1(dhcp-config)# default-router 10.10.10.1
R1(dhcp-config)# dns-server 123.123.123.3
R1(dhcp-config)# domain-name domena.pl
R1(dhcp-config)# exit

# Wyklucz adresy z puli (np. dla rutera i serwerów):
R1(config)# ip dhcp excluded-address 10.10.10.10 10.10.10.20
```

> ⚠️ Interfejs R1 musi mieć adres z **tej samej sieci** co pula DHCP!

```bash
R1(config)# interface fa 0/0
R1(config-if)# ip address 10.10.10.1 255.255.255.0
R1(config-if)# no shutdown
```

---

### 3. Ustaw R2 jako klient DHCP

```bash
R2(config)# interface FastEthernet 0/0
R2(config-if)# ip address dhcp
R2(config-if)# no shutdown
```

> Na PC ustaw kartę sieciową na "uzyskaj adres automatycznie (DHCP)".

---

### 4. Sprawdź stan serwera DHCP

```bash
R1# show ip dhcp binding

# Debug DHCP:
R1# debug ip dhcp server events
```

---

### 5. Zdalna usługa DHCP (helper-address)

Gdy serwer DHCP jest w innej sieci, każdy ruter pośredniczący musi przekazywać zapytania:

```bash
Router(config-if)# ip helper-address 200.200.200.1
```

> `200.200.200.1` = adres następnego przeskoku w kierunku serwera DHCP.

---

### 6. Po zakończeniu – wróć do statycznej adresacji

Usuń `ip address dhcp` i ustaw statyczne adresy IP na wszystkich interfejsach.

---

## ✅ Zadanie D – Mostki sieciowe w ruterach

### Typy mostkowania:

| Typ | Opis |
|-----|------|
| **Legacy bridging** | Rutowanie IP całkowicie wyłączone, ruter = czysty mostek |
| **CRB** | Część interfejsów w mostku, inne rutują IP (bez przepływu między nimi) |
| **IRB** | Jak CRB + możliwość rutowania DO sieci mostkowanej przez interfejs BVI |

> ⚠️ Te tryby **wzajemnie się wykluczają**. Uważaj na pozostałości poprzednich konfiguracji!

---

### D.2 – Legacy Bridging

```bash
# Konfiguracja interfejsów R1 – dołącz do mostka nr 1:
R1(config)# interface fa 0/0
R1(config-if)# bridge-group 1
R1(config-if)# no ip address

R1(config)# interface fa 0/1
R1(config-if)# bridge-group 1
R1(config-if)# no ip address

# Wyłącz rutowanie IP:
R1(config)# no ip routing

# Zezwól na przesyłanie ramek Ethernet przez mostek 1:
R1(config)# bridge 1 protocol ieee
```

> ⚠️ Przynajmniej **dwa interfejsy** przypisane do mostka muszą być w stanie `up`.

```bash
# Na R2 – ustaw adres IP w tej samej sieci co PC (bo R1 jest teraz mostkiem):
R2(config)# interface fa 0/0
R2(config-if)# ip address <adres_z_sieci_PC> 255.255.255.0

# Sprawdź mostek:
R1# show bridge group
R1# show bridge

# Sprawdź Spanning Tree:
R1# show spanning-tree brief

# Traceroute z R2 do PC – R1 NIE powinien być widoczny (działa jako mostek):
R2# traceroute <adres_PC>
```

---

### D.3 – CRB (Concurrent Routing and Bridging)

Modyfikacja konfiguracji z poprzedniego punktu:

```bash
# Włącz rutowanie IP:
R1(config)# ip routing

# Uruchom tryb CRB:
R1(config)# bridge crb

# Wyłącz mostek 1 z procesu rutowania (mostkowanie zamiast rutowania):
R1(config)# no bridge 1 route ip

# Włącz mostek 1 do procesu mostkowania IP:
R1(config)# bridge 1 bridge ip
```

```bash
# Sprawdź traceroute (rutowanie IP działa, ale nie obejmuje mostka):
R2# traceroute <adres_PC>

# Sprawdź ping przez mostek CRB:
R2# ping <adres_PC>
```

---

### D.4 – IRB (Integrated Routing and Bridging)

Modyfikacja konfiguracji z poprzedniego punktu:

```bash
# Wyłącz CRB, włącz IRB:
R1(config)# no bridge crb
R1(config)# bridge irb

# Włącz mostek 1 do mostkowania i rutowania:
R1(config)# bridge 1 bridge ip
R1(config)# bridge 1 route ip

# Utwórz wirtualny interfejs BVI (numer = numer mostka!):
R1(config)# interface BVI 1
R1(config-if)# ip address 200.200.200.3 255.255.255.0
R1(config-if)# no shutdown
```

> ⚠️ Numer BVI **musi być zgodny** z numerem w `bridge-group` (tutaj: 1).

```bash
# Traceroute i ping – R1 (BVI) powinien teraz być osiągalny:
R2# traceroute <adres_PC>
R2# ping 200.200.200.3

# Sprawdź komunikację PC ↔ R2 ↔ BVI:
PC> ping 200.200.200.3
```

---

## 📋 Podsumowanie najważniejszych komend

| Komenda | Co robi |
|---------|---------|
| `enable` | Tryb uprzywilejowany |
| `configure terminal` | Tryb konfiguracji |
| `no ip domain-lookup` | Wyłącza DNS (zapobiega blokowaniu CLI) |
| `no shutdown` | Aktywuje interfejs |
| `show ip interface brief` | Szybki widok wszystkich interfejsów IP |
| `show running-config` | Bieżąca konfiguracja |
| `write mem` | Zapis konfiguracji do NVRAM |
| `write erase` | **Kasowanie konfiguracji z NVRAM** |
| `show cdp neighbors detail` | Sąsiedzi CDP ze szczegółami |
| `show ip dhcp binding` | Przydzielone adresy DHCP |
| `show bridge` | Stan mostka |
| `show spanning-tree brief` | Stan Spanning Tree |

# Sieci Komputerowe – Laboratorium 071

## Rutowanie OSPF, Redystrybucja tras, Tunelowanie GRE

> **Jak korzystać z tej notatki?**  
> Każda komenda jest wyraźnie oznaczona i gotowa do wklejenia do PuTTY.  
> Symbole trybów CLI Cisco:
> - `Router>` – tryb użytkownika (EXEC)
> - `Router#` – tryb uprzywilejowany (privileged EXEC)
> - `Router(config)#` – tryb konfiguracji globalnej
> - `Router(config-if)#` – tryb konfiguracji interfejsu
> - `Router(config-router)#` – tryb konfiguracji protokołu rutowania

---

## ZADANIE A – Rutowanie dynamiczne OSPF

## A.1 – Przygotowanie interfejsów fizycznych i loopback

### Na każdym ruterze (R1 i R2) – wejście w tryb konfiguracji globalnej

```
enable
configure terminal
```

### Konfiguracja interfejsu fizycznego (Ethernet lub Serial)

> Zastąp `fa 0/0` właściwym interfejsem oraz wpisz odpowiedni adres IP dla swojej pary adresowej.

**Na R1:**
```
interface FastEthernet0/0
ip address 200.200.200.1 255.255.255.0
no shutdown
exit
```

**Na R2:**
```
interface FastEthernet0/0
ip address 200.200.200.2 255.255.255.0
no shutdown
exit
```

### Konfiguracja interfejsów loopback (symulują podłączone sieci/hosty)

> Adresy loopback muszą być unikatowe w całej instalacji – nie mogą się pokrywać między ruterami!

**Na R1:**
```
interface Loopback0
ip address 100.100.100.1 255.255.255.0
exit
```

**Na R2:**
```
interface Loopback0
ip address 200.200.201.1 255.255.255.0
exit
```

---

## A.2 – Włączenie rutowania IP i trybu classless

> Wykonaj na **każdym** ruterze.

```
ip routing
ip classless
```

---

## A.3 – Uruchomienie OSPF w pojedynczym obszarze (area 0)

> `150` to identyfikator procesu OSPF – **musi być taki sam na wszystkich ruterach**.

### Włączenie procesu OSPF i rejestracja sieci

**Na R1:**
```
router ospf 150
network 200.200.200.0 0.0.0.255 area 0
network 100.100.100.0 0.0.0.255 area 0
exit
```

**Na R2:**
```
router ospf 150
network 200.200.200.0 0.0.0.255 area 0
network 200.200.201.0 0.0.0.255 area 0
exit
```

> **UWAGA dot. maski:** W OSPF maska jest zapisywana w postaci **inwersji** (wildcard mask).  
> Dla /24 (255.255.255.0) → wpisz `0.0.0.255`  
> Dla /16 (255.255.0.0) → wpisz `0.0.255.255`

---

## A.4 – Sprawdzenie stanu OSPF

### Podstawowa diagnostyka po uruchomieniu OSPF

```
show ip protocols
```
> Pokaże aktywne protokoły rutowania i ich parametry.

```
show ip interface brief
```
> Pokaże stan wszystkich interfejsów (UP/DOWN) i ich adresy IP.

```
show ip ospf interface fa 0/0
```
> Pokaże szczegóły OSPF dla wybranego interfejsu (priorytet, rola DR/BDR, hello interval itd.)

```
show ip route
```
> Pokaże tablicę rutowania. Trasy OSPF oznaczone są literą `O`.

```
show ip ospf neighbor
```
> Pokaże listę sąsiadów OSPF – tu sprawdzisz role DR / BDR / DROTHER.

---

## A.5 – Ping z określonym adresem źródłowym (source ping)

> Używamy tego, gdy chcemy pingować z adresu loopback (a nie z interfejsu fizycznego).

```
ping 200.200.201.1 source 100.100.100.1
```

> Gdzie:  
> `200.200.201.1` – adres docelowy (loopback drugiego rutera)  
> `100.100.100.1` – adres źródłowy (nasz loopback)

---

## A.6 – Rola DR / BDR / DROTHER i zmiana priorytetu OSPF

### Sprawdzenie ról sąsiadów OSPF

```
show ip ospf neighbor
```

> W kolumnie `State` zobaczysz:
> - `FULL/DR` – sąsiad jest Designated Routerem
> - `FULL/BDR` – sąsiad jest Backup DR
> - `FULL/DROTHER` – zwykły ruter

### Zmiana priorytetu OSPF (wymuszenie przeniesienia roli DR)

> Priorytet `0` = ruter **nigdy** nie zostanie DR. Wyższy priorytet = większa szansa na DR.

```
interface FastEthernet0/0
ip ospf priority 0
exit
```

> Aby zobaczyć efekt – sprawdź stan po zmianie:

```
show ip ospf interface fa 0/0
```

> **UWAGA:** Zmiana roli DR/BDR nie zachodzi natychmiast – wymaga wygaśnięcia relacji sąsiedztwa lub resetu procesu OSPF. W sieci point-to-point pojęcia DR/BDR **nie istnieją** (oznaczone jako `-`).

---

## A.7 – Obserwacja pakietów HELLO i debugowanie OSPF

### Włączenie debugowania sąsiedztwa OSPF

```
debug ip ospf adjacency
```

### Obserwacja zdarzeń OSPF

```
debug ip ospf events
```

### Wyłączenie debugowania (po zakończeniu obserwacji!)

```
undebug all
```

### Zmiana interwału HELLO (musi być identyczna na obu końcach łącza!)

```
interface FastEthernet0/0
ip ospf hello-interval 15
exit
```

> Domyślne wartości:
> - Sieci broadcast / point-to-point: Hello = **10s**, Dead = **40s**
> - Non-broadcast / point-to-multipoint: Hello = **30s**, Dead = **120s**

---

## A.8 – Zmiana kosztu łącza OSPF

> Domyślny koszt = 100 000 000 / przepustowość (bandwidth w bps)

```
interface FastEthernet0/0
ip ospf cost 35
exit
```

> Koszt jest naliczany dla ruchu **wchodzącego** do rutera przez dany interfejs.

---

## A.9 – OSPF Multi-area i Virtual Links

### Schemat obszarów:
```
[AREA 0] --- ABR1 --- [AREA 1] --- ABR2 --- [AREA 2]
```

### Dodanie nowego obszaru (area 1) przez dodanie loopback

**Na ruterze R1:**
```
interface Loopback5
ip address 200.200.101.1 255.255.255.0
exit
router ospf 150
network 200.200.101.0 0.0.0.255 area 1
exit
```

### Sprawdzenie routerów brzegowych (ABR)

```
show ip ospf border-routers
```

### Konfiguracja Virtual Link (gdy area 2 nie ma bezpośredniego połączenia z area 0)

> Sprawdź Router ID każdego rutera ABR:

```
show ip ospf interface
```

> W wyniku znajdź linię `Process ID X, Router ID Y.Y.Y.Y` – to jest Router ID.

**Na RuterABR1 (po stronie area 0):**
```
router ospf 150
area 1 virtual-link 6.6.6.6
exit
```

**Na RuterABR2 (po stronie area 2):**
```
router ospf 150
area 1 virtual-link 5.5.5.5
exit
```

> Gdzie `5.5.5.5` i `6.6.6.6` to **Router ID** (nie adresy IP interfejsów!) odpowiednich ruterów.

### Weryfikacja Virtual Links

```
show ip ospf virtual-links
```

---

# ZADANIE B – Redystrybucja tras między protokołami IGP

## B.1 – Schemat instalacji

```
PC <-> R1 <-> R2 <-> R3 <-> PC
       RIP   RIP+OSPF  OSPF
```

- **R1** – tylko RIP
- **R2** – RIP + OSPF (ruter graniczny, wykonuje redystrybucję)
- **R3** – tylko OSPF

---

## B.2 – Konfiguracja interfejsów i adresacji

**Na R1:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 10.0.12.1 255.255.255.0
no shutdown
exit
interface Loopback0
ip address 10.1.1.1 255.255.255.0
exit
```

**Na R2:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 10.0.12.2 255.255.255.0
no shutdown
exit
interface FastEthernet0/1
ip address 10.0.23.2 255.255.255.0
no shutdown
exit
```

**Na R3:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 10.0.23.3 255.255.255.0
no shutdown
exit
interface Loopback0
ip address 10.3.3.3 255.255.255.0
exit
```

---

## B.3 – Włączenie RIP na R1 i R2

**Na R1:**
```
router rip
version 2
network 10.0.12.0
network 10.1.1.0
no auto-summary
exit
```

**Na R2 (tylko strona RIP):**
```
router rip
version 2
network 10.0.12.0
no auto-summary
exit
```

---

## B.4 – Włączenie OSPF na R2 i R3

**Na R2 (tylko strona OSPF):**
```
router ospf 150
network 10.0.23.0 0.0.0.255 area 0
exit
```

**Na R3:**
```
router ospf 150
network 10.0.23.0 0.0.0.255 area 0
network 10.3.3.0 0.0.0.255 area 0
exit
```

---

## B.5 – Sprawdzenie tablic przed redystrybucją

> R1 powinien znać tylko trasy z RIP, R3 tylko z OSPF.

```
show ip route
```

---

## B.6 – Redystrybucja OSPF → RIP (na R2)

> Dzięki temu R1 dowie się o sieciach z domeny OSPF.

```
router ospf 150
redistribute rip metric 170 subnets
exit
```

### Weryfikacja na R3

```
show ip route
```

> Trasy z RIP powinny być widoczne z oznaczeniem `O E1` (OSPF External Type 1).

---

## B.7 – Redystrybucja RIP → OSPF (na R2)

> Dzięki temu R3 dowie się o sieciach z domeny RIP.

```
router rip
redistribute ospf 150 metric 11
exit
```

> **UWAGA:** Metryka RIP to liczba przeskoków (max = 15). Wpisz wartość ≤ 15.  
> Słowo kluczowe `subnets` jest wymagane przy redystrybucji **do OSPF** – bez niego propagowane są tylko sieci classfull.

### Weryfikacja na R1

```
show ip route
```

> Trasy z OSPF pojawią się z oznaczeniem `R` (RIP nie rozróżnia źródła tras).

---

## B.8 – Zamiana OSPF na EIGRP (opcjonalne)

### Usunięcie OSPF

**Na R2 i R3:**
```
no router ospf 150
```

### Konfiguracja EIGRP

**Na R2:**
```
router eigrp 100
network 10.0.23.0 0.0.0.255
no auto-summary
exit
```

**Na R3:**
```
router eigrp 100
network 10.0.23.0 0.0.0.255
network 10.3.3.0 0.0.0.255
no auto-summary
exit
```

### Redystrybucja EIGRP ↔ RIP na R2

```
router eigrp 100
redistribute rip metric 1000 100 255 1 1500
exit

router rip
redistribute eigrp 100 metric 5
exit
```

---

## ZADANIE C – Tunelowanie GRE

## C.1 – Schemat instalacji

```
[R1] -------- [R2 = Internet] -------- [R3]
  \_______________TUNEL GRE_______________/
```

- R2 symuluje Internet – **nie konfigurujemy w nim OSPF/GRE**
- Tunel GRE biegnie **między R1 i R3**, przez R2

---

## C.2 – Konfiguracja interfejsów fizycznych

**Na R1:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 200.200.200.1 255.255.255.0
no shutdown
exit
```

**Na R2:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 200.200.200.2 255.255.255.0
no shutdown
exit
interface FastEthernet0/1
ip address 200.200.201.2 255.255.255.0
no shutdown
exit
```

**Na R3:**
```
enable
configure terminal
interface FastEthernet0/0
ip address 200.200.201.1 255.255.255.0
no shutdown
exit
```

---

## C.3 – Trasy statyczne (symulowany Internet)

> R1 i R3 muszą wiedzieć, jak dotrzeć do siebie przez R2.

**Na R1:**
```
ip route 200.200.201.0 255.255.255.0 200.200.200.2
```

**Na R3:**
```
ip route 200.200.200.0 255.255.255.0 200.200.201.2
```

### Sprawdzenie połączenia przez Internet (przed tunelem)

**Na R1:**
```
ping 200.200.201.1
```
> Powinno działać – jeśli nie, sprawdź konfigurację interfejsów i tras statycznych.

---

## C.4 – Konfiguracja tunelu GRE

> Interfejs `Tunnel 0` to wirtualny interfejs – nie istnieje fizycznie.  
> Adresy `192.168.5.x` to adresy **wewnątrz tunelu** (prywatna przestrzeń adresowa).

**Na R1:**
```
interface Tunnel0
tunnel source FastEthernet0/0
ip address 192.168.5.1 255.255.255.0
tunnel destination 200.200.201.1
exit
```

**Na R3:**
```
interface Tunnel0
tunnel source FastEthernet0/0
ip address 192.168.5.2 255.255.255.0
tunnel destination 200.200.200.1
exit
```

### Sprawdzenie tunelu GRE

**Na R1:**
```
ping 192.168.5.2
```
> Powinno przejść – oznacza to, że tunel GRE działa poprawnie.

```
show interfaces tunnel0
```
> W wyniku sprawdź:
> - `Tunnel source` – adres lokalnego końca tunelu
> - `Tunnel destination` – adres zdalnego końca tunelu
> - Liczniki przesłanych datagramów

---

## C.5 – Interfejsy loopback (symulują sieci lokalne)

**Na R1:**
```
interface Loopback0
ip address 192.168.0.1 255.255.255.0
exit
```

**Na R3:**
```
interface Loopback0
ip address 192.168.1.1 255.255.255.0
exit
```

---

## C.6 – Uruchomienie OSPF przez tunel

> OSPF biegnie **przez tunel GRE**, a nie przez fizyczne łącza.  
> Dlatego rejestrujemy sieci tunelu i loopbacków, **NIE** sieci fizyczne (200.200.x.x).

**Na R1:**
```
router ospf 1
network 192.168.0.0 0.0.0.255 area 0
network 192.168.5.0 0.0.0.255 area 0
log-adjacency-changes
exit
```

**Na R3:**
```
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 192.168.5.0 0.0.0.255 area 0
log-adjacency-changes
exit
```

---

## C.7 – Weryfikacja rutowania przez tunel

### Sprawdzenie tablicy OSPF

```
show ip route ospf
```
> Na R1 powinna pojawić się trasa do `192.168.1.0/24` przez `192.168.5.2` (tunel).  
> Na R3 powinna pojawić się trasa do `192.168.0.0/24` przez `192.168.5.1` (tunel).

### Traceroute potwierdzający ścieżkę przez tunel

**Na R1:**
```
traceroute 192.168.1.1 source 192.168.0.1
```

> W wyniku powinieneś zobaczyć skok przez `192.168.5.2` (wnętrze tunelu), a **nie** przez `200.200.200.2` (fizyczne łącze R2).

### Sprawdzenie sąsiedztwa OSPF

```
show ip ospf neighbor
```
> Powinien pojawić się sąsiad z adresem `192.168.5.x` – to oznacza, że OSPF działa przez tunel.

### Ping z loopbacka do loopbacka (przez tunel)

**Na R1:**
```
ping 192.168.1.1 source 192.168.0.1
```

---

## C.8 – Konfiguracja MTU dla tunelu GRE

> Tunelowanie GRE dodaje dodatkowe nagłówki do każdego datagramu, więc standardowe MTU (1500 bajtów) może powodować problemy z fragmentacją. Należy je zmniejszyć.

**Na R1:**
```
interface Tunnel0
ip mtu 1460
ip tcp adjust-mss 1430
exit
```

**Na R3:**
```
interface Tunnel0
ip mtu 1460
ip tcp adjust-mss 1430
exit
```

> **UWAGA:** Wartości MTU i MSS **muszą być identyczne** po obu stronach tunelu!  
> - `ip mtu 1460` – maksymalny rozmiar datagramu IP w tunelu  
> - `ip tcp adjust-mss 1430` – maksymalny rozmiar segmentu TCP (MSS = MTU - 30 bajtów nagłówka)

---

# Podsumowanie – najważniejsze komendy diagnostyczne

| Komenda | Opis |
|---|---|
| `show ip route` | Tablica rutowania |
| `show ip protocols` | Aktywne protokoły rutowania |
| `show ip interface brief` | Stan interfejsów |
| `show ip ospf neighbor` | Sąsiedzi OSPF i ich role |
| `show ip ospf interface fa0/0` | Szczegóły OSPF dla interfejsu |
| `show ip ospf virtual-links` | Stan virtual linków |
| `show ip ospf border-routers` | Routery ABR/ASBR |
| `show ip route ospf` | Tylko trasy OSPF |
| `show interfaces tunnel0` | Stan interfejsu tunelowego |
| `ping X.X.X.X source Y.Y.Y.Y` | Ping z wybranego interfejsu |
| `traceroute X.X.X.X` | Śledzenie ścieżki |
| `debug ip ospf events` | Debugowanie zdarzeń OSPF |
| `debug ip ospf adjacency` | Debugowanie sąsiedztwa OSPF |
| `undebug all` | Wyłączenie debugowania |

---

> **Tip końcowy:** Zawsze po wprowadzeniu zmian poczekaj ~30-60 sekund na konwergencję protokołów rutowania, zanim zaczniesz diagnozować. Protokoły potrzebują czasu na wymianę informacji o trasach.


# Laboratorium 010 – IP Multicast: Notatka krok po kroku

> **Cel laboratorium:** Poznanie protokołu IGMP, funkcji IGMP Snooping w przełącznikach, oraz rutowania multicast w trybach PIM Dense Mode i PIM Sparse Mode.

---

## 🖥️ Praca na 2 fizycznych komputerach – jak się przełączać?

W laboratorium masz do dyspozycji **dwa fizyczne PC** (nazwijmy je **FizycznyPC-A** i **FizycznyPC-B**). Symulowane urządzenia (Switch, Router, PC1, PC2) działają w środowisku **Cisco Packet Tracer** lub **GNS3** – oprogramowanie to uruchamiasz na jednym z fizycznych komputerów.

### Podział pracy

| Fizyczny komputer | Co na nim robisz |
|-------------------|-----------------|
| **FizycznyPC-A** | Otwierasz Packet Tracer / GNS3, konfigurujesz Switch i Routery przez konsolę w symulatorze. Możesz też uruchomić VLC jako nadawcę (PC1 w symulacji). |
| **FizycznyPC-B** | Uruchamiasz VLC jako odbiorca (PC2 w symulacji) lub Wireshark do obserwacji ruchu. |

> 💡 **Jeśli robisz całe lab na jednym fizycznym PC:** uruchom dwie instancje VLC (jedną jako nadawcę, drugą jako odbiorcę) i Wireshark – wszystko działa lokalnie w ramach symulatora.

### Jak przełączać się między urządzeniami w Packet Tracer / GNS3?

**W Cisco Packet Tracer:**
- Kliknij ikonę urządzenia (Switch, Router) na schemacie → otwiera się okno z zakładkami **CLI** (linia komend) i **Config**.
- Każde urządzenie ma **osobne okno CLI** – możesz mieć kilka otwartych jednocześnie.
- Przełączasz się klikając w odpowiednie okno na ekranie.

**W GNS3:**
- Kliknij prawym przyciskiem myszy na urządzenie → **Console** → otwiera się terminal.
- Możesz mieć wiele terminali otwartych obok siebie.

### Jak uruchomić VLC na fizycznym PC (jako PC1 lub PC2)?

Jeśli lab odbywa się na **prawdziwym sprzęcie** (nie w symulatorze):
- **FizycznyPC-A** = PC1 → uruchamiasz komendę nadawcy VLC
- **FizycznyPC-B** = PC2 → uruchamiasz komendę odbiorcy VLC
- Oba PC są podłączone do fizycznego Switcha i Routera

### Legenda oznaczeń w tej notatce

Każdy krok jest oznaczony, kto go wykonuje:

| Oznaczenie | Gdzie wpisujesz komendę |
|------------|------------------------|
| `🖥️ PC1` | Na komputerze będącym nadawcą (FizycznyPC-A lub okno PC1 w symulatorze) |
| `🖥️ PC2` | Na komputerze będącym odbiorcą (FizycznyPC-B lub okno PC2 w symulatorze) |
| `🔀 Switch` | W oknie CLI przełącznika (w Packet Tracer kliknij Switch → CLI) |
| `📡 Router` | W oknie CLI rutera (w Packet Tracer kliknij Router → CLI) |
| `🔍 Wireshark` | Program uruchamiasz na PC1 lub PC2 (dowolnym) |

---

## Podstawy teoretyczne – czym jest IP Multicast?

- **Unicast** – jeden nadawca, jeden odbiorca (np. zwykła strona www).
- **Broadcast** – jeden nadawca, wszyscy w sieci.
- **Multicast** – jeden nadawca, wybrana grupa odbiorców. Oszczędza przepustowość – strumień jest wysyłany tylko raz, a sieć sama rozgałęzia go do zainteresowanych.

Adresy multicast należą do zakresu **224.0.0.0 – 239.255.255.255** (klasa D).

Protokoły używane w tym lab:
- **IGMP** *(Internet Group Management Protocol)* – hosty rejestrują się do grup multicast w ruterze.
- **PIM** *(Protocol Independent Multicast)* – rutery ustalają między sobą ścieżki dla ruchu multicast.

---

## Zadanie A – IGMP Snooping w przełączniku Ethernet

### Topologia sieci

```
   PC1 ──┐
         ├── Switch Cisco ── Router Cisco
   PC2 ──┘
```

- **PC1** – nadawca strumienia multicast (źródło)
- **PC2** – odbiorca strumienia multicast
- **Switch** – przełącznik z funkcją IGMP Snooping
- **Router** – pełni rolę **IGMP Querier** (odpytuje hosty o przynależność do grup multicast)

### Przykładowa adresacja IP

| Urządzenie | Interfejs | Adres IP         |
|------------|-----------|------------------|
| PC1        | eth0      | 192.168.1.10/24  |
| PC2        | eth0      | 192.168.1.20/24  |
| Router     | fa 0/0    | 192.168.1.1/24   |

### Krok 1 – Włącz IGMP Snooping na przełączniku

> **🔀 Switch** – wpisz w oknie CLI przełącznika (Packet Tracer: kliknij Switch → zakładka CLI)

```
Switch> enable
Switch# configure terminal
Switch(config)# ip igmp snooping
Switch(config)# end
```

**Co to robi?** IGMP Snooping powoduje, że przełącznik „podsłuchuje" wiadomości IGMP wymieniane między hostami a ruterem. Dzięki temu wie, na których portach są odbiorcy danej grupy multicast i wysyła ruch tylko tam – a nie na wszystkie porty (jak to byłoby w przypadku broadcastu).

**Bez IGMP Snooping:** ruch multicast jest traktowany jak broadcast – dociera do wszystkich portów, nawet do tych, które nie są zainteresowane.

### Krok 2 – Skonfiguruj Router jako IGMP Querier

> **📡 Router** – wpisz w oknie CLI rutera (Packet Tracer: kliknij Router → zakładka CLI)

Ruter musi być podłączony do tego samego segmentu sieci co PC-ty. Następnie uruchom na nim rutowanie multicast i PIM na interfejsie:

```
Router> enable
Router# configure terminal
Router(config)# ip multicast-routing
Router(config)# interface fa 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip pim dense-mode
Router(config-if)# no shutdown
Router(config-if)# end
```

**Co to robi:**
- `enable` – wchodzi w tryb uprzywilejowany (z `>` na `#`).
- `configure terminal` – wchodzi w tryb konfiguracji globalnej.
- `ip multicast-routing` – włącza globalne rutowanie multicast na ruterze.
- `ip pim dense-mode` – włącza protokół PIM na interfejsie (tutaj tylko do obsługi IGMP Querier).
- Ruter automatycznie staje się **IGMP Querier** i regularnie pyta hosty: *„Kto chce odbierać ruch multicast?"*

### Krok 3 – Skonfiguruj adresy IP na PC1 i PC2

> **🖥️ PC1** (FizycznyPC-A lub okno PC1 w symulatorze)

W Packet Tracer: kliknij PC1 → zakładka **Desktop** → **IP Configuration** i ustaw:
- IP: `192.168.1.10`
- Maska: `255.255.255.0`
- Brama: `192.168.1.1`

Na prawdziwym systemie Windows (cmd jako Administrator):
```cmd
netsh interface ip set address "Ethernet" static 192.168.1.10 255.255.255.0 192.168.1.1
```

> **🖥️ PC2** (FizycznyPC-B lub okno PC2 w symulatorze) – analogicznie:
- IP: `192.168.1.20`
- Maska: `255.255.255.0`
- Brama: `192.168.1.1`

### Krok 4 – Uruchom źródło multicast na PC1 (VLC Player)

> **🖥️ PC1** – uruchom w wierszu poleceń (cmd) na FizycznymPC-A

```cmd
vlc.exe C:\video\film.mp4 :sout=#rtp{dst=224.1.1.1,port=5004,mux=ts,ttl=10} :sout-all :sout-keep --repeat
```

Zamień `C:\video\film.mp4` na rzeczywistą ścieżkę do pliku wideo.

**Wyjaśnienie parametrów:**
| Parametr | Znaczenie |
|----------|-----------|
| `plik` | Ścieżka do pliku wideo/audio |
| `dst=224.1.1.1` | Docelowy adres grupy multicast |
| `port=5004` | Port UDP, na którym wysyłany jest strumień |
| `mux=ts` | Format strumienia: MPEG2 Transport Stream (TS) – dedykowany do transmisji przez sieć |
| `ttl=10` | Time To Live – ile razy datagram może przejść przez rutery; **musi być > 1**, bo domyślna wartość 1 powoduje, że ruch nie przechodzi przez żaden ruter |
| `:sout-all` | Wysyłaj do wszystkich wyjść |
| `--repeat` | Zapętlaj plik |

> ⚠️ **Uwaga:** TTL > 1 jest konieczne, gdy ruch ma przejść przez rutery. Domyślna wartość TTL=1 to absolutne minimum – przy niej datagram „ginie" na pierwszym ruterze.

### Krok 5 – Uruchom odbiorcę na PC2 (VLC Player)

> **🖥️ PC2** – uruchom w wierszu poleceń (cmd) na FizycznymPC-B

```cmd
vlc.exe rtp://224.1.1.1:5004
```

PC2 dołącza do grupy multicast `224.1.1.1` i odbiera strumień RTP na porcie 5004. Jeśli wszystko działa poprawnie, powinno pojawić się okno odtwarzacza z obrazem.

### Krok 6 – Sprawdź stan IGMP Snooping na przełączniku

> **🔀 Switch** – wróć do okna CLI przełącznika

```
Switch# show ip igmp snooping
Switch# show ip igmp snooping group
Switch# show ip igmp snooping querier
Switch# show ip igmp snooping mrouter
```

**Co pokaże każda komenda:**
- `show ip igmp snooping` – ogólny stan funkcji (czy włączona, dla których VLANów).
- `show ip igmp snooping group` – lista grup multicast i portów, do których są przypisani odbiorcy.
- `show ip igmp snooping querier` – informacja o ruterze IGMP Querier w segmencie.
- `show ip igmp snooping mrouter` – port, przez który podłączony jest ruter multicast.

### Krok 7 – Obserwacja w Wireshark

> **🔍 Wireshark** – uruchom na FizycznymPC-A lub FizycznymPC-B (najlepiej PC2, żeby widzieć przychodzący ruch)

Uruchom Wireshark, wybierz właściwy interfejs sieciowy i zastosuj filtr:
```
igmp or udp.port == 5004
```

Zwróć uwagę na:
- **Ramki IGMP** – wiadomości Membership Query (ruter pyta), Membership Report (host odpowiada), Leave (host opuszcza grupę).
- **Datagramy IP multicast** – docelowy adres IP to adres grupy (np. 224.1.1.1), a adres MAC to specjalny adres multicast Ethernet (postać `01:00:5E:xx:xx:xx`).

### Krok 8 – Wyłącz odbiorcę i obserwuj zachowanie

> **🖥️ PC2** – zamknij VLC (odbiorca przestaje być członkiem grupy)

> **🔀 Switch** – wróć do CLI i przetestuj oba scenariusze:

**Z wyłączonym IGMP Snooping:**
```
Switch(config)# no ip igmp snooping
```
Ruch multicast trafia na wszystkie porty (jak broadcast) – Wireshark na PC2 nadal widzi pakiety mimo zamkniętego VLC.

**Z włączonym IGMP Snooping:**
```
Switch(config)# ip igmp snooping
```
Ruch multicast zostaje ograniczony tylko do portu rutera (nikt inny nie jest zarejestrowany) – Wireshark na PC2 nie widzi pakietów multicast.

> **📡 Router** – wyczyść grupy IGMP:
```
Router# clear ip igmp group
```

---

## Zadanie B – PIM Dense Mode (tryb push)

### Zasada działania

**Dense Mode = „push mode"** – ruter zakłada, że wszystkie węzły chcą odbierać ruch multicast i rozgłasza go wszędzie. Rutery, które nie potrzebują ruchu, wysyłają komunikat **Prune** (wstrzymaj). Mniej wydajny, ale prostszy w konfiguracji.

### Topologia sieci

```
PC1 ── Router1 ── (sieć 200.200.200.0/24) ── Router2 ── (sieć 200.200.201.0/24) ── Router3 ── PC2
         |                                      |                                        |
  200.200.210.0/24                              |                                 200.200.211.0/24
         |                              (środkowy, łączy obie strony)
```

Szczegółowa adresacja:

| Router | Interfejs | Adres IP            | Sieć                   |
|--------|-----------|---------------------|------------------------|
| R1     | fa 0/0    | 200.200.200.1/24    | łącze do R2            |
| R1     | fa 0/1    | 200.200.210.1/24    | sieć PC1               |
| R2     | fa 0/0    | 200.200.200.2/24    | łącze do R1            |
| R2     | fa 0/1    | 200.200.201.1/24    | łącze do R3            |
| R3     | fa 0/0    | 200.200.201.2/24    | łącze do R2            |
| R3     | fa 0/1    | 200.200.211.1/24    | sieć PC2               |

### Jak podłączyć sprzęt

1. PC1 podłącz do fa 0/1 Router1 (lub do switcha, który jest pod R1).
2. Router1 fa 0/0 podłącz do Router2 fa 0/0 (łącze bezpośrednie lub przez switch).
3. Router2 fa 0/1 podłącz do Router3 fa 0/0.
4. PC2 podłącz do fa 0/1 Router3.

### Konfiguracja Router1

> **📡 Router1** – otwórz CLI Router1 w symulatorze

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.200.1 255.255.255.0
 ip pim dense-mode
 no shutdown

interface fa 0/1
 ip address 200.200.210.1 255.255.255.0
 ip pim dense-mode
 no shutdown

router eigrp 100
 network 200.200.200.0
 network 200.200.210.0
end
```

### Konfiguracja Router2 (środkowy)

> **📡 Router2** – otwórz CLI Router2 w symulatorze (osobne okno)

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.200.2 255.255.255.0
 ip pim dense-mode
 no shutdown

interface fa 0/1
 ip address 200.200.201.1 255.255.255.0
 ip pim dense-mode
 no shutdown

router eigrp 100
 network 200.200.200.0
 network 200.200.201.0
end
```

### Konfiguracja Router3

> **📡 Router3** – otwórz CLI Router3 w symulatorze (osobne okno)

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.201.2 255.255.255.0
 ip pim dense-mode
 no shutdown

interface fa 0/1
 ip address 200.200.211.1 255.255.255.0
 ip pim dense-mode
 no shutdown

router eigrp 100
 network 200.200.201.0
 network 200.200.211.0
end
```

**Wyjaśnienie poleceń:**
- `ip multicast-routing` – globalne włączenie obsługi multicast w ruterze.
- `ip pim dense-mode` na interfejsie – włącza PIM DM na tym interfejsie; ruter będzie aktywnie uczestniczył w rozgłaszaniu i przycinaniu ruchu multicast.
- `router eigrp 100` + `network` – rutowanie unicast (konieczne, żeby rutery „wiedziały" o sobie nawzajem i mogły zestawić ścieżki multicast).

### Uruchom transmisję multicast

> **🖥️ PC1** (FizycznyPC-A) – nadawca:
```cmd
vlc.exe C:\video\film.mp4 :sout=#rtp{dst=224.1.1.1,port=5004,mux=ts,ttl=10} :sout-all :sout-keep --repeat
```

> **🖥️ PC2** (FizycznyPC-B) – odbiorca:
```cmd
vlc.exe rtp://224.1.1.1:5004
```

### Diagnostyka PIM Dense Mode

> **📡 Router1 / Router2 / Router3** – te komendy wpisuj na każdym ruterze osobno, żeby porównać wyniki

```
Router# show ip mroute
```
Pokazuje tablicę routingu multicast – wpisy (S,G) czyli źródło i grupę.

```
Router# show ip igmp groups
```
Grupy multicast, do których dołączyli hosty w sieciach bezpośrednio podłączonych.

```
Router# show ip mroute summary
Router# show ip mroute active
Router# show ip mroute count
```
Skróty i statystyki ruchu multicast.

```
Router# show ip mroute pruned
```
Pokazuje strumienie wstrzymane (Prune) – gdy ostatni odbiorca opuścił grupę.

### Symulacja fikcyjnego źródła multicast

> **📡 Router1** – dodaj fikcyjne źródło:

```
Router1(config)# interface fa 0/0
Router1(config-if)# ip igmp join-group 230.200.200.1
```

> **📡 Router2** – sprawdź dostępność:
```
Router2# ping 230.200.200.1
```

### Czyszczenie danych multicast

> **📡 Router1 / Router2 / Router3** – wykonaj na każdym ruterze przed testem w odwrotnym kierunku:

```
Router# clear ip mroute *
Router# clear ip igmp groups
```

Użyj do resetowania tablicy, gdy chcesz przetestować transmisję w odwrotnym kierunku (zamiana ról PC1 i PC2):
- **PC2** (FizycznyPC-B) staje się nadawcą – uruchamiasz tam komendę VLC nadawcy
- **PC1** (FizycznyPC-A) staje się odbiorcą – uruchamiasz tam komendę VLC odbiorcy

---

## Zadanie C – PIM Sparse Mode (tryb pull)

### Zasada działania

**Sparse Mode = „pull mode"** – odwrotność Dense Mode. Ruter **nie** rozgłasza ruchu wszędzie. Zamiast tego:

1. Jeden ruter pełni rolę **Rendezvous Point (RP)** – centralnego punktu spotkań nadawców i odbiorców.
2. Nadawca rejestruje się w RP.
3. Odbiorca (przez IGMP) informuje swój lokalny ruter, że chce dołączyć do grupy.
4. Lokalny ruter zgłasza się do RP i pobiera ruch dopiero wtedy, gdy jest potrzebny.

**Zalety:** Bardziej efektywny przy rozległych sieciach z rzadko rozmieszczonymi odbiorcami.

### Wymaganie dodatkowe: Rendezvous Point

Każdy ruter **musi znać adres IP rutera RP** – nawet on sam (jeśli jest RP).

W tym przykładzie RP to **Router2** (adres `200.200.200.2`).

### Konfiguracja Router1

> **📡 Router1** – CLI Router1

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.200.1 255.255.255.0
 ip pim sparse-mode
 no shutdown

interface fa 0/1
 ip address 200.200.210.1 255.255.255.0
 ip pim sparse-mode
 no shutdown

ip pim rp-address 200.200.200.2

router eigrp 100
 network 200.200.200.0
 network 200.200.210.0
end
```

### Konfiguracja Router2 (RP – Rendezvous Point)

> **📡 Router2** – CLI Router2 (ten ruter jest RP – wskazuje sam siebie jako `rp-address`)

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.200.2 255.255.255.0
 ip pim sparse-mode
 no shutdown

interface fa 0/1
 ip address 200.200.201.1 255.255.255.0
 ip pim sparse-mode
 no shutdown

ip pim rp-address 200.200.200.2

router eigrp 100
 network 200.200.200.0
 network 200.200.201.0
end
```

### Konfiguracja Router3

> **📡 Router3** – CLI Router3

```
Router> enable
Router# configure terminal
ip multicast-routing

interface fa 0/0
 ip address 200.200.201.2 255.255.255.0
 ip pim sparse-mode
 no shutdown

interface fa 0/1
 ip address 200.200.211.1 255.255.255.0
 ip pim sparse-mode
 no shutdown

ip pim rp-address 200.200.200.2

router eigrp 100
 network 200.200.201.0
 network 200.200.211.0
end
```

**Wyjaśnienie kluczowej komendy:**
```
ip pim rp-address 200.200.200.2
```
Wskazuje adres Rendezvous Point. Musi być skonfigurowane na **każdym** ruterze w sieci, w tym na samym RP.

### Uruchom transmisję (tak samo jak w Dense Mode)

> **🖥️ PC1** (FizycznyPC-A) – nadawca:
```cmd
vlc.exe C:\video\film.mp4 :sout=#rtp{dst=224.1.1.1,port=5004,mux=ts,ttl=10} :sout-all :sout-keep --repeat
```

> **🖥️ PC2** (FizycznyPC-B) – odbiorca:
```cmd
vlc.exe rtp://224.1.1.1:5004
```

### Diagnostyka PIM Sparse Mode

> **📡 Router1 / Router2 / Router3** – sprawdzaj na każdym ruterze

```
Router# show ip pim rp
```
Pokazuje aktualnie zarejestrowane Rendezvous Points i grupy, do których są przypisane.

```
Router# show ip pim rp mapping
```
Mapowanie grup multicast na Rendezvous Points.

```
Router# show ip pim neighbor
```
Lista sąsiadów PIM (ruterów, z którymi nawiązano relację PIM).

---

## Podsumowanie – różnice między trybami PIM

| Cecha | Dense Mode | Sparse Mode |
|-------|------------|-------------|
| Zasada | Push – rozgłasza wszędzie, tnie niepotrzebne | Pull – dostarcza tylko tam, gdzie potrzeba |
| Rendezvous Point | Nie wymagany | Wymagany |
| Ruch w sieci | Większy (initial flood) | Mniejszy |
| Złożoność konfiguracji | Prosta | Wymaga wskazania RP |
| Optymalne zastosowanie | Gęsta sieć, wielu odbiorców | Rozproszona sieć, niewielu odbiorców |

---

## Typowe problemy i rozwiązania

| Problem | Możliwa przyczyna | Rozwiązanie |
|---------|-------------------|-------------|
| Odbiorca nie widzi strumienia | TTL=1 w VLC | Dodaj `ttl=10` do komendy VLC |
| Ruch multicast idzie na wszystkie porty switcha | IGMP Snooping wyłączony | `ip igmp snooping` na switchu |
| Rutery nie widzą się w PIM | Brak rutowania unicast | Skonfiguruj EIGRP/RIP/static |
| Sparse mode nie działa | Brak konfiguracji RP | `ip pim rp-address` na każdym ruterze |
| Tablica mroute pusta | PIM nie włączony na interfejsie | `ip pim dense/sparse-mode` na każdym int |

# SIECI KOMPUTEROWE – LAB 076
## Cisco IPv6 – Przewodnik krok po kroku (per urządzenie)

---

> ### 🖥️ Legenda stanowiska
>
> | Symbol | Co to jest |
> |--------|-----------|
> | **PC-A** | Twój komputer (lewa strona) |
> | **R-ALFA** | Twój ruter (lewy) – podłączony do PC-A i do R-BETA |
> | **R-BETA** | Drugi ruter (prawy) – podłączony do R-ALFA i PC-B |
> | **PC-B** | Drugi komputer (prawa strona) |
>
> Połączenie fizyczne: `PC-A — Fa0/1 — [R-ALFA] — Fa0/0 — Serial/Fa — [R-BETA] — Fa0/1 — PC-B`

---

## Schemat adresacji (własna, różna od lab)

| Urządzenie | Interfejs | Adres IPv6 | Uwagi |
|---|---|---|---|
| R-ALFA | Loopback 0 | `CAFE:1::/64 eui-64` | EUI-64 |
| R-ALFA | Fa0/0 (do R-BETA) | `BEEF:A:B::1/64` | łącze WAN |
| R-ALFA | Fa0/1 (do PC-A) | `CAFE:1::1/64` | sieć LAN A |
| R-BETA | Loopback 0 | `DEAD:2::/64 eui-64` | EUI-64 |
| R-BETA | Fa0/0 (do R-ALFA) | `BEEF:A:B::2/64` | łącze WAN |
| R-BETA | Fa0/1 (do PC-B) | `DEAD:2::1/64` | sieć LAN B |

---

---

# ZADANIE A – Konfigurowanie interfejsów IPv6

---

## 🟦 R-ALFA – Zadanie A

### Krok 1 – Wejdź w tryb konfiguracji

```
Router> enable
Router# configure terminal
Router(config)# hostname R-ALFA
```

### Krok 2 – Sprawdź obsługę IPv6 w ruterze

```
R-ALFA# show ipv6 ?
```

> ✅ Jeśli pojawi się lista opcji – ruter obsługuje IPv6. Kontynuuj.

### Krok 3 – Stwórz interfejs Loopback z adresem EUI-64

```
R-ALFA(config)# interface loopback 0
R-ALFA(config-if)# ipv6 address CAFE:1::/64 eui-64
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit
```

> 💡 Młodsze 64 bity adresu są generowane automatycznie z MAC karty sieciowej. Sprawdź co zostało wygenerowane:

```
R-ALFA# show ipv6 interface loopback 0
R-ALFA# show ipv6 int brief
```

### Krok 4 – Skonfiguruj interfejs do drugiego rutera (Fa0/0)

```
R-ALFA(config)# interface fastethernet 0/0
R-ALFA(config-if)# ipv6 address BEEF:A:B::1/64
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit
```

### Krok 5 – Skonfiguruj interfejs do PC-A (Fa0/1)

```
R-ALFA(config)# interface fastethernet 0/1
R-ALFA(config-if)# ipv6 address CAFE:1::1/64
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit
```

### Krok 6 – Statyczna reguła rutowania do sieci R-BETA

```
R-ALFA(config)# ipv6 route DEAD:2::/64 BEEF:A:B::2
R-ALFA(config)# end
```

> Kierujemy ruch do sieci `DEAD:2::/64` (LAN B) przez adres `BEEF:A:B::2` (interfejs R-BETA).

### Krok 7 – Diagnostyka

```
R-ALFA# show ipv6 route
R-ALFA# ping ipv6 BEEF:A:B::2
R-ALFA# ping ipv6 DEAD:2::1
R-ALFA# traceroute ipv6 DEAD:2::1
R-ALFA# debug ipv6 icmp
```

---

## 🟧 R-BETA – Zadanie A

### Krok 1 – Wejdź w tryb konfiguracji

```
Router> enable
Router# configure terminal
Router(config)# hostname R-BETA
```

### Krok 2 – Stwórz interfejs Loopback z adresem EUI-64

```
R-BETA(config)# interface loopback 0
R-BETA(config-if)# ipv6 address DEAD:2::/64 eui-64
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit
```

```
R-BETA# show ipv6 interface loopback 0
R-BETA# show ipv6 int brief
```

### Krok 3 – Skonfiguruj interfejs do R-ALFA (Fa0/0)

```
R-BETA(config)# interface fastethernet 0/0
R-BETA(config-if)# ipv6 address BEEF:A:B::2/64
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit
```

### Krok 4 – Skonfiguruj interfejs do PC-B (Fa0/1)

```
R-BETA(config)# interface fastethernet 0/1
R-BETA(config-if)# ipv6 address DEAD:2::1/64
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit
```

### Krok 5 – Statyczna reguła rutowania do sieci R-ALFA

```
R-BETA(config)# ipv6 route CAFE:1::/64 BEEF:A:B::1
R-BETA(config)# end
```

### Krok 6 – Diagnostyka

```
R-BETA# show ipv6 route
R-BETA# ping ipv6 BEEF:A:B::1
R-BETA# ping ipv6 CAFE:1::1
R-BETA# traceroute ipv6 CAFE:1::1
```

---

---

# ZADANIE B – Rutowanie RIPng (IPv6)

> Najpierw usuń statyczne trasy z Zadania A, żeby RIPng mógł nimi zarządzać.

---

## 🟦 R-ALFA – Zadanie B

### Krok 1 – Usuń statyczne trasy z zadania A

```
R-ALFA(config)# no ipv6 route DEAD:2::/64 BEEF:A:B::2
```

### Krok 2 – Włącz rutowanie IPv6

```
R-ALFA(config)# ipv6 unicast-routing
```

### Krok 3 – Uruchom proces RIPng

```
R-ALFA(config)# ipv6 router rip KRAKEN
R-ALFA(config-rtr)# exit
```

> `KRAKEN` to nazwa procesu – musi być **taka sama** na R-ALFA i R-BETA.

### Krok 4 – Aktywuj RIPng na interfejsach

```
R-ALFA(config)# interface fastethernet 0/0
R-ALFA(config-if)# ipv6 rip KRAKEN enable
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit

R-ALFA(config)# interface fastethernet 0/1
R-ALFA(config-if)# ipv6 rip KRAKEN enable
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit

R-ALFA(config)# interface loopback 0
R-ALFA(config-if)# ipv6 rip KRAKEN enable
R-ALFA(config-if)# exit
R-ALFA(config)# end
```

### Krok 5 – Weryfikacja

```
R-ALFA# show ipv6 rip
R-ALFA# show ipv6 route
R-ALFA# show ipv6 interface fastethernet 0/0
R-ALFA# clear ipv6 route *
```

---

## 🟧 R-BETA – Zadanie B

### Krok 1 – Usuń statyczne trasy

```
R-BETA(config)# no ipv6 route CAFE:1::/64 BEEF:A:B::1
```

### Krok 2 – Włącz rutowanie IPv6

```
R-BETA(config)# ipv6 unicast-routing
```

### Krok 3 – Uruchom proces RIPng (ta sama nazwa!)

```
R-BETA(config)# ipv6 router rip KRAKEN
R-BETA(config-rtr)# exit
```

### Krok 4 – Aktywuj RIPng na interfejsach

```
R-BETA(config)# interface fastethernet 0/0
R-BETA(config-if)# ipv6 rip KRAKEN enable
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit

R-BETA(config)# interface fastethernet 0/1
R-BETA(config-if)# ipv6 rip KRAKEN enable
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit

R-BETA(config)# interface loopback 0
R-BETA(config-if)# ipv6 rip KRAKEN enable
R-BETA(config-if)# exit
R-BETA(config)# end
```

### Krok 5 – Weryfikacja

```
R-BETA# show ipv6 rip
R-BETA# show ipv6 route
R-BETA# ping ipv6 CAFE:1::1
R-BETA# ping ipv6 CAFE:1::1 source DEAD:2::1
```

---

---

# ZADANIE C – OSPFv3 i EIGRP (IPv6)

---

## 🟦 R-ALFA – Zadanie C (OSPFv3)

### Krok 1 – Wyłącz RIPng

```
R-ALFA(config)# no ipv6 router rip KRAKEN
```

### Krok 2 – Uruchom OSPFv3

```
R-ALFA(config)# ipv6 router ospf 20
R-ALFA(config-rtr)# router-id 10.10.10.1
R-ALFA(config-rtr)# exit
```

> ⚠️ `router-id` musi być **unikalne** – tu `10.10.10.1`, na R-BETA użyj `10.10.10.2`

### Krok 3 – Przypisz interfejsy do OSPFv3

```
R-ALFA(config)# interface fastethernet 0/0
R-ALFA(config-if)# ipv6 ospf 20 area 0.0.0.0
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit

R-ALFA(config)# interface fastethernet 0/1
R-ALFA(config-if)# ipv6 ospf 20 area 0.0.0.0
R-ALFA(config-if)# no shutdown
R-ALFA(config-if)# exit

R-ALFA(config)# interface loopback 0
R-ALFA(config-if)# ipv6 ospf 20 area 0.0.0.0
R-ALFA(config-if)# exit
R-ALFA(config)# end
```

### Krok 4 – Weryfikacja OSPFv3

```
R-ALFA# show ipv6 protocols
R-ALFA# show ipv6 route
R-ALFA# show ipv6 ospf neighbor
R-ALFA# show ipv6 ospf interface
R-ALFA# show ipv6 ospf database
R-ALFA# debug ipv6 ospf hello
R-ALFA# debug ipv6 ospf packet
```

---

## 🟧 R-BETA – Zadanie C (OSPFv3 + ABR)

### Krok 1 – Wyłącz RIPng

```
R-BETA(config)# no ipv6 router rip KRAKEN
```

### Krok 2 – Uruchom OSPFv3

```
R-BETA(config)# ipv6 router ospf 20
R-BETA(config-rtr)# router-id 10.10.10.2
R-BETA(config-rtr)# exit
```

### Krok 3 – Przypisz interfejsy do OSPFv3 area 0

```
R-BETA(config)# interface fastethernet 0/0
R-BETA(config-if)# ipv6 ospf 20 area 0.0.0.0
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit

R-BETA(config)# interface fastethernet 0/1
R-BETA(config-if)# ipv6 ospf 20 area 0.0.0.0
R-BETA(config-if)# no shutdown
R-BETA(config-if)# exit
```

### Krok 4 – Stwórz drugi Loopback i dodaj do area 1 (R-BETA jako ABR)

```
R-BETA(config)# interface loopback 1
R-BETA(config-if)# ipv6 address DEAD:9::1/64
R-BETA(config-if)# no shutdown
R-BETA(config-if)# ipv6 ospf 20 area 1
R-BETA(config-if)# exit
R-BETA(config)# end
```

### Krok 5 – Weryfikacja (z R-ALFA sprawdź ABR)

```
R-BETA# show ipv6 ospf neighbor
R-BETA# show ipv6 ospf database
R-ALFA# show ipv6 ospf border-routers
R-ALFA# show ipv6 route
```

---

## 🟦🟧 Opcjonalnie: EIGRP dla IPv6 (tylko na ruterach szkieletowych)

> ⚠️ Dostępne tylko na: Cisco 3640, 3660, 3725, 3745, 3845, 7100, 7200VXR, 7300, 7400, 6500

### Na R-ALFA:

```
R-ALFA(config)# no ipv6 router ospf 20
R-ALFA(config)# ipv6 router eigrp 777
R-ALFA(config-rtr)# eigrp router-id 1
R-ALFA(config-rtr)# exit
R-ALFA(config)# interface fastethernet 0/0
R-ALFA(config-if)# ipv6 eigrp 777
R-ALFA(config-if)# exit
R-ALFA(config)# interface fastethernet 0/1
R-ALFA(config-if)# ipv6 eigrp 777
R-ALFA(config-if)# exit
R-ALFA(config)# end
```

### Na R-BETA:

```
R-BETA(config)# no ipv6 router ospf 20
R-BETA(config)# ipv6 router eigrp 777
R-BETA(config-rtr)# eigrp router-id 2
R-BETA(config-rtr)# exit
R-BETA(config)# interface fastethernet 0/0
R-BETA(config-if)# ipv6 eigrp 777
R-BETA(config-if)# exit
R-BETA(config)# interface fastethernet 0/1
R-BETA(config-if)# ipv6 eigrp 777
R-BETA(config-if)# exit
R-BETA(config)# end
```

---

---

# ZADANIE D – Tunelowanie IPv6 w IPv4

## Topologia i układ fizyczny

```
[PC-A]--[R1]--IPv6--[R2]====TUNEL=====[R4]--IPv6--[R5]--[PC-B]
                       \              /
                        \---[R3]----/
                          (tylko IPv4)
```

> ### 🖥️ Który komputer obsługuje który ruter:
> - **Twoje stanowisko (lewe):** konfiguracja R1 i R2, udział w R3 wspólnie z sąsiadem
> - **Stanowisko sąsiednie (prawe):** konfiguracja R4 i R5
> - **R3** – ruter środkowy, konfigurowany wspólnie z sąsiadującym zespołem

---

## Schemat adresacji Zadanie D

| Ruter | Interfejs | Adres | Protokół |
|---|---|---|---|
| R1 | Fa0/0 → R2 | `ACDC:2::9/64` | IPv6 / RIPng |
| R2 | Fa0/0 → R3 | `172.16.10.2/24` | IPv4 / OSPF |
| R2 | Fa0/1 → R1 | `ACDC:2::1/64` | IPv6 / RIPng |
| R2 | Tunnel0 | `FEED:3::1/64` | IPv6 / RIPng |
| R3 | Fa0/0 → R2 | `172.16.10.1/24` | IPv4 / OSPF |
| R3 | Fa0/1 → R4 | `172.16.20.1/24` | IPv4 / OSPF |
| R4 | Fa0/0 → R3 | `172.16.20.2/24` | IPv4 / OSPF |
| R4 | Fa0/1 → R5 | `BABE:1::1/64` | IPv6 / RIPng |
| R4 | Tunnel0 | `FEED:3::2/64` | IPv6 / RIPng |
| R5 | Fa0/0 → R4 | `BABE:1::9/64` | IPv6 / RIPng |

---

## 🟩 R1 – Konfiguracja (ruter tylko IPv6, lewa strona)

```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 router rip VIPER
R1(config-rtr)# exit
R1(config)# interface fastethernet 0/0
R1(config-if)# no ip address
R1(config-if)# ipv6 address ACDC:2::9/64
R1(config-if)# ipv6 rip VIPER enable
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# end
```

---

## 🟦 R2 – Konfiguracja (bramka IPv6 + tunel)

```
Router> enable
Router# configure terminal
Router(config)# hostname R2
R2(config)# ipv6 unicast-routing
R2(config)# ipv6 router rip VIPER
R2(config-rtr)# exit

R2(config)# interface fastethernet 0/0
R2(config-if)# ip address 172.16.10.2 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface fastethernet 0/1
R2(config-if)# no ip address
R2(config-if)# ipv6 address ACDC:2::1/64
R2(config-if)# ipv6 rip VIPER enable
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# router ospf 5
R2(config-router)# log-adjacency-changes
R2(config-router)# network 172.16.10.0 0.0.0.255 area 0
R2(config-router)# router-id 172.16.10.2
R2(config-router)# exit

R2(config)# interface tunnel 0
R2(config-if)# no ip address
R2(config-if)# ipv6 address FEED:3::1/64
R2(config-if)# ipv6 rip VIPER enable
R2(config-if)# tunnel source fastethernet 0/0
R2(config-if)# tunnel destination 172.16.20.2
R2(config-if)# tunnel mode ipv6ip
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# end
```

---

## 🟫 R3 – Konfiguracja (ruter tylko IPv4, środkowy)

> Konfigurowany **wspólnie z sąsiednim zespołem** – to ruter pośredniczący.

```
Router> enable
Router# configure terminal
Router(config)# hostname R3
R3(config)# ip subnet-zero
R3(config)# ip classless

R3(config)# interface fastethernet 0/0
R3(config-if)# ip address 172.16.10.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface fastethernet 0/1
R3(config-if)# ip address 172.16.20.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# router ospf 5
R3(config-router)# log-adjacency-changes
R3(config-router)# network 172.16.10.0 0.0.0.255 area 0
R3(config-router)# network 172.16.20.0 0.0.0.255 area 0
R3(config-router)# router-id 172.16.10.1
R3(config-router)# exit
R3(config)# end
```

---

## 🟧 R4 – Konfiguracja (bramka IPv6 + tunel, strona sąsiada)

```
Router> enable
Router# configure terminal
Router(config)# hostname R4
R4(config)# ipv6 unicast-routing
R4(config)# ipv6 router rip VIPER
R4(config-rtr)# exit

R4(config)# interface fastethernet 0/0
R4(config-if)# ip address 172.16.20.2 255.255.255.0
R4(config-if)# no shutdown
R4(config-if)# exit

R4(config)# interface fastethernet 0/1
R4(config-if)# no ip address
R4(config-if)# ipv6 address BABE:1::1/64
R4(config-if)# ipv6 rip VIPER enable
R4(config-if)# no shutdown
R4(config-if)# exit

R4(config)# router ospf 5
R4(config-router)# log-adjacency-changes
R4(config-router)# network 172.16.20.0 0.0.0.255 area 0
R4(config-router)# router-id 172.16.20.2
R4(config-router)# exit

R4(config)# interface tunnel 0
R4(config-if)# no ip address
R4(config-if)# ipv6 address FEED:3::2/64
R4(config-if)# ipv6 rip VIPER enable
R4(config-if)# tunnel source fastethernet 0/0
R4(config-if)# tunnel destination 172.16.10.2
R4(config-if)# tunnel mode ipv6ip
R4(config-if)# no shutdown
R4(config-if)# exit
R4(config)# end
```

---

## 🟥 R5 – Konfiguracja (ruter tylko IPv6, prawa strona)

```
Router> enable
Router# configure terminal
Router(config)# hostname R5
R5(config)# ipv6 unicast-routing
R5(config)# ipv6 router rip VIPER
R5(config-rtr)# exit
R5(config)# interface fastethernet 0/0
R5(config-if)# no ip address
R5(config-if)# ipv6 address BABE:1::9/64
R5(config-if)# ipv6 rip VIPER enable
R5(config-if)# no shutdown
R5(config-if)# exit
R5(config)# end
```

---

## Weryfikacja Zadanie D – kolejność sprawdzania

### 1. Sprawdź interfejsy IPv6 (na R1, R2, R4, R5)

```
R2# show ipv6 int brief
```

### 2. Sprawdź czy OSPF działa między R2–R3–R4

```
R2# show ip ospf neighbor
R3# show ip ospf neighbor
R4# show ip ospf neighbor
```

> ✅ Oczekiwany wynik: R2 widzi R3, R3 widzi R2 i R4, R4 widzi R3.

### 3. Sprawdź tablice rutowania

```
R2# show ip route
R2# show ipv6 route

R4# show ip route
R4# show ipv6 route

R1# show ipv6 route
R5# show ipv6 route
```

> ✅ RIPng powinien uzupełnić wpisy o trasy do sieci po drugiej stronie tunelu (`BABE:1::` w R1, `ACDC:2::` w R5).

### 4. Testy ping i traceroute (z R1)

```
R1# ping ipv6 ACDC:2::1
R1# ping ipv6 FEED:3::1
R1# ping ipv6 FEED:3::2
R1# ping ipv6 BABE:1::1
R1# ping ipv6 BABE:1::9
R1# traceroute ipv6 BABE:1::9
```

#### Oczekiwany wynik traceroute:

```
Tracing the route to BABE:1::9

  1  ACDC:2::1   ~30 msec
  2  FEED:3::2   ~70 msec
  3  BABE:1::9  ~110 msec
```

### 5. Test z komputera PC (jeśli obsługuje IPv6)

```cmd
ping -6 BABE:1::1
tracert -6 -d BABE:1::9
```

---

---

## Szybka ściąga – komendy weryfikacyjne

| Komenda | Opis | Gdzie |
|---|---|---|
| `show ipv6 int brief` | Lista interfejsów IPv6 | każdy ruter |
| `show ipv6 route` | Tablica rutowania IPv6 | każdy ruter |
| `show ipv6 rip` | Status RIPng | R-ALFA, R-BETA / R1–R5 |
| `show ipv6 ospf neighbor` | Sąsiedzi OSPFv3 | R-ALFA, R-BETA |
| `show ipv6 ospf database` | Baza LSA OSPFv3 | R-ALFA, R-BETA |
| `show ipv6 ospf border-routers` | Rutery ABR | R-ALFA |
| `show ip ospf neighbor` | Sąsiedzi OSPF (IPv4) | R2, R3, R4 |
| `show ip route` | Tablica rutowania IPv4 | R2, R3, R4 |
| `show ipv6 protocols` | Aktywne protokoły IPv6 | każdy ruter |
| `ping ipv6 X::X` | Ping IPv6 | każdy ruter |
| `traceroute ipv6 X::X` | Traceroute IPv6 | każdy ruter |
| `debug ipv6 icmp` | Debug ICMPv6 | każdy ruter |
| `debug ipv6 ospf hello` | Debug hello OSPFv3 | R-ALFA, R-BETA |
| `clear ipv6 route *` | Kasowanie tablicy IPv6 | każdy ruter |
| `no debug all` | Wyłącz wszystkie debug | po zakończeniu! |

# Sieci Komputerowe – Lab BGP (eBGP, Atrybuty, Policy Routing, Redystrybucja)

> **Uwaga ogólna:** Używamy 3 ruterów Cisco. Komputer A obsługuje R1 i R2, Komputer B obsługuje R3. Połączenia konsolowe przez PuTTY (COM port, baud 9600).

---

## Schemat adresacji (ZMIENIONE adresy względem oryginału)

```
AS 65010 (R1)           AS 65020 (R2)          AS 65030 (R3)
  Loopback: 10.1.1.1      Loopback: 10.2.2.1     Loopback: 10.3.3.1
       |                       |                       |
  fa0/0: 172.16.10.2 --- fa0/0: 172.16.10.1    fa0/1: 172.16.20.2
                          fa0/1: 172.16.20.1 ---
                                                fa0/1: 172.16.30.2 (Zadanie D+)
  fa0/1: 172.16.30.1 ----------------------------^ (bezpośrednie, Zadanie D+)
```

| Łącze / Sieć       | R1 (fa)         | R2 (fa)         | R3 (fa)         |
|--------------------|-----------------|-----------------|-----------------|
| R1–R2              | fa0/0: 172.16.10.2 | fa0/0: 172.16.10.1 | —            |
| R2–R3              | —               | fa0/1: 172.16.20.1 | fa0/0: 172.16.20.2 |
| R1–R3 (Zad. D)     | fa0/1: 172.16.30.1 | —              | fa0/1: 172.16.30.2 |
| Loopback 1         | 10.1.1.1/24     | 10.2.2.1/24     | 10.3.3.1/24     |

---

## Zadanie A – Przygotowanie fizyczne i adresacja

### Podłączenie kabli

```
R1 fa0/0 ←—— kabel prosty ——→ R2 fa0/0
R2 fa0/1 ←—— kabel prosty ——→ R3 fa0/0
```

- R1 i R2 łączysz kablem prostym (interfejsy Ethernet).
- R2 i R3 łączysz kablem prostym.
- Komputer A: kabel konsolowy do R1, potem przepiąć do R2.
- Komputer B: kabel konsolowy do R3.

### Pojęcia AS

- **Single-homed AS:** tylko jedno wyjście do innego AS → R1 (AS 65010) i R3 (AS 65030).
- **Multi-homed AS:** więcej niż jedno wyjście → R2 (AS 65020).
- Numery AS > 65000 – rezerwowe (do ćwiczeń).

---

## Zadanie B – Konfiguracja BGP i Systemów Autonomicznych

### KOMPUTER A – Konfiguracja R1

```
R1> enable
R1# configure terminal
R1(config)# ip routing

! Interfejsy
R1(config)# interface FastEthernet0/0
R1(config-if)# ip address 172.16.10.2 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Loopback (punkt B.5)
R1(config)# interface Loopback1
R1(config-if)# ip address 10.1.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! BGP
R1(config)# router bgp 65010
R1(config-router)# neighbor 172.16.10.1 remote-as 65020
R1(config-router)# network 172.16.10.0 mask 255.255.255.0
R1(config-router)# network 10.1.1.0
R1(config-router)# exit
```

Włącz debug żeby obserwować zdarzenia BGP:
```
R1# debug ip bgp
```

### KOMPUTER A – Konfiguracja R2

```
R2> enable
R2# configure terminal
R2(config)# ip routing

R2(config)# interface FastEthernet0/0
R2(config-if)# ip address 172.16.10.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface FastEthernet0/1
R2(config-if)# ip address 172.16.20.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface Loopback1
R2(config-if)# ip address 10.2.2.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# router bgp 65020
R2(config-router)# neighbor 172.16.10.2 remote-as 65010
R2(config-router)# neighbor 172.16.20.2 remote-as 65030
R2(config-router)# network 172.16.10.0 mask 255.255.255.0
R2(config-router)# network 172.16.20.0 mask 255.255.255.0
R2(config-router)# network 10.2.2.0
R2(config-router)# exit
```

> Jeśli nie tworzy się sesja BGP-peer, wpisz przed konfiguracją BGP:
> `R2(config)# no parser cache`

### KOMPUTER B – Konfiguracja R3

```
R3> enable
R3# configure terminal
R3(config)# ip routing

R3(config)# interface FastEthernet0/0
R3(config-if)# ip address 172.16.20.2 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# interface Loopback1
R3(config-if)# ip address 10.3.3.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# router bgp 65030
R3(config-router)# neighbor 172.16.20.1 remote-as 65020
R3(config-router)# network 172.16.20.0 mask 255.255.255.0
R3(config-router)# network 10.3.3.0
R3(config-router)# exit
```

### Weryfikacja (wszystkie rutery)

```
! Sprawdź znanych sąsiadów BGP
Router# show ip bgp summary

! Sprawdź tablicę BGP
Router# show ip bgp

! Sprawdź konkretny prefiks (np. loopback R1 widoczny z R3)
R3# show ip bgp 10.1.1.0/24
```

### Punkt B.6 – Wyrejestrowanie sieci

Na R1 usuń sieć łączącą AS (np. sieć R1–R2):
```
R1(config)# router bgp 65010
R1(config-router)# no network 172.16.10.0 mask 255.255.255.0
```
Sprawdź czy ping między loopbackami nadal działa (nie powinien po pewnym czasie).

### Punkt B.7 – Reset sesji BGP

```
R1# clear ip bgp *
```
Obserwuj komunikaty debug i sprawdzaj jak odbudowuje się tablica BGP w R2 i R3.

---

## Zadanie C – Manipulowanie atrybutami tras BGP

### KOMPUTER A – Konfiguracja R1 (route-maps + dodatkowy loopback)

Dodaj nowy interfejs loopback:
```
R1(config)# interface Loopback2
R1(config-if)# ip address 10.1.5.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

Zdefiniuj route-mapy:
```
R1(config)# route-map mapa_in permit 10
R1(config-route-map)# set local-preference 50
R1(config-route-map)# set weight 100
R1(config-route-map)# set metric 777
R1(config-route-map)# set as-path prepend 300 400
R1(config-route-map)# exit

R1(config)# route-map mapa_out permit 10
R1(config-route-map)# set as-path prepend 500 600
R1(config-route-map)# exit

R1(config)# route-map mapa_prefix permit 10
R1(config-route-map)# set metric 99999
R1(config-route-map)# set origin incomplete
R1(config-route-map)# exit
```

Przypisz route-mapy do BGP:
```
R1(config)# router bgp 65010
R1(config-router)# neighbor 172.16.10.1 route-map mapa_in in
R1(config-router)# neighbor 172.16.10.1 route-map mapa_out out
R1(config-router)# network 10.1.5.0 route-map mapa_prefix
R1(config-router)# exit
```

Wyczyść sesje BGP i poczekaj na odbudowę:
```
R1# clear ip bgp *
R2# clear ip bgp *
```

### Weryfikacja atrybutów

```
! Na R1 – sprawdź jak R1 widzi trasy od R2 (przez mapa_in)
R1# show ip bgp

! Na R2 – sprawdź czy widzi 10.1.5.0 z metryką z mapa_prefix
R2# show ip bgp 10.1.5.0/24
R2# show ip route

! Szczegóły trasy do loopback R1 widziane przez R2
R1# show ip bgp 10.2.2.0/24
```

**Co zaobserwujesz:**
- `LOCAL-PREFERENCE` i `WEIGHT` nie są wysyłane poza AS (parametry lokalne).
- `MED` (metric) pojawia się w tablicy rutowania jako metryka.
- `AS-PATH prepend` widać w polu AS_PATH danej trasy.
- `ORIGIN incomplete` widać jako `?` w polu Origin w tablicy BGP.

### KOMPUTER A – Punkt C.2 – Filtrowanie AS-PATH w R2

```
R2(config)# ip as-path access-list 20 deny _500_
R2(config)# ip as-path access-list 20 permit ^$

R2(config)# router bgp 65020
R2(config-router)# neighbor 172.16.10.2 filter-list 20 in
R2(config-router)# exit

R2# clear ip bgp *
```

Sprawdź czy R2 otrzymuje trasy z AS 500 w AS-PATH (nie powinien):
```
R2# show ip bgp
```

### Sprzątanie po Zadaniu C

Po zakończeniu usuń route-mapy i filter-listy z BGP:
```
R1(config)# router bgp 65010
R1(config-router)# no neighbor 172.16.10.1 route-map mapa_in in
R1(config-router)# no neighbor 172.16.10.1 route-map mapa_out out
R1(config-router)# no network 10.1.5.0 route-map mapa_prefix

R2(config)# router bgp 65020
R2(config-router)# no neighbor 172.16.10.2 filter-list 20 in
```

---

## Zadanie D – BGP Alternatywne drogi

### Fizyczne podłączenie (nowe)

```
R1 fa0/1 ←—— kabel prosty ——→ R3 fa0/1
(przez przełącznik lub bezpośrednio)
```

Nowa sieć: `172.16.30.0/24`

### KOMPUTER A – Konfiguracja R1 (nowy interfejs i sąsiad)

```
R1(config)# interface FastEthernet0/1
R1(config-if)# ip address 172.16.30.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# router bgp 65010
R1(config-router)# neighbor 172.16.30.2 remote-as 65030
R1(config-router)# network 172.16.30.0 mask 255.255.255.0
R1(config-router)# exit
```

### KOMPUTER B – Konfiguracja R3 (nowy interfejs i sąsiad)

```
R3(config)# interface FastEthernet0/1
R3(config-if)# ip address 172.16.30.2 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit

R3(config)# router bgp 65030
R3(config-router)# neighbor 172.16.30.1 remote-as 65010
R3(config-router)# network 172.16.30.0 mask 255.255.255.0
R3(config-router)# exit
```

### Weryfikacja alternatywnych tras

```
! Na R1 – sprawdź przez który ruter idzie trasa do loopback R3 (10.3.3.1)
R1# show ip route
R1# show ip bgp

! Traceroute – zobaczysz czy omija R2
R1# traceroute 10.3.3.1
```

BGP domyślnie preferuje krótszą ścieżkę AS (bezpośrednio do R3: AS 65010→65030), zamiast przez R2 (65010→65020→65030).

### Test przełączania tras przy awarii

```
! Na R1 wyślij dużo pingów do loopback R3
R1# ping 10.3.3.1 source 10.1.1.1 repeat 10000
```

Podczas pingowania fizycznie odłącz kabel R1–R3 (symuluj awarię). Obserwuj po jakim czasie BGP przełączy ruch przez R2.

---

## Zadanie E – BGP Policy-Based Routing (blokowanie rozgłoszeń)

### Cel

Zmusić ruch z loopback R1 → loopback R3 (i odwrotnie), żeby szedł przez R2, mimo istnienia bezpośredniej trasy R1–R3.

### KOMPUTER B – Blokowanie wysyłania rozgłoszeń z R3 do R1

R3 przestanie informować R1 (sąsiada 172.16.30.1) o swojej sieci loopback (10.3.3.0/24):

```
R3(config)# access-list 25 deny 10.3.3.0 0.0.0.255
R3(config)# access-list 25 permit 0.0.0.0 255.255.255.255

R3(config)# route-map blokuj_wyslij_doR1 permit 10
R3(config-route-map)# match ip address 25
R3(config-route-map)# exit

R3(config)# router bgp 65030
R3(config-router)# neighbor 172.16.30.1 route-map blokuj_wyslij_doR1 out
R3(config-router)# exit
```

### KOMPUTER A – Reset BGP na R1 i sprawdzenie efektu

```
R1# clear ip bgp *
```

Poczekaj na odbudowę sesji BGP, następnie:
```
R1# show ip route
```

**Spodziewany wynik:** trasa do 10.3.3.0 teraz wskazuje przez 172.16.10.1 (R2), nie przez 172.16.30.2 (bezpośrednio do R3).

### KOMPUTER B – Blokowanie odbierania rozgłoszeń o R1 przez R3

```
R3(config)# access-list 26 deny 10.1.1.0 0.0.0.255
R3(config)# access-list 26 permit 0.0.0.0 255.255.255.255

R3(config)# route-map blokuj_odbierz_zR1 permit 10
R3(config-route-map)# match ip address 26
R3(config-route-map)# exit

R3(config)# router bgp 65030
R3(config-router)# neighbor 172.16.30.1 route-map blokuj_odbierz_zR1 in
R3(config-router)# exit

R3# clear ip bgp *
```

Weryfikacja na R3:
```
R3# show ip route
```

**Spodziewany wynik:** trasa do 10.1.1.0 na R3 idzie przez R2 (172.16.20.1), nie przez 172.16.30.1.

### Alternatywna metoda – distribute-list (bez route-map)

```
R3(config)# access-list 25 deny 10.3.3.0 0.0.0.255
R3(config)# access-list 25 permit 0.0.0.0 255.255.255.255

R3(config)# router bgp 65030
R3(config-router)# neighbor 172.16.30.1 distribute-list 25 out
```

Efekt taki sam jak route-map, ale bez definiowania route-map.

---

## Zadanie F – Redystrybucja IGP ↔ EGP (RIP ↔ BGP)

### Zmiana topologii

**Fizycznie:** odłącz kabel R2–R3. R2 przechodzi do AS 65010 (pracuje tylko pod RIP).

```
Nowa topologia:
AS 65010: R1 (RIP + BGP) + R2 (tylko RIP)
AS 65030: R3 (tylko BGP)

R2 fa0/0 ←——→ R1 fa0/0 (sieć 172.16.10.0/24)
R1 fa0/1 ←——→ R3 fa0/1 (sieć 172.16.30.0/24)
```

### KOMPUTER A – Wyłączenie BGP na R2, usunięcie sąsiadów z R1

```
! Na R2 – wyłącz BGP
R2(config)# no router bgp 65020

! Na R1 – usuń R2 jako sąsiada BGP (nie usuwa całego BGP!)
R1(config)# router bgp 65010
R1(config-router)# no neighbor 172.16.10.1 remote-as 65020
R1(config-router)# exit
```

### KOMPUTER B – Usunięcie starego sąsiada z R3

```
R3(config)# router bgp 65030
R3(config-router)# no neighbor 172.16.20.1 remote-as 65020
R3(config-router)# exit
```

### KOMPUTER A – Włączenie RIP na R1 i R2

**R1 (RIP + BGP):**
```
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 10.1.1.0
R1(config-router)# network 172.16.10.0
R1(config-router)# exit
```

**R2 (tylko RIP):**
```
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# network 10.2.2.0
R2(config-router)# network 172.16.10.0
R2(config-router)# exit
```

### Weryfikacja startowa (przed redystrybucją)

```
! R1 ma trasy RIP i BGP
R1# show ip route

! R3 nie widzi wnętrza AS 65010 (brak 10.2.2.0)
R3# show ip route

! R2 nie ma tras poza AS (brak 10.3.3.0)
R2# show ip route
```

### KOMPUTER A – Redystrybucja RIP → BGP (R3 dowie się o R2)

```
R1(config)# router bgp 65010
R1(config-router)# redistribute rip metric 3000
R1(config-router)# exit
```

Sprawdź na R3:
```
R3# show ip route
R3# show ip bgp
```
R3 powinien teraz widzieć sieć 10.2.2.0 z metryką 3000.

### KOMPUTER A – Redystrybucja BGP → RIP (R2 dowie się o R3)

```
R1(config)# router rip
R1(config-router)# redistribute bgp 65010 metric 15
R1(config-router)# exit

! Wymagane dodatkowe polecenie – włączenie redystrybucji wewnętrznych tras BGP
R1(config)# router bgp 65010
R1(config-router)# redistribute-internal
R1(config-router)# exit
```

Sprawdź na R2:
```
R2# show ip route
```
R2 powinien widzieć trasy do sieci 10.3.3.0 i 172.16.30.0 jako trasy RIP (metryka 15).

### Upewnij się, że sieć R1–R3 jest w BGP (ważne dla pingów zwrotnych)

```
R1(config)# router bgp 65010
R1(config-router)# network 172.16.30.0 mask 255.255.255.0
R1(config-router)# exit
```

### Finalny test komunikacji

```
! Ping z R2 do loopback R3
R2# ping 10.3.3.1

! Ping z R3 do loopback R2
R3# ping 10.2.2.1
```

Oba pingi powinny przechodzić dzięki redystrybucji.

---

## Tabela kluczowych komend – szybka ściąga

| Komenda | Opis |
|---------|------|
| `router bgp <ASN>` | Uruchomienie procesu BGP z danym numerem AS |
| `neighbor <IP> remote-as <ASN>` | Rejestracja sąsiada BGP |
| `network <sieć> [mask <maska>]` | Rozgłaszanie sieci przez BGP |
| `show ip bgp` | Tablica BGP (wszystkie prefiksy) |
| `show ip bgp summary` | Status sąsiadów BGP |
| `show ip bgp <prefix>/<mask>` | Szczegóły konkretnej trasy BGP |
| `clear ip bgp *` | Reset wszystkich sesji BGP |
| `debug ip bgp` | Debug zdarzeń BGP na konsoli |
| `route-map <nazwa> permit <nr>` | Tworzenie route-mapy |
| `set metric <wartość>` | Ustawienie MED w route-mapie |
| `set local-preference <wartość>` | Ustawienie LOCAL-PREF |
| `set as-path prepend <ASN...>` | Wydłużanie AS-PATH |
| `set weight <wartość>` | Ustawienie WEIGHT (tylko lokalny ruter) |
| `set origin incomplete` | Origin = `?` (redystrybucja) |
| `neighbor <IP> route-map <nazwa> in/out` | Przypisanie route-mapy do sąsiada |
| `neighbor <IP> filter-list <nr> in/out` | Filtrowanie przez AS-PATH ACL |
| `neighbor <IP> distribute-list <nr> in/out` | Filtrowanie przez IP ACL |
| `redistribute rip metric <wartość>` | Redystrybucja RIP → BGP |
| `redistribute bgp <ASN> metric <wartość>` | Redystrybucja BGP → RIP |
| `redistribute-internal` | Włącz redystrybucję wewnętrznych tras BGP |
| `ip as-path access-list <nr> deny/permit <regex>` | ACL na AS-PATH |

# Lab 068 – Konfiguracja ruterów Cisco SHDSL (Mostkowanie)

## Topologia

```
PC <---Ethernet---> Ruter CO (Slave) <---G.SHDSL---> Ruter CPE (Master) <---Ethernet---> PC
```

---

## Krok 1 – Konfiguracja mostkowania IRB (na obu ruterach)

```
Router> enable
Router# configure terminal

Router(config)# bridge irb
Router(config)# bridge 1 protocol ieee
Router(config)# bridge 1 route ip
Router(config)# ip forward-protocol spanning-tree
```

---

## Krok 2 – Konfiguracja interfejsu Ethernet (na obu ruterach)

```
Router(config)# interface FastEthernet 0/0
Router(config-if)# no ip address
Router(config-if)# bridge-group 1
Router(config-if)# no shutdown
Router(config-if)# exit
```

Weryfikacja:
```
Router# show bridge
Router# show spanning-tree brief
```

---

## Krok 3 – Konfiguracja DSL

Sprawdź dostępność kontrolera DSL:
```
Router(config)# controller DSL 0/0
```
- Jeśli komenda **dostępna** → wykonaj **Wariant 1**
- Jeśli **niedostępna** → wykonaj **Wariant 2**

---

### WARIANT 1 – Konfiguracja przez kontroler DSL

#### Strona CPE (Master)

```
Router(config)# controller DSL 0/0
Router(config-controller)# dsl-mode shdsl symmetric annex A
Router(config-controller)# mode atm
Router(config-controller)# line-term cpe
Router(config-controller)# line-mode auto
Router(config-controller)# no shutdown
Router(config-controller)# exit

Router(config)# interface ATM 0/0
Router(config-if)# no ip address
Router(config-if)# bridge-group 1
Router(config-if)# no shutdown
Router(config-if)# atm ilmi-keepalive
Router(config-if)# pvc tunnel 1/40
Router(config-if-atm-vc)# encapsulation aal5snap
Router(config-if-atm-vc)# exit
Router(config-if)# exit
```

#### Strona CO (Slave)

```
Router(config)# controller DSL 0/0
Router(config-controller)# dsl-mode shdsl symmetric annex A
Router(config-controller)# mode atm
Router(config-controller)# line-term co
Router(config-controller)# line-mode auto
Router(config-controller)# no shutdown
Router(config-controller)# exit

Router(config)# interface ATM 0/0
Router(config-if)# no ip address
Router(config-if)# bridge-group 1
Router(config-if)# no shutdown
Router(config-if)# atm ilmi-keepalive
Router(config-if)# pvc tunnel 1/40
Router(config-if-atm-vc)# encapsulation aal5snap
Router(config-if-atm-vc)# exit
Router(config-if)# exit
```

---

### WARIANT 2 – Konfiguracja przez interfejs ATM

#### Strona CPE (Master)

```
Router(config)# interface ATM 0/0
Router(config-if)# no ip address
Router(config-if)# bridge-group 1
Router(config-if)# no shutdown
Router(config-if)# dsl operating-mode gshdsl symmetric annex A
Router(config-if)# dsl equipment-type cpe
Router(config-if)# dsl linerate auto
Router(config-if)# atm ilmi-keepalive
Router(config-if)# pvc 1/40
Router(config-if-atm-vc)# exit
Router(config-if)# exit
```

#### Strona CO (Slave)

```
Router(config)# interface ATM 0/0
Router(config-if)# no ip address
Router(config-if)# bridge-group 1
Router(config-if)# no shutdown
Router(config-if)# dsl operating-mode gshdsl symmetric annex A
Router(config-if)# dsl equipment-type co
Router(config-if)# dsl linerate auto
Router(config-if)# atm ilmi-keepalive
Router(config-if)# pvc 1/40
Router(config-if-atm-vc)# exit
Router(config-if)# exit
```

> Jeśli linia DSL nie aktywuje się automatycznie, spróbuj ręcznie ustawić prędkość:
> ```
> Router(config-if)# dsl linerate 520
> Router(config-if)# shutdown
> Router(config-if)# no shutdown
> ```

---

## Krok 4 – Konfiguracja interfejsu BVI (na obu ruterach)

Interfejs BVI (Bridge Virtual Interface) umożliwia dostęp IP do rutera przez mostek.

```
Router(config)# interface BVI 1
Router(config-if)# ip address 200.200.200.1 255.255.255.0
Router(config-if)# exit
```

> Na drugim ruterze użyj innego adresu IP, np. `200.200.200.2`

Skonfiguruj stacje PC z adresami z tej samej podsieci, np. `200.200.200.10/24`.

---

## Krok 5 – Weryfikacja

```
Router# show bridge
Router# show spanning-tree brief
Router# show interfaces ATM 0/0
Router# show controllers DSL 0/0
ping 200.200.200.2
```

---

## Oczekiwane komunikaty po poprawnej konfiguracji

```
DSL 0/0 controller Link up! line rate: 2304 Kbps
%CONTROLLER-5-UPDOWN: Controller DSL 0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface ATM0/0, changed state to up
%ATM-5-UPDOWN: Interface ATM0/0, Changing autovc 1/40 to PVC activated
```

---

## Uwagi

- Annex protokołu DSL (**A**, **B**, itd.) musi być **identyczny** po obu stronach
- PVC w obu ruterach musi mieć takie same wartości **VPI/VCI** (np. `1/40`)
- Enkapsulacja `aal5snap` jest wymagana po stronie **CO**, gdyż przenosi wiele protokołów (IP + ARP)
- Jeśli DSL nie aktywuje się – odłącz i ponownie podłącz przewód DSL
- Po zakończeniu konfiguracji zapisz ją: `Router# write memory`

# Lab 039 – Konfiguracja sieci Frame Relay (Cisco)

## Topologia

```
                  ┌──────────────────────┐
                  │  Przełącznik FR (FRS)│
                  │   (rutter z DCE)     │
                  │  Serial 0, 1, 2      │
                  └──┬──────┬──────┬─────┘
                     │      │      │
              DLCI 111│      │DLCI 121
                     │      │     │DLCI 131
              ┌──────┴┐  ┌──┴──┐ ┌┴─────┐
              │  R1   │  │ R2  │ │  R3  │
              │ DTE   │  │ DTE │ │ DTE  │
              └───┬───┘  └──┬──┘ └──┬───┘
                  │Eth      │Eth    │Eth
                 PC1       PC2     PC3
```

Sieci point-to-point między pod-interfejsami:
- **200.200.101.0/24** – R1 ↔ R2 (DLCI 111/112)
- **200.200.102.0/24** – R1 ↔ R3 (DLCI 121/122)
- **200.200.103.0/24** – R2 ↔ R3 (DLCI 131/132)

---

## Podział pracy między 2 komputery

| Komputer | Konsole (PuTTY) do urządzeń |
|---|---|
| **PC1** | Przełącznik Frame Relay (FRS) + Ruter R1 |
| **PC2** | Ruter R2 + Ruter R3 |

---

## Podłączenie kabli

### Kable Serial (DCE/DTE)
- **Przełącznik FR** musi być stroną **DCE** (kabel z oznaczeniem DCE)
- **Rutery R1, R2, R3** są stronami **DTE**
- W praktyce: zamontuj kable Serial tak, by **końcówka DCE** była po stronie przełącznika

```
FRS Serial0 (DCE) ──── (DTE) Serial0/0 R1
FRS Serial1 (DCE) ──── (DTE) Serial0/0 R2
FRS Serial2 (DCE) ──── (DTE) Serial0/0 R3
```

### Kable konsolowe (do PuTTY)
- Każdy ruter podłącz przez **kabel konsolowy** (RJ-45 → COM/USB) do laptopa
- W PuTTY: typ połączenia **Serial**, prędkość **9600 bps**, port np. **COM3**

### Kable Ethernet
- R1 ↔ PC1 (interfejs FastEthernet 0/0)
- R2 ↔ PC2 (interfejs FastEthernet 0/0)
- R3 ↔ PC3 *(opcjonalnie, można zastąpić interfejsem Loopback)*

---

# === PC1 ===

## KONFIGURACJA PRZEŁĄCZNIKA FRAME RELAY (FRS)

```
FRS> enable
FRS# configure terminal

FRS(config)# frame-relay switching
```

### Interfejs Serial 0 (do R1)
```
FRS(config)# interface Serial 0
FRS(config-if)# no ip address
FRS(config-if)# no frame-relay inverse-arp
FRS(config-if)# encapsulation frame-relay
FRS(config-if)# frame-relay intf-type dce
FRS(config-if)# no keepalive
FRS(config-if)# clock rate 64000
FRS(config-if)# frame-relay route 111 interface Serial 1 112
FRS(config-if)# frame-relay route 121 interface Serial 2 122
FRS(config-if)# no shutdown
FRS(config-if)# exit
```

### Interfejs Serial 1 (do R2)
```
FRS(config)# interface Serial 1
FRS(config-if)# no ip address
FRS(config-if)# no frame-relay inverse-arp
FRS(config-if)# encapsulation frame-relay
FRS(config-if)# frame-relay intf-type dce
FRS(config-if)# no keepalive
FRS(config-if)# clock rate 64000
FRS(config-if)# frame-relay route 112 interface Serial 0 111
FRS(config-if)# frame-relay route 131 interface Serial 2 132
FRS(config-if)# no shutdown
FRS(config-if)# exit
```

### Interfejs Serial 2 (do R3)
```
FRS(config)# interface Serial 2
FRS(config-if)# no ip address
FRS(config-if)# no frame-relay inverse-arp
FRS(config-if)# encapsulation frame-relay
FRS(config-if)# frame-relay intf-type dce
FRS(config-if)# no keepalive
FRS(config-if)# clock rate 64000
FRS(config-if)# frame-relay route 122 interface Serial 0 121
FRS(config-if)# frame-relay route 132 interface Serial 1 131
FRS(config-if)# no shutdown
FRS(config-if)# exit
FRS(config)# end
```

### Weryfikacja
```
FRS# show running-config
FRS# show frame-relay route
```

---

## KONFIGURACJA RUTERA R1

```
R1> enable
R1# configure terminal
```

### Główny interfejs Serial 0/0
```
R1(config)# interface Serial 0/0
R1(config-if)# no ip address
R1(config-if)# encapsulation frame-relay
R1(config-if)# no keepalive
R1(config-if)# no shutdown
R1(config-if)# exit
```

### Pod-interfejs do R2 (sieć 200.200.101.0/24, DLCI 111)
```
R1(config)# interface Serial 0/0.1 point-to-point
R1(config-subif)# frame-relay interface-dlci 111
R1(config-subif)# bandwidth 64
R1(config-subif)# ip address 200.200.101.1 255.255.255.0
R1(config-subif)# no shutdown
R1(config-subif)# exit
```

### Pod-interfejs do R3 (sieć 200.200.102.0/24, DLCI 121)
```
R1(config)# interface Serial 0/0.2 point-to-point
R1(config-subif)# frame-relay interface-dlci 121
R1(config-subif)# bandwidth 64
R1(config-subif)# ip address 200.200.102.1 255.255.255.0
R1(config-subif)# no shutdown
R1(config-subif)# exit
```

### Interfejs Ethernet do PC1
```
R1(config)# interface FastEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```

### Routing RIP
```
R1(config)# ip routing
R1(config)# ip classless
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 200.200.101.0
R1(config-router)# network 200.200.102.0
R1(config-router)# network 192.168.1.0
R1(config-router)# no auto-summary
R1(config-router)# end
```

---

# === PC2 ===

## KONFIGURACJA RUTERA R2

```
R2> enable
R2# configure terminal
```

### Główny interfejs Serial 0/0
```
R2(config)# interface Serial 0/0
R2(config-if)# no ip address
R2(config-if)# encapsulation frame-relay
R2(config-if)# no keepalive
R2(config-if)# no shutdown
R2(config-if)# exit
```

### Pod-interfejs do R1 (sieć 200.200.101.0/24, DLCI 112)
```
R2(config)# interface Serial 0/0.1 point-to-point
R2(config-subif)# frame-relay interface-dlci 112
R2(config-subif)# bandwidth 64
R2(config-subif)# ip address 200.200.101.2 255.255.255.0
R2(config-subif)# no shutdown
R2(config-subif)# exit
```

### Pod-interfejs do R3 (sieć 200.200.103.0/24, DLCI 131)
```
R2(config)# interface Serial 0/0.2 point-to-point
R2(config-subif)# frame-relay interface-dlci 131
R2(config-subif)# bandwidth 64
R2(config-subif)# ip address 200.200.103.1 255.255.255.0
R2(config-subif)# no shutdown
R2(config-subif)# exit
```

### Interfejs Ethernet do PC2
```
R2(config)# interface FastEthernet 0/0
R2(config-if)# ip address 192.168.2.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit
```

### Routing RIP
```
R2(config)# ip routing
R2(config)# ip classless
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# network 200.200.101.0
R2(config-router)# network 200.200.103.0
R2(config-router)# network 192.168.2.0
R2(config-router)# no auto-summary
R2(config-router)# end
```

---

## KONFIGURACJA RUTERA R3

```
R3> enable
R3# configure terminal
```

### Główny interfejs Serial 0/0
```
R3(config)# interface Serial 0/0
R3(config-if)# no ip address
R3(config-if)# encapsulation frame-relay
R3(config-if)# no keepalive
R3(config-if)# no shutdown
R3(config-if)# exit
```

### Pod-interfejs do R2 (sieć 200.200.103.0/24, DLCI 132)
```
R3(config)# interface Serial 0/0.1 point-to-point
R3(config-subif)# frame-relay interface-dlci 132
R3(config-subif)# bandwidth 64
R3(config-subif)# ip address 200.200.103.2 255.255.255.0
R3(config-subif)# no shutdown
R3(config-subif)# exit
```

### Pod-interfejs do R1 (sieć 200.200.102.0/24, DLCI 122)
```
R3(config)# interface Serial 0/0.2 point-to-point
R3(config-subif)# frame-relay interface-dlci 122
R3(config-subif)# bandwidth 64
R3(config-subif)# ip address 200.200.102.2 255.255.255.0
R3(config-subif)# no shutdown
R3(config-subif)# exit
```

### Interfejs Ethernet (opcjonalnie, lub Loopback)
```
R3(config)# interface Loopback 0
R3(config-if)# ip address 192.168.3.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit
```

### Routing RIP
```
R3(config)# ip routing
R3(config)# ip classless
R3(config)# router rip
R3(config-router)# version 2
R3(config-router)# network 200.200.102.0
R3(config-router)# network 200.200.103.0
R3(config-router)# network 192.168.3.0
R3(config-router)# no auto-summary
R3(config-router)# end
```

---

## Tabela mapowania DLCI

| Łącze | Sieć IP | Strona | Lokalne DLCI |
|---|---|---|---|
| R1 ↔ FRS ↔ R2 | 200.200.101.0/24 | R1 | 111 |
| | | R2 | 112 |
| R1 ↔ FRS ↔ R3 | 200.200.102.0/24 | R1 | 121 |
| | | R3 | 122 |
| R2 ↔ FRS ↔ R3 | 200.200.103.0/24 | R2 | 131 |
| | | R3 | 132 |

---

## Weryfikacja (na każdym ruterze brzegowym)

```
R1# show frame-relay map
R1# show frame-relay pvc
R1# show ip route
R1# show ip interface brief
```

### Testy ping (z R1)
```
R1# ping 200.200.101.2     # do R2
R1# ping 200.200.102.2     # do R3
R1# ping 200.200.103.1     # do R2 przez sieć Frame Relay
R1# ping 200.200.103.2     # do R3 przez sieć Frame Relay
```

### Test komunikacji PC ↔ PC
Na **PC1** (Windows):
```
ipconfig                   # sprawdź adres IP
ping 192.168.2.10          # ping do PC2
```

---

## Konfiguracja kart sieciowych na PC

### PC1
- IP: `192.168.1.10`
- Maska: `255.255.255.0`
- Brama: `192.168.1.1` (R1)

### PC2
- IP: `192.168.2.10`
- Maska: `255.255.255.0`
- Brama: `192.168.2.1` (R2)

---

## Zapisanie konfiguracji (na każdym urządzeniu)

```
Router# write memory
```
lub
```
Router# copy running-config startup-config
```

---

## Najczęstsze problemy

| Problem | Rozwiązanie |
|---|---|
| Interfejs Serial down | Sprawdź kabel DCE/DTE, włącz `clock rate` po stronie DCE |
| `frame-relay route` nie działa | Co najmniej jeden interfejs musi być DCE |
| `show frame-relay map` pusty | Sprawdź DLCI po obu stronach |
| Brak komunikacji RIP | `no auto-summary` i `version 2` na wszystkich ruterach |
| `keepalive` powoduje down | Wyłącz `no keepalive` w pod-interfejsach |

---

# Sieci Komputerowe – Lab 088, Cisco Catalyst 5500 (VLAN, RSM, Rutowanie)

> **Uwaga ogólna:** Pracujemy na przełączniku zarządzalnym Cisco Catalyst 5500 z modułem RSM (Route Switch Module). Połączenie konsolowe: kabel DB9-RJ45 (BEZ rollover) do gniazda CONSOLE na karcie Supervisor. Parametry PuTTY: **9600 bps, 8 bitów, brak parzystości, 1 bit stopu, brak flow control**.

---

## Architektura sprzętu – co jest w chassis

| Moduł / Karta            | System OS     | Rola                                      |
|--------------------------|---------------|-------------------------------------------|
| Supervisor (karta główna) | CatOS        | Zarządza przełączaniem L2, VLAN, STP      |
| RSM (Route Switch Module) | Cisco IOS    | Rutuje datagramy IP między VLAN (L3)      |
| LightStream ASP + ATM     | —            | Przełącznica ATM – w tym ćwiczeniu nieużywana |

> Interfejs `sc0` – fizyczny port zarządzający w karcie Supervisor (CatOS).  
> Interfejsy VLAN w RSM – wirtualne interfejsy L3 rutera.

---

## Zadanie A – Pierwsze połączenie, przegląd modułów

### Logowanie do przełącznika (CatOS)

Po podłączeniu kabla konsolowego i otwarciu PuTTY:

```
! Wciśnij Enter, pojawi się znak zachęty
Cat5500> enable
! Hasło: cisco lub puste (Enter)
Cat5500> (enable)
```

Znak `(enable)` w karetce = zalogowano poprawnie w trybie uprzywilejowanym.

### Przegląd zainstalowanych modułów

```
Cat5500> (enable) show module
```

Zobaczyć numer gniazda (slotu) każdego modułu – potrzebny do sesji z RSM.

### Przełączenie do modułu RSM (lub ASP)

```
! Zastąp 5 numerem slotu RSM z output show module
Cat5500> (enable) session 5
```

Połączenie nawiązane – pojawi się prompt IOS (`Router>`).

**Wyjście z sesji do RSM (powrót do CatOS):**

```
Router> [naciśnij Ctrl+] ]
telnet> quit
Cat5500> (enable)
```

---

## Zadanie B – Konfiguracja VLAN w module Supervisor (CatOS)

### Krok 1 – Adresacja interfejsu zarządzającego sc0

```
! Zmieniamy względem oryginału: 192.168.50.x zamiast 192.168.123.x
Cat5500> (enable) set interface sc0 192.168.50.10 255.255.255.0
Cat5500> (enable) set ip route 0.0.0.0 192.168.50.1
```

### Krok 2 – Konfiguracja VTP

```
! Sprawdź aktualny stan VTP
Cat5500> (enable) show vtp domain

! Ustaw tryb transparent (nie propaguje VLAN do innych przełączników)
Cat5500> (enable) set vtp mode transparent

! Ustaw nazwę domeny VTP
Cat5500> (enable) set vtp domain labsiec
```

### Krok 3 – Tworzenie VLAN i przypisywanie portów

```
! Utwórz trzy VLAN
Cat5500> (enable) set vlan 10
Cat5500> (enable) set vlan 20
Cat5500> (enable) set vlan 30

! Przypisz porty do VLAN (format: karta/port)
! VLAN 10 – porty 1–8 na karcie 7
Cat5500> (enable) set vlan 10 7/1-8

! VLAN 20 – porty 9 i 10 na karcie 7
Cat5500> (enable) set vlan 20 7/9-10

! VLAN 30 – port 11 na karcie 7
Cat5500> (enable) set vlan 30 7/11
```

> Porty światłowodowe Gigabit Ethernet Supervisora: `1/1` i `1/2`.

### Kasowanie VLAN i cofanie portów

```
! Usuń VLAN 30
Cat5500> (enable) clear vlan 30

! Porty NIE wracają automatycznie do VLAN 1 – cofnij ręcznie
Cat5500> (enable) set vlan 1 7/11
```

### Krok 4 – Weryfikacja VLAN

```
Cat5500> (enable) show vlan
```

Podłącz dwie stacje PC do portów tego samego VLAN i przetestuj ping. Uwaga: STP potrzebuje **30–60 sekund** na aktualizację mostu – poczekaj zanim zaczniesz testować.

### Krok 5 – Diagnostyka Spanning Tree

```
! Ogólny stan STP
Cat5500> (enable) show spantree active
Cat5500> (enable) show spantree summary

! STP dla konkretnego VLAN (np. VLAN 10)
Cat5500> (enable) show spantree 10

! STP dla konkretnego portu (slot/port)
Cat5500> (enable) show spantree statistics 7/1
```

### Krok 6 – PortFast (skrócenie czasu STP dla portów końcowych)

```
! Włącz PortFast dla portów 7/1 do 7/11
Cat5500> (enable) set spantree portfast 7/1-11 enable
```

> PortFast NIE wyłącza STP – tylko skraca czas wejścia portu w stan forwarding. Uważaj na pętle!

### Krok 7 – Telnet do przełącznika przez sc0

Z komputera PC (podłączonego do portu w VLAN 1):

```
telnet 192.168.50.10
```

Dane logowania takie same jak konsola RS232.

### Krok 8 – Przeniesienie portu światłowodowego do VLAN 20

```
! Przenieś drugi port GigabitEthernet Supervisora do VLAN 20
Cat5500> (enable) set vlan 20 1/2
```

Teraz można podłączyć dwa komputery do VLAN 20 i sprawdzić, że **komunikacja istnieje tylko w ramach tego samego VLAN** (stacje z VLAN 10 i VLAN 20 nie będą się wzajemnie pingować bez rutera).

---

## Zadanie C – Konfiguracja modułu RSM (Cisco IOS)

### Połączenie z RSM

```
Cat5500> (enable) session 5
Router> enable
Router# configure terminal
Router(config)#
```

### Krok 1 – Interfejs VLAN 1 rutera

```
! Adres INNY niż sc0 Supervisora (sc0 ma 192.168.50.10)
Router(config)# interface vlan 1
Router(config-if)# ip address 192.168.50.20 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

> W VLAN 1 będą teraz DWA adresy IP: `sc0` (Supervisor, warstwa 2) i interfejs VLAN rutera (warstwa 3). To normalne.

> **Warunek działania interfejsu VLAN rutera:** VLAN musi być zdefiniowany w Supervisorze ORAZ mieć podłączony aktywny port Ethernet z urządzeniem zewnętrznym (albo drugi aktywny ruter w chassis).

### Krok 2 – Serwer HTTP rutera

```
Router(config)# ip http server
```

Wejdź przeglądarką na adres `http://192.168.50.20` – dostęp przez GUI Cisco.

### Krok 3 – Dodatkowe interfejsy VLAN rutera

```
! VLAN 10 (zmienione: zamiast vlan 2 z oryginału)
Router(config)# interface vlan 10
Router(config-if)# ip address 10.10.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! VLAN 20
Router(config)# interface vlan 20
Router(config-if)# ip address 10.10.20.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

### Krok 4 – Weryfikacja interfejsów

```
Router# show ip interface brief
```

> Ruter posiada ukryty interfejs `127.0.0.X` (X = numer slotu) do wewnętrznego telnetu w chassis. Można go użyć: `Router# telnet 127.0.0.5`

### Krok 5 – Konfiguracja PC i test routingu między VLAN

Na stacji PC ustaw bramkę na adres interfejsu RSM w tym samym VLAN:
- PC w VLAN 10 → bramka `10.10.10.1`
- PC w VLAN 20 → bramka `10.10.20.1`

```
! Test routingu między VLAN – z PC w VLAN 10 ping do VLAN 20
ping 10.10.20.X
```

### Krok 6 – Weryfikacja izolacji i rutowania

Sprawdź:
- PC w różnych VLAN **bez rutera** → brak komunikacji (izolacja L2)
- PC w różnych VLAN **z RSM** → komunikacja przez ruter (L3)
- Dostępność sc0 Supervisora z obu VLAN

### Krok 7 – Podłączenie zewnętrznego rutera Cisco do VLAN

Podłącz zewnętrzny ruter Cisco do portu zakwalifikowanego do wybranego VLAN (np. VLAN 10). Skonfiguruj adres w tej samej podsieci:

```
! Na zewnętrznym ruterze
ExternalRouter(config)# interface FastEthernet0/0
ExternalRouter(config-if)# ip address 10.10.10.2 255.255.255.0
ExternalRouter(config-if)# no shutdown

! Stwórz loopback do testów
ExternalRouter(config)# interface Loopback0
ExternalRouter(config-if)# ip address 172.31.1.1 255.255.255.0
ExternalRouter(config-if)# no shutdown

! Włącz RIP na obu ruterach
ExternalRouter(config)# router rip
ExternalRouter(config-router)# version 2
ExternalRouter(config-router)# network 10.10.10.0
ExternalRouter(config-router)# network 172.31.1.0

Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 10.10.10.0
Router(config-router)# network 10.10.20.0
```

Test z PC w VLAN 20:
```
ping 172.31.1.1
```

### Krok 8–10 – Interfejs fizyczny przez moduł VIP

Identyfikacja modułu VIP:
```
Router# show diag 9
Router# show controllers cbus
```

Konfiguracja fizycznego interfejsu Fast Ethernet w VIP:
```
Router(config)# interface FastEthernet 1/0
Router(config-if)# ip address 10.99.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

Podłącz kabel do tego interfejsu i przetestuj:
```
Router# ping 10.99.1.2
```

---

## Zadanie D – Kilka modułów RSM, sieć wielosegmentowa

### Koncepcja

W jednym chassis Catalyst 5500 mogą działać **trzy niezależne rutery RSM** (każdy ze swoim IOS). Każdy RSM rutuje między przypisanymi do niego VLAN. VLAN tranzytowe łączą RSM ze sobą.

### Schemat sieci (zmieniona adresacja)

```
PC_A ── VLAN 40 ── RSM1 ── VLAN 50 ── RSM2 ── VLAN 60 ── RSM3 ── VLAN 70 ── PC_B
```

| VLAN | Sieć IP         | Bramy RSM                       |
|------|-----------------|--------------------------------|
| 40   | 10.40.0.0/24    | RSM1: 10.40.0.1                |
| 50   | 10.50.0.0/24    | RSM1: 10.50.0.1 / RSM2: 10.50.0.2 |
| 60   | 10.60.0.0/24    | RSM2: 10.60.0.1 / RSM3: 10.60.0.2 |
| 70   | 10.70.0.0/24    | RSM3: 10.70.0.1                |

### Konfiguracja Supervisora – przypisanie portów do VLAN

```
Cat5500> (enable) set vlan 40 3/1-5
Cat5500> (enable) set vlan 50
Cat5500> (enable) set vlan 60
Cat5500> (enable) set vlan 70 3/6-10
```

### RSM1 – sesja i konfiguracja

```
Cat5500> (enable) session 5

Router(config)# interface vlan 40
Router(config-if)# ip address 10.40.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface vlan 50
Router(config-if)# ip address 10.50.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 10.40.0.0
Router(config-router)# network 10.50.0.0
Router(config-router)# exit
```

### RSM2 – sesja i konfiguracja

```
Cat5500> (enable) session 6

Router(config)# interface vlan 50
Router(config-if)# ip address 10.50.0.2 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface vlan 60
Router(config-if)# ip address 10.60.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 10.50.0.0
Router(config-router)# network 10.60.0.0
Router(config-router)# exit
```

### RSM3 – sesja i konfiguracja

```
Cat5500> (enable) session 7

Router(config)# interface vlan 60
Router(config-if)# ip address 10.60.0.2 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface vlan 70
Router(config-if)# ip address 10.70.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 10.60.0.0
Router(config-router)# network 10.70.0.0
Router(config-router)# exit
```

### Konfiguracja PC

| Stacja | Adres IP   | Brama      |
|--------|------------|------------|
| PC_A   | 10.40.0.10 | 10.40.0.1  |
| PC_B   | 10.70.0.10 | 10.70.0.1  |

### Weryfikacja trasy end-to-end

```
! Ścieżka pakietu: PC_A → VLAN40 → RSM1 → VLAN50 → RSM2 → VLAN60 → RSM3 → VLAN70 → PC_B
! Z PC_A:
ping 10.70.0.10
tracert 10.70.0.10   (Windows) / traceroute 10.70.0.10 (Linux)
```

---

## Zadanie E – Boot version RSM (testowy IOS z bootflash)

### Sprawdzenie zawartości nośników

```
! Pełna wersja IOS na karcie PCMCIA
Router# dir slot0:

! Wersja testowa (boot) w bootflash
Router# dir bootflash:
```

### Restart do wersji boot (wysuń kartę PCMCIA fizycznie, potem)

```
Router# reload
```

Po załadowaniu wersji boot – ograniczona funkcjonalność (brak protokołów rutowania dynamicznego, minimalne opcje).

### Konfiguracja interfejsu fizycznego VIP w wersji boot

```
Router(config)# interface FastEthernet 1/0
Router(config-if)# ip address 10.99.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

### Zmiana domyślnej bramy w Supervisorze (konieczne do pingu z sc0)

```
! Usuń starą bramkę
Cat5500> (enable) clear ip route 0.0.0.0 192.168.50.1

! Ustaw nową bramkę wskazującą na interfejs RSM w VLAN 1
Cat5500> (enable) set ip route 0.0.0.0 192.168.50.20

! Test pingu do interfejsu fizycznego VIP
Cat5500> (enable) ping 10.99.1.1

! Traceroute
Cat5500> (enable) trace 10.99.1.1
```

---

## Tabela kluczowych komend – szybka ściąga

### CatOS (Supervisor)

| Komenda | Opis |
|---------|------|
| `show module` | Lista modułów w chassis |
| `session <slot>` | Połączenie z modułem RSM/ASP |
| `set interface sc0 <IP> <maska>` | Adres IP interfejsu zarządzającego |
| `set ip route 0.0.0.0 <brama>` | Domyślna trasa Supervisora |
| `clear ip route 0.0.0.0 <brama>` | Usuń domyślną trasę |
| `show vtp domain` | Stan VTP |
| `set vtp mode transparent` | Tryb VTP transparent |
| `set vtp domain <nazwa>` | Nazwa domeny VTP |
| `set vlan <VID>` | Utwórz VLAN |
| `set vlan <VID> <karta>/<porty>` | Przypisz porty do VLAN |
| `clear vlan <VID>` | Usuń VLAN |
| `show vlan` | Lista VLAN i przypisanych portów |
| `show spantree active` | Aktywne drzewo STP |
| `show spantree <VID>` | STP dla danego VLAN |
| `show spantree statistics <slot/port>` | Statystyki STP portu |
| `set spantree portfast <porty> enable` | PortFast dla portów końcowych |
| `ping <IP>` | Ping z Supervisora |
| `trace <IP>` | Traceroute z Supervisora |

### IOS (RSM)

| Komenda | Opis |
|---------|------|
| `show module` | Moduły w chassis (z poziomu RSM) |
| `show diag 9` | Diagnostyka modułu VIP |
| `show controllers cbus` | Kontrolery i moduły VIP |
| `show ip interface brief` | Stan interfejsów IP |
| `interface vlan <VID>` | Konfiguracja wirtualnego interfejsu VLAN |
| `interface FastEthernet 1/0` | Konfiguracja fizycznego interfejsu VIP |
| `ip http server` | Włącz serwer HTTP (GUI Cisco) |
| `dir slot0:` | Zawartość karty PCMCIA |
| `dir bootflash:` | Zawartość pamięci bootflash |
| `reload` | Restart rutera |
| `telnet 127.0.0.<slot>` | Wewnętrzny telnet do RSM w chassis |

---

# Sieci Komputerowe – Lab 056 -  HP ProCurve 2650 (VLAN, Routing, Zabezpieczenia)

> **Uwaga ogólna:** Pracujemy na przełączniku HP ProCurve 2650. Jeden komputer służy do konfiguracji przez konsolę/telnet, drugi jako stacja testująca. Parametry PuTTY (konsola): **9600 bps, 8 bitów, brak parzystości, 1 bit stopu, brak flow control**.

---

## Schemat stanowiska i podłączenie fizyczne

```
[KOMPUTER A] ──── kabel konsolowy RJ45 ──── [port CONSOLE przełącznika HP]
[KOMPUTER A] ──── kabel prosty UTP ──────── [port 1 przełącznika]   ← VLAN 1
[KOMPUTER B] ──── kabel prosty UTP ──────── [port 17 przełącznika]  ← VLAN 20
```

| Kabel | Typ | Od → Do |
|-------|-----|---------|
| Konsolowy | RJ45 (seryjny HP) | Komputer A COM → port CONSOLE HP |
| Sieciowy (A) | UTP prosty | Komputer A karta sieciowa → port 1 switcha |
| Sieciowy (B) | UTP prosty | Komputer B karta sieciowa → port 17 switcha |

> **Drugi przełącznik (Zadanie C.6):** połącz oba przełączniki kablem UTP prostym między portami **tagowanymi (trunk)** – np. port 24 ↔ port 24.

---

## Zadanie A – Pierwsze uruchomienie i podstawowa konfiguracja IP

### KOMPUTER A – logowanie przez PuTTY (Serial)

Otwórz PuTTY → Connection type: **Serial** → Port: COM1 (lub COM3 – sprawdź w Menedżerze urządzeń) → Speed: 9600 → Open.

Po włączeniu przełącznika poczekaj na prompt i wciśnij Enter:

```
! Wejdź w tryb uprzywilejowany
Switch> enable
Switch#

! Wejdź w tryb konfiguracji
Switch# configure terminal
Switch(config)#
```

### KOMPUTER A – Adres IP na interfejsie VLAN 1

```
! Wejdź do konfiguracji VLAN 1 (domyślny VLAN)
Switch(config)# vlan 1
Switch(vlan-1)# ip address 192.168.10.1 255.255.255.0
Switch(vlan-1)# exit
```

> Zasada: **jeden VLAN = jeden interfejs IP**. VLAN 1 to domyślny VLAN – wszystkie porty są do niego przypisane fabrycznie.

### KOMPUTER A – Sprawdzenie konfiguracji

```
Switch# show running-config
Switch# show vlan
Switch# show interfaces brief
```

### KOMPUTER B – Konfiguracja sieciowa stacji

W systemie Windows (Panel sterowania → Sieć → IPv4):
- IP: `192.168.10.2`
- Maska: `255.255.255.0`
- Brama: `192.168.10.1`

Test połączenia z VLAN 1:
```
ping 192.168.10.1
```

---

## Zadanie B – Zabezpieczenia: Telnet, WWW, SSH

### KOMPUTER A – Włączenie Telnetu

```
Switch(config)# telnet-server

! Sprawdzenie stanu
Switch(config)# show telnet

! Wyłączenie (jeśli potrzeba)
Switch(config)# no telnet
```

### KOMPUTER B – Test Telnetu

Otwórz PuTTY → Connection type: **Telnet** → Host: `192.168.10.1` → Port: 23 → Open.

Zaloguj się tak samo jak przez konsolę.

### KOMPUTER A – Włączenie serwera WWW

```
Switch(config)# web-management

! Wyłączenie
Switch(config)# no web-management
```

### KOMPUTER B – Test WWW

Otwórz przeglądarkę → wpisz `http://192.168.10.1` → zaloguj się.

### KOMPUTER A – Konfiguracja SSH

```
! Ustaw długość klucza
Switch(config)# ip ssh key-size 1024

! Wygeneruj klucz (można powtarzać)
Switch(config)# crypto key generate

! Zmień port jeśli inny niż 22 (opcjonalnie)
Switch(config)# ip ssh port 2222

! Włącz serwer SSH
Switch(config)# ip ssh

! Sprawdzenie
Switch(config)# show ip ssh

! Wyłączenie
Switch(config)# no ip ssh
```

### KOMPUTER B – Test SSH

Otwórz PuTTY → Connection type: **SSH** → Host: `192.168.10.1` → Port: 2222 → Open.

---

## Zadanie C – Konfiguracja VLAN

### KOMPUTER A – Sprawdzenie aktualnych VLAN

```
! Lista wszystkich VLAN
Switch(config)# show vlan

! Szczegóły konkretnego VLAN (np. VLAN 1)
Switch(config)# show vlan 1
```

### KOMPUTER A – Tworzenie dwóch nowych VLAN

```
! Utwórz VLAN 20
Switch(config)# vlan 20
Switch(vlan-20)# exit

! Utwórz VLAN 21
Switch(config)# vlan 21
Switch(vlan-21)# exit
```

### KOMPUTER A – Przypisanie portów do VLAN

```
! Wejdź do konfiguracji VLAN 20
Switch(config)# vlan 20

! Port NIETAGOWANY (access) → dla stacji PC (Komputer B)
Switch(vlan-20)# untagged 17-20

! Port TAGOWANY (trunk) → dla połączenia z innym przełącznikiem
Switch(vlan-20)# tagged 24

! Wyjście
Switch(vlan-20)# exit

! VLAN 21 – inne porty
Switch(config)# vlan 21
Switch(vlan-21)# untagged 21-23
Switch(vlan-21)# tagged 24
Switch(vlan-21)# exit
```

> **Różnica tagged / untagged:**
> - `untagged` (access) → port do stacji PC, ramki bez znacznika 802.1Q
> - `tagged` (trunk) → port do innego przełącznika, ramki ze znacznikiem 802.1Q
>
> **Uwaga:** port nie może być jednocześnie `tagged` i `untagged` w różnych VLAN – to błąd konfiguracji!

### KOMPUTER A – Usuwanie portów z VLAN

```
Switch(config)# vlan 20
Switch(vlan-20)# no untagged 17-20
Switch(vlan-20)# no tagged 24
Switch(vlan-20)# exit
```

### KOMPUTER A – Adresy IP dla VLAN

```
! Adres IP interfejsu VLAN 1 (już skonfigurowany w Zadaniu A)
Switch(config)# vlan 1
Switch(vlan-1)# ip address 192.168.10.1 255.255.255.0
Switch(vlan-1)# exit

! Adres IP interfejsu VLAN 20 (zmienione: 172.16.20.x zamiast 200.200.0.x)
Switch(config)# vlan 20
Switch(vlan-20)# ip address 172.16.20.1 255.255.255.0
Switch(vlan-20)# exit

! Adres IP interfejsu VLAN 21
Switch(config)# vlan 21
Switch(vlan-21)# ip address 172.16.21.1 255.255.255.0
Switch(vlan-21)# exit
```

### KOMPUTER A – Weryfikacja interfejsów

```
Switch# show interfaces
Switch# show interfaces brief
Switch# show vlan
```

### KOMPUTER B – Testy komunikacji między VLAN

Zmień konfigurację IP stacji (port 17 należy do VLAN 20):
- IP: `172.16.20.2`
- Maska: `255.255.255.0`
- Brama: `172.16.20.1`

```
! Test w obrębie VLAN 20
ping 172.16.20.1

! Test między VLAN (przed włączeniem routingu – powinien FAIL)
ping 192.168.10.1
```

### Zadanie C.6 – Połączenie dwóch przełączników (praca z parą)

```
! Fizycznie: kabel UTP prosty między port 24 przełącznika 1 a port 24 przełącznika 2

! Na przełączniku 2 (Komputer A przełącza kabel konsolowy):
Switch(config)# vlan 20
Switch(vlan-20)# tagged 24
Switch(vlan-20)# untagged 1-8
Switch(vlan-20)# exit

Switch(config)# vlan 21
Switch(vlan-21)# tagged 24
Switch(vlan-21)# untagged 9-16
Switch(vlan-21)# exit
```

Sprawdź czy stacje podłączone do tego samego VLAN na różnych przełącznikach się pingują.

---

## Zadanie D – Inne ustawienia konfiguracyjne

### KOMPUTER A – Nazwa hosta

```
Switch(config)# hostname SW-LAB
SW-LAB(config)#
```

### KOMPUTER A – Podgląd konfiguracji

```
Switch# show running-config
```

### KOMPUTER A – SNMP community

```
Switch(config)# snmp-server community "labread" unrestricted
```

### KOMPUTER A – Konfiguracja QoS na porcie

```
! Wybierz port 4
Switch(config)# interface 4

! Ustaw priorytet QoS (0–7, gdzie 7 = najwyższy)
Switch(eth-4)# qos priority 5
Switch(eth-4)# exit
```

### KOMPUTER A – Port Trunk (agregacja łączy)

```
! Agreguj porty 1–4 w jeden logiczny trunk o nazwie "Trunk1"
Switch(config)# trunk 1-4 Trunk1
```

> Drugi przełącznik musi mieć tak samo skonfigurowane porty i połączone kablami 1:1.
> Uwaga: HP ogranicza agregację do portów w tej samej grupie 12-portowej (np. 1–12 lub 13–24).

### Skróty komend HP

| Pełna komenda | Skrót |
|---------------|-------|
| `show` | `sh` |
| `address` | `add` |
| `memory` | `mem` |
| `write` | `wr` |
| `configure` | `conf` |
| `terminal` | `t` |

### Nawigacja po menu konfiguracji

```
! Cofnij o jeden poziom
Switch(vlan-20)# exit
Switch(config)#

! Wyjdź na sam szczyt (tryb exec)
Switch(vlan-20)# end
Switch#
```

---

## Zadanie E – Rutowanie między VLAN

### KOMPUTER A – Włączenie rutowania IP

```
Switch(config)# ip routing

! Weryfikacja
Switch# show ip route
```

### KOMPUTER A – Definicja trzech VLAN z adresacją

```
Switch(config)# vlan 10
Switch(vlan-10)# ip address 10.10.10.1 255.255.255.0
Switch(vlan-10)# untagged 1-8
Switch(vlan-10)# exit

Switch(config)# vlan 20
Switch(vlan-20)# ip address 10.20.20.1 255.255.255.0
Switch(vlan-20)# untagged 9-16
Switch(vlan-20)# exit

Switch(config)# vlan 30
Switch(vlan-30)# ip address 10.30.30.1 255.255.255.0
Switch(vlan-30)# untagged 17-22
Switch(vlan-30)# exit
```

### KOMPUTER A – Statyczne trasy rutowania

```
! Trasa statyczna: sieć docelowa, maska, brama
Switch(config)# ip route 10.10.10.0 255.255.255.0 10.20.20.2

! Usunięcie trasy
Switch(config)# no ip route 10.10.10.0 255.255.255.0 10.20.20.2

! Domyślna trasa (wszystko co nie pasuje → idzie tu)
Switch(config)# ip route 0.0.0.0 0.0.0.0 10.10.10.254

! Trasa null – porzuca pakiety do danej sieci
Switch(config)# ip route 10.99.0.0 255.255.255.0 reject
```

### KOMPUTER A – Weryfikacja rutowania

```
Switch# show ip route
Switch# show vlan
Switch# show interfaces brief
```

### KOMPUTER B – Test rutowania między VLAN

Podłącz Komputer B do portu 9 (VLAN 20). Ustaw adres IP:
- IP: `10.20.20.2`
- Maska: `255.255.255.0`
- Brama: `10.20.20.1`

Przełącz Komputer A do portu 1 (VLAN 10). Ustaw adres IP:
- IP: `10.10.10.2`
- Maska: `255.255.255.0`
- Brama: `10.10.10.1`

```
! Z Komputera B – ping do VLAN 10 (przez ruter przełącznika)
ping 10.10.10.2

! Traceroute – zobaczysz przeskok przez interfejs przełącznika
tracert 10.10.10.2     (Windows)
traceroute 10.10.10.2  (Linux)
```

### Tabela konfiguracji PC dla testów rutowania

| Stacja | Port switcha | VLAN | Adres IP | Maska | Brama |
|--------|-------------|------|----------|-------|-------|
| Komputer A | 1 | 10 | 10.10.10.2 | 255.255.255.0 | 10.10.10.1 |
| Komputer B | 9 | 20 | 10.20.20.2 | 255.255.255.0 | 10.20.20.1 |

---

## Zadanie F – Zarządzanie przez WWW

### KOMPUTER B – Dostęp przez przeglądarkę

Upewnij się że `web-management` jest włączone (Zadanie B), następnie:

```
http://192.168.10.1
```

Przez GUI możesz zrobić wszystko co przez konsolę: VLAN, porty, routing, SSH, SNMP. Sprawdź każdą zakładkę i porównaj z tym co skonfigurowałeś konsolą.

---

## Szybka ściąga – kluczowe komendy HP ProCurve

### Tryby pracy

| Tryb | Prompt | Jak wejść |
|------|--------|-----------|
| User EXEC | `Switch>` | domyślny po logowaniu |
| Privileged EXEC | `Switch#` | `enable` |
| Global Config | `Switch(config)#` | `configure terminal` |
| VLAN Config | `Switch(vlan-X)#` | `vlan X` |
| Interface Config | `Switch(eth-X)#` | `interface X` |

### Komendy

| Komenda | Opis |
|---------|------|
| `show running-config` | Aktualna konfiguracja |
| `show vlan` | Lista VLAN i portów |
| `show vlan <nr>` | Szczegóły konkretnego VLAN |
| `show interfaces` | Stan interfejsów |
| `show interfaces brief` | Skrócony stan interfejsów |
| `show ip route` | Tablica routingu |
| `show telnet` | Stan serwera Telnet |
| `show ip ssh` | Stan serwera SSH |
| `vlan <nr>` | Wejdź do konfiguracji VLAN |
| `ip address <IP> <maska>` | Adres IP interfejsu VLAN |
| `ip address <IP>/<prefix>` | Adres IP (notacja CIDR) |
| `untagged <porty>` | Port access (do PC) |
| `tagged <porty>` | Port trunk (do przełącznika) |
| `no untagged <porty>` | Usuń port z VLAN |
| `ip routing` | Włącz routing między VLAN |
| `no ip routing` | Wyłącz routing |
| `ip route <sieć> <maska> <brama>` | Trasa statyczna |
| `ip route 0.0.0.0 0.0.0.0 <brama>` | Trasa domyślna |
| `ip route <sieć> <maska> reject` | Trasa null (porzuć pakiety) |
| `telnet-server` | Włącz Telnet |
| `web-management` | Włącz serwer WWW |
| `ip ssh` | Włącz SSH |
| `crypto key generate` | Generuj klucz SSH |
| `ip ssh key-size 1024` | Długość klucza SSH |
| `trunk <porty> <nazwa>` | Agregacja portów (LAG) |
| `hostname <nazwa>` | Nazwa hosta |
| `interface <nr>` | Konfiguracja portu |
| `qos priority <0-7>` | Priorytet QoS portu |
| `snmp-server community "<nazwa>" unrestricted` | Konfiguracja SNMP |
| `exit` | Cofnij o jeden poziom |
| `end` | Wróć do trybu exec |