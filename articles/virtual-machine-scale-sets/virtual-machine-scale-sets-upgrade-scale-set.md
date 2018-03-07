---
title: "Modificare un set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Modificare un set di scalabilità di macchine virtuali di Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: 836d56012afa9e5d5bdec35d85c37dd4b0b788ce
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificare un set di scalabilità di macchine virtuali
Questo articolo descrive come modificare un set di scalabilità esistente. Illustra come cambiare la configurazione del set di scalabilità, come cambiare la configurazione delle applicazioni in esecuzione nel set di scalabilità, come gestire la disponibilità e altro ancora.

## <a name="fundamental-concepts"></a>Concetti fondamentali

### <a name="the-scale-set-model"></a>Il modello del set di scalabilità

Un set di scalabilità ha un "modello" che acquisisce lo stato *desiderato* del set di scalabilità nel suo insieme. Per eseguire query sul modello per un set di scalabilità è possibile usare:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

Interfaccia della riga di comando: `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per eseguire query sul modello per un set di scalabilità.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. Questo è un esempio di output dell'interfaccia della riga di comando:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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
  .
  .
  .
}
```

Come si può notare, queste proprietà si applicano al set di scalabilità nel suo insieme.



### <a name="the-scale-set-instance-view"></a>Visualizzazione dell'istanza del set di scalabilità

Un set di scalabilità ha anche una "visualizzazione dell'istanza" che acquisisce lo stato di *runtime* corrente del set di scalabilità nel suo insieme. Per eseguire query sulla visualizzazione dell'istanza per un set di scalabilità è possibile usare:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

Interfaccia della riga di comando: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per eseguire query sulla visualizzazione dell'istanza per un set di scalabilità.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. Questo è un esempio di output dell'interfaccia della riga di comando:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
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
  .
  .
  .
}
```

Come si può notare, queste proprietà forniscono un riepilogo dello stato di runtime corrente delle macchine virtuali nel set di scalabilità, incluso lo stato delle estensioni applicate al set di scalabilità stesso (omesso per brevità).



### <a name="the-scale-set-vm-model-view"></a>Visualizzazione modello di macchina virtuale del set di scalabilità

Così come un set di scalabilità ha una visualizzazione modello, anche ogni macchina virtuale nel set di scalabilità ha la propria visualizzazione modello. Per eseguire query sulla visualizzazione modello per un set di scalabilità è possibile usare:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Interfaccia della riga di comando: `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per eseguire query sul modello per una macchina virtuale in un set di scalabilità.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. Questo è un esempio di output dell'interfaccia della riga di comando:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Come si può notare, queste proprietà descrivono la configurazione della macchina virtuale stessa, non quella del set di scalabilità nel suo insieme. Ad esempio, il modello del set di scalabilità ha la proprietà `overprovision`, mentre il modello di una macchina virtuale nel set di scalabilità non ce l'ha. La proprietà overprovision è infatti valida per il set di scalabilità nel suo insieme, non per le singole macchine virtuali nel set di scalabilità (per altre informazioni su questa proprietà, vedere [questa documentazione](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="the-scale-set-vm-instance-view"></a>Visualizzazione dell'istanza di macchina virtuale del set di scalabilità

Così come un set di scalabilità ha una visualizzazione dell'istanza, anche ogni macchina virtuale nel set di scalabilità ha la propria visualizzazione dell'istanza. Per eseguire query sulla visualizzazione dell'istanza per un set di scalabilità è possibile usare:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Interfaccia della riga di comando: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per eseguire query sulla visualizzazione dell'istanza di una macchina virtuale in un set di scalabilità.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. Questo è un esempio di output dell'interfaccia della riga di comando:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
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
  .
  .
  .
}
```

Come si può notare, queste proprietà descrivono lo stato di runtime corrente della macchina virtuale stessa, incluse le eventuali estensioni applicate al set di scalabilità (omesse per brevità).




## <a name="how-to-update-global-scale-set-properties"></a>Come aggiornare le proprietà globali del set di scalabilità

Per aggiornare una proprietà globale del set di scalabilità, occorre eseguire l'aggiornamento nel modello del set di scalabilità. È possibile eseguire questo aggiornamento tramite:

API REST: `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate))

Modelli di Resource Manager: in alternativa è possibile distribuire un modello di Resource Manager usando le proprietà dell'API REST per aggiornare le proprietà globali del set di scalabilità.

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss))

Interfaccia della riga di comando. Per modificare una proprietà: `az vmss update --set {propertyPath}={value}`. Per aggiungere un oggetto a una proprietà di elenco in un set di scalabilità: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. Per rimuovere un oggetto da una proprietà di elenco in un set di scalabilità: `az vmss update --remove {propertyPath} {indexToRemove}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)) In alternativa, se in precedenza si è distribuito il set di scalabilità tramite il comando `az vmss create`, è possibile eseguire di nuovo il comando `az vmss create` per aggiornare il set di scalabilità. A tale scopo occorre verificare che tutte le proprietà nel comando `az vmss create` siano rimaste invariate, ad eccezione di quelle che si vuole modificare.



Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per aggiornare il modello del set di scalabilità.

Dopo l'aggiornamento del modello, la nuova configurazione viene applicata a tutte le nuove macchine virtuali create nel set di scalabilità. Tuttavia, i modelli delle macchine virtuali esistenti nel set di scalabilità devono ancora essere aggiornati con il più recente modello del set di scalabilità generale. Il modello di ogni macchina virtuale contiene una proprietà booleana chiamata `latestModelApplied` che indica se la macchina virtuale è aggiornata o meno con il più recente modello del set di scalabilità generale (`true` significa che la macchina virtuale è aggiornata con il modello più recente).




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Come aggiornare le macchine virtuali con il più recente modello del set di scalabilità

I set di scalabilità hanno un "criterio di aggiornamento" che determina il modo in cui le macchine virtuali vengono aggiornate con l'ultima versione del modello del set di scalabilità. Le tre modalità del criterio di aggiornamento sono:

- Automatico: in questa modalità il set di scalabilità non offre alcuna garanzia sull'ordine in cui le macchine virtuali vengono arrestate. Potrebbe arrestarle tutte contemporaneamente. 
- In sequenza: in questa modalità il set di scalabilità implementa l'aggiornamento in batch con una pausa facoltativa fra i singoli batch.
- Manuale: in questa modalità, quando si aggiorna il modello del set di scalabilità, le macchine virtuali esistenti rimangono invariate. Per aggiornare le macchine virtuali esistenti, è necessario eseguire un "aggiornamento manuale" di ogni singola macchina virtuale. È possibile eseguire l'aggiornamento manuale tramite:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances))

PowerShell: `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance))

Interfaccia della riga di comando: `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances))

È anche possibile usare gli [Azure SDK](https://azure.microsoft.com/downloads/) per eseguire un aggiornamento manuale su una macchina virtuale in un set di scalabilità.

>[!NOTE]
> I cluster di Service Fabric possono usare solo la modalità automatica, ma l'aggiornamento viene gestito in modo diverso. Per altre informazioni sugli aggiornamenti di Service Fabric, vedere la [documentazione di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

>[!NOTE]
> Esiste un tipo di modifica alle proprietà globali del set di scalabilità che non segue il criterio di aggiornamento. Si tratta delle modifiche al profilo del sistema operativo del set di scalabilità, ad esempio il nome utente e la password dell'amministratore. Queste modifiche si applicano solo alle macchine virtuali create dopo la modifica nel modello del set di scalabilità. Per aggiornare le macchine virtuali esistenti, è necessario ricreare l'immagine di ogni singola macchina virtuale. È possibile farlo tramite:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

Interfaccia della riga di comando: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (per altre informazioni, vedere la [documentazione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage))

È anche possibile usare gli [Azure SDK](https://azure.microsoft.com/downloads/) per ricreare l'immagine di una macchina virtuale in un set di scalabilità.




## <a name="properties-with-restrictions-on-modification"></a>Proprietà con restrizioni di modifica

### <a name="create-time-properties"></a>Proprietà della fase di creazione

Alcune proprietà possono essere impostate solo durante la creazione iniziale del set di scalabilità. Queste proprietà includono:

- zone
- editore del riferimento all'immagine
- offerta di riferimento all'immagine

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Proprietà che possono essere modificate solo in base al valore corrente

Alcune proprietà possono essere modificate, con eccezioni che dipendono dal valore corrente. Queste proprietà includono:

- singlePlacementGroup: se singlePlacementGroup ha valore true, può essere impostata su false. Se invece singlePlacementGroup ha valore false, **non può** essere impostata su true.
- subnet: la subnet di un set di scalabilità può essere modificata purché la subnet originale e la nuova subnet facciano parte della stessa rete virtuale.

### <a name="properties-that-require-deallocation-to-change"></a>Proprietà che richiedono la deallocazione per poter essere modificate

Alcune proprietà possono essere modificate su determinati valori solo dopo la deallocazione delle macchine virtuali del set di scalabilità. Queste proprietà includono:

- Nome SKU: se lo SKU della nuova macchina virtuale non è supportato nell'hardware in cui è attualmente in esecuzione il set di scalabilità, è necessario deallocare le macchine virtuali nel set di scalabilità prima di modificare il nome SKU. Per altre informazioni sul ridimensionamento delle macchine virtuali, vedere [questo post di blog di Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Aggiornamenti specifici delle macchine virtuali

Alcune modifiche possono essere applicate solo a macchine virtuali specifiche anziché alle proprietà globali del set di scalabilità. Attualmente l'unico aggiornamento specifico della macchina virtuale supportato è il collegamento/scollegamento di dischi di dati a/da macchine virtuali nel set di scalabilità. Questa funzionalità è in anteprima. Per altre informazioni, vedere la [documentazione della versione di anteprima](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios-application-updates-os-updates-etc"></a>Scenari: aggiornamenti dell'applicazione, aggiornamenti del sistema operativo e altro

### <a name="application-updates"></a>Aggiornamenti dell'applicazione

Se un'applicazione viene distribuita a un set di scalabilità tramite estensioni, l'aggiornamento della configurazione delle estensioni causa l'aggiornamento dell'applicazione in conformità con i criteri di aggiornamento. Ad esempio, se si deve eseguire una nuova versione di uno script in un'estensione dello script personalizzata, si potrebbe aggiornare la proprietà fileUris in modo che punti al nuovo script. In alcuni casi, tuttavia, si potrebbe avere l'esigenza di forzare un aggiornamento anche se la configurazione delle estensioni è rimasta invariata, ad esempio nel caso in cui si sia aggiornato lo script senza cambiarne l'URI. In questi casi è possibile modificare la proprietà forceUpdateTag in modo da forzare un aggiornamento. La piattaforma Azure non interpreta questa proprietà, quindi la modifica del relativo valore non ha alcun effetto sulla modalità di esecuzione dell'estensione. La sua modifica forza semplicemente la ripetizione dell'esecuzione dell'estensione. Per altre informazioni sulla proprietà forceUpdateTag, vedere la [documentazione dell'API REST relativa alle estensioni](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Uno scenario frequente è la distribuzione delle applicazioni tramite un'immagine personalizzata. Questo scenario è illustrato di seguito nella sezione "Aggiornamenti del sistema operativo".

### <a name="os-updates"></a>Aggiornamenti del sistema operativo

Se si usano immagini della piattaforma, è possibile aggiornare l'immagine modificando la proprietà imageReference (per altre informazioni, vedere la [documentazione relativa all'API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Con le immagini della piattaforma si specifica solitamente "latest" come versione del riferimento all'immagine. Questo significa che quando si crea, si scala orizzontalmente e si ricrea l'immagine del set di scalabilità, le macchine virtuali vengono create con l'ultima versione disponibile. **Non** significa però che l'immagine del sistema operativo verrà aggiornata automaticamente nel tempo man mano che vengono rilasciate nuove versioni dell'immagine. Questa è una funzionalità separata, attualmente in anteprima. Per altre informazioni, consultare la [documentazione relativa agli aggiornamenti automatici del sistema operativo](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Se si usano immagini personalizzate, è possibile aggiornare l'immagine aggiornando l'ID della proprietà imageReference (per altre informazioni, vedere la [documentazione relativa all'API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Esempi

### <a name="updating-the-os-image-for-your-scale-set"></a>Aggiornamento dell'immagine del sistema operativo per il set di scalabilità

Supponiamo di avere un set di scalabilità che esegue una versione precedente di Ubuntu LTS 16.04 e di volerlo aggiornare a una versione più recente di Ubuntu LTS 16.04, ad esempio la versione 16.04.201801090. La proprietà della versione del riferimento all'immagine non fa parte di un elenco, quindi è possibile modificare direttamente queste proprietà con questi comandi:

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

Interfaccia della riga di comando: `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>Aggiornamento del bilanciamento del carico del set di scalabilità

Supponiamo di avere un set di scalabilità con un servizio Azure Load Balancer e di voler sostituire tale servizio con un gateway applicazione di Azure. Le proprietà del servizio di bilanciamento del carico e del gateway applicazione di un set di scalabilità fanno parte di un elenco, quindi è possibile usare i comandi per la rimozione e l'aggiunta di elementi di elenco invece di modificare le proprietà direttamente:

Powershell:
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Interfaccia della riga di comando:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> Questi comandi presuppongono che nel set di scalabilità siano presenti una sola configurazione IP e un solo servizio di bilanciamento del carico. Se ce ne sono di più, potrebbe essere necessario usare un indice di elenco diverso da 0.