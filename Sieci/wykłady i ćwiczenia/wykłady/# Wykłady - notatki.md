# Wykłady – notatki

## **Wykład 03.03.2026**

## Sieć komputerowa

**Sieć komputerowa** – zbiór komputerów i innych urządzeń komputerowych połączonych ze sobą kanałami komunikacyjnymi.  
Sieć komputerowa umożliwia wzajemne przekazywanie informacji oraz udostępnianie zasobów (np. plików, drukarek, usług).

---

## Węzeł sieci komputerowej

**Węzeł (node)** – jednostka w sieci komputerowej, która może wysyłać lub odbierać dane.  
Węzły są połączone z innymi węzłami za pomocą mediów komunikacyjnych.

Przykłady węzłów:
- komputer
- serwer
- router
- drukarka sieciowa
- smartfon

---

## Medium transmisyjne, sieć transmisyjna i segment sieci

**Medium transmisyjne** – nośnik umożliwiający przekazywanie informacji w postaci:
- impulsów elektrycznych
- fal elektromagnetycznych
- światła
- fal radiowych

Przykłady:
- kabel miedziany
- światłowód
- sieć bezprzewodowa (Wi-Fi)

**Sieć transmisyjna** – system umożliwiający komunikację wielu urządzeń poprzez medium transmisyjne z wykorzystaniem określonych protokołów komunikacyjnych.

**Segment sieci komputerowej** – fragment sieci komputerowej oddzielony przez urządzenie sieciowe (np. router, przełącznik) lub logicznie wydzielony.

Ruch w sieci można podzielić na:
- **wewnątrzsegmentowy** – odbywający się w obrębie jednego segmentu
- **międzysegmentowy** – odbywający się pomiędzy różnymi segmentami

---

## Internetwork a Internet

| Pojęcie | Znaczenie |
|---|---|
| **Internetwork** | dowolne połączenie wielu sieci |
| **Internet** | największa na świecie internetwork |

**Internetwork** – dowolna sieć złożona z wielu połączonych sieci komputerowych.

**Internet** – globalna publiczna sieć komputerowa łącząca miliony sieci na całym świecie.

---

## Broadcast domain – domena rozgłoszeniowa

**Domena rozgłoszeniowa (broadcast domain)** – fragment sieci, w którym urządzenie może wysłać pakiet rozgłoszeniowy (broadcast), który zostanie odebrany przez wszystkie urządzenia w tej samej domenie.

---

## Domena kolizyjna

**Domena kolizyjna (collision domain)** – fragment sieci, w którym jednoczesna transmisja danych przez dwa urządzenia może spowodować kolizję sygnałów.

W nowoczesnych sieciach domeny kolizyjne są ograniczane przez urządzenia takie jak przełączniki (switch).

---

## Protokoły – definicje

**Protokół komunikacyjny** – zbiór reguł określających zasady nawiązywania komunikacji oraz wymiany danych pomiędzy urządzeniami w sieci komputerowej.

### Protocol suite (zestaw protokołów)

Model opisujący reguły komunikacji w sieci komputerowej wraz z zestawem współpracujących protokołów.

Przykład:
- **:contentReference[oaicite:0]{index=0}**

### Protocol stack (stos protokołów)

Implementacja protocol suite w konkretnej technologii.  
Protokoły w stosie są ułożone warstwowo i współpracują ze sobą, a działanie jednego protokołu często wymaga działania innego.

---

## Pakiety w sieci komputerowej

**Pakiet** – jednostka informacji przesyłana w sieci komputerowej.

Pakiet zawiera:
- **dane (payload)** – właściwą treść przesyłanej informacji
- **informacje sterujące** – potrzebne do przesłania danych

### Elementy pakietu

**Nagłówek (header)**  
Informacje znajdujące się na początku pakietu, np.:
- adres nadawcy
- adres odbiorcy
- typ danych
- numer pakietu

**Trailer**  
Informacje znajdujące się na końcu pakietu, często używane do:
- kontroli błędów
- weryfikacji poprawności transmisji

---

## Datagram

**Datagram** – blok danych przesyłany w sieci komputerowej, który zawiera wszystkie informacje potrzebne do jego dostarczenia.

Nie wymaga wcześniejszego ustanowienia połączenia między nadawcą a odbiorcą.

W niektórych przypadkach datagram jest równoważny pakietowi.

---

## Ramka (frame)

**Ramka** – jednostka danych używana w warstwie łącza danych.  
Jeżeli datagram nie jest umieszczony w innym datagramie, bywa określany jako ramka.

---

## Payload

**Payload** – właściwa część pakietu zawierająca przesyłane dane użytkownika (np. fragment pliku, wiadomość, dane strony internetowej).

Nie obejmuje nagłówków ani informacji sterujących protokołów.

---

## Usługi w sieci komputerowej

**Usługa** – zbiór funkcji udostępnianych użytkownikom lub komponentom systemów informatycznych.

Usługi umożliwiają np.:
- przesyłanie danych
- dostęp do plików
- komunikację między aplikacjami

Usługa między jednostkami jest realizowana poprzez **interfejsy**.

Protokół komunikacyjny stanowi podstawę działania usługi, ale jest **niewidoczny dla użytkownika**, który korzysta jedynie z udostępnionych funkcji.

---

## Czym jest funkcja elementarna?

**Funkcja elementarna** – podstawowa operacja wykonywana przez usługę sieciową.

Przykłady:
- wysłanie danych
- odebranie danych
- nawiązanie połączenia
- zakończenie połączenia

---

## Czym jest jednostka w sieci komputerowej?

**Jednostka (entity)** – element systemu informatycznego uczestniczący w komunikacji sieciowej.

Może to być:
- program
- proces
- moduł systemu operacyjnego
- aplikacja sieciowa

Jednostki komunikują się ze sobą przy użyciu usług i protokołów.

---

## Czym jest interfejs w sieciach komputerowych?

**Interfejs** – zestaw reguł i metod umożliwiających komunikację między różnymi komponentami systemu.

Interfejs określa:
- jakie operacje można wykonać
- w jaki sposób można z nich korzystać

Przykład:
- aplikacja korzysta z interfejsu systemu operacyjnego do wysyłania danych przez sieć.

