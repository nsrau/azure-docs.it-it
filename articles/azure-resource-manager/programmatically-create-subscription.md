---
title: Creare sottoscrizioni di Azure a livello di codice
description: Informazioni su come creare sottoscrizioni di Azure aggiuntive a livello di codice.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 757a542c8583f6a2b3f73e8144b6281438d75ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273593"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Creare sottoscrizioni di Azure a livello di codice (anteprima)

I clienti di Azure con un account di fatturazione [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) o [Microsoft Partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) possono creare sottoscrizioni a livello di programmazione. Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

Quando si crea una sottoscrizione di Azure a livello di codice, tale sottoscrizione è disciplinata dal contratto al di sotto del quale sono stati ottenuti i servizi di Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni Legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Creare sottoscrizioni per un account di fatturazione EA

### <a name="prerequisites"></a>prerequisiti

Per creare una sottoscrizione, è necessario disporre di un ruolo proprietario per un account di registrazione. Esistono due modi per ottenere il ruolo:

* L'amministratore dell'organizzazione della registrazione può [creare un proprietario dell'account](https://ea.azure.com/helpdocs/addNewAccount) (accesso richiesto) che rende un proprietario dell'account di registrazione.

* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](grant-access-to-create-subscription.md). Analogamente, se si desidera utilizzare un'entità servizio per creare una sottoscrizione EA, è necessario [concedere a tale entità servizio la possibilità di creare sottoscrizioni](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo l'aggiunta a un account di registrazione associato a un proprietario dell'account, Azure usa la relazione account-registrazione per determinare dove addebitare gli addebiti per la sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione EA in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione. 

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

### <a name="resttabrest"></a>[REST](#tab/rest)

Richiedere l'elenco di tutti gli account di registrazione a cui si ha accesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

La risposta API elenca tutti gli account di registrazione a cui si ha accesso:

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il `name` di tale account. Se, ad esempio, si desidera creare sottoscrizioni con l'account SignUpEngineering@contoso.com registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Aprire [Azure cloud Shell](https://shell.azure.com/) e selezionare PowerShell.

Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure risponde con un elenco di account di registrazione a cui si ha accesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il `ObjectId` di tale account. Se, ad esempio, si desidera creare sottoscrizioni con l'account SignUpEngineering@contoso.com registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare questo ID oggetto in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso.

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il `name` di tale account. Se, ad esempio, si desidera creare sottoscrizioni con l'account SignUpEngineering@contoso.com registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

L'esempio seguente crea una sottoscrizione denominata *dev team Subscription* nell'account di registrazione selezionato nel passaggio precedente. L'offerta di sottoscrizione è *MS-AZR-0017P* (regular Microsoft Enterprise Agreement). Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

### <a name="resttabrest"></a>[REST](#tab/rest)

Eseguire la richiesta seguente, sostituendo `<enrollmentAccountObjectId>` con l'`name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Nome dell'elemento  | obbligatori | digitare   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installare innanzitutto questo modulo di anteprima eseguendo `Install-Module Az.Subscription -AllowPrerelease`. Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Eseguire il comando [New-AzSubscription](/powershell/module/az.subscription) seguente, sostituendo `<enrollmentAccountObjectId>` con il `ObjectId` raccolto nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | obbligatori | digitare   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per prima cosa, installare questa estensione di anteprima eseguendo `az extension add --name subscription`.

Eseguire il comando [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) sotto, sostituendo `<enrollmentAccountObjectId>` con il `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | obbligatori | digitare   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | No       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 200 sottoscrizioni per ogni account di registrazione. Successivamente, è possibile creare più sottoscrizioni per l'account solo nella portale di Azure. Se si desidera creare più sottoscrizioni tramite l'API, creare un altro account di registrazione.
- Gli utenti che non sono proprietari dell'account, ma sono stati aggiunti a un account di registrazione tramite RBAC, non possono creare sottoscrizioni nella portale di Azure.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Creare sottoscrizioni per un account MCA

### <a name="prerequisites"></a>prerequisiti

Per creare sottoscrizioni, è necessario disporre di un ruolo proprietario, collaboratore o creatore della sottoscrizione di Azure per una sezione della fattura o un ruolo di proprietario o collaboratore in un profilo di fatturazione o un account di fatturazione. Per altre informazioni, vedere [Ruoli e attività di fatturazione della sottoscrizione](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

L'esempio illustrato di seguito usa le API REST. Attualmente, PowerShell e l'interfaccia della riga di comando di Azure non sono supportati.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si ha accesso 

Eseguire la richiesta seguente per elencare tutti gli account di fatturazione.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
La risposta API elenca gli account di fatturazione a cui si ha accesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Utilizzare la proprietà `displayName` per identificare l'account di fatturazione per il quale si desidera creare le sottoscrizioni. Verificare che il agreeementType dell'account sia *MicrosoftCustomerAgreement*. Copiare il `name` dell'account.  Se ad esempio si vuole creare una sottoscrizione per l'account `Contoso` Billing, copiare `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Trovare le sezioni della fattura per creare le sottoscrizioni

Gli addebiti per la sottoscrizione vengono visualizzati in una sezione della fattura di un profilo di fatturazione. Usare l'API seguente per ottenere l'elenco di sezioni della fattura e i profili di fatturazione per i quali si dispone dell'autorizzazione per la creazione di sottoscrizioni di Azure.

Eseguire la richiesta seguente, sostituendo `<billingAccountName>` con l'`name` copiato nel primo passaggio (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
La risposta API elenca tutte le sezioni della fattura e i relativi profili di fatturazione ai quali è possibile accedere per creare sottoscrizioni:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

Utilizzare la proprietà `invoiceSectionDisplayName` per identificare la sezione della fattura per la quale si desidera creare le sottoscrizioni. Copiare la `invoiceSectionId`, `billingProfileId` e una delle `skuId` per la sezione relativa alla fattura. Se, ad esempio, si desidera creare una sottoscrizione di tipo `Microsoft Azure plan` per `Development` sezione fattura, è necessario copiare `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` e `0001`. Incollare questi valori in un punto qualsiasi, in modo da poterli usare nel passaggio successivo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Creare una sottoscrizione per una sezione di fattura

Nell'esempio seguente viene creata una sottoscrizione denominata *dev team Subscription* di tipo *Microsoft Azure piano* per la sezione relativa alla fattura *dello sviluppo* . La sottoscrizione verrà fatturata al profilo di fatturazione di *Contoso Finance* e verrà visualizzata nella sezione *sviluppo* della relativa fattura. 

Eseguire la richiesta seguente, sostituendo `<invoiceSectionId>` con il `invoiceSectionId` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). È necessario passare il `billingProfileId` e `skuId` copiato dal secondo passaggio nei parametri della richiesta dell'API. Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nome dell'elemento  | obbligatori | digitare   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | String | Nome visualizzato della sottoscrizione|
| `billingProfileId`   | Sì      | String | ID del profilo di fatturazione che verrà fatturato per gli addebiti per la sottoscrizione.  |
| `skuId` | Sì      | String | ID SKU che determina il tipo di piano di Azure. |
| `owners`      | No       | String | ID oggetto di qualsiasi utente o entità servizio che si desidera aggiungere come proprietario RBAC nella sottoscrizione al momento della creazione.  |
| `costCenter` | No      | String | Centro di costo associato alla sottoscrizione. Viene visualizzato nel file CSV Usage. |
| `managementGroupId` | No      | String | ID del gruppo di gestione a cui verrà aggiunta la sottoscrizione. Per ottenere l'elenco dei gruppi di gestione, vedere [API gruppi di gestione-List](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Usare l'ID di un gruppo di gestione dall'API. |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Creare sottoscrizioni per un account di fatturazione MPA

### <a name="prerequisites"></a>prerequisiti

Per creare una sottoscrizione per l'account di fatturazione, è necessario disporre di un ruolo di amministratore globale o di amministratore nell'account del provider di soluzioni cloud dell'organizzazione. Per altre informazioni, vedere [centro per i partner-assegnare ruoli e autorizzazioni per gli utenti](https://docs.microsoft.com/partner-center/permissions-overview).

L'esempio illustrato di seguito usa le API REST. Attualmente, PowerShell e l'interfaccia della riga di comando di Azure non sono supportati.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si ha accesso 

Eseguire la richiesta seguente per elencare tutti gli account di fatturazione a cui si ha accesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
La risposta API elenca gli account di fatturazione.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Utilizzare la proprietà `displayName` per identificare l'account di fatturazione per il quale si desidera creare le sottoscrizioni. Verificare che il agreeementType dell'account sia *MicrosoftPartnerAgreement*. Copiare il `name` per l'account. Se ad esempio si vuole creare una sottoscrizione per l'account `Contoso` Billing, copiare `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-customers-that-have-azure-plans"></a>Trova i clienti con piani di Azure

Eseguire la richiesta seguente, sostituendo `<billingAccountName>` con il `name` copiato dal primo passaggio (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) per elencare tutti i clienti nell'account di fatturazione per cui è possibile creare sottoscrizioni di Azure. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
La risposta API elenca i clienti nell'account di fatturazione con i piani di Azure. È possibile creare sottoscrizioni per questi clienti.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

Utilizzare la proprietà `displayName` per identificare il cliente per il quale si desidera creare le sottoscrizioni. Copiare il `id` per il cliente. Se ad esempio si vuole creare una sottoscrizione per `Fabrikam toys`, copiare `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Incollare questo valore in un punto qualsiasi per utilizzarlo nei passaggi successivi.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Facoltativo per i provider indiretti: ottenere i rivenditori per un cliente

Se si è un provider indiretto nel modello a due livelli CSP, è possibile specificare un rivenditore durante la creazione delle sottoscrizioni per i clienti. 

Eseguire la richiesta seguente, sostituendo `<customerId>` con il `id` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) per elencare tutti i rivenditori disponibili per un cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
La risposta API elenca i rivenditori per il cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Utilizzare la proprietà `description` per identificare il rivenditore che verrà associato alla sottoscrizione. Copiare il `resellerId` per il rivenditore. Se ad esempio si desidera associare `Wingtip`, copiare `3xxxxx`. Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="create-a-subscription-for-a-customer"></a>Creare una sottoscrizione per un cliente

Nell'esempio seguente viene creata una sottoscrizione denominata *dev team Subscription* per *Fabrikam Toys* e viene associato *Wingtip* Reseller alla sottoscrizione. T

Eseguire la richiesta seguente, sostituendo `<customerId>` con il `id` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passare il *resellerId* facoltativo copiato dal secondo passaggio nei parametri della richiesta dell'API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome dell'elemento  | obbligatori | digitare   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | String | Nome visualizzato della sottoscrizione|
| `skuId` | Sì      | String | ID SKU del piano di Azure. Usare *0001* per sottoscrizioni di tipo Microsoft Azure piano |
| `resellerId`      | No       | String | ID MPN del rivenditore che verrà associato alla sottoscrizione.  |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio di creazione di una sottoscrizione di Enterprise Agreement (EA) con .NET, vedere [il codice di esempio su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per ulteriori informazioni sulla gestione di numeri elevati di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
