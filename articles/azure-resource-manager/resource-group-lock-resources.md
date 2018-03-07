---
title: Bloccare le risorse di Azure per impedire modifiche | Microsoft Docs
description: Impedire agli utenti di aggiornare o eliminare le risorse critiche di Azure applicando un blocco per tutti gli utenti e i ruoli.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 6832bd6dfb136b944a752ae61da74465a01c80a4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloccare le risorse per impedire modifiche impreviste 

L'amministratore può avere la necessità di bloccare una sottoscrizione, una risorsa o un gruppo di risorse per impedire che altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse strategiche. È possibile impostare il livello di blocco **CanNotDelete** o **ReadOnly**. Nel portale i blocchi sono definiti rispettivamente **Elimina** e **Sola lettura**.

* **CanNotDelete** significa che gli utenti autorizzati possono leggere e modificare una risorsa, ma non eliminarla. 
* **ReadOnly** significa che gli utenti autorizzati possono leggere una risorsa, ma non eliminarla o aggiornarla. L'applicazione di questo blocco è simile alla concessione a tutti gli utenti autorizzati solo le autorizzazioni concesse dal ruolo **Lettore**. 

## <a name="how-locks-are-applied"></a>Come vengono applicati i blocchi

Quando si applica un blocco in un ambito padre, tutte le risorse in tale ambito ereditano lo stesso blocco. Anche le risorse aggiunte successivamente ereditano il blocco dal padre. Il blocco più restrittivo nell'ereditarietà ha la precedenza.

Diversamente dal controllo degli accessi in base al ruolo, i blocchi di gestione consentono di applicare una restrizione a tutti gli utenti e i ruoli. Per informazioni sull'impostazione delle autorizzazioni per utenti e ruoli, vedere [Controllo degli accessi in base al ruolo nel Portale di Azure](../active-directory/role-based-access-control-configure.md).

I blocchi di Resource Manager si applicano solo alle operazioni che si verificano nel piano di gestione, costituito da operazioni inviate a `https://management.azure.com`. I blocchi non limitano il modo in cui le risorse eseguono le proprie funzioni. Vengono limitate le modifiche alle risorse, ma non le operazioni delle risorse. Un blocco di sola lettura in un database SQL impedisce ad esempio l'eliminazione o la modifica del database, ma non impedisce la creazione, l'aggiornamento o l'eliminazione dei dati nel database. Le transazioni di dati sono consentite in quanto tali operazioni non vengono inviate a `https://management.azure.com`.

L'applicazione di **ReadOnly** può causare risultati imprevisti, perché alcune operazioni che sembrano operazioni di lettura richiedono in effetti azioni aggiuntive. Ad esempio, l'inserimento di un blocco **ReadOnly** in un account di archiviazione impedisce a tutti gli utenti di ottenere un elenco delle chiavi. L'operazione di elenco delle chiavi viene gestita tramite una richiesta POST, perché le chiavi restituite sono disponibili per operazioni di scrittura. Per fare un altro esempio, l'inserimento di un blocco **ReadOnly** in una risorsa del servizio app impedisce a Esplora Server di Visual Studio di visualizzare i file relativi alla risorsa, perché tale interazione richiede l'accesso in scrittura.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Chi può creare o eliminare i blocchi nell'organizzazione
Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Dei ruoli predefiniti, solo **Proprietario** e **Amministratore Accesso utenti** garantiscono tali azioni.

## <a name="portal"></a>Portale
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modello
L'esempio seguente illustra un modello che crea un piano di servizio app, un sito Web e un blocco sul sito Web. Il tipo di risorsa del blocco corrisponde al tipo di risorsa della risorsa da bloccare e a **/providers/locks**. Il nome del blocco viene creato concatenando il nome della risorsa con **/Microsoft.Authorization/** e il nome del blocco stesso.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Per distribuire questo modello di esempio con PowerShell, usare:

```powershell
New-AzureRmResourceGroup -Name sitegroup -Location southcentralus
New-AzureRmResourceGroupDeployment -ResourceGroupName sitegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json -hostingPlanName plan0103
```

Per distribuire questo modello di esempio con l'interfaccia della riga di comando di Azure, usare:

```azurecli
az group create --name sitegroup --location southcentralus
az group deployment create --resource-group sitegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/lock.json --parameters hostingPlanName=plan0103
```

## <a name="powershell"></a>PowerShell
Per bloccare le risorse distribuite con Azure PowerShell, usare il comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```powershell
New-AzureRmResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [Get-AzureRmResourceLock](/powershell/module/azurerm.resources/get-azurermresourcelock). Per ottenere tutti i blocchi nella sottoscrizione, usare:

```powershell
Get-AzureRmResourceLock
```

Per ottenere tutti i blocchi per una risorsa, usare:

```powershell
Get-AzureRmResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Per ottenere tutti i blocchi per un gruppo di risorse, usare:

```powershell
Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup
```

Per eliminare un blocco, usare:

```powershell
$lockId = (Get-AzureRmResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzureRmResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per bloccare le risorse distribuite con l'interfaccia della riga di comando di Azure, usare il comando [az lock create](/cli/azure/lock#az_lock_create).

Per bloccare una risorsa, specificare il nome, il tipo e il gruppo di risorse della risorsa.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Per bloccare un gruppo di risorse, specificare il nome del gruppo di risorse.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Per ottenere informazioni su un blocco, usare [az lock list](/cli/azure/lock#az_lock_list). Per ottenere tutti i blocchi nella sottoscrizione, usare:

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
È possibile bloccare le risorse distribuite tramite l'[API REST per i blocchi di gestione](https://docs.microsoft.com/rest/api/resources/managementlocks). L'API REST consente di creare ed eliminare i blocchi e recuperare informazioni sui blocchi esistenti.

Per creare un blocco, eseguire:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

L'ambito può essere una sottoscrizione, un gruppo di risorse o una risorsa. Lock-name indica il nome che si desidera assegnare al blocco. Per api-version, usare **2015-01-01**.

Nella richiesta includere un oggetto JSON che specifica le proprietà per il blocco.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'organizzazione logica delle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md)
* Per modificare il gruppo di risorse in cui si trova una risorsa, vedere [Spostamento delle risorse in un nuovo gruppo di risorse](resource-group-move-resources.md)
* È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Per altre informazioni, vedere [Informazioni su Criteri di Azure](../azure-policy/azure-policy-introduction.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).

