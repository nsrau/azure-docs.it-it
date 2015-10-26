<properties
	pageTitle="Anteprima di AD B2C di Azure | Microsoft Azure"
	description="I tipi di token rilasciati nell'anteprima di Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Anteprima Azure AD B2C: Riferimento al Token

Azure AD B2C genera tipi diversi di token di sicurezza nell'elaborazione di ogni [flusso di autenticazione](active-directory-b2c-apps.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Tipi di token

Azure AD B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md), che usa sia token di accesso che token di aggiornamento. Esso supporta inoltre l'autenticazione e l'accesso tramite [OpenID Connect](active-directory-b2c-reference-protocols.md), che introduce un terzo tipo di token: il token ID. Ognuno di questi token viene rappresentato come "token di connessione".

Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Molti dei token generati da Azure AD B2C vengono implementati come token web JSON o JWT. Un token JWT è un modo compatto e indipendente dall'URL di trasferimento delle informazioni tra due parti. Le informazioni contenute nei token JWT sono note come "attestazioni" o asserzioni di informazioni sulla connessione e sull'oggetto del token. Le attestazioni nei token JWT sono oggetti JSON codificati e serializzati per la trasmissione. Poiché i token JWT emessi da Azure AD B2C sono firmati, ma non crittografati, è possibile esaminarne facilmente i contenuti a scopo di debug. Sono disponibili diversi strumenti per questa operazione, ad esempio [calebb.net](https://calebb.net). Per altre informazioni sui token JWT, fare riferimento alla [specifica di JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Token ID

I token ID sono un tipo di token di sicurezza che l'applicazione riceve dagli endpoint `authorize` e `token` di Azure AD B2C. Vengono rappresentati come [JWT](#types-of-tokens) e contengono attestazioni che è possibile usare per l'accesso dell'utente all'app. Quando acquisite dall’endpoint `authorize`, i token ID vengono spesso utilizzati per accedere all'utente in un'applicazione web. Quando acquisite dall’endpoint `token`, i token ID possono essere inviati in richieste HTTP durante la comunicazione tra due componenti della stessa applicazione o servizio. È possibile usare le attestazioni in un token ID in base alle proprie esigenze. In genere, vengono usate per la visualizzazione di informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'app.

Attualmente i token ID sono firmati, ma non crittografati. Quando l'app o l’API riceve un token ID, deve [convalidare la firma](#validating-tokens) per dimostrare l'autenticità del token e convalidare alcune attestazioni nel token per dimostrarne la validità. Le attestazioni convalidate da un'app variano a seconda dei requisiti dello scenario, ma esistono alcune [convalide di attestazione comuni](#validating-tokens) che l'app deve eseguire in ogni scenario.

Di seguito sono presentate informazioni dettagliate sulle attestazioni nei token ID nonché un token ID di esempio. Si noti che le attestazioni nei token ID non vengono restituite in un ordine particolare. Inoltre, nei token ID è possibile introdurre nuove attestazioni in qualsiasi momento. L'app non deve interrompersi quando vengono introdotte nuove attestazioni. L'elenco seguente include le attestazioni che l'app può interpretare in modo affidabile al momento della stesura di questo articolo. A scopo di esercitazione, provare a verificare le attestazioni nel token ID di esempio incollandole in [calebb.net](https://calebb.net). Se necessario, sono presenti informazioni più dettagliate nella [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Token ID di esempio
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### Attestazioni in token ID

Con Azure AD B2C, è necessario un accurato controllo granulare sul contenuto dei token. È possibile configurare [criteri](active-directory-b2c-reference-policies.md) per inviare un determinato set di dati utente in attestazioni richieste dall'applicazione per le relative operazioni. Tali attestazioni possono includere proprietà standard, ad esempio `displayName` e `emailAddress` dell’utente, nonché [attributi utente personalizzati](active-directory-b2c-reference-custom-attr) che è possibile definire nella directory B2C. Tuttavia, ogni token ID ricevuto conterrà un determinato set di attestazioni relative alla sicurezza che le applicazioni possono utilizzare per autenticare in modo sicuro gli utenti e le richieste. Di seguito sono riportate le attestazioni previste in ogni token ID emesso da Azure AD B2C - qualsiasi altra attestazione sarà determinata dal criterio.

| Nome | Attestazione | Valore di esempio | Descrizione |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifica il destinatario del token. Nei token ID il destinatario è l'ID applicazione assegnato all'app nel portale di registrazione delle app. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
| Issuer | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e la directory di Azure AD in cui l'utente è stato autenticato. L'app deve convalidare l'attestazione Issuer per assicurarsi che il token sia stato fornito dall'endpoint 2.0. |
| Ora di emissione | `iat` | `1438535543` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Expiration Time | `exp` | `1438539443` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1438535543` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. È in genere uguale all’ora di emissione. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Version | `ver` | `1.0` | Versione del token ID, come definito da Azure AD. |
| Code Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del codice è incluso nei token ID solo quando quest'ultimo viene rilasciato insieme a un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) per informazioni dettagliate su come eseguire la convalida. |
| Access Token Hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del token di accesso è incluso nei token ID solo quando quest'ultimo viene rilasciato insieme a un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) per informazioni dettagliate su come eseguire la convalida. |
| Nonce | `nonce` | `12345` | L'attestazione Nonce è una strategia per ridurre gli attacchi di riproduzione del token. L'app può specificare un'attestazione Nonce in una richiesta di autorizzazione usando il parametro di query `nonce`. Il valore specificato nella richiesta verrà generato nell'attestazione `nonce` del token ID senza modifica. In questo modo l'app può verificare il valore rispetto al valore specificato nella richiesta che associa la sessione dell'app a un determinato token ID. L'app deve eseguire la convalida durante il processo di convalida del token ID. |
| Oggetto | `sub` | `Not supported currently. Use oid claim.` | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, pertanto è possibile usarlo per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Tuttavia, l'attestazione dell'oggetto non è ancora implementata nell'anteprima AD B2C Azure. Anziché utilizzare l'attestazione dell’oggetto per l'autorizzazione, è necessario configurare i criteri per includere l'attestazione ID oggetto `oid` e utilizzare il relativo valore per identificare gli utenti. |
| Riferimento alla classe contesto di autenticazione | `acr` | `b2c_1_sign_in` | Il nome dei criterio utilizzato per acquisire il token ID. |
| Ora di autenticazione | `auth_time | `1438535543` | Ora in cui l'utente ha immesso per l’ultima volta le proprie credenziali, nel valore epoch time. |



## Token di accesso

Azure AD B2C non rilascia token di accesso in questo momento. L’autenticazione tra due entità diverse non è supportata in questa fase iniziale di anteprima, tuttavia, è possibile utilizzare i token ID per la comunicazione tra componenti della stessa applicazione. Per ulteriori informazioni sulle applicazioni e sugli scenari di autenticazione supportati per l'anteprima pubblica di Azure AD B2C, fare riferimento all’[articolo sulle App B2C](active-directory-b2c-apps.md).

## Token di aggiornamento

I token di aggiornamento sono token di sicurezza che l'app può usare per acquisire nuovi token ID e di accesso in un flusso di OAuth 2.0. Consentono all'app di ottenere l'accesso a lungo termine alle risorse per conto dell'utente senza l'interazione dell'utente.

Per ricevere un aggiornamento in una risposta del token, l'app deve richiedere l'ambito `offline_acesss`. Per ulteriori informazioni sull’ambito `offline_access`, fare riferimento al [riferimento al protocollo di Azure AD B2C](active-directory-b2c-reference-protocols.md).

I token di aggiornamento sono e saranno sempre, completamente opachi per l'app. Sono emessi da Azure AD e possono essere controllati e interpretati solo da Azure AD. Hanno una durata elevata, ma l'app non deve essere scritta aspettandosi che un token di aggiornamento duri per un periodo indefinito. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'app di sapere se un token di aggiornamento è valido è tentare di riscattarlo mediante una richiesta di token al Azure AD.

Quando si riscattano i token di aggiornamento per un nuovo token di accesso (e se l'app ha concesso l'autorizzazione per l'ambito `offline_access`), viene visualizzato un nuovo token di aggiornamento nella risposta del token. È consigliabile salvare il token di aggiornamento appena generato sostituendo quello usato precedentemente nella richiesta. In questo modo, il token di aggiornamento rimarrà valido più a lungo possibile.

## Convalida dei token

Attualmente, l'unica convalida dei token che le app devono eseguire è la convalida dei token ID. Per convalidare un token ID, l'app deve convalidarne la firma e le attestazioni incluse.

<!-- TODO: Link -->
Sono disponibili molte librerie open source per la convalida di JWT, a seconda del linguaggio preferito. È consigliabile prendere in esame tali opzioni anziché implementare una logica di convalida personalizzata. Le presenti informazioni risultano utili per scoprire come utilizzare adeguatamente queste librerie.

#### Convalida della firma
Un token JWT contiene tre segmenti separati dal carattere `.`. Il primo segmento è noto come **intestazione**, il secondo come **corpo** e il terzo come **firma**. Il segmento di firma può essere usato per convalidare l'autenticità del token ID in modo che possa essere considerato attendibile dall'app.

I token ID vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token ID contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token, mentre le attestazioni `kid` o `x5t` indicano la chiave pubblica specifica usata per firmare il token.

In qualsiasi momento, Azure AD può firmare un token ID usando un determinato set di coppie di chiavi pubbliche/private. Azure AD ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per cercare gli aggiornamenti per le chiavi pubbliche usate da Azure AD è di circa 24 ore.

Azure AD B2C dispone di un endpoint di metadati OpenID Connect, che consente a un'applicazione di recuperare informazioni su Azure AD B2C in fase di esecuzione. Queste informazioni includono endpoint, contenuti del token e chiavi per la firma dei token. Nella directory B2C, esiste un documento di metadati JSON per ogni criterio. Ad esempio, il documento dei metadati per il criterio in `b2c_1_sign_in` nel `fabrikamb2c.onmicrosoft.com` si trova in:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

È possibile acquisire i dati della chiave per la firma necessari per convalidare la firma usando il documento dei metadati OpenID Connect disponibile all'indirizzo:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

dove `fabrikamb2c.onmicrosoft.com` è la directory b2c utilizzata per autenticare l'utente e `b2c_1_sign_in` sono i criteri utilizzati per acquisire il token ID. Per determinare quale criterio è stato utilizzato per la firma di un id\_token (e la posizione dove recuperare i metadati), sono disponibili due opzioni. In primo luogo, il nome del criterio è incluso nell’attestazione `acr` nel token ID. È possibile analizzare le attestazioni all'esterno del corpo del JWT decodificando il corpo con decodifica base 64 e deserializzando la stringa JSON risultante. L’attestazione `acr` sarà il nome del criterio utilizzato per rilasciare il token ID. L'altra opzione consiste nel codificare i criteri nel valore del parametro `state` quando si rilascia la richiesta, per poi decodificarlo e determinare quali criteri sono stati utilizzati. Entrambi i metodi sono perfettamente validi.

Il documento dei metadati è un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione di OpenID Connect. Include inoltre un oggetto `jwks_uri` che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Tale posizione è indicata di seguito, ma è consigliabile recuperarla in modo dinamico usando il documento dei metadati e analizzando l'oggetto `jwks_uri`:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Il documento JSON in questo URL contiene tutte le informazioni sulla chiave pubblica in uso in un determinato momento. L'app può usare le attestazioni `kid` o `x5t` nell'intestazione JWT per selezionare la chiave pubblica usata in questo documento per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

L'esecuzione della convalida della firma non rientra nell'ambito di questo documento, sono tuttavia disponibili numerose librerie open source che contengono informazioni per eseguire questa operazione.

#### Convalida delle attestazioni
Quando l'app o l’API riceve un token ID, deve eseguire anche alcuni controlli a fronte delle attestazioni nel token ID. Sono state illustrate le seguenti operazioni:

- Attestazione **Audience**: per verificare che il token ID era destinato all'app.
- Attestazioni **Not Before** ed **Expiration Time**: per verificare che il token ID non sia scaduto.
- Attestazione **Issuer**: per verificare che il token sia stato effettivamente rilasciato all'app da Azure AD.
- Attestazione **Nonce**: come prevenzione di attacchi di riproduzione del token.

Informazioni dettagliate sui valori previsti per tali attestazioni, sono incluse nella precedente [sezione Token ID](#id_tokens).

## Durata del token

Di seguito vengono fornite informazioni sulla durata dei token utili per gli utenti che sviluppano ed eseguono il debug di app. Le app non devono essere scritte aspettandosi che queste durate rimangano costanti, queste infatti possono e devono cambiare in qualsiasi momento.

| Token | Durata | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| Token ID | 1 ora | I token ID sono in genere validi per un'ora. L'app Web può usare questa durata per la gestione di una sessione con l'utente (scelta consigliata) o scegliere una durata di sessione completamente diversa. Se l'app deve ottenere un nuovo token ID, deve semplicemente inviare una nuova richiesta di accesso ad Azure AD. Se l'utente dispone di una sessione del browser valida con Azure AD, può non essere necessario immettere nuovamente le credenziali. |
| Token di aggiornamento | Fino a 14 giorni | Un singolo token di aggiornamento è valido per un periodo massimo di 14 giorni. Tuttavia, il token di aggiornamento può scadere in qualsiasi momento per diversi motivi, quindi l'app deve continuare a provare e usare un token di aggiornamento fino a quando non si verifica un errore o fino a quando non lo sostituisce con uno nuovo. Inoltre, un token di aggiornamento non risulterà più valido se sono trascorsi 90 giorni dall'immissione delle credenziali da parte dell'utente. |
| Codici di autorizzazione | 5 minuti | I codici di autorizzazione sono intenzionalmente di breve durata e devono essere immediatamente riscattati per i token di accesso, ID e di aggiornamento quando vengono ricevuti. |

<!---HONumber=Oct15_HO3-->