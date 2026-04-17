# Maaltijd Tracker — setup

Eén HTML-bestand dat je direct vanuit Finder kunt dubbelklikken. Geen build-stap.

## 1. Eerste keer starten

Dubbelklik `maaltijd-tracker.html` in Finder. Hij opent in je standaardbrowser. Chrome of Safari werkt allebei.

> **Tip:** Omdat de app `localStorage` en `fetch` gebruikt, werkt `file://` prima voor Claude API en Firebase.

## 2. Firebase project koppelen

In je Firebase-project:

1. **Firestore Database** aanzetten (Build → Firestore Database → Create database → "Start in production mode" → kies regio).
2. **Authentication** aanzetten met **Anonymous** sign-in (Build → Authentication → Sign-in method → Anonymous → Enable).
3. Firestore-regels (Rules-tab) — minimaal:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/{document=**} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
4. Haal je `firebaseConfig` op: **Project settings** → **General** → scrol naar "Your apps" → Web-app (+ toevoegen als je er nog geen hebt) → **SDK setup and configuration** → **Config**. Kopieer de JSON (alleen het object, zonder `const firebaseConfig =`).

## 3. Anthropic API key

1. Ga naar <https://console.anthropic.com> → API Keys → Create Key.
2. Voeg wat credit toe ($5 is meer dan genoeg om lang te testen).

## 4. In de app

Open de app → klik op **⚙** rechtsboven → vul in:

- Je profiel (leeftijd, gewicht, lengte, doel) — hiermee rekent hij je dagdoelen uit (BMR × activiteit, Mifflin-St Jeor).
- Anthropic API key.
- Firebase config (JSON plakken → **Opslaan**).

Ga dan terug naar **Chat** en begin met typen, bv.:

> Hoi, vandaag: boterham met kaas, 8 wafels waarvan 3 met kaas 2 met pindakaas 3 puur, en een bakje yoghurt

Claude antwoordt, eventueel met een vervolgvraag, en slaat items + voedingswaarden op. Rechts zie je live je dagtotalen.

## 5. Kalender & analyse

- **Kalender**-tab: maandoverzicht, klik een dag voor details en vraag dan daar gerichte analyse (macro-balans, tekorten, …).
- **Analyse**-tab: 7/14/30 dagen samenvatting met Claude.

## Over de voedingswaarden

De waarden worden door Claude geschat op basis van kennis van Nederlandse voedingsmiddelen en portiegroottes (NEVO-stijl). Dat is goed genoeg voor dagelijks inzicht. NEVO zelf is geen open API — als je klinische precisie wilt, zou je later de NEVO-export (RIVM) kunnen downloaden en via een lookup-functie laten raadplegen.

## Wat je zou kunnen uitbreiden

- Wijn/alcohol als apart veld + grenswaarden.
- Foto van je bord uploaden (Claude vision) i.p.v. tekst typen.
- Weekgrafieken (Recharts) naast de tekst-analyse.
- Deelbare export (PDF) van een periode.
- Meerdere gebruikersaccounts met e-mail-auth i.p.v. anoniem.

## Bestandslocatie

- `maaltijd-tracker.html` — de hele app (één bestand).
- Je API-key en Firebase-config worden in `localStorage` van je browser bewaard, niet in het HTML-bestand zelf.
