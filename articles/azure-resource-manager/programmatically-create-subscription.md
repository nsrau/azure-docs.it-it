---
title: Creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come creare sottoscrizioni di Azure Enterprise o Sviluppo/test Enterprise aggiuntive a livello di programmazione.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: f55f878d53b3813ea2ff2510998d47820de76a6a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Creare sottoscrizioni di Azure Enterprise a livello di programmazione (anteprima)

In qualità di cliente di Azure in [Enterprise Agreement, EA](https://azure.microsoft.com/pricing/enterprise-agreement/), l'utente può creare sottoscrizioni di EA (MS-AZR-0017P) e di Sviluppo/test EA (MS-AZR-0148P) a livello di programmazione. Per concedere a un altro utente o a un'altra entità servizio l'autorizzazione per creare sottoscrizioni fatturate al proprio account, concedere loro l'accesso [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) all'account di registrazione. 

> [!IMPORTANT]
> Le sottoscrizioni di Azure create tramite questa API sono regolamentate dall'accordo in base al quale l'utente ha ottenuto i servizi di Microsoft Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni Legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

Contenuto dell'articolo:

> [!div class="checklist"]
> * Informazioni su come creare sottoscrizioni a livello di programmazione usando Azure Resource Manager
> * Imparare a usare il Controllo degli accessi in base al ruolo per condividere la possibilità di creare sottoscrizioni fatturate al proprio account EA

Consultare anche il [codice di esempio .NET su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Chiedere all'amministratore della registrazione EA di essere aggiunto come proprietario dell'Account

Per iniziare, chiedere all'amministratore della registrazione di [essere aggiunto come proprietario dell'account mediante il portale EA](https://ea.azure.com/helpdocs/addNewAccount) (accesso richiesto). Seguire le istruzioni nel messaggio di posta elettronica di invito ricevuto per creare manualmente una sottoscrizione iniziale.

> [!IMPORTANT]
> È necessario confermare di essere proprietario dell'account e creare manualmente una sottoscrizione EA iniziale prima di procedere al passaggio successivo. La semplice aggiunta dell'account alla registrazione non è sufficiente.

## <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo essere stato aggiunto a una registrazione di Azure EA come proprietario dell'account, Azure usa la relazione account-registrazione per determinare il destinatario della fattura per i costi di sottoscrizione. Per creare sottoscrizioni, bisogna innanzitutto sapere a quali account di registrazione si ha accesso. Se al momento si è proprietario dell'account EA e si vuole usare questa API, Azure controlla le condizioni seguenti:

- L'account è stato aggiunto a una registrazione EA
- L'utente dispone di una o più sottoscrizioni EA o Sviluppo/test EA, vale a dire che l'utente ha eseguito almeno una registrazione manuale
- È stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui vengono create le sottoscrizioni per impostazione predefinita

Se vengono soddisfatte le tre condizioni precedenti, viene restituita una risorsa `enrollmentAccount` ed è possibile iniziare a creare le sottoscrizioni con tale account. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione EA in cui si trova l'account.

# <a name="resttabrest"></a>[REST](#tab/rest)

Richiesta dell'elenco di tutti gli account di registrazione:

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Usare il [comando Get-AzureRmEnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure risponde con un elenco degli ID oggetto e degli indirizzi di posta elettronica dell'account.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurecli-interactive 
az billing enrollment-account list
```
Azure risponde con un elenco degli ID oggetto e degli indirizzi di posta elettronica dell'account.

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
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Usare `id` come valore di `enrollmentAccount` che verrà usato per creare la sottoscrizione nel passaggio successivo.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico 

L'esempio seguente crea una richiesta per creare la sottoscrizione denominata *Sottoscrizione team Dev* e l'offerta di sottoscrizione è *MS-AZR-0017P* (EA regolare). L'account di registrazione è `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (valore segnaposto, si tratta di un GUID), ovvero l'account di registrazione per SignUpEngineering@contoso.com. Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

# <a name="resttabrest"></a>[REST](#tab/rest)

Usare `id` di `enrollmentAccount` nel percorso della richiesta per creare la sottoscrizione.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome dell'elemento  | Obbligatoria | type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per usare questo modulo di anteprima, installarlo eseguendo prima `Install-Module AzureRM.Subscription -AllowPrerelease`. Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/gallery/psget/get_psget_module).

Usare [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) con l'ID oggetto `enrollmentAccount` come parametro `EnrollmentAccountObjectId` per creare una nuova sottoscrizione. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatoria | type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Si tratta di un valore GUID che si ottiene da `Get-AzureRmEnrollmentAccount`. |
| `OwnerObjectId`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No        | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No        | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.| 

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare questa estensione di anteprima, installarla eseguendo prima `az extension add --name subscription`.

Usare [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) con l'ID oggetto `enrollmentAccount` come parametro `enrollment-account-object-id` per creare una nuova sottoscrizione.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatoria | type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Si tratta di un valore GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No        | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | No        | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.| 

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Delegare l'accesso a un account di registrazione con Controllo degli accessi in base al ruolo

Per consentire a un altro utente o un'altra entità servizio di creare le sottoscrizioni per un account specifico, [assegnare loro un ruolo di proprietario con Controllo degli accessi in base al ruolo nell'ambito dell'account registrazione](../active-directory/role-based-access-control-manage-access-rest.md). L'esempio seguente concede a un utente nel tenant con `principalId` di `<userObjectId>` (per SignUpEngineering@contoso.com) un ruolo di proprietario nell'account di registrazione. 

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

Usare [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) per concedere a un altro utente l'accesso come proprietario all'account di registrazione personale.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 50 sottoscrizioni per l'account. Successivamente, è possibile creare le sottoscrizioni solo tramite il Centro account.
- Nell'account deve essere presente almeno una sottoscrizione EA o Sviluppo/Test EA, il che vuol dire che il proprietario dell'account ha eseguito almeno una registrazione manuale.
- Gli utenti che non sono proprietari dell'account, ma sono stati aggiunti a un account di registrazione tramite il Controllo degli accessi in base al ruolo, non possono creare sottoscrizioni mediante il Centro account.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Per altre informazioni su Azure Resource Manager e le relative API, vedere [Panoramica di Azure Resource Manager](resource-group-overview.md).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md).
* Per una guida completa alle procedure consigliate per organizzazioni di grandi dimensioni sulla governance delle sottoscrizioni, vedere [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](resource-manager-subscription-governance.md)
