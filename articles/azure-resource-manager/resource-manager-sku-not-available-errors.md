---
title: Errori SKU non disponibili
description: Viene descritto come risolvere l'errore SKU non disponibile durante la distribuzione di risorse con Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: 56afca6b6a59ca08f3fd59c4d9b3ebf12bda415a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150484"
---
# <a name="resolve-errors-for-sku-not-available"></a>Risolvere gli errori dovuti all'indisponibilità di SKU

Questo articolo descrive come risolvere l'errore **SkuNotAvailable**. Se non si riesce a trovare uno SKU appropriato in tale area o in un'area alternativa che soddisfi le esigenze aziendali, inviare una [richiesta di SKU](https://aka.ms/skurestriction) al supporto di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintomo

Quando si distribuisce una risorsa, in genere una macchina virtuale, vengono visualizzati il codice di errore e il messaggio di errore seguenti:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Questo errore viene visualizzato quando lo SKU della risorsa selezionato, ad esempio le dimensioni della macchina virtuale, non è disponibile per il percorso selezionato.

## <a name="solution-1---powershell"></a>Soluzione 1: PowerShell

Per determinare quali SKU sono disponibili in un'area, usare il comando [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Filtrare i risultati in base all'area. Per questo comando, è necessaria la versione più recente di PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

I risultati includono un elenco di SKU per la località e le eventuali limitazioni per tale SKU. Si noti che lo SKU potrebbe essere elencato come `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Soluzione 2: interfaccia della riga di comando di Azure

Per determinare quali SKU sono disponibili in un'area, usare il comando `az vm list-skus`. Usare il parametro `--location` per filtrare l'output per il percorso in uso. Usare il parametro `--size` per eseguire la ricerca in base a un nome parziale delle dimensioni.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

Il comando restituisce risultati come:

```azurecli
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

![Creare una macchina virtuale](./media/resource-manager-sku-not-available-errors/create-vm.png)

È possibile filtrare e scorrere tra le dimensioni disponibili.

![SKU disponibili](./media/resource-manager-sku-not-available-errors/available-sizes.png)

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

