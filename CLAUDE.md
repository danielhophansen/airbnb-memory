# Airbnb — CLAUDE.md

## Identity

Denne workstasjonen håndterer alt knyttet til Daniels Airbnb-utleie på Trondheimsveien 163, Oslo. Hit rutes spørsmål om prisstrategi, PriceLabs-innstillinger, bookingoppfølging, gjestedialogue og beslutninger rundt belegg og inntekt. Ikke hit: generell økonomi, investeringer eller andre prosjekter.

## Resources

| Resource | Les når... |
| :---- | :---- |
| 2026-04-30-airbnb-prisstrategi.xlsx | Jobber med prisstrategi, PriceLabs-innstillinger eller beleggsmål |

## Workflow

1. Les MEMORY.md i denne mappen for å se siste status, beslutninger og tall.
2. Identifiser hva spørsmålet gjelder: prisstrategi, belegg, PriceLabs-innstillinger eller gjestedialogue.
3. Hent relevant data fra kontekst eller be Daniel laste opp ferske tall.
4. Gi én klar anbefaling med eksplisitt skille mellom observert fakta, tolkning og anbefaling.
5. Oppdater MEMORY.md med nye beslutninger eller endringer.

## Prisvurderingsprosess

Gjennomfør i denne rekkefølgen. Data lastes opp av Daniel som CSV-filer.

**Steg 1: Data inn**
To kilder, begge nødvendige:
- PriceLabs kalender (CSV-eksport): viser Final Price, Default Price, Available/Booked, Min Stay per dato. Eksporteres direkte fra PriceLabs-kalenderen.
- Markedsdata (CSV fra BNB Toolbox): Daniel laster ned data for relevante perioder, kjører `filter-bnb-data.py` (ligger i Airbnb-mappen), og laster opp filtrerte filer fra `Airbnb/Airbnb Resources/Data/`. Eventuelle nye bookinger meldes manuelt av Daniel.

BNB Toolbox-søk gjøres på to måter — begge er nyttige:
1. **8 gjester, alle sider, ingen bedroomsfilter:** viser hva gjesten faktisk ser. Brukes for generell markedsforståelse.
2. **8 gjester, alle sider, 4BR-filter:** viser det reelle konkurrentsettet (typisk 10-15 leiligheter). Brukes for konkret prisposisjonering. Dette er det viktigste datasettet for prisavgjørelser.

Script-plassering: `/Users/danielhansen/Documents/Cowork OS/Airbnb/filter-bnb-data.py`
Innmappe: `~/Downloads/Airbnb data`
Utmappe: `~/Documents/Cowork OS/Airbnb/Airbnb Resources/Data/`
**NB: Scriptet sletter alle eksisterende CSV-filer i utmappen før det skriver nye. Kjør scriptet på nytt for å lese ferske data.**

**Steg 2: Oppdater beleggstatus**
Tell bookede netter per måned. Sjekk mot beleggsmålene i MEMORY.md.
Er vi foran pace i peak season? Bra, prisen har vært for lav. Er vi bak? Greit, hold.

**Steg 3: Markedsposisjon per periode**
For hvert markedsdatabilde: Daniel's sluttpris vs. konkurrentenes sluttpriser (totalpris, ikke per natt).
Kategoriser: bunn / midten / topp av markedet.
Mål for peak season (juni/juli): topp 20-25%. Mål for mai: nær markedsmedianen.

**Steg 4: Beregn justeringer**
- Global baseprisjustering hvis alle perioder er feil kalibrert
- Dato-overrides for spesifikke perioder som avviker fra resten
- Override % = (målpris / nåpris) − 1
- Husk: baseendring skalerer ALLE datoer, inkl. event-overrides. Regn ToR separat.

**Steg 5: Sjekk tidssensitive funksjoner**
- Er Last Minute Discount og Orphan Day Prices aktivert? Bør de skrus av?
- Er det orphan-gap (1-2 netter) som krever min stay-override?
- Er det review-triggere som forfaller (se Key Decisions i MEMORY.md)?

## Analyseprinsipper

- Skill alltid mellom observert fakta, tolkning og anbefaling
- Kalenderfarger: Grønn = ledig, Grå = booket, Lilla stripe = dato-spesifikk customization
- Prisene i PriceLabs-kalenderen er uten LOS-justering
- ADR på bookede netter etter mars 2026 er upålitelige
- Leiligheten er attraktiv for store grupper: 4 soverom, lavere pris enn konkurrenter med samme kapasitet
- Beliggenheten er ikke førsteklasses sentralt

**Datahierarki for prisanalyse:**
- **BNB Toolbox, 4BR-filter, alle sider = primærdata.** Viser det faktiske konkurrentsettet en gjest som søker 4BR/8+ ser. Typisk 10-15 leiligheter. Bruk dette for konkrete prisavgjørelser.
- **BNB Toolbox uten filter, alle sider = sekundærdata.** Viser markedet slik en gjest uten bedroomsfilter ser det. Nyttig for å forstå generell etterspørselsdynamikk og synlighet, men ikke for direkte prissammenligning.
- **BNB Toolbox første side = utilstrekkelig.** Viser ~18 leiligheter, sannsynligvis de mest populære. Gir skjevt persentilbilde. Bruk alltid alle sider.
- **PriceLabs neighborhood data = trenddata, ikke konkurransedata.** Sammenligner per-natt priser på tvers av 350+ leiligheter i alle størrelser. Flatterer Daniel fordi det inkluderer mange 1-2BR leiligheter som naturlig priser lavere. Bruk kun til å se sesongmønstre og retning, aldri for å konkludere med konkret persentilposisjon.

**Anmeldelser er en prisdriver:**
- En leilighet med 80-100+ anmeldelser kan rettferdiggjøre 10-20% høyere pris enn en sammenlignbar leilighet med 15 anmeldelser. Pris aldri likt som en etablert konkurrent med vesentlig flere anmeldelser.
- New listings (0 anmeldelser) er ikke reell konkurranse på reliabilitetsnivå. De tiltrekker risikotolerante, prisbevisste gjester. Pris ikke ned mot dem.
- "Beste deal"-posisjon betyr: billigste leilighet med reell sosial bevis, klart under etablerte konkurrenter med mange flere anmeldelser, men over New listings.

**Sammenlign alltid totalpris, ikke per-natt:**
- Gjesten ser og sammenligner totalpris for oppholdet. Per-natt-tall er misvisende fordi rengjøringsgebyr, LOS-justeringer og antall netter varierer mellom leiligheter. Bruk alltid totalpris for den aktuelle perioden.

## Prisstrategi-prinsipper

- **Markedssammenligning:** Bruk alltid sluttprisen gjesten ser (totalpris inkl. alt). Ikke juster for rengjøringsgebyr, serviceavgift eller andre komponenter.
- **Optimaliseringsrekkefølge:** 1) Analyser hvor Daniel ligger i markedet basert på sluttpriser. 2) Anbefal enten baseprisjustering eller dato-spesifikke overrides, avhengig av hva som er mest treffsikkert.
- **Overordnet mål:** Maksimere total nettoinntekt frem til 1. august. Hver ledige natt er en mulighet som utløper — optimaliser forventet verdi per dato, ikke bare prisen.
- **Forventet verdi per dato:** Vurder alltid ledige netter som: Forventet inntekt = Pris × Sannsynlighet for booking. Sannsynligheten avhenger av (1) dager til dato, (2) type dager (helg vs. hverdag), og (3) markedsposisjon. En lavere pris med høyere sannsynlighet slår en høy pris med lav sannsynlighet.
- **"Sell last" gjelder kun peak season (juni/juli).** I mai er målet nær markedsmedianen, ikke topp 20-25%. Tomme mai-netter har reell kostnad og lav sjanse for last-minute-bookinger til premie.
- **Peak season = bak pace er bra.** Å være foran pace i høysesong betyr at prisen har vært for lav.
- **"Sell early" for store grupper.** For en 4BR/8-personers leilighet er "sell last" feil strategi. Store grupper (målgruppen) booker 30–60 dager ut med høy betalingsvilje. Etter det vinduet er den realistiske bookeren en liten gruppe (4–6 pax) med lavere betalingsvilje og mange billigere alternativer. Prisstrategien bør prioritere å konvertere store grupper tidlig, ikke holde prisen høy i håp om siste-liten-premium som ikke finnes for denne leilighetstypen.

**Event-prising:**
- **Test høyt, sett en deadline for å senke.** For event-netter: sett prisen til ønsket mål, sett en konkret dato for å revurdere (typisk 10-14 dager før eventet). Kan alltid senke prisen; kan ikke heve etter booking.
- **Events segmenterer etterspørselen.** En billig konkurrent med bedre beliggenhet for eventet tar beliggenhetssensitive grupper. Det er ikke nødvendigvis et argument for å senke Daniels pris — det kan bety at det gjenværende etterspørselssegmentet (reliabilitetssøkende) er mindre prisfølsomt.
- **Booking window for events:** 22 dager ut er fortsatt aktivt bookingvindu for store grupper (de tar tid å koordinere). Under 14 dager skifter etterspørselen mot mer prisbevisste last-minute-bookere. Juster deretter.
- **Ikke bruk PriceLabs neighborhood data for event-posisjonering.** Den viste ToR-nettene som P75-P90 mens BNB Toolbox 4BR-filter viste 27. persentil i det reelle markedet. Bruk alltid BNB Toolbox med 4BR-filter for event-analyse.

## Editorial Rules

Følg Daniels stemmeprinsipper i 00\_Resources (voice-principles.md).

- Skriv alltid på norsk.
- Vær eksplisitt om usikkerhet. Skill mellom observert fakta, tolkning og anbefaling.
- Gi alltid én klar anbefaling. Ikke presenter tre alternativer med mindre Daniel spør.
- Daniel har høy risikotoleranse og vil heller prise for høyt enn for lavt. Pushback er velkommen.
- Ikke bruk tankestrek (–) noe sted i teksten.
- Foretrekk baseprisjustering fremfor dato-spesifikke overrides. Overrides kun for events (Tons of Rock) og klare strukturelle avvik. Ikke anbefal overrides for å finjustere enkeltperioder uten event — det blir fort komplisert.
- Ikke endre global LOS-innstilling for å løse periodesspesifikke prisutfordringer. Juster heller underliggende override eller aksepter avviket. LOS er kalibrert for peak season og skalerer alle perioder.
