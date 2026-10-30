# Dal lead alla consulenza: bot, chiamata del setter e cosa verificare

**Versione:** bozza del 30/10/2026
**Responsabile:** Marco Sansone
**Per chi:** Marco, Andrea, i setter (Sophia, Vincenzo, Simona), i consulenti sales (Mauri e Martina), chi sviluppa WeCRM
**Stato:** bozza. Le parti con ⚠️ sono decisioni o verifiche ancora aperte. Niente di quello che c'è qui è stato approvato in modo definitivo, tranne quello che è segnato come "deciso da Marco il 29/10".

---

## 1. A cosa serve questo documento

Oggi il primo contatto con un lead che lascia la richiesta (Instagram, Facebook, sito, "Chiamami") passa da due strade che non si parlano bene: il bot WhatsApp, che fa anche da setter, e la telefonata di una persona. Il risultato è che circa **metà dei lead risponde alla prima chiamata** e che chi risponde trova messaggi e promesse diversi a seconda del canale.

Il 29/10 Marco ha deciso di cambiare i ruoli:

- **Il bot non fa più il setter.** Il suo unico compito è far arrivare una telefonata a cui il titolare risponde.
- **La persona al telefono fa la qualifica**, con lo script che abbiamo costruito partendo da quello dei consulenti sales.
- **La consulenza con Andrea** è l'obiettivo della telefonata, e il prezzo si dice solo lì.

Questo documento mette in fila **tutto il percorso**, dal momento in cui arriva il lead fino alla consulenza prenotata, e raccoglie **le domande da verificare** con i consulenti sales, con Andrea e con i setter prima di dare tutto per buono.

**Obiettivo misurabile:** portare la risposta alla prima chiamata da circa il 50% al **60-70%**, senza perdere la qualità delle consulenze prenotate.

---

## 2. Il percorso in una pagina

```
 Lead lascia la richiesta (modulo, chat, "Chiamami")
        │
        ▼
 WeCRM controlla: c'è un setter in turno e timbrato?
        │
   ┌────┴─────────────────────┐
   SÌ  (feriali 9-18,         NO (notte, weekend,
   fuori pausa)                pausa, dopo le 18)
   │                           │
   ▼                           ▼
 MODALITÀ A                  MODALITÀ B
 Il bot scrive:              Il bot scrive:
 "Tra pochi minuti ti        "Il team ti chiama
  chiama una persona          domani/lunedì:
  del nostro team"            mattina o pomeriggio?"
   │                           │
   │                           ▼
   │                         Il lead sceglie la fascia
   │                           │
   │                           ▼
   │                         Il sistema propone 2 orari
   │                         lontani nella fascia
   │                           │
   │                           ▼
   │                         Telefonata nel CRM, segnata
   │                         "Ha scelto lui l'orario"
   │                           │
   ▼                           ▼
 Il setter chiama ──────►  Il setter chiama all'ora scelta
   │
   ▼
 Telefonata (script): apertura → filtro titolare → diagnosi →
 spiegazione → interesse e tempi → obiezioni → decisore →
 prenotazione della consulenza con Andrea
   │
   ▼
 Conferma subito (messaggio + email) → conferma il giorno prima
   │
   ▼
 Consulenza con Andrea
```

**Reti di sicurezza**
- In modalità A, se dopo **10 minuti** nessuno ha chiamato, compare un avviso sulla Home di Andrea. Dopo **30 minuti** il bot manda da solo i due orari da scegliere.
- In modalità B, il bot propone gli orari al massimo **due volte**. Poi il lead resta "mai chiamato" e lo prende il primo setter del mattino.

---

## 3. Tappa per tappa

### 3.1 Arrivo del lead e scelta della modalità

Il CRM sa già se c'è un setter in turno e timbrato: è la stessa logica dello smistamento a giro.

| Situazione | Modalità |
|---|---|
| Feriale 9-18, setter timbrato, fuori pausa | **A** |
| Notte, weekend, pausa 13-14 senza nessuno, dopo le 18 | **B** |

**Come il bot sceglie il giorno nella modalità B** (lo scrive il codice, mai il modello):
- Sera di un feriale (dopo le 18, lunedì-giovedì): «domani».
- Da venerdì alle 18 e tutto il weekend: «lunedì».
- Pausa 13-14: «oggi». Si salta la domanda sulla fascia e si danno due orari del pomeriggio, se c'è qualcuno.
- Vigilia di festivo: il primo giorno lavorativo col suo nome («martedì»).
- Se nella fascia scelta quel giorno non c'è nessun setter (il pomeriggio copre solo chi è in turno 12-18), il sistema propone il primo giorno in cui c'è.

### 3.2 Il bot: cosa scrive

**Modalità A: setter timbrato** (template Meta nuovo, da approvare)
> Ciao! Ti scriviamo da WeGest: abbiamo ricevuto la tua richiesta {{su Instagram}}. Tra pochi minuti ti chiama una persona del nostro team: se vedi un numero che non conosci, siamo noi 😊
> Bottoni: **Adesso non posso, chiamami dopo** · **Non l'ho lasciato io**

- Niente nome e niente numero del setter (Marco, 29/10): non si promette una persona se non si è sicuri che ci sia.
- «Adesso non posso» porta agli stessi due passi della modalità B. La telefonata nasce con «Ha scelto lui l'orario».
- Se scrive altro (domanda, «come funziona», prezzo): una riga e rimando. *«Te lo spiega la persona che ti chiama tra poco 😊»*

**Modalità B: nessuno al telefono** (template Meta nuovo, da approvare)
> Ciao! Ti scriviamo da WeGest: abbiamo ricevuto la tua richiesta {{su Instagram}}. Il team ti chiama {{domani / lunedì}}: preferisci la mattina o il pomeriggio?
> Bottoni: **Mattina** · **Pomeriggio** · **Non l'ho lasciato io**

Alla scelta della fascia, due orari lontani dentro la fascia:
> Perfetto, domani mattina: alle 9:30 o alle 12:00? *(pomeriggio: alle 14:30 o alle 17:00)*

- La risposta («il primo», «alle 12», «la seconda») la legge il codice come oggi.
- «Nessuno dei due» o «un altro orario»: altri due orari nella stessa fascia. Massimo due giri.
- Gli orari vengono dal calendario turni, almeno 2 ore dopo l'arrivo del lead.
- La telefonata compare in cima a «Da fare» all'ora giusta, senza assegnatario.

### 3.3 Il bot: cosa fa nei casi che incontra comunque

| Il lead | Il bot |
|---|---|
| Chiede il prezzo | *«Il prezzo dipende da come lavora il tuo salone: te lo spiega al telefono chi ti chiama, nero su bianco.»* **Nessuna cifra, mai.** Questo supera il «69 €» del 12/10. |
| «Non voglio essere chiamato, scrivetemi» | ⚠️ **Da decidere con i consulenti.** Ipotesi di lavoro: *«Non è un problema, non ti chiamiamo se non vuoi 😊 Dimmi tu quando possiamo sentirci, anche due minuti.»* + due orari. Se rifiuta ancora, la chat passa al setter in turno. Finché i consulenti non rispondono, questo caso resta «come oggi»: vedi il paragrafo 3.3 bis. |
| Chiede esplicitamente la videochiamata o Google Meet | Prenota come oggi. **Solo in questo caso** il bot prenota la consulenza. |
| Vuole prenotare un taglio, cerca lavoro, spam, minorenne | Come oggi: chiusura gentile, nessuna telefonata. |
| Già cliente | Come oggi: numero assistenza 0915640490. |
| «Non l'ho lasciato io» | Come oggi: scuse, pausa, **nessuna chiamata**. |
| Ha già un appuntamento fissato | Come oggi: link, sposta, annulla. |
| Risponde a un invito al Tour o è iscritto a un evento | Come oggi: i rami eventi non cambiano. |
| Domande tecniche, fiscali, sulle funzioni | *«Te lo conferma chi ti chiama.»* Non conferma e non smentisce. |
| «Sei un robot?» | *«Sono l'assistente virtuale di WeGest.»* (obbligo di trasparenza sull'AI) |

### 3.3 bis · Che cosa vuol dire «come oggi» per chi non vuole essere chiamato

Fino alla risposta dei consulenti (call del 3/11), **per questo solo caso il bot non applica la regola nuova**. Non usa la frase rassicurante e non spinge verso due orari di telefonata. Resta in chat con il lead e si comporta come oggi:

- **La conversazione resta scritta.** Il bot continua a rispondere ai messaggi del lead.
- **Può ancora qualificare:** chiedere settore, gestionale usato, chi decide.
- **Può ancora prenotare la consulenza** su Calendly, proponendo due orari e chiedendo nome ed email, se il lead vuole vedere il programma.
- **Nessun setter chiama quel lead,** a meno che un operatore prenda in mano la chat.
- **Restano valide le regole che già fermano il bot oggi:** «non scrivetemi più» (smette e chiede il motivo), «non l'ho lasciato io» (pausa, risponde una persona) e chat già in mano a un operatore.

⚠️ **Da decidere · il prompt ridotto non basta.** Quello che abbiamo scritto toglie qualifica e prenotazione, quindi «come oggi» non è possibile senza scegliere una di queste strade:

1. **Tenere il pezzo del prompt attuale** su qualifica e prenotazione, solo per chi non vuole la telefonata. Più codice, due comportamenti da mantenere.
2. **Passare subito la chat a una persona** (il setter in turno, «preferisce chat»). È già prevista come ultimo passo. Più semplice, ma serve qualcuno che risponda in chat.

⚠️ **Da decidere · il prezzo.** Oggi il bot dice «si parte da 69 € al mese» (decisione del 12/10). La regola nuova dice «mai cifre» per tutti i canali. Proposta: nessuna cifra nemmeno in questo caso, altrimenti lo stesso lead trova due regole diverse.

⚠️ **Da verificare.** Ho ricostruito questo comportamento dalle note del second brain (29/10 e stato del bot all'08/10), non leggendo il prompt in produzione. Va confermato con una prova reale prima del 3/11.

### 3.4 Cosa esce dal bot

Domande di qualifica (settore, gestionale, decisore, budget), «domande da consulente», pitch e posizionamento, gestione delle obiezioni («ci penso», «costa troppo»), «il 30% apre un secondo salone», prenotazione della consulenza a chi non la chiede. **Tutto questo passa allo script dei setter.**

La nota «[Bot WhatsApp]» sulla scheda resta, ma contiene solo quello che il lead ha scritto da solo.

### 3.5 Il passaggio dal bot al setter

Prima di comporre il numero, il setter fa **30 secondi di lettura**:
1. Chat e nota «[Bot WhatsApp]» sulla scheda del lead.
2. Se il lead ha scelto lui l'orario o ha premuto «Adesso non posso, chiamami dopo»: si apre con *«Ciao [nome], ci eravamo dati appuntamento!»*, non con l'apertura a freddo.
3. Se ha premuto «Non l'ho lasciato io»: **non si chiama**. La scheda va decisa.
4. Se il lead sta ancora chattando col bot, il setter che chiama lo interrompe: serve una regola chiara su chi vince. ⚠️ Vedi domande al paragrafo 5.4.

---

## 4. La chiamata del setter: lo script

Parte dalla struttura dei consulenti sales (audio e Google Doc «SCRIPT SETTER WEGEST» del 29/10). La struttura resta. Le frasi sono riscritte, perché quelle originali vengono da un modello per corsi e coaching ad alto prezzo («potenzialità di guadagno», «investire nel tuo futuro», «solo X posti») e a un titolare di salone che ha chiesto informazioni su un gestionale suonano false.

**Per chi vale:** lead in entrata (modulo, chat, «Chiamami»). Non sostituisce lo Script vendita a freddo (outbound), la chiamata a chi ha una licenza demo attiva (si parte da «Come sta andando la prova?») né il recupero dei persi.

### Regole sempre valide
- **Mai il prezzo**, né in chat né al telefono. Se lo chiedono: *«Il prezzo dipende da come lavora il tuo salone: in consulenza Andrea ti fa vedere cosa ti serve davvero e ti dice la cifra, nero su bianco.»*
- **Niente demo al telefono.** Si accenna, non si spiega.
- **Solo casi veri e scarsità vera.** I titolari si conoscono tra loro.
- **Silenzio dopo la frase importante.** Aspetta la risposta.
- **Tutto quello che emerge va nella nota della consulenza in WeCRM:** problema, programma usato, quante persone, obiettivo, chi decide. Andrea parte da lì e non rifà le stesse domande.

### Fase 1 · Apertura
Al «Pronto?»:
> **«Ciao [nome]!»**

Non dire «parlo con…?»: sembra un call center. Il lead risponde «con chi parlo?» e a quel punto difficilmente riattacca.

> «Sono [nome], del team consulenze di WeGest. Ti chiamo per la richiesta di consulenza gratuita che hai lasciato su [Instagram / Facebook]. Ti rubo 5 minuti per capire se ha senso fissarla, ok?»

- ⚠️ **Ruolo.** Proposto «del team consulenze» al posto di «Manager del team»: un setter che si presenta come manager si scopre quando alla consulenza trova Andrea. Decide Marco.
- Finché la VSL non è online, di' «dalla pubblicità che hai visto», non «dal video».

### Fase 2 · Filtro: è un titolare?
> «Prima una cosa veloce: hai un salone tuo o lavori in un salone?»

- **Titolare, socio o chi decide:** si va avanti.
- **Dipendente, privato, chi cercava lavoro:** si chiude con gentilezza. *«Grazie del tempo! La consulenza serve a chi gestisce il salone. Se vuoi, fallo sapere al titolare.»* In WeCRM: esito **Fuori target**.

Perché serve: su 96 lead persi, 35 non erano veri clienti possibili.

### Fase 3 · Diagnosi (5-7 minuti, ascolta più di quanto parli)
Una domanda alla volta, aspettando la risposta.

1. *«Cosa ti ha spinto a lasciarci la richiesta?»*
2. *«In quanti siete a lavorare in salone?»*
3. *«Oggi come gestisci appuntamenti e clienti: con un programma o con l'agenda di carta? Come ti ci trovi?»*
4. *«Cosa vorresti che cambiasse nei prossimi mesi?»*

**Se usa già un altro programma:** *«E come ti sei trovato? Cosa ti dà fastidio?»* Da qui escono i problemi veri. Per ogni concorrente si guarda la Battlecard. L'unico concorrente nominato dai lead persi è Treatwell, sempre con lamentele su assistenza e commissioni.

**Perché WeGest:** *«E cosa ti ha incuriosito di noi?»* La risposta serve ad agganciare un nostro punto di forza: assistenza di persone vere, formazione gratuita, un'azienda dietro.

⚠️ In chat la domanda «Cosa ti ha spinto a contattarci?» faceva smettere di rispondere. Al telefono può andare diversamente: va provata e misurata, non data per buona.

### Fase 4 · Spiegazione
> «Hai capito di cosa ci occupiamo?» *(aspetta)*

> «Noi aiutiamo [parrucchieri / barbieri / centri estetici / toelettature] che oggi [fanno tutto a mano / hanno un programma che non li aiuta] a tenere sotto controllo agenda, clienti e incassi, a far tornare le clienti e a perdere meno appuntamenti.»

Poi **un solo esempio vero**, in 30-60 secondi, preso da quello che il lead ha detto o dai casi prima/dopo del Metodo WeGest:
- la cliente che non fa il colore da 30 giorni e torna con un messaggio;
- quanto costa in euro un appuntamento saltato;
- «sapere quanto spendo, non solo quanto incasso».

**Cosa si può dire, perché è vero**
- Il Metodo WeGest nasce da 687 consulenze ai saloni, ed è nato da Marco, partito come barbiere ("From Barber to CEO").
- *«Più di 2.000 saloni lo usano ogni giorno.»* ⚠️ Al 23/10 i clienti attivi erano circa 2.143: rileggere il numero in WeCRM prima di stamparlo nello script.
- *«Ti seguiamo noi: formazione gratuita e senza limiti, assistenza con persone vere e una consulenza sul tuo salone.»* Non si promettono risultati.

Poi silenzio, e: *«Tutto chiaro fin qui?»*

### Fase 5 · Domande sul come e sul quanto
Non si risponde nel merito:
> «Capisco che tu voglia saperne di più. I dettagli pratici te li fa vedere Andrea in consulenza, sui tuoi numeri: adesso rischierei di essere superficiale. Ok?»

Sul prezzo vale la regola in cima.

### Fase 6 · Interesse e tempi
> «Pensi che quello che ti ho detto possa fare al caso tuo?» *(aspetta)*

Scarsità, **solo se vera** (agenda di Andrea o scadenze delle offerte evento):
> «Andrea ha ancora due posti questa settimana: giovedì alle 10 o venerdì alle 15?»

Domanda sui tempi (al posto di «partiresti subito?»):
> «Se in consulenza vedi che fa per te, pensi di partire nelle prossime settimane o è una cosa per più avanti?»

- **«Nelle prossime settimane»:** si passa alla fase 8.
- **«Più avanti»:** si chiede il motivo (contratto con un altro fornitore che scade, apertura tra qualche mese). Poi **si fissa comunque** e si scrive in nota la data che conta.

⚠️ La versione "decisa" dei consulenti («è una priorità per te? deve dire sì», «se sei qui per perdere tempo non fisso») va provata **prima con un solo setter**. Questa bozza usa la versione morbida.

### Fase 7 · Obiezioni
**«Non ho i soldi» / «costa troppo»**
> «Ti dico subito una cosa: se cerchi un'agenda da 19 €, non siamo noi. WeGest è un gestionale completo con un'azienda dietro: formazione, assistenza, consulenza. In consulenza Andrea ti fa vedere cosa ti serve davvero e ti dice il prezzo, nero su bianco.»

**«Non mi fido» / «ci devo pensare»**
> «Fai bene a volerci vedere chiaro. Per questo c'è la consulenza: è gratuita, non ti impegna a niente e vedi con i tuoi occhi come funziona. Cosa ti preoccupa di più?»

Altre obiezioni: schede *Obiezioni e risposte* e *Risposte a «l'altro costa 20 euro» e «è solo un'agenda»*.

### Fase 8 · Decisore e prenotazione
> «Prima di confermare: la consulenza la valuti da solo/a o c'è qualcuno che decide con te, un socio o un familiare?»

- **Decide da solo/a:** si prenota.
- **C'è un'altra persona:** *«Allora ti consiglio di portarla con te: così valutate tutto insieme.»* Se può esserci, si prenota. Se no, **non si prenota**: si riprogramma. Tre demo su quattro non chiudono per «devo parlare col socio».

> «Ti fisso per [giorno] alle [ora], videocall su Google Meet di [⚠️ 20 / 30 / 60] minuti con Andrea.»

⚠️ **Durata da allineare.** Lo script dei consulenti dice 1 ora, la Demo Online su Calendly dura 30 minuti, lo Script a freddo dice 20. Decidono Marco e Andrea.

**Una parola sola** per bot, setter e VSL: **consulenza**, non «demo».

### Fase 9 · Chiusura e no-show
> «Perfetto! Ti mando subito un messaggio e un'email di conferma. Il giorno prima ti scrivo per confermare: ci sarai anche con [il socio]?»

> «Questo è il numero del team: scrivimi qui quando vuoi.»

(È il numero della SIM aziendale, non uno personale.)

Quando sarà pronta la VSL, si manda dopo la prenotazione: il lead arriva sapendo già chi siamo.

In WeCRM: esito giusto, nota completa, e il giorno prima conferma (fase 2 della giornata del setter).

---

## 5. Cosa verificare: le domande

Le domande sono divise per chi può rispondere. Per ognuna c'è il motivo, e dove possibile come lo misuriamo.

### 5.1 Per i consulenti sales (Mauri e Martina), call di martedì 3/11 alle 12 ⚠️ da confermare

Le prime sei sono già nel documento che gli abbiamo mandato (in bozza, da approvare da Marco prima dell'invio). Le altre le propongo io.

**Già nel documento per loro**
1. **Il lead che non vuole essere chiamato** e vuole solo scrivere: come lo gestiamo? Finché non rispondono, il bot fa come oggi (vedi 3.3 bis).
2. **«Cosa ti ha spinto a contattarci?»**: in chat faceva smettere di rispondere. Al telefono la teniamo?
3. **Decisore assente**: si fissa comunque o si aspetta?
4. **Durata della consulenza** da promettere: 20, 30 o 60 minuti?
5. **Scarsità**: usiamo solo quella vera (agenda del venditore, scadenze degli eventi)? C'è qualcosa di vero che stiamo trascurando?
6. **Chi prova per primo** la parte "decisa" dello script, e cosa guardare nelle registrazioni?

**Da aggiungere (proposta)**
7. **Quanto tempo può passare** tra il lead e la prima chiamata prima che la probabilità di risposta crolli? Reggiamo davvero la promessa «tra pochi minuti» o è più prudente «entro un'ora»?
8. **Quanti tentativi** di chiamata, a che distanza, e a che orari, prima di dare il lead per perso? Cosa scriviamo tra un tentativo e l'altro?
9. **Cosa fare dopo «non risponde»**: il messaggio di sollecito che stiamo preparando («Ti abbiamo chiamato e non ti abbiamo trovato. Dimmi tu quando: [orario 1] o [orario 2]?») vi sembra giusto, o troppo insistente?
10. **Il filtro «hai un salone tuo?»** all'inizio: è meglio chiederlo subito o dopo la diagnosi? Rischiamo di far sentire il lead "esaminato"?
11. **Gli esempi**: preferite un caso vero e specifico per ogni tipo di attività (parrucchiere, barbiere, centro estetico, toelettatura) o una storia sola più forte?
12. **Il silenzio e la pausa**: come si insegna a un setter appena assunto a reggerli senza riempire?
13. **Registrazioni**: quante chiamate vi servono, di quali setter e per quanto tempo, per darci un giudizio utile? Come preferite riceverle?
14. **Formazione dei setter**: quante ore servono per portare Sophia, Vincenzo e Simona a un livello decente su questo script? Meglio un'ora di ascolto dell'audio o prove a coppie?
15. **Metriche**: quali numeri guardate voi per dire che uno script funziona (risposta, prenotazioni su raggiunti, show-up, chiusure) e quali soglie vi sembrano buone per un gestionale da circa 69 € al mese?
16. **Cosa NON copiare** dal vostro modello per corsi e coaching: ci sono altre frasi dello script che, secondo voi, con un titolare di salone non funzionano?

### 5.2 Per Andrea (sales manager) e per i setter

Sono le domande per chi chiama davvero. Servono a capire se lo script regge nella pratica.

**Prima di partire**
1. Ascolti l'audio dei consulenti e leggi lo script? Cosa ti suona falso o difficile da dire?
2. Il ruolo «del team consulenze»: va bene o preferisci un'altra formula?
3. Quanti posti hai davvero a settimana per le consulenze? Possiamo dire «due posti questa settimana» senza mentire?
4. Quanto dura oggi una consulenza, e quanto vorresti che durasse?
5. Cosa vuoi trovare scritto nella nota del setter per non rifare le stesse domande?
6. Chi dei tre setter prova per primo lo script, per una settimana, su tutti i lead in entrata?

**Dopo la prima settimana**
7. Quante chiamate sono state raggiunte e quante prenotazioni sono uscite, rispetto agli altri setter che non usano lo script?
8. In quali fasi la conversazione si blocca o il lead riattacca?
9. Quanti lead hanno risposto «più avanti», con quale motivo?
10. Quanti dicevano di essere soli a decidere e poi alla consulenza c'era un'altra persona?
11. Quanti hanno chiesto il prezzo? Cosa hanno risposto quando non lo abbiamo detto?
12. Quante consulenze sono arrivate preparate (sapevano chi eravamo) rispetto a prima?

### 5.3 Per Marco: decisioni ancora aperte

1. ⚠️ Ruolo del setter in presentazione.
2. ⚠️ Durata della consulenza da dichiarare.
3. ⚠️ Chi prova per primo, e se la parte "è una priorità per te?" va nella versione morbida o in quella decisa.
4. ⚠️ Approvazione dei testi dei due template Meta (A e B) prima dell'invio.
5. ⚠️ Cosa succede se la promessa «tra pochi minuti» non regge: si passa a «entro un'ora» o alla modalità B anche in orario?
6. ⚠️ Quante volte il bot riprova i due orari in modalità B (proposta: due).
7. ⚠️ Se i lead non ancora verificati contano ai fini delle provvigioni dei setter (oggi no): vale anche per le telefonate "scelte dal lead"?
8. ⚠️ Cosa facciamo con chi risponde «non l'ho lasciato io» ma poi si rifà vivo.
9. ⚠️ Una regola unica sul prezzo per tutti i canali, per esempio anche per i post e le VSL: oggi dice «non si dice» solo per bot e setter.
10. ⚠️ Chi vince quando il lead sta chattando col bot e il setter vuole chiamare.

### 5.4 Per chi sviluppa (WeCRM) e per il collaudo

**Da sviluppare** (elenco dalla bozza del 29/10, da fare solo dopo l'approvazione). Il lavoro è nel ramo `bot-primo-contatto` di WeCRM: ⚠️ da verificare a che punto è.

| # | Cosa | Note |
|---|---|---|
| 1 | Due template Meta (A e B) al posto di `wecrm_primo_contatto_v1` | Variabili: fonte, giorno, orari. L'approvazione di Meta richiede giorni. Il vecchio resta attivo finché non arriva. |
| 2 | Scelta della modalità all'arrivo del lead | Setter in turno e timbrato → A, altrimenti B. Riusa la logica dello smistamento. |
| 3 | Rete di sicurezza in A | Avviso sulla Home di Andrea a 10 minuti. A 30 minuti il sistema manda i due orari. |
| 4 | Prompt ridotto del bot | Sostituisce `billing_settings.whatsapp_bot_system_prompt`. Copia dell'attuale già in `bot_prompt_versioni`. |
| 5 | Nuovo strumento `proponi_telefonata` | È la strada «Chiamami» di oggi, in due passi: fascia, poi due orari lontani nella fascia. Richiamabile anche a parole. |
| 6 | Primo sollecito dopo «Non risponde» | Template `wecrm_sollecito_tentativo` da rifare. |
| 7 | Handoff «preferisce chat» al setter in turno | La chat compare tra i lead da lavorare e si chiude con gli stessi esiti di una chiamata. |
| 8 | KPI | Risposta alla prima chiamata divisa per modalità A/B e per telefonate "Ha scelto lui l'orario". |
| 9 | Banco di prova del bot | Rifare i casi: i 100 casi assurdi del 29/09 valutano ancora la qualifica. |

**Da verificare con i dati**
1. Quanto tempo passa oggi tra l'arrivo del lead e la prima chiamata? Quanto spesso rispettiamo i 5 minuti?
2. Quanti lead arrivano in modalità A e quanti in modalità B, in una settimana tipo?
3. Il calendario turni è affidabile? Un setter timbrato è davvero al telefono?
4. Il template A e il template B superano l'approvazione di Meta al primo invio?
5. Il bot rispetta le regole nuove: nessuna cifra, nessun nome, una domanda per messaggio, massimo 2 righe, massimo 1 emoji?
6. Nei casi "come oggi" (già cliente, taglio, spam, minorenne, eventi) il comportamento non è cambiato?
7. Il numero «sconosciuto» che chiama viene riconosciuto dai lead come WeGest, grazie all'avviso nel messaggio?
8. La telefonata "Ha scelto lui l'orario" compare in cima a «Da fare» all'ora giusta, senza assegnatario?

---

## 6. Come proviamo (proposta)

1. **Approvazione.** Marco e Andrea decidono i punti ⚠️ del paragrafo 5.3 e correggono direttamente il Google Doc dei consulenti.
2. **Call con i consulenti** martedì 3/11 alle 12 (da confermare): domande del paragrafo 5.1.
3. **Un setter, una settimana, tutti i lead in entrata.** Gli altri continuano come oggi, così abbiamo un confronto.
4. **Si registrano le chiamate** (vivavoce e computer, come detto al kick-off) e si mandano ai consulenti nella chat di gruppo. Per giudicare lo script l'audio serve più dei numeri.
5. **Si misura** nella dashboard dei setter: risposta alla prima chiamata, demo prenotate su lead raggiunti, show-up (demo presentate su demo prenotate) e, più avanti, demo diventate clienti.
6. **Si decide dopo 3-4 settimane.** Con circa 4 lead al giorno una settimana è poca per un confronto vero.

---

## 7. Come misuriamo se funziona

| Indicatore | Oggi | Obiettivo |
|---|---|---|
| Risposta alla prima chiamata | circa 50% | 60-70% |
| Chiamate partite entro 5 minuti (modalità A) | ⚠️ da misurare | tutte, o quasi |
| Lead "mai chiamato" in modalità B | ⚠️ da misurare | in calo |
| Demo/consulenze prenotate su lead raggiunti | ⚠️ da misurare | in crescita rispetto al confronto |
| Show-up (consulenze presentate su prenotate) | ⚠️ da misurare | in crescita |
| Lead chiusi «non interessato» senza chiedere perché | 22 su 96 persi | vicino a 0 |
| Lead fuori target chiusi subito | 35 su 96 persi | chiusi in pochi secondi, senza 5-7 minuti di diagnosi |

---

## 8. Rischi da tenere d'occhio

- **La promessa «tra pochi minuti» non regge.** Se il setter ci mette più di 5 minuti, il lead si sente preso in giro. Per questo c'è la rete di sicurezza a 10 e 30 minuti.
- **Il lead che vuole solo scrivere.** Se lo costringiamo al telefono lo perdiamo. È il motivo per cui la domanda è ai consulenti.
- **Il setter che si sente "recitare".** Lo script è una struttura, non un copione parola per parola. Se suona finto, il lead lo sente.
- **Doppio contatto.** Bot e setter che scrivono e chiamano insieme.
- **Numeri non verificati.** Clienti attivi, «30% apre un secondo salone»: non vanno nello script finché non li rileggiamo.
- **Meta lenta ad approvare i template.** Nel frattempo il vecchio resta attivo e il percorso nuovo non parte.

---

## 9. Fonti

- Decisioni di Marco del 29/10/2026 (chat Claude Code).
- Bozza «Bot WhatsApp — nuovo primo contatto» (29/10/2026).
- Scheda «Script setter dei consulenti — come lo adattiamo a WeGest (ottobre 2026)».
- Documento per i consulenti «Per i consulenti sales — nuovo primo contatto e domande aperte» (29/10/2026).
- Audio e Google Doc «SCRIPT SETTER WEGEST» dei consulenti (29/10/2026).
- Kick-off consulenza sales del 06/10/2026.
