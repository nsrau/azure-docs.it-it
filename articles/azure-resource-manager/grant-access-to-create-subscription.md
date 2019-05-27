---
title: Concedere l'accesso per creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come fornire a un utente o un'entità servizio la capacità di creare sottoscrizioni di Azure Enterprise a livello di codice.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 81f3edcfcffad1b9b6d0ab4b49769601196049ad
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66179857"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concedere l'accesso per creare sottoscrizioni di Azure Enterprise (anteprima)

Come cliente di Azure, in [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), è possibile fornire a un altro utente o entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account. Questo articolo descrive come usare il [controllo degli accessi in base al ruolo (RBAC)](../active-directory/role-based-access-control-configure.md) per condividere la capacità di creare sottoscrizioni e come controllare le creazioni di sottoscrizioni. È necessario disporre di un ruolo Proprietario per l'account da condividere.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Concedi accesso

Per [creare le sottoscrizioni con un account di registrazione](programmatically-create-subscription.md), gli utenti devono avere la [ruolo RBAC proprietario](../role-based-access-control/built-in-roles.md#owner) su quell'account. È possibile concedere un utente o un gruppo di utenti il ruolo RBAC proprietario in un account di registrazione seguendo questa procedura:

1. Ottenere l'ID oggetto dell'account di registrazione che si desidera concedere l'accesso a

    Per concedere ad altri utenti il ruolo RBAC proprietario in un account di registrazione, è necessario essere il proprietario dell'Account o un proprietario di RBAC dell'account.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Richiesta di elenco di tutti gli account di registrazione che è possibile utilizzare:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure risponde con un elenco di tutti gli account di registrazione a cui si ha accesso:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Usare il `principalName` proprietà per identificare l'account che si desidera concedere l'accesso come proprietario di RBAC a. Copia il `name` di quell'account. Ad esempio, se si vuole concedere l'accesso come proprietario di RBAC per la SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte in modo che è possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Provalo** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare doppio clic su windows shell e quindi selezionare **incollare**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure risponde con un elenco di account di registrazione che è possibile utilizzare:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Usare il `principalName` proprietà per identificare l'account che si desidera concedere l'accesso come proprietario di RBAC a. Copia il `ObjectId` di quell'account. Ad esempio, se si vuole concedere l'accesso come proprietario di RBAC per la SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare l'ID dell'oggetto da qualche parte in modo che è possibile usarlo nel passaggio successivo come il `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Provalo** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare doppio clic su windows shell e quindi selezionare **incollare**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure risponde con un elenco di account di registrazione che è possibile utilizzare:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Usare il `principalName` proprietà per identificare l'account che si desidera concedere l'accesso come proprietario di RBAC a. Copia il `name` di quell'account. Ad esempio, se si vuole concedere l'accesso come proprietario di RBAC per la SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte in modo che è possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Ottenere l'ID oggetto dell'utente o gruppo che si desidera assegnare il ruolo RBAC proprietario

    1. Nel portale di Azure, cercare **Azure Active Directory**.
    1. Se si vuole concedere un accesso utente, fare clic su **utenti** nel menu a sinistra. Se si desidera concedere l'accesso a un gruppo, fare clic su **gruppi**.
    1. Selezionare l'utente o gruppo che si desidera assegnare il ruolo RBAC proprietario.
    1. Se si seleziona un utente, troverai l'ID oggetto nella pagina del profilo. Se si seleziona un gruppo, l'ID di oggetto sarà nella pagina di panoramica. Copia il **ObjectID** facendo clic sull'icona a destra della casella di testo. Incollare questa da qualche parte in modo che è possibile usarlo nel passaggio successivo come `userObjectId`.

1. Concedere all'utente o il ruolo RBAC proprietario nell'account di registrazione di gruppo

    Usando i valori che raccolti nei primi due passaggi, concedere all'utente o il ruolo RBAC proprietario nell'account di registrazione di gruppo.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Eseguire il comando seguente, sostituendo ```<enrollmentAccountObjectId>``` con il `name` copiati nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID di oggetto è stato copiato nel secondo passaggio.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Quando il ruolo di proprietario viene assegnato correttamente nell'ambito dell'account di registrazione, Azure risponde con le informazioni dell'assegnazione del ruolo:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Eseguire il comando seguente [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) comando, sostituendo ```<enrollmentAccountObjectId>``` con il `ObjectId` raccolti nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'oggetto ID raccolti nel secondo passaggio.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli-2)

    Eseguire il comando seguente [creazione dell'assegnazione di ruolo az](../active-directory/role-based-access-control-manage-access-azure-cli.md) comando, sostituendo ```<enrollmentAccountObjectId>``` con il `name` copiati nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'oggetto ID raccolti nel secondo passaggio.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Quando un utente diventa un proprietario di RBAC per l'account di registrazione, è possibile [a livello di codice crea le sottoscrizioni](programmatically-create-subscription.md) sotto di esso. Una sottoscrizione creata da un utente delegato è ancora il proprietario dell'Account amministratore del servizio originale, ma include anche l'utente delegato come proprietario RBAC per impostazione predefinita.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controllo dei creatori delle sottoscrizioni mediante i log attività

Per monitorare le sottoscrizioni create tramite questa API, usare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs). Attualmente non è possibile usare PowerShell, l'interfaccia della riga di comando o il portale di Azure per monitorare la creazione delle sottoscrizioni.

1. Come amministratore del tenant di Azure AD [elevare l'accesso](../active-directory/role-based-access-control-tenant-admin-access.md) quindi assegnare un ruolo di lettore all'utente di controllo sull'ambito `/providers/microsoft.insights/eventtypes/management`.
1. Come utente di controllo, chiamare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs) per visualizzare le attività di creazione della sottoscrizione. Esempio:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Per chiamare correttamente questa API dalla riga di comando, provare [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passaggi successivi

* Ora che l'utente o l'entità servizio dispone delle autorizzazioni necessarie per creare una sottoscrizione, è possibile usare tale identità per [creare sottoscrizioni di Azure Enterprise a livello di codice](programmatically-create-subscription.md).
* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Per altre informazioni su Azure Resource Manager e le relative API, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption-guide/subscription-governance)
