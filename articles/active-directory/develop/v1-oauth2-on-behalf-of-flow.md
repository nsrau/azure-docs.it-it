---
title: Autenticazione da servizio a servizio di Azure Active Directory che usa la specifica Outh2.0 On-Behalf-Of| Microsoft Docs
description: Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso on-behalf-of di OAuth2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb64aa401838451191a830a5adbfb435ac5fdf25
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261944"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Chiamate da servizio a servizio tramite l'identità utente delegato nel flusso on-behalf-of

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Il flusso On-Behalf-Of (OBO) di OAuth 2.0 consente a un'applicazione che richiama un servizio o l'API Web di superare l'autenticazione utente a un altro servizio Web o API Web. Il flusso Obi propaga l'identità utente delegato e le autorizzazioni attraverso la catena di richieste. Per eseguire richieste autenticate al servizio downstream, il servizio di livello intermedio deve assicurarsi un token di accesso da Azure Active Directory (Azure AD) per conto dell'utente.

> [!IMPORTANT]
> A partire da maggio 2018, un `id_token` non può essere usato per il flusso On-Behalf-Of.  Le applicazioni a pagina singola (Spa) devono passare il token di accesso a un client riservato di livello intermedio per eseguire i flussi OBO. Vedere [limitazioni](#client-limitations) per altri dettagli sui client che possono effettuare chiamate on-behalf-of.

## <a name="on-behalf-of-flow-diagram"></a>Diagramma del flusso on-behalf-of

Il flusso Obo inizia dopo che l'utente è stato autenticato in un'applicazione usando il [flusso di concessione del codice di autorizzazione OAuth 2.0](v1-protocols-oauth-code.md). A questo punto, l'applicazione invia un token di accesso (token A) che contiene le API Web (API A) contenenti le attestazioni dell'utente e il consenso per accedere all'API A. Successivamente, l'API A esegue una richiesta autenticata al downstream API Web (API B).

Questi passaggi costituiscono il flusso On-Behalf-Of: ![Mostra i passaggi del flusso per conto di OAuth 2.0](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. L'applicazione client esegue una richiesta all'API A con il token A.
1. L'API A esegue l'autenticazione all'endpoint di rilascio del token di Azure AD e richiede un token per accedere all'API B.
1. L'endpoint di rilascio del token di Azure AD convalida le credenziali dell'API A con il token A ed emette il token di accesso per l'API B (token B).
1. La richiesta all'API B contiene il token B nell'intestazione di autorizzazione.
1. I dati della risorsa protetta vengono restituiti dall'API B.

>[!NOTE]
>L'attestazione audience in un token di accesso usato per richiedere un token per un servizio downstream deve essere l'ID del servizio che effettua la richiesta OBO. Il token deve inoltre essere firmato con la chiave di firma globale di Azure Active Directory (ovvero il valore predefinito per le applicazioni registrate tramite **Registrazioni app** nel portale).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrare l'applicazione e il servizio in Azure AD

Registrare sia l'applicazione client che il servizio di livello intermedio in Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrare il servizio di livello intermedio

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto selezionare l'account e guardare nell'elenco **Directory** per scegliere il tenant di Active Directory per l'applicazione.
1. Fare clic su **Altri servizi** nel riquadro a sinistra e scegliere **Azure Active Directory**.
1. Selezionare **registrazioni app** e quindi **nuova registrazione**.
1. Immettere un nome descrittivo per l'applicazione e selezionare il tipo di applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Impostare l'URI di reindirizzamento sull'URL di base.
1. Selezionare **Registra** per creare l'applicazione.
1. Generare un segreto client prima di uscire dal portale di Azure.
1. Nella portale di Azure scegliere l'applicazione e selezionare **certificati & segreti**.
1. Selezionare **nuovo segreto client** e aggiungere un segreto con una durata di un anno o di due anni.
1. Quando si salva questa pagina, nel portale di Azure viene visualizzato il valore Secret. Copiare e salvare il valore del segreto in un luogo sicuro.

> [!IMPORTANT]
> È necessario il segreto per configurare le impostazioni dell'applicazione nell'implementazione. Il valore del segreto non viene visualizzato di nuovo e non è recuperabile con altri mezzi. Registrarlo non appena è visibile nel portale di Azure.

### <a name="register-the-client-application"></a>Registrare l'applicazione client

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra in alto selezionare l'account e guardare nell'elenco **Directory** per scegliere il tenant di Active Directory per l'applicazione.
1. Fare clic su **Altri servizi** nel riquadro a sinistra e scegliere **Azure Active Directory**.
1. Selezionare **registrazioni app** e quindi **nuova registrazione**.
1. Immettere un nome descrittivo per l'applicazione e selezionare il tipo di applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali**.
1. Impostare l'URI di reindirizzamento sull'URL di base.
1. Selezionare **Registra** per creare l'applicazione.
1. Configurare le autorizzazioni per l'applicazione. In **autorizzazioni API**selezionare **Aggiungi un'autorizzazione** e quindi **API personali**.
1. Digitare il nome del servizio di livello intermedio nel campo di testo.
1. Scegliere **Seleziona autorizzazioni** e quindi selezionare **nome \<servizio di accesso >** .

### <a name="configure-known-client-applications"></a>Configurare applicazioni client note

In questo scenario, il servizio di livello intermedio deve ottenere il consenso dell'utente per accedere all'API downstream senza un'interazione utente. La possibilità di concedere l'accesso all'API downstream deve essere presentata in anticipo, come parte del passaggio relativo al consenso durante l'autenticazione.

Attenersi alla procedura seguente per associare in modo esplicito la registrazione dell'app client in Azure AD con la registrazione del servizio di livello intermedio. Questa operazione unisce in una singola finestra di dialogo il consenso richiesto dal livello intermedio e dal client.

1. Passare alla registrazione del servizio di livello intermedio e selezionare **Manifesto** per aprire l'editor manifesto.
1. Individuare la proprietà di matrice `knownClientApplications` e aggiungere l'ID client dell'applicazione client come elemento.
1. Salvare il manifesto selezionando **Salva**.

## <a name="service-to-service-access-token-request"></a>Richiesta del token di accesso da servizio a servizio

Per richiedere un token di accesso, eseguire una richiesta HTTP POST all'endpoint di Azure AD specifico del tenant con i parametri seguenti:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

L'applicazione client è protetta da un segreto condiviso o da un certificato.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primo caso: richiesta del token di accesso con un segreto condiviso

Quando si usa un segreto condiviso, una richiesta di token di accesso da servizio a servizio contiene i parametri seguenti:

| Parametro |  | DESCRIZIONE |
| --- | --- | --- |
| grant_type |Obbligatoria | Il tipo di richiesta del token. Una richiesta OBO usa un token Web JSON (JWT), il valore deve essere **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |Obbligatoria | Il valore del token di accesso usato nella richiesta. |
| client_id |Obbligatoria | L'ID app assegnato al servizio chiamante durante la registrazione con Azure AD. Per trovare l'ID app nel portale di Azure, selezionare **Active Directory**, scegliere la directory e quindi selezionare il nome dell'applicazione. |
| client_secret |Obbligatoria | La chiave registrata per il servizio chiamante in Azure AD. È necessario prendere nota di questo valore al momento della registrazione. |
| resource |Obbligatoria | L'URI dell'ID app del servizio ricevente (risorsa protetta). Per trovare l'URI ID app nel portale di Azure, selezionare **Active Directory** e scegliere la directory. Selezionare il nome dell'applicazione, scegliere **Tutte le impostazioni**, quindi selezionare **Proprietà**. |
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
| grant_type |Obbligatoria | Il tipo di richiesta del token. Una richiesta OBO usa un token di accesso JWT, il valore deve essere **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |Obbligatoria | Il valore del token usato nella richiesta. |
| client_id |Obbligatoria | L'ID app assegnato al servizio chiamante durante la registrazione con Azure AD. Per trovare l'ID app nel portale di Azure, selezionare **Active Directory**, scegliere la directory e quindi selezionare il nome dell'applicazione. |
| client_assertion_type |Obbligatoria |Il valore deve essere `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |Obbligatoria | Un token JSON Web che viene creato e firmato con il certificato registrato come credenziale per l'applicazione. Vedere [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sul formato dell'asserzione e su come registrare il certificato.|
| resource |Obbligatoria | L'URI dell'ID app del servizio ricevente (risorsa protetta). Per trovare l'URI ID app nel portale di Azure, selezionare **Active Directory** e scegliere la directory. Selezionare il nome dell'applicazione, scegliere **Tutte le impostazioni**, quindi selezionare **Proprietà**. |
| requested_token_use |Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso on-behalf-of il valore deve essere **on_behalf_of**. |
| scope |Obbligatoria | Un elenco di ambiti separati da spazi per la richiesta di token. Per OpenID Connect, è necessario specificare l'ambito **openid**.|

Questi parametri sono quasi identici della richiesta tramite segreto condiviso con la differenza che il `client_secret parameter` viene sostituito da due parametri: `client_assertion_type` e `client_assertion`.

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

Una risposta di esito positivo è una risposta OAuth 2.0 JSON con i parametri seguenti:

| Parametro | Descrizione |
| --- | --- |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è **Bearer**. Per altre informazioni sui bearer token, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750). |
| scope |L'ambito di accesso concesso nel token. |
| expires_in |Il periodo di validità del token di accesso (in secondi). |
| expires_on |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
| resource |L'URI dell'ID app del servizio ricevente (risorsa protetta). |
| access_token |Token di accesso richiesto. Il servizio chiamante può usare questo token per l'autenticazione nel servizio ricevente. |
| id_token |Il token ID richiesto. Il servizio chiamante può usare questo token per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| refresh_token |Il token di aggiornamento per il token di accesso richiesto. Il servizio chiamante può usare questo token per richiedere un altro token di accesso dopo la scadenza di quello corrente. |

### <a name="success-response-example"></a>Esempio di risposta di esito positivo

L'esempio seguente mostra una risposta corretta a una richiesta di token di accesso per l'API Web https://graph.windows.net.

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

L'endpoint del token Azure AD restituisce una risposta di errore quando tenta di acquisire un token di accesso per un'API downstream impostata con criteri di accesso condizionale, ad esempio l'autenticazione a più fattori. Il servizio di livello intermedio deve esporre questo errore all'applicazione client in modo che l'applicazione client possa fornire l'interazione dell'utente per soddisfare i criteri di accesso condizionale.

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

Il servizio di livello intermedio può usare il token acquisito per eseguire richieste autenticate all'API Web downstream, impostando il token nell'intestazione `Authorization`.

### <a name="example"></a>Esempio

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Asserzioni SAML ottenute con un flusso di OAuth2.0 OBO

Alcuni servizi Web di OAuth devono accedere ad altre API di servizi Web che accettano le asserzioni SAML in flussi non interattivi. Azure AD può fornire un'asserzione SAML in risposta a un flusso on-behalf-of che usa un servizio Web SAML come risorsa di destinazione.

>[!NOTE]
>Si tratta di un'estensione non standard del flusso on-behalf-of di OAuth 2.0 che consente a un'applicazione OAuth2 di accedere agli endpoint API del servizio Web che usano token SAML.

> [!TIP]
> Se un servizio Web protetto con SAML viene chiamato da un'applicazione Web front-end, è sufficiente chiamare l'API e avviare un flusso di autenticazione interattiva normale con la sessione esistente degli utenti. È necessario usare un flusso OBO quando una chiamata da servizio a servizio richiede un token SAML per fornire il contesto utente.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Ottenere un token SAML usando una richiesta OBO con un segreto condiviso

Una richiesta da servizio a servizio per un'asserzione SAML contiene i parametri seguenti:

| Parametro |  | Descrizione |
| --- | --- | --- |
| grant_type |Obbligatoria | Il tipo di richiesta del token. Per una richiesta che usa un JWT, il valore deve essere **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |Obbligatoria | Il valore del token di accesso usato nella richiesta.|
| client_id |Obbligatoria | L'ID app assegnato al servizio chiamante durante la registrazione con Azure AD. Per trovare l'ID app nel portale di Azure, selezionare **Active Directory**, scegliere la directory e quindi selezionare il nome dell'applicazione. |
| client_secret |Obbligatoria | La chiave registrata per il servizio chiamante in Azure AD. È necessario prendere nota di questo valore al momento della registrazione. |
| resource |Obbligatoria | L'URI dell'ID app del servizio ricevente (risorsa protetta). Si tratta della risorsa che rappresenta i destinatari del token SAML. Per trovare l'URI ID app nel portale di Azure, selezionare **Active Directory** e scegliere la directory. Selezionare il nome dell'applicazione, scegliere **Tutte le impostazioni**, quindi selezionare **Proprietà**. |
| requested_token_use |Obbligatoria | Specifica la modalità di elaborazione della richiesta. Nel flusso on-behalf-of il valore deve essere **on_behalf_of**. |
| requested_token_type | Obbligatoria | Specifica il tipo di token richiesto. Il valore può essere **urn:ietf:params:oauth:token-type:saml2** o **urn:ietf:params:oauth:token-type:saml1**, a seconda dei requisiti della risorsa a cui si accede. |

La risposta contiene un token SAML con codifica UTF8 e Base64url.

- **SubjectConfirmationData per un'asserzione SAML che ha origine da una chiamata OBO**: se l'applicazione di destinazione richiede un valore del destinatario in **SubjectConfirmationData**, nella configurazione dell'applicazione della risorsa deve essere impostato come URL di risposta senza caratteri jolly.
- **Nodo SubjectConfirmationData**: Il nodo non può contenere un attributo **InResponseTo** perché non fa parte di una risposta SAML. L'applicazione che riceve il token SAML deve essere in grado di accettare l'asserzione SAML senza un attributo **InResponseTo**.

- **Consenso**: per ricevere un token SAML che contiene i dati utente in un flusso OAuth, è necessario aver autorizzato il consenso. Per informazioni sulle autorizzazioni e su come ottenere il consenso dell'amministratore, vedere [Autorizzazioni e consenso nell'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Risposta con asserzione SAML

| Parametro | DESCRIZIONE |
| --- | --- |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è **Bearer**. Per altre informazioni sui bearer token, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Framework di autorizzazione di OAuth 2.0: uso dei bearer token - RFC 6750). |
| scope |L'ambito di accesso concesso nel token. |
| expires_in |Il periodo di validità del token di accesso (in secondi). |
| expires_on |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
| resource |L'URI dell'ID app del servizio ricevente (risorsa protetta). |
| access_token |Il parametro che restituisce l'asserzione SAML. |
| refresh_token |Token di aggiornamento. Il servizio chiamante può usare questo token per richiedere un altro token di accesso dopo la scadenza dell'asserzione SAML corrente. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- risorsa: `https://api.contoso.com`
- access_token: \<Asserzione SAML\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<token di aggiornamento\>

## <a name="client-limitations"></a>Limitazioni client

Client pubblici con gli URL di risposta con caratteri jolly non possono utilizzare un `id_token` per i flussi OBO. Tuttavia, i token di **accesso** acquisiti tramite il flusso di concessione implicita possono ancora essere riscattati da un client riservato anche se il client pubblico ha un URI di reindirizzamento con caratteri jolly registrato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul protocollo OAuth 2.0 e su un altro modo per eseguire l'autenticazione da servizio a servizio che usa le credenziali del client:

* [Autenticazione da servizio a servizio tramite la concessione delle credenziali client OAuth 2.0 in Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 in Azure AD](v1-protocols-oauth-code.md)
