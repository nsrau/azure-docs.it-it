---
title: Imposta la durata per i token
titleSuffix: Microsoft identity platform
description: Informazioni su come impostare la durata dei token emessi dalla piattaforma di identità Microsoft. Informazioni su come gestire i criteri predefiniti di un'organizzazione, creare criteri per l'accesso Web, creare criteri per un'app nativa che chiama un'API Web e gestire criteri avanzati.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f70a11165f6433e580fd857f2d5a620deb6640c0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604276"
---
# <a name="configure-token-lifetime-policies-preview"></a>Configurare i criteri di durata del token (anteprima)
In Azure AD sono disponibili diversi scenari che permettono di creare e gestire la durata dei token per le app, le entità servizio e l'intera organizzazione. Per altre informazioni, vedere [durata del token configurabile nella piattaforma di identità Microsoft](active-directory-configurable-token-lifetimes.md). 

> [!IMPORTANT]
> Dopo aver ascoltato i clienti durante l'anteprima, sono state implementate le [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md) in Azure ad l'accesso condizionale. È possibile usare questa nuova funzionalità per configurare la durata dei token di aggiornamento impostando la frequenza di accesso. Dopo il 30 maggio 2020 nessun nuovo tenant sarà in grado di usare i criteri di durata dei token configurabili per configurare i token di sessione e di aggiornamento. La deprecazione avverrà entro diversi mesi, il che significa che non verranno rispettati i criteri di sessione e di aggiornamento dei token esistenti. È comunque possibile configurare la durata dei token di accesso dopo la deprecazione.


Questa sezione illustra alcuni scenari comuni relativi ai criteri che consentono di definire nuove regole per:

* Durata dei token
* Tempo di inattività massimo token
* Validità massima token

Gli esempi illustrano come:

* Gestire i criteri predefiniti di un'organizzazione
* Creare criteri per l'accesso Web
* Creare criteri per un'app nativa che chiama un'API Web
* Gestire criteri avanzati

## <a name="prerequisites"></a>Prerequisiti
Gli esempi seguenti mostrano come creare, aggiornare, collegare ed eliminare criteri per le app, le entità servizio e l'intera organizzazione. Se non si ha familiarità con Azure AD, è consigliabile acquisire informazioni su [come ottenere un tenant di Azure ad](quickstart-create-new-tenant.md) prima di procedere con questi esempi.  

Per iniziare, seguire questa procedura:

1. Scaricare la [versione di anteprima pubblica del modulo Azure ad PowerShell](https://www.powershellgallery.com/packages/AzureADPreview)più recente.
2. Eseguire il comando `Connect` per accedere all'account amministratore di Azure AD. Eseguire questo comando ogni volta che si avvia una nuova sessione.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Per visualizzare tutti i criteri creati nell'organizzazione, eseguire questo comando. Questo comando va usato dopo la maggior parte delle operazioni negli scenari indicato di seguito L'esecuzione del comando consente anche di ottenere ** ** dei criteri.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>Gestire i criteri predefiniti di un'organizzazione
In questo esempio si crea un criterio che consente agli utenti di accedere con minore frequenza nell'intera organizzazione. A tale scopo, creare un criterio per la durata dei token per i token di aggiornamento a fattore singolo, applicato all'interno dell'organizzazione. Tali criteri vengono applicati a ogni applicazione nell'organizzazione e a ogni entità servizio per cui ancora non sono impostati criteri.

1. Creare i criteri per la durata dei token.

    1. Impostare il token di aggiornamento a fattore singolo su "until-revoked". In questo modo il token non scade fino a quando non viene revocato l'accesso. Creare la definizione dei criteri seguente:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Per creare il criterio, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Per rimuovere gli spazi vuoti, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Per visualizzare i nuovi criteri e ottenere il relativo **ObjectId**, eseguire questo comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aggiornare i criteri.

    Il primo criterio impostato in questo esempio potrebbe non essere rigoroso quanto richiesto dal servizio. Per impostare il token di aggiornamento a fattore singolo in modo che scada entro due giorni, eseguire il comando seguente:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Creare criteri per l'accesso Web

In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire più spesso l'autenticazione nell'app Web. Questi criteri consentono di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a più fattori per l'entità servizio dell'app Web.

1. Creare i criteri per la durata dei token.

    Questi criteri per l'accesso Web consentono di impostare su due ore la durata dei token di accesso/ID e la validità massima di un token di sessione a fattore singolo.

    1. Per creare il criterio, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri e per ottenere l' **ObjectID**dei criteri, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri all'entità servizio. È anche necessario ottenere l' **ObjectID** dell'entità servizio.

    1. Usare il cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) per visualizzare tutte le entità servizio dell'organizzazione o una singola entità servizio.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Quando si dispone dell'entità servizio, eseguire il cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Creare criteri per un'app nativa che chiama un'API Web
In questo esempio vengono creati i criteri in base ai quali viene richiesto agli utenti di eseguire l'autenticazione con minore frequenza. Questi criteri prolungano anche l'intervallo di tempo di inattività dell'utente prima che sia necessario rieseguire l'autenticazione. I criteri vengono applicati all'API web. Quando l'app nativa richiede l'API Web come risorsa, vengono applicati questi criteri.

1. Creare i criteri per la durata dei token.

    1. Per creare un criterio rigoroso per un'API Web, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri, eseguire il comando seguente:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri all'API Web. È necessario ottenere anche l'**ObjectId** dell'app. Usare il cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) per trovare l' **ObjectID**dell'app o usare il [portale di Azure](https://portal.azure.com/).

    Ottenere l' **ObjectID** dell'app e assegnare i criteri:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>Gestire criteri avanzati
In questo esempio vengono creati alcuni criteri per apprendere il funzionamento del sistema di priorità. Viene inoltre illustrato come gestire più criteri applicati a diversi oggetti.

1. Creare i criteri per la durata dei token.

    1. Per creare un criterio predefinito dell'organizzazione che imposta la durata del token di aggiornamento a fattore singolo su 30 giorni, eseguire il cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Per visualizzare i nuovi criteri, eseguire il cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Assegnare i criteri a un'entità servizio.

    A questo punto sono disponibili criteri che si applicano all'intera organizzazione. Si supponga di voler conservare tali criteri della durata di 30 giorni per un'entità servizio specifica, impostando però i criteri predefiniti dell'organizzazione sul valore limite superiore, ovvero fino alla revoca.

    1. Per visualizzare tutte le entità servizio dell'organizzazione, usare il cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Quando si dispone dell'entità servizio, eseguire il cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) :

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Impostare il flag `IsOrganizationDefault` su false.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Creare nuovi criteri predefiniti dell'organizzazione.

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    I criteri originali sono ora collegati all'entità servizio e i nuovi criteri sono impostati come criteri predefiniti dell'organizzazione. È importante ricordare che i criteri applicati alle entità servizio hanno priorità rispetto a quelli predefiniti dell'organizzazione.

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulle [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md) in Azure ad l'accesso condizionale.