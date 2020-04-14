---
title: Autenticazione e autorizzazione dell'API - Azure Time Series Insights Documenti Microsoft
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
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 373fc2829e599d0989b59c6386757c8f5f3e1d66
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251725"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo documento descrive come registrare un'app in Azure Active Directory usando il nuovo blade di Azure Active Directory.This document describes how to register an app in Azure Active Directory using the new Azure Active Directory blade. Le app registrate in Azure Active Directory consentono agli utenti di eseguire l'autenticazione e di essere autorizzati a usare l'API Azure Time Series Insight associata a un ambiente Time Series Insights.Apps registered in Azure Active Directory enable users to authenticate to and be authorized to use the Azure Time Series Insight API associated with a Time Series Insights environment.

> [!IMPORTANT]
> Azure Time Series Insights supporta entrambe le librerie di autenticazione seguenti:Azure Time Series Insights supports both of the following authentication libraries:
> * La libreria di autenticazione Microsoft più recente [(MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * Azure [Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Entità servizio

Le sezioni seguenti descrivono come configurare un'applicazione per accedere all'API Time Series Insights per conto di un'app. L'applicazione può quindi eseguire query o pubblicare i dati di riferimento nell'ambiente Time Series Insights usando le proprie credenziali dell'applicazione tramite Azure Active Directory.

## <a name="summary-and-best-practices"></a>Riepilogo e procedure consigliate

Il flusso di registrazione dell'app Azure Active Directory prevede tre passaggi principali.

1. [Registrare un'applicazione](#azure-active-directory-app-registration) in Azure Active Directory.Register an application in Azure Active Directory.
1. Autorizzare l'applicazione per avere [accesso ai dati all'ambiente Time Series Insights](#granting-data-access).
1. Utilizzare **l'ID applicazione** e il `https://api.timeseries.azure.com/` **segreto client** per acquisire un token da [nell'app client.](#client-app-initialization) Il token può quindi essere usato per chiamare l'API Time Series Insights.

Per **ogni passaggio 3**, la separazione delle credenziali dell'applicazione e dell'utente consente di:

* Assegnare all'identità dell'app autorizzazioni distinte dalle proprie. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, puoi consentire all'app di leggere i dati solo da un particolare ambiente Time Series Insights.
* Isolare la sicurezza dell'app dalle credenziali di autenticazione dell'utente che crea utilizzando un **segreto client** o un certificato di sicurezza. Di conseguenza, le credenziali dell'applicazione non dipendono dalle credenziali di un utente specifico. Se il ruolo dell'utente cambia, l'applicazione non richiede necessariamente nuove credenziali o ulteriori configurazioni. Se l'utente modifica la password, tutti gli accessi all'applicazione non richiedono nuove credenziali o chiavi.
* Eseguire uno script automatico utilizzando un **segreto client** o un certificato di sicurezza anziché le credenziali di un utente specifico (che richiedono la presenza).
* Usare un certificato di sicurezza anziché una password per proteggere l'accesso all'API di Azure Time Series Insights.Use a security certificate rather than a password to secure access to your Azure Time Series Insights API.

> [!IMPORTANT]
> Seguire il principio di **separazione dei problemi** (descritto per questo scenario precedente) durante la configurazione dei criteri di sicurezza di Azure Time Series Insights.Follow the principle of Separation of Concerns (described for this scenario above) when configuring your Azure Time Series Insights security policy.

> [!NOTE]
> * L'articolo è incentrato su un'applicazione single-tenant in cui l'applicazione deve essere eseguita in una sola organizzazione.
> * In genere si usano applicazioni single-tenant per le applicazioni line-of-business eseguite nell'organizzazione.

## <a name="detailed-setup"></a>Configurazione dettagliata

### <a name="azure-active-directory-app-registration"></a>Registrazione dell'app Azure Active DirectoryAzure Active Directory app registration

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Concessione dell'accesso ai dati

1. Per l'ambiente Time Series Insights, selezionare **Criteri di accesso ai dati** e quindi **Aggiungi**.

   [![Aggiungere nuovi criteri di accesso ai dati per l'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nella finestra di dialogo Seleziona utente incollare il **nome dell'applicazione** o **l'ID applicazione** dalla sezione Registrazione app Azure Active Directory.In the **Select User** dialog box, paste either the Application Name or the Application ID from the Azure Active Directory app registration section.

   [![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo. Selezionare **Lettore** per eseguire query sui dati o **Collaboratore** per eseguire query sui dati e modificare i dati di riferimento. Selezionare **OK**.

   [![Seleziona lettore o collaboratore nella finestra di dialogo Seleziona ruolo utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvare il criterio selezionando **OK**.

   > [!TIP]
   > Per le opzioni avanzate di accesso ai dati, leggere [concessione dell'accesso ai dati](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inizializzazione dell'app client

* Gli sviluppatori possono usare [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) o [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) per eseguire l'autenticazione con Azure Time Series Insights.

* Ad esempio, per eseguire l'autenticazione tramite ADAL:For example, to authenticate using ADAL:

   1. Usare **l'ID applicazione** e il **segreto client** (chiave dell'applicazione) della sezione Registrazione dell'app Azure Active Directory per acquisire il token per conto dell'applicazione.

   1. Nel linguaggio C, il codice seguente può acquisire il token per conto dell'applicazione. Per un esempio completo, leggere [Interrogare i dati utilizzando C .](time-series-insights-query-data-csharp.md)

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

* In alternativa, gli sviluppatori possono scegliere di eseguire l'autenticazione utilizzando MSAL. Per altre informazioni, [vedere](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) l'articolo Gestire i dati di riferimento GA per un ambiente Azure Time Series Insights usando [C'](time-series-insights-manage-reference-data-csharp.md) per altre informazioni. 

## <a name="common-headers-and-parameters"></a>Intestazioni e parametri comuni

In questa sezione vengono descritti i parametri e le intestazioni di richiesta HTTP comuni usati per eseguire query sulle API GA e di anteprima di Time Series Insights. I requisiti specifici delle API sono trattati in modo più dettagliato nella documentazione di [riferimento dell'API REST](https://docs.microsoft.com/rest/api/time-series-insights/)di Time Series Insights.

> [!TIP]
> Leggere la Guida di [riferimento all'API REST](https://docs.microsoft.com/rest/api/azure/) di Azure per altre informazioni su come usare le API REST, eseguire richieste HTTP e gestire le risposte HTTP.

### <a name="authentication"></a>Authentication

Per eseguire query autenticate sulle [API REST Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/), è necessario passare un token di connessione OAuth 2.0 valido nell'intestazione [Authorization](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) usando un client REST di propria scelta (Postman, JavaScript, C'). 

> [!TIP]
> Leggi la [visualizzazione dell'SDK](https://tsiclientsample.azurewebsites.net/) del client Azure Time Series Insights ospitato per informazioni su come eseguire l'autenticazione con le API time Series Insights a livello di programmazione usando [JavaScript Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) insieme a grafici e grafici.

### <a name="http-headers"></a>Intestazioni HTTP

Le intestazioni di richiesta obbligatorie sono descritte di seguito.

| Intestazione richiesta obbligatoria | Descrizione |
| --- | --- |
| Autorizzazione | Per eseguire l'autenticazione con Time Series Insights, è necessario passare un token OAuth 2.0 Bearer valido nell'intestazione **Authorization.** | 

> [!IMPORTANT]
> Il token deve essere `https://api.timeseries.azure.com/` rilasciato esattamente alla risorsa (noto anche come "audience" del token).
> * Il [postman](https://www.getpostman.com/) **AuthURL** sarà pertanto:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`è valido `https://api.timeseries.azure.com` ma non lo è.

Le intestazioni di richiesta facoltative sono descritte di seguito.

| Intestazione di richiesta facoltativa | Descrizione |
| --- | --- |
| Tipo di contenuto | è `application/json` supportato solo. |
| x-ms-client-request-id | Un ID richiesta client. Il servizio registra questo valore. Consente al servizio di tracciare il funzionamento tra i servizi. |
| x-ms-client-session-id | Un ID di sessione client. Il servizio registra questo valore. Consente al servizio di tracciare un gruppo di operazioni correlate tra i servizi. |
| x-ms-client-nomeapplicazione | Nome dell'applicazione che ha generato la richiesta. Il servizio registra questo valore. |

Le intestazioni di risposta facoltative ma consigliate sono descritte di seguito.

| Intestazione risposta | Descrizione |
| --- | --- |
| Tipo di contenuto | È supportato solo `application/json`. |
| x-ms-request-id | ID richiesta generato dal server. Può essere utilizzato per contattare Microsoft per indagare su una richiesta. |
| x-ms-property-not-found-behavior | Intestazione di risposta facoltativa dell'API GA. I valori `ThrowError` possibili sono `UseNull`(impostazione predefinita) o . |

### <a name="http-parameters"></a>Parametri HTTP

> [!TIP]
> Per ulteriori informazioni sulle query obbligatorie e facoltative, fare riferimento alla documentazione di [riferimento.](https://docs.microsoft.com/rest/api/time-series-insights/)

I parametri obbligatori della stringa di query dell'URL dipendono dalla versione dell'API.

| Versione | Possibili valori di versione dell'API |
| --- |  --- |
| Disponibilità generale | `api-version=2016-12-12`|
| Anteprima | `api-version=2018-11-01-preview` |
| Anteprima | `api-version=2018-08-15-preview` |

I parametri facoltativi della stringa di query dell'URL includono l'impostazione di un timeout per i tempi di esecuzione delle richieste HTTP.

| Parametro di query facoltativo | Descrizione | Versione |
| --- |  --- | --- |
| `timeout=<timeout>` | Timeout sul lato server per l'esecuzione della richiesta HTTP. Applicabile solo alle API [Get Environment Events](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) e Get Environment [Aggregates.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) Il valore di timeout deve essere nel formato `"PT20S"` di durata ISO `1-30 s`8601, ad esempio e deve essere compreso nell'intervallo . Il valore predefinito è `30 s`. | GA |
| `storeType=<storeType>` | Per gli ambienti di anteprima con l'archivio `WarmStore` a `ColdStore`caldo abilitato, la query può essere eseguita su o . Questo parametro nella query definisce in quale archivio deve essere eseguita la query. Se non è definita, la query verrà eseguita sull'archivio a freddo. Per eseguire una query sull'archivio `WarmStore`a caldo, **storeType** deve essere impostato su . Se non è definita, la query verrà eseguita sull'archivio a freddo. | Anteprima |

## <a name="next-steps"></a>Passaggi successivi

- Per il codice di esempio che chiama l'API GA Time Series Insights, leggere [Query data using C'](./time-series-insights-query-data-csharp.md).

- Per gli esempi di codice dell'API di Preview Time Series Insights, leggere [Dati di Anteprima query con C .](./time-series-insights-update-query-data-csharp.md)

- Per informazioni di riferimento sull'API, leggere la documentazione di [riferimento dell'API di query.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).
