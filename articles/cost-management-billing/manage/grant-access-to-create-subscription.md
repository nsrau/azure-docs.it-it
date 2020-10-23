---
title: Concedere l'accesso per creare sottoscrizioni di Azure Enterprise
description: Informazioni su come fornire a un utente o un'entità servizio la capacità di creare sottoscrizioni di Azure Enterprise a livello di codice.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: 02919cf2e35fdd4d981f3fde53085c2174f11bd1
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132568"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concedere l'accesso per creare sottoscrizioni di Azure Enterprise (anteprima)

Come cliente di Azure, in [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), è possibile fornire a un altro utente o entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account. Questo articolo descrive come usare il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per condividere la capacità di creare sottoscrizioni e come controllare le creazioni di sottoscrizioni. È necessario disporre di un ruolo Proprietario per l'account da condividere.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Concedere l'accesso

Per [creare sottoscrizioni in un account di registrazione](programmatically-create-subscription.md), gli utenti devono avere il [ruolo Proprietario](../../role-based-access-control/built-in-roles.md#owner) di Controllo degli accessi in base al ruolo di Azure in tale account. È possibile concedere a un utente o a un gruppo di utenti il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure in un account di registrazione seguendo questa procedura:

1. Ottenere l'ID oggetto dell'account di registrazione a cui si vuole concedere l'accesso

    Per concedere ad altri utenti il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure in un account di registrazione, è necessario essere il Proprietario dell'account o un Proprietario di Controllo degli accessi in base al ruolo di Azure dell'account.

    # <a name="rest"></a>[REST](#tab/rest)

    Richiesta per ottenere l'elenco di tutti gli account di registrazione a cui si può accedere:

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

    Usare la proprietà `principalName` per identificare l'account per cui si vuole concedere al Proprietario di Controllo degli accessi in base al ruolo di Azure. Copiare il valore `name` di tale account. Se, ad esempio, si vuole concedere al Proprietario di Controllo degli accessi in base al ruolo di Azure l'accesso all'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. È l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Prova** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse nelle finestre della shell e quindi selezionare **Incolla**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure risponde con un elenco degli account di registrazione a cui si ha accesso:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Usare la proprietà `principalName` per identificare l'account per cui si vuole concedere l'accesso al Proprietario di Controllo degli accessi in base al ruolo di Azure. Copiare il valore `ObjectId` di tale account. Se, ad esempio, si vuole concedere al Proprietario di Controllo degli accessi in base al ruolo di Azure l'accesso all'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare l'ID oggetto da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    Usare il comando [az billing enrollment-account list](/cli/azure/billing) per elencare tutti gli account di registrazione a cui si ha accesso. Selezionare **Prova** per aprire [Azure Cloud Shell](https://shell.azure.com/). Per incollare il codice, fare clic con il pulsante destro del mouse nelle finestre della shell e quindi selezionare **Incolla**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure risponde con un elenco degli account di registrazione a cui si ha accesso:

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

    Usare la proprietà `principalName` per identificare l'account per cui si vuole concedere l'accesso al Proprietario di Controllo degli accessi in base al ruolo di Azure. Copiare il valore `name` di tale account. Se, ad esempio, si vuole concedere al Proprietario di Controllo degli accessi in base al ruolo di Azure l'accesso all'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. È l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Ottenere l'ID oggetto dell'utente o gruppo a cui si vuole assegnare il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure

    1. Nel portale di Azure cercare **Azure Active Directory**.
    1. Se si vuole concedere l'accesso a un utente, scegliere **Utenti** dal menu a sinistra. Per concedere l'accesso a un gruppo, selezionare **Gruppi**.
    1. Selezionare l'utente o il gruppo a cui si vuole assegnare il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure.
    1. Se è stato selezionato un utente, l'ID oggetto sarà disponibile nella pagina Profilo. Se è stato selezionato un gruppo, l'ID oggetto sarà disponibile nella pagina Panoramica. Copiare il valore **ObjectID** selezionando l'icona alla destra della casella di testo. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo come `userObjectId`.

1. Concedere all'utente o al gruppo il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure nell'account di registrazione

    Usando i valori raccolti nei primi due passaggi, concedere all'utente o al gruppo il ruolo Proprietario di Controllo degli accessi in base al ruolo di Azure nell'account di registrazione.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Eseguire questo comando, sostituendo ```<enrollmentAccountObjectId>``` con il valore `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto copiato dal secondo passaggio.

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Eseguire questo comando [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md), sostituendo ```<enrollmentAccountObjectId>``` con il valore the `ObjectId` raccolto nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli-2)

    Eseguire questo comando [az role assignment create](../../role-based-access-control/role-assignments-cli.md), sostituendo ```<enrollmentAccountObjectId>``` con il valore `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Sostituire ```<userObjectId>``` con l'ID oggetto raccolto nel secondo passaggio.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Quando un utente diventa Proprietario di Controllo degli accessi in base al ruolo di Azure per l'account di registrazione, può [creare sottoscrizioni a livello di codice](programmatically-create-subscription.md) in tale account. In una sottoscrizione creata da un utente delegato il proprietario dell'account originale è ancora amministratore del servizio, ma anche l'utente delegato è un Proprietario di Controllo degli accessi in base al ruolo di Azure per impostazione predefinita.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controllo dei creatori delle sottoscrizioni mediante i log attività

Per monitorare le sottoscrizioni create tramite questa API, usare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs). Attualmente non è possibile usare PowerShell, l'interfaccia della riga di comando o il portale di Azure per monitorare la creazione delle sottoscrizioni.

1. Come amministratore del tenant di Azure AD [elevare l'accesso](../../role-based-access-control/elevate-access-global-admin.md) quindi assegnare un ruolo di lettore all'utente di controllo sull'ambito `/providers/microsoft.insights/eventtypes/management`. Questo accesso è disponibile nel [Ruolo con autorizzazioni di lettura](../../role-based-access-control/built-in-roles.md#reader), nel ruolo [Collaboratore per il monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) o in un [ruolo personalizzato](../../role-based-access-control/custom-roles.md).
1. Come utente di controllo, chiamare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs) per visualizzare le attività di creazione della sottoscrizione. Esempio:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Per chiamare correttamente questa API dalla riga di comando, provare [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passaggi successivi

* Ora che l'utente o l'entità servizio dispone delle autorizzazioni necessarie per creare una sottoscrizione, è possibile usare tale identità per [creare sottoscrizioni di Azure Enterprise a livello di codice](programmatically-create-subscription.md).
* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Per altre informazioni su Azure Resource Manager e le relative API, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](../../governance/management-groups/overview.md)
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption-guide/subscription-governance)