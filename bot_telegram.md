##### 18/11/23, 22:41                                                                   Creare un Bot Telegram in pochi minuti! | Hackers Tribe
## Prima parte: chat bot
*Telegram ha due tipi di bot: chat bot e inline bot. I chat bot usano i comandi (come /start , /help ) per
interagire tra utenti. Gli inline bot possono invece essere chiamati da qualsiasi parte e aiutano gli utenti a
trovare e inviare contenuti dal vostro bot a qualsiasi chat che desiderano. In questa sede ci occupiamo dei
chat bot.*
## Step 0.1: Scaricare Node.js e npm
*Qui ci sono i link alla versione di [Node.js](https://hackerstribe.com/tag/node-js/) che useremo per questo workshop:

Windows: https://nodejs.org/dist/v6.6.o/node--v6.6.o--x64msi

MacOS: http://nodejs.org/dist/v6.6.o/node--v6.6.o.pkg

Linux: dovreste già sapere come procurarvi Node.js*

## Step 0.2: Telegram
*Prima di fare un [Bot Telegram](https://hackerstribe.com/tag/bot-telegram/), avrete bisogno di un **account Telegram**. Andate su [telegram.org/dl](https://desktop.telegram.org/) e
create il vostro account.*

##Step 1: Creiamo la directory di progetto
*Ora che abbiamo Node.js e npm installati e il nostro account Telegram è pronto, possiamo partire col
nostro lavoro. Aprite il vostro terminale preferito a digitate questi comandi:*

*`mkdir codeday-telegram-bot`*

*`cd codeday-telegram-bot`*

Adesso che abbiamo una directory per il nostro bot Telegram, abbiamo bisogno di un package.json , se
non siete neofiti di Node.js, non è necessario che vi spieghi a cosa serve. Altrimenti sappiate che molto
banalmente si può considerare come un descrittore del nostro progetto. Un file che indica nome,
descrizione e fra le altre cose anche le dipendenze (intese come pacchetti software) necessari al nostro
programma per funzionare.
Per crearlo molto semplicemente, dalla directory del nostro progetto lanciamo il comando:

`npm init`

Questo dirà al client **npm** di inizializzare la nostra project directory con un package.json predefinito.
Cliccate Invio a tutti i prompts, possiamo occuparci di “compilaro” più tardi.

## Step 2: Installate i pacchetti necessari
Per questo progetto, abbiamo bisogno soltanto di 1 pacchetto npm.
Un pacchetto npm è sostanzialmente un pezzo di codice nel registro npm che possiamo scaricare con un
semplice comando, npm install .
Noi andremo ad usare un pacchetto chiamato node-telegram-bot-api e possiamo installarlo così:
`npm install--save node-telegram-bot-api`

La parte --save indica a npm di salvare questo pacchetto nel nostro package.json cosicché sarà più
facile installarlo dopo, senza ulteriori complicanze.

## Step 3: creare il bot Telegram

Su Telegram, contattate **@botfather** e usate il comando /newbot per creare un nuovo bot.
Chiamatelo come preferite.

## Step 4: scriviamo un po di codice!

Adesso che abbiamo il nostro bot, possiamo usarlo per interfacciarci con Telegram e le sue API.
Create un file index.js nella vostra project directory, poi apritelo nel vostro text editor preferito.
Qui c’è qualche semplice codice “Hello world” che ho scritto per assistervi in questo step.

`var TelegramBot = require('node-telegram-bot-api'),
// Be sure to replace YOUR_BOT_TOKEN with your actual bot token on this line.
telegram = new TelegramBot("YOUR_BOT_TOKEN", { polling: true });
telegram.on("text", (message) => {
telegram.sendMessage(message.chat.id, "Hello world");
});`

Sostanzialmente, ciò che questo codice fa è includere la libreria che abbiamo scaricato da npm
precedentemente, poi istanziare un nuovo oggetto “bot Telegram” con il nostro token e dirgli di
interrogare costantemente Telegram in cerca di eventuali nuovi messaggi.
Poi lo ascolterà per l’evento “text” (che in questa libreria è un semplice messaggio di testo) e invierà un
messaggio alla chat nella quale il messaggio originario era stato salvato dicendo “Hello world”.
Ora, andate sul file index.js con questo comando:

`node index`

Mandate un messaggio al vostro bot. Dovrebbe rispondere con “Hello World”. Se lo fa, ottimo! Altrimenti
commentate sotto.

## Step 5: Implementare un semplice comando

Allo stato attuale, questo bot non è realmente utile.
Rendiamolo utile aggiungendo un comando /codeday che ci dica quando finisce il CodeDay!
Utilizzeremo due librerie in più qui, codeday-clear e moment .
Codeday-clear è la libreria ufficiale Node.js per interagire con le API di Clear.
Clear è il nostro sistema interno di gestione del CodeDay e ha un API molto semplice da usare. Lo
useremo per sapere la data finale del CodeDay e poi useremo moment per convertirla in un formato più
adatto.
Per installare queste librerie:

`npm install --save codeday-clear moment`

Nel nostro codice, abbiamo bisogno di includere queste due librerie e rappresentare un esempio “Clear”.
Da quando il Clear API richiede un accesso per Clear e un paio di API token/secret, ho creato una semplice
app con permessi limitati apposta per questo workshop.

`Token: 1YZiGaj3baaLU8IKVsASRIWaNF2oJNg0
Secret: 1COMnWyGnGBsNqkhaZ6WMBWB9UWZw6QZ`

Ecco come dovrebbe apparire:

`var Clear = require('codeday-clear'),
// Our sample app token and secret
clear = new Clear("1YZiGaj3baaLU8IKVsASRIWaNF2oJNg0", "1COMnWyGnGBsNqkhaZ6WMBWB9UWZw6Q
// moment is not a class, just a simple function
var moment = require('moment');`

Questo codice dovrebbe andare bene dopo che abbiamo già dichiarato l’istanza di Telegram. Ora che
abbiamo i nostri esempi con Clear e moment dichiarati, possiamo usarli in un semplice comando.
Sostituiamo il codice dentro l’evento “text” con qualcosa del genere:

`telegram.on("text", (message) => {
if(message.text.toLowerCase().indexOf("/codeday") === 0){
clear.getEventById("oo4QIuKQQTYA", (codedayEvent) => {
var endsAt = moment(codedayEvent.ends_at * 1000);
telegram.sendMessage(message.chat.id, "CodeDay ends " + endsAt.fromNow() + "!");
});
}
});`

Qui c’è un’analisi di quanto stiamo facendo:
– Verifichiamo con la funzione indexOf di JavaScript se il messaggio inizia con /codeday.
– Se è cosi, diremo alla libreria Clear di presidiare l’evento con l’ID oo4QIuKQQTYA.
– Usiamo la callback e poi analizziamo la data ends_at con moment. Moltiplichiamo per 1000 per
convertirlo in millisecondi (moment li comprende) e Clear invia questa data in un formato timestamp
UNIX.
– Usiamo la funzione fromNow di moment per ottenere il tempo relativo alla data “endsAt” e poi inviamo
tutto attraverso le API di Telegram.

## Step 6: Markdown

Telegram supporta i messaggi inviati con MarkDown, un modo semplice di formattare il testo. Facciamo
la data di fine in grassetto come esempio.
Nella versione Telegram di Markdown, delimiteremo il testo che vogliamo in grassetto con degli asterischi,
cosicché il messaggio dovrebbe apparire così:

>CodeDay ends *in 2 months*!

Per implementare questo è piuttosto facile: tutto quello che dobbiamo fare è delimitare la data di fine con
degli asterischi e trasmettere il parametro “parse_mode” al Telegram API e impostarlo su “Markdown”,
questo indicherà alla lib Telegram che vogliamo il testo come MarkDown.
Il codice modificato dovrebbe apparire in questo modo:

`telegram.sendMessage(message.chat.id, "CodeDay ends *" + endsAt.fromNow() + "*!", {
parse_mode: "Markdown"
});`

Ora eseguiamo il comando /codeday e dovrebbe apparire questo:

 ![Testo alternativo](https://hackerstribe.com/wp-content/uploads/2017/07/telegram-markdown.png)

 Telegram supporta anche corsivo, codice in linea, blocchi di codice e URL in linea insieme all’opzione
grassetto che abbiamo appena usato.

## Inline bot di Telegram

Questa parte spiegherà come trasformare questo bot in un bot in linea, che può essere richiamato da
qualsiasi parte e aiuta gli utenti a trovare e inviare contenuti al vostro bot. I Bot in linea infatti permettono
l’invio di contenuti più strutturati e di seguire determinati “flow” scritti all’interno del nostro bot per
svolgere le azioni più disparate.

## Step 0: Abilita la modalità linea in Botfather

I bot non hanno la modalità in linea abilitata di default, quindi avrai bisogno di inviare il
comando /setinline a Botfather per abilitarla.
Puoi usare qualsiasi cosa come segnaposto testuale, io ho usato “Search Codeday...” per il mio.


 ![Testo alternativo](https://hackerstribe.com/wp-content/uploads/2017/07/discussione-botfather-telegram.png)

Dopo aver abilitato la modalità in linea, potresti aver bisogno di riavviare qualsiasi Telegram client che stai
utilizzando per far si che le modifiche siano efficaci. Una volta che avete riavviato, digitate il vostro
username seguito da uno spazio. Se vedete il placeholder che avete impostato, siete pronti per il passo
successivo.

## Step 1: implementare query in linea

Ora abbiamo bisogno di indicare al nostro script come reagire alle query in linea.
Per far questo, ecco il codice “Hello World” creato per l’occasione.

`telegram.on("inline_query", (query) => {
telegram.answerInlineQuery(query.id, [
{
type: "article",
id: "testarticle",
title: "Hello world",
input_message_content: {
message_text: "Hello, world! This was sent from my super cool inline bot."
}
}
]);
});`

Come funziona:
Su una query in linea
Rispondi con una matrice con il risultato di 1 articolo
Con il titolo “Hello World” e invia il messaggio “Hello world! Questo è stato inviato dal mio superfigo bot
in linea”.

## Step 2: implementare le Clear API

Ora finalmente facciamo vedere gli eventi Codeday. Abbiamo bisogno di passare su ogni evento e
trasformarlo in un oggetto che Telegram API possa comprendere, quindi dovreste avere qualcosa del
genere:

`telegram.on("inline_query", (query) => {
var searchTerm = query.query.trim();
clear.getRegions((regions) => {
var queryResults = [ ];
regions.forEach((region) => {
if(region.name.toLowerCase().indexOf(searchTerm.toLowerCase()) !== -1 && region.cur
queryResults.push({
type: "article",
id: region.id,
title: "CodeDay " + region.name,
description: "Hosted at " + region.current_event.venue.full_address,
input_message_content: {
latitude: region.location.lat,
longitude: region.location.lng,
title: "CodeDay " + region.name,
address: region.current_event.venue.full_address
}
});
}
});
telegram.answerInlineQuery(query.id, queryResults);
});
});
`
Quello che stiamo facendo è:
– sopra una query in linea...
– chiedere a Clear una lista di eventi...
– trasformare questi eventi in oggetti che Telegram API sappia leggere
– fare in modo che il messaggio contenga il luogo dove CodeDay si trova.

##Parte III: conclusioni

Ora che padroneggiate le basi della creazione di un bot, vi invito a giocare con i bot API di Telegram e
fare qualcosa di divertente con essi! Potete fare giochi, tools, calcolatori... qualsiasi cosa, davvero.
Prendete confidenza con Node e telegram e vi renderete conto di quanto flessibile possa essere un Bot.
Date un’occhiata a questa pagina per vedere quante cose potete costruire.
Fateci sapere se vi è piaciuto e restate in attesa del prossimo articolo su come effettuare il deploy del
nostro bot su un server online.
