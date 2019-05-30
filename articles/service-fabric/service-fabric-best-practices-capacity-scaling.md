---
title: Procedure consigliate per la pianificazione della capacità e il ridimensionamento di Azure Service Fabric | Microsoft Docs
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
ms.openlocfilehash: 9bddb6552b11dd506ee3e2c1c416c15da11048b7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258746"
---
# <a name="capacity-planning-and-scaling"></a>Pianificazione della capacità e ridimensionamento

Prima di creare cluster Azure Service Fabric o ridimensionare le risorse di calcolo che ospitano un cluster, è importante definire un piano per la capacità. Per altre informazioni sulla pianificazione della capacità, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Per un'ulteriore procedura consigliata materiale sussidiario per la scalabilità del cluster di vedere [considerazioni sulla scalabilità di Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

Oltre a prendere in considerazione le caratteristiche di cluster e tipo di nodo, è consigliabile pianificare per operazioni di ridimensionamento a richiedere più tempo rispetto a un'ora per il completamento di un ambiente di produzione indipendentemente dal numero di macchine virtuali che si sta aggiungendo.

## <a name="auto-scaling"></a>Ridimensionamento automatico
Operazioni di ridimensionamento devono essere eseguita tramite la distribuzione dei modelli di risorse di Azure, perché è la procedura consigliata per trattare [configurazioni di risorse come codice]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)e l'utilizzo di set di scalabilità di macchine virtuali la scalabilità automatica comporterà il modello di Resource Manager con controllo delle versioni in modo non corretto la definizione di scalabilità di macchine virtuali impostato conteggi delle istanze; aumentare il rischio di distribuzioni future causa imprevista delle operazioni di scalabilità e, in generale è consigliabile usare la scalabilità automatica se:

* La distribuzione di modelli di Resource Manager con un'opportuna capacità dichiarata non è adatta al proprio caso.
  * Oltre a scalabilità manuale, è possibile configurare un [integrazione continua e la Pipeline di recapito in servizi di DevOps di Azure con i progetti di distribuzione gruppo di risorse di Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), che normalmente viene generato da un'App per la logica che si basa su le metriche delle prestazioni di macchine virtuali sottoposte a query da [API REST di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); in modo efficace la scalabilità automatica in base qualsiasi metriche desiderate, durante l'ottimizzazione per l'aggiunta di valore di Azure Resource Manager.
* Si deve ridimensionare orizzontalmente un solo nodo di set di scalabilità di macchine virtuali alla volta.
  * Per scalabilità orizzontale a 3 o più nodi contemporaneamente, dovrebbe [scalabilità orizzontale di un cluster di Service Fabric mediante l'aggiunta di un set di scalabilità di macchine virtuali](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), è consigliabile eseguire la scalabilità in e out macchina virtuale i set di scalabilità orizzontale a 1 nodo alla volta.
* Si dispone di un livello di affidabilità Silver o superiore per il cluster di Service Fabric e di un livello di durabilità Silver o superiore su qualsiasi set di scalabilità per cui si configurano le regole di ridimensionamento automatico.
  * Capacità di regole di scalabilità automatica (minima) deve essere uguale o maggiore di 5 istanze di macchina virtuale e deve essere uguale o maggiore di almeno il livello di affidabilità per il tipo di nodo primario.

> [!NOTE]
> Informazioni sullo stato di service fabric di Azure Service Fabric: / System/InfastructureService/< NODE_TYPE_NAME >, viene eseguito su ogni tipo di nodo con Silver o fornire una maggiore durabilità, che è l'unico servizio di sistema che è supportato per l'esecuzione in Azure in uno qualsiasi dei tipi di nodo cluster .

## <a name="vertical-scaling-considerations"></a>Considerazioni sul ridimensionamento verticale

[Il ridimensionamento verticale](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) un tipo di nodo in Azure Service Fabric richiede un numero di passaggi e considerazioni. Ad esempio:

* Prima del ridimensionamento, il cluster deve essere integro, In caso contrario, si verrà destabilizzare solo ulteriormente il cluster.
* **Livello di durabilità superiore o a un livello Silver** è obbligatorio per tutti i tipi di nodo di Cluster di Service Fabric che ospitano servizi con stato.

> [!NOTE]
> Il tipo di nodo primario che ospita i servizi di sistema Stateful Service Fabric deve essere maggiore o a un livello di durabilità Silver. Dopo che si è abilitata la durabilità Silver, le operazioni del cluster, come gli aggiornamenti, l'aggiunta o la rimozione di nodi e così via, saranno più lente perché il sistema è ottimizzato più per la sicurezza dei dati che non per la velocità delle operazioni.

Un set di scalabilità di macchine virtuali di scalabilità verticale è un'operazione distruttiva. In alternativa, per completare in modo sicuro un'operazione di ridimensionamento verticale, ridimensionare il cluster orizzontalmente aggiungendo un nuovo set di scalabilità con lo SKU desiderato ed eseguendo la migrazione dei servizi a tale SKU. La modifica di una risorsa di set di scalabilità di macchine virtuali SKU è un'operazione distruttiva in quanto immagini nuovamente gli host che rimuove tutti gli stati locale persistenti.

Le [proprietà dei nodi e i vincoli di posizionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) di Service Fabric vengono usati dal cluster per decidere dove ospitare i servizi delle applicazioni. Quando la scalabilità verticale del tipo di nodo primario, dichiarare i valori di proprietà identici per `"nodeTypeRef"`, che si trova nella scalabilità di macchine virtuali imposta l'estensione Service Fabric. Il frammento seguente del modello di Resource Manager mostra le proprietà che si dovranno dichiarare, con lo stesso valore per i nuovi set di scalabilità di cui è stato effettuato il provisioning, ed è supportato solo come configurazione con stato temporanea per il cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Evitare di lasciare il cluster in esecuzione con più set di scalabilità che usano lo stesso valore della proprietà `nodeTypeRef` più a lungo del tempo necessario per completare un'operazione di ridimensionamento verticale.
> Verificare sempre le operazioni in ambienti di test prima di provare ad apportare le modifiche nell'ambiente di produzione. Per impostazione predefinita, servizi di sistema di Service Fabric Cluster hanno un vincolo di posizionamento al solo tipo di nodo primario di destinazione.

Con le proprietà dei nodi e i vincoli di posizionamento dichiarati, eseguire i passaggi seguenti in un'istanza di macchina virtuale alla volta. Ciò consente l'arresto normale dei servizi di sistema (e dei servizi con stato) nell'istanza di macchina virtuale che viene rimossa, mentre vengono create nuove repliche altrove.

1. In PowerShell eseguire `Disable-ServiceFabricNode` con il parametro Intent impostato su RemoveNode per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Se ad esempio si ha un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre di uno il numero di macchine virtuali in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere le fasi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

> [!NOTE]
> È uno scenario supportato per i casi in cui eseguire le operazioni di ridimensionamento verticale: È possibile migrare i Cluster di Service Fabric e dell'applicazione da dischi non gestiti a Managed Disks senza tempi di inattività dell'applicazione. Effettuando il provisioning di una nuova macchina virtuale di set di scalabilità con dischi gestiti ed esecuzione di un aggiornamento dell'applicazione con i vincoli di posizionamento che hanno come destinazione il provisioning della capacità; cluster di Service Fabric è possibile pianificare il carico di lavoro nella capacità del nodo cluster con provisioning che viene implementata dal dominio di aggiornamento senza tempi di inattività dell'applicazione. [Azure SKU Basic di servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) gli endpoint del pool back-end possono essere una macchina virtuale in un unico set di disponibilità o set di scalabilità di macchine virtuali. Ciò significa che non è possibile utilizzare un servizio di bilanciamento del carico dello SKU Basic, se l'applicazione di Service Fabric sistemi si sposta tra i set di scalabilità, senza causare inaccessibilità temporaneo dell'infrastruttura del servizio di gestione endpoint cluster, anche se il cluster e la relativa applicazione sono ancora in esecuzione; comunemente utente eseguire il provisioning di un bilanciamento del carico dello SKU Standard, quando si esegue uno scambio di indirizzo IP (VIP) virtuale tra le risorse di bilanciamento del carico dello SKU Basic e bilanciamento del carico dello SKU Standard, per attenuare eventuali future circa 30 secondi di inaccessibilità necessari per lo scambio VIP.

## <a name="horizontal-scaling"></a>Scalabilità orizzontale

In Service Fabric il ridimensionamento orizzontale può essere eseguito [in modalità manuale](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) o [a livello di codice](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se si ridimensiona un tipo di nodo con durabilità Silver o Gold, l'operazione verrà eseguita più lentamente.

### <a name="scaling-out"></a>Aumento del numero di istanze

Scalare orizzontalmente un cluster di Service Fabric, aumentando il numero di istanze per un set di scalabilità di macchina virtuale specifica. L'operazione può essere eseguita a livello di codice usando AzureClient e l'ID del set di scalabilità desiderato per aumentare la capacità.

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

* I servizi di sistema di Service Fabric vengono eseguiti nel tipo di nodo primario del cluster. Non arrestare né ridurre mai il numero di istanze in questo tipo di nodo per evitare di avere un numero di istanze inferiore a quello garantito dal livello di affidabilità. 
* Per un servizio con stato, è necessario che un determinato numero di nodi sia sempre attivo per assicurare la disponibilità e mantenere lo stato del servizio. Come minimo, è necessario un numero di nodi uguale al totale dei set di repliche di destinazione della partizione o del servizio.

Per ridurre il numero di istanze, seguire questi passaggi:

1. In PowerShell eseguire `Disable-ServiceFabricNode` con il parametro Intent impostato su RemoveNode per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Se ad esempio si ha un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre di uno il numero di macchine virtuali in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere le fasi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Per ridimensionare manualmente, aggiornare la capacità nella proprietà SKU dell'oggetto desiderato [set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) risorsa.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Ripetere i passaggi da 1 a 3 fino a completare il provisioning della capacità desiderata. Non ridurre il numero di istanze nei tipi di nodo primari a un valore inferiore a quello garantito dal livello di affidabilità. Per informazioni dettagliate sui livelli di affidabilità e sul numero di istanze necessarie, vedere [Considerazioni sulla pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Per la riduzione del numero di istanze a livello di codice, è necessario preparare il nodo per l'arresto. A tale scopo, cercare il nodo da rimuovere, ovvero il nodo dell'istanza con il numero più alto, e disattivarlo. Ad esempio: 

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

Dopo avere identificato il nodo da rimuovere, disattivarlo e rimuoverlo usando la stessa istanza di `FabricClient` (`client` in questo caso) e lo stesso nome dell'istanza di nodo (`instanceIdString` in questo caso) usati nel codice precedente:

```c#
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

> [!NOTE]
> Quando si ridimensiona un cluster verrà visualizzato l'istanza di rimozione nodo/macchina virtuale visualizzato in uno stato non integro in Service Fabric Explorer. Per una spiegazione di questo comportamento, vedere [comportamenti è possibile osservare in Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). È possibile:
> * Chiamare [cmd Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) con il nome del nodo appropriato.
> * Distribuire [applicazione di service fabric di scalabilità automatica helper](https://github.com/Azure/service-fabric-autoscale-helper/) nel cluster che assicura la scalabilità verso il basso i nodi vengono cancellati da Service Fabric Explorer.

## <a name="reliability-levels"></a>Livelli di affidabilità

Il [a livello di affidabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) è una proprietà della risorsa Cluster di Service Fabric e non può essere configurata in modo diverso per singoli tipi di nodo. Questo livello controlla il fattore di replica dei servizi di sistema per il cluster ed è definito tramite un'impostazione a livello di risorsa cluster. Il livello di affidabilità determina il numero minimo di nodi che deve avere il tipo di nodo primario. Il livello di affidabilità può avere i valori seguenti:

* Platinum: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a sette e con nove nodi di inizializzazione.
* Gold: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a sette e con sette nodi di inizializzazione.
* Server: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a cinque e con cinque nodi di inizializzazione.
* Bronze: esegue i servizi di sistema con un totale di set di repliche di destinazione pari a tre e con tre nodi di inizializzazione.

Il livello minimo di affidabilità consigliato è Silver.

Il livello di affidabilità è impostato nella sezione delle proprietà della [risorsa Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), come in questo esempio:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Livelli di durabilità

> [!WARNING]
> I tipi di nodo in esecuzione con durabilità Bronze _non ottengono privilegi_. Ciò significa che i processi di infrastruttura che influiscono sui carichi di lavoro senza stato non verranno interrotti o posticipati, il che potrebbe influire sui carichi di lavoro. Usare il livello di durabilità Bronze solo per i tipi di nodo che eseguono carichi di lavoro senza stato. Per i carichi di lavoro di produzione, usare il livello Silver o superiore per garantire la coerenza dello stato. Scegliere il livello di affidabilità appropriato seguendo le indicazioni riportate nella [documentazione sulla pianificazione della capacità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Il livello di durabilità deve essere impostato in due risorse: Il profilo di estensione del [risorsa del set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

E in `nodeTypes` nella [risorsa Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
