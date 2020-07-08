---
title: Autenticazione e autorizzazione API - Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 94fef951bf1c5c9d69a9b49cd9465d7d248c74a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85099223"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory con il nuovo pannello Azure Active Directory. Le app registrate in Azure Active Directory consentono agli utenti di eseguire l'autenticazione e ottenere l'autorizzazione a usare l'API di Azure Time Series Insights associata a un ambiente Time Series Insights.

## <a name="service-principal"></a>Entità servizio

Le sezioni seguenti illustrano come configurare un'applicazione per accedere all'API di Time Series Insights per conto di un'app. L'applicazione può quindi eseguire query o pubblicare dati di riferimento nell'ambiente Time Series Insights usando le proprie credenziali dell'applicazione tramite Azure Active Directory.

## <a name="summary-and-best-practices"></a>Riepilogo e procedure consigliate

Il flusso di registrazione dell'app Azure Active Directory prevede tre passaggi principali.

1. [Registrare un'applicazione](#azure-active-directory-app-registration) in Azure Active Directory.
1. Autorizzare l'applicazione ad [accedere ai dati nell'ambiente Time Series Insights](#granting-data-access).
1. Usare l'**ID applicazione** e il **segreto client** per acquisire un token da `https://api.timeseries.azure.com/` nell'[app client](#client-app-initialization). Il token può quindi essere usato per chiamare l'API Time Series Insights.

Come indicato nel **passaggio 3**, la separazione delle credenziali dell'applicazione e dell'utente consente di:

* Assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, è possibile consentire all'app di leggere i dati soltanto in un determinato ambiente Time Series Insights.
* Isolare la sicurezza dell'app dalla creazione delle credenziali di autenticazione dell'utente usando un **segreto client** o un certificato di sicurezza. Di conseguenza, le credenziali dell'applicazione non dipendono dalle credenziali di un utente specifico. Se il ruolo dell'utente cambia, per l'applicazione non serviranno necessariamente nuove credenziali o un'ulteriore configurazione. Se l'utente modifica la password, per l'accesso all'applicazione non sono necessarie nuove credenziali o chiavi.
* Eseguire uno script automatico utilizzando un **segreto client** o un certificato di sicurezza al posto delle credenziali di un utente specifico (che ne richiedono la presenza).
* Usare un certificato di sicurezza anziché una password per proteggere l'accesso all'API di Azure Time Series Insights.

> [!IMPORTANT]
> Durante la configurazione del criterio di sicurezza di Azure Time Series Insights, attenersi al principio di **separazione degli interessi** (descritto sopra per questo scenario).

> [!NOTE]
> * Questo articolo è incentrato su un'applicazione a tenant singolo che prevede l'esecuzione dell'applicazione all'interno di una sola organizzazione.
> * Le applicazioni a tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.

## <a name="detailed-setup"></a>Configurazione dettagliata

### <a name="azure-active-directory-app-registration"></a>Registrazione dell'app Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessione dell'accesso ai dati

1. Per l'ambiente Time Series Insights, selezionare **Criteri di accesso ai dati**, quindi **Aggiungi**.

   [![Aggiungere il nuovo criterio di accesso ai dati per l'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nella finestra di dialogo **Seleziona utente** incollare il **Nome applicazione** o l'**ID applicazione** indicati nella sezione relativa alla registrazione dell'app Azure Active Directory.

   [![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo. Selezionare **Lettore** per eseguire query sui dati o **Collaboratore** per eseguire query sui dati e modificare i dati di riferimento. Selezionare **OK**.

   [![Selezionare Lettore o Collaboratore nella finestra di dialogo Selezionare il ruolo utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvare il criterio selezionando **OK**.

   > [!TIP]
   > Per le opzioni avanzate di accesso ai dati, vedere [Concessione dell'accesso ai dati](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inizializzazione dell'app client

* Gli sviluppatori possono utilizzare la [Microsoft Authentication Library (MSAL) per l'autenticazione con Azure Time Series Insights.

* Per eseguire l'autenticazione con ADAL:

   1. Usare l'**ID applicazione** e il **segreto client** (chiave applicazione) indicati nella sezione relativa alla registrazione dell'app Azure Active Directory al fine di acquisire il token per conto dell'applicazione.

   1. In C# il codice seguente può acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati con C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

> [!IMPORTANT]
> Se si usa [Azure Active Directory Authentication Library (adal),](https://docs.microsoft.com/azure/active-directory/azuread-dev/active-directory-authentication-libraries) vedere la pagina relativa [alla migrazione a MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration).

    See our [Manage GA reference data for an Azure Time Series Insights environment using C#](time-series-insights-manage-reference-data-csharp.md) article to learn more.

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

Questa sezione descrive le intestazioni e i parametri della richiesta HTTP comuni usati per eseguire query sulle API di anteprima e di disponibilità generale di Time Series Insights. I requisiti specifici dell'API sono descritti in dettaglio nella [documentazione di riferimento dell'API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

> [!TIP]
> Vedere [Informazioni di riferimento sull'API REST di Azure](https://docs.microsoft.com/rest/api/azure/) per altre informazioni su come usare le API REST, effettuare richieste HTTP e gestire le risposte HTTP.

### <a name="authentication"></a>Authentication

Per eseguire query autenticate sulle [API REST di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/) è necessario passare un token di connessione OAuth 2.0 valido nell'[intestazione dell'autorizzazione](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) tramite un client REST di propria scelta (Postman, JavaScript, C#).

> [!TIP]
> Leggere l'articolo relativo alla [visualizzazione di esempio dell'SDK del client](https://tsiclientsample.azurewebsites.net/) Azure Time Series Insights ospitato per informazioni su come eseguire in modo programmatico l'autenticazione con le API di Time Series Insights tramite l'[SDK del client JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) e i grafici.

### <a name="http-headers"></a>Intestazioni HTTP

Di seguito sono descritte le intestazioni della richiesta obbligatorie.

| Intestazione della richiesta obbligatoria | Descrizione |
| --- | --- |
| Autorizzazione | Per eseguire l'autenticazione con Time Series Insights, è necessario passare un token di connessione OAuth 2.0 valido nell'intestazione **Autorizzazione**. |

> [!IMPORTANT]
> Il token deve essere emesso esattamente nella risorsa `https://api.timeseries.azure.com/` (nota anche come "destinatario" del token).
> * L'**AuthURL** [Postman](https://www.getpostman.com/) sarà: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com/.default`
> * `https://api.timeseries.azure.com/` è valido, ma `https://api.timeseries.azure.com` non lo è.

Di seguito sono descritte le intestazioni della richiesta facoltative.

| Intestazione di richiesta facoltativa | Descrizione |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-client-request-id | ID della richiesta client. Il servizio registra questo valore. Consente al servizio di tenere traccia dell'operazione nei diversi servizi. |
| x-ms-client-session-id | ID della sessione client. Il servizio registra questo valore. Consente al servizio di tenere traccia di un gruppo di operazioni correlate nei diversi servizi. |
| x-ms-client-application-name | Nome dell'applicazione che ha generato questa richiesta. Il servizio registra questo valore. |

Di seguito sono descritte le intestazioni della risposta facoltative ma consigliate.

| Intestazione risposta | Descrizione |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-request-id | ID della richiesta generato dal server. Può essere usato per contattare Microsoft se dovesse essere necessario esaminare una richiesta. |
| x-ms-Property-not-found-Behavior | Intestazione della risposta facoltativa dell'API di disponibilità generale. I valori possibili sono `ThrowError` (impostazione predefinita) o `UseNull`. |

### <a name="http-parameters"></a>Parametri HTTP

> [!TIP]
> Per approfondire le informazioni sulle query obbligatorie e facoltative, vedere la [documentazione di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/).

I parametri della stringa di query dell'URL obbligatori dipendono dalla versione dell'API.

| Versione | Possibili valori della versione dell'API |
| --- |  --- |
| Disponibilità generale | `api-version=2016-12-12`|
| Anteprima | `api-version=2018-11-01-preview` |
| Anteprima | `api-version=2018-08-15-preview` |

I parametri della stringa di query dell'URL facoltativi includono l'impostazione di un timeout per i tempi di esecuzione della richiesta HTTP.

| Parametro di query facoltativo | Descrizione | Versione |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout lato server per l'esecuzione di una richiesta HTTP. Applicabile solo alle API [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e [Get Environment Aggregates](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api). Il valore di timeout deve essere nel formato di durata ISO 8601, ad esempio `"PT20S"`, e deve essere compreso nell'intervallo `1-30 s`. Il valore predefinito è `30 s`. | GA |
| `storeType=<storeType>` | Negli ambienti di anteprima con archivio ad accesso frequente abilitato, la query può essere eseguita in `WarmStore` o in `ColdStore`. Questo parametro nella query definisce l'archivio in cui quest'ultima deve essere eseguita. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. Per eseguire la query nell'archivio ad accesso frequente, occorre impostare **storeType** su `WarmStore`. Se non è definito, la query verrà eseguita nell'archivio ad accesso sporadico. | Anteprima |

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio di codice che chiama l'API di Time Series Insights a disponibilità generale, vedere [Eseguire query sui dati con C#](./time-series-insights-query-data-csharp.md).

* Per esempi di codice dell'API di Time Series Insights di anteprima, vedere [Eseguire query dei dati di anteprima con C#](./time-series-insights-update-query-data-csharp.md).

* Per le informazioni di riferimento sull'API, vedere la documentazione di [ riferimento sull'API di query](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

* Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).
