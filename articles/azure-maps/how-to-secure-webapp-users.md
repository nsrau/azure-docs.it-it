---
title: Come proteggere un'applicazione Web con l'accesso Single Sign-on interattivo
titleSuffix: Azure Maps
description: Come configurare un'applicazione Web che supporta Azure AD Single Sign-on con Azure Maps Web SDK usando il protocollo OpenID Connect.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895587"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Proteggere un'applicazione Web con l'accesso utente

La guida seguente è relativa a un'applicazione ospitata su server Web, gestisce più scenari aziendali e viene distribuita nei server Web. L'applicazione ha la necessità di fornire risorse protette protette solo a Azure AD utenti. L'obiettivo dello scenario è consentire all'applicazione Web di eseguire l'autenticazione a Azure AD e chiamare le API REST di Azure Maps per conto dell'utente.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Creare una registrazione dell'applicazione in Azure AD

Per consentire agli utenti di eseguire l'accesso, è necessario creare l'applicazione Web in Azure AD. Questa applicazione Web delegherà quindi l'accesso utente alle API REST di Azure maps.

1. Nell'elenco dei servizi di Azure portale di Azure selezionare **Azure Active Directory**  >  **registrazioni app**  >  **nuova registrazione** .  

    > [!div class="mx-imgBorder"]
    > ![Registrazione delle app](./media/how-to-manage-authentication/app-registration.png)

2. Immettere un **nome** , scegliere un **tipo di account di supporto** , specificare un URI di reindirizzamento che rappresenterà l'URL che Azure ad emetterà il token ed è l'URL in cui è ospitato il controllo mappa. Per altri dettagli, vedere Azure AD [scenario: app Web che esegue l'accesso agli utenti](../active-directory/develop/scenario-web-app-sign-user-overview.md). Completare i passaggi forniti dallo scenario Azure AD.  

3. Al termine della registrazione dell'applicazione, verificare che l'accesso all'applicazione funzioni per gli utenti. Una volta eseguito l'accesso, all'applicazione è possibile concedere l'accesso delegato alle API REST di Azure maps.
    
4.  Per assegnare le autorizzazioni per le API delegate a mappe di Azure, passare all'applicazione. Quindi selezionare **autorizzazioni API**  >  **Aggiungi un'autorizzazione** . In **API l'organizzazione USA** , cercare e selezionare **mappe di Azure** .

    > [!div class="mx-imgBorder"]
    > ![Aggiungi autorizzazioni API app](./media/how-to-manage-authentication/app-permissions.png)

5. Selezionare la casella di controllo accanto a **Accedi a mappe di Azure** e quindi selezionare **Aggiungi autorizzazioni** .

    > [!div class="mx-imgBorder"]
    > ![Selezionare le autorizzazioni dell'API per le app](./media/how-to-manage-authentication/select-app-permissions.png)

6. Abilitare l'applicazione Web per chiamare le API REST di Azure Maps configurando la registrazione dell'app con un segreto dell'applicazione. per i passaggi dettagliati, vedere [un'app Web che chiama API Web: registrazione dell'app](../active-directory/develop/scenario-web-app-call-api-app-registration.md). Per eseguire l'autenticazione Azure AD per conto dell'utente, è necessario un segreto. Il certificato o il segreto per la registrazione dell'app deve essere archiviato in un archivio sicuro per consentire all'applicazione Web di recuperare per l'autenticazione Azure AD. 
   
   * Se l'applicazione ha già configurato un Azure AD la registrazione dell'app e un segreto, questo passaggio può essere ignorato.

> [!Tip]
> Se l'applicazione è ospitata in un ambiente Azure, è consigliabile usare [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) e un'istanza di Azure Key Vault per accedere ai segreti [acquisendo un token di accesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) per l'accesso ai segreti Azure Key Vault o ai certificati. Per connettersi a Azure Key Vault per recuperare i segreti, vedere [esercitazione per la connessione tramite identità gestita](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Implementare un endpoint del token di sicurezza per Azure Maps Web SDK per accedere a un token. 
   
   * Per un controller di token di esempio, vedere [Azure Maps Azure ad esempi](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Per un'implementazione non AspNetCore o un altro, vedere [acquisire il token per l'app](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) da Azure ad documentazione.
   * L'endpoint del token protetto è responsabile della restituzione di un token di accesso per l'utente autenticato e autorizzato per chiamare le API REST di Azure maps.

8. Configurare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per utenti o gruppi. Vedere [concedere l'accesso in base al ruolo per gli utenti](#grant-role-based-access-for-users-to-azure-maps).

9. Configurare la pagina dell'applicazione Web con Azure Maps Web SDK per accedere all'endpoint del token di sicurezza. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sullo scenario dell'applicazione Web:
> [!div class="nextstepaction"]
> [Scenario: App Web per l'accesso degli utenti](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:
> [!div class="nextstepaction"]
> [Esempi di app Web di Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)