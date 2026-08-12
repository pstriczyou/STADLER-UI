# Stadler Depo — wzorzec UI

Punkt wyjścia dla warstwy widoku aplikacji, którą będziemy rozwijać dalej. W folderze
są trzy rzeczy:

| Co | Gdzie | Po co |
| --- | --- | --- |
| **Makieta** | [`makieta/index.html`](makieta/index.html) | działający ekran do obejrzenia w przeglądarce — wzorcowy formularz z kompletem pól, stanów i komunikatów |
| **Konwencje** | [`KONWENCJE.md`](KONWENCJE.md) | reguły, których makieta jest ilustracją: tokeny, typografia, układ formularza, wybór kontrolki, dostępność |
| **Skill** | [`.claude/skills/front-platformy/SKILL.md`](.claude/skills/front-platformy/SKILL.md) | żeby Claude Code budował kolejne ekrany w tej samej konwencji, bez przypominania mu jej za każdym razem |

To jest wzorzec wizualny, nie architektoniczny — opisuje wygląd, układ, zachowanie
kontrolek i dostępność. Nie mówi, w czym aplikacja ma być napisana, skąd biorą się
dane ani jak wygląda warstwa serwera; te decyzje należą do repozytorium aplikacji
i są od tego wzorca niezależne.

---

## Otwórz makietę

Dwuklik na [`makieta/index.html`](makieta/index.html) — bez serwera, bez instalacji,
bez połączenia z internetem, to zwykły plik.

Warto kliknąć Zapisz na pustym formularzu (pokazuje obsługę błędów), przełączyć
adnotacje wzorca na górze strony (żółte ramki znikają i zostaje czysty ekran)
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

Makieta jest jednym plikiem celowo — style siedzą w `<style>`, logika w `<script>`
u góry dokumentu. Dzięki temu obejrzenie wzorca nie wymaga budowania projektu.

---

## Skąd wzięła się ta estetyka

Formularz bywa wypełniany na hali, w rękawicach, przy monitorze pod światło, a dane
wjeżdżają do niego czytnikiem kodów kreskowych. Stąd kilka decyzji, które w zwykłej
aplikacji webowej wyglądałyby na przesadę:

- minimalna wysokość celu 44 px, bo mniejszy przycisk klika się obok;
- kontrast tekstu pomocniczego podniesiony ponad typowe szarości;
- pole skanera przechwytuje `Enter`, bo inaczej czytnik kodów wysyła formularz
  w połowie wypełniania;
- kolor nigdy nie niesie znaczenia sam — przy każdym kolorze stoi tekst albo ikona;
- treść pola nie mniejsza niż 16 px, bo poniżej tego Safari na iOS przybliża stronę
  przy ognisku i rozjeżdża układ.

Kolor `#00599C` zmierzyliśmy z pliku `logo.png` (piksel rdzenia litery), więc
nagłówek zgadza się ze znakiem słownym co do piksela. Fonty firmowe nie są
wczytywane — interfejs stoi na kroju systemowym, żeby tekst pojawiał się od razu.

---

## Zakładanie nowego repozytorium

```bash
cp -r wzorzec-frontu ../nazwa-nowego-repo
cd ../nazwa-nowego-repo
git init && git add . && git commit -m "Dodaj wzorzec UI: makieta, konwencje, skill"
```

Folder `.claude/skills/` zostaje w korzeniu nowego repo. Claude Code znajduje skille
też w podkatalogach, ale wtedy wiąże je z tym podkatalogiem — skill leżący głębiej
zadziała tylko przy plikach pod nim, nie w całym projekcie.

> To repozytorium powinno być prywatne. Zawiera znak słowny i logo firmy
> (`makieta/assets/logo.png`) oraz firmową paletę. Kod i konwencje same w sobie
> nie są wrażliwe, ale znak towarowy w publicznym repo to osobna sprawa.
>
> Jeśli repo ma być publiczne: usuń `logo.png`, zamień `--niebieski` na wartość
> zastępczą i wytnij nazwę firmy z nagłówka. Poza tymi trzema miejscami nic
> firmowego w folderze nie ma — dane w makiecie są zmyślone, a numery dokumentów
> mają postać przykładową.

---

## Czego tu nie ma i gdzie tego szukać

Makieta pokazuje formularz. Ekran listy z filtrami, tabela wyników, ekran szczegółów
i nawigacja modułowa to osobne wzorce — dochodzą tak samo, jednym plikiem na wzorzec,
i tak samo trafiają do skilla. Kolejność zależy od tego, co powstanie w aplikacji
jako pierwsze.

Reguły, które makieta ilustruje, ale których nie widać z samego wyglądu, stoją
w [`KONWENCJE.md`](KONWENCJE.md).
