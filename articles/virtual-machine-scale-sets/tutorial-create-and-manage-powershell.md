---
title: Esercitazione - Creare e gestire un set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per creare un set di scalabilità di macchine virtuali, con alcune attività di gestione comuni come l'avvio e l'arresto di un'istanza o la modifica della capacità del set di scalabilità.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 10fc55886e4c91a2d468704d13d3b206f4a9cf51
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980246"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Esercitazione: Creare e gestire un set di scalabilità di macchine virtuali con Azure PowerShell

Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. Nel ciclo di vita del set di scalabilità di una macchina virtuale potrebbe essere necessario eseguire una o più attività di gestione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un set di scalabilità di macchine virtuali e connettersi
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare dimensioni di istanze di VM specifiche
> * Ridimensionare manualmente un set di scalabilità
> * Eseguire attività comuni per la gestione dei set di scalabilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]



## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È necessario creare un gruppo di risorse prima di un set di scalabilità di macchine virtuali. Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *EastUS*. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
In questa esercitazione, il nome del gruppo di risorse viene specificato quando si crea o si modifica un set di scalabilità.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Impostare prima di tutto nome utente e password dell'amministratore delle istanze di macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare ora un set di scalabilità di macchine virtuali con [New-AzVmss](/powershell/module/az.compute/new-azvmss). Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

La creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Visualizzare le istanze di VM in un set di scalabilità
Per visualizzare un elenco delle istanze di VM in un set di scalabilità, usare [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) come illustrato di seguito:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

L'output di esempio seguente mostra due istanze di VM nel set di scalabilità:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `-InstanceId` a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). L'esempio seguente consente di visualizzare informazioni sull'istanza di macchina virtuale *1*:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Visualizzare l'elenco delle informazioni di connessione
Al servizio di bilanciamento del carico che indirizza il traffico alle singole istanze di VM viene assegnato un indirizzo IP pubblico. Per impostazione predefinita, al servizio di bilanciamento del carico di Azure che inoltra il traffico di connessione remota a ogni VM su una determinata porta vengono aggiunte regole NAT (Network Address Translation). Per connettersi alle istanze di VM di un set di scalabilità, si crea una connessione remota a un indirizzo IP pubblico e un numero di porta assegnati.

Per visualizzare l'elenco delle porte NAT per la connessione alle istanze di VM di un set di scalabilità, recuperare per prima cosa l'oggetto servizio di bilanciamento del carico con [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Visualizzare quindi le regole NAT in ingresso con [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

L'output di esempio seguente mostra il nome dell'istanza, l'indirizzo IP pubblico del servizio di bilanciamento del carico e il numero di porta a cui le regole NAT inoltrano il traffico:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Il valore di *Name* della regola è in linea con il nome dell'istanza di VM visualizzato con un precedente comando [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). Per connettersi all'istanza di VM *0*, ad esempio, si usa *myScaleSet3389.0* e si stabilisce la connessione alla porta *50001*. Per connettersi all'istanza di VM *1*, si usa il valore di *myScaleSet3389.1* e si stabilisce la connessione alla porta *50002*. Per usare la comunicazione remota di PowerShell, si stabilisce la connessione alla regola delle istanze di VM appropriata per la porta *TCP* *5985*.

Visualizzare l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Output di esempio:

```powershell
IpAddress
---------
52.168.121.216
```

Creare una connessione remota alla prima istanza di VM. Specificare l'indirizzo IP pubblico e il numero di porta dell'istanza di VM richiesta, in base a quanto visualizzato dai comandi precedenti. Quando richiesto, immettere le credenziali usate durante la creazione del set di scalabilità. Per impostazione predefinita, nei comandi di esempio sono *azureuser* e *P@ssw0rd!*. Se si usa Azure Cloud Shell, eseguire questo passaggio da un prompt di PowerShell in locale o da un client Desktop remoto. L'esempio seguente stabilisce la connessione all'istanza di VM *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Dopo aver effettuato l'accesso all'istanza di VM, è possibile apportare alcune modifiche manuali alla configurazione in base alle esigenze. Per il momento, chiudere la connessione remota.


## <a name="understand-vm-instance-images"></a>Informazioni sulle immagini delle istanze di VM
Azure Marketplace include molte immagini utilizzabili per creare istanze di VM. Per visualizzare un elenco degli editori disponibili, usare il comando [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Per visualizzare un elenco delle immagini di un determinato editore, usare [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). L'elenco di immagini può anche essere filtrato per `-PublisherName` o `–Offer`. Nell'esempio seguente, l'elenco viene filtrato per individuare tutte le immagini con nome dell'editore *MicrosoftWindowsServer* e offerta corrispondente a *WindowsServer*:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

L'output di esempio seguente mostra tutte le immagini Windows Server disponibili:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Quando è stato creato un set di scalabilità all'inizio dell'esercitazione, per le istanze di VM è stata usata un'immagine di VM predefinita *Windows Server 2016 DataCenter*. È possibile specificare un'immagine di VM diversa in base all'output di [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). L'esempio seguente creerà un set di scalabilità con il parametro `-ImageName` per specificare l'immagine di VM *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Dato che la creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti, non è necessario distribuire il set di scalabilità seguente:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Informazioni sulle dimensioni delle istanze di VM
Una dimensione di istanza di VM, o *SKU*, determina la quantità di risorse di calcolo, come CPU, GPU e memoria, disponibili per l'istanza di VM. È necessario dimensionare le istanze di VM di un set di scalabilità in modo appropriato per il carico di lavoro previsto.

### <a name="vm-instance-sizes"></a>Dimensioni delle istanze di VM
La tabella seguente classifica le dimensioni di VM comuni in base ai casi d'uso.

| Type                     | Dimensioni comuni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo/test e soluzioni di dati e applicazioni medio-piccole.  |
| [Ottimizzate per il calcolo](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| [Ottimizzate per la memoria](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](../virtual-machines/windows/sizes-storage.md)      | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video.       |
| [Prestazioni elevate](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Trovare le dimensioni di istanze di VM disponibili
Per visualizzare un elenco delle dimensioni di istanze di VM disponibili in una determinata area, usare il comando [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

L'output è simile all'esempio sintetico seguente, che mostra le risorse assegnate a ogni dimensione di VM:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Quando è stato creato un set di scalabilità all'inizio dell'esercitazione, per le istanze di VM è stato usato lo SKU di VM predefinito *Standard_DS1_v2*. È possibile specificare una dimensione di istanza di VM diversa in base all'output di [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize). L'esempio seguente creerà un set di scalabilità con il parametro `-VmSize` per specificare la dimensione di istanza di VM *Standard_F1*. Dato che la creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti, non è necessario distribuire il set di scalabilità seguente:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scalabilità
Quando è stato creato un set di scalabilità, sono state richieste due istanze di VM. Per aumentare o ridurre il numero di istanze di VM nel set di scalabilità, è possibile modificare la capacità manualmente. Il set di scalabilità crea o rimuove il numero necessario di istanze di VM, quindi configura il servizio di bilanciamento del carico per la distribuzione del traffico.

Creare prima di tutto un oggetto set di scalabilità con [Get-AzVmss](/powershell/module/az.compute/get-azvmss) e quindi specificare un nuovo valore per `sku.capacity`. Per applicare la modifica della capacità, usare [Update-AzVmss](/powershell/module/az.compute/update-azvmss). L'esempio seguente imposta il numero di istanze di VM nel set di scalabilità su *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Sono necessari alcuni minuti per aggiornare la capacità del set di scalabilità. Per visualizzare il numero di istanze attualmente presente nel set di scalabilità, usare [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

L'output di esempio seguente mostra che la capacità del set di scalabilità è ora *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Attività di gestione comuni
È ora possibile creare un set di scalabilità, visualizzare l'elenco delle informazioni di connessione e connettersi alle istanze di VM. È stato illustrato come è possibile usare una diversa immagine del sistema operativo per le istanze di VM, selezionare una diversa dimensione di VM o ridimensionare manualmente il numero di istanze. Nell'ambito della gestione quotidiana, potrebbe essere necessario arrestare, avviare o riavviare le istanze di VM del set di scalabilità.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Arrestare e deallocare le istanze di VM di un set di scalabilità
Per arrestare una o più VM in un set di scalabilità, usare [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Il parametro `-InstanceId` consente di specificare una o più macchine virtuali da arrestare. Se non si specifica un ID istanza, vengono arrestate tutte le macchine virtuali del set di scalabilità. L'esempio seguente arresta l'istanza *1*:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Per impostazione predefinita, le VM arrestate vengono deallocate e quindi non generano costi di calcolo. Se si vuole che la VM resti nello stato di provisioning quando viene arrestata, aggiungere il parametro `-StayProvisioned` al comando precedente. Le VM arrestate che rimangono nello stato di provisioning generano costi di calcolo.

### <a name="start-vm-instances-in-a-scale-set"></a>Avviare le istanze di VM di un set di scalabilità
Per avviare una o più VM in un set di scalabilità, usare [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Il parametro `-InstanceId` consente di specificare una o più macchine virtuali da avviare. Se non si specifica un ID istanza, vengono avviate tutte le macchine virtuali del set di scalabilità. L'esempio seguente avvia l'istanza *1*:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Riavviare le istanze di VM di un set di scalabilità
Per riavviare una o più VM in un set di scalabilità, usare [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Il parametro `-InstanceId` consente di specificare una o più macchine virtuali da riavviare. Se non si specifica un ID istanza, vengono riavviate tutte le macchine virtuali del set di scalabilità. L'esempio seguente riavvia l'istanza *1*:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Pulire le risorse
Quando si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute, come le istanze di VM, la rete virtuale e i dischi. Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire alcune attività di base per la creazione e la gestione dei set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Creare un set di scalabilità di macchine virtuali e connettersi
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare manualmente un set di scalabilità
> * Eseguire attività comuni per la gestione dei set di scalabilità

Passare all'esercitazione successiva per informazioni sui dischi dei set di scalabilità.

> [!div class="nextstepaction"]
> [Usare dischi di dati con set di scalabilità](tutorial-use-disks-powershell.md)
