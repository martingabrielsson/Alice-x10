# Plan: Alice-x10 (multiplikationstabellen 0–12)

Status: **plan uppdaterad med dina svar. Inget är byggt än.** Logga levereras senare (jag använder en tillfällig tills dess).

## PÅMINNELSE: nästa steg (innan fas 2 byggs)
- Fas 1 är byggd och pushad. Designändringar görs först (ny logga "Ax9", rosa text på mörkgrön bakgrund, gula siffror).
- **Fas 2 (synk mellan enheter) väntar på två svar:** (1) samma Firebase-projekt som glosappen? (2) kod `Alice-x10-multiplikator` eller med slumptecken på slutet?
- Gamla ikoner och gamla loggan (x10 på lavendel) ligger sparade i `old-icons/`.

## 1. Beslut
| Fråga | Beslut |
|---|---|
| Namn | **Alice-x10** (undertitel/Firebase-kod: Alice-x10-multiplikator) |
| Användare | Alice, 10 år. Ett barn, flera enheter (iPhone, iPad, Windows) |
| Repo / adress | `https://github.com/martingabrielsson/Alice-x10.git`, app på `https://martingabrielsson.github.io/alice-x10/` |
| Tabeller | **0 till 12** (0 × 0 till 12 × 12 = **169 fakta**) |
| 7 × 8 och 8 × 7 | **Olika fakta**, övas och följs upp var för sig |
| Division | **Mycket enkel division från början** (se 4) |
| Röst | **Ingen röst alls** (varken uppläsning eller att tala in svar) |
| Fokus | **Hitta det hon inte kan och ställa de frågorna igen och igen** tills de sitter (se 3) |
| Synk | Som glosappen, via Firebase, så att allt följer med mellan enheterna |

## 2. Funktioner
Startsidan (stora färgade knappar, samma stil som glosappen), i den här ordningen:
1. **Dagens träning** (stora huvudknappen): 15 frågor som appen väljer själv bland det Alice kan sämst (se 3). Ingen inställning behövs.
2. **Hitta vad jag kan**: en kartläggning som snabbt visar vilka fakta som är nya, osäkra eller sitter (se 3).
3. **Mina tabeller**: välj vilka tabeller som ska övas, spara namngivna läxor ("Vecka 47: 6:an och 7:an").
4. **Öva**: lägen (nedan).
5. **Flash cards**: manuell övning, vänd kortet.
6. **Memory**: hitta par av "7 × 8" och "56".
7. **Tabellen**: titta på tabellerna, rutnät 13 × 13, knep.
8. **Mina framsteg**: värmekarta över alla 169 fakta, vad som kommer igen, rekord.
9. Små knappar: ljud på/av, ☁️ Synka enheter.

### Öva-lägen
- **Skriv svaret**: "7 × 8 = ?" med egen stor sifferknappsats (0–9, ⌫, OK).
- **Hitta talet**: "7 × ? = 56".
- **Flerval**: fyra alternativ (för osäkra fakta eller som uppvärmning).
- **Division**: "56 ÷ 7 = ?", enkelt först (se 4).
- **Blandat**: alla lägen slumpas per fråga.
- **Tidsutmaning**: 60 sekunder, så många rätt som möjligt, personligt rekord.
- Ledtråd: visar "7 + 7 + 7 …" eller en liten ruta med 7 rader × 8 prickar, kostar poäng.

### Flash cards, Memory, Tabellen
- **Flash cards**: kort visar "7 × 8", hon tänker eller skriver, vänder kortet (56), Förra/Nästa/Svårt, slut-panel med "Öva bara de svåra". Svårt-markeringarna skickas vidare till träningsmotorn.
- **Memory**: par av uttryck och svar, max 20 par, bara från tabellerna som valts.
- **Tabellen**: lista och rutnät 13 × 13, mönster markeras, korta knep per tabell (9:ans fingertrick, 11:an, 5:ans slut på 0 eller 5). Allt utan ljud.

### Övrigt (som glosappen)
Hjälptext ("Så här gör du", hopfälld), export/import av säkerhetskopia, ingen pinch-zoom, ikon för hemskärm och bokmärke, start-skärm med logga, fungerar offline.

## 3. Träningsmotorn: det som gör att svaga fakta kommer igen och igen
Det här är hjärtat i appen. Varje faktum (t.ex. `7x8`, `8x7`, `56/7`) har egen status.

**Status per faktum:** nivå 0–5 (0 = ny/osäker, 5 = sitter), antal rätt i rad, senaste svarstid, antal fel, när det ska komma igen.

**Under en omgång (kommer igen och igen direkt):**
- Svarar Alice fel visas rätt svar tydligt, tillsammans med en liten bild (7 rader × 8 prickar). Sedan ställs **samma fråga igen** efter ca 2 frågor, igen efter ca 5, igen efter ca 10, tills hon svarat **rätt 3 gånger i rad**. Frågan försvinner inte bara för att hon gissat rätt en gång.
- Långsamt rätt svar (över ca 6 sekunder) räknas som "halvt rätt": det kommer tillbaka lite senare.

**Mellan omgångar (kommer igen i morgon, i övermorgon):**
- Fel → faktumet tillbaka till nivå 0 och kommer igen **samma dag** (efter någon minut).
- Rätt men långsamt → nivån står still.
- Snabbt rätt → upp en nivå. Intervall mellan nivåerna: samma dag, 1 dag, 3 dagar, 7 dagar, 14 dagar.
- 0- och 1-fakta (0 × n, 1 × n) kräver bara en rätt för att räknas som kända.

**Dagens träning** väljer 15 frågor i den här ordningen: (1) fakta som är förfallna eller felsvarade nyligen, (2) fakta på nivå 0–1, (3) några kända fakta som uppfräkning, (4) nya fakta först när de svaga är under kontroll. Därmed får de svåraste fakta (ofta 6×7, 6×8, 7×8, 7×9, 8×9 och deras spegelbilder) mest övning.

**Hitta vad jag kan** (kartläggning): frågar alla 169 fakta en gång, en snabb omgång på ca 4 minuter som går att dela upp (t.ex. 40 frågor i taget). Snabbt rätt = grönt, långsamt rätt = gult, fel = rött. Röda och gula blir startpunkten för Dagens träning.

**Alla lägen matar samma motor:** Öva, Flash cards (Svårt), Memory (felklick), Tidsutmaning och Division lägger resultat i samma statistik, så svagheten följer med oavsett hur hon övar.

**Mina framsteg**: värmekarta 13 × 13 (rad = första talet, kolumn = andra talet, 7×8 och 8×7 är två egna rutor). Färg: grå = ny, röd = svag, gul = under inlärning, grön = sitter, guld = snabbt. Under kartan: "Det här övar vi mest på" (de 5 svagaste), antal "kan" av 169, streak (dagar i rad), medaljer när en hel tabell blir grön.

## 4. Division (mycket enkel, från början)
- Division är **inversen** av en känd multiplikation: "56 ÷ 7 = ?" hör ihop med 7 × 8.
- Första steget är **bara de enklaste**: ÷ 1, ÷ 2, ÷ 5 och ÷ 10 (t.ex. 18 ÷ 2, 35 ÷ 5, 90 ÷ 10).
- Fler divisorer låses upp **automatiskt** när motsvarande multiplikationsfakta sitter (blir "kan"): när 3:an sitter kommer ÷ 3, osv. Då ligger "låst" kvar som grå ruta i divisionsdelen av kartan.
- Division har egna fakta i motorn (`56/7`), men ett fel där höjer även sannolikheten att motsvarande multiplikation (`7x8`) kommer igen.

## 5. Synk mellan enheter (som glosappen)
- Firebase Firestore (REST), **fast kod** i appen så att alla enheter delar samma data utan inloggning: `Alice-x10-multiplikator`.
- **Föreslaget: återanvänd samma Firebase-projekt som glosappen** (`glosor-wordgame`). Då behöver du bara lägga till en ny regel för det nya dokumentet (jag skriver texten, du klistrar in den i Firebase → Firestore → Rules).
- Det som synkas: läxor (valda tabeller), status per faktum (nivå, antal rätt/fel, tid, nästa tillfälle), rekord, streak. Ljud på/av stannar på varje enhet.
- Sammanslagning: för varje faktum vinner den enhet som har **senaste** svar; antal rätt/fel och rekord tar högsta värdet. En faktum-post är liten, så hela statistiken ryms långt under Firestores gräns (ca 169 × 80 byte).
- Observera: koden ligger öppet i den publika koden. `Alice-x10-multiplikator` är lätt att gissa; vi kan lägga till några slumptecken (t.ex. `Alice-x10-multiplikator-7Kq9`) om du vill.

## 6. Datamodell
```
state = {
  homeworks: [ {id, name, tables:[6,7], updated} ],
  facts: { "7x8": {box, streak, ok, miss, lastMs, due, t},   // 169 mult. + enkla div. ("56/7")
           ... },
  best: { timeAttack: 23 }, streakDays: {n, last},
  settings: { muted }
}
```
Bygg så att varje post har `t` (senaste ändring) från början, så att synken kan läggas på utan omskrivning.

## 7. Byggfaser
1. **Fas 0 (uppstart):** mapp, git, repo `Alice-x10`, GitHub Pages, tillfällig logga/ikon. *Kräver ditt ja att skapa repot och slå på Pages.*
2. **Fas 1 (första användbara versionen):** startsida, Mina tabeller + läxor, **träningsmotorn**, **Öva skriv svaret** med sifferknappsats, **Dagens träning**, **Hitta vad jag kan**, **Mina framsteg** (värmekarta), lokal lagring, hjälptext, ikoner, ingen pinch-zoom. Redan här fungerar det viktigaste: svaga fakta kommer igen och igen.
3. **Fas 2 (synk + backup):** Firebase-synk mellan Alices enheter, export/import. Görs tidigt eftersom du vill kunna använda den på flera enheter.
4. **Fas 3 (fler lägen):** Division (enkel), Hitta talet, Flerval, Tidsutmaning, Flash cards, Memory, Tabellen med knep.
5. **Fas 4 (finjustering):** belöningar/medaljer, tester på riktiga enheter, justera tidsgränser och intervall efter hur Alice klarar sig.

## 8. Vad jag behöver från dig
1. Ja till att jag **skapar GitHub-repot** `Alice-x10` och **slår på Pages** (jag gör det med `gh` om du vill, annars skapar du det tomt själv).
2. **Logga**: bakgrundsbild + en logga som kan snurra på start-skärmen, som i glosappen. Tills dess använder jag en enkel tillfällig.
3. Firebase: **ja till samma projekt som glosappen**? (Jag skriver regeltexten.)
4. Slumptecken på slutet av koden, eller behålla `Alice-x10-multiplikator`?
