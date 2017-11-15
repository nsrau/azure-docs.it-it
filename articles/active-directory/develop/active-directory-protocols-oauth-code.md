---
title: Informazioni sul flusso del codice di autorizzazione OAuth 2.0 in Azure AD | Documentazione Microsoft
description: Questo articolo descrive come usare messaggi HTTP per autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant con Azure Active Directory e OAuth 2.0.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: de3412cb-5fde-4eca-903a-4e9c74db68f2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 916652f2d6336da625be91431c3771a730204a73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory
Azure Active Directory (Azure AD) usa OAuth 2.0 per consentire di autorizzare l'accesso ad applicazioni Web e API Web nel proprio tenant di Azure AD. Questa guida, indipendente dal linguaggio, descrive come inviare e ricevere messaggi HTTP senza usare una delle librerie open source di Microsoft.

Il flusso del codice di autorizzazione di OAuth 2.0 è descritto nella [sezione 4.1 della specifica di OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Viene usato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, tra cui app Web e app native.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Flusso di autorizzazione di OAuth 2.0
A livello generale, l'intero flusso di autenticazione per un'applicazione ha un aspetto analogo al seguente:

![Flusso del codice di autenticazione di OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Richiedere un codice di autorizzazione
Il flusso del codice di autorizzazione ha inizio con il client che indirizza l'utente all'endpoint `/authorize` . In questa richiesta, il client indica le autorizzazioni che deve acquisire dall'utente. È possibile ottenere gli endpoint di OAuth 2.0 nella pagina dell'applicazione nel portale di Azure classico, con il pulsante **Visualizza endpoint** nel pannello in basso.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` o `common` per i token indipendenti dai tenant |
| client_id |Obbligatoria |ID applicazione assegnato all'app quando è stata registrata in Azure AD. ed è reperibile nel portale di Azure. Fare clic su **Active Directory** e quindi sulla directory, sull'applicazione e infine su **Configura** |
| response_type |Obbligatoria |Deve includere `code` per il flusso del codice di autorizzazione. |
| redirect_uri |consigliato |URI di reindirizzamento dell'app dove le risposte di autenticazione possono essere inviate e ricevute dall'app.  Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale, ad eccezione del fatto che deve essere codificato come URL.  Per le app native e le app per dispositivi mobili è necessario usare il valore predefinito `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Consigliato |Specifica il metodo da usare per restituire il token risultante all'app.  Può essere `query` o `form_post`. |
| state |consigliato |Valore incluso nella richiesta che viene restituito nella risposta del token. Per [evitare gli attacchi di richiesta intersito falsa](http://tools.ietf.org/html/rfc6749#section-10.12), viene in genere usato un valore univoco generato casualmente.  Lo stato viene inoltre usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| resource |Facoltativa |L'URI ID app dell'API Web (risorsa protetta). Per trovare l'URI ID app dell'API Web, nel portale di Azure fare clic su **Active Directory**, fare clic sulla directory e sull'applicazione e quindi su **Configura**. |
| prompt |Facoltativa |Indica il tipo di interazione obbligatoria dell'utente.<p> I valori validi sono: <p> *login*: all'utente deve essere chiesto di ripetere l'autenticazione. <p> *consent*: il consenso dell'utente è stato concesso, ma deve essere aggiornato. All'utente deve essere chiesto di indicare il consenso. <p> *admin_consent*: a un amministratore deve essere chiesto di indicare il consenso per conto di tutti gli utenti dell'organizzazione |
| login_hint |Facoltativa |Consente di pre-compilare il campo nome utente/indirizzo di posta elettronica dell'utente nella pagina di accesso, se già si conosce il nome utente.  Le app usano spesso questo parametro durante la riautenticazione, dopo aver estratto il nome utente da un accesso precedente tramite l'attestazione `preferred_username`. |
| domain_hint |Facoltativa |Offre un suggerimento relativo al tenant o al dominio che l'utente deve usare per accedere. Il valore di domain_hint è un dominio registrato per il tenant. Se il tenant è federato a una directory locale, AAD reindirizza al server federativo tenant specificato. |

> [!NOTE]
> Se l'utente fa parte di un'organizzazione, un amministratore dell'organizzazione può acconsentire o rifiutare per conto dell'utente oppure permettere all'utente di dare il consenso. L'utente ha la possibilità di dare il consenso solo se l'amministratore lo permette.
>
>

A questo punto all'utente verrà chiesto di immettere le credenziali e dare il consenso per le autorizzazioni indicate nel parametro di query `scope` . Dopo che l'utente ha eseguito l'autenticazione e concesso il consenso, Azure AD invia una risposta all'app all'indirizzo `redirect_uri` della richiesta.

### <a name="successful-response"></a>Risposta con esito positivo
Una risposta con esito positivo può avere un aspetto simile al seguente:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametro | Descrizione |
| --- | --- |
| admin_consent |Il valore è True se un amministratore ha acconsentito a una richiesta di consenso. |
| code |Codice di autorizzazione richiesto dall'applicazione. L'applicazione può usare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. |
| session_state |Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere trattato come un valore opaco passato senza alcun controllo. |
| state |Se un parametro di stato è incluso nella richiesta, lo stesso valore viene visualizzato nella risposta. È consigliabile che l'applicazione verifichi che i valori dello stato nella richiesta e nella risposta siano identici, prima di usare la risposta. Ciò consente di rilevare gli [attacchi di richiesta intersito falsa (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contro il client. |

### <a name="error-response"></a>Risposta di errore
Le risposte di errore possono essere inviate anche a `redirect_uri` , in modo che l'applicazione possa gestirle adeguatamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| --- | --- |
| error |Valore del codice di errore definito nella sezione 5.2 del [framework di autorizzazione di OAuth 2.0](http://tools.ietf.org/html/rfc6749). La tabella successiva descrive i codici errore restituiti da Azure AD. |
| error_description |Descrizione più dettagliata dell'errore. Questo messaggio non vuole essere semplice da usare. |
| state |Il valore di state è un valore non riutilizzato generato casualmente che viene inviato nella richiesta e restituito nella risposta per impedire attacchi di tipo richiesta intersito falsa. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici per gli errori dell'endpoint di autorizzazione
La tabella seguente descrive i diversi codici errore che possono essere restituiti nel parametro `error` della risposta di errore.

| Codice di errore | Descrizione | Azione client |
| --- | --- | --- |
| invalid_request |Errore del protocollo, ad esempio un parametro obbligatorio mancante. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| unauthorized_client |All'applicazione client non è consentito richiedere un codice di autorizzazione. |Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| access_denied |Consenso negato dal proprietario della risorsa |L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsenta. |
| unsupported_response_type |Il server di autorizzazione non supporta il tipo di risposta nella richiesta. |Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale. |
| server_error |Errore imprevisto rilevato dal server. |ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| temporarily_unavailable |Il server è temporaneamente troppo occupato per gestire la richiesta. |ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| invalid_resource |La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. |Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Usare un codice di autorizzazione per richiedere un token di accesso
Dopo aver acquisito un codice di autorizzazione e aver ottenuto l'autorizzazione dall'utente, è possibile riscattare il codice per un token di accesso per la risorsa desiderata inviando una richiesta POST all'endpoint `/token` :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametro |  | Descrizione |
| --- | --- | --- |
| tenant |Obbligatoria |Il valore `{tenant}` del percorso della richiesta può essere usato per controllare chi può accedere all'applicazione.  I valori consentiti sono gli identificatori dei tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` o `common` per i token indipendenti dai tenant |
| client_id |Obbligatoria |ID applicazione assegnato all'app quando è stata registrata in Azure AD. È disponibile nel portale di Azure classico. Fare clic su **Active Directory** e quindi sulla directory, sull'applicazione e infine su **Configura** |
| grant_type |Obbligatoria |Deve essere `authorization_code` per il flusso del codice di autorizzazione. |
| code |Obbligatorio |Il valore `authorization_code` acquisito nella sezione precedente |
| redirect_uri |Obbligatoria |Lo stesso valore `redirect_uri` utilizzato per acquisire `authorization_code`. |
| client_secret |Obbligatorio per app Web |Segreto dell'applicazione creato per l'app nel portale di registrazione delle app.  È consigliabile non usarlo in un'app nativa, perché i segreti client non possono essere archiviati in modo affidabile nei dispositivi.  Il segreto è obbligatorio per le app Web e le API Web che possono archiviare `client_secret` in modo sicuro sul lato server. |
| resource |Obbligatorio se è specificato nella richiesta del codice di autorizzazione, altrimenti facoltativo |L'URI ID app dell'API Web (risorsa protetta). |

Per trovare l'URI ID app, nel portale di gestione di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sull'applicazione e infine su **Configura**.

### <a name="successful-response"></a>Risposta con esito positivo
Azure AD restituisce un token di accesso in caso di risposta corretta. Per ridurre al minimo le chiamate di rete dall'applicazione client e la latenza associata, l'applicazione client deve memorizzare i token di accesso nella cache per la durata del token specificata nella risposta OAuth 2.0. Per determinare la durata del token, usare i valori dei parametri `expires_in` o `expires_on`.

Se una risorsa API Web restituisce un codice di errore `invalid_token` , ciò può indicare che la risorsa ha determinato che il token è scaduto. Se l'ora del client e l'ora della risorsa sono diverse ("sfasamento dell'ora"), la risorsa potrebbe considerare il token scaduto prima che venga cancellato dalla cache del client. In questo caso, cancellare il token dalla cache anche se si trova ancora nella durata calcolata.

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
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parametro | Descrizione |
| --- | --- |
| access_token |Token di accesso richiesto. L'app può usare questo token per l'autenticazione alla risorsa protetta, ad esempio un'API Web. |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è Bearer. Per altre informazioni sui token di connessione, vedere [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Validità del token di accesso (espressa in secondi). |
| expires_on |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
| resource |L'URI ID app dell'API Web (risorsa protetta). |
| scope |Autorizzazioni di rappresentazione concesse all'applicazione client. L'autorizzazione predefinita è `user_impersonation`. Il proprietario della risorsa protetta può registrare valori aggiuntivi in Azure AD. |
| refresh_token |Token di aggiornamento di OAuth 2.0. L'app può usare questo token per acquisire token di accesso aggiuntivi dopo la scadenza del token di accesso corrente.  I token di aggiornamento hanno durata elevata e possono essere usati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |
| id_token |Token JWT (Token Web JSON) non firmato. L'app può decodificare i segmenti di questo token per richiedere informazioni sull'utente che ha eseguito l'accesso. L'app può memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per eventuali autorizzazioni o limiti di sicurezza. |

### <a name="jwt-token-claims"></a>Attestazioni nei token JWT
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

Per altre informazioni sui token Web JSON, vedere la [bozza della specifica di JWT di IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Per altre informazioni sui tipi di token e sulle attestazioni, vedere [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md)

Il parametro `id_token` include i tipi di attestazione seguenti:

| Tipo di attestazione | Descrizione |
| --- | --- |
| aud |Destinatari del token. Quando il token viene rilasciato a un'applicazione client, il destinatario è il `client_id` del client. |
| exp |Scadenza. Data e ora in cui scade il token. Perché il token sia valido, il valore di data/ora corrente deve essere minore o uguale al valore di `exp` . L'ora è rappresentata come numero di secondi dal 1° gennaio 1970 (1970-01-01T0:0:0Z) UTC fino all'ora in cui scade la validità del token.|
| family_name |Cognome dell'utente. Questo valore può essere visualizzato dall'applicazione. |
| given_name |Nome dell'utente. Questo valore può essere visualizzato dall'applicazione. |
| iat |Data e ora di rilascio. Data e ora in cui il token JWT è stato rilasciato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| iss |Identifica l'autorità di certificazione del token. |
| nbf |Inizio validità. Data e ora in cui il token diventa valido. Perché il token sia valido, il valore di data/ora corrente deve essere maggiore o uguale al valore di nbf. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| oid |Identificatore (ID) dell'oggetto utente in Azure AD. |
| sub |Identificatore del soggetto del token. È un identificatore permanente e non modificabile dell'utente descritto dal token. Usare questo valore nella logica di memorizzazione nella cache. |
| tid |Identificatore (ID) del tenant di Azure AD che ha rilasciato il token. |
| unique_name |Identificatore univoco che può essere visualizzato all'utente. È in genere il nome dell'entità utente (UPN). |
| upn |Nome dell'entità utente dell'utente. |
| ver |Versione. Versione del token JWT, in genere 1.0. |

### <a name="error-response"></a>Risposta di errore
Gli errori dell'endpoint di rilascio dei token sono codici di errore HTTP perché il client chiama direttamente l'endpoint di rilascio dei token. Oltre al codice di stato HTTP, l'endpoint di rilascio dei token di Azure AD restituisce anche un documento JSON con gli oggetti che descrivono l'errore.

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
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error_codes |Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp |Ora in cui si è verificato l'errore. |
| trace_id |Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation_id |Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

#### <a name="http-status-codes"></a>Codici di stato HTTP
La tabella seguente elenca i codici di stato HTTP restituiti dall'endpoint di rilascio dei token. In alcuni casi il codice di errore è sufficiente a descrivere la risposta, ma in caso di errori sarà necessario analizzare il documento JSON associato ed esaminare il codice errore.

| Codice HTTP | Descrizione |
| --- | --- |
| 400 |Codice HTTP predefinito. Usato nella maggior parte dei casi, è in genere causato da una richiesta in formato non valido. Correggere e inviare di nuovo la richiesta. |
| 401 |Autenticazione non riuscita. Ad esempio, nella richiesta manca il parametro client_secret. |
| 403 |Autorizzazione non riuscita. Ad esempio, l'utente non ha l'autorizzazione per accedere alla risorsa. |
| 500 |Si è verificato un errore interno nel servizio. ripetere la richiesta. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Codici per gli errori degli endpoint di token
| Codice di errore | Descrizione | Azione client |
| --- | --- | --- |
| invalid_request |Errore del protocollo, ad esempio un parametro obbligatorio mancante. |Correggere e inviare di nuovo la richiesta. |
| invalid_grant |Il codice di autorizzazione non è valido o è scaduto. |Provare una nuova richiesta all'endpoint `/authorize` |
| unauthorized_client |Il client autenticato non è autorizzato a usare questo tipo di concessione dell'autorizzazione. |Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| invalid_client |Autenticazione client non riuscita. |Credenziali del client non valide. Per risolvere il problema, l'amministratore applicazione aggiorna le credenziali. |
| unsupported_grant_type |Il server di autorizzazione non supporta il tipo di concessione dell'autorizzazione. |Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato durante il test iniziale. |
| invalid_resource |La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. |Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| interaction_required |La richiesta richiede l'interazione dell'utente. Ad esempio, è necessario un passaggio di autenticazione aggiuntivo. | Invece di una richiesta non interattiva, provare di nuovo con una richiesta di autorizzazione interattiva per la stessa risorsa. |
| temporarily_unavailable |Il server è temporaneamente troppo occupato per gestire la richiesta. |ripetere la richiesta. L'applicazione client può comunicare all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |

## <a name="use-the-access-token-to-access-the-resource"></a>Usare il token di accesso per accedere alla risorsa
Dopo aver ottenuto un `access_token` è possibile usarlo in richieste alle API Web includendolo nell'intestazione `Authorization`. La specifica [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) illustra come usare i token di connessione nelle richieste HTTP per accedere a risorse protette.

### <a name="sample-request"></a>Richiesta di esempio
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Risposta di errore
Le risorse protette che implementano RFC 6750 rilasciano codici di stato HTTP. Se la richiesta non include le credenziali di autenticazione o è priva del token, la risposta include un'intestazione `WWW-Authenticate` . Quando una richiesta non riesce, il server delle risorse risponde con un codice di stato HTTP e un codice di errore.

L'esempio seguente illustra una risposta non riuscita quando la richiesta del client non include il token di connessione:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametri dell'errore
| Parametro | Descrizione |
| --- | --- |
| authorization_uri |URI (endpoint fisico) del server di autorizzazione. Questo valore viene usato anche come chiave di ricerca per ottenere altre informazioni sul server da un endpoint di individuazione. <p><p> Il client deve convalidare l'attendibilità del server di autorizzazione. Quando la risorsa è protetta da Azure AD, è sufficiente verificare che l'URL inizi con https://login.microsoftonline.com o con un altro nome host supportato da Azure AD. Una risorsa specifica del tenant deve sempre restituire un URI di autorizzazione specifico del tenant. |
| error |Valore del codice di errore definito nella sezione 5.2 del [framework di autorizzazione di OAuth 2.0](http://tools.ietf.org/html/rfc6749). |
| error_description |Descrizione più dettagliata dell'errore. Questo messaggio non vuole essere semplice da usare. |
| resource_id |Restituisce l'identificatore univoco della risorsa. L'applicazione client può usare questo identificatore come valore del parametro `resource` quando richiede un token per la risorsa. <p><p> È importante per l'applicazione client verificare questo valore, in caso contrario un servizio dannoso potrebbe riuscire ad attuare un attacco di tipo **elevazione dei privilegi** <p><p> La strategia consigliata per prevenire un attacco consiste nel verificare che `resource_id` corrisponda alla base dell'URL dell'API Web a cui si accede. Ad esempio, se si accede ad https://service.contoso.com/data, `resource_id` può essere htttps://service.contoso.com/. L'applicazione client deve rifiutare un `resource_id` che non inizia con l'URL di base a meno che non esista un modo alternativo affidabile per verificare l'ID. |

#### <a name="bearer-scheme-error-codes"></a>Codici errore dello schema di connessione
La specifica RFC 6750 definisce gli errori seguenti per le risorse che usano l'intestazione WWW-Authenticate e lo schema di connessione nella risposta.

| Codice di stato HTTP | Codice di errore | Descrizione | Azione client |
| --- | --- | --- | --- |
| 400 |invalid_request |Richiesta non ben formata. Ad esempio, potrebbe mancare un parametro o essere usato lo stesso parametro due volte. |Correggere l'errore e ripetere la richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato nel test iniziale. |
| 401 |invalid_token |Il token di accesso è mancante, non valido o è stato revocato. Il valore del parametro error_description fornisce dettagli aggiuntivi. |Richiedere un nuovo token al server di autorizzazione. Se il nuovo token ha esito negativo, si è verificato un errore imprevisto. Inviare un messaggio di errore all'utente e riprovare dopo alcuni ritardi casuali. |
| 403 |insufficient_scope |Il token di accesso non contiene le autorizzazioni di rappresentazione necessarie per accedere alla risorsa. |Inviare una nuova richiesta di autorizzazione all'endpoint di autorizzazione. Se la risposta contiene il parametro scope, usare il valore di scope nella richiesta alla risorsa. |
| 403 |insufficient_access |Il soggetto del token non ha le autorizzazioni necessarie per accedere alla risorsa. |Chiedere all'utente di usare un account diverso o di richiedere le autorizzazioni alla risorsa specificata. |

## <a name="refreshing-the-access-tokens"></a>Aggiornamento dei token di accesso
I token di accesso hanno breve durata ed è necessario aggiornarli dopo la scadenza per continuare ad accedere alle risorse. È possibile aggiornare `access_token` inviando un'altra richiesta `POST` all'endpoint `/token` e specificando `refresh_token` anziché il valore `code`.

I token di aggiornamento non hanno una durata specificata. La durata dei token di aggiornamento è in genere relativamente lunga. In alcuni casi, tuttavia, i token di aggiornamento scadono, vengono revocati o non hanno privilegi sufficienti per l'azione desiderata. L'applicazione deve prevedere e gestire correttamente gli errori restituiti dall'endpoint di rilascio del token.

Quando si riceve una risposta con un errore relativo al token di aggiornamento, rimuovere il token di aggiornamento corrente e richiedere un nuovo codice di autorizzazione o un nuovo token di accesso. In particolare, se si usa un token di aggiornamento nel flusso di concessione del codice di autorizzazione e si riceve una risposta con i codici di errore `interaction_required` o `invalid_grant`, rimuovere il token di aggiornamento e richiedere un nuovo codice di autorizzazione.

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

### <a name="successful-response"></a>Risposta con esito positivo
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
| Parametro | Descrizione |
| --- | --- |
| token_type |Tipo di token. L'unico valore supportato è **bearer**. |
| expires_in |Durata residua del token, in secondi. Un valore tipico è 3600 (un'ora). |
| expires_on |Data e ora in cui scadrà il token. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. |
| resource |Identifica la risorsa protetta a cui il token di accesso consente di accedere. |
| scope |Autorizzazioni di rappresentazione concesse all'applicazione client nativa. L'autorizzazione predefinita è **user_impersonation**. Il proprietario della risorsa di destinazione può registrare valori alternativi in Azure AD. |
| access_token |Nuovo token di accesso che è stato richiesto. |
| refresh_token |Nuovo refresh_token OAuth 2.0 che può essere usato per richiedere nuovi token di accesso alla scadenza del token di questa risposta. |

### <a name="error-response"></a>Risposta di errore
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

| Parametro | Descrizione |
| --- | --- |
| error |Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli. |
| error_description |Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore di autenticazione. |
| error_codes |Elenco dei codici di errore specifici del servizio token di sicurezza utile per la diagnostica. |
| timestamp |Ora in cui si è verificato l'errore. |
| trace_id |Identificatore univoco per la richiesta utile per la diagnostica. |
| correlation_id |Identificatore univoco per la richiesta utile per la diagnostica tra i componenti. |

Per una descrizione dei codici di errore e l'azione consigliata per il client, vedere [Codici per gli errori degli endpoint di token](#error-codes-for-token-endpoint-errors).
