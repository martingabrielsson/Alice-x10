# Synk för Alice-x10 (görs en gång)

Alice-x10 använder **samma Firebase-projekt som glosappen** (`glosor-wordgame`), men ett **eget dokument** med en egen kod. Appen är redan inställd. Det som återstår är att lägga till en ny regel i Firebase.

## Så gör du: uppdatera reglerna i Firebase
1. Gå till <https://console.firebase.google.com> och öppna projektet **glosor-wordgame**.
2. Välj **Build → Firestore Database → fliken Rules**.
3. Ersätt allt innehåll med texten nedan och tryck **Publish**. Texten innehåller både glosappens dokument och Alice-x10:s nya dokument, så glosappen fortsätter fungera.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Glosappen
    match /families/Torpaskolan4A {
      allow get: if true;
      allow create, update: if request.resource.data.keys().hasOnly(['data'])
                            && request.resource.data.data is string
                            && request.resource.data.data.size() < 500000;
      allow list, delete: if false;
    }

    // Alice-x10 (multiplikation)
    match /families/Alice-x10-multiplikator-kvgscgtp {
      allow get: if true;
      allow create, update: if request.resource.data.keys().hasOnly(['data'])
                            && request.resource.data.data is string
                            && request.resource.data.data.size() < 500000;
      allow list, delete: if false;
    }
  }
}
```

## Hur det fungerar
- Alla enheter som öppnar appen använder koden `Alice-x10-multiplikator-kvgscgtp` automatiskt. Det finns inget att skriva in.
- Det som delas: läxor (Mina tabeller), framsteg per tal (nivå, rätt, fel, när det ska komma igen), svåra tal, rekord i Memory och dagar i rad.
- Synken sker några sekunder efter varje ändring, när appen öppnas och varje minut. Den väntar tills en pågående omgång eller Memory är klar.
- Samtidiga ändringar slås ihop: den senaste ändringen av en läxa vinner, och för varje tal vinner det senaste svaret medan antal rätt och fel tar det högsta värdet.
- **Synk & kopia** på startsidan visar status, har knappen "Synka nu" och möjlighet att spara och läsa in en säkerhetskopia som fil.
- Ljud på/av och vilka tabeller som är valda i Öva och Memory sparas på varje enhet och synkas inte.

## Observera
- Koden ligger öppet i den publika koden (appen ligger på GitHub Pages). Slumptecknen på slutet gör den svår att gissa, men den är inget lösenord. Det som delas är bara siffror och läxnamn.
- Vill du byta kod: ändra `code` i `SYNC_CFG` högst upp i skriptet i `index.html` **och** dokumentnamnet i regeln ovan.
- Fungerar synken inte visas "Ingen kontakt just nu" under **Synk & kopia**. Då har regeln oftast inte publicerats, eller så saknas internet.
- På iPhone och iPad har Safari och hemskärmsikonen var sin lagring, men båda synkar mot samma data.
