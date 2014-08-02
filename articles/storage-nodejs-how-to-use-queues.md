<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

Come utilizzare il Servizio di accodamento da Node.js
=====================================================

In questa guida viene illustrato come eseguire scenari comuni del Servizio di accodamento di Azure. Gli esempi sono scritti utilizzando l'API Node.js. Gli scenari presentati includono l'**inserimento**, la **visualizzazione**, il **recupero** e l'**eliminazione** dei messaggi in coda, oltre alle procedure di **creazione ed eliminazione di code**. Per ulteriori informazioni sulle code, fare riferimento alla sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sul Servizio di accodamento](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Creazione di un'applicazione Node.js](#create-app)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione](#configure-access)
-   [Configurazione di una stringa di connessione di archiviazione di Azure](#setup-connection-string)
-   [Procedura: Creare una coda](#create-queue)
-   [Procedura: Inserire un messaggio in una coda](#insert-message)
-   [Procedura: Visualizzare il messaggio successivo](#peek-message)
-   [Procedura: Rimuovere il messaggio successivo dalla coda](#get-message)
-   [Procedura: Cambiare il contenuto di un messaggio in coda](#change-contents)
-   [Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda](#advanced-get)
-   [Procedura: Recuperare la lunghezza della coda](#get-queue-length)
-   [Procedura: Eliminare una coda](#delete-queue)
-   [Passaggi successivi](#next-steps)

Informazioni sul Servizio di accodamento
----------------------------------------

Il Servizio di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione pari a 100 TB. Di seguito sono riportati gli utilizzi più comuni per il Servizio di accodamento.

-   Creazione di un backlog di lavoro da elaborare in modo asincrono
-   Recapito dei messaggi da un ruolo Web di Azure a un ruolo di lavoro

Concetti
--------

Il Servizio di accodamento contiene i componenti seguenti:

![Queue1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **Formato dell'URL:** è possibile fare riferimento alle code utilizzando il formato di URL seguente:

        http://storageaccount.queue.core.windows.net/queue  

    L'URL seguente fa riferimento a una delle code nel diagramma:

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. L'account di archiviazione è il livello più elevato dello spazio dei nomi per accedere alle code. La dimensione totale dei contenuti di BLOB, tabelle e code in un account di archiviazione non può superare 100 TB.

-   **Coda:** una coda contiene un set di messaggi. Tutti i messaggi devono essere inclusi in una coda.

-   **Messaggio:** un messaggio, in qualsiasi formato, con dimensione massima di 64 KB.

Creazione di un account di archiviazione di Azure
-------------------------------------------------

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito (È possibile creare un account di archiviazione anche [utilizzando l'API REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx)).

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.  Nella parte inferiore del pannello di navigazione fare clic su **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.

    ![Finestra di dialogo Creazione rapida](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  Nel campo URL, digitare un nome di sottodominio da utilizzare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

6.  Fare clic su **Create storage account**.

Creazione di un'applicazione Node.js
------------------------------------

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servizio cloud Node.js](/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/) (utilizzando Windows PowerShell) o [Sito Web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configurazione dell'applicazione per l'accesso all'archiviazione
----------------------------------------------------------------

Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Node.js Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare Node Package Manager (NPM) per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire il seguente output:

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella sarà presente il pacchetto **azure**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

    var azure = require('azure');

Configurazione di una connessione di archiviazione di Azure
-----------------------------------------------------------

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createQueueService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Servizio cloud Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Procedura: Creare una coda
--------------------------

La coda seguente crea un oggetto **QueueService** che consente di utilizzare le code.

    var queueService = azure.createQueueService();

Utilizzare il metodo **createQueueIfNotExists** che restituisce la coda specificata se esiste già o, in caso contrario, crea una nuova coda con il nome specificato.

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // Queue exists
        }
    });

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **QueueService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

     function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

     function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **QueueService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

Procedura: Inserire un messaggio in una coda
--------------------------------------------

Per inserire un messaggio in una coda, utilizzare il metodo **createMessage** per creare un nuovo messaggio e aggiungerlo alla coda.

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Message inserted
        }
    });

Procedura: Visualizzare il messaggio successivo
-----------------------------------------------

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peekMessages**. Per impostazione predefinita, **peekMessages** visualizza un singolo messaggio.

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Messages peeked
            // Text is available in messages[0].messagetext
        }
    });

> [WACOM.NOTE] Se si utilizza **peekMessage** in assenza di messaggi in coda non verrà visualizzato un errore, ma non verrà restituito alcun messaggio.

Procedura: Rimuovere il messaggio successivo dalla coda
-------------------------------------------------------

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Per impostazione predefinita, chiamando **getMessages**, si ottiene il messaggio successivo in una coda. Un messaggio restituito da **getMessages** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **deleteMessage**. Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **deleteMessage** immediatamente dopo l'elaborazione del messaggio.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Process the message in less than 30 seconds, the message
            // text is available in messages[0].messagetext 
            var message = messages[0]
            queueService.deleteMessage(queueName
                , message.messageid
                , message.popreceipt
                , function(error){
                    if(!error){
                        // Message deleted
                    }
                });
        }
    });

> [WACOM.NOTE] Se si utilizza **getMessage** in assenza di messaggi in coda non verrà visualizzato un errore, ma non verrà restituito alcun messaggio.

Procedura: Cambiare il contenuto di un messaggio in coda
--------------------------------------------------------

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. La coda seguente utilizza il metodo **updateMessage** per aggiornare un messaggio.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Got the message
            var message = messages[0];
            queueService.updateMessage(queueName
                , message.messageid
                , message.popreceipt
                , 10
                , { messagetext: 'in your message, doing stuff.' }
                , function(error){
                    if(!error){
                        // Message updated successfully
                    }
                });
        }
    });

Procedura: Opzioni aggiuntive per rimuovere i messaggi dalla coda
-----------------------------------------------------------------

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo **getMessages** per recuperare 15 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo for. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

    queueService.getMessages(queueName
        , {numofmessages: 15, visibilitytimeout: 5 * 60}
        , function(error, messages){
        if(!error){
            // Messages retreived
            for(var index in messages){
                // text is available in messages[index].messagetext
                var message = messages[index];
                queueService.deleteMessage(queueName
                    , message.messageid
                    , message.popreceipt
                    , function(error){
                        if(!error){
                            // Message deleted
                        }
                    });
            }
        }
    });

Procedura: Recuperare la lunghezza della coda
---------------------------------------------

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo **getQueueMetadata** chiede al servizio di accodamento di restituire i metadati relativi alla coda e la proprietà della risposta **approximatemessagecount** contiene un conteggio del numero di messaggi in coda. Il conteggio è solo approssimativo, poiché è possibile aggiungere o rimuovere messaggi dopo la risposta del servizio di accodamento.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // Queue length is available in queueInfo.approximatemessagecount
        }
    });

Procedura: Eliminare una coda
-----------------------------

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **deleteQueue** sull'oggetto coda.

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // Queue has been deleted
        }
    });

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Archivio [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) su GitHub

  [Node.js Cloud Service]: {localLink:2221} "Web App with Express"]: {localLink:2221} "Web App with Express"