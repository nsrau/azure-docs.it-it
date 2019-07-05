---
title: Autenticare e autorizzare usando un'API in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543981"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory usando il nuovo pannello Azure Active Directory. Le app registrate in Azure Active Directory agli utenti di autenticare e autorizzare l'utilizzo delle API Azure Time Series Insights associata a un ambiente Time Series Insights.

## <a name="service-principal"></a>Entità servizio

Le sezioni seguenti descrivono come configurare un'applicazione di accedere l'API Time Series Insights per conto di un'app. L'applicazione può quindi eseguire una query o pubblicare dati di riferimento nell'ambiente di Time Series Insights usando le proprie credenziali dell'applicazione tramite Azure Active Directory.

## <a name="summary-and-best-practices"></a>Riepiloghi e procedure consigliate

Il flusso di registrazione app di Azure Active Directory prevede tre passaggi principali.

1. [Registrare un'applicazione](#azure-active-directory-app-registration) in Azure Active Directory.
1. Autorizzare l'applicazione abbia [accesso ai dati nell'ambiente Time Series Insights](#granting-data-access).
1. Usare la **ID applicazione** e **segreto Client** per acquisire un token da `https://api.timeseries.azure.com/` nel [app client](#client-app-initialization). Il token può quindi essere usato per chiamare l'API Time Series Insights.

Per ogni **passaggio 3**, la separazione tra l'applicazione e le credenziali utente consente di:

* Assegnare autorizzazioni all'identità dell'app che sono diverse dalle proprie autorizzazioni. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, è possibile consentire all'app di leggere i dati solo da un particolare ambiente Time Series Insights.
* Isolare la sicurezza dell'app dalle credenziali di autenticazione dell'utente usando un **segreto Client** o certificato di sicurezza. Di conseguenza, le credenziali dell'applicazione non sono interdipendenti le credenziali di un utente specifico. Se viene modificato il ruolo dell'utente, l'applicazione non richiede necessariamente nuove credenziali o altre opzioni di configurazione. Se l'utente modifica la password, tutti gli accessi all'applicazione non richiedono nuove credenziali o chiavi.
* Eseguire uno script automatico tramite un **segreto Client** o certificato di sicurezza anziché le credenziali di un utente specifico (la necessità che siano presenti).
* Usare un certificato di sicurezza anziché una password per proteggere l'accesso per l'API Azure Time Series Insights.

> [!IMPORTANT]
> Attenersi al principio dei **separazione delle competenze** (descritto in questo scenario riportato sopra) quando la configurazione di criteri di sicurezza di Azure Time Series Insights.

> [!NOTE]
> * L'articolo è incentrato su un'applicazione a tenant singolo in cui l'applicazione deve essere eseguito in una sola organizzazione.
> * Si userà in genere le applicazioni a tenant singolo per le applicazioni line-of-business eseguite all'interno dell'organizzazione.

## <a name="detailed-setup"></a>Programma di installazione dettagliata

### <a name="azure-active-directory-app-registration"></a>Registrazione di app di Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessione dell'accesso ai dati

1. Per l'ambiente Time Series Insights, selezionare **i criteri di accesso di dati** e selezionare **Add**.

   [![Aggiungere nuovi criteri di accesso di dati per l'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nel **Seleziona utente** finestra di dialogo incollare uno il **nome applicazione** o le **ID applicazione** dalla sezione di registrazione app di Azure Active Directory.

   [![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo. Selezionare **Reader** per eseguire query sui dati o **collaboratore** per eseguire query sui dati e modificare i dati di riferimento. Selezionare **OK**.

   [![Selezionare lettore o collaboratore nella finestra di dialogo selezionare il ruolo utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvare i criteri selezionando **OK**.

   > [!TIP]
   > Conoscenza [concedere l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights in Azure Active Directory.

### <a name="client-app-initialization"></a>Inizializzazione dell'app client

1. Usare la **ID applicazione** e **privata del Client** (chiave dell'applicazione) della sezione di registrazione app di Azure Active Directory per acquisire il token per conto dell'applicazione.

    In C#, il codice seguente è possibile acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

## <a name="next-steps"></a>Passaggi successivi

- Per esempio di codice che chiama l'API di Time Series Insights disponibile a livello generale, vedere [tramite dati di Query C# ](./time-series-insights-query-data-csharp.md).

- Per esempi di codice API Time Series Insights di anteprima, vedere [tramite dati di anteprima Query C# ](./time-series-insights-update-query-data-csharp.md).

- Per informazioni di riferimento sull'API, vedere il [riferimento all'API di query](/rest/api/time-series-insights/ga-query-api).

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).