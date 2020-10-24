---
title: Modificare le impostazioni predefinite per la durata dei token per app Azure AD personalizzate
description: Informazioni su come aggiornare i criteri di durata del token per l'applicazione che si sta sviluppando in Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 9483fe972cf1a4dce4fb285ced3cb390d0bda725
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516784"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Come modificare le impostazioni predefinite per la durata del token per un'applicazione personalizzata

Questo articolo illustra come usare Azure AD PowerShell per impostare i criteri per la durata dei token. Azure AD Premium consente agli sviluppatori di app e agli amministratori di tenant di configurare la durata dei token emessi per i client non riservati. I criteri di durata dei token vengono impostati a livello di tenant o per le risorse a cui si accede.

> [!IMPORTANT]
> Dopo il 30 gennaio 2021, i tenant non saranno più in grado di configurare la durata del token di sessione e di aggiornamento e Azure Active Directory smetterà di rispettare la configurazione del token di sessione e di aggiornamento esistente nei criteri dopo tale data. È comunque possibile configurare la durata dei token di accesso dopo la deprecazione. Per altre informazioni, vedere [durata dei token configurabili in Azure ad](./active-directory-configurable-token-lifetimes.md).
> Sono state implementate le [funzionalità di gestione delle sessioni di autenticazione](../conditional-access/howto-conditional-access-session-lifetime.md)   in Azure ad l'accesso condizionale. È possibile usare questa nuova funzionalità per configurare la durata dei token di aggiornamento impostando la frequenza di accesso.  

Per impostare i criteri di durata del token, è necessario scaricare il [modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Eseguire il comando **Connect-AzureAD -Confirm**.

Ecco un criterio di esempio che richiede agli utenti di eseguire l'autenticazione con maggiore frequenza nell'app Web. Questi criteri consentono di impostare la durata dei token di accesso/ID e la validità massima di un token di sessione a più fattori per l'entità servizio dell'app Web. Creare il criterio e assegnarlo all'entità servizio. È necessario ottenere anche l'ObjectId dell'entità servizio.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Durate dei token configurabile in Azure AD](./active-directory-configurable-token-lifetimes.md) per informazioni su come configurare la durata dei token emessi da Azure AD, tra cui come impostare la durata dei token per tutte le app di un'organizzazione, per un'applicazione multi-tenant o per un'entità servizio specifica in un'organizzazione. 
* [Riferimento al token Azure AD](./id-tokens.md)