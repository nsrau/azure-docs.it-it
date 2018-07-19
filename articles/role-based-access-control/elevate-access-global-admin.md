---
title: Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory | Microsoft Docs
description: Questo articolo descrive come eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory usando il portale di Azure o l'API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0abf0a5971435fc3842a93e79d39468cba5c74da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445212"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Eseguire con privilegi elevati l'accesso per l'amministratore globale in Azure Active Directory

In alcune occasioni un [amministratore globale](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) in Azure Active Directory (Azure AD) potrebbe avere bisogno di eseguire le operazioni seguenti:

- Ottenere nuovamente l'accesso a una sottoscrizione di Azure quando un utente ha perso l'accesso
- Concedere a un altro utente o a se stesso l'accesso a una sottoscrizione di Azure
- Visualizzare tutte le sottoscrizioni di Azure in un'organizzazione
- Concedere a un'app di automazione (come un'app di fatturazione o di controllo) l'accesso a tutte le sottoscrizioni di Azure

Per impostazione predefinita, i ruoli di amministratore di Azure AD e i ruoli di controllo degli accessi in base al ruolo di Azure non si estendono ad Azure AD e Azure. Tuttavia, un amministratore globale di Azure AD può eseguire l'accesso con privilegi elevati per gestire le sottoscrizioni di Azure e i gruppi di gestione. Quando si esegue l'accesso con privilegi elevati, verrà concesso il ruolo [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) (un ruolo di controllo degli accessi in base al ruolo di Azure) in tutte le sottoscrizioni per un determinato tenant. Il ruolo Amministratore Accesso utenti consente di concedere ad altri utenti l'accesso alle risorse di Azure all'ambito radice (`/`).

Questa elevazione dei privilegi dovrebbe essere temporanea ed eseguita solo quando necessario.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Eseguire con privilegi elevati l'accesso per un amministratore globale usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) o all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com).

1. Nell'elenco di navigazione fare clic su **Azure Active Directory** e quindi su **Proprietà**.

   ![Proprietà di Azure AD - Screenshot](./media/elevate-access-global-admin/aad-properties.png)

1. In **Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione** impostare l'opzione su **Sì**.

   ![Gli amministratori globali possono gestire le sottoscrizioni di Azure e i gruppi di gestione - Screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando si imposta l'opzione su **Sì**, l'account di amministratore globale (utente attualmente connesso) viene aggiunto al ruolo Amministratore Accesso utenti nel Controllo degli accessi in base al ruolo di Azure all'ambito radice (`/`), concedendo all'utente l'accesso per visualizzare e creare report per tutte le sottoscrizioni di Azure associate al tenant di Azure AD.

   Quando si imposta l'opzione su **No**, l'account di amministratore globale (utente attualmente connesso) viene rimosso dal ruolo Amministratore Accesso utenti nel Controllo degli accessi in base al ruolo di Azure. Non è possibile visualizzare tutte le sottoscrizioni di Azure associate al tenant di Azure AD ed è possibile visualizzare e gestire solo le sottoscrizioni di Azure a cui è stato concesso l'accesso.

1. Fare clic su **Salva** per salvare l'impostazione.

   Questa impostazione non è una proprietà globale e si applica solo all'utente attualmente connesso.

1. Eseguire le attività necessarie per cui sono richiesti privilegi elevati. Al termine, impostare nuovamente l'opzione su **No**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Elencare l'assegnazione di ruolo all'ambito radice (/) tramite PowerShell

Per elencare l'assegnazione di ruolo Amministratore Accesso utenti per un utente all'ambito radice (`/`), usare il comando [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>Rimuovere un'assegnazione di ruolo all'ambito radice (/) tramite PowerShell

Per rimuovere un'assegnazione di ruolo Amministratore Accesso utenti per un utente all'ambito radice (`/`), usare il comando [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Eseguire con privilegi elevati l'accesso per l'amministratore globale usando l'API REST

Usare la procedura di base seguente per eseguire con privilegi elevati l'accesso per l'amministratore globale usando l'API REST.

1. Usando REST, chiamare `elevateAccess`, che concede all'utente il ruolo Amministratore Accesso utenti all'ambito radice (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Creare un'[assegnazione di ruolo](/rest/api/authorization/roleassignments) per assegnare un ruolo in qualsiasi ambito. Nell'esempio seguente vengono illustrate le proprietà per l'assegnazione del ruolo {roleDefinitionID} all'ambito radice (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Come Amministratore Accesso utenti, è inoltre possibile rimuovere le assegnazioni di ruolo all'ambito radice (`/`).

1. Rimuovere i privilegi di Amministratore Accesso utenti fino a quando non saranno di nuovo necessari.

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>Elencare le assegnazioni di ruolo all'ambito radice (/) tramite l'API REST

È possibile elencare tutte le assegnazioni di ruolo per un utente all'ambito radice (`/`).

- Chiamare [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) dove `{objectIdOfUser}` è l'ID oggetto dell'utente di cui si vogliono recuperare le assegnazioni di ruolo.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>Rimuovere l'accesso con privilegi elevati tramite l'API REST

Quando si chiama `elevateAccess`, si crea un'assegnazione di ruolo per se stessi in modo da revocare i privilegi necessari per rimuovere l'assegnazione.

1. Chiamare [GET roleDefinitions](/rest/api/authorization/roledefinitions/get) con `roleName` uguale a User Access Administrator per determinare l'ID del nome del ruolo Amministratore Accesso utenti.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Salvare l'ID dal parametro `name`, in questo caso `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. È inoltre necessario elencare l'assegnazione di ruolo per l'amministratore del tenant nell'ambito del tenant. Elencare tutte le assegnazioni nell'ambito del tenant per `principalId` dell'amministratore del tenant che ha effettuato la chiamata per l'accesso con privilegi elevati. Sono incluse tutte le assegnazioni nel tenant per il parametro objectId.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Un amministratore del tenant non dovrebbe avere molte assegnazioni. Se la query precedente restituisce troppe assegnazioni, è anche possibile eseguire una query per ottenere tutte le assegnazioni solo a livello di ambito del tenant e quindi filtrare i risultati: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Le chiamate precedenti restituiscono un elenco di assegnazioni di ruolo. Individuare l'assegnazione di ruolo in cui l'ambito è `"/"`, `roleDefinitionId` termina con l'ID del nome del ruolo presente nel passaggio 1 e `principalId` corrisponde a objectId dell'amministratore del tenant. 
    
    Esempio di assegnazione di ruolo:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Di nuovo, salvare l'ID dal parametro `name`, in questo caso e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Usare infine l'ID di assegnazione di ruolo per rimuovere l'assegnazione aggiunta da `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Controllo degli accessi in base al ruolo con REST](role-assignments-rest.md)
- [Gestire l'accesso alle risorse di Azure con Privileged Identity Management](pim-azure-resource.md)
- [Gestire l'accesso alla gestione di Azure con accesso condizionale](conditional-access-azure-management.md)
