<properties 
	pageTitle="Usare le funzionalità delle app per la logica" 
	description="Informazioni su come usare le funzionalità avanzate delle app per la logica." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="stepsic"/>
	
# Usare le funzionalità delle app per la logica

Nell'[argomento precedente][Create a new logic app] è stata creata la prima app per la logica. In questa fase verrà mostrato come creare un processo più completo usando le app per la logica del servizio app. Questo argomento introduce i nuovi concetti relativi alle app per la logica elencati di seguito:

- Logica condizionale, che esegue un'azione solo quando viene soddisfatta una determinata condizione.
- Ripetizione di azioni.
- Visualizzazione del codice per modificare un'app per la logica esistente.
- Opzioni per avviare un flusso di lavoro.

Prima di completare questo argomento, completare i passaggi elencati nell'articolo [Creare una nuova app per la logica]. Nel [portale di Azure], passare all'app per la logica e fare clic su **Trigger e azioni** nel riepilogo per modificare la definizione di app per la logica.

## Aggiunta di logica condizionale e ripetizione

Benché il flusso originale funzioni, vi sono alcune aree che è possibile migliorare. Innanzitutto, l'azione invia all'utente solo il primo tweet restituito. Logicamente, l'utente vorrebbe ricevere tutti i tweet contenenti la parola chiave. Per ripetere un'azione per un elenco di voci, ad esempio i tweet restituiti, è necessario usare la proprietà `repeat`.

### Ripetizione
La ripetizione esegue l'azione per ciascun elemento di un elenco di elementi. I passaggi successivi consentono di aggiornare l'azione esistente in modo da usare le ripetizioni, operazione sicuramente consigliabile per un elenco di tweet.

1. Tornare al flusso di lavoro creato e fare clic sul collegamento **Definizione** nelle **Informazioni di base**. 

2. Per modificare l'azione **Connettore Dropbox**, fare clic sull'icona a forma di matita.

3. Fare clic sull'icona a forma di ingranaggio e selezionare **Ripetere in un elenco**.
 
2. Accanto alla casella **Ripeti** fare clic su `...` e selezionare **Corpo**. L'input verrà inserito:

    	@body('twitterconnector')

	Nella casella di testo. Questa funzione produce un elenco di tweet.

3. Selezionare tutto il testo nella casella di testo **Contenuto** ed eliminarlo. Quindi, fare clic su `...` e selezionare **Testo tweet**. Verrà inserita la funzione **repeatItem()**, che restituisce ogni elemento presente in elenco.

Da notare, infine, che gli output delle azioni ripetute sono speciali. Se si intende fare riferimento ai risultati dell'operazione Dropbox, ad esempio, *non* è possibile eseguire la normale funzione `@actions('dropboxconnector').outputs.body`, invece viene eseguita la funzione `@actions('dropboxconnector').outputs.repeatItems`: Questa funzione restituisce un elenco di tutte le volte in cui è stata eseguita l'operazione, assieme agli output di ciascuna di esse. Ad esempio, `@first(actions('dropboxconnector').outputs.repeatItems).outputs.body.FilePath` restituirà il percorso del primo file caricato.

### Condizionale
Questa app per la logica implica il caricamento di moltissimi file su Dropbox. I passaggi seguenti consentono di aggiungere un'ulteriore logica per assicurarsi di ricevere un file solo quando il tweet riceve un determinato numero di retweet.

1. Fare clic sull'icona a forma di ingranaggio nella parte superiore dell'azione e selezionare **Aggiungere una condizione da soddisfare**.

2. Nella casella di testo, digitare quanto segue:

    	@greater(repeatItem().Retweet_Count , 5)
    
	La funzione **greater** confronta due valori e consente di eseguire l'azione solo quando il primo valore è maggiore del secondo. Si accede a una determinata proprietà come un punto (.) seguito dal nome della proprietà, come ad esempio `.Retweet_Count` sopra riportata.

3. Fare clic sul segno di spunta per salvare l'azione di Dropbox.

## Uso della visualizzazione codice per modificare un'app per la logica

Oltre alla finestra di progettazione, è possibile modificare direttamente il codice che definisce un'app per la logica, come segue.

1. Fare clic sul pulsante **Visualizzazione Codice** nella barra dei comandi. 

	Verrà aperto un editor completo che mostra la definizione appena modificata.

	![Visualizzazione del codice](./media/app-service-logic-use-logic-app-features/codeview.png)

    Usando l'editor di testo, è possibile copiare e incollare un numero qualsiasi di azioni all'interno della stessa app per la logica o tra app per la logica. È anche possibile aggiungere o rimuovere facilmente intere sezioni dalla definizione, oltre a condividere definizioni con altri.

2. Dopo aver apportato le modifiche nella visualizzazione codice, fare clic su **Salva**.

### Parametri
Vi sono alcune funzionalità delle app per la logica che possono essere usate solo in visualizzazione codice, come ad esempio i parametri. I parametri semplificano il riutilizzo dei valori all'interno di un'app per la logica. Ad esempio, se si ha un indirizzo e-mail che si vuole usare in diverse azioni, è consigliabile definirlo come parametro.

Il codice seguente consente di aggiornare l'app per la logica esistente in modo da usare parametri per il termine della query.

1. Nella visualizzazione codice individuare l'oggetto `parameters : {}` e inserire il seguente argomento:

	    "topic" : {
		    "type" : "string",
		    "defaultValue" : "MicrosoftAzure"
	    }
    
2. Scorrere fino all'azione `twitterconnector`, individuare il valore della query e sostituirlo con `@concat('#', parameters('topic'))`. La funzione **concat** unisce due o più stringhe.
 
3. Infine, passare all'azione `dropboxconnector` e aggiunger il parametro dell'argomento, come segue:

    	@concat('/tweets/', parameters('topic'), '/',repeatItem().TweetID,'.txt')

I parametri costituiscono un buon metodo per estrarre valori che probabilmente verranno modificati molto. Sono particolarmente utili quando è necessario eseguire l'override dei parametri in diversi ambienti. Per altre informazioni su come eseguire l'override dei parametri in base all'ambiente, vedere la [documentazione sulle API REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

A questo punto, quando si fa clic su **Salva**, ogni ora tutti i nuovi tweet con più di 5 retweet verranno recapitati in una cartella denominata **tweet** nel proprio Dropbox.

## Avvio di un flusso di lavoro dell'app per la logica
Vi sono diverse opzioni per avviare il flusso di lavoro definito nell'app per la logica. Un flusso di lavoro può sempre essere avviato su richiesta nel [portale di Azure].

### Trigger di ricorrenza
Un trigger di ricorrenza eseguito a un intervallo specificato dall'utente. Quando il trigger è caratterizzato dalla logica condizionale, determina se è necessario o no eseguire il flusso di lavoro. Un trigger indica che deve essere eseguito restituendo un codice di stato `200`. Quando non è necessario eseguirlo, restituisce il codice di stato `202`.

### Callback tramite le API REST
I servizi possono chiamare un endpoint dell'app per la logica per avviare un flusso di lavoro. È possibile trovare l'endpoint per accedere passando al pannello **Proprietà** dal pulsante **Impostazioni** sulla barra dei comandi dell'app per la logica.

È possibile usare il callback per richiamare un'app per la logica all'interno dell'applicazione personalizzata. È necessario usare l'autenticazione **di base**. Il nome utente `default` viene creato per l'utente e la password è il campo **Chiave di accesso primaria** nel pannello **Proprietà**. ad esempio:

        POST https://default:<<your primary access key>>@<< your endpoint>>/run?api-version=2015-02-01-preview
        Content-type: application/json
        {
            "name" : "nameOfTrigger",
            "outputs" : { "property" : "value" }
        }

È possibile passare gli output al flusso di lavoro e farvi riferimento nel flusso di lavoro. Ad esempio, con il trigger sopra riportato, se si include `@triggers().outputs.property` si otterrà `value`.

Per altre informazioni, vedere la [documentazione di REST](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409).

### Esecuzione manuale
È possibile definire un'app per la logica che non ha un trigger. In questo caso, il flusso di lavoro deve essere avviato su richiesta. Questo tipo di app per la logica è più adatto a un processo che necessita di essere eseguito solo a intermittenza. Per creare un'app per la logica senza un trigger, selezionare la casella **Esegui app per la logica manualmente** in **Avvia app per la logica** nella finestra di progettazione.

Per avviare l'app per la logica su richiesta, fare clic sul pulsante **Esegui adesso**sulla barra dei comandi.

<!-- Shared links -->
[Create a new logic app]: app-service-logic-create-a-logic-app.md
[Creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[portale di Azure]: https://portal.azure.com
<!--HONumber=54-->