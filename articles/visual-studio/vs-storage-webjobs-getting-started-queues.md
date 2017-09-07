---
title: Introduzione all'archiviazione code e ai Servizi connessi di Visual Studio (progetti processo Web) | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio di archiviazione di accodamento di Azure in un progetto WebJob dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio.
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ad43b5bb4f6f51f25acb9b2160661addab481762
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Introduzione all'archiviazione di accodamento di Azure e ai servizi relativi a Visual Studio (progetti WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica
Questo articolo descrive come iniziare a usare l'archiviazione code di Azure in un progetto di tipo processo Web di Azure in Visual Studio dopo avere creato o fatto riferimento a un account di archiviazione di Azure usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio. Quando si aggiunge un account di archiviazione a un progetto WebJob tramite la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio, vengono installati i pacchetti NuGet di archiviazione di Azure appropriati, i riferimenti .NET appropriati vengono aggiunti al progetto e le stringhe di connessione per l'account di archiviazione vengono aggiornate nel file App.config.  

Questo articolo fornisce esempi di codice C# che illustrano come usare Azure WebJobs SDK versione 1.x con il servizio di archiviazione di accodamento di Azure.

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per altre informazioni, vedere [Introduzione all'archiviazione code di Azure con .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) . Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Come attivare una funzione quando viene ricevuto un messaggio in coda
Per scrivere una funzione che viene chiamata da WebJobs SDK quando viene ricevuto un messaggio di coda, usare l'attributo **QueueTrigger** . Il costruttore dell'attributo accetta un parametro di stringa che specifica il nome della coda di cui eseguire il polling. Per informazioni su come impostare il nome della coda in modo dinamico, vedere l'argomento relativo a [come impostare le opzioni di configurazione](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Messaggi stringa in coda
Nell'esempio seguente, la coda contiene una stringa di messaggio, in modo che l'attributo **QueueTrigger** venga applicato a un parametro di stringa denominato **logMessage** che include il contenuto del messaggio in coda. La funzione [scrive un messaggio di log nel dashboard](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Oltre a **string**, il parametro può essere una matrice di byte, un oggetto **CloudQueueMessage** o un oggetto POCO definito dall'utente.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Nell'esempio seguente, il messaggio in coda contiene JSON per un oggetto **BlobInformation** che include una proprietà **BlobName**. L'SDK deserializza automaticamente l'oggetto.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

L'SDK usa il pacchetto [Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano messaggi di coda in un programma che non usa WebJobs SDK, è possibile scrivere codice simile a quello del seguente esempio per creare un messaggio di coda POCO analizzabile dall'SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Funzioni asincrone
La seguente funzione asincrona [scrive un log nel dashboard](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Le funzioni asincrone possono usare un [token di annullamento](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), come illustrato nel seguente esempio che copia un BLOB. Per una spiegazione del segnaposto **queueTrigger**, vedere la sezione relativa ai [BLOB](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message).

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>Tipi usati dall'attributo QueueTrigger
È possibile usare **QueueTrigger** con i seguenti tipi:

* **string**
* Tipo POCO serializzato come JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo di polling
L'SDK implementa un algoritmo di backoff esponenziale casuale per ridurre l'effetto del polling delle code inattive sui costi delle transazioni di archiviazione.  Quando viene trovato un messaggio, l'SDK attende due secondi e controlla la presenza di un altro messaggio. Quando non viene trovato alcun messaggio, rimane in attesa per circa quattro secondi prima di riprovare. Dopo alcuni tentativi non riusciti per ottenere un messaggio nella coda, il tempo di attesa continua ad aumentare finché non raggiunge il tempo massimo di attesa, che per impostazione predefinita è di un minuto. [Il tempo di attesa massimo è configurabile](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Più istanze
Se l'app Web viene eseguita in più istanze, in ogni computer i processi Web verranno eseguiti in modalità continua e ogni computer attenderà i trigger e proverà a eseguire le funzioni. Poiché in alcuni scenari è possibile che per questo motivo alcune funzioni elaborino gli stessi dati due volte, le funzioni dovrebbero essere idempotent (scritte in modo tale che, anche se chiamate più volte con gli stessi dati di input, non generano risultati duplicati).  

## <a name="parallel-execution"></a>Esecuzione parallela
Se si dispongono di più funzioni in ascolto in code diverse, l'SDK le chiamerà in parallelo quando i messaggi vengono ricevuti simultaneamente.

Lo stesso vale quando si ricevono più messaggi per una singola coda. Per impostazione predefinita, l'SDK ottiene un batch di 16 messaggi in coda alla volta ed esegue la funzione che li elabora in parallelo. [La dimensione del batch è configurabile](#how-to-set-configuration-options). Quando il numero elaborato viene ridotto alla metà della dimensione del batch, l'SDK ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è pari a una volta e mezza la dimensione del batch. Questo limite si applica separatamente a ogni funzione caratterizzata da un attributo **QueueTrigger** . Se non si vuole avviare l'esecuzione parallela per i messaggi ricevuti su una coda, impostare le dimensioni del batch su 1.

## <a name="get-queue-or-queue-message-metadata"></a>Ottenere i metadati della coda o del messaggio in coda
Con l'aggiunta di parametri alla firma del metodo, è possibile ottenere le proprietà del messaggio seguenti:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (contiene il testo del messaggio)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Per lavorare direttamente con l'API del servizio di archiviazione di Azure, è anche possibile aggiungere un parametro **CloudStorageAccount** .

Nell'esempio seguente tutti questi metadati vengono scritti in un log applicazione INFO. Nell'esempio, gli attributi logMessage e queueTrigger includono il contenuto del messaggio in coda.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Ecco un log di esempio scritto dal codice di esempio:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Arresto normale
Una funzione che viene eseguita in un processo Web continuo può accettare un parametro **CancellationToken** che consente al sistema operativo di notificare la funzione quando il processo Web sta per essere terminato. È possibile usare questa notifica per assicurarsi che la funzione non termini in modo imprevisto lasciando i dati in uno stato incoerente.

L'esempio seguente illustra come controllare la chiusura imminente di un processo Web in una funzione.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Nota:** è possibile che il dashboard non mostri correttamente lo stato e il risultato delle funzioni che sono state interrotte.

Per altre informazioni, vedere l'articolo sull' [arresto normale dei processi Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Come creare un messaggio di coda durante l'elaborazione di un messaggio di coda
Per scrivere una funzione che crea un nuovo messaggio di coda, usare l'attributo **Queue** . Come per l'attributo **QueueTrigger**, si passa il nome della coda come stringa oppure è possibile [configurare dinamicamente il nome della coda](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Messaggi stringa in coda
Il seguente esempio di codice non asincrono crea nella coda denominata "outputqueue" un nuovo messaggio di coda con lo stesso contenuto del messaggio di coda ricevuto nella coda denominata "inputqueue". Per le funzioni asincrone, usare **IAsyncCollector<T>** come illustrato più avanti in questa sezione.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Per creare un messaggio di coda contenente un oggetto POCO anziché una stringa, passare il tipo POCO come parametro di output al costruttore dell'attributo **Queue** .

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

L'SDK deserializza automaticamente l'oggetto in JSON. Viene sempre creato un messaggio in coda, anche se l'oggetto è null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Creare più messaggi o in funzioni asincrone
Per creare più messaggi, impostare il tipo di parametro per la coda di output **ICollector<T>** o **IAsyncCollector<T>**, come illustrato nell'esempio seguente.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Ogni messaggio in coda viene creato immediatamente quando viene chiamato il metodo **Add** .

### <a name="types-that-the-queue-attribute-works-with"></a>Tipi usati dall'attributo Queue
È possibile usare l'attributo **Queue** nei seguenti tipi di parametri:

* **out string** (crea il messaggio di coda se il valore del parametro è diverso da null quando termina la funzione)
* **out byte** (funziona come **string**)
* **out CloudQueueMessage** (funziona come **string**)
* **out POCO** (tipo serializzabile, crea un messaggio con un oggetto null se il parametro è null quando termina la funzione)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (per la creazione manuale dei messaggi direttamente con l'API di archiviazione di Azure)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Usare gli attributi di WebJobs SDK nel corpo di una funzione
Se è necessario eseguire alcune operazioni nella funzione prima di usare un attributo di WebJobs SDK come **Queue**, **Blob** o **Table**, è possibile usare l'interfaccia **IBinder**.

Nell'esempio seguente viene accettato un messaggio di coda di input e creato un nuovo messaggio con lo stesso contenuto in una coda di output. Il nome della coda di output viene impostato dal codice nel corpo della funzione.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

L'interfaccia **IBinder** può essere usata anche con gli attributi **Table** e **Blob**.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Come leggere e scrivere BLOB e tabelle durante l'elaborazione di un messaggio in coda
Gli attributi **Blob** e **Table** consentono di leggere e scrivere BLOB e tabelle. Gli esempi in questa sezione si applicano ai BLOB. Per esempi di codice che illustrano come attivare processi quando i BLOB vengono creati o aggiornati, vedere [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) e per esempi di codice che leggono e scrivono tabelle, vedere [Come usare il servizio di archiviazione tabelle di Azure con WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### <a name="string-queue-messages-triggering-blob-operations"></a>Messaggi di coda stringa che attivano operazioni BLOB
Per un messaggio in coda che contiene una stringa, **queueTrigger** è un segnaposto che è possibile usare nel parametro **blobPath** dell'attributo **Blob** che include il contenuto del messaggio.

Nell'esempio seguente vengono utilizzati oggetti **Stream** per leggere e scrivere i BLOB. Il messaggio di coda è il nome di un BLOB presente nel contenitore textblobs. Una copia del BLOB con "-new" aggiunto al nome viene creata nello stesso contenitore.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Il costruttore dell'attributo **Blob** usa un parametro **blobPath** che specifica il contenitore e il nome del BLOB. Per altre informazioni su questo segnaposto, vedere [Come usare il servizio di archiviazione BLOB di Azure con WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md).

Quando l'attributo decora un oggetto **Stream**, un altro parametro del costruttore specifica la modalità **FileAccess** come lettura, scrittura o lettura/scrittura.

Nell'esempio seguente viene utilizzato un oggetto **CloudBlockBlob** per eliminare un BLOB. Il messaggio di coda è il nome del BLOB.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messaggi di coda POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Per un oggetto POCO archiviato nel formato JSON nel messaggio della coda, è possibile usare i segnaposto che denominano le proprietà dell'oggetto nel parametro **blobPath** dell'attributo **Queue**. È anche possibile usare nomi di proprietà dei metadati di coda come segnaposto. Vedere [Ottenere i metadati della coda o del messaggio in coda](#get-queue-or-queue-message-metadata).

Il seguente esempio copia un BLOB in un nuovo BLOB con un'estensione diversa. Il messaggio di coda è un oggetto **BlobInformation** che include le proprietà **BlobName** e **BlobNameWithoutExtension**. I nomi delle proprietà vengono usati come segnaposto nel percorso BLOB per gli attributi **Blob** .

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

L'SDK usa il pacchetto [Newtonsoft.Json NuGet](http://www.nuget.org/packages/Newtonsoft.Json) per serializzare e deserializzare i messaggi. Se si creano messaggi di coda in un programma che non usa WebJobs SDK, è possibile scrivere codice simile a quello del seguente esempio per creare un messaggio di coda POCO analizzabile dall'SDK.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Se è necessario eseguire alcune operazioni nella funzione prima di associare un BLOB a un oggetto, è possibile usare l'attributo nel corpo della funzione, come mostrato in [Usare gli attributi di WebJobs SDK nel corpo di una funzione](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipi con cui è possibile usare l'attributo Blob
L'attributo **Blob** può essere usato con i seguenti tipi:

* **Stream** (lettura o scrittura, specificata tramite il parametro del costruttore FileAccess)
* **TextReader**
* **TextWriter**
* **string** (lettura)
* **out string** (scrittura; crea un BLOB solo se il parametro di stringa è diverso da null quando la funzione restituisce un valore)
* POCO (lettura)
* out POCO (scrittura; crea sempre un BLOB come oggetto null se il parametro POCO è null quando la funzione restituisce un valore)
* **CloudBlobStream** (scrittura)
* **ICloudBlob** (lettura o scrittura)
* **CloudBlockBlob** (lettura o scrittura)
* **CloudPageBlob** (lettura o scrittura)

## <a name="how-to-handle-poison-messages"></a>Come gestire i messaggi non elaborabili
I messaggi il cui contenuto comporta l'esito negativo di una funzione sono denominati *messaggi non elaborabili*. Quando la funzione non riesce, il messaggio in coda non viene eliminato e infine viene prelevato, causando la ripetizione del ciclo. L'SDK può interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile farlo manualmente.

### <a name="automatic-poison-message-handling"></a>Gestione automatica dei messaggi non elaborabili
L'SDK chiamerà una funzione fino a 5 volte per elaborare un messaggio nella coda. Se il quinto tentativo non riesce, il messaggio viene spostato in una coda non elaborabile. È possibile vedere come configurare il numero massimo di tentativi nell'argomento relativo a [come impostare le opzioni di configurazione](#how-to-set-configuration-options).

La coda non elaborabile è denominata *{nomecodaoriginale}*-poison. È possibile scrivere una funzione per elaborare i messaggi dalla coda non elaborabile archiviandoli o inviando una notifica della necessità di un intervento manuale.

Nell'esempio seguente, la funzione **CopyBlob** non riesce quando un messaggio della coda contiene il nome di un BLOB inesistente. In questo caso, il messaggio viene spostato dalla coda copyblobqueue alla coda non elaborabile copyblobqueue-poison. **ProcessPoisonMessage** registra quindi il messaggio non elaborabile.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

Nella figura seguente viene illustrato l'output di console di queste funzioni quando viene elaborato un messaggio non elaborabile.

![Output di console per la gestione dei messaggi non elaborabili](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Gestione manuale dei messaggi non elaborabili
È possibile ottenere il numero di volte in cui un messaggio è stato selezionato per l'elaborazione aggiungendo un parametro **int** denominato **dequeueCount** alla funzione. Sarà quindi possibile controllare il conteggio di rimozione dalla coda nel codice della funzione ed eseguire la gestione dei messaggi non elaborabili quando il numero supera una certa soglia, come illustrato nell'esempio seguente.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a name="how-to-set-configuration-options"></a>come impostare le opzioni di configurazione
È possibile usare il tipo **JobHostConfiguration** per impostare le opzioni di configurazione seguenti:

* Impostare le stringhe di connessione SDK nel codice.
* Configurare le impostazioni di **QueueTrigger** , ad esempio il conteggio rimozione dalla coda.
* Ottenere i nomi delle code dalla configurazione.

### <a name="set-sdk-connection-strings-in-code"></a>Impostare le stringhe di connessione SDK nel codice
Impostare le stringhe di connessione SDK nel codice per poter usare i propri nomi di stringa di connessione nei file di configurazione o nelle variabili di ambiente, come illustrato nell'esempio seguente.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="configure-queuetrigger--settings"></a>Configurare le impostazioni di QueueTrigger
È possibile configurare le seguenti impostazioni valide per l'elaborazione del messaggio di coda:

* Numero massimo di messaggi in coda che vengono prelevati contemporaneamente per l'esecuzione in parallelo (il valore predefinito è 16).
* Numero massimo di tentativi prima che un messaggio nella coda venga inviato a una coda non elaborabile (il valore predefinito è 5).
* Tempo massimo di attesa prima di eseguire nuovamente il polling quando una coda è vuota (il valore predefinito è 1 minuto).

L'esempio seguente illustra come configurare queste impostazioni:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Impostare i valori per i parametri del costruttore WebJobs SDK nel codice
A volte si desidera specificare un nome di coda, un nome di BLOB o un contenitore oppure un nome di tabella nel codice anziché impostarlo come hardcoded. È ad esempio possibile specificare il nome della coda per **QueueTrigger** in una variabile di ambiente o in un file di configurazione.

A tale scopo, passare un oggetto **NameResolver** al tipo **JobHostConfiguration**. Includere segnaposto speciali racchiusi tra segni di percentuale (%) nei parametri del costruttore dell'attributo di SDK e il codice **NameResolver** specifica i valori effettivi da usare in sostituzione di questi segnaposto.

Si supponga, ad esempio, di voler usare una coda denominata logqueuetest nell'ambiente di test e una coda denominata logqueueprod nell'ambiente di produzione. Invece di un nome di coda hardcoded, è preferibile specificare il nome di una voce nella raccolta **appSettings** caratterizzata dal nome della coda effettivo. Se la chiave **appSettings** è logqueue, la funzione potrebbe essere simile all'esempio seguente.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

La classe **NameResolver** potrebbe quindi ottenere il nome della coda dalla raccolta **appSettings**, come illustrato nell'esempio seguente:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

È possibile passare la classe **NameResolver** all'oggetto **JobHost**, come illustrato nell'esempio seguente.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Nota:** i nomi di coda, tabella e BLOB vengono risolti ogni volta che viene chiamata una funzione, ma i nomi di contenitore di BLOB vengono risolti solo all'avvio dell'applicazione. Non è possibile modificare il nome del contenitore di BLOB durante l'esecuzione del processo.

## <a name="how-to-trigger-a-function-manually"></a>Come attivare manualmente una funzione
Per attivare manualmente una funzione, usare il metodo **Call** o **CallAsync** sull'oggetto **JobHost** e l'attributo **NoAutomaticTrigger** nella funzione, come illustrato nell'esempio seguente.

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a name="how-to-write-logs"></a>Come scrivere i log
Il dashboard visualizza i log in due posizioni: la pagina del processo Web e la pagina di una particolare chiamata al processo Web.

![Log nella pagina del processo Web](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Log nella pagina di una chiamata di funzione](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

L'output dei metodi Console chiamati in una funzione o nel metodo **Main()** viene visualizzato nella pagina Dashboard del WebJob, non nella pagina di una chiamata a un metodo specifico. L'output dell'oggetto TextWriter ottenuto da un parametro nella firma del metodo viene visualizzato nella pagina Dashboard di una chiamata al metodo.

L'output di Console non può essere collegato a una chiamata a un metodo particolare perché Console è a thread singolo, mentre potrebbero essere in esecuzione più funzioni di processo contemporaneamente. Per questo motivo l'SDK fornisce a ogni chiamata di funzione il proprio oggetto writer di log univoco.

Per scrivere [log di traccia dell'applicazione](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), usare **Console.Out** (crea log contrassegnati come INFO) e **Console.Error** (crea log contrassegnati come ERROR). In alternativa, è possibile usare [Trace o TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), che fornisce livelli dettagliati, di avviso e critici, oltre ai livelli di informazioni e di errore. I log di traccia dell'applicazione vengono visualizzati nei file di log dell'app Web, nelle tabelle di Azure o nei BLOB di Azure a seconda di come è configurata l'app Web di Azure. Come per tutti gli output di Console, anche i 100 registri applicazioni più recenti vengono visualizzati nella pagina Dashboard del processo Web e non nella pagina di una chiamata di funzione.

L'output di Console viene visualizzato nel dashboard solo se il programma viene eseguito in un processo Web di Azure e non se il programma viene eseguito localmente o in un altro ambiente.

È possibile disabilitare la registrazione impostando la stringa di connessione del dashboard su Null. Per altre informazioni, vedere l'argomento relativo a [come impostare le opzioni di configurazione](#how-to-set-configuration-options).

Nell'esempio seguente vengono illustrati diversi modi per scrivere log:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

Nel dashboard di WebJobs SDK viene visualizzato l'output dell'oggetto **TextWriter** quando si visita la pagina relativa a una chiamata di funzione specifica e si fa clic su **Attiva/Disattiva output**:

![Collegamento della chiamata](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Log nella pagina di una chiamata di funzione](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Nel dashboard di WebJobs SDK le 100 righe più recenti dell'output di Console vengono visualizzate quando si visita la pagina relativa al processo Web (non alla chiamata di funzione) e si fa clic su **Attiva/Disattiva output**.

![Attiva/Disattiva Output](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

In un processo Web continuo, i log dell'applicazione vengono visualizzati in /data/jobs/continuous/*{nomeprocessoweb}*/job_log.txt nel file system dell'app Web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In un BLOB di Azure, l'aspetto dei registri applicazione è simile al seguente: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

In una tabella di Azure i log **Console.Out** e **Console.Error** hanno un aspetto simile al seguente:

![Log delle informazioni nella tabella](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Log degli errori nella tabella](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha fornito esempi di codice che illustrano come gestire scenari comuni per l'uso di code di Azure. Per altre informazioni su come usare Processi Web di Azure e WebJobs SDK, vedere le [risorse di documentazione di Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).


