---
title: Microsoft Identity Platform e flusso On-Behalf-Of di OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso on-behalf-of di OAuth2.0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 018d67b3e4e730cd46eb524a8927b3a6d68d74e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958661"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform e flusso On-Behalf-Of di OAuth 2.0


Il flusso On-Behalf-Of (OBO) di OAuth 2.0 viene usato quando un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'altra API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Per eseguire richieste autenticate al servizio downstream, il servizio di livello intermedio deve assicurarsi un token di accesso da Microsoft Identity Platform per conto dell'utente.

Questo articolo descrive come programmare direttamente in base al protocollo nell'applicazione.  Quando possibile, è consigliabile usare le librerie di autenticazione Microsoft (MSAL) supportate anziché [acquisire i token e chiamare le API Web protette](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Vedere anche le [app di esempio che usano MSAL](sample-v2-code.md).

> [!NOTE]
> A partire da maggio 2018, non è possibile usare un `id_token` derivato da flusso implicito per il flusso OBO. Le applicazioni a pagina singola dovrebbero invece passare un token di **accesso** a un client riservato di livello intermedio per eseguire i flussi OBO. Per altre informazioni su quali client possono eseguire chiamate OBO, vedere le [limitazioni](#client-limitations).

## <a name="protocol-diagram"></a>Diagramma di protocollo

Si supponga che l'utente sia stato autenticato in un'applicazione usando il [flusso di concessione del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md) o un altro flusso di accesso. A questo punto, l'applicazione contiene un token di accesso *per l'API A* (token A) con le attestazioni dell'utente e il consenso per accedere all'API Web di livello intermedio (API A). L'API A deve ora eseguire una richiesta autenticata all'API Web downstream (API B).

I passaggi che seguono costituiscono il flusso OBO e vengono descritti con l'aiuto del diagramma seguente.

![Mostra il flusso On-Behalf-Of di OAuth2.0](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. L'applicazione client esegue una richiesta all'API A con il token A, con un'attestazione `aud` dell'API A.
1. L'API A esegue l'autenticazione all'endpoint di rilascio del token di Microsoft Identity Platform e richiede un token per accedere all'API B.
1. L'endpoint di rilascio del token di Microsoft Identity Platform convalida le credenziali dell'API A con il token A ed emette il token di accesso per l'API B (token B) all'API A.
1. Il token B viene impostato dall'API A nell'intestazione dell'autorizzazione della richiesta all'API B.
1. I dati della risorsa protetta vengono restituiti dall'API B all'API A e da qui al client.

> [!NOTE]
> In questo scenario, il servizio di livello intermedio non ha alcuna interazione utente per ottenere il consenso dell'utente per accedere all'API downstream. Pertanto, l'opzione per la concessione dell'accesso all'API downstream viene presentata in anticipo come parte della fase del consenso durante l'autenticazione. Per altre informazioni su questa impostazione per l'app, vedere [Ottenere il consenso per l'applicazione di livello intermedio](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Richiesta di token di accesso di livello intermedio

Per richiedere un token di accesso, eseguire una richiesta HTTP POST all'endpoint del token di Microsoft Identity Platform specifico del tenant con i parametri seguenti.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

L'applicazione client può scegliere di essere protetta da un segreto condiviso oppure da un certificato.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

Quando si usa un segreto condiviso, una richiesta di token di accesso da servizio a servizio contiene i parametri seguenti:

| Parametro | Type | Descrizione |
| --- | --- | --- |
| `grant_type` | Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obbligatoria | ID dell'applicazione (client) che la pagina [Registrazioni app del portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `client_secret` | Obbligatoria | Segreto client generato per l'app nella pagina Registrazioni app del portale di Azure. |
| `assertion` | Obbligatoria | Il token di accesso inviato all'API di livello intermedio.  Questo token deve avere un' `aud` attestazione audience () dell'app che effettua questa richiesta OBO (l'app indicata dal `client-id` campo). Le applicazioni non possono riscattare un token per un'app diversa, ad esempio se un client invia un'API a un token per MS Graph, l'API non può riscattarla con OBO.  Il token deve invece essere rifiutato.  |
| `scope` | Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per altre informazioni, vedere [Scopes](v2-permissions-and-consent.md) (Ambiti). |
| `requested_token_use` | Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso OBO il valore deve essere impostato su `on_behalf_of`. |

#### <a name="example"></a>Esempio

La richiesta HTTP POST seguente richiede un token di accesso e un token di aggiornamento con ambito `user.read` per l'API Web https://graph.microsoft.com.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato

Una richiesta di token di accesso da servizio a servizio con un certificato contiene i parametri seguenti:

| Parametro | Type | Descrizione |
| --- | --- | --- |
| `grant_type` | Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Obbligatoria |  ID dell'applicazione (client) che la pagina [Registrazioni app del portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908) ha assegnato all'app. |
| `client_assertion_type` | Obbligatoria | Il valore deve essere `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Per informazioni sulla registrazione del certificato e il formato dell'asserzione, vedere le [credenziali del certificato](active-directory-certificate-credentials.md). |
| `assertion` | Obbligatoria |  Il token di accesso inviato all'API di livello intermedio.  Questo token deve avere un' `aud` attestazione audience () dell'app che effettua questa richiesta OBO (l'app indicata dal `client-id` campo). Le applicazioni non possono riscattare un token per un'app diversa, ad esempio se un client invia un'API a un token per MS Graph, l'API non può riscattarla con OBO.  Il token deve invece essere rifiutato.  |
| `requested_token_use` | Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso OBO il valore deve essere impostato su `on_behalf_of`. |
| `scope` | Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per altre informazioni, vedere [Scopes](v2-permissions-and-consent.md) (Ambiti).|

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro `client_secret` che viene sostituito da due parametri: `client_assertion_type` e `client_assertion`.

#### <a name="example"></a>Esempio

La richiesta HTTP POST seguente richiede un token di accesso con ambito `user.read` per l'API Web https://graph.microsoft.com con un certificato.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Risposta del token di accesso di livello intermedio

Una risposta di esito positivo è una risposta OAuth 2.0 JSON con i parametri seguenti.

| Parametro | Descrizione |
| --- | --- |
| `token_type` | Indica il valore del tipo di token. L'unico tipo supportato da Microsoft Identity Platform è `Bearer`. Per altre informazioni sui bearer token, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750). |
| `scope` | L'ambito di accesso concesso nel token. |
| `expires_in` | Il periodo di validità, in secondi, del token di accesso. |
| `access_token` | Token di accesso richiesto. Il servizio chiamante può usare questo token per l'autenticazione nel servizio ricevente. |
| `refresh_token` | Il token di aggiornamento per il token di accesso richiesto. Il servizio chiamante può usare questo token per richiedere un altro token di accesso dopo la scadenza di quello corrente. Il token di aggiornamento viene fornito solo se è stato richiesto l'ambito `offline_access`. |

### <a name="success-response-example"></a>Esempio di risposta di esito positivo

L'esempio seguente mostra una risposta corretta a una richiesta di token di accesso per l'API Web https://graph.microsoft.com.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

> [!NOTE]
> Il token di accesso precedente è un token in formato v 1.0 per Microsoft Graph. Questo perché il formato del token è basato sulla **risorsa** a cui si accede e non è correlato agli endpoint usati per richiederlo. L'API Microsoft Graph è configurata per accettare token v1.0, di conseguenza Microsoft Identity Platform genera i token di accesso v1.0 quando un client richiede i token per Microsoft Graph. Altre app possono indicare che vogliono usare i token di formato v 2.0, i token di formato v 1.0 o anche i formati di token crittografati o proprietari.  Entrambi gli endpoint v 1.0 e v 2.0 possono emettere uno dei due formati di token. in questo modo, la risorsa può ottenere sempre il formato corretto del token indipendentemente dalla modalità o dal punto in cui il token è stato richiesto dal client. 
>
> Solo le applicazioni devono esaminare i token di accesso. I client **non devono** esaminarli. Esaminando i token di accesso per le altre app nel codice, l'app si interrompe in modo imprevisto quando tale app modifica il formato dei token o inizia a crittografarli. 

### <a name="error-response-example"></a>Esempio di risposta con errore

Quando si tenta di acquisire un token di accesso per l'API downstream, se questa dispone di criteri di accesso condizionale, ad esempio l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md) impostata, l'endpoint del token restituisce una risposta di errore. Il servizio di livello intermedio segnala l'errore all'applicazione client in modo che questa possa fornire l'interazione dell'utente per soddisfare i criteri di accesso condizionale.

```json
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

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Asserzioni SAML ottenute con un flusso di OAuth2.0 OBO

Alcuni servizi Web di OAuth devono accedere ad altre API di servizi Web che accettano le asserzioni SAML in flussi non interattivi. Azure AD può fornire un'asserzione SAML in risposta a un flusso on-behalf-of che usa un servizio Web SAML come risorsa di destinazione.

>[!NOTE]
>Si tratta di un'estensione non standard del flusso on-behalf-of di OAuth 2.0 che consente a un'applicazione OAuth2 di accedere agli endpoint API del servizio Web che usano token SAML.

> [!TIP]
> Se un servizio Web protetto con SAML viene chiamato da un'applicazione Web front-end, è sufficiente chiamare l'API e avviare un flusso di autenticazione interattiva normale con la sessione esistente degli utenti. È necessario usare un flusso OBO quando una chiamata da servizio a servizio richiede un token SAML per fornire il contesto utente.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Ottenere il consenso per l'applicazione di livello intermedio

A seconda dell'architettura o dell'utilizzo dell'applicazione, è possibile prendere in considerazione diverse strategie per garantire che il flusso OBO abbia esito positivo. In tutti i casi, l'obiettivo finale è quello di garantire il consenso appropriato, in modo che l'app client possa chiamare l'app di livello intermedio e l'app di livello intermedio disponga dell'autorizzazione per chiamare la risorsa back-end.

> [!NOTE]
> In precedenza il sistema di account Microsoft (account personali) non supportava il campo "applicazione client nota", né poteva visualizzare il consenso combinato.  Questa funzionalità è stata aggiunta e tutte le app di Microsoft Identity Platform possono usare l'approccio basato sull'applicazione client nota per ottenere il consenso per le chiamate a OBO.

### <a name="default-and-combined-consent"></a>/.default e consenso combinato

L'applicazione di livello intermedio aggiunge il client all'elenco delle applicazioni client note nel relativo manifesto e quindi il client può attivare un flusso di consenso combinato per se stesso e l'applicazione di livello intermedio. Nell'endpoint Microsoft Identity Platform, questa operazione viene eseguita usando l'[ambito `/.default`](v2-permissions-and-consent.md#the-default-scope). Quando si attiva una schermata di consenso con le applicazioni client note e `/.default`, la schermata di consenso visualizzerà le autorizzazioni **sia** del client per l'API di livello intermedio e richiederà anche le eventuali autorizzazioni necessarie per l'API di livello intermedio. L'utente fornisce il consenso per entrambe le applicazioni e quindi il flusso OBO funziona.

### <a name="pre-authorized-applications"></a>Applicazioni preautorizzate

Le risorse possono indicare che una determinata applicazione disponga sempre dell'autorizzazione per ricevere determinati ambiti. Ciò risulta particolarmente utile per semplificare le connessioni tra un client front-end e una risorsa back-end. Una risorsa può dichiarare più applicazioni preautorizzate: qualsiasi applicazione di questo tipo può richiedere tali autorizzazioni in un flusso OBO e riceverle senza che l'utente fornisca il consenso.

### <a name="admin-consent"></a>Consenso dell'amministratore

Un amministratore del tenant può garantire che le applicazioni siano autorizzate a chiamare le API necessarie fornendo il consenso dell'amministratore per l'applicazione di livello intermedio. A tale scopo, l'amministratore può trovare l'applicazione di livello intermedio nel proprio tenant, aprire la pagina delle autorizzazioni necessarie e scegliere di concedere l'autorizzazione per l'app. Per altre informazioni sul consenso dell'amministratore, vedere la [documentazione su autorizzazioni e consenso](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Uso di un'applicazione singola

In alcuni scenari può esserci solo una singola associazione del client di livello intermedio e front-end. In questo scenario può risultare più semplice impostare l'applicazione come singola, senza la necessità di un'applicazione di livello intermedio. Per eseguire l'autenticazione tra il front-end e l'API Web, è possibile usare i cookie, un id_token o un token di accesso richiesto per l'applicazione stessa. Quindi, richiedere il consenso da questa applicazione singola alla risorsa back-end.

## <a name="client-limitations"></a>Limitazioni client

Se un client usa il flusso implicito per ottenere un id_token e quel client dispone anche di caratteri jolly in un URL di risposta, non è possibile usare l'id_token per un flusso OBO.  Tuttavia, i token di accesso acquisiti tramite il flusso di concessione implicita possono ancora essere riscattati da un client riservato anche se il client di origine dispone di un URL di risposta con caratteri jolly registrato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul protocollo OAuth 2.0 e su un altro modo per eseguire l'autenticazione da servizio a servizio usando le credenziali del client.

* [Concessione di credenziali client OAuth 2.0 in Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [Flusso del codice OAuth 2.0 in Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Uso dell'ambito `/.default`](v2-permissions-and-consent.md#the-default-scope)
