---
title: Informazioni sull'ambito per RBAC di Azure
description: Informazioni sull'ambito per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e su come determinare l'ambito di una risorsa.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856152"
---
# <a name="understand-scope-for-azure-rbac"></a>Informazioni sull'ambito per RBAC di Azure

*Scope* è il set di risorse a cui si applica l'accesso. Quando si assegna un ruolo, è importante comprendere l'ambito in modo che sia possibile concedere a un'entità di sicurezza solo l'accesso effettivamente necessario. Limitando l'ambito, si limitano le risorse a rischio se l'entità di sicurezza è sempre compromessa.

## <a name="scope-levels"></a>Livelli di ambito

In Azure è possibile specificare un ambito a quattro livelli: [gruppo di gestione](../governance/management-groups/overview.md), sottoscrizione, [gruppo di risorse](../azure-resource-manager/management/overview.md#resource-groups)e risorsa. Gli ambiti sono strutturati in una relazione padre-figlio. Ogni livello di gerarchia rende più specifico l'ambito. È possibile assegnare i ruoli a qualsiasi livello dell'ambito. Il livello selezionato determina la frequenza con cui viene applicato il ruolo. I livelli inferiori ereditano le autorizzazioni dei ruoli dai livelli più alti. 

![Ambito per un'assegnazione di ruolo](./media/scope-overview/rbac-scope-no-label.png)

I gruppi di gestione sono un livello di ambito sopra le sottoscrizioni, ma i gruppi di gestione supportano gerarchie più complesse. Nel diagramma seguente viene illustrato un esempio di una gerarchia di gruppi di gestione e sottoscrizioni che è possibile definire. Per ulteriori informazioni sui gruppi di gestione, vedere informazioni sui [gruppi di gestione di Azure](../governance/management-groups/overview.md).

![Gruppo di gestione e gerarchia delle sottoscrizioni](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Formato ambito

Se si aggiungono assegnazioni di ruolo tramite la riga di comando, è necessario specificare l'ambito. Per gli strumenti da riga di comando, l'ambito è una stringa potenzialmente lunga che identifica l'ambito esatto dell'assegnazione di ruolo. Nell'portale di Azure questo ambito è in genere elencato come *ID risorsa*.

L'ambito è costituito da una serie di identificatori separati dal carattere barra (/). Questa stringa può essere considerata come espressione della seguente gerarchia, in cui il testo senza segnaposto (`{}`) è rappresentato da identificatori fissi:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` è l'ID della sottoscrizione da usare (GUID).
- `{resourcesGroupName}` è il nome del gruppo di risorse che contiene la risorsa.
- `{providerName}` è il nome del [provider di risorse](../azure-resource-manager/management/azure-services-resource-providers.md) che gestisce la risorsa e quindi `{resourceType}` `{resourceSubType*}` identifica altri livelli all'interno del provider di risorse.
- `{resourceName}` è l'ultima parte della stringa che identifica una risorsa specifica.

I gruppi di gestione sono un livello superiore alle sottoscrizioni e hanno l'ambito più ampio (meno specifico). Le assegnazioni di ruolo a questo livello si applicano alle sottoscrizioni all'interno del gruppo di gestione. L'ambito di un gruppo di gestione ha il formato seguente:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Esempi di ambito

> [!div class="mx-tableFixed"]
> | Scope | Esempio |
> | --- | --- |
> | Gruppo di gestione | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscription | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Risorsa | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Come determinare l'ambito di una risorsa

È piuttosto semplice determinare l'ambito per un gruppo di gestione, una sottoscrizione o un gruppo di risorse. È necessario solo conoscerne il nome e l'ID sottoscrizione. Tuttavia, la determinazione dell'ambito di una risorsa richiede un minor numero di operazioni. Ecco alcuni modi in cui è possibile determinare l'ambito di una risorsa.

- Nella portale di Azure aprire la risorsa e quindi esaminare le proprietà. La risorsa dovrebbe elencare l' **ID di risorsa** in cui è possibile determinare l'ambito. Ecco ad esempio gli ID risorsa per un account di archiviazione.

    ![ID risorsa per un account di archiviazione in portale di Azure](./media/scope-overview/scope-resource-id.png)

- Un altro modo consiste nell'usare il portale di Azure per assegnare un ruolo temporaneamente nell'ambito della risorsa e quindi usare [Azure PowerShell](role-assignments-list-powershell.md) o l'interfaccia della riga di comando di [Azure](role-assignments-list-cli.md) per elencare l'assegnazione di ruolo. Nell'output l'ambito verrà elencato come proprietà.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Procedura per aggiungere un'assegnazione di ruolo](role-assignments-steps.md)
- [Provider di risorse per i servizi di Azure](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Che cosa sono i gruppi di gestione di Azure?](../governance/management-groups/overview.md)
