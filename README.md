# Viborg Caravan Center — GoG XML Feed

Automatisk synkronisering af varebeholdning fra Framer CMS til [GulogGratis (GoG)](https://www.guloggratis.dk).

## Hvordan det virker

```
Framer CMS (viborg-caravancenter.dk/data-eksport)
        ↓  scrapes hver time via GitHub Actions
   feed.py
        ↓  genererer XML
   public/feed.xml  →  publiceres via GitHub Pages
        ↑
   GulogGratis henter denne fil automatisk
```

1. GitHub Actions kører `feed.py` **hver time**
2. Scriptet åbner `viborg-caravancenter.dk/data-eksport` med en headless browser (Playwright)
3. Det henter produktdata + billeder og bygger en XML-fil i GoG's format
4. Filen gemmes som `public/feed.xml` og publiceres via GitHub Pages
5. GoG poller URL'en og opdaterer annoncerne automatisk

## Hvad GoG henter

GoG henter én fil: `public/feed.xml` — tilgængelig via GitHub Pages på det domæne der er konfigureret i `CNAME`.

## ⚠️ Kritisk: Framer-feltrækkefølge må aldrig ændres

Scriptet læser felter **positionelt** — ikke efter navn. Rækkefølgen i din Framer-stack skal altid være præcis:

| Position | Felt |
|---|---|
| 1 | Slug |
| 2 | Type |
| 3 | Model |
| 4 | År |
| 5 | Pris |
| 6 | Egenvægt |
| 7 | Totalvægt |
| 8 | Kategori |

Ændres rækkefølgen i Framer, går feedet i stykker uden fejlbesked.

## Fejlfinding

- **GitHub Actions-fanen** — se om det seneste job fejlede og hvad fejlen var
- **Framer-felter** — tjek at de 8 felter stadig er i den rigtige rækkefølge
- **`public/feed.xml`** — åbn filen direkte og tjek om data ser fornuftigt ud

## Teknisk stack

| Komponent | Teknologi |
|---|---|
| Scraping | Playwright + BeautifulSoup4 |
| XML-generering | lxml |
| Automatisering | GitHub Actions (hourly cron) |
| Hosting af feed | GitHub Pages |
