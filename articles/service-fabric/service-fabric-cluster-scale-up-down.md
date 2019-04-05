---
title: Aumentare o ridurre le istanze del cluster di Service Fabric | Documentazione Microsoft
description: Aumentare o ridurre un cluster di Service Fabric per rispondere alla domanda impostando le regole di scalabilità automatica per ogni set di scalabilità macchine virtuali/tipo nodo. Aggiungere o rimuovere nodi in un cluster di Service Fabric
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: 400e4653800d445506d4854e70034a707dcc4629
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049182"
---
# <a name="scale-a-cluster-in-or-out"></a>Aumentare o ridurre un cluster

> [!WARNING]
> Leggere questa sezione prima si modifica la scala

Il ridimensionamento di risorse di calcolo per originare il carico di lavoro dell'applicazione richiede una pianificazione intenzionale, che quasi sempre richiede più di un'ora in un ambiente di produzione e richiede la comprensione del carico di lavoro e del contesto di business. Se non hai mai eseguito questa attività in precedenza, è consigliabile iniziare leggendo e comprendendo le [considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md), prima di continuare con il resto di questo documento. Questo suggerimento intende evitare problemi LiveSite non intenzionali. Inoltre, si consiglia di testare correttamente le operazioni che si decide di eseguire su un ambiente non di produzione. È possibile [segnalare, in qualsiasi momento, problemi di produzione o richiedere supporto a pagamento per Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Per i tecnici assegnati all’esecuzione di queste operazioni che conoscono il contesto di esecuzione appropriato, questo articolo descrive le operazioni di scalabilità, ma è necessario stabilire e comprendere quali operazioni siano appropriate per il caso d'uso in questione; ad esempio le risorse da ridimensionare (CPU, archiviazione, memoria), la direzione di ridimensionamento (orizzontale o verticale) e le operazioni da eseguire (distribuzione modelli delle risorse, portale, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di ridimensionamento automatico o manualmente
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Service Fabric viene configurato come set di scalabilità di macchine virtuali distinto. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Più informazioni, vedere la [tipi di nodo di Service Fabric](service-fabric-cluster-nodetypes.md) documento. Poiché i tipi di nodo di Service Fabric nel cluster sono costituiti da set di scalabilità di macchine virtuali al back-end, è necessario configurare regole di scalabilità automatica per ogni set di scalabilità macchine virtuali/tipo nodo.

> [!NOTE]
> È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per aggiungere le nuove VM che costituiranno il cluster. Non esiste attualmente una funzionalità di convalida del modello quindi, se viene raggiunto uno dei limiti della quota, verrà visualizzato un errore in fase di distribuzione. Anche un tipo di nodo singolo non può superare 100 nodi per VMSS. Potrebbe essere necessario aggiungere VMSS per ottenere le il ridimensionamento di destinazione e la scalabilità automatica non può aggiungere VMSS in automatico. L'aggiunta di VMSS sul posto a un cluster in tempo reale è un'attività complessa che di norma spinge gli utenti a effettuare il provisioning di nuovi cluster con i tipi di nodo appropriati al momento della creazione; [pianifica la capacità del cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) di conseguenza. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Scegliere il tipo di nodo o il set di scalabilità di macchine virtuali da ridimensionare
Per creare un cluster di Service Fabric non è attualmente possibile specificare le regole di ridimensionamento automatico per i set di scalabilità di macchine virtuali tramite il portale. Si userà quindi Azure PowerShell 1.0 o versione successiva per elencare i tipi di nodo e aggiungervi poi le regole di ridimensionamento automatico.

Per ottenere l'elenco dei set di scalabilità di macchine virtuali che costituiscono il cluster, eseguire i cmdlet seguenti:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Impostare le regole di scalabilità automatica per set di scalabilità di macchine virtuali/tipo nodo
Se il cluster include più tipi di nodo, quindi ripetere che questa operazione per ogni scalabilità di macchine virtuali/tipi di nodo set che si desidera scalare (interna o esterna). Considerare il numero di nodi che essere disponibili prima di configurare il ridimensionamento automatico. Il numero minimo di nodi necessari per il tipo di nodo primario è determinato dal livello di affidabilità scelto. Per altre informazioni, vedere la sezione relativa ai [livelli di affidabilità](service-fabric-cluster-capacity.md).

> [!NOTE]
> La riduzione delle istanze del tipo di nodo primario, impostando un valore inferiore al numero minimo, renderà il cluster instabile o lo arresterà. In questo caso, è possibile che si verifichi una perdita di dati per le applicazioni e per i servizi di sistema.
> 
> 

Attualmente la funzionalità di ridimensionamento automatico non è determinata dai carichi che le applicazioni segnalano a Service Fabric. Al momento, il ridimensionamento automatico che si ottiene dipende esclusivamente dai contatori delle prestazioni generati da ognuna delle istanze del set di scalabilità di macchine virtuali.  

Seguire queste istruzioni [per configurare la scalabilità automatica per ogni set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> In una riduzione delle prestazioni scenario, a meno che il tipo di nodo ha un [a livello di durabilità] [ durability] Gold o Silver è necessario chiamare il [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) con il nome del nodo appropriato. Per la durabilità Bronze, non è consigliabile ridurre più di un nodo alla volta.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Aggiungere manualmente le VM a un set di scalabilità macchine virtuali/tipo nodo

Quando si aumenta il numero di istanze, si aggiungono altre istanze di una macchina virtuale al set di scalabilità. Queste istanze diventano i nodi usati da Service Fabric. Service Fabric determina quando vengono aggiunte altre istanze al set di scalabilità e reagisce automaticamente. 

> [!NOTE]
> Aggiunta di macchine virtuali richiede tempo, in modo che non prevedono le aggiunte a un processo istantaneo. Quindi, prevede di aggiungere capacità in anticipo, per consentire più di 10 minuti prima che la capacità delle macchine Virtuali sia disponibile per le istanze o repliche del servizio si posizionano.
> 

### <a name="add-vms-using-a-template"></a>Aggiungere le macchine virtuali usando un modello
Seguire l'esempio o le istruzioni nel [raccolta di modelli di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) per modificare il numero di macchine virtuali in ogni tipo di nodo. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Aggiungere macchine virtuali usando i comandi di PowerShell o CLI
Il codice seguente ottiene un set di scalabilità in base al nome e aumenta di 1 la **capacità** del set di scalabilità.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Rimuovere manualmente le VM da un set di scalabilità macchina virtuale/tipo nodo
Quando si scala in un tipo di nodo, è rimuovere istanze di macchine Virtuali dal set di scalabilità. Se il tipo di nodo è il livello di durabilità Bronze, Service Fabric è a conoscenza che cosa è accaduto e segnala che un nodo è non mancano. Service Fabric segnala quindi uno stato non integro per il cluster. Per evitare tale stato non valido, è necessario rimuovere il nodo dal cluster in modo esplicito e rimuovere lo stato del nodo.

I servizi di sistema di service fabric è stato eseguito nel tipo di nodo primario del cluster. Quando si ridimensiona il tipo di nodo primario, non ridurre il numero di istanze su un valore inferiore rispetto a ciò che il [livello di affidabilità](service-fabric-cluster-capacity.md) merita. 
 
Per un servizio con stato, è necessario un determinato numero di nodi per essere sempre in grado di assicurare la disponibilità e mantenere lo stato del servizio. Come minimo, è necessario un numero di nodi uguale al conteggio dei set di repliche di destinazione della partizione o dei servizi.

### <a name="remove-the-service-fabric-node"></a>Rimuovere il nodo di Service Fabric

I passaggi per la rimozione manuale dello stato del nodo si applicano solo ai tipi di nodo con un *bronzo* livello di durabilità.  Per la *Silver* e *Gold* livello di durabilità, questi passaggi vengono eseguiti automaticamente dalla piattaforma. Per altre informazioni sulla durabilità, vedere [Pianificazione della capacità dei cluster di Service Fabric][durability].

Per garantire la distribuzione uniforme dei nodi del cluster tra i domini di aggiornamento e di errore e consentirne quindi un utilizzo uniforme, è opportuno rimuovere per primo l'ultimo nodo creato. In altre parole, i nodi devono essere rimossi in ordine inverso rispetto a quello in cui sono stati creati. L'ultimo nodo creato è quello che presenta il valore più elevato per la proprietà `virtual machine scale set InstanceId`. Negli esempi di codice seguenti viene restituito l'ultimo nodo creato.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Il cluster di Service Fabric deve determinare che questo nodo verrà rimosso. È necessario eseguire tre passaggi:

1. Disabilitare il nodo in modo che non sia più una replica per i dati.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Arrestare il nodo in modo che il runtime di Service Fabric venga arrestato normalmente e l'app riceva una richiesta di interruzione.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Rimuovere il nodo dal cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Dopo avere applicato questi tre passaggi al nodo, è possibile rimuoverlo dal set di scalabilità. Se si usa un livello di durabilità superiore a [Bronze][durability], questi passaggi vengono eseguiti automaticamente quando viene rimossa l'istanza del set di scalabilità.

Il blocco di codice seguente ottiene l'ultimo nodo creato, disabilita, arresta e rimuove il nodo dal cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Nel **sfctl** codice sotto, il comando seguente viene usato per ottenere il **-nome del nodo** valore l'ultimo nodo creato: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Usare le query **sfctl** seguenti per controllare lo stato di ogni passaggio
>
> **Controllare lo stato di disattivazione**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Controllare lo stato di arresto**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Ridurre il numero di istanze del set di scalabilità

Ora che il nodo di Service Fabric è stato rimosso dal cluster, è possibile ridurre il numero di istanze del set di scalabilità di macchine virtuali. Nell'esempio seguente la capacità del set di scalabilità è ridotta di 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamenti che è possibile osservare in Service Fabric Explorer
Quando si aumentano le istanze di un cluster, Service Fabric Explorer riflette il numero di nodi, ovvero le istanze del set di scalabilità di macchine virtuali, che fanno parte del cluster.  Quando tuttavia si riducono le istanze di un cluster, il nodo o l'istanza della VM rimossa viene ancora visualizzata con uno stato non integro, a meno di chiamare il cmdlet [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) con il nome del nodo appropriato.   

Ecco la spiegazione di questo comportamento.

I nodi elencati in Service Fabric Explorer riflettono le informazioni a disposizione dei servizi di sistema di Service Fabric, in particolare FM, circa il numero di nodi presenti nel cluster attualmente o in precedenza. Quando si riducono le istanze del set di scalabilità di macchine virtuali, la macchina virtuale viene eliminata, ma il servizio di sistema FM continua a ritenere che il nodo, precedentemente mappato alla macchina virtuale eliminata, verrà ripristinato. Service Fabric Explorer continua quindi a visualizzare tale nodo, anche se lo stato di integrità è sconosciuto o di errore.

Per assicurarsi che un nodo venga rimosso quando si rimuove una VM, sono disponibili due opzioni:

1. Scegliere un livello di durabilità Gold o Silver per i tipi di nodo del cluster, che offre l'integrazione dell'infrastruttura. In questo caso i nodi saranno rimossi automaticamente dallo stato dei servizi di sistema (FM) quando si riducono le istanze.
Fare riferimento ai [i dettagli sui livelli di durabilità qui](service-fabric-cluster-capacity.md)

2. Dopo aver ridotto le istanze della VM, chiamare il [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, non è in genere sicuro arrestare tutte le macchine virtuali del cluster senza avere prima eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pianificazione della capacità del cluster, l'aggiornamento di un cluster e il partizionamento dei servizi, vedere gli articoli seguenti:

* [Pianificare la capacità del cluster](service-fabric-cluster-capacity.md)
* [Aggiornamenti dei cluster](service-fabric-cluster-upgrade.md)
* [Partizionare Reliable services per la massima scalabilità](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
