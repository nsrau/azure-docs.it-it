---
title: Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure | Documentazione Microsoft
description: Informazioni su come definire i ruoli personalizzati con il Controllo degli accessi in base al ruolo per una gestione più precisa delle identità nella sottoscrizione di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3baf616e448f1f6d5292161ae125502d72141940
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266595"
---
# <a name="create-custom-roles-in-azure"></a>Creare ruoli personalizzati in Azure

Se i [ruoli predefiniti](built-in-roles.md) non soddisfano le proprie esigenze di accesso specifiche, è possibile creare ruoli personalizzati. Analogamente ai ruoli predefiniti, è possibile assegnare i ruoli personalizzati a utenti, gruppi ed entità servizio nell'ambito della sottoscrizione, del gruppo di risorse e della risorsa. I ruoli personalizzati vengono archiviati in un tenant di Azure Active Directory (Azure AD) e possono essere condivisi tra le sottoscrizioni. Ogni tenant può avere al massimo 2000 ruoli personalizzati. È possibile creare ruoli personalizzati usando Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

Questo articolo descrive un esempio di come iniziare a creare ruoli personalizzati mediante PowerShell e l'interfaccia della riga di comando di Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Creare un ruolo personalizzato per aprire richieste di supporto mediante PowerShell

Per creare un ruolo personalizzato, è possibile iniziare con un ruolo predefinito, modificarlo e quindi creare un nuovo ruolo. Per questo esempio il ruolo predefinito [Lettore](built-in-roles.md#reader) viene modificato per creare un ruolo personalizzato denominato "Lettore livello di accesso ticket di supporto". Consente all'utente di visualizzare tutti gli elementi nella sottoscrizione e anche di aprire richieste di supporto.

> [!NOTE]
> Gli unici due ruoli predefiniti che consentono a un utente di aprire richieste di supporto sono [Proprietario](built-in-roles.md#owner) e [Collaboratore](built-in-roles.md#contributor). Per consentire a un utente di aprire richieste di supporto, è necessario che gli sia stato assegnato un ruolo nell'ambito della sottoscrizione, perché tutte le richieste di supporto vengono create in base a una sottoscrizione di Azure.

In PowerShell usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per esportare il ruolo [Lettore](built-in-roles.md#reader) in formato JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Di seguito è riportato l'output JSON per il ruolo [Lettore](built-in-roles.md#reader). Un ruolo tipico è costituito da tre sezioni principali: `Actions`, `NotActions` e `AssignableScopes`. La sezione `Actions` elenca tutte le operazioni consentite per questo ruolo. Per escludere operazioni da `Actions`, è necessario aggiungerle a `NotActions`. Le autorizzazioni effettive vengono calcolate sottraendo le operazioni `NotActions` dalle operazioni `Actions`.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Successivamente, si modifica l'output JSON per creare il ruolo personalizzato. In questo caso per creare ticket di supporto è necessario aggiungere l'operazione `Microsoft.Support/*`. Ogni operazione è resa disponibile da un provider di risorse. Per ottenere un elenco delle operazioni per un provider di risorse, è possibile usare il comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) oppure vedere [Azure Resource Manager Resource Provider operations](resource-provider-operations.md) (Operazioni dei provider di risorse di Azure Resource Manager).

È obbligatorio che il ruolo contenga gli ID di sottoscrizione espliciti in cui viene usato. Gli ID di sottoscrizione sono elencati in `AssignableScopes`. In caso contrario non sarà possibile importare il ruolo nella sottoscrizione.

Infine, è necessario impostare la proprietà `IsCustom` su `true` per specificare che si tratta di un ruolo personalizzato.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Per creare il nuovo ruolo personalizzato, usare il comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e specificare il file di definizione del ruolo JSON aggiornato.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Dopo l'esecuzione di [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), il nuovo ruolo personalizzato è disponibile nel portale di Azure e può essere assegnato agli utenti.

![Screenshot del ruolo personalizzato importato nel portale di Azure](./media/custom-roles/18.png)

![Screenshot dell'assegnazione del ruolo importato personalizzato all'utente nella stessa directory](./media/custom-roles/19.png)

![Screenshot delle autorizzazioni per il ruolo importato personalizzato](./media/custom-roles/20.png)

Gli utenti con questo ruolo personalizzato possono creare nuove richieste di supporto.

![Screenshot del ruolo personalizzato che crea richieste di supporto](./media/custom-roles/21.png)

Gli utenti con questo ruolo personalizzato non possono eseguire altre azioni, ad esempio creare macchine virtuali o creare gruppi di risorse.

![Screenshot del ruolo personalizzato che non può creare macchine virtuali](./media/custom-roles/22.png)

![Screenshot del ruolo personalizzato che non può creare nuovi gruppi di risorse](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Creare un ruolo personalizzato per aprire richieste di supporto mediante l'interfaccia della riga di comando di Azure

I passaggi per creare un ruolo personalizzato usando l'interfaccia della riga di comando di Azure sono simili alla procedura con PowerShell, ad eccezione del fatto che l'output JSON è diverso.

Per questo esempio, è possibile iniziare con il ruolo predefinito [Lettore](built-in-roles.md#reader). Per elencare le azioni del ruolo [Lettore](built-in-roles.md#reader), usare il comando [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Creare un file JSON con il formato seguente. L'operazione `Microsoft.Support/*` è stata aggiunta nella sezione `Actions` in modo che questo utente possa aprire richieste di supporto pur continuando a essere un lettore. È necessario aggiungere l'ID sottoscrizione in cui verrà usato questo ruolo nella sezione `AssignableScopes`.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Per creare il nuovo ruolo personalizzato, usare il comando [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Il nuovo ruolo personalizzato è ora disponibile nel portale di Azure e il processo per usare questo ruolo è uguale a quello descritto nella sezione precedente per PowerShell.

![Screenshot del portale di Azure del ruolo personalizzato creato usando l'interfaccia della riga di comando 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Vedere anche 
- [Informazioni sulle definizioni del ruolo](role-definitions.md)
- [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](role-assignments-powershell.md)
- [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](role-assignments-cli.md)
- [Gestire il controllo degli accessi in base al ruolo con l'API REST](role-assignments-rest.md)
