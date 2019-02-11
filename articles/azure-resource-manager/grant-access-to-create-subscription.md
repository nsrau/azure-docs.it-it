---
title: Concedere l'accesso per creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come fornire a un utente o un'entità servizio la capacità di creare sottoscrizioni di Azure Enterprise a livello di codice.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 3577edff19788ed9f0925876e3de737eb749b90e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490924"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Concedere l'accesso per creare sottoscrizioni di Azure Enterprise (anteprima)

Come cliente di Azure, in [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), è possibile fornire a un altro utente o entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account. Questo articolo descrive come usare il [controllo degli accessi in base al ruolo (RBAC)](../active-directory/role-based-access-control-configure.md) per condividere la capacità di creare sottoscrizioni e come controllare le creazioni di sottoscrizioni. È necessario disporre di un ruolo Proprietario per l'account da condividere.

Per creare una sottoscrizione, vedere [Creare sottoscrizioni di Azure Enterprise a livello di programmazione (anteprima)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegare l'accesso a un account di registrazione con Controllo degli accessi in base al ruolo

Per consentire a un altro utente o un'altra entità servizio di creare le sottoscrizioni per un account specifico, [assegnare loro un ruolo di proprietario con Controllo degli accessi in base al ruolo nell'ambito dell'account registrazione](../active-directory/role-based-access-control-manage-access-rest.md). L'esempio seguente concede a un utente nel tenant con `principalId` di `<userObjectId>` (per SignUpEngineering@contoso.com) un ruolo di proprietario nell'account di registrazione. Per trovare l'ID dell'account di registrazione e l'ID di entità, vedere [Creare sottoscrizioni di Azure Enterprise a livello di programmazione (anteprima)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Usare [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) per concedere a un altro utente l'accesso come proprietario all'account di registrazione personale.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) per concedere a un altro utente l'accesso come proprietario all'account di registrazione personale.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Quando un utente diventa proprietario con Controllo degli account in base al ruolo per l'account di registrazione, in esso può creare sottoscrizioni a livello di programmazione. In una sottoscrizione creata da un utente delegato il proprietario dell'account originale è ancora amministratore del servizio, ma anche l'utente delegato è un proprietario per impostazione predefinita. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controllo dei creatori delle sottoscrizioni mediante i log attività

Per monitorare le sottoscrizioni create tramite questa API, usare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs). Attualmente non è possibile usare PowerShell, l'interfaccia della riga di comando o il portale di Azure per monitorare la creazione delle sottoscrizioni.

1. Come amministratore del tenant di Azure AD [elevare l'accesso](../active-directory/role-based-access-control-tenant-admin-access.md) quindi assegnare un ruolo di lettore all'utente di controllo sull'ambito `/providers/microsoft.insights/eventtypes/management`.
1. Come utente di controllo, chiamare l'[API del log attività del tenant](/rest/api/monitor/tenantactivitylogs) per visualizzare le attività di creazione della sottoscrizione. Esempio:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Per chiamare correttamente questa API dalla riga di comando, provare [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Passaggi successivi

* Ora che l'utente o l'entità servizio dispone delle autorizzazioni necessarie per creare una sottoscrizione, è possibile usare tale identità per [creare sottoscrizioni di Azure Enterprise a livello di codice](programmatically-create-subscription.md).
* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Per altre informazioni su Azure Resource Manager e le relative API, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption-guide/subscription-governance)
