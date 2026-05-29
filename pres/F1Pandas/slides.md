---
theme: seriph
title: Analiza Danych Formuły 1
info: |
  ## F1 Data Science — Ewolucja Technologiczna
  Projekt szkolny. Zbudowany na podstawie zbioru danych Kaggle/Ergast F1 (1950–2026) w Pythonie + DuckDB.
layout: cover
class: text-center
transition: slide-left
mdc: true
---

# Analiza Danych Formuły 1
## Ewolucja technologiczna na przestrzeni 70+ lat wyścigów

<div class="pt-10 opacity-80">
  Igor Barcik · Łukasz Domagała · Informatyka · 2026
</div>
<div class="pt-2 opacity-50 text-sm">
  Dane: Kaggle (Ergast) F1 dataset · Python · DuckDB · matplotlib
</div>

<!--
Rozpocznij od pytania przewodniego: jak innowacje techniczne zmieniły wydajność, niezawodność i konkurencję w F1 od 1950 roku? Powiedz, że cały proces należy do Ciebie: CSV-y załadowane do DuckDB, zapytania w SQL, wykresy w matplotlib. Sześć analiz, jeden zbiór danych.
-->

---
layout: default
---

# Agenda

<div class="text-lg leading-relaxed pt-4">

1. Zbiór danych i metoda
2. Dominacja zespołów w czasie
3. Najlepsi konstruktorzy (punkty na sezon)
4. Pojedynki kierowców z tego samego zespołu
5. Niezawodność — historia DNF
6. Tempo wyścigowe — sprawdzamy "prawo Moore'a"
7. Wpływ paliwa na czas okrążenia
8. Kluczowe wnioski i ograniczenia

</div>

<!--
20-sekundowy plan. Powiedz, że trzy najsilniejsze wyniki to niezawodność, efekt ciężaru paliwa i cykl dominacji — oraz że będziesz szczery w kwestii ograniczeń metryk.
-->

---
layout: section
---

# 1 · Zbiór Danych i Metoda

---
layout: default
---

# Przegląd Analizy

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_analysis_summary.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Źródło:</b> Kaggle/Ergast F1 data (1950–2026) w DuckDB. Cztery główne sygnały: niezawodność, tempo, dominacja top-3 oraz zmiana zakazu tankowania w 2010 roku.</p>
</div>

<!--
Metoda: CSV-y -> tabele DuckDB (races, results, lap_times, drivers, constructors, status), zapytania w SQL, wykresy w matplotlib. Zakres danych różni się według metryki: wykresy oparte na wynikach obejmują 1950–2026; wykresy czasów okrążeń zaczynają się od 1994; najszybsze okrążenia od ~1982 (wtedy zaczęto rejestrować czasy okrążeń). Pomarańczowy panel czasów okrążeń to tylko Silverstone; zielony panel najszybszych okrążeń miesza wszystkie tory — zaznacz oba, aby nikt nie przeczytał ich błędnie.
-->

---
layout: section
---

# 2 · Dominacja i Wydajność

---
layout: default
---

# Dominacja Top-3 w Czasie

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_top3_dominance_main.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Wniosek:</b> Top-3 zespoły zdobywają ~70–80% wszystkich punktów każdego sezonu — koncentracja osiągnęła szczyt w latach 50. (~98% w 1952), rozluźniła się na początku lat 80. (~51% minimum) i ponownie wzrasta dziś.</p>
</div>

<!--
To STOSUNEK w każdym roku (punkty top-3 / wszystkie punkty), więc NIE jest zniekształcony przez inflację punktów — to jego siła. Przerywana linia to wielomian 2. stopnia: płytka litera U. Opowieść: wczesne stawki były małe i niezrównoważone, pole poszerzyło się w połowie historii, a era hybrydowa ponownie skoncentrowała sukces. Dobre miejsce, aby wspomnieć o Mercedesie 2014–2020 i Red Bullu ostatnimi czasy.
-->

---
layout: default
---

# Najlepsi Konstruktorzy — Punkty na Sezon

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_top_constructors_by_points_per_season.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Wniosek — czytaj uważnie:</b> Mercedes (~440) i Red Bull (~370) prowadzą w średniej liczbie punktów/sezon, ale ta metryka nagradza nowoczesną erę wysokich punktów, więc faworyzuje nowsze zespoły nad Ferrari (~150) i innymi długimi nazwiskami w historii.</p>
</div>

<!--
To najważniejsza uwaga w całej prezentacji. Metryka = całkowite punkty / sezony zaliczone (>=3 sezony). System punktowy mocno się rozrosnął: wygrana w latach 50. to ~8 pkt z nielicznymi punktującymi; dziś wygrana to 25 pkt z top 10 punktujących plus punkty za najszybsze okrążenie/pościg. Więc zespoły, które ścigały się tylko ostatnio (np. "Lotus F1", zespół Enstone 2012–2015 z ~175) plasują się nad Ferrari, które jest ciągnięte w dół przez dekady sezonów ery niskich punktów. Bądź jasny: to klasyfikuje średnią punktową na sezon w nowoczesnych kategoriach, NIE całkowitą wielkość ani tytuły. Jeśli zapytają jak to naprawić: normalizuj punkty w każdym sezonie lub klasyfikuj według mistrzostw/współczynnika wygranych.
-->

---
layout: section
---

# 3 · Pojedynki Kierowców

---
layout: default
---

# Pojedynki w Zespole

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_teammate_duels.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Wniosek:</b> Verstappen prowadzi w pojedynkach tym samym samochodem (~83%); Hamilton jest nisko (~58%) — ale metryka liczy tylko ukończone wyścigi i nie uwzględnia siły poszczególnych kolegów z zespołu.</p>
</div>

<!--
Metoda: dla każdego wyścigu, w którym zespół miał dokładnie dwóch sklasyfikowanych kierowców, lepsza pozycja końcowa wygrywa pojedynek; % zwycięstw we wszystkich takich wyścigach; kierowcy z >=50 wspólnymi wyścigami, top 20. Dlaczego ranking jest nierówny: nagradza kierowców, którzy mieli SŁABSZYCH kolegów z zespołu (Verstappen vs Gasly/Albon/Perez) i karze tych, którzy mieli silnych (Hamilton vs Alonso, Button, Rosberg, Russell). Więc to mierzy "pokonaj konkretnego kolegę z zespołu", nie absolutne umiejętności. Ponadto: liczy tylko wyścigi, w których obaj ukończyli, więc DNF/awarie są wykluczone. Świetny punkt szczerości na Q&A — zauważ, że siła przeciwnika jest nieważona.
-->

---
layout: section
---

# 4 · Niezawodność

---
layout: default
---

# Trendy DNF i Niezawodności

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_dnf_reliability.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Wniosek:</b> Sezonowy wskaźnik DNF spadł z ~47% szczytu (koniec lat 60.) do około 10–15% dziś — ukończenie wyścigu zmieniło się z rzutu monetą w normę. To najjaśniejsza historia postępu w danych.</p>
</div>

<!--
Metoda: DNF = status wyniku nie na "ukończony/sklasyfikowany" białej liście; wskaźnik = DNF / całkowite starty na sezon; trend wielomianu 2. stopnia (parabola: rośnie, potem spada). Dwa dobrowolne zastrzeżenia: (1) "DNF" tu obejmuje wypadki i kolizje, nie tylko awarie mechaniczne, więc nieco przesadza czystą niezawodność inżynieryjną; (2) mały wzrost na samym końcu to 2026 — niepełny, trwający sezon z niewieloma wyścigami, nie prawdziwa regresja niezawodności. Dolne słupki pokazują absolutne DNF vs całkowite starty, więc wskaźnik nie jest tylko artefaktem rozmiaru stawki.
-->

---
layout: section
---

# 5 · Ewolucja Wydajności

---
layout: default
---

# "Prawo Moore'a" Najszybszych Okrążeń?

<div class="flex flex-col items-center gap-3 mt-3">
  <img src="./images/f1_moores_law_fastest_lap_from_laptimes.png" class="max-h-[20rem] max-w-[90%] object-contain rounded-lg shadow-lg" />
  <p class="text-sm opacity-80 max-w-4xl text-center"><b>Wniosek:</b> Tempo <b>nie</b> poprawiało się liniowo — najszybsze okrążenia osiągnęły szczyt na początku lat 2000, cofnęły się po zmianach regulaminowych w 2009 i 2014, potem odzyskały. "Prawo" działa tylko w połowie.</p>
</div>

<!--
Oś Y jest ODWÓCONA, więc szybsze (mniej sekund) pojawia się wyżej. Metoda: na wyścig bierzemy jedno najszybsze okrążenie, średnia ze wszystkich wyścigów w roku, ~1982–2026. Dwa szczere punkty: (1) metryka miesza tory różnej długości, więc roczne zygzaki częściowo odzwierciedlają, które tory były w kalendarzu, nie czyste tempo — czytaj trend, nie wierzchołki; (2) spadki pokrywają się z resetami regulaminu (2009 cięcia aero + zakaz tankowania, 2014 cięższe hybrydy V6) zanim rozwój odzyskał tempo. Więc w przeciwieństwie do tranzystorów, tempo F1 jest wielokrotnie resetowane przez przepisy.
-->

---
layout: section
---

# 6 · Strategia i Warunki

---
layout: two-cols-header
---

# Wpływ Ładunku Paliwa na Czas Okrążenia

W miarę spalania paliwa samochód staje się lżejszy i szybszy — ten sam efekt widać na obu torach, i jest większy w cięższej erze bez tankowania (2010+).

::left::

<div class="flex flex-col items-center gap-2">
  <img src="./images/f1_fuel_impact_Silverstone_Circuit.png" class="max-h-[18rem] max-w-full object-contain rounded shadow" />
  <p class="text-sm opacity-70">Silverstone (najczęściej ścigany tor)</p>
</div>

::right::

<div class="flex flex-col items-center gap-2">
  <img src="./images/f1_fuel_impact_Hungaroring.png" class="max-h-[18rem] max-w-full object-contain rounded shadow" />
  <p class="text-sm opacity-70">Hungaroring (2. historyczny tor)</p>
</div>

<!--
Metoda: progresja czasów okrążeń wewnątrz wyścigu, era tankowania (1994–2009, czerwona) vs bez tankowania (2010+, niebieska); usunięcie wartości odstających IQR; trendy wielomianu 2. stopnia; tory auto-wybierane według liczby wyścigów. Czytaj to w trzech krokach: (1) wewnątrz wyścigu, czasy okrążeń spadają w miarę opróżniania zbiornika — lżejszy samochód, szybsze okrążenie; (2) era bez tankowania jest wyraźnie wolniejsza, ponieważ samochody startują z pełnym bakiem na cały wyścig; (3) powtarza się na OBU torach, więc to prawdziwy efekt ciężaru, nie przypadek jednego toru. Pionowa przerywana linia oznacza zakaz tankowania w 2010 roku.
-->

---
layout: section
---

# Kluczowe Wnioski

---
layout: default
---

# Co Mówią Nam Dane

<div class="text-lg leading-relaxed pt-2">

- 🔧 **Niezawodność to najjaśniejszy sukces** — sezonowy wskaźnik DNF spadł około 3–4× od lat 60.
- ⛽ **Ciężar paliwa ma mierzalny, powtarzalny koszt** — widoczny na różnych torach i w różnych erach
- 🏆 **Dominacja jest cykliczna, nie jednokierunkowa** — udział top-3 spadł w połowie historii, teraz znowu rośnie
- ⚖️ **"Najlepszy vs kolega z zespołu" zależy od metryki** — tylko ukończenia, siła przeciwnika nieważona
- 📈 **Czyste tempo jest niemonotoniczne** — regulacje zresetowały krzywą w 2009 i 2014 roku

</div>

<div class="pt-5 opacity-70 text-sm">
<b>Ograniczenia:</b> punkty na sezon faworyzują nowoczesną erę; najszybsze okrążenia mieszają tory. To analiza opisowa — predykcyjny model na 2026 rok to zamierzony kolejny krok.
</div>

<!--
Postaw mocno trzy główne: niezawodność, ciężar paliwa, cykl dominacji. Następnie wyprzedź egzaminatora, wymieniając własne ograniczenia — to pokazuje, że rozumiesz metryki, nie tylko jak je rysować. Wspomnij o predykcji na 2026 jako przyszłej pracy, ponieważ notebook przygotowuje metryki, ale jeszcze ich nie modeluje.
-->

---
layout: center
class: text-center
---

# Dziękujemy

Pytania?

<div class="pt-8 opacity-60 text-sm">
Igor Barcik · Łukasz Domagała · Informatyka · Dane: Kaggle / Ergast F1 dataset
</div>

<!--
Miej eksport PDF jako backup. Bądź gotowy na: "jak zdefiniowano DNF?", "dlaczego Hamilton jest nisko?", "dlaczego Mercedes pokonuje tutaj Ferrari?" — twoje notatki na każdym slajdzie pokrywają wszystkie trzy.
-->