🏠 Analiza Rynku Mieszkaniowego z Wskaźnikami Socjo-Ekonomicznymi
> Raport Power BI badający zależności między cenami mieszkań a czynnikami socjo-ekonomicznymi w polskich miastach wojewódzkich.
---
📌 Opis projektu
Projekt analizuje rynek nieruchomości w 16 polskich miastach wojewódzkich, łącząc dane o cenach mieszkań z szeregiem wskaźników socjo-ekonomicznych. Głównym celem jest odpowiedź na pytanie:
> \*\*Które miasto oferuje najlepsze ceny mieszkań w relacji do warunków socjo-ekonomicznych?\*\*
Raport umożliwia interaktywną eksplorację danych w podziale na lata i typy rynku (pierwotny / wtórny).
---
📊 Zakres analizy
Rynek mieszkaniowy
Średnia cena ofertowa za m²
Średnia cena transakcyjna za m²
Mediana ceny za m²
Zmiana kwartalna (QoQ)
Spread oferta–transakcja
Wskaźniki socjo-ekonomiczne
Wskaźnik	Kierunek wpływu
Stopa bezrobocia	↓ niższe = lepiej
Przeciętne wynagrodzenie	↑ wyższe = lepiej
PKB per capita	↑ wyższe = lepiej
Dostępność mieszkaniowa (cena/płaca)	↓ niższe = lepiej
Przestępczość	↓ niższe = lepiej
Emisja zanieczyszczeń	↓ niższe = lepiej
Konsumpcja	↑ wyższe = lepiej
Liczba łóżek szpitalnych	↑ wyższe = lepiej
Migracja	↑ wyższe = lepiej
Populacja	↑ wyższe = lepiej
Infrastruktura drogowa	↑ wyższe = lepiej
Przyrost naturalny	↑ wyższe = lepiej
---
🗂️ Struktura modelu danych
```
Dim\_Region          -- miasta i województwa
Dim\_Time            -- kalendarz (rok, kwartał)
Dim\_Indicators      -- słownik wskaźników
Dim\_Market\_Type     -- typ rynku (pierwotny/wtórny)
Fact\_Housing\_Market -- ceny mieszkań
Fact\_Socio\_Economic -- wartości wskaźników socjo-ekonomicznych
```
---
🔢 Ogólny Wskaźnik Socjo-Ekonomiczny (OWS)
Syntetyczna miara agregująca wszystkie wskaźniki w jedną wartość z przedziału 0–1, gdzie wyższy wynik oznacza lepsze warunki socjo-ekonomiczne. Szczegóły obliczania w pliku DAX_TECHNICAL.md.
---
📁 Zawartość repozytorium
```
📦 projekt
 ┣ 📊 BI\_działający.pbix       # główny plik raportu Power BI
 ┣ 📄 README.md                # ten plik
 ┗ 📄 DAX\_TECHNICAL.md         # dokumentacja techniczna miar DAX
```
---
🛠️ Technologie
Power BI Desktop
DAX — język miar i kalkulacji
Power Query (M) — transformacja danych
---
📈 Kluczowe wnioski
Poznań oferuje najlepszą relację ceny do wskaźników socjo-ekonomicznych — wysokie OWS przy relatywnie niskich cenach mieszkań
Warszawa ma najwyższy OWS, ale również najwyższe ceny — relacja wartość/cena jest niekorzystna
Zielona Góra ma najniższe ceny, ale również najniższe wskaźniki socjo-ekonomiczne
