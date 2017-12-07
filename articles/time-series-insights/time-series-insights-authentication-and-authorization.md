---
title: Come configurare l'autenticazione e l'autorizzazione per l'API in Azure Time Series Insights
description: Questo articolo descrive come configurare l'autenticazione e l'autorizzazione per un'applicazione personalizzata che chiama l'API Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: dd78e1e726029aaceef5aff0e0eed84acac646cf
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticazione e autorizzazione per l'API Azure Time Series Insights

Questo articolo descrive come configurare l'autenticazione e l'autorizzazione usata in un'applicazione personalizzata che chiama l'API Azure Time Series Insights.

## <a name="service-principal"></a>Entità servizio

Questa sezione illustra come configurare un'applicazione per accedere all'API Time Series Insights per conto dell'applicazione. L'applicazione può quindi eseguire query sui dati o pubblicare dati di riferimento nell'ambiente Time Series Insights con le credenziali dell'applicazione e non con quelle dell'utente.

Quando un'applicazione deve accedere a Time Series Insights, è necessario configurare un'applicazione Azure Active Directory e assegnare i criteri di accesso ai dati nell'ambiente Time Series Insights. Questo approccio è preferibile all'esecuzione dell'app con le credenziali dell'utente per i motivi seguenti:

* È possibile assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle operazioni necessarie per l'app. Ad esempio, è possibile consentire all'app solo di leggere i dati in un particolare ambiente Time Series Insights.
* Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente.
* È possibile usare un certificato o una chiave dell'applicazione per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.

Questo articolo illustra come eseguire questa procedura tramite il portale di Azure. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.

Il flusso di configurazione è costituito da tre passaggi generali:

1. Creare un'applicazione in Azure Active Directory.
2. Autorizzare questa applicazione ad accedere all'ambiente Time Series Insights.
3. Usare l'ID e la chiave dell'applicazione per acquisire un token per un destinatario o una risorsa `"https://api.timeseries.azure.com/"`. Il token può quindi essere usato per chiamare l'API Time Series Insights.

Ecco di seguito i passaggi dettagliati:

1. Nel portale di Azure selezionare **Azure Active Directory** > **Registrazioni per l'app** > **Registrazione nuova applicazione**.

   ![Registrazione di una nuova applicazione in Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Assegnare un nome all'applicazione, selezionare il tipo **App Web/API**, selezionare un URI valido in **URL di accesso** e fare clic su **Crea**.

   ![Creare l'applicazione in Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Selezionare l'applicazione appena creata e copiare il relativo ID in un editor di testo.

   ![Copiare l'ID applicazione](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Selezionare **Chiavi**, immettere il nome della chiave, selezionare la scadenza e fare clic su **Salva**.

   ![Selezionare le chiavi dell'applicazione](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Immettere il nome e la scadenza della chiave e fare clic su Salva](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copiare la chiave in un editor di testo.

   ![Copiare la chiave dell'applicazione](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Per l'ambiente Time Series Insights, selezionare **Criteri di accesso ai dati** e fare clic su **Aggiungi**.

   ![Aggiungere nuovi criteri di accesso ai dati per l'ambiente Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Nella finestra di dialogo **Seleziona utente** incollare il nome dell'applicazione (dal passaggio 2) o l'ID dell'applicazione (dal passaggio 3).

   ![Trovare un'applicazione nella finestra di dialogo Seleziona utente](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Selezionare il ruolo (**Lettore** per eseguire query sui dati, **Collaboratore** per eseguire query sui dati e modificare i dati di riferimento) e fare clic su **Ok**.

   ![Selezionare Lettore o Collaboratore nella finestra di dialogo Selezionare un ruolo](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Salvare il criterio facendo clic su **Ok**.

10. Usare l'ID dell'applicazione (dal passaggio 3) e la chiave dell'applicazione (dal passaggio 5) per acquisire il token per conto dell'applicazione. Il token può quindi essere passato nell'intestazione `Authorization` quando l'applicazione chiama l'API Time Series Insights.

    Se si usa C#, è possibile usare il codice seguente per acquisire il token per conto dell'applicazione. Per un esempio completo, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Usare l'ID e la chiave dell'applicazione per eseguire l'autenticazione con Azure Time Series Insight. 

## <a name="next-steps"></a>Passaggi successivi
- Per un esempio di codice che chiama l'API Time Series Insights, vedere [Eseguire query sui dati tramite C#](time-series-insights-query-data-csharp.md).
- Per informazioni di riferimento sull'API, vedere il [riferimento all'API di query](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Creare un'entità servizio](../azure-resource-manager/resource-group-create-service-principal-portal.md)
