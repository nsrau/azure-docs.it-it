---
title: Gestire i criteri di consenso delle app in Azure AD
description: Informazioni su come gestire i criteri di consenso app predefiniti e personalizzati per controllare quando è possibile concedere il consenso.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperfq2
ms.openlocfilehash: 07637a8be49fb2449c5c92c1a1ea4b2c7ace9a8d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442260"
---
# <a name="manage-app-consent-policies"></a>Gestire i criteri di consenso delle app

Con Azure AD PowerShell è possibile visualizzare e gestire i criteri di consenso delle app.

Un criterio di consenso dell'app è costituito da uno o più set di condizioni "includes" e da zero o più set di condizioni "excludes". Affinché un evento venga considerato in un criterio di consenso dell'app, deve corrispondere almeno a un set di condizioni "includes" e non deve corrispondere *ad* *alcun* set di condizioni "excludes".

Ogni set di condizioni è costituito da diverse condizioni. Affinché un evento corrisponda a un set di condizioni, è necessario che *tutte* le condizioni nel set di condizioni siano soddisfatte.

I criteri di consenso delle app in cui l'ID inizia con "Microsoft-" sono criteri predefiniti. Alcuni di questi criteri predefiniti vengono usati nei ruoli della directory predefiniti esistenti. Ad esempio, i `microsoft-application-admin` criteri di consenso delle app descrivono le condizioni in base alle quali i ruoli amministratore applicazione e amministratore applicazione cloud possono concedere il consenso dell'amministratore a livello di tenant. I criteri predefiniti possono essere usati nei ruoli della directory personalizzati e per configurare le impostazioni di consenso dell'utente, ma non possono essere modificati o eliminati.

## <a name="pre-requisites"></a>Prerequisiti

1. Accertarsi di usare il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true). Questo passaggio è importante se sono stati installati sia il modulo [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) sia il modulo [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)).

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Connettersi ad Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Elenca i criteri di consenso delle app esistenti

Per iniziare, è consigliabile acquisire familiarità con i criteri di consenso delle app esistenti nell'organizzazione:

1. Elencare tutti i criteri di consenso delle app:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Visualizzare i set di condizioni "includes" di un criterio:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Visualizzare i set di condizioni "excludes":

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Creare un criterio di consenso dell'app personalizzato

Seguire questa procedura per creare criteri di consenso dell'app personalizzati:

1. Creare un nuovo criterio di consenso per le app vuote.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Aggiungere set di condizioni "includes".

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Ripetere questo passaggio per aggiungere ulteriori set di condizioni di "inclusione".

1. Facoltativamente, aggiungere set di condizioni "excludes".

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Ripetere questo passaggio per aggiungere ulteriori set di condizioni "exclude".

Una volta creati i criteri di consenso per l'app, è possibile [consentire il consenso dell'utente](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) in base a questi criteri.

## <a name="delete-a-custom-app-consent-policy"></a>Eliminare un criterio di consenso dell'app personalizzato

1. Di seguito viene illustrato come è possibile eliminare un criterio di consenso dell'app personalizzato. **Questa azione non può essere annullata.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Non è possibile ripristinare i criteri di consenso delle app eliminati. Se si elimina accidentalmente un criterio di consenso dell'app personalizzato, sarà necessario ricreare i criteri.

---

### <a name="supported-conditions"></a>Condizioni supportate

La tabella seguente include l'elenco delle condizioni supportate per i criteri di consenso delle app.

| Condizione | Descrizione|
|:---------------|:----------|
| PermissionClassification | [Classificazione delle autorizzazioni](configure-permission-classifications.md) per l'autorizzazione che viene concessa o "All" per la corrispondenza con qualsiasi classificazione di autorizzazione (incluse le autorizzazioni non classificate). Il valore predefinito è "All". |
| PermissionType | Tipo di autorizzazione dell'autorizzazione concessa. Usare "Application" per le autorizzazioni dell'applicazione (ad esempio, i ruoli dell'app) o "delegate" per le autorizzazioni delegate. <br><br>**Nota** : il valore "delegatedUserConsentable" indica le autorizzazioni delegate che non sono state configurate dal server di pubblicazione API per richiedere il consenso dell'amministratore. questo valore può essere usato nei criteri di concessione delle autorizzazioni predefinite, ma non può essere usato nei criteri di concessione delle autorizzazioni personalizzate. Obbligatorio. |
| ResourceApplication | **AppID** dell'applicazione della risorsa (ad esempio, l'API) per cui viene concessa un'autorizzazione o "any" per la corrispondenza con qualsiasi API o applicazione della risorsa. Il valore predefinito è "any". |
| Autorizzazioni | Elenco di ID di autorizzazione per le autorizzazioni specifiche con cui trovare una corrispondenza o un elenco con il singolo valore "All" per la corrispondenza con qualsiasi autorizzazione. Il valore predefinito è "All". <ul><li>Gli ID autorizzazione delegati si trovano nella proprietà **OAuth2Permissions** dell'oggetto SERVICEPRINCIPAL dell'API.</li><li>Gli ID delle autorizzazioni dell'applicazione sono reperibili nella proprietà **AppRoles** dell'oggetto SERVICEPRINCIPAL dell'API.</li></ol> |
| ClientApplicationIds | Elenco di valori **AppID** per le applicazioni client con cui trovare una corrispondenza o un elenco con il singolo valore "All" per la corrispondenza con qualsiasi applicazione client. Il valore predefinito è "All". |
| ClientApplicationTenantIds | Elenco di Azure Active Directory ID tenant in cui è registrata l'applicazione client o un elenco con il singolo valore "All" per la corrispondenza con le app client registrate in qualsiasi tenant. Il valore predefinito è "All". |
| ClientApplicationPublisherIds | Elenco di ID di Microsoft Partner Network (MPN) per i server di [pubblicazione verificati](../develop/publisher-verification-overview.md) dell'applicazione client o un elenco con il singolo valore "All" per la corrispondenza con le app client da qualsiasi server di pubblicazione. Il valore predefinito è "All". |
| ClientApplicationsFromVerifiedPublisherOnly | Impostare su in `$true` modo che corrisponda solo alle applicazioni client con i [Publisher verificati](../develop/publisher-verification-overview.md). Impostare su `$false` per trovare la corrispondenza con qualsiasi app client, anche se non dispone di un server di pubblicazione verificato. Il valore predefinito è `$false`. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni:

* [Configurare le impostazioni di consenso dell'utente](configure-user-consent.md)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Informazioni su come gestire il consenso alle applicazioni e valutare le richieste di consenso](manage-consent-requests.md)
* [Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

Per ottenere assistenza o trovare risposte alle domande:
* [Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
