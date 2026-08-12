---
name: front-platformy
description: Użyj przy budowaniu albo zmienianiu dowolnego ekranu użytkownika — formularza, listy, tabeli, panelu, komunikatu. Wywołuj, gdy w zadaniu pada "formularz", "ekran", "widok", "szablon", "strona", "UI", "front", "przycisk", "pole", "walidacja", "CSS", "styl", "makieta", "układ", "dostępność", albo gdy zmiana dotyczy pliku .html lub .css. Skill niesie wzorzec wizualny: tokeny, typografię, układ formularza, wybór kontrolki, stany, komunikaty, dostępność i ograniczenia hali produkcyjnej. Dotyczy wyłącznie warstwy widoku — nie rozstrzyga technologii ani architektury.
---

# Wzorzec UI — Stadler Depo

Warstwa widoku ma jeden wzorzec i jest nim [`makieta/index.html`](../../../makieta/index.html).
Reguły z uzasadnieniami stoją w [`KONWENCJE.md`](../../../KONWENCJE.md).

**Ten skill nie powtarza tamtych plików.** Mówi, w jakiej kolejności je czytać,
co skopiować i czego pilnować, żeby nowy ekran nie był trzecim wariantem tego samego.

Zakres: **wygląd, układ, zachowanie kontrolek, dostępność.** Skąd biorą się dane
i w czym jest napisany serwer — nie należy do tego skilla.

---

## Zanim napiszesz pierwszą linijkę

1. **Otwórz `makieta/index.html` i znajdź element, który już robi to, czego potrzebujesz.**
   Pole tekstowe, grupa checkboxów, komunikat, pasek akcji, znacznik statusu — są tam
   z kompletem stanów i atrybutów dostępności. Skopiuj i podmień treść.
2. **Nowa klasa CSS jest ostatecznością.** Najpierw sprawdź, czy nie ma już
   `.karta`, `.pole`, `.pomoc`, `.blad-pola`, `.siatka`, `.grupa-wyboru`, `.przycisk`,
   `.znacznik`, `.komunikat`, `.pasek-akcji`, `.z-przyrostkiem`, `.przelacznik`.
3. **Nowego `#hex` nie wpisujesz.** Kolory są tokenami w `:root`. Jeśli któregoś naprawdę
   brakuje — dołóż token do `:root`, nie wartość do reguły.
4. **Klasa nazywa rolę, nie wygląd.** `.znacznik.uwaga`, nie `.znacznik-zolty`.

Jeśli robisz wzorzec, którego w makiecie nie ma (ekran listy, tabela, kreator
wielokrokowy) — **zbuduj go i dopisz do makiety**, żeby następny raz zaczął się od kroku 1.

---

## Szkielet pola — kopiuj to

Stan zwykły:

```html
<div class="pole">
  <label for="numer">Numer dokumentu <span class="wymagane" aria-hidden="true">*</span></label>
  <input type="text" id="numer" name="numer" class="mono"
         value="" placeholder="SI_3210103" autocomplete="off"
         aria-invalid="false"
         aria-describedby="numer-pomoc">
  <p class="pomoc" id="numer-pomoc">Dwie litery, podkreślnik, siedem cyfr.</p>
</div>
```

Ten sam blok po odrzuceniu formularza. **Trzy zmiany, żadnej innej:**

```html
<div class="pole">
  <label for="numer">Numer dokumentu <span class="wymagane" aria-hidden="true">*</span></label>
  <input type="text" id="numer" name="numer" class="mono"
         value="SI-321" placeholder="SI_3210103" autocomplete="off"
         aria-invalid="true"                                    <!-- 1 -->
         aria-describedby="numer-pomoc numer-blad">             <!-- 2 -->
  <p class="pomoc" id="numer-pomoc">Dwie litery, podkreślnik, siedem cyfr.</p>
  <p class="blad-pola" id="numer-blad">Numer ma postać: dwie litery,
     podkreślnik, siedem cyfr — np. SI_3210103.</p>             <!-- 3 -->
</div>
```

Warunki, które muszą się zgadzać, i to one najczęściej wypadają:

- `for` w etykiecie = `id` pola;
- `aria-describedby` wymienia **wszystkie** opisy pola — przy błędzie pomoc **zostaje**,
  a identyfikator błędu jest dopisywany, nie podmieniany;
- `aria-invalid` przełącza się na `true` razem z pojawieniem się błędu;
- pomoc jest **zawsze**, błąd **tylko gdy istnieje**;
- `value` niesie to, co użytkownik wpisał — nigdy nie wraca puste.

---

## Podsumowanie błędów — na górze formularza, bez wyjątku

Renderowane **tylko wtedy, gdy formularz ma błędy**, jako pierwszy element formularza:

```html
<div class="komunikat blad" role="alert" tabindex="-1" id="podsumowanie-bledow">
  <div class="tresc">
    <strong>Formularz ma 2 błędy — nic nie zostało zapisane.</strong>
    <ul>
      <li><a href="#numer">Numer ma postać: dwie litery, podkreślnik, siedem cyfr.</a></li>
      <li><a href="#obszary">Zaznacz przynajmniej jeden obszar.</a></li>
    </ul>
  </div>
</div>
```

Każda pozycja listy jest **linkiem do `id` błędnego pola** — nie samym tekstem.
`tabindex="-1"` pozwala przenieść na ten blok ognisko, `role="alert"` sprawia,
że czytnik ekranu przeczyta go bez pytania.

Bez tego bloku ktoś poruszający się klawiaturą dostaje po kliknięciu `Zapisz` brak
reakcji i nie ma jak się dowiedzieć, że formularz w ogóle ma błędy.

---

## Którą kontrolkę wybrać

| Sytuacja | Kontrolka |
| --- | --- |
| 2–5 opcji, wybór jeden | grupa radio, cały prostokąt klikalny |
| 6 i więcej, wybór jeden | `<select>` z pustą pozycją `— wybierz —` na początku |
| wybór wielokrotny | siatka checkboxów — **nigdy** `<select multiple>` |
| włącznik zachowania | przełącznik `role="switch"` |
| liczba z miarą | pole liczbowe z jednostką **w polu**, nie w etykiecie |
| wartość wyliczana | pole `readonly`, szare, `tabindex="-1"` |
| sekcja rzadko używana | `<details>` — działa z klawiatury bez JavaScriptu |

---

## Czerwone flagi — jeśli to piszesz, zatrzymaj się

| Kod | Dlaczego nie |
| --- | --- |
| `outline: none` | usuwa pierścień ogniska; ekran przestaje być obsługiwalny klawiaturą |
| `placeholder` zamiast `<label>` | placeholder znika po pierwszym znaku |
| `display: none` na polu schowanym wizualnie | wypada z kolejności `Tab`; użyj zerowego rozmiaru |
| `<select multiple>` | wymaga `Ctrl`; w rękawicach nie do obsłużenia |
| `<select>` bez pustej pozycji na początku | niewybrana wartość wchodzi do bazy jako decyzja |
| `maxlength` bez licznika | ucina po cichu, użytkownik pisze dalej |
| `disabled` bez wyjaśnienia | ślepy zaułek |
| przycisk zapisu wyłączony do czasu poprawnego wypełnienia | nie widać, czego brakuje |
| pomoc wyłącznie w dymku na najechanie | nie istnieje na dotyku ani dla klawiatury |
| formularz w oknie modalnym | gubi się przy odświeżeniu, nie ma własnego adresu |
| drugi przycisk główny na ekranie | na ekranie jest dokładnie jeden |
| `#hex` poza `:root` | tokeny przestają być jednym miejscem |
| czcionka pola mniejsza niż 16 px | Safari na iOS przybliża stronę przy ognisku |

---

## Hala produkcyjna — cztery rzeczy, o których łatwo zapomnieć

1. **44 px** minimalnej wysokości pola i przycisku (`--wys-pola`). Rękawice.
2. **Pole na kod kreskowy przechwytuje `Enter`** — czytnik kończy nim wpis, a bez
   przechwycenia wysyła formularz w połowie wypełniania:

   ```html
   @keydown.enter.prevent="$refs.nastepne.focus()"
   ```

3. **Kolor nie niesie znaczenia sam.** Przy każdym kolorze stoi tekst albo ikona.
4. **Stan „zapisywanie…" jest widoczny**, a przycisk na ten czas nieaktywny —
   inaczej ktoś kliknie drugi raz i powstaną dwa rekordy.

---

## Zanim powiesz, że gotowe

- [ ] Ekran da się wypełnić **samą klawiaturą**, od pierwszego pola do zapisu.
- [ ] `Zapisz` na pustym formularzu → podsumowanie błędów, dane nie znikają.
- [ ] Każde pole ma `<label for>` + `aria-describedby` do pomocy i błędu.
- [ ] Przy 360 px nic nie wystaje i nic nie przewija się w poziomie.
- [ ] Każde pole i każdy przycisk ma co najmniej 44 px wysokości.
- [ ] Zero `#hex` poza `:root`.
- [ ] Żaden stan nie jest rozpoznawalny wyłącznie po kolorze.
- [ ] Dokładnie jeden przycisk główny.

Pełne uzasadnienia każdej z tych reguł: [`KONWENCJE.md`](../../../KONWENCJE.md).
Wzór wizualny: [`makieta/index.html`](../../../makieta/index.html).
