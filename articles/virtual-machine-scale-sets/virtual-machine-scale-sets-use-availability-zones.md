---
title: "Creare un set di scalabilità di Azure che usa le zone di disponibilità (Anteprima) | Microsoft Docs"
description: "Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano le zone di disponibilità per aumentare la ridondanza in caso di interruzioni"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 397afc28b5f4c4f7f84afde13b6d031d83aaced4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità (Anteprima)
Per proteggere i set di scalabilità di macchine virtuali dagli errori che possono verificarsi a livello di data center, è possibile creare un set di scalabilità tra le zone di disponibilità. Le aree di Azure che supportano le zone di disponibilità includono almeno tre zone distinte, ognuna dotata di risorse di alimentazione, di rete e di raffreddamento indipendenti. Per ulteriori informazioni, vedere [Overview of Availability Zones](../availability-zones/az-overview.md) (Panoramica delle zone di disponibilità in Azure).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Set di scalabilità a zona singola o con ridondanza della zona
Quando si distribuisce un set di scalabilità di macchine virtuali, è possibile scegliere di usare una sola zona di disponibilità in un'area o più zone.

Quando si crea un set di scalabilità in una sola zona, la zona in cui tutte le istanze delle macchine virtuali vengono eseguite è sotto controllo e il set di scalabilità viene gestito e ridimensionato automaticamente solo all'interno di questa zona. La figura seguente illustra come è possibile creare più set di scalabilità a zona singola con un servizio di bilanciamento del carico con ridondanza della zona che distribuisce il traffico:

![Distribuzione di set di scalabilità a zona singola con servizio di bilanciamento del carico con ridondanza della zona](media/virtual-machine-scale-sets-use-availability-zones/zonal-vmss.png)

Un set di scalabilità con ridondanza della zona consente di creare un solo set di scalabilità che include più zone. Quando vengono create le istanze delle macchine virtuali, per impostazione predefinita vengono bilanciate equamente tra le diverse zone. Se si verifica un'interruzione in una di queste zone, un set di scalabilità non aumenta automaticamente le istanze per incrementare la capacità. Una procedura consigliata è quella di configurare regole di scalabilità automatica in base alla CPU o all'utilizzo della memoria. Le regole di scalabilità automatica consentono al set di scalabilità di rispondere a una perdita delle istanze delle macchine virtuali in una zona aumentando il numero di istanze nelle restanti zone operative. La figura seguente illustra un esempio di un set di scalabilità singolo distribuito in più zone:

![Distribuzione di un set di scalabilità con ridondanza della zona e servizio di bilanciamento del carico](media/virtual-machine-scale-sets-use-availability-zones/zone-redundant-vmss.png)

Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'[area di Azure supportata](../availability-zones/az-overview.md#regions-that-support-availability-zones). È anche necessario eseguire la [registrazione per l'anteprima delle zone di disponibilità](http://aka.ms/azenroll). È possibile creare un set di scalabilità che usa le zone di disponibilità in uno dei modi seguenti:

- [Azure portal](#use-the-azure-portal)
- [Interfaccia della riga di comando di Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modelli di Gestione risorse di Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Usare il portale di Azure
La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'[articolo introduttivo](virtual-machine-scale-sets-create-portal.md). Assicurarsi di aver eseguito la [registrazione per l'anteprima delle zone di disponibilità](http://aka.ms/azenroll). Quando si seleziona un'area di Azure supportata, è possibile creare un set di scalabilità in una delle zone disponibili, come illustrato nell'esempio seguente:

![Creare un set di scalabilità in una sola zona di disponibilità](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Il set di scalabilità e le risorse di supporto, ad esempio Azure Load Balancer e l'indirizzo IP pubblico, vengono creati nella sola zona specificata.


## <a name="use-the-azure-cli-20"></a>Usare l'interfaccia della riga di comando di Azure 2.0
La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'[articolo introduttivo](virtual-machine-scale-sets-create-cli.md). Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata ed eseguire la [registrazione per l'anteprima delle zone di disponibilità](http://aka.ms/azenroll).

Aggiungere il parametro `--zones` al comando [az vmss create](/cli/azure/vmss#az_vmss_create) e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*. L'esempio seguente crea un set di scalabilità a zona singola denominato *myScaleSet* nella zona *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
Per un esempio completo di un set di scalabilità a zona singola e delle relative risorse di rete, vedere [questo script di esempio dell'interfaccia della riga di comando](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.).

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona
Per creare un set di scalabilità con ridondanza della zona, si usano un indirizzo IP pubblico e un servizio di bilanciamento del carico dello SKU *Standard*. Per una ridondanza ottimizzata, lo SKU *Standard* crea risorse di rete con ridondanza della zona. Per altre informazioni, vedere [Panoramica dello SKU Standard di Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md). La prima volta che si crea un servizio di bilanciamento del carico o un set di scalabilità con ridondanza della zona, è necessario eseguire questa procedura per registrare l'account per le funzionalità di anteprima.

1. Registrare l'account per il set di scalabilità con ridondanza della zona e le relative funzionalità di rete con il comando [az feature register](/cli/azure/feature#az_feature_register) come indicato di seguito:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. La registrazione alle funzionalità può richiedere alcuni minuti. È possibile verificare lo stato dell'operazione con il comando [az feature show](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    L'esempio seguente illustra lo stato desiderato della funzionalità, *Registered*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Quando il set di scalabilità con ridondanza della zona e le relative risorse di rete risultano entrambi *Registered*, eseguire nuovamente la registrazione dei provider *Compute* e *Network* con il comando [az provider register](/cli/azure/provider#az_provider_register) come indicato di seguito:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Per creare un set di scalabilità con ridondanza della zona, specificare più zone nel parametro `--zones`. L'esempio seguente crea un set di scalabilità con ridondanza della zona denominato *myScaleSet* nelle zone *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Le operazioni da eseguire per creare e configurare tutte le macchine virtuali e le risorse del set di scalabilità nelle zone specificate richiedono alcuni minuti. Per un esempio completo di un set di scalabilità con ridondanza della zona e delle relative risorse di rete, vedere [questo script di esempio dell'interfaccia della riga di comando](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh).


## <a name="use-azure-powershell"></a>Usare Azure PowerShell
La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'[articolo introduttivo](virtual-machine-scale-sets-create-powershell.md). Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata ed eseguire la [registrazione per l'anteprima delle zone di disponibilità](http://aka.ms/azenroll). Aggiungere il parametro `-Zone` al comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*. 

L'esempio seguente crea una configurazione di un set di scalabilità a zona singola denominata *vmssConfig* nella zona *1* dell'area *East US 2*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Per un esempio completo di un set di scalabilità a zona singola e delle relative risorse di rete, vedere [questo script di esempio di PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1).

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona
Per creare un set di scalabilità con ridondanza della zona, si usano un indirizzo IP pubblico e un servizio di bilanciamento del carico dello SKU *Standard*. Per una ridondanza ottimizzata, lo SKU *Standard* crea risorse di rete con ridondanza della zona. Per altre informazioni, vedere [Panoramica dello SKU Standard di Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md). La prima volta che si crea un servizio di bilanciamento del carico o un set di scalabilità con ridondanza della zona, è necessario eseguire questa procedura per registrare l'account per le funzionalità di anteprima.

1. Registrare l'account per il set di scalabilità con ridondanza della zona e per le relative funzionalità di rete con il comando [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) come indicato di seguito:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. La registrazione alle funzionalità può richiedere alcuni minuti. È possibile verificare lo stato dell'operazione con il comando [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    L'esempio seguente illustra lo stato desiderato della funzionalità, *Registered*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Quando il set di scalabilità con ridondanza della zona e le relative risorse di rete risultano entrambi *Registered*, eseguire nuovamente la registrazione dei provider *Compute* e *Network* con il comando [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) come indicato di seguito:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Per creare un set di scalabilità con ridondanza della zona, specificare più zone nel parametro `-Zone`. L'esempio seguente crea una configurazione del set di scalabilità con ridondanza della zona denominata *myScaleSet* nelle zone *1, 2, 3* dell'area *East US 2*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Se si crea un indirizzo IP pubblico con il comando [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) o un servizio di bilanciamento del carico con il comando [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), specificare *-SKU "Standard"* per creare risorse di rete con ridondanza della zona. È anche necessario creare un gruppo di sicurezza di rete e le relative regole per consentire ogni tipo di traffico. Per altre informazioni, vedere [Panoramica dello SKU Standard di Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md).

Per un esempio completo di un set di scalabilità con ridondanza della zona e delle relative risorse di rete, vedere [questo script di esempio di PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1).


## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure
La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'articolo introduttivo per [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md). Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata ed eseguire la [registrazione per l'anteprima delle zone di disponibilità](http://aka.ms/azenroll). Aggiungere la proprietà `zones` al tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets* nel modello e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*.

L'esempio seguente crea un set di scalabilità a zona singola per Linux denominato *myScaleSet* nella zona *1* dell'area *East US 2*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Per un esempio completo di un set di scalabilità a zona singola e delle relative risorse di rete, vedere [questo modello di esempio di Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json).

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona
Per creare un set di scalabilità con ridondanza della zona, specificare più valori nella proprietà `zones` del tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets*. L'esempio seguente crea un set di scalabilità con ridondanza della zona denominato *myScaleSet* nelle zone *1,2,3* dell'area *East US 2*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Se si crea un indirizzo IP pubblico o un servizio di bilanciamento del carico, specificare la proprietà *"sku": { "name": "Standard" }"* per creare risorse di rete con ridondanza della zona. È anche necessario creare un gruppo di sicurezza di rete e le relative regole per consentire ogni tipo di traffico. Per altre informazioni, vedere [Panoramica dello SKU Standard di Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md).

Per un esempio completo di un set di scalabilità con ridondanza della zona e delle relative risorse di rete, vedere [questo modello di esempio di Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json).


## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un set di scalabilità in una zona di disponibilità, è possibile visualizzare informazioni su come [distribuire le applicazioni nei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-deploy-app.md) o come [usare la scalabilità automatica con i set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).
