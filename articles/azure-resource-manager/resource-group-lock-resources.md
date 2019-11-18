---
title: Bloccare le risorse per impedire modifiche
description: Impedire agli utenti di aggiornare o eliminare le risorse critiche di Azure applicando un blocco per tutti gli utenti e i ruoli.
ms.topic: conceptual
ms.date: 05/14/2019
ms.openlocfilehash: 5dd5d5f58e13039842dca85ca65d6a26ce54c7e5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150786"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloccare le risorse per impedire modifiche impreviste

L'amministratore può avere la necessità di bloccare una sottoscrizione, una risorsa o un gruppo di risorse per impedire che altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse strategiche. È possibile impostare il livello di blocco **CanNotDelete** o **ReadOnly**. Nel portale i blocchi sono definiti rispettivamente **Elimina** e **Sola lettura**.

* **CanNotDelete** significa che gli utenti autorizzati possono leggere e modificare una risorsa, ma non eliminarla. 
* **ReadOnly** significa che gli utenti autorizzati possono leggere una risorsa, ma non eliminarla o aggiornarla. L'applicazione di questo blocco è simile alla concessione a tutti gli utenti autorizzati solo le autorizzazioni concesse dal ruolo **Lettore**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Come vengono applicati i blocchi

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo nel Portale di Azure](../role-based-access-control/role-assignments-portal.md).

I blocchi di Resource Manager si applicano solo alle operazioni che si verificano nel piano di gestione, costituito da operazioni inviate a `https://management.azure.com`. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Ad esempio, un blocco ReadOnly applicato a un database SQL impedisce l'eliminazione o la modifica del database. Non impedisce la creazione, l'aggiornamento o l'eliminazione di dati nel database. Le transazioni di dati sono consentite in quanto tali operazioni non vengono inviate a `https://management.azure.com`.

L'applicazione di **ReadOnly** può causare risultati imprevisti perché alcune operazioni che non sembrano modificare la risorsa richiedono effettivamente azioni bloccate dal blocco. Il blocco **ReadOnly** può essere applicato alla risorsa o al gruppo di risorse che contiene la risorsa. Alcuni esempi comuni delle operazioni bloccate da un blocco **ReadOnly** sono:

* Un blocco **ReadOnly** in un account di archiviazione impedisce a tutti gli utenti di elencare le chiavi. L'operazione di elenco delle chiavi viene gestita tramite una richiesta POST, perché le chiavi restituite sono disponibili per operazioni di scrittura.

* Un blocco **ReadOnly** applicato a una risorsa Servizio app impedisce a Visual Studio Server Explorer di visualizzare i file della risorsa perché questa interazione richiede l'accesso in scrittura.

* Un blocco **ReadOnly** in un gruppo di risorse che contiene una macchina virtuale impedisce a tutti gli utenti di avviare o riavviare la macchina virtuale. Queste operazioni richiedono una richiesta POST.

## <a name="who-can-create-or-delete-locks"></a>Utenti che possono creare o eliminare blocchi
Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.

## <a name="managed-applications-and-locks"></a>Applicazioni e blocchi gestiti

Alcuni servizi di Azure, ad esempio Azure Databricks, usano [le applicazioni gestite](../managed-applications/overview.md) per implementare il servizio. In tal caso, il servizio crea due gruppi di risorse. Un gruppo di risorse contiene una panoramica del servizio e non è bloccato. L'altro gruppo di risorse contiene l'infrastruttura per il servizio ed è bloccato.

Se si prova a eliminare il gruppo di risorse dell'infrastruttura, viene visualizzato un errore che informa che il gruppo di risorse è bloccato. Se si tenta di eliminare il blocco per il gruppo di risorse dell'infrastruttura, viene visualizzato un errore che informa che il blocco non può essere eliminato perché è di proprietà di un'applicazione di sistema.

Eliminare invece il servizio, che elimina anche il gruppo di risorse dell'infrastruttura.

Per le applicazioni gestite selezionare il servizio distribuito.

![Seleziona servizio](./media/resource-group-lock-resources/select-service.png)

Si noti che il servizio include un collegamento per un **gruppo di risorse gestite**. Il gruppo di risorse possiede l'infrastruttura ed è bloccato. Non può essere eliminato direttamente.

![Mostra gruppo gestito](./media/resource-group-lock-resources/show-managed-group.png)

Per eliminare tutti gli elementi per il servizio, incluso il gruppo di risorse dell'infrastruttura bloccata, selezionare **Elimina** per il servizio.

![Delete service](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>di Microsoft Azure
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modello

Quando si usa un modello di Gestione risorse per distribuire un blocco, si usano valori diversi per il nome e il tipo, a seconda dell'ambito del blocco.

Quando si applica un blocco a una **risorsa**, usare i formati seguenti:

* nome-`{resourceName}/Microsoft.Authorization/{lockName}`
* `{resourceProviderNamespace}/{resourceType}/providers/locks` di tipo

Quando si applica un blocco a un **gruppo di risorse** o a una **sottoscrizione**, usare i formati seguenti:

* nome-`{lockName}`
* `Microsoft.Authorization/locks` di tipo

L'esempio seguente illustra un modello che crea un piano di servizio app, un sito Web e un blocco sul sito Web. Il tipo di risorsa del blocco corrisponde al tipo di risorsa della risorsa da bloccare e a **/providers/locks**. Il nome del blocco viene creato concatenando il nome della risorsa con **/Microsoft.Authorization/** e il nome del blocco stesso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Per un esempio di impostazione di un blocco su un gruppo di risorse, vedere [creare un gruppo di risorse e bloccarlo](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Per bloccare le risorse distribuite con Azure PowerShell, usare il comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Per ottenere tutti i blocchi nella sottoscrizione, usare:

```azurepowershell-interactive
Get-AzResourceLock
```

Per ottenere tutti i blocchi per una risorsa, usare:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per ottenere tutti i blocchi per un gruppo di risorse, usare:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Per eliminare un blocco, usare:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per bloccare le risorse distribuite con l'interfaccia della riga di comando di Azure, usare il comando [az lock create](/cli/azure/lock#az-lock-create).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [az lock list](/cli/azure/lock#az-lock-list). Per ottenere tutti i blocchi nella sottoscrizione, usare:

```azurecli
az lock list
```

Per ottenere tutti i blocchi per una risorsa, usare:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Per ottenere tutti i blocchi per un gruppo di risorse, usare:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Per eliminare un blocco, usare:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
È possibile bloccare le risorse distribuite tramite l' [API REST per i blocchi di gestione](https://docs.microsoft.com/rest/api/resources/managementlocks). L'API REST consente di creare ed eliminare i blocchi e recuperare informazioni sui blocchi esistenti.

Per creare un blocco, eseguire:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Lock-name indica il nome che si desidera assegnare al blocco. Per API-Version, usare **2016-09-01**.

Nella richiesta includere un oggetto JSON che specifica le proprietà per il blocco.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md)
* È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Per altre informazioni, vedere [Informazioni su Criteri di Azure](../governance/policy/overview.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

