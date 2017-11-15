---
title: Autenticazione da servizio a servizio di Azure AD tramite la specifica on-behalf-of di OAuth2.0 | Microsoft Docs
description: Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso on-behalf-of di OAuth2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0bb74816f216f0965c3ec780c4895cf7e488c3cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Chiamate da servizio a servizio tramite l'identità utente delegato nel flusso on-behalf-of
Il flusso on-behalf-of di OAuth 2.0 viene usato quando un'applicazione richiama un servizio o un'API Web, che a sua volta deve chiamare un altro servizio o un'altra API Web. Lo scopo è di propagare l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Per eseguire richieste autenticate al servizio downstream, il servizio di livello intermedio deve assicurarsi un token di accesso da Azure Active Directory (Azure AD) per conto dell'utente.

## <a name="on-behalf-of-flow-diagram"></a>Diagramma del flusso on-behalf-of
Si supponga che l'utente sia stato autenticato in un'applicazione usando il [flusso di concessione del codice di autorizzazione OAuth 2.0](active-directory-protocols-oauth-code.md). A questo punto, l'applicazione contiene un token di accesso (token A) con le richieste dell'utente e il consenso per accedere all'API Web di livello intermedio (API A). L'API A deve ora eseguire una richiesta autenticata all'API Web downstream (API B).

I passaggi che seguono costituiscono il flusso on-behalf-of e vengono descritti con l'aiuto del diagramma seguente.

![Flusso on-behalf-of di OAuth2.0](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. L'applicazione client esegue una richiesta all'API A con il token A.
2. L'API A esegue l'autenticazione all'endpoint di rilascio del token di Azure AD e richiede un token per accedere all'API B.
3. L'endpoint di rilascio del token di Azure AD convalida le credenziali dell'API A con il token A ed emette il token di accesso per l'API B (token B).
4. Il token B viene impostato nell'intestazione di autorizzazione della richiesta all'API B.
5. I dati della risorsa protetta vengono restituiti dall'API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrare l'applicazione e il servizio in Azure AD
Registrare sia l'applicazione client che il servizio di livello intermedio in Azure AD.
### <a name="register-the-middle-tier-service"></a>Registrare il servizio di livello intermedio
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Registrazione nuova applicazione**.
5. Immettere un nome descrittivo per l'applicazione e selezionare il tipo di applicazione. In base al tipo di applicazione, impostare l'URL di accesso o l'URL di reindirizzamento sull'URL di base. Fare clic su **Crea** per creare l'applicazione.
6. Sempre nel portale di Azure scegliere l'applicazione e fare clic su **Impostazioni**. Nel menu Impostazioni scegliere **Chiavi** e aggiungere una chiave. Selezionare una durata della chiave di 1 o 2 anni. Quando si salva la pagina, viene visualizzato il valore della chiave. È necessario copiare e salvare tale valore in un luogo sicuro in quanto servirà in seguito per configurare le impostazioni dell'applicazione nell'implementazione. Il valore della chiave non viene più visualizzato e non è in alcun modo recuperabile in un secondo momento, pertanto è necessario prenderne nota nel momento in cui è visibile nel portale di Azure.

### <a name="register-the-client-application"></a>Registrare l'applicazione client
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **Registrazioni per l'app** e scegliere **Registrazione nuova applicazione**.
5. Immettere un nome descrittivo per l'applicazione e selezionare il tipo di applicazione. In base al tipo di applicazione, impostare l'URL di accesso o l'URL di reindirizzamento sull'URL di base. Fare clic su **Crea** per creare l'applicazione.
6. Configurare le autorizzazioni per l'applicazione. Nel menu Impostazioni scegliere la sezione **Autorizzazioni necessarie**, fare clic su **Aggiungi**, quindi **Selezionare un'API** e digitare il nome del servizio di livello intermedio nella casella di testo. Fare quindi clic su **Selezionare le autorizzazioni** e selezionare "Accedi a *nome servizio*".

### <a name="configure-known-client-applications"></a>Configurare applicazioni client note
In questo scenario, il servizio di livello intermedio non ha alcuna interazione utente per ottenere il consenso dell'utente per accedere all'API downstream. La possibilità di concedere l'accesso all'API downstream deve quindi essere presentata in anticipo, come parte del passaggio relativo al consenso durante l'autenticazione.
Seguire a tale scopo la procedura sottostante per associare in modo esplicito la registrazione dell'app client in Azure AD con la registrazione del servizio di livello intermedio, che unisce il consenso richiesto sia dal client sia dal livello intermedio in una singola finestra di dialogo.
1. Passare alla registrazione del servizio di livello intermedio e fare clic su **Manifesto** per aprire l'editor manifesto.
2. Nel manifesto individuare la proprietà di matrice `knownClientApplications` e aggiungere l'ID client dell'applicazione client come elemento.
3. Salvare il manifesto facendo clic su Salva.

## <a name="service-to-service-access-token-request"></a>Richiesta del token di accesso da servizio a servizio
Per richiedere un token di accesso, eseguire una richiesta HTTP POST all'endpoint di Azure AD specifico del tenant con i parametri seguenti.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
L'applicazione client può scegliere di essere protetta da un segreto condiviso oppure da un certificato.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso
Quando si usa un segreto condiviso, una richiesta di token di accesso da servizio a servizio contiene i parametri seguenti:

| Parametro |  | Descrizione |
| --- | --- | --- |
| grant_type |Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |Obbligatoria | Il valore del token usato nella richiesta. |
| client_id |Obbligatoria | L'ID app assegnato al servizio chiamante durante la registrazione con Azure AD. Per trovare l'ID app nel portale di gestione di Azure, fare clic su **Active Directory**, selezionare la directory e quindi fare clic sul nome dell'applicazione. |
| client_secret |Obbligatoria | La chiave registrata per il servizio chiamante in Azure AD. È necessario prendere nota di questo valore al momento della registrazione. |
| resource |Obbligatoria | L'URI dell'ID app del servizio ricevente (risorsa protetta). Per trovare l'URI dell'ID app, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sul nome dell'applicazione, scegliere **Tutte le impostazioni** e quindi fare clic su **Proprietà**. |
| requested_token_use |Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso on-behalf-of il valore deve essere **on_behalf_of**. |
| scope |Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per OpenID Connect, è necessario specificare l'ambito **openid**.|

#### <a name="example"></a>Esempio
La richiesta HTTP POST seguente richiede un token di accesso per l'API Web https://graph.windows.net. `client_id` identifica il servizio che richiede il token di accesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Secondo caso: richiesta del token di accesso con un certificato
Una richiesta di token di accesso da servizio a servizio con un certificato contiene i parametri seguenti:

| Parametro |  | Descrizione |
| --- | --- | --- |
| grant_type |Obbligatoria | Il tipo di richiesta del token. Per una richiesta con un token JWT, il valore deve essere **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |Obbligatoria | Il valore del token usato nella richiesta. |
| client_id |Obbligatoria | L'ID app assegnato al servizio chiamante durante la registrazione con Azure AD. Per trovare l'ID app nel portale di gestione di Azure, fare clic su **Active Directory**, selezionare la directory e quindi fare clic sul nome dell'applicazione. |
| client_assertion_type |Obbligatoria |Il valore deve essere `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione.  Leggere l'articolo relativo alle [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sulla registrazione del certificato e il formato dell'asserzione.|
| resource |Obbligatoria | L'URI dell'ID app del servizio ricevente (risorsa protetta). Per trovare l'URI dell'ID app, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sul nome dell'applicazione, scegliere **Tutte le impostazioni** e quindi fare clic su **Proprietà**. |
| requested_token_use |Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso on-behalf-of il valore deve essere **on_behalf_of**. |
| scope |Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per OpenID Connect, è necessario specificare l'ambito **openid**.|

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro client_secret che viene sostituito da due parametri: client_assertion_type e client_assertion.

#### <a name="example"></a>Esempio
La richiesta HTTP POST seguente richiede un token di accesso per l'API Web https://graph.windows.net con un certificato. `client_id` identifica il servizio che richiede il token di accesso.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Risposta del token di accesso da servizio a servizio
Una risposta di esito positivo è una risposta OAuth 2.0 JSON con i parametri seguenti.

| Parametro | Descrizione |
| --- | --- |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è **Bearer**. Per altre informazioni sui token di connessione, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)(Framework di autorizzazione di OAuth 2.0: uso dei token di connessione - RFC 6750). |
| scope |L'ambito di accesso concesso nel token. |
| expires_in |Il periodo di validità del token di accesso (in secondi). |
| expires_on |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
| resource |L'URI dell'ID app del servizio ricevente (risorsa protetta). |
| access_token |Token di accesso richiesto. Il servizio chiamante può usare questo token per l'autenticazione nel servizio ricevente. |
| id_token |Il token ID richiesto. Il servizio chiamante può usarlo per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| refresh_token |Il token di aggiornamento per il token di accesso richiesto. Il servizio chiamante può usare questo token per richiedere un altro token di accesso dopo la scadenza di quello corrente. |

### <a name="success-response-example"></a>Esempio di risposta di esito positivo
L'esempio seguente mostra una risposta di esito positivo a una richiesta di token di accesso per l'API Web https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Esempio di risposta con errore
Quando si tenta di acquisire un token di accesso per l'API downstream, se questa dispone di criteri di accesso condizionale, ad esempio l'autenticazione a più fattori impostata, l'endpoint del token di Azure AD restituisce una risposta con errore. Il servizio di livello intermedio dovrebbe segnalare l'errore all'applicazione client in modo che questa possa fornire l'interazione dell'utente per soddisfare i criteri di accesso condizionale.

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
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul protocollo OAuth 2.0 e su un altro modo per eseguire l'autenticazione da servizio a servizio usando le credenziali del client.
* [Service to service auth using OAuth 2.0 client credentials grant in Azure AD](active-directory-protocols-oauth-service-to-service.md) (Autenticazione da servizio a servizio tramite la concessione delle credenziali client OAuth 2.0 in Azure AD)
* [OAuth 2.0 in Azure AD](active-directory-protocols-oauth-code.md)
