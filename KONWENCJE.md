# Konwencje UI

Reguły, których [`makieta/index.html`](makieta/index.html) jest ilustracją. Makieta
pokazuje, jak to wygląda; ten plik tłumaczy dlaczego i co zrobić w przypadkach,
których makieta nie pokazuje.

Dokument dotyczy wyłącznie warstwy widoku: kolorów, typografii, układu, kontrolek,
stanów i dostępności. Nie rozstrzyga o technologii ani o tym, skąd biorą się dane.

Odbiorcą nie jest programista, tylko operator na hali albo inżynier między dwoma
spotkaniami. Dlatego prostota i czytelność liczą się bardziej niż eleganckie
rozwiązanie, którego trzeba się nauczyć.

---

## 1. Tokeny — jedyne miejsce z wartościami

Kolory, promienie i wymiary trzymamy w `:root` jako zmienne CSS. Nowy ekran nie
wpisuje własnego `#hex` ani `padding: 13px`, tylko korzysta z istniejącego tokenu
albo dopisuje nowy do `:root`, jeśli faktycznie go brakuje.

| Grupa | Tokeny |
| --- | --- |
| marka | `--niebieski` `--niebieski-jasny` `--niebieski-tlo` `--niebieski-ciemny` |
| powierzchnie | `--tlo` `--karta` `--biel` `--obwodka` `--obwodka-mocna` |
| tekst | `--tekst` `--tekst-jasny` `--tekst-placeholder` |
| znaczenia | `--blad` `--uwaga` `--sukces` (+ warianty `-tlo`) |
| kształt | `--promien` `--promien-karty` `--cien-karty` `--cien-ogniska` `--wys-pola` |

`--niebieski: #00599C` zmierzyliśmy bezpośrednio z pliku logo (piksel rdzenia
litery), dzięki czemu nagłówek zgadza się ze znakiem słownym co do piksela. Przy
zmianie znaku firmowego wystarczy zmienić tę jedną wartość.

Kolory znaczeniowe są cztery: niebieski (informacja), pomarańczowy (ostrzeżenie),
czerwony (błąd), zielony (powodzenie). Piątego celowo nie dodajemy — np. fioletu
na „specjalny stan" przy jednym ekranie — bo wtedy użytkownik przestaje polegać
na tym, że kolor cokolwiek znaczy.

---

## 2. Typografia i rytm

Krój systemowy (`Segoe UI` → `Helvetica Neue` → `system-ui`). Fontów firmowych
nie wczytujemy, żeby tekst pojawiał się od razu, bez czekania na plik.

| Element | Rozmiar | Grubość |
| --- | --- | --- |
| tytuł strony | 24 px | 700 |
| nagłówek sekcji | 16 px | 700 |
| tekst i treść pól | 16 px | 400 |
| etykieta pola | 13,5 px | 600 |
| pomoc, błąd, meta | 12,5 px | 400 / 600 |
| znacznik | 11 px | 700 |

Pole nie powinno mieć czcionki mniejszej niż 16 px — Safari na iOS przy ognisku
w mniejszym polu przybliża stronę i rozjeżdża układ.

Interlinia 1,5 dla tekstu ciągłego. Odstępy: 6 px wewnątrz pola (etykieta → pole →
pomoc), 16–20 px między polami, 18 px między kartami, 22–24 px wyściółki karty.
Inne wartości traktujemy jako wyjątek, który trzeba uzasadnić.

---

## 3. Nazewnictwo i organizacja CSS

Klasy po polsku, tak jak reszta kodu: `.karta`, `.pole`, `.pomoc`, `.blad-pola`,
`.pasek-akcji`, `.znacznik` — nie `.card`, nie `.form-group`. Mieszanie języków
w nazwach klas utrudnia grepowanie.

Klasa opisuje rolę, nie wygląd: `.znacznik.uwaga`, nie `.znacznik-zolty`. Kolor
ostrzeżenia da się wtedy zmienić w jednym miejscu, zamiast poprawiać nazwę klasy
w dwudziestu plikach.

Styl jednego ekranu zostaje przy tym ekranie. Do arkusza wspólnego trafia tylko
to, czego używa więcej niż jeden ekran.

---

## 4. Formularz — układ

Kolejność pionowa jest stała:

```text
etykieta
[ pole ]
pomoc (zawsze, jeśli format nie jest oczywisty)
błąd (tylko gdy jest)
```

Etykieta nad polem, nie obok — układ z etykietami po lewej rozjeżdża się przy
dłuższych polskich napisach i słabo skaluje się do jednej kolumny na tablecie.

Pomoc i błąd pod polem, nie nad — czytający wraca wzrokiem do pola, w którym
stoi, zamiast szukać w górę.

Pomoc opisuje format, zanim ktoś się pomyli. „Dwie litery, podkreślnik, siedem
cyfr" powinno stać pod polem od razu, a nie dopiero pojawić się w komunikacie
po odrzuceniu formularza.

Sekcje mają nagłówek i jedno zdanie opisu — formularz dłuższy niż osiem pól bez
podziału na sekcje trudno ogarnąć wzrokiem.

Pola wymagane oznaczamy gwiazdką z `aria-hidden="true"`. Czytnik ekranu bierze
wymagalność z komunikatu błędu i z `aria-invalid`, nie z samego znaku graficznego
— inaczej ogłasza „gwiazdka" przy niemal każdym polu. Tam, gdzie większość pól
jest wymagana, pola opcjonalne oznaczamy słowem „— opcjonalne".

Siatka dwukolumnowa, z wyjątkami na całą szerokość: pole długiego tekstu, grupa
checkboxów i wybór pliku zajmują obie kolumny, bo ściśnięte do połowy stają się
nieczytelne.

---

## 5. Wybór właściwej kontrolki

| Sytuacja | Kontrolka |
| --- | --- |
| 2–5 opcji, wybór jeden | grupa radio, cały prostokąt klikalny |
| 6 i więcej, wybór jeden | `<select>` z pustą pierwszą pozycją `— wybierz —` |
| wybór wielokrotny | siatka checkboxów |
| włącznik zachowania | przełącznik (`role="switch"`) |
| wartość liczbowa z miarą | pole liczbowe z jednostką w polu, nie w etykiecie |
| wartość powstająca z innych | pole `readonly`, szare, poza kolejnością `Tab` |

`<select multiple>` nie używamy — wymaga trzymania `Ctrl`, co jest niewykonalne
w rękawicach.

Przełącznik stosujemy tylko wtedy, gdy chodzi o włączenie zachowania
(„powiadamiaj zespół"), a nie o zaznaczenie faktu („dotyczy dachu"). Pomylenie
tych dwóch znaczeń to częsty błąd w formularzach wewnętrznych.

Domyślna wartość, której nikt nie wybrał, bywa niebezpieczna: `<select>` startuje
od pustej pozycji „— wybierz —", bo inaczej pierwsza opcja na liście trafia do
bazy jako świadoma decyzja, chociaż nikt jej nie podjął. Wyjątek: pole z prawdziwą
wartością domyślną (priorytet „zwykły") — tam pusta pozycja nie ma sensu, bo
odpowiedź faktycznie istnieje.

Limit długości zawsze z licznikiem — sam `maxlength` ucina tekst po cichu i
użytkownik nie widzi, że dalsza część się nie zmieściła.

Pole zablokowane zawsze mówi dlaczego — `disabled` bez wyjaśnienia zostawia
użytkownika bez informacji, co robić dalej.

Jednostka mieszka w polu, nie w etykiecie — po wypełnieniu etykiety już nie
widać, więc `12,5` bez `kg` przy wartości traci sens.

---

## 6. Błędy, komunikaty, stan zapisu

Po nieudanym zapisie:

1. na górze formularza pojawia się podsumowanie błędów — ogniskowalne
   (`tabindex="-1"`), z `role="alert"`, z błędami jako linkami do pól;
2. każde błędne pole dostaje `aria-invalid="true"` i komunikat pod sobą;
3. wpisane dane zostają, nic nie znika.

Podsumowanie na górze jest konieczne dla dostępności: bez niego osoba poruszająca
się klawiaturą albo czytnikiem ekranu nie ma jak się dowiedzieć, że formularz
zawiera błędy — widzi tylko brak reakcji po kliknięciu Zapisz.

Komunikat błędu mówi, co zrobić, nie tylko że coś poszło źle: „Numer ma postać:
dwie litery, podkreślnik, siedem cyfr — np. `SI_3210103`", a nie „Nieprawidłowy
format".

Cztery znaczenia komunikatu: informacja (nic nie trzeba robić), ostrzeżenie
(można iść dalej, ale wynik będzie niepełny), błąd (nie udało się, nic nie
zapisano), powodzenie. Każdy ma pogrubiony nagłówek i zdanie treści.

Stan „zapisywanie…" musi być widoczny, a przycisk na ten czas nieaktywny — jego
brak jest częstą przyczyną podwójnych zapisów, gdy ktoś klika drugi raz, bo nic
się nie działo.

Niezapisane zmiany sygnalizujemy w pasku akcji, żeby dało się wyjść z formularza
świadomie, a nie stracić wypełnione dane przez przypadek.

Na ekranie jest jeden przycisk główny, reszta jest drugorzędna. Dwa przyciski
w tym samym kolorze oznaczają, że nie ustalono, która akcja jest właściwa.

---

## 7. Dostępność

Ekran bywa obsługiwany klawiaturą, przy słabym oświetleniu i przez kogoś, kto
nie widzi dobrze z bliska — stąd poniższe zasady, niezależnie od zgodności
z normami.

- Każde pole ma `<label for="…">`. Placeholder nie zastępuje etykiety — znika
  po wpisaniu pierwszego znaku i nie da się do niego wrócić wzrokiem.
- `aria-describedby` wiąże pole z pomocą i błędem. Przy błędzie identyfikator
  błędu dopisujemy, nie podmieniamy — pomoc powinna zostać widoczna dla
  czytnika.
- Pierścień ogniska (`:focus-visible`) zostaje zawsze. `outline: none` bez
  zamiennika zostawia użytkownika klawiatury bez informacji, gdzie jest fokus.
- Element schowany wizualnie zostaje w kolejności `Tab`. `display: none` na
  polu wyboru pliku wyrzuca je z klawiatury — lepiej dać mu zerowy rozmiar
  i pierścień rysowany przez etykietę.
- Grupy pól (radio, checkboxy) mają `role="group"` i `aria-labelledby`
  wskazujące na etykietę grupy. Błąd grupy wisi pod całą grupą, bo nie dotyczy
  jednego konkretnego pola.
- Kolejność `Tab` powinna odpowiadać kolejności czytania. Jeśli się rozjeżdża,
  poprawiamy strukturę HTML, nie dodajemy `tabindex`.
- Kolor nie niesie znaczenia sam — zawsze towarzyszy mu tekst albo ikona.
- `prefers-reduced-motion` wyłącza animacje jedną regułą na cały arkusz.

---

## 8. Zachowanie na wąskim ekranie

Dwa progi:

- 900 px — panel boczny przestaje być kolumną i ląduje nad treścią, bo niesie
  wskazówki potrzebne przed wypełnianiem formularza, nie po;
- 640 px — siatka schodzi do jednej kolumny, nawigacja znika z nagłówka,
  przyciski w pasku akcji rozciągają się na pełną szerokość.

Nic nie przewija się w poziomie. Jeśli coś wystaje przy 360 px, to błąd do
naprawienia, nie cecha treści.
