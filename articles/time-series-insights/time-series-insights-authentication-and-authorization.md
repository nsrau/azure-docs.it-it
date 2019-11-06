---
title: Eseguire l'autenticazione e l'autorizzazione usando un'API in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4fd68f770cbe48b15646ec41c0bf94be5e760a50
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990195"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory usando il nuovo pannello Azure Active Directory. Le app registrate in Azure Active Directory consentono agli utenti di eseguire l'autenticazione a ed essere autorizzati a usare l'API Azure Time Series Insight associata a un ambiente Time Series Insights.

## <a name="service-principal"></a>Entità servizio

Le sezioni seguenti descrivono come configurare un'applicazione per accedere all'API Time Series Insights per conto di un'app. L'applicazione può quindi eseguire query o pubblicare dati di riferimento nell'ambiente Time Series Insights usando le proprie credenziali dell'applicazione tramite Azure Active Directory.

## <a name="summary-and-best-practices"></a>Riepilogo e procedure consigliate

Il flusso di registrazione dell'app Azure Active Directory prevede tre passaggi principali.

1. [Registrare un'applicazione](#azure-active-directory-app-registration) in Azure Active Directory.
1. Autorizzare l'applicazione ad [accedere ai dati dell'ambiente Time Series Insights](#granting-data-access).
1. Usare l' **ID applicazione** e il **segreto client** per acquisire un token da `https://api.timeseries.azure.com/` nell' [app client](#client-app-initialization). Il token può quindi essere usato per chiamare l'API Time Series Insights.

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

   [![aggiungere nuovi criteri di accesso ai dati all'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nella finestra di dialogo **Seleziona utente** incollare il nome dell' **applicazione** o l' **ID dell'applicazione** nella sezione relativa alla registrazione dell'app Azure Active Directory.

   [![trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo. Selezionare **Reader** per eseguire query sui dati o sul **collaboratore** per eseguire query sui dati e modificare i dati di riferimento. Selezionare **OK**.

   [![scegliere Reader o collaboratore nella finestra di dialogo Seleziona ruolo utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

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

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

Questa sezione descrive le intestazioni e i parametri della richiesta HTTP comuni usati per eseguire query sulle API Time Series Insights GA e Preview. I requisiti specifici dell'API sono trattati più dettagliatamente nella [documentazione di riferimento dell'API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Autenticazione

Per eseguire query autenticate sulle [API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), è necessario passare un bearer token OAuth 2,0 valido nell' [intestazione Authorization](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) usando un client REST di propria scelta (postazione, JavaScript, C#). 

> [!IMPORTANT]
> Il token deve essere emesso esattamente con la risorsa `https://api.timeseries.azure.com/` (nota anche come "audience" del token).
> * Il [Postman](https://www.getpostman.com/) **AuthURL** con è quindi conforme a: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Per informazioni su come eseguire l'autenticazione con le API di Time Series Insights a livello di codice tramite [JavaScript client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) insieme a grafici e grafici, vedere la visualizzazione dell'esempio hosted Azure Time Series Insights [client SDK](https://tsiclientsample.azurewebsites.net/) .

### <a name="http-headers"></a>Intestazioni HTTP

Intestazioni richieste obbligatorie:

- `Authorization` per l'autenticazione e l'autorizzazione, è necessario passare un token di porta OAuth 2,0 valido nell'intestazione dell'autorizzazione. Il token deve essere emesso esattamente con la risorsa `https://api.timeseries.azure.com/` (nota anche come "audience" del token).

Intestazioni di richiesta facoltative:

- `application/json` solo `Content-type` è supportato.
- `x-ms-client-request-id`: ID richiesta client. Il servizio registra questo valore. Consente al servizio di tracciare l'operazione tra i servizi.
- `x-ms-client-session-id`: ID sessione client. Il servizio registra questo valore. Consente al servizio di tracciare un gruppo di operazioni correlate tra i servizi.
- `x-ms-client-application-name`: nome dell'applicazione che ha generato la richiesta. Il servizio registra questo valore.

Intestazioni risposta:

- `application/json` solo `Content-type` è supportato.
- ID richiesta generato dal server `x-ms-request-id`. Può essere usato per contattare Microsoft per esaminare una richiesta.

### <a name="http-parameters"></a>Parametri HTTP

Parametri della stringa di query dell'URL necessario:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Parametri della stringa di query dell'URL facoltativo:

- `timeout=<timeout>`: timeout lato server per l'esecuzione della richiesta. Applicabile solo alle API [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Il valore di timeout deve essere nel formato di durata ISO 8601, ad esempio `"PT20S"` e deve essere compreso nell'intervallo `1-30 s`. Il valore predefinito è `30 s`.

## <a name="next-steps"></a>Passaggi successivi

- Per il codice di esempio che chiama l'API Time Series Insights GA, vedere [eseguire C#query sui dati usando ](./time-series-insights-query-data-csharp.md).

- Per l'anteprima Time Series Insights esempi di codice dell'API, vedere [eseguire C#query sui dati di anteprima usando ](./time-series-insights-update-query-data-csharp.md).

- Per informazioni di riferimento sull'API, vedere il [riferimento all'API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).