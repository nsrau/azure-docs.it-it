---
title: Accesso con privilegi elevati per l'amministratore tenat - Azure AD | Microsoft Docs
description: Questo argomento descrive i ruoli predefiniti per il controllo degli accessi in base al ruolo.
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: andredm
ms.openlocfilehash: c1f49e2c7836a56f37aafcaad0cb74278213a720
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Accesso con privilegi elevati come amministratore tenant con il Controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo consente agli amministratori tenant di ottenere i privilegi elevati temporanei per l'accesso in modo da poter concedere autorizzazioni superiori rispetto al normale. Un amministratore tenant può elevare se stesso al ruolo di Amministratore Accesso utenti quando necessario. Tale ruolo offre all'amministratore tenant le autorizzazioni per concedere a se stesso o ad altri i ruoli nell'ambito del "/".

Questa funzionalità è importante perché consente all'amministratore tenant di visualizzare tutte le sottoscrizioni presenti in un'organizzazione. Consente inoltre alle app di automazione, ad esempio la fatturazione e il controllo, di accedere a tutte le sottoscrizioni e offrire una visualizzazione accurata dello stato dell'organizzazione per la gestione della fatturazione o degli asset.  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>Usare elevateAccess per l'accesso ai tenant con l'interfaccia di amministrazione di Azure AD

1. Passare all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) e accedere con le proprie credenziali.

2. Scegliere **Proprietà** dal menu di sinistra di Azure AD.

3. Nel pannello **Proprietà** cercare **Gli amministratori globali possono gestire le sottoscrizioni di Azure**, scegliere **Sì**, quindi **Salva**.
    > [!IMPORTANT] 
    > Se si sceglie **Sì**, si assegna il ruolo **Amministratore Accesso utenti** a livello di radice "/" (ambito radice) all'utente con cui è stata effettuata la connessione al portale. **Ciò consente all'utente di visualizzare tutte le altre sottoscrizioni di Azure.**
    
    > [!NOTE] 
    > Se si sceglie **No**, si rimuove il ruolo **Amministratore Accesso utente** a livello di radice "/" (ambito radice) dall'utente con cui è stata effettuata la connessione al portale.

> [!TIP] 
> Questa funzionalità può dare l'impressione di essere una proprietà globale per Azure Active Directory, ma in realtà funziona per utente con riferimento all'utente connesso in quel momento. Gli utenti con diritti di amministratore globale in Azure Active Directory possono chiamare la funzionalità elevateAccess per l'utente con cui è stata effettuata la connessione all'interfaccia di amministrazione di Azure Active Directory.

![Screenshot dell'interfaccia di amministrazione di Azure AD - Proprietà - Gli amministratori globali possono gestire le sottoscrizioni di Azure](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>Visualizzare le assegnazioni di ruolo nell'ambito "/" tramite PowerShell
Per visualizzare l'assegnazione **Amministratore Accesso utenti** nell'ambito  **/** , usare il cmdlet PowerShell `Get-AzureRmRoleAssignment`.
    
```
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**Output di esempio**:

RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>Eliminare l'assegnazione di ruolo nell'ambito "/" tramite PowerShell:
È possibile eliminare l'assegnazione tramite il cmdlet PowerShell seguente:
```
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>Usare elevateAccess per concedere l'accesso ai tenant con l'API REST

Il processo di base funziona con i passaggi seguenti:

1. Usando REST, chiamare *elevateAccess*, che concede all'utente il ruolo di Amministratore Accesso utenti nell'ambito "/".

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. Creare un'[assegnazione di ruolo](/rest/api/authorization/roleassignments) per assegnare un ruolo in qualsiasi ambito. Nell'esempio seguente vengono illustrate le proprietà per l'assegnazione del ruolo di lettore nell'ambito "/":

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. Come Amministratore Accesso utenti, è possibile inoltre eliminare le assegnazioni di ruolo nell'ambito "/".

4. Revocare i privilegi di Amministratore Accesso utenti fino a quando non saranno di nuovo necessari.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Come annullare l'azione elevateAccess con l'API REST

Quando si chiama *elevateAccess* si crea un'assegnazione di ruolo per se stessi in modo da revocare i privilegi necessari per eliminare l'assegnazione.

1.  Chiamare le definizioni di ruolo GET dove roleName = Amministratore Accesso utenti per determinare il GUID del nome del ruolo Amministratore Accesso utenti.
    1.  GET *https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator*

        ```
        {"value":[{"properties":{
        "roleName":"User Access Administrator",
        "type":"BuiltInRole",
        "description":"Lets you manage user access to Azure resources.",
        "assignableScopes":["/"],
        "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
        "createdOn":"0001-01-01T08:00:00.0000000Z",
        "updatedOn":"2016-05-31T23:14:04.6964687Z",
        "createdBy":null,
        "updatedBy":null},
        "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "type":"Microsoft.Authorization/roleDefinitions",
        "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
        "nextLink":null}
        ```

        Salvare il GUID dal parametro *nome*, in questo caso **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**.

2. È inoltre necessario elencare l'assegnazione di ruolo per l'amministratore del tenant nell'ambito del tenant. Elencare tutte le assegnazioni nell'ambito del tenant per PrincipalId di TenantAdmin che ha effettuato la chiamata per l'accesso con privilegi elevati. Sono incluse tutte le assegnazioni nel tenant per il parametro ObjectID. 
    1. GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'*
    
        >[!NOTE] 
        >Un amministratore di tenant non deve avere molte assegnazioni. Se la query precedente restituisce troppi assegnazioni, è anche possibile eseguire una query su tutte le assegnazioni solo a livello di ambito di tenant, quindi filtrare i risultati: GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()*
        
    2. Le suddette chiamate restituiscono un elenco di assegnazioni di ruolo. Individuare l'assegnazione di ruolo in cui l'ambito è "/" e RoleDefinitionId termina con il GUID del nome del ruolo presente nel passaggio 1 e PrincipalId corrisponde a ObjectId dell'amministratore del tenant. L'assegnazione del ruolo dovrebbe risultare simile alla seguente:

        ```
        {"value":[{"properties":{
        "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "principalId":"{objectID}",
        "scope":"/",
        "createdOn":"2016-08-17T19:21:16.3422480Z",
        "updatedOn":"2016-08-17T19:21:16.3422480Z",
        "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
        "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
        "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
        "type":"Microsoft.Authorization/roleAssignments",
        "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
        "nextLink":null}
        ```
        
        Di nuovo, salvare il GUID dal parametro *nome*, in questo caso **e7dd75bc-06f6-4e71-9014-ee96a929d099**.

    3. Usare quindi l'**ID RoleAssignment** evidenziato per eliminare l'assegnazione aggiunta da elevateAccess:

        DELETE https://management.azure.com /providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sulla [gestione del controllo degli accessi in base al ruolo con REST](role-based-access-control-manage-access-rest.md)

- [Gestire le assegnazioni di accesso](role-based-access-control-manage-assignments.md) nel Portale di Azure
