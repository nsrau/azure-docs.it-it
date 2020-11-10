---
title: Creare sottoscrizioni di Azure a livello di codice con API in anteprima
description: Informazioni su come creare sottoscrizioni di Azure aggiuntive a livello di codice usando le versioni in anteprima dell'API REST, dell'interfaccia della riga di comando di Azure e di Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/29/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3ffdeb0add8622e1b9f28f9603dc146b78f742cd
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043293"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Creare sottoscrizioni di Azure a livello di codice con API in anteprima

Questo articolo illustra come creare a livello di codice le sottoscrizioni di Azure usando l'API in anteprima meno recente. È stata rilasciata una [versione più recente dell'API](programmatically-create-subscription.md). È possibile usare le informazioni in questo articolo se non si vuole usare la versione più recente. Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

I clienti di Azure con un account di fatturazione per i tipi di contratto seguenti possono creare sottoscrizioni a livello di codice:

- [Contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Contratto del cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Contratto Microsoft Partner](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)

Quando si crea una sottoscrizione di Azure a livello di codice, tale sottoscrizione è regolamentata dal contratto in base al quale l'utente ha ottenuto i servizi di Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Creare sottoscrizioni per un account di fatturazione con Contratto Enterprise

Usare le informazioni disponibili nelle sezioni seguenti per creare sottoscrizioni con Contratto Enterprise.

### <a name="prerequisites"></a>Prerequisiti

Per creare una sottoscrizione, è necessario avere un ruolo Proprietario o un account di registrazione. È possibile ottenere il ruolo in due modi:

* L'amministratore dell'organizzazione della registrazione può [configurare l'utente come proprietario dell'account](https://ea.azure.com/helpdocs/addNewAccount) e con accesso obbligatorio, in modo che diventi un proprietario dell'account di registrazione.
* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](grant-access-to-create-subscription.md). Analogamente, per usare un'entità servizio per creare la sottoscrizione con Contratto Enterprise, è necessario [concedere a tale entità servizio la possibilità di creare sottoscrizioni](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo l'aggiunta dell'utente a un account di registrazione associato a un proprietario dell'account, Azure usa la relazione tra account e registrazione per determinare il destinatario degli addebiti della sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione con Contratto Enterprise in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione.

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

### <a name="rest"></a>[REST](#tab/rest)

Richiesta per ottenere l'elenco di tutti gli account di registrazione a cui si può accedere:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

La risposta dell'API elenca tutti gli account di registrazione a cui si può accedere:

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il valore `name` di tale account. Per creare ad esempio sottoscrizioni nell'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. L'identificatore è l'ID oggetto dell'account di registrazione. Incollare il valore da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aprire [Azure Cloud Shell](https://shell.azure.com/) e selezionare PowerShell.

Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure risponde con un elenco degli account di registrazione a cui si ha accesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il valore `ObjectId` di tale account. Per creare ad esempio sottoscrizioni nell'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare l'ID oggetto da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az billing enrollment-account list](/cli/azure/billing) per elencare tutti gli account di registrazione a cui si ha accesso.

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare il valore `name` di tale account. Per creare ad esempio sottoscrizioni nell'account di registrazione SignUpEngineering@contoso.com, copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. L'identificatore è l'ID oggetto dell'account di registrazione. Incollare il valore da qualche parte per poterlo usare nel passaggio successivo come `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

L'esempio seguente crea una sottoscrizione denominata *Dev Team Subscription* nell'account di registrazione selezionato nel passaggio precedente. L'offerta di sottoscrizione è *MS-AZR-0017P* (Contratto Microsoft Enterprise normale). Aggiunge facoltativamente anche due utenti come proprietari con Controllo degli accessi in base al ruolo di Azure per la sottoscrizione.

### <a name="rest"></a>[REST](#tab/rest)

Eseguire la richiesta seguente, sostituendo `<enrollmentAccountObjectId>` con l'`name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, vedere [Come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

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

| Nome dell'elemento  | Obbligatorio | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No       | string | ID oggetto di un utente da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione.  |

Nella risposta, come parte dell'intestazione `Location`, viene restituito un URL su cui è possibile eseguire una query per ottenere lo stato nell'operazione di creazione della sottoscrizione. Al termine della creazione della sottoscrizione, un'operazione GET sull'URL `Location` restituirà un oggetto `subscriptionLink`, che contiene l'ID sottoscrizione. Per altri dettagli, vedere la [documentazione dell'API Sottoscrizione](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per installare la versione più recente del modulo che contiene il cmdlet `New-AzSubscription`, eseguire `Install-Module Az.Subscription`. Per installare una versione recente di PowerShellGet, vedere [Ottenere il modulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Eseguire il comando [New-AzSubscription](/powershell/module/az.subscription) riportato di seguito, sostituendo `<enrollmentAccountObjectId>` con il valore `ObjectId` raccolto nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, vedere [Come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatorio | Type   | Descrizione |
|---------------|----------|--------|----|
| `Name` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio *Microsoft Azure Enterprise*. |
| `OfferType`   | Sì      | string | Offerta di sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No       | string | ID oggetto di un utente da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No       | string | Indirizzo di posta elettronica di un utente da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione. È possibile usare il parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No       | string | ID applicazione di un'entità servizio da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione. È possibile usare il parametro anziché `OwnerObjectId`. Quando si usa il parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Installare prima di tutto l'estensione in anteprima eseguendo `az extension add --name subscription`.

Eseguire il comando [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) riportato di seguito, sostituendo `<enrollmentAccountObjectId>` con il valore `name` copiato dal primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, vedere [Come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatorio | Type   | Descrizione |
|---------------|----------|--------|------------|
| `display-name` | No      | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio *Microsoft Azure Enterprise*.|
| `offer-type`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No       | string | ID oggetto di un utente da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No       | string | Indirizzo di posta elettronica di un utente da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione. È possibile usare il parametro anziché `owner-object-id`.|
| `owner-spn` | No       | string | ID applicazione di un'entità servizio da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione. È possibile usare il parametro anziché `owner-object-id`. Quando si usa il parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando l'API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 2000 sottoscrizioni per account di registrazione. Dopo tale limite, sarà possibile creare altre sottoscrizioni per l'account nel portale di Azure. Per creare più sottoscrizioni tramite l'API, creare un altro account di registrazione. Le sottoscrizioni annullate, eliminate e trasferite vengono conteggiate ai fini del limite di 2000.
- Gli utenti che non sono proprietari dell'account, ma che sono stati aggiunti a un account di registrazione con Controllo degli accessi in base al ruolo di Azure, non possono creare sottoscrizioni nel portale di Azure.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Creare sottoscrizioni per un account con Contratto del cliente Microsoft

Usare le informazioni disponibili nelle sezioni seguenti per creare sottoscrizioni per un account con Contratto del cliente Microsoft.

### <a name="prerequisites"></a>Prerequisiti

Per creare sottoscrizioni, è necessario avere un ruolo di tipo Proprietario, Collaboratore o Autore di sottoscrizioni di Azure in una sezione della fattura oppure un ruolo di tipo Proprietario o Collaboratore in un profilo di fatturazione o un account di fatturazione. Per altre informazioni, vedere [Ruoli e attività di fatturazione della sottoscrizione](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Gli esempi seguenti usano le API REST. PowerShell e l'interfaccia della riga di comando di Azure non sono attualmente supportati.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si può accedere

Creare la richiesta seguente per elencare tutti gli account di fatturazione.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
La risposta dell'API elenca gli account di fatturazione a cui si può accedere.

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
Usare la proprietà `displayName` per identificare l'account di fatturazione per cui si vogliono creare le sottoscrizioni. Assicurarsi che agreementType dell'account sia *MicrosoftCustomerAgreement*. Copiare il valore `name` dell'account.  Per creare ad esempio una sottoscrizione per l'account di fatturazione `Contoso`, copiare `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Incollare il valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Trovare sezioni della fattura per creare sottoscrizioni

Gli addebiti per la sottoscrizione vengono visualizzati in una sezione della fattura di un profilo di fatturazione. Usare l'API seguente per ottenere l'elenco di sezioni della fattura e profili di fatturazione per cui si è autorizzati a creare sottoscrizioni di Azure.

Eseguire la richiesta seguente, sostituendo `<billingAccountName>` con l'`name` copiato nel primo passaggio (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

La risposta dell'API elenca tutte le sezioni della fattura e i rispettivi profili di fatturazione a cui è possibile accedere per creare sottoscrizioni:

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

Usare la proprietà `invoiceSectionDisplayName` per identificare la sezione della fattura per cui si vogliono creare le sottoscrizioni. Copiare `invoiceSectionId`, `billingProfileId` e uno dei valori `skuId` per la sezione della fattura. Per creare ad esempio una sottoscrizione di tipo `Microsoft Azure plan` per la sezione della fattura `Development`, copiare `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` e `0001`. Incollare i valori da qualche parte per poterli usare nel passaggio successivo.

### <a name="create-a-subscription-for-an-invoice-section"></a>Creare una sottoscrizione per una sezione della fattura

L'esempio seguente crea una sottoscrizione denominata *Dev Team subscription* di tipo *Piano di Microsoft Azure* per la sezione della fattura *Sviluppo*. La sottoscrizione viene fatturata al profilo di fatturazione di *Contoso finance* e verrà visualizzata nella sezione *Sviluppo* della rispettiva fattura.

Creare la richiesta seguente, sostituendo `<invoiceSectionId>` con il valore `invoiceSectionId` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Passare i valori `billingProfileId` e `skuId` copiati dal secondo passaggio nei parametri della richiesta dell'API. Per specificare i proprietari, vedere [Come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

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

| Nome dell'elemento  | Obbligatorio | Tipo   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | string | Nome visualizzato della sottoscrizione|
| `billingProfileId`   | Sì      | string | ID del profilo di fatturazione a cui vengono fatturati gli addebiti della sottoscrizione.  |
| `skuId` | Sì      | string | ID SKU che determina il tipo di piano di Azure. |
| `owners`      | No       | string | ID oggetto di un'entità utente o servizio da aggiungere come proprietario con Controllo degli accessi in base al ruolo di Azure nella sottoscrizione al momento della creazione.  |
| `costCenter` | No      | string | Centro di costo associato alla sottoscrizione. Viene visualizzato nel file CSV relativo all'utilizzo. |
| `managementGroupId` | No      | string | ID del gruppo di gestione a cui verrà aggiunta la sottoscrizione. Per ottenere l'elenco dei gruppi di gestione, vedere [Gruppi di gestione - API Elenco](/rest/api/resources/managementgroups/list). Usare l'ID di un gruppo di gestione dall'API. |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituisce un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Creare sottoscrizioni per un account di fatturazione con Contratto Microsoft Partner

Usare le informazioni disponibili nelle sezioni seguenti per creare sottoscrizioni per un account con Contratto Microsoft Partner.

### <a name="prerequisites"></a>Prerequisiti

Per creare una sottoscrizione per l'account di fatturazione, è necessario avere un ruolo di tipo Amministratore globale o Agente amministratore nell'account del provider di soluzioni cloud dell'organizzazione. Per altre informazioni, vedere [Centro per i partner - Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview).

Gli esempi seguenti usano le API REST. PowerShell e l'interfaccia della riga di comando di Azure non sono attualmente supportati.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Trovare gli account di fatturazione a cui si può accedere

Creare la richiesta seguente per elencare tutti gli account di fatturazione a cui si può accedere.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

La risposta dell'API elenca gli account di fatturazione.

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
Usare la proprietà `displayName` per identificare l'account di fatturazione per cui si vogliono creare le sottoscrizioni. Assicurarsi che agreementType dell'account sia *MicrosoftPartnerAgreement*. Copiare il valore `name` per l'account. Per creare ad esempio una sottoscrizione per l'account di fatturazione `Contoso`, copiare `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Incollare il valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="find-customers-that-have-azure-plans"></a>Trovare clienti con piani di Azure

Creare la richiesta seguente, sostituendo `<billingAccountName>` con il valore `name` copiato dal primo passaggio (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) per elencare tutti i clienti nell'account di fatturazione per cui è possibile creare sottoscrizioni di Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
La risposta API elenca i clienti nell'account di fatturazione con piani di Azure. È possibile creare sottoscrizioni per i clienti.

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

Usare la proprietà `displayName` per identificare il cliente per cui si vogliono creare le sottoscrizioni. Copiare il valore `id` per il cliente. Per creare ad esempio una sottoscrizione per `Fabrikam toys`, copiare `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Incollare il valore da qualche parte per usarlo nei passaggi successivi.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Operazione facoltativa per provider indiretti: Ottenere i rivenditori per un cliente

I provider indiretti nel modello a due livelli del programma CSP possono specificare un rivenditore durante la creazione delle sottoscrizioni per i clienti.

Creare la richiesta seguente, sostituendo `<customerId>` con il valore `id` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) per elencare tutti i rivenditori disponibili per un cliente.

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

Usare la proprietà `description` per identificare il rivenditore da associare alla sottoscrizione. Copiare il valore `resellerId` per il rivenditore. Per associare ad esempio `Wingtip`, copiare `3xxxxx`. Incollare il valore da qualche parte per poterlo usare nel passaggio successivo.

### <a name="create-a-subscription-for-a-customer"></a>Creare una sottoscrizione per un cliente

L'esempio seguente crea una sottoscrizione denominata *Dev Team subscription* per *Fabrikam toys* e associa il rivenditore *Wingtip* alla sottoscrizione. 

Creare la richiesta seguente, sostituendo `<customerId>` con il valore `id` copiato dal secondo passaggio (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passare il valore *resellerId* facoltativo copiato dal secondo passaggio nei parametri della richiesta dell'API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome dell'elemento  | Obbligatorio | Tipo   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sì      | string | Nome visualizzato della sottoscrizione|
| `skuId` | Sì      | string | ID SKU del piano di Azure. Usare *0001* per le sottoscrizioni di tipo Piano di Microsoft Azure |
| `resellerId`      | No       | string | ID del Contratto Microsoft Partner del rivenditore che verrà associato alla sottoscrizione.  |

Nella risposta verrà restituito un oggetto `subscriptionCreationResult` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionCreationResult` restituisce un oggetto `subscriptionLink`. Include l'ID sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare un esempio della creazione di una sottoscrizione con Contratto Enterprise con .NET, vedere [Codice di esempio in GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni sulla gestione di un numero elevato di sottoscrizioni mediante i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](../../governance/management-groups/overview.md).