---
title: "Creare un set di scalabilità di Azure che usa le zone di disponibilità (anteprima) | Documenti Microsoft"
description: "Informazioni su come creare set di scalabilità di macchine virtuali di Azure che utilizzano le aree di disponibilità per ottenere una maggiore ridondanza da interruzioni"
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
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Creare un set di scalabilità della macchina virtuale che utilizza le zone di disponibilità (anteprima)
Per proteggere i set di scalabilità della macchina virtuale da errori a livello di Data Center, è possibile creare un set in una zona di disponibilità di scalabilità. Aree di Azure che supportano le zone di disponibilità dispone di almeno tre zone separate, ognuna con i propri indipendente dal risparmio energia di origine, rete e raffreddamento. Per ulteriori informazioni, vedere [Overview of Availability Zones](../availability-zones/az-overview.md) (Panoramica delle zone di disponibilità in Azure).

Per utilizzare le zone di disponibilità, è necessario creare il set di scalabilità in un [area di Azure supportata](../availability-zones/az-overview.md#regions-that-support-availability-zones). È possibile creare un set di scalabilità che utilizza le zone di disponibilità con uno dei metodi seguenti:

- [Azure portal](#use-the-azure-portal)
- [Interfaccia della riga di comando di Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modelli di Gestione risorse di Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Usare il portale di Azure
Il processo per creare un set di scalabilità che utilizza un orario di disponibilità è lo stesso come descritto in dettaglio nella [introduzione articolo](virtual-machine-scale-sets-create-portal.md). Quando si seleziona un'area di Azure supportata, è possibile creare una scala impostata in una delle aree disponibili, come illustrato nell'esempio seguente:

![Creare un set in una singola zona disponibilità di scalabilità](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Il set di scalabilità e il supporto di risorse, ad esempio il servizio di bilanciamento del carico di Azure e l'indirizzo IP pubblico, vengono creati nella zona singola specificato.


## <a name="use-the-azure-cli-20"></a>Usare l'interfaccia della riga di comando di Azure 2.0
Il processo per creare un set di scalabilità che utilizza un orario di disponibilità è lo stesso come descritto in dettaglio nella [introduzione articolo](virtual-machine-scale-sets-create-cli.md). Per utilizzare le zone di disponibilità, è necessario creare il set in un'area di Azure supportata di scalabilità. Aggiungere il `--zones` parametro per il [vmss az creare](/cli/azure/vmss#az_vmss_create) comando e specificare il fuso orario da utilizzare (ad esempio zona *1*, *2*, o *3*). L'esempio seguente crea un set denominato di scalabilità *myScaleSet* nella zona *1*:

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

Sono necessari alcuni minuti per creare e configurare tutti i set di scalabilità di risorse e le macchine virtuali nell'area che si specifica.


## <a name="use-azure-powershell"></a>Usare Azure PowerShell
Il processo per creare un set di scalabilità che utilizza un orario di disponibilità è lo stesso come descritto in dettaglio nella [introduzione articolo](virtual-machine-scale-sets-create-powershell.md). Per utilizzare le zone di disponibilità, è necessario creare il set in un'area di Azure supportata di scalabilità. Aggiungere il `-Zone` parametro per il [New AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) comando e specificare il fuso orario da utilizzare (ad esempio zona *1*, *2*, o *3*). Nell'esempio seguente viene creata una scala di set di configurazione denominata *vmssConfig* in *Stati Uniti orientali 2* zona *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Per creare il set di scalabilità effettivo, seguire i passaggi aggiuntivi, come descritto in dettaglio nella [introduzione articolo](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure
Il processo per creare un set di scalabilità che utilizza un orario di disponibilità è lo stesso come descritto in dettaglio nell'articolo recupero avviata per [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md). Per utilizzare le zone di disponibilità, è necessario creare il set in un'area di Azure supportata di scalabilità. Aggiungere il `zones` proprietà per il *Microsoft.Compute/virtualMachineScaleSets* risorse digitare nel modello e specificare il fuso orario da utilizzare (ad esempio zona *1*, *2*, o *3*). Nell'esempio seguente viene creata una scala di Linux set denominata *myScaleSet* in *Stati Uniti orientali 2* zona *1*:

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

Per creare il set di scalabilità effettivo, seguire i passaggi aggiuntivi, come descritto in dettaglio nell'articolo recupero avviata per [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un set in una zona di disponibilità di scalabilità, è possibile visualizzare informazioni come [set di scalabilità delle applicazioni di distribuzione nella macchina virtuale](virtual-machine-scale-sets-deploy-app.md) o [utilizzare scalabilità automatica con il set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).
