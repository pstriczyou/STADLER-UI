# Stadler Depo — wzorzec UI

Punkt wyjścia dla warstwy widoku aplikacji, która będzie rozwijana dalej. Trzy rzeczy
w jednym folderze:

| Co | Gdzie | Po co |
| --- | --- | --- |
| **Makieta** | [`makieta/index.html`](makieta/index.html) | działający ekran do obejrzenia w przeglądarce — wzorcowy formularz z kompletem pól, stanów i komunikatów |
| **Konwencje** | [`KONWENCJE.md`](KONWENCJE.md) | reguły, których makieta jest ilustracją: tokeny, typografia, układ formularza, wybór kontrolki, dostępność |
| **Skill** | [`.claude/skills/front-platformy/SKILL.md`](.claude/skills/front-platformy/SKILL.md) | żeby Claude Code budował kolejne ekrany w tej samej konwencji, bez przypominania mu jej za każdym razem |

**To jest wzorzec wizualny, nie architektoniczny.** Opisuje wygląd, układ, zachowanie
kontrolek i dostępność. Nie mówi, w czym aplikacja ma być napisana, skąd biorą się dane
ani jak wygląda warstwa serwera — te decyzje mieszkają w repozytorium aplikacji
i są od tego wzorca niezależne.

---

## Otwórz makietę

Dwuklik na [`makieta/index.html`](makieta/index.html). Bez serwera, bez instalowania
czegokolwiek, bez połączenia z internetem — to zwykły plik.

Warto kliknąć **Zapisz** na pustym formularzu (pokazuje obsługę błędów), przełączyć
**adnotacje wzorca** na górze strony (żółte ramki znikają i zostaje czysty ekran)
i zwęzić okno poniżej 640 px (układ schodzi do jednej kolumny).

---

## Co jest w środku

```text
wzorzec-frontu/
├── README.md                 ← ten plik
├── KONWENCJE.md              ← reguły UI, z uzasadnieniami
├── makieta/
│   ├── index.html            ← makieta: jeden plik, cały wzorzec
│   └── assets/
│       ├── logo.png
│       └── vendor/           ← Alpine; napędza interakcje makiety
└── .claude/skills/front-platformy/
    └── SKILL.md              ← instrukcja dla Claude Code
```

Makieta jest **jednym plikiem** celowo. Style siedzą w `<style>`, logika w `<script>`
u góry dokumentu. Nie dlatego, że tak jest ładniej — dlatego, że wzorzec, którego
obejrzenie wymaga zbudowania projektu, przestaje być oglądany.

---

## Skąd wzięła się ta estetyka

Odbiorcą nie jest programista. Formularz bywa wypełniany **na hali, w rękawicach,
przy monitorze pod światło**, a dane wjeżdżają do niego **czytnikiem kodów kreskowych**.
Stąd rzeczy, które w zwykłej aplikacji webowej wyglądają na przesadę:

- minimalna wysokość celu **44 px** — mniejszy przycisk klika się obok;
- **kontrast tekstu pomocniczego** podniesiony ponad typowe szarości;
- **pole skanera przechwytuje `Enter`** — czytnik kończy nim wpis i bez przechwycenia
  wysyła formularz w połowie wypełniania;
- **kolor nigdy nie niesie znaczenia sam** — przy każdym kolorze stoi tekst albo ikona;
- **treść pola nigdy mniejsza niż 16 px** — poniżej tego Safari na iOS przybliża stronę
  przy ognisku i rozjeżdża układ.

Kolor `#00599C` jest zmierzony z pliku `logo.png` (piksel rdzenia litery), nie znaleziony
w sieci — dzięki temu nagłówek zgadza się ze znakiem słownym co do piksela.
Fonty firmowe nie są wczytywane; interfejs idzie na krój systemowy, żeby tekst pojawiał
się od razu.

---

## Zakładanie nowego repozytorium

```bash
cp -r wzorzec-frontu ../nazwa-nowego-repo
cd ../nazwa-nowego-repo
git init && git add . && git commit -m "Dodaj wzorzec UI: makieta, konwencje, skill"
```

Folder `.claude/skills/` ma zostać **w korzeniu** nowego repo. Claude Code znajduje
skille także w podkatalogach, ale wtedy wiąże je z tym podkatalogiem — skill leżący
głębiej zadziała tylko przy plikach pod nim, a nie w całym projekcie.

> **To repozytorium powinno być prywatne.** Zawiera znak słowny i logo firmy
> (`makieta/assets/logo.png`) oraz firmową paletę. Kod i konwencje same w sobie
> nie są wrażliwe, ale znak towarowy w publicznym repo to osobna sprawa —
> niezależna od tego, co robi kod.
>
> Jeśli repo ma być publiczne: usuń `logo.png`, zamień `--niebieski` na wartość
> zastępczą i wytnij nazwę firmy z nagłówka. Poza tymi trzema miejscami nic
> firmowego w folderze nie ma — dane w makiecie są zmyślone, a numery dokumentów
> mają postać przykładową.

---

## Czego tu nie ma i gdzie tego szukać

Makieta pokazuje **formularz**. Ekran listy z filtrami, tabela wyników, ekran
szczegółów i nawigacja modułowa to osobne wzorce — dochodzą tak samo, jednym plikiem
na wzorzec, i tak samo trafiają do skilla. Kolejność wynika z tego, co powstanie
w aplikacji jako pierwsze.

Reguły, które makieta ilustruje, ale których nie widać z samego wyglądu, stoją
w [`KONWENCJE.md`](KONWENCJE.md).
# STADLER-UI
