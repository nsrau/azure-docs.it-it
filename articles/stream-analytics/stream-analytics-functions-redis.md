---
title: Elaborazione in tempo reale in Analisi di flusso per le funzioni di Azure | Microsoft Docs
description: Informazioni su come utilizzare una funzione di Azure connessa a una coda del bus di servizio per popolare una cache Redis di Azure dall'output di un processo di analisi di flusso.
keywords: flusso di dati, cache redis, coda bus di servizio
services: stream-analytics
author: samacha
manager: jhubbard
documentationcenter: 
ms.assetid: d428bb33-4244-4001-b93d-c77bed816527
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 70471749ef2b0ccaa4662cbf8a1dfa7ef4b21cfa
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Come archiviare i dati dall'analisi di flusso di Azure nella Cache Redis di Azure utilizzando le funzioni di Azure
L'analisi di flusso di Azure ti consente di sviluppare e distribuire rapidamente soluzioni a basso costo per ottenere informazioni approfondite in tempo reale da dispositivi, sensori, infrastruttura e applicazioni, o qualsiasi flusso di dati. Ciò consente vari casi di utilizzo, tra cui: monitoraggio e gestione in tempo reale, comando e controllo, rilevamento di frodi, automobili connesse e molti altri. In molti scenari, l'utente può avere il desiderio di archiviare i dati generati dall'analisi di flusso di Azure in un archivio dati distribuito, come ad esempio una cache Redis di Azure.

Immaginare di far parte di una società di telecomunicazioni. L'utente prova a rilevare una frode SIM caratterizzata da una serie di chiamate simultanee provenienti dalla stessa identità, ma in diverse aree geografiche. L'utente deve occuparsi dell'archiviazione di tutte le telefonate potenzialmente illecite in una cache Redis di Azure. In questo blog, vengono fornite le indicazioni su come è possibile completare tale operazione. 

## <a name="prerequisites"></a>Prerequisiti
Portare a termine la procedura dettagliata relativa al [rilevamento delle frodi in tempo reale][fraud-detection] per ASA

## <a name="architecture-overview"></a>Panoramica dell'architettura
![Schermata dell'architettura](./media/stream-analytics-functions-redis/architecture-overview.png)

Come illustrato nella figura precedente, la funzionalità di analisi di flusso consente di generare delle query sul flusso di dati in ingresso e di inviarlo a un output. Basandosi sull'output, le funzioni di Azure possono successivamente attivare alcuni tipi di evento. 

In questo blog, l'attenzione viene posta soprattutto sulle funzioni di Azure che fanno parte della pipeline o, più nello specifico, sull'attivazione di un evento che archivia i dati illeciti nella cache.
Dopo aver completato l'esercitazione sul [rilevamento delle frodi in tempo reale][fraud-detection], saranno disponibili un input (un hub eventi), una query e un output (archivio BLOB) già configurati e in esecuzione. In questo blog, l'output viene modificato per utilizzare, invece, una coda del bus di servizio. Successivamente, una funzione di Azure viene collegata alla coda. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Creare e collegare un output della coda di del bus di servizio
Per creare una coda del bus di servizio, seguire i passaggi 1 e 2 della sezione .NET in [Introduzione alle code del bus di servizio][servicebus-getstarted].
A questo punto è possibile connettere la coda al processo di analisi di flusso creato durante la precedente procedura dettagliata sul rilevamento delle frodi.

1. Nel portale di Azure accedere al pannello **Output** del proprio processo, quindi selezionare **Aggiungi** nella parte superiore della pagina.
   
    ![Aggiunta di output](./media/stream-analytics-functions-redis/adding-outputs.png)
2. Scegliere **Coda del bus di servizio** come **Sink** e seguire le istruzioni visualizzate sullo schermo. Assicurarsi di scegliere lo spazio dei nomi della coda del bus di servizio creata in [Introduzione alle code del bus di servizio][servicebus-getstarted]. Al termine, fare clic sul pulsante di conferma.
3. Specificare i valori seguenti:
   
   * **Formato serializzatore eventi**: JSON
   * **Codifica**: UTF8
   * **FORMATO**: riga separata
4. Fare clic sul pulsante **Crea** per aggiungere l'origine e per verificare che Analisi di flusso possa connettersi all'account di archiviazione.
5. Nella scheda **Query** , sostituire la query corrente con quanto segue. Sostituire *[YOUR SERVICE BUS NAME]* con il nome di output creato nel passaggio 3. 
   
    ```    
   
        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
   
        INTO [YOUR SERVICE BUS NAME]
   
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   
        WHERE CS1.SwitchNum != CS2.SwitchNum
   
    ```

## <a name="create-an-azure-redis-cache"></a>Creare una cache Redis di Azure
Creare una cache Redis di Azure seguendo la sezione di .NET descritta in [Come usare Cache Redis di Azure][use-rediscache] e proseguire fino alla sezione ***Configurare i client della cache***.
Al termine dell'operazione, è disponibile una nuova cache Redis. In **Tutte le impostazioni** selezionare **Chiavi di accesso** e annotare la ***Stringa di connessione primaria***.

![Schermata dell'architettura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Creare una funzione di Azure
Seguire l'esercitazione [Creare la prima funzione di Azure][functions-getstarted] per iniziare a usare le funzioni di Azure. Se si dispone già di una funzione di Azure da utilizzare, passare direttamente alla sezione [Scrivere nella cache Redis](#Writing-to-Redis-Cache)

1. Nel portale, selezionare i servizi app dal menu di navigazione a sinistra, quindi fare clic sul nome dell'app per le funzioni di Azure per accedere al sito Web corrispondente.
    ![Schermata dell'elenco di funzioni dei servizi app](./media/stream-analytics-functions-redis/app-services-function-list.png)
2. Fare clic su **Nuova funzione > ServiceBusQueueTrigger – C#**. Per i campi indicati sotto, seguire queste istruzioni:
   
   * **Nome coda**: lo stesso nome immesso al momento della creazione della coda in [Introduzione alle code del bus di servizio][servicebus-getstarted] (non il nome del bus di servizio). Assicurarsi di utilizzare la coda connessa all'output di analisi di flusso.
   * **Connessione del bus di servizio**: selezionare **Aggiungere una stringa di connessione**. Per trovare la stringa di connessione, accedere al portale classico, selezionare **Bus di servizio** (il bus di servizio creato) e **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della schermata. Assicurarsi di essere nella schermata principale della pagina. Copiare e incollare la stringa di connessione. È possibile immettere qualsiasi nome di connessione.
     
       ![Schermata di connessione al bus di servizio](./media/stream-analytics-functions-redis/servicebus-connection.png)
   * **AccessRights**: scegliere **Gestisci**
3. Fare clic su **Crea**

## <a name="writing-to-redis-cache"></a>Scrivere nella cache Redis
A questo punto è stata creata una funzione di Azure leggibile dalla coda del bus di servizio. Adesso l'unica cosa che rimane da fare è utilizzare la funzione per scrivere i dati nella cache Redis. 

1. Selezionare **ServiceBusQueueTrigger** di recente creazione e fare clic su **Impostazioni dell'app per le funzioni** nell'angolo in alto a destra. Selezionare **Vai alle impostazioni del servizio app > Impostazioni > Impostazioni applicazione**
2. Nella sezione relativa alle stringhe di connessione, immettere un nome nella sezione **Nome** . Incollare la stringa di connessione primaria trovata nel passaggio **Creare una Cache Redis** della sezione **Valore**. Selezionare **Personalizzato** per **Database SQL**.
3. Nella parte superiore, fare clic su **Salva** .
   
    ![Schermata di connessione al bus di servizio](./media/stream-analytics-functions-redis/function-connection-string.png)
4. Tornare alle impostazioni del servizio app e selezionare **Strumenti > Editor del servizio app (anteprima) > Attivo > Vai**.
   
    ![Schermata di connessione al bus di servizio](./media/stream-analytics-functions-redis/app-service-editor.png)
5. Nell'editor prescelto, creare un file JSON denominato **project.json** e salvarlo sul disco locale.
   
        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }
6. Caricare il file nella directory radice della funzione (non WWWROOT). A questo punto dovrebbe apparire automaticamente un file denominato **project.lock.json** , a conferma dell'avvenuta importazione dei pacchetti Nuget "StackExchange.Redis" e "Newtonsoft.Json".
7. Nel file **run.csx** , sostituire il codice pre-generato con il codice seguente. Nella funzione lazyConnection, sostituire "CONN NAME" con il nome immesso nel passaggio 2 della procedura **Archiviare i dati nella cache Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");

        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Avviare il processo di analisi di flusso
1. Avviare l'applicazione telcodatagen.exe. L'utilizzo è il seguente: ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````
2. Dal pannello del processo di analisi di flusso nel portale, fare clic su **Avvia** nella parte superiore della pagina.
   
    ![Schermata del processo di avvio](./media/stream-analytics-functions-redis/starting-job.png)
3. Nel pannello **Avvia processo** visualizzato selezionare **Adesso** e fare clic sul pulsante **Avvia** nella parte inferiore della schermata. Lo stato del processo diventa prima In avvio e poco dopo In esecuzione.
   
    ![Schermata di selezione dell'ora del processo di avvio](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Eseguire la soluzione e verificarne i risultati
Tornare alla pagina **ServiceBusQueueTrigger** in cui si dovrebbero visualizzare le istruzioni di log. Questi log mostrano che l'utente ha ricevuto dei dati dalla coda del bus di servizio, che sono stati inseriti nel database e recuperati utilizzando l'ora come chiave.

Per verificare che i dati sono presenti nella cache di Redis, andare alla pagina della cache Redis nel nuovo portale (come illustrato nel precedente passaggio [Creare una cache Redis di Azure](#Create-an-Azure-Redis-Cache) ) e selezionare Console.

Adesso è possibile scrivere i comandi di Redis per confermare la presenza dei dati nella cache.

![Schermata della console Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Passaggi successivi
Siamo entusiasti di vedere tutto ciò che le nuove funzioni di Azure e l'analisi di flusso sono in grado di fare insieme e ci auguriamo che possano offrire nuove possibilità agli utenti. Per condividere i propri commenti e suggerimenti per il futuro, utilizzare liberamente il [sito UserVoice di Azure](https://feedback.azure.com/forums/270577-stream-analytics).

Per i neofiti di Microsoft Azure, li invitiamo a provare questa soluzione registrandosi per ottenere un [account di prova gratuito di Azure](https://azure.microsoft.com/pricing/free-trial/). Per i neofiti della funzionalità di analisi di flusso, li invitiamo a [creare il primo processo di analisi di flusso](stream-analytics-create-a-job.md).

In caso di domande o per ricevere assistenza, pubblicare un messaggio nel forum [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) o [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics). 

È anche possibile consultare le risorse seguenti:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](../azure-functions/functions-reference.md)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](../azure-functions/functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#](../azure-functions/functions-reference-fsharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](../azure-functions/functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](../azure-functions/functions-triggers-bindings.md)
* [Come monitorare Cache Redis di Azure](../redis-cache/cache-how-to-monitor.md)

Per tenersi aggiornati sulle ultime novità e sulle funzionalità più recenti, seguire [@AzureStreaming](https://twitter.com/AzureStreaming) su Twitter.

[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

