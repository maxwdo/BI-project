# 🏠 Analiza Rynku Mieszkaniowego z Wskaźnikami Socjo-Ekonomicznymi

> Raport Power BI badający zależności między cenami mieszkań a czynnikami socjo-ekonomicznymi w polskich miastach wojewódzkich.

---

## 📌 Opis projektu

Projekt analizuje rynek nieruchomości w **16 polskich miastach wojewódzkich**, łącząc dane o cenach mieszkań z szeregiem wskaźników socjo-ekonomicznych. Głównym celem jest odpowiedź na pytanie:

> **Które miasto oferuje najlepsze ceny mieszkań w relacji do warunków socjo-ekonomicznych?**

Raport umożliwia interaktywną eksplorację danych w podziale na lata i typy rynku (pierwotny / wtórny).

---

## 📊 Zakres analizy

### Rynek mieszkaniowy
- Średnia cena ofertowa za m²
- Średnia cena transakcyjna za m²
- Mediana ceny za m²
- Zmiana kwartalna (QoQ)
- Spread oferta–transakcja

### Wskaźniki socjo-ekonomiczne
| Wskaźnik | Kierunek wpływu |
|---|---|
| Stopa bezrobocia | ↓ niższe = lepiej |
| Przeciętne wynagrodzenie | ↑ wyższe = lepiej |
| PKB per capita | ↑ wyższe = lepiej |
| Dostępność mieszkaniowa (cena/płaca) | ↓ niższe = lepiej |
| Przestępczość | ↓ niższe = lepiej |
| Emisja zanieczyszczeń | ↓ niższe = lepiej |
| Konsumpcja | ↑ wyższe = lepiej |
| Liczba łóżek szpitalnych | ↑ wyższe = lepiej |
| Migracja | ↑ wyższe = lepiej |
| Populacja | ↑ wyższe = lepiej |
| Infrastruktura drogowa | ↑ wyższe = lepiej |
| Przyrost naturalny | ↑ wyższe = lepiej |

---

## 🗂️ Struktura modelu danych

```
Dim_Region          -- miasta i województwa
Dim_Time            -- kalendarz (rok, kwartał)
Dim_Indicators      -- słownik wskaźników
Dim_Market_Type     -- typ rynku (pierwotny/wtórny)
Fact_Housing_Market -- ceny mieszkań
Fact_Socio_Economic -- wartości wskaźników socjo-ekonomicznych
```

---

## 🔢 Ogólny Wskaźnik Socjo-Ekonomiczny (OWS)

Syntetyczna miara agregująca wszystkie wskaźniki w jedną wartość z przedziału **0–1**, gdzie wyższy wynik oznacza lepsze warunki socjo-ekonomiczne. Szczegóły obliczania w pliku [DAX_TECHNICAL.md](./DAX_TECHNICAL.md).

---

## 📁 Zawartość repozytorium

```
📦 projekt
 ┣ 📊 BI_działający.pbix       # główny plik raportu Power BI
 ┣ 📄 README.md                # ten plik
 ┗ 📄 DAX_TECHNICAL.md         # dokumentacja techniczna miar DAX
```

---

## 🚀 Jak uruchomić

1. Pobierz plik `BI_działający.pbix`
2. Otwórz w **Power BI Desktop** (wersja ≥ March 2024)
3. Odśwież dane jeśli masz dostęp do źródeł
4. Użyj slicerów **Rok** i **Typ rynku** do filtrowania

---

## 🛠️ Technologie

- **Power BI Desktop**
- **DAX** — język miar i kalkulacji
- **Power Query (M)** — transformacja danych

---

## 📈 Kluczowe wnioski

- **Poznań** oferuje najlepszą relację ceny do wskaźników socjo-ekonomicznych — wysokie OWS przy relatywnie niskich cenach mieszkań
- **Warszawa** ma najwyższy OWS, ale również najwyższe ceny — relacja wartość/cena jest niekorzystna
- **Zielona Góra** ma najniższe ceny, ale również najniższe wskaźniki socjo-ekonomiczne
