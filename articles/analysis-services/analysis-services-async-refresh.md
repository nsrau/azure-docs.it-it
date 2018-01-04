---
title: Aggiornamento asincrono per i modelli di Analysis Services di Azure | Documenti Microsoft
description: Informazioni sull'aggiornamento asincrono del codice tramite l'API REST.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/18/2017
ms.author: owend
ms.openlocfilehash: 06d807b83f700c675c6979998dd8f74372a4845f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Aggiornamento asincrono con l'API REST
Utilizzando qualsiasi linguaggio di programmazione che supporta chiamate REST, è possibile eseguire operazioni di aggiornamento asincrono dei dati in modelli tabulari del Azure Analysis Services. Ciò include la sincronizzazione delle repliche di sola lettura per la scalabilità di query. 

Le operazioni di aggiornamento dei dati possono richiedere del tempo in base a diversi fattori tra cui volume di dati, livello di ottimizzazione utilizzando partizioni e così via. Queste operazioni siano state richiamate in genere con i metodi esistenti, ad esempio utilizzando [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (modello a oggetti tabulare), [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) cmdlet, o [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (modello tabulare Scripting Language). Tuttavia, questi metodi possono richiedere spesso le connessioni HTTP non affidabili, con esecuzione prolungata.

L'API REST per Azure Analysis Services consente operazioni di aggiornamento dei dati devono essere effettuati in modo asincrono. Tramite l'API REST, le connessioni HTTP con esecuzione prolungata dalle applicazioni client non sono necessarie. Esistono inoltre altre funzionalità incorporate per l'affidabilità, ad esempio i tentativi di auto e commit in batch.

## <a name="base-url"></a>URL di base

L'URL di base il formato seguente:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Si consideri ad esempio un modello denominato AdventureWorks, in un server denominato myserver, che si trova nell'area occidentale Microsoft Azure, è il nome del server:

```
asazure://westus.asazure.windows.net/myserver 
```

URL di base per il nome del server è:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Tramite l'URL di base, risorse e alle operazioni possono essere aggiunto in base ai seguenti: 

![Aggiornamento asincrono](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Tutto ciò che termina con **s** è una raccolta.
- Tutto ciò che termina con **()** è una funzione.
- Qualsiasi altro valore è un oggetto o della risorsa.

Ad esempio, è possibile utilizzare il verbo POST per la raccolta viene aggiornata per eseguire un'operazione di aggiornamento, simile al seguente:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Tutte le chiamate devono essere autenticate con un token di Azure Active Directory (OAuth 2) valido nell'intestazione di autorizzazione e devono soddisfare i requisiti seguenti:

- Il token deve essere un token utente o un'entità servizio dell'applicazione.
- Utente o dell'applicazione necessario disporre delle autorizzazioni sufficienti sul server o del modello per effettuare la chiamata di richiesta. Il livello di autorizzazione è determinato dai ruoli all'interno del modello o il gruppo di amministrazione sul server.
- Il token deve avere i destinatari corretto impostare `https://*.asazure.windows.net`.

## <a name="post-refreshes"></a>POST /refreshes

Per eseguire un'operazione di aggiornamento, utilizzare il verbo POST sulla raccolta /refreshes per aggiungere un nuovo elemento di aggiornamento alla raccolta. L'intestazione di posizione nella risposta include l'ID di aggiornamento. L'applicazione client può disconnettere e controllare lo stato in un secondo momento, se necessario perché è asincrona.

Operazione di aggiornamento solo una viene accettata in un momento per un modello. Se è presente un'operazione di aggiornamento corrente in esecuzione e un altro viene inviato, viene restituito il codice di stato HTTP di conflitto 409.

Il corpo può simile al seguente:

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
Specifica dei parametri non è necessaria. Il valore predefinito viene applicato.

|NOME  |type  |DESCRIZIONE  |Predefinito  |
|---------|---------|---------|---------|
|type     |  Enum       |  Il tipo di elaborazione da eseguire. I tipi sono allineati con la TMSL [comando refresh](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) tipi: full, clearValues, calcolare, dataOnly, automatic, aggiungere e deframmentare.       |   Automatico      |
|Enumerazione CommitMode consente     |  Enum       |  Determina se gli oggetti verrà eseguito il commit nel batch o solo al termine. Modalità includono: partialBatch transazionale, impostazione predefinita.  |  Transazionale       |
|MaxParallelism     |   int      |  Questo valore determina il numero massimo di thread su cui eseguire i comandi di elaborazione in parallelo. Questo allineato con la proprietà MaxParallelism che può essere impostata nella finestra di TMSL [sequenza comando](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) o tramite altri metodi.       | 10        |
|RetryCount    |    int     |   Indica il numero massimo di che tentativi prima che si verifichi l'operazione.      |     0    |
|Oggetti     |   Array      |   Matrice di oggetti da elaborare. Ogni oggetto include: "table" durante l'elaborazione di intera tabella oppure "table" e "una partizione" durante l'elaborazione di una partizione. Se è specificato alcun oggetto, viene aggiornato l'intero modello. |   L'intero modello di processo      |

Enumerazione CommitMode consente è uguale a partialBatch. Viene utilizzato quando si esegue un caricamento iniziale di grandi set di dati che potrebbe richiedere ore. Se l'operazione di aggiornamento non riesce dopo aver completato il commit di uno o più batch, i batch eseguito il commit verranno mantenuto il commit (non ripristina batch eseguito il commit).

> [!NOTE]
> In fase di scrittura, la dimensione del batch è il valore MaxParallelism, ma è stato possibile modificare questo valore.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId >

Per controllare lo stato di un'operazione di aggiornamento, utilizzare il verbo GET sull'ID dell'aggiornamento. Di seguito è riportato un esempio di corpo della risposta. Se l'operazione è in corso, **inProgress** viene restituito lo stato.

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

## <a name="get-refreshes"></a>OTTENERE /refreshes

Per ottenere un elenco di operazioni di aggiornamento cronologici per un modello, utilizzare il verbo GET per la raccolta di /refreshes. Di seguito è riportato un esempio di corpo della risposta. 

> [!NOTE]
> In fase di scrittura, gli ultimi 30 giorni di operazioni di aggiornamento vengono archiviati e restituiti, ma è stato possibile modificare questo numero.

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

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId >

Per annullare un'operazione di aggiornamento in corso, usano il verbo DELETE sull'ID dell'aggiornamento.

## <a name="post-sync"></a>/Sync POST

Aver eseguito le operazioni di aggiornamento, potrebbe essere necessario sincronizzare i nuovi dati con le repliche per la scalabilità di query. Per eseguire un'operazione di sincronizzazione per un modello, utilizzare il verbo POST per la funzione /sync. L'intestazione di posizione nella risposta include l'ID operazione di sincronizzazione.

## <a name="get-sync-status"></a>OTTENERE lo stato /sync

Per controllare lo stato di un'operazione di sincronizzazione, utilizzare il verbo GET passando l'ID operazione come parametro. Di seguito è riportato un esempio di corpo della risposta:

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

Valori per stato di sincronizzazione:

- 0: la replica. File di database vengono replicati in una cartella di destinazione.
- 1: rehydrating. Il database è viene riattivato su istanze del server di sola lettura.
- 2: completato. L'operazione di sincronizzazione è stata completata.
- 3: non è riuscita. Impossibile eseguire l'operazione di sincronizzazione.
- 4: finalizzazione. L'operazione di sincronizzazione è stata completata ma sta eseguendo la procedura di pulizia.

## <a name="code-sample"></a>Esempio di codice

Ecco un esempio di codice c# per iniziare, [RestApiSample su GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Per utilizzare l'esempio di codice

1.  Clona o scaricare il repository. Aprire la soluzione RestApiSample.
2.  Trovare la riga **client. BaseAddress =...** e fornire il [URL di base](#base-url).

L'esempio di codice è possibile utilizzare account di accesso interattivo, nome utente/password o [dell'entità servizio](#service-principle).

#### <a name="interactive-login-or-usernamepassword"></a>Accesso interattivo o nome utente/password

Questo tipo di autenticazione è necessario creare un'applicazione Azure con le autorizzazioni necessarie di API assegnate. 

1.  Nel portale di Azure, fare clic su **New** > **Azure Active Directory** > **registrazioni di App** > **New registrazione dell'applicazione**.

    ![Nuova registrazione dell'applicazione](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  In **crea**, digitare un nome, selezionare **nativo** tipo di applicazione. Per **URI di reindirizzamento**, immettere **urn: ietf:wg:oauth:2.0:oob**, quindi fare clic su **crea**.

    ![Impostazioni](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Selezionare l'app e quindi copiare e salvare il **ID applicazione**.

    ![Copiare l'ID applicazione](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  In **impostazioni**, fare clic su **delle autorizzazioni necessarie** > **Aggiungi**.

    ![Aggiungi accesso all'API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  In **selezionare un'API**, tipo **SQL Server Analysis Services** nella casella di ricerca e quindi selezionare **Azure Analysis Services (SQL Server Analysis Services Azure)**.

    ![Seleziona un'API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Selezionare **leggere e scrivere tutti i modelli**, quindi fare clic su **selezionare**. Quando sono selezionati, fare clic su **eseguita** per aggiungere le autorizzazioni. Potrebbe richiedere alcuni minuti per la propagazione.

    ![Selezionare lettura e scrittura di tutti i modelli](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  Nell'esempio di codice, è possibile trovare il **UpdateToken()** metodo. Osservare il contenuto di questo metodo.
8.  Trovare **stringa clientID =...** , quindi immettere il **ID applicazione** copiato dal passaggio 3.
9.  Eseguire l'esempio.

#### <a name="service-principal"></a>Entità servizio

Vedere il [automazione di Azure Analysis Services con le entità servizio e PowerShell](https://azure.microsoft.com/blog/automation-of-azure-analysis-services-with-service-principals-and-powershell/) post di blog per informazioni su come configurare un'entità servizio e assegnare le autorizzazioni necessarie in Azure Analysis Services. Dopo aver completato i passaggi illustrati nel post di blog, completare i passaggi aggiuntivi seguenti:

1.  Nell'esempio di codice, trovare **stringa autorità =...** , sostituire **comuni** con l'organizzazione tenant ID.
2.  Commento o rimuovere il commento della classe ClientCredential viene utilizzato per creare un'istanza dell'oggetto credenziali. Verificare che il \<ID App > e \<chiave App > valori viene eseguiti in modo sicuro o utilizzare l'autenticazione basata su certificato per le entità servizio.
3.  Eseguire l'esempio.


## <a name="see-also"></a>Vedere anche 

[Esempi](analysis-services-samples.md)   
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


