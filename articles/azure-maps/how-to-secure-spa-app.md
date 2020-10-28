---
title: Come proteggere un'applicazione a pagina singola con accesso non interattivo
titleSuffix: Azure Maps
description: Come configurare un'applicazione a pagina singola con il controllo degli accessi in base al ruolo di Azure non interattivo (RBAC di Azure) e Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: c39104912c99b199d38cf489bb61d64e83b89286
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895597"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Come proteggere un'applicazione a pagina singola con accesso non interattivo

La guida seguente è relativa a un'applicazione che usa Azure Active Directory (Azure AD) per fornire un token di accesso alle applicazioni di Azure Maps quando l'utente non riesce ad accedere a Azure AD. Questo flusso richiede l'hosting di un servizio Web che deve essere protetto solo per l'accesso da parte dell'applicazione Web a pagina singola. Sono disponibili più implementazioni che consentono di eseguire l'autenticazione per Azure AD. Questa guida sfrutta il prodotto, funzione di Azure per acquisire i token di accesso.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Mappe di Azure può supportare i token di accesso dai flussi di accesso/interattivo dell'utente. I flussi interattivi consentono un ambito più limitato di revoca di accesso e gestione dei segreti.

## <a name="create-azure-function"></a>Creare la funzione di Azure

Creare un'applicazione di servizio Web protetta responsabile dell'autenticazione per Azure AD. 

1. Creare una funzione nell'portale di Azure. Per altre informazioni, vedere [creare funzioni di Azure](../azure-functions/functions-create-first-azure-function.md).

2. Configurare i criteri CORS nella funzione di Azure in modo che siano accessibili tramite l'applicazione Web a pagina singola. In questo modo i client del browser potranno proteggere le origini consentite dell'applicazione Web. Vedere [aggiungere la funzionalità CORS](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Aggiungere un'identità assegnata dal sistema](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) nella funzione di Azure per consentire la creazione di un'entità servizio per l'autenticazione Azure ad.  

4. Concedere l'accesso in base al ruolo per l'identità assegnata dal sistema all'account Azure maps. Per informazioni dettagliate, vedere [concedere l'accesso in base al ruolo](#grant-role-based-access) .

5. Scrivere il codice per la funzione di Azure per ottenere i token di accesso di Azure Maps usando l'identità assegnata dal sistema con uno dei meccanismi supportati o il protocollo REST. Vedere [ottenere i token per le risorse di Azure](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Esempio di protocollo REST di esempio:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Risposta di esempio:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Configurare la sicurezza per la funzione di Azure HttpTrigger

   * [Creare una chiave di accesso alla funzione](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * [Endpoint HTTP sicuro](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) per la funzione di Azure nell'ambiente di produzione.
   
7. Configurare l'applicazione Web Azure Maps Web SDK. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Concedere l'accesso in base al ruolo

Si concede l'accesso al *controllo degli accessi in base al ruolo* di Azure, assegnando l'identità assegnata dal sistema a una o più definizioni di ruolo di Azure. Per visualizzare le definizioni dei ruoli di Azure disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)** . Selezionare **Roles (ruoli** ) e quindi cercare i ruoli che iniziano con *Maps di Azure* .

1. Passare all' **account Azure Maps** . Selezionare l'assegnazione di ruolo **controllo di accesso (IAM)**  >  **Role assignment** .

    > [!div class="mx-imgBorder"]
    > ![Concedi l'accesso con RBAC di Azure](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Nella scheda **assegnazioni di ruolo** , in **ruolo** , selezionare una definizione di ruolo predefinita di Azure Maps, ad esempio **lettore dati** di Azure Maps o **collaboratore dati di Azure Maps** . In **assegna accesso a** selezionare **app per le funzioni** . Selezionare l'entità in base al nome. Selezionare quindi **Salva** .

   * Vedere informazioni dettagliate su come [aggiungere o rimuovere assegnazioni di ruolo](../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Le definizioni di ruolo predefinite di mappe di Azure forniscono un accesso di autorizzazione molto grande a molte API REST di Azure maps. Per limitare l'accesso alle API almeno, vedere [creare una definizione di ruolo personalizzata e assegnare l'identità assegnata dal sistema](../role-based-access-control/custom-roles.md) alla definizione di ruolo personalizzata. Questo consentirà il privilegio minimo necessario all'applicazione per accedere ad Azure maps.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sullo scenario di applicazione a pagina singola:
> [!div class="nextstepaction"]
> [Applicazione a pagina singola](../active-directory/develop/scenario-spa-overview.md)

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Esplorare altri esempi che illustrano come integrare Azure AD con mappe di Azure:
> [!div class="nextstepaction"]
> [Esempi di mappe di Azure](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)