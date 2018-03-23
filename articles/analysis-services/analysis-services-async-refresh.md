---
title: Aggiornamento asincrono per i modelli di Analysis Services di Azure | Microsoft Docs
description: Informazioni su come codificare l'aggiornamento asincrono tramite l'API REST.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/05/2018
ms.author: owend
ms.openlocfilehash: 4c317736af30b4181fa975713258a41b42ed0da3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Aggiornamento asincrono con l'API REST
È possibile eseguire operazioni di aggiornamento asincrono dei dati sui modelli tabulari di Azure Analysis Services usando qualsiasi linguaggio di programmazione che supporta le chiamate REST. È inclusa la sincronizzazione delle repliche di sola lettura per la scalabilità orizzontale delle query. 

Le operazioni di aggiornamento dei dati possono richiedere tempo a causa di fattori diversi tra cui volume dei dati, livello di ottimizzazione nell'uso delle partizioni e così via. Queste operazioni erano chiamate in genere con metodi esistenti come l'uso di [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model), cmdlet di [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) o [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). Tuttavia questi metodi possono richiedere spesso connessioni HTTP non affidabili e con esecuzione prolungata.

L'API REST per Azure Analysis Services consente di eseguire le operazioni di aggiornamento dei dati in modo asincrono. Se si usa l'API REST non sono necessarie connessioni HTTP con esecuzione prolungata dalle applicazioni client. Ci sono anche altre funzionalità integrate che garantiscono l'affidabilità, ad esempio i tentativi automatici e il commit in batch.

## <a name="base-url"></a>URL di base

L'URL di base presenta questo formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Si consideri ad esempio un modello denominato AdventureWorks in un server denominato myserver che si trova nell'area di Azure Stati Uniti occidentali, il nome del server è:

```
asazure://westus.asazure.windows.net/myserver 
```

L'URL di base per questo nome di server è:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Usando l'URL di base è possibile accodare risorse e operazioni in base a quanto segue: 

![Aggiornamento asincrono](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tutto ciò che termina con **s** è una raccolta.
- Tutto ciò che termina con **()** è una funzione.
- Tutto il resto è un oggetto/risorsa.

Ad esempio è possibile usare il verbo POST sulla raccolta Refreshes per eseguire un'operazione di aggiornamento, come segue:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido nell'intestazione di autorizzazione e devono soddisfare i requisiti seguenti:

- Il token deve essere un token utente o un'entità servizio dell'applicazione.
- Il token deve avere i destinatari corretti impostati su `https://*.asazure.windows.net`.
- L'utente o l'applicazione deve avere autorizzazioni sufficienti per il server o il modello per poter effettuare la chiamata di richiesta. Il livello di autorizzazione è determinato dai ruoli all'interno del modello o del gruppo di amministrazione sul server.

    > [!IMPORTANT]
    > Attualmente sono obbligatorie le autorizzazioni dell'**amministratore del server**.

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

|NOME  |type  |DESCRIZIONE  |Predefinito  |
|---------|---------|---------|---------|
|type     |  Enum       |  Il tipo di elaborazione da eseguire. I tipi sono allineati con i tipi del [comando refresh](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) di TMSL: full, clearValues, calculate, dataOnly, automatic, automatic, add e defragment.       |   automatic      |
|CommitMode     |  Enum       |  Determina se verrà eseguito il commit degli oggetti in batch o solo al termine. Le modalità comprendono: default, transactional, partialBatch.  |  transactional       |
|MaxParallelism     |   int      |  Questo valore determina il numero massimo di thread su cui eseguire i comandi di elaborazione in parallelo. Questo si allinea con la proprietà MaxParallelism che può essere impostata nel [ comando Sequence](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) di TMSL o usando altri metodi.       | 10        |
|RetryCount    |    int     |   Indica il numero massimo di tentativi dell'operazione prima che venga considerata non riuscita.      |     0    |
|Oggetti     |   Array      |   Una matrice di oggetti da elaborare. Ogni oggetto include: "table" quando viene elaborata un'intera tabella oppure "table" e "partition" quando viene elaborata una partizione. Se non viene specificato alcun oggetto, viene aggiornato l'intero modello. |   Elaborare l'intero modello      |

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

Dopo aver eseguito operazioni di aggiornamento, potrebbe essere necessario sincronizzare i nuovi dati con le repliche per la scalabilità orizzontale delle query. Per eseguire un'operazione di sincronizzazione per un modello, usare il verbo POST sulla funzione /sync. L'intestazione Location nella risposta include l'ID dell'operazione di aggiornamento.

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

Valori per syncstate:

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

L'esempio di codice può usare l'accesso interattivo, nome utente/password o l'[entità servizio](#service-principle).

#### <a name="interactive-login-or-usernamepassword"></a>Accesso interattivo o nome utente/password

Questo tipo di autenticazione richiede di creare un'applicazione Azure con assegnate le autorizzazioni API. 

1.  Nel portale di Azure fare clic su **Crea una risorsa** > **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

    ![Registrazione nuova applicazione](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  In **Crea** digitare un nome e selezionare il tipo di applicazione **Nativo** . Per **URI di reindirizzamento** immettere **urn:ietf:wg:oauth:2.0:oob** e poi fare clic su **Crea**.

    ![Impostazioni](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Selezionare l'app e poi copiare e salvare l'**ID applicazione**.

    ![Copiare l'ID applicazione](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  In **Impostazioni** fare clic su **Autorizzazioni necessarie** > **Aggiungi**.

    ![Aggiungere l'accesso all'API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  In **Seleziona un'API** digitare **SQL Server Analysis Services** nella casella di ricerca e poi selezionare **Azure Analysis Services (SQL Server Analysis Services Azure)**.

    ![Selezionare l'API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Selezionare **Read and Write all models** (Leggi e scrivi tutti i modelli), quindi fare clic su **Seleziona**. Quando sono selezionati entrambi, fare clic su **Fine** per aggiungere le autorizzazioni. La propagazione può richiedere alcuni minuti.

    ![Selezionare Read and Write all models (Leggi e scrivi tutti i modelli)](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  Nell'esempio di codice trovare il metodo **UpdateToken()**. Osservare il contenuto di questo metodo.
8.  Trovare **string clientID = …** e poi immettere l'**ID applicazione** copiato al passaggio 3.
9.  Eseguire l'esempio.

#### <a name="service-principal"></a>Entità servizio

Per informazioni su come configurare un'entità servizio e assegnare le autorizzazioni necessarie in Azure Analysis Services, vedere il post di blog [Automation of Azure Analysis Services with Service Principals and PowerShell](https://azure.microsoft.com/blog/automation-of-azure-analysis-services-with-service-principals-and-powershell/) (Automazione di Azure Analysis Services con le entità servizio e PowerShell). Dopo aver completato i passaggi descritti nel post di blog, completare i passaggi aggiuntivi seguenti:

1.  Nell'esempio di codice trovare **string authority = …** e sostituire **common** con l'ID tenant dell'organizzazione.
2.  Aggiungere o rimuovere il commento in modo che la classe ClientCredential venga usata per creare un'istanza dell'oggetto cred. Assicurarsi che l'accesso ai valori \<App ID> e \<App Key> sia eseguito in modo sicuro o usare l'autenticazione basata su certificato per le entità servizio.
3.  Eseguire l'esempio.


## <a name="see-also"></a>Vedere anche 

[Esempi](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


