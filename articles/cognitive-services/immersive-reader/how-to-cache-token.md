---
title: Memorizzare il token di autenticazione nella cache
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come memorizzare nella cache il token di autenticazione.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946166"
---
# <a name="how-to-cache-the-authentication-token"></a>Come memorizzare nella cache il token di autenticazione

Questo articolo illustra come memorizzare nella cache il token di autenticazione per migliorare le prestazioni dell'applicazione.

## <a name="using-aspnet"></a>Uso di ASP.NET

Importare il pacchetto NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** , usato per acquisire un token. Usare quindi il codice seguente per acquisire un oggetto `AuthenticationResult`, usando i valori di autenticazione ottenuti quando è stata [creata la risorsa Reader immersiva](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

L' `AuthenticationResult` oggetto ha una `AccessToken` proprietà che è il token effettivo che verrà usato quando si avvia il Reader immersivo usando l'SDK. Dispone inoltre di una `ExpiresOn` proprietà che indica quando scadrà il token. Prima di avviare il Reader immersivo, è possibile verificare se il token è scaduto e acquisire un nuovo token solo se è scaduto.

## <a name="using-nodejs"></a>Uso di node. JS

Aggiungere il pacchetto NPM della [**richiesta**](https://www.npmjs.com/package/request) al progetto. Usare il codice seguente per acquisire un token, usando i valori di autenticazione ottenuti quando è stata [creata la risorsa Reader immersiva](./how-to-create-immersive-reader.md).

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

La `expires_on` proprietà è la data e l'ora in cui il token scade, espresso come numero di secondi a partire dal 1 ° gennaio 1970 UTC. Usare questo valore per determinare se il token è scaduto prima di tentare di acquisirne uno nuovo.

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