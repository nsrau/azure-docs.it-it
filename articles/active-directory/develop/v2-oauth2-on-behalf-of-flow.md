---
title: Microsoft Identity Platform & OAuth 2.0 per conto di Microsoft Flow | Azure
description: Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso on-behalf-of di OAuth2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa58f63e70c09e17328b849e7728604a65cb7ae1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964320"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform e OAuth 2,0, per conto di Microsoft Flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Il flusso On-Behalf-Of (OBO) di OAuth 2.0 viene usato quando un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'altra API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Affinché il servizio di livello intermedio effettui richieste autenticate al servizio downstream, deve proteggere un token di accesso dalla piattaforma di identità Microsoft per conto dell'utente.

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare invece le librerie di autenticazione Microsoft (MSAL) supportate per [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Esaminare anche le [app di esempio che usano MSAL](sample-v2-code.md).

> [!NOTE]
>
> - L'endpoint della piattaforma Microsoft Identity non supporta tutti gli scenari e le funzionalità. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](active-directory-v2-limitations.md). In particolare, le applicazioni client note non sono supportate per le app con account Microsoft (MSA) e i destinatari di Azure AD. Di conseguenza, un modello di consenso comune per OBO non funzionerà per i client che eseguono l'accesso sia con account personali che con account aziendali o di istituti di istruzione. Per altre informazioni su come gestire questo passaggio del flusso, vedere [Ottenere il consenso per l'applicazione di livello intermedio](#gaining-consent-for-the-middle-tier-application).
> - A partire da maggio 2018, non è possibile usare un `id_token` derivato da flusso implicito per il flusso OBO. Le applicazioni a pagina singola dovrebbero invece passare un token di **accesso** a un client riservato di livello intermedio per eseguire i flussi OBO. Per altre informazioni su quali client possono eseguire chiamate OBO, vedere le [limitazioni](#client-limitations).

## <a name="protocol-diagram"></a>Diagramma di protocollo

Si supponga che l'utente sia stato autenticato in un'applicazione usando il [flusso di concessione del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md). A questo punto, l'applicazione contiene un token di accesso *per l'API A* (token A) con le richieste dell'utente e il consenso per accedere all'API Web di livello intermedio (API A). L'API A deve ora eseguire una richiesta autenticata all'API Web downstream (API B).

I passaggi che seguono costituiscono il flusso OBO e vengono descritti con l'aiuto del diagramma seguente.

![Mostra il flusso per conto di OAuth 2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. L'applicazione client esegue una richiesta all'API A con il token A, con un'attestazione `aud` dell'API A.
1. L'API A esegue l'autenticazione all'endpoint di rilascio del token della piattaforma di identità Microsoft e richiede un token per accedere all'API B.
1. L'endpoint di rilascio dei token della piattaforma Microsoft Identity convalida le credenziali dell'API A con il token A e rilascia il token di accesso per l'API B (token B).
1. Il token B viene impostato nell'intestazione dell'autorizzazione della richiesta all'API B.
1. I dati della risorsa protetta vengono restituiti dall'API B.

> [!NOTE]
> In questo scenario, il servizio di livello intermedio non ha alcuna interazione utente per ottenere il consenso dell'utente per accedere all'API downstream. Pertanto, l'opzione per la concessione dell'accesso all'API downstream viene presentata in anticipo come parte della fase del consenso durante l'autenticazione. Per altre informazioni su questa impostazione per l'app, vedere [Ottenere il consenso per l'applicazione di livello intermedio](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Richiesta del token di accesso da servizio a servizio

Per richiedere un token di accesso, creare un HTTP POST per l'endpoint del token della piattaforma di identità Microsoft Identity specifico del tenant con i parametri seguenti.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

L'applicazione client può scegliere di essere protetta da un segreto condiviso oppure da un certificato.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

Quando si usa un segreto condiviso, una richiesta di token di accesso da servizio a servizio contiene i parametri seguenti:

| Parametro |  | Description |
| --- | --- | --- |
| `grant_type` | Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obbligatoria | ID dell'applicazione (client) che [la pagina portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `client_secret` | Obbligatoria | Il segreto client generato per l'app nella pagina portale di Azure-Registrazioni app. |
| `assertion` | Obbligatoria | Il valore del token usato nella richiesta. |
| `scope` | Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per altre informazioni, vedere [Scopes](v2-permissions-and-consent.md) (Ambiti). |
| `requested_token_use` | Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso OBO il valore deve essere impostato su `on_behalf_of`. |

#### <a name="example"></a>Esempio

La richiesta HTTP POST seguente richiede un token di accesso e un token di aggiornamento con ambito `user.read` per l'API Web https://graph.microsoft.com.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato

Una richiesta di token di accesso da servizio a servizio con un certificato contiene i parametri seguenti:

| Parametro |  | Description |
| --- | --- | --- |
| `grant_type` | Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obbligatoria |  ID dell'applicazione (client) che [la pagina portale di Azure-registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `client_assertion_type` | Obbligatoria | Il valore deve essere `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Per informazioni sulla registrazione del certificato e il formato dell'asserzione, vedere le [credenziali del certificato](active-directory-certificate-credentials.md). |
| `assertion` | Obbligatoria | Il valore del token usato nella richiesta. |
| `requested_token_use` | Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso OBO il valore deve essere impostato su `on_behalf_of`. |
| `scope` | Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per altre informazioni, vedere [Scopes](v2-permissions-and-consent.md) (Ambiti).|

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro `client_secret` che viene sostituito da due parametri: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Esempio

La richiesta HTTP POST seguente richiede un token di accesso con ambito `user.read` per l'API Web https://graph.microsoft.com con un certificato.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Risposta del token di accesso da servizio a servizio

Una risposta di esito positivo è una risposta OAuth 2.0 JSON con i parametri seguenti.

| Parametro | Description |
| --- | --- |
| `token_type` | Indica il valore del tipo di token. L'unico tipo supportato dalla piattaforma di identità Microsoft è `Bearer`. Per altre informazioni sui token di porta, vedere il [Framework di autorizzazione OAuth 2,0: utilizzo token di porta (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | L'ambito di accesso concesso nel token. |
| `expires_in` | Il periodo di validità, in secondi, del token di accesso. |
| `access_token` | Token di accesso richiesto. Il servizio chiamante può usare questo token per l'autenticazione nel servizio ricevente. |
| `refresh_token` | Il token di aggiornamento per il token di accesso richiesto. Il servizio chiamante può usare questo token per richiedere un altro token di accesso dopo la scadenza di quello corrente. Il token di aggiornamento viene fornito solo se è stato richiesto l'ambito `offline_access`. |

### <a name="success-response-example"></a>Esempio di risposta di esito positivo

L'esempio seguente mostra una risposta corretta a una richiesta di token di accesso per l'API Web https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Il token di accesso precedente è un token formattato v1.0. Questo perché il token viene fornito in base alle risorse a cui si accede. Il Microsoft Graph richiede token v 1.0, pertanto Microsoft Identity Platform produce token di accesso v 1.0 quando un client richiede token per Microsoft Graph. Solo le applicazioni devono esaminare i token di accesso. I client non devono controllarli.

### <a name="error-response-example"></a>Esempio di risposta con errore

Una risposta di errore viene restituita dall'endpoint del token quando si tenta di acquisire un token di accesso per l'API downstream, se per l'API downstream sono impostati criteri di accesso condizionale, ad esempio l'autenticazione a più fattori. Il servizio di livello intermedio deve esporre questo errore all'applicazione client in modo che l'applicazione client possa fornire l'interazione dell'utente per soddisfare i criteri di accesso condizionale.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Usare il token di accesso per accedere alla risorsa protetta

Il servizio di livello intermedio può ora usare il token acquisito in precedenza per eseguire richieste autenticate all'API Web downstream, impostando il token nell'intestazione `Authorization`.

### <a name="example"></a>Esempio

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Ottenere il consenso per l'applicazione di livello intermedio

A seconda del gruppo di destinatari per l'applicazione, è possibile prendere in considerazione diverse strategie per verificare che il flusso OBO abbia esito positivo. In tutti i casi l'obiettivo finale consiste nell'assicurare che venga fornito il consenso appropriato. Il modo in cui ciò si verifica, tuttavia, dipende da quali utenti supporta l'applicazione.

### <a name="consent-for-azure-ad-only-applications"></a>Consenso per le applicazioni solo per Azure AD

#### <a name="default-and-combined-consent"></a>/.default e consenso combinato

Per le applicazioni che devono eseguire l'accesso solo con account aziendali o di istituti di istruzione, è sufficiente l'approccio tradizionale "Applicazioni client note". L'applicazione di livello intermedio aggiunge il client all'elenco delle applicazioni client note nel relativo manifesto e quindi il client può attivare un flusso di consenso combinato per se stesso e l'applicazione di livello intermedio. Nell'endpoint della piattaforma Microsoft Identity questa operazione viene eseguita usando l' [ambito`/.default`](v2-permissions-and-consent.md#the-default-scope). Quando si attiva una schermata di consenso con le applicazioni client note e `/.default`, la schermata di consenso visualizzerà le autorizzazioni per il client per l'API di livello intermedio, oltre a richiedere le eventuali autorizzazioni necessarie per l'API di livello intermedio. L'utente fornisce il consenso per entrambe le applicazioni e quindi il flusso OBO funziona.

Attualmente il sistema di account Microsoft personale non supporta il consenso combinato, pertanto questo approccio non funziona per le app che vogliono accedere in modo specifico tramite account personali. Gli account Microsoft personali usati come account guest in un tenant vengono gestiti tramite il sistema Azure AD e possono passare attraverso il consenso combinato.

#### <a name="pre-authorized-applications"></a>Applicazioni preautorizzate

Una funzionalità del portale applicazioni è "applicazioni pre-autorizzate". In questo modo, una risorsa può indicare che una determinata applicazione disponga sempre dell'autorizzazione per ricevere determinati ambiti. Ciò risulta particolarmente utile per semplificare le connessioni tra un client front-end e una risorsa back-end. Una risorsa può dichiarare più applicazioni preautorizzate: qualsiasi applicazione di questo tipo può richiedere tali autorizzazioni in un flusso OBO e riceverle senza che l'utente fornisca il consenso.

#### <a name="admin-consent"></a>Consenso dell'amministratore

Un amministratore del tenant può garantire che le applicazioni siano autorizzate a chiamare le API necessarie fornendo il consenso dell'amministratore per l'applicazione di livello intermedio. A tale scopo, l'amministratore può trovare l'applicazione di livello intermedio nel proprio tenant, aprire la pagina delle autorizzazioni necessarie e scegliere di concedere l'autorizzazione per l'app. Per altre informazioni sul consenso dell'amministratore, vedere la [documentazione su autorizzazioni e consenso](v2-permissions-and-consent.md).

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>Consenso per Azure AD e applicazioni di account Microsoft

A causa delle restrizioni del modello di autorizzazione per gli account personali e della mancanza di un tenant di governance, i requisiti di consenso per gli account personali sono leggermente diversi da Azure AD. Non c'è nessun tenant per cui fornire il consenso a livello di tenant, né è presente la possibilità del consenso combinato. In questo modo, si presentano altre strategie, che tuttavia possono essere usate per le applicazioni che devono solo supportare anche gli account Azure AD.

#### <a name="use-of-a-single-application"></a>Uso di un'applicazione singola

In alcuni scenari può esserci solo una singola associazione del client di livello intermedio e front-end. In questo scenario può risultare più semplice impostare l'applicazione come singola, senza la necessità di un'applicazione di livello intermedio. Per eseguire l'autenticazione tra il front-end e l'API Web, è possibile usare i cookie, un id_token o un token di accesso richiesto per l'applicazione stessa. Quindi, richiedere il consenso da questa applicazione singola alla risorsa back-end.

## <a name="client-limitations"></a>Limitazioni client

Se un client usa il flusso implicito per ottenere un id_token e il client dispone anche di caratteri jolly in un URL di risposta, non è possibile usare il id_token per un flusso OBO.  Tuttavia, i token di accesso acquisiti tramite il flusso di concessione implicita possono ancora essere riscattati da un client riservato anche se il client di origine dispone di un URL di risposta con caratteri jolly registrato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul protocollo OAuth 2.0 e su un altro modo per eseguire l'autenticazione da servizio a servizio usando le credenziali del client.

* [Concessione di credenziali client OAuth 2,0 nella piattaforma di identità Microsoft](v2-oauth2-client-creds-grant-flow.md)
* [Flusso di codice OAuth 2,0 nella piattaforma di identità Microsoft](v2-oauth2-auth-code-flow.md)
* [Uso dell'ambito `/.default`](v2-permissions-and-consent.md#the-default-scope)
