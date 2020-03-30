---
title: Pianificazione della capacità e scalabilità per Azure Service FabricCapacity planning and scaling for Azure Service Fabric
description: Procedure consigliate per la pianificazione e il ridimensionamento di cluster e applicazioni Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258993"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Pianificazione della capacità e scalabilità per Azure Service FabricCapacity planning and scaling for Azure Service Fabric

Prima di creare qualsiasi cluster di Azure Service Fabric o ridimensionare le risorse di calcolo che ospitano il cluster, è importante pianificare la capacità. Per altre informazioni sulla pianificazione della capacità, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Per ulteriori indicazioni sulle procedure consigliate per la scalabilità dei cluster, vedere [Considerazioni sulla scalabilità](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)di Service Fabric .

Oltre a considerare le caratteristiche del tipo di nodo e del cluster, è necessario prevedere che il completamento delle operazioni di ridimensionamento richiederà più di un'ora per un ambiente di produzione. Questa considerazione è vera indipendentemente dal numero di macchine virtuali che si sta aggiungendo.

## <a name="autoscaling"></a>Scalabilità automatica
È consigliabile eseguire operazioni di ridimensionamento tramite modelli di Azure Resource Manager, perché è consigliabile considerare [le configurazioni delle risorse come codice.]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) 

L'uso della scalabilità automatica tramite set di scalabilità di macchine virtuali consentirà al modello di Resource Manager con controllo delle versioni di definire in modo impreciso i conteggi delle istanze per i set di scalabilità delle macchine virtuali. Una definizione imprecisa aumenta il rischio che le distribuzioni future provochino operazioni di ridimensionamento indesiderate. In generale, è consigliabile usare la scalabilità automatica se:In general, you should use autoscaling if:

* La distribuzione di modelli di Resource Manager con un'opportuna capacità dichiarata non è adatta al proprio caso.
     
   Oltre al ridimensionamento manuale, è possibile configurare una pipeline di [integrazione e recapito continua nei servizi DevOps](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)di Azure usando i progetti di distribuzione dei gruppi di risorse di Azure. Questa pipeline viene in genere attivata da un'app per la logica che usa le metriche delle prestazioni delle macchine virtuali sottoposte a query dall'API REST di Monitoraggio di Azure.This pipeline is commonly triggered by a logic app that uses virtual machine performance metrics queryed from the [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). La pipeline viene ridimensionata automaticamente in base alle metriche desiderate, ottimizzando al contempo i modelli di Resource Manager.The pipeline effectively autoscales based on whatever metrics you want, while optimizing for Resource Manager templates.
* È necessario ridimensionare orizzontalmente un solo nodo del set di scalabilità di macchine virtuali alla volta.
   
   Per aumentare la scalabilità orizzontale di tre o più nodi alla volta, è consigliabile scalare orizzontalmente un cluster di Service Fabric aggiungendo un set di scalabilità di [macchine virtuali.](virtual-machine-scale-set-scale-node-type-scale-out.md) È più sicuro scalare orizzontalmente e scalare orizzontalmente i set di scalabilità delle macchine virtuali, un nodo alla volta.
* Si dispone di affidabilità Silver o superiore per il cluster Service Fabric e durabilità Silver o superiore su qualsiasi scala in cui si configurano le regole di scalabilità automatica.
  
   La capacità minima per le regole di scalabilità automatica deve essere uguale o maggiore di cinque istanze di macchina virtuale. Deve inoltre essere uguale o maggiore del livello di affidabilità minimo per il tipo di nodo primario.

> [!NOTE]
> Il servizio di gestione dello stato di Service Fabric fabric:/System/InfastructureService/<NODE_TYPE_NAME> viene eseguito su ogni tipo di nodo con durata Silver o superiore. È l'unico servizio di sistema supportato per l'esecuzione in Azure in qualsiasi tipo di nodo cluster.

## <a name="vertical-scaling-considerations"></a>Considerazioni sul ridimensionamento verticale

[La scalabilità verticale](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) di un tipo di nodo in Azure Service Fabric richiede una serie di passaggi e considerazioni. Ad esempio:

* Prima del ridimensionamento, il cluster deve essere integro, In caso contrario, si destabilizza ulteriormente il cluster.
* Il livello di durabilità dell'argento o superiore è necessario per tutti i tipi di nodo del cluster di Service Fabric che ospitano servizi con stato.

> [!NOTE]
> Il tipo di nodo primario che ospita i servizi di sistema di Service Fabric con stato deve essere Livello di durabilità Silver o superiore. Dopo aver abilitato la durabilità dell'argento, le operazioni del cluster, ad esempio gli aggiornamenti, l'aggiunta o la rimozione di nodi e così via, saranno più lente perché il sistema ottimizza la sicurezza dei dati sulla velocità delle operazioni.

La scalabilità verticale di un set di scalabilità di macchine virtuali è un'operazione distruttiva. Al contrario, scalare orizzontalmente il cluster aggiungendo un nuovo set di scalabilità con lo SKU desiderato. Eseguire quindi la migrazione dei servizi allo SKU desiderato per completare un'operazione di ridimensionamento verticale sicuro. La modifica di uno SKU di risorse del set di scalabilità di una macchina virtuale è un'operazione distruttiva perché riconsente di ricreare gli host, rimuovendo tutti gli stati persistenti in locale.

Il cluster usa le proprietà del nodo di Service Fabric [e i vincoli](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) di posizionamento per decidere dove ospitare i servizi dell'applicazione. Quando si ridimensiona verticalmente il tipo di nodo `"nodeTypeRef"`primario, dichiarare valori di proprietà identici per . Questi valori sono disponibili nell'estensione Service Fabric per i set di scalabilità delle macchine virtuali. 

Il frammento di codice seguente di un modello di Resource Manager mostra le proprietà che dichiarerai. Ha lo stesso valore per i set di scalabilità appena sottoposti a provisioning in base ai quali si esegue la scalabilità ed è supportato solo come servizio temporaneo con stato per il cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Non lasciare il cluster in esecuzione con più `nodeTypeRef` set di scalabilità che usano lo stesso valore di proprietà più a lungo del necessario per completare un'operazione di ridimensionamento verticale riuscita.
>
> Convalidare sempre le operazioni negli ambienti di test prima di tentare le modifiche all'ambiente di produzione. Per impostazione predefinita, i servizi di sistema cluster di Service Fabric hanno un vincolo di posizionamento solo per il tipo di nodo primario di destinazione.

Con le proprietà dei nodi e i vincoli di posizionamento dichiarati, eseguire i passaggi seguenti in un'istanza di macchina virtuale alla volta. In questo modo i servizi di sistema (e i servizi con stato) possono essere arrestati normalmente nell'istanza della macchina virtuale da rimuovere quando vengono create nuove repliche altrove.

1. Da PowerShell, `Disable-ServiceFabricNode` eseguire `RemoveNode` con l'intenzione di disabilitare il nodo che si desidera rimuovere. Rimuovere il tipo di nodo con il numero più alto. Ad esempio, se si dispone di un cluster a sei nodi, rimuovere l'istanza della macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio d'ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Diminuire il numero di macchine virtuali di uno in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere le fasi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).
5. Una volta che tutte le macchine virtuali sono andati (rappresentato come "Down") il fabric:/System/InfrastructureService/[nome nodo] mostrerà uno stato di errore. Quindi, è possibile aggiornare la risorsa cluster per rimuovere il tipo di nodo. È possibile usare la distribuzione del modello ARM oppure modificare la risorsa cluster tramite Gestione risorse di [Azure.](https://resources.azure.com) Verrà avviato un aggiornamento del cluster che rimuoverà il servizio fabric:/System/InfrastructureService/[tipo di nodo] in stato di errore.
 6. Dopo di che è possibile eliminare facoltativamente il VMScaleSet, sarà ancora vedere i nodi come "Down" dalla visualizzazione di Esplora utilità di servizio però. L'ultimo passo sarebbe quello `Remove-ServiceFabricNodeState` di pulirli con il comando.

## <a name="horizontal-scaling"></a>Scalabilità orizzontale

È possibile eseguire il ridimensionamento orizzontale [manualmente](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) o [a livello di codice.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)

> [!NOTE]
> Se si ridimensiona un tipo di nodo con durata Silver o Gold, il ridimensionamento sarà lento.

### <a name="scaling-out"></a>Aumento del numero di istanze

Scalare orizzontalmente un cluster di Service Fabric aumentando il numero di istanze per un determinato set di scalabilità di macchine virtuali. È possibile aumentare la `AzureClient` scalabilità orizzontale utilizzando e l'ID per il set di scalabilità desiderato per aumentare la capacità.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Per la scalabilità orizzontale manualmente, aggiornare la capacità nella proprietà SKU della risorsa del set di [scalabilità](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) della macchina virtuale desiderata.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Riduzione

La scalabilità orizzontale richiede una maggiore considerazione rispetto alla scalabilità orizzontale. Per esempio:

* I servizi di sistema di Service Fabric vengono eseguiti nel tipo di nodo primario del cluster. Non arrestare né ridurre mai il numero di istanze in questo tipo di nodo per evitare di avere un numero di istanze inferiore a quello garantito dal livello di affidabilità. 
* Per un servizio con stato, è necessario un certo numero di nodi che sono sempre aggiornati per mantenere la disponibilità e mantenere lo stato del servizio. Come minimo, è necessario un numero di nodi uguale al numero di set di repliche di destinazione della partizione o del servizio.

Per ridurre il numero di istanze, seguire questi passaggi:

1. Da PowerShell, `Disable-ServiceFabricNode` eseguire `RemoveNode` con l'intenzione di disabilitare il nodo che si desidera rimuovere. Rimuovere il tipo di nodo con il numero più alto. Ad esempio, se si dispone di un cluster a sei nodi, rimuovere l'istanza della macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio d'ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Diminuire il numero di macchine virtuali di uno in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere i passaggi da 1 a 3 in base alle esigenze fino a quando non si esegue il provisioning della capacità desiderata. Non ridurre il numero di istanze nei tipi di nodo primari a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Per aumentare la scalabilità manuale, aggiornare la capacità nella proprietà SKU della risorsa set di [scalabilità della macchina virtuale](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desiderata.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

È necessario preparare il nodo per la scalabilità dell'arresto a livello di codice. Individuare il nodo da rimuovere (nodo con l'istanza più alta). Ad esempio:

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

Disattivare e rimuovere il `FabricClient` nodo`client` utilizzando la stessa istanza`instanceIdString` (in questo caso) e l'istanza del nodo (in questo caso) utilizzata nel codice precedente:

```csharp
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
> Quando si riduce un cluster, verrà visualizzata l'istanza del nodo/macchina virtuale rimossa visualizzata in uno stato non integro in Esplora utilità di infrastruttura del servizio. Per una spiegazione di questo comportamento, vedere [Comportamenti che è possibile osservare in Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). È possibile:
> * Chiamare il [comando Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) con il nome del nodo appropriato.
> * Distribuire l'applicazione helper di [scalabilità automatica di Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) nel cluster. Questa applicazione garantisce che i nodi ridimensionati vengano cancellati da Service Fabric Explorer.This application ensures that the scale-down nodes are cleared from Service Fabric Explorer.

## <a name="reliability-levels"></a>Livelli di affidabilità

Il livello di [affidabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) è una proprietà della risorsa cluster di Service Fabric.The reliability level is a property of your Service Fabric cluster resource. Non può essere configurato in modo diverso per i singoli tipi di nodo. Questo livello controlla il fattore di replica dei servizi di sistema per il cluster ed è definito tramite un'impostazione a livello di risorsa cluster. 

Il livello di affidabilità determina il numero minimo di nodi che deve avere il tipo di nodo primario. Il livello di affidabilità può avere i valori seguenti:

* Platino: esegue i servizi di sistema con un numero di set di repliche di destinazione di sette e nove nodi di serie.
* Oro: esegue i servizi di sistema con un numero di set di repliche di destinazione di sette e sette nodi di serie.
* Argento: esegue i servizi di sistema con un numero di set di repliche di destinazione di cinque e cinque nodi di serie.
* Bronzo: esegue i servizi di sistema con un numero di set di repliche di destinazione di tre e tre nodi di serie.

Il livello minimo di affidabilità consigliato è Silver.

Il livello di affidabilità è impostato nella sezione delle proprietà della [risorsa Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), come in questo esempio:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Livelli di durabilità

> [!WARNING]
> I tipi di nodo in esecuzione con durabilità Bronze _non ottengono privilegi_. I processi di infrastruttura che influiscono sui carichi di lavoro senza stato non verranno arrestati o ritardati, il che potrebbe influire sui carichi di lavoro. 
>
> Usare il livello di durabilità Bronze solo per i tipi di nodo che eseguono carichi di lavoro senza stato. Per i carichi di lavoro di produzione, eseguire Silver o superiore per garantire la coerenza dello stato. Scegliere il livello di affidabilità appropriato seguendo le indicazioni riportate nella [documentazione sulla pianificazione della capacità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Il livello di durabilità deve essere impostato in due risorse: Uno è il profilo di estensione della risorsa del set di scalabilità della [macchina virtuale:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

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

L'altra risorsa `nodeTypes` è presente nella [risorsa Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster in macchine virtuali o computer che eseguono Windows Server: [Creazione cluster di Service Fabric per Windows Server.](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster in macchine virtuali o computer che eseguono Linux: [Creare un cluster Linux.](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
