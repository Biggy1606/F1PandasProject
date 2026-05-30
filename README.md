# F1 Data Science: Technological Evolution & 2026 Prediction

Analiza ewolucji technicznej Formuły 1 (1950–2026) z wykorzystaniem danych wyścigowych z Kaggle i bazy DuckDB.

## Analizy

1. **Wpływ paliwa na czas okrążenia** – porównanie progresji lap times w erze z refuelingiem (1994–2009) vs bez (2010+) na Silverstone i Hungaroring
2. **Efficiency Gap** – dominacja punktowa Top 3 konstruktorów w każdym sezonie
3. **Niezawodność technologii** – trend DNF rate od 1950 do 2026 (48% → 21%)
4. **"Prawo Moore'a" w F1** – ewolucja średniego najszybszego okrążenia sezonowego (1982–2026)
5. **Najlepsi konstruktorzy** – ranking historyczny wg średniej liczby punktów na sezon

## Stack

`DuckDB` · `Pandas` · `Matplotlib` · `Seaborn` · `KaggleHub`

Dane: [`jtrotman/formula-1-race-data`](https://www.kaggle.com/datasets/jtrotman/formula-1-race-data) + [`jtrotman/formula-1-race-events`](https://www.kaggle.com/datasets/jtrotman/formula-1-race-events)

## Pliki

| Plik | Opis |
|------|------|
| [`Project1.ipynb`](Project1.ipynb) | Główny notebook z analizami |
| [`REPORT.md`](REPORT.md) | Pełny raport z wnioskami |
| [`pres/F1Pandas/slides-export.pdf`](pres/F1Pandas/slides-export.pdf) | Prezentacja (PDF) |
| [`pres/F1Pandas/slides-export.pptx`](pres/F1Pandas/slides-export.pptx) | Prezentacja (PowerPoint) |

## Uruchomienie

```bash
uv sync
jupyter notebook Project1.ipynb
```
