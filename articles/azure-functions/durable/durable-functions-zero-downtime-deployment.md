---
title: Distribuzione di tempo di inattività zero per le funzioni durevoli
description: Informazioni su come abilitare l'orchestrazione di funzioni permanenti per distribuzioni con tempo di inattività zero.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231264"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Distribuzione di tempo di inattività zero per le funzioni durevoli

Il modello di [esecuzione affidabile](durable-functions-checkpointing-and-replay.md) di funzioni durevoli richiede che le orchestrazioni siano deterministiche, il che crea un'ulteriore sfida da considerare quando si distribuiscono gli aggiornamenti. Quando una distribuzione contiene modifiche alle firme delle funzioni di attività o alla logica dell'agente di orchestrazione, le istanze di orchestrazione in corso hanno esito negativo. Questa situazione è particolarmente un problema per le istanze di orchestrazioni a esecuzione prolungata, che potrebbero rappresentare ore o giorni di lavoro.

Per evitare che si verifichino questi errori, sono disponibili due opzioni:To prevent these failures happening, you have two options: 
- Ritardare la distribuzione fino al completamento di tutte le istanze di orchestrazione in esecuzione.
- Assicurarsi che tutte le istanze di orchestrazione in esecuzione utilizzino le versioni esistenti delle funzioni. 

> [!NOTE]
> Questo articolo fornisce indicazioni per le app di funzioni destinate a Funzioni durevoli 1.x.This article provides guidance for functions apps that target Durable Functions 1.x. Non è stato aggiornato per tenere conto delle modifiche introdotte in Funzioni durevoli 2.x. Per ulteriori informazioni sulle differenze tra le versioni delle estensioni, vedere Versioni di [Funzioni permanenti](durable-functions-versions.md).

The following chart compares the three main strategies to achieve a zero-downtime deployment for Durable Functions: 

| Strategia |  Utilizzo | Vantaggi | Svantaggi |
| -------- | ------------ | ---- | ---- |
| [Controllo delle versioni](#versioning) |  Applicazioni che non riscontrano [frequenti modifiche di rilievo.](durable-functions-versioning.md) | Semplice da implementare. |  Aumento delle dimensioni dell'app per le funzioni in memoria e del numero di funzioni.<br/>Duplicazione del codice. |
| [Controllo dello stato con slot](#status-check-with-slot) | Sistema che non dispone di orchestrazioni a esecuzione prolungata della durata superiore a 24 ore o che si sovrappongono di frequente orchestrazioni. | Base di codice semplice.<br/>Non richiede la gestione di app con funzioni aggiuntive. | Richiede una gestione aggiuntiva dell'account di archiviazione o dell'hub attività.<br/>Richiede periodi di tempo in cui non sono in esecuzione orchestrazioni. |
| [Routing delle applicazioni](#application-routing) | Sistema che non dispone di periodi di tempo in cui le orchestrazioni non sono in esecuzione, ad esempio i periodi di tempo con orchestrazioni che durano più di 24 ore o con orchestrazioni spesso sovrapposte. | Gestisce le nuove versioni dei sistemi con orchestrazioni in esecuzione continuamente con modifiche di rilievo. | Richiede un router intelligente dell'applicazione.<br/>Potrebbe massimizzare il numero di app per le funzioni consentite dall'abbonamento. Il valore predefinito è 100. |

## <a name="versioning"></a>Controllo delle versioni

Definire nuove versioni delle funzioni e lasciare le versioni precedenti nell'app per le funzioni. Come si può vedere nel diagramma, la versione di una funzione diventa parte del suo nome. Poiché le versioni precedenti delle funzioni vengono mantenute, le istanze di orchestrazione in corso possono continuare a farvi riferimento. Nel frattempo, le richieste per le nuove istanze di orchestrazione richiedono la versione più recente, a cui la funzione client dell'orchestrazione può fare riferimento da un'impostazione dell'app.

![Strategia di controllo delle versioni](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

In questa strategia, ogni funzione deve essere copiata e i relativi riferimenti ad altre funzioni devono essere aggiornati. È possibile semplificare la scrittura di uno script. Ecco un [progetto di esempio](https://github.com/TsuyoshiUshio/DurableVersioning) con uno script di migrazione.

>[!NOTE]
>Questa strategia usa gli slot di distribuzione per evitare tempi di inattività durante la distribuzione. Per informazioni più dettagliate su come creare e usare nuovi slot di distribuzione, vedere Slot di distribuzione di Funzioni di Azure.For more detailed information about how to create and use new deployment [slots,](../functions-deployment-slots.md)see Azure Functions deployment slots .

## <a name="status-check-with-slot"></a>Controllo dello stato con slot

Mentre la versione corrente dell'app per le funzioni è in esecuzione nello slot di produzione, distribuisci la nuova versione dell'app per le funzioni nello slot di gestione temporanea. Prima di scambiare gli slot di produzione e gestione temporanea, verificare se sono presenti istanze di orchestrazione in esecuzione. Dopo aver completato tutte le istanze di orchestrazione, è possibile eseguire lo scambio. Questa strategia funziona quando si hanno periodi prevedibili in cui non sono in corso istanze di orchestrazione. Questo è l'approccio migliore quando le orchestrazioni non sono a esecuzione prolungata e quando le esecuzioni orchestrazioni non si sovrappongono frequentemente.

### <a name="function-app-configuration"></a>Configurazione dell'app per le funzioni

Utilizzare la procedura seguente per impostare questo scenario.

1. [Aggiungere slot](../functions-deployment-slots.md#add-a-slot) di distribuzione all'app per le funzioni per la gestione temporanea e la produzione.

1. Per ogni slot, impostare [l'impostazione dell'applicazione AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) sulla stringa di connessione di un account di archiviazione condivisa. Questa stringa di connessione dell'account di archiviazione viene usata dal runtime di Funzioni di Azure.This storage account connection string is used by the Azure Functions runtime. Questo account viene usato dal runtime di Funzioni di Azure e gestisce le chiavi della funzione.

1. Per ogni slot, creare una nuova `DurableManagementStorage`impostazione dell'app, ad esempio . Impostare il valore sulla stringa di connessione di account di archiviazione diversi. Questi account di archiviazione vengono usati dall'estensione Funzioni permanenti per [un'esecuzione affidabile.](durable-functions-checkpointing-and-replay.md) Usare un account di archiviazione separato per ogni slot. Non contrassegnare questa impostazione come impostazione dello slot di distribuzione.

1. Nella [sezione durableTask del file host.json dell'app](durable-functions-bindings.md#hostjson-settings)per le funzioni specificare `azureStorageConnectionStringName` il nome dell'impostazione dell'app creata nel passaggio 3.

Nel diagramma seguente viene illustrata la configurazione descritta degli slot di distribuzione e degli account di archiviazione. In questo potenziale scenario di pre-distribuzione, la versione 2 di un'app per le funzioni è in esecuzione nello slot di produzione, mentre la versione 1 rimane nello slot di gestione temporanea.

![Slot di distribuzione e account di archiviazione](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Esempi di host.json

I frammenti JSON seguenti sono esempi dell'impostazione della stringa di connessione nel file *host.json.*

#### <a name="functions-20"></a>Funzioni 2.0

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

### <a name="cicd-pipeline-configuration"></a>Configurazione pipeline CI/CD

Configurare la pipeline CI/CD per la distribuzione solo quando l'app per le funzioni non contiene istanze di orchestrazione in sospeso o in esecuzione. Quando si usa le pipeline di Azure, è possibile creare una funzione che controlla queste condizioni, come nell'esempio seguente:When you're using Azure Pipelines, you can create a function that checks for these conditions, as in the following example:

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

Configurare quindi il gate di gestione temporanea in modo che attenda fino a quando non sono in esecuzione orchestrazioni. Per altre informazioni, vedere Controllo della distribuzione di [rilascio tramite gateFor](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) more information, see Release deployment control using gates

![Attività di controllo di distribuzione](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Pipeline di Azure controlla l'app per le funzioni per l'esecuzione di istanze di orchestrazione prima dell'avvio della distribuzione.

![Gate di distribuzione (in esecuzione)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Ora la nuova versione dell'app per le funzioni deve essere distribuita nello slot di gestione temporanea.

![Slot di gestione temporanea](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Infine, scambiare gli slot. 

Anche le impostazioni dell'applicazione che non sono contrassegnate come impostazioni dello slot di distribuzione vengono scambiate, pertanto l'app versione 2 mantiene il riferimento all'account di archiviazione A.Application settings that aren't marked as deployment slot settings are also swapped, so the version 2 app keeps its reference to storage account A. Poiché lo stato dell'orchestrazione viene registrato nell'account di archiviazione, tutte le orchestrazioni in esecuzione nell'app versione 2 continuano a essere eseguite nel nuovo slot senza interruzioni.

![Slot di distribuzione](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Per usare lo stesso account di archiviazione per entrambi gli slot, è possibile modificare i nomi degli hub attività. In questo caso, devi gestire lo stato degli slot e le impostazioni HubName dell'app. Per altre informazioni, vedere [Hub attività in Funzioni durevoli](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Routing delle applicazioni

Questa strategia è la più complessa. Tuttavia, può essere usato per le app per le funzioni che non hanno tempo tra l'esecuzione di orchestrazioni.

Per questa strategia, è necessario creare un *router dell'applicazione* davanti alle funzioni durevoli. Questo router può essere implementato con funzioni durevoli. Il router ha la responsabilità di:

* Distribuire l'app per le funzioni.
* Gestire la versione di Funzioni durevoli. 
* Indirizzare le richieste di orchestrazione alle app per le funzioni.

La prima volta che viene ricevuta una richiesta di orchestrazione, il router esegue le attività seguenti:The first time an orchestration request is received, the router does the following tasks:

1. Crea una nuova app per le funzioni in Azure.
2. Distribuisce il codice dell'app per le funzioni nella nuova app per le funzioni in Azure.
3. Inoltra la richiesta di orchestrazione alla nuova app.

Il router gestisce lo stato della versione del codice dell'app distribuita in quale app per le funzioni in Azure.

![Routing delle applicazioni (prima volta)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Il router indirizza le richieste di distribuzione e orchestrazione all'app per le funzioni appropriata in base alla versione inviata con la richiesta. Ignora la versione della patch.

Quando distribuisci una nuova versione dell'app senza una modifica sostanziale, puoi incrementare la versione della patch. Il router viene distribuito nell'app per le funzioni esistente e invia le richieste per le versioni precedenti e nuove del codice, che vengono instradate alla stessa app per le funzioni.

![Routing dell'applicazione (nessuna modifica di rilievo)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Quando distribuisci una nuova versione dell'app con una modifica di rilievo, puoi incrementare la versione principale o secondaria. Il router dell'applicazione crea quindi una nuova app per le funzioni in Azure, viene distribuita e indirizza le richieste per la nuova versione dell'app. Nel diagramma seguente, l'esecuzione di orchestrazioni nella versione 1.0.1 dell'app continua a essere in esecuzione, ma le richieste per la versione 1.1.0 vengono instradate alla nuova app per le funzioni.

![Routing dell'applicazione (modifica di rilievo)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Il router monitora lo stato delle orchestrazioni nella versione 1.0.1 e rimuove le app al termine di tutte le orchestrazioni. 

### <a name="tracking-store-settings"></a>Rilevamento delle impostazioni dell'archivio

Ogni app per le funzioni deve usare code di pianificazione separate, possibilmente in account di archiviazione separati. Se si desidera eseguire una query su tutte le istanze di orchestrazioni in tutte le versioni dell'applicazione, è possibile condividere tabelle di istanze e cronologia tra le app per le funzioni. È possibile condividere le `trackingStoreConnectionStringName` tabelle `trackingStoreNamePrefix` configurando le impostazioni e nel file di [impostazioni host.json](durable-functions-bindings.md#host-json) in modo che utilizzino tutti gli stessi valori.

Per altre informazioni, vedere [Gestire le istanze in Funzioni durevoli in Azure.For](durable-functions-instance-management.md)more information, see Manage instances in Durable Functions in Azure.

![Rilevamento delle impostazioni dell'archivio](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Controllo delle versioni delle funzioni durevoliVersioning Durable Functions](durable-functions-versioning.md)

