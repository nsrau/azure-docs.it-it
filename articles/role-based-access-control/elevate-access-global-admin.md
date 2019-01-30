---
title: Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure | Microsoft Docs
description: Viene descritto come elevare i privilegi di accesso per un amministratore globale per gestire tutte le sottoscrizioni e i gruppi di gestione in Azure Active Directory usando il portale di Azure o l'API REST.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7552018c32078295c164023f909a604c6522c32f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437471"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure

Come amministratore globale in Azure Active Directory (Azure AD), potrebbe non essere disponibile l'accesso a tutte le sottoscrizioni e i gruppi di gestione nella directory. Questo articolo descrive i modi in cui è possibile elevare i privilegi di accesso a tutte le sottoscrizioni e tutti i gruppi di gestione.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Motivi per cui può essere necessario elevare i privilegi di accesso

In alcune occasioni, un amministratore globale potrebbe avere bisogno di eseguire le operazioni seguenti:

- Ottenere nuovamente l'accesso a una sottoscrizione o a un gruppo di gestione di Azure quando un utente ha perso l'accesso
- Concedere a un altro utente o a se stesso l'accesso a una sottoscrizione o a un gruppo di gestione di Azure
- Visualizzare tutte le sottoscrizioni o tutti i gruppi di gestione di Azure in un'organizzazione
- Concedere a un'app di automazione (come un'app di fatturazione o di controllo) l'accesso a tutte le sottoscrizioni o a tutti i gruppi di gestione di Azure

## <a name="how-does-elevate-access-work"></a>Come funziona l'elevazione dei privilegi di accesso

Le risorse di Azure AD e Azure sono protette in modo indipendente le une dalle altre. Questo significa che le assegnazioni di ruolo di Azure AD non concedono l'accesso alle risorse di Azure e le assegnazioni di ruolo di Azure non concedono l'accesso ad Azure AD. Un [amministratore globale](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) di Azure AD può tuttavia assegnare a se stesso l'accesso a tutte le sottoscrizioni e ai gruppi di gestione di Azure nella directory. Usare questa funzionalità se non si ha accesso alle risorse della sottoscrizione di Azure, come le macchine virtuali o gli account di archiviazione, e si vuole usare il privilegio di amministratore globale per ottenere l'accesso a queste risorse.

Quando si elevano i privilegi di accesso, si viene assegnati al ruolo [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) nell'ambito radice (`/`). Questo consente di visualizzare tutte le risorse e assegnare l'accesso in qualsiasi sottoscrizione o gruppo di gestione nella directory. Le assegnazioni del ruolo Amministratore Accesso utenti possono essere rimosse tramite PowerShell.

L'accesso con privilegi elevati dovrebbe essere rimosso una volta apportate le modifiche necessarie nell'ambito radice.

![Elevare i privilegi di accesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portale di Azure

Seguire questa procedura per eseguire con privilegi elevati l'accesso per un amministratore globale usando il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com) o all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) come amministratore globale.

1. Nell'elenco di navigazione fare clic su **Azure Active Directory** e quindi su **Proprietà**.

   ![Proprietà di Azure AD - Screenshot](./media/elevate-access-global-admin/aad-properties.png)

1. In **Gestione degli accessi per le risorse di Azure** impostare l'interruttore su **Sì**.

   ![Gestione degli accessi per le risorse di Azure - screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando si imposta l'interruttore su **Sì**, si viene assegnati al ruolo Amministratore Accesso utenti nel controllo degli accessi in base al ruolo Azure nell'ambito radice (/). In questo modo si ottiene l'autorizzazione ad assegnare ruoli in tutte le sottoscrizioni e i gruppi di gestione di Azure associati a questa directory di Azure AD. Questo interruttore è disponibile solo per gli utenti a cui è assegnato il ruolo Amministratore globale in Azure AD.

   Quando si imposta l'interruttore su **No**, il ruolo Amministratore Accesso utenti nel controllo degli accessi in base al ruolo Azure viene rimosso dall'account utente. Non è quindi più possibile assegnare ruoli in tutte le sottoscrizioni e i gruppi di gestione di Azure associati a questa directory di Azure AD. È possibile visualizzare e gestire solo le sottoscrizioni e i gruppi di gestione di Azure ai quali si ha accesso.

1. Fare clic su **Salva** per salvare l'impostazione.

   Questa impostazione non è una proprietà globale e si applica solo all'utente attualmente connesso. Non è possibile elevare i privilegi di accesso per tutti i membri del ruolo Amministratore globale.

1. Disconnettersi e accedere nuovamente per aggiornare l'accesso.

    A questo punto dovrebbe essere disponibile l'accesso a tutti i gruppi di gestione e le sottoscrizioni nella directory. Si noterà l'assegnazione del ruolo Amministratore Accesso utenti nell'ambito radice.

   ![Assegnazioni di ruolo per la sottoscrizione con ambito radice - screenshot](./media/elevate-access-global-admin/iam-root.png)

1. Apportare le modifiche necessarie per cui sono richiesti privilegi elevati.

    Per informazioni sull'assegnazione dei ruoli, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](role-assignments-portal.md). Se si usa Azure AD Privileged Identity Management (PIM), vedere [Individuare le risorse di Azure per la gestione in PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) oppure [Assegnare i ruoli delle risorse di Azure in PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Al termine, impostare l'interruttore **Gestione degli accessi per le risorse di Azure** di nuovo su **No**. Poiché si tratta di un'impostazione per utente, è necessario essere connessi con lo stesso utente usato per elevare i privilegi di accesso.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-role-assignment-at-the-root-scope-"></a>Elencare l'assegnazione di ruolo all'ambito radice (/)

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
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Rimuovere un'assegnazione di ruolo all'ambito radice (/)

Per rimuovere un'assegnazione di ruolo Amministratore Accesso utenti per un utente nell'ambito radice (`/`), seguire questa procedura.

1. Accedere come utente autorizzato a rimuovere l'accesso con privilegi elevati. Può essere lo stesso utente usato per elevare i privilegi di accesso o un altro Amministratore globale con accesso con privilegi elevati nell'ambito radice.


1. Usare il comando [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) per rimuovere l'assegnazione del ruolo Amministratore Accesso utenti.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Eseguire con privilegi elevati l'accesso per l'amministratore globale

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

### <a name="list-role-assignments-at-the-root-scope-"></a>Elencare le assegnazioni di ruolo all'ambito radice (/)

È possibile elencare tutte le assegnazioni di ruolo per un utente all'ambito radice (`/`).

- Chiamare [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope) dove `{objectIdOfUser}` è l'ID oggetto dell'utente di cui si vogliono recuperare le assegnazioni di ruolo.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Elenca le assegnazioni di rifiuto all'ambito radice (/)

È possibile elencare tutte le assegnazioni di rifiuto per un utente all'ambito radice (`/`).

- Chiamare GET denyAssignments dove `{objectIdOfUser}` è l'ID oggetto dell'utente di cui si vogliono recuperare le assegnazioni di rifiuto.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Rimuovere l'accesso con privilegi elevati

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

2. È necessario anche elencare l'assegnazione di ruolo per l'amministratore della directory nell'ambito della directory. Elencare tutte le assegnazioni nell'ambito della directory per il `principalId` dell'amministratore della directory che ha effettuato la chiamata per l'accesso con privilegi elevati. Sono incluse tutte le assegnazioni nella directory per il parametro objectId.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Un amministratore della directory non dovrebbe avere molte assegnazioni. Se la query precedente restituisce troppe assegnazioni, è anche possibile eseguire una query per ottenere tutte le assegnazioni solo a livello di ambito della directory e quindi filtrare i risultati: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Le chiamate precedenti restituiscono un elenco di assegnazioni di ruolo. Individuare l'assegnazione di ruolo in cui l'ambito è `"/"`, `roleDefinitionId` termina con l'ID del nome del ruolo presente nel passaggio 1 e `principalId` corrisponde all'objectId dell'amministratore della directory. 
    
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

- [Informazioni sui diversi ruoli](rbac-and-directory-admin-roles.md)
- [Controllo degli accessi in base al ruolo con REST](role-assignments-rest.md)
