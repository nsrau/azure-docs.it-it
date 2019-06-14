---
title: Pianificazione della capacità e scalabilità per Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per la pianificazione e il ridimensionamento di cluster e applicazioni Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 58c50eac60f1a8a47aac9a88125bc3e0132ec3db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059152"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Pianificazione della capacità e scalabilità per Azure Service Fabric

Prima di creare tutti i cluster Azure Service Fabric oppure ridimensionare le risorse che ospitano il cluster di calcolo, è importante pianificare la capacità. Per altre informazioni sulla pianificazione della capacità, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Per altre indicazioni di procedure consigliate per la scalabilità del cluster, vedere [considerazioni sulla scalabilità di Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Oltre a prendere in considerazione le caratteristiche di cluster e tipo di nodo, è necessario prevedere che le operazioni di ridimensionamento richieda più di un'ora per il completamento di un ambiente di produzione. Questa considerazione vale indipendentemente dal numero di macchine virtuali che si sta aggiungendo.

## <a name="autoscaling"></a>Scalabilità automatica
È consigliabile eseguire le operazioni di scalabilità tramite i modelli di Azure Resource Manager, perché è la procedura consigliata per trattare [configurazioni di risorse come codice]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Tramite la scalabilità automatica tramite set di scalabilità di macchine virtuali renderà il modello di Resource Manager con controllo delle versioni in modo non corretto definire il numero di istanze per set di scalabilità di macchine virtuali. Definizione non accurati aumenta il rischio che le distribuzioni future fa sì che le operazioni di scalabilità non intenzionali. In generale, è consigliabile usare la scalabilità automatica se:

* La distribuzione di modelli di Resource Manager con un'opportuna capacità dichiarata non è adatta al proprio caso.
     
   Oltre a scalabilità manuale, è possibile configurare un [integrazione e recapito pipeline continua in servizi di Azure DevOps con i progetti di distribuzione gruppo di risorse di Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Questa pipeline normalmente viene generata da un'app per la logica che usa le metriche delle prestazioni di macchine virtuali sottoposte a query dal [API REST di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). La pipeline in modo efficace viene ridimensionato automaticamente in base per tutte le metriche desiderate, durante l'ottimizzazione dei modelli di Resource Manager.
* È necessario scalare orizzontalmente solo un nodo di set di scalabilità di macchine virtuali alla volta.
   
   Per la scalabilità orizzontale da tre o più nodi contemporaneamente, dovrebbe [scala orizzontalmente un cluster di Service Fabric mediante l'aggiunta di un set di scalabilità di macchine virtuali](virtual-machine-scale-set-scale-node-type-scale-out.md). È consigliabile eseguire la scalabilità in e out di scalabilità di macchine virtuali dei set di scalabilità orizzontale, un nodo alla volta.
* Si dispone di affidabilità argento o superiore per il cluster di Service Fabric e livello di durabilità Silver o superiore di qualsiasi dimensione in cui si configurare regole di scalabilità automatica.
  
   La capacità minima per le regole di scalabilità automatica deve essere uguale o maggiore di cinque istanze di macchina virtuale. Deve inoltre essere uguale o maggiore di almeno il livello di affidabilità per il tipo di nodo primario.

> [!NOTE]
> Infrastruttura di servizio con stato di Service Fabric: / System/InfastructureService/< NODE_TYPE_NAME > viene eseguito su ogni tipo di nodo con durabilità Silver o superiore. È il servizio di sistema solo che è supportato per l'esecuzione in Azure in uno qualsiasi dei tipi di nodo cluster.

## <a name="vertical-scaling-considerations"></a>Considerazioni sul ridimensionamento verticale

[Il ridimensionamento verticale](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) un tipo di nodo in Azure Service Fabric richiede un numero di passaggi e considerazioni. Ad esempio:

* Prima del ridimensionamento, il cluster deve essere integro, In caso contrario, si sarà destabilizzare ulteriormente il cluster.
* Livello di durabilità Silver o a un livello supera è obbligatorio per tutti i tipi di nodo del cluster Service Fabric che ospitano servizi con stato.

> [!NOTE]
> Il tipo di nodo primario ospita i servizi di sistema di Service Fabric con stato deve essere maggiore o a un livello di durabilità Silver. Dopo aver abilitato la durabilità Silver, operazioni di cluster, ad esempio gli aggiornamenti, aggiunta o rimozione di nodi e così via, sarà più lento perché consente di ottimizzare il sistema per la sicurezza dei dati rispetto alla velocità delle operazioni.

Un set di scalabilità di macchine virtuali di scalabilità verticale è un'operazione distruttiva. Al contrario, scalare orizzontalmente il cluster mediante l'aggiunta di un nuovo set di scalabilità con lo SKU desiderato. Quindi, eseguire la migrazione dei servizi per lo SKU desiderato per completare un'operazione di ridimensionamento verticale sicuro. La modifica di una risorsa di set di scalabilità di macchine virtuali SKU è un'operazione distruttiva perché ricrea l'immagine dell'host, in locale che rimuove uno stato persistente.

Il cluster Usa Service Fabric [proprietà del nodo e vincoli di posizionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) deve decidere dove ospitare i servizi dell'applicazione. Quando si esegue scalabilità verticale il tipo di nodo primario, dichiarare i valori di proprietà identici per `"nodeTypeRef"`. È possibile trovare questi valori nell'estensione di Service Fabric per il set di scalabilità di macchine virtuali. 

Il frammento seguente di un modello di Resource Manager illustra le proprietà che è possibile dichiarare. Ha lo stesso valore per i set di scalabilità appena sottoposti a provisioning ridimensionate per ed è supportata solo come un servizio con stato temporaneo per il cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Non lasciare il cluster in esecuzione con più set di scalabilità che utilizzano la stessa `nodeTypeRef` valore della proprietà più tempo necessario per completare un'operazione di ridimensionamento verticale completata correttamente.
>
> Convalidare sempre operazioni negli ambienti di test prima di provare le modifiche all'ambiente di produzione. Per impostazione predefinita, servizi di sistema del cluster di Service Fabric hanno un vincolo di posizionamento solo al tipo di nodo primario di destinazione.

Con le proprietà dei nodi e i vincoli di posizionamento dichiarati, eseguire i passaggi seguenti in un'istanza di macchina virtuale alla volta. In questo modo i servizi di sistema (e i servizi con stato) essere chiuso normalmente nell'istanza di VM da rimuovere quando vengono create nuove repliche altrove.

1. Da PowerShell, eseguire `Disable-ServiceFabricNode` preventivo `RemoveNode` per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Ad esempio, se si dispone di un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre il numero di macchine virtuali da uno di quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere le fasi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

### <a name="example-scenario"></a>Scenario di esempio
È uno scenario supportato cui si vuole eseguire un'operazione di ridimensionamento verticale: si vuole eseguire la migrazione del cluster di Service Fabric e dell'applicazione da un disco non gestito a managed disks senza tempi di inattività dell'applicazione. 

È possibile effettuare il provisioning di un nuovo set di scalabilità di macchine virtuali con dischi gestiti ed eseguire l'aggiornamento di un'applicazione con i vincoli di posizionamento destinate a capacità con provisioning. Cluster di Service Fabric è quindi possibile programmare il carico di lavoro nella capacità del nodo cluster con provisioning che viene implementata dal dominio di aggiornamento senza tempi di inattività dell'applicazione. 

Gli endpoint di pool back-end per il [SKU di Azure Load Balancer Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) possono essere macchine virtuali in un set di disponibilità o un set di scalabilità di macchine virtuali. Ciò significa che se si trasferiscono le applicazioni di sistemi di Service Fabric tra set di scalabilità, senza causare inaccessibilità temporaneo per l'endpoint di Gestione cluster di Service Fabric, è possibile utilizzare un servizio di bilanciamento del carico dello SKU Basic. Questo vale anche se il cluster e la relativa applicazione sono ancora in esecuzione.

Gli utenti in genere eseguire il provisioning di un servizio di bilanciamento del carico dello SKU Standard quando si esegue uno scambio di indirizzo (VIP) IP virtuale tra SKU Basic load balancer e le risorse del servizio di bilanciamento di carico dello SKU Standard. Questa tecnica consente di limitare qualsiasi futura inaccessibilità per circa 30 secondi, necessario per lo scambio VIP.

## <a name="horizontal-scaling"></a>Scalabilità orizzontale

È possibile eseguire una scalabilità orizzontale [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) oppure [a livello di programmazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se si sta il ridimensionamento di un tipo di nodo con durabilità Gold o Silver, ridimensionamento verrà eseguite lentamente.

### <a name="scaling-out"></a>Aumento del numero di istanze

Scalare orizzontalmente un cluster di Service Fabric, aumentando il numero di istanze per un set di scalabilità di macchina virtuale specifica. È possibile scalare orizzontalmente a livello di programmazione usando `AzureClient` e l'ID per il set per aumentare la capacità di scalabilità desiderata.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Per scalare orizzontalmente manualmente, aggiornare la capacità nella proprietà SKU dell'oggetto desiderato [set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) risorsa.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Riduzione

La riduzione del numero di istanze richiede qualche considerazione in più rispetto all'aumento. Ad esempio:

* Servizi di sistema Service Fabric vengono eseguiti nel tipo di nodo primario del cluster. Non arrestare né ridurre mai il numero di istanze in questo tipo di nodo per evitare di avere un numero di istanze inferiore a quello garantito dal livello di affidabilità. 
* Per un servizio con stato, è necessario un certo numero di nodi che sono sempre a mantenere la disponibilità e lo stato del servizio. Come minimo, è necessario un numero di nodi uguale per il numero di set di repliche di destinazione della partizione o del servizio.

Per ridurre il numero di istanze, seguire questi passaggi:

1. Da PowerShell, eseguire `Disable-ServiceFabricNode` preventivo `RemoveNode` per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Ad esempio, se si dispone di un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre il numero di macchine virtuali da uno di quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere i passaggi da 1 a 3 in base alle esigenze fino a quando non si effettua il provisioning di capacità desiderate. Non ridurre il numero di istanze nei tipi di nodo primari a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Per ridimensionare manualmente, aggiornare la capacità nella proprietà SKU dell'oggetto desiderato [set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) risorsa.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

È necessario preparare il nodo per scalare in a livello di programmazione dell'arresto. Trovare il nodo da rimuovere (il nodo più alto istanza). Ad esempio:

```c#
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

Disattivare e rimuovere il nodo utilizzando le stesse `FabricClient` istanza (`client` in questo caso) e l'istanza di nodo (`instanceIdString` in questo caso) usato nel codice precedente:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Quando si ridimensiona un cluster, si noterà l'istanza di rimozione nodo/macchina virtuale visualizzato in uno stato non integro in Service Fabric Explorer. Per una spiegazione di questo comportamento, vedere [comportamenti è possibile osservare in Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). È possibile:
> * Chiamare il [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) con il nome del nodo appropriato.
> * Distribuire il [dell'applicazione helper di scalabilità automatica di Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) nel cluster. Questa applicazione assicura che i nodi ridotta vengono cancellati da Service Fabric Explorer.

## <a name="reliability-levels"></a>Livelli di affidabilità

Il [a livello di affidabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) è una proprietà della risorsa cluster di Service Fabric. Non può essere configurato in modo diverso per singoli tipi di nodo. Questo livello controlla il fattore di replica dei servizi di sistema per il cluster ed è definito tramite un'impostazione a livello di risorsa cluster. 

Il livello di affidabilità determina il numero minimo di nodi che deve avere il tipo di nodo primario. Il livello di affidabilità può avere i valori seguenti:

* Platinum: Esegue i servizi di sistema con un numero di set di repliche di destinazione di sette e nove nodi di inizializzazione.
* Gold: Esegue i servizi di sistema con un numero di set di repliche di destinazione di sette e sette nodi di inizializzazione.
* Silver: Esegue i servizi di sistema con un numero di set di repliche di destinazione di cinque e cinque nodi di inizializzazione.
* Bronze: Esegue i servizi di sistema con un numero di set di repliche di destinazione di tre e tre nodi di inizializzazione.

Il livello minimo di affidabilità consigliato è Silver.

Il livello di affidabilità è impostato nella sezione delle proprietà della [risorsa Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), come in questo esempio:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Livelli di durabilità

> [!WARNING]
> I tipi di nodo in esecuzione con durabilità Bronze _non ottengono privilegi_. Processi dell'infrastruttura che influiscono su carichi di lavoro senza stati non verrà arrestati o ritardati, che possono influire sui carichi di lavoro. 
>
> Usare il livello di durabilità Bronze solo per i tipi di nodo che eseguono carichi di lavoro senza stato. Per i carichi di lavoro di produzione, eseguire argento o superiore per garantire la coerenza dello stato. Scegliere il livello di affidabilità appropriato seguendo le indicazioni riportate nella [documentazione sulla pianificazione della capacità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Il livello di durabilità deve essere impostato in due risorse: Uno è il profilo di estensione del [risorsa del set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

L'altra risorsa è sotto `nodeTypes` nella [risorsa servicefabric/Clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
