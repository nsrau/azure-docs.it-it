---
title: Accesso con privilegi elevati per l&quot;amministratore tenat - Azure AD | Microsoft Docs
description: Questo argomento descrive i ruoli predefiniti per il controllo degli accessi in base al ruolo.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: b9eb9c7f59d0936364bad788ed3b6635b2453739
ms.openlocfilehash: 9bcad7aaf7f1bd8c51dbfa88381276a70a4def5c


---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>Accesso con privilegi elevati come amministratore tenant con il Controllo degli accessi in base al ruolo

Il Controllo degli accessi in base al ruolo consente agli amministratori tenant di ottenere i privilegi elevati temporanei per l'accesso in modo da poter concedere autorizzazioni superiori rispetto al normale. Un amministratore tenant può elevare se stesso al ruolo di Amministratore Accesso utenti quando necessario. Tale ruolo offre all'amministratore tenant le autorizzazioni per concedere a se stesso o ad altri i ruoli nell'ambito del "/".

Questa funzionalità è importante perché consente all'amministratore tenant di visualizzare tutte le sottoscrizioni presenti in un'organizzazione. Consente inoltre alle app di automazione (ad esempio la fatturazione e il controllo) di accedere a tutte le sottoscrizioni e offrire una visualizzazione accurata dello stato dell'organizzazione da una prospettiva di gestione della fatturazione o delle risorse.  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>Procedura: usare elevateAccess per concedere l'accesso ai tenant

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


## <a name="how-to-undo-the-elevateaccess-action"></a>Procedura per annullare l'azione elevateAccess

Quando si chiama *elevateAccess* si crea un'assegnazione di ruolo per se stessi in modo da revocare i privilegi necessari per eliminare l'assegnazione.

1.  Chiamare [roleDefinition GET](/rest/api/authorization/roledefinitions#RoleDefinitions_Get) dove roleName = Amministratore Accesso utenti per determinare il nome GUID del ruolo Amministratore Accesso utenti. La risposta dovrebbe avere l'aspetto seguente:

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

2. Chiamare [roleAssignments GET](/rest/api/authorization/roleassignments#RoleAssignments_Get) dove principalId = il proprio ObjectId. Questo elenca tutte le assegnazioni nel tenant. Cercare quello in cui l'ambito è "/" e il RoleDefinitionId termina con il nome del ruolo GUID individuato nel passaggio 1. L'assegnazione del ruolo dovrebbe risultare simile al seguente:

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

3. Infine, chiamare [roleAssignments DELETE](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById) dove roleAssignmentId = il nome GUID è stato individuato nel passaggio 2.

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sulla [gestione del controllo degli accessi in base al ruolo con REST](role-based-access-control-manage-access-rest.md)

- [Gestire le assegnazioni di accesso](role-based-access-control-manage-assignments.md) nel Portale di Azure



<!--HONumber=Feb17_HO2-->


