---
title: "Scalabilità a livello di codice di Azure Service Fabric | Documentazione Microsoft"
description: Aumentare o ridurre le istanze di un cluster Service Fabric in Azure a livello di codice in base a trigger personalizzati
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bfa020e29a9bb67f0634d220725bc11279e1565c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Aumentare o ridurre le istanze di un cluster di Service Fabric a livello di codice 

Le nozioni di base sulla scalabilità di un cluster Service Fabric in Azure sono trattate nella documentazione sulla [scalabilità del cluster](./service-fabric-cluster-scale-up-down.md). Questo articolo descrive come i cluster Service Fabric sono costruiti su set di scalabilità di macchine virtuali ed è possibile ottenere la scalabilità manualmente o usando regole di scalabilità automatica. Vengono esaminati i metodi a livello di codice per il coordinamento delle operazioni di scalabilità in Azure per scenari più avanzati. 

## <a name="reasons-for-programmatic-scaling"></a>Motivi per eseguire la scalabilità a livello di codice
In molti scenari, la scalabilità manuale o mediante regole di scalabilità automatica sono soluzioni valide. In altri scenari, tuttavia, potrebbero non essere la scelta adatta. Alcuni potenziali svantaggi di questi approcci sono:

- La scalabilità manuale richiede l'accesso e la richiesta esplicita delle operazioni di scalabilità. Questo approccio potrebbe non essere una soluzione valida se le operazioni di scalabilità vengono richieste di frequente oppure in momenti imprevisti.
- Quando le regole di scalabilità automatica rimuovono un'istanza da un set di scalabilità di macchine virtuali, non rimuovono automaticamente le informazioni di questo nodo dal cluster Service Fabric associato, a meno che il tipo di nodo non disponga di un livello di durabilità Silver o Gold. Poiché funzionano a livello di set di scalabilità anziché a livello di Service Fabric, le regole di scalabilità automatica possono rimuovere i nodi Service Fabric senza arrestarli in modo normale. Questo tipo di rimozione dei nodi lascerà il nodo Service Fabric nello stato "ghost" dopo le operazioni di riduzione delle istanze. Un utente o un servizio dovrà pulire periodicamente lo stato del nodo rimosso nel cluster Service Fabric.
  - Un tipo di nodo con un livello di durabilità Gold o Silver esegue automaticamente la pulizia dei nodi rimossi. Non è necessaria una pulizia aggiuntiva.
- Anche se le regole di scalabilità automatica supportano [molte metriche](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md), si tratta comunque di un set limitato. Se lo scenario richiede la scalabilità basata su alcune metriche non incluse in questo set, le regole di scalabilità automatica potrebbero non essere una soluzione valida.

Considerando queste limitazioni, è consigliabile implementare più modelli di scalabilità automatica personalizzati. 

## <a name="scaling-apis"></a>Scalabilità delle API
Esistono API Azure che consentono alle applicazioni di usare a livello di codice i set di scalabilità di macchine virtuali e i cluster Service Fabric. Se le opzioni di scalabilità automatica esistenti non funzionano per lo scenario specifico, queste API consentono di implementare una logica di scalabilità personalizzata. 

Un approccio all'implementazione di questa funzionalità di scalabilità automatica "interna" consiste nell'aggiungere un nuovo servizio senza stato all'applicazione Service Fabric per gestire le operazioni di scalabilità. All'interno del metodo `RunAsync` del servizio, un set di trigger può determinare se la scalabilità è necessaria, inclusi i parametri di controllo, come la dimensione massima di un cluster e i tempi di raffreddamento della scalabilità.   

L'API usata per le interazioni dei set di scalabilità di macchine virtuali, sia per verificare il numero corrente di istanze di macchine virtuali che per modificarlo, è la [libreria Azure Management Compute Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). La libreria Fluent fornisce un'API facile da usare per l'interazione con i set di scalabilità di macchine virtuali.

Per interagire con il cluster Service Fabric, usare [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Naturalmente non è necessario che il codice di scalabilità sia in esecuzione come servizio nel cluster di cui modificare la scalabilità. Sia `IAzure` sia `FabricClient` possono connettersi in remoto alle risorse di Azure associate; il servizio di scalabilità può quindi essere facilmente un'applicazione console o il servizio Windows in esecuzione dall'esterno dell'applicazione Service Fabric. 

## <a name="credential-management"></a>Gestione delle credenziali
Un problema in fase di scrittura di un servizio per gestire la scalabilità è che il servizio deve essere in grado di accedere alle risorse dei set di scalabilità di macchine virtuali senza un accesso interattivo. L'accesso al cluster Service Fabric è semplice se il servizio di scalabilità sta modificando la propria applicazione Service Fabric, ma sono necessarie le credenziali per accedere al set di scalabilità. Per accedere, è possibile usare un'[entità servizio](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) creata con l'[interfaccia della riga di comando di Azure 2.0](https://github.com/azure/azure-cli).

È possibile creare un'entità servizio con i passaggi seguenti:

1. Accedere all'interfaccia della riga di comando di Azure (`az login`) come utente con accesso al set di scalabilità di macchine virtuali
2. Creare l'entità servizio con `az ad sp create-for-rbac`
    1. Prendere nota di appId (denominato altrove "ID client"), nome, password e tenant per un uso successivo.
    2. Sarà inoltre necessario l'ID sottoscrizione, che può essere visualizzato con `az account list`

La libreria di calcolo Fluent può eseguire l'accesso usando queste credenziali come indicato di seguito. Si noti che i tipi di Azure fluent principali, ad esempio `IAzure`, sono nel pacchetto [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Una volta eseguito l'accesso, è possibile eseguire una query del numero di istanze di set di scalabilità tramite `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Aumento del numero di istanze
Usando l'SDK di calcolo di Azure Fluent è possibile aggiungere istanze al set di scalabilità di macchine virtuali con poche chiamate.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

In alternativa, le dimensioni del set di scalabilità di macchine virtuali possono essere gestite anche con i cmdlet di PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) consente di recuperare l'oggetto set di scalabilità di macchine virtuali. La capacità corrente è disponibile tramite la proprietà `.sku.capacity`. Dopo avere impostato la capacità sul valore desiderato, il set di scalabilità di macchine virtuali in Azure può essere aggiornato con il comando [`Update-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

Quando si aggiunge manualmente un nodo, l'aggiunta di un'istanza del set di scalabilità dovrebbe essere sufficiente per avviare un nuovo nodo Service Fabric in quanto il modello del set di scalabilità include le estensioni per aggiungere automaticamente nuove istanze al cluster Service Fabric. 

## <a name="scaling-in"></a>Riduzione

La riduzione è simile all'aumento del numero di istanze. Le modifiche effettive al set di scalabilità di macchine virtuali sono praticamente le stesse. Tuttavia, come illustrato in precedenza, Service Fabric pulisce automaticamente solo i nodi rimossi con la durabilità Gold o Silver. Pertanto, nel caso di riduzione con la durabilità Bronze, è necessario interagire con il cluster Service Fabric per arrestare il nodo da rimuovere, quindi rimuovere il relativo stato.

La preparazione del nodo per l'arresto implica la ricerca del nodo da rimuovere, ovvero l'istanza del set di scalabilità di macchine virtuali aggiunta più di recente, e la relativa disattivazione. Le istanze del set di scalabilità di macchine virtuali sono numerate nell'ordine in cui sono state aggiunte, quindi i nodi più recenti si possono trovare confrontando il suffisso numerico nei nomi dei nodi, che corrispondono ai nomi delle istanze del set di scalabilità di macchine virtuali sottostanti. 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Dopo avere individuato il nodo da rimuovere, questo può essere disattivato e rimosso usando la stessa istanza `FabricClient` e l'istanza `IAzure` precedente.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Anche in questo caso, come per l'aumento del numero di istanze, è possibile usare i cmdlet di PowerShell per modificare la capacità del set di scalabilità di macchine virtuali, se si preferisce un approccio basato sugli script. Dopo avere rimosso l'istanza di macchina virtuale, è possibile rimuovere lo stato del nodo Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Potenziali svantaggi

Come illustrato nei frammenti di codice precedenti, la creazione di un servizio di scalabilità proprio offre il massimo livello di controllo e personalizzazione sul comportamento di scalabilità dell'applicazione. Questa condizione può essere utile negli scenari che richiedono un controllo preciso su quando e come aumentare o ridurre il numero di istanze dell'applicazione. Tuttavia, questo controllo implica un compromesso a livello di complessità del codice. Questo approccio richiede un codice di scalabilità proprio, il che non è semplice.

L'approccio da scegliere per la scalabilità di Service Fabric dipende dallo scenario specifico. Se la scalabilità non è comune, la possibilità di aggiungere o rimuovere nodi manualmente è probabilmente sufficiente. Per gli scenari più complessi, gli SDK e le regole di scalabilità automatica che espongono la capacità di eseguire la scalabilità a livello di codice offrono alternative molto efficaci.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a implementare la logica di scalabilità automatica, acquisire familiarità con i seguenti concetti e le utili API:

- [Scalabilità manuale o con regole di scalabilità automatica](./service-fabric-cluster-scale-up-down.md)
- [Fluent Azure Management Libraries per .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (utili per l'interazione con i set di scalabilità di macchine virtuali sottostanti del cluster Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (utile per l'interazione con un cluster Service Fabric e i relativi nodi)
