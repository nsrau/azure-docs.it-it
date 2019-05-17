---
title: Creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come creare sottoscrizioni di Azure Enterprise o Sviluppo/test Enterprise aggiuntive a livello di programmazione.
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
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796070"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Creare sottoscrizioni di Azure Enterprise a livello di programmazione (anteprima)

In qualità di cliente di Azure in [Enterprise Agreement, EA](https://azure.microsoft.com/pricing/enterprise-agreement/), l'utente può creare sottoscrizioni di EA (MS-AZR-0017P) e di Sviluppo/test EA (MS-AZR-0148P) a livello di programmazione. Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

Qualsiasi sottoscrizione di Azure creata tramite questa API è regolamentata dal contratto in base al quale l'utente ha ottenuto i servizi di Microsoft Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni Legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario un ruolo di proprietario nell'Account di registrazione che si desidera creare le sottoscrizioni. È possibile procedere in due modi per ottenere questi ruoli:

* L'amministratore delle registrazioni può [rendere è proprietario dell'Account](https://ea.azure.com/helpdocs/addNewAccount) (accesso richiesto) che ti rende un proprietario dell'Account di registrazione. Seguire le istruzioni nel messaggio di posta elettronica di invito ricevuto per creare manualmente una sottoscrizione iniziale. Confermare di essere proprietario dell'account e creare manualmente una sottoscrizione EA iniziale prima di procedere al passaggio successivo. La semplice aggiunta dell'account alla registrazione non è sufficiente.

* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](grant-access-to-create-subscription.md). Analogamente, se si desidera utilizzare un'entità servizio per creare la sottoscrizione EA, è necessario [concedere a tale entità servizio la possibilità di creare sottoscrizioni](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo essere stato aggiunto a una registrazione di Azure EA come proprietario dell'account, Azure usa la relazione account-registrazione per determinare il destinatario della fattura per i costi di sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione EA in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione. 

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia il `name` di quell'account. Ad esempio, se si desidera creare sottoscrizioni per il SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte in modo che è possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Aprire [Azure Cloud Shell](https://shell.azure.com/) e selezionare PowerShell.

Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure risponde con un elenco di account di registrazione che è possibile utilizzare:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia il `ObjectId` di quell'account. Ad esempio, se si desidera creare sottoscrizioni per il SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Incollare l'ID dell'oggetto da qualche parte in modo che è possibile usarlo nel passaggio successivo come il `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) per elencare tutti gli account di registrazione a cui si ha accesso.

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copia il `name` di quell'account. Ad esempio, se si desidera creare sottoscrizioni per il SignUpEngineering@contoso.com account di registrazione, è necessario copiare ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Questo è l'ID oggetto dell'account di registrazione. Incollare questo valore da qualche parte in modo che è possibile usarlo nel passaggio successivo come `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

L'esempio seguente crea una sottoscrizione denominata *sottoscrizione Team Dev* nell'account di registrazione selezionato nel passaggio precedente. L'offerta di sottoscrizione è *MS-AZR - 0017p* (regolari Microsoft Enterprise Agreement). Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

# <a name="resttabrest"></a>[REST](#tab/rest)

Eseguire la seguente richiesta, sostituendo `<enrollmentAccountObjectId>` con il `name` copiati nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se si desidera specificare i proprietari, apprendere [come ottenere l'ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | N.      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | N.       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Prima di tutto installare il modulo di anteprima eseguendo `Install-Module Az.Subscription -AllowPrerelease`. Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/gallery/installing-psget).

Eseguire la [New-AzSubscription](/powershell/module/az.subscription) comando seguente, sostituendo `<enrollmentAccountObjectId>` con il `ObjectId` raccolti nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se si desidera specificare i proprietari, apprendere [come ottenere l'ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatorio | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | N.      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | N.       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | N.       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | N.       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

In primo luogo, installare questa estensione di anteprima eseguendo `az extension add --name subscription`.

Eseguire la [az account creare](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) comando seguente, sostituendo `<enrollmentAccountObjectId>` con il `name` copiati nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se si desidera specificare i proprietari, apprendere [come ottenere l'ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatorio | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | N.      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | N.       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | N.       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | N.       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 50 sottoscrizioni per l'account di registrazione iniziale, ma è possibile [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per aumentare il limite di 200. Successivamente, è possibile creare sottoscrizioni solo tramite il centro Account.
- Nell'account deve essere presente almeno una sottoscrizione EA o Sviluppo/Test EA, il che vuol dire che il proprietario dell'account ha eseguito almeno una registrazione manuale.
- Gli utenti che non sono proprietari dell'account, ma che sono stati aggiunti a un account di registrazione tramite il Controllo degli accessi in base al ruolo, non possono creare sottoscrizioni mediante il Centro account.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
