---
title: Aumentare le prestazioni di un tipo di nodo di Azure Service Fabric | Microsoft Docs
description: Informazioni su come ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: e6b429189491af71f6215f1c7660be5965741bf7
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044827"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric
In questo articolo viene descritto come aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric aumentando le risorse delle macchine virtuali. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile scalare un tipo di nodo del cluster in verticale (modificare le risorse dei nodi) o aggiornare il sistema operativo delle macchine virtuali del tipo di nodo.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

> [!WARNING]
> Non iniziare a modificare lo SKU di macchina virtuale del tipo di nodo primario, se il cluster non è integro. Se il cluster non è integro, provando a modificare lo SKU di macchina virtuale, questo verrà ulteriormente destabilizzato.
>
> È consigliabile non modificare lo SKU VM di un tipo di nodo o set di scalabilità, a meno che non sia in esecuzione al [livello di durabilità Silver o superiore](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). La modifica delle dimensioni della SKU della macchina virtuale è un'operazione dell'infrastruttura sul posto distruttiva per i dati. Senza la capacità di ritardare o monitorare questa modifica, è possibile che l'operazione causi una perdita di dati per i servizi con stato o provochi altri problemi operativi non previsti, anche per i carichi di lavoro senza stato. Ciò interessa il tipo di nodo primario, che esegue i servizi di sistema con stato di Service Fabric, o qualsiasi tipo di nodo che esegue i carichi di lavoro delle applicazioni con stato.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Aggiornare le dimensioni e il sistema operativo delle macchine virtuali del tipo di nodo primario
Di seguito è illustrato il processo per l'aggiornamento delle dimensioni e del sistema operativo delle macchine virtuali del tipo di nodo primario.  Dopo l'aggiornamento, le macchine virtuali del tipo di nodo primario sono di dimensioni D4_V2 standard ed eseguono Windows Server 2016 Data Center con contenitori.

> [!WARNING]
> Prima di provare a eseguire questa procedura su un cluster di produzione, è consigliabile esaminare i modelli di esempio e verificare il processo su un cluster di test. Il cluster inoltre risulterà non disponibile per un certo tempo. NON è possibile effettuare modifiche in più set di scalabilità di macchine virtuali dichiarate come lo stesso NodeType in parallelo; sarà necessario eseguire operazioni di distribuzione separate per applicare individualmente le modifiche a ogni set di scalabilità di macchine virtuali NodeType.

1. Distribuire il cluster iniziale con due tipi di nodo e due set di scalabilità (un set di scalabilità per ogni tipo di nodo) usando questi file di esempio di [modelli](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) e [parametri](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Entrambi i set di scalabilità sono dimensioni D2_V2 standard ed eseguono Windows Server 2012 R2 Datacenter.  Attendere che il cluster completi l'aggiornamento della baseline.   
2. Distribuire un esempio con stato nel cluster (facoltativo).
3. Dopo aver deciso di aggiornare le macchine virtuali del tipo di nodo primario, aggiungere un nuovo set di scalabilità al tipo di nodo primario usando questi file di esempio di [modelli](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parametri](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json). A questo punto, il tipo di nodo primario ha due set di scalabilità.  Servizi di sistema e applicazioni utente possono eseguire la migrazione tra le macchine virtuali nei due diversi set di scalabilità.  Le macchine virtuali del nuovo set di scalabilità sono di dimensioni D4_V2 standard ed eseguono Windows Server 2016 Datacenter con contenitori.  Con il nuovo set di scalabilità vengono anche aggiunti un nuovo bilanciamento del carico e l'indirizzo IP pubblico.  
    Per trovare il nuovo set di scalabilità nel modello, cercare la risorsa "Microsoft.Compute/virtualMachineScaleSets" tramite il parametro *vmNodeType2Name*.  Il nuovo set di scalabilità verrà aggiunto al tipo di nodo primario usando l'impostazione proprietà -> virtualMachineProfile - > extensionProfile -> estensioni -> proprietà -> impostazioni -> nodeTypeRef.
4. Controllare l'integrità del cluster e verificare che tutti i nodi siano integri.
5. Disabilitare i nodi nel set di scalabilità precedente del tipo di nodo primario allo scopo di rimuovere il nodo. È possibile disabilitare tutti i nodi contemporaneamente e le operazioni verranno inserite nella coda. Attendere che tutti i nodi vengano disabilitati. L'operazione potrebbe richiedere un certo tempo.  Poiché i nodi precedenti nel tipo di nodo sono disabilitati, i servizi di sistema e i nodi di inizializzazione eseguono la migrazione alle macchine virtuali del nuovo set di scalabilità nel tipo di nodo primario.
6. Rimuovere il set di scalabilità precedente dal tipo di nodo primario.
7. Rimuovere il bilanciamento del carico associato al set di scalabilità precedente. Il cluster non è disponibile durante la configurazione del nuovo indirizzo IP pubblico e del bilanciamento del carico per il nuovo set di scalabilità.  
8. Archiviare in una variabile le impostazioni DNS dell'indirizzo IP pubblico associato al set di scalabilità del tipo di nodo primario precedente e rimuovere tale indirizzo IP pubblico.
9. Sostituire le impostazioni DNS dell'indirizzo IP pubblico associato al nuovo set di scalabilità del tipo di nodo primario con le impostazioni DNS dell'indirizzo IP pubblico eliminato.  Ora il cluster è nuovamente raggiungibile.
10. Rimuovere lo stato dei nodi dal cluster.  Se il livello di durabilità del set di scalabilità precedente era Silver o Gold, questo passaggio viene eseguito automaticamente dal sistema.
11. Se è stata distribuita l'applicazione con stato in un passaggio precedente, verificare che l'applicazione funzioni.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [aggiungere un tipo di nodo a un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

