<properties urlDisplayName="Service Bus Queues" pageTitle="Come usare le code del bus di servizio (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Come usare le code del bus di servizio

Questa guida illustra come usare le code del bus di servizio. Gli esempi sono
scritti in JavaScript e utilizzano il modulo Node.js di Azure. Gli scenari
presentati includono **creazione di code, invio e ricezione di messaggi**, nonché
**eliminazione di code**. Per altre informazioni sulle code, vedere la sezione [Passaggi
successivi].

## Sommario

-   [Informazioni sulle code del bus di servizio][]
-   [Creare uno spazio dei nomi servizio][]
-   [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi][]
-   [Creazione di un'applicazione Node.js](#create-app)
-   [Configurare l'applicazione per l'uso del bus di servizio](#configure-app)
-   [Procedura: Creare una coda](#create-queue)
-   [Procedura: Inviare messaggi a una coda](#send-messages)
-   [Procedura: Ricevere messaggi da una coda](#receive-messages)
-   [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione](#handle-crashes)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Creazione di un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Servizio cloud Node.js] (usando Windows PowerShell) o [Sito Web con WebMatrix].

## <a name="configure-app"> </a>Configurare l'applicazione per l'uso del bus di servizio

Per usare il bus di servizio di Azure, è necessario scaricare 
e usare il pacchetto Azure Node.js che include un set di librerie di riferimento che
comunicano con i servizi REST del bus di servizio.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare la finestra di comando di **Windows PowerShell per Node.js** per
    passare alla cartella **c:\\node\\sbqueues\\WebRole1** in cui è stata creata
    l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe
    restituire un output simile al seguente:

        azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella
    **node\_modules**. All'interno di tale cartella individuare il pacchetto
    **azure**, che contiene le librerie necessarie per accedere
    alle code del bus di servizio.

### Importare il modulo

Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore
del file **server.js** dell'applicazione:

    var azure = require('azure');

### Configurazione di una connessione del bus di servizio di Azure

Il modulo azure leggerà le variabili di ambiente AZURE\_SERVICEBUS\_NAMESPACE e AZURE\_SERVICEBUS\_ACCESS\_KEY per recuperare le informazioni necessarie per la connessione al bus di servizio di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni dell'account durante la chiamata a **createServiceBusService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Servizio cloud Node.js con archiviazione].

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione]

## <a name="create-queue"> </a>Come creare una coda

L'oggetto **ServiceBusService** consente di usare le code. Il
codice seguente permette di creare un oggetto **ServiceBusService**. Aggiungerlo nella parte
superiore del file **server.js** dopo l'istruzione per l'importazione del 
module azure:

    var serviceBusService = azure.createServiceBusService();

Quando si effettua la chiamata a **createQueueIfNotExists** sull'oggetto **ServiceBusService**
, viene restituita la coda specificata, se esiste, o viene creata
una nuova coda con il nome specificato. Nel codice seguente viene usato
**createQueueIfNotExists** per creare o connettersi alla coda denominata
'myqueue':

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** supporta inoltre opzioni aggiuntive che
permettono di sostituire le impostazioni predefinite delle code, come ad esempio la
durata (TTL) dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la
dimensione massima della coda su 5 GB e una durata di 1 minuto:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite usando **ServiceBusService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **ServiceBusService** che usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Come inviare messaggi a una coda

Per inviare un messaggio a una coda del Bus di servizio, l'applicazione chiamerà il
metodo **sendQueueMessage** sull'oggetto **ServiceBusService**.
I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti
**BrokeredMessage** e hanno un set di proprietà standard, ad esempio
**Label** e **TimeToLive**, un dizionario usato per contenere le proprietà personalizzate specifiche
dell'applicazione e un corpo di dati arbitrari
dell'applicazione. Un'applicazione può impostare il corpo del messaggio
passando un valore stringa come messaggio, in modo da popolare le proprietà standard
necessarie con valori predefiniti.

L'esempio seguente illustra come inviare un messaggio di prova alla
coda denominata 'myqueue' usando **sendQueueMessage**:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

Le code del bus di servizio supportano messaggi di dimensioni massime pari a 256 KB,in cui la dimensione massima dell'intestazione,
che include le proprietà standard e personalizzate dell'applicazione, non può
superare 64 KB. Non esiste alcun limite al numero di messaggi
mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei
messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un
limite massimo di 5 GB.

## <a name="receive-messages"> </a>Come ricevere messaggi da una coda

I messaggi vengono ricevuti da una coda tramite il metodo **receiveQueueMessage**
sull'oggetto **ServiceBusService**. Per impostazione predefinita, i messaggi vengono
eliminati dalla coda non appena vengono letti. È tuttavia possibile leggere (peek)
e bloccare il messaggio senza eliminarlo dalla coda, impostando il
parametro facoltativo **isPeekLock** su **true**.

Il comportamento predefinito di lettura ed eliminazione del messaggio nell'ambito dell'
operazione di ricezione costituisce il modello più semplice ed è adatto per scenari in
cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso
di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il
consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima
dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come usato,
quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi,
il messaggio usato prima dell'arresto anomalo risulterà perso.

Se il parametro **isPeekLock** è impostato su **true**, l'operazione di ricezione viene
suddivisa in due fasi, in modo da consentire il supporto di applicazioni
che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una
richiesta, individua il messaggio successivo da usare, lo blocca per impedirne
la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione.
Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una 
successiva elaborazione, esegue la seconda fase del processo di ricezione chiamando 
il metodo **deleteMessage** e fornendo il messaggio 
da eliminare come parametro. Il metodo **deleteMessage** contrassegna
il messaggio come usato e lo rimuove dalla coda.

Nell'esempio seguente viene illustrato come ricevere ed elaborare messaggi 
usando la modalità **receiveQueueMessage**. Nell'esempio viene ricevuto ed
eliminato un messaggio, quindi viene ricevuto un messaggio con **isPeekLock** impostato
su true e infine il messaggio viene eliminato usando **deleteMessage**:

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso
di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se
un'applicazione ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo,
può chiamare il metodo **unlockMessage** sull'oggetto
**ServiceBusService**. In questo modo, il bus di servizio sbloccherà il
messaggio nella coda rendendolo nuovamente disponibile per la ricezione
da parte della stessa o da un'altra applicazione
consumer.

Al messaggio bloccato nella coda è inoltre associato un
timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del
timeout, ad esempio a causa di un arresto anomalo, il bus di
servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile
per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio
ma prima della chiamata al metodo **deleteMessage**, il messaggio verrà
nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo
**At-Least-Once**, per indicare che ogni messaggio verrà elaborato
almeno una volta ma che in determinate situazioni potrà essere recapitato
una seconda volta. Se lo scenario non tollera la doppia elaborazione,
gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione
per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà
**MessageId** del messaggio, che rimane costante in tutti i
tentativi di recapito.

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti
per altre informazioni.

-   Vedere il riferimento in MSDN: [Code, argomenti e sottoscrizioni][]
-   Repository [Azure SDK per Node] su GitHub.

  [Azure SDK per Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Passaggi successivi]: #next-steps
  [Informazioni sulle code del bus di servizio]: #what-are-service-bus-queues
  [Creare uno spazio dei nomi servizio]: #create-a-service-namespace
  [Recuperare le credenziali di gestione predefinite per lo spazio dei nomi]: #obtain-default-credentials
  [Creazione di un'applicazione Node.js]: #create-app
  [Configurare l'applicazione per l'uso del bus di servizio]: #configure-app
  [Procedura: Creare una coda]: #create-queue
  [Procedura: Inviare messaggi a una coda]: #send-messages
  [Procedura: Ricevere messaggi da una coda]: #receive-messages
  [Procedura: Gestire arresti anomali e messaggi illeggibili dell'applicazione]: #handle-crashes
  [Concetti relativi alle code]: ../../dotNet/Media/sb-queues-08.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Code, argomenti e sottoscrizioni]: http://msdn.microsoft.com/it-it/library/windowsazure/hh367516.aspx
  [Sito Web con WebMatrix]: /it-it/develop/nodejs/tutorials/web-site-with-webmatrix/
[Portale di gestione precedente]: ../../Shared/Media/previous-portal.png
  [Creazione e distribuzione di un'applicazione Node.js in un Sito Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-app-with-storage/
  [Applicazione Web Node.js con archiviazione]: /it-it/develop/nodejs/tutorials/web-site-with-storage/

