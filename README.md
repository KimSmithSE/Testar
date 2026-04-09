# 🐱 Peppol Lookup

> Ett seriöst verktyg för att slå upp Peppol-deltagare. Med katter.

**Live:** https://kimsmithse.github.io/Testar/

---

## Vad gör det?

Slår upp ett Peppol-ID (eller svenskt organisationsnummer) i Peppol-nätverket och visar:

- Vilken **SMP-server** deltagaren är registrerad hos
- **DNS-namnet** i SML (Service Metadata Locator)
- Alla **dokumenttyper** de kan ta emot (fakturor, kreditnotor, ordrar m.m.)
- Direktlänk till rå SMP-XML

## Hur fungerar det?

```
Browser → Cloudflare Worker → SML (DNS) → SMP (XML) → JSON → Browser
```

1. Användaren skriver in ett Peppol-ID, t.ex. `0007:5565597015`
2. **Cloudflare Worker** normaliserar ID:t och hashar det (SHA-256)
3. Worker gör en DNS-uppslag mot **Peppol SML** (`edelivery.tech`) för att hitta rätt SMP
4. Worker hämtar tjänstemetadata från **SMP:n** som XML
5. XML parsas och returneras som JSON till webbläsaren
6. Sidan visar resultatet — med katter

DNS och SMP-hämtningen måste ske server-side (Worker) eftersom webbläsaren blockerar direkta DNS-anrop och CORS-headers saknas på SMP:erna.

## Tekniker

| Del | Teknik |
|-----|--------|
| Frontend | Vanilla HTML/CSS/JS |
| Backend | Cloudflare Workers (gratis) |
| DNS-lookup | Cloudflare DNS-over-HTTPS |
| Peppol-standard | SML + SMP (ISO 6523) |
| Hosting | GitHub Pages |
| Katter | Ja |

## Filer

```
index.html   — frontend (det du ser)
worker.js    — Cloudflare Worker-koden (backend)
README.md    — det här
```

## Sätta upp själv

### 1. Cloudflare Worker

1. Skapa konto på [cloudflare.com](https://cloudflare.com)
2. Gå till **Workers & Pages → Create → Worker**
3. Klistra in innehållet från `worker.js`
4. Klicka **Deploy**
5. Notera din Worker-URL (t.ex. `https://peppol-lookup.DITT-NAMN.workers.dev`)

### 2. Frontend

1. Öppna `index.html`
2. Byt ut Worker-URL:en på raden:
   ```js
   const WORKER = 'https://peppol-lookup.kim-smith.workers.dev';
   ```
3. Ladda upp till GitHub Pages (eller kör lokalt)

## Exempel på Peppol-ID-format

| Format | Exempel | Beskrivning |
|--------|---------|-------------|
| `XXXX:ID` | `0007:5565597015` | ICD-kod + ID |
| Bara siffror | `5565597015` | Tolkas som svenskt org.nr (ICD 0007) |
| Fullt format | `iso6523-actorid-upis::0007:5565597015` | Komplett participant-ID |

Vanliga ICD-koder: `0007` (Sverige), `0088` (GLN), `9918` (IBAN).

## Begränsningar

- Visar bara dokumenttyper, inte detaljer om enskilda endpoints (AS4-URL, certifikat m.m.)
- Kräver att deltagaren är registrerad i **produktions-SML** (`edelivery.tech`)
- Inte alla SMP:er returnerar XML i exakt samma format — parsningen klarar de vanligaste

---

*Byggt med Claude på claude.ai · Cloudflare Workers · GitHub Pages · 🐾*
