---
title: Concessione dell'accesso per la creazione di sottoscrizioni Azure Enterprise
description: Informazioni su come fornire a un utente o un'entità servizio la capacità di creare sottoscrizioni di Azure Enterprise a livello di codice.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 8f1f7837b6243f1af49d3b5eb0f3632c5e144f6c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149836"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concedere l'accesso per creare sottoscrizioni di Azure Enterprise (anteprima)

Come cliente di Azure, in [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), è possibile fornire a un altro utente o entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account. Questo articolo descrive come usare il [controllo degli accessi in base al ruolo (RBAC)](../active-directory/role-based-access-control-configure.md) per condividere la capacità di creare sottoscrizioni e come controllare le creazioni di sottoscrizioni. È necessario disporre di un ruolo Proprietario per l'account da condividere.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Concedere l'accesso

Per [creare sottoscrizioni in un account di registrazione](programmatically-create-subscription.md), è necessario che gli utenti dispongano del [ruolo di proprietario RBAC](../role-based-access-control/built-in-roles.md#owner) per tale account. È possibile concedere a un utente o a un gruppo di utenti il ruolo di proprietario RBAC in un account di registrazione attenendosi alla procedura seguente:

1. Ottenere l'ID oggetto dell'account di registrazione a cui si vuole concedere l'accesso

    Per concedere ad altri utenti il ruolo di proprietario RBAC per un account di registrazione, è necessario essere il proprietario dell'account o un proprietario RBAC dell'account.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Richiedere l'elenco di tutti gli account di registrazione a cui si ha accesso:

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

    Utilizzare la proprietà `principalName` per identificare l'account a cui si desidera concedere l'accesso al proprietario RBAC. Copiare il `name` di tale account. Ad esempio, se si vuole concedere l'accesso del proprietario RBAC al SignUpEngineering@contoso.com account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **prova** per aprire [Azure cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse sulle finestre della shell e selezionare **Incolla**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure risponde con un elenco di account di registrazione a cui si ha accesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Utilizzare la proprietà `principalName` per identificare l'account a cui si desidera concedere l'accesso al proprietario RBAC. Copiare il `ObjectId` di tale account. Ad esempio, se si vuole concedere l'accesso del proprietario RBAC al SignUpEngineering@contoso.com account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare questo ID oggetto in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **prova** per aprire [Azure cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse sulle finestre della shell e selezionare **Incolla**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure risponde con un elenco di account di registrazione a cui si ha accesso:

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

    Utilizzare la proprietà `principalName` per identificare l'account a cui si desidera concedere l'accesso al proprietario RBAC. Copiare il `name` di tale account. Ad esempio, se si vuole concedere l'accesso del proprietario RBAC al SignUpEngineering@contoso.com account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Ottenere l'ID oggetto dell'utente o del gruppo a cui si vuole assegnare il ruolo di proprietario RBAC

    1. Nella portale di Azure cercare **Azure Active Directory**.
    1. Se si desidera concedere l'accesso utente, fare clic su **utenti** nel menu a sinistra. Se si desidera concedere l'accesso a un gruppo, fare clic su **gruppi**.
    1. Selezionare l'utente o il gruppo a cui si desidera assegnare il ruolo di proprietario RBAC.
    1. Se è stato selezionato un utente, l'ID oggetto verrà trovato nella pagina del profilo. Se è stato selezionato un gruppo, l'ID oggetto sarà presente nella pagina panoramica. Copiare il valore **ObjectID** facendo clic sull'icona a destra della casella di testo. Incollarlo in un punto in modo che sia possibile usarlo nel passaggio successivo come `userObjectId`.

1. Concedere all'utente o al gruppo il ruolo di proprietario RBAC nell'account di registrazione

    Usando i valori raccolti nei primi due passaggi, concedere all'utente o al gruppo il ruolo di proprietario RBAC nell'account di registrazione.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Eseguire il comando seguente, sostituendo ```<enrollmentAccountObjectId>``` con il `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto copiato dal secondo passaggio.

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

    Eseguire il comando [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) seguente, sostituendo ```<enrollmentAccountObjectId>``` con il `ObjectId` raccolto nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli-2)

    Eseguire il comando [AZ Role Assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) seguente, sostituendo ```<enrollmentAccountObjectId>``` con il `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Una volta che un utente diventa un proprietario RBAC per l'account di registrazione, può [creare sottoscrizioni a livello di codice](programmatically-create-subscription.md) . Una sottoscrizione creata da un utente delegato ha ancora il proprietario dell'account originale come amministratore del servizio, ma ha anche l'utente delegato come proprietario del controllo degli accessi in base al ruolo per impostazione predefinita.

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
* Per ulteriori informazioni sulla gestione di numeri elevati di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption-guide/subscription-governance)
