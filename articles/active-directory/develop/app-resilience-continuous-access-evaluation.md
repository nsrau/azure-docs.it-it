---
title: Come usare le API abilitate per la valutazione dell'accesso continuo nelle applicazioni | Azure
titleSuffix: Microsoft identity platform
description: Come aumentare la sicurezza e la resilienza delle app aggiungendo il supporto per la valutazione dell'accesso continuo, abilitando token di accesso di lunga durata che possono essere revocati in base a eventi critici e valutazione dei criteri.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 86c379316737b7718b62165a6feb93ca3a0e9954
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484040"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Come usare le API abilitate per la valutazione dell'accesso continuo nelle applicazioni

La [valutazione di accesso continuo](../conditional-access/concept-continuous-access-evaluation.md) (CAE) è uno standard di settore emergente che consente di revocare i token di accesso in base agli [eventi critici](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) e alla [valutazione dei criteri](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) anziché basarsi sulla scadenza dei token in base alla durata. Per alcune API delle risorse, poiché il rischio e i criteri vengono valutati in tempo reale, è possibile che la durata dei token aumenti fino a 28 ore. Questi token di lunga durata verranno aggiornati in modo proattivo dalla Microsoft Authentication Library (MSAL), aumentando la resilienza delle applicazioni.

Questo articolo illustra come usare le API abilitate per CAE nelle applicazioni.

## <a name="implementation-considerations"></a>Considerazioni sull'implementazione

Per usare la valutazione di accesso continuo, sia l'app che l'API delle risorse a cui accede devono essere abilitate per la funzionalità CAE. Tuttavia, la preparazione del codice per l'uso di una risorsa abilitata per il CAE non impedirà l'uso di API che non sono abilitate per il CAE.

Se un'API della risorsa implementa CAE e l'applicazione dichiara che può gestire il CAE, l'app otterrà i token CAE per tale risorsa. Per questo motivo, se si dichiara che il CAE dell'app è pronto, l'applicazione deve gestire la richiesta di attestazione CAE per tutte le API delle risorse che accettano i token di accesso di Microsoft Identity. Se non si gestiscono le risposte CAE in queste chiamate API, l'app potrebbe finire in un ciclo ritentando una chiamata API con un token che si trova ancora nella durata del token restituita, ma è stato revocato a causa di CAE.

## <a name="the-code"></a>Codice

Il primo passaggio consiste nell'aggiungere il codice per gestire una risposta dall'API di risorsa che rifiuta la chiamata a causa di CAE. Con CAE, le API restituiranno uno stato 401 e un'intestazione WWW-Authenticate quando il token di accesso è stato revocato o l'API rileva una modifica nell'indirizzo IP usato. L'intestazione WWW-Authenticate contiene una richiesta di attestazione che l'applicazione può usare per acquisire un nuovo token di accesso.

Ad esempio:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

L'app verificherà quanto segue:

- chiamata API che restituisce lo stato 401
- esistenza di un'intestazione WWW-Authenticate contenente:
  - parametro "Error" con il valore "insufficient_claims"
  - parametro "claims"

Quando vengono soddisfatte queste condizioni, l'app può estrarre e decodificare la richiesta di attestazioni.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

L'app userà quindi la richiesta di attestazioni per acquisire un nuovo token di accesso per la risorsa.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Quando l'applicazione è pronta per gestire la richiesta di attestazione restituita da una risorsa abilitata per CAE, è possibile indicare a Microsoft Identity che l'app è pronta per il CAE. Per eseguire questa operazione nell'applicazione MSAL, compilare il client pubblico usando le funzionalità client di "CP1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

È possibile testare l'applicazione eseguendo l'accesso di un utente all'applicazione, quindi usando il portale di Azure per revocare le sessioni dell'utente. Alla successiva chiamata dell'API abilitata per il CAE, all'utente verrà chiesto di eseguire di nuovo l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [valutazione dell'accesso continuo](../conditional-access/concept-continuous-access-evaluation.md).
