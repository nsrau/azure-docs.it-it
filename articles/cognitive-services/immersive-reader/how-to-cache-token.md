---
title: Memorizzare il token di autenticazione nella cache
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come memorizzare nella cache il token di autenticazione.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946166"
---
# <a name="how-to-cache-the-authentication-token"></a>Come memorizzare nella cache il token di autenticazioneHow to cache the authentication token

In questo articolo viene illustrato come memorizzare nella cache il token di autenticazione per migliorare le prestazioni dell'applicazione.

## <a name="using-aspnet"></a>Utilizzo di ASP.NET

Importare il pacchetto **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, utilizzato per acquisire un token. Successivamente, utilizzare il codice `AuthenticationResult`seguente per acquisire un oggetto , utilizzando i valori di autenticazione ottenuti al momento [della creazione della risorsa Lettore immersivo](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

L'oggetto `AuthenticationResult` `AccessToken` dispone di una proprietà che è il token effettivo che verrà utilizzato quando si avvia Immersive Reader utilizzando l'SDK. Ha anche `ExpiresOn` una proprietà che indica quando il token scadrà. Prima di avviare il lettore immersivo, è possibile verificare se il token è scaduto e acquisire un nuovo token solo se è scaduto.

## <a name="using-nodejs"></a>Utilizzo di Node.JS

Aggiungere il pacchetto della [**richiesta**](https://www.npmjs.com/package/request) npm al progetto. Utilizzare il codice seguente per acquisire un token, utilizzando i valori di autenticazione ottenuti al momento [della creazione della risorsa Lettore immersivo](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

La `expires_on` proprietà è la data e l'ora di scadenza del token, espresse come numero di secondi dal 1 gennaio 1970 UTC. Utilizzare questo valore per determinare se il token è scaduto prima di tentare di acquisire uno nuovo.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)