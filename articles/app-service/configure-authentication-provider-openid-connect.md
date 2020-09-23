---
title: Configurare un provider OpenID Connect (anteprima)
description: Informazioni su come configurare un provider OpenID Connect come provider di identità per il servizio app o l'app funzioni di Azure.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983877"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Configurare il servizio app o l'app Funzioni di Azure per l'accesso tramite un provider OpenID Connect (anteprima)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare un provider di autenticazione personalizzato che rispetta la specifica di [OpenID Connect](https://openid.net/connect/). OpenID Connect (OIDC) è uno standard di settore usato da molti provider di identità (IDP). Non è necessario comprendere i dettagli della specifica per configurare l'app in modo da usare un IDP aderente.

Il può configurare l'app per l'uso di uno o più provider OIDC. Alla configurazione deve essere assegnato un nome univoco e solo uno può fungere da destinazione di reindirizzamento predefinito.

> [!CAUTION]
> L'abilitazione di un provider OpenID Connect Disabilita la gestione della funzionalità di autenticazione/autorizzazione del servizio app per l'applicazione tramite alcuni client, ad esempio portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell. La funzionalità si basa su una nuova superficie API che, durante l'anteprima, non è ancora stata contabilizzata in tutte le esperienze di gestione.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrare l'applicazione con il provider di identità

Per il provider sarà necessario registrare i dettagli dell'applicazione. Vedere le istruzioni relative a tale provider. Sarà necessario raccogliere un **ID client** e un **segreto client** per l'applicazione.

> [!IMPORTANT]
> Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
>

> [!NOTE]
> Alcuni provider possono richiedere passaggi aggiuntivi per la configurazione e come usare i valori che forniscono. Ad esempio, Apple fornisce una chiave privata che non viene usata come segreto client OIDC ed è invece necessario usare un JWT che viene considerato come il segreto fornito nella configurazione dell'app (vedere la sezione "creazione del segreto client" della [documentazione relativa all'accesso con Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)).
>

Aggiungere il segreto client come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) per l'app, usando un nome di impostazione scelto. Prendere nota di questo nome per un momento successivo.

Sono inoltre necessari i metadati di OpenID Connect per il provider. Spesso viene esposto tramite un [documento di metadati di configurazione](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), ovvero l'URL dell'autorità emittente del provider con suffisso `/.well-known/openid-configuration` . Raccogliere questo URL di configurazione.

Se non si riesce a usare un documento di metadati di configurazione, sarà necessario raccogliere separatamente i valori seguenti:

- URL dell'autorità emittente (talvolta visualizzato come `issuer` )
- [Endpoint di autorizzazione OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.1) (talvolta visualizzato come `authorization_endpoint` )
- [Endpoint del token OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.2) (talvolta visualizzato come `token_endpoint` )
- URL del documento del [set di chiavi Web JSON OAuth 2,0](https://tools.ietf.org/html/rfc8414#section-2) (talvolta visualizzato come `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Aggiungere informazioni sul provider all'applicazione

> [!NOTE]
> La configurazione richiesta è in un nuovo formato API, attualmente supportata solo dalla [configurazione basata su file (anteprima)](.\app-service-authentication-how-to.md#config-file). È necessario attenersi alla procedura seguente usando tale file.

In questa sezione viene illustrato come aggiornare la configurazione per includere il nuovo IDP. Di seguito è riportata una configurazione di esempio.

1. All'interno dell' `identityProviders` oggetto aggiungere un `openIdConnectProviders` oggetto, se non ne esiste già uno.
1. All'interno dell' `openIdConnectProviders` oggetto aggiungere una chiave per il nuovo provider. Si tratta di un nome descrittivo utilizzato per fare riferimento al provider nel resto della configurazione. Se, ad esempio, si desidera che tutte le richieste vengano autenticate con questo provider, impostare `globalValidation.unauthenticatedClientAction` su "RedirectToLoginPage" e impostare `redirectToProvider` su questo stesso nome descrittivo.
1. Assegnare un oggetto alla chiave con un `registration` oggetto al suo interno e, facoltativamente, un `login` oggetto:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. All'interno dell'oggetto di registrazione, impostare sull' `clientId` ID client raccolto, impostare sul `clientCredential.secretSettingName` nome dell'impostazione dell'applicazione in cui è stato archiviato il segreto client e creare un `openIdConnectConfiguration` oggetto:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. All'interno dell' `openIdConnectConfiguration` oggetto specificare i metadati di OpenID Connect raccolti in precedenza. Sono disponibili due opzioni, in base alle informazioni raccolte:

    - Impostare la `wellKnownOpenIdConfiguration` proprietà sull'URL dei metadati di configurazione raccolto in precedenza.
    - In alternativa, impostare i quattro singoli valori raccolti come segue:
        - Imposta `issuer` sull'URL dell'autorità emittente
        - Imposta `authorizationEndpoint` sull'endpoint di autorizzazione
        - Impostare `tokenEndpoint` sull'endpoint del token
        - Impostare `certificationUri` sull'URL del documento del set di chiavi Web JSON

    Queste due opzioni si escludono a vicenda.

Una volta impostata questa configurazione, si è pronti per usare il provider OpenID Connect per l'autenticazione nell'app.

Una configurazione di esempio può essere simile alla seguente (usando l'accesso con Apple come esempio, in cui l'impostazione APPLE_GENERATED_CLIENT_SECRET punta a un JWT generato come per la [documentazione di Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
