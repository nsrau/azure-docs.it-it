---
title: Come configurare l'autenticazione e l'autorizzazione per l'API in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237044"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo articolo descrive come configurare l'autenticazione e l'autorizzazione usata in un'applicazione personalizzata che chiama l'API Azure Time Series Insights.

> [!TIP]
> Conoscenza [concedere l'accesso ai dati](./time-series-insights-data-access.md) all'ambiente Time Series Insights in Azure Active Directory.

## <a name="service-principal"></a>Entità servizio

Questa e le sezioni seguenti descrivono come configurare un'applicazione per l'API Time Series Insights di accesso per conto dell'applicazione. L'applicazione può quindi eseguire una query o pubblicare i dati di riferimento nell'ambiente Time Series Insights con le credenziali dell'applicazione anziché con le credenziali dell'utente.

## <a name="best-practices"></a>Procedure consigliate

Se si ha un'applicazione che deve accedere a Time Series Insights:

1. Configurare un'app di Azure Active Directory.
1. [Assegnare i criteri di accesso dati](./time-series-insights-data-access.md) nell'ambiente di Time Series Insights.

L'uso di applicazione anziché con le credenziali dell'utente è utile perché:

* È possibile assegnare autorizzazioni all'identità dell'app che sono diverse dalle proprie autorizzazioni. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, è possibile consentire all'app solo di leggere i dati in un particolare ambiente Time Series Insights.
* Si è autorizzati a modificare le credenziali dell'app, se vostre responsabilità dovessero cambiare.
* È possibile usare un certificato o una chiave dell'applicazione per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.

Le sezioni seguenti illustrano come eseguire questa procedura tramite il portale di Azure. L'articolo è incentrato su un'applicazione a tenant singolo in cui l'applicazione deve essere eseguito in una sola organizzazione. Si userà in genere le applicazioni a tenant singolo per le applicazioni line-of-business eseguite all'interno dell'organizzazione.

## <a name="set-up-summary"></a>Configurazione di riepilogo

Il flusso di programma di installazione è costituito da tre passaggi:

1. Creare un'applicazione in Azure Active Directory.
1. Autorizzare questa applicazione ad accedere all'ambiente Time Series Insights.
1. Usare l'ID applicazione e la chiave per acquisire un token da `https://api.timeseries.azure.com/`. Il token può quindi essere usato per chiamare l'API Time Series Insights.

## <a name="detailed-setup"></a>Programma di installazione dettagliata

1. Nel portale di Azure selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

   [![Registrazione nuova applicazione in Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Assegnare un nome all'applicazione, selezionare il tipo **App Web/API**, selezionare un URI valido in **URL di accesso** e fare clic su **Crea**.

   [![Creare l'applicazione in Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Selezionare l'applicazione appena creata e copiare il relativo ID in un editor di testo.

   [![Copiare l'ID applicazione](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Selezionare **Chiavi**, immettere il nome della chiave, selezionare la scadenza e fare clic su **Salva**.

   [![Selezionare le chiavi dell'applicazione](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Immettere il nome della chiave e la scadenza e fare clic su Salva](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Copiare la chiave in un editor di testo.

   [![Copiare la chiave dell'applicazione](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Per l'ambiente Time Series Insights, selezionare **Criteri di accesso ai dati** e fare clic su **Aggiungi**.

   [![Aggiungere nuovi criteri di accesso di dati per l'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Nel **Seleziona utente** finestra di dialogo incollare il nome dell'applicazione (dal **passaggio 2**) o l'ID applicazione (dal **passaggio 3**).

   [![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selezionare il ruolo (**Reader** per eseguire query sui dati, **collaboratore** per eseguire query sui dati e la modifica dei dati di riferimento) e fare clic su **OK**.

   [![Selezionare lettore o collaboratore nella finestra di dialogo Seleziona ruolo](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salvare il criterio facendo clic **OK**.

1. Usare l'ID applicazione (dal **passaggio 3**) e la chiave applicazione (dal **passaggio 5**) per acquisire il token per conto dell'applicazione. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

    Se si usa C#, è possibile usare il codice seguente per acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

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

Usare la **ID applicazione** e **chiave** nell'applicazione per l'autenticazione con Azure Time Series Insights.

## <a name="next-steps"></a>Passaggi successivi

- Per un esempio di codice che chiama l'API Time Series Insights, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

- Per informazioni di riferimento sull'API, vedere il [riferimento all'API di query](/rest/api/time-series-insights/ga-query-api).

- Informazioni su come [creare un'entità servizio](../active-directory/develop/howto-create-service-principal-portal.md).
