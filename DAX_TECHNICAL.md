# 📐 Dokumentacja Techniczna DAX

> Opis wszystkich miar DAX użytych w raporcie Power BI analizy rynku mieszkaniowego.

---

## 1. Normalizacja wskaźników

Wszystkie wskaźniki cząstkowe są normalizowane metodą **Z-score + funkcja sigmoid**, która zapewnia wartości w przedziale 0–1 bez ekstremalnego rozciągania skali (problem metody min-max).

### Wzór ogólny — wskaźnik rosnący (im wyższy, tym lepiej)

```dax
Idx_NazwaWskaznika =
VAR Val      = AVERAGE ( Fact_Socio_Economic[Indicator_Value] )
VAR MeanVal  = AVERAGEX ( ALL ( Dim_Region ), CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ) ) )
VAR StdDev   = SQRT ( AVERAGEX ( ALL ( Dim_Region ),
                   ( CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ) ) - MeanVal ) ^ 2 ) )
VAR ZScore   = DIVIDE ( Val - MeanVal, StdDev, 0 )
RETURN
    -- BEZ odwracania
    DIVIDE ( 1, 1 + EXP ( -ZScore ), BLANK () )
```

### Wzór ogólny — wskaźnik malejący (im niższy, tym lepiej)

```dax
Idx_NazwaWskaznika =
VAR Val      = AVERAGE ( Fact_Socio_Economic[Indicator_Value] )
VAR MeanVal  = AVERAGEX ( ALL ( Dim_Region ), CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ) ) )
VAR StdDev   = SQRT ( AVERAGEX ( ALL ( Dim_Region ),
                   ( CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ) ) - MeanVal ) ^ 2 ) )
VAR ZScore   = DIVIDE ( Val - MeanVal, StdDev, 0 )
RETURN
    -- Z odwracaniem (1 - ...)
    DIVIDE ( 1, 1 + EXP ( ZScore ), BLANK () )
```

---

## 2. Wskaźniki cząstkowe

### Idx_Bezrobocie (odwracany)
```dax
Idx_Bezrobocie =
VAR SelectedYear = SELECTEDVALUE ( Dim_Time[Year] )
VAR Val =
    CALCULATE (
        AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
        Dim_Time[Year] = SelectedYear,
        Dim_Indicators[Indicator_ID] = 1
    )
VAR MeanVal =
    AVERAGEX ( ALL ( Dim_Region ),
        CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
            Dim_Time[Year] = SelectedYear,
            Dim_Indicators[Indicator_ID] = 1 ) )
VAR StdDev =
    SQRT ( AVERAGEX ( ALL ( Dim_Region ),
        ( CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
            Dim_Time[Year] = SelectedYear,
            Dim_Indicators[Indicator_ID] = 1 ) - MeanVal ) ^ 2 ) )
VAR ZScore = DIVIDE ( Val - MeanVal, StdDev, 0 )
RETURN
    DIVIDE ( 1, 1 + EXP ( ZScore ), BLANK () )   -- odwrócony
```

### Idx_Wynagrodzenia (rosnący)
```dax
Idx_Wynagrodzenia =
VAR SelectedYear = SELECTEDVALUE ( Dim_Time[Year] )
VAR Val =
    CALCULATE (
        AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
        Dim_Time[Year] = SelectedYear,
        Dim_Indicators[Indicator_ID] = 2
    )
-- [analogiczna kalkulacja MeanVal i StdDev]
VAR ZScore = DIVIDE ( Val - MeanVal, StdDev, 0 )
RETURN
    DIVIDE ( 1, 1 + EXP ( -ZScore ), BLANK () )  -- NIE odwrócony
```

### Idx_Dostepnosc_Mieszkan (odwracany)
Relacja średniej ceny za m² do przeciętnego wynagrodzenia.
```dax
Idx_Dostepnosc_Mieszkan =
VAR SelectedYear = SELECTEDVALUE ( Dim_Time[Year] )
VAR AvgPrice =
    CALCULATE (
        AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] ),
        Dim_Time[Year] = SelectedYear
    )
VAR AvgWage =
    CALCULATE (
        AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
        Dim_Time[Year] = SelectedYear,
        Dim_Indicators[Indicator_ID] = 2
    )
VAR Ratio = DIVIDE ( AvgPrice, AvgWage, BLANK () )
VAR MeanRatio =
    AVERAGEX ( ALL ( Dim_Region ),
        DIVIDE (
            CALCULATE ( AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] ),
                Dim_Time[Year] = SelectedYear ),
            CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
                Dim_Time[Year] = SelectedYear,
                Dim_Indicators[Indicator_ID] = 2 )
        ) )
VAR StdDev =
    SQRT ( AVERAGEX ( ALL ( Dim_Region ),
        ( DIVIDE (
            CALCULATE ( AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] ),
                Dim_Time[Year] = SelectedYear ),
            CALCULATE ( AVERAGE ( Fact_Socio_Economic[Indicator_Value] ),
                Dim_Time[Year] = SelectedYear,
                Dim_Indicators[Indicator_ID] = 2 )
        ) - MeanRatio ) ^ 2 ) )
VAR ZScore = DIVIDE ( Ratio - MeanRatio, StdDev, 0 )
RETURN
    DIVIDE ( 1, 1 + EXP ( ZScore ), BLANK () )   -- odwrócony
```

### Idx_AvgPrice (odwracany)
```dax
Idx_AvgPrice =
VAR Val =
    AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] )
VAR MeanVal =
    AVERAGEX ( ALL ( Dim_Region ),
        CALCULATE ( AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] ) ) )
VAR StdDev =
    SQRT ( AVERAGEX ( ALL ( Dim_Region ),
        ( CALCULATE ( AVERAGE ( Fact_Housing_Market[Avg_Price_Per_m2_Transaction] ) ) - MeanVal ) ^ 2 ) )
VAR ZScore = DIVIDE ( Val - MeanVal, StdDev, 0 )
RETURN
    DIVIDE ( 1, 1 + EXP ( ZScore ), BLANK () )   -- odwrócony: niższa cena = lepiej
```

---

## 3. Ogólny Wskaźnik Socjo-Ekonomiczny (OWS)

```dax
Ogolny_Wskaznik_Socjoekonomiczny =
VAR W_Bezrobocie         = 0.08
VAR W_Wynagrodzenia      = 0.18
VAR W_Dostepnosc         = 0.20
VAR W_Crime              = 0.04
VAR W_Emission           = 0.02
VAR W_GDP                = 0.15
VAR W_Konsumpcja         = 0.04
VAR W_Lozka              = 0.02
VAR W_Migracja           = 0.10
VAR W_Populacja          = 0.10
VAR W_Roads              = 0.02
VAR W_Przyrost_Naturalny = 0.05
-- SUMA WAG = 1.00

VAR Idx1  = [Idx_Bezrobocie]
VAR Idx2  = [Idx_Wynagrodzenia]
VAR Idx3  = [Idx_Dostepnosc_Mieszkan]
VAR Idx4  = [Idx_Crime]
VAR Idx5  = [Idx_Emission]
VAR Idx6  = [Idx_GDP]
VAR Idx7  = [Idx_Konsumpcja]
VAR Idx8  = [Idx_Lozka]
VAR Idx9  = [Idx_Migracja]
VAR Idx10 = [Idx_Populacja]
VAR Idx11 = [Idx_Roads]
VAR Idx12 = [Idx_Przyrost_Naturalny]

RETURN
    W_Bezrobocie         * Idx1
        + W_Wynagrodzenia      * Idx2
        + W_Dostepnosc         * Idx3
        + W_Crime              * Idx4
        + W_Emission           * Idx5
        + W_GDP                * Idx6
        + W_Konsumpcja         * Idx7
        + W_Lozka              * Idx8
        + W_Migracja           * Idx9
        + W_Populacja          * Idx10
        + W_Roads              * Idx11
        + W_Przyrost_Naturalny * Idx12
```

### Uzasadnienie wag

| Wskaźnik | Waga | Uzasadnienie |
|---|---|---|
| Dostępność mieszkaniowa | 0.20 | Najsilniejszy bezpośredni wpływ na rynek |
| Wynagrodzenia | 0.18 | Siła nabywcza napędza popyt |
| PKB per capita | 0.15 | Zamożność regionu |
| Migracja | 0.10 | Napływ ludności winduje ceny |
| Populacja | 0.10 | Wielkość rynku |
| Bezrobocie | 0.08 | Hamuje popyt |
| Przyrost naturalny | 0.05 | Długoterminowy popyt |
| Crime / Konsumpcja | 0.04 | Jakość życia |
| Emisja / Łóżka / Drogi | 0.02 | Marginalny wpływ |

---

## 4. Miary porównawcze

### Regresja liniowa OWS → Cena

```dax
Slope_OWS_Cena =
VAR MeanX = AVERAGEX ( ALL ( Dim_Region ), [Ogolny_Wskaznik_Socjoekonomiczny] )
VAR MeanY = AVERAGEX ( ALL ( Dim_Region ), [Avg_Cena_m2] )
VAR Numerator =
    SUMX ( ALL ( Dim_Region ),
        ( [Ogolny_Wskaznik_Socjoekonomiczny] - MeanX ) * ( [Avg_Cena_m2] - MeanY ) )
VAR Denominator =
    SUMX ( ALL ( Dim_Region ),
        ( [Ogolny_Wskaznik_Socjoekonomiczny] - MeanX ) ^ 2 )
RETURN
    DIVIDE ( Numerator, Denominator, BLANK () )
```

```dax
Intercept_OWS_Cena =
VAR MeanX = AVERAGEX ( ALL ( Dim_Region ), [Ogolny_Wskaznik_Socjoekonomiczny] )
VAR MeanY = AVERAGEX ( ALL ( Dim_Region ), [Avg_Cena_m2] )
RETURN
    MeanY - [Slope_OWS_Cena] * MeanX
```

```dax
Predicted_Cena_m2 =
    [Intercept_OWS_Cena] + [Slope_OWS_Cena] * [Ogolny_Wskaznik_Socjoekonomiczny]
```

### Odchylenie od modelu

```dax
Odchylenie_od_Modelu =
    [Avg_Cena_m2] - [Predicted_Cena_m2]

Odchylenie_Procent =
    DIVIDE ( [Avg_Cena_m2] - [Predicted_Cena_m2], [Predicted_Cena_m2], BLANK () ) * 100
```

> **Interpretacja:** wartość ujemna oznacza że miasto jest **tańsze** niż wskazują czynniki socjo-ekonomiczne — to najlepsza oferta dla kupującego.

---

## 5. Kategoryzacja i ranking

```dax
Kategoria_OWS =
VAR Score = [Ogolny_Wskaznik_Socjoekonomiczny]
RETURN
    SWITCH ( TRUE (),
        ISBLANK ( Score ), "Brak danych",
        Score >= 0.75,     "Bardzo dobry",
        Score >= 0.50,     "Dobry",
        Score >= 0.25,     "Średni",
                           "Słaby"
    )
```

```dax
Ranking_OWS =
RANKX (
    ALL ( Dim_Region[City_Name] ),
    [Ogolny_Wskaznik_Socjoekonomiczny],
    , DESC, DENSE
)
```
