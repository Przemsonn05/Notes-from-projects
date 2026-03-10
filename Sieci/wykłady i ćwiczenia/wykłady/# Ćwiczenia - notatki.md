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