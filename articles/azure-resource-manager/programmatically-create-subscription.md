---
title: Creare sottoscrizioni di Azure Enterprise a livello di programmazione | Microsoft Docs
description: Informazioni su come creare sottoscrizioni di Azure Enterprise o Sviluppo/test Enterprise aggiuntive a livello di programmazione.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 755eabe97508b403205ff04a8d2d35feee314eb9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258937"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Creare sottoscrizioni di Azure Enterprise a livello di programmazione (anteprima)

In qualità di cliente di Azure in [Enterprise Agreement, EA](https://azure.microsoft.com/pricing/enterprise-agreement/), l'utente può creare sottoscrizioni di EA (MS-AZR-0017P) e di Sviluppo/test EA (MS-AZR-0148P) a livello di programmazione. Questo articolo contiene informazioni su come creare sottoscrizioni a livello di codice usando Azure Resource Manager.

Qualsiasi sottoscrizione di Azure creata tramite questa API è regolamentata dal contratto in base al quale l'utente ha ottenuto i servizi di Microsoft Azure da Microsoft o da un rivenditore autorizzato. Per altre informazioni, vedere [Informazioni Legali su Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di un ruolo proprietario nell'account di registrazione in cui si desidera creare le sottoscrizioni. È possibile procedere in due modi per ottenere questi ruoli:

* L'amministratore della registrazione può [creare un proprietario dell'account](https://ea.azure.com/helpdocs/addNewAccount) (accesso richiesto) che rende un proprietario dell'account di registrazione. Seguire le istruzioni nel messaggio di posta elettronica di invito ricevuto per creare manualmente una sottoscrizione iniziale. Confermare di essere proprietario dell'account e creare manualmente una sottoscrizione EA iniziale prima di procedere al passaggio successivo. La semplice aggiunta dell'account alla registrazione non è sufficiente.

* Un proprietario esistente dell'account di registrazione può [concedere l'accesso](grant-access-to-create-subscription.md). Analogamente, se si desidera utilizzare un'entità servizio per creare la sottoscrizione EA, è necessario [concedere a tale entità servizio la possibilità di creare sottoscrizioni](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Trovare gli account a cui si ha accesso

Dopo essere stato aggiunto a una registrazione di Azure EA come proprietario dell'account, Azure usa la relazione account-registrazione per determinare il destinatario della fattura per i costi di sottoscrizione. Tutte le sottoscrizioni create con l'account vengono fatturate alla registrazione EA in cui si trova l'account. Per creare sottoscrizioni, è necessario passare i valori sull'account di registrazione e le entità utente proprietarie della sottoscrizione. 

Per eseguire i comandi seguenti è necessario che sia stato effettuato l'accesso nella *home directory* del proprietario dell'account, ovvero la directory in cui, per impostazione predefinita, vengono create le sottoscrizioni.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare l' `name` oggetto di quell'account. Se, ad esempio, si desidera creare sottoscrizioni nell' SignUpEngineering@contoso.com account di registrazione, copiare. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio `enrollmentAccountObjectId`successivo come.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare l' `ObjectId` oggetto di quell'account. Se, ad esempio, si desidera creare sottoscrizioni nell' SignUpEngineering@contoso.com account di registrazione, copiare. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Incollare questo ID oggetto in un punto in modo che sia possibile usarlo nel passaggio successivo `enrollmentAccountObjectId`come.

## <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

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

Usare la proprietà `principalName` per identificare l'account a cui si desidera addebitare le sottoscrizioni. Copiare l' `name` oggetto di quell'account. Se, ad esempio, si desidera creare sottoscrizioni nell' SignUpEngineering@contoso.com account di registrazione, copiare. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Questo identificatore è l'ID oggetto dell'account di registrazione. Incollare questo valore in un punto in modo che sia possibile usarlo nel passaggio `enrollmentAccountObjectId`successivo come.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creare sottoscrizioni in un account di registrazione specifico

L'esempio seguente crea una sottoscrizione denominata *dev team Subscription* nell'account di registrazione selezionato nel passaggio precedente. L'offerta di sottoscrizione è *MS-AZR-0017P* (regular Microsoft Enterprise Agreement). Aggiunge anche facoltativamente due utenti come proprietari con Controllo degli accessi in base al ruolo per la sottoscrizione.

# <a name="resttabrest"></a>[REST](#tab/rest)

Eseguire la richiesta seguente, sostituendo `<enrollmentAccountObjectId>` con l'oggetto `name` copiato dal primo passaggio```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```(). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

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

| Nome dell'elemento  | Obbligatoria | Type   | DESCRIZIONE                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |

Nella risposta verrà restituito un oggetto `subscriptionOperation` per il monitoraggio. Al termine dell'operazione di creazione della sottoscrizione, l'oggetto `subscriptionOperation` restituirà un oggetto `subscriptionLink` che contiene l'ID della sottoscrizione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Installare innanzitutto questo modulo di `Install-Module Az.Subscription -AllowPrerelease`anteprima eseguendo. Per assicurarsi che `-AllowPrerelease` funzioni, installare una versione recente di PowerShellGet da [Ottenere il modulo PowerShellGet](/powershell/gallery/installing-psget).

Eseguire il comando [New-AzSubscription](/powershell/module/az.subscription) di seguito, `<enrollmentAccountObjectId>` sostituendo `ObjectId` con il raccolto nel primo passaggio```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```(). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Yes       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `OwnerSignInName`    | No       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`.|
| `OwnerApplicationId` | No       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `OwnerObjectId`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [New-AzSubscription](/powershell/module/az.subscription).

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per prima cosa, installare questa estensione di `az extension add --name subscription`anteprima eseguendo.

Eseguire il comando [AZ account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) sotto, sostituendo `<enrollmentAccountObjectId>` con il `name` copiato nel primo passaggio (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Per specificare i proprietari, [vedere informazioni su come ottenere gli ID oggetto utente](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome dell'elemento  | Obbligatoria | Type   | Descrizione                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | No      | String | Nome visualizzato della sottoscrizione Se non specificato, viene impostato il nome dell'offerta, ad esempio "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sì      | String | Offerta della sottoscrizione. Esistono due opzioni per EA, ovvero [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso in produzione) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (sviluppo/test, deve essere [attivato tramite il portale EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sì       | String | ID oggetto dell'account di registrazione con cui la sottoscrizione viene creata e a cui viene addebitata. Il valore è un GUID che si ottiene da `az billing enrollment-account list`. |
| `owner-object-id`      | No       | String | ID oggetto di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione.  |
| `owner-upn`    | No       | String | Indirizzo di posta elettronica di un utente che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`.|
| `owner-spn` | No       | String | ID applicazione di un'entità servizio che si desidera aggiungere come proprietario con Controllo degli accessi in base al ruolo nella sottoscrizione al momento della creazione. È possibile usare questo parametro anziché `owner-object-id`. Quando si usa questo parametro, l'entità servizio deve avere [accesso in lettura alla directory](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Per visualizzare un elenco completo di tutti i parametri, vedere [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitazioni dell'API di creazione della sottoscrizione di Azure Enterprise

- Usando questa API è possibile creare solo le sottoscrizioni di Azure Enterprise.
- È previsto un limite di 200 sottoscrizioni per ogni account di registrazione. Successivamente, è possibile creare più sottoscrizioni per l'account solo tramite il centro account. Se si desidera creare più sottoscrizioni tramite l'API, creare un altro account di registrazione.
- Gli utenti che non sono proprietari dell'account, ma sono stati aggiunti a un account di registrazione tramite RBAC, non possono creare sottoscrizioni tramite il centro account.
- Non è possibile selezionare il tenant in cui creare la sottoscrizione. La sottoscrizione viene sempre creata nel tenant home del proprietario dell'account. Per spostare la sottoscrizione in un tenant diverso, vedere l'articolo su come [modificare il tenant della sottoscrizione](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio su come creare le sottoscrizioni che usano .NET, vedere l'[esempio di codice su GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ora che la sottoscrizione è stata creata, è possibile concedere la medesima possibilità ad altri utenti ed entità servizio. Per altre informazioni, vedere [Grant access to create Azure Enterprise subscriptions (preview)](grant-access-to-create-subscription.md) (Concedere l'accesso a creare sottoscrizioni di Azure Enterprise (anteprima)).
* Per altre informazioni come gestire un numero elevato di sottoscrizioni usando i gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md)
