---
title: Convalida del token di accesso dell'API di Azure per FHIR
description: Esamina la convalida dei token e fornisce suggerimenti su come risolvere i problemi di accesso
services: healthcare-apis
author: caitlinv39
ms.reviewer: mihansen
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 672acd4f68acd4998fd7631240c2f998299def11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871231"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Convalida del token di accesso dell'API di Azure per FHIR

Il modo in cui l'API di Azure per FHIR convalida il token di accesso dipende dall'implementazione e dalla configurazione. In questo articolo verranno illustrati i passaggi di convalida che possono risultare utili per la risoluzione dei problemi di accesso.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Il token di convalida non presenta problemi con il provider di identità

Il primo passaggio della convalida del token consiste nel verificare che il token sia stato emesso dal provider di identità corretto e che non sia stato modificato. Il server FHIR verrà configurato per l'uso di un provider di identità specifico noto come autorità `Authority` . Il server FHIR recupererà le informazioni sul provider di identità dall' `/.well-known/openid-configuration` endpoint. Quando si usa Azure AD, l'URL completo è:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

dove `<TENANT-ID>` è il tenant di Azure ad specifico, ovvero un ID tenant o un nome di dominio.

Azure AD restituirà un documento simile a quello riportato di seguito al server FHIR.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Le proprietà importanti per il server FHIR sono `jwks_uri` , che indica al server dove recuperare le chiavi di crittografia necessarie per convalidare la firma del token e `issuer` , che indica al server quale sarà l'attestazione dell'autorità emittente ( `iss` ) dei token emessi da questo server. Il server FHIR può usarlo per verificare che riceva un token autentico.

## <a name="validate-claims-of-the-token"></a>Convalidare le attestazioni del token

Dopo la verifica dell'autenticità del token da parte del server, il server FHIR proseguirà per verificare che il client disponga delle attestazioni necessarie per accedere al token.

Quando si usa l'API di Azure per FHIR, il server esegue la convalida:

1. Il token ha il diritto `Audience` ( `aud` attestazione).
1. L'utente o l'entità a cui è stato emesso il token è autorizzato ad accedere al piano dati del server FHIR. L' `oid` attestazione del token contiene un ID oggetto identità che identifica in modo univoco l'utente o l'entità.

È consigliabile configurare il servizio FHIR [per usare](configure-azure-rbac.md) il controllo degli accessi in base al ruolo di Azure per gestire le assegnazioni dei ruoli del piano dati. È anche possibile configurare il controllo degli accessi in base al ruolo [locale](configure-local-rbac.md) se il servizio FHIR usa un tenant Azure Active Directory esterno o secondario. 

Quando si usa il server OSS Microsoft FHIR per Azure, il server convaliderà:

1. Il token ha il diritto `Audience` ( `aud` attestazione).
1. Il token ha un ruolo nell' `roles` attestazione, a cui è consentito l'accesso al server FHIR.

Vedere i dettagli su come [definire i ruoli nel server FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Un server FHIR può inoltre verificare che un token di accesso disponga degli ambiti (in attestazione token `scp` ) per accedere alla parte dell'API FHIR a cui un client sta tentando di accedere. Attualmente, l'API di Azure per FHIR e il server FHIR per Azure non convalidano gli ambiti del token.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come esaminare la convalida dei token, è possibile completare l'esercitazione per creare un'applicazione JavaScript e leggere i dati FHIR.

>[!div class="nextstepaction"]
>[Esercitazione sull'applicazione Web](tutorial-web-app-fhir-server.md)