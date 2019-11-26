---
title: Distribuzione senza tempi di inattività per Durable Functions
description: Informazioni su come abilitare l'orchestrazione Durable Functions per le distribuzioni senza tempi di inattività.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231264"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Distribuzione senza tempi di inattività per Durable Functions

Il [modello di esecuzione affidabile](durable-functions-checkpointing-and-replay.md) di Durable Functions richiede che le orchestrazioni siano deterministiche, il che crea una sfida aggiuntiva da considerare quando si distribuiscono gli aggiornamenti. Quando una distribuzione contiene modifiche alle firme delle funzioni attività o alla logica dell'agente di orchestrazione, le istanze di orchestrazione in corso hanno esito negativo Questa situazione è soprattutto un problema per le istanze di orchestrazioni a esecuzione prolungata, che potrebbero rappresentare ore o giorni di lavoro.

Per evitare che si verifichino questi errori, sono disponibili due opzioni: 
- Ritarda la distribuzione finché tutte le istanze di orchestrazione in esecuzione non sono state completate.
- Verificare che tutte le istanze di orchestrazione in esecuzione usino le versioni esistenti delle funzioni. 

> [!NOTE]
> Questo articolo fornisce indicazioni per le app per le funzioni destinate a Durable Functions 1. x. Non è stato aggiornato per tenere conto delle modifiche introdotte in Durable Functions 2. x. Per ulteriori informazioni sulle differenze tra le versioni delle estensioni, vedere [Durable Functions versioni](durable-functions-versions.md).

Nel grafico seguente vengono confrontate le tre strategie principali per ottenere una distribuzione senza tempi di inattività per Durable Functions: 

| Strategia |  Quando usare la funzionalità | Vantaggi | Svantaggi |
| -------- | ------------ | ---- | ---- |
| [Controllo delle versioni](#versioning) |  Applicazioni che non presentano [modifiche di rilievo frequenti.](durable-functions-versioning.md) | Semplice da implementare. |  Aumento delle dimensioni dell'app per le funzioni in memoria e numero di funzioni.<br/>Duplicazione del codice. |
| [Verifica dello stato con slot](#status-check-with-slot) | Sistema che non dispone di orchestrazioni a esecuzione prolungata che durano più di 24 ore o che si sovrappongono spesso. | Base di codice semplice.<br/>Non richiede la gestione aggiuntiva delle app per le funzioni. | Richiede un account di archiviazione o una gestione Hub attività aggiuntiva.<br/>Richiede periodi di tempo in cui non sono in esecuzione orchestrazioni. |
| [Routing delle applicazioni](#application-routing) | Sistema che non dispone di periodi di tempo durante i quali le orchestrazioni non sono in esecuzione, ad esempio i periodi di tempo con orchestrazioni che durano più di 24 ore o con orchestrazioni sovrapposte di frequente. | Gestisce le nuove versioni dei sistemi con orchestrazioni continuamente in esecuzione con modifiche di rilievo. | Richiede un router applicazione intelligente.<br/>Potrebbe essere il numero massimo di app per le funzioni consentite dalla sottoscrizione. Il valore predefinito è 100. |

## <a name="versioning"></a>Controllo delle versioni

Definire le nuove versioni delle funzioni e lasciare le versioni precedenti nell'app per le funzioni. Come si può notare nel diagramma, la versione di una funzione diventa parte del nome. Poiché le versioni precedenti delle funzioni vengono mantenute, le istanze di orchestrazione in corso possono continuare a farvi riferimento. Nel frattempo, le richieste di nuove istanze di orchestrazione chiamano la versione più recente, a cui la funzione del client di orchestrazione può fare riferimento da un'impostazione dell'app.

![Strategia di controllo delle versioni](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

In questa strategia, ogni funzione deve essere copiata e i relativi riferimenti ad altre funzioni devono essere aggiornati. È possibile semplificare la scrittura di uno script. Ecco un [progetto di esempio](https://github.com/TsuyoshiUshio/DurableVersioning) con uno script di migrazione.

>[!NOTE]
>Questa strategia USA gli slot di distribuzione per evitare tempi di inattività durante la distribuzione. Per informazioni più dettagliate su come creare e usare nuovi slot di distribuzione, vedere [slot di distribuzione di funzioni di Azure](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Verifica dello stato con slot

Mentre la versione corrente dell'app per le funzioni è in esecuzione nello slot di produzione, distribuire la nuova versione dell'app per le funzioni nello slot di staging. Prima di scambiare gli slot di produzione e di staging, verificare se sono presenti istanze di orchestrazione in esecuzione. Una volta completate tutte le istanze di orchestrazione, è possibile eseguire lo scambio. Questa strategia funziona quando si dispone di periodi stimabili quando nessuna istanza di orchestrazione è in fase di elaborazione. Si tratta dell'approccio migliore quando le orchestrazioni non sono a esecuzione prolungata e quando le esecuzioni dell'orchestrazione non si sovrappongono spesso.

### <a name="function-app-configuration"></a>Configurazione dell'app per le funzioni

Per configurare questo scenario, attenersi alla procedura riportata di seguito.

1. [Aggiungere gli slot di distribuzione](../functions-deployment-slots.md#add-a-slot) all'app per le funzioni per la gestione temporanea e la produzione.

1. Per ogni slot, impostare l' [impostazione dell'applicazione AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) sulla stringa di connessione di un account di archiviazione condiviso. Questa stringa di connessione dell'account di archiviazione viene usata dal runtime di funzioni di Azure. Questo account viene usato dal runtime di funzioni di Azure e gestisce le chiavi della funzione.

1. Per ogni slot, creare una nuova impostazione di app, ad esempio `DurableManagementStorage`. Impostarne il valore sulla stringa di connessione di account di archiviazione diversi. Questi account di archiviazione vengono usati dall'estensione Durable Functions per l' [esecuzione affidabile](durable-functions-checkpointing-and-replay.md). Usare un account di archiviazione separato per ogni slot. Non contrassegnare questa impostazione come impostazione dello slot di distribuzione.

1. Nella [sezione durableTask del file host. JSON](durable-functions-bindings.md#hostjson-settings)dell'app per le funzioni specificare `azureStorageConnectionStringName` come nome dell'impostazione dell'app creata nel passaggio 3.

Il diagramma seguente illustra la configurazione descritta degli slot di distribuzione e degli account di archiviazione. In questo potenziale scenario di pre-distribuzione, la versione 2 di un'app per le funzioni è in esecuzione nello slot di produzione, mentre la versione 1 rimane nello slot di staging.

![Slot di distribuzione e account di archiviazione](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>esempi di host. JSON

I frammenti JSON seguenti sono esempi di impostazioni della stringa di connessione nel file *host. JSON* .

#### <a name="functions-20"></a>Funzioni 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Configurazione della pipeline CI/CD

Configurare la pipeline CI/CD per la distribuzione solo quando l'app per le funzioni non dispone di istanze di orchestrazione in sospeso o in esecuzione. Quando si usa Azure Pipelines, è possibile creare una funzione che controlla le condizioni, come nell'esempio seguente:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Configurare quindi il Gate di gestione temporanea in modo che attenda che non siano in esecuzione orchestrazioni. Per altre informazioni, vedere [Release Deployment Control Using Gates](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Controllo distribuzione](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines controlla l'app per le funzioni per l'esecuzione di istanze di orchestrazione prima dell'avvio della distribuzione.

![Controllo distribuzione (in esecuzione)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

A questo punto la nuova versione dell'app per le funzioni deve essere distribuita nello slot di staging.

![Slot di staging](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Infine, scambia gli slot. 

Vengono anche scambiate le impostazioni dell'applicazione non contrassegnate come slot di distribuzione, quindi l'app versione 2 conserva il riferimento all'account di archiviazione A. Poiché lo stato dell'orchestrazione viene registrato nell'account di archiviazione, le orchestrazioni in esecuzione nell'app versione 2 continuano a essere eseguite nel nuovo slot senza interruzioni.

![Slot di distribuzione](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Per usare lo stesso account di archiviazione per entrambi gli slot, è possibile modificare i nomi degli hub attività. In questo caso, è necessario gestire lo stato degli slot e le impostazioni HubName dell'app. Per altre informazioni, vedere [Hub attività in Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Routing delle applicazioni

Questa strategia è la più complessa. Può tuttavia essere usato per le app per le funzioni che non hanno tempo tra l'esecuzione di orchestrazioni.

Per questa strategia, è necessario creare un *router dell'applicazione* davanti all'Durable Functions. Questo router può essere implementato con Durable Functions. Il router è responsabile di:

* Distribuire l'app per le funzioni.
* Gestire la versione di Durable Functions. 
* Inviare le richieste di orchestrazione alle app per le funzioni.

La prima volta che viene ricevuta una richiesta di orchestrazione, il router esegue le attività seguenti:

1. Crea una nuova app per le funzioni in Azure.
2. Distribuisce il codice dell'app per le funzioni nella nuova app per le funzioni in Azure.
3. Invia la richiesta di orchestrazione alla nuova app.

Il router gestisce lo stato della versione del codice dell'app in cui viene distribuita l'app per le funzioni in Azure.

![Routing applicazione (per la prima volta)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Il router indirizza le richieste di distribuzione e orchestrazione all'app per le funzioni appropriata in base alla versione inviata con la richiesta. Ignora la versione della patch.

Quando si distribuisce una nuova versione dell'app senza modifiche sostanziali, è possibile incrementare la versione della patch. Il router viene distribuito nell'app per le funzioni esistente e invia le richieste per le versioni precedenti e nuove del codice, che vengono instradate alla stessa app per le funzioni.

![Routing dell'applicazione (nessuna modifica di rilievo)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Quando si distribuisce una nuova versione dell'app con una modifica di rilievo, è possibile incrementare la versione principale o secondaria. Il router applicazione crea quindi una nuova app per le funzioni in Azure, la distribuisce e instrada le richieste per la nuova versione dell'app. Nel diagramma seguente, le orchestrazioni in esecuzione nella versione 1.0.1 dell'app continuano a essere eseguite, ma le richieste per la versione 1.1.0 vengono indirizzate alla nuova app per le funzioni.

![Routing applicazione (modifica di rilievo)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Il router monitora lo stato delle orchestrazioni nella versione 1.0.1 e rimuove le app al termine di tutte le orchestrazioni. 

### <a name="tracking-store-settings"></a>Impostazioni archivio di rilevamento

Ogni app per le funzioni deve usare code di pianificazione separate, possibilmente in account di archiviazione separati. Se si desidera eseguire una query su tutte le istanze di orchestrazioni in tutte le versioni dell'applicazione, è possibile condividere le tabelle di istanze e di cronologia nelle app per le funzioni. È possibile condividere le tabelle configurando le impostazioni `trackingStoreConnectionStringName` e `trackingStoreNamePrefix` nel file di [Impostazioni host. JSON](durable-functions-bindings.md#host-json) in modo che tutti usino gli stessi valori.

Per altre informazioni, vedere [gestire le istanze in Durable Functions in Azure](durable-functions-instance-management.md).

![Impostazioni archivio di rilevamento](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Controllo delle versioni Durable Functions](durable-functions-versioning.md)

