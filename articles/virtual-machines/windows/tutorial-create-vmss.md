---
title: 'Esercitazione: Creare un set di scalabilità di macchine virtuali per Windows in Azure | Microsoft Docs'
description: In questa esercitazione si apprenderà come usare Azure PowerShell per creare e distribuire un'applicazione a disponibilità elevata nelle macchine virtuali Windows usando un set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 49754fd4409b1fbc6b15577d37e216290582ef2b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190810"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>Esercitazione: Creare un set di scalabilità di macchine virtuali e distribuire un'app a disponibilità elevata in Windows con Azure PowerShell
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. È possibile ridimensionare manualmente il numero di VM nel set di scalabilità o definire regole di scalabilità automatica in base all'utilizzo delle risorse, ad esempio la CPU, alla richiesta di memoria o al traffico di rete. In questa esercitazione viene distribuito un set di scalabilità di macchine virtuali in Azure. Si apprenderà come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per definire un sito IIS da ridimensionare
> * Creare un bilanciamento del carico per il set di scalabilità
> * Creare un set di scalabilità di macchine virtuali
> * Aumentare o diminuire il numero di istanze in un set di scalabilità
> * Creare regole di scalabilità automatica

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.


## <a name="scale-set-overview"></a>Informazioni generali sui set di scalabilità
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. Le macchine virtuali di un set di scalabilità vengono distribuite in domini logici di errore e di aggiornamento in uno o più *gruppi di posizionamento*. Si tratta di gruppi di VM configurate in modo simile, analoghi ai [set di disponibilità](tutorial-availability-sets.md).

Le VM vengono create in base alle esigenze in un set di scalabilità. È possibile definire regole di scalabilità automatica per controllare le modalità e i tempi di aggiunta e rimozione delle VM dal set di scalabilità. Queste regole possono essere attivate in base a determinate metriche, ad esempio il carico della CPU, l'utilizzo della memoria o il traffico di rete.

I set di scalabilità supportano fino a 1.000 VM quando si usa un'immagine della piattaforma Azure. Per i carichi di lavoro con requisiti significativi di installazione o personalizzazione di VM, si consiglia di [creare un'immagine di VM personalizzata](tutorial-custom-images.md). È possibile creare fino a 300 macchine virtuali in un set di scalabilità quando si usa un'immagine personalizzata.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Creare un set di scalabilità di macchine virtuali con il comando [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che usa l'immagine della piattaforma *Windows Server 2016 Datacenter*. Vengono create automaticamente le risorse di rete di Azure per la rete virtuale, l'indirizzo IP pubblico e il bilanciamento del carico. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
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
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per vedere il set di scalabilità in azione, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP per *myPublicIP* creato come parte del set di scalabilità:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzata l'app Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico:

![Esecuzione del sito IIS](./media/tutorial-create-vmss/running-iis-site.png)

Per verificare il funzionamento del set di scalabilità, è possibile imporre l'aggiornamento del Web browser per visualizzare la distribuzione del traffico da parte del bilanciamento del carico tra tutte le VM che eseguono l'app.


## <a name="management-tasks"></a>Attività di gestione
Nel ciclo di vita del set di scalabilità, potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. Azure PowerShell offre un modo rapido per eseguire queste operazioni. Di seguito vengono illustrate alcune attività comuni.

### <a name="view-vms-in-a-scale-set"></a>Visualizzare le macchine virtuali in un set di scalabilità
Per visualizzare un elenco delle istanze di macchina virtuale in un set di scalabilità, usare [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) come illustrato di seguito:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

L'output di esempio seguente mostra due istanze di VM nel set di scalabilità:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). L'esempio seguente consente di visualizzare informazioni sull'istanza di macchina virtuale *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentare o diminuire le istanze delle macchine virtuali
Per visualizzare il numero di istanze attualmente presenti in un set di scalabilità, usare il comando [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) ed eseguire una query su *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

È possibile quindi aumentare o ridurre manualmente il numero di macchine virtuali nel set di scalabilità con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L'esempio seguente imposta il numero di macchine virtuali del set di scalabilità su *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

Sono necessari alcuni minuti per aggiornare il numero specificato di istanze del set di scalabilità.


### <a name="configure-autoscale-rules"></a>Configurare le regole di scalabilità automatica
Anziché scalare manualmente il numero di istanze del set di scalabilità, si definiscono regole di scalabilità automatica. Queste regole monitorano le istanze nel set di scalabilità e rispondono di conseguenza in base alle metriche e alle soglie definite. L'esempio seguente aumenta il numero di istanze di uno quando il carico della CPU medio è maggiore del 60% per un periodo di 5 minuti. Se il carico della CPU medio scende poi al di sotto del 30% per un periodo di 5 minuti, le istanze vengono ridotte di una istanza:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
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
$myRuleScaleDown = New-AzureRmAutoscaleRule `
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
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
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
