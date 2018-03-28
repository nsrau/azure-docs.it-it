---
title: Creare un set di scalabilità di Azure che usa macchine virtuali con priorità bassa (Anteprima) | Microsoft Docs
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali con priorità bassa per ridurre i costi
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: 9e4970ecc538caab537281931b89bfd57d994cfa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Macchine virtuali con priorità bassa nei set di scalabilità (anteprima)

L'uso di macchine virtuali con priorità bassa nei set di scalabilità consente di sfruttare la capacità inutilizzata a costi notevolmente inferiori. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali con priorità bassa. Queste macchine virtuali sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità inutilizzata disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono macchine virtuali con priorità bassa in set di scalabilità, Azure le alloca se c'è capacità disponibile, ma non esiste alcun contratto di servizio per queste macchine virtuali. Un set di scalabilità con priorità bassa viene distribuito in un singolo dominio di errore e non offre alcuna garanzia di disponibilità elevata.

> [!NOTE]
> I set di scalabilità con priorità bassa sono in versione di anteprima e sono pronti per scenari di sviluppo e test. 

## <a name="eviction-policy"></a>Criteri di rimozione

Quando le macchine virtuali del set di scalabilità con priorità bassa vengono rimosse, passano allo stato di arresto (deallocazione) per impostazione predefinita. Con questi criteri di rimozione è possibile ridistribuire istanze rimosse, ma non c'è alcuna garanzia di successo dell'allocazione. Le macchine virtuali arrestate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si vuole che le macchine virtuali del set di scalabilità con priorità bassa vengano eliminate dopo essere state rimosse, è possibile impostare i criteri di rimozione per l'eliminazione nel [modello di Azure Resource Manager](#use-azure-resource-manager-templates). Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. È consigliabile usare la funzionalità di ridimensionamento automatico sui set di scalabilità con priorità bassa solo quando si impostano i criteri di rimozione per l'eliminazione, per evitare l'addebitamento dei costi dei dischi e il raggiungimento dei limiti di quota. 

> [!NOTE]
> Durante il periodo di anteprima sarà possibile impostare i criteri di rimozione tramite i [modelli di Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Distribuzione di macchine virtuali con priorità bassa nei set di scalabilità

Per distribuire macchine virtuali con priorità bassa nei set di scalabilità, è possibile impostare il nuovo flag *Priority* su *Low*. Per tutte le macchine virtuali nel set di scalabilità verrà impostata la priorità bassa. Per creare un set di scalabilità con macchine virtuali con priorità bassa, usare uno dei metodi seguenti:
- [Interfaccia della riga di comando di Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modelli di Gestione risorse di Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Usare l'interfaccia della riga di comando di Azure 2.0

La procedura per creare un set di scalabilità con macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'[articolo introduttivo](virtual-machine-scale-sets-create-cli.md). Basta aggiungere il parametro "--Priority" alla chiamata dell'interfaccia della riga di comando e impostarlo su *Low* come illustrato nell'esempio seguente:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

La procedura per creare un set di scalabilità con macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'[articolo introduttivo](virtual-machine-scale-sets-create-powershell.md).
Basta aggiungere il parametro "--Priority" a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e impostarlo su *Low* come illustrato nell'esempio seguente:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Azure Resource Manager

La procedura per creare un set di scalabilità che usa macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'articolo introduttivo per [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md). Aggiungere la proprietà "priority" al tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* nel modello e specificare *Low* come valore. Usare l'API versione *2017-10-30-preview* o successiva. 

Per impostare i criteri di rimozione per l'eliminazione, aggiungere il parametro "evictionPolicy" e impostarlo su *delete*.

L'esempio seguente crea un set di scalabilità con priorità bassa di Linux denominato *myScaleSet* in *East US 2*, che *eliminerà* le macchine virtuali nel set di scalabilità al momento della rimozione:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un set di scalabilità con macchine virtuali con priorità bassa, provare a distribuire il [modello di scalabilità automatica usando la priorità bassa](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).