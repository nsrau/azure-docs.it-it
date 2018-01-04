---
title: Eseguire funzioni di Azure con i processi Analitica di flusso di Azure | Documenti Microsoft
description: Informazioni su come configurare Azure funzione come sink di output per i processi di analisi di flusso.
keywords: flusso di dati di output, dati, Azure (funzione)
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: adc147fc9f47e78cc0a2fcaf53f064bcfa5eee2c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Eseguire funzioni di Azure con i processi Analitica di flusso di Azure 
 
> [!IMPORTANT]
> Questa funzionalità è in anteprima.

È possibile eseguire le funzioni di Azure con Azure flusso Analitica configurando le funzioni di Azure come uno dei sink di output per il processo di flusso Analitica. Funzione Azure è un basato sugli eventi, l'esperienza di calcolo su richiesta che consente di implementare il codice che viene attivato dagli eventi che si verificano in Azure o i servizi di terze parti. Questa capacità della funzione di Azure per rispondere ai trigger rende un output fisica al processo Analitica di flusso di Azure.

Azure Analitica flusso richiama la funzione di Azure tramite trigger HTTP. L'adattatore di output della funzione di Azure consente agli utenti di connettersi funzioni Azure Analitica di flusso in modo che gli eventi possono essere attivati sulla base delle query Analitica di flusso. 

Questa esercitazione viene illustrato come connettersi Analitica di flusso di Azure per [Cache Redis di Azure](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) utilizzando [Azure funzioni](../azure-functions/functions-overview.md). 

## <a name="configure-stream-analytics-job-to-run-an-azure-function"></a>Configurare il processo di flusso Analitica per eseguire una funzione di Azure 

In questa sezione viene illustrato come configurare un processo di flusso Analitica per eseguire una funzione di Azure che scrive dati in Cache Redis di Azure. Il processo di flusso Analitica legge gli eventi dall'Hub eventi, esegue una query che richiama la funzione di Azure. Questa funzione Azure legge i dati dal processo Analitica flusso e scrive la Cache Redis di Azure.

![Immagine per illustrare l'esercitazione](./media/stream-analytics-with-azure-functions/image1.png)

I passaggi seguenti sono necessari per completare questa attività:
* [Creare il processo di flusso Analitica con Hub eventi come input.](#create-stream-analytics-job-with-event-hub-as-input)  
* [Creare una Cache Redis di Azure.](#create-an-azure-redis-cache)  
* [Creare una funzione di Azure che può scrivere dati nella Cache Redis.](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Aggiornare il processo di analisi di flusso con funzione di Azure come output.](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Per i risultati, consultare Cache Redis.](#check-redis-cache-for-results)  

## <a name="create-stream-analytics-job-with-event-hub-as-input"></a>Creare il processo di flusso Analitica con Hub eventi come input

Seguire il [delle frodi in tempo reale](stream-analytics-real-time-fraud-detection.md) esercitazione per creare un hub eventi, avviare l'applicazione di generatore di evento e creare un Analitica di flusso di Azure (ignorare i passaggi per creare la query e l'output, ma installerà un Funzioni di Azure l'output nella sezione successiva.)

## <a name="create-an-azure-redis-cache"></a>Creare una cache Redis di Azure

1. Creare una Cache Redis di Azure tramite la procedura descritta in [creare una cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) sezione dell'articolo Cache Redis.  

2. Dopo la creazione della Cache Redis, passare alla cache creata > **chiavi di accesso** > e prendere nota del **stringa di connessione primaria**.

   ![Stringa di connessione della Cache redis](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-an-azure-function-that-can-write-data-to-the-redis-cache"></a>Creare una funzione di Azure che può scrivere dati nella Cache Redis

1. Utilizzare il [creare un'app di funzione](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) sezione della documentazione di funzioni di Azure per creare un'App di Azure (funzione) e un [attivato HTTP funzione Azure](../azure-functions/functions-create-first-azure-function.md#create-function) (noto anche come Webhook) utilizzando **CSharp** language.  

2. Passare il **run.csx** funzione e aggiornarlo con il codice seguente (assicurarsi di sostituire la "\<qui la stringa di connessione della cache redis\>" testo con la stringa di connessione primaria della Cache Redis quale è stato recuperato nella sezione precedente):  

   ```c#
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
}    

   ```

   Quando Azure flusso Analitica riceve 413 eccezione (Http entità della richiesta troppo grande) dalla funzione di Azure, riduce le dimensioni dei batch inviato alle funzioni di Azure. Nella funzione di Azure, utilizzare il codice seguente per verificare che il Analitica di flusso di Azure non inviare batch di dimensioni eccessive. Assicurarsi che i valori di conteggio e delle dimensioni massime batch utilizzati nella funzione siano coerenti con i valori immessi nel portale di flusso Analitica.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. In un editor di testo di propria scelta, creare un file JSON denominato **Project** con il codice seguente e salvarlo nel computer locale. Questo file contiene le dipendenze dei pacchetti NuGet necessari per la funzione di c#.  
   
   ```json
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

   ```
 
4. Tornare al portale di Azure > passare alla funzione Azure > dal **funzionalità della piattaforma** scheda > fare clic su **Editor di servizio App** che si trova in **gli strumenti di sviluppo**. 
 
   ![Schermata dell'editor di servizio App](./media/stream-analytics-with-azure-functions/image3.png)

5. Nell'Editor di servizio App, fare clic con il pulsante destro sul nome della directory radice e caricare il **Project** file. Dopo il caricamento ha esito positivo, aggiornare la pagina, dovrebbe essere un file generato automaticamente denominato **lock**.  Il file generato automaticamente contiene riferimenti alle DLL che vengono specificati nel file Project.  

   ![Caricare file Project JSON](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytic-job-with-azure-function-as-output"></a>Aggiornare il processo di analisi di flusso con funzione di Azure come output

1. Aprire il processo Analitica di flusso di Azure nel portale di Azure.  

2. Passare alla funzione Azure > **Panoramica** > **output** > **Aggiungi** un nuovo output > selezionare **funzione Azure** per l'opzione di Sink. Il nuovo adattatore di output di funzione di Azure è disponibile con le proprietà seguenti:  

   |**Nome della proprietà**|**Descrizione**|
   |---|---|
   |Alias di output| Nome descrittivo utilizzato nella query del processo per l'output di riferimento. |
   |Opzione di importazione| È possibile utilizzare la funzione azure dalla sottoscrizione corrente o immettere manualmente le impostazioni, se la funzione si trova in altra sottoscrizione. |
   |App per le funzioni| Nome dell'App di Azure (funzione). |
   |Funzione| Nome della funzione nell'App di funzione (nome della funzione di run.csx).|
   |Dimensioni massime batch|Questa proprietà viene utilizzata per impostare le dimensioni massime per ogni batch di output, che viene inviata alla funzione Azure. Per impostazione predefinita, questo valore è impostato su 256 KB.|
   |Numero massimo di batch|Questa proprietà consente di specificare il numero massimo di eventi in ogni batch viene inviato alla funzione di Azure. Il valore di conteggio massima del batch predefinito è 100. Questa proprietà è facoltativa.|
   |Chiave|Questa proprietà consente di utilizzare una funzione di Azure da un'altra sottoscrizione. Fornire il valore della chiave per accedere a una funzione. Questa proprietà è facoltativa.|

3. Specificare un nome per l'alias di output. In questa esercitazione è nome **saop1** (è possibile utilizzare qualsiasi nome desiderato) e specificare altri dettagli.  

4. Aprire il processo di flusso Analitica e aggiornare la query al seguente (assicurarsi di sostituire il testo "saop1", denominato in modo diverso il sink di output):  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Avviare l'applicazione telcodatagen.exe eseguendo il comando seguente nella riga di comando (il comando accetta il formato - `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`)  
   
   **telcodatagen.exe 1000 2 à 2**
    
6.  Avviare il processo di flusso Analitica.

## <a name="check-redis-cache-for-results"></a>Cache Redis di controllo per i risultati

1. Passare al portale di Azure e trovare la Cache Redis > selezionare **Console**.  

2. Utilizzare [comandi cache Redis](https://redis.io/commands) per verificare che i dati sono in cache Redis. (Il comando richiede il formato-Get {chiave}). Ad esempio: 

   **Ottenere "19/12/2017 21:32:24-123414732"**

   Questo comando deve visualizzare il valore per la chiave specificata:

   ![Output della Cache redis](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Problemi noti

* Nel portale di Azure, quando si tenta di reimpostare le dimensioni del Max Batch / numero di Batch massimo valore empty(default), diventa nuovamente il valore immesso in precedenza al momento di salvataggio. Deliberatamente immettere i valori predefiniti per questi campi in questo caso.

## <a name="next-steps"></a>Passaggi successivi
