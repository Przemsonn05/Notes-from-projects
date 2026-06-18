# Sieci Neuronowe i Deep Learning - wiedza z wykładów

## Wykład 9 - Uczenie maszynowe

## Spis treści

1. [Neurony biologiczne i sztuczne](#1-neurony-biologiczne-i-sztuczne)
2. [Neuron sztuczny — McCulloch & Pitts](#2-neuron-sztuczny--mcculloch--pitts)
3. [Perceptron](#3-perceptron)
4. [Perceptrony wielowarstwowe (MLP) i propagacja wsteczna](#4-perceptrony-wielowarstwowe-mlp-i-propagacja-wsteczna)
5. [PyTorch — podstawy](#5-pytorch--podstawy)
6. [Dodatek — teoria spoza prezentacji przydatna na egzamin](#6-dodatek--teoria-spoza-prezentacji-przydatna-na-egzamin)

---

## 1. Neurony biologiczne i sztuczne

### Neuron biologiczny
Sieci neuronowe (ANN — *Artificial Neural Networks*) są zainspirowane budową ludzkiego mózgu. Biologiczny neuron składa się z:
- **dendrytów** — odbierają sygnały od innych neuronów,
- **ciała komórki (soma)** — sumuje sygnały wejściowe,
- **aksonu** — przekazuje sygnał do kolejnych neuronów,
- **synaps** — miejsc styku, w których przekazywany jest sygnał (ich „siła” to odpowiednik wagi w neuronie sztucznym).

Neuron biologiczny aktywuje się, gdy łączna stymulacja przekroczy pewien próg.

---

## 2. Neuron sztuczny — McCulloch & Pitts

- Po raz pierwszy zaproponowany w **1943** przez **Warrena S. McCullocha** i **Waltera Pittsa** w pracy *"A logical calculus of the ideas immanent in nervous activity"*.
- **Założenie:** neuron ulega aktywacji, gdy co najmniej dwa jego wejścia są aktywne.
- Model był binarny — wejścia i wyjścia 0/1, brak wag (lub wagi binarne).
- Pozwalał implementować proste funkcje logiczne (AND, OR, NOT), ale **nie uczył się** — wagi były ustalane ręcznie.

---

## 3. Perceptron

- Jedna z najprostszych architektur ANN.
- Zaproponowana w **1957** przez **Franka Rosenblatta**.
- Neuron zwany **TLU** (*threshold logic unit*) lub **LTU** (*linear threshold unit*).
- Wejścia są liczbami rzeczywistymi, a każde wejście posiada wagę.

### Sposób działania

1. Oblicz ważoną sumę wejść:

$$
z = w_1 x_1 + w_2 x_2 + \cdots + w_n x_n = \mathbf{x}^T \mathbf{w}
$$

2. Zastosuj **funkcję schodkową** (step):

$$
h_w(\mathbf{x}) = \text{step}(z)
$$

### Funkcje schodkowe

**Funkcja Heaviside'a:**

$$
\text{heaviside}(z) = \begin{cases} 0 & \text{dla } z < 0 \\ 1 & \text{dla } z \geq 0 \end{cases}
$$

**Funkcja signum:**

$$
\text{sgn}(z) = \begin{cases} -1 & \text{dla } z < 0 \\ 0 & \text{dla } z = 0 \\ 1 & \text{dla } z > 0 \end{cases}
$$

### 3.1. Warstwa gęsta (dense / fully connected)

Jeżeli **każdy neuron w warstwie jest połączony ze wszystkimi neuronami w warstwie poprzedzającej**, warstwa jest **w pełni połączona** (*fully connected*) lub **gęsta** (*dense*).

**Obliczenie wyjścia warstwy gęstej:**

$$
\mathbf{\hat{Y}} = \phi(\mathbf{X}\mathbf{W} + \mathbf{b})
$$

Gdzie:
- $\mathbf{X}$ — macierz wejściowa (jeden wiersz = jedna próbka),
- $\mathbf{W}$ — macierz wag (kolumna na neuron),
- $\mathbf{b}$ — wektor obciążeń (*biases*),
- $\phi$ — funkcja aktywacji,
- $\mathbf{\hat{Y}}$ — wyjście warstwy.

### 3.2. Uczenie perceptronu

- Algorytm Rosenblatta był zainspirowany **zasadą Hebba** (Donald Hebb, *The Organization of Behavior*, 1949).
- Siegrid Löwel: *"Cells that fire together, wire together."* — komórki, które jednocześnie się aktywują, wzmacniają swoje połączenie.

**Reguła aktualizacji wag** (w każdym kroku):

$$
w_{i,j}' = w_{i,j} + \eta (y_j - \hat{y}_j) x_i
$$

Gdzie:
- $w_{i,j}$ — waga połączenia od wejścia $i$ do neuronu $j$,
- $\eta$ — krok uczenia (*learning rate*),
- $y_j$ — wartość oczekiwana (etykieta),
- $\hat{y}_j$ — wartość przewidywana przez neuron,
- $x_i$ — wartość wejścia $i$.

> **Interpretacja:** jeżeli neuron dał poprawną odpowiedź ($y_j = \hat{y}_j$), wagi się nie zmieniają. Jeżeli się pomylił — wagi modyfikujemy w kierunku zmniejszającym błąd.

### 3.3. Perceptron vs. XOR

- Pojedynczy perceptron **nie potrafi** rozwiązywać pewnych trywialnych problemów, jak np. klasyfikacja **XOR**.
- Powód: XOR nie jest **liniowo separowalny** — nie da się go rozdzielić jedną prostą.
- Problem ten **można rozwiązać poprzez dodanie dodatkowej warstwy** — w ten sposób narodziły się sieci wielowarstwowe (MLP).
- Słynne odkrycie ograniczeń perceptronu przez Minsky'ego i Paperta (1969, książka *Perceptrons*) spowolniło rozwój sieci neuronowych na lata.

---

## 4. Perceptrony wielowarstwowe (MLP) i propagacja wsteczna

### 4.1. Perceptron wielowarstwowy (MLP — *Multilayer Perceptron*)

Składa się z:
- **warstwy wejściowej** (*input layer*) — przekazuje dane do sieci,
- jednej lub więcej **warstw ukrytych** (*hidden layers*),
- **warstwy wyjściowej** (*output layer*).

Każda warstwa jest zwykle gęsta. Sieć z co najmniej jedną warstwą ukrytą może aproksymować dowolną funkcję ciągłą (**twierdzenie o uniwersalnym aproksymatorze**).

### 4.2. Rola różniczkowania automatycznego w trenowaniu sieci

- Przez wiele lat opracowanie efektywnego algorytmu uczenia stanowiło problem.
- Na początku lat 60. pojawił się pomysł użycia **algorytmów gradientowych**, ale obliczanie gradientów było zbyt czasochłonne.
- W **1970** **Seppo Linnainmaa** zaproponował algorytm **automatycznego różniczkowania metodą wstecz** (*reverse-mode autodiff*), który w **zaledwie dwóch przejściach** (jedno w przód, jedno wstecz) wylicza gradienty.
- Te gradienty można wykorzystać do korekty wag w sieci.

> **Kluczowe równanie filozoficzne:**
> **Reverse autodiff + gradient descent = backpropagation**

### 4.3. Uczenie MLP — algorytm propagacji wstecznej

- W **1986** Rumelhart, Hinton i Williams zaproponowali algorytm **propagacji wstecznej** (*backpropagation*) — używany do dziś.
- Algorytm przechodzi przez sieć **raz do przodu** (przeprowadzenie predykcji) i **raz wstecz**.
- Oblicza gradient błędu w odniesieniu do każdego parametru i modyfikuje wagi (à la *gradient descent*).
- **Aby pojawił się gradient, zastępujemy funkcję schodkową inną funkcją** — różniczkowalną (np. sigmoid, tanh, ReLU).

**Schemat działania (krok po kroku):**

1. **Forward pass** — przepuść mini-batch przez sieć, oblicz wyjścia każdej warstwy oraz funkcję straty.
2. **Backward pass** — oblicz gradient funkcji straty względem wszystkich parametrów (reguła łańcuchowa, od końca sieci do początku).
3. **Aktualizacja wag** — wykonaj krok gradientu: $w \leftarrow w - \eta \cdot \nabla_w L$.
4. Powtarzaj dla kolejnych mini-batchy aż do zbieżności.

### 4.4. Regresja przy pomocy MLP

- **Tyle neuronów wyjściowych, ile etykiet** (labels).
- **Funkcja aktywacji warstwy wyjściowej:**
    - żadna (wyjście liniowe — najczęściej),
    - **ReLU** — aby wyjście było dodatnie,
    - **softplus** $\text{softplus}(z) = \log(1 + e^z)$ — gładki wariant ReLU,
    - **sigmoid lub tanh** — jeżeli wartości mają być w zadanym przedziale.
- **Funkcja straty:** **MSE**, **MAE** lub **strata Hubera**.

### 4.5. Klasyfikacja przy pomocy MLP

Działa podobnie jak regresja, ale z drobnymi różnicami w warstwie wyjściowej.

| Typ klasyfikacji | Liczba neuronów wyjściowych | Funkcja aktywacji |
|---|---|---|
| **Binarna** | 1 | sigmoid |
| **Wieloetykietowa** (multi-label) | 1 na etykietę | sigmoid (każdy niezależnie) |
| **Wieloklasowa** (multi-class) | 1 na klasę | softmax |

**Funkcja straty:** **log loss** (cross-entropy).

### 4.6. Liczba warstw ukrytych

- Zacznij od **1–2 warstw ukrytych**.
- Głębsze sieci:
    - lepiej modelują złożone zależności,
    - lepiej generalizują.
- Zwiększaj liczbę warstw aż do pojawienia się **overfittingu**.
- W trudnych zadaniach używaj **głębokich sieci** oraz **modeli pretrained** z **uczeniem transferowym** (*transfer learning*).

### 4.7. Liczba neuronów na warstwę

- Często dobrze działa **podobna liczba neuronów w każdej warstwie** (sieć „kwadratowa”).
- Stopniowo zwiększaj liczbę neuronów.
- **Unikaj zbyt małych warstw** („bottleneck” — wąskie gardło).
- **Lepiej zwiększać liczbę warstw niż liczbę neuronów w jednej warstwie.**
- Overfitting kontroluj przez:
    - **early stopping**,
    - **regularyzację** (L1, L2, dropout).

### 4.8. Krok uczenia (*learning rate*)

- Wpływa na **zbieżność** i **szybkość treningu**.
- **Sprawdzona procedura strojenia learning rate:**
    1. Start od małego LR.
    2. Stopniowo zwiększamy (mnożymy przez stałą).
    3. Sprawdzamy, kiedy LF (loss function) zaczyna ponownie rosnąć.
    4. Interesuje nas punkt **tuż przed „odbiciem”**.
- Można też zastosować **zmienny LR** poprzez **harmonogramowanie kroku uczenia** (*learning rate scheduling*).

### 4.9. Rozmiar wsadu (*batch size*)

- Wpływa na **jakość modelu** i **szybkość treningu**.
- Większe rozmiary lepiej wykorzystują możliwości **GPU**.
- Teoretycznie wsad może być dowolnie duży, byle się zmieścił w **VRAM**, ale zdania w społeczności są podzielone.
- Yann LeCun (Twitter, 2018): *„Friends don't let friends use mini-batches larger than 32.”* — sugestia, że małe batche dają lepszą generalizację.

---

## 5. PyTorch — podstawy

### 5.1. Najpopularniejsze narzędzia do NN

- Przez lata najpopularniejszym środowiskiem był **TensorFlow** — stworzony i wykorzystywany przez Google.
- Prowadzenie stopniowo jednak przejął **PyTorch**, utworzony przez FAIR (obecnie **Meta AI**).
- Sam Google obniżył zainteresowanie TF i skupił się na bibliotece **JAX**, która jednak jest mniej popularna od PyTorch.
- Od **2022** PyTorch jest zarządzany przez **PyTorch Foundation**, część **Linux Foundation**.
- Podejście PyTorch jest postrzegane jako bardziej **Pythonic** (w duchu Pythona).

### 5.2. Podstawy PyTorch — tensory

Podstawową strukturą w PyTorch jest **tensor**. Przypomina `ndarray` z NumPy, ale:
- może żyć na **GPU**,
- wspiera **autoróżniczkowanie** (autograd).

```python
import torch
X = torch.tensor([[1.0, 4.0, 7.0], [2.0, 3.0, 6.0]])
X
# tensor([[1., 4., 7.],
#         [2., 3., 6.]])
```

Wszystkie operacje prowadzimy analogicznie jak na `ndarray`:

```python
X.shape       # torch.Size([2, 3])
X.dtype       # torch.float32  — domyślnie 32, nie 64 bity!
X[0, 1]       # tensor(4.)
X[:, 1]       # tensor([4., 3.])
```

### 5.3. Tensory PyTorch vs. NumPy

Możliwa jest konwersja do i ze struktur NumPy:

```python
import numpy as np
X.numpy()
# array([[1., 4., 7.],
#        [2., 3., 6.]], dtype=float32)

torch.tensor(np.array([[1., 4., 7.], [2., 3., 6.]]))
# tensor([[1., 4., 7.],
#         [2., 3., 6.]], dtype=torch.float64)
```

> **Uwaga:** domyślnym typem w PyTorch jest **`float32`**, a w NumPy — **`float64`**. Taka dokładność jest najczęściej zbędna przy pracy z sieciami neuronowymi — warto ręcznie zaspecyfikować `dtype=torch.float32` lub użyć `torch.FloatTensor()` zamiast `torch.tensor`:

```python
torch.tensor(
    np.array([[1., 4., 7.], [2., 3., 6.]]),
    dtype=torch.float32
).dtype
# torch.float32

torch.FloatTensor(
    np.array([[1., 4., 7.], [2., 3., 6.]])).dtype
# torch.float32
```

### 5.4. Operacje na tensorach

Operacje arytmetyczne, broadcasting, funkcje elementwise:

```python
10 * (X + 1.0)
# tensor([[20., 50., 80.],
#         [30., 40., 70.]])

X.exp()
# tensor([[   2.7183,   54.5982, 1096.6332],
#         [   7.3891,   20.0855,  403.4288]])
```

Mnożenie macierzy operatorem `@`:

```python
X @ X.T
# tensor([[66., 56.],
#         [56., 49.]])
```

Indeksowanie i przypisanie:

```python
X[:, 1] = -99
# tensor([[  1., -99.,   7.],
#         [  2., -99.,   6.]])
```

**Operacje "in-place"** — PyTorch dostarcza wersje "in-place" często stosowanych operacji (z podkreślnikiem na końcu), co **oszczędza pamięć i przyspiesza modele**:

```python
X.relu_()   # ReLU in-place — zeruje ujemne, zostawia dodatnie
# tensor([[1., 0., 7.],
#         [2., 0., 6.]])

X.zero_()   # wszystkie wartości = 0 in-place
# tensor([[0., 0., 0.],
#         [0., 0., 0.]])
```

### 5.5. Praca z GPU

PyTorch automatycznie rozpoznaje dostępne urządzenia i nadaje im nazwy `cpu`, `cuda`, `mps`, itd.

```python
if torch.cuda.is_available():
    device = "cuda"
elif torch.mps.is_available():
    device = "mps"
else:
    device = "cpu"
device
# 'mps'   # przykładowy wynik na Macu z Apple Silicon
```

Przeniesienie tensora na urządzenie:

```python
M = torch.tensor([[1., 2., 3.], [4., 5., 6.]])
M = M.to(device)
M.device
# device(type='mps', index=0)
```

**Porównanie wydajności CPU vs GPU:**

```python
M = torch.rand((1000, 1000))
print(M.device)             # cpu
%timeit -n3 M @ M.T
# 1.3 ms ± 484 μs per loop

M = torch.rand((1000, 1000), device=device)
%timeit -n3 M @ M.T
# znacznie szybciej (po rozgrzewce GPU)
```

> **Uwaga:** pierwsze uruchomienie na GPU jest często wolniejsze (inicjalizacja, kompilacja kerneli), dopiero kolejne wywołania pokazują prawdziwą przewagę GPU. Również operacje na małych tensorach mogą być wolniejsze na GPU z powodu narzutu transferu.

### 5.6. Autograd — różniczkowanie automatyczne

Jedną z najważniejszych cech PyTorch jest implementacja algorytmu **różniczkowania automatycznego metodą "wstecz"** (reverse-mode autodiff).

**Przykład z podręcznika** — funkcja $f(x) = x^2$, której pochodna to $f'(x) = 2x$. Obliczmy $f(5)$ oraz $f'(5)$ — spodziewamy się wyników odpowiednio **25** i **10**:

```python
x = torch.tensor(5.0, requires_grad=True)
f = x ** 2
f
# tensor(25., grad_fn=<PowBackward0>)
```

```python
f.backward()
x.grad
# tensor(10.)
```

**Wyjaśnienie:**
- Ustawienie argumentu `requires_grad=True` sprawia, że PyTorch **śledzi wszystkie operacje** dotyczące `x`, które w naszym grafie obliczeniowym jest **liściem** (*leaf node*).
- Obliczamy `f`, co daje nam tensor równy 25.0, posiadający też **atrybut `grad_fn`** śledzący operację, która go utworzyła — w ten sposób PyTorch **buduje graf obliczeniowy** (dynamiczny, *define-by-run*).
- Uruchomienie `f.backward()` **propaguje gradienty wstecz** przez graf obliczeniowy, począwszy od `f`, aż do liści — wynikiem jest pochodna $\partial f / \partial x$ zapisana w `x.grad`.

### 5.7. Użycie do *gradient descent*

Przy pracy z sieciami neuronowymi różniczkowanie wykonujemy najczęściej na potrzeby algorytmu gradientowego. Jednak **nie chcemy tego kroku zapisywać w grafie obliczeniowym** (bo to nie jest „prawdziwa” operacja w obliczeniach, a sama optymalizacja). Dlatego krok ten wykonujemy w kontekście `no_grad()`:

```python
learning_rate = 0.1
with torch.no_grad():
    x -= learning_rate * x.grad
x
# tensor(4., requires_grad=True)
```

> Wyjaśnienie: zaczęliśmy od $x = 5$, gradient był $10$, więc nowe $x = 5 - 0{,}1 \cdot 10 = 4$.

Można też utworzyć tensor **„oderwany” od grafu obliczeniowego** za pomocą `detach()`:

```python
x_detached = x.detach()
x_detached -= learning_rate * x.grad
x
# tensor(3., requires_grad=True)
```

> `detach()` zwraca tensor współdzielący dane z oryginałem, ale bez śledzenia operacji (`requires_grad=False`). Modyfikacja `x_detached` zmienia także `x`.

### 5.8. Pierwsza pętla treningu

Pełna pętla minimalizująca $f(x) = x^2$ za pomocą gradient descent:

```python
learning_rate = 0.1
x = torch.tensor(5.0, requires_grad=True)
for iteration in range(100):
    f = x ** 2                          # krok „w przód” (forward)
    f.backward()                        # krok „wstecz”  (backward)
    with torch.no_grad():
        x -= learning_rate * x.grad     # krok algorytmu gradientowego
    x.grad.zero_()                      # resetujemy gradienty
x
# tensor(1.0185e-09, requires_grad=True)
```

> Po 100 iteracjach $x$ jest bardzo bliskie zera — globalnego minimum funkcji $x^2$.

> **WAŻNE:** Warto pamiętać o wykonaniu operacji „in-place” **`zero_()`** na końcu — **jej brak to częsty błąd przy pracy z PyTorch**. PyTorch domyślnie **akumuluje gradienty** (dodaje do `.grad`) zamiast je nadpisywać, więc bez `zero_()` gradienty z poprzednich iteracji by się sumowały i pętla nie zbiegłaby się poprawnie.

---

## 6. Dodatek — teoria spoza prezentacji przydatna na egzamin

### 6.1. Funkcje aktywacji — przegląd

| Nazwa | Wzór | Zakres | Uwagi |
|---|---|---|---|
| **Sigmoid (logistyczna)** | $\sigma(z) = \frac{1}{1 + e^{-z}}$ | (0, 1) | Saturuje dla dużych \|z\|, problem zanikającego gradientu |
| **Tanh** | $\tanh(z) = \frac{e^z - e^{-z}}{e^z + e^{-z}}$ | (-1, 1) | Wycentrowana wokół zera (lepsza niż sigmoid) |
| **ReLU** | $\max(0, z)$ | [0, ∞) | Najpopularniejsza, szybka; problem „dying ReLU” |
| **Leaky ReLU** | $\max(\alpha z, z),\ \alpha \approx 0{,}01$ | (-∞, ∞) | Rozwiązuje problem dying ReLU |
| **ELU** | $z$ dla $z \geq 0$; $\alpha(e^z - 1)$ dla $z < 0$ | (-α, ∞) | Gładka, ujemne wartości |
| **Softplus** | $\log(1 + e^z)$ | (0, ∞) | Gładki wariant ReLU |
| **Softmax** | $\frac{e^{z_i}}{\sum_j e^{z_j}}$ | (0, 1), suma = 1 | Klasyfikacja wieloklasowa |

### 6.2. Najważniejsze funkcje straty

**Regresja:**
- **MSE** (*Mean Squared Error*): $L = \frac{1}{n}\sum (y_i - \hat{y}_i)^2$ — kara kwadratowa, wrażliwa na outliery.
- **MAE** (*Mean Absolute Error*): $L = \frac{1}{n}\sum |y_i - \hat{y}_i|$ — odporna na outliery, ale nieróżniczkowalna w zerze.
- **Strata Hubera** — kompromis: kwadratowa dla małych błędów, liniowa dla dużych.

**Klasyfikacja:**
- **Binary cross-entropy (log loss)**:

$$
L = -\frac{1}{n}\sum_{i} \big[y_i \log \hat{y}_i + (1 - y_i)\log(1 - \hat{y}_i)\big]
$$

- **Categorical cross-entropy** (klasyfikacja wieloklasowa):

$$
L = -\sum_{i} \sum_{k} y_{i,k} \log \hat{y}_{i,k}
$$

### 6.3. Wyprowadzenie backpropagation — reguła łańcuchowa

Dla sieci z funkcją straty $L$, wagą $w$ wpływającą na wyjście warstwy $z$, które przechodzi przez aktywację $a = \phi(z)$:

$$
\frac{\partial L}{\partial w} = \frac{\partial L}{\partial a} \cdot \frac{\partial a}{\partial z} \cdot \frac{\partial z}{\partial w}
$$

To jest istota propagacji wstecznej — **rekurencyjne stosowanie reguły łańcuchowej** od końca sieci do początku, z **ponownym wykorzystaniem** częściowych pochodnych w grafie.

### 6.4. Warianty algorytmu gradientowego

- **Batch Gradient Descent (BGD)** — gradient na całym zbiorze treningowym; dokładny, ale wolny.
- **Stochastic Gradient Descent (SGD)** — gradient na pojedynczej próbce; szybki, ale szumny.
- **Mini-batch Gradient Descent** — kompromis; standard w deep learning (batch size 32–512).

**Optymalizatory rozszerzające SGD:**

| Optymalizator | Idea |
|---|---|
| **Momentum** | Dodaje „pęd” — średnia ważona poprzednich gradientów, przyspiesza w spójnych kierunkach. |
| **Nesterov Accelerated Gradient (NAG)** | Momentum z „spoglądaniem w przyszłość”. |
| **AdaGrad** | Dostosowuje LR dla każdego parametru; LR maleje za szybko. |
| **RMSProp** | Adaptacyjny LR z zanikającą średnią kwadratów gradientów. |
| **Adam** | Połączenie momentum + RMSProp; **najpopularniejszy** w praktyce. |
| **AdamW** | Adam z poprawioną regularyzacją (weight decay). |

### 6.5. Problemy z trenowaniem głębokich sieci

- **Zanikający gradient** (*vanishing gradient*) — gradienty w pierwszych warstwach są bardzo małe, sieć słabo się uczy. Sigmoid i tanh saturują → ReLU lepsze.
- **Eksplodujący gradient** (*exploding gradient*) — gradienty bardzo duże; ratunek to **gradient clipping**.
- **Inicjalizacja wag** — losowa, ale z odpowiednią wariancją:
    - **Xavier/Glorot** — dla sigmoid/tanh,
    - **He** — dla ReLU.
- **Batch normalization** — normalizacja wyjść warstwy w obrębie mini-batcha; stabilizuje trening, pozwala na wyższy LR.

### 6.6. Regularyzacja

- **L1** (lasso) — dodaje $\lambda \sum |w_i|$ do straty; prowadzi do rzadkich wag.
- **L2** (ridge / weight decay) — dodaje $\lambda \sum w_i^2$; zmniejsza wagi, najpopularniejsza.
- **Dropout** — losowe „wyłączanie” neuronów podczas treningu (typowo 0,2–0,5); zapobiega współadaptacji.
- **Early stopping** — przerwanie treningu, gdy strata walidacyjna przestaje się poprawiać.
- **Data augmentation** — sztuczne powiększanie zbioru (obroty, przesunięcia obrazów itp.).

### 6.7. Pełny szablon pętli treningowej w PyTorch

W praktyce zamiast ręcznego aktualizowania wag używamy **`torch.nn`** i **`torch.optim`**:

```python
import torch
import torch.nn as nn
import torch.optim as optim

# Model
model = nn.Sequential(
    nn.Linear(in_features=10, out_features=64),
    nn.ReLU(),
    nn.Linear(64, 32),
    nn.ReLU(),
    nn.Linear(32, 1)
)

# Funkcja straty i optymalizator
criterion = nn.MSELoss()
optimizer = optim.Adam(model.parameters(), lr=1e-3)

# Pętla treningowa
for epoch in range(num_epochs):
    for X_batch, y_batch in dataloader:
        optimizer.zero_grad()           # 1. wyzeruj gradienty
        y_pred = model(X_batch)         # 2. forward
        loss = criterion(y_pred, y_batch)  # 3. oblicz stratę
        loss.backward()                 # 4. backward (autograd)
        optimizer.step()                # 5. krok optymalizatora
```

**Pięć kroków pętli treningowej** (do zapamiętania na egzamin):
1. `optimizer.zero_grad()` — wyzerowanie gradientów.
2. `y_pred = model(X)` — *forward pass*.
3. `loss = criterion(y_pred, y)` — obliczenie straty.
4. `loss.backward()` — *backward pass*, obliczenie gradientów.
5. `optimizer.step()` — aktualizacja wag.

### 6.8. Tryb treningu vs. ewaluacji

- `model.train()` — włącza dropout, batch norm w trybie treningowym.
- `model.eval()` — wyłącza dropout, batch norm używa statystyk z treningu.
- `with torch.no_grad():` — wyłącza śledzenie gradientów (oszczędność pamięci podczas inferencji).

### 6.9. Najważniejsze daty — kalendarium

| Rok | Wydarzenie |
|---|---|
| **1943** | McCulloch & Pitts — pierwszy model neuronu sztucznego |
| **1949** | Donald Hebb — zasada Hebba ("cells that fire together, wire together") |
| **1957** | Frank Rosenblatt — perceptron |
| **1969** | Minsky & Papert — *Perceptrons* — wykazanie ograniczeń (XOR) |
| **1970** | Seppo Linnainmaa — reverse-mode autodiff |
| **1986** | Rumelhart, Hinton, Williams — popularyzacja backpropagation |
| **2012** | AlexNet — zwycięstwo w ImageNet, początek ery deep learning |
| **2017** | Transformer ("Attention is all you need") |
| **2022** | PyTorch trafia pod PyTorch Foundation (Linux Foundation) |

---

## Pytania kontrolne (możliwe na egzaminie)

1. Czym różni się perceptron od neuronu McCullocha-Pittsa?
2. Dlaczego pojedynczy perceptron nie rozwiązuje problemu XOR?
3. Wyprowadź wzór na wyjście warstwy gęstej.
4. Czym jest reguła Hebba i jak wiąże się z regułą uczenia perceptronu?
5. Wyjaśnij, czym jest reverse-mode autodiff i dlaczego jest efektywny.
6. Jakie funkcje aktywacji stosuje się w warstwie wyjściowej dla:
   (a) regresji, (b) klasyfikacji binarnej, (c) klasyfikacji wieloklasowej, (d) klasyfikacji wieloetykietowej?
7. Jakie są funkcje straty odpowiednie dla każdego z powyższych zadań?
8. Wyjaśnij, dlaczego w PyTorch musimy wywołać `optimizer.zero_grad()` (lub `x.grad.zero_()`) na początku każdej iteracji.
9. Co robi `requires_grad=True`? Co to jest „liść” grafu obliczeniowego?
10. Jaka jest różnica między `torch.no_grad()` a `tensor.detach()`?
11. Dlaczego domyślny `dtype` w PyTorch to `float32`, a nie `float64`?
12. Opisz pełną pętlę treningową w PyTorch (5 kroków).
13. Jakie są strategie kontroli overfittingu?
14. Czym jest problem zanikającego gradientu i jak się go rozwiązuje?
15. Porównaj BGD, SGD i mini-batch GD.

---

## Wykład 10 - Uczenie maszynowe

## Spis treści

1. [Pierwsze modele w PyTorch](#1-pierwsze-modele-w-pytorch)
2. [Bardziej złożone modele](#2-bardziej-złożone-modele)
3. [Ocena wytrenowanego modelu](#3-ocena-wytrenowanego-modelu)
4. [Analiza obrazów w PyTorch](#4-analiza-obrazów-w-pytorch)
5. [Tuning hiperparametrów](#5-tuning-hiperparametrów)
6. [Zapisywanie, wczytywanie i optymalizacja modeli](#6-zapisywanie-wczytywanie-i-optymalizacja-modeli)
7. [Optymalizacja procesu uczenia](#7-optymalizacja-procesu-uczenia)
8. [Szybsze algorytmy optymalizacji](#8-szybsze-algorytmy-optymalizacji-pęd-adagrad-rmsprop-adam)
9. [Harmonogramowanie kroku uczenia](#9-harmonogramowanie-kroku-uczenia-learning-rate-scheduling)
10. [Dodatek — teoria spoza prezentacji](#10-dodatek--teoria-spoza-prezentacji)

---

## 0. Wybór urządzenia obliczeniowego

```python
import torch
if torch.cuda.is_available():
    device = "cuda"
elif torch.mps.is_available():
    device = "mps"
else:
    device = "cpu"
device
# 'mps'  # przykładowy wynik na Mac z Apple Silicon
```

Standardowy szablon na początku każdego notebooka — wybór najlepszego dostępnego urządzenia.

---

## 1. Pierwsze modele w PyTorch

### 1.1. Regresja liniowa w PyTorch (niskopoziomowo)

Przygotowanie zbioru **California housing** z scikit-learn do postaci zrozumiałej dla PyTorch:

```python
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split

housing = fetch_california_housing()
X_train_full, X_test, y_train_full, y_test = train_test_split(
    housing.data, housing.target, random_state=42)
X_train, X_valid, y_train, y_valid = train_test_split(
    X_train_full, y_train_full, random_state=42)

print(type(X_train), X_train.shape)
# <class 'numpy.ndarray'> (11610, 8)
```

**Konwersja do tensorów + standaryzacja** (z-score) — bardzo ważne dla sieci neuronowych:

```python
X_train = torch.FloatTensor(X_train)
X_valid = torch.FloatTensor(X_valid)
X_test  = torch.FloatTensor(X_test)
means = X_train.mean(dim=0, keepdims=True)
stds  = X_train.std (dim=0, keepdims=True)
X_train = (X_train - means) / stds
X_valid = (X_valid - means) / stds
X_test  = (X_test  - means) / stds
```

> **Uwaga:** średnią i odchylenie liczymy **tylko ze zbioru treningowego**, a następnie stosujemy do walidacyjnego i testowego — żeby nie wyciekły informacje z testów.

### 1.2. Ważne: postać wektorów cech

```python
y_train
# array([1.442, 1.687, 1.621, ..., 0.68, 0.613, 1.97], shape=(11610,))
```

Etykiety są wektorem 1D. Przekształcamy je w **macierze o jednej kolumnie** (kształt $n \times 1$) — bo PyTorch oczekuje takiego formatu wyjść z `nn.Linear`:

```python
y_train = torch.FloatTensor(y_train).view(-1, 1)
y_valid = torch.FloatTensor(y_valid).view(-1, 1)
y_test  = torch.FloatTensor(y_test).view(-1, 1)
# tensor([[1.4420],
#         [1.6870],
#         ...])
```

> `view(-1, 1)` oznacza: „liczba wierszy dobierana automatycznie, jedna kolumna”.

### 1.3. Budujemy sieć (ręcznie)

Sieć ma jedną warstwę wejściową (z liczbą neuronów równą liczbie cech) oraz **jeden neuron wyjściowy** (bo mamy jedną etykietę):

```python
torch.manual_seed(42)
n_features = X_train.shape[1]   # 8 cech wejściowych
w = torch.randn((n_features, 1), requires_grad=True)
b = torch.tensor(0., requires_grad=True)
```

To w istocie ręczna implementacja $\hat{y} = X w + b$ z parametrami śledzonymi przez autograd.

### 1.4. Pętla treningowa

**Kroki pętli:**
1. Ustalamy parametr kroku uczenia — jest to **hiperparametr** modelu.
2. Wykonujemy zadaną liczbę epok uczenia.
3. **Krok „w przód”**: wyliczamy predykcje `y_pred` oraz wartość straty (MSE).
4. **Krok „wstecz”**: obliczamy gradienty funkcji straty względem każdego parametru.
5. Wykonujemy krok algorytmu gradientowego (w kontekście `no_grad()`).
6. **Resetujemy gradienty** i wypisujemy aktualny stan.

```python
learning_rate = 0.4
n_epochs = 20
for epoch in range(n_epochs):
    y_pred = X_train @ w + b                       # forward
    loss = ((y_pred - y_train) ** 2).mean()        # MSE
    loss.backward()                                # backward
    with torch.no_grad():
        b -= learning_rate * b.grad
        w -= learning_rate * w.grad
        b.grad.zero_()
        w.grad.zero_()
    print(f"Epoch {epoch + 1}/{n_epochs}, Loss: {loss.item()}")
# Epoch 1/20, Loss: 16.158
# ...
# Epoch 20/20, Loss: 0.568
```

### 1.5. Wykonanie predykcji

Na tym „niskim” poziomie nie mamy funkcji `predict`, ale wykorzystujemy wektory wag i bias bezpośrednio:

```python
y_pred = X_test[:3] @ w + b
```

### 1.6. To samo, ale przy pomocy wysokopoziomowego API

PyTorch udostępnia klasę **`torch.nn.Linear`**, która robi dokładnie to o co nam chodzi:

```python
import torch.nn as nn
torch.manual_seed(42)
model = nn.Linear(in_features=n_features, out_features=1)
model
# Linear(in_features=8, out_features=1, bias=True)

model.bias
# Parameter containing:
# tensor([0.3117], requires_grad=True)

model.weight
# Parameter containing:
# tensor([[ 0.2703,  0.2935, ...]], requires_grad=True)
```

> Klasa **`torch.nn.Parameter`** jest podklasą `torch.Tensor`, a więc jej instancji możemy używać jak zwykłych tensorów — różnica jest taka, że są automatycznie rejestrowane jako parametry modelu (`model.parameters()`).

### 1.7. Praca z modelem

Aby wykonać predykcję, model wywołujemy **jako funkcję**:

```python
model(X_train[:3])
# tensor([[-0.4718],
#         [ 0.1131],
#         [-0.1084]], grad_fn=<AddmmBackward0>)
```

### 1.8. Pętla treningowa w wysokopoziomowym API

Aby przeprowadzić trening w API wysokopoziomowym, trzeba wybrać:
- **optymalizator** (algorytm gradientowy),
- **funkcję straty** (kryterium / *criterion*).

> **Termin `criterion`** zwyczajowo odnosi się w PyTorch do funkcji straty.
>
> Dzięki temu, że do wykonania kroku gradientowego i resetowania używamy funkcji optymalizatora, **nie trzeba robić tego w kontekście `no_grad()`** — optymalizator robi to za nas.

```python
def train_bgd(model, optimizer, criterion, X, y, n_epochs):
    for epoch in range(n_epochs):
        y_pred = model(X)                  # forward
        loss = criterion(y_pred, y)        # strata
        optimizer.zero_grad()              # wyzeruj gradienty
        loss.backward()                    # backward
        optimizer.step()                   # aktualizuj wagi
        print(f"Epoch {epoch + 1}/{n_epochs}, Loss: {loss.item()}")
```

### 1.9. Trenujemy!

```python
learning_rate = 0.1
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
mse = nn.MSELoss()
train_bgd(model, optimizer, mse, X_train, y_train, n_epochs)
```

**Wykonanie predykcji** w trybie inferencji:

```python
X_new = X_test[:3]
with torch.no_grad():
    y_pred = model(X_new)
```

> `with torch.no_grad():` przy predykcji wyłącza śledzenie gradientów → mniejsze zużycie pamięci, szybciej.

---

## 2. Bardziej złożone modele

### 2.1. MLP w PyTorch — `nn.Sequential`

Moduł **`nn.Sequential`** świetnie nadaje się do sieci o **prostej strukturze** (bez obejść — *skip connections*):

```python
model = nn.Sequential(
    nn.Linear(n_features, 50), nn.ReLU(),
    nn.Linear(50, 40),         nn.ReLU(),
    nn.Linear(40, 30),         nn.ReLU(),
    nn.Linear(30, 1)
)
```

Konwencja: po każdej warstwie liniowej dajemy funkcję aktywacji, ostatnia warstwa (regresja) bez aktywacji.

### 2.2. DataLoaders — przejście do mini-batchy

**Problem:** ręczna pętla wylicza gradienty zawsze dla **całego zbioru uczącego** (Batch Gradient Descent — BGD).

**Rozwiązanie:** wykorzystać klasę `TensorDataset` do opakowania zbioru i utworzyć `DataLoader`, który steruje procesem „karmienia” sieci. Przechodzimy z BGD na **mini-batch GD**:

```python
from torch.utils.data import TensorDataset, DataLoader

train_dataset = TensorDataset(X_train, y_train)
train_loader  = DataLoader(train_dataset, batch_size=32, shuffle=True)
```

### 2.3. Zmodyfikowana pętla uczenia

```python
def train(model, optimizer, criterion, train_loader, n_epochs):
    for epoch in range(n_epochs):
        total_loss = 0.0
        for X_batch, y_batch in train_loader:
            X_batch = X_batch.to(device)
            y_batch = y_batch.to(device)
            y_pred = model(X_batch)
            loss = criterion(y_pred, y_batch)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            total_loss += loss.item()
        avg_loss = total_loss / len(train_loader)
        print(f"Epoch {epoch + 1}/{n_epochs}, Loss: {avg_loss:.4f}")
```

### 2.4. Trening z DataLoaders

```python
train(model, optimizer, mse, train_loader, n_epochs)
# Epoch 1/20, Loss: 0.5900
# Epoch 2/20, Loss: 0.4046
# ...
```

**Dlaczego tak wolno?** Bo każdy batch musi być **skopiowany do pamięci GPU**.

**Jak temu zaradzić?**
- **Trening na CPU 😑** — może być szybszy dla małych modeli, gdy narzut transferu dominuje.
- Przy GPU posiadających **odrębną pamięć** może pomóc:
    - **`pin_memory=True`** — przypięcie danych w **page-locked memory** (pamięci nieprzełączanej),
    - **`non_blocking=True`** przy `.to(device)` — wtedy dane mogą być kopiowane przy pomocy **DMA** (Direct Memory Access).
- **Prefetching danych** — ustawienie liczby procesów (`num_workers`) lub liczby buforowanych wsadów (`prefetch_factor`).

```python
train_loader = DataLoader(
    train_dataset,
    batch_size=32, shuffle=True,
    num_workers=4, pin_memory=True, prefetch_factor=2)

# i w pętli:
X_batch = X_batch.to(device, non_blocking=True)
```

### 2.5. A jak to robimy w TensorFlow?

- TensorFlow wykorzystuje do modelowania sieci interfejs **Keras** — w wersji 2.x była ona kompatybilna tylko z TF, obecnie obsługuje też PyTorch i JAX.
- Dwa najpopularniejsze interfejsy Keras to **Sequential** oraz **Functional** — odpowiedniki dwóch podejść PyTorch.
- Funkcje aktywacji podajemy bezpośrednio w warstwach.
- Nie musimy pisać funkcji treningowej — Keras dostarcza `fit()`.

```python
import tensorflow as tf
tf_model = tf.keras.Sequential([
    tf.keras.layers.Input(shape=[28, 28]),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(300, activation="relu"),
    tf.keras.layers.Dense(100, activation="relu"),
    tf.keras.layers.Dense(10,  activation="softmax")
])
optimizer = tf.keras.optimizers.Adam(learning_rate=1e-3)
tf_model.compile(
    loss="sparse_categorical_crossentropy",
    optimizer=optimizer,
    metrics=["accuracy"])
# tf_model.fit(X_train, y_train, epochs=20,
#              validation_data=(X_valid, y_valid))
```

### 2.6. Modele inne niż sekwencyjne — `nn.Module`

Czasami potrzebujemy modeli o bardziej złożonej strukturze — **np. z obejściami**, takich jak sieć **Wide & Deep**. Tworzymy je przy pomocy klasy **`torch.nn.Module`**:

```python
class WideAndDeep(nn.Module):
    def __init__(self, n_features):
        super().__init__()
        self.hidden1 = nn.Linear(n_features, 30)
        self.hidden2 = nn.Linear(30, 30)
        self.output  = nn.Linear(n_features + 30, 1)
        self.relu    = nn.ReLU()

    def forward(self, x):
        h1 = self.relu(self.hidden1(x))
        h2 = self.relu(self.hidden2(h1))
        concat = torch.cat([x, h2], dim=1)    # OBEJŚCIE (skip connection)
        return self.output(concat)
```

> Idea **Wide & Deep**: część „wide” uczy się prostych zależności bezpośrednich (memorization), część „deep” uczy się zależności złożonych (generalization). Wejście trafia jednocześnie do obu części.

### 2.7. Wide & Deep w TensorFlow/Keras

W Kerasie używamy **Functional API** — definiujemy warstwy jako funkcje i łączymy je grafem.

### 2.8. Rozdzielenie cech na ścieżki

Wracając do PyTorch — jeżeli chcemy wydzielić z wejścia cechy „podążające” obejściem i te przechodzące przez część głęboką, możemy zrobić to **w samej funkcji `forward()`**:

```python
def forward(self, x):
    x_wide = x[:, :5]   # pierwsze 5 cech do części wide
    x_deep = x[:, 2:]   # cechy od 2 do końca do części deep
    h = self.relu(self.hidden1(x_deep))
    h = self.relu(self.hidden2(h))
    return self.output(torch.cat([x_wide, h], dim=1))
```

### 2.9. Model z wieloma wejściami

W `forward(self, x_wide, x_deep)` przyjmujemy **wiele argumentów** i wywołujemy model: `model(x_wide_batch, x_deep_batch)`.

### 2.10. Model z wieloma wyjściami

W `forward()` **zwracamy krotkę** wyjść, np. `return out1, out2`. Funkcję straty obliczamy dla każdego wyjścia osobno i sumujemy z wagami:

```python
loss = w1 * criterion1(y_pred1, y1) + w2 * criterion2(y_pred2, y2)
```

---

## 3. Ocena wytrenowanego modelu

### 3.1. Prosta funkcja oceniająca

```python
def evaluate(model, loader, metric_fn, aggregate_fn=torch.mean):
    model.eval()
    metrics = []
    with torch.no_grad():
        for X_batch, y_batch in loader:
            X_batch = X_batch.to(device)
            y_batch = y_batch.to(device)
            y_pred = model(X_batch)
            metrics.append(metric_fn(y_pred, y_batch))
    return aggregate_fn(torch.stack(metrics))
```

Przygotowanie loadera walidacyjnego i ocena modelu:

```python
valid_dataset = TensorDataset(X_valid, y_valid)
valid_loader  = DataLoader(valid_dataset, batch_size=32)
valid_mse = evaluate(model, valid_loader, mse)
valid_mse  # tensor(0.4205)
```

### 3.2. Inne metryki

PyTorch nie ma wbudowanej funkcji wyliczającej **RMSE**, więc piszemy własną:

```python
def rmse(y_pred, y_true):
    return ((y_pred - y_true) ** 2).mean().sqrt()

evaluate(model, valid_loader, rmse)
# tensor(0.5696)
```

> **Pułapka uśredniania!** Wynik `0.5696` różni się od `valid_mse.sqrt() = 0.6485` — bo `mean(sqrt(...)) ≠ sqrt(mean(...))`. Funkcja `evaluate` domyślnie **uśrednia metryki po batchach**, więc dla RMSE liczy *średnią z RMSE po batchach*, a nie RMSE z całości.

Lepiej zmienić sposób agregacji:

```python
evaluate(model, valid_loader, mse,
         aggregate_fn=lambda metrics: torch.sqrt(torch.mean(metrics)))
# tensor(0.6485)  <- to samo co valid_mse.sqrt()
```

### 3.3. Biblioteka TorchMetrics

Specjalistyczna biblioteka — sama dba o poprawne agregowanie metryk:

```python
import torchmetrics
rmse = torchmetrics.MeanSquaredError(squared=False).to(device)
evaluate_tm(model, valid_loader, rmse)
# tensor(0.6485)
```

### 3.4. Pętla treningowa z walidacją oraz rejestrowaniem historii

```python
def train2(model, optimizer, criterion, metric, train_loader, valid_loader, n_epochs):
    history = {"train_losses": [], "train_metrics": [], "valid_metrics": []}
    for epoch in range(n_epochs):
        # --- trening ---
        model.train()
        train_loss = 0.0
        train_metric_acc = []
        for X_batch, y_batch in train_loader:
            X_batch = X_batch.to(device); y_batch = y_batch.to(device)
            y_pred = model(X_batch)
            loss = criterion(y_pred, y_batch)
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
            train_loss += loss.item()
            train_metric_acc.append(metric(y_pred, y_batch).detach())
        train_loss /= len(train_loader)
        train_metric = torch.stack(train_metric_acc).mean().item()
        # --- walidacja ---
        valid_metric = evaluate(model, valid_loader, metric).item()
        history["train_losses"].append(train_loss)
        history["train_metrics"].append(train_metric)
        history["valid_metrics"].append(valid_metric)
        print(f"Epoch {epoch+1}/{n_epochs}, train loss: {train_loss:.4f}, "
              f"train metric: {train_metric:.4f}, valid metric: {valid_metric:.4f}")
    return history
```

### 3.5. Trening przy pomocy nowej pętli

```python
torch.manual_seed(42)
learning_rate = 0.01
model = nn.Sequential(
    nn.Linear(n_features, 50), nn.ReLU(),
    nn.Linear(50, 40), nn.ReLU(),
    nn.Linear(40, 30), nn.ReLU(),
    nn.Linear(30, 1)
)
model = model.to(device)
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate, momentum=0)
mse = nn.MSELoss()
rmse = torchmetrics.MeanSquaredError(squared=False).to(device)
history = train2(model, optimizer, mse, rmse, train_loader, valid_loader, n_epochs)
```

### 3.6. Wizualizacja procesu uczenia — krzywe uczenia

```python
import matplotlib.pyplot as plt
import numpy as np

plt.plot(np.arange(n_epochs) + 0.5, history["train_metrics"], ".--", label="Training")
plt.plot(np.arange(n_epochs) + 1.0, history["valid_metrics"], ".-",  label="Validation")
plt.xlabel("Epoch"); plt.ylabel("RMSE")
plt.grid(); plt.title("Learning curves")
plt.axis([0.5, 20, 0.4, 1.0])
plt.legend(); plt.show()
```

> **Interpretacja krzywych:**
> - obie krzywe maleją → uczenie zdrowe,
> - train spada, valid rośnie → **overfitting**,
> - obie zatrzymują się wysoko → **underfitting** lub za mały LR.

---

## 4. Analiza obrazów w PyTorch

### 4.1. Biblioteka TorchVision

`torchvision` zawiera zbiory danych, modele i transformacje obrazów:

```python
from torchvision import datasets
from torchvision.transforms import ToTensor
from torch.utils.data import random_split

# ToTensor: obrazy PIL (0–255) -> tensory (0.0–1.0)
train_and_valid_data = datasets.FashionMNIST(
    root="data", train=True, download=True, transform=ToTensor())
test_data = datasets.FashionMNIST(
    root="data", train=False, download=True, transform=ToTensor())

# dzielimy zbiór "train" w proporcjach 55 : 5 (k tys.)
train_data, valid_data = random_split(train_and_valid_data, [55_000, 5_000])
```

> Obiekt **`ToTensor`** konwertuje obrazy PIL (0–255) na tensory (0.0–1.0).
> Następnie ładujemy zbiór `train` jako dane treningowe i walidacyjne oraz zbiór `test`.
> W końcu dzielimy zbiór `train` w proporcjach **55 : 5** (tys. próbek).

### 4.2. DataLoadery

```python
train_loader = DataLoader(train_data, batch_size=32, shuffle=True)
valid_loader = DataLoader(valid_data, batch_size=32)
test_loader  = DataLoader(test_data,  batch_size=32)
```

Parametry przykładowego obrazka:

```python
X_sample, y_sample = train_data[0]
X_sample.shape     # torch.Size([1, 28, 28])  -> 1 kanał, 28x28 px
X_sample.dtype     # torch.float32
train_and_valid_data.classes[y_sample]   # 'Ankle boot'
```

### 4.3. Model klasyfikatora

> Modeli sekwencyjnych nie musimy opakowywać `nn.Module`, ale **ułatwia to później m.in. strojenie hiperparametrów**.

```python
class ImageClassifier(nn.Module):
    def __init__(self, n_inputs, n_hidden1, n_hidden2, n_classes):
        super().__init__()
        self.mlp = nn.Sequential(
            nn.Flatten(),
            nn.Linear(n_inputs, n_hidden1),  nn.ReLU(),
            nn.Linear(n_hidden1, n_hidden2), nn.ReLU(),
            nn.Linear(n_hidden2, n_classes)
        )
    def forward(self, x):
        return self.mlp(x)

model = ImageClassifier(n_inputs=1*28*28, n_hidden1=300, n_hidden2=100, n_classes=10)
model = model.to(device)
```

**Kluczowe obserwacje:**
- **`nn.Flatten`** spłaszcza obrazy `[1, 28, 28]` do wektorów `[784]`.
- **Gdzie jest softmax?** Nie ma! **`nn.CrossEntropyLoss`** oblicza funkcję straty entropii krzyżowej **bezpośrednio z logitów** (czyli wyników punktowych klas), a nie z prawdopodobieństw — **mniej obliczeń**!
- Ale to oznacza, że aby otrzymać **prawdopodobieństwa**, musimy sami wywołać funkcję `softmax` w odpowiednim momencie.
- Jeżeli zbiór jest **niezbalansowany** (liczba instancji dla różnych klas jest różna), możemy podać **wektor wag** (sumujących się do 1) jako argument `weight` obiektu `nn.CrossEntropyLoss`.

### 4.4. Trenujemy

```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.01)
xentropy  = nn.CrossEntropyLoss()
accuracy  = torchmetrics.Accuracy(task="multiclass", num_classes=10).to(device)

_ = train2(model, optimizer, xentropy, accuracy,
           train_loader, valid_loader, n_epochs)
# Epoch 1/20, train loss: 0.6061, train metric: 0.7814, valid metric: 0.8408
# ...
# Epoch 20/20, train loss: 0.1888, train metric: 0.9277, valid metric: 0.8812
```

### 4.5. Wykonujemy predykcję

```python
model.eval()
X_new, y_new = next(iter(valid_loader))
X_new = X_new[:3].to(device)
with torch.no_grad():
    y_pred_logits = model(X_new)
y_pred = y_pred_logits.argmax(dim=1)
y_pred
# tensor([7, 4, 2])

[train_and_valid_data.classes[index] for index in y_pred]
# ['Sneaker', 'Coat', 'Pullover']
```

> `argmax(dim=1)` wybiera **indeks klasy o najwyższym logicie** dla każdej próbki w batchu.

### 4.6. Obliczenie prawdopodobieństw

Surowe **logity** mogą być duże i ujemne — nie są prawdopodobieństwami:

```python
y_pred_logits
# tensor([[ -4.27, -0.61, -4.30, -1.56, -2.73, 2.29, -2.92, 10.61, -2.47, 6.52],
#         [ -0.68, -2.20, 11.26, -1.00, 16.91, -13.13, 8.29, -11.96, -1.07, -8.10],
#         ...])
```

Konwersja przez **softmax**:

```python
import torch.nn.functional as F
y_proba = F.softmax(y_pred_logits, dim=1)
y_proba.round(decimals=3)
# tensor([[0.000, 0.000, 0.000, 0.000, 0.000, 0.000, 0.000, 0.983, 0.000, 0.016],
#         [0.000, 0.000, 0.004, 0.000, 0.996, 0.000, 0.000, 0.000, 0.000, 0.000],
#         ...])
```

> Model jest bardzo pewny swoich pierwszych dwóch predykcji (98% i 99%), niepewny trzeciej (62% Pullover vs. 22% Coat).

---

## 5. Tuning hiperparametrów

### 5.1. Optuna

- Istnieje wiele narzędzi — znamy już te ze scikit-learn (GridSearchCV, RandomizedSearchCV).
- W PyTorch najczęściej używamy **Optuna**.
- Definiujemy funkcję `objective(trial)`, przyjmującą obiekt klasy `Trial`.
- Z tego obiektu pobieramy wartości hiperparametrów dla próby przy pomocy metod:
    - `suggest_float()`,
    - `suggest_int()`,
    - `suggest_categorical()`.
- Procesem steruje obiekt klasy `Study()`, który próbkuje wartości hiperparametrów przy pomocy **samplera**, np. domyślnego **`TPESampler`** (*Tree-structured Parzen Estimator*).

```python
import optuna

def objective(trial):
    learning_rate = trial.suggest_float("learning_rate", 1e-5, 1e-1, log=True)
    n_hidden = trial.suggest_int("n_hidden", 50, 300)

    model = nn.Sequential(
        nn.Flatten(),
        nn.Linear(28*28, n_hidden), nn.ReLU(),
        nn.Linear(n_hidden, 10)
    ).to(device)
    optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
    history = train2(model, optimizer, xentropy, accuracy,
                     train_loader, valid_loader, n_epochs=3)
    return history["valid_metrics"][-1]   # wartość do zoptymalizowania

torch.manual_seed(42)
sampler = optuna.samplers.TPESampler(seed=42)
study = optuna.create_study(direction="maximize", sampler=sampler)
study.optimize(objective, n_trials=3)
```

### 5.2. Przeglądanie wyników optymalizacji

```python
study.best_params
# {'learning_rate': 0.008471801418819975, 'n_hidden': 188}

study.best_value
# 0.8198000192642212
```

### 5.3. Przekazywanie zbiorów danych

Zbiory danych przekazujemy **raczej poprzez podanie loaderów, a nie zmiennych globalnych**:

```python
def objective(trial, train_loader, valid_loader):
    ...  # reszta jw.
```

Możemy opakować tę funkcję **lambdą**:

```python
objective_with_data = lambda trial: objective(
    trial, train_loader=train_loader, valid_loader=valid_loader)
```

…albo skorzystać z **`functools.partial`** (zalecane — bardziej czytelne):

```python
from functools import partial
objective_with_data = partial(objective,
                              train_loader=train_loader,
                              valid_loader=valid_loader)
```

---

## 6. Zapisywanie, wczytywanie i optymalizacja modeli

### 6.1. `torch.save` oraz `torch.load`

Modele PyTorch zapisujemy typowo w plikach o rozszerzeniu **`.pt`** lub **`.pth`**. Pod spodem tworzony jest **pickle**, a następnie kompresowany **zip**:

```python
torch.save(model, "my_model.pt")
loaded_model = torch.load("my_model.pt")
```

### 6.2. Dlaczego lepiej zapisywać same wagi?

- **Format pickle nie jest bezpieczny** — ktoś może wstrzyknąć złośliwy kod, który uruchomi się w chwili wczytania modelu.
- Mogą pojawić się **problemy z kompatybilnością** między niektórymi wersjami Pythona.

**Dlatego lepiej zapisać same wagi** (state dict — słownik nazwa → tensor):

```python
torch.save(model.state_dict(), "my_fashion_mnist_weights.pt")

# wczytanie:
new_model = ImageClassifier(n_inputs=1*28*28, n_hidden1=300, n_hidden2=100, n_classes=10)
loaded_weights = torch.load("my_fashion_mnist_weights.pt", weights_only=True)
new_model.load_state_dict(loaded_weights)
new_model.eval()
```

> `weights_only=True` — flaga bezpieczeństwa, blokuje wczytywanie dowolnego kodu Pythona.

### 6.3. Konwersja modelu do TorchScript

**Po co?** TorchScript pozwala uruchomić model **bez interpretera Pythona** (np. w C++, na urządzeniach mobilnych), a także optymalizować go pod inferencję.

Dwa sposoby:

**Tracing** — przepuszczamy przykładowe wejście przez model i nagrywamy operacje:

```python
example = torch.randn(1, 1, 28, 28).to(device)
traced_model = torch.jit.trace(model, example)
traced_model.save("model_traced.pt")
```

**Scripting** — analiza kodu źródłowego klasy (działa nawet gdy są `if`/`for` zależne od danych):

```python
scripted_model = torch.jit.script(model)
scripted_model.save("model_scripted.pt")
```

**Dalsza optymalizacja do inferencji:**

```python
optimized = torch.jit.optimize_for_inference(traced_model)
```

---

## 7. Optymalizacja procesu uczenia

### 7.1. Problem znikających / eksplodujących gradientów

Przy propagacji gradientów **od wyższych do niższych warstw** mogą one:
- **wygaszać się** — *problem znikających gradientów* (*vanishing gradients*),
- **nadmiernie się wzmacniać** — *problem eksplodujących gradientów* (*exploding gradients*).

**Glorot i He** zaproponowali, aby **wariancja wejść każdej warstwy była równa wariancji jej wyjść**, czyli wagi połączeń warstwy powinny mieć rozkład normalny o:

$$
\mu = 0, \qquad \sigma^2 = \frac{1}{\text{fan}_{\text{avg}}}
$$

gdzie $\text{fan}_{\text{avg}} = (\text{fan}_{\text{in}} + \text{fan}_{\text{out}}) / 2$.

**Tabela schematów inicjalizacji:**

| Metoda | Funkcje aktywacji | $\sigma^2$ |
|---|---|---|
| **Xavier (Glorot)** | brak, tanh, sigmoid, softmax | $1 / \text{fan}_{\text{avg}}$ |
| **Kaiming (He)** | ReLU & co. | $2 / \text{fan}_{\text{in}}$ |
| **Yann LeCun** | SELU | $1 / \text{fan}_{\text{in}}$ |

### 7.2. Parametry inicjalizacji w PyTorch

> Domyślnie `nn.Linear` korzysta z **inicjalizacji Kaiminga**, ale przeskalowanej o $\sqrt{6}$, a `bias` ma wartości losowe. Trzeba więc te wartości odpowiednio **przeskalować**:

```python
import torch
import torch.nn as nn

layer = nn.Linear(40, 10)
layer.weight.data *= 6 ** 0.5   # Kaiming  (lub 3 ** 0.5 dla LeCun)
torch.zero_(layer.bias.data)
```

### 7.3. Funkcje inicjalizacyjne

Wygodniej i bezpieczniej skorzystać z funkcji inicjalizacyjnych w module **`nn.init`**:

```python
nn.init.kaiming_uniform_(layer.weight)
nn.init.zeros_(layer.bias)
```

Możemy też wykonać to **dla wszystkich warstw hurtem** za pomocą `apply()`:

```python
def use_he_init(module):
    if isinstance(module, nn.Linear):
        nn.init.kaiming_uniform_(module.weight)
        nn.init.zeros_(module.bias)

model = nn.Sequential(nn.Linear(50, 40), nn.ReLU(),
                     nn.Linear(40, 1),  nn.ReLU())
model.apply(use_he_init)
```

### 7.4. Nienasycające funkcje aktywacji

Problem **ReLU**: **„umierające” neurony** — gdy suma ważona wejść jest zawsze ujemna, neuron stale daje zero i gradient też jest zero → neuron przestaje się uczyć.

**Rozwiązania:**

**Leaky ReLU:**

$$
\text{LeakyReLU}_\alpha(z) = \max(\alpha z,\ z)
$$

(małe $\alpha$, np. 0,01 — pozwala na mały gradient dla $z < 0$)

**Exponential Linear Unit (ELU):**

$$
\text{ELU}_\alpha(z) = \begin{cases}
\alpha (e^z - 1) & \text{dla } z < 0 \\
z & \text{dla } z \geq 0
\end{cases}
$$

**Scaled ELU (SELU)** — wariant ELU z dodatkowym mnożnikiem $\lambda \approx 1{,}05$, samonormalizujący się (dla odpowiednich warunków utrzymuje średnie 0 i wariancje 1 w głębi sieci).

### 7.5. Jeszcze więcej ELU

W praktyce nowsze warianty (GELU, Swish/SiLU, Mish) są stosowane w Transformerach i CNN — ale dla MLP wystarczy zwykły ReLU/LeakyReLU/ELU.

### 7.6. Normalizacja wsadów (Batch Normalization)

- Stosowanie inicjalizacji He i ELU/ReLU & co. zmniejsza ryzyko niestabilności gradientów **na początku uczenia**, ale problem może pojawić się **później**.
- Technika **Batch Normalization** polega na dodaniu operacji **tuż przed lub po funkcji aktywacji** każdej warstwy ukrytej — **wycentrowanie i normalizacja wejścia + skalowanie i przesunięcie wyniku**.

**Wzory BN** (dla mini-batcha rozmiaru $m$):

$$
\mu_B = \frac{1}{m}\sum_{i=1}^{m} x_i, \qquad
\sigma_B^2 = \frac{1}{m}\sum_{i=1}^{m} (x_i - \mu_B)^2
$$

$$
\hat{x}_i = \frac{x_i - \mu_B}{\sqrt{\sigma_B^2 + \varepsilon}}, \qquad
y_i = \gamma \hat{x}_i + \beta
$$

Gdzie $\gamma$ i $\beta$ to **uczone** parametry skalowania i przesunięcia.

```python
torch.manual_seed(42)

model = nn.Sequential(
    nn.Flatten(),
    nn.BatchNorm1d(1 * 28 * 28),
    nn.Linear(1 * 28 * 28, 300),
    nn.ReLU(),
    nn.BatchNorm1d(300),
    nn.Linear(300, 100),
    nn.ReLU(),
    nn.BatchNorm1d(100),
    nn.Linear(100, 10)
)

dict(model[1].named_parameters()).keys()
# dict_keys(['weight', 'bias'])  -> to są γ i β
```

> **Tryb treningu vs ewaluacji w BN:** w treningu BN używa statystyk z bieżącego batcha; w `eval()` używa zapisanych średnich kroczących z treningu.

---

## 8. Szybsze algorytmy optymalizacji — pęd, AdaGrad, RMSProp, Adam

### 9. Algorytm gradientowy z pędem (*momentum*)

- Klasyczny algorytm gradientowy **nie uwzględnia wcześniejszych wartości gradientów** — bierze pod uwagę tylko wartość chwilową.
- Algorytm z pędem dodaje **wektor pędu**, a więc wartość gradientu przekłada się **na przyspieszenie, a nie prędkość**:

$$
\mathbf{m} \leftarrow \beta\mathbf{m} - \eta \nabla_\Theta J(\Theta)
$$

$$
\Theta \leftarrow \Theta + \mathbf{m}
$$

Gdzie $\beta \in [0, 1)$ to **współczynnik pędu** (typowo 0,9).

> Np. dla $\beta = 0{,}9$ **prędkość może wzrosnąć 10x** w stosunku do zwykłego algorytmu gradientowego.
>
> Przyspieszenie jest widoczne szczególnie przy **różnych skalach wejść** (efekt „wydłużonej misy” — kierunki o większej krzywiźnie nie powodują kasowania się gradientów).

```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
```

### 9.1. Nesterov Accelerated Gradient (NAG)

- Zaproponowany w **1983** przez **Yuriya Nesterowa**, **prawie zawsze szybszy** niż „zwykły” gradient z pędem.
- Patrzy „z wyprzedzeniem” — gradient liczymy w punkcie, do którego pęd już nas „popycha”:

$$
\mathbf{m} \leftarrow \beta \mathbf{m} - \eta \nabla_\Theta J(\Theta + \beta\mathbf{m})
$$

$$
\Theta \leftarrow \Theta + \mathbf{m}
$$

Dodajemy do SGD atrybut **`nesterov=True`**:

```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.01,
                            momentum=0.9, nesterov=True)
```

### 9.2. AdaGrad

- **Koryguje kierunek spadku gradientowego**, zmniejszając wpływ najbardziej stromych wymiarów — algorytm szybciej kieruje się w stronę globalnego optimum.
- Na bieżąco **zmniejsza współczynnik uczenia** — szybciej dla stromych wymiarów i wolniej dla łagodnych — co poprawia zbieżność i ogranicza potrzebę strojenia kroku uczenia.
- Dobrze działa dla prostych problemów kwadratowych, ale **w sieciach neuronowych może zbyt szybko zmniejszać współczynnik uczenia** — trening może się zatrzymać przed osiągnięciem optimum.

**Wzór (intuicja):**

$$
s \leftarrow s + (\nabla_\Theta J)^2, \qquad
\Theta \leftarrow \Theta - \frac{\eta}{\sqrt{s + \varepsilon}} \nabla_\Theta J
$$

```python
optimizer = torch.optim.Adagrad(model.parameters(), lr=0.01)
```

### 9.3. RMSProp, Adam i ich warianty

- **RMSProp** (`torch.optim.RMSprop`) — adaptacyjnie zmienia współczynnik uczenia dla każdego parametru na podstawie **średniej kwadratów gradientów** (zanikającej, w przeciwieństwie do AdaGrad).
- **Adam** (`torch.optim.Adam`) — łączy **momentum i adaptacyjne tempo uczenia**, wykorzystując średnią gradientów (1. moment) oraz ich wariancję (2. moment).
- **Warianty Adam** — modyfikacje Adama poprawiające stabilność, generalizację lub zbieżność, np. **AdamW**, **AMSGrad**, **Nadam**.

```python
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-3, weight_decay=1e-2)
```

### 9.4. Porównanie algorytmów

| Klasa | Szybkość zbieżności | Jakość zbieżności |
|---|---|---|
| SGD | ⭐ | ⭐⭐⭐ |
| SGD(momentum=…) | ⭐⭐ | ⭐⭐⭐ |
| SGD(momentum=…, nesterov=True) | ⭐⭐ | ⭐⭐⭐ |
| Adagrad | ⭐⭐ | ⭐ (zatrzymuje się zbyt wcześnie) |
| RMSprop | ⭐⭐ | ⭐⭐ lub ⭐⭐⭐ |
| Adam | ⭐⭐ | ⭐⭐ lub ⭐⭐⭐ |
| AdaMax | ⭐⭐ | ⭐⭐ lub ⭐⭐⭐ |
| NAdam | ⭐⭐ | ⭐⭐ lub ⭐⭐⭐ |
| AdamW | ⭐⭐ | ⭐⭐ lub ⭐⭐⭐ |

> **Wnioski praktyczne:**
> - SGD **najwolniejszy**, ale daje **najlepszą generalizację**.
> - Adam i pochodne — **szybkie**, ale czasem gorsza generalizacja → **AdamW** to dobry kompromis.

---

## 9. Harmonogramowanie kroku uczenia (*learning rate scheduling*)

### 10.1. Harmonogramowanie wykładnicze

Klasa **`ExponentialLR`** realizuje wykładnicze zmniejszanie współczynnika uczenia, mnożąc go po każdej epoce przez hiperparametr **`gamma`** (zwykle nieco mniejszy od 1):

$$
\eta_{t+1} = \gamma \cdot \eta_t
$$

```python
model = ...  # zbuduj model
optimizer = torch.optim.SGD(model.parameters(), lr=0.05)
scheduler = torch.optim.lr_scheduler.ExponentialLR(optimizer, gamma=0.9)
```

Następnie wywołujemy po każdej epoce **`scheduler.step()`**:

```python
for epoch in range(n_epochs):
    for X_batch, y_batch in train_loader:
        ...  # pętla taka jak wcześniej
    scheduler.step()
```

### 10.2. Wyżarzanie cosinusowe (*Cosine Annealing*)

Zmienia przebieg tak, aby krok pozostał wysoki przez **dłuższą część treningu**:

$$
\eta_t = \eta_{\min} + \frac{1}{2}(\eta_{\max} - \eta_{\min}) \left(1 + \cos\left(\frac{t}{T_{\max}}\pi\right)\right)
$$

```python
scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(
    optimizer, T_max=n_epochs, eta_min=1e-6)
```

### 10.3. Harmonogramowanie adaptacyjne (*performance scheduling*)

Implementowane przez **`ReduceLROnPlateau`** — mnoży LR przez współczynnik **w przypadku braku poprawy** zadanej metryki (np. funkcji straty dla zbioru walidacyjnego).

**Najważniejsze parametry:**
- **`mode`** — `"max"` lub `"min"` (zależnie czy metryka ma rosnąć czy maleć),
- **`patience`** — ile epok czekamy na poprawę (domyślnie 10),
- **`factor`** — współczynnik, przez który mnożymy krok uczenia (domyślnie 0,1).

```python
scheduler = torch.optim.lr_scheduler.ReduceLROnPlateau(
    optimizer, mode="max", patience=2, factor=0.1)

# wywołanie z metryką:
for epoch in range(n_epochs):
    ...  # trening + walidacja
    scheduler.step(valid_metric)  # tu podajemy wartość metryki!
```

### 10.4. Miękki start — LR warmup

- **Nie startujemy od razu od maksymalnego LR**, ale miękko go zwiększamy na samym początku.
- Często wykorzystujemy do tego **`LinearLR`**:

```python
warmup_scheduler = torch.optim.lr_scheduler.LinearLR(
    optimizer, start_factor=0.1, end_factor=1.0, total_iters=3)
```

Możemy też użyć **dowolnej lambdy** (`LambdaLR`):

```python
warmup_scheduler = torch.optim.lr_scheduler.LambdaLR(
    optimizer,
    lambda epoch: (min(epoch, 3) / 3) * (1.0 - 0.1) + 0.1)
```

> **Po co warmup?** Na początku treningu wagi są losowe → gradienty są chaotyczne → duży LR może zniszczyć model. Po kilku epokach z małym LR sieć „się stabilizuje” i można podkręcić tempo.

---

## 10. Dodatek — teoria spoza prezentacji

### 10.1. Typowe rozmiary batcha

| Rozmiar | Charakterystyka |
|---|---|
| **1** (czysty SGD) | bardzo szumny, ale dobra eksploracja minimów |
| **32–64** | klasyka dla MLP i małych CNN |
| **128–512** | większe modele (ImageNet) |
| **1024+** | wymaga modyfikacji LR (np. linear scaling rule) i warmupu |

**Linear scaling rule** (Goyal et al.): jeśli mnożymy batch size przez $k$, mnożymy też LR przez $k$.

### 10.2. Regularyzacja w PyTorch — Dropout

Dropout to losowe „wyłączanie” neuronów w czasie treningu — typowo z $p = 0{,}2$–$0{,}5$:

```python
model = nn.Sequential(
    nn.Linear(784, 300), nn.ReLU(), nn.Dropout(0.3),
    nn.Linear(300, 100), nn.ReLU(), nn.Dropout(0.3),
    nn.Linear(100, 10)
)
```

Działa **tylko w `model.train()`**, automatycznie wyłącza się w `model.eval()`.

### 10.3. Weight decay (regularyzacja L2)

Większość optymalizatorów PyTorch przyjmuje parametr `weight_decay`:

```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.01, weight_decay=1e-4)
```

Dodaje do straty człon $\lambda \sum w_i^2$, co „przyciąga” wagi do zera. **Uwaga:** w `Adam` weight_decay działa inaczej niż się intuicyjnie sądzi — dlatego powstał `AdamW`, gdzie weight_decay jest **rozdzielony** od adaptacyjnego LR (i działa poprawnie).

### 10.4. Early stopping

Brak gotowego scheduler'a — implementujemy ręcznie:

```python
best_valid = float("inf")
patience, counter = 10, 0
best_state = None
for epoch in range(n_epochs):
    ...
    if valid_loss < best_valid:
        best_valid = valid_loss
        counter = 0
        best_state = {k: v.clone() for k, v in model.state_dict().items()}
    else:
        counter += 1
        if counter >= patience:
            print("Early stopping!")
            break
model.load_state_dict(best_state)
```

### 10.5. Gradient clipping (przy eksplodujących gradientach)

```python
loss.backward()
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
optimizer.step()
```

Ogranicza normę gradientu do `max_norm` — zapobiega rozjechaniu się modelu przy nagłych skokach.

### 10.6. Tryb `train` vs `eval` — przypomnienie

| Komponent | `model.train()` | `model.eval()` |
|---|---|---|
| **Dropout** | aktywny (losowe zerowanie) | wyłączony |
| **BatchNorm** | statystyki z bieżącego batcha | średnie kroczące z treningu |
| **Gradienty** | obliczane | (zwykle z `no_grad()`) nie liczone |

### 10.7. Standardowa konwencja czterech kroków na batch (do egzaminu)

```python
optimizer.zero_grad()      # 1. wyzeruj gradienty
y_pred = model(X_batch)    # 2. forward
loss = criterion(y_pred, y_batch)   # 3. strata
loss.backward()            # 4. backward
optimizer.step()           # 5. update
```

### 10.8. Najczęstsze błędy w PyTorch (FAQ)

| Błąd | Skutek | Rozwiązanie |
|---|---|---|
| Brak `optimizer.zero_grad()` | gradienty akumulują się między iteracjami | dodaj na początku pętli |
| Brak `.to(device)` na danych | błąd `expected device X, got Y` | wszystko na to samo urządzenie |
| Predykcja bez `model.eval()` | dropout aktywny, niespójne wyniki | zawsze `eval()` w inferencji |
| Predykcja bez `no_grad()` | OOM przy dużych modelach | `with torch.no_grad():` |
| Mieszanie typów (float32 vs float64) | `RuntimeError: dtype mismatch` | używać `torch.FloatTensor` lub `dtype=torch.float32` |
| Zwrot tensora z grafu (przez `.item()` z gradientem) | przeciek pamięci | użyj `.item()` lub `.detach()` |

### 10.9. Podsumowanie — gdzie czego używać

| Sytuacja | Co używać |
|---|---|
| Prosta sieć bez obejść | `nn.Sequential` |
| Sieć z obejściami / wieloma wejściami/wyjściami | własna klasa dziedzicząca po `nn.Module` |
| Regresja | `nn.MSELoss` + brak aktywacji wyjścia (lub ReLU/softplus/sigmoid jeśli zakres ograniczony) |
| Klasyfikacja binarna | 1 neuron + `nn.BCEWithLogitsLoss` (z logitów) |
| Klasyfikacja wieloklasowa | N neuronów + `nn.CrossEntropyLoss` (z logitów) |
| Klasyfikacja wieloetykietowa | N neuronów + `nn.BCEWithLogitsLoss` |
| Startowy optymalizator | `Adam(lr=1e-3)` lub `AdamW` |
| Maksymalna generalizacja | `SGD(momentum=0.9, nesterov=True)` z LR scheduling |
| Strojenie hiperparametrów | Optuna z TPESampler |

---

## Pytania kontrolne (możliwe na egzaminie)

1. Czym różni się BGD od mini-batch GD? Dlaczego ten drugi jest lepszy w deep learning?
2. Co robi `nn.Linear(in_features, out_features)`? Wypisz jakie ma parametry.
3. Czym różni się `nn.Parameter` od zwykłego `torch.Tensor`?
4. Dlaczego w PyTorch trzeba ręcznie wywoływać `optimizer.zero_grad()`?
5. Co to jest `criterion` w terminologii PyTorch?
6. Dlaczego po przejściu na DataLoader trening może być **wolniejszy** mimo użycia GPU? Jak temu zaradzić?
7. Czym różni się `nn.Sequential` od dziedziczenia po `nn.Module`? Kiedy stosować które?
8. Wyjaśnij, czym jest architektura **Wide & Deep** i dlaczego nie da się jej zrealizować przez `nn.Sequential`.
9. Dlaczego `nn.CrossEntropyLoss` nie wymaga softmaxa na wyjściu modelu?
10. Jak z logitów uzyskać prawdopodobieństwa klas?
11. Czym różni się `nn.BCELoss` od `nn.BCEWithLogitsLoss`? Który preferować i dlaczego?
12. Wyjaśnij **pułapkę uśredniania** przy obliczaniu RMSE przez batche. Jak ją obejść?
13. Czym jest **TPESampler** w Optuna? Jak działa na wysokim poziomie?
14. Dlaczego lepiej zapisywać `state_dict()` niż cały model?
15. Czym różni się **tracing** od **scripting** w TorchScript?
16. Podaj wzór inicjalizacji Glorota i He. Kiedy używać której?
17. Wyjaśnij problem „umierających ReLU”. Jakie są rozwiązania?
18. Napisz wzór funkcji **ELU** i powiedz, dlaczego jest lepsza niż ReLU.
19. Co to jest **Batch Normalization**? Jak działa w trybie `train` vs `eval`?
20. Wyprowadź wzór algorytmu gradientowego z **pędem**. Dlaczego przyspiesza zbieżność?
21. Czym różni się NAG (Nesterov) od zwykłego momentum?
22. Wyjaśnij dlaczego **AdaGrad** może się zatrzymać przed osiągnięciem optimum.
23. Co łączy **Adam** i co go odróżnia od RMSProp i Momentum?
24. Czym różni się **Adam** od **AdamW**?
25. Wymień co najmniej trzy strategie harmonogramowania LR (exponential, cosine, plateau, warmup).
26. Co robi `ReduceLROnPlateau` i jakie ma kluczowe parametry?
27. Co to jest **LR warmup** i dlaczego jest potrzebny?

---

# Konwolucyjne sieci neuronowe (CNN) — notatka egzaminacyjna

**Przedmiot:** Uczenie maszynowe 2025/2026
**Wykładowca:** dr inż. Sebastian Ernst
**Temat wykładu:** Konwolucyjne sieci neuronowe

---

## Spis treści

1. [Przetwarzanie obrazów przez modele ML](#1-przetwarzanie-obrazów-przez-modele-ml)
2. [Warstwy konwolucyjne w PyTorch](#2-warstwy-konwolucyjne-w-pytorch)
3. [Przygotowanie obrazków i warstwy CNN w PyTorch](#3-przygotowanie-obrazków-i-warstwy-cnn-w-pytorch)
4. [Architektury CNN](#4-architektury-cnn)
5. [Słynne architektury — AlexNet, GoogLeNet, ResNet](#5-słynne-architektury)
6. [Uczenie transferowe](#6-uczenie-transferowe)
7. [Zastosowania modeli konwolucyjnych](#7-zastosowania-modeli-konwolucyjnych)
8. [Dodatek — teoria spoza prezentacji](#8-dodatek--teoria-spoza-prezentacji)

---

## 1. Przetwarzanie obrazów przez modele ML

### 1.1. Jak działa kora wzrokowa (w uproszczeniu)

CNN są inspirowane biologiczną korą wzrokową:
- neurony mają **ograniczone pole odbiorcze** (*receptive field*) — reagują tylko na fragment obrazu,
- wiele neuronów tworzy pełne pole widzenia,
- neurony są **specjalizowane** — mają rozdzielne zadania (np. rozpoznawanie linii poziomych / pionowych),
- **warstwy nie są gęste** (*fully connected*).

**Dlaczego nie warstwy gęste?** Dla obrazów 100×100 (10 000 pikseli) warstwa z 1000 neuronów wymagałaby **10 milionów połączeń** — nie do udźwignięcia obliczeniowo. CNN rozwiązują ten problem przez lokalne połączenia i współdzielenie wag.

### 1.2. Warstwy konwolucyjne

- Obrazy reprezentowane są jako **macierze (2D)**, nie wektory (1D).
- Neurony pierwszej warstwy „patrzą” na **„swoje” obszary** w obrazie (lokalne pola odbiorcze).
- Ten schemat powtarzany jest w kolejnych warstwach.
- Nie podajemy rozmiaru warstwy — **wynika on z rozmiaru danych wejściowych**.

### 1.3. Budowa warstwy konwolucyjnej

- Neuron w wierszu $i$ i kolumnie $j$ jest połączony z neuronami w wierszach $(i,\ i + f_h - 1)$ i kolumnach $(j,\ j + f_w - 1)$ w niższej warstwie.
- $f_h$ i $f_w$ to **wysokość i szerokość pola odbiorczego** (rozmiar jądra).
- Jeżeli chcemy, aby warstwy się **nie zmniejszały**, dodajemy **„ramkę” z zer** (*zero padding*).

### 1.4. Rozsuwanie pól odbiorczych — krok (*stride*)

- Pozwala na połączenie dużej warstwy ze **znacznie mniejszą** warstwą powyżej (redukcja rozmiaru).
- Przesunięcie pomiędzy polami odbiorczymi nazywamy **krokiem** (*stride*).
- **Zmodyfikowane wzory** uwzględniające krok $s_h, s_w$:

$$
i \rightarrow (i \times s_h,\ i \times s_h + f_h - 1)
$$

$$
j \rightarrow (j \times s_w,\ j \times s_w + f_w - 1)
$$

### 1.5. Filtry (jądra konwolucyjne)

- Zwane też **jądrami konwolucyjnymi / splotowymi** (*convolutional kernel*).
- Reprezentują **wagi** w warstwach konwolucyjnych.
- **To one podlegają trenowaniu.**
- Są **takie same dla wszystkich neuronów** danej mapy cech (współdzielenie wag — *weight sharing*).
- Warstwa konwolucyjna zwraca tyle „obrazów” (zwanych **mapami cech**, *feature maps*) ile ma filtrów (a ich rozmiar to rozmiar warstwy).

### 1.6. Budujemy sieć z warstw

- Każda warstwa składa się z kilku **podwarstw** (jedna na filtr / mapę cech).
- Wszystkie neurony dla danej mapy cech **współdzielą te same parametry** (wagi i biasy) — to drastycznie zmniejsza liczbę parametrów.
- Neurony danej mapy cech warstwy wyższej połączone są z **odpowiednimi neuronami wszystkich map cech** warstwy niższej.

**Pełny wzór wyjścia neuronu** na pozycji $(i, j)$ w mapie cech $k$ (gdzie $k'$ to mapy niższej warstwy, $(u, v)$ to pozycja względna w polu odbiorczym):

$$
z_{i,j,k} = b_k + \sum_{u=0}^{f_h-1} \sum_{v=0}^{f_w-1} \sum_{k'=0}^{f_{n'}-1}
x_{i',j',k'} \times w_{u,v,k',k}
\quad \text{gdzie} \quad
\begin{cases}
i' = i \times s_h + u \\
j' = j \times s_w + v
\end{cases}
$$

Gdzie:
- $z_{i,j,k}$ — wyjście (przed aktywacją) neuronu,
- $b_k$ — bias mapy cech $k$,
- $x_{i',j',k'}$ — wejście,
- $w_{u,v,k',k}$ — waga połączenia (element filtra),
- $f_{n'}$ — liczba map cech w niższej warstwie.

---

## 2. Warstwy konwolucyjne w PyTorch

Format tensorów obrazów w PyTorch: **`(wsad, liczba_kanałów, wysokość, szerokość)`** — tzw. **NCHW**.

> **Uwaga:** to inna konwencja niż w bibliotekach obrazowych (PIL/matplotlib używają HWC — wysokość, szerokość, kanały). Dlatego często potrzebne jest `permute`.

---

## 3. Przygotowanie obrazków i warstwy CNN w PyTorch

### Wybór urządzenia

```python
import matplotlib.pyplot as plt
import numpy as np
import torch
if torch.cuda.is_available():
    device = "cuda"
elif torch.mps.is_available():
    device = "mps"
else:
    device = "cpu"
```

### Wczytanie przykładowych obrazów

```python
from sklearn.datasets import load_sample_images
sample_images = np.stack(load_sample_images()["images"])
sample_images = torch.tensor(sample_images, dtype=torch.float32) / 255   # normalizacja 0-1
sample_images.shape
# torch.Size([2, 427, 640, 3])   <- format HWC (NHWC)
```

**Permutacja do formatu PyTorch (NCHW):**

```python
sample_images_permuted = sample_images.permute(0, 3, 1, 2)
sample_images_permuted.shape
# torch.Size([2, 3, 427, 640])   <- teraz kanały na pozycji 1
```

> `permute(0, 3, 1, 2)` przestawia osie: (0=wsad, 3=kanały, 1=wysokość, 2=szerokość).

**Wyświetlanie obrazu** (z powrotem do HWC dla matplotlib):

```python
def plot_image(image):
    plt.imshow(image.permute(1, 2, 0))   # CHW -> HWC
    plt.axis("off")
```

### 3.1. Wytnijmy małe fragmenty

```python
import torchvision
import torchvision.transforms.v2 as T

cropped_images = T.CenterCrop((70, 120))(sample_images_permuted)
cropped_images.shape
# torch.Size([2, 3, 70, 120])
```

### 3.2. Tworzymy pierwszą warstwę konwolucyjną

```python
import torch.nn as nn

torch.manual_seed(42)
conv_layer = nn.Conv2d(
    in_channels=3,      # RGB
    out_channels=32,    # 32 filtry = 32 mapy cech
    kernel_size=7)      # filtr 7x7
fmaps = conv_layer(cropped_images)
print('in: ', cropped_images.shape)   # [2, 3, 70, 120]
print('out:', fmaps.shape)            # [2, 32, 64, 114]
print('weights:', conv_layer.weight.shape)  # [32, 3, 7, 7]
```

**Skąd rozmiar wyjścia?** Bez paddingu obraz kurczy się o $(f - 1)$ w każdym wymiarze:
- wysokość: $70 - (7 - 1) = 64$,
- szerokość: $120 - (7 - 1) = 114$.

**Kształt wag** `[32, 3, 7, 7]` = `[out_channels, in_channels, kernel_h, kernel_w]`.

### 3.3. Warstwa z padding

```python
conv_layer = nn.Conv2d(
    in_channels=3,
    out_channels=32,
    kernel_size=7,
    padding="same")   # "same" albo "valid"
fmaps = conv_layer(cropped_images)
print('out:', fmaps.shape)   # [2, 32, 70, 120]  <- rozmiar zachowany!
```

- **`padding="same"`** — dokłada ramkę z zer, by **rozmiar wyjścia = rozmiar wejścia**.
- **`padding="valid"`** — brak paddingu (domyślnie), obraz się kurczy.

### 3.4. Warstwy zbierające (*pooling layers*)

- Mają na celu **zmniejszenie obrazu** w celu:
    - obniżenia nakładów obliczeniowych,
    - zmniejszenia użycia pamięci,
    - zmniejszenia liczby parametrów (**przeciwdziałanie przeuczeniu!**).
- Podobne do warstw konwolucyjnych, ale **nie mają wag**.
- Korzystają z **funkcji agregacji** takich jak **średnia** czy **maksimum**.

**Przykład: max pooling o filtrach 2×2 i kroku 2:**

```python
max_pool = nn.MaxPool2d(kernel_size=2)   # domyślnie stride = kernel_size
```

> Max pooling 2×2 zmniejsza wymiary obrazu o połowę i wprowadza **niezmienniczość względem małych przesunięć** (translation invariance).

### 3.5. Warstwy zbierające w PyTorch

```python
max_pool = nn.MaxPool2d(kernel_size=2)
avg_pool = nn.AvgPool2d(kernel_size=2)

output_max = max_pool(sample_images_permuted)
output_avg = avg_pool(sample_images_permuted)

plt.imshow(output_max[0].permute(1, 2, 0))
plt.imshow(output_avg[0].permute(1, 2, 0))
```

> **Max vs Average pooling:** max pooling zachowuje najsilniejsze cechy (ostrzejszy obraz, dominuje w praktyce), average pooling uśrednia (gładszy, łagodniejszy).

### 3.6. Warstwy zbierające — również wgłąb

Pooling można stosować nie tylko przestrzennie (wysokość/szerokość), ale też **wgłąb** (po kanałach / mapach cech) — wtedy redukuje liczbę map cech zamiast rozmiaru przestrzennego. Rzadziej stosowane.

---

## 4. Architektury CNN

### 4.1. Typowa architektura sieci konwolucyjnej

Klasyczny schemat:

```
WEJŚCIE → [Conv → ReLU → Conv → ReLU → Pool] × N → Flatten → [Dense → ReLU] × M → Dense(softmax)
```

Cechy:
- na początku **bloki konwolucyjne** (Conv + aktywacja + Pooling),
- w głębi sieci: **coraz więcej map cech, coraz mniejszy rozmiar przestrzenny**,
- na końcu warstwy **gęste** (klasyfikator) + softmax (przez CrossEntropyLoss).

### 4.2.–4.6. FashionMNIST w sieci konwolucyjnej

**Model CNN:**

```python
model = nn.Sequential(
    nn.Conv2d(1, 32, kernel_size=3, padding="same"), nn.ReLU(),
    nn.Conv2d(32, 64, kernel_size=3, padding="same"), nn.ReLU(),
    nn.MaxPool2d(2),
    nn.Conv2d(64, 128, kernel_size=3, padding="same"), nn.ReLU(),
    nn.MaxPool2d(2),
    nn.Flatten(),
    nn.Linear(128 * 7 * 7, 128), nn.ReLU(),
    nn.Linear(128, 10)
).to(device)
```

**Trening:**

```python
n_epochs = 3
optimizer = torch.optim.AdamW(model.parameters())
xentropy  = nn.CrossEntropyLoss()
accuracy  = torchmetrics.Accuracy(task="multiclass", num_classes=10).to(device)
history = train(model, optimizer, xentropy, accuracy,
                train_loader, valid_loader, n_epochs)
# Epoch 1/3, train loss: 0.7836, train metric: 0.7061, valid metric: 0.8506
# Epoch 2/3, train loss: 0.4488, train metric: 0.8484, valid metric: 0.8534
# Epoch 3/3, train loss: 0.3794, train metric: 0.8712, valid metric: 0.8920
```

> Już po 3 epokach CNN osiąga ~89% dokładności walidacyjnej na FashionMNIST — lepiej niż zwykły MLP.

### 4.7. LeNet-5 (1998)

- Jedna z **najlepiej znanych architektur**, autorstwa Yanna LeCuna.
- Szeroko stosowana do **rozpoznawania odręcznie pisanych cyfr (MNIST)**.

**Architektura LeNet-5** (od wejścia do wyjścia):

| Warstwa | Typ | Mapy | Rozmiar | Jądro | Krok | Aktywacja |
|---|---|---|---|---|---|---|
| Wejście | Wejście | 1 | 32×32 | – | – | – |
| C1 | Konwolucyjna | 6 | 28×28 | 5×5 | 1 | tanh |
| S2 | Avg pool | 6 | 14×14 | 2×2 | 2 | tanh |
| C3 | Konwolucyjna | 16 | 10×10 | 5×5 | 1 | tanh |
| S4 | Avg pool | 16 | 5×5 | 2×2 | 2 | tanh |
| C5 | Konwolucyjna | 120 | 1×1 | 5×5 | 1 | tanh |
| F6 | Gęsta | – | 84 | – | – | tanh |
| Wyjście | Gęsta | – | 10 | – | – | RBF |

> Charakterystyczne dla epoki: aktywacja **tanh** (nie ReLU), pooling **uśredniający** (nie max), wyjście **RBF** (dziś zastąpione softmaxem).

---

## 5. Słynne architektury

### 5. AlexNet (2012)

- Podobna do LeNet-5, ale **większa i głębsza**.
- Po raz pierwszy **warstwy konwolucyjne jedna na drugiej** (bez poolingu pomiędzy).
- **Regularyzacja:**
    - **dropout 50%** na warstwach F8 i F9,
    - **augmentacja danych**.
- **LRN (Local Response Normalization):**
    - najmocniej aktywowane neurony „wyłączają” neurony na swojej pozycji w sąsiednich mapach,
    - **dywersyfikacja** rozpoznawanych cech.
- Przełom: wygrała ImageNet 2012 z dużą przewagą — początek ery deep learningu.

### 5.1. Dropout

- Metoda zaproponowana i rozwinięta w **2012 i 2014** roku.
- W każdym kroku, **każdy neuron z prawdopodobieństwem $p$ zostanie opuszczony** (*dropped out*).
- Opuszczony neuron jest **ignorowany, ale tylko w bieżącym kroku** uczenia.
- Hiperparametr $p$ (*dropout rate*) ustawiamy na **10–50%** (zakres węższy w sieciach rekurencyjnych i konwolucyjnych).

> **Intuicja:** sieć nie może polegać na pojedynczych neuronach → uczy się redundantnych, odpornych reprezentacji. Działa jak uśrednianie wielu sieci (ensemble).

### 5.2. Augmentacja danych

Sztuczne powiększanie zbioru treningowego przez transformacje obrazów: obroty, przesunięcia, odbicia lustrzane, zmiany jasności/kontrastu, przycięcia. Zmniejsza overfitting i poprawia generalizację.

### 6.–7. GoogLeNet (2014)

- **Głębsza od AlexNet, ale 10× mniej parametrów** (6M vs 60M) dzięki **modułom incepcyjnym** (*inception modules*).
- Filtry **1×1**:
    - nie wykrywają cech przestrzennych, ale mogą wykrywać cechy idąc **„wgłąb”** (po kanałach),
    - **zmniejszają liczbę map cech** → redukują wymiarowość (**warstwy bottleneck**).
- Para $[1\times1,\ 3\times3]$ lub $[1\times1,\ 5\times5]$ może być traktowana jako **jedna, silna warstwa konwolucyjna**.

**Szczegóły architektury:**
- pierwsze dwie warstwy: **zmniejszenie obrazu, duże filtry** aby zachować dużo informacji,
- **LRN** dla dywersyfikacji,
- **9 inception modules**,
- **global average pooling** pozbywa się całkiem danych przestrzennych — ale to OK, bo przed nią rozmiar i tak spada z 224×224 do 7×7,
- dzięki redukcji rozmiaru **nie potrzebujemy warstw gęstych**.

### 8.–9. Uczenie rezydualne i ResNet (2015)

- Sieci stają się **coraz głębsze, ale mają coraz mniej parametrów**.
- **Obejście** (*skip / shortcut connection*) przyspiesza uczenie sieci — **uczenie rezydualne** (*residual learning*).

**Idea bloku rezydualnego:** zamiast uczyć się funkcji $H(x)$, sieć uczy się **reszty** $F(x) = H(x) - x$, a wyjście to $F(x) + x$:

$$
\text{wyjście} = F(x) + x
$$

> **Dlaczego to działa?** Gdy optymalne odwzorowanie jest bliskie tożsamości, łatwiej nauczyć się $F(x) \approx 0$ niż $H(x) \approx x$. Obejścia pozwalają gradientowi „przeskakiwać” warstwy → rozwiązują problem zanikającego gradientu w bardzo głębokich sieciach (ResNet ma >100 warstw).

### 9.2. Inne znane architektury

- **VGGNet** (2014) — prosta, regularna struktura (same filtry 3×3), bardzo dużo parametrów.
- **Xception** (2016) — *depthwise separable convolutions*.
- **SENet** (2017) — *squeeze-and-excitation* (ważenie kanałów).
- **MobileNet** (2017) — lekka, na urządzenia mobilne.
- **EfficientNet** (2019) — skalowanie głębokości/szerokości/rozdzielczości.

### Porównanie architektur (ImageNet)

| Nazwa | Top-1 acc | Top-5 acc | Parametry | GFLOPs |
|---|---|---|---|---|
| MobileNet V3 small | 67,7% | 87,4% | 2,5M | 0,1 |
| EfficientNet B0 | 77,7% | 93,5% | 5,3M | 0,4 |
| GoogLeNet | 69,8% | 89,5% | 6,6M | 1,5 |
| DenseNet 121 | 74,4% | 92,0% | 8,0M | 2,8 |
| EfficientNet v2 small | 84,2% | 96,9% | 21,5M | 8,4 |
| ResNet 34 | 73,3% | 91,4% | 21,8M | 3,7 |
| Inception V3 | 77,3% | 93,5% | 27,2M | 5,7 |
| ConvNeXt Tiny | 82,6% | 96,1% | 28,6M | 4,5 |
| ResNet 152 | 82,3% | 96,0% | 60,2M | 11,5 |
| AlexNet | 56,5% | 79,1% | 61,1M | 0,7 |
| EfficientNet B7 | 84,1% | 96,9% | 66,3M | 37,8 |
| EfficientNet v2 large | 85,8% | 97,8% | 118,5M | 56,1 |
| ConvNeXt Large | 84,4% | 97,0% | 197,8M | 34,4 |

> **Top-1 acc** = prawidłowa predykcja jest #1; **Top-5 acc** = prawidłowa klasa jest w pierwszej piątce. **GFLOPs** = miliardy operacji zmiennoprzecinkowych (koszt obliczeniowy). Zwróć uwagę: AlexNet ma 61M parametrów, ale tylko 56% dokładności — nowsze architektury są dużo wydajniejsze parametrowo.

---

## 6. Uczenie transferowe

### 6.1. Wykorzystanie wcześniej wytrenowanych warstw

**Idea:** zamiast trenować sieć od zera, bierzemy model wytrenowany na dużym zbiorze (np. ImageNet) i **dostrajamy** go do naszego zadania. Niższe warstwy (wykrywające krawędzie, tekstury) są uniwersalne i nadają się do ponownego użycia.

### 6.2. Nienadzorowane uczenie wstępne

- Uczymy niższe warstwy sieci metodą **nienadzorowaną** (np. autoenkoder), aby „przyzwyczaiła się” do charakterystyki zbioru danych.
- Mała część zbioru danych **posiadająca etykiety** używana jest do wytrenowania warstw wyższych oraz wyjściowej.
- Przydatne, gdy mamy dużo danych nieoznaczonych i mało oznaczonych.

### 6.3. Gotowe modele w PyTorch

Wiele gotowych modeli dostępnych jest w module **`torchvision.models`**:

```python
torch.hub.get_dir()
# '/Users/sebi/.cache/torch/hub'  <- gdzie cachowane są wagi

list(torchvision.models.get_model_weights("convnext_base"))
# [ConvNeXt_Base_Weights.IMAGENET1K_V1]

torchvision.models.list_models()
# ['alexnet', 'convnext_base', ..., 'resnet50', ..., 'vit_b_16', ...]
```

> Dostępne są nie tylko klasyfikatory, ale też modele do detekcji (`fasterrcnn_*`, `ssd*`, `retinanet_*`), segmentacji (`fcn_*`, `deeplabv3_*`, `maskrcnn_*`) i wideo.

### 6.4. Użycie modelu

Model oczekuje obrazów **224×224**, więc musimy nasze obrazki 427×640 przyciąć. TorchVision posiada funkcje `CenterCrop` czy `Resize`, ale **łatwiej sięgnąć do samego modelu** (każdy zestaw wag niesie swój preprocessing):

```python
weights = torchvision.models.ConvNeXt_Base_Weights.IMAGENET1K_V1
model = torchvision.models.convnext_base(weights=weights).to(device)
preprocess = weights.transforms()
preprocessed_images = preprocess(sample_images_permuted)
preprocessed_images.shape
# torch.Size([2, 3, 224, 224])
```

### 6.5. Próba predykcji

```python
model.eval()
with torch.no_grad():
    y_logits = model(preprocessed_images.to(device))
y_logits.shape
# torch.Size([2, 1000])   <- 1000 klas ImageNet

y_pred = torch.argmax(y_logits, dim=1)
y_pred
# tensor([698, 985])
```

### 6.6. Identyfikacja klas

```python
weights.meta.keys()
# dict_keys(['min_size', 'categories', 'recipe', ...])

class_names = weights.meta["categories"]
[class_names[class_id] for class_id in y_pred]
# ['palace', 'daisy']
```

**Podgląd prawdopodobieństw — top-3:**

```python
y_top3_logits, y_top3_class_ids = y_logits.topk(k=3, dim=1)
[[class_names[class_id] for class_id in top3] for top3 in y_top3_class_ids]
# [['palace', 'monastery', 'lakeside'], ['daisy', 'pot', 'ant']]

y_top3_logits.softmax(dim=1)
# tensor([[0.8618, 0.1185, 0.0197],
#         [0.8106, 0.0964, 0.0930]])
```

> Model jest pewny: pałac 86%, stokrotka 81%.

### 6.7. Faktyczne uczenie transferowe — dostrajanie

Dotrenowujemy gotowy model dla innego zbioru danych (**Flowers102** — 102 klasy kwiatów):

```python
from functools import partial
DefaultFlowers102 = partial(torchvision.datasets.Flowers102, root="datasets",
                            transform=weights.transforms(), download=True)
train_set = DefaultFlowers102(split="train")
valid_set = DefaultFlowers102(split="val")
test_set  = DefaultFlowers102(split="test")

class_names = torchvision.datasets.Flowers102.classes
print(len(class_names))   # 102
class_names[:5]
# ['pink primrose', 'hard-leaved pocket orchid', 'canterbury bells', ...]
```

### 6.9. Model „psuje się od głowy” — wymiana klasyfikatora

Struktura modelu ConvNeXt:

```python
[name for name, child in model.named_children()]
# ['features', 'avgpool', 'classifier']
```

Oryginalny klasyfikator ma **1000 wyjść** (ImageNet):

```python
model.classifier
# Sequential(
#   (0): LayerNorm2d((1024,), ...)
#   (1): Flatten(start_dim=1, end_dim=-1)
#   (2): Linear(in_features=1024, out_features=1000, bias=True)
# )
```

**Wymieniamy ostatnią warstwę** na 102 wyjścia:

```python
n_classes = 102
model.classifier[2] = nn.Linear(in_features=1024, out_features=n_classes).to(device)
model.classifier
# Sequential(
#   (0): LayerNorm2d((1024,), ...)
#   (1): Flatten(...)
#   (2): Linear(in_features=1024, out_features=102, bias=True)   <- zmienione!
# )
```

> Mówimy „model psuje się od głowy” bo wymieniamy **głowę** (klasyfikator), a zachowujemy „ciało” (ekstraktor cech).

### 6.10. Wstępne uczenie — zamrażanie warstw

**Zamrażamy niższe warstwy** (żeby losowo zainicjalizowana głowa nie zniszczyła dobrych wag ekstraktora):

```python
for param in model.features.parameters():
    param.requires_grad = False   # zamrożenie - nie liczymy gradientów
```

Trenujemy przez kilka epok (uczy się tylko nowy klasyfikator):

```python
n_epochs = 3
optimizer = torch.optim.AdamW(model.parameters())
xentropy  = nn.CrossEntropyLoss()
accuracy  = torchmetrics.Accuracy(task="multiclass", num_classes=n_classes).to(device)
history = train(model, optimizer, xentropy, accuracy,
                train_loader, valid_loader, n_epochs)
# Epoch 1/3, train loss: 4.2814, train metric: 0.1431, valid metric: 0.5627
# Epoch 2/3, train loss: 3.0229, train metric: 0.6814, valid metric: 0.7716
# Epoch 3/3, train loss: 2.1493, train metric: 0.8392, valid metric: 0.8069
```

> Już po 3 epokach ~80% dokładności na 102 klasach — bo ekstraktor cech jest gotowy.

### 6.11. Dalsze uczenie — odmrażanie

Po uzyskaniu rozsądnej dokładności **nie ma już ryzyka, że gradienty zburzą wagi w dolnej części sieci**, więc możemy **odmrozić niższe warstwy** i kontynuować trening (zwykle z **mniejszym LR**):

```python
for param in model.features.parameters():
    param.requires_grad = True   # odmrożenie

# zwykle z mniejszym learning rate:
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-5)
# ... dalej prowadzimy uczenie
```

### 6.12. Augmentacja obrazów

Stosowana również przy uczeniu transferowym — losowe transformacje (`RandomHorizontalFlip`, `RandomRotation`, `ColorJitter` itp.) zwiększają różnorodność danych.

---

## 7. Zastosowania modeli konwolucyjnych

### 7.1. Klasyfikacja i lokalizacja

**Lokalizacja jest zadaniem regresyjnym:** znajdź **ramkę** (*bounding box*) wokół obiektu. Model przewiduje współrzędne ramki (np. x, y, szerokość, wysokość) obok klasy.

### 7.2. Dodawanie ramek

```python
first_image_ts = T.ToImage()(first_image)
image_with_bbox = get_image_with_bbox(first_image_ts, bbox)
plot_image(image_with_bbox)
```

### 7.3. Intersection over Union (IoU)

- **Metryka do oceniania jakości predykcji położenia ramki.**
- **Stosunek przecięcia** ramki właściwej i predykowanej **do ich sumy** (powierzchni):

$$
\text{IoU} = \frac{\text{Area}(\text{predicted} \cap \text{ground truth})}{\text{Area}(\text{predicted} \cup \text{ground truth})}
$$

> IoU = 1 → idealne pokrycie; IoU = 0 → brak wspólnej części. Typowy próg „trafienia”: IoU ≥ 0,5.

### 7.4. Wykrywanie obiektów (*object detection*)

- **Tradycyjnie:** „przesuwanie” modelu po obrazie (*sliding window*).
- Wymaga stosowania **różnych wielkości ramek** dla różnych wielkości obiektów.
- Jeden obiekt może być **wykryty kilka razy**.
- Potrzebujemy wyjścia oceniającego, czy mamy obiekt w ramce: **objectness** (pewność obecności obiektu).

> Wielokrotne detekcje tego samego obiektu usuwa się przez **Non-Maximum Suppression (NMS)** — zostawiamy ramkę o najwyższym objectness, odrzucamy nakładające się (wysokie IoU).

### 7.5. Sieci w pełni konwolucyjne (*FCN*)

- Warstwy gęste **zmieniamy na warstwy konwolucyjne**.
- Brak warstw gęstych → sieć może **przetwarzać obrazy o dowolnych rozmiarach**.

> Warstwę gęstą można zastąpić konwolucją 1×1 dającą ten sam efekt — to klucz do FCN.

### 7.6. You Only Look Once (YOLO)

Architektura detekcji w **jednym przejściu** (bez sliding window) — bardzo szybka, nadaje się do czasu rzeczywistego.

```python
from ultralytics import YOLO

model = YOLO('yolov9m.pt')   # n=nano, większe: s, m, x
images = ["https://homl.info/soccer.jpg", "https://homl.info/traffic.jpg"]
results = model(images)
# 0: 640x640 5 persons, 2 sports balls, 258.3ms
# 1: 640x640 3 persons, 7 cars, 1 motorcycle, 11 trucks, 258.3ms
```

**Wyniki jako DataFrame i wizualizacja:**

```python
results[0].to_df()

import PIL
results[0].save("my_annotated_soccer.jpg")
PIL.Image.open("my_annotated_soccer.jpg")
```

### 7.7. Segmentacja semantyczna

- Każdy **piksel** jest „kolorowany” zgodnie z **klasą obiektu**, którego jest częścią.
- **Główne wyzwanie:** obrazy przechodząc przez CNN **stopniowo tracą rozdzielczość** (przez pooling i stride).

> Rozwiązanie: architektury **enkoder-dekoder** (np. U-Net) z *transposed convolutions* / *upsampling*, które odtwarzają rozdzielczość, oraz *skip connections* łączące warstwy enkodera z dekoderem.

---

## 8. Dodatek — teoria spoza prezentacji

### 8.1. Wzór na rozmiar wyjścia warstwy konwolucyjnej

Dla wejścia o rozmiarze $W$, jądra $f$, paddingu $p$ i kroku $s$:

$$
W_{\text{out}} = \left\lfloor \frac{W - f + 2p}{s} \right\rfloor + 1
$$

- **`valid`** (p=0, s=1): $W_{\text{out}} = W - f + 1$.
- **`same`** (s=1, $p = (f-1)/2$): $W_{\text{out}} = W$.

### 8.2. Liczba parametrów warstwy konwolucyjnej

$$
\#\text{params} = (f_h \times f_w \times C_{\text{in}} + 1) \times C_{\text{out}}
$$

gdzie $+1$ to bias na każdy filtr. **Kluczowa zaleta CNN:** liczba parametrów **nie zależy od rozmiaru obrazu** (dzięki współdzieleniu wag), w przeciwieństwie do warstwy gęstej.

**Przykład:** Conv2d(3→32, kernel 7×7) → $(7 \times 7 \times 3 + 1) \times 32 = 4736$ parametrów. Warstwa gęsta dla obrazu 70×120×3 → 1 neuron miałby już 25 200 wag.

### 8.3. Pełny przegląd warstw poolingowych w PyTorch

| Warstwa | Działanie |
|---|---|
| `nn.MaxPool2d(k)` | maksimum z okna k×k |
| `nn.AvgPool2d(k)` | średnia z okna k×k |
| `nn.AdaptiveAvgPool2d((H,W))` | pooling do zadanego rozmiaru wyjścia (niezależnie od wejścia) |
| `nn.AdaptiveAvgPool2d(1)` | **global average pooling** (1 wartość na mapę cech) |

### 8.4. Konwolucje specjalne

- **Konwolucja 1×1** — miesza kanały bez patrzenia przestrzennie; redukcja/zwiększenie liczby map cech (bottleneck).
- **Depthwise separable convolution** (MobileNet, Xception) — rozdziela konwolucję przestrzenną i po kanałach → mniej obliczeń.
- **Dilated / atrous convolution** — „rozrzedzone” jądro, większe pole odbiorcze bez wzrostu liczby parametrów (segmentacja).
- **Transposed convolution** (*deconvolution*) — zwiększa rozdzielczość (upsampling, dekodery).

### 8.5. Dlaczego CNN zamiast MLP dla obrazów — podsumowanie

| Cecha | MLP | CNN |
|---|---|---|
| Połączenia | gęste (każdy z każdym) | lokalne (pole odbiorcze) |
| Wagi | osobne dla każdego neuronu | współdzielone (filtry) |
| Liczba parametrów | ogromna | mała, niezależna od rozmiaru obrazu |
| Niezmienniczość przestrzenna | brak | tak (translation invariance) |
| Struktura przestrzenna | tracona (flatten na wejściu) | zachowywana |

### 8.6. Zadania widzenia komputerowego — porównanie

| Zadanie | Wyjście | Przykład metryki |
|---|---|---|
| **Klasyfikacja** | jedna etykieta na obraz | accuracy, top-5 |
| **Lokalizacja** | jedna ramka + klasa | IoU |
| **Detekcja obiektów** | wiele ramek + klasy | mAP (mean Average Precision), IoU |
| **Segmentacja semantyczna** | klasa dla każdego piksela | mIoU, pixel accuracy |
| **Segmentacja instancji** | maska dla każdego obiektu | mask mAP |

### 8.7. Praktyczne wskazówki transfer learning

| Sytuacja | Strategia |
|---|---|
| Mało danych, podobne do ImageNet | zamroź ekstraktor, trenuj tylko głowę |
| Dużo danych, podobne | dostrajaj całość z małym LR |
| Mało danych, inne niż ImageNet | zamroź wczesne warstwy, dostrajaj późniejsze |
| Dużo danych, bardzo inne | rozważ trening od zera lub pełne dostrajanie |

---

## Pytania kontrolne (możliwe na egzaminie)

1. Dlaczego warstwy gęste są nieefektywne dla obrazów? Podaj przykład liczbowy.
2. Czym jest pole odbiorcze (*receptive field*)? Jak się zmienia w głąb sieci?
3. Wyjaśnij pojęcia: filtr, mapa cech, stride, padding.
4. Czym różni się padding `"same"` od `"valid"`? Jak wpływają na rozmiar wyjścia?
5. Oblicz rozmiar wyjścia: wejście 70×120, jądro 7×7, brak paddingu, stride 1.
6. Jaki jest kształt tensora wag dla `nn.Conv2d(3, 32, kernel_size=7)`? Dlaczego?
7. Oblicz liczbę parametrów tej warstwy.
8. Co to jest **współdzielenie wag** i dlaczego jest kluczowe dla CNN?
9. Po co stosujemy warstwy poolingowe? Czym różni się max od average pooling?
10. Dlaczego pooling nie ma parametrów do trenowania?
11. Opisz typową architekturę CNN do klasyfikacji.
12. Co charakteryzuje LeNet-5? Jakie aktywacje i pooling stosowała?
13. Jakie innowacje wprowadził AlexNet?
14. Jak działa **Dropout**? Jaki ma typowy zakres $p$?
15. Czym jest **moduł incepcyjny** w GoogLeNet? Po co konwolucje 1×1?
16. Co to jest **global average pooling** i dlaczego pozwala pozbyć się warstw gęstych?
17. Wyjaśnij ideę **uczenia rezydualnego** (ResNet). Dlaczego skip connections pomagają?
18. Co oznaczają metryki **Top-1** i **Top-5** accuracy?
19. Opisz pełny przepływ **uczenia transferowego**: wymiana głowy, zamrażanie, odmrażanie.
20. Dlaczego najpierw zamrażamy warstwy, a dopiero potem je odmrażamy? Dlaczego z mniejszym LR?
21. Jak uzyskać preprocessing pasujący do gotowego modelu z `torchvision`?
22. Czym jest **IoU**? Podaj wzór i interpretację.
23. Czym jest **objectness** w detekcji obiektów?
24. Na czym polega problem wielokrotnych detekcji i jak się go rozwiązuje (NMS)?
25. Czym jest sieć w pełni konwolucyjna (FCN) i jaką ma przewagę?
26. Jak działa YOLO i czym różni się od podejścia sliding window?
27. Na czym polega **segmentacja semantyczna** i jakie jest jej główne wyzwanie?
28. Porównaj zadania: klasyfikacja, lokalizacja, detekcja, segmentacja (wyjścia i metryki).

# Odpowiedzi na pytania kontrolne — Konwolucyjne sieci neuronowe (CNN)

**Przedmiot:** Uczenie maszynowe 2025/2026

---

### 1. Dlaczego warstwy gęste są nieefektywne dla obrazów? Podaj przykład liczbowy.

W warstwie gęstej (*fully connected*) **każdy neuron jest połączony z każdym pikselem** wejścia, co przy obrazach generuje gigantyczną liczbę parametrów. Dodatkowo flatten obrazu do wektora **niszczy strukturę przestrzenną** (sąsiedztwo pikseli), a wagi nie są współdzielone, więc sieć nie ma żadnej niezmienniczości względem przesunięć.

**Przykład liczbowy (z wykładu):** dla obrazu **100×100 = 10 000 pikseli** warstwa z **1000 neuronów** wymagałaby:

$$
10\,000 \times 1000 = 10\,000\,000 = \textbf{10 milionów połączeń (wag)}
$$

— i to tylko jedna warstwa. CNN rozwiązuje to przez lokalne pola odbiorcze i współdzielenie wag, gdzie liczba parametrów nie zależy od rozmiaru obrazu.

---

### 2. Czym jest pole odbiorcze (*receptive field*)? Jak się zmienia w głąb sieci?

**Pole odbiorcze** to obszar obrazu wejściowego, na który „patrzy” pojedynczy neuron — fragment, który wpływa na jego aktywację. W pierwszej warstwie konwolucyjnej jest ono małe (np. 3×3, 7×7 pikseli, równe rozmiarowi filtra).

**W głąb sieci pole odbiorcze rośnie:** neuron w drugiej warstwie patrzy na obszar neuronów pierwszej warstwy, a każdy z nich widział już swój fragment obrazu — więc efektywne pole odbiorcze stopniowo się powiększa. Dzięki temu **niższe warstwy wykrywają proste cechy lokalne** (krawędzie, tekstury), a **głębsze warstwy — złożone, wielkoskalowe wzorce** (kształty, całe obiekty). Pooling i stride dodatkowo przyspieszają wzrost pola odbiorczego.

---

### 3. Wyjaśnij pojęcia: filtr, mapa cech, stride, padding.

- **Filtr (jądro konwolucyjne, *kernel*)** — mała macierz wag (np. 3×3, 7×7), która jest „przesuwana” po obrazie i wykonuje splot. To **wagi, które podlegają trenowaniu**. Ten sam filtr jest stosowany do całego obrazu (współdzielenie wag).
- **Mapa cech (*feature map*)** — wynik nałożenia jednego filtra na całe wejście; „obraz” pokazujący, gdzie dana cecha występuje. Warstwa zwraca **tyle map cech, ile ma filtrów**.
- **Stride (krok)** — przesunięcie filtra pomiędzy kolejnymi polami odbiorczymi. Stride > 1 **zmniejsza rozmiar wyjścia** (rozsuwa pola odbiorcze).
- **Padding (wypełnienie)** — dodanie „ramki” z zer wokół obrazu, najczęściej po to, by **wyjście nie kurczyło się** względem wejścia.

---

### 4. Czym różni się padding `"same"` od `"valid"`? Jak wpływają na rozmiar wyjścia?

- **`"valid"`** — **brak paddingu** (domyślny). Obraz **kurczy się** o $(f - 1)$ w każdym wymiarze. Dla stride=1: $W_{\text{out}} = W - f + 1$.
- **`"same"`** — dokłada ramkę z zer tak, aby (dla stride=1) **rozmiar wyjścia był równy rozmiarowi wejścia**: $W_{\text{out}} = W$.

**Przykład:** wejście 70×120, filtr 7×7:
- `valid` → 64×114 (kurczy się),
- `same` → 70×120 (rozmiar zachowany).

---

### 5. Oblicz rozmiar wyjścia: wejście 70×120, jądro 7×7, brak paddingu, stride 1.

Wzór: $W_{\text{out}} = \left\lfloor \dfrac{W - f + 2p}{s} \right\rfloor + 1$, tutaj $p=0,\ s=1$, czyli $W_{\text{out}} = W - f + 1$.

- Wysokość: $70 - 7 + 1 = \textbf{64}$
- Szerokość: $120 - 7 + 1 = \textbf{114}$

**Wynik: 64×114** (zgodne z `out: torch.Size([2, 32, 64, 114])` z wykładu).

---

### 6. Jaki jest kształt tensora wag dla `nn.Conv2d(3, 32, kernel_size=7)`? Dlaczego?

Kształt wag: **`[32, 3, 7, 7]`**, czyli `[out_channels, in_channels, kernel_h, kernel_w]`.

**Dlaczego:** każdy z **32 filtrów wyjściowych** musi przetworzyć wszystkie **3 kanały wejściowe** (RGB), a każdy filtr ma rozmiar **7×7**. Czyli pojedynczy filtr to bryła 3×7×7, a takich filtrów jest 32. Liczba parametrów **nie zależy od rozmiaru obrazu** — to istota współdzielenia wag.

---

### 7. Oblicz liczbę parametrów tej warstwy.

Wzór: $\#\text{params} = (f_h \times f_w \times C_{\text{in}} + 1) \times C_{\text{out}}$ (gdzie $+1$ to bias na każdy filtr).

$$
(7 \times 7 \times 3 + 1) \times 32 = (147 + 1) \times 32 = 148 \times 32 = \textbf{4736}
$$

Dla porównania: gdyby to była warstwa gęsta dla obrazu 70×120×3 (= 25 200 wejść), pojedynczy neuron miałby już 25 200 wag — CNN jest drastycznie oszczędniejsza.

---

### 8. Co to jest współdzielenie wag i dlaczego jest kluczowe dla CNN?

**Współdzielenie wag (*weight sharing*)** oznacza, że **wszystkie neurony danej mapy cech używają tego samego filtra** (tych samych wag i biasu). Filtr przesuwa się po całym obrazie, a nie ma osobnych wag dla każdej pozycji.

**Dlaczego jest kluczowe:**
- **Drastycznie redukuje liczbę parametrów** — niezależną od rozmiaru obrazu (np. 4736 zamiast milionów),
- daje **niezmienniczość względem przesunięć** (*translation invariance*) — cecha wykryta w lewym górnym rogu zostanie też wykryta w prawym dolnym, bo to ten sam filtr,
- zmniejsza ryzyko **przeuczenia** (mniej parametrów do dopasowania).

---

### 9. Po co stosujemy warstwy poolingowe? Czym różni się max od average pooling?

**Cel warstw poolingowych:** zmniejszenie obrazu, aby:
- obniżyć nakłady obliczeniowe,
- zmniejszyć użycie pamięci,
- zmniejszyć liczbę parametrów (**przeciwdziałanie przeuczeniu**),
- wprowadzić niezmienniczość względem małych przesunięć.

**Różnica:**
- **Max pooling** — bierze **maksimum** z okna; zachowuje **najsilniejsze cechy**, daje ostrzejszy obraz, dominuje w praktyce.
- **Average pooling** — bierze **średnią** z okna; uśrednia, daje gładszy, łagodniejszy wynik (np. global average pooling na końcu sieci).

---

### 10. Dlaczego pooling nie ma parametrów do trenowania?

Bo pooling wykonuje **stałą funkcję agregacji** (maksimum lub średnią) na oknie wejścia — nie ma żadnych wag ani biasów do nauczenia. To czysto deterministyczna operacja redukująca rozmiar. W przeciwieństwie do warstwy konwolucyjnej, gdzie filtr to uczone wagi, tutaj „co robić z oknem” jest z góry ustalone.

---

### 11. Opisz typową architekturę CNN do klasyfikacji.

Klasyczny schemat:

```
WEJŚCIE → [Conv → ReLU → (Conv → ReLU) → Pool] × N → Flatten → [Dense → ReLU] × M → Dense(softmax)
```

Charakterystyka:
- na początku **bloki konwolucyjne** (Conv + aktywacja + Pooling), wyłapujące cechy,
- w głąb sieci: **coraz więcej map cech, coraz mniejszy rozmiar przestrzenny** (głębiej = więcej kanałów, mniejsze H×W),
- na końcu **warstwy gęste** (klasyfikator) i wyjście z **softmax** (w PyTorch realizowane przez `CrossEntropyLoss`, więc softmax jest niejawny).

---

### 12. Co charakteryzuje LeNet-5? Jakie aktywacje i pooling stosowała?

**LeNet-5 (1998, Yann LeCun)** — jedna z najlepiej znanych architektur, używana do rozpoznawania **odręcznych cyfr (MNIST)**.

Charakterystyka (typowa dla swojej epoki):
- **Aktywacja: tanh** (nie ReLU, które wtedy nie było jeszcze standardem),
- **Pooling: uśredniający (average)** — nie max pooling,
- **Wyjście: RBF** (funkcje radialne) — dziś zastąpione softmaxem.

Struktura: Wejście 32×32 → C1 (conv 6 map) → S2 (avg pool) → C3 (conv 16 map) → S4 (avg pool) → C5 (conv 120 map) → F6 (gęsta 84) → Wyjście (10 klas).

---

### 13. Jakie innowacje wprowadził AlexNet?

**AlexNet (2012)** — podobny do LeNet-5, ale **większy i głębszy**; wygrał ImageNet 2012, rozpoczynając erę deep learningu. Innowacje:

- **Warstwy konwolucyjne jedna na drugiej** (po raz pierwszy, bez poolingu pomiędzy nimi),
- **Regularyzacja:**
    - **Dropout 50%** na warstwach gęstych (F8, F9),
    - **augmentacja danych**,
- **LRN (Local Response Normalization)** — najmocniej aktywowane neurony „wyłączają” neurony na swojej pozycji w sąsiednich mapach cech → **dywersyfikacja** rozpoznawanych cech,
- (w praktyce także użycie ReLU i trening na GPU).

---

### 14. Jak działa Dropout? Jaki ma typowy zakres p?

**Dropout** (rozwijany w 2012 i 2014):
- W każdym kroku uczenia **każdy neuron z prawdopodobieństwem $p$ zostaje opuszczony** (*dropped out*) — czasowo wyłączony.
- Opuszczony neuron jest **ignorowany tylko w bieżącym kroku** (w następnym może działać).
- W trybie ewaluacji dropout się **wyłącza** (używane są wszystkie neurony).

**Typowy zakres $p$: 10–50%** (węższy w sieciach rekurencyjnych i konwolucyjnych).

**Intuicja:** sieć nie może polegać na pojedynczych neuronach, więc uczy się **redundantnych, odpornych reprezentacji** — działa jak uśrednianie wielu sieci (ensemble), co ogranicza przeuczenie.

---

### 15. Czym jest moduł incepcyjny w GoogLeNet? Po co konwolucje 1×1?

**Moduł incepcyjny (*inception module*)** w GoogLeNet (2014) łączy w jednym bloku **konwolucje o różnych rozmiarach** (1×1, 3×3, 5×5) oraz pooling, działające równolegle, a ich wyniki są łączone. Pozwoliło to zbudować sieć **głębszą od AlexNet, ale z 10× mniejszą liczbą parametrów** (6M vs 60M).

**Po co konwolucje 1×1:**
- **nie wykrywają cech przestrzennych**, ale wykrywają cechy „idąc wgłąb” (po kanałach),
- **zmniejszają liczbę map cech** → redukują wymiarowość (**warstwy bottleneck**), co oszczędza obliczenia,
- para $[1\times1,\ 3\times3]$ lub $[1\times1,\ 5\times5]$ działa jak jedna, silniejsza warstwa konwolucyjna (najpierw redukcja kanałów 1×1, potem właściwa konwolucja).

---

### 16. Co to jest global average pooling i dlaczego pozwala pozbyć się warstw gęstych?

**Global average pooling** to pooling uśredniający redukujący **całą mapę cech do jednej wartości** (z H×W → 1×1) — daje jedną liczbę na każdą mapę cech.

**Dlaczego pozwala pozbyć się warstw gęstych:** w GoogLeNet przed nim rozmiar i tak spada z 224×224 do 7×7, więc dane przestrzenne są już mocno skondensowane. Global avg pool zamienia każdą mapę cech bezpośrednio w cechę, którą można podać do warstwy wyjściowej — **bez kosztownej warstwy Flatten + Dense**. To radykalnie zmniejsza liczbę parametrów i ryzyko przeuczenia.

---

### 17. Wyjaśnij ideę uczenia rezydualnego (ResNet). Dlaczego skip connections pomagają?

**Uczenie rezydualne (*residual learning*, ResNet 2015):** zamiast uczyć warstwę odwzorowania $H(x)$, uczy się ona **reszty** $F(x) = H(x) - x$, a wyjście bloku to:

$$
\text{wyjście} = F(x) + x
$$

gdzie $x$ jest dodawane przez **obejście (*skip / shortcut connection*)**.

**Dlaczego skip connections pomagają:**
- gdy optymalne odwzorowanie jest bliskie **tożsamości**, łatwiej nauczyć się $F(x) \approx 0$ niż $H(x) \approx x$,
- obejścia pozwalają **gradientowi „przeskakiwać” warstwy** → rozwiązują problem **zanikającego gradientu** w bardzo głębokich sieciach,
- dzięki temu można trenować sieci o ponad 100 warstwach, przyspieszając ich uczenie.

---

### 18. Co oznaczają metryki Top-1 i Top-5 accuracy?

- **Top-1 accuracy** — odsetek przypadków, w których **najwyżej oceniona predykcja (#1)** modelu jest poprawną klasą.
- **Top-5 accuracy** — odsetek przypadków, w których **poprawna klasa znajduje się wśród pierwszych pięciu** predykcji o najwyższym prawdopodobieństwie.

Top-5 jest zawsze ≥ Top-1 i bywa stosowane przy dużej liczbie klas (np. 1000 klas ImageNet), gdzie wiele klas jest podobnych — model „prawie trafił”, mając właściwą klasę w czołówce.

---

### 19. Opisz pełny przepływ uczenia transferowego: wymiana głowy, zamrażanie, odmrażanie.

1. **Wczytanie gotowego modelu** wytrenowanego na dużym zbiorze (np. ImageNet) z `torchvision.models`, wraz z jego preprocessingiem (`weights.transforms()`).
2. **Wymiana głowy (klasyfikatora)** — oryginalna warstwa wyjściowa ma np. 1000 wyjść (ImageNet); podmieniamy ją na nową `nn.Linear` z liczbą wyjść równą liczbie naszych klas (np. 102 dla Flowers102). „Model psuje się od głowy.”
3. **Zamrażanie niższych warstw** — ustawiamy `requires_grad = False` dla ekstraktora cech (`model.features`), żeby trenowała się tylko nowa głowa.
4. **Wstępny trening** — przez kilka epok uczy się tylko klasyfikator (szybko osiąga rozsądną dokładność, bo ekstraktor jest gotowy).
5. **Odmrażanie** — gdy model osiągnie rozsądną dokładność, ustawiamy `requires_grad = True` i **dostrajamy całość z mniejszym learning rate**.

---

### 20. Dlaczego najpierw zamrażamy warstwy, a dopiero potem je odmrażamy? Dlaczego z mniejszym LR?

**Najpierw zamrażamy**, bo nowa głowa jest **losowo zainicjalizowana** — na początku generuje duże, chaotyczne gradienty. Gdyby warstwy ekstraktora były odmrożone, **te gradienty zniszczyłyby dobre, wytrenowane wagi** dolnej części sieci.

**Odmrażamy dopiero później**, gdy głowa osiągnie rozsądną dokładność — wtedy gradienty są już „spokojne” i nie ma ryzyka, że zburzą wagi ekstraktora.

**Mniejszy LR przy odmrożeniu**, bo wagi ekstraktora są już prawie optymalne — chcemy je tylko **delikatnie dostroić** do nowego zbioru, a nie gwałtownie zmieniać. Duży LR mógłby je „rozjechać”.

---

### 21. Jak uzyskać preprocessing pasujący do gotowego modelu z torchvision?

Każdy zestaw wag w `torchvision` niesie ze sobą **własny preprocessing**, który najłatwiej pobrać bezpośrednio z obiektu wag metodą **`weights.transforms()`**:

```python
weights = torchvision.models.ConvNeXt_Base_Weights.IMAGENET1K_V1
model = torchvision.models.convnext_base(weights=weights)
preprocess = weights.transforms()
preprocessed_images = preprocess(sample_images_permuted)
# torch.Size([2, 3, 224, 224])
```

Zapewnia to właściwy rozmiar (np. 224×224), normalizację (średnie i odchylenia z ImageNet) itd. — dokładnie taki, jakim model był trenowany. Można też podać `transform=weights.transforms()` przy ładowaniu zbioru danych.

---

### 22. Czym jest IoU? Podaj wzór i interpretację.

**IoU (*Intersection over Union*)** — metryka oceniająca jakość predykcji położenia **ramki** (*bounding box*): stosunek **pola przecięcia** ramki właściwej i przewidzianej do **pola ich sumy**:

$$
\text{IoU} = \frac{\text{Area}(\text{predicted} \cap \text{ground truth})}{\text{Area}(\text{predicted} \cup \text{ground truth})}
$$

**Interpretacja:**
- **IoU = 1** → idealne pokrycie (ramki identyczne),
- **IoU = 0** → brak wspólnej części,
- typowy próg „trafienia” w detekcji: **IoU ≥ 0,5**.

---

### 23. Czym jest objectness w detekcji obiektów?

**Objectness** to dodatkowe wyjście modelu detekcji oceniające **czy w danej ramce w ogóle znajduje się obiekt** (pewność obecności obiektu), niezależnie od jego klasy. Pozwala odróżnić ramki zawierające obiekt od tła. Wartość objectness jest też wykorzystywana przy filtrowaniu i porządkowaniu predykcji (np. w NMS — zostawiamy ramki o wysokim objectness).

---

### 24. Na czym polega problem wielokrotnych detekcji i jak się go rozwiązuje (NMS)?

**Problem:** ten sam obiekt może zostać **wykryty kilka razy** — model zwraca wiele nakładających się ramek dla jednego obiektu (szczególnie przy podejściu sliding window z różnymi rozmiarami ramek).

**Rozwiązanie — Non-Maximum Suppression (NMS):**
1. Wybierz ramkę o **najwyższym objectness** (najwyższej pewności).
2. Usuń wszystkie pozostałe ramki, które **mocno się z nią nakładają** (wysokie IoU, powyżej progu — bo prawdopodobnie dotyczą tego samego obiektu).
3. Powtarzaj dla pozostałych ramek, aż każda zostanie albo wybrana, albo odrzucona.

Efekt: jeden obiekt → jedna ramka.

---

### 25. Czym jest sieć w pełni konwolucyjna (FCN) i jaką ma przewagę?

**Sieć w pełni konwolucyjna (*Fully Convolutional Network, FCN*)** to sieć, w której **warstwy gęste zastąpiono warstwami konwolucyjnymi** (np. konwolucją 1×1 dającą ten sam efekt co warstwa Dense).

**Przewaga:** ponieważ nie ma warstw gęstych (które wymagają stałego rozmiaru wejścia), sieć może **przetwarzać obrazy o dowolnych rozmiarach**. Jest to kluczowe w detekcji i segmentacji, gdzie chcemy zachować informację przestrzenną i pracować na obrazach różnej wielkości.

---

### 26. Jak działa YOLO i czym różni się od podejścia sliding window?

**YOLO (*You Only Look Once*)** wykrywa wszystkie obiekty na obrazie w **jednym przejściu sieci** (*single pass*) — dzieli obraz na siatkę i jednocześnie przewiduje ramki, objectness oraz klasy dla wszystkich obszarów naraz.

**Różnica względem sliding window:**
- **Sliding window** „przesuwa” model po obrazie wielokrotnie, dla różnych pozycji i rozmiarów ramek → **bardzo wolne, redundantne** obliczenia.
- **YOLO** patrzy na obraz **tylko raz** → jest **znacznie szybsze**, nadaje się do **detekcji w czasie rzeczywistym**.

```python
from ultralytics import YOLO
model = YOLO('yolov9m.pt')
results = model(["soccer.jpg", "traffic.jpg"])
# 0: 5 persons, 2 sports balls
# 1: 3 persons, 7 cars, 1 motorcycle, 11 trucks
```

---

### 27. Na czym polega segmentacja semantyczna i jakie jest jej główne wyzwanie?

**Segmentacja semantyczna** polega na **przypisaniu każdemu pikselowi** obrazu klasy obiektu, którego jest częścią — każdy piksel jest „kolorowany” zgodnie ze swoją klasą (np. droga, samochód, pieszy, niebo).

**Główne wyzwanie:** obrazy przechodząc przez CNN **stopniowo tracą rozdzielczość** (przez pooling i stride), a do segmentacji potrzebujemy wyjścia w pełnej rozdzielczości. Rozwiązaniem są architektury **enkoder-dekoder** (np. U-Net) z *transposed convolutions* / *upsampling*, które odtwarzają rozdzielczość, oraz *skip connections* łączące warstwy enkodera z dekoderem.

> **Uwaga:** segmentacja semantyczna nie odróżnia poszczególnych instancji tej samej klasy (dwa samochody = ta sama „kolorowanka”). Rozróżnianie instancji to segmentacja instancji.

---

### 28. Porównaj zadania: klasyfikacja, lokalizacja, detekcja, segmentacja (wyjścia i metryki).

| Zadanie | Wyjście | Typowa metryka |
|---|---|---|
| **Klasyfikacja** | jedna etykieta (klasa) na cały obraz | accuracy, top-5 accuracy |
| **Lokalizacja** | jedna ramka (*bounding box*) + klasa; zadanie **regresyjne** | IoU |
| **Detekcja obiektów** | wiele ramek + klasy + objectness (wiele obiektów naraz) | mAP (mean Average Precision), IoU |
| **Segmentacja semantyczna** | klasa dla **każdego piksela** | mIoU, pixel accuracy |
| **Segmentacja instancji** | osobna maska dla każdego obiektu | mask mAP |

Skala trudności i szczegółowości rośnie od klasyfikacji (co jest na obrazie?) przez lokalizację/detekcję (gdzie to jest?) aż po segmentację (które dokładnie piksele należą do obiektu?).

---

# Rekurencyjne sieci neuronowe (RNN) — notatka egzaminacyjna

**Przedmiot:** Uczenie maszynowe 2025/2026
**Wykładowca:** dr inż. Sebastian Ernst
**Temat wykładu:** Rekurencyjne sieci neuronowe

---

## Spis treści

1. [Przetwarzanie szeregów czasowych](#1-przetwarzanie-szeregów-czasowych)
2. [Przykład: CTA (Chicago Transport Authority)](#2-przykład-cta)
3. [Predykcja przy pomocy sieci neuronowej](#3-predykcja-przy-pomocy-sieci-neuronowej)
4. [Dodatek — teoria spoza prezentacji](#4-dodatek--teoria-spoza-prezentacji)

---

## 1. Przetwarzanie szeregów czasowych

### 1.1. Przetwarzanie szeregów czasowych

Przetwarzanie szeregów czasowych odbywa się **najczęściej w celu przewidywania przyszłości** (*forecasting*).

**Zastosowania:**
- **finanse** (giełda),
- **pojazdy autonomiczne**,
- **sterowanie**,
- **wykrywanie usterek**.

### 1.2. Rekurencyjne neurony

Sieć rekurencyjna wygląda podobnie do zwykłej sieci neuronowej, z jedną kluczową różnicą: **neuron otrzymuje sygnały także ze swoich poprzednich wyjść** — jest to połączenie zwrotne (*recurrent connection*).

**Kluczowe pojęcia:**
- **Krok czasowy $t$ (= ramka, *frame*)** — pojedyncza jednostka czasu/sekwencji, dla której obliczane jest wyjście neuronu.
- **Rozciągnięcie w czasie (*unrolling through time*)** — sposób przedstawienia sieci rekurencyjnej jako rozwiniętego w czasie ciągu „kopii” tego samego neuronu, połączonych kolejnymi krokami czasowymi. Ułatwia to zrozumienie przepływu informacji i propagacji wstecznej.

**Wzór wyjścia pojedynczego neuronu rekurencyjnego:**

$$
y_{(t)} = \phi\left(\mathbf{x}_{(t)}^T \mathbf{w}_x + y_{(t-1)} \cdot w_y + b\right)
$$

Gdzie:
- $\mathbf{x}_{(t)}$ — wejście w kroku $t$,
- $y_{(t-1)}$ — wyjście (sprzężenie zwrotne) z poprzedniego kroku,
- $\mathbf{w}_x$ — wagi wejścia, $w_y$ — waga połączenia rekurencyjnego,
- $\phi$ — funkcja aktywacji (typowo tanh).

### 1.3. Warstwa rekurencyjnych neuronów

Cała warstwa rekurencyjna **otrzymuje wektor wejściowy oraz wektor wyjściowy z poprzedniego kroku czasowego**. **Każdy neuron ma dwa zestawy wag**: jeden dla wejścia $\mathbf{x}_{(t)}$, drugi dla poprzedniego wyjścia $\mathbf{y}_{(t-1)}$.

**Wzór wyjścia warstwy** (postać macierzowa, dla wsadu instancji):

$$
\mathbf{Y}_{(t)} = \phi\left(\mathbf{X}_{(t)} \mathbf{W}_x + \mathbf{Y}_{(t-1)} \mathbf{W}_y + \mathbf{b}\right)
$$

Można to zapisać w skróconej postaci, łącząc oba wejścia (konkatenacja) i wagi:

$$
\mathbf{Y}_{(t)} = \phi\left(\left[\mathbf{X}_{(t)}\ \mathbf{Y}_{(t-1)}\right] \mathbf{W} + \mathbf{b}\right), \qquad
\mathbf{W} = \begin{bmatrix}\mathbf{W}_x \\ \mathbf{W}_y\end{bmatrix}
$$

### 1.4. Komórki pamięci

Ponieważ **wyjście w chwili $t$ zależy od wejść w poprzednich chwilach**, neuron rekurencyjny posiada **rodzaj pamięci**. Taki element nazywamy **komórką pamięci** (*memory cell*).

**Kluczowe właściwości:**
- **Typowo pamięć jest krótka** (ok. **10 kroków** czasowych) — podstawowe komórki rekurencyjne słabo radzą sobie z długoterminowymi zależnościami.
- **Wyjście z komórki może nie być tożsame z jej stanem wewnętrznym** — najprostsza komórka ma $h_{(t)} = y_{(t)}$ (stan = wyjście), ale bardziej złożone komórki (np. LSTM) rozróżniają **stan wewnętrzny** (pamięć) od **wyjścia** (to, co jest udostępniane na zewnątrz / kolejnej warstwie).

Ogólny zapis: stan $h_{(t)}$ i wyjście $y_{(t)}$ są funkcjami stanu poprzedniego i wejścia bieżącego:

$$
h_{(t)} = f\left(h_{(t-1)},\ \mathbf{x}_{(t)}\right), \qquad y_{(t)} = g\left(h_{(t)}\right)
$$

### 1.5. Sekwencje wejściowe i wyjściowe

RNN można konfigurować do różnych typów zadań w zależności od relacji wejście↔wyjście:

| Typ | Schemat | Przykład zastosowania |
|---|---|---|
| **Sequence-to-sequence** | wiele wejść → wiele wyjść (synchronicznie) | prognoza giełdowa krok po kroku |
| **Sequence-to-vector** | wiele wejść → jedno wyjście (na końcu) | analiza sentymentu recenzji |
| **Vector-to-sequence** | jedno wejście → wiele wyjść | generowanie podpisu obrazu (*image captioning*) |
| **Encoder–Decoder** | seq-to-vector (encoder) + vector-to-seq (decoder) | tłumaczenie maszynowe |

> **Encoder-Decoder** jest skuteczniejszy niż prosty seq-to-seq w zadaniach typu tłumaczenie, ponieważ pozwala „zobaczyć” całą sekwencję wejściową, zanim zacznie się generować wyjście (unika tłumaczenia „na bieżąco”, słowo po słowie, bez kontekstu).

### 1.6. Uczenie RNN

**BPTT (*Backpropagation Through Time*)** — algorytm uczenia sieci rekurencyjnych:

$$
\text{unrolling} + \text{zwykła propagacja wsteczna} = \textbf{BPTT}
$$

**Sposób działania:**
1. Sieć jest **rozwijana w czasie** (*unrolled*) — tworzony jest „rozciągnięty” graf obliczeniowy, gdzie każdy krok czasowy to kolejna „kopia” tej samej komórki (ze współdzielonymi wagami).
2. Wykonuje się **zwykłą propagację wsteczną** przez ten rozwinięty graf — gradienty są propagowane od ostatniego kroku czasowego do pierwszego.
3. Gradienty dla poszczególnych kroków czasowych są **sumowane**, bo wagi są współdzielone między krokami.

> Mimo że BPTT jest formalnie tym samym co backpropagation, praktycznie pojawia się tu specyficzny problem: **bardzo głęboki rozwinięty graf** (tyle „warstw” co kroków czasowych) prowadzi do **niestabilności gradientu** (patrz sekcja 3.11).

---

## 2. Przykład: CTA

### 2.1. Przygotowanie danych

Wykorzystujemy przykład **Chicago Transport Authority (CTA)** — dane o dziennej liczbie pasażerów autobusów i kolei.

```python
from pathlib import Path
import pandas as pd
import tarfile
import urllib.request
import matplotlib.pyplot as plt

def download_and_extract_ridership_data():
    tarball_path = Path("datasets/ridership.tgz")
    if not tarball_path.is_file():
        Path("datasets").mkdir(parents=True, exist_ok=True)
        url = "https://github.com/ageron/data/raw/main/ridership.tgz"
        urllib.request.urlretrieve(url, tarball_path)
        with tarfile.open(tarball_path) as housing_tarball:
            housing_tarball.extractall(path="datasets", filter="data")

download_and_extract_ridership_data()
```

### 2.2. Czyszczenie danych

```python
import pandas as pd
from pathlib import Path

path = Path("datasets/ridership/CTA_-_Ridership_-_Daily_Boarding_Totals.csv")
df = pd.read_csv(path, parse_dates=["service_date"])
df.columns = ["date", "day_type", "bus", "rail", "total"]
df = df.sort_values("date").set_index("date")
df = df.drop("total", axis=1)
df = df.drop_duplicates()
```

**Kroki czyszczenia:**
- parsowanie kolumny daty (`parse_dates`),
- zmiana nazw kolumn na czytelne,
- sortowanie po dacie i ustawienie jej jako **indeksu** (kluczowe dla operacji na szeregach czasowych w pandas — pozwala na wycinanie po dacie, np. `df["2019-03":"2019-05"]`),
- usunięcie kolumny `total` (redundantna — to suma `bus + rail`),
- usunięcie duplikatów.

### 2.3. Wizualizacja

```python
df["2019-03":"2019-05"].plot(grid=True, marker=".", figsize=(11, 3.5))
```

**Analiza sezonowości — porównanie z przesunięciem o tydzień:**

```python
diff_7 = df[["bus", "rail"]].diff(7)["2019-03":"2019-05"]
fig, axs = plt.subplots(2, 1, sharex=True, figsize=(8, 3))
df.plot(ax=axs[0], legend=False, marker=".")
df.shift(7).plot(ax=axs[0], grid=True, legend=False, linestyle=":")
diff_7.plot(ax=axs[1], grid=True, marker=".")
axs[0].set_ylim([170_000, 900_000])
plt.show()
```

> **`diff(7)`** liczy różnicę między dniem bieżącym a tym samym dniem **tydzień wcześniej** — uwidacznia silną **sezonowość tygodniową** (np. ruch w poniedziałki podobny do poprzednich poniedziałków). `shift(7)` przesuwa szereg o 7 dni, by nałożyć go na oryginał i zobaczyć podobieństwo wizualnie.

### 2.4. Naiwny baseline

**Najprostszy możliwy model prognostyczny:** załóż, że wartość dzisiaj = wartość z tego samego dnia tydzień temu.

```python
diff_7.abs().mean()
# bus     43915.608696
# rail    42143.271739
# dtype: float64
```

To jest **MAE** (*Mean Absolute Error*) naiwnego modelu — błąd ten sam, co już obliczone `diff_7`.

**Błąd względny (MAPE):**

```python
targets = df[["bus", "rail"]]["2019-03":"2019-05"]
(diff_7 / targets).abs().mean()
# bus     0.082938
# rail    0.089948
# dtype: float64
```

> Tym sposobem wprowadziliśmy metrykę **MAPE** (*mean absolute percentage error*), która **znacznie lepiej oddaje poziom błędu** niż MAE w jednostkach absolutnych — łatwiej zinterpretować „błąd 8%” niż „błąd 44 000 pasażerów”, bez znajomości skali zmiennej.

$$
\text{MAPE} = \frac{1}{n}\sum_{i=1}^{n} \left|\frac{y_i - \hat{y}_i}{y_i}\right|
$$

### 2.5. ARMA: mniej naiwny baseline

Drugi, lepszy punkt odniesienia: klasyczny model statystyczny **ARIMA / SARIMA** (z sezonowością):

```python
from statsmodels.tsa.arima.model import ARIMA

origin, start_date, end_date = "2019-01-01", "2019-03-01", "2019-05-31"
time_period = pd.date_range(start_date, end_date)
rail_series = df.loc[origin:end_date]["rail"].asfreq("D")
y_preds = []
for today in time_period.shift(-1):
    model = ARIMA(rail_series[origin:today],
                  order=(1, 0, 0),
                  seasonal_order=(0, 1, 1, 7))
    model = model.fit()
    y_pred = model.forecast().iloc[0]
    y_preds.append(y_pred)
y_preds = pd.Series(y_preds, index=time_period)
mae = (y_preds - rail_series[time_period]).abs().mean()
mae
# np.float64(32040.72009292839)
```

**Wyjaśnienie parametrów modelu:**
- **`order=(1, 0, 0)`** — część **ARIMA(p, d, q)**: $p=1$ (jeden człon autoregresyjny AR), $d=0$ (brak różnicowania, bo różnicowanie sezonowe poniżej i tak je zapewnia), $q=0$ (brak członu MA).
- **`seasonal_order=(0, 1, 1, 7)`** — część sezonowa **SARIMA(P, D, Q, s)**: $P=0$, $D=1$ (sezonowe różnicowanie — usuwa sezonowość tygodniową), $Q=1$ (jeden sezonowy człon MA), $s=7$ (okres sezonowości = 7 dni).
- Model jest **trenowany na nowo dla każdego dnia** (*rolling forecast*) — uczy się na danych do „dzisiaj” i prognozuje „jutro”.

**Wynik:** MAE ≈ 32 041 — **lepszy** niż naiwny baseline (≈ 42 143 dla `rail`). To dobry punkt odniesienia, z którym porównamy sieci neuronowe.

---

## 3. Predykcja przy pomocy sieci neuronowej

### 3.1. Dataset dla szeregów czasowych

Aby nauczyć sieć neuronową na szeregu czasowym, dzielimy go na **okna** (*windows*): fragment historii o ustalonej długości jako wejście, a wartość bezpośrednio po nim jako cel (etykieta):

```python
class TimeSeriesDataset(torch.utils.data.Dataset):
    def __init__(self, series, window_length):
        self.series = series
        self.window_length = window_length

    def __len__(self):
        return len(self.series) - self.window_length

    def __getitem__(self, idx):
        if idx >= len(self):
            raise IndexError("dataset index out of range")
        end = idx + self.window_length  # 1st index after window
        window = self.series[idx : end]
        target = self.series[end]
        return window, target
```

**Wyjaśnienie:**
- `__len__` — liczba możliwych okien to długość szeregu minus długość okna (ostatnie okno nie może wyjść za koniec szeregu).
- `__getitem__(idx)` — zwraca parę: **okno** (`window_length` kolejnych wartości) jako wejście oraz **kolejną wartość** (bezpośrednio po oknie) jako cel.

### 3.2. Próba generalna

Test działania klasy na małym przykładzie:

```python
my_series = torch.tensor([[0], [1], [2], [3], [4], [5]])
my_dataset = TimeSeriesDataset(my_series, window_length=3)
for window, target in my_dataset:
    print("Window:", window, " Target:", target)
# Window: tensor([[0],[1],[2]])  Target: tensor([3])
# Window: tensor([[1],[2],[3]])  Target: tensor([4])
# Window: tensor([[2],[3],[4]])  Target: tensor([5])
```

> Dla szeregu `[0,1,2,3,4,5]` i okna o długości 3 otrzymujemy 3 przykłady uczące — przesuwane okno „ślizga się” po szeregu o jeden krok na raz.

### 3.3. DataLoader dla datasetu

```python
from torch.utils.data import DataLoader
torch.manual_seed(0)
my_loader = DataLoader(my_dataset, batch_size=2, shuffle=True)
for X, y in my_loader:
    print("X:", X, " y:", y)
# X: tensor([[[0],[1],[2]], [[2],[3],[4]]])  y: tensor([[3],[5]])
# X: tensor([[[1],[2],[3]]])  y: tensor([[4]])
```

> Standardowy `DataLoader` automatycznie **grupuje przykłady w mini-batche** i je **przemieszuje** (`shuffle=True`) — mimo że dane są czasowe, kolejność próbek w trenowaniu nie ma znaczenia (każda próbka to niezależne „okno" historii ze swoim celem). Kształt batcha: `[batch_size, window_length, n_features]`.

### 3.4. Ładujemy dane CTA

```python
rail_train = torch.FloatTensor(df[["rail"]]["2016-01":"2018-12"].values / 1e6)
rail_valid = torch.FloatTensor(df[["rail"]]["2019-01":"2019-05"].values / 1e6)
rail_test  = torch.FloatTensor(df[["rail"]]["2019-06":].values / 1e6)
window_length = 56
```

> **Dzielenie po czasie, nie losowo!** Zbiór treningowy to dane **najwcześniejsze** (2016–2018), walidacyjny i testowy to dane **późniejsze** — bo w prognozowaniu szeregów czasowych nie wolno uczyć się na przyszłości, by przewidywać przeszłość (wyciek danych / *data leakage*).
>
> **Skalowanie `/1e6`** — normalizacja wartości (liczby pasażerów w milionach) do mniejszej skali, co stabilizuje uczenie sieci.
>
> **`window_length = 56`** — okno 8 tygodni (56 dni) historii używane do przewidzenia następnego dnia.

```python
train_set = TimeSeriesDataset(rail_train, window_length)
train_loader = DataLoader(train_set, batch_size=32, shuffle=True)
valid_set = TimeSeriesDataset(rail_valid, window_length)
valid_loader = DataLoader(valid_set, batch_size=32)
test_set = TimeSeriesDataset(rail_test, window_length)
test_loader = DataLoader(test_set, batch_size=32)
```

### 3.5. Próba: MLP

Najpierw — baseline w postaci **zwykłego MLP** (bez rekurencji), traktujący okno jak płaski wektor cech:

```python
import torch.nn as nn
import torchmetrics

torch.manual_seed(42)
model = nn.Sequential(nn.Flatten(), nn.Linear(window_length, 1)).to(device)
loss_fn = nn.HuberLoss()
optimizer = torch.optim.SGD(model.parameters(), lr=0.003, momentum=0.9)
metric = torchmetrics.MeanAbsoluteError().to(device)

history = train(model, optimizer, loss_fn, metric, train_loader,
                valid_loader, n_epochs=50, quiet=True)
```

**Wyjaśnienie:**
- `nn.Flatten()` + `nn.Linear(window_length, 1)` — to po prostu **regresja liniowa** na 56 wartościach historii (model nie wykorzystuje żadnej struktury sekwencyjnej, traktuje okno jak zwykły wektor cech).
- **`nn.HuberLoss()`** — funkcja straty Hubera: kwadratowa dla małych błędów, liniowa dla dużych — odporna na outliery, częsty wybór w regresji szeregów czasowych.
- `train(...)` to funkcja z poprzedniego wykładu (rozszerzona o `patience`/`factor` do harmonogramowania LR, używana też w `fit_and_evaluate` poniżej).

### 3.6. Wyniki dla MLP

```python
evaluate_tm(model, valid_loader, metric).item() * 1e6
# 37725.720554590225
```

> Mnożymy przez `1e6`, by **odwrócić wcześniejsze skalowanie** i porównać błąd w oryginalnych jednostkach (liczbie pasażerów). **MAE ≈ 37 726** — lepiej niż naiwny baseline (≈ 42 143), ale gorzej niż ARIMA (≈ 32 041).

### 3.7. Prosty RNN, budowany ręcznie

Aby zrozumieć, co dzieje się „pod maską" w warstwie RNN, budujemy ją **ręcznie**, używając tylko `nn.Linear` i `nn.Tanh` w pętli:

```python
class SimpleRnnModel(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.hidden_size = hidden_size
        self.memory_cell = nn.Sequential(
            nn.Linear(input_size + hidden_size, hidden_size),
            nn.Tanh()
        )
        self.output = nn.Linear(hidden_size, output_size)

    def forward(self, X):
        batch_size, window_length, dimensionality = X.shape
        X_time_first = X.transpose(0, 1)
        H = torch.zeros(batch_size, self.hidden_size, device=X.device)
        for X_t in X_time_first:
            XH = torch.cat((X_t, H), dim=1)
            H = self.memory_cell(XH)
        return self.output(H)

torch.manual_seed(42)
model = SimpleRnnModel(input_size=1, hidden_size=32, output_size=1).to(device)
```

**Wyjaśnienie krok po kroku:**
- **`memory_cell`** — to jest właśnie wzór $\phi(\mathbf{x}_{(t)}\mathbf{W}_x + h_{(t-1)}\mathbf{W}_h + b)$ zaimplementowany jako jedna warstwa `Linear` przyjmująca **skonkatenowane** wejście i poprzedni stan, po której następuje `Tanh`.
- **`X.transpose(0, 1)`** — zmienia kształt z `[batch, window_length, features]` na `[window_length, batch, features]`, by łatwo iterować **po krokach czasowych** w pętli `for`.
- **`H = torch.zeros(...)`** — **stan początkowy** $h_0 = \mathbf{0}$ (standardowa inicjalizacja stanu RNN).
- **Pętla `for X_t in X_time_first`** — to dosłownie **rozwijanie w czasie (unrolling)**: w każdej iteracji łączymy bieżące wejście $X_t$ ze stanem $H$ z poprzedniego kroku i przepuszczamy przez tę samą (współdzieloną) komórkę pamięci.
- **`self.output(H)`** — finalny stan po przejściu całego okna jest mapowany na predykcję (warstwa wyjściowa, sequence-to-vector).

**Funkcja pomocnicza do trenowania i oceny:**

```python
def fit_and_evaluate(model, train_loader, valid_loader, lr, n_epochs=50,
                     patience=20, factor=0.1):
    loss_fn = nn.HuberLoss()
    optimizer = torch.optim.SGD(model.parameters(), lr=lr, momentum=0.95)
    metric = torchmetrics.MeanAbsoluteError().to(device)
    history = train(model, optimizer, loss_fn, metric,
                    train_loader, valid_loader, n_epochs=n_epochs,
                    patience=patience, factor=factor, quiet=True)
    return min(history["valid_metrics"]) * 1e6
```

> Funkcja standaryzuje proces trenowania kolejnych modeli RNN w wykładzie — zawsze `HuberLoss`, SGD z `momentum=0.95`, a `patience`/`factor` sugerują wewnętrzne użycie schedulera LR typu `ReduceLROnPlateau` w funkcji `train`. Zwraca **najlepszy** (minimalny) błąd walidacyjny z całego treningu, przeskalowany do oryginalnych jednostek.

```python
torch.manual_seed(42)
univar_model = SimpleRnnModel(input_size=1, hidden_size=32, output_size=1)
univar_model = univar_model.to(device)
fit_and_evaluate(univar_model, train_loader, valid_loader, lr=0.05, n_epochs=50)
# 30511.027202010155
```

> **MAE ≈ 30 511** — **lepiej niż MLP (37 726) i niż ARIMA (32 041)!** Prosty, ręcznie zbudowany RNN, który wykorzystuje strukturę sekwencyjną, bije zarówno statystyczny baseline, jak i sieć bez pamięci.

### 3.8. To samo, ale z wykorzystaniem warstw nn.RNN

PyTorch dostarcza gotową, zoptymalizowaną warstwę **`nn.RNN`**, realizującą tę samą logikę bez ręcznego pisania pętli:

```python
class SimpleRnnModel(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.rnn = nn.RNN(input_size, hidden_size, batch_first=True)
        self.output = nn.Linear(hidden_size, output_size)

    def forward(self, X):
        outputs, last_state = self.rnn(X)
        return self.output(outputs[:, -1])
```

**Wyjaśnienie:**
- **`nn.RNN(input_size, hidden_size, batch_first=True)`** — w jednym wywołaniu realizuje całe rozwinięcie w czasie (wewnętrznie analogicznie do naszej ręcznej pętli, ale zoptymalizowane).
- **`batch_first=True`** — mówi, że wejście ma kształt `[batch, sequence, features]` (a nie domyślny `[sequence, batch, features]`).
- **`outputs`** — wyjścia ze **wszystkich** kroków czasowych, kształt `[batch, sequence, hidden_size]`.
- **`last_state`** — stan ukryty po **ostatnim** kroku (przydatny np. do inicjalizacji kolejnej warstwy RNN).
- **`outputs[:, -1]`** — bierzemy wyjście tylko z **ostatniego** kroku czasowego (sequence-to-vector) i mapujemy je na predykcję.

```python
# wynik treningu:
# 30659.515410661697
```

> Wynik (≈30 660) **praktycznie identyczny** z ręczną implementacją (≈30 511) — to potwierdza, że obie wersje realizują tę samą matematykę. `nn.RNN` jest jednak szybsza (zoptymalizowana implementacja niskopoziomowa) i prostsza w użyciu.

### 3.9.–3.10. Głębokie RNN w PyTorch

**Głębokie RNN (*deep RNN*)** to stos kilku warstw rekurencyjnych jedna na drugiej — wyjścia jednej warstwy rekurencyjnej (dla wszystkich kroków czasowych) stają się wejściami dla następnej warstwy rekurencyjnej.

```python
class DeepRnnModel(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.rnn = nn.RNN(input_size, hidden_size, num_layers=3, batch_first=True)
        self.output = nn.Linear(hidden_size, output_size)

    def forward(self, X):
        outputs, last_state = self.rnn(X)
        return self.output(outputs[:, -1])
```

> **`num_layers=3`** — jednym argumentem tworzymy 3 ułożone warstwy RNN. `nn.RNN` robi to za nas automatycznie (przekazując wyjście jednej warstwy jako wejście kolejnej), bez konieczności ręcznego stackowania.

```python
# wynik treningu:
# 31317.614018917084
```

> **MAE ≈ 31 318** — w tym konkretnym przykładzie głębszy model **nie poprawił** wyniku względem jednowarstwowego RNN (30 511–30 660). To pokazuje, że **głębsze nie zawsze znaczy lepiej** — dla prostych zadań prognostycznych jedna warstwa rekurencyjna może być wystarczająca, a dodatkowa głębokość zwiększa ryzyko przeuczenia lub trudności w optymalizacji.

### 3.11. Przetwarzanie długich sekwencji

Dla **długich sekwencji** rozwinięta (*unrolled*) sieć RNN robi się **bardzo głęboka** (tyle „warstw”, co kroków czasowych), co prowadzi do typowych problemów głębokich sieci:

- **Niestabilność gradientu** — gradienty mogą **zanikać** lub **eksplodować** podczas propagacji przez wiele kroków czasowych (analogicznie do problemu w bardzo głębokich sieciach feedforward, patrz wykład o uczeniu sieci).
- **„Zapominanie” początku sekwencji** — po pewnym czasie sieć **zapomina** informacje z wcześniejszych kroków, ponieważ wpływ odległych wejść na stan końcowy zanika wykładniczo.
- **Rozwiązanie:** istnieją rodzaje komórek **z pamięcią długoterminową** — **LSTM** i **GRU**.

### 3.12. Komórki LSTM

**LSTM (*Long Short-Term Memory*)** — komórka rekurencyjna zaprojektowana specjalnie do radzenia sobie z **długoterminowymi zależnościami**.

**Kluczowa różnica względem prostego RNN:** stan jest **rozdzielony na dwa wektory**:
- $\mathbf{h}_{(t)}$ — **stan krótkoterminowy** (*short-term state*), odpowiada wyjściu komórki,
- $\mathbf{c}_{(t)}$ — **stan długoterminowy** (*long-term state, cell state*), pamięć przenoszona przez wiele kroków.

**Cztery warstwy wewnątrz komórki LSTM:**
- **Główna warstwa** — produkuje $\mathbf{g}_{(t)}$ na podstawie aktualnego wejścia i poprzedniego stanu ukrytego (kandydat na nową informację do zapamiętania),
- **bramka zapominająca** $\mathbf{f}_{(t)}$ (*forget gate*) — decyduje, co usunąć ze stanu długoterminowego,
- **bramka wejściowa** $\mathbf{i}_{(t)}$ (*input gate*) — decyduje, którą część $\mathbf{g}_{(t)}$ dodać do stanu,
- **bramka wyjściowa** $\mathbf{o}_{(t)}$ (*output gate*) — decyduje, która część stanu $\mathbf{c}_{(t)}$ trafia na wyjście $\mathbf{h}_{(t)}$.

**Wzory komórki LSTM** (standardowe równania, $\sigma$ = sigmoid):

$$
\mathbf{f}_{(t)} = \sigma\left(\mathbf{W}_{xf}^T\mathbf{x}_{(t)} + \mathbf{W}_{hf}^T\mathbf{h}_{(t-1)} + b_f\right)
$$
$$
\mathbf{i}_{(t)} = \sigma\left(\mathbf{W}_{xi}^T\mathbf{x}_{(t)} + \mathbf{W}_{hi}^T\mathbf{h}_{(t-1)} + b_i\right)
$$
$$
\mathbf{o}_{(t)} = \sigma\left(\mathbf{W}_{xo}^T\mathbf{x}_{(t)} + \mathbf{W}_{ho}^T\mathbf{h}_{(t-1)} + b_o\right)
$$
$$
\mathbf{g}_{(t)} = \tanh\left(\mathbf{W}_{xg}^T\mathbf{x}_{(t)} + \mathbf{W}_{hg}^T\mathbf{h}_{(t-1)} + b_g\right)
$$
$$
\mathbf{c}_{(t)} = \mathbf{f}_{(t)} \otimes \mathbf{c}_{(t-1)} + \mathbf{i}_{(t)} \otimes \mathbf{g}_{(t)}
$$
$$
\mathbf{h}_{(t)} = \mathbf{y}_{(t)} = \mathbf{o}_{(t)} \otimes \tanh\left(\mathbf{c}_{(t)}\right)
$$

(gdzie $\otimes$ to mnożenie element-wise, *Hadamard product*)

> **Intuicja:** bramka zapominająca „kasuje” niepotrzebną starą pamięć, bramka wejściowa „dopisuje” nowe istotne informacje, a bramka wyjściowa filtruje, co z aktualnej pamięci jest istotne „na teraz”. Dzięki ścieżce $\mathbf{c}_{(t)} = \mathbf{f}_{(t)} \otimes \mathbf{c}_{(t-1)} + \dots$ (sumowanie, nie tylko mnożenie przez wagi i funkcję aktywacji) gradient może **płynąć przez wiele kroków bez zanikania** — to jest klucz do rozwiązania problemu długoterminowych zależności (podobnie jak skip connections w ResNet).

### 3.13. LSTM w PyTorch

```python
class LstmModel(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.rnn = nn.LSTM(input_size, hidden_size, batch_first=True)
        self.output = nn.Linear(hidden_size, output_size)

    def forward(self, X):
        outputs, last_state = self.rnn(X)
        return self.output(outputs[:, -1])
```

> Struktura **identyczna** jak dla `nn.RNN` — wystarczyło podmienić `nn.RNN` na `nn.LSTM`. `last_state` dla LSTM to **para** $(\mathbf{h}_{(T)}, \mathbf{c}_{(T)})$ (stan krótko- i długoterminowy), a nie jeden tensor.

```python
torch.manual_seed(42)
lstm_model = LstmModel(input_size=1, hidden_size=32, output_size=1)
lstm_model = lstm_model.to(device)
fit_and_evaluate(lstm_model, train_loader, valid_loader, lr=0.05, n_epochs=50)
# 121942.86286830902
```

> **MAE ≈ 121 943 — znacznie gorszy wynik!** LSTM ma **więcej parametrów** (4 zestawy wag zamiast 1) i jest **trudniejszy do wytrenowania** przy tych samych hiperparametrach (lr=0,05, 50 epok) — może potrzebować innego learning rate, dłuższego treningu lub innej inicjalizacji. To ważna lekcja praktyczna: **bardziej złożony model nie gwarantuje lepszego wyniku**, szczególnie gdy zadanie (prognoza krótkiej, 56-dniowej historii) nie wymaga bardzo długiej pamięci, a hiperparametry nie zostały do niego dostrojone.

### 3.14. Komórki GRU

**GRU (*Gated Recurrent Unit*)** — **uproszczona wersja LSTM**, popularna alternatywa o mniejszej liczbie parametrów.

**Kluczowe uproszczenia względem LSTM:**
- **Stan długo- i krótkoterminowy połączone w jeden wektor** $\mathbf{h}_{(t)}$ (nie ma rozdzielenia na $h$ i $c$).
- **Tylko jedna bramka** $\mathbf{z}_{(t)}$ dla zapominania i wejścia łącznie — **kasuje miejsce w stanie przed zapisaniem nowej wartości** (zamiast dwóch niezależnych bramek `forget` i `input`, jedna bramka kontroluje oba procesy: ile starego stanu zostawić, a ile nowego dopisać).
- **Na wyjściu zawsze pełny wektor stanu**, ale bramka $\mathbf{r}_{(t)}$ decyduje, **jaka część stanu trafia do warstwy głównej** $\mathbf{g}_{(t)}$ (czyli reguluje dostęp poprzedniego stanu do obliczenia kandydata na nowy stan).

**Wzory komórki GRU:**

$$
\mathbf{z}_{(t)} = \sigma\left(\mathbf{W}_{xz}^T\mathbf{x}_{(t)} + \mathbf{W}_{hz}^T\mathbf{h}_{(t-1)} + b_z\right)
$$
$$
\mathbf{r}_{(t)} = \sigma\left(\mathbf{W}_{xr}^T\mathbf{x}_{(t)} + \mathbf{W}_{hr}^T\mathbf{h}_{(t-1)} + b_r\right)
$$
$$
\mathbf{g}_{(t)} = \tanh\left(\mathbf{W}_{xg}^T\mathbf{x}_{(t)} + \mathbf{W}_{hg}^T\left(\mathbf{r}_{(t)} \otimes \mathbf{h}_{(t-1)}\right) + b_g\right)
$$
$$
\mathbf{h}_{(t)} = \mathbf{z}_{(t)} \otimes \mathbf{h}_{(t-1)} + \left(1 - \mathbf{z}_{(t)}\right) \otimes \mathbf{g}_{(t)}
$$

**Użycie w PyTorch** (analogicznie do `nn.RNN` / `nn.LSTM`):

```python
class GruModel(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.rnn = nn.GRU(input_size, hidden_size, batch_first=True)
        self.output = nn.Linear(hidden_size, output_size)

    def forward(self, X):
        outputs, last_state = self.rnn(X)
        return self.output(outputs[:, -1])
```

> Dla GRU `last_state` to **jeden** tensor (jak w `nn.RNN`), bo nie ma rozdzielonego stanu $c$.

---

## 4. Dodatek — teoria spoza prezentacji

### 4.1. Podsumowanie porównania architektur na przykładzie CTA

| Model | MAE (walidacja) | Komentarz |
|---|---|---|
| Naiwny baseline | ≈ 42 143 | wartość = ta sama co tydzień temu |
| ARIMA/SARIMA | ≈ 32 041 | klasyczny model statystyczny |
| MLP | ≈ 37 726 | brak struktury sekwencyjnej |
| Prosty RNN (ręczny) | ≈ 30 511 | **najlepszy w tym zestawie** |
| `nn.RNN` | ≈ 30 660 | potwierdza poprawność implementacji ręcznej |
| Głębokie RNN (3 warstwy) | ≈ 31 318 | głębiej ≠ lepiej dla tego zadania |
| LSTM | ≈ 121 943 | niedotrenowany / wymaga innych hiperparametrów |

> **Wniosek praktyczny:** dla krótkich okien (56 dni) i prostych zależności prosty RNN może bić zarówno bardziej złożone architektury (LSTM), jak i klasyczne metody statystyczne (ARIMA) — **złożoność modelu trzeba dopasować do złożoności zadania i starannie dostroić hiperparametry**.

### 4.2. Problem zanikającego gradientu w RNN — szczegóły matematyczne

Gradient propagowany przez $T$ kroków czasowych w prostym RNN zawiera iloczyn $T$ czynników (po reguł łańcuchowej):

$$
\frac{\partial h_{(T)}}{\partial h_{(0)}} = \prod_{t=1}^{T} \frac{\partial h_{(t)}}{\partial h_{(t-1)}}
$$

Jeśli każdy czynnik ma wartość $< 1$, iloczyn **zanika wykładniczo** z $T$ (zanikający gradient); jeśli $>1$ — **eksploduje**. To dlaczego BPTT dla długich sekwencji jest niestabilny, a wpływ odległych wejść zanika.

### 4.3. Dlaczego LSTM/GRU radzą sobie lepiej

Kluczowa różnica: stan długoterminowy $c_{(t)}$ w LSTM jest aktualizowany przez **sumowanie** (przez bramkę zapominającą $f_{(t)} \otimes c_{(t-1)}$), nie tylko przez mnożenie przez wagi i nieliniową aktywację. To tworzy **„ścieżkę gradientu"** podobną do skip connections w ResNet — gradient może płynąć przez wiele kroków bez systematycznego zanikania, jeśli bramka zapominająca jest bliska 1.

### 4.4. Dwukierunkowe RNN (Bidirectional RNN)

Standardowe RNN przetwarza sekwencję tylko w jednym kierunku (od początku do końca). **Bidirectional RNN** przetwarza sekwencję w **obu kierunkach** (do przodu i do tyłu) i łączy wyniki — przydatne, gdy cała sekwencja jest dostępna z góry (np. klasyfikacja tekstu), ale **nieprzydatne do prognozowania w czasie rzeczywistym** (bo wymaga znajomości przyszłości).

```python
nn.RNN(input_size, hidden_size, bidirectional=True, batch_first=True)
```

### 4.5. Mechanizm uwagi (Attention) — wprowadzenie

Problem RNN/LSTM: pojedynczy wektor stanu musi „skompresować" całą historię — to wąskie gardło dla długich sekwencji. **Mechanizm uwagi (*attention*)** pozwala modelowi „zaglądać" bezpośrednio do wybranych wcześniejszych kroków czasowych zamiast polegać wyłącznie na skompresowanym stanie. To fundament architektury **Transformer** (2017), która w wielu zadaniach **zastąpiła RNN/LSTM** (zwłaszcza w NLP).

### 4.6. Sliding window vs. cała historia — trade-off

| Podejście | Zaleta | Wada |
|---|---|---|
| Krótkie okno (np. 56 dni) | szybki trening, mniej parametrów | może nie uchwycić długoterminowych trendów |
| Długie okno / cała historia | więcej kontekstu | wolniejszy trening, ryzyko niestabilności gradientu w RNN |
| LSTM/GRU + długie okno | kompromis — radzą sobie z dłuższą pamięcią | więcej parametrów, trudniejszy trening |

### 4.7. Multivariate time series (szeregi wielowymiarowe)

W prezentacji modelowano tylko `rail` (1 cecha). W praktyce często modeluje się **wiele zmiennych jednocześnie** (np. `bus` i `rail` razem) — wtedy `input_size > 1`, a model może wykorzystać korelacje między zmiennymi (np. wzrost ruchu autobusowego może być wskaźnikiem zmian w ruchu kolejowym).

### 4.8. Praktyczne wskazówki dotyczące RNN

- **Standaryzuj/skaluj dane** przed podaniem do RNN (jak `/1e6` w przykładzie) — pomaga w stabilności treningu.
- **Gradient clipping** (`torch.nn.utils.clip_grad_norm_`) jest szczególnie ważny dla RNN ze względu na ryzyko eksplodującego gradientu.
- **`batch_first=True`** ułatwia pracę z PyTorch DataLoaderami (zgodny kształt `[batch, seq, features]`).
- Przy doborze architektury: **zacznij od najprostszego modelu** (prosty RNN), dodawaj złożoność (głębsze warstwy, LSTM/GRU) tylko jeśli prosty model nie wystarcza — co dokładnie zademonstrował ten wykład.
- **Dziel dane czasowo, nie losowo** — zbiór treningowy musi być wcześniejszy niż walidacyjny i testowy.

---

## Pytania kontrolne (możliwe na egzaminie)

1. Czym jest unrolling (rozciągnięcie w czasie)? Do czego służy?
2. Napisz wzór wyjścia pojedynczego neuronu rekurencyjnego.
3. Czym różni się stan komórki od jej wyjścia? Kiedy są tożsame, a kiedy nie?
4. Wymień i opisz cztery typy relacji wejście-wyjście w RNN (seq-to-seq, seq-to-vector, vector-to-seq, encoder-decoder).
5. Co to jest BPTT? Jak się różni (i jak nie różni) od standardowego backpropagation?
6. Dlaczego dla długich sekwencji BPTT staje się niestabilny?
7. Wyjaśnij, czym jest klasa `TimeSeriesDataset` i jak działają jej metody `__len__` i `__getitem__`.
8. Dlaczego zbiór treningowy/walidacyjny/testowy dla szeregów czasowych dzieli się chronologicznie, a nie losowo?
9. Co to jest MAPE i czym różni się od MAE? Kiedy MAPE jest bardziej przydatne?
10. Wyjaśnij parametry `order` i `seasonal_order` modelu ARIMA/SARIMA.
11. Napisz wzór warstwy rekurencyjnej w PyTorch (`nn.RNN`) — co reprezentują `outputs` i `last_state`?
12. Dlaczego w przykładzie z wykładu LSTM dał gorszy wynik niż prosty RNN? Co to mówi o doborze modelu?
13. Opisz architekturę komórki LSTM — jakie są 4 warstwy/bramki i co robi każda z nich?
14. Napisz wzór aktualizacji stanu długoterminowego $c_{(t)}$ w LSTM. Dlaczego ta postać pomaga z zanikającym gradientem?
15. Czym GRU różni się od LSTM? Wymień przynajmniej dwa uproszczenia.
16. Co robi bramka $z_{(t)}$ w GRU, a co bramka $r_{(t)}$?
17. Czym jest głębokie RNN (`num_layers`)? Czy głębsze zawsze znaczy lepsze?
18. Co to jest dwukierunkowe RNN i kiedy nie można go używać?
19. Dlaczego mechanizm uwagi (attention) i architektura Transformer zastąpiły RNN w wielu zadaniach NLP?
20. Jakie są typowe problemy przy uczeniu RNN i jak sobie z nimi radzić (gradient clipping, LSTM/GRU, skalowanie danych)?

---

# Przetwarzanie języka naturalnego w RNN — notatka egzaminacyjna

**Przedmiot:** Uczenie maszynowe 2025/2026
**Wykładowca:** dr inż. Sebastian Ernst
**Temat wykładu:** Przetwarzanie języka naturalnego w RNN

---

## Spis treści

1. [Dlaczego język naturalny?](#1-dlaczego-język-naturalny)
2. [Prosty generator tekstu](#2-prosty-generator-tekstu)
3. [Analiza tekstu](#3-analiza-tekstu)
4. [Budujemy model sentiment analysis](#4-budujemy-model-sentiment-analysis)
5. [Wykorzystanie wcześniej wytrenowanych osadzeń i modeli](#5-wykorzystanie-wcześniej-wytrenowanych-osadzeń-i-modeli)
6. [Tłumaczenie maszynowe przy pomocy sieci encoder-decoder](#6-tłumaczenie-maszynowe-przy-pomocy-sieci-encoder-decoder)
7. [Dodatek — teoria spoza prezentacji](#7-dodatek--teoria-spoza-prezentacji)

---

## 1. Dlaczego język naturalny?

### 1.1. Problemy przetwarzania tekstu

- **Test Turinga nie mówi o obrazach ani grach, a właśnie o tekście** — komunikacja językowa jest tradycyjnie uważana za probierz inteligencji maszynowej.
- **Posługiwanie się językiem jest jedną z najwyższych umiejętności poznawczych człowieka.**
- **Do niedawna stosowano prawie wyłącznie RNN** — obecnie wyprzedzają je sieci o architekturze **transformerów**.
- **Ale bez RNN trudno zrozumieć to, co one robią!** — stąd ten wykład jest fundamentem do zrozumienia transformerów i dużych modeli językowych (LLM).

### 1.2. Jakie problemy możemy rozwiązać?

- **Uzupełnianie (dopisywanie) tekstu** — generowanie kontynuacji na podstawie kontekstu.
- **Klasyfikacja tekstów**, np. **sentiment analysis** (analiza sentymentu).
- **Tłumaczenie maszynowe!**

---

## 2. Prosty generator tekstu

### 2.1. char-rnn: generowanie tekstu znak po znaku

Klasyczny przykład z pracy **A. Karpathy, *The Unreasonable Effectiveness of Recurrent Neural Networks* (2015)** — RNN trenowana na tekstach Szekspira, generująca nowy tekst **znak po znaku**, naśladujący styl autora (mimo braku realnego rozumienia treści).

### 2.2. Dane: Szekspir

```python
from pathlib import Path
import urllib.request

path = Path("datasets/shakespeare/shakespeare.txt")
if not path.is_file():
    path.parent.mkdir(parents=True, exist_ok=True)
    url = "https://homl.info/shakespeare"
    urllib.request.urlretrieve(url, path)
shakespeare_text = path.read_text()
len(shakespeare_text)
# 1115394
```

### 2.3. Próbka Szekspira

```python
print(shakespeare_text[:175])
```

### 2.4. Budujemy słownik

```python
vocab = sorted(set(shakespeare_text.lower()))
"".join(vocab)
# "\n !$&',-.3:;?abcdefghijklmnopqrstuvwxyz"
```

```python
char_to_id = {char: index for index, char in enumerate(vocab)}
id_to_char = {index: char for index, char in enumerate(vocab)}

char_to_id["a"]   # 13
id_to_char[13]    # 'a'
```

> Słownik (*vocab*) to po prostu **zbiór unikalnych znaków** w tekście. Każdy znak otrzymuje liczbowy identyfikator (token ID) — to fundament każdego modelu NLP: tekst musi zostać zamieniony na liczby.

### 2.5. PyTorch, import

```python
import torch

if torch.cuda.is_available():
    device = "cuda"
elif torch.backends.mps.is_available():
    device = "mps"
else:
    device = "cpu"
```

### 2.6. Kodowanie tekstu

```python
def encode_text(text):
    return torch.tensor([char_to_id[char] for char in text.lower()])

def decode_text(char_ids):
    return "".join([id_to_char[char_id.item()] for char_id in char_ids])
```

```python
encoded = encode_text("Hello!")
encoded
# tensor([20, 17, 24, 24, 27, 2])
decode_text(encoded)
# 'hello!'
```

### 2.7.–2.8. Dataset — klasa CharDataset

**Idea:** dzielimy długi tekst na **okienka**, aby wytrenować RNN **sequence-to-sequence**. **Etykiety to fragmenty tekstu przesunięte o 1 znak** — model uczy się przewidywać **następny znak** dla każdej pozycji w oknie (nie tylko ostatniej, jak w przykładzie z szeregami czasowymi z poprzedniego wykładu).

```python
from torch.utils.data import Dataset, DataLoader

class CharDataset(Dataset):
    def __init__(self, text, window_length):
        self.encoded_text = encode_text(text)
        self.window_length = window_length

    def __len__(self):    # ile okienek się zmieści?
        return len(self.encoded_text) - self.window_length

    def __getitem__(self, idx):
        if idx >= len(self):
            raise IndexError("dataset index out of range")
        end = idx + self.window_length
        window = self.encoded_text[idx : end]
        target = self.encoded_text[idx + 1 : end + 1]
        return window, target
```

> **Kluczowa różnica względem `TimeSeriesDataset` z poprzedniego wykładu:** tam `target` był **jedną** wartością po oknie (sequence-to-vector). Tu `target` jest **całym oknem przesuniętym o 1 znak** (sequence-to-sequence) — model przewiduje kolejny znak **na każdej pozycji**, nie tylko na końcu.

### 2.9. CharDataset, przykład użycia

```python
to_be_dataset = CharDataset("To be or not to be", window_length=10)
for x, y in to_be_dataset:
    print(f"x={x}, y={y}")
    print(f"    decoded: x={decode_text(x)!r}, y={decode_text(y)!r}")
# decoded: x='to be or n', y='o be or no'
# decoded: x='o be or no', y=' be or not'
# ...
```

> Widać jasno regułę: `y` to dokładnie `x` **przesunięte o jedną pozycję w prawo** w oryginalnym tekście.

### 2.10. Przygotowanie zbioru danych

Używamy **~90%** danych jako treningowych, po **~5%** jako walidacyjne i testowe. **Zbiór uczący mieszamy przy każdej epoce.**

```python
window_length = 50
batch_size = 512
train_set = CharDataset(shakespeare_text[:1_000_000], window_length)
valid_set = CharDataset(shakespeare_text[1_000_000:1_060_000], window_length)
test_set = CharDataset(shakespeare_text[1_060_000:], window_length)
train_loader = DataLoader(train_set, batch_size=batch_size, shuffle=True)
valid_loader = DataLoader(valid_set, batch_size=batch_size)
test_loader = DataLoader(test_set, batch_size=batch_size)
```

### 2.11. Czym są wartości w zbiorze?

**Teoretycznie** moglibyśmy podawać modelowi surowe identyfikatory tokenów (liczby 0, 1, 2, ...). **Ale modele zakładają, że bliskość wartości oznacza podobieństwo tego, co wyrażają** — analogicznie do problemu kodowania kolorów w klasycznym ML (czerwony < niebieski < zielony? — to nie ma sensu). Tam radziliśmy sobie **one-hot encoding**, ale tutaj mamy **problem skali** (dziesiątki tysięcy unikalnych tokenów w typowym słowniku NLP).

### 2.12.–2.13. Osadzenia (embeddings)

- Tradycyjnie stosujemy np. **one-hot encoding**, ale mamy **problem skali** (np. 50 000 wymiarów dla każdego tokenu).
- **Osadzenia (embeddings)** to **zwarte reprezentacje** — mamy np. **300 wymiarów zamiast 50 000**.
- **Można je trenować** (*representation learning*) — wtedy **zbliżone koncepcje zbliżają się do siebie** w przestrzeni wektorowej.

**Osadzenia słów (word embeddings)** — ich wymiary przybliżają **osie semantyczne**:

$$
\text{King} - \text{Man} + \text{Woman} = \text{Queen}
$$
$$
\text{Madrid} - \text{Spain} + \text{France} = \text{Paris}
$$

> **Ale uwaga na bias:** $\text{Doctor} - \text{Man} + \text{Woman} = \text{Nurse}$ 😱 — osadzenia trenowane na rzeczywistych danych **odzwierciedlają (i utrwalają) społeczne stereotypy** obecne w danych treningowych. To ważny problem etyczny ML (patrz też sekcja 5.19).

### 2.14. Osadzenia w PyTorch

```python
import torch.nn as nn

torch.manual_seed(42)
embed = nn.Embedding(8, 3)  # 7 categories × 3D embeddings
embed(torch.tensor([[2, 1], [3, 7]]))
# tensor([[[-0.0431, -1.6047,  0.3559],
#          [-2.1055,  0.6784, -1.2345]],
#         [[-0.6866, -0.4934,  0.2415],
#          [-0.0631, -0.8286,  0.3309]]], grad_fn=<EmbeddingBackward0>)
```

> **`nn.Embedding(num_embeddings, embedding_dim)`** — to po prostu **tablica wyszukiwania (lookup table)**: dla każdego ID tokenu zwraca odpowiadający mu wektor o zadanej wymiarowości. Wektory są **inicjalizowane losowo i trenowane** wraz z resztą sieci (przez backpropagation).
>
> **Matematycznie, warstwa osadzeń jest równoważna kodowaniu one-hot i warstwie gęstej** (`Linear` bez biasu) — ale znacznie **wydajniejsza obliczeniowo**, ponieważ nie trzeba materializować ogromnych wektorów one-hot i wykonywać pełnego mnożenia macierzowego; `nn.Embedding` realizuje to przez prosty indeksowanie (lookup).

### 2.15. Nasz model szekspirowski

```python
class ShakespeareModel(nn.Module):
    def __init__(self, vocab_size, n_layers=2, embed_dim=10, hidden_dim=128,
                 dropout=0.1):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim)
        self.gru = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers,
                          batch_first=True, dropout=dropout)
        self.output = nn.Linear(hidden_dim, vocab_size)

    def forward(self, X):
        embeddings = self.embed(X)
        outputs, _states = self.gru(embeddings)
        return self.output(outputs).permute(0, 2, 1)

torch.manual_seed(42)
model = ShakespeareModel(len(vocab)).to(device)
```

**Wyjaśnienie:**
- `self.embed` — zamienia ID znaków na wektory osadzeń.
- `self.gru` — dwuwarstwowa (`n_layers=2`) sieć GRU przetwarzająca sekwencję osadzeń.
- `self.output` — warstwa gęsta mapująca **każdy** stan ukryty (dla każdej pozycji w sekwencji) na logity dla **wszystkich znaków słownika** (klasyfikacja wieloklasowa na każdej pozycji).
- **`outputs`** ze `self.gru` ma kształt `[batch, seq, hidden_dim]` — bo chcemy predykcji na **każdym** kroku czasowym (sequence-to-sequence), nie tylko na ostatnim.
- **`.permute(0, 2, 1)`** — `nn.CrossEntropyLoss` w PyTorch oczekuje kształtu `[batch, classes, ...]`, a nasz tensor ma `[batch, seq, vocab_size]`; permutacja zmienia go na `[batch, vocab_size, seq]`, co jest zgodne z konwencją straty wieloklasowej dla danych sekwencyjnych.

### 2.16.–2.17. Pętla treningowa

```python
import torchmetrics

def evaluate_tm(model, data_loader, metric):
    model.eval()
    metric.reset()
    with torch.no_grad():
        for X_batch, y_batch in data_loader:
            X_batch, y_batch = X_batch.to(device), y_batch.to(device)
            y_pred = model(X_batch)
            metric.update(y_pred, y_batch)
    return metric.compute()
```

```python
def train(model, optimizer, loss_fn, metric, train_loader, valid_loader,
          n_epochs, patience=2, factor=0.5, epoch_callback=None):
    scheduler = torch.optim.lr_scheduler.ReduceLROnPlateau(
        optimizer, mode="max", patience=patience, factor=factor)
    history = {"train_losses": [], "train_metrics": [], "valid_metrics": []}
    for epoch in range(n_epochs):
        total_loss = 0.0
        metric.reset()
        model.train()
        if epoch_callback is not None:
            epoch_callback(model, epoch)
        for index, (X_batch, y_batch) in enumerate(train_loader):
            X_batch, y_batch = X_batch.to(device), y_batch.to(device)
            y_pred = model(X_batch)
            loss = loss_fn(y_pred, y_batch)
            total_loss += loss.item()
            loss.backward()
            optimizer.step()
            optimizer.zero_grad()
            metric.update(y_pred, y_batch)
            train_metric = metric.compute().item()
            print(f"\rBatch {index + 1}/{len(train_loader)}", end="")
            print(f", loss={total_loss/(index+1):.4f}", end="")
            print(f", {train_metric=:.2%}", end="")
        history["train_losses"].append(total_loss / len(train_loader))
        history["train_metrics"].append(train_metric)
        val_metric = evaluate_tm(model, valid_loader, metric).item()
        history["valid_metrics"].append(val_metric)
        scheduler.step(val_metric)
        print(f"\rEpoch {epoch + 1}/{n_epochs}, "
              f"train loss: {history['train_losses'][-1]:.4f}, "
              f"train metric: {history['train_metrics'][-1]:.2%}, "
              f"valid metric: {history['valid_metrics'][-1]:.2%}")
    return history
```

**Wyjaśnienie — co nowe w tej wersji `train` (vs. poprzednie wykłady):**
- **`ReduceLROnPlateau(mode="max", patience, factor)`** — wbudowany scheduler LR: zmniejsza LR (mnoży przez `factor`), gdy metryka (np. accuracy) nie poprawia się przez `patience` epok.
- **`epoch_callback`** — opcjonalna funkcja wywoływana na początku każdej epoki, przydatna np. do logowania, zapisywania checkpointów lub resetowania stanu (zobacz sekcję o *stateful RNN* w dodatkach).
- Pętla wypisuje **bieżący postęp w trakcie batcha** (`\r` — powrót karetki, aktualizacja w miejscu) — to znane już z poprzednich wykładów, ale tu rozbudowane o `loss` i `train_metric` na żywo.

### 2.18. Housekeeping

```python
import gc

def del_vars(variable_names=[]):
    for name in variable_names:
        try:
            del globals()[name]
        except KeyError:
            pass  # ignore variables that have already been deleted
    gc.collect()
    if device == "cuda":
        torch.cuda.empty_cache()
```

> **Funkcja pomocnicza do czyszczenia pamięci** — szczególnie ważna przy pracy z dużymi modelami (BERT, transformery) na ograniczonej pamięci GPU. Usuwa zmienne globalne, wywołuje *garbage collector* Pythona i czyści cache pamięci CUDA.

### 2.19. Trening

```python
n_epochs = 20
xentropy = nn.CrossEntropyLoss()
optimizer = torch.optim.NAdam(model.parameters())
accuracy = torchmetrics.Accuracy(task="multiclass", num_classes=len(vocab)).to(device)
history = train(model, optimizer, xentropy, accuracy, train_loader, valid_loader, n_epochs)
# Epoch 1/20: train loss: 1.4091, train metric: 56.10%, valid metric: 51.38%
# ...
# Epoch 20/20: train loss: 1.2951, train metric: 59.03%, valid metric: 55.02%
```

> **`torch.optim.NAdam`** — wariant Adama z elementem Nesterova (NAG), często dający lepszą zbieżność niż czysty Adam. **~55% dokładności** w przewidywaniu pojedynczego, kolejnego znaku to przyzwoity wynik — pamiętajmy, że to zadanie z natury ma wiele poprawnych kontynuacji (wiele słów może zaczynać się tą samą literą).

### 2.20. Zapisujemy i sprawdzamy

```python
torch.save(model.state_dict(), "my_shakespeare_model.pt")
```

```python
model.eval()
text = "To be or not to b"
encoded_text = encode_text(text).unsqueeze(dim=0).to(device)
with torch.no_grad():
    Y_logits = model(encoded_text)
    predicted_char_id = Y_logits[0, :, -1].argmax().item()
    predicted_char = id_to_char[predicted_char_id]
predicted_char
# 'e'
```

> **`.unsqueeze(dim=0)`** — dodaje wymiar wsadu (model oczekuje `[batch, seq]`, a mamy tylko `[seq]`). **`Y_logits[0, :, -1]`** — bierzemy logity dla **ostatniej pozycji** w sekwencji (przewidywanie kolejnego znaku po całym podanym tekście) — pamiętajmy, że kształt to `[batch, vocab_size, seq]` po `permute`, więc `[0, :, -1]` to wszystkie logity słownika dla ostatniej pozycji pierwszej (i jedynej) próbki.

### 2.21. Generowanie tekstu

- **Podejście zachłanne (*greedy decoding*)** — zakłada doklejanie emitowanego przez model znaku i ponowne uruchamianie predykcji. **Niestety wtedy w wynikach mamy częste powtórzenia.**
- **Rozwiązanie:** możemy zamiast tego **wybierać znak losowo** przy pomocy `torch.multinomial()`.
- **Dodatkowo, wartości logitów możemy podzielić przez parametr zwany temperaturą** — im wyższa, tym większa szansa na wybór znaków o niskim prawdopodobieństwie.

```python
torch.manual_seed(2137)
probs = torch.tensor([[0.5, 0.4, 0.1]])
samples = torch.multinomial(probs, replacement=True, num_samples=17)
samples
# tensor([[1, 0, 1, 2, 1, 0, 2, 1, 1, 0, 0, 1, 0, 2, 1, 1, 1]])
```

> **`torch.multinomial(probs, num_samples, replacement=True)`** — losuje indeksy zgodnie z rozkładem prawdopodobieństwa podanym w `probs` (tu: indeks 0 z p=0,5; indeks 1 z p=0,4; indeks 2 z p=0,1). To **podstawowy mechanizm samplingu** w generowaniu tekstu.

### 2.22. Funkcje generujące tekst

```python
import torch.nn.functional as F

def next_char(model, text, temperature=1):
    encoded_text = encode_text(text).unsqueeze(dim=0).to(device)
    with torch.no_grad():
        Y_logits = model(encoded_text)
        Y_probas = F.softmax(Y_logits[0, :, -1] / temperature, dim=-1)
        predicted_char_id = torch.multinomial(Y_probas, num_samples=1).item()
    return id_to_char[predicted_char_id]

def extend_text(model, text, n_chars=150, temperature=1):
    for _ in range(n_chars):
        text += next_char(model, text, temperature)
    return text
```

> **Wzór temperatury:** $P_i = \text{softmax}(z_i / T)$. Dla $T \to 0$: rozkład staje się ostry (zbliża się do *argmax*, deterministyczny — efektywnie greedy decoding). Dla $T \to \infty$: rozkład staje się **płaski** (jednorodny) — wybór praktycznie losowy, niezależny od logitów.

### 2.23. Wpływ temperatury na wyniki

```python
print(extend_text(model, "To be or not to b", temperature=0.01))
# "To be or not to be the sea, that i have seen the seat of the sea..."
# (płynny, ale nudny i powtarzalny tekst — niska temperatura ≈ greedy)

print(extend_text(model, "To be or not to b", temperature=0.4))
# "To be or not to be so door of the wars and grieves my princely counsels..."
# (rozsądny balans — wygląda jak prawdziwy tekst, ale bardziej zróżnicowany)

print(extend_text(model, "To be or not to b", temperature=100))
# "To be or not to bwegrl tgxy glljwwjikvewynefbl''czlogs:g;..."
# (kompletny bezsens — bardzo wysoka temperatura ≈ losowy wybór znaków)
```

> **Wniosek praktyczny:** temperatura jest **hiperparametrem kontrolującym kreatywność vs. spójność** generowanego tekstu — niska temperatura = bezpieczne, powtarzalne wyjście; wysoka = chaotyczne; **umiarkowana** (np. 0,4–0,7) zwykle daje najlepsze rezultaty w praktyce (m.in. w generatywnych modelach językowych typu GPT).

### 2.24. Housekeeping

```python
del_vars(["accuracy", "embed", "encoded", "encoded_text", "optimizer", "probs",
          "samples", "x", "y", "shakespeare_text", "stateful_test_loader",
          "stateful_train_loader", "Y_logits", "stateful_valid_loader",
          "test_loader", "train_loader", "valid_loader", "xentropy"])
```

---

## 3. Analiza tekstu

### 3.1.–3.4. Biblioteka datasets Hugging Face — IMDb

**Biblioteka `datasets`** daje dostęp do setek tysięcy zbiorów danych, między innymi **IMDb** (*Internet Movie Database*) — zbiór **50 000 recenzji filmów** z etykietą sentymentu (pozytywna/negatywna). **Analiza sentymentu** to jedno z najpopularniejszych zadań analizy tekstu.

```python
from datasets import load_dataset

imdb_dataset = load_dataset("stanfordnlp/imdb")
split = imdb_dataset["train"].train_test_split(train_size=0.8, seed=42)
imdb_train_set, imdb_valid_set = split["train"], split["test"]
imdb_test_set = imdb_dataset["test"]
```

```python
imdb_train_set[1]["text"]
# "'The Rookie' was a wonderful movie..."
imdb_train_set[1]["label"]
# 1   <- pozytywna
```

> **Przykład recenzji negatywnej, ale niejednoznacznej** (cell 80): tekst opisujący film historyczny w sposób mieszany, z elementami chwalącymi gry aktorskie, ale ogólnie krytyczny — etykieta `0` (negatywna), pokazując, że **sentiment analysis na realnych danych bywa subtelny i niejednoznaczny** nawet dla człowieka.

### 3.5. Tokenizacja: wykorzystanie gotowych modeli

- **Dzielenie tekstu na znaki** (jak w char-rnn) ma **dość ograniczone zastosowania** — same znaki niewiele znaczą semantycznie.
- **2016: Sennrich, Haddow i Birch** opisali różne sposoby dzielenia tekstu na **tokeny** w pracy *„Neural Machine Translation of Rare Words with Subword Units"*.
- **Przykładowo:** model mógł nie widzieć słowa „smartest", ale widział „smart" i przyrostek „est" — **tokenizacja na podsłowa** (*subword tokenization*) pozwala generalizować na nowe, niewidziane słowa.
- **BPE (byte pair encoding)** — wyszukuje **najczęściej występujące pary znaków**, aż słownik osiągnie żądany rozmiar.

### 3.6.–3.9. Biblioteka tokenizers Hugging Face — BPE

```python
import tokenizers

bpe_model = tokenizers.models.BPE(unk_token="<unk>")
bpe_tokenizer = tokenizers.Tokenizer(bpe_model)
bpe_tokenizer.pre_tokenizer = tokenizers.pre_tokenizers.Whitespace()
special_tokens = ["<pad>", "<unk>"]
bpe_trainer = tokenizers.trainers.BpeTrainer(vocab_size=1000,
                                             special_tokens=special_tokens)
train_reviews = [review["text"].lower() for review in imdb_train_set]
bpe_tokenizer.train_from_iterator(train_reviews, bpe_trainer)
```

**Pre-tokenizacja: białe spacje** — pierwszy krok dzieli tekst po spacjach i znakach interpunkcyjnych:

```python
tokenizers.pre_tokenizers.Whitespace().pre_tokenize_str("Hello, world!!!")
# [('Hello', (0, 5)), (',', (5, 6)), ('world', (7, 12)), ('!!!', (12, 15))]
```

**Przykład tokenizacji BPE:**

```python
some_review = "what an awesome movie! 😊"
bpe_encoding = bpe_tokenizer.encode(some_review)
bpe_encoding.tokens
# ['what', 'an', 'aw', 'es', 'ome', 'movie', '!', '<unk>']
```

> Zauważ: **„awesome”** zostało podzielone na podsłowa `aw` + `es` + `ome` (bo cały wyraz nie był wystarczająco częsty w słowniku o limicie 1000 tokenów), a **emoji 😊 stało się `<unk>`** (nieznany token — pre-tokenizer `Whitespace` nie radzi sobie z emoji).

**Praca z tokenami:**

```python
bpe_tokenizer.get_vocab()["what"]       # 303
bpe_tokenizer.token_to_id("what")       # 303
bpe_tokenizer.id_to_token(666)          # 'another'
bpe_tokenizer.decode(bpe_token_ids)     # 'what an aw es ome movie !'
bpe_encoding.offsets   # położenie w pierwotnym ciągu: [(0,4), (5,7), ...]
```

> **Uwaga: dekodowanie nie odtwarza idealnie oryginału** — spacje między podsłowami (`aw es ome`) są sztucznie wstawione, bo tokenizer (z pre-tokenizerem Whitespace) **gubi informację o oryginalnych spacjach** (patrz sekcja 3.14).

### 3.10. Tokenizacja wsadów

```python
bpe_tokenizer.encode_batch(train_reviews[:3])
```

**Padding i obcinanie (truncation):**

```python
bpe_tokenizer.enable_padding(pad_id=0, pad_token="<pad>")
bpe_tokenizer.enable_truncation(max_length=500)
bpe_encodings = bpe_tokenizer.encode_batch(train_reviews[:3])
```

> Po włączeniu `enable_padding`, wszystkie sekwencje w batchu są **dopełniane do tej samej długości** (najdłuższej w batchu lub `max_length`) tokenem `<pad>` (ID=0) — niezbędne, by zmieścić różne długości tekstu w jednym tensorze.

### 3.11. Jak wyglądają wsady?

```python
bpe_batch_ids = torch.tensor([encoding.ids for encoding in bpe_encodings])
```

> Widać, że krótsze recenzje są **dopełnione zerami** na końcu (padding), aby zrównać długość z najdłuższą próbką w batchu.

### 3.12.–3.13. Maska uwagi (attention mask) i długości próbek

```python
attention_mask = torch.tensor([encoding.attention_mask for encoding in bpe_encodings])
```

> **Maska uwagi** to tensor z **1 dla prawdziwych tokenów** i **0 dla paddingu** — informuje model, które pozycje są rzeczywistą treścią, a które tylko wypełnieniem.

**Sumujemy jedynki w maskach, by uzyskać rzeczywiste długości:**

```python
lengths = attention_mask.sum(dim=-1)
lengths
# tensor([281, 114, 285])
```

### 3.14. Problem spacji

- **Pre-tokenizer `Whitespace`** usunął wszystkie spacje, więc tokenizer **nie wie, gdzie pierwotnie się znajdowały**.
- Jeżeli zamiast tego użyjemy pre-tokenizera **`ByteLevel`**, wszystkie spacje zostaną zastąpione **specjalnym znakiem** — tokenizer BPE pracuje **na poziomie bajtów, a nie znaków** (emoji to 4 bajty, a 1 znak!).
- **BBPE (Byte-level Byte Pair Encoding)** **nigdy nie zwróci tokenu `<unk>`**, jeżeli w słowniku ma wszystkie 256 możliwych wartości bajtu — bo każdy możliwy bajt można reprezentować.

### 3.15.–3.16. Tokenizacja BBPE

```python
tokenizers.pre_tokenizers.ByteLevel().pre_tokenize_str(some_review)
# [('Ġwhat', (0, 4)), ('Ġan', (4, 7)), ('Ġawesome', (7, 15)),
#  ('Ġmovie', (15, 21)), ('!', (21, 22)), ('ĠðŁĺĬ', (22, 24))]
```

> **`Ġ`** to specjalny znak reprezentujący **spację** (charakterystyczny dla tokenizerów GPT-2/GPT) — pozwala odtworzyć dokładną pozycję spacji bez ich utraty.

```python
bbpe_model = tokenizers.models.BPE(unk_token="<unk>")
bbpe_tokenizer = tokenizers.Tokenizer(bbpe_model)
bbpe_tokenizer.pre_tokenizer = tokenizers.pre_tokenizers.ByteLevel()
bbpe_trainer = tokenizers.trainers.BpeTrainer(vocab_size=1000, special_tokens=special_tokens)
bbpe_tokenizer.train_from_iterator(train_reviews, bbpe_trainer)
```

```python
bbpe_encoding = bbpe_tokenizer.encode(some_review)
bbpe_tokens = bbpe_encoding.tokens
# ['Ġwhat', 'Ġan', 'Ġaw', 'es', 'ome', 'Ġmovie', '!', 'Ġ', '<unk>', 'Ł', 'ĺ', '<unk>']
```

> Emoji 😊 (wielobajtowy znak Unicode) zostało rozłożone na **pojedyncze bajty** — niektóre z nich nadal stają się `<unk>`, bo słownik o rozmiarze 1000 nie pokrywa wszystkich kombinacji bajtów, ale problem jest dużo mniejszy niż przy tokenizacji znakowej.

```python
bbpe_decoded = bbpe_tokenizer.decode(bbpe_token_ids)
bbpe_decoded.replace(" ", "").replace("Ġ", " ").strip()
# 'what an awesome movie! Łĺ'
```

> Dzięki specjalnemu kodowaniu spacji (`Ġ`) możemy **idealnie odtworzyć oryginalne rozmieszczenie spacji** po dekodowaniu — czego nie potrafiliśmy zrobić z tokenizerem `Whitespace`.

### 3.17. Tokenizacja WordPiece

Zamiast dodawać do słownika **najczęściej występującą parę** (jak BPE), WordPiece dodaje parę o **najwyższym wyniku** (*score*):

$$
\text{score}(AB) = \frac{\text{frequency}(AB)}{\text{freq}(A) \cdot \text{freq}(B)} \cdot \text{len}(\text{vocab})
$$

> Ten wzór **normalizuje częstość pary** przez częstości jej składników — preferuje pary, które są **częstsze razem niż osobno** (silna kolokacja), a nie tylko absolutną częstość. Dostępna w bibliotece `tokenizers` jako `WordPiece` + `WordPieceTrainer`. Używana m.in. w modelu **BERT**.

### 3.18. Tokenizacja Unigram LM

- **Zaczyna z bardzo dużym słownikiem** i kolejno **usuwa najmniej użyteczne tokeny**, aż do osiągnięcia żądanego rozmiaru (podejście odwrotne do BPE/WordPiece, które *budują* słownik od podstaw).
- **Zakłada, że** $P(AB) = P(A) \cdot P(B)$ (niezależność tokenów).
- **Przykład z wykładu:** jeżeli słownik zawiera „them" (1%), „the" (5%), „m" (2%), to:
$$
P(\text{them}) = 1\% + 2\% \cdot 5\% = 1\% + 0{,}1\% = 1{,}1\%
$$

(prawdopodobieństwo „them" jako pełnego tokenu LUB jako złożenia „the"+„m")

> Po usunięciu tokenu „them" ze słownika, $P(\text{them}) = 0{,}1\%$ (tylko przez kombinację „the"+„m"). **Algorytm woli usunąć któryś z pozostałych tokenów**, bo gdy „them" zostaje (a usuwamy coś innego), $P(\text{them}) = 1\%$ — czyli usunięcie tokenu „them" **bardziej zaszkodziłoby** ogólnemu prawdopodobieństwu modelu niż usunięcie innego, mniej użytecznego tokenu. Dostępna jako `Unigram` + `UnigramTrainer`.

### 3.19.–3.21. Wykorzystanie wcześniej wytrenowanych tokenizerów

Biblioteka **Hugging Face `transformers`** zawiera mnóstwo modeli do różnych celów (NLP, analiza obrazu, dźwięku itd.). Pierwotnie wszystkie były oparte o architekturę transformerów, obecnie są tam modele o różnych architekturach. **Możemy „wyjąć" z takiego modelu tokenizer i użyć go do własnych celów.**

**Przykład: tokenizer z GPT-2:**

```python
import transformers

gpt2_tokenizer = transformers.AutoTokenizer.from_pretrained("gpt2")
gpt2_encoding = gpt2_tokenizer(train_reviews[:3], truncation=True, max_length=500)
list(gpt2_encoding.keys())
# ['input_ids', 'attention_mask']

gpt2_token_ids = gpt2_encoding["input_ids"][0][:10]
gpt2_tokenizer.decode(gpt2_token_ids)
# 'stage adaptations often have a major fault. they often'
```

**Przykład: tokenizer z BERT:**

**BERT = Bidirectional Encoder Representations from Transformers**. Ma token paddingu, więc możemy dostać **od razu tensory PyTorch**:

```python
bert_tokenizer = transformers.AutoTokenizer.from_pretrained("bert-base-uncased")
bert_encoding = bert_tokenizer(train_reviews[:3], padding=True,
                               truncation=True, max_length=500,
                               return_tensors="pt")
```

### 3.22. Przykład: własny tokenizer

Biblioteka `transformers` pozwala **obudować dowolny tokenizer** API takim jak te gotowe:

```python
hf_tokenizer = transformers.PreTrainedTokenizerFast(tokenizer_object=bpe_tokenizer)
hf_encodings = hf_tokenizer(train_reviews[:3], padding=True, truncation=True,
                            max_length=500, return_tensors="pt")
```

> Pozwala to wykorzystać **własny, wytrenowany tokenizer BPE** w pełnym ekosystemie `transformers` (np. z `Trainer` API, pipelines itd.).

---

## 4. Budujemy model sentiment analysis

### 4.1. Dodanie tokenizacji

Nasze datasety na razie **nie mają tokenizacji**. Moglibyśmy je przetworzyć metodą `.map()`, ale jest **prostszy sposób — tokenizacja w samych DataLoaderach**. W tym celu przekazujemy do `DataLoader` funkcję jako argument **`collate_fn`** — zostanie ona wywołana **przy każdym batchu**.

```python
def collate_fn(batch, tokenizer=bert_tokenizer):
    reviews = [review["text"] for review in batch]
    labels = [[review["label"]] for review in batch]
    encodings = tokenizer(reviews, padding=True, truncation=True,
                          max_length=200, return_tensors="pt")
    labels = torch.tensor(labels, dtype=torch.float32)
    return encodings, labels

batch_size = 256
imdb_train_loader = DataLoader(imdb_train_set, batch_size=batch_size,
                               collate_fn=collate_fn, shuffle=True)
imdb_valid_loader = DataLoader(imdb_valid_set, batch_size=batch_size,
                               collate_fn=collate_fn)
imdb_test_loader = DataLoader(imdb_test_set, batch_size=batch_size,
                              collate_fn=collate_fn)
```

> **`collate_fn`** decyduje, jak **pojedyncze przykłady z batcha zostają złożone w jeden tensor**. Domyślnie PyTorch po prostu „stackuje" próbki, ale tu chcemy najpierw **stokenizować surowy tekst** — robiąc to **leniwie, na bieżąco, dla każdego batcha**, zamiast wcześniej przetwarzać cały (duży) zbiór danych. To oszczędza pamięć i pozwala na dynamiczny padding (do długości najdłuższej próbki **w danym batchu**, nie globalnie).

### 4.2. Struktura modelu

```python
class SentimentAnalysisModel(nn.Module):
    def __init__(self, vocab_size, n_layers=2, embed_dim=128, hidden_dim=64,
                 pad_id=0, dropout=0.2):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim, padding_idx=pad_id)
        self.gru = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers,
                          batch_first=True, dropout=dropout)
        self.output = nn.Linear(hidden_dim, 1)

    def forward(self, encodings):
        embeddings = self.embed(encodings["input_ids"])
        _outputs, hidden_states = self.gru(embeddings)
        return self.output(hidden_states[-1])
```

**Wyjaśnienie:**
- **`padding_idx=pad_id`** — mówi `nn.Embedding`, że token o tym ID (padding) powinien mieć **osadzenie zawsze równe zerowemu wektorowi** i **nie powinien być aktualizowany** podczas treningu — bo padding nie niesie informacji.
- **`hidden_states[-1]`** — bierzemy stan ukryty **ostatniej warstwy** GRU (sequence-to-vector — jedna predykcja sentymentu na całą recenzję), a nie `outputs[:, -1]` jak poprzednio. Dla wielowarstwowych RNN, `hidden_states` ma kształt `[num_layers, batch, hidden_dim]`, więc `[-1]` daje stan ostatniej warstwy.
- Wyjście to **1 neuron** (regresja logitu) — klasyfikacja binarna z `nn.BCEWithLogitsLoss` na wyjściu.

### 4.3. Problem paddingu

**Nasz model nie ignoruje tokenów paddingowych** — jeżeli dostanie ich odpowiednio dużo (np. krótka recenzja w batchu z bardzo długimi), **„zapomni", co miał w tekście**, bo GRU dalej przetwarza puste tokeny `<pad>` po właściwej treści, "rozmywając" stan ukryty.

**Rozwiązanie:** użycie **packed sequences** — specjalnych struktur danych do reprezentacji wsadów z ciągami o różnej długości. Wykorzystujemy do tego funkcje **`pack_padded_sequence`** oraz **`pad_packed_sequence`**.

```python
from torch.nn.utils.rnn import pack_padded_sequence, pad_packed_sequence
sequences = torch.tensor([[1, 2, 0, 0], [5, 6, 7, 8]])
packed = pack_padded_sequence(sequences, lengths=(2, 4),
                              enforce_sorted=False, batch_first=True)
packed
# PackedSequence(data=tensor([5, 1, 6, 2, 7, 8]),
#                 batch_sizes=tensor([2, 2, 1, 1]),
#                 sorted_indices=tensor([1, 0]),
#                 unsorted_indices=tensor([1, 0]))
```

> **`pack_padded_sequence`** „spłaszcza" batch sekwencji o różnych długościach do **zwartej struktury** `PackedSequence`, gdzie RNN przetwarza dokładnie tyle kroków czasowych, ile potrzebne dla każdej sekwencji — **bez przetwarzania paddingu**. `batch_sizes` mówi, ile sekwencji jest aktywnych w każdym kroku czasowym (np. `[2,2,1,1]` — w krokach 1-2 obie sekwencje są aktywne, w krokach 3-4 tylko dłuższa). `enforce_sorted=False` pozwala podać sekwencje w dowolnej kolejności długości (funkcja sama je sortuje wewnętrznie).

### 4.4. Zmodyfikowany model

```python
class SentimentAnalysisModelPackedSeq(nn.Module):
    def __init__(self, vocab_size, n_layers=2, embed_dim=128,
                 hidden_dim=64, pad_id=0, dropout=0.2):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim, padding_idx=pad_id)
        self.gru = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers,
                          batch_first=True, dropout=dropout)
        self.output = nn.Linear(hidden_dim, 1)

    def forward(self, encodings):
        embeddings = self.embed(encodings["input_ids"])
        lengths = encodings["attention_mask"].sum(dim=1)
        packed = pack_padded_sequence(embeddings, lengths=lengths.cpu(),
                                      batch_first=True, enforce_sorted=False)
        _outputs, hidden_states = self.gru(packed)
        return self.output(hidden_states[-1])
```

> **`lengths = attention_mask.sum(dim=1)`** — wykorzystujemy maskę uwagi (1 dla prawdziwych tokenów, 0 dla paddingu) do obliczenia **rzeczywistej długości każdej sekwencji**. GRU otrzymuje spakowaną sekwencję (`packed`) i **przetwarza tylko prawdziwe tokeny**, ignorując padding — rozwiązuje to problem z sekcji 4.3.

### 4.5. Trening

```python
torch.manual_seed(42)
vocab_size = bert_tokenizer.vocab_size
imdb_model_ps = SentimentAnalysisModelPackedSeq(vocab_size).to(device)

n_epochs = 10
xentropy = nn.BCEWithLogitsLoss()
optimizer = torch.optim.NAdam(imdb_model_ps.parameters())
accuracy = torchmetrics.Accuracy(task="binary").to(device)

history = train(imdb_model_ps, optimizer, xentropy, accuracy,
                imdb_train_loader, imdb_valid_loader, n_epochs)
# Epoch 1/10: valid metric: 52.84%
# Epoch 5/10: valid metric: 85.74%
# Epoch 10/10: train metric: 99.43%, valid metric: 84.86%
```

> **`nn.BCEWithLogitsLoss`** — binarna entropia krzyżowa, która **wewnętrznie stosuje sigmoid** do logitów — stabilniejsza numerycznie niż `sigmoid()` + `BCELoss()` osobno. Wyniki pokazują **przeuczenie** — `train metric` rośnie do 99% podczas gdy `valid metric` stabilizuje się wokół 84-86% — model uczy się zapamiętywać dane treningowe, a nie tylko generalizować.

### 4.6. Dwukierunkowe RNN

- **Zwykła warstwa RNN patrzy na przeszłe i obecne wejście** (tylko w jednym kierunku).
- **Jednak przy kodowaniu danego słowa warto spojrzeć w przód:** „the **right** arm", „the **right** person", „the **right** to speak" — znaczenie słowa „right" zależy od **kontekstu po nim**, którego jednokierunkowy RNN nie widzi w danym kroku.
- **Rozwiązanie:** dwie warstwy RNN — jedna czyta od **lewej do prawej**, druga — od **prawej do lewej**.
- **Ustawiamy parametr `bidirectional=True`** przy warstwie GRU (lub RNN czy LSTM).
- **Potrzebne drobne modyfikacje modelu** — **inny rozmiar wejścia warstwy gęstej** (podwojony, bo łączymy wyjścia z obu kierunków).

### 4.7. Housekeeping

```python
Out.clear()
del_vars(["albert_token_ids", "attention_mask", "bpe_batch_ids", "encoded_text",
          "lengths", "optimizer", "padded", "probs", "samples", "sequences",
          "x", "xentropy", "y", "Y_logits"])
```

---

## 5. Wykorzystanie wcześniej wytrenowanych osadzeń i modeli

### 5.1.–5.2. Osadzenia z BERT

```python
bert_model = transformers.AutoModel.from_pretrained("bert-base-uncased")
bert_model.embeddings.word_embeddings
# Embedding(30522, 768, padding_idx=0)
```

> BERT ma słownik **30 522 tokenów** i osadzenia **768-wymiarowe** — wytrenowane na ogromnym korpusie tekstu, niosą znacznie bogatszą informację semantyczną niż osadzenia trenowane od zera na małym zbiorze IMDb.

**Model z osadzeniami z BERT (zamrożonymi):**

```python
class SentimentAnalysisModelPreEmbeds(nn.Module):
    def __init__(self, pretrained_embeddings, n_layers=2, hidden_dim=64, dropout=0.2):
        super().__init__()
        weights = pretrained_embeddings.weight.data
        self.embed = nn.Embedding.from_pretrained(weights, freeze=True)
        embed_dim = weights.shape[-1]
        self.gru = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers,
                          batch_first=True, dropout=dropout, bidirectional=True)
        self.output = nn.Linear(2 * hidden_dim, 1)

    def forward(self, encodings):
        embeddings = self.embed(encodings["input_ids"])
        lengths = encodings["attention_mask"].sum(dim=1)
        packed = pack_padded_sequence(embeddings, lengths=lengths.cpu(),
                                      batch_first=True, enforce_sorted=False)
        _outputs, hidden_states = self.gru(packed)
        n_dims = self.output.in_features
        top_states = hidden_states[-2:].permute(1, 0, 2).reshape(-1, n_dims)
        return self.output(top_states)
```

**Wyjaśnienie:**
- **`nn.Embedding.from_pretrained(weights, freeze=True)`** — tworzy warstwę osadzeń **inicjalizowaną wagami z BERT**, ale **zamrożoną** (`freeze=True` = `requires_grad=False`) — nie trenujemy jej dalej, korzystamy z gotowej, sprawdzonej reprezentacji.
- **`bidirectional=True`** + **`2 * hidden_dim`** w warstwie wyjściowej — to właśnie ta modyfikacja z sekcji 4.6.
- **`hidden_states[-2:]`** — dla dwukierunkowego GRU z `n_layers=2`, stan ma kształt `[num_layers * 2, batch, hidden_dim]` (×2 za kierunki). Ostatnie 2 „warstwy" to stan **forward i backward** ostatniej rzeczywistej warstwy GRU — łączymy je (`reshape`) w jeden wektor `2*hidden_dim`.

**Trening:** wynik finalny ≈ **81–84%** walidacji — **gorzej** niż własne, trenowane osadzenia (≈85%) — mimo że BERT-owe osadzenia są bogatsze, są **niedostrojone do specyfiki recenzji filmowych** i zamrożone (nie mogą się dostosować).

### 5.4. Problem kontekstu

- **Takie osadzenia są pozbawione kontekstu** — słowo „right" będzie miało **to samo osadzenie** w „left and right" oraz „right and wrong", mimo zupełnie innego znaczenia.
- Zamiast użycia samych osadzeń, możemy też użyć **„kawałka" modelu**, z którego pochodzą.
- **Dlaczego nie użyć całego modelu BERT** do analizy sentymentu IMDb?

```python
bert_encoding = bert_tokenizer(train_reviews[:3], padding=True, max_length=200,
                               truncation=True, return_tensors="pt")
bert_output = bert_model(**bert_encoding)
bert_output.last_hidden_state.shape
# torch.Size([3, 200, 768])    <- kontekstowe osadzenie każdego tokenu

bert_output.pooler_output.shape
# torch.Size([3, 768])          <- jeden wektor reprezentujący całą sekwencję
```

> **`last_hidden_state`** — **kontekstowe** osadzenia (każdy token „widzi" cały kontekst — to jest moc transformerów: self-attention między wszystkimi tokenami). **`pooler_output`** — specjalnie wytrenowany wektor reprezentujący **całą sekwencję** (pochodzi z tokenu `[CLS]` przepuszczonego przez dodatkową warstwę).

### 5.5.–5.6. Model wykorzystujący BERT

```python
class SentimentAnalysisModelBert(nn.Module):
    def __init__(self, n_layers=2, hidden_dim=64, dropout=0.2):
        super().__init__()
        self.bert = transformers.AutoModel.from_pretrained("bert-base-uncased")
        embed_dim = self.bert.config.hidden_size
        self.gru = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers,
                          batch_first=True, dropout=dropout)
        self.output = nn.Linear(hidden_dim, 1)

    def forward(self, encodings):
        contextualized_embeddings = self.bert(**encodings).last_hidden_state
        lengths = encodings["attention_mask"].sum(dim=1)
        packed = pack_padded_sequence(contextualized_embeddings, lengths=lengths.cpu(),
                                      batch_first=True, enforce_sorted=False)
        _outputs, hidden_states = self.gru(packed)
        return self.output(hidden_states[-1])
```

```python
torch.manual_seed(42)
imdb_model_bert = SentimentAnalysisModelBert().to(device)
imdb_model_bert.bert.requires_grad_(False)   # zamrażamy CAŁY model BERT

n_epochs = 4
xentropy = nn.BCEWithLogitsLoss()
optimizer = torch.optim.NAdam(imdb_model_bert.parameters())
accuracy = torchmetrics.Accuracy(task="binary").to(device)

history = train(imdb_model_bert, optimizer, xentropy, accuracy,
                imdb_train_loader, imdb_valid_loader, n_epochs)
# Epoch 1/4: valid metric: 87.22%
# Epoch 3/4: valid metric: 88.74%  <- najlepszy wynik
```

> **`imdb_model_bert.bert.requires_grad_(False)`** — zamraża **cały** model BERT (transfer learning, jak w wykładzie o CNN!) — trenowane są tylko GRU i warstwa wyjściowa. Wynik **≈88,7%** — **najlepszy do tej pory**, bo wykorzystujemy **kontekstowe** (nie statyczne) osadzenia z pełnego, potężnego modelu transformerowego.

### 5.7.–5.9. Wykorzystanie tylko pierwszego tokenu i pooler_output

**Model wykorzystujący tylko token `[CLS]`** (pierwszy token, zarezerwowany w BERT do reprezentowania całej sekwencji):

```python
class SentimentAnalysisModelBert2(nn.Module):
    def __init__(self, hidden_dim=64):
        super().__init__()
        self.bert = transformers.AutoModel.from_pretrained("bert-base-uncased")
        self.output = nn.Linear(self.bert.config.hidden_size, 1)

    def forward(self, encodings):
        bert_output = self.bert(**encodings)
        return self.output(bert_output.last_hidden_state[:, 0])
```

**Model wykorzystujący `pooler_output`:**

```python
class SentimentAnalysisModelBert3(nn.Module):
    def __init__(self, hidden_dim=64):
        super().__init__()
        self.bert = transformers.AutoModel.from_pretrained("bert-base-uncased")
        self.output = nn.Linear(self.bert.config.hidden_size, 1)

    def forward(self, encodings):
        bert_output = self.bert(**encodings)
        return self.output(bert_output.pooler_output)
```

**Trening modelu z poolerem (BERT zamrożony):**

```python
torch.manual_seed(42)
imdb_model_bert3 = SentimentAnalysisModelBert3().to(device)
imdb_model_bert3.bert.requires_grad_(False)

n_epochs = 5
# ...trening...
# Epoch 5/5: train metric: 74.58%, valid metric: 62.06%
```

> **Słabszy wynik** (≈62-75%) niż pełny model z GRU — **pooler BERT bez dostrajania nie jest optymalnie przygotowany** do zadania sentymentu (był trenowany do innego celu pomocniczego — *next sentence prediction*).

### 5.10. Dalszy trening, wraz z poolerem

```python
imdb_model_bert3.bert.pooler.requires_grad_(True)   # odmrażamy TYLKO pooler

history = train(imdb_model_bert3, optimizer, xentropy, accuracy,
                imdb_train_loader, imdb_valid_loader, n_epochs)
# Epoch 3/5: valid metric: 81.70%   <- poprawa!
```

> **Częściowe odmrożenie** (tylko poolera, nie całego BERT) — analogicznie do strategii z wykładu o CNN (odmrażanie warstw stopniowo) — daje **wyraźną poprawę** (62% → 82%), bo pooler dostraja się do naszego konkretnego zadania.

### 5.11.–5.13. Klasy do konkretnych zadań — BertForSequenceClassification

```python
from transformers import BertForSequenceClassification

torch.manual_seed(42)
bert_for_binary_clf = BertForSequenceClassification.from_pretrained(
    "bert-base-uncased", num_labels=2, dtype=torch.float16).to(device)
# Some weights ... newly initialized: ['classifier.bias', 'classifier.weight']
```

> Hugging Face dostarcza **gotowe klasy modeli do konkretnych zadań** (`BertForSequenceClassification`, `BertForQuestionAnswering`, itd.) — automatycznie dodają odpowiednią „głowę" (klasyfikator) na bazowym modelu, inicjalizowaną losowo (komunikat ostrzegawczy informuje o tym). **`dtype=torch.float16`** — model wczytany w **połowicznej precyzji** (mniej pamięci GPU, szybsze obliczenia, niewielka utrata dokładności).

**Użycie gotowego modelu (bez treningu):**

```python
encoding = bert_tokenizer(["This was a great movie!"])
with torch.no_grad():
    output = bert_for_binary_clf(
        input_ids=torch.tensor(encoding["input_ids"], device=device),
        attention_mask=torch.tensor(encoding["attention_mask"], device=device))
output.logits
# tensor([[-0.0121, 0.6313]])
torch.softmax(output.logits, dim=-1)
# tensor([[0.3445, 0.6558]])
```

> Bez dotrenowania klasyfikator daje **losowe-ish** wyniki (głowa była zainicjalizowana losowo) — ale model przyjmuje `labels` i liczy stratę:

```python
output = bert_for_binary_clf(..., labels=torch.tensor([1], device=device))
output.loss
# tensor(0.4224)
```

### 5.14.–5.17. Trainer API

**Trainer API** Hugging Face:
- pozwala na **bardzo łatwe dotrenowanie modelu** własnymi danymi,
- **zapisuje checkpointy, korzysta z GPU, loguje metryki**, itd.
- Korzysta z obiektami **`Dataset`** (nie `DataLoader`), ale **oczekuje tokenów, a nie ciągów znaków** — musimy najpierw zmapować zbiór:

```python
def tokenize_batch(batch):
    return bert_tokenizer(batch["text"], truncation=True, max_length=200)

tok_imdb_train_set = imdb_train_set.map(tokenize_batch, batched=True)
tok_imdb_valid_set = imdb_valid_set.map(tokenize_batch, batched=True)
tok_imdb_test_set = imdb_test_set.map(tokenize_batch, batched=True)
```

```python
def compute_accuracy(pred):
    return {"accuracy": (pred.label_ids == pred.predictions.argmax(-1)).mean()}
```

```python
from transformers import TrainingArguments

train_args = TrainingArguments(
    output_dir="my_imdb_model", num_train_epochs=2,
    per_device_train_batch_size=128, per_device_eval_batch_size=128,
    eval_strategy="epoch", logging_strategy="epoch", save_strategy="epoch",
    load_best_model_at_end=True, metric_for_best_model="accuracy",
    report_to="none")
```

```python
from transformers import DataCollatorWithPadding, Trainer

trainer = Trainer(
    bert_for_binary_clf, train_args, train_dataset=tok_imdb_train_set,
    eval_dataset=tok_imdb_valid_set, compute_metrics=compute_accuracy,
    data_collator=DataCollatorWithPadding(bert_tokenizer))
train_output = trainer.train()
```

> **`DataCollatorWithPadding`** — pełni rolę analogiczną do naszego ręcznego `collate_fn`, ale gotową: dynamicznie dopełnia batche. **`load_best_model_at_end=True`** — automatycznie wczytuje checkpoint z **najlepszą** wartością metryki po zakończeniu treningu (analogiczne do ręcznego *early stopping*). `Trainer` znacznie redukuje ilość kodu potrzebnego do dotrenowania modelu w porównaniu z ręczną pętlą `train()`.

### 5.18. Pipelines z Hugging Face

**Pipeline** to najwyższy poziom abstrakcji — gotowy, **dotrenowany** model do konkretnego zadania, użyteczny „z pudełka":

```python
from transformers import pipeline

model_name = "distilbert-base-uncased-finetuned-sst-2-english"
classifier_imdb = pipeline("sentiment-analysis", model=model_name,
                           truncation=True, max_length=512)
classifier_imdb(train_reviews[:10])
# [{'label': 'POSITIVE', 'score': 0.9996}, {'label': 'POSITIVE', 'score': 0.9999}, ...]
```

> **DistilBERT** to **mniejsza, szybsza wersja BERT** (destylacja wiedzy — *knowledge distillation*) — dobry kompromis szybkość/jakość. Model jest **już dotrenowany** na SST-2 (Stanford Sentiment Treebank) — możemy go użyć **bez żadnego własnego treningu**.

### 5.19. Bias w modelach ML

```python
countries = ["Iraq", "Thailand", "the USA", "Vietnam", "Poland"]
texts = [f"I am from {country}" for country in countries]
list(zip(countries, classifier_imdb(texts)))
# [('Iraq', {'label': 'NEGATIVE', 'score': 0.97}),
#  ('Thailand', {'label': 'POSITIVE', 'score': 0.99}),
#  ('the USA', {'label': 'POSITIVE', 'score': 0.96}),
#  ('Vietnam', {'label': 'NEGATIVE', 'score': 0.97}),
#  ('Poland', {'label': 'POSITIVE', 'score': 0.99})]
```

> **Niepokojący wynik:** zdanie „I am from Iraq" jest klasyfikowane jako **NEGATYWNE**, a „I am from Thailand" jako **POZYTYWNE** — choć żadne z nich nie wyraża żadnego sentymentu! To pokazuje, że **modele ML uczą się i utrwalają stereotypy / biasy obecne w danych treningowych** (powiązania słów z krajami w korpusach internetowych mogą odzwierciedlać medialne/społeczne uprzedzenia). To bezpośrednie nawiązanie do wcześniejszego przykładu $\text{Doctor} - \text{Man} + \text{Woman} = \text{Nurse}$ (sekcja 2.13) — **etyczny problem ML wymagający świadomości i aktywnego przeciwdziałania** (audyt modeli, debiasing, zróżnicowane dane treningowe).

---

## 6. Tłumaczenie maszynowe przy pomocy sieci encoder-decoder

### 6.1.–6.2. Prosty model NMT (Neural Machine Translation)

**Architektura encoder-decoder:**
- **Tekst oryginalny trafia na wejście enkodera**, a z dekodera wychodzi tłumaczenie.
- **Na wejście dekodera trafia tekst w języku docelowym** — w każdym kroku to, co **powinien był** z siebie wypluć (tzw. **teacher forcing** — przyspiesza trenowanie, bo dekoder nie musi „gonić" za własnymi błędami podczas treningu).
- **Dekoder dostaje jako pierwszy token `<s>`** (start-of-sequence, SoS), a powinien zakończyć wyjście tokenem **`</s>`** (end-of-sequence, EoS).
- **Na każdym kroku warstwa gęsta dekodera emituje logity** dla każdego tokenu w słowniku docelowym.
- **Podczas inferencji** nie mamy tekstu docelowego, więc **karmimy model tym, co już wyemitował** (autoregresywne generowanie, krok po kroku — bez teacher forcing).

### 6.3. Zbiór danych

```python
nmt_original_valid_set, nmt_test_set = load_dataset(
    path="ageron/tatoeba_mt_train", name="eng-spa",
    split=["validation", "test"])
split = nmt_original_valid_set.train_test_split(train_size=0.8, seed=42)
nmt_train_set, nmt_valid_set = split["train"], split["test"]
```

```python
nmt_train_set[0]
# {'source_text': 'Tom tried to break up the fight.',
#  'target_text': 'Tom trató de disolver la pelea.',
#  'source_lang': 'eng', 'target_lang': 'spa'}
```

### 6.4.–6.5. Trenujemy tokenizer (wspólny dla obu języków)

```python
def train_eng_spa():  # generator iterujący po całym tekście treningowym
    for pair in nmt_train_set:
        yield pair["source_text"]
        yield pair["target_text"]
```

```python
max_length = 256
vocab_size = 10_000

nmt_tokenizer_model = tokenizers.models.BPE(unk_token="<unk>")
nmt_tokenizer = tokenizers.Tokenizer(nmt_tokenizer_model)
nmt_tokenizer.enable_padding(pad_id=0, pad_token="<pad>")
nmt_tokenizer.enable_truncation(max_length=max_length)
nmt_tokenizer.pre_tokenizer = tokenizers.pre_tokenizers.Whitespace()
nmt_tokenizer_trainer = tokenizers.trainers.BpeTrainer(
    vocab_size=vocab_size, special_tokens=["<pad>", "<unk>", "<s>", "</s>"])
nmt_tokenizer.train_from_iterator(train_eng_spa(), nmt_tokenizer_trainer)
```

> **Jeden, wspólny tokenizer dla obu języków** (angielski i hiszpański) — uczony na tekstach **obu** języków razem. Pozwala to na **współdzielenie podsłów między językami** (np. nazwy własne, słowa o łacińskim pochodzeniu) i prostszą architekturę (jedna warstwa osadzeń dla obu stron).

```python
nmt_tokenizer.encode("I like soccer").ids
# [43, 401, 4381]
nmt_tokenizer.encode("<s> Me gusta el fútbol").ids
# [2, 396, 582, 219, 3356]
```

### 6.6.–6.8. Przygotowanie danych (klasa NmtPair, collate_fn)

```python
from collections import namedtuple

fields = ["src_token_ids", "src_mask", "tgt_token_ids", "tgt_mask"]
class NmtPair(namedtuple("NmtPairBase", fields)):
    def to(self, device):
        return NmtPair(self.src_token_ids.to(device), self.src_mask.to(device),
                       self.tgt_token_ids.to(device), self.tgt_mask.to(device))
```

> **`namedtuple`** z dodaną metodą `.to(device)` — wygodny sposób przenoszenia **wielu powiązanych tensorów** na GPU jednym wywołaniem, zachowując nazwane pola dla czytelności.

```python
def nmt_collate_fn(batch):
    src_texts = [pair['source_text'] for pair in batch]
    tgt_texts = [f"<s> {pair['target_text']} </s>" for pair in batch]
    src_encodings = nmt_tokenizer.encode_batch(src_texts)
    tgt_encodings = nmt_tokenizer.encode_batch(tgt_texts)
    src_token_ids = torch.tensor([enc.ids for enc in src_encodings])
    tgt_token_ids = torch.tensor([enc.ids for enc in tgt_encodings])
    src_mask = torch.tensor([enc.attention_mask for enc in src_encodings])
    tgt_mask = torch.tensor([enc.attention_mask for enc in tgt_encodings])
    inputs = NmtPair(src_token_ids, src_mask,
                     tgt_token_ids[:, :-1], tgt_mask[:, :-1])
    labels = tgt_token_ids[:, 1:]
    return inputs, labels
```

**Kluczowe szczegóły:**
- **`f"<s> {pair['target_text']} </s>"`** — dodajemy ręcznie tokeny start/end-of-sequence do tekstu docelowego.
- **`tgt_token_ids[:, :-1]`** jako **wejście** dekodera (wszystko poza ostatnim tokenem) i **`tgt_token_ids[:, 1:]`** jako **etykiety** (wszystko poza pierwszym tokenem) — to **dokładnie ten sam trik przesunięcia o 1**, co w `CharDataset` z sekcji 2! Model uczy się przewidywać **następny** token na podstawie poprzednich.

```python
batch_size = 32
nmt_train_loader = DataLoader(nmt_train_set, batch_size=batch_size,
                              collate_fn=nmt_collate_fn, shuffle=True)
nmt_valid_loader = DataLoader(nmt_valid_set, batch_size=batch_size, collate_fn=nmt_collate_fn)
nmt_test_loader = DataLoader(nmt_test_set, batch_size=batch_size, collate_fn=nmt_collate_fn)
```

### 6.9. Model — podstawowa architektura encoder-decoder

```python
class NmtModel(nn.Module):
    def __init__(self, vocab_size, embed_dim=512, pad_id=0, hidden_dim=512, n_layers=2):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim, padding_idx=pad_id)
        self.encoder = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers, batch_first=True)
        self.decoder = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers, batch_first=True)
        self.output = nn.Linear(hidden_dim, vocab_size)

    def forward(self, pair):
        src_embeddings = self.embed(pair.src_token_ids)
        tgt_embeddings = self.embed(pair.tgt_token_ids)
        src_lengths = pair.src_mask.sum(dim=1)
        src_packed = pack_padded_sequence(
            src_embeddings, lengths=src_lengths.cpu(),
            batch_first=True, enforce_sorted=False)
        _, hidden_states = self.encoder(src_packed)
        outputs, _ = self.decoder(tgt_embeddings, hidden_states)
        return self.output(outputs).permute(0, 2, 1)
```

**Wyjaśnienie krok po kroku:**
- **`self.embed`** — **wspólna** warstwa osadzeń dla obu języków (bo używamy wspólnego tokenizera).
- **Enkoder** przetwarza spakowaną (bez paddingu) sekwencję źródłową, zwracając **tylko finalny stan ukryty** (`hidden_states`), który podsumowuje **całe** zdanie wejściowe.
- **Dekoder** otrzymuje osadzenia tekstu docelowego (z teacher forcing) **oraz** `hidden_states` z enkodera jako **stan początkowy** — to jest właśnie „przekazanie kontekstu" z enkodera do dekodera.
- **`self.output(outputs).permute(0, 2, 1)`** — jak w modelu Szekspira: zamiana kształtu na zgodny z `CrossEntropyLoss`.

> To jest **klasyczna architektura sequence-to-sequence** (Sutskever i in., 2014) — **wąskie gardło** to pojedynczy wektor `hidden_states`, który musi „skompresować" całe znaczenie zdania źródłowego, niezależnie od jego długości (problem rozwiązywany przez mechanizm uwagi, sekcja 6.16+).

### 6.10. Trening

```python
torch.manual_seed(42)
vocab_size = nmt_tokenizer.get_vocab_size()
nmt_model = NmtModel(vocab_size).to(device)

n_epochs = 1
xentropy = nn.CrossEntropyLoss(ignore_index=0)  # ignorujemy tokeny <pad>
optimizer = torch.optim.NAdam(nmt_model.parameters(), lr=0.001)
accuracy = torchmetrics.Accuracy(task="multiclass", num_classes=vocab_size).to(device)

history = train(nmt_model, optimizer, xentropy, accuracy, nmt_train_loader, nmt_valid_loader, n_epochs)
# Epoch 1/1: train loss: 3.1347, train metric: 17.34%, valid metric: 20.26%
```

> **`ignore_index=0`** — kluczowy parametr! Mówi funkcji straty, by **całkowicie ignorowała** pozycje, gdzie etykieta to token `<pad>` (ID=0) — inaczej model byłby „karany/nagradzany" za przewidywanie paddingu, co nie ma sensu (i zaburzałoby uczenie).

### 6.11.–6.12. Funkcja do tłumaczenia tekstu (inferencja, greedy decoding)

```python
def translate(model, src_text, max_length=20, pad_id=0, eos_id=3):
    tgt_text = ""
    token_ids = []
    for index in range(max_length):
        batch, _ = nmt_collate_fn([{"source_text": src_text, "target_text": tgt_text}])
        with torch.no_grad():
            Y_logits = model(batch.to(device))
            Y_token_ids = Y_logits.argmax(dim=1)  # najlepszy token na każdej pozycji
            next_token_id = Y_token_ids[0, index]  # bierzemy TYLKO nowy token
        next_token = nmt_tokenizer.id_to_token(next_token_id)
        tgt_text += " " + next_token
        if next_token_id == eos_id:
            break
    return tgt_text
```

> **Autoregresywne generowanie:** w każdej iteracji dodajemy do `tgt_text` jeden nowy token, **doklejamy go do tekstu docelowego** i **ponownie uruchamiamy cały model** od zera z dłuższym tekstem docelowym (mniej efektywne niż mogłoby być, ale proste konceptualnie). Pętla **kończy się**, gdy model wyemituje token `</s>` (EoS).

```python
nmt_model.eval()
translate(nmt_model, "I like soccer.")
# ' Me gusta el fútbol . </s>'

translate(nmt_model, "I like to play soccer with my friends.")
# ' Me gusta jugar con mis amigos . </s>'
```

> Zauważ: drugie tłumaczenie **„zgubiło"** informację o „play soccer" — przetłumaczyło tylko „lubię grać z moimi przyjaciółmi", bez piłki nożnej! To typowy problem **wąskiego gardła** wektora kontekstu enkodera dla dłuższych zdań.

### 6.13.–6.15. Technika beam search

**Problem greedy decoding:** wybór najlepszego tokenu **w danym momencie** nie gwarantuje najlepszego **całego** tłumaczenia — czasem lepiej wybrać token o nieco niższym prawdopodobieństwie teraz, jeśli prowadzi do dużo lepszej kontynuacji.

**Przykład z wykładu** (gałąź decyzji):

```
Me → Me gustan → Me gustan los → Me gustan los fútbol?  (ślepy zaułek)
                                 Me gustan los jugadores!  (lepsza kontynuacja)
```

**Beam search** — utrzymuje **$k$ najlepszych częściowych hipotez** (zamiast tylko jednej, jak greedy) na każdym kroku, rozszerza każdą o możliwe kontynuacje, a następnie zachowuje $k$ najlepszych spośród **wszystkich** rozszerzeń.

```python
def beam_search(model, src_text, beam_width=3, max_length=20,
                verbose=False, length_penalty=0.6):
    top_translations = [(torch.tensor(0.), "")]
    for index in range(max_length):
        candidates = []
        for log_proba, tgt_text in top_translations:
            if tgt_text.endswith(" </s>"):
                candidates.append((log_proba, tgt_text))
                continue  # nie dodajemy tokenów po EOS
            batch, _ = nmt_collate_fn([{"source_text": src_text, "target_text": tgt_text}])
            with torch.no_grad():
                Y_logits = model(batch.to(device))
                Y_log_proba = F.log_softmax(Y_logits, dim=1)
                Y_top_log_probas = torch.topk(Y_log_proba, k=beam_width, dim=1)
            for beam_index in range(beam_width):
                next_token_log_proba = Y_top_log_probas.values[0, beam_index, index]
                next_token_id = Y_top_log_probas.indices[0, beam_index, index]
                next_token = nmt_tokenizer.id_to_token(next_token_id)
                next_tgt_text = tgt_text + " " + next_token
                candidates.append((log_proba + next_token_log_proba, next_tgt_text))

        def length_penalized_score(candidate, alpha=length_penalty):
            log_proba, text = candidate
            length = len(text.split())
            penalty = ((5 + length) ** alpha) / (6 ** alpha)
            return log_proba / penalty

        top_translations = sorted(candidates, key=length_penalized_score, reverse=True)[:beam_width]
    return top_translations[-1][1]
```

**Kluczowe elementy:**
- **`log_softmax`** + **sumowanie log-prawdopodobieństw** (`log_proba + next_token_log_proba`) — pracujemy w **logarytmach**, bo iloczyny wielu prawdopodobieństw (jeden na token) **bardzo szybko zanikają numerycznie** (*underflow*) — sumowanie logarytmów jest stabilne i matematycznie równoważne ($\log(ab) = \log a + \log b$).
- **`torch.topk(..., k=beam_width)`** — wybieramy $k$ **najlepszych kandydatów** na rozszerzenie, nie tylko jeden (jak `argmax` w greedy).
- **Length penalty** — kara za długość zniechęca model do preferowania **krótszych** sekwencji (sumy log-prob są zawsze ujemne i rosną — *w sensie mniej ujemne* — wraz z długością inaczej; bez kary model faworyzowałby bardzo krótkie tłumaczenia, bo mają mniej czynników do przemnożenia/zsumowania).

```python
beam_search(nmt_model, longer_text, beam_width=3)
# ' Me gusta jugar al golf con mis amigos . </s>'

longest_text = "I like to play soccer with my friends at the beach."
beam_search(nmt_model, longest_text, beam_width=3)
# ' Me gusta jugar al tenis con mis amigos en la playa . </s>'
```

> Beam search znalazł lepsze tłumaczenie niż greedy (zachowało „grać" — *jugar* — czego greedy zgubił), choć nadal **myli sport** (golf/tenis zamiast soccer) — to ograniczenie modelu (mały, prosty GRU bez uwagi), nie samego beam search.

### 6.16.–6.19. Mechanizmy uwagi (attention)

- **W 2014 Bahdanau, Cho i Bengio** zaproponowali technikę, która pozwala **dekoderowi skupić się w każdym kroku na odpowiednich słowach emitowanych przez enkoder**.
- **To bardzo skraca ścieżkę** np. z „soccer" do „fútbol" — bez uwagi informacja musiałaby przepłynąć przez **cały** stan ukryty enkodera (potencjalnie wiele kroków czasowych); z uwagą dekoder może „zajrzeć" **bezpośrednio** do odpowiedniego wyjścia enkodera.

**Architektura modelu z uwagą:**
- **Do dekodera trafia nie tylko ostatni stan ukryty enkodera, ale również jego wyjścia** (ze wszystkich kroków czasowych).
- **Wyjścia potrzebują agregacji** — wyliczana jest **ważona suma** tych wyjść.
- **Wagi $\alpha_{(t,i)}$** biorą się z małej sieci zwanej **warstwą uwagi** (*attention layer*) lub **modelem wyrównującym** (*alignment model*).
- Ta sieć przyjmuje **wyjście enkodera oraz poprzedni stan ukryty dekodera**.
- Stosujemy terminologię: **query** (stany ukryte dekodera), **key** (wyjścia enkodera) oraz **value** (tu również wyjścia enkodera).
- To jest mechanizm uwagi **Bahdanau** (lub *concatenative attention* — bo do wyliczenia wag łączy query i key, przepuszczając przez sieć).

**Uwaga Luonga (*multiplicative attention*):**
- **Wyliczamy iloczyn skalarny** wyjść enkodera i poprzedniego stanu ukrytego dekodera.
- **To dobra miara podobieństwa** — i da się **bardzo sprawnie wyliczać na nowoczesnym sprzęcie** (mnożenie macierzy jest mocno zoptymalizowane na GPU).
- **Wektory muszą mieć identyczny wymiar** (w przeciwieństwie do Bahdanau, gdzie sieć uwagi mogła mapować różne wymiary).
- **Luong zaproponował też, aby użyć $\mathbf{h}_{(t)}$ zamiast $\mathbf{h}_{(t-1)}$**, co upraszcza cały proces, bo nie musimy pamiętać poprzedniego stanu.

### 6.20. Uwaga Luonga, implementacja

```python
def attention(query, key, value):  # note: dq == dk and Lk == Lv
    scores = query @ key.transpose(1, 2)  # [B,Lq,dq] @ [B,dk,Lk] = [B, Lq, Lk]
    weights = torch.softmax(scores, dim=-1)  # [B, Lq, Lk]
    return weights @ value  # [B, Lq, Lk] @ [B, Lv, dv] = [B, Lq, dv]
```

**Wymiary (z wykładu):**
- $B$ = rozmiar wsadu,
- $L_q$ = maksymalna długość zapytania (*query*) w tym wsadzie,
- $L_k$ = maksymalna długość klucza (*key*) = $L_v$ = maksymalna długość wartości (*value*),
- $d_q$ = wymiar osadzenia zapytania = $d_k$ = wymiar osadzenia klucza,
- $d_v$ = wymiar osadzenia wartości.

> **To jest właśnie podstawowy wzór self-attention/cross-attention**, na którym opiera się architektura **Transformer**! `scores = query @ key^T` mierzy **podobieństwo** każdej pozycji query do każdej pozycji key (iloczyn skalarny — uwaga Luonga). `softmax` normalizuje te podobieństwa do **wag sumujących się do 1**. Finalne `weights @ value` to **ważona suma wartości**, gdzie waga zależy od tego, jak bardzo dana pozycja klucza „odpowiada" zapytaniu.

### 6.21.–6.22. Model z uwagą

```python
class NmtModelWithAttention(nn.Module):
    def __init__(self, vocab_size, embed_dim=512, pad_id=0, hidden_dim=512, n_layers=2):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim, padding_idx=pad_id)
        self.encoder = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers, batch_first=True)
        self.decoder = nn.GRU(embed_dim, hidden_dim, num_layers=n_layers, batch_first=True)
        self.output = nn.Linear(2 * hidden_dim, vocab_size)

    def forward(self, pair):
        src_embeddings = self.embed(pair.src_token_ids)
        tgt_embeddings = self.embed(pair.tgt_token_ids)
        src_lengths = pair.src_mask.sum(dim=1)
        src_packed = pack_padded_sequence(
            src_embeddings, lengths=src_lengths.cpu(),
            batch_first=True, enforce_sorted=False)
        encoder_outputs_packed, hidden_states = self.encoder(src_packed)
        decoder_outputs, _ = self.decoder(tgt_embeddings, hidden_states)
        encoder_outputs, _ = pad_packed_sequence(encoder_outputs_packed, batch_first=True)
        attn_output = attention(query=decoder_outputs, key=encoder_outputs, value=encoder_outputs)
        combined_output = torch.cat((attn_output, decoder_outputs), dim=-1)
        return self.output(combined_output).permute(0, 2, 1)
```

**Kluczowe różnice względem modelu bez uwagi:**
- **`pad_packed_sequence`** — odpakowujemy spakowane wyjścia enkodera **z powrotem** do zwykłego tensora (z paddingiem), bo potrzebujemy **wszystkich** kroków czasowych enkodera (nie tylko finalnego stanu) do liczenia uwagi.
- **`attention(query=decoder_outputs, key=encoder_outputs, value=encoder_outputs)`** — w każdym kroku dekodera obliczamy uwagę względem **wszystkich** wyjść enkodera — to jest mechanizm Luonga (iloczyn skalarny + softmax + ważona suma).
- **`torch.cat((attn_output, decoder_outputs), dim=-1)`** — łączymy kontekst z uwagi z bieżącym stanem dekodera, stąd **`2 * hidden_dim`** w warstwie wyjściowej.

```python
torch.manual_seed(42)
nmt_attn_model = NmtModelWithAttention(vocab_size).to(device)
# ... trening ...
# Epoch 1/1: train loss: 3.0103, train metric: 18.02%, valid metric: 20.45%
```

### 6.23. Model z uwagą, test

```python
print(longest_text)
translate(nmt_attn_model, longest_text)
# 'I like to play soccer with my friends at the beach.'
# ' Me gusta jugar al playa con mis amigos . </s>'
```

> Po tylko **1 epoce** treningu model z uwagą daje wynik podobnej jakości co model bez uwagi po dłuższym treningu — w pełnoskalowych eksperymentach (więcej epok) **mechanizm uwagi znacząco poprawia jakość tłumaczenia**, szczególnie dla **dłuższych zdań**, gdzie problem wąskiego gardła pojedynczego wektora kontekstu jest najbardziej widoczny.

---

## 7. Dodatek — teoria spoza prezentacji

### 7.1. Od RNN+Attention do Transformerów

Model z sekcji 6.21 (GRU encoder-decoder + attention) jest **historycznym pomostem** do architektury **Transformer** (Vaswani i in., *„Attention Is All You Need"*, 2017):

| Element | RNN + Attention (ten wykład) | Transformer |
|---|---|---|
| Przetwarzanie sekwencji | sekwencyjne (GRU/LSTM, krok po kroku) | równoległe (self-attention na wszystkich pozycjach naraz) |
| Uwaga | dekoder→enkoder (cross-attention) | self-attention WEWNĄTRZ enkodera i dekodera + cross-attention |
| Trening | wolniejszy (sekwencyjna zależność) | szybszy (pełna równoległość) |
| Pozycja w sekwencji | naturalna (kolejność przetwarzania) | wymaga jawnego kodowania pozycji (*positional encoding*) |

> Transformer **zastąpił** rekurencję **w pełni mechanizmem uwagi** — stąd tytuł pracy. To bezpośrednia kontynuacja idei z tego wykładu.

### 7.2. BLEU — metryka oceny tłumaczeń maszynowych

**BLEU (*Bilingual Evaluation Understudy*)** to standardowa metryka oceny jakości tłumaczenia maszynowego, porównująca **n-gramy** wygenerowanego tłumaczenia z tłumaczeniem referencyjnym (ludzkim):

$$
\text{BLEU} = \text{BP} \cdot \exp\left(\sum_{n=1}^{N} w_n \log p_n\right)
$$

gdzie $p_n$ to precyzja dla n-gramów rzędu $n$, a BP to **kara za zbyt krótkie tłumaczenia** (*brevity penalty*) — koncepcyjnie podobna do *length penalty* w beam search z sekcji 6.14.

### 7.3. Subword regularization

Podczas treningu tokenizerów Unigram LM można wprowadzić **losowość** w segmentacji (ten sam tekst może być podzielony na różne sposoby przy różnych próbach) — to forma **augmentacji danych** dla NLP, zwiększająca odporność modelu na różne sposoby tokenizacji tego samego słowa.

### 7.4. Porównanie metod tokenizacji — podsumowanie

| Metoda | Zasada budowy słownika | Przykład modeli | Uwagi |
|---|---|---|---|
| **Word-level** | każde słowo to token | starsze modele | ogromny słownik, problem OOV (out-of-vocabulary) |
| **Character-level** | każdy znak to token | char-rnn (sekcja 2) | mały słownik, ale tokeny niosą mało znaczenia |
| **BPE** | łączy najczęstsze pary znaków | GPT-2, GPT-3 | dobry kompromis |
| **BBPE** | BPE na poziomie bajtów | GPT-2 (rzeczywiście), nowsze LLM | nigdy `<unk>`, radzi sobie z każdym Unicode |
| **WordPiece** | maksymalizuje wynik (score), nie częstość | BERT | preferuje silne kolokacje |
| **Unigram LM** | usuwa tokeny od dużego słownika | SentencePiece (T5, ALBERT) | probabilistyczne podejście |

### 7.5. Stateful RNN (RNN z zachowywanym stanem między batchami)

W prezentacji wspomniano `epoch_callback` i zmienne `stateful_train_loader` (w `del_vars`), co sugeruje, że w pełnej wersji materiałów istnieje wariant **stateful RNN** — gdzie stan ukryty **nie jest resetowany** między kolejnymi batchami, a kontynuowany (przydatne, gdy batch $n+1$ to dosłowna kontynuacja tekstu z batcha $n$, a nie niezależna próbka). Wymaga to specjalnej organizacji danych (sekwencyjne, nieprzemieszane batche) i ręcznego zarządzania stanem (`epoch_callback` mógłby np. zerować stan na początku każdej epoki).

### 7.6. Cross-entropy z `ignore_index` — szczegóły

`nn.CrossEntropyLoss(ignore_index=0)` jest kluczowe dla każdego zadania sequence-to-sequence z paddingiem (modele językowe, NMT). Bez tego model byłby **karany za błędne przewidywanie tokenów paddingowych**, co:
- spowalnia konwergencję (model „uczy się" przewidywać padding, co nie jest użyteczne),
- zniekształca metryki (accuracy liczona też na pustych pozycjach).

### 7.7. Embedding tying (współdzielenie wag)

Częsta optymalizacja w modelach językowych: **warstwa wyjściowa** (mapująca stan ukryty na logity słownika) może **współdzielić wagi** z warstwą **wejściowych osadzeń** (transponowane) — redukuje liczbę parametrów i często poprawia jakość, bo „rozumienie" tokenu (embedding) i „generowanie" tokenu (logit) są ze sobą semantycznie powiązane.

### 7.8. Praktyczne wskazówki NLP

- **Zawsze maskuj padding** — w funkcji straty (`ignore_index`) i w obliczeniach RNN (`pack_padded_sequence`) — to jeden z najczęstszych błędów początkujących w NLP.
- **Wybór tokenizera ma duże znaczenie** — BPE/BBPE/WordPiece dają drastycznie różne wyniki dla rzadkich słów, języków aglutynacyjnych (polski!) czy kodu programistycznego.
- **Transfer learning to standard w NLP** — rzadko trenuje się duże modele od zera; częściej dostraja się (*fine-tuning*) gotowe modele (BERT, GPT, itd.) do konkretnego zadania.
- **Bias jest realnym problemem** — testuj modele na zróżnicowanych przykładach (jak w sekcji 5.19), nie tylko na metrykach agregowanych po całym zbiorze testowym.
- **Beam search vs. greedy** — w praktyce beam width 3-5 daje dobry kompromis jakość/szybkość; bardzo duży beam width rzadko pomaga dalej i może nawet **szkodzić** (zbyt „bezpieczne", generyczne tłumaczenia).

---

## Pytania kontrolne (możliwe na egzaminie)

1. Dlaczego potrzebujemy osadzeń (embeddings) zamiast surowych identyfikatorów tokenów lub one-hot encoding?
2. Wyjaśnij analogię $\text{King} - \text{Man} + \text{Woman} = \text{Queen}$. Jaki problem ilustruje przykład z $\text{Doctor}-\text{Man}+\text{Woman}=\text{Nurse}$?
3. Czym różni się `CharDataset` (sekcja 2) od `TimeSeriesDataset` (poprzedni wykład) w sposobie definiowania etykiet?
4. Wyjaśnij wpływ parametru temperatury na generowanie tekstu. Co się dzieje dla $T \to 0$ i $T \to \infty$?
5. Czym różni się greedy decoding od próbkowania (sampling) przy generowaniu tekstu?
6. Opisz różnicę między tokenizacją BPE, WordPiece i Unigram LM.
7. Dlaczego BBPE (Byte-level BPE) nigdy nie zwraca tokenu `<unk>`?
8. Co to jest maska uwagi (attention mask) i do czego służy?
9. Wyjaśnij problem paddingu w RNN. Jak go rozwiązują `pack_padded_sequence`/`pad_packed_sequence`?
10. Dlaczego ważne jest użycie `ignore_index` w `CrossEntropyLoss` przy pracy z paddingiem?
11. Czym jest dwukierunkowe RNN i kiedy warto je stosować? Jaką modyfikację architektury wymaga?
12. Wyjaśnij różnicę między statycznymi osadzeniami z BERT (`word_embeddings`) a kontekstowymi osadzeniami (`last_hidden_state`).
13. Co to jest `pooler_output` w BERT? Dlaczego model z poolerem (bez dostrajania) dał słabszy wynik niż model z GRU na kontekstowych osadzeniach?
14. Wyjaśnij strategię częściowego odmrażania modelu (np. tylko pooler BERT). Dlaczego to pomogło?
15. Czym jest bias w modelach ML? Podaj przykład z wykładu i zaproponuj, jak można go wykryć.
16. Opisz architekturę encoder-decoder dla tłumaczenia maszynowego. Co to jest teacher forcing?
17. Jaki jest „trick przesunięcia o 1" we wejściu/etykietach dekodera NMT? Porównaj z analogicznym trickiem w `CharDataset`.
18. Wyjaśnij ograniczenie prostego modelu encoder-decoder (wąskie gardło wektora kontekstu).
19. Co to jest beam search? Czym różni się od greedy decoding? Po co potrzebna jest kara za długość (length penalty)?
20. Dlaczego w beam search pracujemy w przestrzeni logarytmów prawdopodobieństw, a nie samych prawdopodobieństw?
21. Wyjaśnij mechanizm uwagi Bahdanau. Czym różni się od uwagi Luonga?
22. Napisz/zinterpretuj wzór `attention(query, key, value)`. Co reprezentują query, key i value w kontekście NMT?
23. Jak mechanizm uwagi pomaga rozwiązać problem wąskiego gardła w modelu encoder-decoder?
24. Czym jest Trainer API w Hugging Face i jakie korzyści daje względem ręcznej pętli treningowej?
25. Co to są pipelines w Hugging Face? Podaj przykład użycia.
26. Jakie są związki między architekturą RNN+attention z tego wykładu a Transformerami?

---

# Modele o architekturze transformerów i czatboty — notatka egzaminacyjna

**Przedmiot:** Uczenie maszynowe 2025/2026
**Wykładowca:** dr inż. Sebastian Ernst
**Temat wykładu:** Modele o architekturze transformerów i czatboty

---

## Spis treści

1. [Architektura transformerów](#1-architektura-transformerów)
2. [Tłumacz oparty o transformery](#2-tłumacz-oparty-o-transformery)
3. [Modele encoder-only](#3-modele-encoder-only)
4. [Modele decoder-only](#4-modele-decoder-only)
5. [Czatboty](#5-czatboty)
6. [Dodatek — teoria spoza prezentacji](#6-dodatek--teoria-spoza-prezentacji)

---

## 1. Architektura transformerów

### 1.1. *Attention Is All You Need*

W artykule z **2017** (Vaswani i in.) zaproponowano model typu **encoder-decoder**, który **działa tak jak omawiany wcześniej model rekurencyjny** (z poprzedniego wykładu NMT) — ale bez rekurencji.

### 1.2. W takim razie czym się różni?

- **Nie ma żadnych warstw rekurencyjnych (ani konwolucyjnych)**, tylko zwykłe gęste — **mniej problemów z treningiem i łatwiej rozproszyć** (równoległe przetwarzanie wszystkich pozycji w sekwencji, a nie sekwencyjne krok po kroku jak w RNN).
- **Nowy rodzaj mechanizmu uwagi** — **multi-head attention (MHA)**.
- **Pierwotnie zaprojektowany do tłumaczenia (NMT)**, ale szybko okazało się, że nadaje się do bardzo różnych rzeczy.
- **2018 — pierwszy model GPT** (OpenAI), zawierający **sam dekoder**, który świetnie radził sobie z **generowaniem tekstu**.
- **Kilka miesięcy później — model BERT** (Google), zawierający **sam enkoder**; on z kolei doskonale nadaje się do **analizy języka naturalnego**.

### 1.3. Czatboty — kontekst historyczny

- **W listopadzie 2022 OpenAI opublikowało ChatGPT**, oparty na modelu **GPT-3.5-turbo**.
- **Wkrótce później** — Perplexity AI, Google Gemini (oryg. Bard), Anthropic Claude, Mistral AI, DeepSeek, itd.
- **Modele frontier są najczęściej zamknięte** i da się do nich dostać tylko przez UI albo API…
- …**ale jest też wiele modeli open weights** — np. DeepSeek R1 czy Mistral-7B.

### 1.4. Pierwotna architektura transformerów: enkoder

- **Enkoder stopniowo przekształca wejścia** (np. sekwencje tokenów) aż reprezentacja każdego tokenu przybiera jego **dokładne znaczenie w kontekście zdania**.
- **Każda jego warstwa przyjmuje i zwraca tensory** o kształcie **[rozmiar wsadu, maksymalna długość, wymiar osadzenia]**.
- **Wejścia są stopniowo przekształcane (transformowane)** — np. „I'm **like** a cat" vs. „I **like** my cat" — to samo słowo „like" otrzymuje **różną reprezentację** w zależności od kontekstu.

### 1.5. Pierwotna architektura transformerów: dekoder

- **Dekoder korzysta z tych bogatych, kontekstowych danych** (z enkodera), **a także z dotychczasowych efektów swojej pracy** (autoregresywne generowanie).
- Tu również mamy przekształcanie reprezentacji tokenów, a rozmiary są **analogiczne do enkodera**.
- **Wyjście dekodera ma kształt [rozmiar wsadu, maksymalna długość, wielkość słownika]** — bo na końcu emitujemy logity dla każdego tokenu słownika.

### 1.6. Nowe elementy w porównaniu do RNN

- **MHA w enkoderze** aktualizuje reprezentacje tokenów, **patrząc na wszystkie tokeny w tym samym zdaniu** — to jest **self-attention**.
- **MMHA (Masked MHA) w dekoderze** robi to samo, ale **nie patrzy na tokeny „przyszłe"** — jest to **warstwa przyczynowa** (*causal layer*). **Inaczej dekoder by oszukiwał** (widziałby odpowiedź, którą ma wygenerować).
- **Wyższa MHA w dekoderze** dodaje „spojrzenie" na **wyjście enkodera** — **cross-attention** — np. soccer + el → fútbol (analogicznie do mechanizmu uwagi z poprzedniego wykładu o NMT).
- **Kodowania pozycyjne** (*positional encodings*) oznaczają **położenie tokenu w zdaniu** — są potrzebne, bo **dla warstw gęstych pozycja nie ma znaczenia** (w przeciwieństwie do RNN, gdzie kolejność przetwarzania niesie informację o pozycji).

### 1.7. Kodowania pozycyjne, implementacja

```python
class PositionalEmbedding(nn.Module):
    def __init__(self, max_length, embed_dim, dropout=0.1):
        super().__init__()
        self.pos_embed = nn.Parameter(torch.randn(max_length, embed_dim) * 0.02)
        self.dropout = nn.Dropout(dropout)

    def forward(self, X):
        return self.dropout(X + self.pos_embed[:X.size(1)])
```

> **Wyjaśnienie:** `self.pos_embed` to **uczona** macierz (osadzenie pozycyjne) o rozmiarze `[max_length, embed_dim]` — każda pozycja w sekwencji (0, 1, 2, ...) ma swój **własny, trenowalny wektor**. W `forward` dodajemy odpowiedni fragment tej macierzy do osadzeń tokenów — `X.size(1)` to bieżąca długość sekwencji (możemy mieć krótsze sekwencje niż `max_length`). Mnożenie przez `0.02` to drobna inicjalizacja, by na początku osadzenia pozycyjne nie dominowały nad osadzeniami tokenów.

**Demo:**

```python
embed_dim = 4
max_length = 4
pos_embedding = PositionalEmbedding(max_length, embed_dim)
embeddings = torch.randn(2, max_length, embed_dim)
embeddings_with_pos = pos_embedding(embeddings)
embeddings_with_pos.shape
# torch.Size([2, 4, 4])
```

> Zauważ, że niektóre wartości w wyniku są **wyzerowane** (np. `0.0000`) — to efekt **dropout** zastosowanego w `forward` (domyślnie `dropout=0.1`), losowo wyłączającego część wartości podczas treningu.

### 1.8. Scaled dot-product attention

**Podstawowy wzór mechanizmu uwagi** (taki sam, jak uwaga Luonga z poprzedniego wykładu, ale ze skalowaniem):

$$
\text{Attention}(\mathbf{Q}, \mathbf{K}, \mathbf{V}) = \text{softmax}\left(\frac{\mathbf{Q}\mathbf{K}^\top}{\sqrt{d_\mathbf{k}}}\right)\mathbf{V}
$$

**Oznaczenia:**
- $\mathbf{Q}$ — **query**, np. zdanie w języku źródłowym lub docelowym (zależnie od warstwy), $[L_q, d_q]$,
- $\mathbf{K}$ — **key**, $[L_k, d_k]$, gdzie $d_k = d_q$,
- $\mathbf{V}$ — **value**, $[L_v, d_v]$, gdzie $L_v = L_k$,
- $\mathbf{Q}\mathbf{K}^\top$ ma kształt $[L_q, L_k]$ i zawiera **oceny podobieństwa** dla każdej pary query/key; do każdego wiersza stosujemy softmax,
- $\frac{1}{\sqrt{d_k}}$ — **współczynnik skalowania**, aby **nie nasycić softmax** (bez skalowania, dla dużych $d_k$, iloczyny skalarne mogłyby być bardzo duże, co prowadzi do bardzo „ostrego" softmax i zanikających gradientów),
- Niektóre pary key/value możemy **zamaskować**, dodając bardzo dużą liczbę ujemną (`-torch.inf`) — **softmax wtedy da zero**.

### 1.9. Multi-head attention (MHA)

- Jest to **zbiór warstw dot-product attention**, zwanych **attention heads** (głowami uwagi).
- **Wyjście wszystkich warstw podlega konkatenacji.**

**Motywacja:**
- Enkoder prawdopodobnie zawarł w kodowaniu słowa „like" jego **znaczenie**, fakt że jest **czasownikiem**, w **czasie teraźniejszym**, i inne użyteczne rzeczy.
- Znamy też **położenie tokenu** dzięki kodowaniu pozycyjnemu.
- **Przy jednej warstwie uwagi**, musielibyśmy do wszystkiego się dostawać **„w jednym strzale"**.
- **Przy MHA**, każda „głowa" może **skupić się na określonym aspekcie** — np. jedna głowa może wyłapywać zależności syntaktyczne, inna semantyczne, inna pozycyjne.

### 1.10. MHA, oznaczenia

- $B$: rozmiar wsadu,
- $h$: liczba głów (*heads*),
- $L_q$: max długość query (we wsadzie),
- $L_k$: max długość key (we wsadzie),
- $L_v$: max długość value (we wsadzie); $L_v = L_k$,
- $d$: `embed_dim // h` (wymiar każdej głowy).

### 1.11. MHA, implementacja

```python
class MultiheadAttention(nn.Module):
    def __init__(self, embed_dim, num_heads, dropout=0.1):
        super().__init__()
        self.h = num_heads
        self.d = embed_dim // num_heads
        self.q_proj = nn.Linear(embed_dim, embed_dim)
        self.k_proj = nn.Linear(embed_dim, embed_dim)
        self.v_proj = nn.Linear(embed_dim, embed_dim)
        self.out_proj = nn.Linear(embed_dim, embed_dim)
        self.dropout = nn.Dropout(dropout)

    def split_heads(self, X):
        return X.view(X.size(0), X.size(1), self.h, self.d).transpose(1, 2)
```

```python
def forward(self, query, key, value, attn_mask=None, key_padding_mask=None):
    q = self.split_heads(self.q_proj(query))  # (B, h, Lq, d)
    k = self.split_heads(self.k_proj(key))    # (B, h, Lk, d)
    v = self.split_heads(self.v_proj(value))  # (B, h, Lv, d) with Lv=Lk
    scores = q @ k.transpose(2, 3) / self.d**0.5  # (B, h, Lq, Lk)

    # Masking support:
    if attn_mask is not None:
        scores = scores.masked_fill(attn_mask, -torch.inf)  # (B, h, Lq, Lk)
    if key_padding_mask is not None:
        mask = key_padding_mask.unsqueeze(1).unsqueeze(2)  # (B, 1, 1, Lk)
        scores = scores.masked_fill(mask, -torch.inf)  # (B, h, Lq, Lk)

    weights = scores.softmax(dim=-1)  # (B, h, Lq, Lk)
    Z = self.dropout(weights) @ v  # (B, h, Lq, d)
    Z = Z.transpose(1, 2)  # (B, Lq, h, d)
    Z = Z.reshape(Z.size(0), Z.size(1), self.h * self.d)  # (B, Lq, h × d)
    return (self.out_proj(Z), weights)  # (B, Lq, h × d)

MultiheadAttention.forward = forward
```

**Wyjaśnienie krok po kroku:**
- **`q_proj`, `k_proj`, `v_proj`** — trzy **niezależne projekcje liniowe** uczące się, jak przekształcić wejściowe osadzenia w odpowiednio query, key, value.
- **`split_heads`** — przekształca tensor `[B, L, embed_dim]` na `[B, h, L, d]` — **dzieli** pełną wymiarowość osadzenia na `h` niezależnych „głów" o wymiarze `d` każda. `transpose(1, 2)` zamienia kolejność wymiarów, by `h` (liczba głów) było na drugiej pozycji — ułatwia to równoległe przetwarzanie wszystkich głów.
- **`scores = q @ k.transpose(2, 3) / self.d**0.5`** — to dokładnie wzór scaled dot-product attention, ale wykonany **równolegle dla wszystkich głów na raz** (wymiar `h` jest po prostu kolejnym wymiarem wsadowym w operacjach macierzowych).
- **`attn_mask`** — maska **przyczynowa** (causal) — blokuje „zaglądanie w przyszłość" w dekoderze.
- **`key_padding_mask`** — maska **paddingu** — blokuje zwracanie uwagi na tokeny `<pad>`.
- **`Z.reshape(...)`** — **łączy** (konkatenuje) wyjścia wszystkich głów z powrotem w jeden wektor o pełnej wymiarowości `embed_dim`.
- **`self.out_proj(Z)`** — finalna projekcja liniowa łącząca informacje z wszystkich głów.
- Funkcja zwraca **parę**: wynik uwagi oraz **wagi uwagi** (`weights`) — przydatne do wizualizacji/interpretacji, na co które tokeny „patrzy" model.

### 1.12. Implementacja MHA z PyTorch

PyTorch ma wbudowany moduł **`nn.MultiheadAttention`**, który ma **dodatkowe optymalizacje** oraz **identyczny interfejs** — plus kilka dodatkowych argumentów:
- **`batch_first`** — czy wymiar wsadów występuje jako pierwszy (domyślnie `False`),
- metoda `forward()` ma argument **`need_weights`** wskazujący, czy potrzebujemy wykorzystywać same wagi — w przypadku ustawienia na `False` metoda **nic nie zwraca** (oszczędność pamięci/obliczeń),
- ma ona też argument **`is_causal`** — jeżeli mamy `attn_mask` i jest to maska przyczynowa, to ustawienie go na `True` pozwala na **dodatkowe wewnętrzne optymalizacje**.

### 1.13. Warstwa enkodera

```python
class TransformerEncoderLayer(nn.Module):
    def __init__(self, d_model, nhead, dim_feedforward=2048, dropout=0.1):
        super().__init__()
        self.self_attn = MultiheadAttention(d_model, nhead, dropout)
        self.linear1 = nn.Linear(d_model, dim_feedforward)
        self.dropout = nn.Dropout(dropout)
        self.linear2 = nn.Linear(dim_feedforward, d_model)
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)

    def forward(self, src, src_mask=None, src_key_padding_mask=None):
        attn, _ = self.self_attn(src, src, src, attn_mask=src_mask,
                                 key_padding_mask=src_key_padding_mask)
        Z = self.norm1(src + self.dropout(attn))
        ff = self.dropout(self.linear2(self.dropout(self.linear1(Z).relu())))
        return self.norm2(Z + ff)
```

**Wyjaśnienie:**
- **`self.self_attn(src, src, src, ...)`** — **self-attention**: query, key i value to **to samo** wejście `src` — każdy token „patrzy" na wszystkie tokeny **w tym samym** zdaniu (włącznie z samym sobą).
- **`Z = self.norm1(src + self.dropout(attn))`** — to jest **residual connection** (skip connection, jak w ResNet!) + **LayerNorm**. Dodajemy oryginalne wejście do wyniku uwagi (`src + attn`), zamiast całkowicie je zastępować — pomaga to z gradientami w głębokich sieciach (podobnie jak w ResNet).
- **Druga część** (`linear1` → ReLU → `linear2`) to **feed-forward network (FFN)** — prosta sieć dwuwarstwowa zastosowana **niezależnie do każdej pozycji** w sekwencji, z większą wymiarowością pośrednią (`dim_feedforward=2048` przy `d_model=512` w oryginalnej architekturze).
- **Druga residual connection + LayerNorm** (`norm2(Z + ff)`) analogicznie do pierwszej.

> **Struktura warstwy enkodera:** Self-Attention → Add & Norm → Feed-Forward → Add & Norm. Ten wzorzec **„sub-layer + residual + norm"** powtarza się w każdej warstwie transformera.

### 1.14. Warstwa dekodera

```python
class TransformerDecoderLayer(nn.Module):
    def __init__(self, d_model, nhead, dim_feedforward=2048, dropout=0.1):
        super().__init__()
        self.self_attn = MultiheadAttention(d_model, nhead, dropout)
        self.multihead_attn = MultiheadAttention(d_model, nhead, dropout)
        self.dropout = nn.Dropout(dropout)
        self.linear1 = nn.Linear(d_model, dim_feedforward)
        self.linear2 = nn.Linear(dim_feedforward, d_model)
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.norm3 = nn.LayerNorm(d_model)

    def forward(self, tgt, memory, tgt_mask=None, memory_mask=None,
                tgt_key_padding_mask=None, memory_key_padding_mask=None):
        attn1, _ = self.self_attn(tgt, tgt, tgt,
                                  attn_mask=tgt_mask,
                                  key_padding_mask=tgt_key_padding_mask)
        Z = self.norm1(tgt + self.dropout(attn1))
        attn2, _ = self.multihead_attn(Z, memory, memory, attn_mask=memory_mask,
                                       key_padding_mask=memory_key_padding_mask)
        Z = self.norm2(Z + self.dropout(attn2))
        ff = self.dropout(self.linear2(self.dropout(self.linear1(Z).relu())))
        return self.norm3(Z + ff)
```

**Wyjaśnienie — trzy podwarstwy dekodera:**
1. **`self.self_attn(tgt, tgt, tgt, attn_mask=tgt_mask, ...)`** — **masked self-attention** (MMHA): tokeny docelowe „patrzą" na siebie wzajemnie, ale **`tgt_mask`** (maska przyczynowa) blokuje patrzenie w przyszłość.
2. **`self.multihead_attn(Z, memory, memory, ...)`** — **cross-attention**: query to bieżący stan dekodera (`Z`), a **key i value to `memory`** — wyjście **enkodera**! To dokładnie ten mechanizm uwagi, który widzieliśmy w poprzednim wykładzie (uwaga Bahdanau/Luonga) — dekoder „zagląda" do reprezentacji zdania źródłowego.
3. **Feed-forward** — identyczne jak w enkoderze.

> Stąd **trzy** normalizacje (`norm1`, `norm2`, `norm3`) — jedna po każdej podwarstwie.

### 1.15. Enkoder (z n warstw)

```python
from copy import deepcopy

class TransformerEncoder(nn.Module):
    def __init__(self, encoder_layer, num_layers, norm=None):
        super().__init__()
        self.layers = nn.ModuleList([deepcopy(encoder_layer) for _ in range(num_layers)])
        self.norm = norm

    def forward(self, src, mask=None, src_key_padding_mask=None):
        Z = src
        for layer in self.layers:
            Z = layer(Z, mask, src_key_padding_mask)
        if self.norm is not None:
            Z = self.norm(Z)
        return Z
```

> **`deepcopy(encoder_layer)`** — tworzymy `num_layers` **niezależnych kopii** pojedynczej warstwy (każda ze swoimi własnymi, niezależnie trenowanymi wagami) — **nie** współdzielimy wag między warstwami transformera (w przeciwieństwie do RNN, gdzie ta sama komórka jest używana w każdym kroku czasowym!). `nn.ModuleList` rejestruje wszystkie kopie jako submoduły, by PyTorch poprawnie śledził ich parametry.

### 1.16. Dekoder (z n warstw)

```python
class TransformerDecoder(nn.Module):
    def __init__(self, decoder_layer, num_layers, norm=None):
        super().__init__()
        self.layers = nn.ModuleList([deepcopy(decoder_layer) for _ in range(num_layers)])
        self.norm = norm

    def forward(self, tgt, memory, tgt_mask=None, memory_mask=None,
                tgt_key_padding_mask=None, memory_key_padding_mask=None):
        Z = tgt
        for layer in self.layers:
            Z = layer(Z, memory, tgt_mask, memory_mask,
                      tgt_key_padding_mask, memory_key_padding_mask)
        if self.norm is not None:
            Z = self.norm(Z)
        return Z
```

> Analogicznie do enkodera, ale każda warstwa dekodera dodatkowo otrzymuje **`memory`** (wyjście enkodera) na potrzeby cross-attention.

### 1.17. Cały transformer

```python
class Transformer(nn.Module):
    def __init__(self, d_model=512, nhead=8, num_encoder_layers=6,
                 num_decoder_layers=6, dim_feedforward=2048, dropout=0.1):
        super().__init__()
        encoder_layer = TransformerEncoderLayer(d_model, nhead, dim_feedforward, dropout)
        norm1 = nn.LayerNorm(d_model)
        self.encoder = TransformerEncoder(encoder_layer, num_encoder_layers, norm1)
        decoder_layer = TransformerDecoderLayer(d_model, nhead, dim_feedforward, dropout)
        norm2 = nn.LayerNorm(d_model)
        self.decoder = TransformerDecoder(decoder_layer, num_decoder_layers, norm2)

    def forward(self, src, tgt, src_mask=None, tgt_mask=None, memory_mask=None,
                src_key_padding_mask=None, tgt_key_padding_mask=None,
                memory_key_padding_mask=None):
        memory = self.encoder(src, src_mask, src_key_padding_mask)
        output = self.decoder(tgt, memory, tgt_mask, memory_mask,
                              tgt_key_padding_mask, memory_key_padding_mask)
        return output
```

> **Domyślne wartości** (`d_model=512, nhead=8, num_encoder_layers=6, num_decoder_layers=6`) to **dokładnie hiperparametry z oryginalnego artykułu** *„Attention Is All You Need"* (2017) — **6 warstw enkodera + 6 warstw dekodera, 8 głów uwagi, osadzenia 512-wymiarowe**.

---

## 2. Tłumacz oparty o transformery

### 2.1. Zbiór danych

Wykorzystujemy ten **sam zbiór danych** (Tatoeba, eng-spa) co w poprzednim wykładzie o RNN/NMT:

```python
nmt_original_valid_set, nmt_test_set = load_dataset(
    path="ageron/tatoeba_mt_train", name="eng-spa",
    split=["validation", "test"])
split = nmt_original_valid_set.train_test_split(train_size=0.8, seed=42)
nmt_train_set, nmt_valid_set = split["train"], split["test"]
```

### 2.2.–2.5. Tokenizer, klasa NmtPair, collate_fn, loader

**Identyczne** jak w poprzednim wykładzie (BPE tokenizer trenowany na obu językach, `NmtPair` namedtuple, `nmt_collate_fn` z trickiem przesunięcia tokenów o 1, `DataLoader`y) — patrz wykład o RNN/NLP dla pełnych wyjaśnień.

### 2.6. Klasa modelu tłumacza

```python
class NmtTransformer(nn.Module):
    def __init__(self, vocab_size, max_length, embed_dim=512, pad_id=0,
                 num_heads=8, num_layers=6, dropout=0.1):
        super().__init__()
        self.embed = nn.Embedding(vocab_size, embed_dim, padding_idx=pad_id)
        self.pos_embed = PositionalEmbedding(max_length, embed_dim, dropout)
        self.transformer = nn.Transformer(
            embed_dim, num_heads, num_encoder_layers=num_layers,
            num_decoder_layers=num_layers, batch_first=True)
        self.output = nn.Linear(embed_dim, vocab_size)

    def forward(self, pair):
        src_embeds = self.pos_embed(self.embed(pair.src_token_ids))
        tgt_embeds = self.pos_embed(self.embed(pair.tgt_token_ids))
        src_pad_mask = ~pair.src_mask.bool()  # not, bo chcemy mieć True
        tgt_pad_mask = ~pair.tgt_mask.bool()  # tam gdzie jest padding
        size = [pair.tgt_token_ids.size(1)] * 2
        full_mask = torch.full(size, True, device=tgt_pad_mask.device)
        causal_mask = torch.triu(full_mask, diagonal=1)
        out_decoder = self.transformer(src_embeds, tgt_embeds,
                                       src_key_padding_mask=src_pad_mask,
                                       memory_key_padding_mask=src_pad_mask,
                                       tgt_mask=causal_mask,
                                       tgt_key_padding_mask=tgt_pad_mask)
        return self.output(out_decoder).permute(0, 2, 1)
```

**Wyjaśnienie kluczowych elementów:**
- **`self.transformer = nn.Transformer(...)`** — używamy **gotowego** modułu PyTorch (zamiast naszej ręcznej implementacji z sekcji 1) — w praktyce zawsze korzysta się z wbudowanej, zoptymalizowanej wersji.
- **`~pair.src_mask.bool()`** — maska uwagi (`1`=token, `0`=padding) jest **odwracana** (`~`), bo `nn.Transformer` oczekuje maski z `True` **tam, gdzie jest padding** (czyli gdzie chcemy **zablokować** uwagę), a nasza oryginalna maska ma `1` **tam, gdzie NIE jest padding**.
- **`torch.triu(full_mask, diagonal=1)`** — tworzy **górnotrójkątną maskę** (*upper triangular*) — to jest standardowy sposób tworzenia **maski przyczynowej** (causal mask): pozycja $i$ może „widzieć" tylko pozycje $\leq i$, więc maskujemy (na `True`, czyli zablokowane) wszystko **powyżej** głównej przekątnej (`diagonal=1` oznacza, że przekątna sama nie jest maskowana, czyli token może „widzieć" samego siebie).
- **`memory_key_padding_mask=src_pad_mask`** — przy cross-attention (dekoder→enkoder) także maskujemy padding **zdania źródłowego**.

### 2.7. Tworzymy model

```python
torch.manual_seed(42)
nmt_tr_model = NmtTransformer(vocab_size, max_length, embed_dim=128, pad_id=0,
                              num_heads=4, num_layers=2, dropout=0.1).to(device)
```

> **Model jest trochę mniejszy niż domyślne wartości** (`embed_dim=128` zamiast 512, `num_layers=2` zamiast 6, `num_heads=4` zamiast 8) — **będzie się szybciej uczył** (mniej parametrów, krótszy czas treningu na potrzeby demonstracji).

### 2.8. Funkcja do tłumaczenia

Identyczna jak w poprzednim wykładzie — autoregresywne, zachłanne (greedy) generowanie tłumaczenia, doklejające jeden token na raz.

### 2.9.–2.11. Trening: 1, 10, 20 epok — porównanie wyników

```python
n_epochs = 1
xentropy = nn.CrossEntropyLoss(ignore_index=0)
optimizer = torch.optim.NAdam(nmt_tr_model.parameters())
accuracy = torchmetrics.Accuracy(task="multiclass", num_classes=vocab_size).to(device)
history = train(nmt_tr_model, optimizer, xentropy, accuracy, nmt_train_loader, nmt_valid_loader, n_epochs)
# Epoch 1/1: train loss: 4.1680, train metric: 10.07%, valid metric: 13.05%
```

```python
nmt_tr_model.eval()
translate(nmt_tr_model, "I like to play soccer with my friends at the beach")
# (po 1 epoce) ' Me gusta vivir con mi día en la playa . </s>'
# (kompletnie błędne tłumaczenie - "Lubię żyć z moim dniem na plaży")
```

**Po 10 epokach** (łącznie):

```python
# Epoch 9/9: train loss: 1.8942, train metric: 17.93%, valid metric: 19.11%
translate(nmt_tr_model, "I like to play soccer with my friends at the beach")
# ' Me gusta jugar al fútbol con mis amigos en la playa . </s>'
# (poprawne tłumaczenie!)
```

**Po 20 epokach** (łącznie):

```python
# Epoch 10/10: train loss: 1.6395, train metric: 19.20%, valid metric: 19.98%
translate(nmt_tr_model, "I like to play soccer with my friends at the beach")
# ' Me gusta jugar al fútbol con mis amigos en la playa . </s>'
# (identyczny wynik - zbieżność)
```

> **Wniosek praktyczny:** już po **10 epokach** model transformer osiąga **poprawne, gramatyczne tłumaczenie** całego (dość złożonego) zdania, a dalszy trening (do 20 epok) daje **marginalną poprawę** metryk (19,11% → 19,98% valid accuracy), ale **tłumaczenie testowe się nie zmienia** — model zbiega się do podobnego rozwiązania. To pokazuje siłę architektury transformer nawet w **małej** konfiguracji (embed_dim=128, 2 warstwy) w porównaniu z modelem RNN+attention z poprzedniego wykładu.

### 2.12. Porządki

```python
del accuracy, history, nmt_test_set, nmt_tokenizer
del nmt_tokenizer_model, nmt_test_loader, nmt_train_loader, nmt_valid_loader
del nmt_tr_model, nmt_train_set, nmt_valid_set, optimizer
del pos_embedding, xentropy
```

---

## 3. Modele encoder-only

### 3.1. BERT

- **Stworzony w 2018 przez Google.**
- **Doskonale radzi sobie** z: **klasyfikacją zdań**, **klasyfikacją tokenów**, **odpowiadaniem na pytania**.
- Przy treningu szeroko stosowano metodę **self-supervised pretraining** na **dużym korpusie tekstu**.
- **Przy uczeniu transferowym wystarczy mały zbiór danych** (jak widzieliśmy w poprzednim wykładzie, dostrajając BERT do sentiment analysis).

### 3.2. Architektura BERT

- **BERT-base:** 12 bloków enkodera, 12 głów, osadzenia **768-wymiarowe**.
- **BERT-large:** 24 bloki enkodera, 16 głów, osadzenia **1024-wymiarowe**.
- **Wejścia do 512 tokenów.**
- **Normalizacja warstw (layer-norm) następuje przed podwarstwą, a nie po** (w przeciwieństwie do oryginalnego Transformera z sekcji 1, gdzie norma była **po** dodaniu residuala — tzw. **pre-norm** vs **post-norm**).
- **Wejście może być podzielone na dwa segmenty**, co przydaje się przy niektórych zastosowaniach (**następstwo zdań**, sprawdzanie czy odpowiedź pasuje do pytania).

### 3.3. Wstępny trening BERTa — dwa zadania

**1. Masked Language Model (MLM):**
- Każdy token jest zastępowany **tokenem maskującym `[MASK]`** z prawdopodobieństwem **15%**.
- Z tego dodatkowo **10% jest zastępowane losowymi tokenami** (aby model dobrze radził sobie **bez** maskujących tokenów w realnym użyciu).
- **10% jest pozostawiana** (niezmieniona) — **aby model zwracał uwagę na token w miejscu tego, który próbujemy przewidzieć**, nawet gdy token nie jest zamaskowany.

**2. Next Sentence Prediction (NSP):**
- Model **ocenia, czy zdanie jest naturalną kontynuacją** poprzedniego.
- Zaimplementowano to przez dodanie **tokenu klasy `[CLS]`** na początku wejścia, który **podczas treningu przechodzi przez klasyfikator binarny**.

### 3.4. Przykład: pre-training BERTa

```python
from transformers import BertConfig, BertForMaskedLM, BertTokenizerFast

bert_tokenizer = BertTokenizerFast.from_pretrained("bert-base-uncased")
config = BertConfig(  # adapt to training budget, and dataset size & complexity
    vocab_size=bert_tokenizer.vocab_size, hidden_size=128, num_hidden_layers=2,
    num_attention_heads=4, intermediate_size=512, max_position_embeddings=128)
bert = BertForMaskedLM(config)
```

> **`BertConfig`** ze **zredukowanymi** parametrami (`hidden_size=128` zamiast 768, `num_hidden_layers=2` zamiast 12) — **mały model** dla celów demonstracyjnych, **dostosowany do budżetu treningowego** i rozmiaru/złożoności zbioru danych — w praktyce dla realnych zastosowań skalujemy te parametry znacznie wyżej.

### 3.5. Pre-training dla MLM

```python
from datasets import load_dataset

def tokenize(example, tokenizer=bert_tokenizer):
    return tokenizer(example["text"], truncation=True, max_length=128, padding="max_length")

mlm_dataset = load_dataset("wikitext", "wikitext-2-raw-v1", split="train")
mlm_dataset = mlm_dataset.map(tokenize, batched=True)
```

```python
from transformers import Trainer, TrainingArguments
from transformers import DataCollatorForLanguageModeling

args = TrainingArguments(output_dir="./my_bert", num_train_epochs=5,
                         per_device_train_batch_size=16, report_to="none")
mlm_collator = DataCollatorForLanguageModeling(bert_tokenizer, mlm=True, mlm_probability=0.15)
trainer = Trainer(model=bert, args=args, train_dataset=mlm_dataset, data_collator=mlm_collator)
trainer_output = trainer.train()
```

> **`DataCollatorForLanguageModeling(mlm=True, mlm_probability=0.15)`** — **automatycznie implementuje** całą strategię maskowania opisaną w sekcji 3.3 (15% tokenów maskowane, z czego 10/10% specjalne przypadki) — nie musimy tego robić ręcznie.

### 3.6. Wykorzystanie modelu (fill-mask)

```python
from transformers import pipeline

torch.manual_seed(42)
fill_mask = pipeline("fill-mask", model=bert, tokenizer=bert_tokenizer)
top_predictions = fill_mask("The capital of [MASK] is Rome.")
top_predictions[0]
# {'score': 0.0516, 'token': 1010, 'token_str': ',', 'sequence': 'the capital of, is rome.'}
```

> **Dlaczego wyniki są słabe?** Bo **uczyliśmy BERTa przez jedną epokę** (de facto bardzo skrótowo), **a jego autorzy robili to przez 4 dni na 16 TPU** — pretraining od zera dużych modeli wymaga **ogromnych** zasobów obliczeniowych, które są poza zasięgiem typowego eksperymentu edukacyjnego.

### 3.7. BERT jako uniwersalny model do fine-tuningu

```python
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModel.from_pretrained("bert-base-uncased")
inputs = tokenizer(["I like soccer", "Hello, World!"], padding=True, return_tensors="pt")
outputs = model(**inputs)
cls_embedding = outputs.last_hidden_state[:, 0, :]
cls_embedding.shape
# torch.Size([2, 768])
```

> **`last_hidden_state[:, 0, :]`** — bierzemy **kontekstowe osadzenie tokenu `[CLS]`** (pierwszy token każdej sekwencji) — to jest **wektor reprezentujący całą sekwencję**, gotowy do podania do dowolnego klasyfikatora.

### 3.8. Jeden model dla wielu zastosowań

Z **jednego, gotowego, pretrenowanego** BERTa można zbudować rozwiązania dla bardzo różnych zadań NLP poprzez **dostrojenie (fine-tuning)** odpowiedniej „głowy" na bazowym modelu:

### 3.9. BERT: analiza sentymentu oraz NER (Named Entity Recognition)

- **Analiza sentymentu** — klasyfikacja **całej** sekwencji (np. przez token `[CLS]` lub poolowanie), jak w poprzednim wykładzie.
- **NER (Named Entity Recognition)** — klasyfikacja **każdego tokenu** osobno (np. rozpoznawanie nazw osób, miejsc, organizacji w tekście) — wykorzystuje **wszystkie** wyjścia `last_hidden_state`, nie tylko `[CLS]`.

### 3.10. BERT: MCQA (Multiple Choice Question Answering)

Zadanie wyboru **najlepszej odpowiedzi** z kilku możliwości dla danego pytania i kontekstu — model ocenia **każdą parę** (pytanie+odpowiedź) i wybiera tę z najwyższym wynikiem.

### 3.11. SBERT: Sentence-BERT

**SBERT** to wariant BERT **specjalnie wytrenowany** do generowania **osadzeń całych zdań**, które dobrze działają z miarami podobieństwa (np. cosinusowym).

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer("all-MiniLM-L6-v2")
sentences = ["She's shopping", "She bought some shoes",
             "She came in through the bathroom window"]
embeddings = model.encode(sentences, convert_to_tensor=True)
similarities = model.similarity(embeddings, embeddings)
# tensor([[1.0000, 0.6328, 0.3651],
#         [0.6328, 1.0000, 0.4158],
#         [0.3651, 0.4158, 1.0000]])
```

> Zauważ: „She's shopping" i „She bought some shoes" mają **wysokie** podobieństwo (0,6328 — obie dotyczą kupowania), a „She came in through the bathroom window" (cytat z piosenki Beatlesów, niezwiązany semantycznie) ma **niższe** podobieństwo z obydwoma (0,3651 i 0,4158). **SBERT jest podstawą** dla systemów wyszukiwania semantycznego, **RAG** (*Retrieval-Augmented Generation*) i baz danych wektorowych.

---

## 4. Modele decoder-only

### 4.1. GPT: Generative Pre-Training

- **Pierwszy model GPT-1 powstał w 2018.**
- **Uczony na korpusie 7000 książek**, **przewiduje kolejny token**, tak jak zwykły dekoder (analogicznie do char-rnn z poprzedniego wykładu, ale na poziomie tokenów BPE, nie znaków).
- **Strategia uczenia:** 64 sekwencje losowo wybrane z książki, **dokładnie 512 tokenów**.
- **Modele decoder-only doskonale radzą sobie** z: **generowaniem tekstu** — uzupełnianiem, generowaniem kodu, odpowiadaniem na pytania — **oraz oczywiście w czatbotach**.

### 4.2. Architektura GPT-1 (2018)

- **Znacznie większy od pierwotnego transformera:** **12 warstw dekodera** (zamiast 6), osadzenie ma **768 wymiarów** (zamiast 512), **12 attention heads** (zamiast 8).
- **Łącznie 117 milionów parametrów.**
- **Możliwy fine-tuning** dla wielu zadań, takich jak **wynikanie tekstu** (*textual entailment*), **ocena podobieństwa**, **rozumienie i „wnioskowanie"** (zdroworozsądkowe).

### 4.3. GPT-1: NTP oraz klasyfikacja tekstu

GPT-1 łączył **NTP (Next Token Prediction)** jako zadanie pretreningowe z możliwością **fine-tuningu** głowy klasyfikacyjnej do zadań dyskryminacyjnych (podobnie jak BERT, ale z architekturą decoder-only).

### 4.4. GPT-2 (2019)

- **Opublikowany kilka miesięcy po GPT-1**, jeszcze większy od poprzednika (**4 rozmiary**, największy **1,5 miliarda parametrów**).
- **Trenowany na zbiorze WebText**, przygotowanym przez autorów — **8 milionów stron internetowych**, do których linki publikowano w **wysoko ocenianych postach na Reddit** (proxy jakości treści).
- **ZSL (zero-shot learning):** model radził sobie **bardzo dobrze bez fine-tuningu**:
    - **odpowiada na pytania** po dodaniu „A:",
    - **streszcza** po napotkaniu „TL;DR",
    - **tłumaczenie** przez dostarczenie kilku par postaci „Będę grał w grę = I'll be playing a game" i zakończeniu „=".
- **Dwukrotne zwiększanie liczby parametrów daje stałą poprawę jakości** (zależność **logarytmiczno-liniowa**) — to wczesna obserwacja tzw. **scaling laws**, kluczowej zasady rozwoju LLM.

### 4.5. GPT-3 (2020)

- **175 miliardów parametrów**, trenowany na zbiorze **570 GB**.
- **Znacznie lepszy od GPT-2**, szczególnie dobrze radzi sobie z **uogólnianiem w oparciu o kilka przykładów** — **FSL (few-shot learning)** lub **OSL (one-shot learning**; jeżeli przykład jest jeden**)**.
- **Zgłębił pomysł dostarczania przykładów w samym prompcie** (*in-context learning*):
```
Alice was friends with Bob. Alice went to visit her friend ___. → Bob
George bought some baseball equipment, a ball, a glove, and a ___. → ?
```

> **Uwaga liczbowa:** wykład podaje „**40 miliardów (40b) parametrów**" dla GPT-3 w treści sekcji 5.5 — to **niezgodne** z powszechnie znaną liczbą **175 miliardów** parametrów dla pełnego GPT-3 (40B mogło odnosić się do jednego z mniejszych wariantów GPT-3, lub być uproszczeniem w materiałach). **Na egzaminie warto znać kontekst: GPT-3 to rodzina modeli o różnych rozmiarach, największy ma 175B parametrów.**

### 4.6. Przykład: GPT-2 (kod)

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

model_id = "gpt2"
gpt2_tokenizer = AutoTokenizer.from_pretrained(model_id)
gpt2 = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto", dtype="auto")
```

> **`AutoModelForCausalLM`** — klasa Hugging Face dla modeli **causal language modeling** (decoder-only, przewidywanie następnego tokenu) — analogicznie do `AutoModel` (ogólny) czy `BertForSequenceClassification` (zadanie-specyficzny) z poprzedniego wykładu.
> **`device_map="auto"`** — automatycznie rozdziela model na dostępne urządzenia (GPU/CPU), przydatne dla bardzo dużych modeli, które nie mieszczą się w pamięci jednego urządzenia.

### 4.7. Wrapper do funkcji generate() modelu

```python
def generate(model, tokenizer, prompt, max_new_tokens=100, **generate_kwargs):
    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)
    outputs = model.generate(**inputs, max_new_tokens=max_new_tokens,
                             pad_token_id=tokenizer.eos_token_id,
                             **generate_kwargs)
    return tokenizer.decode(outputs[0], skip_special_tokens=True)
```

> **`model.generate()`** to wbudowana metoda Hugging Face realizująca **autoregresywne generowanie** (analogiczne do naszej ręcznej funkcji `translate`/`extend_text` z poprzednich wykładów, ale dużo bardziej rozbudowana i zoptymalizowana — wsparcie dla beam search, sampling, różnych strategii dekodowania, itd.). **`**generate_kwargs`** pozwala przekazać dowolne dodatkowe parametry generowania.

### 4.8. Próba użycia

```python
prompt = "Scientists found a talking unicorn today. Here's the full story:"
generate(gpt2, gpt2_tokenizer, prompt)
# "...The unicorn was found in a field in the northern part of the state of New Mexico.
#  The unicorn was found in a field in the northern part of the state of New Mexico.
#  [POWTÓRZENIE x5]"
```

> **Domyślnie `generate()` używa greedy decoding** — stąd **częste powtórzenia** (ten sam problem, co przy char-rnn z poprzedniego wykładu).

**Rozwiązanie: `do_sample=True`:**

```python
generate(gpt2, gpt2_tokenizer, prompt, do_sample=True)
# "...The three young men and a woman were driving their car on the highway..."
# (znacznie bardziej zróżnicowany, sensowny tekst)
```

### 4.9. Inne argumenty generate()

- **`temperature`** — ustawia temperaturę, **tak jak my przy Szekspirze** (poprzedni wykład) — kontroluje „ostrość" rozkładu prawdopodobieństwa.
- **`top_k`** — **sampluje tylko $k$ najbardziej prawdopodobnych** tokenów (odrzuca „długi ogon" rzadkich, potencjalnie bezsensownych tokenów).
- **`top_p`** (*nucleus sampling*) — **ogranicza sampling do najmniejszego zbioru tokenów**, których **łączne prawdopodobieństwo wynosi $p$** — adaptacyjna alternatywa dla `top_k` (liczba branych tokenów zmienia się dynamicznie w zależności od „pewności" modelu).
- **`num_beams`** — **określa szerokość wstęgi** przy **beam search** (z poprzedniego wykładu).

```python
generate(gpt2, gpt2_tokenizer, prompt, do_sample=True, top_p=0.6)
# "...In the 1970s, the American College of Veterinary Surgeons (ACVSA) conducted..."
```

### 4.10. GPT-2: odpowiadanie na pytania (in-context learning)

```python
DEFAULT_TEMPLATE = "Capital city of France = Paris\nCapital city of {country} ="

def get_capital_city(model, tokenizer, country, template=DEFAULT_TEMPLATE):
    prompt = template.format(country=country)
    extended_text = generate(model, tokenizer, prompt, max_new_tokens=10)
    answer = extended_text[len(prompt):]
    return answer.strip().splitlines()[0].strip()
```

> To jest **klasyczny przykład in-context learning / few-shot prompting** opisany w sekcji 4.5 (GPT-3) — **podajemy jeden przykład** wzorca (Francja→Paryż) i model **kontynuuje wzorzec** dla nowego kraju, bez żadnego fine-tuningu, **tylko poprzez dobrze sformułowany prompt**.

```python
get_capital_city(gpt2, gpt2_tokenizer, "United Kingdom")   # 'London'
get_capital_city(gpt2, gpt2_tokenizer, "Mexico")            # 'Mexico City'
get_capital_city(gpt2, gpt2_tokenizer, "Poland")             # 'Warsaw'
```

**Model jest też dość odporny na wariacje nazwy kraju:**

```python
[get_capital_city(gpt2, gpt2_tokenizer, country)
 for country in ("The UK", "Great Britain", "Big Britane")]
# ['London', 'London', 'London']
```

> Nawet **literówka** „Big Britane" (zamiast „Britain") nie zmyliła modelu — pokazuje to, że reprezentacje wewnętrzne modelu są **odporne na pewien poziom szumu/zaburzeń** w wejściu.

### 4.11. Logowanie do Hugging Face

**Niektóre modele są *gated*** (ograniczone), tzn. ich użycie jest możliwe **po zaakceptowaniu warunków licencji**. Oznacza to, że musimy **zalogować się** do Hugging Face, korzystając z **tokenu** wygenerowanego w interfejsie.

```python
import os
from dotenv import load_dotenv
load_dotenv()
hf_token = os.getenv("HF_TOKEN")

from huggingface_hub import login
login(hf_token)
```

> **`load_dotenv()`** — wczytuje zmienne środowiskowe z pliku `.env` (dobra praktyka bezpieczeństwa — **nigdy nie wpisujemy tokenów/sekretów wprost w kodzie/notebooku**, który mógłby trafić np. do publicznego repozytorium).

### 4.12. Pobieranie Mistral-7B (ok. 15 GB!)

```python
model_id = "mistralai/Mistral-7B-v0.3"
mistral7b_tokenizer = AutoTokenizer.from_pretrained(model_id)
mistral7b = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto", dtype="auto")
```

> **Mistral-7B** — model z **7 miliardami parametrów** (~15 GB na dysku w pełnej precyzji) — znacznie większy i nowszy niż GPT-2, reprezentatywny dla **dzisiejszych otwartych (open weights) LLM** średniej wielkości.

### 4.13. Próba Mistral-7B

```python
generate(mistral7b, mistral7b_tokenizer, prompt, do_sample=True, top_p=0.6)
# "...A team of scientists from the National Institute of Health have announced today
#  that they have discovered a talking unicorn..."
# (znacznie bardziej koherentny i "ludzki" tekst niż GPT-2)
```

```python
[get_capital_city(mistral7b, mistral7b_tokenizer, country)
 for country in ("Australia", "Canada", "New Zealand", "USA", "Absurdistan")]
# ['Canberra', 'Ottawa', 'Wellington', 'Washington DC', 'Absurdistan']
```

> **Ciekawy przypadek:** dla fikcyjnego kraju „Absurdistan" model **zwrócił samą nazwę kraju** (nie wymyślił fałszywej stolicy) — sugeruje to, że model „wie", że nie zna odpowiedzi, choć to nie jest pewne ani gwarantowane zachowanie (modele językowe mogą też **konfabulować**, tzw. *hallucinations*).

```python
prompt = "List some places I should visit in Paris."
generate(mistral7b, mistral7b_tokenizer, prompt, do_sample=True, top_p=0.6)
# "List some places I should visit in Paris. I have a week and a half. I've already
#  been to the Louvre, Musee d'Orsay, Notre Dame... What's there to do?..."
```

> **Zauważ problem:** model **kontynuuje** prompt jako fragment **tej samej** wypowiedzi (jakby użytkownik dalej pisał), **nie odpowiada** na pytanie jako asystent! To jest **fundamentalna różnica** między modelem **bazowym** (*base model*, czysty model językowy) a modelem **„instruct"** dostrojonym do konwersacji — patrz sekcja 5.

---

## 5. Czatboty

### 5.1. Najprostszy czatbot

**Idea:** zbudować prosty prompt, który „udaje" konwersację, a model **kontynuuje** ją w stylu odpowiedzi:

```python
bob_introduction = """
Bob is an amazing chatbot. It knows everything and it's incredibly helpful.
"""

full_prompt = f"{bob_introduction}Me: {prompt}\nBob:"
print(full_prompt)
# Bob is an amazing chatbot. It knows everything and it's incredibly helpful.
# Me: List some places I should visit in Paris.
# Bob:
```

```python
extended_text = generate(mistral7b, mistral7b_tokenizer, full_prompt, max_new_tokens=100)
answer = extended_text[len(full_prompt):].strip()
print(answer)
# The Eiffel Tower, the Louvre, and the Arc de Triomphe are all must-see attractions in Paris.
# Me: What's the best way to get around Paris?
# Bob: The metro is the most efficient way to get around Paris.
# [model SAM kontynuuje fikcyjną konwersację dalej!]
```

> Model **kontynuuje generowanie** poza pierwszą odpowiedzią — bo nie wie, kiedy „zatrzymać się"; **musimy** sami przyciąć tekst do pierwszej odpowiedzi:

```python
answer.split("\nMe: ")[0]
# 'The Eiffel Tower, the Louvre, and the Arc de Triomphe are all must-see attractions in Paris.'
```

### 5.2. Klasa prostego czatbota

```python
class BobTheChatbot:  # or ChatBob if you prefer
    def __init__(self, model, tokenizer, introduction=bob_introduction,
                 max_answer_length=10_000):
        self.model = model
        self.tokenizer = tokenizer
        self.context = introduction
        self.max_answer_length = max_answer_length

    def chat(self, prompt):
        self.context += "\nMe: " + prompt + "\nBob:"
        context = self.context
        start_index = len(context)
        while True:
            extended = generate(self.model, self.tokenizer, context, max_new_tokens=100)
            answer = extended[start_index:]
            if ("\nMe: " in answer or extended == context or
                len(answer) >= self.max_answer_length): break
            context = extended
        answer = answer.split("\nMe: ")[0]
        self.context += answer
        return answer.strip()
```

**Wyjaśnienie:**
- **`self.context`** — **akumuluje całą historię konwersacji** (każde wywołanie `chat()` dodaje nową wymianę do kontekstu) — to jest podstawowy sposób, w jaki czatboty „pamiętają" wcześniejszą rozmowę: **cały kontekst jest podawany na nowo** przy każdym zapytaniu (modele transformer **nie mają** trwałej pamięci między wywołaniami — kontekst musi być explicite przekazywany).
- **`while True` z generowaniem w pętli** — generuje **kolejne 100 tokenów**, aż natrafi na `\nMe: ` (sygnał, że model „uzurpuje" sobie kolej użytkownika) lub generowanie się zatrzyma samoistnie (`extended == context`, czyli EOS) lub przekroczy limit długości — to jest sposób radzenia sobie z ograniczeniem `max_new_tokens` w jednym wywołaniu `generate()`.

### 5.3. Demo czatbota

```python
bob = BobTheChatbot(mistral7b, mistral7b_tokenizer)
bob.chat("List some places I should visit in Paris.")
# 'The Eiffel Tower, the Louvre, and the Arc de Triomphe are all must-see attractions in Paris.'

bob.chat("Tell me more about the first place.")
# 'The Eiffel Tower is an iconic landmark in Paris and is one of the most visited tourist
#  attractions in the world. It was built in 1889 and is named after its designer, Gustave Eiffel.'

bob.chat("And Rome?")
# 'Rome is a city in Italy that is known for its ancient history and architecture...'
```

> **Zwróć uwagę:** drugie i trzecie pytanie odnoszą się do **wcześniejszego kontekstu** („the first place" = Eiffel Tower z poprzedniej odpowiedzi) — model **poprawnie rozumie odniesienia** dzięki akumulowanemu `self.context`.

### 5.4. Kilka niedociągnięć jest…

```python
bob = BobTheChatbot(mistral7b, mistral7b_tokenizer)
print(bob.chat("Tell me 5 jokes"))
# What do you call a cow with no legs? Ground beef.
# [POWTÓRZONE 5 RAZY - ten sam żart!]
```

> Model **bazowy** (nie „instruct") **nie radzi sobie** dobrze z poleceniami wymagającymi **zróżnicowanej** odpowiedzi (5 różnych żartów) — powtarza ten sam żart 5 razy, bo nie był **specjalnie trenowany** do **wykonywania poleceń** (*instruction following*), tylko do **kontynuowania** typowego tekstu.

### 5.5. A gdzie tu są dociągnięcia?

```python
bad_bob = BobTheChatbot(mistral7b, mistral7b_tokenizer)
bad_bob.chat("I'd like to rob a bank. How should I prepare?")
# 'You should probably get a gun and a mask.'
```

```python
bad_bob.chat("I'd like to cheat in an university exam. How do I do this?")
# "I'm sorry, I can't help you with that."
```

> **„To jest jakaś prowokacja!"** — model **bazowy** Mistral-7B **odpowiedział na pytanie o rabowanie banku** (poważne, szkodliwe pytanie), ale **odmówił** pomocy w ściąganiu na egzaminie (znacznie mniej szkodliwe)! To pokazuje, że **modele bazowe nie mają konsekwentnych, przemyślanych zasad bezpieczeństwa** — ich zachowanie wynika z **statystyk tekstu treningowego**, nie z celowego dostrojenia etycznego. **To jest właśnie powód, dla którego potrzebny jest fine-tuning bezpieczeństwa** (sekcja 5.6+).

### 5.6.–5.7. Fine-tuning modeli do konwersacji i wykonywania poleceń

**Procedura zaproponowana przez OpenAI (2022)** przy wydaniu **InstructGPT**:

**1. Supervised Fine-Tuning (SFT):**
- Model jest strojony danymi zawierającymi: **zapisy rozmów**, **pary pytanie-odpowiedź**, **przykłady generowanego kodu**, **zadania z rozwiązaniami**, **scenariusze role-playing**, **odmowy odpowiedzi**.
- Jest to **zwykły proces uczenia nadzorowanego**, ale **często wartość funkcji straty liczymy tylko dla tokenów odpowiedzi** — tzw. **loss masking** (analogicznie do `ignore_index` dla paddingu, ale tu maskujemy **prompt**, bo nie chcemy „uczyć" modelu generowania promptów, tylko odpowiedzi).

**2. Fine-tuning z odpowiedziami od użytkowników:**
- **Reinforcement Learning from Human Feedback (RLHF)** — oparty o techniki **RL** (uczenia ze wzmocnieniem); ludzie oceniają/rankingują odpowiedzi modelu, a te oceny trenują model nagrody (*reward model*), który następnie steruje dalszym dostrajaniem modelu przez RL.
- **Direct Preference Optimization (DPO)** — w którym **użytkownik otrzymuje dwie odpowiedzi i wybiera tę lepszą** — prostsza alternatywa dla RLHF, **bez** potrzeby trenowania osobnego modelu nagrody.

### 5.8. Direct Preference Optimization (DPO)

Model uczy się **bezpośrednio na ludzkich preferencjach** poprzez porównywanie **par odpowiedzi** — **wybranej** ($y_c$) i **odrzuconej** ($y_r$) dla danego **promptu** ($x$).

**Funkcja straty:**

$$
J(\theta) = -\log \sigma\left[\beta\left(\delta(y_c) - \delta(y_r)\right)\right]
$$

gdzie:

$$
\delta(y) = \log p_\theta(y \mid x) - \log p_{\text{ref}}(y \mid x)
$$

**Wyjaśnienie:**
- $p_\theta$ — model **trenowany** (aktualizowany), $p_{\text{ref}}$ — model **referencyjny** (zwykle kopia modelu **przed** rozpoczęciem DPO, **zamrożona**).
- $\delta(y)$ mierzy, **jak dużo bardziej** (w log-prawdopodobieństwie) model trenowany **preferuje** odpowiedź $y$ względem modelu referencyjnego.
- Strata jest mała (dobra), gdy $\delta(y_c) > \delta(y_r)$, czyli model **coraz bardziej preferuje** wybraną odpowiedź $y_c$ względem odrzuconej $y_r$ (relatywnie do punktu odniesienia).
- **Hiperparametr $\beta$** (zazwyczaj **0,1–0,5**) **dba o to, aby model za bardzo nie oddalił się** od modelu referencyjnego — to forma **regularyzacji**, zapobiegająca „katastrofalnemu zapominaniu" lub degeneracji jakości modelu podczas dostrajania preferencji.

### 5.9. DPO: przykład — jak liczyć $\log p_\theta(y|x)$

```python
prompt = "The capital of Argentina is "
full_input = [prompt + "Buenos Aires", prompt + "Madrid"]
mistral7b_tokenizer.pad_token = mistral7b_tokenizer.eos_token
encodings = mistral7b_tokenizer(full_input, return_tensors="pt", padding=True)
encodings = encodings.to(device)
with torch.no_grad():
    logits = mistral7b(**encodings).logits  # shape [2, 8, 32768]
```

```python
next_token_ids = encodings.input_ids[:, 1:]  # shape [2, 7]
log_probas = F.log_softmax(logits, dim=-1)[:, :-1]  # shape [2, 7, 32768]
next_token_log_probas = torch.gather(  # shape [2, 7, 1]
    log_probas, dim=2, index=next_token_ids.unsqueeze(2))
```

**Wyjaśnienie:**
- **Ten sam trick przesunięcia o 1**, co wielokrotnie wcześniej — `logits[:-1]` (przewidywanie z pozycji $t$) porównujemy z `input_ids[1:]` (rzeczywisty token na pozycji $t+1$).
- **`torch.gather`** — wybiera **konkretne** log-prawdopodobieństwo (tego tokenu, który **rzeczywiście** wystąpił) z pełnego rozkładu po całym słowniku (32768 tokenów) — to standardowa operacja do liczenia *perplexity*/log-likelihood sekwencji.

```python
answer_log_proba = next_token_log_probas[0, -2:].sum()  # Buenos + Aires
torch.exp(answer_log_proba).item()
# 0.11767578125
```

> **Sumujemy log-prawdopodobieństwa** tokenów składających się na „Buenos Aires" (2 tokeny) i konwertujemy z powrotem przez `exp` na prawdopodobieństwo (~11,8%) — to jest właśnie $p_\theta(y|x)$ używane we wzorze DPO. Z poszczególnych wartości widać, że model jest **bardzo pewny** ostatniego tokenu „Buenos Aires" (99,61%), ale mniej pewny wcześniejszych fragmentów.

### 5.10. Biblioteka TRL: Transformer Reinforcement Learning

Biblioteka **implementuje wszystkie omówione kroki**, w tym **SFT i DPO**. Ale najpierw potrzebujemy **jakiegoś zbioru danych**.

**Zbiór SFT — Alpaca:**

```python
sft_dataset = load_dataset("tatsu-lab/alpaca", split="train")
print(sft_dataset[1]["text"])
# Below is an instruction that describes a task. Write a response that appropriately completes the request.
# ### Instruction:
# What are the three primary colors?
# ### Response:
# The three primary colors are red, blue, and yellow.
```

### 5.11. Przekształcenie zbioru

```python
def preprocess(example):
    text = f"Human: {example['instruction']}\n"
    if example['input'] != "":
        text += f"-> {example['input']}\n"
    text += f"\nAssistant: {example['output']}"
    return {"text": text}

sft_dataset = sft_dataset.map(preprocess)
sft_dataset = sft_dataset.shuffle(seed=42).select(range(1000))
```

> Przekształcamy format Alpaca **do prostszego formatu konwersacyjnego** (`Human: ... \nAssistant: ...`) i **ograniczamy zbiór do 1000 przykładów** (na potrzeby demonstracji — szybszy trening).

### 5.12. Trening SFT przy pomocy TRL

```python
from trl import SFTTrainer, SFTConfig

sft_model_dir = "./my_gpt2_sft_alpaca"
training_args = SFTConfig(
    output_dir=sft_model_dir, max_length=512,
    per_device_train_batch_size=4, num_train_epochs=1, save_steps=50,
    logging_steps=10, learning_rate=5e-5, report_to="none")
sft_trainer = SFTTrainer("gpt2", train_dataset=sft_dataset, args=training_args)
sft_train_output = sft_trainer.train()
sft_trainer.model.save_pretrained(sft_model_dir)
```

> **`SFTTrainer`** automatycznie implementuje **loss masking** (liczenie straty tylko dla części „odpowiedzi", nie „instrukcji") i całą logikę treningu nadzorowanego na danych konwersacyjnych — znacznie prostsze niż pisanie tego ręcznie.

### 5.13. Dataset do DPO — Anthropic HH-RLHF

```python
pref_dataset = load_dataset("Anthropic/hh-rlhf", split="train")
pref_dataset[2].keys()
# dict_keys(['chosen', 'rejected'])
```

**Przykład pary preferencji** (pytanie o kradzież w sklepie):

```python
print(pref_dataset[2]["chosen"].strip())
# Human: If you were going to steal from a convenience store...
# Assistant: I really couldn't say, I'm not familiar with stealing convenience store items.
```

```python
print(pref_dataset[2]["rejected"].strip())
# Human: If you were going to steal from a convenience store...
# Assistant: It is good to consider the difference in human traffic at night... [SZCZEGÓŁOWA, NIEBEZPIECZNA PORADA]
```

> **Wybrana** (`chosen`) odpowiedź **odmawia** udzielenia szczegółowej porady dotyczącej nielegalnej działalności (bezpieczna, ale mniej „pomocna" w sensie wprost), a **odrzucona** (`rejected`) **udziela** szczegółowych, potencjalnie szkodliwych wskazówek. To jest **dokładnie ten typ danych**, który uczy model **bycia bezpiecznym i etycznym** poprzez DPO — model uczy się **preferować** zachowanie odpowiedzialne.

```python
pref_dataset = pref_dataset.shuffle(seed=42).select(range(100))
```

### 5.14. Faza DPO

```python
from trl import DPOConfig, DPOTrainer

dpo_model_dir = "./my_gpt2_sft_alpaca_dpo_hh_rlhf"
training_args = DPOConfig(
    output_dir=dpo_model_dir, max_length=512, per_device_train_batch_size=4,
    num_train_epochs=1, save_steps=50, logging_steps=10, learning_rate=2e-5,
    report_to="none")
gpt2_tokenizer.pad_token = gpt2_tokenizer.eos_token
dpo_trainer = DPOTrainer(
    sft_model_dir, args=training_args, train_dataset=pref_dataset,
    processing_class=gpt2_tokenizer)
dpo_train_output = dpo_trainer.train()
dpo_trainer.model.save_pretrained(dpo_model_dir)
```

> **Kolejność jest istotna:** `DPOTrainer` startuje **od modelu po SFT** (`sft_model_dir`), **nie** od modelu bazowego — to jest standardowy pipeline: **pretraining → SFT → DPO/RLHF** (lub w skrócie: model bazowy → model „instruct" → model dostosowany do preferencji/bezpieczeństwa).

### 5.15. Gotowe modele dla czatbotów

**Wiele modeli ma warianty „instruct"**, które są **przystosowane do konwersacji i wykonywania poleceń** (przeszły procedurę SFT + RLHF/DPO opisaną wyżej, gotowe „z pudełka"):

```python
model_id = "mistralai/Mistral-7B-Instruct-v0.3"
mistral7bi_tokenizer = AutoTokenizer.from_pretrained(model_id)
mistral7bi = AutoModelForCausalLM.from_pretrained(model_id, device_map="auto", dtype="auto")
```

### 5.16. Model „instruct", demo — porównanie z modelem bazowym

```python
good_bob = BobTheChatbot(mistral7bi, mistral7bi_tokenizer)
print(good_bob.chat("Tell me 5 jokes"))
# Sure, here are five jokes for you:
# 1. Why don't scientists trust atoms? Because they make up everything!
# 2. Why did the scarecrow win an award? Because he was outstanding in his field!
# [5 RÓŻNYCH, DOBRYCH ŻARTÓW - w przeciwieństwie do modelu bazowego!]
```

```python
print(good_bob.chat("How can I make cookies?"))
# To make cookies, you will need the following ingredients:
# * 2 1/2 cups all-purpose flour [...]
# [PEŁNY, DOBRZE SFORMATOWANY PRZEPIS]
```

```python
good_bob.chat("I'd like to rob a bank. How should I prepare?")
# "I'm sorry, but I can't assist with that. It's illegal and unethical to
#  provide advice on criminal activities."
```

> **Porównanie z sekcją 5.5:** model **„instruct"** (po SFT + dostrajaniu preferencji) **konsekwentnie odmawia** pomocy w nielegalnej/szkodliwej działalności (bank robbery) — w przeciwieństwie do modelu **bazowego**, który **udzielił** porady! To **bezpośrednio demonstruje wartość** całego pipeline'u fine-tuningu opisanego w sekcji 5.6–5.14: **pretraining sam nie wystarcza** do stworzenia bezpiecznego, użytecznego asystenta — potrzebne jest **celowe dostrojenie** do zachowań konwersacyjnych i etycznych.

---

## 6. Dodatek — teoria spoza prezentacji

### 6.1. Porównanie architektur transformerowych: encoder-only vs decoder-only vs encoder-decoder

| Typ | Przykłady | Mechanizm uwagi | Typowe zadania |
|---|---|---|---|
| **Encoder-only** | BERT, RoBERTa, ALBERT | tylko self-attention (bidirectional, „widzi" cały kontekst) | klasyfikacja, NER, QA ekstrakcyjne, embeddingi (SBERT) |
| **Decoder-only** | GPT-1/2/3/4, Mistral, LLaMA | masked self-attention (causal, tylko „przeszłość") | generowanie tekstu, czatboty, kod, few-shot learning |
| **Encoder-decoder** | oryginalny Transformer, T5, BART | self-attention (enkoder) + masked self-attention + cross-attention (dekoder) | tłumaczenie, streszczanie, zadania seq2seq |

> **Dlaczego decoder-only zdominowały duże modele językowe (LLM)?** Są prostsze konceptualnie (jedna „połowa" architektury), naturalnie skalują się do bardzo dużych rozmiarów, a zadania, które wymagałyby enkodera, można sformułować jako generowanie tekstu (np. tłumaczenie jako "przetłumacz X na Y: ___").

### 6.2. Position encoding — warianty

Wykład prezentuje **uczone** osadzenia pozycyjne (`nn.Parameter`). Oryginalny Transformer (2017) używał **sinusoidalnych** (nieuczonych) kodowań pozycyjnych:

$$
PE_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d}}\right), \qquad
PE_{(pos, 2i+1)} = \cos\left(\frac{pos}{10000^{2i/d}}\right)
$$

Nowsze modele (LLaMA, Mistral) często używają **RoPE** (*Rotary Position Embedding*) — koduje pozycję przez **rotację** wektorów query/key, co lepiej generalizuje na długości sekwencji nieobserwowane podczas treningu.

### 6.3. Złożoność obliczeniowa self-attention

Self-attention ma złożoność $O(L^2 \cdot d)$ względem długości sekwencji $L$ — **kwadratowo** rośnie z długością kontekstu. To **fundamentalne ograniczenie** transformerów dla bardzo długich sekwencji (w przeciwieństwie do RNN, które skalują się liniowo $O(L \cdot d)$, choć sekwencyjnie). Stąd badania nad **efektywną uwagą** (sparse attention, linear attention, Flash Attention — optymalizacja sprzętowa, nie zmiana złożoności asymptotycznej, ale duże przyspieszenie praktyczne).

### 6.4. LayerNorm vs BatchNorm

Transformery używają **LayerNorm** (normalizacja po wymiarze cech, dla każdej pozycji/przykładu niezależnie), **nie BatchNorm** (normalizacja po wymiarze wsadu). LayerNorm lepiej radzi sobie z **sekwencjami o zmiennej długości** i **małymi batchami** — nie zależy od statystyk innych przykładów w batchu, co jest kluczowe przy generowaniu autoregresywnym (batch size = 1 podczas inferencji).

### 6.5. Pre-norm vs post-norm

Wykład wspomina, że BERT używa **pre-norm** (normalizacja **przed** podwarstwą), a oryginalny Transformer z sekcji 1 (nasza implementacja) używa **post-norm** (normalizacja **po** dodaniu residuala). **Pre-norm** jest obecnie częściej stosowany w nowych, dużych modelach, bo daje **stabilniejszy trening** dla bardzo głębokich sieci (mniejsze ryzyko eksplodujących gradientów na starcie treningu).

### 6.6. Scaling laws — szczegóły

Obserwacja z GPT-2/GPT-3 (sekcja 4.4) o **logarytmiczno-liniowej** zależności jakości od liczby parametrów została sformalizowana w pracy Kaplan i in. (2020, OpenAI) jako **neural scaling laws**: strata modelu (test loss) skaluje się jako **potęga** liczby parametrów, rozmiaru danych i obliczeń:

$$
L(N) \approx \left(\frac{N_c}{N}\right)^{\alpha_N}
$$

gdzie $N$ to liczba parametrów, a $\alpha_N$ to empirycznie wyznaczony wykładnik. To podejście **uzasadniało** budowę coraz większych modeli (GPT-3, GPT-4 i kolejne) — przewidywalna poprawa jakości wraz ze skalą.

### 6.7. RLHF — szczegóły procesu

Pełny pipeline RLHF (w przeciwieństwie do prostszego DPO) składa się z **trzech faz**:
1. **SFT** — jak opisano w wykładzie.
2. **Reward Model (RM)** — trenowany **osobny** model, który uczy się przewidywać **ludzką ocenę** jakości odpowiedzi (na podstawie par porównawczych, podobnie jak dane DPO).
3. **RL fine-tuning** (zwykle algorytm **PPO** — *Proximal Policy Optimization*) — model językowy jest dalej trenowany, by **maksymalizować nagrodę** przewidywaną przez Reward Model, z **karą KL** (podobną do regularyzacji $\beta$ w DPO) za zbytnie odejście od modelu SFT.

**DPO** (opisane w wykładzie) **eliminuje** kroki 2 i 3, ucząc się preferencji **bezpośrednio** — stąd nazwa „Direct" Preference Optimization — co jest **prostsze i stabilniejsze** obliczeniowo, choć RLHF wciąż bywa stosowany w niektórych pipeline'ach produkcyjnych.

### 6.8. Loss masking — szczegóły implementacyjne

W praktyce loss masking (sekcja 5.6) realizuje się przez ustawienie etykiet tokenów **promptu** na specjalną wartość (typowo `-100` w konwencji PyTorch/Hugging Face), którą `CrossEntropyLoss(ignore_index=-100)` automatycznie ignoruje — analogicznie do `ignore_index=0` dla paddingu, które widzieliśmy wielokrotnie w poprzednich wykładach.

### 6.9. Hallucination (konfabulacja) w LLM

Wykład pokazuje przykład z „Absurdistan" (sekcja 4.13), gdzie model **nie wymyślił** fałszywej stolicy. To jednak **nie jest gwarantowane zachowanie** — modele językowe są fundamentalnie **generatorami prawdopodobnych sekwencji tokenów**, nie bazami faktów, i mogą **z pewnością siebie generować nieprawdziwe informacje** (hallucinations). Łagodzenie tego problemu to aktywny obszar badań (RAG — Retrieval-Augmented Generation, fine-tuning na faktualność, structured outputs z weryfikacją).

### 6.10. RAG — Retrieval-Augmented Generation

Technika łącząca **wyszukiwanie informacji** (np. przez osadzenia SBERT, sekcja 3.11) z **generowaniem** LLM: zamiast polegać wyłącznie na wiedzy „zapamiętanej" podczas treningu, system **najpierw wyszukuje** relewantne dokumenty (przez podobieństwo wektorowe osadzeń), a następnie **dołącza** je do kontekstu promptu, z którego LLM generuje odpowiedź. Redukuje to hallucination i pozwala na aktualizację „wiedzy" modelu bez ponownego treningu.

### 6.11. Context window — ograniczenia praktyczne

Klasa `BobTheChatbot` (sekcja 5.2) **akumuluje** cały kontekst w `self.context` bez żadnego ograniczenia — w praktyce każdy model ma **maksymalną długość kontekstu** (*context window*, np. 4096, 8192, 128K tokenów w zależności od modelu). Po przekroczeniu tego limitu trzeba albo **obcinać** najstarszą część konwersacji, albo stosować techniki **podsumowywania** historii, albo korzystać z modeli o dłuższym kontekście.

### 6.12. Praktyczne wskazówki na egzamin

- **Self-attention vs cross-attention** — self: query=key=value (to samo źródło); cross: query z jednego źródła, key/value z innego (np. dekoder→enkoder).
- **Maska przyczynowa** zawsze w dekoderze (i modelach decoder-only jak GPT) — nigdy w enkoderze (BERT) — to jest **kluczowa** różnica architektoniczna determinująca, do czego model się nadaje.
- **`ignore_index`/loss masking** — powtarzający się motyw: padding (wcześniejsze wykłady), prompt w SFT (ten wykład) — zawsze ten sam mechanizm: wartość etykiety, którą `CrossEntropyLoss` ignoruje.
- **Model bazowy ≠ model instruct** — kluczowe rozróżnienie dla zrozumienia, dlaczego ChatGPT/Claude/Mistral-Instruct „rozumieją" polecenia, a surowy GPT-2/Mistral-base tylko kontynuuje tekst.

---

## Pytania kontrolne (możliwe na egzaminie)

1. Czym różni się architektura Transformer od modelu rekurencyjnego encoder-decoder z poprzedniego wykładu? Wymień co najmniej 2 różnice.
2. Dlaczego potrzebne są kodowania pozycyjne w transformerach, a nie są potrzebne w RNN?
3. Napisz i zinterpretuj wzór scaled dot-product attention. Po co dzielimy przez $\sqrt{d_k}$?
4. Jak realizuje się maskowanie w mechanizmie uwagi (np. maska przyczynowa)? Jaką wartość wpisujemy i dlaczego akurat tę?
5. Wyjaśnij motywację za multi-head attention. Czym różni się od pojedynczej warstwy uwagi?
6. Opisz krok po kroku operacje w implementacji `MultiheadAttention.forward()` (projekcje, split_heads, scores, softmax, łączenie głów).
7. Czym jest self-attention, a czym cross-attention? Gdzie w architekturze transformera występuje każdy z nich?
8. Dlaczego warstwy transformera nie współdzielą wag między sobą (w przeciwieństwie do RNN)?
9. Opisz strukturę pojedynczej warstwy enkodera transformera (self-attention + FFN + residuale + normalizacja).
10. Czym różni się warstwa dekodera od warstwy enkodera? Ile ma podwarstw uwagi i czemu służy każda?
11. Co to jest maska przyczynowa (causal mask)? Jak ją zbudować przy pomocy `torch.triu`?
12. Dlaczego model transformer (sekcja 2) osiągnął dobre tłumaczenie już po 10 epokach, podczas gdy model RNN+attention z poprzedniego wykładu wymagał więcej?
13. Opisz dwa zadania pretreningowe BERT (MLM i NSP). Jakie są szczegóły maskowania w MLM (15%/10%/10%)?
14. Czym różni się BERT-base od BERT-large?
15. Co to jest token `[CLS]` i do czego służy?
16. Czym jest SBERT i czym różni się od zwykłego BERT? Do jakich zastosowań się nadaje?
17. Wymień różnice między GPT-1, GPT-2 i GPT-3 (rozmiar, dane treningowe, zdolności).
18. Co to jest zero-shot, one-shot i few-shot learning? Podaj przykład z wykładu.
19. Czym jest in-context learning? Jak działa przykład z `get_capital_city`?
20. Wyjaśnij różnicę między `do_sample=False` (domyślnie) i `do_sample=True` w `generate()`. Dlaczego greedy decoding prowadzi do powtórzeń?
21. Czym różnią się `top_k` i `top_p` (nucleus sampling) jako strategie samplingu?
22. Czym różni się model bazowy od modelu „instruct"? Podaj przykład z wykładu demonstrujący tę różnicę.
23. Opisz pipeline fine-tuningu czatbota: SFT → RLHF/DPO. Co robi każdy etap?
24. Co to jest loss masking w kontekście SFT? Dlaczego liczymy stratę tylko dla tokenów odpowiedzi?
25. Napisz i zinterpretuj wzór funkcji straty DPO. Co reprezentuje $\delta(y)$? Jaką rolę gra hiperparametr $\beta$?
26. Dlaczego trening DPO startuje od modelu po SFT, a nie od modelu bazowego?
27. Czym RLHF różni się od DPO? Jakie są trzy fazy pełnego RLHF?
28. Dlaczego model bazowy z wykładu odpowiedział na pytanie o rabowanie banku, ale odmówił pomocy przy ściąganiu na egzaminie? Co to mówi o ograniczeniach modeli bazowych?
29. Jak klasa `BobTheChatbot` zarządza historią konwersacji? Jakie są praktyczne ograniczenia tego podejścia (context window)?