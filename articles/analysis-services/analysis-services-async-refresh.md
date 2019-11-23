---
title: Aggiornamento asincrono per i modelli di Analysis Services di Azure | Microsoft Docs
description: Viene descritto come usare l'API REST di Azure Analysis Services per codificare l'aggiornamento asincrono dei dati del modello.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c6fba10264939335cdef26f288973f8217f340b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573385"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Aggiornamento asincrono con l'API REST

È possibile eseguire operazioni di aggiornamento asincrono dei dati sui modelli tabulari di Azure Analysis Services usando qualsiasi linguaggio di programmazione che supporta le chiamate REST. È inclusa la sincronizzazione delle repliche di sola lettura per la scalabilità orizzontale delle query. 

Le operazioni di aggiornamento dei dati possono richiedere del tempo a seconda di diversi fattori, tra cui il volume dei dati, il livello di ottimizzazione mediante le partizioni e così via. Queste operazioni sono state tradizionalmente richiamate con i metodi esistenti, ad esempio l'uso di [Tom](https://docs.microsoft.com/bi-reference/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model), i cmdlet di [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) o [TMSL](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). Tuttavia questi metodi possono richiedere spesso connessioni HTTP non affidabili e con esecuzione prolungata.

L'API REST per Azure Analysis Services consente di eseguire le operazioni di aggiornamento dei dati in modo asincrono. Se si usa l'API REST non sono necessarie connessioni HTTP con esecuzione prolungata dalle applicazioni client. Ci sono anche altre funzionalità integrate che garantiscono l'affidabilità, ad esempio i tentativi automatici e il commit in batch.

## <a name="base-url"></a>URL di base

L'URL di base presenta questo formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Si consideri ad esempio un modello denominato AdventureWorks in un server denominato myserver che si trova nell'area di Azure Stati Uniti occidentali. Il nome del server è:

```
asazure://westus.asazure.windows.net/myserver 
```

L'URL di base per questo nome di server è:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Usando l'URL di base è possibile accodare risorse e operazioni in base ai parametri seguenti: 

![Aggiornamento asincrono](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tutto ciò che termina con **s** è una raccolta.
- Tutto ciò che termina con **()** è una funzione.
- Tutto il resto è un oggetto/risorsa.

Ad esempio, è possibile usare il verbo POST sulla raccolta Refreshes per eseguire un'operazione di aggiornamento:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticazione

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido nell'intestazione di autorizzazione e devono soddisfare i requisiti seguenti:

- Il token deve essere un token utente o un'entità servizio dell'applicazione.
- Il token deve avere i destinatari corretti impostati su `https://*.asazure.windows.net`.
- L'utente o l'applicazione deve avere autorizzazioni sufficienti per il server o il modello per poter effettuare la chiamata di richiesta. Il livello di autorizzazione è determinato dai ruoli all'interno del modello o del gruppo di amministrazione sul server.

    > [!IMPORTANT]
    > Attualmente sono necessarie le autorizzazioni del ruolo **amministratore del server**.

## <a name="post-refreshes"></a>POST /refreshes

Per eseguire un'operazione di aggiornamento, usare il verbo POST sulla raccolta /refreshes per aggiungere un nuovo elemento di aggiornamento alla raccolta. L'intestazione Location nella risposta include l'ID aggiornamento. L'applicazione client può disconnettersi e controllare lo stato in un secondo momento, se necessario, perché è asincrona.

Per un modello viene accettata una sola operazione di aggiornamento alla volta. Se un'operazione di aggiornamento è in esecuzione e ne viene inviata un'altra, viene restituito il codice di stato HTTP 409 - Conflitto.

Il corpo dovrebbe essere simile al seguente:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametri

Non è necessario specificare parametri. Viene applicato il valore predefinito.

| Nome             | Type  | Descrizione  |Default  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Il tipo di elaborazione da eseguire. I tipi sono allineati con i tipi del [comando refresh](https://docs.microsoft.com/bi-reference/tmsl/refresh-command-tmsl) di TMSL: full, clearValues, calculate, dataOnly, automatic e defragment. Il tipo add non è supportato.      |   automatic      |
| `CommitMode`     | Enum  | Determina se verrà eseguito il commit degli oggetti in batch o solo al termine. Le modalità comprendono: default, transactional, partialBatch.  |  transactional       |
| `MaxParallelism` | int   | Questo valore determina il numero massimo di thread su cui eseguire i comandi di elaborazione in parallelo. Questo valore è allineato alla proprietà MaxParallelism che può essere impostata nel [comando Sequence](https://docs.microsoft.com/bi-reference/tmsl/sequence-command-tmsl) di TMSL o usando altri metodi.       | 10        |
| `RetryCount`     | int   | Indica il numero massimo di tentativi dell'operazione prima che venga considerata non riuscita.      |     0    |
| `Objects`        | Array | Una matrice di oggetti da elaborare. Ogni oggetto include: "table" quando viene elaborata un'intera tabella oppure "table" e "partition" quando viene elaborata una partizione. Se non viene specificato alcun oggetto, viene aggiornato l'intero modello. |   Elaborare l'intero modello      |

CommitMode equivale a partialBatch. Viene usato quando si esegue un caricamento iniziale di set di dati di grandi dimensioni che potrebbe richiedere ore. Se l'operazione di aggiornamento non riesce dopo l'avvenuto commit di uno o più batch, i batch il cui commit è riuscito rimarranno nello stato di commit (non sarà eseguito il rollback dei batch il cui commit è riuscito).

> [!NOTE]
> Al momento della stesura del presente testo, la dimensione del batch è il valore MaxParallelism, ma questo valore potrebbe cambiare.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Per controllare lo stato di un'operazione di aggiornamento, usare il verbo GET sull'ID aggiornamento. Ecco un esempio del corpo della risposta. Se l'operazione è in corso, nello stato viene restituito **inProgress**.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Per ottenere un elenco delle operazioni di aggiornamento cronologiche per un modello, usare il verbo GET sulla raccolta /refreshes. Ecco un esempio del corpo della risposta. 

> [!NOTE]
> Al momento della stesura del presente testo, vengono archiviati e restituiti gli ultimi 30 giorni di operazioni di aggiornamento, ma questo numero potrebbe cambiare.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Per annullare un'operazione di aggiornamento in corso, usare il verbo DELETE sull'ID aggiornamento.

## <a name="post-sync"></a>POST /sync

Dopo aver eseguito le operazioni di aggiornamento, potrebbe essere necessario sincronizzare i nuovi dati con le repliche per la scalabilità orizzontale delle query. Per eseguire un'operazione di sincronizzazione per un modello, usare il verbo POST sulla funzione/Sync. L'intestazione Location nella risposta include l'ID dell'operazione di aggiornamento.

## <a name="get-sync-status"></a>GET /sync status

Per controllare lo stato di un'operazione di sincronizzazione, usare il verbo GET passando l'ID operazione come parametro. Ecco un esempio del corpo della risposta:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Valori per `syncstate`:

- 0: replica in corso. I file di database sono in fase di replica in una cartella di destinazione.
- 1: riattivazione in corso. Il database è in fase di riattivazione su istanze del server di sola lettura.
- 2: comokleto. L'operazione di sincronizzazione è stata completata correttamente.
- 3: operazione non riuscita. L'operazione di sincronizzazione non è riuscita.
- 4: finalizzazione in corso. L'operazione di sincronizzazione è stata completata ma è in corso la fase di pulizia.

## <a name="code-sample"></a>Esempio di codice

Ecco un esempio di codice C# adatto per iniziare, [RestApiSample su GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Per usare l'esempio di codice

1.  Clonare o scaricare il repository. Aprire la soluzione RestApiSample.
2.  Trovare la riga **client.BaseAddress = …** e inserire l'[URL di base](#base-url).

Nell'esempio di codice viene usata l'autenticazione basata su [entità servizio](#service-principal) .

### <a name="service-principal"></a>Entità servizio

Vedere [Creare un'entità servizio - Portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md) e [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md) per altre informazioni su come configurare un'entità servizio e assegnare le autorizzazioni necessarie in Azure Analysis Services. Dopo aver completato i passaggi, eseguire i passaggi aggiuntivi seguenti:

1.  Nell'esempio di codice trovare **string authority = …** e sostituire **common** con l'ID tenant dell'organizzazione.
2.  Aggiungere o rimuovere il commento in modo che la classe ClientCredential venga usata per creare un'istanza dell'oggetto cred. Assicurarsi che l'accesso ai valori \<App ID> e \<App Key> sia eseguito in modo sicuro o usare l'autenticazione basata su certificato per le entità servizio.
3.  Eseguire l'esempio.


## <a name="see-also"></a>Vedere anche

[Esempi](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


