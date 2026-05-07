# Ciekawe zagadnienia bazując na labach z ML

## lab 1 - wprowadzenie

## Z lab2

### SVC i SGD Classifier

SVC (z rodziny Support Vector Machines - SVM) to niezwykle potężny algorytm klasyfikacyjny. Wyobraź sobie, że masz na stole rozsypane czerwone i niebieskie piłeczki. Twoim celem jest położenie między nimi prostego kija (hiperpłaszczyzny), tak aby rozdzielał kolory.

Ale SVC idzie o krok dalej: nie szuka jakiegokolwiek kija. Szuka takiego kija, który jest jak najdalej od najbliższych piłeczek obu kolorów. Chce stworzyć jak najszerszą "ulicę" (margines) oddzielającą klasy. Piłeczki, które leżą na krawędziach tej "ulicy", to właśnie Wektory Nośne (Support Vectors) – one w 100% definiują położenie granicy. Reszta danych mogłaby w ogóle zniknąć!

Co, jeśli danych nie da się oddzielić prostą linią? Tu wkracza magia, czyli tzw. Sztuczka Jądrowa (Kernel Trick). Model używa zaawansowanych funkcji matematycznych (np. RBF - Radial Basis Function), by w locie przenieść dane do wyższych wymiarów, gdzie nagle można je przekroić płaską kartką papieru.

Funkcje matematyczne:

- Równanie hiperpłaszczyzny (granicy decyzyjnej): $$w^T x + b = 0$$Gdzie $w$ to wektor wag (prostopadły do hiperpłaszczyzny), $x$ to cechy, a $b$ to przesunięcie (bias).

- Szerokość marginesu: Wynosi $\frac{2}{||w||}$. Chcemy go zmaksymalizować, co matematycznie sprowadza się do minimalizacji $||w||$.

- Funkcja celu optymalizacji (Miękki Margines): W prawdziwym świecie dane rzadko są idealnie separowalne, dlatego pozwalamy modelowi na drobne błędy za pomocą "zmiennych luzu" $\xi_i$ (xi).$$\min_{w, b, \xi} \left( \frac{1}{2} ||w||^2 + C \sum_{i=1}^{n} \xi_i \right)$$ Pod warunkiem: $y_i(w^T x_i + b) \ge 1 - \xi_i$ oraz $\xi_i \ge 0$.

- Parametr $C$ jest tutaj królem:

    - Małe $C$: "Wyluzowany" model, pozwala na błędy, byle "ulica" była szeroka (chroni przed overfittingiem).

    - Duże $C$: Surowy model, drastycznie zwęża "ulicę", byle tylko każda kropka była po właściwej stronie (ryzyko overfittingu).

Algorytm w uproszczeniu (Sekwencyjna Minimalizacja Optymalna - SMO):

- Model rozwiązuje to równanie za pomocą zaawansowanego programowania kwadratowego (Quadratic Programming).

- Zmapuj dane do wyższego wymiaru używając wybranego Jądra (Kernel: Linear, Polynomial, RBF).

- Znajdź punkty leżące na brzegach (Wektory Nośne).

- Wylicz wagi $w$ i $b$ tak, aby zrównoważyć maksymalizację marginesu z karą $C$ za błędnie sklasyfikowane punkty.

- Klasyfikacja nowego punktu polega na sprawdzeniu, po której stronie hiperpłaszczyzny ten punkt wylądował.

Standardowe modele (jak klasyczne SVC) podczas nauki analizują cały zbiór danych na raz, aby zrobić jeden krok optymalizacyjny. Przy milionach rekordów zużyje to całą pamięć RAM i potrwa wieki.

SGDClassifier to sprinter. Bierze tylko jedną (lub kilka - tzw. mini-batch) losową próbkę, błyskawicznie zgaduje wynik, oblicza błąd, delikatnie poprawia wagi i biegnie do następnej próbki. Jego ścieżka do celu jest zygzakowata i chaotyczna ("stochastyczna"), ale na ogromnych zbiorach danych (Big Data) to często jedyny sposób na wytrenowanie modelu.

Funkcje matematyczne:

To, jakim matematycznie modelem stanie się SGDClassifier, zależy od wybranego parametru loss (funkcji kosztu $J(\theta)$).

- Jeśli loss='hinge': Model staje się Liniowym SVM (Support Vector Machine).$$J(\theta) = \max(0, 1 - y_i(\theta^T x_i))$$

- Jeśli loss='log_loss': Model staje się Regresją Logistyczną. $$J(\theta) = - \left[ y_i \log(h_\theta(x_i)) + (1 - y_i) \log(1 - h_\theta(x_i)) \right]$$

Algorytm (Stochastic Gradient Descent):

- Inicjalizacja: Ustaw losowe wagi $\theta$.

- Epoki: Powtarzaj poniższe kroki dla całego zbioru (np. 100 razy).

- Tasowanie (Shuffle): Wymieszaj zbiór danych treningowych.

- Iteracja stochastyczna: Dla każdej pojedynczej próbki $i$ z danych:

    - Oblicz predykcję używając aktualnych wag.

    - Wylicz gradient funkcji kosztu tylko dla tej jednej próbki.

    - Zaktualizuj wagi: $\theta := \theta - \eta \nabla J(\theta)$ (Gdzie $\eta$ to learning rate - krok uczenia, który stopniowo maleje z czasem).

Zatrzymaj się, gdy straty przestaną spadać (algorytm zbiegł).

### Accuracy score, Cross validation i Confusion matrix

1. Confusion Matrix (Macierz Pomyłek)

Gdy model klasyfikacyjny zgaduje (np. "czy to jest spam?", "czy pacjent jest chory?"), jego predykcje zderzają się z rzeczywistością. Macierz pomyłek to po prostu tabela $2 \times 2$ (dla dwóch klas), która w niezwykle przejrzysty sposób podsumowuje, gdzie dokładnie model się myli.Zamiast jednej liczby oznaczającej "błąd", macierz dzieli wyniki na cztery kategorie:

- TP (True Positive) – Prawdziwie Pozytywne: Model powiedział "TAK" (np. to jest spam) i rzeczywistość to potwierdza. Pełen sukces.

- TN (True Negative) – Prawdziwie Negatywne: Model powiedział "NIE" i miał rację. Sukces.

- FP (False Positive) – Fałszywie Pozytywne (Błąd I rodzaju): Model powiedział "TAK", ale w rzeczywistości było "NIE". Zjawisko nadgorliwości (np. wrzucenie ważnego maila od szefa do folderu spam).

- FN (False Negative) – Fałszywie Negatywne (Błąd II rodzaju): Model powiedział "NIE", ale w rzeczywistości było "TAK". Bardzo niebezpieczny błąd (np. odesłanie do domu chorego pacjenta z diagnozą, że jest zdrowy).

Dzięki macierzy pomyłek od razu widać, czy nasz model ma tendencję do "nadgorliwości" (dużo FP), czy raczej jest "zbyt ostrożny" (dużo FN).

2. Accuracy Score (Dokładność)

Gdy mamy już Macierz Pomyłek, wyliczenie accuracy jest trywialne. Jest to po prostu odsetek wszystkich poprawnych odpowiedzi w stosunku do wszystkich prób.

Wzór: $$Accuracy = \frac{TP + TN}{TP + TN + FP + FN}$$

Problem i "Paradoks Dokładności":

- Accuracy to najbardziej intuicyjna metryka, ale kryje w sobie gigantyczną pułapkę, gdy mamy do czynienia z niezbalansowanymi danymi (imbalanced data).

- Wyobraź sobie, że budujesz model wykrywający rzadką chorobę, na którą choruje 1 osoba na 1000.

- Piszesz fatalny model, który po prostu ignoruje dane pacjenta i ZAWSZE mówi: "Zdrowy" (czyli strzela samymi Negatywami).

- Jaka będzie jego skuteczność? Zgadnie dla 999 osób i pomyli się tylko 1 raz.Jego Accuracy wynosi 99.9%. Brzmi świetnie, prawda?

- A jednak model jest całkowicie bezużyteczny, bo nie wykrył ani jednej chorej osoby (współczynnik TP = 0). Dlatego przy niezbalansowanych klasach Accuracy odkładamy na bok na rzecz innych metryk (Precision, Recall, F1-Score).

3. Cross-Validation (Walidacja Krzyżowa)

Przejdźmy teraz do tego, jak testujemy model. Standardowy proces w Machine Learningu to podział danych na "Zbiór Treningowy" (np. 80% danych, na których model się uczy) i "Zbiór Testowy" (20% danych, których model nie widział i na których wyliczamy np. Accuracy).

- Problem: Taki jednorazowy podział to loteria. A co, jeśli los tak sprawił, że do zbioru testowego trafiły same najłatwiejsze i najbardziej oczywiste przypadki? Model zdobędzie 100% dokładności na teście, uznamy go za genialny, a w prawdziwym świecie całkowicie polegnie.

- Rozwiązanie: k-Fold Cross Validation (k-krotna walidacja krzyżowa): Zamiast dzielić dane raz, robimy to systematycznie i wielokrotnie, aby upewnić się, że model radzi sobie dobrze niezależnie od tego, jak potasujemy dane.

Algorytm (na przykładzie $k=5$):

- Podział: Zamiast podziału 80/20, dzielimy cały nasz zbiór danych na 5 równych części (zwanych foldami lub paczkami).

- Iteracja 1: Zostawiamy paczkę nr 1 z boku jako Zbiór Testowy. Trenujemy model na paczkach 2, 3, 4 i 5. Zapisujemy wynik Accuracy.

- Iteracja 2: Zostawiamy paczkę nr 2 jako Zbiór Testowy. Trenujemy model na paczkach 1, 3, 4 i 5. Zapisujemy wynik Accuracy.

- Powtarzanie: Kontynuujemy to 5 razy, tak aby każda z paczek (od 1 do 5) była dokładnie raz użyta jako zbiór testowy.

- Ocena końcowa: Wyciągamy średnią z 5 otrzymanych wyników Accuracy. Dodatkowo patrzymy na odchylenie standardowe wyników (jeśli raz wynik wynosi 95%, a w innej iteracji 60%, to znaczy, że nasz model jest bardzo niestabilny).

Dzięki walidacji krzyżowej każdy pojedynczy wiersz w naszych danych zostanie wykorzystany zarówno w procesie uczenia, jak i testowania Zyskujemy pewność, że wynik jest miarodajny.

## Z lab3

### LinearRegression


Regresja liniowa to model parametryczny, który zakłada, że relacja między zmiennymi objaśniającymi (cechami) a zmienną objaśnianą (wynikiem) jest wprost proporcjonalna. Model uczy się poprzez optymalizację wag tak, aby zminimalizować błąd. Najpopularniejszym sposobem "uczenia" (znajdowania wag) jest analityczna Metoda Najmniejszych Kwadratów (OLS) lub iteracyjny algorytm Spadku Gradientu (Gradient Descent).

Funkcje matematyczne:

- Hipoteza (Funkcja predykcji):$$h_\theta(x) = \theta_0 + \theta_1x_1 + \theta_2x_2 + \dots + \theta_nx_n = \theta^T x$$

Gdzie $\theta$ to wektor wag, a $x$ to wektor cech (z $x_0 = 1$ dla wyrazu wolnego).

- Funkcja kosztu (Mean Squared Error - MSE):$$J(\theta) = \frac{1}{2m} \sum_{i=1}^{m} (h_\theta(x^{(i)}) - y^{(i)})^2$$

Gdzie $m$ to liczba próbek treningowych. Dzielenie przez $2$ ułatwia późniejsze liczenie pochodnej.

Algorytm (Spadek Gradientu - Gradient Descent):

- Inicjalizacja: Ustaw początkowe wartości wag $\theta$ (np. same zera lub małe wartości losowe).

- Obliczanie predykcji: Dla każdej próbki w zbiorze danych oblicz przewidywaną wartość $h_\theta(x)$.

- Obliczanie błędu: Porównaj predykcje z rzeczywistymi wartościami $y$ za pomocą funkcji kosztu.

- Aktualizacja wag: Oblicz gradient (pochodną) funkcji kosztu i zaktualizuj każdą wagę, poruszając się w kierunku przeciwnym do gradientu (aby zminimalizować błąd): $$\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J(\theta)$$ $$\theta_j := \theta_j - \alpha \frac{1}{m} \sum_{i=1}^{m} (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)}$$

Gdzie $\alpha$ to współczynnik uczenia (learning rate) – decyduje, jak duże kroki robimy.

### KNN

k-NN to model nieparametryczny oparty na instancjach (instance-based learning). Nie posiada fazy "treningu" w tradycyjnym sensie – model po prostu zapamiętuje cały zbiór danych w pamięci (stąd nazwa lazy learning). Faza predykcji jest za to bardzo kosztowna obliczeniowo, ponieważ model musi za każdym razem obliczyć odległość nowego punktu od wszystkich punktów w zbiorze treningowym.

Funkcje matematyczne:

- Metryka odległości (np. Odległość Euklidesowa): $$d(x, x') = \sqrt{\sum_{j=1}^{n} (x_j - x'_j)^2}$$ (Inne popularne to odległość Manhattan lub Minkowskiego).

- Funkcja predykcji (Regresja - średnia arytmetyczna sąsiadów): $$\hat{y} = \frac{1}{k} \sum_{i \in N_k(x)} y_i$$ Gdzie $N_k(x)$ to zbiór $k$ najbliższych sąsiadów dla punktu $x$.

- Funkcja predykcji (Klasyfikacja - głosowanie większościowe): $$\hat{y} = \text{argmax}_c \sum_{i \in N_k(x)} I(y_i = c)$$ Gdzie $I$ to funkcja wskaźnikowa (zwraca 1, jeśli sąsiad należy do klasy $c$, inaczej 0).

Algorytm (Predykcja dla nowego punktu $x_{nowy}$):

- Wybór parametrów: Wybierz wartość $k$ (liczbę sąsiadów) oraz metrykę odległości.

- Obliczanie odległości: Przejdź przez każdy punkt $x_{treningowy}$ w zbiorze danych i oblicz jego odległość od $x_{nowy}$.

- Sortowanie: Posortuj wszystkie punkty treningowe rosnąco według obliczonej odległości.

- Wybór sąsiadów: Wybierz $k$ pierwszych punktów z posortowanej listy (najbliższych sąsiadów).

- Agregacja wyników:

    - Jeśli to regresja: wyciągnij średnią z wartości $y$ tych $k$ sąsiadów.

    - Jeśli to klasyfikacja: przypisz $x_{nowy}$ do najczęściej występującej klasy wśród sąsiadów.

### Polynomial Features

To technika transformacji danych wejściowych, a nie samodzielny model predykcyjny. Często nazywana jest regresją wielomianową, gdy połączymy ją z regresją liniową. Algorytmy liniowe potrafią modelować tylko płaskie powierzchnie. Generując nowe cechy, które są kombinacjami (np. $x_1 \cdot x_2$) i potęgami (np. $x_1^2, x_2^3$) oryginalnych cech, sztucznie zwiększamy wymiarowość danych. W tej nowej, wielowymiarowej przestrzeni model liniowy "widzi" płaską płaszczyznę, ale po zmapowaniu z powrotem do oryginalnej przestrzeni 2D/3D, ta płaszczyzna staje się krzywą (np. parabolą).

Funkcje matematyczne:

- Funkcja mapująca $\phi(x)$ (dla 1 cechy i stopnia $d$):$$\phi(x) = \begin{bmatrix} 1 \\ x \\ x^2 \\ \vdots \\ x^d \end{bmatrix}$$

- Nowa funkcja predykcji (Wstawienie do regresji liniowej):$$h_\theta(x) = \theta_0 + \theta_1 x + \theta_2 x^2 + \dots + \theta_d x^d$$Matematycznie, traktujemy $x^2$ po prostu jako nową cechę $z_2$, a $x^d$ jako cechę $z_d$, co sprowadza problem do zwykłego równania: $h_\theta(z) = \theta^T z$.

Algorytm (Inżynieria Cech + Trenowanie):

- Określenie stopnia: Wybierz stopień wielomianu $d$ (uwaga: zbyt duże $d$ powoduje drastyczny overfitting i eksplozję liczby cech).

- Transformacja macierzy: Dla każdego wektora cech $x^{(i)}$ w zbiorze danych, wygeneruj nowy wektor zawierający wszystkie potęgi i interakcje między cechami aż do stopnia $d$.

- Podstawienie: Podmień oryginalny zbiór danych $X$ na nowy zbiór przetransformowany $\Phi(X)$.

- Trening liniowy: Przeprowadź standardowy algorytm regresji liniowej (np. Spadek Gradientu) na nowym zbiorze $\Phi(X)$, aby znaleźć optymalne wagi $\theta$.

### Metryki - MSE, RMSE, MAE i R2

1. MAE (Mean Absolute Error – Średni Błąd Bezwzględny)

Zacznijmy od najprostszej i najbardziej intuicyjnej metryki. MAE to po prostu średnia z różnic (w wartościach bezwzględnych) między tym, co model przewidział, a tym, co jest w rzeczywistości.

Wzór: $$MAE = \frac{1}{n} \sum_{i=1}^{n} |y_i - \hat{y}_i|$$ (Gdzie $y_i$ to wartość rzeczywista, $\hat{y}_i$ to predykcja, a $n$ to liczba próbek).

Jak to interpretować? Jeśli przewidujesz ceny mieszkań w tysiącach złotych, a Twój MAE wynosi 50, to znaczy, że Twój model myli się średnio o 50 tysięcy złotych na każdym mieszkaniu.

Cechy: Traktuje wszystkie błędy proporcjonalnie. Pomyłka o 10 jest dokładnie dwa razy gorsza niż pomyłka o 5. Jest bardzo odporna na wartości odstające (outliery).

2. MSE (Mean Squared Error – Błąd Średniokwadratowy)

Zamiast wyciągać wartość bezwzględną z błędu, MSE podnosi każdy błąd do kwadratu, a dopiero potem wyciąga średnią. To najpopularniejsza funkcja kosztu podczas trenowania modeli (np. regresji liniowej czy sieci neuronowych).

Wzór: $$MSE = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$

Jak to interpretować? Interpretacja bywa trudna, bo zmieniają się jednostki. Jeśli przewidujesz ceny w złotówkach, MSE będzie wyrażone w "złotówkach do kwadratu" ($PLN^2$), co dla człowieka nie ma większego sensu.

Cechy: Podnoszenie do kwadratu sprawia, że MSE brutalnie karze duże błędy. Jeśli model pomyli się o 2, kara wynosi 4. Jeśli pomyli się o 10, kara wynosi aż 100! Używamy MSE, gdy chcemy mieć pewność, że nasz model unika drastycznych pomyłek (nawet kosztem częstszych, ale malutkich błędów).

3. RMSE (Root Mean Squared Error – Pierwiastek Błędu Średniokwadratowego)

RMSE powstało po to, aby naprawić główny problem z MSE – dziwne jednostki. Robimy to w najprostszy możliwy sposób: wyciągamy pierwiastek z wyniku MSE.

Wzór: $$RMSE = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2}$$

Jak to interpretować? Wymiar wraca do normy. Wynik jest znowu w złotówkach, metrach czy kilogramach. Jest to wartość zazwyczaj nieco wyższa niż MAE.

Cechy: Podobnie jak MSE, RMSE jest wrażliwe na wartości odstające, ale dzięki sprowadzeniu do oryginalnej jednostki, pozwala biznesowi lub badaczom łatwiej ocenić, czy model ma sens w rzeczywistym zastosowaniu.

4. $R^2$ (R-Squared – Współczynnik Determinacji)

To zupełnie inna bestia. Poprzednie metryki mówiły, "jak duży jest błąd". $R^2$ mówi, "o ile nasz model jest lepszy od zgadywania samej średniej".

Wzór:$$R^2 = 1 - \frac{\text{Suma kwadratów błędów modelu (MSE)}}{\text{Suma kwadratów odchyleń od średniej (Wariancja)}}$$ $$R^2 = 1 - \frac{\sum (y_i - \hat{y}_i)^2}{\sum (y_i - \bar{y})^2}$$J

ak to interpretować? $R^2$ przyjmuje wartości zazwyczaj od 0 do 1 (może być ujemne, jeśli model jest tragiczny).

- $R^2 = 1$: Model idealnie przewiduje dane.

- $R^2 = 0$: Model działa dokładnie tak samo, jakbyśmy zawsze jako odpowiedź podawali po prostu średnią wartość ze zbioru treningowego (jest bezużyteczny).

- Wynik np. 0.85 oznacza, że "model tłumaczy 85% wariancji w danych".

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

---

## Z lab5

### Czym jest XAI

XAI, czyli Explainable Artificial Intelligence (Wytłumaczalna Sztuczna Inteligencja), to obecnie jeden z najważniejszych nurtów w świecie uczenia maszynowego. Jej głównym celem jest sprawienie, aby decyzje podejmowane przez algorytmy były zrozumiałe dla człowieka.

Co bardzo ciekawe – Twoje poprzednie pytania dotyczyły drzew decyzyjnych, a one są wręcz książkowym, idealnym przykładem algorytmów XAI!

Aby dobrze zrozumieć, czym jest XAI, musimy najpierw spojrzeć na problem, który ona rozwiązuje.

Problem "Czarnej Skrzynki" (Black Box)
Większość współczesnych, potężnych algorytmów AI (np. głębokie sieci neuronowe, Lasy Losowe) działa jak "czarna skrzynka".

Wrzucasz do nich dane (np. wyniki badań krwi pacjenta).

Wewnątrz dzieje się niesamowicie skomplikowana matematyka (miliony obliczeń, których człowiek nie jest w stanie prześledzić).

Model wypluwa wynik: "Rak złośliwy, prawdopodobieństwo 98%".

Problem polega na tym, że model nie mówi dlaczego. Lekarz nie wie, na jakiej podstawie podjęto decyzję, pacjent jest przerażony, a jeśli model się pomylił, nikt nie wie, gdzie leży błąd.

Jak XAI otwiera tę skrzynkę?
XAI to zbiór narzędzi i technik, które mają przekształcić AI z "magicznej wyroczni" w asystenta, który potrafi wytłumaczyć swój tok rozumowania. Dzieli się to zazwyczaj na dwa podejścia:

1. Modele z natury wytłumaczalne (White-box models)
To algorytmy, które same w sobie są tak proste i logiczne, że człowiek może je łatwo przeczytać.

Przykład: Wspomniane wcześniej Drzewa Decyzyjne (widzisz dokładnie, jaka ścieżka pytań doprowadziła do decyzji) lub Regresja Liniowa (widzisz, jak dużą wagę model przypisał do konkretnej cechy).

2. Metody post-hoc (Tłumaczenie czarnych skrzynek)
Jeśli musimy użyć skomplikowanej sieci neuronowej, bo daje lepsze wyniki, stosujemy do niej dodatkowe narzędzia (np. algorytmy o nazwie SHAP lub LIME). Pozwalają one "zapytać" model już po podjęciu decyzji, co było dla niego najważniejsze.

Przykład: Złożony model odrzuca Twój wniosek o kredyt. Algorytm XAI analizuje tę decyzję i generuje raport: "Wniosek odrzucony. Decydujące czynniki: (1) Zbyt krótka historia zatrudnienia obniżyła szansę o 40%, (2) Brak wkładu własnego obniżył szansę o 30%".

3. Techniki XAI

LIME (Local Interpretable Model-agnostic Explanations)
Nazwa w języku polskim oznacza: Lokalne, interpretowalne i niezależne od modelu wyjaśnienia. To brzmi mądrze, ale idea jest zaskakująco prosta.

Intuicja (Człowiek z opaską na oczach):
Wyobraź sobie, że stoisz na bardzo pofałdowanym terenie w górach (to Twoja skomplikowana czarna skrzynka), masz opaskę na oczach i chcesz wiedzieć, jak ukształtowany jest teren dokładnie tam, gdzie stoisz. Co robisz? Nie musisz badać całych gór! Po prostu robisz kilka małych kroków w lewo, w prawo, w przód i w tył. Jeśli idąc w lewo czujesz, że robisz krok pod górę, a w prawo w dół – już wiesz, że stoisz na zboczu opadającym w prawą stronę. Dokładnie tak działa LIME.

Jak to działa technicznie?

- Wybór pacjenta (Lokalność): Wybieramy jedną, konkretną decyzję do wyjaśnienia (np. wniosek kredytowy Pana Jana).

- Klonowanie i mutacja (Perturbacje): LIME bierze dane Pana Jana i tworzy wokół niego chmurę "sztucznych klonów", nieznacznie zmieniając ich dane (np. jednemu klonowi dodaje 200 zł do wypłaty, innemu odejmuje rok z wieku).

- Wysłanie na pożarcie czarnej skrzynce: LIME przepuszcza wszystkie te sztuczne punkty przez czarną skrzynkę i patrzy, jak zmieniły się werdykty.

- Zbudowanie prostego modelu: Na podstawie tylko tych lokalnych zmian, LIME buduje banalnie prosty model (np. regresję liniową), który uczy się naśladować czarną skrzynkę, ale tylko w najbliższym otoczeniu Pana Jana.

SHAP (SHapley Additive exPlanations)
SHAP to dzisiaj "złoty standard" branży XAI. Jest dużo bardziej rygorystyczny matematycznie niż LIME, a jego korzenie sięgają... Nagrody Nobla z dziedziny Teorii Gier (konkretnie Wartości Shapleya). Wykres kaskadowy (waterfall), który widziałeś w poprzedniej wiadomości, to typowy wynik działania SHAP.

Intuicja (Rozliczanie zespołu z premii):
Wyobraź sobie, że trzech graczy – Anna, Bartek i Celina – wzięło udział w turnieju e-sportowym i wspólnie wygrali 10 000 zł (nagroda to decyzja modelu, np. wysokie ryzyko kredytowe). Chcą podzielić nagrodę sprawiedliwie, adekwatnie do wkładu, ale w e-sporcie role się zazębiają.
Aby ustalić zasługi, musielibyśmy zagrać mecz bez Anny, potem bez Bartka, potem tylko Anny i Celiny itd. Sprawdzając wszystkie te kombinacje, moglibyśmy policzyć, o ile punktów średnio spadał wynik zespołu, gdy kogoś brakowało.

Jak to działa technicznie w AI?
Zamiast graczy, SHAP rozlicza cechy z Twojego zbioru danych (Wiek, Zarobki, Wzrost).

- Gra w chowanego: SHAP bierze model i zaczyna go "odpytywać", losowo ukrywając przed nim pewne cechy pacjenta (np. zmusza model do oceny ryzyka raka wiedząc tylko o wieku, ale nie o paleniu).

- Obliczanie udziałów: Sprawdza każdą możliwą kombinację ukrytych i widocznych cech.

- Przydział winy/zasługi: Oblicza matematycznie, o ile procent przeciętnie obecność np. "Palenia papierosów" podniosła ryzyko, niezależnie od tego, jakie inne cechy były obecne.

### Jak działa Random Forest

Intuicja: Konsylium Lekarskie a "Mądrość Tłumu"
Wyobraź sobie, że masz nietypowy ból brzucha:

Pojedyncze Drzewo Decyzyjne to pójście do jednego, bardzo skrupulatnego lekarza. Ten lekarz ma jednak tendencję do nadinterpretacji. Może spojrzeć na to, że masz pieprzyk na lewym ramieniu i zjadłeś wczoraj jabłko, po czym na tej podstawie zdiagnozować rzadką chorobę tropikalną (stworzył zbyt szczegółową regułę – przeuczył się).

Random Forest to zwołanie konsylium 100 różnych lekarzy. Każdy z nich ma trochę inne doświadczenie i dostaje nieco inną część Twoich wyników badań. Każdy z nich stawia własną diagnozę. Na koniec robisz głosowanie. Jeśli 80 lekarzy mówi "to tylko niestrawność", a 20 mówi "choroba tropikalna", ufasz większości.

Zjawisko to w uczeniu maszynowym nazywamy uczeniem zespołowym (Ensemble Learning). Zespół wielu przeciętnych, ale różnorodnych modeli prawie zawsze pokona jednego "genialnego", ale niestabilnego eksperta.

Jak dokładnie działa Random Forest? (Dwa filary losowości)
Aby Las Losowy działał, jego drzewa muszą się od siebie różnić. Gdybyśmy wytrenowali 100 drzew na tych samych danych, dostalibyśmy 100 identycznych klonów i głosowanie nie miałoby sensu. Las wprowadza losowość na dwa sposoby:

1. Bagging (Bootstrap Aggregating) – Losowanie pacjentów
Algorytm nie daje każdemu drzewu całego zbioru danych. Zamiast tego, dla każdego drzewa losuje podzbiór danych ze zwracaniem.
Oznacza to, że jeśli mamy 1000 pacjentów, pierwsze drzewo dostanie pacjenta nr 1, 5, 5 (wylosowanego dwa razy), 8 itd., a drugie drzewo pacjenta 2, 3, 9... Dzięki temu każde drzewo ma nieco inne "doświadczenie życiowe".

2. Losowanie cech (Feature Randomness) – Zasłanianie wyników badań
Kiedy pojedyncze drzewo buduje węzeł (szuka np. najwyższego spadku Gini), patrzy na wszystkie dostępne cechy (Wiek, Waga, Ciśnienie, Cukier).
W Lesie Losowym jest inaczej! W każdym nowym węźle algorytm losuje np. tylko 3 cechy i pozwala drzewu wybrać najlepszą regułę tylko z tej trójki.
Dlaczego? Załóżmy, że cecha "Wiek" jest bardzo silna. Gdybyśmy jej nie blokowali, każde ze 100 drzew zaczęłoby swój korzeń od pytania o Wiek i wszystkie byłyby do siebie zbyt podobne. Ograniczanie cech zmusza drzewa do odkrywania innych, ukrytych zależności.

Co się dzieje na końcu? (Agregacja)
Gdy przepuścisz nowego pacjenta przez Las Losowy, każde drzewo w lesie wydaje werdykt.

W klasyfikacji (Classification): Odbywa się głosowanie większościowe (tzw. twarde głosowanie) lub wyciągana jest średnia z prawdopodobieństw (miękkie głosowanie). Wygrywa klasa z największym poparciem.

W regresji (Regression): Model po prostu sumuje wyniki wszystkich drzew i dzieli przez ich liczbę, podając Ci średnią arytmetyczną (np. średnią przewidywaną cenę mieszkania).

Podsumowanie (Plusy i Minusy Random Forest)

Zalety:

- Odporność na overfitting: Dzięki głosowaniu i losowości, bardzo trudno jest przeuczyć Random Forest.

- Moc wyjęta z pudełka: Często działa rewelacyjnie na domyślnych hiperparametrach, bez żmudnego strojenia.

- Wbudowana ważność cech (Feature Importance): Las potrafi sam z siebie ocenić, które cechy były używane najczęściej i dawały największy zysk z podziału, zwracając Ci ładny ranking najważniejszych zmiennych.

Wady:

- Utrata interpretowalności (Czarna Skrzynka): O ile pojedyncze drzewo możesz narysować i przeczytać, o tyle odczytanie 500 drzew naraz jest dla człowieka niemożliwe. Właśnie tutaj, aby zrozumieć decyzję Lasu Losowego, musimy użyć technik XAI (takich jak SHAP), o których rozmawialiśmy wcześniej!

- Rozmiar i czas: Trenowanie 500 drzew i przewidywanie za ich pomocą zajmuje więcej pamięci operacyjnej i czasu niż korzystanie z jednego prostego drzewa.

### Czym modele drzwiaste typu Random Forest są podatne na overfitting

1. Kiedy Las Losowy może się przeuczyć?
Pamiętasz analogię z konsylium lekarskim? Nawet 100 świetnych lekarzy postawi złą diagnozę, jeśli wszyscy pozwolą sobie na zbyt głęboką, nielogiczną nadinterpretację wyników (brak limitów) na bardzo zaszumionych, wadliwych danych.

Główne przyczyny overfittingu w Lasach Losowych to:

- Brak ograniczeń dla pojedynczych drzew: Domyślnie biblioteki takie jak scikit-learn pozwalają drzewom w lesie rosnąć bez żadnych limitów (max_depth=None, min_samples_leaf=1). Oznacza to, że każde ze 100 drzew uczy się danych treningowych na pamięć (w 100%). Mimo że proces uśredniania (Bagging) łagodzi ten efekt, to przy bardzo specyficznych, zaszumionych danych, średnia z 100 potężnie przeuczonych drzew wciąż daje lekko przeuczony las.

- Bardzo głośny "szum" w danych: Jeśli Twoje dane zawierają dużo błędów, wartości skrajnych (outlierów) lub losowych anomalii, a Ty nie ograniczysz głębokości drzew, model nauczy się tych anomalii i uzna je za ważne reguły.

- Zbyt duża liczba cech używanych przy podziale: Jeśli hiperparametr max_features jest ustawiony zbyt wysoko (np. algorytm widzi prawie wszystkie cechy przy każdym podziale), drzewa w lesie stają się do siebie zbyt podobne. Tracimy wtedy magię "różnorodności tłumu" i wracamy do problemu pojedynczego drzewa.

2. Wielki Mit: "Więcej drzew = większe przeuczenie"
Wielu początkujących analityków myśli, że jeśli ustawią parametr n_estimators (liczba drzew w lesie) na ogromną wartość, np. 5000, to model się przeuczy. To matematycznie niemożliwe w Lesie Losowym.

Zwiększanie liczby drzew w algorytmie Baggingu (jakim jest Random Forest) nigdy nie zwiększa overfittingu. Posiadanie 5000 drzew zamiast 500 sprawi jedynie, że granica decyzyjna będzie gładsza, a model bardziej stabilny. W pewnym momencie (np. przy 300 drzewach) dokładność przestanie rosnąć i po prostu "osiądzie" na stałym poziomie. Jedynym minusem dodania 5000 drzew jest to, że Twój komputer będzie to liczył godzinami.

3. Jak chronić Las Losowy przed przeuczeniem?
Ponieważ Las składa się z drzew, używamy dokładnie tych samych "hamulców" (hiperparametrów), o których pisaliśmy wcześniej, aby utemperować zapędy algorytmu:

- max_depth (np. 10-15): Nie pozwól drzewom rosnąć w nieskończoność. Niech będą to drzewa "płytkie", które widzą szerszy obraz.

- min_samples_leaf (np. 5-10): Wymuś, aby każdy liść w każdym drzewie opierał swoją decyzję na co najmniej kilku obserwacjach, a nie na pojedynczych przypadkach.

- max_features (np. 'sqrt' lub 'log2'): Zmuszaj drzewa do poszukiwania kreatywnych rozwiązań, dając im przy każdym podziale dostęp tylko do ułamka (np. pierwiastka kwadratowego) wszystkich dostępnych cech. To zwiększa różnorodność lasu.

Podsumowując: Las Losowy to niesamowicie bezpieczny algorytm "wyjęty prosto z pudełka". Wybacza bardzo dużo błędów, na które pojedyncze drzewo byłoby niezwykle wrażliwe. Zawsze warto jednak monitorować wyniki (porównywać F1-score ze zbioru treningowego i testowego), aby sprawdzić, czy model nie próbuje nauczyć się danych na pamięć.

### Parametry i hiperparametry Random Forest

1. Parametry (To, czego model uczy się sam)Parametry to wewnętrzne wartości, które algorytm sam wylicza na podstawie danych treningowych. Nie ustawiasz ich ręcznie. W przypadku Lasu Losowego parametrami są:

- Zestaw wygenerowanych reguł podziału: Konkretne pytania w każdym węźle każdego drzewa (np. "Czy Wiek $\le$ 45.5?", "Czy Zarobki > 5200?").

- Struktura drzew: To, jak drzewa ostatecznie wyglądają (ile mają liści, jak głęboko wyrosły – w ramach narzuconych limitów).

- Wartości w liściach: Ostateczne prognozy przypisane do każdego liścia (np. "Prawdopodobieństwo klasy A wynosi 85%").

- Feature Importances (Ważność cech): Wynikowy ranking, który model tworzy po zakończeniu treningu, określający, które cechy najmocniej przyczyniły się do redukcji błędu.

2. Hiperparametry (Twoje "pokrętła kontrolne")Hiperparametry to ustawienia, które Ty (Data Scientist) konfigurujesz przed uruchomieniem treningu. W Lasach Losowych dzielimy je na dwie grupy: te sterujące rozmiarem całego lasu oraz te sterujące kształtem pojedynczych drzew.

A. Hiperparametry całego Lasu (Zespołowe)

- n_estimators (Liczba drzew):

    - Co robi: Określa, z ilu pojedynczych drzew będzie składał się Twój las.

    - Wpływ: Im więcej, tym lepiej. Większa liczba drzew wygładza granice decyzyjne i stabilizuje wynik. W przeciwieństwie do innych algorytmów, zwiększanie tej liczby nie powoduje przeuczania (overfittingu). Jedynym minusem jest dłuższy czas treningu i większe zużycie pamięci.

    - Praktyka: Zazwyczaj ustawia się wartości od 100 do 500.

- max_features (Maksymalna liczba cech przy podziale):

- Co robi: To najważniejszy hiperparametr Lasu Losowego. Określa, ile losowych cech algorytm może wziąć pod uwagę, gdy szuka najlepszego podziału w pojedynczym węźle.

- Wpływ: Jeśli ustawisz za wysoką wartość (np. wszystkie cechy), drzewa będą do siebie zbyt podobne (najsilniejsza cecha zdominuje wszystkie korzenie). Ograniczenie tej liczby wymusza na drzewach różnorodność i kreatywność.

- Praktyka: W klasyfikacji standardem jest pierwiastek kwadratowy z liczby wszystkich cech (np. mając 100 kolumn danych, każde drzewo widzi tylko 10 losowych przy danym podziale). W regresji często bierze się 1/3 cech.

- bootstrap (Losowanie próbek ze zwracaniem):

    - Co robi: Włącza lub wyłącza technikę "Baggingu" (o której pisaliśmy wcześniej). Jeśli True, każde drzewo dostaje nieco inny zbiór pacjentów/danych (niektórzy się powtarzają, innych brakuje).

    - Praktyka: Prawie zawsze zostawia się True. Ustawienie na False sprawi, że każde drzewo zbuduje się na dokładnie tym samym zbiorze danych, co niszczy sens Lasu Losowego.

- max_samples (Rozmiar próbki Bootstrap):

    - Co robi: Jeśli włączyłeś bootstrap, ten parametr pozwala określić, jak duży podzbiór danych wylosować dla każdego drzewa. Jeśli masz zbiór 100 000 wierszy, możesz nakazać każdemu drzewu uczyć się tylko na 10 000 losowych wierszach.

    - Praktyka: Świetne narzędzie do przyspieszania treningu na gigantycznych zbiorach danych.

B. Hiperparametry pojedynczych drzew (Zabezpieczenia)

To są dokładnie te same parametry, co w zwykłym Drzewie Decyzyjnym. W Lesie Losowym możesz pozwolić im na trochę więcej swobody, ale wciąż warto je kontrolować.

- max_depth (Maksymalna głębokość): Hamulec bezpieczeństwa. Ogranicza, jak bardzo skomplikowane pytania mogą zadawać drzewa.

- min_samples_split: Ile minimalnie obserwacji musi być w węźle, by można było zadać kolejne pytanie (domyślnie 2).

- min_samples_leaf: Ile minimalnie obserwacji musi znaleźć się na samym końcu gałęzi (w liściu), by podział był ważny. Świetne do ignorowania szumu.

### Czym są i jak działają drzewa decyzyjne

Drzewa decyzyjne (ang. Decision Trees) to jeden z najbardziej intuicyjnych i popularnych algorytmów uczenia maszynowego. Możesz o nich myśleć jak o zaawansowanej grze w "Zgadnij kto to?" lub logicznym schemacie blokowym (flowcharcie), który komputer buduje samodzielnie na podstawie danych.

Można je stosować zarówno do klasyfikacji (np. przewidywanie: czy e-mail to spam?), jak i do regresji (np. przewidywanie ceny mieszkania).

Czym są i z czego się składają?
Drzewo decyzyjne składa się z trzech głównych elementów konstrukcyjnych (wizualnie zazwyczaj rysuje się je "do góry nogami" – korzeń jest na samej górze):

- Korzeń (Root Node): Pierwszy, najważniejszy punkt decyzyjny. Obejmuje cały zestaw danych.

- Węzły wewnętrzne (Internal Nodes): Miejsca, w których dane są dzielone na podstawie określonej reguły (np. pytania w stylu "Czy wiek > 30?"). Każdy węzeł wewnętrzny ma co najmniej dwie gałęzie wychodzące.

- Liście (Leaf Nodes): Końcowe punkty drzewa. Nie ma tu już żadnych pytań. Liść zawiera ostateczną decyzję (np. "Klasa: Zdrowy" lub "Przewidywana cena: 450 000 zł").

Jak dokładnie działają?
Algorytm buduje drzewo w sposób "z góry na dół" (top-down), stosując podejście zwane algorytmem zachłannym (greedy algorithm). Działa to krok po kroku:

- Szukanie najlepszego podziału: Algorytm analizuje wszystkie dostępne cechy (np. wiek, zarobki, wzrost) i testuje różne punkty podziału.

- Ocena podziału: Dla każdego możliwego podziału oblicza matematyczną "jakość".

W klasyfikacji używa miar takich jak Wskaźnik Giniego (Gini Impurity) lub Entropia, które mierzą, jak "czyste" są dane po podziale (idealnie chcemy, aby w jednej grupie znaleźli się sami przedstawiciele Klasy A, a w drugiej sami Klasy B).

W regresji używa miar takich jak Błąd Średniokwadratowy (MSE) – szuka podziału, po którym wartości w nowo utworzonych grupach będą do siebie jak najbardziej zbliżone.

- Wybór: Wybiera tę cechę i ten próg, który daje największy zysk informacyjny (najlepiej separuje dane).

- Powtarzanie (Rekurencja): Proces ten jest powtarzany dla każdej nowo utworzonej gałęzi, aż do spełnienia kryterium zatrzymania (np. osiągnięto maksymalną głębokość drzewa lub w węźle zostały próbki tylko jednej klasy).

Najważniejsze cechy drzew decyzyjnych (Zalety i Wady)

Zalety:

- Łatwość interpretacji (Biała Skrzynka): To największa zaleta. Możesz narysować drzewo i dokładnie prześledzić, dlaczego model podjął taką, a nie inną decyzję.

- Brak konieczności standaryzacji: Drzewa nie dbają o skalę danych. Cecha wyrażona w milionach (np. cena) działa tak samo dobrze obok cechy wyrażonej w ułamkach (np. procenty).

- Odporność na wartości odstające (outliery): Ponieważ podziały opierają się na progach (np. "większe niż"), pojedyncza ekstremalnie wysoka wartość nie psuje całego modelu.

- Nieliniowość: Świetnie radzą sobie z danymi, których nie da się oddzielić prostą linią.

Wady: 

- Skłonność do przeuczania (Overfitting): Drzewa mają tendencję do rozrastania się w nieskończoność. Jeśli im na to pozwolić, wyrosną tak głęboko, że stworzą osobną regułę dla każdej pojedynczej próbki w zbiorze treningowym. Przestaną wtedy uogólniać wiedzę. Ratunkiem jest ograniczanie ich głębokości (tzw. przycinanie - pruning).

- Niestabilność: Drzewa są bardzo czułe na dane treningowe. Bardzo mała zmiana w danych (np. usunięcie kilku punktów) może spowodować, że model wybierze zupełnie inną cechę na samym początku (w korzeniu), co zaowocuje zbudowaniem zupełnie innego drzewa.

Dlatego w dzisiejszych czasach rzadko używa się pojedynczych drzew decyzyjnych do zaawansowanych problemów. Zamiast tego łączy się setki drzew w potężne algorytmy takie jak Lasy Losowe (Random Forests) lub XGBoost, które eliminują wadę niestabilności i przeuczania.

### DecisionTreeClassifier i DecisionTreeRegressor - jak działają

1. DecisionTreeClassifier (Klasyfikator)

Cel: Podzielenie danych tak, aby na samym końcu (w liściach) znalazły się próbki należące w 100% do jednej klasy (np. sami zdrowi lub sami chorzy). Szukamy maksymalnej czystości.

Jak model ocenia podział? (Matematyka)
Klasyfikator najczęściej używa miary zwanej Wskaźnikiem zanieczyszczenia Giniego (Gini Impurity) lub Entropii. Domyślnie w scikit-learn jest to Gini.

Wzór na wskaźnik Giniego dla pojedynczego węzła to:

$$Gini = 1 - \sum_{i=1}^{C} p_i^2$$

Gdzie $C$ to liczba klas (np. 2: chory/zdrowy), a $p_i$ to ułamek próbek danej klasy w tym węźle.

- Przykład idealny (czysty): W węźle jest 10 osób chorych i 0 zdrowych. $p_1 = 1$, $p_2 = 0$.$Gini = 1 - (1^2 + 0^2) = 0$. Wartość 0 oznacza idealną czystość – model jest w 100% pewny.

- Przykład najgorszy (całkowity chaos): W węźle jest 5 chorych i 5 zdrowych. $p_1 = 0.5$, $p_2 = 0.5$.$Gini = 1 - (0.5^2 + 0.5^2) = 1 - 0.5 = 0.5$. Model rzuca monetą.

Jak działa podejmowanie decyzji o podziale?
Algorytm bierze po kolei każdą cechę (np. rozmiar guza) i każdy możliwy próg podziału (np. rozmiar $\le$ 2.5). Następnie oblicza ważoną średnią wskaźnika Giniego dla lewej i prawej gałęzi, która by z tego podziału powstała. Algorytm wybiera ten próg, który daje najmniejszą wartość Gini po podziale.

Co przewiduje liść?
Kiedy drzewo przestaje rosnąć, a nowe dane wpadną do konkretnego liścia, model patrzy, jaka klasa ma tam większość. Jeśli w liściu zostało 8 chorych i 2 zdrowych, model krzyknie: "Chory!". Prawdopodobieństwo tej decyzji wynosi 80%.

2. DecisionTreeRegressor (Regresor)

Cel: Podzielenie danych tak, aby na końcu znalazły się próbki o bardzo zbliżonych do siebie wartościach liczbowych (np. domy o podobnej cenie). Szukamy minimalnego błędu.

Jak model ocenia podział? (Matematyka)
Regresor nie może liczyć klas. Zamiast tego domyślnie używa Błędu Średniokwadratowego (MSE – Mean Squared Error).

Wzór na MSE w węźle:

$$MSE = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y})^2$$

Gdzie $n$ to liczba próbek w węźle, $y_i$ to rzeczywista wartość próbki, a $\hat{y}$ to średnia wartość wszystkich próbek w tym węźle.

- Jak to rozumieć? Algorytm sprawdza, jak bardzo liczby w danym węźle różnią się od swojej średniej. Jeśli w węźle mamy domy warte [100k, 105k, 95k], średnia to 100k, a wartości są bardzo blisko niej – błąd MSE jest mały (węzeł jest "dobry"). Jeśli mamy [100k, 500k, 10k], średnia mało nam mówi, a błąd MSE wybije w kosmos.

Jak działa podejmowanie decyzji o podziale?
Podobnie jak w klasyfikacji, algorytm testuje różne cechy i punkty podziału (np. liczba pokoi $\le$ 3). Oblicza średnią z lewej grupy, średnią z prawej grupy i sprawdza, który podział sprawi, że suma błędów MSE z obu stron będzie najmniejsza.

Co przewiduje liść?
Dla nowych danych, które wpadną do danego liścia, drzewo regresyjne po prostu zwraca średnią arytmetyczną ($\hat{y}$) ze wszystkich próbek treningowych, które wcześniej do tego liścia trafiły.

Wspólny mianownik: Zachłanność (Greedy Approach)
Zarówno regresor, jak i klasyfikator to algorytmy "zachłanne". Oznacza to, że szukając najlepszego podziału w danym kroku, wybierają ten, który daje najlepszy rezultat tu i teraz. Model nie planuje 5 kroków w przód (bo wymagałoby to gigantycznej mocy obliczeniowej). Konsekwencją tego jest to, że drzewa rosną tak długo, aż każdy liść będzie idealnie czysty lub błąd zerowy.

Zrozumienie tych matematycznych fundamentów bardzo ułatwia dobieranie odpowiednich parametrów. Czy chciałbyś, abym wyjaśnił, dlaczego drzewa mają przez to ogromną tendencję do "przeuczania się" (overfittingu) i jak temu zapobiegać w praktyce?

### Czym jest Gini Impurity

Wskaźnik zanieczyszczenia Giniego to najważniejsza miara matematyczna używana przez drzewa decyzyjne do oceny podziałów w zadaniach klasyfikacji.

Mówiąc najprościej: Gini mierzy, jak bardzo "wymieszane" są dane w jednym miejscu (węźle).

Intuicja (Worek z kulkami)

Wyobraź sobie, że masz przed sobą worek z piłeczkami w dwóch kolorach: czerwonym (Klasa A) i niebieskim (Klasa B). Jeśli włożysz rękę do worka i wylosujesz jedną piłeczkę, a następnie spróbujesz zgadnąć jej kolor na podstawie tego, jakich piłeczek jest najwięcej w worku, jak często się pomylisz? Właśnie to prawdopodobieństwo błędu mierzy Gini.

- Idealna czystość ($Gini = 0$): W worku są tylko czerwone piłki. Nie ma żadnego zanieczyszczenia innymi kolorami. Model jest w 100% pewien decyzji.

- Całkowity chaos ($Gini = 0.5$): W worku jest dokładnie połowa czerwonych i połowa niebieskich piłek. Zanieczyszczenie jest maksymalne. Dla modelu to jak rzut monetą – nie wie, jaką decyzję podjąć.

(Uwaga: Wartość maksymalna wynosi 0.5 tylko dla dwóch klas. Przy większej liczbie klas ten próg rośnie, ale zawsze dąży do zera, gdy węzeł staje się czystszy).

Jak to policzyć? (Matematyka)

Wzór na Gini Impurity jest bardzo prosty:

$$Gini = 1 - \sum (p_i)^2$$

Gdzie $p_i$ to ułamek (prawdopodobieństwo) występowania konkretnej klasy w danym zbiorze.

Przykład obliczeń:

Mamy węzeł, w którym znalazło się 10 pacjentów: 8 chorych i 2 zdrowych.

- Ułamek chorych ($p_1$) = 8/10 = 0.8

- Ułamek zdrowych ($p_2$) = 2/10 = 0.2

- Podnosimy ułamki do kwadratu: $0.8^2 = 0.64$, oraz $0.2^2 = 0.04$

- Odejmujemy ich sumę od jedynki: $1 - (0.64 + 0.04) = 1 - 0.68 = \mathbf{0.32}$

Wynik $0.32$ mówi nam, że węzeł jest już w miarę czysty, ale wciąż jest miejsce na poprawę (kolejny podział drzewa).

Dlaczego to jest ważne dla algorytmu?

Gdy drzewo decyzyjne szuka reguły (np. "Czy pacjent ma gorączkę?"), oblicza Gini przed podziałem i po podziale. Jeśli nowy podział znacznie zmniejsza ogólne zanieczyszczenie (zbliża je do zera w nowo powstałych gałęziach), drzewo uznaje to za świetny krok i wybiera tę regułę. 

### Jakie są hiperparametry drzew decyzyjnych

Hiperparametry to "pokrętła kontrolne" modelu, które ustawiasz zanim model zacznie się uczyć (w przeciwieństwie do zwykłych parametrów, których model uczy się sam z danych, takich jak konkretne progi podziału np. wiek > 30).

W przypadku drzew decyzyjnych sterowanie hiperparametrami służy głównie jednemu celowi: powstrzymaniu drzewa przed przeuczeniem (overfittingiem). Jak ustaliliśmy wcześniej, puszczone samopas drzewo będzie rosło w nieskończoność, tworząc osobną regułę dla każdego najdrobniejszego szumu w danych.

Oto najważniejsze hiperparametry drzew decyzyjnych (używam nazw z biblioteki scikit-learn, z której korzystałeś w swoim kodzie):

1. max_depth (Maksymalna głębokość)
To najważniejszy i najczęściej używany hamulec. Określa, jak wiele poziomów pytań "Tak/Nie" drzewo może zadać.

- Mała wartość (np. 2-3): Drzewo zada tylko kilka pytań. Model będzie bardzo prosty, ale może nie wyłapać skomplikowanych zależności (ryzyko underfittingu - niedouczenia).

- Duża wartość (np. 20) lub None: Drzewo rośnie, aż liście będą w 100% czyste. Prawie na pewno skończy się to potężnym przeuczeniem (overfitting).

2. min_samples_split (Minimalna liczba próbek do podziału)
Określa, ile co najmniej obserwacji musi znajdować się w węźle, aby algorytm w ogóle spróbował go podzielić.

- Domyślnie wynosi 2. Oznacza to, że nawet jeśli w węźle zostały tylko 2 próbki z różnych klas, algorytm spróbuje zadać kolejne pytanie, by je rozdzielić.

- Zwiększenie tej wartości (np. do 10 lub 20) zmusza drzewo do ignorowania mikroskopijnych, nieistotnych różnic. Zatrzymuje tworzenie bardzo specyficznych reguł.

3. min_samples_leaf (Minimalna liczba próbek w liściu)
Podobne do powyższego, ale działa na samym końcu. Reguła ta mówi: "Możesz wykonać podział tylko wtedy, jeśli w obu nowych gałęziach (liściach) znajdzie się co najmniej X próbek".

- Domyślnie wynosi 1. To pozwala na tworzenie liści z jednym, pojedynczym pacjentem/domem, co jest definicją przeuczenia.

- Ustawienie np. na 5 sprawia, że model "uogólnia" – ostateczna decyzja musi być oparta na doświadczeniu z co najmniej 5 podobnych przypadków, a nie na jednym wyjątkowym.

4. max_features (Maksymalna liczba cech)
Drzewo w każdym węźle sprawdza wszystkie dostępne cechy, szukając najlepszego podziału. Ten parametr pozwala wymusić, aby w danym węźle algorytm sprawdził tylko losową pulę cech (np. tylko 3 losowe z 10 dostępnych).

Po co? Wprowadza to element losowości, co pomaga zapobiegać przeuczeniu i przyspiesza obliczenia. Jest to fundament działania potężniejszego algorytmu – Lasów Losowych (Random Forest).

5. criterion (Kryterium podziału)
Zmienia matematyczny "silnik" oceniający jakość podziału.

- W klasyfikacji: Zazwyczaj do wyboru jest gini (Wskaźnik Giniego - domyślny, często szybszy) lub entropy (Entropia z teorii informacji). W praktyce dają bardzo zbliżone wyniki.

- W regresji: Wybór to najczęściej squared_error (Błąd średniokwadratowy - MSE) lub absolute_error (Średni błąd bezwzględny - MAE). MSE mocniej karze duże odchylenia (outliery).

### Czy drzewa są modelami parametrycznymi

Krótka i treściwa odpowiedź brzmi: Nie, drzewa decyzyjne są modelami nieparametrycznymi.

W uczeniu maszynowym rozróżnienie na modele parametryczne i nieparametryczne nie dotyczy tego, czy model "ma jakieś parametry" (bo prawie każdy ma), ale tego, jak ich liczba i struktura zmieniają się wraz z danymi.

Oto dlaczego drzewa zaliczamy do grupy modeli nieparametrycznych:

1. Brak założeń dotyczących rozkładu danych
Modele parametryczne (np. regresja liniowa czy logistyczna) z góry zakładają postać funkcji, którą próbują dopasować do danych (np. że zależność jest liniowa). Muszą "zmieścić" dane w konkretny, sztywny wzór.

Drzewa decyzyjne nie robią żadnych założeń co do kształtu relacji między cechami a celem. Nie zakładają, że dane mają rozkład normalny czy że granica decyzyjna jest prostą linią.

2. Złożoność zależna od wielkości zbioru danych
W modelach parametrycznych liczba parametrów (wag) jest stała i znana przed rozpoczęciem treningu (np. w regresji liniowej masz dokładnie tyle wag, ile jest cech + wyraz wolny).

W drzewach decyzyjnych:

- Struktura modelu (liczba węzłów, głębokość, podziały) powstaje dopiero w trakcie treningu.

- Im więcej masz danych i im bardziej są one złożone, tym "większe" i bardziej skomplikowane może stać się drzewo (jeśli go nie ograniczysz). Liczba "parametrów" (splitów) rośnie więc wraz z danymi.

3. Elastyczność
Modele nieparametryczne są zazwyczaj znacznie bardziej elastyczne. Drzewa potrafią idealnie dopasować się do danych treningowych (nawet za bardzo, co prowadzi do overfittingu), ponieważ mogą dzielić przestrzeń cech na coraz mniejsze "pudełka", aż uchwycą każdą zależność.

### Czym jest F1 score

F1 Score to jedna z najważniejszych metryk oceny modeli klasyfikacji w uczeniu maszynowym. Jest to średnia harmoniczna precyzji (Precision) oraz czułości (Recall).

Stosuje się ją głównie wtedy, gdy zależy nam na znalezieniu złotego środka między tymi dwiema wartościami, szczególnie w sytuacjach, gdy mamy do czynienia z niezbalansowanymi zbiorami danych (np. wykrywanie rzadkich chorób lub oszustw bankowych).

1. Składniki F1 Score

Aby zrozumieć F1 Score, musisz najpierw znać jego dwa filary:

- Precyzja (Precision): Odpowiada na pytanie: „Z wszystkich przypadków, które model oznaczył jako pozytywne, ile faktycznie nimi było?”. Skupia się na unikaniu błędów typu False Positive (fałszywych alarmów).Czułość 

- (Recall / Sensitivity): Odpowiada na pytanie: „Z wszystkich faktycznie pozytywnych przypadków, ile model zdołał poprawnie wykryć?”. Skupia się na unikaniu błędów typu False Negative (przeoczeń).

2. Wzór matematycznyF1 Score obliczamy za pomocą średniej harmonicznej:$$F1 = 2 \cdot \frac{\text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}$$

Dlaczego średnia harmoniczna, a nie zwykła (arytmetyczna)?

Średnia harmoniczna bardzo "karze" niskie wartości. Jeśli jedna z metryk (np. Precision) wynosi 0, F1 Score również wyniesie 0, nawet jeśli Recall wynosi 1. To sprawia, że F1 jest bardzo bezpiecznym wskaźnikiem – wysoki wynik F1 oznacza, że model radzi sobie dobrze na obu polach.

3. Kiedy używać F1 Score zamiast Accuracy (Dokładności)?

Wyobraź sobie test na rzadką chorobę, która występuje u 1 na 1000 osób.Jeśli model zawsze mówi "Jesteś zdrowy", jego Accuracy wyniesie 99,9%. Brzmi świetnie, ale model jest bezużyteczny, bo nie wykrył żadnego chorego.

W takim przypadku jego Recall wyniesie 0, a co za tym idzie – F1 Score również wyniesie 0. F1 Score natychmiast obnaży fakt, że model kompletnie zawodzi w wykrywaniu klasy pozytywnej.

### Zalety i wady drzew decyzyjnych

1. Zalety drzew decyzyjnych

- Wysoka interpretowalność: Model można przedstawić graficznie w formie schematu blokowego. Nawet osoba bez wiedzy technicznej zrozumie, dlaczego model podjął taką, a nie inną decyzję.

- Minimalne przygotowanie danych: * Nie wymagają skalowania ani normalizacji cech (w przeciwieństwie do SVM czy KNN).

- Radzą sobie z brakującymi wartościami (zależy od implementacji).

- Obsługują zarówno dane liczbowe, jak i kategoryczne.

- Naturalna selekcja cech: Podczas budowy drzewa, algorytm sam wybiera najbardziej istotne zmienne, umieszczając je na górze (w korzeniu i pierwszych węzłach).

- Modelowanie nieliniowości: Drzewa świetnie radzą sobie z wykrywaniem skomplikowanych, nieliniowych zależności między cechami bez konieczności ręcznej transformacji danych.

- Odporność na wartości odstające (outliers): Ponieważ drzewa dzielą dane na przedziały, pojedynczy rekord o ekstremalnej wartości ma zazwyczaj ograniczony wpływ na strukturę całego modelu.

2. Wady drzew decyzyjnych

- Tendencja do przeuczenia (Overfitting): To największa bolączka. Bez nałożenia ograniczeń (np. maksymalnej głębokości), drzewo będzie rosło tak długo, aż idealnie zapamięta zbiór treningowy, tracąc zdolność do generalizacji na nowych danych.

- Niestabilność (Wysoka wariancja): Mała zmiana w danych treningowych może skutkować budową zupełnie innego drzewa. Jeden nowy rekord może zmienić główny podział w korzeniu, co zburzy całą dalszą strukturę.

- Problemy z diagonalnymi zależnościami: Drzewa dzielą przestrzeń zawsze prostopadle do osi (poziomo lub pionowo). Jeśli zależność w danych jest ukośna, drzewo musi stworzyć mnóstwo małych "schodków", by ją odwzorować, co jest mało efektywne.

- Brak płynności (w regresji): Drzewa regresyjne nie przewidują wartości w sposób ciągły, lecz "skokowy" (średnia z liścia). Nie potrafią też przewidywać wartości spoza zakresu widzianego w treningu (brak ekstrapolacji).

- Dominacja cech o wielu poziomach: Algorytmy drzewiaste mają tendencję do preferowania cech kategorycznych, które mają bardzo dużo unikalnych wartości (np. ID użytkownika), co może prowadzić do błędnych wniosków.

### Jaki wpływ ma głębokość na drzewa

1. Płytkie drzewo (Mała głębokość)
Gdy drzewo ma tylko 1-3 poziomy głębokości, jest bardzo proste. Takie drzewo nazywamy czasem „pniakiem decyzyjnym” (decision stump), jeśli ma tylko jeden poziom.

Zaleta: Bardzo małe ryzyko przeuczenia (Low Variance). Model jest stabilny i łatwy do zrozumienia.

Wada: Zbyt duże uproszczenie (High Bias). Model może nie zauważyć ważnych zależności w danych, co prowadzi do underfittingu (niedouczenia).

Przykład: Jeśli drzewo o głębokości 1 ma przewidzieć cenę domu tylko na podstawie metrażu, zignoruje lokalizację, rok budowy i standard, dając bardzo niedokładny wynik.

2. Głębokie drzewo (Duża głębokość)
Głębokie drzewo tworzy bardzo specyficzne reguły dla małych grup danych.

Zaleta: Bardzo wysoka precyzja na zbiorze treningowym (Low Bias). Model potrafi wychwycić nawet najbardziej subtelne i nieliniowe korelacje.

Wada: Ekstremalnie wysokie ryzyko overfittingu (przeuczenia / High Variance). Drzewo zaczyna "uczyć się na pamięć" szumu i błędów w danych treningowych zamiast ogólnych trendów.

Wynik: Model ma 100% skuteczności na danych, które już widział, ale kompletnie gubi się na danych testowych.

3. Kluczowy balans: Bias-Variance Tradeoff
Wpływ głębokości najlepiej obrazuje poniższa tabela, pokazująca, jak zmieniają się błędy w zależności od tego, jak głęboko pozwolimy drzewu "rosnąć":

![alt text](image-6.png)

Jak kontrolować głębokość?

W praktyce rzadko zostawia się drzewom "wolną rękę". Stosuje się techniki takie jak:

- Early Stopping: Ustalenie sztywnego max_depth.

- Pruning (Przycinanie): Pozwolenie drzewu urosnąć, a następnie usuwanie gałęzi, które wnoszą bardzo mało do ogólnej trafności modelu.

- Min Samples Split: Określenie, że w węźle musi być co najmniej $N$ obserwacji, aby można było go dalej dzielić.

Złota zasada: Zawsze monitoruj błąd na zbiorze walidacyjnym. Jeśli błąd na treningu spada, a na walidacji zaczyna rosnąć – to znak, że Twoje drzewo jest już za głębokie.

### Różnica między F1 scora a Accuracy

Różnica między Accuracy (Dokładnością) a F1 Score to klasyczny problem "liczb, które kłamią". Choć obie metryki mierzą skuteczność modelu, robią to z zupełnie innej perspektywy.

W skrócie: Accuracy mówi Ci, jak często model ma rację ogółem, a F1 Score mówi Ci, jak dobrze model radzi sobie z konkretnym zadaniem (zazwyczaj tym trudniejszym), nie dając się zwieść statystykom.

1. Accuracy (Dokładność)

To najbardziej intuicyjna miara. Odpowiada na pytanie: „Ile procent wszystkich przewidywań było trafionych?”.

$$Accuracy = \frac{\text{Poprawne przewidywania (TP + TN)}}{\text{Wszystkie przypadki}}$$

- Kiedy działa: Tylko wtedy, gdy klasy w Twoim zbiorze są zbalansowane (np. masz 500 zdjęć psów i 500 zdjęć kotów).

- Kiedy zawodzi: Gdy jedna klasa dominuje nad drugą (tzw. class imbalance).

2. F1 ScoreTo średnia harmoniczna Precyzji (Precision) i Czułości (Recall). Nie bierze pod uwagę wyników negatywnych, które model przewidział poprawnie jako negatywne (TN), skupiając się całkowicie na klasie pozytywnej (tej, na której nam zależy).

$$F1 = 2 \cdot \frac{\text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}$$

- Kiedy działa: Zawsze, a zwłaszcza gdy zbiór jest niezbalansowany.

- Kiedy zawodzi: Gdy obie klasy są dla Ciebie tak samo ważne i nie chcesz ignorować trafności w klasie negatywnej (choć to rzadki przypadek).

Bezpośrednie porównanie: Przykład "Test na rzadką chorobę"

Wyobraź sobie, że badasz 1000 osób pod kątem rzadkiej choroby. Wiemy, że 10 osób jest chorych, a 990 osób jest zdrowych.

- Scenariusz: Twój model jest leniwy i zawsze mówi "Jesteś zdrowy"

- Accuracy: Model trafił 990 razy na 1000. Wynik: 99%. Brzmi jak genialny model, prawda? W rzeczywistości jest bezużyteczny, bo nie wykrył nikogo chorego.

- F1 Score: Ponieważ model nie wykrył żadnego chorego, jego Recall wynosi 0. To sprawia, że F1 Score wynosi 0.F1 Score od razu pokazuje, że model „poległ”, podczas gdy Accuracy gratuluje mu sukcesu.

---

## Z lab6

### Logistic Regression

Mimo nazwy „regresja”, jest to algorytm służący do klasyfikacji. Zamiast budować drzewo pytań, stara się on wyznaczyć linię (lub płaszczyznę), która najlepiej oddziela od siebie dwie klasy.

Jak to działa?

- Model liniowy: Algorytm mnoży każdą cechę przez odpowiednią wagę i sumuje je.

- Funkcja Sigmoidalna: Wynik tej sumy jest przepuszczany przez funkcję logistyczną (sigmoid), która „ściska” dowolną liczbę do przedziału od 0 do 1. $$\sigma(z) = \frac{1}{1 + e^{-z}}$$ 

- Prawdopodobieństwo: Wynik interpretujemy jako prawdopodobieństwo przynależności do klasy (np. jeśli wynik to 0.85, to mamy 85% szans, że to klasa „1”).

Zalety:

- Bardzo szybka i efektywna obliczeniowo.

- Zwraca prawdopodobieństwo, co jest użyteczne w biznesie (np. ocena ryzyka kredytowego).

- Mniej podatna na przeuczenie niż drzewa (zwłaszcza przy użyciu regularyzacji L1/L2).

Wady:

- Zakłada liniową zależność między cechami a wynikiem (słabo radzi sobie ze złożonymi, nieliniowymi wzorcami bez inżynierii cech).

### VotingClassifier, Hard/Soft Voting

Voting Classifier (klasyfikator głosujący) to jedna z najprostszych, a zarazem najskuteczniejszych metod Ensemble Learningu (uczenia zespołowego). Idea jest prosta: zamiast ufać jednemu algorytmowi, trenujemy kilka różnych modeli i pozwalamy im głosować nad ostatecznym wynikiem.

1. Hard Voting (Głosowanie twarde)

To system większościowy. Każdy model w zespole przewiduje konkretną klasę (np. „To jest pies” lub „To jest kot”). Klasyfikator głosujący sumuje te głosy i wybiera tę etykietę, która pojawiała się najczęściej.

Przykład: Mamy 3 modele klasyfikujące zdjęcie:

- Model A: Pies

- Model B: Pies

- Model C: Kot

- Wynik końcowy: Pies (bo wygrał 2:1).

Kiedy stosować?

Gdy Twoje modele nie potrafią dobrze oszacować prawdopodobieństwa (czyli jak bardzo są pewne swojego wyboru) lub gdy po prostu chcesz prostego, stabilnego rozwiązania.

2. Soft Voting (Głosowanie miękkie)

To podejście bardziej subtelne i zazwyczaj skuteczniejsze. Zamiast patrzeć na ostateczny werdykt („Pies”/„Kot”), patrzymy na to, jak bardzo każdy model jest pewny swojej decyzji (czyli analizujemy prawdopodobieństwo).

Algorytm wyciąga średnią z prawdopodobieństw dla każdej klasy i wybiera tę, która ma najwyższą średnią.

Przykład:

- Model A: Jestem na 90% pewny, że to Pies.

- Model B: Jestem na 45% pewny, że to Pies (czyli 55%, że Kot).

- Model C: Jestem na 90% pewny, że to Pies.

- Wynik: Średnia dla Psa to $(0.9 + 0.45 + 0.9) / 3 = 0.75$. 

- Wynik końcowy: Pies.

Dlaczego to jest lepsze?

Bo daje większą wagę modelom, które są „bardzo pewne” swojego zdania. Jeśli dwa modele ledwo co skłaniają się ku opcji A, a jeden jest absolutnie przekonany o opcji B, w Soft Voting opcja B może wygrać, mimo że w Hard Voting przegrałaby 1:2.

### Bagging i Pasting

1. Bagging (Bootstrap Aggregating)

Bagging to skrót od Bootstrap Aggregating. Polega na losowaniu podzbiorów danych ze zwracaniem (sampling with replacement).

Jak to działa: Jeśli masz 100 przykładów w zbiorze treningowym, losujesz z niego 100 razy, ale po każdym wylosowaniu przykład „wraca do puli”.

- Efekt: W jednym podzbiorze niektóre przykłady mogą pojawić się kilka razy, a inne mogą nie pojawić się wcale.

- Zaleta: Bardzo dobrze redukuje wariancję modelu (zapobiega overfittingowi). Modele są od siebie bardziej niezależne.

2. Pasting

Pasting jest bardzo podobny do baggingu, ale losowanie odbywa się bez zwracania (sampling without replacement).

- Jak to działa: Losujesz podzbiór przykładów (np. 50 ze 100), ale raz wyciągnięty przykład nie może być wybrany ponownie do tego samego podzbioru.

- Efekt: Każdy przykład w konkretnym podzbiorze jest unikalny.

- Zaleta: Często stosowany, gdy mamy bardzo duży zbiór danych i nie chcemy, aby modele widziały te same dane wielokrotnie w ramach jednej próbki.

3. Half-Bagging i Half-Pasting

Terminy te nie są standardowymi nazwami algorytmów w literaturze naukowej, ale odnoszą się do konfiguracji parametrów w bibliotekach takich jak Scikit-Learn (parametr max_samples).

W klasycznym Baggingu/Pastingu zazwyczaj losuje się podzbiór o takim samym rozmiarze jak oryginalny zbiór treningowy ($n$). Wersje "Half" oznaczają, że ograniczamy rozmiar podzbioru do 50% (połowy) oryginalnych danych.

- Half-Bagging

    - Losujemy podzbiór o rozmiarze równym 0.5 oryginalnego zbioru, stosując zwracanie.Zwiększa to różnorodność między modelami, ponieważ każdy z nich widzi tylko połowę dostępnych informacji.

- Half-Pasting

    - Losujemy podzbiór o rozmiarze równym 0.5 oryginalnego zbioru, bez zwracania. Każdy model trenuje się na unikalnej „połówce” danych.

### BaggingClassifier, AdaBoostClassifier and GradientBoostingClassifier

1. BaggingClassifier (Równoległość)

Bagging (Bootstrap Aggregating) to podejście „demokratyczne”. Trenujemy wiele modeli jednocześnie i niezależnie od siebie, a ostateczny wynik jest średnią (lub wynikiem głosowania) ich wszystkich.

Jak to działa: Każdy model w zespole trenuje się na nieco innym podzbiorze danych (losowanie ze zwracaniem – tzw. bootstrapping).

Główny cel: Redukcja wariancji (overfittingu). Dzięki temu, że modele widzą różne dane, błędy pojedynczych drzew znoszą się w tłumie.

Przykład: Las Losowy (Random Forest) to w uproszczeniu BaggingClassifier używający wyłącznie drzew decyzyjnych.

2. AdaBoostClassifier (Sekwencyjność + Wagi)

AdaBoost (Adaptive Boosting) to podejście „uczenia się na błędach”. Modele są trenowane jeden po drugim (sekwencyjnie).

Jak to działa: 

- 1. Trenujemy pierwszy model.

- 2. Sprawdzamy, które przykłady danych źle zaklasyfikował.

- 3. Zwiększamy wagę tych trudnych przykładów, aby kolejny model skupił się głównie na nich.

- 4. Powtarzamy proces, a każdy kolejny model staje się „ekspertem” od błędów poprzednika.

Główny cel: Redukcja obciążenia (bias) i błędu.

3. GradientBoostingClassifier (Sekwencyjność + Reszty)

Gradient Boosting to „starszy i potężniejszy brat” AdaBoost. Również działa sekwencyjnie, ale zamiast zmieniać wagi danych, używa rachunku różniczkowego.

Jak to działa:

- 1. Trenujemy model $M1$.

- 2. Obliczamy błąd (resztę) – czyli różnicę między tym, co model przewidział, a rzeczywistością.

- 3. Trenujemy model $M2$, ale nie po to, by przewidział wynik końcowy, tylko by przewidział błąd modelu M1.

- 4. Dodajemy te przewidywania błędu do wyniku $M1$, aby go skorygować.

Główny cel: Maksymalizacja skuteczności. To jeden z najpotężniejszych algorytmów dla danych tabelarycznych (na jego bazie powstały słynne biblioteki jak XGBoost czy LightGBM).

AdaBoost (Adaptive Boosting): Skupia się na wagach punktów danych. Jeśli dany przykład został źle sklasyfikowany przez poprzedni model, w następnej iteracji otrzymuje on większą wagę. Dzięki temu kolejny „słaby uczeń” (weak learner) kładzie większy nacisk na te trudne przypadki.

Gradient Boosting: Skupia się na funkcji straty (loss function). Zamiast zmieniać wagi punktów, Gradient Boosting próbuje przewidzieć reszty (residuals), czyli różnicę między wartością rzeczywistą a przewidzianą przez poprzednie modele. Wykorzystuje do tego algorytm spadku gradientu (gradient descent), aby zminimalizować błąd.

### Dlaczego Random Forest daje inne rezultaty niż Bagging + drzewa decyzyjne?

Mimo że oba podejścia opierają się na technice Baggingu (losowaniu wierszy ze zwracaniem), istnieje jedna fundamentalna różnica, która sprawia, że Random Forest jest zazwyczaj potężniejszy: sposób losowania cech.

Zwykły Bagging + Drzewa: Każde drzewo w zespole ma dostęp do wszystkich cech (chyba że ręcznie ustawimy max_features w BaggingClassifier). Jeśli w danych istnieje jedna, bardzo silna cecha (np. "zarobki" przy przewidywaniu zdolności kredytowej), to każde drzewo w zespole prawdopodobnie użyje jej na samym początku. Wynik? Otrzymujemy zestaw 100 drzew, które są do siebie bardzo podobne (skorelowane).

Random Forest: Wprowadza dodatkowy poziom losowości. Cechy są losowane przy każdym podziale węzła (split).

Nawet jeśli cecha "zarobki" jest najsilniejsza, algorytm w wielu węzłach celowo jej "nie widzi", zmuszając drzewo do szukania wzorców w słabszych cechach.

Efekt: Drzewa w Lesie Losowym są od siebie odkorelowane. Dzięki temu, gdy wyciągamy z nich średnią, błąd wariancji spada znacznie mocniej niż w zwykłym Baggingu.

### Problemy z samplingiem cech przy identyfikacji ważności

Stwierdzenie z punktu 4. odnosi się do faktu, że losowe wybieranie podzbioru cech (tak jak robiłeś to w kodzie z max_features=2) może "oślepić" model na rzeczywiste relacje w danych. Oto dlaczego nie jest to idealne narzędzie diagnostyczne:

A. Problem Interakcji (Zależności)
Niektóre cechy działają jak "drużyna". Pojedynczo mogą nie znaczyć nic, ale razem dają ogromną informację.

Przykład: Wyobraź sobie, że przewidujesz, czy ktoś kupi lody. Masz cechy: "Temperatura" i "Czy jest lato". Jeśli Twój sampling cech wylosuje tylko "Czy jest lato", model może nie zauważyć, że ludzie nie kupują lodów w chłodne, letnie dni.

Jeśli algorytm losuje tylko małe podzbiory, może nigdy nie trafić na kombinację cech, która jest kluczowa.

B. Multikoliniowość (Cechy nadmiarowe)
Jeśli dwie cechy są ze sobą silnie skorelowane (np. "Wiek" i "Rok urodzenia"), niosą tę samą informację.

Przy samplingu cech, raz model użyje jednej, raz drugiej. W analizie ważności cech wynik zostanie "rozmyty" między obie kolumny, przez co żadna z nich nie będzie wyglądać na dominującą, mimo że informacja o czasie jest kluczowa.

C. Fałszywe wnioski
Gdy oceniasz ważność cech na podstawie samplingu (tak jak w Twoim pętli for), widzisz tylko, jak dana cecha radzi sobie w losowym, ograniczonym środowisku. To nie mówi Ci, jak ważna byłaby ta cecha, gdyby model miał do dyspozycji pełen kontekst danych.

---

## Z lab7

### Algorytm KMeans (K-Średnich)

KMeans to algorytm partycjonujący, który dzieli zbiór danych na $k$ rozłącznych grup (klastrów). Jego celem jest minimalizacja sumy kwadratów odległości między punktami a środkami ich klastrów (tzw. centroidami).

Jak to działa krok po kroku?

- Inicjalizacja: Wybierasz liczbę $k$ (liczbę grup). Algorytm losuje $k$ punktów, które stają się pierwszymi centroidami.

- Przypisanie (Assignment): Dla każdego punktu w zbiorze danych obliczana jest odległość (zazwyczaj euklidesowa) do każdego z $k$ centroidów. Punkt zostaje przypisany do klastra, którego centroid jest najbliżej.

- Aktualizacja (Update): Gdy wszystkie punkty są już przypisane, algorytm oblicza nową pozycję każdego centroidu. Nowy centroid to średnia arytmetyczna współrzędnych wszystkich punktów przypisanych do danej grupy.

- Powtarzanie: Kroki 2 i 3 są powtarzane tak długo, aż centroidy przestaną się przesuwać lub zostanie osiągnięta maksymalna liczba iteracji.

Kluczowe wzory

- Odległość euklidesowa między punktem $x$ a centroidem $c$ w przestrzeni $n$-wymiarowej:

$$d(x, c) = \sqrt{\sum_{i=1}^{n} (x_i - c_i)^2}$$

### Silhouette Score (Wskaźnik Sylwetki)

Sam algorytm KMeans nie powie Ci, czy wybrana liczba $k$ jest optymalna. Do tego służy Silhouette Score. Pozwala on zmierzyć, jak dobrze dany punkt pasuje do swojego klastra w porównaniu do klastrów sąsiednich.

Jak obliczany jest wynik dla pojedynczego punktu?

- Dla każdego punktu $i$ obliczamy dwie wartości:

    - $a(i)$ (Kohezja): Średnia odległość punktu $i$ od wszystkich innych punktów w tym samym klastrze. Chcemy, aby ta wartość była jak najmniejsza (punkt blisko swoich "kolegów").

    - $b(i)$ (Separacja): Średnia odległość punktu $i$ od punktów znajdujących się w najbliższym klastrze (tym, do którego punkt $i$ nie należy). Chcemy, aby ta wartość była jak największa.

- Wzór na Silhouette Score dla punktu $i$: $$s(i) = \frac{b(i) - a(i)}{\max(a(i), b(i))}$$

- Interpretacja wyników: Wskaźnik sylwetki przyjmuje wartości od -1 do 1:

    - Blisko 1: Punkt jest bardzo dobrze przypisany. Jest blisko swojego klastra i daleko od innych.

    - Blisko 0: Punkt znajduje się na granicy dwóch klastrów (klastry nachodzą na siebie).

    - Blisko -1: Punkt prawdopodobnie został przypisany do błędnego klastra.

Średni Silhouette Score dla całego zbioru danych pozwala nam wybrać najlepsze $k$ – zazwyczaj wybieramy taką liczbę klastrów, dla której średnia sylwetka jest najwyższa.

### DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

1. Dwa kluczowe parametry

Zanim DBSCAN ruszy do pracy, musimy zdefiniować dwie wartości:

- $\epsilon$ (Epsilon): Promień sąsiedztwa. Określa, jak blisko siebie muszą być punkty, aby uznać je za "sąsiadów".

- MinPts (Minimum Points): Minimalna liczba punktów, która musi znaleźć się wewnątrz promienia $\epsilon$ (wliczając sam punkt), aby dany obszar został uznany za gęsty i mógł stać się klastrem.

2. Trzy rodzaje punktów

DBSCAN dzieli wszystkie punkty w zbiorze na trzy kategorie:

- Punkty rdzeniowe (Core Points): To punkty, które w promieniu $\epsilon$ mają co najmniej MinPts sąsiadów. To one są "silnikami" klastrów.

- Punkty graniczne (Border Points): Mają mniej sąsiadów niż MinPts, ale znajdują się w promieniu $\epsilon$ od jakiegoś punktu rdzeniowego. Należą do klastra, ale nie mogą go dalej rozbudowywać.

- Szum (Noise / Outliers): Punkty, które nie są rdzeniowe i do których nie można dotrzeć z żadnego punktu rdzeniowego. Są po prostu ignorowane przez algorytm.

3. Algorytm krok po kroku

- Wybór punktu: Algorytm wybiera losowy, nieodwiedzony jeszcze punkt ze zbioru.

- Sprawdzanie gęstości: Sprawdza, ile punktów znajduje się w odległości $\epsilon$ od niego.

- Tworzenie klastra: Jeśli punkt ma wystarczającą liczbę sąsiadów ($\ge MinPts$), staje się punktem rdzeniowym i zaczyna nowy klaster. Wszystkie punkty w jego otoczeniu ($\epsilon$) są dodawane do tego klastra.

- Ekspansja: Algorytm sprawdza sąsiadów sąsiadów. Jeśli oni również są punktami rdzeniowymi, klaster "rozlewa się" dalej, dołączając kolejne punkty. Proces trwa, dopóki klaster nie przestanie rosnąć.

- Obsługa szumu: Jeśli wybrany na początku punkt ma za mało sąsiadów i nie sąsiaduje z żadnym punktem rdzeniowym, zostaje oznaczony jako szum (tymczasowo, bo później może stać się punktem granicznym innego klastra).

- Powtórzenie: Algorytm wybiera kolejny nieodwiedzony punkt i powtarza proces, aż przetworzy cały zbiór.

4. Dlaczego DBSCAN jest potężny?

Główną przewagą DBSCAN nad KMeans jest jego zdolność do radzenia sobie z klastrami o dowolnych kształtach. KMeans zawsze próbuje tworzyć "bańki" (klastry sferyczne), podczas gdy DBSCAN potrafi znaleźć klastry w kształcie liter, pierścieni czy linii.

Główne zalety:

- Nie musisz podawać liczby klastrów ($k$): Algorytm sam wykryje, ile grup znajduje się w danych.

- Odporność na szum: Jako jeden z niewielu algorytmów potrafi wprost powiedzieć: "te punkty to błędy/anomalie, nie pasują nigdzie".

- Dowolne kształty: Świetnie radzi sobie z danymi przestrzennymi i nietypowymi strukturami.

Wady:

- Wrażliwość na parametry: Wybór złego $\epsilon$ może sprawić, że wszystko stanie się jednym klastrem lub wszystko zostanie uznane za szum.

- Różna gęstość: Jeśli w Twoich danych są klastry bardzo gęste i bardzo rzadkie, jedno $\epsilon$ nie zadziała dobrze dla obu jednocześnie.

### Czym jest klasteryzacja

Klasteryzacja vs Klasyfikacja – gdzie leży różnica?

To najczęstsze miejsce, w którym początkujący analitycy danych się gubią.

- Klasyfikacja (Nadzorowana): Masz kosz owoców. Dajesz modelowi 1000 zdjęć podpisanych "jabłko" i 1000 podpisanych "banan". Model uczy się ich cech. Kiedy dajesz mu nowy owoc, on mówi: "To z pewnością jest banan z prawdopodobieństwem 95%". Model wie, czego szuka.

- Klasteryzacja (Nienadzorowana): Masz kosz dziwnych, kosmicznych owoców, których nikt nigdy nie widział i nie nazwał. Model analizuje je i dzieli na trzy grupy: "te twarde i okrągłe", "te podłużne i miękkie" oraz "te małe i kolczaste". Model nie ma pojęcia, co to jest. Po prostu zauważył, że pewne obiekty są do siebie bardzo podobne. Dopiero Ty (człowiek) musisz spojrzeć na te grupy i nadać im sens biznesowy.

Do czego używa się klasteryzacji w prawdziwym świecie?

Skoro algorytm nie daje nam konkretnych odpowiedzi ("to jest spam", "to jest kot"), to po co nam on? Okazuje się, że to jedno z najpotężniejszych narzędzi w biznesie:

- Segmentacja klientów (Marketing): Sklepy internetowe mają góry danych o zakupach, ale nie wiedzą, kim są ich klienci. Klasteryzacja analizuje koszyki i wiek, po czym dzieli klientów na grupy. Nagle okazuje się, że masz wyraźny klaster "łowców weekendowych promocji", "rodziców kupujących pieluchy" i "entuzjastów elektroniki premium". Możesz dzięki temu wysyłać idealnie spersonalizowane reklamy.

- Systemy Rekomendacji (Netflix / Spotify): Dlaczego Netflix wie, co Ci się spodoba? Bo algorytm umieścił Cię w jednym klastrze z milionem innych ludzi o podobnym guście. Jeśli 90% ludzi w Twoim klastrze świetnie oceniło nowy serial sci-fi, to Netflix poleci go również Tobie.

- Wykrywanie Oszustw i Anomalii (Fraud Detection): Większość transakcji kartą kredytową jest nudna i przewidywalna – tworzą one jeden wielki, gęsty klaster "normalnych zachowań". Jeśli nagle ktoś kupuje trzy telewizory w kraju, w którym nigdy Cię nie było, ten punkt na wykresie wyląduje bardzo daleko od głównego klastra (będzie outlierem). System natychmiast blokuje kartę.

- Medycyna i Genetyka: Badacze wrzucają do modelu dane o genach tysięcy pacjentów z podobnymi objawami. Klasteryzacja może odkryć, że choroba "X" to tak naprawdę trzy różne, delikatnie różniące się od siebie podtypy chorób, które wymagają innych leków.

Podsumowując: klasteryzacja nie daje Ci gotowych odpowiedzi. Daje Ci kompas w świecie gigantycznego, chaotycznego oceanu danych, pozwalając dostrzec w nim porządek.

### Inertia i Elbow Method

1. Inercja (Inertia / WCSS)

Inercja (w literaturze często spotykana pod skrótem WCSS, czyli Within-Cluster Sum of Squares) to wewnętrzna miara błędu algorytmu K-Means. Mówi nam ona o tym, jak bardzo "zbite" i spójne są nasze klastry.

Zasada: Dla każdego punktu w klastrze mierzymy jego odległość do środka tego klastra (centroidu), podnosimy tę odległość do kwadratu (aby uniknąć wartości ujemnych i mocniej ukarać dalekie punkty), a na koniec sumujemy te wartości dla wszystkich punktów we wszystkich klastrach.

Matematyka: $$WCSS = \sum_{j=1}^{k} \sum_{i \in C_j} d(x_i, \mu_j)^2$$

Gdzie:

- $k$ to całkowita liczba klastrów.

- $C_j$ to zbiór punktów należących do klastra $j$.

- $x_i$ to konkretny punkt danych.

- $\mu_j$ to centroid (środek) klastra $j$.

- $d$ to metryka odległości (np. euklidesowa).

Interpretacja: Im mniejsza inercja, tym lepiej – oznacza to, że punkty leżą bardzo blisko swoich centroidów. Haczyk (Dlaczego sama inercja nie wystarczy?):

- Możesz pomyśleć: "W takim razie znajdźmy takie $k$, dla którego inercja jest najmniejsza!". Tu pojawia się problem. Jeśli masz 100 punktów danych i ustawisz $k=100$ (każdy punkt jest swoim własnym klastrem i centroidem jednocześnie), odległość do środka wyniesie $0$. Inercja spadnie do idealnego zera, ale taki model jest całkowicie bezużyteczny. Inercja zawsze maleje (lub pozostaje bez zmian), gdy zwiększamy $k$.

2. Metoda Łokcia (Elbow Method)

Skoro inercja zawsze maleje wraz ze wzrostem liczby klastrów, musimy znaleźć optymalny kompromis (trade-off) między niską inercją a małą, sensowną biznesowo liczbą klastrów. Do tego służy Metoda Łokcia.

Jak to działa krok po kroku:

- Uruchamiamy algorytm K-Means wielokrotnie, za każdym razem zwiększając liczbę klastrów ($k=1, k=2, k=3, \dots$).

- Dla każdego uruchomienia zapisujemy końcową wartość inercji.

- Rysujemy wykres liniowy: na osi X odkładamy liczbę klastrów ($k$), a na osi Y obliczoną Inercję.

Czego szukamy? Wykres ten zawsze będzie przypominał opadające ramię. Szukamy na nim "łokcia" (punktu przegięcia) – miejsca, w którym gwałtowny spadek inercji nagle zwalnia i staje się niemal płaski.

Logika "Łokcia": Ten punkt oznacza, że dodawanie kolejnych klastrów nie przynosi już znaczącej poprawy w "zbiciu" danych. Zatem punkt przegięcia wyznacza optymalne $k$.

## Z lab8

### Przekleństwo wymiarowości, Projekcja, Rozmaitość, Mainfold Learning

1. Przekleństwo wymiarowości (The Curse of Dimensionality)

Więcej danych to zwykle lepiej, ale więcej wymiarów (cech) to często przepis na katastrofę. 

Dlaczego?

- Pustka kosmiczna: Wraz ze wzrostem liczby wymiarów, objętość przestrzeni rośnie wykładniczo. Dane stają się niesamowicie rzadkie (sparse). Twoje 70 000 obrazków MNIST w przestrzeni 784-wymiarowej to tylko maleńkie, samotne punkty w nieskończonej próżni.

- Zanikanie dystansu: W wysokich wymiarach odległość euklidesowa "wariuje". Odległość między najbliższym a najdalszym punktem staje się niemal taka sama. Wszystkie punkty wydają się być „daleko” i „tak samo blisko” jednocześnie, co zabija algorytmy oparte na odległości (jak KNN czy KMeans).

- Overfitting: Przy ogromnej liczbie cech model łatwo znajduje przypadkowe korelacje, które są tylko szumem, a nie prawdziwą zależnością.

2. Projekcje (Projections)

Projekcja to najprostszy sposób na walkę z wymiarami. Wyobraź sobie, że świecisz latarką na trójwymiarowy obiekt, a na ścianie widzisz jego dwuwymiarowy cień.

Jak to działa? 

Rzutujemy punkty z przestrzeni $N$-wymiarowej na podprzestrzeń o mniejszej liczbie wymiarów ($d < N$).

- Główny gracz: PCA (Principal Component Analysis). Algorytm szuka takich osi (kierunków), wzdłuż których dane są najbardziej rozproszone (mają największą wariancję), i na nie rzutuje dane.

- Problem: Projekcja "płaska" (liniowa) zawodzi, gdy dane są zwinięte lub zakrzywione. Jeśli rzucisz cień "szwajcarskiej rolady" (Swiss Roll) na płaską kartkę, zgnieciesz jej strukturę i wymieszasz punkty, które w rzeczywistości są od siebie daleko.

3. Rozmaitość (Manifold) i Hipoteza Rozmaitości

To tutaj robi się ciekawie. Rozmaitość to przestrzeń, która lokalnie przypomina płaską przestrzeń euklidesową, ale globalnie może być bardzo skomplikowana (zakrzywiona).

Przykład Ziemi: Z perspektywy mrówki idącej po ogrodzie, Ziemia jest płaska (2D). Ale my wiemy, że to zakrzywiona sferyczna rozmaitość osadzona w przestrzeni 3D.

Hipoteza Rozmaitości (Manifold Hypothesis): Zakłada ona, że większość rzeczywistych danych o wysokiej wymiarowości (np. zdjęcia twarzy, cyfry MNIST) leży w rzeczywistości na znacznie niżej wymiarowej, zakrzywionej rozmaitości.

- Przykład z MNIST: Obrazek cyfry ma 784 piksele (784 wymiary). Ale nie każda kombinacja pikseli tworzy cyfrę. Większość to po prostu losowy szum. Cyfry tworzą się tylko wtedy, gdy zmieniamy kilka parametrów: kąt nachylenia, grubość kreski, stopień domknięcia pętli w "8". Te kilka parametrów to właśnie te niskie wymiary ukryte wewnątrz 784-wymiarowej przestrzeni.

4. Manifold Learning (Uczenie Rozmaitości)

Zamiast brutalnie rzutować dane (jak PCA), algorytmy Manifold Learning (np. t-SNE, LLE, Isomap) próbują „rozwinąć” te zakrzywione struktury.

Wyobraź sobie wspomnianą szwajcarską roladę:PCA próbowałoby ją spłaszczyć, miażdżąc warstwy ciasta.

Algorytm rozmaitości próbuje ją delikatnie rozwinąć na stole tak, aby punkty, które są blisko siebie "w cieście", pozostały blisko siebie na płaszczyźnie.

### PCA i SVD oraz kompresja jako efekt uboczny

1. PCA (Principal Component Analysis) – Analiza Składowych Głównych

PCA to metoda statystyczna, której celem jest uproszczenie danych przy zachowaniu jak największej ilości informacji (wariancji).

Jak to działa w praktyce?

Wyobraź sobie chmurę punktów w 3D, która układa się w kształt cygara. PCA znajduje nową oś (Składową Główną nr 1), która przechodzi idealnie wzdłuż tego cygara. To na tej osi punkty są najbardziej "rozstrzelone". Druga oś (Składowa Główna nr 2) jest prostopadła do pierwszej i wyłapuje resztę zmienności.

- Centrowanie danych: Od każdej cechy odejmujemy jej średnią (przesuwamy chmurę punktów do środka układu współrzędnych).

- Szukanie kierunków: PCA szuka kierunku, w którym dane mają największą wariancję.

- Transformacja: Obracamy cały układ współrzędnych tak, aby nasze nowe osie (składowe) stały się nowymi wymiarami.

2. SVD (Singular Value Decomposition) – Rozkład według wartości osobliwych

SVD to czysta algebra. Twierdzi ona, że każdą macierz $A$ (nawet niekwadratową) można rozłożyć na iloczyn trzech specyficznych macierzy: 

- $$A = U \Sigma V^T$$$U$ (Macierz lewostronnych wektorów osobliwych): Opisuje relacje między wierszami (np. w MNIST – między obrazkami).

- $\Sigma$ (Sigma - Macierz diagonalna): Zawiera tzw. wartości osobliwe uporządkowane od największej do najmniejszej. Mówią nam one, jak ważna jest każda "składowa".

- $V^T$ (Macierz prawostronnych wektorów osobliwych): Opisuje relacje między cechami (np. pikselami).

SVD jest jak rozkładanie liczby na czynniki pierwsze (np. $12 = 2 \times 2 \times 3$), tylko robimy to dla skomplikowanych zbiorów danych.

3. PCA vs SVD – Jaka jest różnica?

To najczęstsze pytanie na rozmowach kwalifikacyjnych dla Data Scientistów.

- SVD to narzędzie matematyczne: Możesz je zastosować do dowolnej macierzy. Nie wymaga ono, aby dane miały jakąś interpretację statystyczną.

- PCA to proces statystyczny: PCA zazwyczaj używa SVD jako swojego silnika. Jeśli najpierw scentrujesz dane (odejmiesz średnią), a potem wykonasz na nich SVD, to wynik będzie identyczny z PCA. 

W skrócie: PCA = Centrowanie danych + SVD.

4. Kompresja jako "efekt uboczny"Gdy wykonasz SVD lub PCA, zauważysz, że wartości w macierzy $\Sigma$ (lub wariancja w PCA) drastycznie spadają. Pierwsze kilka składowych zawiera 90% informacji o danych, a reszta to często drobne detale lub zwykły szum.

Na czym polega kompresja (Low-Rank Approximation)?

Decydujemy się zachować tylko $k$ pierwszych składowych (tych najważniejszych), a resztę zerujemy.

- Zysk: Zamiast trzymać 784 piksele dla każdego obrazka MNIST, trzymasz np. tylko 20 liczb. To ogromna oszczędność pamięci.

- Efekt: Gdy spróbujesz odtworzyć obrazek z tych 20 liczb, będzie on nieco rozmazany, ale wciąż rozpoznawalny. Usunąłeś "szum" i zbędne szczegóły, zostawiając esencję (rozmaitość, o której mówiliśmy wcześniej).

- Czyszczenie danych: Kompresja często poprawia działanie modeli ML, bo usuwa drobne, przypadkowe wahania w danych (overfitting), zostawiając tylko główne trendy.

### CZym jest LLE

LLE to algorytm z rodziny Manifold Learning (uczenia rozmaitości). Jego głównym założeniem jest to, że nawet jeśli zbiór danych jest bardzo skomplikowany i zakrzywiony globalnie (np. jak wspomniana wcześniej "szwajcarska rolada"), to jeśli spojrzymy na niego przez lupę, każdy mały fragment jest niemal płaski (liniowy).

Serce algorytmu: "Pokaż mi swoich sąsiadów"

- LLE próbuje zachować lokalne relacje między punktami. Zamiast patrzeć na to, jak daleko jest punkt A od punktu Z, patrzy na to, jak punkt A jest "zmontowany" ze swoich najbliższych sąsiadów.

Algorytm składa się z trzech głównych kroków:

- 1. Szukanie sąsiadów (Find Neighbors)

    - Dla każdego punktu $x_i$ w zbiorze danych szukamy jego $k$ najbliższych sąsiadów (zazwyczaj używając zwykłej odległości euklidesowej).

- 2. Rekonstrukcja wag (Weights Reconstruction)

    - To jest najsprytniejsza część. Zakładamy, że każdy punkt $x_i$ można przedstawić jako liniową kombinację jego sąsiadów.

    - Szukamy takich wag $w_{ij}$, aby błąd przybliżenia punktu $x_i$ przez jego sąsiadów był jak najmniejszy.

    - Równanie: Minimalizujemy funkcję błędu:$$\varepsilon(W) = \sum_{i} \left| x_i - \sum_{j} w_{ij}x_j \right|^2$$

    - Wagi muszą się sumować do 1 ($\sum w_{ij} = 1$), co sprawia, że są one odporne na przesunięcia czy obroty danych.

- 3. Odwzorowanie w niskim wymiarze (Embedding)

- Teraz "przenosimy" punkty do przestrzeni o niższym wymiarze (np. z 784D do 2D).

- Zasada: Chcemy, aby w nowej, niskowymiarowej przestrzeni punkty $y_i$ były powiązane tymi samymi wagami $w_{ij}$, które wyliczyliśmy w kroku 2.

- Wagi zostają zamrożone, a my szukamy nowych współrzędnych $y_i$, które najlepiej je "szanują": $$\Phi(Y) = \sum_{i} \left| y_i - \sum_{j} w_{ij}y_j \right|^2$$

Dlaczego LLE jest wyjątkowe?

LLE "rozsznurowuje" dane. Jeśli masz dane w kształcie litery "S" w 3D, PCA zmiażdży tę literę, nakładając jej końce na siebie. LLE natomiast rozprostuje ją do linii prostej na płaszczyźnie, zachowując informację o tym, kto obok kogo stał w oryginale.

Zalety:

- Nieliniowość: Świetnie radzi sobie z zawiniętymi strukturami.

- Brak globalnych założeń: Nie obchodzi go, czy cała chmura danych ma kształt elipsy czy spirali – interesuje go tylko lokalna gęstość.

- Mało parametrów: W zasadzie musisz wybrać tylko liczbę sąsiadów $k$ i docelowy wymiar.

Wady:

- Czułość na szum: Jeśli między warstwami "rolady" znajdzie się przypadkowy punkt (szum), LLE może błędnie "skleić" te warstwy.

- Problemy z brzegami: Na krawędziach rozmaitości LLE czasem radzi sobie gorzej, bo punkty mają tam sąsiadów tylko z jednej strony.

---