---
title: Eseguire l'autenticazione e l'autorizzazione usando un'API in Azure Time Series Insights | Microsoft Docs
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
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 602623d48457498963cb5928081d24c1d1132ad4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935258"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory usando il nuovo pannello Azure Active Directory. Le app registrate in Azure Active Directory consentono agli utenti di eseguire l'autenticazione a ed essere autorizzati a usare l'API Azure Time Series Insight associata a un ambiente Time Series Insights.

## <a name="service-principal"></a>Entità servizio

Le sezioni seguenti descrivono come configurare un'applicazione per accedere all'API Time Series Insights per conto di un'app. L'applicazione può quindi eseguire query o pubblicare dati di riferimento nell'ambiente Time Series Insights usando le proprie credenziali dell'applicazione tramite Azure Active Directory.

## <a name="summary-and-best-practices"></a>Riepilogo e procedure consigliate

Il flusso di registrazione dell'app Azure Active Directory prevede tre passaggi principali.

1. [Registrare un'applicazione](#azure-active-directory-app-registration) in Azure Active Directory.
1. Autorizzare l'applicazione ad [accedere ai dati dell'ambiente Time Series Insights](#granting-data-access).
1. Usare l' **ID applicazione** e il **segreto client** per acquisire un token `https://api.timeseries.azure.com/` da nell' [app client](#client-app-initialization). Il token può quindi essere usato per chiamare l'API Time Series Insights.

Al **passaggio 3**, la separazione delle credenziali dell'applicazione e dell'utente consente di:

* Assegnare le autorizzazioni all'identità dell'app che sono diverse dalle proprie autorizzazioni. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, è possibile consentire all'app di leggere i dati solo da un particolare ambiente Time Series Insights.
* Isolare la sicurezza dell'app dalla creazione delle credenziali di autenticazione dell'utente usando un **segreto client** o un certificato di sicurezza. Di conseguenza, le credenziali dell'applicazione non dipendono dalle credenziali di un utente specifico. Se il ruolo dell'utente cambia, l'applicazione non richiede necessariamente nuove credenziali o una configurazione aggiuntiva. Se l'utente modifica la password, per tutti gli accessi all'applicazione non sono necessarie nuove credenziali o chiavi.
* Eseguire uno script automatico usando un **segreto client** o un certificato di sicurezza, anziché le credenziali di un utente specifico (che devono essere presenti).
* Usare un certificato di sicurezza anziché una password per proteggere l'accesso all'API Azure Time Series Insights.

> [!IMPORTANT]
> Per configurare i criteri di sicurezza Azure Time Series Insights, seguire il principio della **separazione delle problematiche** (descritte per questo scenario).

> [!NOTE]
> * L'articolo è incentrato su un'applicazione a tenant singolo in cui l'applicazione deve essere eseguita solo in un'organizzazione.
> * In genere si usano applicazioni a tenant singolo per le applicazioni line-of-business in esecuzione nell'organizzazione.

## <a name="detailed-setup"></a>Installazione dettagliata

### <a name="azure-active-directory-app-registration"></a>Registrazione dell'app Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessione dell'accesso ai dati

1. Per l'ambiente di Time Series Insights, selezionare **criteri di accesso ai dati** e selezionare **Aggiungi**.

   [![Aggiungere nuovi criteri di accesso ai dati all'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nella finestra di dialogo **Seleziona utente** incollare il nome dell' **applicazione** o l' **ID dell'applicazione** nella sezione relativa alla registrazione dell'app Azure Active Directory.

   [![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo. Selezionare **Reader** per eseguire query sui dati o sul collaboratore per eseguire query sui dati e modificare i dati di riferimento. Selezionare **OK**.

   [![Seleziona lettore o collaboratore nella finestra di dialogo Seleziona ruolo utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvare il criterio selezionando **OK**.

   > [!TIP]
   > Leggere le informazioni sulla [concessione dell'accesso ai dati](./time-series-insights-data-access.md) all'ambiente di Time Series Insights in Azure Active Directory.

### <a name="client-app-initialization"></a>Inizializzazione dell'app client

1. Usare l' **ID applicazione** e il **segreto client** (chiave applicazione) dalla sezione registrazione app Azure Active Directory per acquisire il token per conto dell'applicazione.

    In C#il codice seguente può acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

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

- Per il codice di esempio che chiama l'API Time Series Insights GA, vedere [eseguire C#query sui dati usando ](./time-series-insights-query-data-csharp.md).

- Per l'anteprima Time Series Insights esempi di codice dell'API, vedere [eseguire C#query sui dati di anteprima usando ](./time-series-insights-update-query-data-csharp.md).

- Per informazioni di riferimento sull'API, vedere il [riferimento all'API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).