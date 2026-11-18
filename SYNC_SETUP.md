# Skapa Firebase-projektet "Alice-x10" (görs en gång)

Alice-x10 har ett **eget** Firebase-projekt, skilt från glosappen. Appen är redan förberedd. Det som saknas är två värden (`apiKey` och `projectId`) från det nya projektet.

Du behöver ett Google-konto (samma som för glosappen går bra). Det kostar inget: den gratis nivån räcker gott.

## 1. Skapa projektet
1. Gå till <https://console.firebase.google.com> och logga in.
2. Tryck **Create a project** (Skapa projekt).
3. Skriv namnet **Alice-x10** och tryck **Continue**.
   - Firebase visar under namnet ett **projekt-ID**, till exempel `alice-x10` eller `alice-x10-4f2a1`. Är namnet taget lägger Firebase till tecken. Skriv upp projekt-ID:t, du behöver det senare.
4. **Google Analytics**: stäng av (reglaget till **av**) och tryck **Create project**.
5. Vänta några sekunder och tryck **Continue**.

## 2. Skapa databasen (Firestore)
1. I menyn till vänster: **Build → Firestore Database**.
2. Tryck **Create database**.
3. **Edition**: om du får välja, välj **Standard**.
4. **Location**: välj en plats i Europa, till exempel **eur3 (Europe)** eller **europe-north1 (Finland)**. Det går inte att ändra efteråt. Tryck **Next**.
5. **Secure rules**: välj **Start in production mode** och tryck **Create** (Enable).
6. Vänta tills databasen är klar.

## 3. Lägg in reglerna
1. Öppna fliken **Rules** i Firestore Database.
2. Ta bort allt som står där och klistra in texten nedan.
3. Tryck **Publish**.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
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

Reglerna släpper bara in åtkomst till **ett** dokument (koden i appen) och tillåter inte att man listar eller raderar något.

## 4. Registrera en webbapp och hämta två värden
1. Tryck på kugghjulet bredvid **Project Overview** och välj **Project settings**.
2. Fliken **General**, scrolla ner till **Your apps**.
3. Tryck på webbikonen **`</>`** (Web).
4. **App nickname**: skriv `Alice-x10`. Låt rutan **Firebase Hosting** vara **tom** (ikryssa den inte). Tryck **Register app**.
5. Ett kodblock visas. Du behöver inte installera något eller köra `npm`. Leta upp två rader i `firebaseConfig`:
   - `apiKey: "AIza…"`
   - `projectId: "…"`
6. Kopiera de två värdena och skicka dem till mig i chatten. De är inte lösenord utan bara adresser som pekar ut projektet. Skyddet ligger i reglerna och den långa koden.
7. Tryck **Continue to console**.

## 5. Det jag gör sedan
När jag fått `apiKey` och `projectId` lägger jag in dem i `index.html` (raden `SYNC_CFG` högst upp i skriptet), testar och pushar. Därefter ska **Synk & kopia** på startsidan visa "✓ Ansluten" med ett klockslag.

## Så fungerar synken
- Alla enheter använder koden `Alice-x10-multiplikator-kvgscgtp` automatiskt. Det finns inget att skriva in.
- Det som delas: läxor, framsteg per tal, svåra tal, rekord i Memory och dagar i rad. Ljud på/av och valda tabeller i Öva och Memory ligger kvar på varje enhet.
- Synken sker några sekunder efter varje ändring, när appen öppnas och varje minut. Den väntar tills en pågående omgång eller Memory är klar.
- Samtidiga ändringar slås ihop: den senaste ändringen av en läxa vinner, och för varje tal vinner det senaste svaret medan antal rätt och fel tar det högsta värdet.
- **Synk & kopia** har också export och import av en säkerhetskopia som fil.

## Observera
- Koden ligger öppet i den publika koden. De slumpmässiga tecknen gör den svår att gissa, men den är inget lösenord.
- Vill du byta kod: ändra `code` i `SYNC_CFG` **och** dokumentnamnet i regeln.
- Visas "Ingen kontakt just nu" under **Synk & kopia** har regeln oftast inte publicerats, eller så saknas internet.
- Har du tidigare lagt till en regel för Alice-x10 i glosappens projekt (`glosor-wordgame`) kan du ta bort den där. Den används inte längre och glosappen påverkas inte.
