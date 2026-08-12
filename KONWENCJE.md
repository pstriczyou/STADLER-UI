# Konwencje UI

Reguły, których [`makieta/index.html`](makieta/index.html) jest ilustracją. Makieta
pokazuje **jak to wygląda**; ten plik mówi **dlaczego tak** i **co zrobić w przypadku,
którego makieta nie pokazuje**.

Dokument dotyczy wyłącznie warstwy widoku: kolorów, typografii, układu, kontrolek,
stanów i dostępności. Nie ma tu decyzji o technologii ani o tym, jak dane trafiają
na ekran.

Zasada nadrzędna: **odbiorcą nie jest programista.** Ekran wypełnia operator na hali
albo inżynier między dwoma spotkaniami. Rozwiązanie eleganckie, którego trzeba się
nauczyć, przegrywa z topornym, które widać od razu.

---

## 1. Tokeny — jedyne miejsce z wartościami

Wszystkie kolory, promienie i wymiary stoją w `:root` jako zmienne CSS. **Nowy ekran
nie wpisuje własnego `#hex` ani własnego `padding: 13px`** — sięga po token albo dokłada
nowy do `:root`, jeśli naprawdę brakuje.

| Grupa | Tokeny |
| --- | --- |
| marka | `--niebieski` `--niebieski-jasny` `--niebieski-tlo` `--niebieski-ciemny` |
| powierzchnie | `--tlo` `--karta` `--biel` `--obwodka` `--obwodka-mocna` |
| tekst | `--tekst` `--tekst-jasny` `--tekst-placeholder` |
| znaczenia | `--blad` `--uwaga` `--sukces` (+ warianty `-tlo`) |
| kształt | `--promien` `--promien-karty` `--cien-karty` `--cien-ogniska` `--wys-pola` |

`--niebieski: #00599C` jest **zmierzony z pliku logo** (piksel rdzenia litery), nie
znaleziony w sieci — dzięki temu nagłówek zgadza się ze znakiem słownym co do piksela.
Jeśli kiedyś zmieni się znak firmowy, zmienia się jedna linijka.

Kolorów znaczeniowych są **cztery i nie ma piątego**: niebieski (informacja),
pomarańczowy (ostrzeżenie), czerwony (błąd), zielony (powodzenie). Fioletowy „specjalny
stan" dołożony przy jednym ekranie sprawia, że użytkownik przestaje ufać, że kolor
w ogóle coś znaczy.

---

## 2. Typografia i rytm

**Krój systemowy** (`Segoe UI` → `Helvetica Neue` → `system-ui`). Fontów firmowych
nie wczytujemy — tekst ma się pojawić od razu, a nie po doczytaniu pliku.

| Element | Rozmiar | Grubość |
| --- | --- | --- |
| tytuł strony | 24 px | 700 |
| nagłówek sekcji | 16 px | 700 |
| tekst i **treść pól** | 16 px | 400 |
| etykieta pola | 13,5 px | 600 |
| pomoc, błąd, meta | 12,5 px | 400 / 600 |
| znacznik | 11 px | 700 |

**Pole nigdy poniżej 16 px.** Safari na iOS przybliża stronę przy ognisku w polu
o mniejszej czcionce i użytkownik zostaje z rozjechanym układem, którego nie zamówił.

Interlinia 1,5 dla tekstu ciągłego. Odstępy: **6 px** wewnątrz pola (etykieta → pole →
pomoc), **16–20 px** między polami, **18 px** między kartami, **22–24 px** wyściółki karty.
Inne wartości znaczą, że coś jest wyjątkiem — a wyjątek trzeba umieć uzasadnić.

---

## 3. Nazewnictwo i organizacja CSS

**Klasy po polsku**, tak jak reszta kodu: `.karta`, `.pole`, `.pomoc`, `.blad-pola`,
`.pasek-akcji`, `.znacznik`. Nie `.card`, nie `.form-group`. Mieszanka języków w klasach
kosztuje przy każdym `grep`-ie.

**Klasa opisuje rolę, nie wygląd.** `.znacznik.uwaga`, nie `.znacznik-zolty` — kolor
ostrzeżenia można zmienić w jednym miejscu, nazwę klasy rozsianą po dwudziestu plikach
już nie.

**Styl jednego ekranu zostaje przy tym ekranie.** Do arkusza wspólnego trafia wyłącznie
to, czego używa **więcej niż jeden** ekran — i wtedy naprawdę trafia, a nie zostaje
skopiowane po raz drugi.

---

## 4. Formularz — układ

**Kolejność pionowa jest stała:**

```text
etykieta
[ pole ]
pomoc (zawsze, jeśli format nie jest oczywisty)
błąd (tylko gdy jest)
```

Etykieta **nad** polem, nie obok — układ z etykietami po lewej rozjeżdża się przy polskich
napisach i nie schodzi sensownie do jednej kolumny na tablecie.

Pomoc i błąd **pod** polem, nie nad. Czytający wraca wzrokiem do pola, w którym stoi,
a nie skacze w górę.

**Pomoc opisuje format, zanim ktoś się pomyli.** „Dwie litery, podkreślnik, siedem cyfr"
stoi pod polem od początku, a nie dopiero w komunikacie po odrzuceniu formularza.

**Sekcje mają nagłówek i jedno zdanie opisu.** Formularz dłuższy niż osiem pól bez
podziału na sekcje jest ścianą.

**Pola wymagane oznaczamy gwiazdką z `aria-hidden="true"`.** Czytnik ekranu bierze
wymagalność z komunikatu błędu i z `aria-invalid`, nie ze znaku graficznego — inaczej
czyta „gwiazdka" przy co drugim polu. Pola opcjonalne w formularzu, gdzie większość
jest wymagana, oznaczamy słowem „— opcjonalne".

**Siatka dwukolumnowa, z wyjątkami na całą szerokość.** Pole długiego tekstu, grupa
checkboxów i wybór pliku zajmują obie kolumny — ściśnięte do połowy stają się nieczytelne.

---

## 5. Wybór właściwej kontrolki

| Sytuacja | Kontrolka |
| --- | --- |
| 2–5 opcji, wybór jeden | grupa radio, cały prostokąt klikalny |
| 6 i więcej, wybór jeden | `<select>` z pustą pierwszą pozycją `— wybierz —` |
| wybór wielokrotny | siatka checkboxów |
| włącznik **zachowania** | przełącznik (`role="switch"`) |
| wartość liczbowa z miarą | pole liczbowe z jednostką **w polu**, nie w etykiecie |
| wartość powstająca z innych | pole `readonly`, szare, poza kolejnością `Tab` |

`<select multiple>` **nie jest używany nigdy** — wymaga trzymania `Ctrl` i w rękawicach
jest nie do obsłużenia.

Przełącznik tylko wtedy, gdy chodzi o włączenie **zachowania** („powiadamiaj zespół"),
nie o zaznaczenie **faktu** („dotyczy dachu"). Pomylenie tych dwóch znaczeń to najczęstszy
błąd w formularzach wewnętrznych.

**Domyślna wartość, której nikt nie wybrał, jest niebezpieczna.** `<select>` startuje
od pustej pozycji, bo inaczej pierwsza opcja wchodzi do bazy jako świadoma decyzja
i nikt się nie zorientuje. Wyjątek: pole, gdzie istnieje prawdziwa wartość domyślna
(priorytet „zwykły") — wtedy jest domyślna, bo to jest odpowiedź, nie brak odpowiedzi.

**Limit długości zawsze z licznikiem.** `maxlength` ucina po cichu; użytkownik pisze
dalej i nie widzi, że tekst nie wchodzi.

**Pole zablokowane zawsze mówi dlaczego.** `disabled` bez wyjaśnienia to ślepy zaułek.

**Jednostka mieszka w polu.** Po wypełnieniu nie widać etykiety, więc `12,5` bez `kg`
obok wartości jest liczbą bez znaczenia.

---

## 6. Błędy, komunikaty, stan zapisu

**Po nieudanym zapisie:**

1. na górze formularza staje **podsumowanie błędów** — ogniskowalne (`tabindex="-1"`),
   z `role="alert"`, wymieniające błędy jako **linki do pól**;
2. każde błędne pole dostaje `aria-invalid="true"` i komunikat pod sobą;
3. **żadne wpisane dane nie znikają.**

Podsumowanie na górze nie jest ozdobą. Bez niego ktoś poruszający się klawiaturą albo
czytnikiem ekranu nie ma jak się dowiedzieć, że formularz w ogóle ma błędy — dostaje
tylko brak reakcji na `Zapisz`.

**Komunikat błędu mówi, co zrobić**, nie tylko że się nie udało. „Numer ma postać:
dwie litery, podkreślnik, siedem cyfr — np. `SI_3210103`" zamiast „Nieprawidłowy format".

**Cztery znaczenia komunikatu:** informacja (nic nie trzeba robić) · ostrzeżenie
(można iść dalej, ale wynik będzie niepełny) · błąd (nie udało się, nic nie zapisano) ·
powodzenie. Każdy ma pogrubiony nagłówek i zdanie treści.

**Stan „zapisywanie…" musi być widoczny**, a przycisk na ten czas nieaktywny. To ten
stan najczęściej ginie, a jego brak produkuje podwójne zapisy — ktoś klika drugi raz,
bo nic się nie stało.

**Niezapisane zmiany są sygnalizowane w pasku akcji.** Formularz, z którego można wyjść
bez ostrzeżenia, prędzej czy później skasuje komuś kwadrans pracy.

**Na ekranie jest dokładnie jeden przycisk główny.** Reszta jest drugorzędna albo cicha.
Dwa przyciski w tym samym kolorze znaczą, że nikt nie rozstrzygnął, co jest właściwą akcją.

---

## 7. Dostępność — nie jest opcją

Nie z powodu zgodności z normą. Z powodu tego, że ekran bywa obsługiwany klawiaturą,
przy złym oświetleniu i przez kogoś, kto nie widzi dobrze z bliska.

- **Każde pole ma `<label for="…">`.** Placeholder **nie jest** etykietą — znika
  po wpisaniu pierwszego znaku i nikt nie pamięta, co miał wpisać.
- **`aria-describedby`** wiąże pole z pomocą i błędem. Przy błędzie identyfikator błędu
  jest **dopisywany**, nie podmieniany — pomoc ma zostać.
- **Pierścień ogniska (`:focus-visible`) nigdy nie jest usuwany.** `outline: none`
  bez zamiennika to defekt, nie decyzja estetyczna.
- **Element schowany wizualnie zostaje w kolejności `Tab`.** `display: none` na polu
  wyboru pliku wyrzuca je z klawiatury; zamiast tego zerowy rozmiar i pierścień
  rysowany przez etykietę.
- **Grupy pól** (radio, checkboxy) mają `role="group"` i `aria-labelledby` wskazujące
  na etykietę grupy. Błąd grupy wisi pod całą grupą, bo nie należy do żadnego
  pojedynczego pola.
- **Kolejność `Tab` jest kolejnością czytania.** Jeśli się rozjeżdża, winna jest
  struktura HTML, nie brak `tabindex` — i to ją się naprawia.
- **Kolor nie niesie znaczenia sam.** Przy każdym kolorze stoi tekst albo ikona.
- **`prefers-reduced-motion`** wyłącza animacje. Jedna reguła na cały arkusz.

---

## 8. Hala produkcyjna — ograniczenia fizyczne

| Ograniczenie | Konsekwencja w UI |
| --- | --- |
| rękawice robocze | `--wys-pola: 44px` jako minimum dla pól i przycisków; cały prostokąt opcji klikalny, nie sam kwadracik |
| czytnik kodów kreskowych | zachowuje się jak klawiatura i kończy wpis `Enter`-em → pole skanera **przechwytuje `Enter`** i przenosi ognisko dalej, zamiast wysyłać formularz |
| monitor pod światło | kontrast tekstu pomocniczego podniesiony ponad typowe szarości |
| tablet w pionie | jedna kolumna poniżej 640 px, przyciski na pełną szerokość |

---

## 9. Zachowanie na wąskim ekranie

Dwa progi, nie pięć:

- **900 px** — panel boczny przestaje być kolumną i ląduje **nad** treścią, bo niesie
  wskazówki potrzebne przed wypełnianiem, nie po;
- **640 px** — siatka schodzi do jednej kolumny, nawigacja znika z nagłówka,
  przyciski w pasku akcji rozciągają się na pełną szerokość.

**Nic nie przewija się w poziomie.** Jeśli coś wystaje przy 360 px, to jest defekt,
a nie cecha treści.

---

## 10. Czego nie robimy

- **Modali do wprowadzania danych.** Formularz w oknie modalnym gubi się przy odświeżeniu
  i nie ma własnego adresu. Modal wolno użyć do potwierdzenia („na pewno usunąć?"),
  nie do zbierania danych.
- **Pomocy wyłącznie w dymku.** Tooltip na najechanie nie istnieje na ekranie dotykowym
  i nie istnieje dla klawiatury. Wszystko, co trzeba wiedzieć, żeby wypełnić pole, stoi
  pod polem na stałe.
- **Blokowania przycisku zapisu do czasu poprawnego wypełnienia.** Użytkownik zostaje
  z nieaktywnym przyciskiem i bez informacji, czego brakuje. Przycisk jest aktywny,
  a odrzucenie wyjaśnia powód.
- **Zapisu w tle bez sygnału.** Jeśli coś zapisuje się samo, ma to być widoczne.
- **Ikon jako fontu.** SVG w miejscu albo `data:` URI — font ikon to pusty prostokąt,
  gdy się nie wczyta.
- **Gotowych bibliotek stylów.** Tokeny plus kilkaset linii własnego CSS są mniejsze
  i nie narzucają cudzej estetyki, która potem walczy z firmową.
- **Ciemnego motywu** — na razie. Interfejs jest jasny i konsekwentny; motyw dochodzi
  jako komplet tokenów albo wcale, nigdy jako pojedynczy ekran.

---

## 11. Zanim uznasz ekran za gotowy

- [ ] Da się go wypełnić **samą klawiaturą**, od pierwszego pola do przycisku zapisu.
- [ ] `Zapisz` na pustym formularzu daje **podsumowanie błędów** i nie gubi danych.
- [ ] Każde pole ma `<label for>`, a pomoc i błąd są wpięte przez `aria-describedby`.
- [ ] Przy 360 px szerokości nic nie wystaje poza ekran.
- [ ] Każde pole i każdy przycisk ma co najmniej 44 px wysokości.
- [ ] W kodzie nie ma ani jednego `#hex` poza `:root`.
- [ ] Żaden stan nie jest rozpoznawalny wyłącznie po kolorze.
- [ ] Na ekranie jest **dokładnie jeden** przycisk główny.
