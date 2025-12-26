# Monstertruck Samler (Firebase Edition)

En installérbar PWA (Progressive Web App) for å registrere og holde oversikt over Monstertruck-samlingen din, nå drevet av Google Firebase.

## Funksjoner
- **Oversikt**: Rutenett og listevisning av samlingen.
- **Registrering**: Legg til trucker med bilde, verdi, tilstand, etc.
- **Statistikk**: Total verdi og antall.
- **Offline-støtte**: Fungerer uten nett (lesing og registrering).
- **Deling**: Del samlingen din med en offentlig lenke.
- **PDF**: Generer samlersertifikat og lister.

## Oppsett med Firebase

Denne appen bruker Firebase for autentisering, database og bildelagring.

### 1. Opprett Firebase-prosjekt
1. Gå til [Firebase Console](https://console.firebase.google.com/).
2. Trykk "Add project" og gi det et navn (f.eks. "Monstertruck Samler").
3. Slå av Google Analytics hvis du vil gjøre det enkelt.

### 2. Konfigurer App
1. I prosjektoversikten, trykk på web-ikonet (`</>`) for å registrere en app.
2. Gi den et navn (f.eks. "Monstertruck Web").
3. Du får nå opp en `firebaseConfig`-blokk.
4. **VIKTIG**: Kopier innholdet i denne blokken og lim det inn øverst i `app.js` i dette prosjektet.

### 3. Aktiver Autentisering
1. Gå til "Build" -> "Authentication" i menyen til venstre.
2. Trykk "Get started".
3. Velg "Email/Password" -> Slå på "Email link (passwordless sign-in)".
4. Trykk "Save".

### 4. Opprett Database (Firestore)
1. Gå til "Build" -> "Firestore Database".
2. Trykk "Create database".
3. Velg en lokasjon (f.eks. `eur3` for Europa).
4. Velg **"Start in test mode"** (vi fikser regler senere).

### 5. Opprett Lagring (Storage)
1. Gå til "Build" -> "Storage".
2. Trykk "Get started".
3. Start i **"test mode"**.
4. Trykk "Done".

### 6. Sikkerhetsregler (Viktig for produksjon)
Når du er ferdig med testing, oppdater reglene i Firebase Console:

**Firestore Rules:**
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /monstertrucks/{truckId} {
      // Eier kan lese og skrive. Andre kan lese (for deling).
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == request.resource.data.user_id;
    }
  }
}
```

**Storage Rules:**
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      // Eier kan laste opp. Alle kan se bilder.
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

## Kjøring og Installering

### Lokal kjøring
Du må kjøre appen via en lokal server for at moduler og Service Worker skal fungere (f.eks. Live Server i VS Code).

### Deploy (Vercel/Netlify)
Last opp mappen til Netlify eller Vercel. Det fungerer "ut av boksen" siden det er en ren statisk app.
