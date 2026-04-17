# Meal Tracker

Conversationele maaltijdregistratie met Claude en Firebase. Eén HTML-bestand, geen build-stap.

**Live:** https://jobhuberts.github.io/mealtracker/

## Hoe werkt het

- Netflix-stijl profielen: ieder huishoudlid kiest z'n eigen profiel (naam, icoon, leeftijd, gewicht, doel) — gesynced via Firebase, dus iedere device ziet dezelfde profielen.
- Chat met Claude: vertel wat je hebt gegeten en hij schat calorieën, macro's en micronutriënten (NEVO-stijl).
- Firestore bewaart je dagen per profiel; rechts zie je live je dagdoelen en totalen.
- Kalender-tab: maandoverzicht. Klik een dag voor details en analyse.
- Analyse-tab: 7/14/30-daagse analyse via Claude.

## Setup (éénmalig per gebruiker)

Open de app en klik op het tandwiel ⚙:

1. **Anthropic API key** — haal er een op [console.anthropic.com](https://console.anthropic.com). Key wordt alleen in jouw browser bewaard (localStorage).
2. **Firebase config** — plak het `firebaseConfig`-object (unquoted JS of geldige JSON mag allebei).
3. **Profiel** — maak een profiel aan met naam, icoon, leeftijd, gewicht, lengte, beweging en doel. Hiermee worden je dagdoelen berekend (Mifflin–St Jeor). Meer profielen kun je toevoegen via het startscherm.

## Lokaal draaien

```bash
# Dubbelklik index.html, of:
open index.html
```

Werkt direct vanaf `file://`. Geen bundler, geen npm.

## Firebase backend setup (voor maintainer)

1. Firebase project aanmaken.
2. **Build → Firestore Database** → Create database → locatie EU → production mode.
3. **Build → Authentication → Sign-in method** → **Anonymous** aanzetten.
4. Firestore **Rules** tab:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       // Gedeelde profielen (Netflix-stijl) — iedereen die ingelogd is
       // mag ieders profiel lezen/schrijven.
       match /profiles/{profileId}/{document=**} {
         allow read, write: if request.auth != null;
       }
       // Legacy data (voor migratie van oude versies zonder profielen)
       match /users/{uid}/{document=**} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
5. **Authentication → Settings → Authorized domains** → voeg je Pages-URL toe (bv. `jobhuberts.github.io`).

## Tech stack

- React 18 via CDN + Babel standalone (JSX in `<script type="text/babel">`)
- Tailwind via CDN
- Firebase v10 modular (Auth + Firestore) via CDN
- Claude Sonnet 4.6 via browser-directe API-call met `anthropic-dangerous-direct-browser-access`

## Roadmap

- Email/password auth (nu anonymous) — strengere rules per profiel-owner
- NEVO CSV-import voor klinische nauwkeurigheid
- Foto-invoer (Claude vision) i.p.v. tekst
