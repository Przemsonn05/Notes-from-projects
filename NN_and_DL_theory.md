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