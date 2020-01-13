---
title: Autenticazione e autorizzazione dell'API-Azure Time Series Insights | Microsoft Docs
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
ms.date: 12/09/2019
ms.custom: seodec18
ms.openlocfilehash: b54034dc8828fb8a96f488197e517ef07ed55ab5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460415"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory usando il nuovo pannello Azure Active Directory. Le app registrate in Azure Active Directory consentono agli utenti di eseguire l'autenticazione a ed essere autorizzati a usare l'API Azure Time Series Insight associata a un ambiente Time Series Insights.

> [!IMPORTANT]
> Azure Time Series Insights supporta entrambe le librerie di autenticazione seguenti:
> * [Microsoft Authentication Library più recente (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Libreria di autenticazione Azure Active Directory (adal)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

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
   > Per le opzioni avanzate di accesso ai dati, vedere [concessione dell'accesso ai dati](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inizializzazione dell'app client

* Gli sviluppatori possono utilizzare [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) o [Azure Active Directory Authentication Library (adal)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) per l'autenticazione con Azure Time Series Insights.

* Ad esempio, per eseguire l'autenticazione con ADAL:

   1. Usare l' **ID applicazione** e il **segreto client** (chiave applicazione) dalla sezione registrazione app Azure Active Directory per acquisire il token per conto dell'applicazione.

   1. In C#il codice seguente può acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

* In alternativa, gli sviluppatori possono scegliere di eseguire l'autenticazione con MSAL. Per altre informazioni, vedere la pagina relativa [alla migrazione a MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) . 

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

Questa sezione descrive le intestazioni e i parametri della richiesta HTTP comuni usati per eseguire query sulle API Time Series Insights GA e Preview. I requisiti specifici dell'API sono trattati più dettagliatamente nella [documentazione di riferimento dell'API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Vedere le informazioni di [riferimento sull'API REST di Azure](https://docs.microsoft.com/rest/api/azure/) per altre informazioni su come usare le API REST, effettuare richieste HTTP e gestire le risposte http.

### <a name="authentication"></a>Autenticazione

Per eseguire query autenticate sulle [API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), è necessario passare un bearer token OAuth 2,0 valido nell' [intestazione Authorization](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) usando un client REST di propria scelta (postazione, JavaScript, C#). 

> [!TIP]
> Per informazioni su come eseguire l'autenticazione con le API di Time Series Insights a livello di codice tramite [JavaScript client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) insieme a grafici e grafici, vedere la visualizzazione dell'esempio hosted Azure Time Series Insights [client SDK](https://tsiclientsample.azurewebsites.net/) .

### <a name="http-headers"></a>Intestazioni HTTP

Le intestazioni di richiesta richieste sono descritte di seguito.

| Intestazione richiesta obbligatoria | Description |
| --- | --- |
| Autorizzazione | Per eseguire l'autenticazione con Time Series Insights, è necessario passare un token di porta OAuth 2,0 valido nell'intestazione dell' **autorizzazione** . | 

> [!IMPORTANT]
> Il token deve essere emesso esattamente con la risorsa `https://api.timeseries.azure.com/` (nota anche come "audience" del token).
> * Il [](https://www.getpostman.com/) **AuthUrl** di pubblicazione sarà quindi: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` è valido, ma `https://api.timeseries.azure.com` non lo è.

Le intestazioni di richiesta facoltative sono descritte di seguito.

| Intestazione di richiesta facoltativa | Description |
| --- | --- |
| Tipo di contenuto | è supportata solo `application/json`. |
| x-ms-client-request-id | ID della richiesta client. Il servizio registra questo valore. Consente al servizio di tracciare l'operazione tra i servizi. |
| x-MS-client-session-ID | ID di sessione del client. Il servizio registra questo valore. Consente al servizio di tracciare un gruppo di operazioni correlate tra i servizi. |
| x-MS-client-Application-Name | Nome dell'applicazione che ha generato la richiesta. Il servizio registra questo valore. |

Le intestazioni di risposta facoltative ma consigliate sono descritte di seguito.

| Intestazione risposta | Description |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-request-id | ID richiesta generato dal server. Può essere usato per contattare Microsoft per esaminare una richiesta. |
| x-ms-Property-not-found-Behavior | Intestazione della risposta facoltativa dell'API GA. I valori possibili sono `ThrowError` (impostazione predefinita) o `UseNull`. |

### <a name="http-parameters"></a>Parametri HTTP

> [!TIP]
> Per ulteriori informazioni sulle query obbligatorie e facoltative, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/).

I parametri della stringa di query dell'URL richiesti dipendono dalla versione dell'API.

| Rilascio | Possibili valori della versione API |
| --- |  --- |
| Disponibilità generale | `api-version=2016-12-12`|
| Anteprima | `api-version=2018-11-01-preview` |
| Anteprima | `api-version=2018-08-15-preview` |

I parametri facoltativi della stringa di query dell'URL includono l'impostazione di un timeout per la richiesta HTTP.

| Parametro di query facoltativo | Description | Versione |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout lato server per l'esecuzione di una richiesta HTTP. Applicabile solo alle API [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Il valore di timeout deve essere nel formato di durata ISO 8601, ad esempio `"PT20S"` e deve essere compreso nell'intervallo `1-30 s`. Il valore predefinito è `30 s`. | Disponibilità a livello generale |
| `storeType=<storeType>` | Per gli ambienti di anteprima con warm Store abilitato, la query può essere eseguita nel `WarmStore` o `ColdStore`. Questo parametro nella query definisce l'archivio in cui deve essere eseguita la query. Se non è definito, la query verrà eseguita nell'archivio a freddo. Per eseguire una query nell'archivio a caldo, **storeType** deve essere impostato su `WarmStore`. Se non è definito, la query verrà eseguita sull'archivio a freddo. | Anteprima |

## <a name="next-steps"></a>Passaggi successivi

- Per il codice di esempio che chiama l'API Time Series Insights GA, vedere [eseguire C#query sui dati usando ](./time-series-insights-query-data-csharp.md).

- Per l'anteprima Time Series Insights esempi di codice dell'API, vedere [eseguire C#query sui dati di anteprima usando ](./time-series-insights-update-query-data-csharp.md).

- Per informazioni di riferimento sulle API, vedere la documentazione di [riferimento dell'API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) .

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).
