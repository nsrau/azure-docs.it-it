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
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803199"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Macchine virtuali con priorità bassa nei set di scalabilità (anteprima)

L'uso di macchine virtuali con priorità bassa nei set di scalabilità consente di sfruttare la capacità inutilizzata a costi notevolmente inferiori. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali con priorità bassa. Queste macchine virtuali sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità inutilizzata disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono macchine virtuali con priorità bassa in set di scalabilità, Azure le alloca se c'è capacità disponibile, ma non esiste alcun contratto di servizio per queste macchine virtuali. Un set di scalabilità con priorità bassa viene distribuito in un singolo dominio di errore e non offre alcuna garanzia di disponibilità elevata.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si creano set di scalabilità con priorità bassa, è possibile impostare i criteri di rimozione su *Deallocare* (impostazione predefinita) o *Eliminare*. 

Il criterio *Deallocare* cambia le macchine virtuali rimosse portandole allo stato deallocato arrestato, consentendo di ridistribuire le istanze rimosse. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le macchine virtuali del set di scalabilità con priorità bassa vengano eliminate dopo essere state rimosse, è possibile impostare i criteri di rimozione su *elimina*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. È consigliabile usare la funzionalità di ridimensionamento automatico sui set di scalabilità con priorità bassa solo quando si impostano i criteri di rimozione per l'eliminazione, per evitare l'addebitamento dei costi dei dischi e il raggiungimento dei limiti di quota. 

> [!NOTE]
> Durante la fase di anteprima sarà possibile impostare i criteri di rimozione tramite il [portale di Azure](#use-the-azure-portal) e i [modelli di Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Distribuzione di macchine virtuali con priorità bassa nei set di scalabilità

Per distribuire macchine virtuali con priorità bassa nei set di scalabilità, è possibile impostare il nuovo flag *Priority* su *Low*. Per tutte le macchine virtuali nel set di scalabilità verrà impostata la priorità bassa. Per creare un set di scalabilità con macchine virtuali con priorità bassa, usare uno dei metodi seguenti:
- [Portale di Azure](#use-the-azure-portal)
- Interfaccia della riga di comando di Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelli di Gestione risorse di Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

La procedura per creare un set di scalabilità che usi macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'[articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag con priorità bassa e il criterio di eliminazione: ![Creare un set di scalabilità con macchine virtuali con priorità bassa](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

La procedura per creare un set di scalabilità con macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'[articolo introduttivo](quick-create-cli.md). Basta aggiungere il parametro "--Priority" alla chiamata dell'interfaccia della riga di comando e impostarlo su *Low* come illustrato nell'esempio seguente:

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

La procedura per creare un set di scalabilità con macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'[articolo introduttivo](quick-create-powershell.md).
Basta aggiungere il parametro "-Priority" a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) e impostarlo su *Low*, come illustrato nell'esempio seguente:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Azure Resource Manager

La procedura per creare un set di scalabilità che usa macchine virtuali con priorità bassa è identica a quella descritta in dettaglio nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). Aggiungere la proprietà "priority" al tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* nel modello e specificare *Low* come valore. Usare l'API versione *2018-03-01* o successiva. 

Per impostare i criteri di rimozione per l'eliminazione, aggiungere il parametro "evictionPolicy" e impostarlo su *delete*.

L'esempio seguente crea un set di scalabilità con priorità bassa di Linux denominato *myScaleSet* in *East US 2*, che *eliminerà* le macchine virtuali nel set di scalabilità al momento della rimozione:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
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
## <a name="faq"></a>Domande frequenti

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>È possibile convertire i set di scalabilità esistenti in set di scalabilità con priorità bassa?
No, l'impostazione del flag con priorità bassa è supportata solo in fase di creazione.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>È possibile creare un set di scalabilità sia con macchine virtuali regolari sia con macchine virtuali con priorità bassa?
No, un set di scalabilità non supporta più di un tipo di priorità.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Come viene gestita la quota per le macchine virtuali con priorità bassa?
Le macchine virtuali con priorità bassa e le macchine virtuali regolari condividono lo stesso pool di quota. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Si può usare la scalabilità automatica con i set di scalabilità con priorità bassa?
Sì, è possibile impostare le regole di scalabilità automatica nel set di scalabilità con priorità bassa. Se le macchine virtuali vengono rimosse, la scalabilità automatica può provare a creare nuove macchine virtuali con priorità bassa. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>La scalabilità automatica funziona con entrambi i criteri di rimozione (deallocare ed eliminare)?
È consigliabile impostare i criteri di rimozione da eliminare quando si usa la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un set di scalabilità con macchine virtuali con priorità bassa, provare a distribuire il [modello di scalabilità automatica usando la priorità bassa](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).
