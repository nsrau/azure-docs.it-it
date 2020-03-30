---
title: Creare sottoscrizioni di Azure a livello di codice
description: Informazioni su come creare sottoscrizioni di Azure aggiuntive a livello di codice.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460399"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Creare sottoscrizioni di Azure a livello di codice (anteprima)Programmatically create Azure subscriptions (preview)

I clienti di Azure con un account di fatturazione [Con contratto Enterprise Agreement (EA),](https://azure.microsoft.com/pricing/enterprise-agreement/) [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) o Microsoft Partner Agreement [(MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) possono creare sottoscrizioni a livello di programmazione. Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

Quando si crea una sottoscrizione di Azure a livello di codice, tale sottoscrizione è regolata dal contratto in base al quale sono stati ottenuti i servizi di Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni Legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Creare sottoscrizioni per un account di fatturazione EA

Utilizzare le informazioni nelle sezioni seguenti per creare sottoscrizioni EA.

### <a name="prerequisites"></a>Prerequisiti

Per creare una sottoscrizione, è necessario disporre di un ruolo Proprietario in un account di registrazione. Esistono due modi per ottenere il ruolo:

* L'amministratore aziendale della registrazione può [impostare l'utente come proprietario dell'account](https://ea.azure.com/helpdocs/addNewAccount) (è necessario eseguire l'accesso) che rende l'utente proprietario dell'account di registrazione.

* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](grant-access-to-create-subscription.md). Analogamente, se si desidera utilizzare un'entità servizio per creare una sottoscrizione EA, è necessario concedere a [tale entità servizio la possibilità di creare sottoscrizioni.](grant-access-to-create-subscription.md)

### <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo essere stato aggiunto a un account di registrazione associato a un proprietario dell'account, Azure usa la relazione account-registrazione per determinare dove fatturare gli addebiti della sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione EA in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione.

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

### <a name="rest"></a>[REST](#tab/rest)

Richiesta di elencare tutti gli account di registrazione a cui si ha accesso:Request to list all enrollment accounts you have access to:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

La risposta API elenca tutti gli account di registrazione a cui si ha accesso:The API response lists all enrollment accounts you have access to:

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia `name` il tuo account. Ad esempio, se si desidera SignUpEngineering@contoso.com creare sottoscrizioni con l'account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in `enrollmentAccountObjectId`cui è possibile utilizzarlo nel passaggio successivo come .

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aprire Azure Cloud Shell e selezionare PowerShell.Open [Azure Cloud Shell](https://shell.azure.com/) and select PowerShell.

Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure risponde con un elenco di account di registrazione a cui si ha accesso:Azure responds with a list of enrollment accounts you have access to:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia `ObjectId` il tuo account. Ad esempio, se si desidera SignUpEngineering@contoso.com creare sottoscrizioni con l'account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare l'ID oggetto da qualche parte in `enrollmentAccountObjectId`modo che sia possibile utilizzarlo nel passaggio successivo come file .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurecli-interactive
az billing enrollment-account list
```

Azure risponde con un elenco di account di registrazione a cui si ha accesso:Azure responds with a list of enrollment accounts you have access to:

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia `name` il tuo account. Ad esempio, se si desidera SignUpEngineering@contoso.com creare sottoscrizioni con l'account di registrazione, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in `enrollmentAccountObjectId`cui è possibile utilizzarlo nel passaggio successivo come .

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

Nell'esempio seguente viene creata una sottoscrizione denominata *Dev Team Subscription* nell'account di registrazione selezionato nel passaggio precedente. L'offerta di abbonamento è *MS-A-R-0017P* (contratto Enterprise Microsoft regolare). Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

### <a name="rest"></a>[REST](#tab/rest)

Eseguire la richiesta seguente, sostituendo `<enrollmentAccountObjectId>` con l'`name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se si desidera specificare i proprietari, vedere [come ottenere gli ID oggetto utente.](grant-access-to-create-subscription.md#userObjectId)

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

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No       | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Innanzitutto, installare questo `Install-Module Az.Subscription -AllowPrerelease`modulo di anteprima eseguendo . Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Eseguire il comando [New-AzSubscription](/powershell/module/az.subscription) `<enrollmentAccountObjectId>` riportato `ObjectId` di seguito,```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```sostituendo con la raccolta nel primo passaggio ( ). Se si desidera specificare i proprietari, vedere [come ottenere gli ID oggetto utente.](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No       | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No       | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No       | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Innanzitutto, installare questa `az extension add --name subscription`estensione di anteprima eseguendo .

Eseguire il comando [az account](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) `<enrollmentAccountObjectId>` create `name` riportato di seguito,```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```sostituendo con l'oggetto copiato nel primo passaggio ( ). Se si desidera specificare i proprietari, vedere [come ottenere gli ID oggetto utente.](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No       | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No       | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | No       | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- Esiste un limite di 500 sottoscrizioni per account di registrazione. Successivamente, è possibile creare più sottoscrizioni per l'account solo nel portale di Azure.After that, more subscriptions for the account can only be created in the Azure portal. Se si desidera creare più sottoscrizioni tramite l'API, creare un altro account di registrazione.
- Gli utenti che non sono proprietari dell'account, ma sono stati aggiunti a un account di registrazione tramite il controllo degli accessi in base al ruolo, non possono creare sottoscrizioni nel portale di Azure.Users that aren't Account Owners, but were added to an enrollment account via RBAC, can't create subscriptions in the Azure portal.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Creare sottoscrizioni per un account MCA

### <a name="prerequisites"></a>Prerequisiti

Per creare sottoscrizioni, è necessario disporre di un ruolo di proprietario, collaboratore o creatore della sottoscrizione di Azure in una sezione della fattura o di un ruolo di proprietario o collaboratore in un profilo di fatturazione o in un account di fatturazione. Per altre informazioni, vedere [Ruoli e attività di fatturazione della sottoscrizione](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

L'esempio seguente usa le API REST. Attualmente, PowerShell e l'interfaccia della riga di comando di Azure non sono supportati.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si ha accesso

Fai la richiesta qui sotto per elencare tutti gli account di fatturazione.

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
Utilizzare `displayName` la proprietà per identificare l'account di fatturazione per il quale si desidera creare sottoscrizioni. Assicurarsi che il accettementType dell'account sia *MicrosoftCustomerAgreement*. Copiare `name` il file dell'account.  Ad esempio, se si desidera creare `Contoso` una sottoscrizione per `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`l'account di fatturazione, copiare . Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Trovare le sezioni fattura per creare sottoscrizioni

Gli addebiti per l'abbonamento vengono visualizzati in una sezione della fattura di un profilo di fatturazione. Usare l'API seguente per ottenere l'elenco delle sezioni di fattura e dei profili di fatturazione per i quali si dispone dell'autorizzazione per creare sottoscrizioni di Azure.Use the following API to get the list of invoice sections and billing profiles for which you have permission to create Azure subscriptions.

Eseguire la richiesta seguente, sostituendo `<billingAccountName>` con l'`name` copiato nel primo passaggio (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
La risposta API elenca tutte le sezioni relative alle fatture e i relativi profili di fatturazione a cui si ha accesso per creare sottoscrizioni:

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

Utilizzare `invoiceSectionDisplayName` la proprietà per identificare la sezione relativa alla fattura per la quale si desidera creare sottoscrizioni. Copiare `invoiceSectionId` `billingProfileId` la sezione `skuId` , e una di quella per la fattura. Ad esempio, se si desidera creare `Microsoft Azure plan` `Development` una sottoscrizione di tipo `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX` `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` per `0001`la sezione fattura, copiare , e . Incollare questi valori in un punto qualsiasi in modo da poterli utilizzare nel passaggio successivo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Creare una sottoscrizione per una sezione fattura

Nell'esempio seguente viene creata una sottoscrizione denominata *Sottoscrizione del team* di sviluppo di tipo Piano di Microsoft *Azure* per la fattura di *sviluppo.* La sottoscrizione verrà fatturata al profilo di fatturazione *di Contoso Finance* e verrà visualizzata nella sezione *Sviluppo* della fattura.

Effettuare la richiesta `<invoiceSectionId>` seguente, `invoiceSectionId` sostituendo la copia```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```del secondo passaggio ( ). È necessario passare il `billingProfileId` `skuId` e copiato dal secondo passaggio nei parametri di richiesta dell'API. Se si desidera specificare i proprietari, vedere [come ottenere gli ID oggetto utente.](grant-access-to-create-subscription.md#userObjectId)

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

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | string | Nome visualizzato della sottoscrizione|
| `billingProfileId`   | Sì      | string | ID del profilo di fatturazione che verrà fatturato per gli addebiti della sottoscrizione.  |
| `skuId` | Sì      | string | ID sku che determina il tipo di piano di Azure.The sku ID that determines the type of Azure plan. |
| `owners`      | No       | string | ID oggetto di qualsiasi utente o entità servizio che si vuole aggiungere come proprietario RBAC nella sottoscrizione al momento della creazione.  |
| `costCenter` | No      | string | Centro di costo associato alla sottoscrizione. Viene visualizzato nel file csv di utilizzo. |
| `managementGroupId` | No      | string | ID del gruppo di gestione a cui verrà aggiunta la sottoscrizione. Per ottenere l'elenco dei gruppi di gestione, vedere Gruppi di [gestione - API elenco](/rest/api/resources/managementgroups/list). Usare l'ID di un gruppo di gestione dall'API. |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Creare sottoscrizioni per un account di fatturazione MPA

### <a name="prerequisites"></a>Prerequisiti

Per creare la sottoscrizione per l'account di fatturazione, è necessario disporre di un ruolo Amministratore globale o Agente amministratore nell'account del provider di soluzioni cloud dell'organizzazione. Per ulteriori informazioni, vedere Centro per i [partner - Assegnare ruoli e autorizzazioni](https://docs.microsoft.com/partner-center/permissions-overview)degli utenti .

L'esempio seguente usa le API REST. Attualmente, PowerShell e l'interfaccia della riga di comando di Azure non sono supportati.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si ha accesso

Fai la richiesta qui sotto per elencare tutti gli account di fatturazione a cui hai accesso.

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
Utilizzare `displayName` la proprietà per identificare l'account di fatturazione per il quale si desidera creare sottoscrizioni. Assicurarsi che il accettementType dell'account sia *MicrosoftPartnerAgreement*. Copiare `name` il per l'account. Ad esempio, se si desidera creare `Contoso` una sottoscrizione per `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`l'account di fatturazione, copiare . Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-customers-that-have-azure-plans"></a>Trovare i clienti con piani di AzureFind customers that have Azure plans

Effettuare la richiesta `<billingAccountName>` seguente, `name` sostituendo la copia```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```del primo passaggio ( ) per elencare tutti i clienti nell'account di fatturazione per i quali è possibile creare sottoscrizioni di Azure.Make the following request, replacing with the copied from the first step ( ) to list all customers in the billing account for cui è possibile creare sottoscrizioni di Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
La risposta API elenca i clienti nell'account di fatturazione con i piani di Azure.The API response lists the customers in the billing account with Azure plans. È possibile creare sottoscrizioni per questi clienti.

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

Utilizzare `displayName` la proprietà per identificare il cliente per il quale si desidera creare sottoscrizioni. Copiare `id` il per il cliente. Ad esempio, se si desidera `Fabrikam toys`creare una sottoscrizione `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`per , è necessario copiare . Incollare questo valore in un punto qualsiasi per utilizzarlo nei passaggi successivi.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Facoltativo per i provider indiretti: ottenere i rivenditori per un clienteOptional for Indirect providers: Get the resellers for a customer

Se si è un provider indiretto nel modello a due livelli CSP, è possibile specificare un rivenditore durante la creazione di sottoscrizioni per i clienti.

Effettuare la richiesta `<customerId>` seguente, `id` sostituendola con```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```la copia copiata dal secondo passaggio ( ) per elencare tutti i rivenditori disponibili per un cliente.

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
Utilizzare `description` la proprietà per identificare il rivenditore che verrà associato alla sottoscrizione. Copiare `resellerId` il per il rivenditore. Ad esempio, se si `Wingtip`desidera associare `3xxxxx`, è necessario copiare . Incollare questo valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="create-a-subscription-for-a-customer"></a>Creare una sottoscrizione per un clienteCreate a subscription for a customer

Nell'esempio seguente viene creata una sottoscrizione denominata *Sottoscrizione del team* di sviluppo per i giocattoli *Fabrikam* e il rivenditore *Wingtip* alla sottoscrizione. T

Effettuare la richiesta `<customerId>` seguente, `id` sostituendo la copia```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```del secondo passaggio ( ). Passare il *resellerId* facoltativo copiato dal secondo passaggio nei parametri di richiesta dell'API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | string | Nome visualizzato della sottoscrizione|
| `skuId` | Sì      | string | ID sku del piano di Azure. Usare 0001 per le sottoscrizioni di tipo Piano di Microsoft AzureUse *0001* for subscriptions of type Microsoft Azure Plan |
| `resellerId`      | No       | string | ID MPN del rivenditore che verrà associato alla sottoscrizione.  |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio sulla creazione di una sottoscrizione Enterprise Agreement (EA) tramite .NET, vedere [codice di esempio in GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni sulla gestione di un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere Organizzare le risorse con i gruppi di gestione di [AzureTo](../../governance/management-groups/overview.md) learn more about managing large numbers of subscriptions using management groups, see Organize your resources with Azure management groups
