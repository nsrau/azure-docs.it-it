---
title: "Applicare la disponibilità elevata ai dati delle applicazioni in Azure | Microsoft Docs"
description: "Usare l'archiviazione con ridondanza geografica e accesso in lettura per applicare la disponibilità elevata ai dati delle applicazioni"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 5b1c443cae8481d98c32a3f4d9e3899621d1dd89
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Applicare la disponibilità elevata ai dati delle applicazioni con l'archiviazione di Azure

Questa esercitazione è la prima parte di una serie e illustra come garantire la disponibilità elevata dei dati delle applicazioni in Azure. Al termine, si disporrà di un'applicazione console che recupera e carica un BLOB in un account di archiviazione [con ridondanza geografica e accesso in lettura](../common/storage-redundancy.md#read-access-geo-redundant-storage). L'archiviazione con ridondanza geografica e accesso in lettura funziona replicando le transazioni dall'area primaria all'area secondaria. Il processo di replica garantisce che i dati nell'area secondaria abbiano coerenza finale. L'applicazione usa il criterio [Interruttore](/azure/architecture/patterns/circuit-breaker) per determinare l'endpoint a cui connettersi. L'applicazione passa all'endpoint secondario quando viene simulato un errore.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Scaricare l'esempio
> * Impostare la stringa di connessione
> * Eseguire l'applicazione console

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
  - **Sviluppo di Azure**

  ![Sviluppo di Azure (in Web e cloud)](media/storage-create-geo-redundant-storage/workloads.png)

* (Facoltativo) Scaricare e installare [Fiddler](https://www.telerik.com/download/fiddler)
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* Installare [Python](https://www.python.org/downloads/)
* Scaricare e installare [Azure Storage SDK per Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python)
* (Facoltativo) Scaricare e installare [Fiddler](https://www.telerik.com/download/fiddler)

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti.

Seguire questa procedura per creare un account di archiviazione con ridondanza geografica e accesso in lettura:

1. Selezionare il pulsante **Crea una risorsa** visualizzato nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Archiviazione** nella pagina **Nuovo** e selezionare **Account di archiviazione: BLOB, File, Tabelle, Code** in **In primo piano**.
3. Compilare il modulo dell'account di archiviazione con le informazioni seguenti, come illustrato nell'immagine seguente e selezionare **Crea**:

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Un valore univoco per l'account di archiviazione |
   | **Modello di distribuzione** | Gestione risorse  | Gestione risorse include le funzionalità più recenti.|
   | **Tipo di account** | StorageV2 | Per informazioni dettagliate sui tipi di account, consultare i [tipi di account di archiviazione](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Prestazioni** | Standard | Standard è sufficiente per lo scenario di esempio. |
   | **Replica**| Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS). | È necessario ai fini dell'esempio. |
   |**Trasferimento sicuro necessario** | Disabled| Il trasferimento sicuro non è necessario per questo scenario. |
   |**Sottoscrizione** | sottoscrizione in uso |Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   |**ResourceGroup** | myResourceGroup |Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   |**Posizione** | Stati Uniti orientali | Scegliere un paese. |

![Creare un account di archiviazione](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Scaricare l'esempio

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) ed estrarre (decomprimere) il file storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. È anche possibile usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Il progetto di esempio contiene un'applicazione console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Scaricare il progetto di esempio](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) ed estrarre (decomprimere) il file storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. È anche possibile usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Il progetto di esempio contiene un'applicazione Python di base.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>Impostare la stringa di connessione

Nell'applicazione è necessario inserire la stringa di connessione per l'account di archiviazione. È consigliabile archiviare questa stringa di connessione in una variabile di ambiente sul computer locale che esegue l'applicazione. Seguire uno degli esempi riportati più avanti, in base al sistema operativo, per creare la variabile di ambiente.

Nel portale di Azure passare all'account di archiviazione. Nell'account di archiviazione selezionare **Chiavi di accesso** in **Impostazioni**. Copiare la **stringa di connessione** dalla chiave primaria o secondaria. Sostituire \<stringaconnessione\> con la stringa di connessione effettiva eseguendo uno dei comandi seguenti in base al sistema operativo in uso. Il comando salva una variabile di ambiente nel computer locale. In Windows, la variabile di ambiente non è disponibile finché non si ricarica il **prompt dei comandi** o la shell in uso. Sostituire **\<stringaconnessione\>** nell'esempio seguente:

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<stringaconnessione\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<stringaconnessione\>"

---


## <a name="run-the-console-application"></a>Eseguire l'applicazione console

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
In Visual Studio premere **F5** o selezionare **Avvia** per avviare il debug dell'applicazione. Visual Studio ripristina automaticamente i pacchetti NuGet mancanti, se sono stati configurati. Per altre informazioni, vedere l'articolo relativo a [installazione e reinstallazione di pacchetti con Ripristino pacchetto](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Si apre una finestra della console e l'applicazione avvia l'esecuzione. L'applicazione carica l'immagine **HelloWorld.png** dalla soluzione nell'account di archiviazione. L'applicazione verifica che l'immagine sia stata replicata nell'endpoint RA-GRS secondario. Si avvia quindi il download dell'immagine fino a 999 volte. Ogni lettura è rappresentata da una **P** o una **S**. **P** rappresenta l'endpoint primario e **S** rappresenta l'endpoint secondario.

![App console in esecuzione](media/storage-create-geo-redundant-storage/figure3.png)

Nell'esempio di codice l'attività `RunCircuitBreakerAsync` nel file `Program.cs` viene usata per scaricare un'immagine dall'account di archiviazione con il metodo [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Prima del download viene definita una classe [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Il contesto dell'operazione definisce i gestori dell'evento, generati quando il download viene completato correttamente o se un download ha esito negativo ed esegue un nuovo tentativo.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Per eseguire l'applicazione in un terminale o al prompt dei comandi, passare alla directory **circuitbreaker.py** e quindi immettere `python circuitbreaker.py`. L'applicazione carica l'immagine **HelloWorld.png** dalla soluzione nell'account di archiviazione. L'applicazione verifica che l'immagine sia stata replicata nell'endpoint RA-GRS secondario. Si avvia quindi il download dell'immagine fino a 999 volte. Ogni lettura è rappresentata da una **P** o una **S**. **P** rappresenta l'endpoint primario e **S** rappresenta l'endpoint secondario.

![App console in esecuzione](media/storage-create-geo-redundant-storage/figure3.png)

Nel codice di esempio, il metodo `run_circuit_breaker` nel file `circuitbreaker.py` viene usato per scaricare un'immagine dall'account di archiviazione con il metodo [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

La funzione di ripetizione dei tentativi dell'oggetto di archiviazione è impostata su un criterio di ripetizione lineare. La funzione di ripetizione dei tentativi determina se eseguire o meno un nuovo tentativo per una richiesta e specifica il numero di secondi di attesa prima del nuovo tentativo. Impostare il valore di **retry\_to\_secondary** su true se la richiesta dovrà essere ripetuta all'endpoint secondario in caso di esito negativo della richiesta iniziale all'endpoint primario. Nell'applicazione di esempio, nella funzione `retry_callback` dell'oggetto di archiviazione viene definito un criterio di ripetizione dei tentativi personalizzato.
 
Prima del download vengono definite le funzioni [retry_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e [response_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) dell'oggetto del servizio, che definiscono i gestori eventi che vengono attivati quando un download viene completato oppure ha esito negativo e viene eseguito un nuovo tentativo.  

---

### <a name="retry-event-handler"></a>Riprovare il gestore dell'evento

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

Quando il download dell'immagine non riesce ed è impostata la ripetizione dei tentativi, viene chiamato il gestore eventi `OperationContextRetrying`. Se viene raggiunto il numero massimo di tentativi definito nell'applicazione, il valore di [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) della richiesta viene modificato in `SecondaryOnly`. Questa impostazione forza l'applicazione a tentare di scaricare l'immagine dall'endpoint secondario. Questa configurazione riduce il tempo necessario per richiedere che l'immagine non venga ripetuta all'infinito come endpoint primario.

Nell'esempio di codice l'attività `RunCircuitBreakerAsync` nel file `Program.cs` viene usata per scaricare un'immagine dall'account di archiviazione con il metodo [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet). Prima del download viene definito [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Il contesto dell'operazione definisce i gestori dell'evento, generati quando il download viene completato correttamente o se un download ha esito negativo ed esegue un nuovo tentativo.
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Quando il download dell'immagine non riesce ed è impostata la ripetizione dei tentativi, viene chiamato il gestore eventi `retry_callback`. Se viene raggiunto il numero massimo di tentativi definito nell'applicazione, il valore di [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) della richiesta viene modificato in `SECONDARY`. Questa impostazione forza l'applicazione a tentare di scaricare l'immagine dall'endpoint secondario. Questa configurazione riduce il tempo necessario per richiedere che l'immagine non venga ripetuta all'infinito come endpoint primario.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

---


### <a name="request-completed-event-handler"></a>Gestore dell'evento per la richiesta completata
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

Quando il download dell'immagine riesce viene chiamato il gestore dell'evento `OperationContextRequestCompleted`. Se l'applicazione usa l'endpoint secondario, continua a usarlo fino a 20 volte. Dopo 20 volte, l'applicazione reimposta [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) su `PrimaryThenSecondary` ed esegue un nuovo tentativo con l'endpoint primario. Se una richiesta ha esito positivo, l'applicazione continua a eseguire la lettura dall'endpoint primario.
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-tabpython"></a>[Python] (#tab/python) 

Quando il download dell'immagine riesce viene chiamato il gestore dell'evento `response_callback`. Se l'applicazione usa l'endpoint secondario, continua a usarlo fino a 20 volte. Dopo 20 volte, l'applicazione reimposta [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) su `PRIMARY` ed esegue un nuovo tentativo con l'endpoint primario. Se una richiesta ha esito positivo, l'applicazione continua a eseguire la lettura dall'endpoint primario.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

---

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie, è stato descritto come applicare la disponibilità elevata a un'applicazione con gli account di archiviazione RA-GRS, ad esempio come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Scaricare l'esempio
> * Impostare la stringa di connessione
> * Eseguire l'applicazione console

Passare alla seconda parte della serie per informazioni su come simulare un errore e forzare l'applicazione a usare l'endpoint RA-GRS secondario.

> [!div class="nextstepaction"]
> [Simulare un errore nella connessione all'endpoint di archiviazione primario](storage-simulate-failure-ragrs-account-app.md)
