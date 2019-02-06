---
title: Procedure consigliate per la pianificazione della capacità e il ridimensionamento di Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per la pianificazione e il ridimensionamento di cluster e applicazioni Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d6f2ca53829642009adbc50061966c5a7e924f7e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240404"
---
# <a name="capacity-planning-and-scaling"></a>Pianificazione della capacità e ridimensionamento

Prima di creare cluster Azure Service Fabric o ridimensionare le risorse di calcolo che ospitano un cluster, è importante definire un piano per la capacità. Per altre informazioni sulla pianificazione della capacità, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Oltre a considerare le caratteristiche del cluster e del tipo di nodo, prevedere più di un'ora per le operazioni di ridimensionamento in un ambiente di produzione, indipendentemente dal numero di macchine virtuali che si aggiungono.

## <a name="auto-scaling"></a>Ridimensionamento automatico
Le operazioni di ridimensionamento devono essere eseguite tramite la distribuzione di modelli di risorse di Azure, perché è consigliabile trattare le [configurazioni delle risorse come codice]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) e il ridimensionamento automatico dei set di scalabilità di macchine virtuali ha come effetto una definizione poco precisa del numero di istanze dei set nella specifica versione del modello di Resource Manager, aumentando così il rischio che le distribuzioni future provochino operazioni di ridimensionamento impreviste. In linea generale è consigliabile usare il ridimensionamento automatico nei casi seguenti:

* La distribuzione di modelli di Resource Manager con un'opportuna capacità dichiarata non è adatta al proprio caso.
  * Oltre al ridimensionamento manuale, è possibile configurare una [pipeline di integrazione continua/recapito continuo in Azure DevOps Services con progetti di distribuzione di tipo Gruppo di risorse di Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), che viene normalmente attivata da un'app per la logica basata sulle metriche delle prestazioni di macchine virtuali ottenute dall'[API REST di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). In questo modo, il ridimensionamento automatico viene eseguito in modo efficace in base alle metriche desiderate e l'ambiente viene ottimizzato per il valore aggiunto di Azure Resource Manager.
* Si deve ridimensionare orizzontalmente un solo nodo di set di scalabilità di macchine virtuali alla volta.
  * Per aggiungere contemporaneamente tre o più nodi, è necessario [aggiungere un set di scalabilità di macchine virtuali al cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) ed è più sicuro aumentare o ridurre il numero di set di scalabilità di macchine virtuali per un nodo alla volta.
* Si dispone di un livello di affidabilità Silver o superiore per il cluster di Service Fabric e di un livello di durabilità Silver o superiore su qualsiasi set di scalabilità per cui si configurano le regole di ridimensionamento automatico.
  * La capacità minima delle regole di ridimensionamento automatico deve essere uguale o superiore a cinque istanze di macchina virtuale e al livello minimo di affidabilità per il tipo di nodo primario.

> [!NOTE]
> Il servizio con stato di Azure Service Fabric, fabric:/System/InfastructureService/<NOME_TIPO_DI_NODO>, viene eseguito su ogni tipo di nodo con livello di durabilità Silver o superiore, ed è quindi l'unico servizio di sistema che può essere eseguito in Azure su qualsiasi tipo di nodo di cluster. 

## <a name="vertical-scaling-considerations"></a>Considerazioni sul ridimensionamento verticale

Per [ridimensionare verticalmente](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out#upgrade-the-size-and-operating-system-of-the-primary-node-type-vms) un tipo di nodo in Azure Service Fabric, ovvero modificarne la capacità, è necessario eseguire una serie di passaggi e tenere presenti alcune considerazioni. Ad esempio: 
* Prima del ridimensionamento, il cluster deve essere integro, altrimenti si rischia solo di destabilizzarlo ulteriormente.
* Per tutti i tipi di nodo del cluster di Service Fabric che ospitano servizi con stato è necessario il **livello di durabilità Silver o superiore**.

> [!NOTE]
> Il tipo di nodo primario che ospita i servizi di sistema con stato di Service Fabric deve avere un livello di durabilità Silver o superiore. Dopo che si è abilitata la durabilità Silver, le operazioni del cluster, come gli aggiornamenti, l'aggiunta o la rimozione di nodi e così via, saranno più lente perché il sistema è ottimizzato più per la sicurezza dei dati che non per la velocità delle operazioni.

Il ridimensionamento verticale di un set di scalabilità di macchine virtuali è un'operazione distruttiva. In alternativa, per completare in modo sicuro un'operazione di ridimensionamento verticale, ridimensionare il cluster orizzontalmente aggiungendo un nuovo set di scalabilità con lo SKU desiderato ed eseguendo la migrazione dei servizi a tale SKU. La modifica dello SKU delle risorse di un set di scalabilità di macchine virtuali è un'operazione distruttiva perché ridefinisce le immagini degli host, rimuovendo tutti i dati di stato salvati in locale.

Le [proprietà dei nodi e i vincoli di posizionamento](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) di Service Fabric vengono usati dal cluster per decidere dove ospitare i servizi delle applicazioni. Quando si ridimensiona verticalmente il tipo di nodo primario, dichiarare valori di proprietà identici per `"nodeTypeRef"`, che si trova nell'estensione di Service Fabric per i set di scalabilità di macchine virtuali. Il frammento seguente del modello di Resource Manager mostra le proprietà che si dovranno dichiarare, con lo stesso valore per i nuovi set di scalabilità di cui è stato effettuato il provisioning, ed è supportato solo come configurazione con stato temporanea per il cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Evitare di lasciare il cluster in esecuzione con più set di scalabilità che usano lo stesso valore della proprietà `nodeTypeRef` più a lungo del tempo necessario per completare un'operazione di ridimensionamento verticale.
> Verificare sempre le operazioni in ambienti di test prima di provare ad apportare le modifiche nell'ambiente di produzione. Per impostazione predefinita, i servizi di sistema del cluster di Service Fabric hanno un vincolo di posizionamento per cui possono essere applicati solo al tipo di nodo primario.

Con le proprietà dei nodi e i vincoli di posizionamento dichiarati, eseguire i passaggi seguenti in un'istanza di macchina virtuale alla volta. Ciò consente l'arresto normale dei servizi di sistema (e dei servizi con stato) nell'istanza di macchina virtuale che viene rimossa, mentre vengono create nuove repliche altrove.
1. In PowerShell eseguire `Disable-ServiceFabricNode` con il parametro Intent impostato su RemoveNode per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Se ad esempio si ha un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre di uno il numero di macchine virtuali in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere i passaggi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

## <a name="horizontal-scaling"></a>Ridimensionamento orizzontale

In Service Fabric il ridimensionamento orizzontale può essere eseguito [in modalità manuale](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) o [a livello di codice](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Se si ridimensiona un tipo di nodo con durabilità Silver o Gold, l'operazione verrà eseguita più lentamente.

### <a name="scaling-out"></a>Aumento del numero di istanze

Per un cluster di Service Fabric, l'aumento del numero di istanze viene applicato a un determinato set di scalabilità di macchine virtuali. L'operazione può essere eseguita a livello di codice usando AzureClient e l'ID del set di scalabilità desiderato per aumentare la capacità.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Per eseguire l'operazione manualmente, aggiornare il valore della capacità nella proprietà SKU della risorsa [Set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desiderata.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Riduzione del numero di istanze

La riduzione del numero di istanze richiede qualche considerazione in più rispetto all'aumento. Ad esempio: 
* I servizi di sistema di Service Fabric vengono eseguiti nel tipo di nodo primario del cluster. Non arrestare né ridurre mai il numero di istanze in questo tipo di nodo per evitare di avere un numero di istanze inferiore a quello garantito dal livello di affidabilità. 
* Per un servizio con stato, è necessario che un determinato numero di nodi sia sempre attivo per assicurare la disponibilità e mantenere lo stato del servizio. Come minimo, è necessario un numero di nodi uguale al totale dei set di repliche di destinazione della partizione o del servizio.

Per ridurre il numero di istanze, seguire questi passaggi:

1. In PowerShell eseguire `Disable-ServiceFabricNode` con il parametro Intent impostato su RemoveNode per disabilitare il nodo che si intende rimuovere. Rimuovere il tipo di nodo con il numero più alto. Se ad esempio si ha un cluster a sei nodi, rimuovere l'istanza di macchina virtuale "MyNodeType_5".
2. Eseguire `Get-ServiceFabricNode` per assicurarsi che il nodo sia disabilitato. In caso contrario, attendere la disabilitazione del nodo. L'operazione potrebbe richiedere un paio di ore per ogni nodo. Non continuare finché il nodo non risulta disabilitato.
3. Ridurre di uno il numero di macchine virtuali in quel tipo di nodo. L'istanza di macchina virtuale con il numero più alto verrà rimossa.
4. Ripetere i passaggi da 1 a 3 come necessario, ma non ridurre il numero di istanze nel nodo primario a un valore inferiore a quello garantito dal livello di affidabilità. Per un elenco di istanze consigliate, vedere [Pianificazione della capacità del cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Per eseguire questa operazione manualmente, aggiornare il valore della capacità nella proprietà SKU della risorsa [Set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) desiderata.

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

## <a name="reliability-levels"></a>Livelli di affidabilità

Il [livello di affidabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) è una proprietà della risorsa Cluster di Service Fabric e non può essere configurato in modo diverso per singoli tipi di nodo. Questo livello controlla il fattore di replica dei servizi di sistema per il cluster ed è definito tramite un'impostazione a livello di risorsa cluster. Il livello di affidabilità determina il numero minimo di nodi che deve avere il tipo di nodo primario. Il livello di affidabilità può avere i valori seguenti:
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

Il livello di durabilità deve essere impostato in due risorse: Nel profilo dell'estensione della [risorsa Set di scalabilità di macchine virtuali](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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
