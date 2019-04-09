---
title: Creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come creare sottoscrizioni di Azure Enterprise o Sviluppo/test Enterprise aggiuntive a livello di programmazione.
services: azure-resource-manager
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: tomfitz
ms.openlocfilehash: 93df0c196d78a4685ff82108354b82a07d67695d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256924"
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

# [<a name="rest"></a>REST](#tab/rest)

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

# [<a name="powershell"></a>PowerShell](#tab/azure-powershell)

Usare il cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) per elencare tutti gli account di registrazione a cui si ha accesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure risponde con un elenco degli ID oggetto e degli indirizzi di posta elettronica dell'account.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# [<a name="azure-cli"></a>Interfaccia della riga di comando di Azure](#tab/azure-cli)

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

L'esempio seguente crea una richiesta per creare la sottoscrizione denominata *Sottoscrizione team Dev* e l'offerta di sottoscrizione è *MS-AZR-0017P* (EA regolare). L'account di registrazione è `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (valore segnaposto, il valore è un GUID), ovvero l'account di registrazione per SignUpEngineering@contoso.com. Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

# [<a name="rest"></a>REST](#tab/rest)

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

| Nome dell'elemento  | Obbligatorio | Type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

# [<a name="powershell"></a>PowerShell](#tab/azure-powershell)

Per usare questo modulo di anteprima, installarlo eseguendo prima `Install-Module Az.Subscription -AllowPrerelease`. Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/gallery/installing-psget).

Usare [New-AzSubscription](/powershell/module/az.subscription) con l'ID oggetto `enrollmentAccount` come parametro `EnrollmentAccountObjectId` per creare una nuova sottoscrizione. 

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatorio | Type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No        | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No        | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription.preview).

# [<a name="azure-cli"></a>Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare questa estensione di anteprima, installarla eseguendo prima `az extension add --name subscription`.

Usare [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) con l'ID oggetto `enrollmentAccount` come parametro `enrollment-account-object-id` per creare una nuova sottoscrizione.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatorio | Type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | No       | string | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | string | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | string | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No        | string | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No        | string | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | No        | string | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 50 sottoscrizioni per l'account. Successivamente, è possibile creare le sottoscrizioni solo tramite il Centro account.
- Nell'account deve essere presente almeno una sottoscrizione EA o Sviluppo/Test EA, il che vuol dire che il proprietario dell'account ha eseguito almeno una registrazione manuale.
- Gli utenti che non sono proprietari dell'account, ma che sono stati aggiunti a un account di registrazione tramite il Controllo degli accessi in base al ruolo, non possono creare sottoscrizioni mediante il Centro account.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
