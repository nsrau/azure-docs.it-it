---
title: Ridimensionare un cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come ridimensionare rapidamente un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.openlocfilehash: d203580f676d55acbad4936160982a40592af1d0
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="scale-a-service-fabric-cluster"></a>Ridimensionare un cluster di Service Fabric

Questa esercitazione, la seconda di una serie, illustra come aumentare e ridurre il numero di istanze del cluster esistente. Al termine, si riuscirà a ridimensionare il cluster e a pulire le risorse rimaste.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Leggere il conteggio dei nodi del cluster
> * Aggiungere nodi del cluster (aumentare il numero di istanze)
> * Rimuovere nodi del cluster (ridurre il numero di istanze)

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) o [la versione 2.0 dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Creare [un cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) o un [cluster di Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protetto in Azure
- Se si distribuisce un cluster di Windows, configurare un ambiente di sviluppo di Windows. Installare [Visual Studio 2017](http://www.visualstudio.com) e installare i carichi di lavoro per lo **sviluppo di Azure**, lo **sviluppo ASP.NET e Web** e lo **sviluppo multipiattaforma .NET Core**.  Configurare un [ambiente di sviluppo .NET](service-fabric-get-started.md).
- Se si distribuisce un cluster Linux, configurare un ambiente di sviluppo Java in [Linux](service-fabric-get-started-linux.md) o [MacOS](service-fabric-get-started-mac.md).  Installare l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al proprio account di Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per completare questa parte dell'esercitazione, è necessario connettersi sia al cluster di Service Fabric che al set di scalabilità di macchine virtuali (che ospita il cluster). Il set di scalabilità di macchine virtuali è la risorsa di Azure che ospita Service Fabric in Azure.

Quando ci si connette a un cluster, è possibile eseguirne una query per trovare informazioni. È possibile usare il cluster per sapere quali nodi sono riconosciuti dal cluster. Nel codice seguente per la connessione al cluster viene usato lo stesso certificato creato nella prima parte di questa serie. Impostare le variabili `$endpoint` e `$thumbprint` sui valori usati.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Dopo avere stabilito la connessione, è possibile usare un comando per ottenere lo stato di ogni nodo nel cluster. Per PowerShell usare il comando `Get-ServiceFabricClusterHealth` e per **sfctl** usare il comando ``.

## <a name="scale-out"></a>Scalabilità orizzontale

Quando si aumenta il numero di istanze, si aggiungono altre istanze di una macchina virtuale al set di scalabilità. Queste istanze diventano i nodi usati da Service Fabric. Service Fabric determina quando vengono aggiunte altre istanze al set di scalabilità e reagisce automaticamente. Il codice seguente ottiene un set di scalabilità in base al nome e aumenta di 1 la **capacità** del set di scalabilità.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Ridurre il numero di istanze

La riduzione del numero di istanze è identica all'aumento, ma si usa un valore di **capacity** minore. Quando si riduce il numero di istanze del set di scalabilità, si rimuovono le istanze di una macchina virtuale dal set di scalabilità. Service Fabric in genere non rileva ciò che è accaduto e ritiene che un nodo sia mancante. Service Fabric segnala quindi uno stato non integro per il cluster. Per evitare tale stato non valido, è necessario informare Service Fabric che si prevede che il nodo venga rimosso.

### <a name="remove-the-service-fabric-node"></a>Rimuovere il nodo di Service Fabric

> [!NOTE]
> Questa parte si applica solo al livello di durabilità *Bronzo*. Per altre informazioni sulla durabilità, vedere [Pianificazione della capacità dei cluster di Service Fabric][durability].

Quando si riduce il numero di istanze di un set di scalabilità di macchine virtuali, il set di scalabilità di macchine virtuali (nella maggior parte dei casi) rimuove l'istanza della macchina virtuale creata per ultima. È quindi necessario trovare il nodo di Service Fabric corrispondente creato per ultimo. È possibile trovare quest'ultimo nodo controllando il valore della proprietà `NodeInstanceId` più elevato nei nodi di Service Fabric. Gli esempi di codice seguenti ordinano per nodo l'istanza e restituiscono i dettagli sull'istanza con il valore di ID più elevato. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```azurecli
`sfctl node list --query "sort_by(items[*], &instanceId)[-1]"`
```

Il cluster di Service Fabric deve determinare che questo nodo verrà rimosso. È necessario eseguire tre passaggi:

1. Disabilitare il nodo in modo che non sia più una replica per i dati.  
PowerShell: `Disable-ServiceFabricNode`  
sfcli: `sfctl node disable`

2. Arrestare il nodo in modo che il runtime di Service Fabric venga arrestato normalmente e l'app riceva una richiesta di interruzione.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfcli: `sfctl node transition --node-transition-type Stop`

2. Rimuovere il nodo dal cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfcli: `sfctl node remove-state`

Dopo avere applicato questi tre passaggi al nodo, è possibile rimuoverlo dal set di scalabilità. Se si usa un livello di durabilità superiore a [Bronze][durability], questi passaggi vengono eseguiti automaticamente quando viene rimossa l'istanza del set di scalabilità.

Il blocco di codice seguente ottiene l'ultimo nodo creato, disabilita, arresta e rimuove il nodo dal cluster.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

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

Nel codice **sfctl** sotto, il comando seguente viene usato per ottenere i valori di **node-name** e **node-instance-id** dell'ultimo nodo creato: `sfctl node list --query "sort_by(items[*], &instanceId)[-1].[instanceId,name]"`

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
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].nodeDeactivationInfo"`
>
> **Controllare lo stato di arresto**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>Ridurre il numero di istanze del set di scalabilità

Ora che il nodo di Service Fabric è stato rimosso dal cluster, è possibile ridurre il numero di istanze del set di scalabilità di macchine virtuali. Nell'esempio seguente la capacità del set di scalabilità è ridotta di 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Leggere il conteggio dei nodi del cluster
> * Aggiungere nodi del cluster (aumentare il numero di istanze)
> * Rimuovere nodi del cluster (ridurre il numero di istanze)


Procedere con l'esercitazione seguente per scoprire come distribuire un'applicazione e usare la gestione API.
> [!div class="nextstepaction"]
> [Distribuire Gestione API](service-fabric-tutorial-deploy-api-management.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
