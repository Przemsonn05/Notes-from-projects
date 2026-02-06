
## Polecenia `GitLab:`

#### `git config`

Ustawienie danych użytkownika (globalnie):
```bash
git config --global user.name "Jan Kowalski"
git config --global user.email "jan@agh.edu.pl"
```


Sprawdzenie konfiguracji:
```bash
git config --list            # Wszystkie ustawienia
git config --global --list   # Tylko globalne
```


Zmiana edytora domyślnego:
```bash
git config --global core.editor "code --wait"
```


Alias (skrót do polecenia):
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --get-regexp alias   #sprawdzenie aliasów
```
Polecenia potem mogą być pisane za pomocą aliasu, np. `git st` , czyli to samo co `git status`


Edycja pliku konfiguracyjnego w edytorze:
```bash
git config --global -e     # Otwiera ~/.gitconfig
git config -e              # Otwiera .git/config repo
```


#### `git init`

## Co dokładnie robi?

- Tworzy katalog `.git` (niewidoczny domyślnie)
    
- Ustawia domyślną gałąź (`main` lub `master`, zależnie od konfiguracji)
    
- Przygotowuje wszystko, byś mógł robić `add`, `commit`, `push` itd.

```bash
mkdir nowy-projekt
cd nowy-projekt
git init
```


#### `git add`

Polecenie `git add` **dodaje pliki do tzw. _staging area_** (obszaru indeksowania) w Git. Oznacza to, że przygotowujesz konkretne zmiany do kolejnego commita.

```bash
git add <plik>
git add .         # wszystkie zmienione pliki w bieżącym katalogu i niżej
git add -A        # wszystko (nowe, zmodyfikowane, usunięte pliki)
```


#### `git status`

Polecenie `git status` pokazuje **bieżący stan** Twojego repozytorium w odniesieniu do obszaru roboczego, indeksu (staging area) oraz ostatniego commita:

#### Co zwraca?

1. **Na jakiej gałęzi jesteś**
    
2. **Zmiany śledzone** (tracked): zmodyfikowane pliki, które **nie zostały jeszcze dodane** do indeksu
    
3. **Zmiany gotowe do commita** (staged): pliki dodane przez `git add`
    
4. **Nowe, nieśledzone pliki** (untracked): pliki, których Git jeszcze nie śledzi
```bash
git status
```


#### `git status -s`

Polecenie `git status -s` wyświetla **skróconą wersję** standardowego statusu repozytorium. Zamiast szczegółowych informacji, pokazuje **krótką i zwięzłą tabelę**, w której widoczne są zmienione pliki i ich status. Jest to bardzo pomocne, gdy chcesz szybko sprawdzić stan repozytorium.

```bash
$ git status -s
 M file1.txt
A  file2.txt
?? file3.txt
```
**Pierwsza kolumna** — status pliku względem repozytorium:

- **`M`** — Modyfikacja (plik został zmodyfikowany).
    
- **`A`** — Dodanie (plik został dodany do staging area, ale jeszcze nie zcommiowany).
    
- **`??`** — Nowy plik (plik nie jest jeszcze śledzony przez Git).
    
- **`D`** — Usunięcie (plik został usunięty, ale nie zcommitowany).
    

**Druga kolumna** — nazwa pliku, który został zmieniony.


#### `git log`

Polecenie `git log` pokazuje **historię commitów** w bieżącej gałęzi, od najnowszego do najstarszego:
```bash
git log
```

|Flaga|Opis|
|---|---|
|`--oneline`|Pokazuje każdy commit w jednej linii (skrót SHA + wiadomość)|
|`--graph`|Rysuje ASCII-graf zależności gałęzi|
|`-n <ilość>`|Wyświetla tylko `<ilość>` ostatnich commitów|
|`--author="imię"`|Filtruje commity danego autora|
|`--since=2.days`|Commity z ostatnich 2 dni|
|`--stat`|Pokazuje zmienione pliki i liczbę dodanych/usuniętych linii|

#### `git ls-files`

**Służy do wyświetlenia listy plików, które są śledzone przez Git** w danym repozytorium.
```bash
git ls-files
```


#### `git rm`

**Służy do usuwania pliku z repozytorium Git** (i opcjonalnie także z systemu plików).
```bash
git rm plik1.cpp
git commit -m "Wiadomość"

git rm --cached plik2.txt    #Usuwa plik z repozytorium, ale nie z dysku
```
Wymaga zatwierdzenia `git commit`


#### `git mv`

To polecenie Gita służące do **zmiany nazwy lub przenoszenia plików/folderów** w sposób, który Git rozpoznaje i śledzi.

## Co robi `git mv`?

W rzeczywistości `git mv` to skrót dla trzech operacji:

1. **Zmienia nazwę/przenosi plik w systemie plików** (`mv`)
    
2. **Dodaje nową nazwę do indeksu** (`git add`)
    
3. **Usuwa starą nazwę z indeksu** (`git rm`)

```bash
git mv main.cpp src/main.cpp
git commit -m "Przeniesiono main.cpp do katalogu src"
```

Jeśli plik `nowa_ścieżka` już istnieje, otrzymasz błąd!
```bash
rm nowa_ścieżka
mv stara_ścieżka nowa_ścieżka
git rm stara_ścieżka
git add nowa_ścieżka
```


#### `git commit`

Polecenie `git commit` **zapisuje (utrwala) zmiany** znajdujące się w staging area (czyli po `git add`) do historii repozytorium. To jeden z najważniejszych kroków pracy z Gitem — tworzy "punkt kontrolny", do którego zawsze można wrócić.

```bash
git add main.cpp
git commit -m "Dodano funkcję main do programu"
git commit -am "tresc commita"   #dodaje wszystkie pliki i commituje od razu
git commit --amend   #służy do modyfikacji ostatniego commita
# -m pozwala na dodanie komentarza do commita
```

Commit zapisuje:

- co się zmieniło w plikach (dodane/zmodyfikowane/usunięte linie),
    
- kto wykonał commit (z `git config`),
    
- kiedy commit został zrobiony,
    
- unikalny identyfikator (`SHA-1`),
    
- wiadomość opisu zmian.


#### `alias`

```bash
git config --global alias.ac "commit -am"
```

Tworzy alias, nie trzeba pisać długiej nazwy, teraz można krócej.


#### `.gitignore`

Plik `.gitignore` to plik tekstowy w repozytorium Gita, który zawiera **wzorce nazw plików i folderów**, które Git ma **ignorować**. Oznacza to, że Git nie będzie ich dodawał do commitów, nie będzie ich śledził ani pokazywał w `git status`.
Do pliku .gitignore dodaje tak samo jak do innych plików.

```gitignore
# Pliki kompilacji
*.o
*.obj
*.exe
*.out
*.a
*.lib
*.so
*.dylib
*.dll

# Foldery buildów i katalogi kompilacji
build/
bin/
lib/
Debug/
Release/

# Pliki tymczasowe edytorów i IDE
.vscode/
.idea/

# Pliki systemowe
.DS_Store      # macOS
Thumbs.db      # Windows

# Pliki logów
*.log

# Pliki konfiguracyjne lokalne
*.env
*.config

# Pliki wykonane przez edytor (np. pliki swap)
*~
*.swp
```


```bash
code .gitignore    #aby wejść do pliku .gitignore
git add .gitignore
git commit -m "Added .gitignore file"   #klasycznie: add i commit
```


#### `git diff`

To polecenie w systemie kontroli wersji Git, które **pokazuje różnice** między różnymi wersjami plików w repozytorium. Jest to jedno z najczęściej używanych narzędzi, które umożliwia programistom śledzenie, jakie zmiany zostały wprowadzone do plików, co zostało dodane lub usunięte oraz jakie są różnice w kodzie źródłowym lub innych plikach.
```bash
$ git diff
diff --git a/file1.txt b/file1.txt
index 7cea8bb..4a5ebd4 100644
--- a/file1.txt
+++ b/file1.txt
@@ -0,0 +1,3 @@
+Hello, world!
+This is a test file.
+Bye, world!
```

`git diff --staged` - różnice między staging area a ostatnim commitem
`git diff <plik>` - porównanie jednego pliku
`git diff <commit1> <commit2>` - porównanie dwóch commitów


#### `git difftool`

To polecenie Gita, które pozwala porównywać różnice między plikami **za pomocą zewnętrznego narzędzia do porównywania** (diff tool), np. **Meld**, **Beyond Compare**, **KDiff3**, **VS Code**, itp.

Jest to **alternatywa dla `git diff`**, która zamiast wyświetlania zmian w konsoli, otwiera **graficzne narzędzie porównujące**, co może być wygodniejsze w analizie dużych lub złożonych różnic.

Potrzebna jest konfiguracja narzędzia ze środowiskiem
`git difftool` w użyciu jest podobne do `git diff`


#### `git log --oneline`

To **skrócona wersja logu Git**, która pokazuje historię commitów w bardzo czytelnej i kompaktowej formie.

##### ✅ Co robi `git log --oneline`?

Wyświetla historię commitów jako **jedna linia na commit**, zawierającą:

- Skrócony **hash** commita (np. `a1b2c3d`)
    
- **Komentarz** (message) z commita

```bash
git log --oneline
a1b2c3d (HEAD -> main) Dodano nową funkcję do logowania
7f8e9d1 Poprawka błędu w formularzu
2a4f6c3 Inicjalny commit projektu

git log -S "fraza..."   #wyszukiwanie commitów po treści
```

```bash
git log --graph --oneline --all   #Pokazuje historię commitów w formie **graficznej gałęzi**, w jednej linii na commit. Idealne do analizy skomplikowanej historii merge’ów i gałęzi.
```


#### `git show`

`git show` to polecenie w Git, które wyświetla szczegółowe informacje o **jednym commicie**, w tym:

- pełny hash commit’a, autora, datę,
    
- wiadomość (message) commita,
    
- zmiany wprowadzone w plikach (diff).

`git show` - wyświetli ostatni commit 
`git show <commit_hash>` - wyświetla konkretny commit

```bash
commit 3f4e9b6f8a1c...
Author: Przemson <pkondrat@student.agh.edu.pl>
Date:   Tue May 13 10:00 2025 +0200

    Poprawka wyświetlania danych użytkownika

diff --git a/main.cpp b/main.cpp
index e69de29..4b825dc 100644
--- a/main.cpp
+++ b/main.cpp
@@ -0,0 +1,2 @@
+#include <iostream>
+int main() { std::cout << "Hello"; }
```

Dodatki:
```bash
git show --stat   #podsumowanie zmian (ile linii dodano/usunięto)
git show --name-only   #pokazuje tylko nazwy zmodyfikowanych plików
git show --name-status   #pokazuje status plików (A - dodane, M - zmodyfikowane, D - usunięte)
git show HEAD:<nazwa_pliku>   #wyświetli zawartość pliku <nazwa_pliku>
```



#### `git ls-tree`

`git ls-tree` to zaawansowane polecenie Gita, które służy do **wyświetlania zawartości drzewa** (np. katalogu projektu) w danym commicie lub gałęzi.

```bash
#wynik tego polecenia
100644 blob a3c6e4f4f3d123abcde12345678 file1.txt
100644 blob b7f9a88f6eabc4321def34567 file2.cpp
040000 tree c8f8d7e4bde6789cba1234abc include
```



#### `git restore`

To nowoczesne polecenie Gita (od wersji 2.23), które służy do **przywracania plików** do wcześniejszego stanu. Zostało wprowadzone jako **czytelniejsza alternatywa dla `checkout` i `reset`** przy pracy z plikami.

|Cel|Polecenie|Co robi|
|---|---|---|
|Przywrócenie pliku do stanu z ostatniego commita|`git restore file.cpp`|Nadpisuje zmiany w katalogu roboczym|
|Usunięcie zmian ze staging area (unstage)|`git restore --staged file.cpp`|Usuwa plik ze staging area|
|Jednoczesne unstage + przywrócenie|`git restore --source=HEAD --staged --worktree file.cpp`|Całkowicie resetuje plik|

`git restore main.cpp` - cofnięcie zmian w pliku (z katalogu roboczego)
`git restore --staged main.cpp` - usunięcie pliku ze staging area
`git restore --source=abc123 file.cpp` - przywrócenie pliku z konkretnego commita



#### `git clean`

To polecenie w Git, które **usuwa nieśledzone pliki lub katalogi** z katalogu roboczego. Jest przydatne, gdy chcesz „wyczyścić” repozytorium ze zbędnych plików, które **nie są śledzone przez Git** (czyli nie zostały dodane przez `git add`).

```bash
git clean -n    #sprawdza co zostanie usunięte
git clean -f    #usuwa plik nieśledzone
		  -d    #usuwa nieśledzone katalogi
		  -x    #usuwa pliki ignorowane przez .gitignore
```



#### `git clone`

To polecenie klonuje zdalne repozytorium na lokalny komputer:
```bash
git clone <url>
```



#### `git branch`, `git checkout`, `git switch`

Polecenie `git branch` w systemie kontroli wersji **Git** służy do **zarządzania gałęziami (ang. branches)** w repozytorium. Gałęzie umożliwiają równoległą pracę nad różnymi funkcjami lub poprawkami, bez wpływu na główną wersję kodu.

```bash
git branch branch_name   #tworzenie nowej gałęzi
git branch -d branch_name   #usuwanie nowej gałęzi
git checkout branch_name   #przełączanie się na inną gałąź
git checkout -b branch_name   #tworzenie i przełączanie się na inną gałąź
git checkout -b <new_branch> <commit>   #Tworzy nową gałąź zaczynającą się od danego commita.
git switch   #robi to samo co checkout tylko w nowszych wersjach
```



#### `git merge`

Polecenie `git merge` służy do **scalania dwóch gałęzi** w Git. Najczęściej używa się go, aby połączyć zmiany z jednej gałęzi (np. funkcjonalnej) do drugiej (np. `main`).

```bash
git branch
git checkout file1.txt   #przełącz się na gałąź, do której chcesz scalić zmiany
git merge file2.txt    #scal inną gałąź do file1.txt
```



#### `git fetch`

**`git fetch`** pobiera wszystkie zmiany z **zdalnego repozytorium** do lokalnego repozytorium, ale **nie** scala ich z Twoimi lokalnymi gałęziami. Jest to bardziej bezpieczne, ponieważ pozwala na zapoznanie się z nowymi zmianami przed połączeniem ich z Twoją wersją.
#### Jak działa:

- Pobiera wszystkie zmiany (commity, gałęzie, tagi) z zdalnego repozytorium, ale nie zmienia Twojej lokalnej pracy.
    
- Pozwala sprawdzić, co się zmieniło na serwerze przed wykonaniem np. `git merge` lub `git pull`.



#### `git pull`

**`git pull`** to połączenie **`git fetch`** oraz **`git merge`**. Pobiera zmiany z repozytorium zdalnego, a następnie automatycznie scala je z Twoją lokalną gałęzią.

#### Jak działa:

- Pobiera zmiany z zdalnego repozytorium (jak `git fetch`).
    
- Następnie, **automatycznie scala** je z Twoją aktualną lokalną gałęzią (jak `git merge`).



#### `git push`

**`git push`** wysyła Twoje **lokalne zmiany** do repozytorium zdalnego. Używasz go, gdy chcesz podzielić się swoimi zmianami z innymi, np. przesłać commit do zdalnej gałęzi na GitHubie.

```bash
git push --force   #wypycha Twoje zmiany do zdalnego repozytorium wymuszając nadpisanie istniejącej historii
```

#### Jak działa:

- Wysyła lokalne zmiany (commity) do zdalnego repozytorium.
    
- Może być konieczne **autoryzowanie się** (login + hasło lub klucz SSH).
    
- Przesyła zmiany tylko na tę gałąź, którą określisz (np. `main`, `develop`).

```bash
git push -u origin <branch>   #ustawia zdlane powiązanie dla lokalnej gałęzi, za każdym kolejnym razem uzycia git push/git pull nie trzeba podawać origin <branch>
```



#### `git reset`

Polecenie **`git reset`** w Git służy do **cofania zmian** – głównie commitów lub plików ze staging area (obszaru przygotowanego do commita).

```bash
git reset --soft HEAD~1   #cofa ostatni commit, ale **nie traci zmian** – można je poprawić i zrobić nowy commit

git reset --mixed HEAD~1   #cofa ostatni commit, pliki są "odstaged", ale dalej zmodyfikowane – można je dodać ponownie lub zmienić

git reset --hard HEAD~1   #wszystko zostaje wyczyszczone – **nie da się cofnąć** bez specjalnych zabiegów
```



#### `git stash`

Służy do **tymczasowego zapisania zmian roboczych**, których nie chcesz jeszcze commitować, ale też nie chcesz ich stracić. To jak wrzucenie niedokończonej pracy „do szuflady” – możesz ją potem przywrócić w dowolnym momencie.

```bash
git stash   #Zapisuje aktualne niezacommitowane zmiany

git stash show -p   #pokazanie zmian w stashu

git stash --keep-index   #schowanie plików tylko z katalogu roboczego

git stash save "Some desciption"   #zachowanie zmian wraz z opisem

git stash list   #Wynik: stash@{0}: WIP on main: 123abc Dodano formularz
                 #       stash@{1}: WIP on dev: 456def Zmiana stylów
git stash apply   #przywraca stash, ale nie usuwa go ze stosu

git stash pop   #przywraca stash i usuwa go ze stosu

git stash drop   #usunięcie ostatniego stash, clear to usunięcie wszystkich
```



#### `git cherry-pick`

**`git cherry-pick`** pozwala na **wybranie jednego konkretnego commita** (lub kilku) z innej gałęzi i **włączenie go do bieżącej gałęzi**, **bez scalania całej historii**.

To jak "wybranie wisienki z tortu" – bierzesz tylko ten commit, który Cię interesuje.

```bash
git checkout main
git cherry-pick <specific commit>

git cherry-pick --abort   #przerywa cherry-pick
```



#### `git tag`

**`git tag`** służy do **tworzenia znaczników (tagów)** w Git, które oznaczają **konkretne commity** – najczęściej używa się ich do oznaczania **wydań wersji** (np. `v1.0.0`, `v2.1`).

Tagi są **czytelnymi punktami odniesienia** – nie zmieniają się i nie przesuwają, w przeciwieństwie do gałęzi.

```bash
git tag <name>   #tworzenie tagu

git tag <name> <commit_hash>   #tworzenie tagu na konkretnym commicie

git tag   #liczba wszystkich tagów

git tag -d <name>   #usuwanie tagu lokalnie

git push origin <tag_name>   #aby wypchnąć tag do zdalnego repozytorium
```



#### `git branch` - dalsze komendy

`git branch` to polecenie służące do **zarządzania gałęziami** (ang. _branches_) w Git. Gałąź to **wskaźnik do konkretnego commita** – pozwala pracować nad zmianami niezależnie od głównej linii rozwoju (np. `main`).

```bash
git branch   #wyświtlanie listy gałęzi
git branch <branch_name>   #tworzenie nowej gałęzi
git checkout <branch_name>   #przełączenie ię na gałąź
git branch   #usunięcie gałęzi lokalnie
git branch -a   #zobaczenie wszystkich gałęzi (lokalne + zdalne)
git branch -M main   #zamieni nazwę bieżącej gałęzi na main, wymuszając zmianę, jęsli istnieje taka gałąź
```



#### `git rebase`

`git rebase` to jedno z najpotężniejszych (i najczęściej niezrozumianych) narzędzi Gita. Służy do **przenoszenia commitów** z jednej gałęzi na inną, tworząc **czystszą i liniową historię zmian** – jakby "na płasko".

```bash
git rebase <gałąź-docelowa>

#Przykład:
git checkout feature
git rebase main
git rebase -i HEAD~3 #gdy chce uporządkować ostatnie 3 commity
#Przenosi commity z `feature` na czubek aktualnego `main`
```

## Możliwe operacje w interactive rebase

- **pick** – zostaw commit bez zmian
    
- **reword** – zmień tylko wiadomość commita
    
- **edit** – zmień commit (dodaj/usuwaj pliki)
    
- **squash (s)** – połącz commit z poprzednim (np. drobne poprawki)
    
- **fixup (f)** – jak squash, ale ignoruje wiadomość commita
    
- **drop** – usuń commit



#### `git blame`

`git blame` to polecenie Git, które pokazuje, który commit i który autor ostatnio zmodyfikował każdą linię w pliku. Jest przydatne do śledzenia zmian w kodzie.

```bash
git blame -e <file_name>   #pokaż email zamiast nazwy autora
		  -t <file_name>   #pokaż datę commita zamiast autora
		  -l <file_name>   #pokaż oryginalną nazwę pliku
```



#### `git reflog`

Ta komenda pokazuje **historię wszystkich ruchów HEAD** – commitów, resetów, mergów itd. 
Przykład:
```bash
e7d9c1e HEAD@{0}: reset: moving to HEAD~1
b2f84f2 HEAD@{1}: commit: Drugi commit
a1c23e4 HEAD@{2}: commit: Pierwszy commit
```


### Jak odzyskać usunięty commit?

```bash
git reset --hard HEAD~2   #straciłem 2 ostatnie commity

git reflog
#znajdź hash utraconego commita, np. a1b2c3

git ckeckout indeks_commita
```


### Można wrócić do poprzedniego stanu branchu

```bash
git reflog
git checkout HEAD@{2}  # wróć do stanu sprzed 2 ruchów
```


#### `git gc`

Oczyszcza repozytorium ze zbędnych danych (nieużywanych commitów, obiektów, starych plików itd.), aby zmniejszyć jego rozmiar i przyspieszyć działanie.

```bash
git gc
```



#### `git submodule add <repo> <path>`

Dodaje inne repozytorium Git jako **submoduł** do obecnego repozytorium.

```bash
git submodule add https://github.com/uzytkownik/lib.git external/lib
```



#### `git bisect`

Pomaga znaleźć commit, który **wprowadził błąd**. Git automatycznie testuje kolejne commity i zawęża problem binarnie.

```bash
git bisect start
git bisect bad           # obecna wersja ma błąd
git bisect good abc123   # stara wersja działała
```
Potem Git przeskakuje przez commity, Ty testujesz i wpisujesz `git bisect good` lub `git bisect bad`. Na końcu Git pokaże dokładny commit, który wprowadził błąd.



#### `git grep <pattern>`

Szuka tekstu we wszystkich plikach śledzonych przez Git (działa szybciej niż `grep -r`).

```bash
git grep "TODO"         # znajdź wszystkie TODO
git grep -n "main()"    # pokaż z numerami linii
```

**Zastosowania:**

- Szybkie przeszukiwanie dużego projektu
    
- Znajdowanie kodu, komentarzy, błędów


#### `git submodules` cd.

```bash
#Tworzenie submodule
git submodule add <repo-url> <ścieżka/w/projekcie>
```


Przykład:
```bash
git submodule add https://github.com/username/helper.git libs/helper
```


Co się dzieje:

1. Git tworzy folder `libs/helper`.
    
2. Klonuje tam repo `helper`.
    
3. W repo głównym Git zapisuje **hash aktualnego commita submodule** w pliku `.gitmodules` i w indeksie.
    
4. `.gitmodules` wygląda np. tak:
5. ```bash
   [submodule "libs/helper"]
    path = libs/helper
    url = https://github.com/username/helper.git
   ```


```bash
git clone <repo-url> myproject
cd myproject
git submodule update --init --recursive
```

- `update --init` – pobiera pliki submodule zgodnie z zapisanym commitem.
    
- `--recursive` – jeśli submodule też ma submodule (nested), pobiera je wszystkie.
    
- **Ważne:** bez tego folder submodule będzie pusty lub nie zaktualizowany.


Cofnięcie zmian w submodule:
```bash
git checkout <hash>     # przywraca submodule do konkretnego commita
git add <submodule-folder>
git commit -m "reset submodule"
```

**Trzeba pamiętać o commitowaniu submodule osobno!!!**



### `GitHub Flow`

## 1️⃣ Zawsze startuj od aktualnego main

`git checkout main git pull`

---

## 2️⃣ Utwórz branch do zadania / eksperymentu

`git checkout -b feature/exp-xgboost`

---

## 3️⃣ Pracujesz, edytujesz pliki…

---

## 4️⃣ Sprawdź co się zmieniło

`git status`

---

## 5️⃣ Dodaj zmiany do commita

`git add .`

---

## 6️⃣ Zrób commit

`git commit -m "add xgboost baseline"`

---

## 7️⃣ Wyślij branch na GitHub

`git push origin feature/exp-xgboost`

---

## 8️⃣ (Na GitHubie) — otwierasz Pull Request

`feature/exp-xgboost  →  main`

---

## 9️⃣ Jeśli po review musisz coś poprawić

`# robisz poprawki 
`git add . 
`git commit -m "fix leakage" 
`git push`

(PR sam się aktualizuje)

---

## 🔟 Po merge PR — aktualizujesz lokalny main

`git checkout main` 
`git pull`

---

## 1️⃣1️⃣ Usuwasz branch (sprzątanie)

`git branch -d feature/exp-xgboost`
`git push origin --delete feature/exp-xgboost`