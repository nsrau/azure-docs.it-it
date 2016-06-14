<properties
	pageTitle="Gestione degli errori in OAuth 2.0 | Microsoft Azure"
	description="Questo articolo descrive le comuni classi di errori in OAuth 2.0 con Azure Active Directory e le procedure consigliate per gestirli."
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

# Gestione degli errori in OAuth 2.0

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Questo articolo illustra alcune procedure consigliate per gestire le comuni classi di errori che si possono verificare quando si autorizza l'applicazione con Azure Active Directory (Azure AD). Per altre informazioni sull'endpoint di autorizzazione e sull'endpoint di rilascio dei token, vedere [Flusso di autenticazione per un'applicazione in Azure AD](active-directory-protocols-oauth-code.md).

## Errori dell'endpoint di autorizzazione

Nel primo passaggio del processo di autorizzazione l'applicazione client invia una richiesta all'endpoint `/authorize` di Azure AD, con l'elenco di autorizzazioni necessarie da parte dell'utente.

Gli errori dell'endpoint di autorizzazione hanno origine nell'endpoint `/authorize`. Vengono restituiti come errori HTTP 200 in una pagina Web o usando un codice di reindirizzamento HTTP 302 quando è disponibile un'applicazione client per gestire l'errore.

Ecco una risposta di errore HTTP 302 di esempio dall'endpoint di autorizzazione di Azure AD, quando in una richiesta manca il parametro `response_type` obbligatorio.

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?error=invalid_request&error_description=AADSTS90014%3a+The+request+body+must+contain+the+following+parameter%3a+%27response_type%27.%0d%0aTrace+ID%3a+57f5cb47-2278-4802-a018-d05d9145daad%0d%0aCorrelation+ID%3a+570a9ed3-bf1d-40d1-81ae-63465cc25488%0d%0aTimestamp%3a+2013-12-31+05%3a51%3a35Z&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametro | Descrizione |
|-----------|-------------|
| error | Valore del codice errore definito nella sezione 5.2 del [framework di autorizzazione di OAuth 2.0](http://tools.ietf.org/html/rfc6749). La tabella successiva descrive i codici errore restituiti da Azure AD. |
| error\_description | Descrizione più dettagliata dell'errore. Questo messaggio non vuole essere semplice da usare. |
| state | Il valore di state è un valore non riutilizzato generato casualmente che viene inviato nella richiesta e restituito nella risposta per impedire attacchi di tipo richiesta intersito falsa. |

### Codici per gli errori dell'endpoint di autorizzazione

La tabella seguente descrive i diversi codici errore che possono essere restituiti nel parametro `error` della risposta di errore.

| Codice di errore | Descrizione | Azione client |
|------------|-------------|---------------|
| invalid\_request | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale.|
| unauthorized\_client | All'applicazione client non è consentito richiedere un codice di autorizzazione. | Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| access\_denied | Consenso negato dal proprietario della risorsa | L'applicazione client può notificare all'utente che non può proseguire a meno che l'utente non acconsenta. |
| unsupported\_response\_type | Il server di autorizzazione non supporta il tipo di risposta nella richiesta. | Correggere e inviare di nuovo la richiesta. Si tratta di un errore di sviluppo rilevato in genere durante il test iniziale.|
|server\_error | Errore imprevisto rilevato dal server. | ripetere la richiesta. Questi errori possono dipendere da condizioni temporanee. L'applicazione client sta comunicando all'utente che la risposta è stata ritardata a causa di un errore temporaneo. |
| temporarily\_unavailable | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client sta comunicando all'utente che la risposta è stata ritardata a causa di una condizione temporanea. |
| invalid\_resource |La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata.| Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |

## Errori dell'endpoint di rilascio dei token

Una volta che l'applicazione riceve un codice di autorizzazione dall'endpoint `/authorize` lo passa all'endpoint `/token` per ricevere un token di accesso per la risorsa specificata.

Gli errori dell'endpoint di rilascio dei token hanno origine nell'endpoint `/token`. Sono codici errore HTTP perché il client chiama direttamente l'endpoint di rilascio dei token. Oltre al codice di stato HTTP, l'endpoint di rilascio dei token di Azure AD restituisce anche un documento JSON con gli oggetti che descrivono l'errore.

Ad esempio, se il parametro `client_id` nella richiesta non è valido, l'errore sarà simile al seguente:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{"error":"invalid_request","error_description":"AADSTS90011: Request is ambiguous, multiple application identifiers found. Application identifiers: '197451ec-ade4-40e4-b403-02105abd9049, 597451ec-ade4-40e4-b403-02105abd9049'.\r\nTrace ID: 4457d068-2a03-42b2-97f2-d55325289d86\r\nCorrelation ID: 6b3474d8-233e-463f-b0a3-86433d8ba889\r\nTimestamp: 2013-12-31 06:31:41Z","error_codes":[90011],"timestamp":"2013-12-31 06:31:41Z","trace_id":"4457d068-2a03-42b2-97f2-d55325289d86","correlation_id":"6b3474d8-233e-463f-b0a3-86433d8ba889"}
```
### Codici di stato HTTP

La tabella seguente elenca i codici di stato HTTP restituiti dall'endpoint di rilascio dei token. In alcuni casi, il codice errore è sufficiente a descrivere la risposta, ma in caso di errori, sarà necessario analizzare il documento JSON associato ed esaminare il codice errore.

| Codice HTTP | Descrizione |
|-----------|-------------|
| 400 | Codice HTTP predefinito. Usato nella maggior parte dei casi, è in genere causato da una richiesta in formato non valido. Correggere e inviare di nuovo la richiesta. |
| 401 | Autenticazione non riuscita. Ad esempio, nella richiesta manca il parametro client\_secret.|
| 403 | Autorizzazione non riuscita. Ad esempio, l'utente non ha l'autorizzazione per accedere alla risorsa. |
| 500 | Si è verificato un errore interno nel servizio. ripetere la richiesta. |

### Oggetti del documento JSON nella risposta di errore

| Oggetto JSON | Descrizione |
|-------------|-------------|
| error | Valore del codice errore definito nella sezione 5.2 del [framework di autorizzazione di OAuth 2.0](http://tools.ietf.org/html/rfc6749). La tabella successiva descrive i codici errore restituiti da Azure AD. |
| error\_description | Descrizione più dettagliata dell'errore. Questo messaggio non vuole essere semplice da usare. |
| timestamp | Data e ora in cui si è verificato l'errore nell'ora UTC (Universal Coordinated Time). |
| trace\_id | ID che identifica la traccia dell'errore. |
| correlation\_id | 	Valore generato dal client. Questo ID è incluso nel documento degli errori JSON quando la richiesta all'endpoint di rilascio dei token include questo valore nell'intestazione `client-request-id`. Questo ID può essere usato da altre chiamate nella stessa sessione. |
| error\_codes | Contiene un elenco di codici errore che eseguono il mapping a condizioni diverse del servizio. Non usare questi codici nella logica dell'applicazione. È tuttavia possibile usarli per diagnosticare un problema e cercare informazioni online in base al codice errore. |

### Valori dei codici errore del documento JSON

| Codice di errore | Descrizione | Azione client |
|------------|-------------|---------------|
| invalid\_request | Errore del protocollo, ad esempio un parametro obbligatorio mancante. | Correggere e inviare di nuovo la richiesta. |
| invalid\_grant | La concessione dell'autorizzazione (o i relativi parametri) o il token di aggiornamento non è valido, è scaduto o è stato revocato. | Correggere e inviare di nuovo la richiesta. |
| unauthorized\_client | Il client autenticato non è autorizzato a usare questo tipo di concessione dell'autorizzazione. | Si verifica in genere quando l'applicazione client non è registrata in Azure AD o non è stata aggiunta al tenant di Azure AD dell'utente. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| invalid\_client | Autenticazione client non riuscita. | Credenziali del client non valide. Per risolvere il problema, l'amministratore applicazione aggiorna le credenziali. |
| unsupported\_grant\_type | Il server di autorizzazione non supporta il tipo di concessione dell'autorizzazione. | Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato durante il test iniziale. |
| invalid\_resource | La risorsa di destinazione non è valida perché non esiste, Azure AD non riesce a trovarla o non è attualmente configurata. | Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione può chiedere all'utente di installare l'applicazione e di aggiungerla ad Azure AD. |
| interaction\_required | La richiesta richiede l'interazione dell'utente. Ad esempio, è necessario un passaggio di autenticazione aggiuntivo. | Ripetere la richiesta con la stessa risorsa. |
| temporarily\_unavailable | Il server è temporaneamente troppo occupato per gestire la richiesta. | ripetere la richiesta. L'applicazione client sta comunicando all'utente che la risposta è stata ritardata a causa di una condizione temporanea.|

## Errori da risorse protette

Si tratta degli errori che una risorsa protetta, ad esempio un'API Web, può restituire se implementa la specifica [RFC 6750](http://tools.ietf.org/html/rfc6750) del framework di autorizzazione di OAuth 2.0.

Le risorse protette che implementano RFC 6750 rilasciano codici di stato HTTP. Se la richiesta non include le credenziali di autenticazione o è priva del token, la risposta include un'intestazione `WWW-Authenticate`. Quando una richiesta non riesce, il server delle risorse risponde con un codice di stato HTTP e un codice errore.

L'esempio seguente illustra una risposta non riuscita quando la richiesta del client non include il token di connessione:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

## Parametri dell'errore

| Parametro | Descrizione |
|-----------|-------------|
| authorization\_uri | URI (endpoint fisico) del server di autorizzazione. Questo valore viene usato anche come chiave di ricerca per ottenere altre informazioni sul server da un endpoint di individuazione. <p><p> Il client deve convalidare l'attendibilità del server di autorizzazione. Quando la risorsa è protetta da Azure AD, è sufficiente verificare che l'URL inizi con https://login.windows.net o un altro nome host supportato da Azure AD. Una risorsa specifica del tenant deve sempre restituire un URI di autorizzazione specifico del tenant. |
| error | Valore del codice errore definito nella sezione 5.2 del [framework di autorizzazione di OAuth 2.0](http://tools.ietf.org/html/rfc6749).|
| error\_description | Descrizione più dettagliata dell'errore. Questo messaggio non vuole essere semplice da usare.|
| resource\_id | Restituisce l'identificatore univoco della risorsa. L'applicazione client può usare questo identificatore come valore del parametro `resource` quando richiede un token per la risorsa. <p><p> È molto importante per l'applicazione client verificare questo valore. In caso contrario, un servizio dannoso potrebbe riuscire a indurre un attacco di tipo **elevazione dei privilegi** <p><p> La strategia consigliata per evitare un attacco consiste nel verificare che `resource_id` corrisponda alla base dell'URL dell'API Web a cui è in corso l'accesso. Se si accede a https://service.contoso.com/data, ad esempio, `resource_id` può essere htttps://service.contoso.com/. L'applicazione client deve rifiutare un `resource_id` che non inizia con l'URL di base a meno che non esista un modo alternativo affidabile per verificare l'ID. |

## Codici errore dello schema di connessione

La specifica RFC 6750 definisce gli errori seguenti per le risorse che usano l'intestazione WWW-Authenticate e lo schema di connessione nella risposta.

| Codice di stato HTTP | Codice di errore | Descrizione | Azione client |
|------------------|------------|-------------|---------------|
| 400 | invalid\_request | Richiesta non ben formata. Ad esempio, potrebbe mancare un parametro o essere usato lo stesso parametro due volte. | Correggere l'errore e ripetere la richiesta. Questo tipo di errore dovrebbe verificarsi solo durante lo sviluppo ed essere rilevato nel test iniziale. |
| 401 | invalid\_token | Il token di accesso è mancante, non valido o è stato revocato. Il valore del parametro error\_description fornisce dettagli aggiuntivi. | Richiedere un nuovo token al server di autorizzazione. Se il nuovo token ha esito negativo, si è verificato un errore imprevisto. Inviare un messaggio di errore all'utente e riprovare dopo alcuni ritardi casuali. |
| 403 | insufficient\_scope | Il token di accesso non contiene le autorizzazioni di rappresentazione necessarie per accedere alla risorsa. | Inviare una nuova richiesta di autorizzazione all'endpoint di autorizzazione. Se la risposta contiene il parametro scope, usare il valore di scope nella richiesta alla risorsa. |
| 403 | insufficient\_access | Il soggetto del token non ha le autorizzazioni necessarie per accedere alla risorsa. | Chiedere all'utente di usare un account diverso o di richiedere le autorizzazioni alla risorsa specificata. |

<!---HONumber=AcomDC_0601_2016-->