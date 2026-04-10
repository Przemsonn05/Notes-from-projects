# Ciekawe zagadnienia bazując na labach z ML

## Z lab4

### Po co as_frame = True

Domyślnie Scikit-learn zwraca dane w formacie tablic NumPy. Jeśli ustawisz True, dane zostaną załadowane jako pandas DataFrame.

Dlaczego to ważne? DataFrame jest znacznie czytelniejszy dla człowieka – ma nazwane kolumny i indeksy, co ułatwia analizę (zobaczysz to w następnym kroku).

### Po co robimy .values

Zamieniasz obiekt typu DataFrame (tabelę Pandas) na czystą tablicę NumPy (macierz liczb).

Po co? Większość algorytmów w bibliotece Scikit-learn preferuje surowe tablice liczbowe zamiast rozbudowanych obiektów Pandas. Usuwasz w ten sposób nagłówki i indeksy, zostawiając same dane do obliczeń.

### Czym jest LinearSVC

To klasyfikator, który próbuje narysować prostą linię (lub płaszczyznę) oddzielającą jedną grupę od drugiej.

Słowo "Linear" oznacza, że algorytm zakłada, iż dane da się rozdzielić linią prostą (w 2D), płaszczyzną (w 3D) lub hiperpłaszczyzną (w wielu wymiarach).

Zaleta: Jest bardzo szybki, nawet przy ogromnej liczbie danych.

Wada: Jeśli Twoje dane układają się np. w kształt koła (jedna klasa w środku drugiej), LinearSVC sobie z tym nie poradzi, bo nie potrafi "zginać" linii podziału.

### Różnica między SVC a SVR

O ile w klasyfikacji (SVC) szukaliśmy linii, która trzyma punkty jak najdalej od siebie, o tyle w regresji (SVR) szukamy linii, która trzyma punkty jak najbliżej siebie.

Wyobraź sobie, że Twoim zadaniem jest narysowanie prostej linii przez chmurę punktów.

- W zwykłej regresji liniowej każda odległość punktu od linii jest karana.

- W LinearSVR tworzymy wokół linii "bezpieczny korytarz" (tubę) o szerokości określanej przez parametr $\epsilon$ (epsilon). 

Dopóki punkty znajdują się wewnątrz tej tuby, model uznaje, że błąd wynosi zero. Nie przejmuje się nimi. Model zaczyna "cierpieć" (naliczać karę) dopiero wtedy, gdy punkty wypadają poza tubę.

Model LinearSVR próbuje zbalansować dwie rzeczy:

- Płaskość: Chce, aby linia była jak najmniej "stroma" (małe wagi $w$).

- Błędy: Chce, aby jak najwięcej punktów mieściło się w tubie. Te, które się nie mieszczą, są nazywane wektorami nośnymi (support vectors) i to one "wyginają" linię tak, by do nich pasowała.

![alt text](image-3.png)

### Czym jest loss i jakie ma opcje

Parametr loss określa funkcję straty, czyli matematyczny sposób karania modelu za błędy podczas nauki. W LinearSVC masz dwie główne opcje:

1. loss='hinge' (Strata zawiasowa)
To klasyczne podejście dla SVM.

Jak działa: Model dostaje "karę" tylko wtedy, gdy punkt danych znajdzie się po złej stronie marginesu lub wewnątrz niego. Jeśli punkt jest daleko po właściwej stronie, kara wynosi zero.

Kiedy stosować: Gdy zależy Ci na matematycznym "oryginale" SVM. Wymaga ona jednak użycia parametru dual=True w konfiguracji modelu (to techniczny wymóg biblioteki scikit-learn).

2. loss='squared_hinge' (Kwadratowa strata zawiasowa)
To jest opcja domyślna w LinearSVC.

Jak działa: To po prostu hinge podniesione do kwadratu. Kara za błędy rośnie tutaj kwadratowo, co oznacza, że model bardzo surowo traktuje punkty, które są ewidentnie po złej stronie.

Kiedy stosować: Zazwyczaj daje bardziej stabilne wyniki i szybciej się oblicza. Jest domyślnym wyborem w większości przypadków.

### Po co jest max_iter

max_iter=10000: Algorytm uczy się iteracyjnie (metodą prób i błędów). Czasami domyślna liczba kroków (zwykle 1000) to za mało, by znaleźć idealną linię, więc zwiększamy ją do 10 000, żeby dać mu "więcej czasu na myślenie".

### Czym jest Pipeline

W scikit-learn Pipeline to obiekt, który wiąże kilka kroków przetwarzania danych w jeden wspólny obiekt. Zazwyczaj składa się z:

Transformatorów (dowolna liczba): kroki, które czyszczą lub zmieniają dane (np. StandardScaler, uzupełnianie brakujących wartości, wybór cech).

Estymatora (tylko jeden, na końcu): czyli Twój właściwy model (np. LinearSVC, RandomForest).

Dlaczego stosujemy?

- Zapobieganie "wyciekowi danych" (Data Leakage)
    
    To najważniejszy, techniczny powód.

    - Problem: Jeśli przeskalujesz całe dane (używając średniej z całego zbioru) przed podziałem na testowe i treningowe, Twój model "podejrzy" informacje ze zbioru testowego. To tak, jakby uczeń przed egzaminem zobaczył średnią ocen z arkusza, którego jeszcze nie rozwiązywał – to oszustwo.

    - Rozwiązanie: Pipeline dba o to, by skaler uczył się parametrów (średniej i odchylenia) tylko na danych treningowych, a potem stosował te same parametry do danych testowych.

### Skalowanie - StandardScaler()

Co robi? Standaryzuje dane, czyli przekształca je tak, aby każda cecha miała średnią równą 0 i odchylenie standardowe 1.

Po co to tutaj jest? Pamiętasz, jak wspomniałem, że LinearSVC jest wrażliwy na skalę? mean area (duże liczby) i mean smoothness (małe ułamki) bez skalera "dezorientują" model. Skaler sprawia, że obie cechy są traktowane jako równie ważne.

### Jak liczymy metryke accuracy

![alt text](image-4.png)

### Czym jest mean_squared_error

1. Definicja w jednym zdaniuMSE (Mean Squared Error), czyli błąd średniokwadratowy, to średnia arytmetyczna kwadratów różnic między wartościami przewidzianymi przez model a wartościami rzeczywistymi.

2. Rozbicie na czynniki pierwsze (Proces)Wyobraź sobie, że MSE to trzyetapowy proces "rozliczania" modelu:

- Liczymy błąd (Residuum): Dla każdego punktu sprawdzamy, o ile model się pomylił ($y - \hat{y}$).

- Podnosimy do kwadratu: Każdy błąd potęgujemy. To kluczowy krok, bo eliminuje on znaki minus (błędy się nie znoszą) i sprawia, że duże błędy stają się gigantycznymi karami.

- Wyciągamy średnią: Sumujemy te kwadraty i dzielimy przez liczbę wszystkich obserwacji ($n$), żeby wiedzieć, jak model radzi sobie "przeciętnie".

### Co zwracają podwójne nawiasy `[[...]]`

W bibliotece Pandas nawiasy kwadratowe służą do wybierania danych, ale ich liczba zmienia to, co otrzymasz w wyniku:

- Pojedynczy nawias df['kolumna']: Zwraca obiekt typu Series (to po prostu jedna kolumna, coś jak lista).

- Podwójny nawias df[['kolumna1', 'kolumna2']]: Zwraca obiekt typu DataFrame (czyli mniejszą tabelę).

Modele uczenia maszynowego (jak Twój LinearSVC) zawsze oczekują danych wejściowych $X$ w formacie dwuwymiarowym (tabela z wierszami i kolumnami). Nawet gdybyś wybierał tylko jedną kolumnę, użycie podwójnego nawiasu df[['petal length (cm)']] gwarantuje, że wynik będzie "pionową tabelą", a nie "płaską listą".

### Jak działa reshape

Funkcja reshape przyjmuje argumenty w formacie (liczba_wierszy, liczba_kolumn).

1 (drugi argument): Mówisz programowi: "Chcę mieć dokładnie jedną kolumnę".

-1 (pierwszy argument): To jest "magiczny" znak zastępczy. Mówi on NumPy: "Oblicz resztę za mnie". NumPy patrzy, ile masz wszystkich elementów w danych i tak dobiera liczbę wierszy, żeby wszystko pasowało do tej jednej kolumny.

### Czym jest kernel='poly'

W standardowym modelu liniowym szukamy prostej linii. Jednak wiele danych w świecie rzeczywistym układa się w krzywe.

- Zamiast fizycznie dodawać nowe kolumny do tabeli (jak $x^2, x^3$), Kernel pozwala modelowi obliczyć matematyczne relacje między punktami tak, jakby znajdowały się one w przestrzeni o wielu wymiarach.

- Zaleta: Oszczędzasz pamięć RAM, bo nie tworzysz gigantycznej tabeli z nowymi cechami. Wszystko dzieje się "w locie" podczas obliczeń matematycznych.

### Kernel trick

To jest „supermoc” SVM. Często danych nie da się oddzielić prostą linią na płaskiej kartce (np. gdy kropki smogowe są otoczone kropkami czystymi).

SVM „wypycha” dane do trzeciego (lub wyższego) wymiaru.

To tak, jakbyś rzucił kropki w górę i w locie przeciął je kartką papieru. Z perspektywy 2D wygląda to na skomplikowaną krzywą, ale w wyższym wymiarze to była prosta płaszczyzna.

### Parametry C i Coef0

C: To siła regularyzacji. Małe C oznacza szeroką, wybaczającą błędy "tubę". Duże C zmusza model do bardzo ciasnego trzymania się punktów (ryzyko overfittingu).

    - Małe C (np. 0.1, 1): Model jest „wyluzowany”. Pozwala na to, by niektóre punkty znalazły się po złej stronie granicy, byle tylko zachować jak najszerszy i najprostszy margines.

        - Zaleta: Lepiej radzi sobie z nowymi danymi (dobra generalizacja).

        - Ryzyko: Może zbyt uprościć problem (Underfitting).

    - Duże C (np. 100, 1000): Model staje się „perfekcjonistą”. Chce za wszelką cenę uniknąć błędów w treningu, nawet jeśli oznacza to stworzenie bardzo powyginanej, nienaturalnej granicy.

        - Zaleta: Wysoka celność na danych, które już zna.

        - Ryzyko: Przetrenowanie (Overfitting) – model „nauczy się na pamięć” Twoich outlierów z Krakowa i zawiedzie w przyszłości.

coef0: Kluczowy przy jądrze wielomianowym. Kontroluje, jak bardzo model ma być zdominowany przez wysokie potęgi (stopień 4) w stosunku do niższych. Pomaga kontrolować "wygięcie" krzywej.

Parametr coef0 występuje tylko w jądrach poly i sigmoid. W matematyce oznacza on stałą $r$ we wzorze:

$$K(x, y) = (\gamma \cdot \langle x, y \rangle + r)^d$$

W praktyce kontroluje on, jak bardzo model ma być podatny na wpływ wielomianów wyższego stopnia w porównaniu do tych niższych.

- Jeśli coef0 = 0: Model opiera się głównie na czystym mnożeniu cech. Jest bardzo wrażliwy na skalę danych.

- Jeśli coef0 > 0: Dodajemy stałą wartość do wyniku przed podniesieniem go do potęgi. To sprawia, że model staje się bardziej elastyczny i stabilny.

Pomaga to modelowi „zauważyć” prostsze zależności, nawet jeśli kazałeś mu użyć skomplikowanego wielomianu (np. stopnia 3).

### Dlaczego scoring='neg_mean_squared_error'

Używamy MSE (o którym rozmawialiśmy), ale w wersji negatywnej (neg_).

Po co? Scikit-learn zawsze próbuje zmaksymalizować wynik. Ponieważ w błędach chcemy, aby wynik był jak najniższy, biblioteka zamienia go na minus (np. błąd 5 staje się -5). Im "większa" liczba (bliższa zeru), tym lepszy model.

### Co robi parametr n_jobs

n_jobs=-1: To dopalacz. Mówi komputerowi: "użyj wszystkich dostępnych rdzeni procesora". Dzięki temu testowanie 9 kombinacji pójdzie znacznie szybciej, bo będą liczone równolegle.

### Co robi .best_params_

Przykład: best_C = gs.best_params_['svr__C']:
Obiekt gs (Twój GridSearchCV) po zakończeniu przeszukiwania przechowuje zwycięską kombinację w słowniku best_params_. Tutaj wyciągasz wartość C, która dała najniższy błąd (najmniejszy neg_mean_squared_error).

### Różnica między SVR a PolynomialFeatures

A. PolynomialFeatures + Linear Model (Podejście "Ręczne")

- Tutaj fizycznie tworzysz nowe kolumny w danych przed uruchomieniem modelu.

- Jeśli masz cechę $x$, to PolynomialFeatures stworzy $x^2, x^3$ itd.Problem: Liczba kolumn rośnie drastycznie (eksplozja wymiarowości). 

- Jeśli masz 10 cech i stopień wielomianu 3, nagle masz setki kolumn. To spowalnia komputer i grozi przetrenowaniem.

B. SVR(kernel='poly') (Podejście "Kernel Trick")

- Tutaj nie tworzysz żadnych nowych kolumn. Twoje dane wejściowe pozostają takie same.

- Magia: SVR oblicza „podobieństwo” między punktami tak, jakby były one w wyższym wymiarze, ale robi to za pomocą sprytnego wzoru matematycznego (jądra).

- Zaleta: Jest znacznie szybszy obliczeniowo przy wielu cechach i nie zajmuje dodatkowej pamięci RAM na nowe kolumny.

### Kiedy nie warto stosowac accuracy

Główne wady Accuracy:

1. Problem niezbalansowanych klas (Imbalanced Data):

- Wyobraź sobie, że w Krakowie dni z ekstremalnym smogiem (powyżej 200 µg/m³) jest tylko 5 w roku. Pozostałe 360 dni jest "w normie".Jeśli Twój model będzie zawsze, bezmyślnie mówił: „Nie będzie smogu”, to jego Accuracy wyniesie 98,6% ($360/365$).Model jest matematycznie „genialny”, ale w praktyce bezużyteczny, bo nie wykrył ani jednego alarmu.

2. Brak rozróżnienia na typy błędów:

- Accuracy traktuje każdy błąd tak samo. W przypadku jakości powietrza mamy dwa rodzaje pomyłek:

    - False Positive (Fałszywy alarm): Model mówi „będzie smog”, a jest czysto. Ludzie niepotrzebnie zostają w domu. (Mała szkodliwość).
    - False Negative (Przeoczenie): Model mówi „jest super”, a w rzeczywistości pył dusi miasto. Ludzie wychodzą biegać i niszczą sobie płuca. (Bardzo duża szkodliwość).Accuracy nie powie Ci, który błąd model popełnia częściej.

## Z lab5

### Czym jest XAI

### Jak działa Random Forest

### Czym modele drzwiaste typu Random Forest są podatne na overfitting

### Parametry Random Forest

