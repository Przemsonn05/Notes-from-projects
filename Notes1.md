# Notatki z Pandas i ML

### pd.cut()

pd.cut() to funkcja Pandas służąca do dzielenia wartości ciągłych na przedziały (binning), czyli zamiany liczb na kategorie / klasy.

Czyli bins to są przedziały i liczy ile i jakie liczby są w danym przedziale

```python
bins = [0, 20, 50, 80, 150]
labels = ['Good', 'Moderate', 'Poor', 'Very Poor']

all_data['PM10_level'] = pd.cut(
    all_data['PM10_ŚLĄSKIE'],
    bins=bins,
    labels=labels
)
```

---

### unstack()

```objectivec
time        region        PM10
2023-01-01  ŚLĄSKIE       30
            MAŁOPOLSKIE   45
2023-01-02  ŚLĄSKIE       28
            MAŁOPOLSKIE   40
```

Po df.unstack():

```yaml
             PM10
region   ŚLĄSKIE  MAŁOPOLSKIE
time
2023-01-01   30        45
2023-01-02   28        40
```

---

### Dopisanie wartosći nad słupkiem:

```python
for container in ax[1].containers:
    ax[1].bar_label(container, fmt='%.2f', padding=3)
```

- ax[1].containers → grupy słupków (np. każdy kolor osobno)

- bar_label() → automatycznie:

    - pobiera wysokość słupka

    - ustawia tekst nad nim

- fmt='%.2f' → 2 miejsca po przecinku

- padding=3 → odstęp od słupka

---

### notna()

To funkcja w pandas, która sprawdza, czy dana wartość NIE jest brakująca.

```python
df[df['PM10'].notna()]
```

zostawia tylko wiersze, gdzie PM10 istnieje.

---

