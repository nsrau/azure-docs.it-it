<properties
	pageTitle="Panoramica del protocollo .NET per Azure AD | Microsoft Azure"
	description="Questo articolo descrive come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory e OAuth 2.0."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) usa OAuth 2.0 per consentire di autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant di Azure AD. Questa guida, indipendente dal linguaggio, descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie open source di Microsoft.

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui app Web e app native.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## Flusso di autorizzazione di OAuth 2.0

A livello generale, l'intero flusso di autenticazione per un'applicazione ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## Richiedere un codice di autorizzazione

Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize`. In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente. È possibile ottenere gli endpoint di OAuth 2.0 nella pagina dell'applicazione nel portale di Azure classico, con il pulsante **Visualizza endpoint** nel pannello in basso.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il valore `{tenant}` nel percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com`, e `common` per i token indipendenti dai tenant. |
| client\_id | Obbligatorio | ID applicazione assegnato all'app quando è stata registrata in Azure AD. È disponibile nel portale di gestione di Azure. Fare clic su **Active Directory** e quindi sulla directory, sull'applicazione e infine su **Configura**. |
| response\_type | Obbligatorio | Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect\_uri | consigliato | URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL. Per le app native e le app per dispositivi mobili è consigliabile usare il valore predefinito `urn:ietf:wg:oauth:2.0:oob`. |
| scope | Obbligatorio | Elenco separato da spazi di [ambiti](active-directory-v2-scopes.md) a cui si vuole che l'utente dia il consenso. |
| response\_mode | Consigliato | Specifica il metodo da usare per restituire il token risultante all'app. Può essere `query` o `form_post`. |
| state | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Per [prevenire attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12) viene in genere usato un valore univoco generato in modo casuale. Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| prompt | Facoltativo | Indica il tipo di interazione obbligatoria dell'utente. Gli unici valori validi in questa fase sono "login", "none" e "consent". `prompt=login` impone all'utente di immettere le credenziali per la richiesta, negando l'accesso Single Sign-On, mentre `prompt=none` provoca l'effetto contrario, ovvero garantisce che all'utente non venga presentato alcun prompt interattivo. Se la richiesta non può essere completata automaticamente tramite Single-Sign-On, l'endpoint 2.0 restituirà un errore. `prompt=consent` attiverà la finestra di dialogo di consenso di OAuth in seguito all'accesso dell'utente, chiedendo all'utente di concedere le autorizzazioni per l'app. |
| login\_hint | Facoltativo | Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente. Le app in genere usano questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente con l'attestazione `preferred_username`. |
| domain\_hint | Facoltativo | Può essere `consumers` o `organizations`. Se incluso, non verrà eseguito il processo di individuazione basata sulla posta elettronica a cui viene sottoposto l'utente nella pagina di accesso della versione 2.0. Questo comporta un'esperienza utente leggermente semplificata. Le app in genere usano questo parametro durante la riautenticazione, estraendo `tid` da un accesso precedente. Se il valore dell'attestazione `tid` è `9188040d-6c67-4c5b-b112-36a304b66dad`, è consigliabile usare `domain_hint=consumers`. In caso contrario, usare `domain_hint=organizations`. |

> [AZURE.NOTE] Se l'utente fa parte di un'organizzazione, un amministratore dell'organizzazione può acconsentire o rifiutare per conto dell'utente oppure permettere all'utente di dare il consenso. L'utente ha la possibilità di dare il consenso solo se l'amministratore lo permette.

A questo punto, all'utente verrà richiesto di immettere le proprie credenziali e dare il consenso per le autorizzazioni indicate nel parametro di query `scope`. Dopo che l'utente ha eseguito l'autenticazione e concesso il consenso, Azure AD invia una risposta all'app all'indirizzo `redirect_uri` della richiesta.

### Risposta con esito positivo

Una risposta con esito positivo può avere un aspetto simile al seguente:

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| Parametro | Descrizione |
| -----------------------| --------------- |
| admin\_consent | Il valore è True se un amministratore ha acconsentito a una richiesta di consenso.|
| code | Codice di autorizzazione richiesto dall'applicazione. L'applicazione può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. |
| session\_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state | Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. L'applicazione deve verificare i valori dello stato nella richiesta.
Se nella richiesta è stato incluso un parametro di stato, lo stesso valore viene visualizzato nella risposta. È consigliabile che l'applicazione verifichi che i valori dello stato nella richiesta e nella risposta siano identici.

### Risposta di errore

Le risposte di errore possono essere inviate anche a `redirect_uri`, in modo che l'applicazione possa gestirle adeguatamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## Usare un codice di autorizzazione per richiedere un token di accesso

Dopo aver acquisito un codice di autorizzazione e aver ottenuto l'autorizzazione dall'utente, è possibile riscattare il codice per un token di accesso per la risorsa desiderata inviando una richiesta POST all'endpoint `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | Obbligatorio | Il valore `{tenant}` nel percorso della richiesta può essere usato per controllare chi può accedere all'applicazione. I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com`, e `common` per i token indipendenti dai tenant. |
| client\_id | Obbligatorio | ID applicazione assegnato all'app quando è stata registrata in Azure AD. È disponibile nel portale di Azure classico. Fare clic su **Active Directory** e quindi sulla directory, sull'applicazione e infine su **Configura**. |
| grant\_type | Obbligatorio | Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| code | Obbligatorio | Il valore `authorization_code` acquisito nella sezione precedente. |
| redirect\_uri | Obbligatorio | Lo stesso valore `redirect_uri` usato per acquisire `authorization_code`. |
| client\_secret | Obbligatorio per app Web | Segreto dell'applicazione creato per l'app nel portale di registrazione delle app. È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi. Il segreto è obbligatorio per le app Web e le API Web che possono archiviare `client_secret` in modo sicuro sul lato server. |


### Risposta con esito positivo

Una risposta con esito positivo può avere un aspetto simile al seguente:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| access\_token | Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token\_type | Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. Per altre informazioni sui token di connessione, vedere la [specifica relativa all'uso dei token di autorizzazione del framework di autorizzazione di OAuth2.0 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires\_in | Validità del token di accesso (espressa in secondi). |
| scope | Autorizzazioni di rappresentazione concesse all'applicazione client. L'autorizzazione predefinita è `user_impersonation`. Il proprietario della risorsa protetta può registrare valori aggiuntivi in Azure AD.|
| refresh\_token | Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente. I token di aggiornamento hanno durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |
| id\_token | Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. |

### Attestazioni nei token JWT
Il token JWT nel valore del parametro `id_token` può essere decodificato nelle attestazioni seguenti:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Il parametro `id_token` include i tipi di attestazione riportati di seguito. Per altre informazioni sui token Web JSON, vedere la [bozza di specifica di JWT di IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Per altre informazioni sui tipi di token e sulle attestazioni, vedere [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md).

| Tipo di attestazione | Descrizione |
|------------|-------------|
| aud | Destinatari del token. Quando il token viene rilasciato a un'applicazione client, il destinatario è il `client_id` del client.
| exp | Scadenza. Data e ora in cui scade il token. Perché il token sia valido, il valore di data/ora corrente deve essere minore o uguale al valore di `exp`. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| family\_name | Cognome dell'utente. Questo valore può essere visualizzato dall'applicazione. |
| given\_name | Nome dell'utente. Questo valore può essere visualizzato dall'applicazione. |
| iat | Data e ora di rilascio. Data e ora in cui il token JWT è stato rilasciato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| iss | Identifica l'autorità di certificazione del token. |
| nbf | Inizio validità. Data e ora in cui il token diventa valido. Perché il token sia valido, il valore di data/ora corrente deve essere maggiore o uguale al valore di nbf. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| oid | Identificatore (ID) dell'oggetto utente in Azure AD. |
| sub | Identificatore del soggetto del token. È un identificatore permanente e non modificabile dell'utente descritto dal token. Usare questo valore nella logica di memorizzazione nella cache. |
| tid | Identificatore (ID) del tenant di Azure AD che ha rilasciato il token. |
| unique\_name | Identificatore univoco che può essere visualizzato all'utente. È in genere il nome dell'entità utente (UPN). |
| upn | Nome dell'entità utente dell'utente. |
| ver | Versione. Versione del token JWT, in genere 1.0. |

### Risposta di errore

Una risposta di errore di esempio può avere un aspetto simile al seguente:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| error | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error\_description | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error\_codes | Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp | Ora in cui si è verificato l'errore. |
| trace\_id | Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation\_id | Identificatore univoco per la richiesta utile per la diagnostica tra i componenti.|

## Usare il token di accesso per accedere alla risorsa

Dopo aver acquisito un `access_token`, è possibile usare il token in richieste alle API Web includendolo nell'intestazione `Authorization`. La specifica [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) illustra come usare i token di connessione nelle richieste HTTP per accedere a risorse protette.

### Richiesta di esempio

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## Aggiornamento dei token di accesso

I token di accesso hanno breve durata ed è necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. È possibile aggiornare `access_token` inviando un'altra richiesta `POST` all'endpoint `/token` e specificando `refresh_token` anziché il valore `code`.

La durata di un token di aggiornamento non è specificata e varia in base alle impostazioni dei criteri e al momento in cui la concessione del codice di autorizzazione viene revocata da Azure AD. L'applicazione deve prevedere e gestire i casi in cui la richiesta di un nuovo token di accesso ha esito negativo. Se ciò si verifica, deve tornare al codice che richiede un nuovo token di accesso.

Una richiesta di esempio all'endpoint **specifico del tenant** (è possibile usare anche l'endpoint **common**) per ottenere un nuovo token di accesso usando un token di aggiornamento si presenta come segue:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parametro | Descrizione |
|-----------|-------------|
| access\_token | Nuovo token di accesso che è stato richiesto.|
| expires\_in | Durata residua del token, in secondi. Un valore tipico è 3600 (un'ora). |
| expires\_on | Data e ora in cui scadrà il token. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. |
| refresh\_token | Nuovo refresh\_token OAuth 2.0 che può essere usato per richiedere nuovi token di accesso alla scadenza del token di questa risposta. |
| resource | Identifica la risorsa protetta a cui il token di accesso consente di accedere. |
| scope | Autorizzazioni di rappresentazione concesse all'applicazione client nativa. L'autorizzazione predefinita è **user\_impersonation**. Il proprietario della risorsa di destinazione può registrare valori alternativi in Azure AD. |
| token\_type | Tipo di token. L'unico valore supportato è **bearer**. |

### Risposta con esito positivo

Una risposta token con esito positivo ha un aspetto simile al seguente:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### Risposta di errore

Una risposta di errore di esempio può avere un aspetto simile al seguente:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0601_2016-->