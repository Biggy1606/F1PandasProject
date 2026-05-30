# Analiza Danych Formuły 1: Jak technika zmieniała wyścigi przez ponad 70 lat

**Przedmiot:** Analiza danych w języku Python (biblioteka pandas)
**Skład sekcji (zespołu):** Igor Barcik, Łukasz Domagała
**Kierunek:** Informatyka
**Rok:** 2026

---

## Spis treści

- [Analiza Danych Formuły 1: Jak technika zmieniała wyścigi przez ponad 70 lat](#analiza-danych-formuły-1-jak-technika-zmieniała-wyścigi-przez-ponad-70-lat)
  - [Spis treści](#spis-treści)
  - [1. Co badamy i dlaczego](#1-co-badamy-i-dlaczego)
  - [2. Skąd dane i co zawierają](#2-skąd-dane-i-co-zawierają)
    - [2.1. Skąd wzięliśmy dane](#21-skąd-wzięliśmy-dane)
    - [2.2. Jak duże są dane](#22-jak-duże-są-dane)
    - [2.3. Najważniejsze tabele](#23-najważniejsze-tabele)
    - [2.4. Czyszczenie danych przed analizą](#24-czyszczenie-danych-przed-analizą)
  - [3. Analiza danych](#3-analiza-danych)
    - [3.1. Jak ciężar paliwa wpływa na czas okrążenia](#31-jak-ciężar-paliwa-wpływa-na-czas-okrążenia)
    - [3.2. Czy 3 najlepsze zespoły zgarniają wszystko](#32-czy-3-najlepsze-zespoły-zgarniają-wszystko)
    - [3.3. Który zespół zdobywa najwięcej punktów na sezon](#33-który-zespół-zdobywa-najwięcej-punktów-na-sezon)
    - [3.4. Czy auta dziś częściej dojeżdżają do mety (niezawodność)](#34-czy-auta-dziś-częściej-dojeżdżają-do-mety-niezawodność)
    - [3.5. Czy auta z roku na rok jeżdżą szybciej](#35-czy-auta-z-roku-na-rok-jeżdżą-szybciej)
    - [3.6. Kto wygrywa z kolegą z tego samego zespołu](#36-kto-wygrywa-z-kolegą-z-tego-samego-zespołu)
    - [3.7. Wykres zbiorczy](#37-wykres-zbiorczy)
  - [4. Podsumowanie i wnioski](#4-podsumowanie-i-wnioski)
    - [Ograniczenia i co dalej](#ograniczenia-i-co-dalej)
  - [5. Załączniki](#5-załączniki)
    - [Użyte narzędzia](#użyte-narzędzia)
    - [Jak uruchomić](#jak-uruchomić)

---

## 1. Co badamy i dlaczego

W Formule 1 wynik na torze zależy nie tylko od kierowcy, ale przede wszystkim od techniki — od tego,
jak dobry jest bolid. Co kilka lat zmieniają się przepisy (np. inny rodzaj silnika), a inżynierowie
budują coraz lepsze auta. Każda taka zmiana zostawia ślad w liczbach: w czasach okrążeń, w liczbie
zwycięstw, w tym ile aut dojeżdża do mety.

**Cel projektu:** sprawdzić na liczbach, jak postęp techniczny zmieniał wyścigi przez 77 sezonów
(lata 1950–2026). Patrzymy na trzy rzeczy: jak szybkie były auta, jak bardzo były awaryjne i jak
wyrównana była walka między zespołami.

Pytania, na które szukamy odpowiedzi:

- **Czy auta dziś rzadziej się psują niż kiedyś?** Czyli: czy dojechanie do mety stało się normą?
- **Czy auta z roku na rok jeżdżą coraz szybciej?** Czy może czasem zwalniają, bo nowe przepisy
  „cofają" rozwój?
- **Czy widać, że pełny bak paliwa spowalnia auto?** I czy widać w danych zakaz tankowania w czasie
  wyścigu (wprowadzony w 2010 roku)?
- **Czy zawsze wygrywają te same 2–3 zespoły, czy walka bywa bardziej wyrównana?**
- **Czy liczby pokazujące „talent" kierowcy są uczciwe?** Czy łatwo nimi zmanipulować wynik?

Projekt jest **opisowy** — to znaczy, że opisujemy i tłumaczymy, co się działo, ale nie przewidujemy
przyszłości. Ważne: przy każdej liczbie mówimy też, kiedy może ona mylić. Pokazanie słabości danej
miary jest tak samo cenne jak sama liczba.

---

## 2. Skąd dane i co zawierają

### 2.1. Skąd wzięliśmy dane

Dane pochodzą z darmowej, publicznej bazy wyników F1 (projekt **Ergast**, udostępniony na platformie
**Kaggle**). To zwykłe pliki **CSV** (tabele z danymi, jak w Excelu).

Pliki wczytujemy do **DuckDB**. DuckDB to mała baza danych, która rozumie język **SQL** (język do
zadawania pytań danym, np. „pokaż wszystkie wyścigi z 2020 roku"). Działa szybko i nie wymaga osobnego
serwera — uruchamia się od razu w programie. Dzięki temu możemy zadawać pytania nawet do bardzo dużych
tabel (setki tysięcy wierszy) i od razu dostawać wynik w Pythonie.

```python
# Pobierz pliki CSV z Kaggle i wczytaj każdy jako osobną tabelę w bazie
race_data = kagglehub.dataset_download("jtrotman/formula-1-race-data")
conn = duckdb.connect("f1.db")
for f in glob.glob(f"{race_data}/*.csv"):
    name = os.path.splitext(os.path.basename(f))[0]
    conn.execute(f"CREATE OR REPLACE TABLE {name} AS SELECT * FROM read_csv_auto('{f}')")
```

### 2.2. Jak duże są dane

| Co | Ile |
|---|---|
| Zakres lat | **1950 – 2026** |
| Liczba sezonów | 77 |
| Liczba wyścigów (Grand Prix) | 1 171 |
| Liczba kierowców | 865 |
| Liczba zespołów (konstruktorów) | 214 |
| Liczba torów | 78 |
| Liczba pojedynczych okrążeń (tabela `lap_times`) | **871 076** |

> **Ważne o latach:** nie każda analiza obejmuje pełne 1950–2026. Niektóre dane zaczęto zapisywać
> później — np. czasy pojedynczych okrążeń istnieją dopiero od lat 80./90. Sezon 2026 jest **w trakcie**
> (jeszcze się nie skończył), więc ostatni punkt na wykresach trzeba czytać ostrożnie.

### 2.3. Najważniejsze tabele

Tabela = arkusz z danymi. Każda ma swój temat. Łączymy je ze sobą po wspólnych numerach (tzw.
identyfikatorach, np. `raceId` = numer wyścigu).

| Tabela | Liczba wierszy | Co zawiera |
|---|---:|---|
| `races` | 1 171 | Kalendarz wyścigów: rok, runda, tor, data |
| `results` | 27 348 | Wyniki: zajęte miejsce, punkty, czy auto dojechało do mety |
| `lap_times` | 871 076 | Czas każdego pojedynczego okrążenia |
| `qualifying` | 11 080 | Wyniki kwalifikacji (walka o miejsce startowe) |
| `pit_stops` | 22 249 | Postoje w boksie (zmiana opon itd.) |
| `drivers` | 865 | Kierowcy: imię, nazwisko, narodowość, data urodzenia |
| `constructors` | 214 | Zespoły: nazwa, narodowość |
| `status` | 140 | Słownik powodów zakończenia (np. „dojechał", „silnik", „kolizja") |
| `circuits` | 78 | Tory: nazwa, kraj, położenie |

### 2.4. Czyszczenie danych przed analizą

Surowe dane nigdy nie są idealne. Zanim coś policzymy, trzeba je posprzątać:

- **Braki danych** — gdy czegoś brakuje, w plikach jest znak `\N`. Takie wiersze pomijamy lub
  oznaczamy jako „puste".
- **Łączenie tabel** — sklejamy tabele po wspólnych numerach (np. wynik + nazwa kierowcy + nazwa toru).
- **Czas w milisekundach** — czasy okrążeń zamieniamy na milisekundy, żeby dało się je dodawać
  i porównywać.
- **Usuwanie dziwnych wartości (metoda IQR)** — IQR to prosty sposób na wyrzucenie wartości skrajnych.
  Np. okrążenie podczas postoju w boksie jest bardzo wolne i zaburzyłoby wynik, więc je odrzucamy.
- **Czy auto dojechało do mety** — z powodu zakończenia robimy prostą informację: „dojechał" albo
  „nie dojechał".

---

## 3. Analiza danych

Zrobiliśmy sześć osobnych analiz. Każda działa tak samo: pytamy bazę o dane (SQL), liczymy w Pythonie
(`pandas`, `numpy`) i rysujemy wykres (`matplotlib`, `seaborn`). Gdzie trzeba, rysujemy też **linię
trendu** — gładką krzywą, która pokazuje ogólny kierunek mimo wahań.

### 3.1. Jak ciężar paliwa wpływa na czas okrążenia

**Co robimy.** Bierzemy dwa tory, które najczęściej gościły wyścigi (Silverstone i Hungaroring).
Patrzymy, jak zmienia się czas okrążenia w trakcie jednego wyścigu. Dzielimy historię na dwa okresy:
**gdy wolno było tankować w czasie wyścigu (1994–2009)** i **gdy zakazano tankowania (od 2010)**.

**Wynik.** Im więcej paliwa się spali, tym auto jest lżejsze i szybsze — czasy okrążeń maleją w trakcie
wyścigu. Efekt jest **mocniejszy po 2010 roku**, bo wtedy auta startują z paliwem na cały wyścig (są
cięższe na starcie). Ten sam wzór widać na **obu torach** — czyli to naprawdę wina ciężaru paliwa,
a nie przypadek na jednym torze.

![Wpływ paliwa — Silverstone](pres/F1Pandas/images/f1_fuel_impact_Silverstone_Circuit.png)
![Wpływ paliwa — Hungaroring](pres/F1Pandas/images/f1_fuel_impact_Hungaroring.png)

### 3.2. Czy 3 najlepsze zespoły zgarniają wszystko

**Co robimy.** Dla każdego sezonu liczymy, jaki **procent** wszystkich punktów zdobyły 3 najlepsze
zespoły. Liczymy procent (a nie samą liczbę punktów), bo zasady punktowania zmieniały się przez lata —
procent jest sprawiedliwszy do porównań.

**Wynik.** Co sezon 3 czołowe zespoły biorą około 70–80% punktów. Najbardziej dominowały w latach 50.
(aż ~98% w 1952). Potem walka się wyrównała (~51% na początku lat 80.), a teraz znów dominacja rośnie
(np. Mercedes 2014–2020, później Red Bull). **Wniosek: dominacja kręci się w cyklach — raz większa,
raz mniejsza.**

![Dominacja Top-3](pres/F1Pandas/images/f1_top3_dominance_main.png)

### 3.3. Który zespół zdobywa najwięcej punktów na sezon

**Co robimy.** Liczymy: suma wszystkich punktów zespołu ÷ liczba sezonów, w których jeździł
(minimum 3 sezony).

**Wynik — uwaga, tu można się pomylić.** Najlepiej wypadają Mercedes (~440) i Red Bull (~370). Ale ta
miara **faworyzuje nowsze zespoły**, bo dziś za jeden wyścig rozdaje się dużo więcej punktów niż kiedyś
(dawniej zwycięstwo = ~8 pkt, dziś = 25 pkt + punkty dla wielu kierowców). Dlatego stare, zasłużone
zespoły jak Ferrari (~150) wypadają gorzej, choć mają więcej tytułów. **Ta liczba mówi o tempie
zbierania punktów dziś — a nie o tym, kto jest najlepszy w historii.**

![Konstruktorzy — punkty na sezon](pres/F1Pandas/images/f1_top_constructors_by_points_per_season.png)

### 3.4. Czy auta dziś częściej dojeżdżają do mety (niezawodność)

**Co robimy.** DNF to skrót od „nie ukończył wyścigu" (ang. *Did Not Finish*). Dla każdego sezonu
liczymy: ile aut nie dojechało ÷ ile wystartowało. To pokazuje, jak często auta się psuły.

**Wynik.** Kiedyś było bardzo źle — pod koniec lat 60. nawet ~47% aut nie kończyło wyścigu (prawie
co drugie!). Dziś to tylko **10–15%**. Dojechanie do mety zmieniło się z loterii w normę. **To
najmocniejszy dowód, że technika poszła do przodu.** Dwa zastrzeżenia: (1) do DNF liczą się też wypadki
i kolizje, nie tylko awarie; (2) mały wzrost na końcu wykresu to niedokończony sezon 2026, a nie realne
pogorszenie.

![Trend DNF](pres/F1Pandas/images/f1_dnf_reliability.png)

### 3.5. Czy auta z roku na rok jeżdżą szybciej

**Co robimy.** Z każdego wyścigu bierzemy jedno **najszybsze okrążenie**, a potem liczymy średnią dla
każdego roku. Sprawdzamy, czy czasy z roku na rok spadają (szybciej = lepiej).

**Wynik.** Auta **nie** robiły się szybsze równo z roku na rok. Najszybciej było na początku lat 2000.
Potem przyszły nowe przepisy w 2009 (mniej aerodynamiki) i 2014 (cięższe silniki hybrydowe) i auta
**zwolniły**, a dopiero później znów nadrabiały. Czyli postęp nie jest stały — **co kilka lat przepisy
„resetują" rozwój.** Uwaga: różne tory mają różną długość, więc patrzymy na ogólny trend, a nie na
pojedyncze lata.

![Najszybsze okrążenia](pres/F1Pandas/images/f1_moores_law_fastest_lap_from_laptimes.png)

### 3.6. Kto wygrywa z kolegą z tego samego zespołu

**Co robimy.** Dwóch kierowców z tego samego zespołu jeździ takim samym autem — więc kto wyżej, ten
lepszy danego dnia. Liczymy, jaki procent takich „pojedynków" wygrał każdy kierowca (bierzemy kierowców
z min. 50 wspólnymi wyścigami).

**Wynik.** Najwyżej jest Verstappen (~83%), nisko Hamilton (~58%). Ale ranking trzeba czytać ostrożnie:
łatwiej wygrywać, gdy ma się słabszego partnera, a trudniej, gdy partner jest mocny. Dodatkowo liczymy
tylko wyścigi, które obaj ukończyli (awarie pomijamy), i nie bierzemy pod uwagę, jak silny był rywal.
**Ta liczba mówi „jak często bił swojego kolegę" — a nie kto jest najlepszy w ogóle.**

![Pojedynki w zespole](pres/F1Pandas/images/f1_teammate_duels.png)

### 3.7. Wykres zbiorczy

Najważniejsze wyniki (niezawodność, szybkość, dominacja, wpływ paliwa) zebraliśmy na jednym wspólnym
obrazku.

![Panel zbiorczy](pres/F1Pandas/images/f1_analysis_summary.png)

---

## 4. Podsumowanie i wnioski

Czego nauczyły nas dane:

1. **Auta są dużo bardziej niezawodne niż kiedyś.** Odsetek aut, które nie kończą wyścigu, spadł
   3–4 razy od lat 60. To najwyraźniejszy dowód postępu technicznego.

2. **Pełny bak realnie spowalnia auto.** Widać to na różnych torach i w różnych latach — a zakaz
   tankowania z 2010 roku jest dobrze widoczny w danych.

3. **Dominacja zespołów chodzi w cyklach.** Czasem walka jest wyrównana, czasem rządzi jeden zespół.
   Teraz dominacja znów rośnie.

4. **Auta nie jeżdżą szybciej co roku.** Nowe przepisy dwa razy (2009 i 2014) cofnęły rozwój. Postęp
   nie jest równy.

5. **Niektóre „rankingi talentu" potrafią mylić.** Trzeba uważać, jak liczy się dane wskaźniki —
   bo łatwo wyciągnąć z nich błędny wniosek.

### Ograniczenia i co dalej

- Miara „punkty na sezon" faworyzuje nowsze zespoły — można ją poprawić, licząc punkty sprawiedliwiej
  w obrębie każdego sezonu albo patrząc na liczbę tytułów.
- Analiza najszybszych okrążeń miesza tory o różnej długości — można to poprawić, uwzględniając
  długość toru.
- Projekt tylko **opisuje** przeszłość. Następny krok to **przewidywanie sezonu 2026** — czyli model
  uczenia maszynowego (np. Random Forest), który na podstawie lat ze zmianą przepisów (2014, 2022)
  spróbuje zgadnąć, jak zmieni się układ sił. Dane są już przygotowane, ale model jeszcze nie powstał.

---

## 5. Załączniki

| Załącznik | Gdzie | Co to |
|---|---|---|
| **Kod źródłowy (notebook)** | [`Project1.ipynb`](Project1.ipynb) | Cały kod: wczytanie danych → pytania SQL → analiza → wykresy |
| **Baza danych** | `f1.db` | Lokalna baza DuckDB z danymi F1 (1950–2026) |
| **Lista zależności** | [`pyproject.toml`](pyproject.toml) | Spis bibliotek potrzebnych do uruchomienia |
| **Prezentacja (slajdy)** | [`pres/F1Pandas/slides.md`](pres/F1Pandas/slides.md) | Prezentacja w formacie Slidev (Markdown) |
| **Prezentacja (PDF)** | [`pres/F1Pandas/slides-export.pdf`](pres/F1Pandas/slides-export.pdf) | Prezentacja jako PDF |
| **Prezentacja (PPTX)** | [`pres/F1Pandas/slides-export.pptx`](pres/F1Pandas/slides-export.pptx) | Prezentacja jako PowerPoint |
| **Wykresy** | `pres/F1Pandas/images/` | Gotowe obrazki (PNG) |

### Użyte narzędzia

- **Język:** Python (wersja 3.13 lub nowsza)
- **Baza danych:** DuckDB (pytania w języku SQL)
- **Liczenie na danych:** pandas, numpy
- **Wykresy:** matplotlib, seaborn
- **Pobieranie danych:** kagglehub (zbiór F1 z Kaggle)
- **Prezentacja:** Slidev

### Jak uruchomić

```bash
uv sync                       # zainstaluj potrzebne biblioteki
uv run jupyter notebook       # otwórz notebook Project1.ipynb
```