# F1 Data Science: Technological Evolution & 2026 Prediction

## 📋 Opis Projektu
Projekt skupia się na analizie wpływu postępu technicznego w Formule 1 na osiągi, niezawodność i strukturę rywalizacji, kończąc się modelem predykcyjnym dla nowej ery regulacyjnej w 2026 roku.

## 🛠️ Kluczowe Etapy Analizy

### 1. Analiza Predykcyjna 2026: „Model Przejścia Regulacyjnego”
- **Metoda**: Machine Learning (Random Forest / XGBoost).
- **Cel**: Wykorzystanie danych z lat 'resetu' (2014, 2022) do prognozowania zmian w układzie sił.

### 2. Strategiczne „Under-fueling” i Pit-Stops (Big Data)
- **Metoda**: Apache Spark (`lap_times.csv`).
- **Cel**: Przetwarzanie milionów rekordów w celu zbadania korelacji między masą paliwa a tempem w różnych erach.

### 3. „Efficiency Gap” – Dominacja technologiczna zespołów
- **Metoda**: Analiza wskaźnika *Supertimes* (%).
- **Cel**: Statystyczny pomiar 'rozwarstwienia' stawki i identyfikacja okresów największej przewagi technicznej.

### 4. Analiza „Niezawodności Technologii” (Dumping of DNFs)
- **Metoda**: Eksploracyjna analiza danych (EDA) tabeli `status.csv`.
- **Cel**: Ilościowy dowód na postęp inżynieryjny poprzez spadek awaryjności podzespołów mechanicznych.

### 5. „Prawo Moore'a” w F1: Ewolucja tempa kwalifikacyjnego
- **Metoda**: Analiza szeregów czasowych (Time Series).
- **Cel**: Badanie przyrostu prędkości na torach historycznych (Monza, Monaco) jako miara tempa innowacji.

## 📈 Wymagania Techniczne
- **Pre-processing**: Czyszczenie braków danych (`\N`), mapowanie ID, normalizacja czasu.
- **Wizualizacja**: Dashboardy Seaborn/Matplotlib.
- **Statystyka**: Testy istotności (ANOVA/T-test) dla różnic między erami silnikowymi.
- **Big Data**: Przetwarzanie rozproszone w środowisku Spark.
