---
title: Modificare un set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come modificare e aggiornare un set di scalabilità di macchine virtuali di Azure con le API REST, Azure PowerShell e l'interfaccia della riga di comando di Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: ce031b5c0dba96ab1a51532ad771eebeafb5d599
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413261"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificare un set di scalabilità di macchine virtuali
Per tutto il ciclo di vita delle applicazioni, potrebbe essere necessario modificare o aggiornare il set di scalabilità di macchine virtuali. Questi aggiornamenti possono includere come aggiornare la configurazione del set di scalabilità o modificare la configurazione dell'applicazione. Questo articolo descrive come modificare un set di scalabilità esistente con le API REST, Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="fundamental-concepts"></a>Concetti fondamentali

### <a name="the-scale-set-model"></a>Il modello del set di scalabilità
Un set di scalabilità ha un "modello" che acquisisce lo stato *desiderato* del set di scalabilità nel suo insieme. Per eseguire query sul modello per un set di scalabilità è possibile usare 

- API REST con [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) come segue:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell con [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Interfaccia della riga di comando di Azure con [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- È anche possibile usare [resources.azure.com](https://resources.azure.com) o gli [SDK di Azure](https://azure.microsoft.com/downloads/) specifici del linguaggio.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. L'esempio seguente illustra l'output di esempio condensato dall'interfaccia della riga di comando di Azure:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Queste proprietà si applicano al set di scalabilità nel suo insieme.


### <a name="the-scale-set-instance-view"></a>Visualizzazione dell'istanza del set di scalabilità
Un set di scalabilità ha anche una "visualizzazione dell'istanza" che acquisisce lo stato di *runtime* corrente del set di scalabilità nel suo insieme. Per eseguire query sulla visualizzazione dell'istanza per un set di scalabilità è possibile usare:

- API REST con [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) come segue:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell con [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Interfaccia della riga di comando di Azure con [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- È anche possibile usare [resources.azure.com](https://resources.azure.com) o gli [SDK di Azure](https://azure.microsoft.com/downloads/) specifici del linguaggio

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. L'esempio seguente illustra l'output di esempio condensato dall'interfaccia della riga di comando di Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Queste proprietà forniscono un riepilogo dello stato di runtime corrente delle macchine virtuali nel set di scalabilità, ad esempio lo stato delle estensioni applicate al set di scalabilità stesso.


### <a name="the-scale-set-vm-model-view"></a>Visualizzazione modello di macchina virtuale del set di scalabilità
Come un set di scalabilità ha una visualizzazione modello, così anche ogni istanza di macchina virtuale nel set di scalabilità ha una propria visualizzazione modello. Per eseguire query sulla visualizzazione modello per un'istanza di macchina virtuale specifica in un set di scalabilità è possibile usare:

- API REST con [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) come segue:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell con [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Interfaccia della riga di comando di Azure con [az vmss show](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- È anche possibile usare [resources.azure.com](https://resources.azure.com) o gli [SDK di Azure](https://azure.microsoft.com/downloads/).

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. L'esempio seguente illustra l'output di esempio condensato dall'interfaccia della riga di comando di Azure:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Queste proprietà descrivono la configurazione di un'istanza della macchina virtuale all'interno di un set di scalabilità e non quella del set di scalabilità nel suo insieme. Ad esempio, il modello del set di scalabilità ha la proprietà `overprovision`, a differenza del modello di un'istanza di macchina virtuale all'interno di un set di scalabilità. La proprietà overprovision è infatti valida per il set di scalabilità nel suo insieme, non per le singole istanze di macchine virtuali nel set di scalabilità. Per altre informazioni su questa proprietà, vedere [Considerazioni sulla progettazione per i set di scalabilità](virtual-machine-scale-sets-design-overview.md#overprovisioning).


### <a name="the-scale-set-vm-instance-view"></a>Visualizzazione dell'istanza di macchina virtuale del set di scalabilità
Come un set di scalabilità ha una visualizzazione istanza, così anche ogni istanza di macchina virtuale nel set di scalabilità ha una propria visualizzazione istanza. Per eseguire query sulla visualizzazione istanza per un'istanza di macchina virtuale specifica all'interno di un set di scalabilità, è possibile usare:

- API REST con [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) come segue:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell con [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Interfaccia della riga di comando di Azure con [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- È anche possibile usare [resources.azure.com](https://resources.azure.com) o gli [SDK di Azure](https://azure.microsoft.com/downloads/)

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. L'esempio seguente illustra l'output di esempio condensato dall'interfaccia della riga di comando di Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Queste proprietà descrivono lo stato di runtime corrente di un'istanza della macchina virtuale all'interno di un set di scalabilità, che include le eventuali estensioni applicate al set di scalabilità.


## <a name="how-to-update-global-scale-set-properties"></a>Come aggiornare le proprietà globali del set di scalabilità
Per aggiornare una proprietà globale del set di scalabilità, occorre eseguire l'aggiornamento nel modello del set di scalabilità. È possibile eseguire questo aggiornamento tramite:

- API REST con [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) come segue:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- È possibile distribuire un modello di Resource Manager con le proprietà dell'API REST per aggiornare le proprietà globali del set di scalabilità.

- Azure PowerShell con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Interfaccia della riga di comando di Azure con [az vmss update](/cli/azure/vmss#az_vmss_update):
    - Per modificare una proprietà:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Per aggiungere un oggetto a una proprietà di elenco in un set di scalabilità: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Per rimuovere un oggetto da una proprietà di elenco in un set di scalabilità: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se in precedenza si è distribuito il set di scalabilità con il comando `az vmss create`, è possibile eseguire di nuovo il comando `az vmss create` per aggiornare il set di scalabilità. Verificare che tutte le proprietà nel comando `az vmss create` siano rimaste invariate, a eccezione di quelle che si vuole modificare.

- È anche possibile usare [resources.azure.com](https://resources.azure.com) o gli [SDK di Azure](https://azure.microsoft.com/downloads/).

Dopo l'aggiornamento del modello, la nuova configurazione viene applicata a tutte le nuove macchine virtuali create nel set di scalabilità. Tuttavia, i modelli delle macchine virtuali esistenti nel set di scalabilità devono ancora essere aggiornati con il più recente modello del set di scalabilità generale. Il modello di ogni macchina virtuale contiene una proprietà booleana chiamata `latestModelApplied` che indica se la macchina virtuale è aggiornata o meno con il più recente modello del set di scalabilità generale (`true` significa che la macchina virtuale è aggiornata con il modello più recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Come aggiornare le macchine virtuali con il più recente modello del set di scalabilità
I set di scalabilità hanno un "criterio di aggiornamento" che determina il modo in cui le macchine virtuali vengono aggiornate con l'ultima versione del modello del set di scalabilità. Le tre modalità del criterio di aggiornamento sono:

- **Automatico**: in questa modalità il set di scalabilità non offre alcuna garanzia sull'ordine in cui le macchine virtuali vengono arrestate. Potrebbe arrestarle tutte contemporaneamente. 
- **In sequenza**: in questa modalità il set di scalabilità implementa l'aggiornamento in batch con una pausa facoltativa fra i singoli batch.
- **Manuale**: in questa modalità, quando si aggiorna il modello del set di scalabilità, le macchine virtuali esistenti rimangono invariate.
 
Per aggiornare le macchine virtuali esistenti, è necessario eseguire un "aggiornamento manuale" di ogni singola macchina virtuale. È possibile eseguire l'aggiornamento manuale con:

- API REST con [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) come segue:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell con [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Interfaccia della riga di comando di Azure con [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- È anche possibile usare gli [SDK di Azure](https://azure.microsoft.com/downloads/) specifici del linguaggio.

>[!NOTE]
> I cluster di Service Fabric possono usare solo la modalità *automatica*, ma l'aggiornamento viene gestito in modo diverso. Per altre informazioni, vedere [Aggiornamenti delle applicazioni di Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Esiste un tipo di modifica alle proprietà globali del set di scalabilità che non segue il criterio di aggiornamento. Le modifiche al profilo del sistema operativo del set di scalabilità (ad esempio, il nome utente amministratore e la password) possono essere apportate solo nella versione dell'API *2017-12-01* o successiva. Queste modifiche si applicano solo alle macchine virtuali create dopo la modifica nel modello del set di scalabilità. Per aggiornare le macchine virtuali esistenti, è necessario ricreare l'immagine di ogni singola macchina virtuale. È possibile farlo tramite:

- API REST con [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) come segue:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell con [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Interfaccia della riga di comando di Azure con [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- È anche possibile usare gli [SDK di Azure](https://azure.microsoft.com/downloads/) specifici del linguaggio.


## <a name="properties-with-restrictions-on-modification"></a>Proprietà con restrizioni di modifica

### <a name="create-time-properties"></a>Proprietà della fase di creazione
Alcune proprietà possono essere impostate solo quando si crea il set di scalabilità. Queste proprietà includono:

- Zone di disponibilità
- Editore del riferimento all'immagine
- Offerta di riferimento all'immagine
- Tipo di account di archiviazione su disco del sistema operativo gestito

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Proprietà che possono essere modificate solo in base al valore corrente
Alcune proprietà possono essere modificate, con eccezioni che dipendono dal valore corrente. Queste proprietà includono:

- **singlePlacementGroup**: se singlePlacementGroup ha valore true, può essere impostata su false. Se invece singlePlacementGroup ha valore false, **non può** essere impostata su true.
- **subnet**: la subnet di un set di scalabilità può essere modificata purché la subnet originale e la nuova subnet facciano parte della stessa rete virtuale.

### <a name="properties-that-require-deallocation-to-change"></a>Proprietà che richiedono la deallocazione per poter essere modificate
Alcune proprietà possono essere modificate su determinati valori solo dopo la deallocazione delle macchine virtuali del set di scalabilità. Queste proprietà includono:

- **Nome SKU**: se lo SKU della nuova macchina virtuale non è supportato nell'hardware in cui è attualmente in esecuzione il set di scalabilità, è necessario deallocare le macchine virtuali nel set di scalabilità prima di modificare il nome SKU. Per altre informazioni, vedere [Come ridimensionare una VM di Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aggiornamenti specifici delle macchine virtuali
Alcune modifiche possono essere applicate solo a macchine virtuali specifiche anziché alle proprietà globali del set di scalabilità. Attualmente l'unico aggiornamento specifico della macchina virtuale supportato è il collegamento/scollegamento di dischi di dati a/da macchine virtuali nel set di scalabilità. Questa funzionalità è in anteprima. Per altre informazioni, vedere la [documentazione della versione di anteprima](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenari

### <a name="application-updates"></a>Aggiornamenti dell'applicazione
Se un'applicazione viene distribuita a un set di scalabilità tramite estensioni, un aggiornamento alla configurazione delle estensioni causa l'aggiornamento dell'applicazione in conformità con i criteri di aggiornamento. Se ad esempio si deve eseguire una nuova versione di uno script in un'estensione dello script personalizzata, si potrebbe aggiornare la proprietà *fileUris* in modo che punti al nuovo script. In alcuni casi si potrebbe avere l'esigenza di forzare un aggiornamento anche se la configurazione delle estensioni è rimasta invariata, ad esempio nel caso in cui si sia aggiornato lo script senza modifiche all'URI. In questi casi è possibile modificare la proprietà *forceUpdateTag* per poter forzare un aggiornamento. La piattaforma di Azure non interpreta questa proprietà. Se si modifica il valore, l'esecuzione dell'estensione non subirà conseguenze. Una modifica forza semplicemente la ripetizione dell'esecuzione dell'estensione. Per altre informazioni sulla proprietà *forceUpdateTag*, vedere la [documentazione dell'API REST relativa alle estensioni](/rest/api/compute/virtualmachineextensions/createorupdate). Notare che la proprietà *forceUpdateTag* non può essere usata con tutte le estensioni e non solo con l'estensione di script personalizzata.

Uno scenario frequente è la distribuzione delle applicazioni tramite un'immagine personalizzata. Questo scenario è illustrato nella sezione seguente.

### <a name="os-updates"></a>Aggiornamenti del sistema operativo
Se si usano immagini della piattaforma di Azure, è possibile aggiornare l'immagine modificando la proprietà *imageReference*. Per altre informazioni, vedere la [documentazione relativa all'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Con le immagini della piattaforma si specifica solitamente "latest" come versione del riferimento all'immagine. Quando si crea, si aumenta il numero di istanze e si ricrea l'immagine, le macchine virtuali vengono create con l'ultima versione disponibile. **Non** significa però che l'immagine del sistema operativo viene aggiornata automaticamente nel tempo man mano che vengono rilasciate nuove versioni dell'immagine. È attualmente in anteprima una funzionalità separata che fornisce aggiornamenti automatici del sistema operativo. Per altre informazioni, consultare la [documentazione relativa agli aggiornamenti automatici del sistema operativo](virtual-machine-scale-sets-automatic-upgrade.md).

Se si usano immagini personalizzate, è possibile aggiornare l'immagine aggiornando l'ID della proprietà *imageReference*. Per altre informazioni, vedere la [documentazione relativa all'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Esempi

### <a name="update-the-os-image-for-your-scale-set"></a>Aggiornare l'immagine del sistema operativo per il set di scalabilità
Si può avere un set di scalabilità che esegue una versione precedente di Ubuntu LTS 16.04. Si vuole eseguire l'aggiornamento a una versione più recente di Ubuntu LTS 16.04, ad esempio la versione *16.04.201801090*. La proprietà della versione del riferimento all'immagine non fa parte di un elenco, quindi è possibile modificare direttamente queste proprietà con uno dei comandi seguenti:

- Azure PowerShell con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) come segue:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Interfaccia della riga di comando di Azure con [az vmss update](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

In alternativa è possibile modificare l'immagine usata dal set di scalabilità. Ad esempio è possibile aggiornare o modificare un'immagine personalizzata usata dal set di scalabilità. È possibile modificare l'immagine usata dal set di scalabilità aggiornando la proprietà dell'ID di riferimento all'immagine. La proprietà dell'ID del riferimento all'immagine non fa parte di un elenco, quindi è possibile modificare direttamente questa proprietà con uno dei comandi seguenti:

- Azure PowerShell con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) come segue:

    ```powershell
    Update-AzureRmVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Interfaccia della riga di comando di Azure con [az vmss update](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aggiornare il bilanciamento del carico del set di scalabilità
Supponiamo di avere un set di scalabilità con un servizio Azure Load Balancer e di voler sostituire tale servizio con un gateway applicazione di Azure. Le proprietà del servizio di bilanciamento del carico e del gateway applicazione di un set di scalabilità fanno parte di un elenco, quindi è possibile usare i comandi per rimuovere o aggiungere elementi di elenco invece di modificare le proprietà direttamente:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Interfaccia della riga di comando di Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Questi comandi presuppongono che nel set di scalabilità siano presenti una sola configurazione IP e un solo servizio di bilanciamento del carico. Se ce ne sono di più, potrebbe essere necessario usare un indice di elenco diverso da *0*.


## <a name="next-steps"></a>Passaggi successivi
È anche possibile eseguire attività di gestione comuni sui set di scalabilità con l'[interfaccia della riga di comando di Azure](virtual-machine-scale-sets-manage-cli.md) o [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
