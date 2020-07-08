---
title: Come proteggere un'applicazione a pagina singola con l'accesso utente
titleSuffix: Azure Maps
description: Come configurare un'applicazione a pagina singola che supporta Azure AD Single Sign-on con Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 307f1ea8d98aec8594dd4f666d4e18922122b0f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988436"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Proteggere un'applicazione a pagina singola con accesso utente

La guida seguente riguarda un'applicazione ospitata in un server di contenuti o con dipendenze minime del server Web. L'applicazione fornisce risorse protette protette solo per gli utenti Azure AD. L'obiettivo dello scenario è consentire all'applicazione Web di eseguire l'autenticazione a Azure AD e chiamare le API REST di Azure Maps per conto dell'utente.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Creare una registrazione dell'applicazione in Azure AD

Creare l'applicazione Web in Azure AD per consentire agli utenti di effettuare l'accesso. L'applicazione Web delega l'accesso utente alle API REST di Azure maps.

1. Nell'elenco dei servizi di Azure portale di Azure selezionare **Azure Active Directory**  >  **registrazioni app**  >  **nuova registrazione**.  

    > [!div class="mx-imgBorder"]
    > ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

2. Immettere un **nome**, scegliere un **tipo di account di supporto**, specificare un URI di reindirizzamento che rappresenterà l'URL che Azure ad emetterà il token ed è l'URL in cui è ospitato il controllo mappa. Per un esempio dettagliato, vedere [Azure Maps Azure ad esempi](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Selezionare **Registra**.  

3. Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione. In **registrazioni app**selezionare **autorizzazioni API**  >  **Aggiungi un'autorizzazione**. In **API l'organizzazione USA**, cercare e selezionare **mappe di Azure**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

4. Selezionare la casella di controllo accanto a **Accedi a mappe di Azure**e quindi selezionare **Aggiungi autorizzazioni**.

    > [!div class="mx-imgBorder"]
    > ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

5. Abilitare `oauth2AllowImplicitFlow`. Per abilitarla, nella sezione **manifesto** della registrazione dell'app, impostare `oauth2AllowImplicitFlow` su `true` .

6. Copiare il Azure AD ID app e l'ID tenant Azure AD dalla registrazione dell'app da usare in Web SDK. Aggiungere i dettagli di registrazione dell'app Azure AD e `x-ms-client-id` dall'account mappa di Azure a Web SDK.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Configurare il controllo degli accessi in base al ruolo di Azure per utenti o gruppi. [Per abilitare RBAC](#grant-role-based-access-for-users-to-azure-maps), vedere le sezioni seguenti.
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sullo scenario di applicazione a pagina singola:
> [!div class="nextstepaction"]
> [Applicazione a pagina singola](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:
> [!div class="nextstepaction"]
> [Esempi di mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
