---
title: Errori SKU non disponibili
description: Viene descritto come risolvere l'errore SKU non disponibile durante la distribuzione di risorse con Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942719"
---
# <a name="resolve-errors-for-sku-not-available"></a>Risolvere gli errori dovuti all'indisponibilità di SKU

Questo articolo descrive come risolvere l'errore **SkuNotAvailable**. Se non si riesce a trovare uno SKU appropriato in tale area/area o in un'area/area alternativa che soddisfi le esigenze aziendali, inviare una [richiesta di SKU](https://aka.ms/skurestriction) al supporto tecnico di Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa, in genere una macchina virtuale, vengono visualizzati il codice di errore e il messaggio di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato.

Se si distribuisce una macchina virtuale di Azure spot o un'istanza del set di scalabilità di punti, non esiste alcuna capacità per Azure spot in questa località. Per ulteriori informazioni, vedere [messaggi di errore di individuazione](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Soluzione 1: PowerShell

Per determinare quali SKU sono disponibili in un'area/area, usare il comando [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) . Filtrare i risultati in base all'area. Per questo comando, è necessaria la versione più recente di PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

I risultati includono un elenco di SKU per la località e le eventuali limitazioni per tale SKU. Si noti che lo SKU potrebbe essere elencato come `NotAvailableForSubscription`.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Esempi aggiuntivi:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

L'aggiunta di "FC" alla fine restituisce maggiori dettagli.

## <a name="solution-2---azure-cli"></a>Soluzione 2: interfaccia della riga di comando di Azure

Per determinare quali SKU sono disponibili in un'area, usare il comando `az vm list-skus`. Usare il parametro `--location` per filtrare l'output per il percorso in uso. Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Il comando restituisce risultati come:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Soluzione 3: portale di Azure

Per determinare quali SKU sono disponibili in un'area, usare il [portale](https://portal.azure.com). Accedere al portale e aggiungere una risorsa tramite l'interfaccia. Quando si impostano i valori, vengono visualizzati gli SKU disponibili per tale risorsa. Non è necessario completare la distribuzione.

Ad esempio, avviare il processo di creazione di una macchina virtuale. Per visualizzare altre dimensioni disponibili, selezionare **Modifica dimensioni**.

![Creare una macchina virtuale](./media/error-sku-not-available/create-vm.png)

È possibile filtrare e scorrere tra le dimensioni disponibili.

![SKU disponibili](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Soluzione 4: REST

Per determinare quali SKU sono disponibili in un'area, usare l'operazione [SKU di risorse - Elenco](/rest/api/compute/resourceskus/list).

Le aree e gli SKU disponibili vengono restituiti nel formato seguente:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

