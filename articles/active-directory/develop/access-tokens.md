---
title: Token di accesso di Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui token di accesso emessi dagli endpoint Azure AD v 1.0 e Microsoft Identity Platform (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 909c8910a86734b0a34787f75c233975cd3503c3
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518244"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Token di accesso di Microsoft Identity Platform

I token di accesso consentono ai client di chiamare in modo sicuro le API Web protette e vengono usate dalle API Web per eseguire l'autenticazione e l'autorizzazione. Per la specifica OAuth, i token di accesso sono stringhe opache senza un formato set. alcuni provider di identità usano GUID, altri usano i BLOB crittografati. La piattaforma Microsoft Identity usa un'ampia gamma di formati di token di accesso a seconda della configurazione dell'API che accetta il token. Le [API personalizzate registrate dagli sviluppatori](quickstart-configure-app-expose-web-apis.md) nella piattaforma di identità Microsoft possono scegliere tra due formati diversi di token Web JSON (token JWT), denominati "V1" e "V2", e le API sviluppate da Microsoft, come Microsoft Graph o le API in Azure, hanno formati di token proprietari aggiuntivi. Questi formati proprietari possono essere token crittografati, token JWT o token speciali simili a JWT che non verranno convalidati.

I client devono considerare i token di accesso come stringhe opache perché il contenuto del token è destinato solo alla risorsa (l'API). *Solo* a scopo di convalida e debug, gli sviluppatori possono decodificare token JWT usando un sito come [JWT.ms](https://jwt.ms). Tenere presente, tuttavia, che i token ricevuti per un'API Microsoft potrebbero non essere sempre JWT e che non è sempre possibile decodificarli.

Per informazioni dettagliate sul contenuto del token di accesso, i client devono usare i dati di risposta del token restituiti con il token di accesso al client. Quando il client richiede un token di accesso, la piattaforma di identità Microsoft restituisce anche alcuni metadati relativi al token di accesso per l'utilizzo dell'app. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per cui è valido. Questi dati consentono all'app di eseguire la memorizzazione intelligente nella cache dei token di accesso senza la necessità di analizzare il token di accesso stesso.

Vedere le sezioni seguenti per informazioni su come l'API può convalidare e usare le attestazioni all'interno di un token di accesso.  

> [!NOTE]
> Tutta la documentazione in questa pagina, eccetto se indicata, si applica solo ai token emessi per le API registrate.  Non si applica ai token emessi per le API di proprietà di Microsoft, né questi token possono essere usati per convalidare il modo in cui la piattaforma di identità Microsoft rilascia i token per un'API creata.  

## <a name="token-formats-and-ownership"></a>Formati e proprietà del token

### <a name="v10-and-v20"></a>v 1.0 e v 2.0 

Sono disponibili due versioni dei token di accesso nella piattaforma di identità Microsoft: v 1.0 e v 2.0.  Queste versioni controllano le attestazioni presenti nel token, assicurando che un'API Web possa controllare l'aspetto dei token. Per le API Web è stata selezionata una di queste impostazioni predefinite durante la registrazione-v 1.0 per le app solo Azure AD e v 2.0 per le app che supportano gli account utente.  Questo è controllabile dalle applicazioni che usano l' `accessTokenAcceptedVersion` impostazione nel [manifesto dell'applicazione](reference-app-manifest.md#manifest-reference), dove `null` e `1` generano i token v 1.0 e `2` genera i token v 2.0.

### <a name="what-app-is-a-token-for"></a>Quale app è un token "per"?

In una richiesta di token di accesso sono coinvolte due parti: il client, chi richiede il token e la risorsa (l'API) che accetta il token quando viene chiamata l'API. L' `aud` attestazione in un token indica la risorsa a cui il token è destinato (i *destinatari*). I client usano il token, ma non devono comprendere o tentare di analizzarlo. Le risorse accettano il token.  

La piattaforma Microsoft Identity supporta l'emissione di qualsiasi versione di token da qualsiasi endpoint della versione, non correlati. Questo è il motivo per cui un'impostazione `accessTokenAcceptedVersion` `2` della risorsa indica che un client che chiama l'endpoint v 1.0 per ottenere un token per l'API riceverà un token di accesso v 2.0.  Le risorse sono sempre proprietarie dei propri token (quelli con la relativa `aud` attestazione) e sono le uniche applicazioni che possono modificare i dettagli del token. Questo è il motivo per cui la modifica delle [attestazioni facoltative](active-directory-optional-claims.md) del token di accesso per il *client* non modifica il token di accesso ricevuto quando viene richiesto un token `user.read` , che è di proprietà della risorsa Microsoft Graph.

### <a name="sample-tokens"></a>Token di esempio

I token v1.0 e v2.0 sono simili e contengono molte delle stesse attestazioni. Di seguito sono illustrati esempi di ognuno. Questi token di esempio non verranno [convalidati](#validating-tokens), tuttavia, poiché le chiavi sono state ruotate prima della pubblicazione e le informazioni personali sono state rimosse da tali token.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Visualizzare questo token v1.0 in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Visualizzare questo token v2.0 in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Attestazioni nei token di accesso

I token JWT (token JSON Web) sono divisi in tre parti:

* **Intestazione**: fornisce informazioni su come [convalidare il token](#validating-tokens), incluse informazioni sul tipo di token e su come è stato firmato.
* **Payload**: contiene tutti i dati importanti relativi all'utente o all'app che sta cercando di chiamare il servizio.
* **Firma**: rappresenta il materiale non elaborato usato per convalidare il token.

Ogni parte è separata da un punto (`.`) e a ogni parte viene applicata separatamente la codifica Base64.

Le attestazioni sono presenti solo se c'è un valore da inserire. L'app non deve assumere una dipendenza da un'attestazione presente. Gli esempi includono `pwd_exp` (non ogni tenant richiede la scadenza delle password) e `family_name` (i flussi delle[credenziali client](v2-oauth2-client-creds-grant-flow.md) sono per conto di applicazioni che non hanno nomi). Le attestazioni usate per la convalida dei token di accesso sono sempre presenti.

Alcune attestazioni vengono usate per proteggere i token in Azure AD in caso di riutilizzo. In questo caso vengono descritte come "opache" per indicare che non sono destinate all'uso pubblico. Queste attestazioni possono o meno essere presenti in un token e possono venirne aggiunte di nuove senza preavviso.

### <a name="header-claims"></a>Attestazioni di intestazione

|Attestazione | Format | Descrizione |
|--------|--------|-------------|
| `typ` | Stringa, sempre "JWT" | Indica che il token è un token JWT.|
| `nonce` | string | Identificatore univoco usato per la protezione contro attacchi di riproduzione dei token. La risorsa può registrare questo valore per la protezione da operazioni di riproduzione. |
| `alg` | string | Indica l'algoritmo usato per firmare il token, ad esempio "RS256" |
| `kid` | string | Specifica l'identificazione personale per la chiave pubblica usata per firmare il token. Generata nei token di accesso sia v1.0 che v2.0. |
| `x5t` | string | Funziona in modo analogo, per uso e valore, a `kid`. `x5t` è un'attestazione legacy generata solo nei token di accesso v1.0 per motivi di compatibilità. |

### <a name="payload-claims"></a>Attestazioni di payload

| Attestazione | Format | Descrizione |
|-----|--------|-------------|
| `aud` | Stringa, URI ID app o GUID | Identifica il destinatario del token desiderato.  L'API deve convalidare questo valore e rifiutare il token se il valore non corrisponde. Nei token della versione 2.0, questo è sempre l'ID client dell'API, mentre nei token v 1.0 può essere l'ID client o l'URI di risorsa usato nella richiesta, a seconda di come il client ha richiesto il token.|
| `iss` | Stringa, un URI del servizio token di sicurezza | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. Se il token emesso è un token v2.0 (vedere l'attestazione `ver`), l'URI terminerà con `/v2.0`. Il GUID che indica che l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. L'app può usare la parte GUID dell'attestazione per limitare il set di tenant che può accedere all'app, se applicabile. |
|`idp`| Stringa, di solito un URI del servizio token di sicurezza | Registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia nello stesso tenant dell'autorità di certificazione, ad esempio guest. Se l'attestazione non è presente, significa che è possibile usare invece il valore `iss`.  Per gli account personali usati in un contesto aziendale, ad esempio, un account personale invitato in un tenant di Azure AD, l'attestazione `idp` potrebbe essere "live.com" o un URI STS contenente il tenant dell'account Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| `iat` | int, timestamp UNIX | "Issued At" indica quando è avvenuta l'autenticazione per il token. |
| `nbf` | int, timestamp UNIX | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione. |
| `exp` | int, timestamp UNIX | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. È importante notare che una risorsa può rifiutare il token anche prima di questa ora, ad esempio quando è necessaria una modifica nell'autenticazione oppure quando è stata rilevata una revoca di un token. |
| `aio` | Stringa opaca | Attestazione interna usata da Azure AD per registrare i dati per il riutilizzo dei token. Le risorse non devono usare questa attestazione. |
| `acr` | Stringa, "0" o "1" | Presente solo nei token v1.0. Attestazione "Authentication context class" (classe contesto di autenticazione). Il valore "0" indica che l'autenticazione dell'utente finale non soddisfa i requisiti ISO/IEC 29115. |
| `amr` | Matrice di stringhe JSON | Presente solo nei token v1.0. Identifica la modalità di autenticazione dell'oggetto del token. Per maggiori dettagli, vedere la [sezione relativa all'attestazione amr](#the-amr-claim). |
| `appid` | Stringa, un GUID | Presente solo nei token v1.0. ID applicazione del client che usa il token. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD. |
| `azp` | Stringa, un GUID | Presente solo nei token v2.0, in sostituzione di `appid`. ID applicazione del client che usa il token. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD. |
| `appidacr` | "0", "1" o "2" | Presente solo nei token v1.0. Indica la modalità di autenticazione del client. Per un client pubblico, il valore è "0". Se vengono usati l'ID client e il segreto client, il valore è "1". Se per l'autenticazione è stato usato un certificato client, il valore è "2". |
| `azpacr` | "0", "1" o "2" | Presente solo nei token v2.0, in sostituzione di `appidacr`. Indica la modalità di autenticazione del client. Per un client pubblico, il valore è "0". Se vengono usati l'ID client e il segreto client, il valore è "1". Se per l'autenticazione è stato usato un certificato client, il valore è "2". |
| `preferred_username` | string | Nome utente primario che rappresenta l'utente. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può variare nel tempo. Poiché è mutevole, questo valore non deve essere usato per prendere decisioni in merito alle autorizzazioni.  Può essere usato per gli hint per il nome utente, tuttavia, e nell'interfaccia utente leggibile come nome utente. Per ricevere questa attestazione, è necessario l'ambito `profile` . Presente solo nei token della versione 2.0. |
| `name` | string | Fornisce un valore leggibile che identifica l'oggetto del token. Il valore potrebbe non essere univoco, è modificabile e può essere usato solo per scopi di visualizzazione. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| `scp` | Stringa, elenco di ambiti separati da spazi | Set di ambiti esposti dall'applicazione per cui l'applicazione client ha richiesto (e ricevuto) il consenso. L'app deve verificare che questi ambiti siano ambiti validi esposti dall'app e prendere decisioni relative alle autorizzazioni in base al valore di questi ambiti. Inclusa solo per i [token utente](#user-and-application-tokens). |
| `roles` | Matrice di stringhe, un elenco di autorizzazioni | Il set di autorizzazioni esposto dall'applicazione per chi all'applicazione o all'utente richiedente è stata concessa l'autorizzazione per la chiamata. Per i [token delle applicazioni](#user-and-application-tokens), si usa durante il flusso di credenziali client ([v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) al posto degli ambiti utente.  Per i [token utente](#user-and-application-tokens), questa attestazione viene popolata con i ruoli a cui è stato assegnato l'utente nell'applicazione di destinazione. |
| `wids` | Matrice di GUID [RoleTemplateID](../roles/permissions-reference.md#role-template-ids) | Indica i ruoli a livello di tenant assegnati a questo utente dalla sezione dei ruoli presenti nella [pagina di ruoli di amministratore](../roles/permissions-reference.md#role-template-ids).  Questa attestazione viene configurata per ogni applicazione, tramite la proprietà `groupMembershipClaims` del [manifesto dell'applicazione](reference-app-manifest.md).  È necessario impostarla su "All" o su "DirectoryRole".  Può non essere presente nei token ottenuti tramite il flusso implicito, a causa di problemi di lunghezza del token. |
| `groups` | Matrice di GUID JSON | Fornisce gli ID oggetto che rappresentano le appartenenze ai gruppi del soggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere usati in modo sicuro per la gestione dell'accesso, ad esempio l'attivazione dell'autorizzazione per accedere a una risorsa. I gruppi inclusi nell'attestazione dei gruppi sono configurati per ogni singola applicazione, tramite la proprietà `groupMembershipClaims` del [manifesto dell'applicazione](reference-app-manifest.md). Il valore null escluderà tutti i gruppi, il valore "SecurityGroup" includerà solo Active Directory le appartenenze ai gruppi di sicurezza e il valore "All" includerà sia i gruppi di sicurezza che Microsoft 365 elenchi di distribuzione. <br><br>Vedere di seguito l'attestazione `hasgroups` per informazioni dettagliate sull'uso dell'attestazione `groups` con la concessione implicita. <br>Per gli altri flussi, se il numero di gruppi in cui è presente l'utente è superiore al limite (150 per SAML, 200 per JWT), nelle origini delle attestazioni viene aggiunta un'attestazione di eccedenza che punta all'endpoint Microsoft Graph contenente l'elenco dei gruppi per l'utente. |
| `hasgroups` | Boolean | Se presente, sempre `true`, per indicare che l'utente è presente in almeno un gruppo. Usato invece dell'attestazione `groups` per i token JWT nei flussi di concessione implicita se l'attestazione completa dei gruppi estenderebbe il frammento dell'URI oltre i limiti di lunghezza dell'URL (attualmente 6 o più gruppi). Indica che il client deve usare l'API Microsoft Graph per determinare i gruppi dell'utente (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`). |
| `groups:src1` | Oggetto JSON | Per le richieste di token che non hanno un limite di lunghezza (vedere `hasgroups` più indietro), ma sono comunque troppo grandi per il token, verrà incluso un collegamento all'elenco di gruppi completo per l'utente. Per i token JWT come un'attestazione distribuita, per SAML come una nuova attestazione invece dell'attestazione `groups`. <br><br>**Valore token JWT di esempio**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | string | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa oppure come chiave nelle tabelle di database. Dato che il soggetto è sempre presente nei token generati da Azure AD, è consigliabile usare questo valore in un sistema di autorizzazione di uso generico. L'oggetto è tuttavia un identificatore pairwise univoco per un ID di applicazione specifico. Se quindi un singolo utente accede a due app diverse usando due ID client differenti, queste app riceveranno due valori differenti per l'attestazione dell'oggetto. Questa condizione può non essere appropriata a seconda dei requisiti a livello di architettura e di privacy. Vedere anche l'attestazione `oid` (che rimane invariata nelle app all'interno di un tenant). |
| `oid` | Stringa, un GUID | Identificatore non modificabile per un oggetto in Microsoft Identity Platform, in questo caso un account utente. Può essere usato anche per eseguire i controlli di autorizzazioni in modo sicuro e come chiave nelle tabelle di database. Questo ID identifica in modo univoco l'utente nelle applicazioni; due applicazioni differenti che consentono l'accesso dello stesso utente riceveranno lo stesso valore nell'attestazione `oid`. È quindi possibile usare `oid` quando si eseguono query sui Microsoft Online Services, ad esempio Microsoft Graph. Microsoft Graph restituirà l'ID come proprietà `id` per un determinato [account utente](/graph/api/resources/user). Poiché `oid` consente a più app di correlare utenti, per ricevere questa attestazione è necessario l'ambito `profile`. Si noti che se un singolo utente è presente in più tenant, l'utente conterrà un ID oggetto diverso in ogni tenant; vengono considerati account diversi, anche se l'utente accede a ogni account con le stesse credenziali. |
| `tid` | Stringa, un GUID | Rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID è l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali il valore è `9188040d-6c67-4c5b-b112-36a304b66dad`. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| `unique_name` | string | Presente solo nei token v1.0. Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore potrebbe non essere univoco all'interno di un tenant e deve essere usato solo per scopi di visualizzazione. |
| `uti` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Le risorse non devono usare questa attestazione. |
| `rh` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Le risorse non devono usare questa attestazione. |
| `ver` | Stringa, `1.0` o `2.0` | Indica la versione del token di accesso. |

**Attestazione di eccedenza dei gruppi**

Per assicurarsi che le dimensioni dei token non superino i limiti delle dimensioni delle intestazioni HTTP, Azure AD limita il numero di ID oggetto inclusi nell'attestazione dei gruppi. Se un utente è membro di più gruppi rispetto al limite eccedenza (150 per i token SAML, 200 per i token JWT e solo 6 se emesso tramite il flusso implicito), Azure AD non emette l'attestazione dei gruppi nel token. Include invece un'attestazione di eccedenza nel token, che indica all'applicazione di eseguire una query sull'API Microsoft Graph per recuperare l'appartenenza al gruppo dell'utente.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

È possibile usare `BulkCreateGroups.ps1` disponibile nella cartella di [script di creazione di app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) per testare gli scenari di eccedenza.

#### <a name="v10-basic-claims"></a>Attestazioni di base v1.0

Le attestazioni seguenti verranno incluse nei token v1.0, se applicabile, ma per impostazione predefinita non sono incluse nei token v2.0. Se si usa la versione 2.0 ed è necessaria una di queste attestazioni, richiederla usando le [attestazioni facoltative](active-directory-optional-claims.md).

| Attestazione | Format | Descrizione |
|-----|--------|-------------|
| `ipaddr`| string | Indirizzo IP da cui l'utente ha eseguito l'autenticazione. |
| `onprem_sid`| Stringa, in [formato SID](/windows/desktop/SecAuthZ/sid-components) | Nei casi in cui l'utente ha un'autenticazione in locale, questa attestazione fornisce il relativo SID. È possibile usare `onprem_sid` per l'autorizzazione nelle applicazioni legacy.|
| `pwd_exp`| int, timestamp UNIX | Indica quando scade la password dell'utente. |
| `pwd_url`| string | URL a cui gli utenti possono essere rimandati per reimpostare la password. |
| `in_corp`| boolean | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non è inclusa. |
| `nickname`| string | Nome aggiuntivo per l'utente, distinto dal nome o dal cognome.|
| `family_name` | string | Fornisce il cognome dell'utente, come definito nell'oggetto utente. |
| `given_name` | string | Fornisce il nome dell'utente, come impostato nell'oggetto utente. |
| `upn` | string | Nome utente dell'utente. Può essere un numero di telefono, un indirizzo di posta elettronica o una stringa non formattata. Deve essere usata solo per scopi di visualizzazione e per fornire suggerimenti per il nome utente in scenari di ripetizione dell'autenticazione. |

#### <a name="the-amr-claim"></a>Attestazione `amr`

Le identità Microsoft possono eseguire l'autenticazione in vari modi, che possono essere pertinenti per l'applicazione. L'attestazione `amr` è una matrice che può contenere più elementi, ad esempio `["mfa", "rsa", "pwd"]`, per un'autenticazione che ha usato sia una password che l'app Authenticator.

| valore | Descrizione |
|-----|-------------|
| `pwd` | Autenticazione della password, ovvero una password Microsoft dell'utente o un segreto client dell'app. |
| `rsa` | L'autenticazione è avvenuta in base alla prova di una chiave RSA, ad esempio con l'[app Microsoft Authenticator](https://aka.ms/AA2kvvu). Indica se l'autenticazione è stata eseguita da un token JWT autofirmato con un certificato X509 di proprietà del servizio. |
| `otp` | Passcode monouso tramite un messaggio di posta elettronica o un SMS. |
| `fed` | È stata usata un'asserzione di autenticazione federata (ad esempio token JWT o SAML). |
| `wia` | Autenticazione integrata di Windows |
| `mfa` | È stata usata l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md). Quando è presente sono inclusi anche gli altri metodi di autenticazione. |
| `ngcmfa` | Equivalente a `mfa`, usata per il provisioning di determinati tipi di credenziali avanzate. |
| `wiaormfa`| L'utente ha usato Windows o una credenziale di autenticazione a più fattori per l'autenticazione. |
| `none` | Non è stata eseguita alcuna autenticazione. |

## <a name="validating-tokens"></a>Convalida dei token

Non tutte le app devono convalidare i token. Solo in scenari specifici le app devono convalidare un token:

* Le [API Web](quickstart-configure-app-expose-web-apis.md) devono convalidare i token di accesso inviati da un client.  Devono accettare solo token contenenti la relativa `aud` attestazione.
* App Web riservate come ASP.NET Core devono convalidare i token ID inviati tramite il browser dell'utente nel flusso ibrido, prima di consentire l'accesso ai dati di un utente o la creazione di una sessione.

Se nessuno degli scenari precedenti si applica, l'applicazione non trarrà vantaggio dalla convalida del token e può presentare un rischio di sicurezza e affidabilità se le decisioni vengono prese in base alla validità del token.  I client pubblici come app native o Spa non traggono vantaggio dalla convalida dei token. l'app comunica direttamente con l'IDP, quindi la protezione SSL garantisce che i token siano validi.

 Le API e le app Web devono convalidare solo i token che dispongono di un' `aud` attestazione corrispondente all'applicazione; altre risorse possono avere regole di convalida dei token personalizzate. Ad esempio, i token per Microsoft Graph non verranno convalidati in base a queste regole a causa del formato proprietario. La convalida e l'accettazione di token destinati a un'altra risorsa è un esempio del problema del [Vice confuso](https://cwe.mitre.org/data/definitions/441.html) .

Se l'applicazione deve convalidare un id_token o un access_token in base a quanto indicato in precedenza, l'app deve prima convalidare la firma del token e l'autorità emittente rispetto ai valori nel documento di individuazione OpenID. Ad esempio, la versione indipendente dal tenant del documento si trova all'indirizzo [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Per coloro che desiderano comprendere il processo sottostante vengono fornite le informazioni seguenti. Il middleware di Azure AD offre funzionalità predefinite per la convalida dei token di accesso ed è possibile cercare tra gli [esempi](sample-v2-code.md) per trovarne una nel linguaggio preferito. Per la convalida di JWT sono disponibili anche diverse librerie open source di terze parti: esiste almeno un'opzione per quasi tutte le piattaforme e le lingue. Per ulteriori informazioni sulle librerie di autenticazione Azure AD ed esempi di codice, vedere le [librerie di autenticazione](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Convalida della firma

Un token JWT contiene tre segmenti separati dal carattere `.` . Il primo segmento è noto come **intestazione**, il secondo come **corpo** e il terzo come **firma**. Il segmento di firma può essere usato per convalidare l'autenticità del token, in modo che possa essere considerato attendibile dall'app.

I token emessi da Azure AD vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RS256. L'intestazione del token JWT contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token, mentre l'attestazione `kid` indica la chiave pubblica specifica usata per convalidarlo.

In qualsiasi momento, Azure AD può firmare un token ID usando un determinato set di coppie di chiavi pubbliche/private. Azure AD ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate da Azure AD è di circa 24 ore.

È possibile acquisire i dati della chiave di firma necessari per convalidare la firma usando il [documento dei metadati OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) disponibile all'indirizzo:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Provare questo [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) in un browser.

Il documento di metadati ha le caratteristiche seguenti:

* È un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione di OpenID Connect.
* Include un oggetto `jwks_uri` che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Il token JWK (chiave Web JSON) disponibile in `jwks_uri` contiene tutte le informazioni sulla chiave pubblica in uso in un determinato momento.  Il formato di JWK è descritto in [RFC 7517](https://tools.ietf.org/html/rfc7517).  L'app può usare l'attestazione `kid` nell'intestazione JWT per selezionare quale chiave pubblica è stata usata in questo documento per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

> [!NOTE]
> È consigliabile usare l'attestazione `kid` per convalidare il token. Sebbene i token v 1.0 contengano entrambe le `x5t` `kid` attestazioni e, i token v 2.0 contengono solo l' `kid` attestazione.

L'esecuzione della convalida della firma esula dall'ambito di questo documento. Sono però disponibili numerose librerie open source che contengono informazioni per eseguire questa operazione.  Tuttavia, la piattaforma di identità Microsoft dispone di un'estensione per la firma di token per le chiavi di firma personalizzate standard.

Se l'app include chiavi di firma personalizzate perché è stata usata la funzionalità di [mapping delle attestazioni](active-directory-claims-mapping.md), è necessario aggiungere un parametro di query `appid` contenente l'ID app per ottenere un indirizzo `jwks_uri` che punta alle informazioni della chiave di firma dell'app, che dovranno essere usate per la convalida. Ad esempio, `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` contiene una l'indirizzo `jwks_uri` `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`.

### <a name="claims-based-authorization"></a>Autorizzazione basata su attestazioni

La logica di business dell'applicazione richiede questo passaggio e di seguito sono elencati alcuni metodi di autorizzazione comuni.

* Controllare l'attestazione `scp` o `roles` per verificare che tutti gli ambiti presenti corrispondano a quelli esposti dall'API e consentire al client di eseguire l'azione richiesta.
* Verificare che il client chiamante sia autorizzato a chiamare l'API usando l'attestazione `appid`.
* Convalidare lo stato dell'autenticazione del client chiamante usando `appidacr`: il valore non deve essere 0 se i client pubblici non sono autorizzati a chiamare l'API.
* Eseguire un controllo rispetto all'elenco di attestazioni `nonce` passate per verificare che il token non venga riprodotto.
* Controllare che `tid` corrisponda a un tenant autorizzato a chiamare l'API.
* Usare l'attestazione `acr` per verificare che l'utente abbia eseguito l'autenticazione a più fattori. Questa azione deve essere applicata usando l'[accesso condizionale](../conditional-access/overview.md).
* Se è stata richiesta l'attestazione `roles` o `groups` nel token di accesso, verificare che l'utente sia incluso nel gruppo autorizzato a eseguire questa azione.
  * Per i token recuperati tramite il flusso implicito, sarà probabilmente necessario eseguire una query su [Microsoft Graph](https://developer.microsoft.com/graph/) per ottenere questi dati, in quanto in genere le dimensioni sono troppo grandi per l'inserimento nel token.

## <a name="user-and-application-tokens"></a>Token utente e dell'applicazione

L'applicazione può ricevere i token per l'utente (il flusso in genere descritto) o direttamente da un'applicazione (tramite il [flusso di credenziali client](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)). Questi token solo app indicano che la chiamata proviene da un'applicazione e non da un utente. Questi token vengono gestiti in gran parte:

* Usare `roles` per visualizzare le autorizzazioni concesse all'oggetto del token.
* Usare `oid` o `sub` per verificare che l'entità servizio chiamante sia quella prevista.

Se l'app deve distinguere tra i token di accesso solo app e i token di accesso per gli utenti, usare l' `idtyp` [attestazione facoltativa](active-directory-optional-claims.md).  Aggiungendo l' `idtyp` attestazione al `accessToken` campo e controllando il valore `app` , è possibile rilevare i token di accesso solo app.  I token ID e i token di accesso per gli utenti non avranno l' `idtyp` attestazione inclusa.

## <a name="token-revocation"></a>Revoca dei token

I token di aggiornamento possono essere invalidati o revocati in qualsiasi momento per vari motivi. Questi motivi appartengono a due categorie: timeout e revoca.

### <a name="token-timeouts"></a>Timeout dei token

Usando la [configurazione della durata dei token](active-directory-configurable-token-lifetimes.md) è possibile alterare la durata dei token di aggiornamento.  È normale e previsto che alcuni token non vengano usati, ad esempio se l'utente non apre l'app per tre mesi, e che pertanto scadano.  Le app riscontreranno scenari in cui il server di accesso rifiuta un token di aggiornamento a causa dell'età.

* MaxInactiveTime: se il token di aggiornamento non è stato usato entro l'intervallo di tempo specificato da MaxInactiveTime, non sarà più valido.
* MaxSessionAge: se il parametro MaxAgeSessionMultiFactor o MaxAgeSessionSingleFactor è stato impostato su un valore diverso da quello predefinito (Until-revoked), sarà necessaria una nuova autenticazione allo scadere del tempo impostato in MaxAgeSession*.
* Esempi:
  * Il tenant ha un parametro MaxInactiveTime impostato su cinque giorni, ma l'utente è andato in vacanza per una settimana e di conseguenza Azure AD non riscontra una sua nuova richiesta di token da sette giorni. Alla successiva richiesta di un nuovo token, l'utente noterà che il token di aggiornamento è stato revocato e dovrà immettere di nuovo le credenziali.
  * Nel caso di un'applicazione riservata, MaxAgeSessionSingleFactor è impostato su un giorno. Se un utente accede il lunedì e il martedì (dopo che sono trascorse 25 ore) dovrà ripetere l'autenticazione.

### <a name="revocation"></a>Revoca

I token di aggiornamento possono essere revocati dal server a causa di una modifica delle credenziali oppure in conseguenza dell'azione di un amministratore o di un utente.  I token di aggiornamento rientrano in due classi, ovvero quelle rilasciate ai client riservati (la colonna più a destra) e quelle rilasciate ai client pubblici (tutte le altre colonne).

| Modifica | Cookie basato su password | Token basato su password | Cookie non basato su password | Token non basato su password | Token client riservato |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Scadenza password | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Password cambiata dall'utente | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Utente esegue SSPR | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Amministratore reimposta password | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Utente revoca token di aggiornamento [tramite PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revocato | Revocato | Revocato | Revocato | Revocato |
| L'amministratore revoca tutti i token di aggiornamento per un utente [tramite PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revocato | Revocato |Revocato | Revocato | Revocato |
| Single Sign-Out ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out)) sul Web | Revocato | Rimarrà attivo | Revocato | Rimarrà attivo | Rimarrà attivo |

#### <a name="non-password-based"></a>Non basato su password

Un account di accesso *non basato su password* è quello in cui l'utente non ha digitato una password per ottenerlo. Esempi di account di accesso non basati su password includono:

- Uso del viso con Windows Hello
- Chiave di FIDO2
- sms
- Chiamata vocale
- PIN

> [!NOTE]
> I token di aggiornamento primari in Windows 10 vengono separati in base alle credenziali. Ad esempio, Windows Hello e le password hanno i rispettivi token di aggiornamento primari, isolati uno dall'altro. Quando un utente accede con le credenziali di Hello (PIN o biometria) e poi cambia la password, il token di aggiornamento primario basato su password ottenuto in precedenza verrà revocato. Se si accede di nuovo con una password, il token di aggiornamento primario precedente viene invalidato e ne viene richiesto uno nuovo.
>
> I token di aggiornamento non vengono invalidati o revocati quando vengono usati per recuperare un nuovo token di accesso e un token di aggiornamento.  Tuttavia, l'app deve rimuovere quello precedente non appena viene usato e sostituirlo con quello nuovo, perché il nuovo token include una nuova data di scadenza.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su [`id_tokens` in Azure AD](id-tokens.md).
* Leggere le informazioni su autorizzazioni e consenso ([v1.0](../azuread-dev/v1-permissions-consent.md), [v2.0](v2-permissions-and-consent.md)).
