---
title: 'Esercitazione: Creare un set di scalabilità di macchine virtuali per Windows in Azure'
description: In questa esercitazione si apprenderà come usare Azure PowerShell per creare e distribuire un'applicazione a disponibilità elevata nelle macchine virtuali Windows usando un set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6fcf0c14d58e70659ee5e21b03245b705294b445
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067899"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Esercitazione: Creare un set di scalabilità di macchine virtuali e distribuire un'app a disponibilità elevata in Windows con Azure PowerShell
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile adattare manualmente il numero di macchine virtuali nel set di scalabilità, nonché definire regole di scalabilità automatica in base all'utilizzo della CPU, alla richiesta di memoria o al traffico di rete. In questa esercitazione viene distribuito un set di scalabilità di macchine virtuali in Azure e viene spiegato come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per definire un sito IIS da ridimensionare
> * Creare un bilanciamento del carico per il set di scalabilità
> * Creare un set di scalabilità di macchine virtuali
> * Aumentare o diminuire il numero di istanze in un set di scalabilità
> * Creare regole di scalabilità automatica

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="scale-set-overview"></a>Informazioni generali sui set di scalabilità
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. Le macchine virtuali di un set di scalabilità vengono distribuite in domini logici di errore e di aggiornamento in uno o più *gruppi di posizionamento*. I gruppi di posizionamento sono gruppi di macchine virtuali configurate in modo simile, analoghi ai [set di disponibilità](tutorial-availability-sets.md).

Le VM vengono create in base alle esigenze in un set di scalabilità. È possibile definire regole di scalabilità automatica per controllare le modalità e i tempi di aggiunta e rimozione delle VM dal set di scalabilità. Queste regole possono essere attivate in base a determinate metriche, ad esempio il carico della CPU, l'utilizzo della memoria o il traffico di rete.

I set di scalabilità supportano fino a 1.000 VM quando si usa un'immagine della piattaforma Azure. Per i carichi di lavoro con requisiti significativi di installazione o personalizzazione di VM, si consiglia di [creare un'immagine di VM personalizzata](tutorial-custom-images.md). È possibile creare fino a 300 macchine virtuali in un set di scalabilità quando si usa un'immagine personalizzata.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Creare un set di scalabilità di macchine virtuali con [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss). L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che usa l'immagine della piattaforma *Windows Server 2016 Datacenter*. Vengono create automaticamente le risorse di rete di Azure per la rete virtuale, l'indirizzo IP pubblico e il bilanciamento del carico. Quando richiesto, è possibile impostare le credenziali amministrative per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="deploy-sample-application"></a>Distribuire l'applicazione di esempio
Per testare il set di scalabilità, installare un'applicazione Web di base. Usare l'estensione script personalizzati di Azure per scaricare ed eseguire uno script che installa IIS nelle istanze di macchina virtuale. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](extensions-customscript.md).

Usare l'estensione script personalizzati per installare un server Web IIS di base. Applicare l'estensione di script personalizzati che installa IIS nel modo seguente:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>Consentire il traffico verso l'applicazione

Per consentire l'accesso all'applicazione Web di base, creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) e [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup). Per altre informazioni, vedere [Rete per i set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per verificare il funzionamento del set di scalabilità, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). L'esempio seguente mostra l'indirizzo IP per *myPublicIP* creato come parte del set di scalabilità:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

Immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzata l'app Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico:

![Esecuzione del sito IIS](./media/tutorial-create-vmss/running-iis-site.png)

Per verificare il funzionamento del set di scalabilità, è possibile imporre l'aggiornamento del Web browser per visualizzare la distribuzione del traffico da parte del bilanciamento del carico tra tutte le VM che eseguono l'app.


## <a name="management-tasks"></a>Attività di gestione
Nel ciclo di vita del set di scalabilità, potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. Azure PowerShell offre un modo rapido per eseguire queste operazioni. Di seguito vengono illustrate alcune attività comuni.

### <a name="view-vms-in-a-scale-set"></a>Visualizzare le macchine virtuali in un set di scalabilità
Per visualizzare un elenco delle istanze di VM in un set di scalabilità, usare [Get-AzVmssVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm) come illustrato di seguito:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

L'output di esempio seguente mostra due istanze di VM nel set di scalabilità:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `-InstanceId` a [Get-AzVmssVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm). L'esempio seguente consente di visualizzare informazioni sull'istanza di macchina virtuale *1*:

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentare o diminuire le istanze delle macchine virtuali
Per visualizzare il numero di istanze attualmente presente in un set di scalabilità, usare [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) ed eseguire una query su *sku.capacity*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

È quindi possibile aumentare o ridurre manualmente il numero di macchine virtuali nel set di scalabilità con [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). L'esempio seguente imposta il numero di macchine virtuali del set di scalabilità su *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Sono necessari alcuni minuti per aggiornare il numero specificato di istanze del set di scalabilità.


### <a name="configure-autoscale-rules"></a>Configurare le regole di scalabilità automatica
Anziché scalare manualmente il numero di istanze del set di scalabilità, si definiscono regole di scalabilità automatica. Queste regole monitorano le istanze nel set di scalabilità e rispondono di conseguenza in base alle metriche e alle soglie definite. L'esempio seguente aumenta il numero di istanze di uno quando il carico della CPU medio è maggiore del 60% per un periodo di 5 minuti. Se il carico della CPU medio scende poi al di sotto del 30% per un periodo di 5 minuti, le istanze vengono ridotte di una istanza:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

Per altre informazioni di progettazione sull'uso della scalabilità automatica, vedere [Procedure consigliate per la scalabilità automatica](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato creato un set di scalabilità di macchine virtuali. Si è appreso come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per definire un sito IIS da ridimensionare
> * Creare un bilanciamento del carico per il set di scalabilità
> * Creare un set di scalabilità di macchine virtuali
> * Aumentare o diminuire il numero di istanze in un set di scalabilità
> * Creare regole di scalabilità automatica

Passare all'esercitazione successiva per maggiori informazioni sui concetti di bilanciamento del carico per le macchine virtuali.

> [!div class="nextstepaction"]
> [Bilanciare il carico delle macchine virtuali](tutorial-load-balancer.md)
