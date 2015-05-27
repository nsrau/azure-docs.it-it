<properties 
	pageTitle="Creare un processo aziendale" 
	description="Creare un processo aziendale" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service-logic" 
	documentationCenter=""/>



<tags
	ms.service="app-service-logic"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="stepsic"/>

# Sviluppo di un processo aziendale tramite le app di flusso e le app per le API

Dopo avere creato un flusso di base, verrà ora creato un processo aziendale tramite le app di flusso. In questo articolo verranno trattati diversi aspetti chiave:

- Aggiunta della logica condizionale (ad esempio, eseguire un'azione solo se viene soddisfatta una determinata condizione) e aggiunta di cicli
- App per le API di BizTalk
- Uso della visualizzazione codice per modificare un flusso
- Diversi tipi di trigger

Per iniziare, fare clic qui per creare un elemento nella sottoscrizione che dispone di un flusso di base già compilato. Dopo aver creato questo flusso, fare clic sui trigger e sulle azioni per modificarlo.

## Aggiunta di logica condizionale e aggiunta di cicli

Anche se funziona correttamente, il flusso di base presenta alcuni problemi. Innanzitutto, invia tramite posta elettronica solo il tweet superiore, non tutti i tweet relativi alla parola chiave. Per ripetere un'azione per un elenco di elementi è possibile usare la proprietà "ripetizione".

### Ripetizione

La ripetizione esegue l'azione per ciascun elemento di un elenco di elementi. Per usare la ripetizione, fare clic sull'icona della freccia circolare nella parte superiore dell'azione. Viene visualizzata una casella di testo. In tale casella, digitare:

    @triggers().outputs.body

Come con il flusso di base, viene restituito un elenco di tweet. Quindi, per usare effettivamente il tweet nell'azione, sostituire il messaggio con:

    @repeatItem().message

"repeatItem()" è una funzione che fornisce ciascun elemento dell'elenco. Fare clic sul segno di spunta.

### Condizionale

A questo punto, il flusso potrebbe presentare ancora problemi perché determinerà l'invio di una notevole quantità di messaggi di posta elettronica. È possibile aggiungere più logica in modo da ricevere un messaggio di posta elettronica solo quando la persona che effettua il tweet dispone di un determinato numero di follower. A questo scopo, fare clic sull'icona della freccia circolare nella parte superiore dell'azione.

All'interno della casella di testo digitare:

    @greaterThan(repeatItem().person.followers, 1000)

Viene mostrata la funzione repeatItem(), ma anche una nuova funzione denominata greaterThan(). Questa funzione confronta due valori e consentirà l'esecuzione dell'azione solo se il primo è maggiore del secondo. Si noti la sintassi per il recupero dei follower: per ogni proprietà desiderata aggiungere un punto seguito dal nome della proprietà. Fare clic sul segno di spunta per salvare la condizione.

## App per le API di BizTalk

In Azure sono disponibili poche categorie di app per le API diverse. Twitter e Office 365 sono eccellenti connettori per questo scenario, ma sono disponibili anche numerose app per le API fornite da BizTalk che è possibile sfruttare nel flusso.

Fare clic sul servizio XXXXXX nel riquadro di destra. Verrà aggiunto questo servizio, con tecnologia BizTalk, nella finestra di progettazione.

### Passaggio di dati tra azioni

Per usare più azioni in un flusso, è necessario passare dati tra le azioni. A questo scopo, usare la funzione actions().

Dopo aver aggiunto l'azione XXXXXX, digitare:

    @actions(‘SendMail’).outputs.YYYYYY

In questo modo la proprietà YYYYYY verrà trasmessa dagli output dell'azione SendMail. È sempre possibile visualizzare il nome di un'azione guardando direttamente sotto l'icona relativa a tale l'azione. Analogamente, è possibile visualizzare tutti gli output per l'azione nella parte inferiore della finestra.

## Uso della visualizzazione codice per modificare un flusso

Oltre alla finestra di progettazione, è possibile modificare direttamente la definizione di un flusso in modo manuale. A tale scopo, fare clic sul pulsante Visualizzazione codice nella barra dei comandi. Verrà aperto un editor completo che mostra la definizione del flusso appena modificato.

Un editor di testo può semplificare determinate operazioni; ad esempio, è possibile copiare e incollare qualsiasi numero di azioni all'interno del flusso stesso (o anche tra flussi). È anche possibile aggiungere o rimuovere intere sezioni dal flusso contemporaneamente.

### Parametri

Sono inoltre disponibili alcune funzionalità che sono esposte solo nella visualizzazione codice, ad esempio i parametri. I parametri semplificano il riutilizzo dei valori in un flusso. Ad esempio, se si dispone di un indirizzo e-mail che si vuole includere in diverse azioni, è possibile trasformarlo in un parametro.

Per definire i parametri, passare all'oggetto {} dei parametri e aggiungere al suo interno: "emailAddress" : { "type" : "string", "defaultValue" : "me@example.com" }

A questo punto, scorrere fino all'azione di invio tramite posta elettronica. Verrà visualizzato l'input per l'invio tramite posta elettronica; sostituire l'indirizzo e-mail con:

    @parameters(‘emailAddress’)

I parametri costituiscono un buon metodo per estrarre valori che probabilmente verranno modificati molto.

## Diversi tipi di trigger

Esistono alcuni modi diversi per avviare i flussi, ciascuno dei quali presenta un comportamento leggermente differente; assicurarsi, pertanto, di scegliere quello appropriato per i propri scenari.

### Trigger di polling

Alcuni trigger eseguono un controllo su un'app per le API in base a un determinato intervallo. È possibile definire tale intervallo, ma è anche necessario passare un campo denominato "triggerState". Lo stato di trigger è rappresentato dalle informazioni da passare alla chiamata del trigger in modo che quest'ultimo rilevi ciò che ha restituito al controllo precedente. Lo stato di trigger è in genere la stringa:

    @triggers().outputs.body.triggerState

### Trigger di callback

Altri trigger chiamano di nuovo il flusso quando hanno una nuova richiesta. In questo caso è necessario assegnare al trigger l'URI di callback del flusso. Per ottenere questa copia accedere all'endpoint dal pannello Proprietà della schermata Impostazioni quando si passa a un flusso.

### Chiamata manuale

In terzo luogo, è possibile eseguire manualmente un flusso. Nel portale è disponibile un pulsante Esegui che è possibile selezionare per avviare un flusso.

<!--HONumber=54-->