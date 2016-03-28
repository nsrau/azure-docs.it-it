<properties
	pageTitle="Riferimento al token della versione 2.0 di Azure AD | Microsoft Azure"
	description="Tipi di token e attestazioni generati dall'endpoint v2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Riferimento al token della versione 2.0

L'endpoint 2.0 genera tipi diversi di token di sicurezza nell'elaborazione di ogni [flusso di autenticazione](active-directory-v2-flows.md). Questo documento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

> [AZURE.NOTE]
	Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).

## Tipi di token

L'endpoint 2.0 supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-v2-protocols.md), che usa sia token di accesso che token di aggiornamento. L'endpoint supporta inoltre l'autenticazione e l'accesso tramite [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow), che introduce un terzo tipo di token: il token ID. Ognuno di questi token viene rappresentato come "token di connessione".

Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Molti dei token generati dall'endpoint 2.0 vengono implementati come token JWT (token Web JSON). Un token JWT è un modo compatto e indipendente dall'URL di trasferimento delle informazioni tra due parti. Le informazioni contenute nei token JWT sono note come "attestazioni" o asserzioni di informazioni sulla connessione e sull'oggetto del token. Le attestazioni nei token JWT sono oggetti JSON codificati e serializzati per la trasmissione. Poiché i token JWT emessi dall'endpoint 2.0 sono firmati, ma non crittografati, è possibile esaminarne facilmente i contenuti a scopo di debug. Sono disponibili diversi strumenti per questa operazione, ad esempio [calebb.net](http://jwt.calebb.net). Per altre informazioni sui token JWT, fare riferimento alla [specifica di JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## Token ID

I token ID sono un tipo di token di sicurezza di accesso che l'app riceve quando esegue l'autenticazione usando [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Vengono rappresentati come [JWT](#types-of-tokens) e contengono attestazioni che è possibile usare per l'accesso dell'utente all'app. È possibile usare le attestazioni in un token ID in base alle proprie esigenze. In genere, vengono usate per la visualizzazione di informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'app. L'endpoint 2.0 genera solo un tipo di token ID che dispone di un set coerente di attestazioni indipendentemente dal tipo di utente che ha eseguito l'accesso. Ossia il formato e il contenuto dei token ID è identico per gli utenti con account Microsoft personali e per quelli con account aziendali o dell'istituto di istruzione.

Attualmente i token ID sono firmati, ma non crittografati. Quando l'app riceve un token ID, deve [convalidare la firma](#validating-tokens) per dimostrare l'autenticità del token e convalidare alcune attestazioni nel token per dimostrarne la validità. Le attestazioni convalidate da un'app variano a seconda dei requisiti dello scenario, ma esistono alcune [convalide di attestazione comuni](#validating-tokens) che l'app deve eseguire in ogni scenario.

Di seguito sono presentate informazioni dettagliate sulle attestazioni nei token ID nonché un token ID di esempio. Si noti che le attestazioni nei token ID non vengono restituite in un ordine particolare. Inoltre, nei token ID è possibile introdurre nuove attestazioni in qualsiasi momento. L'app non deve interrompersi quando vengono introdotte nuove attestazioni. L'elenco seguente include le attestazioni che l'app può interpretare in modo affidabile al momento della stesura di questo articolo. Se necessario, sono presenti informazioni più dettagliate nella [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### Token ID di esempio

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] A scopo di esercitazione, provare a verificare le attestazioni nel token ID di esempio incollandole in [calebb.net](https://calebb.net).

#### Attestazioni nei token ID
| Nome | Attestazione | Valore di esempio | Descrizione |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Audience | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identifica il destinatario del token. Nei token ID il destinatario è l'ID applicazione assegnato all'app nel portale di registrazione delle app. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
| Issuer | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. L'app deve convalidare l'attestazione Issuer per assicurarsi che il token sia stato fornito dall'endpoint 2.0. Può inoltre usare la parte GUID dell'attestazione per limitare il set di tenant che può accedere all'app. Il GUID che indica l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Ora di emissione | `iat` | `1452285331` | Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| Expiration Time | `exp` | `1452289231` | Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Non prima | `nbf` | `1452285331` | L’ora in cui il token diventa valido, rappresentata dal valore epoch time. È in genere uguale all’ora di emissione. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| Version | `ver` | `2.0` | Versione del token ID, come definito da Azure AD. Per l'endpoint v2.0, il valore sarà `2.0`. |
| ID tenant | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Valore GUID che rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID sarà l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali, il valore sarà `9188040d-6c67-4c5b-b112-36a304b66dad`. Per ricevere questa attestazione è necessario l'ambito `profile`. |
| Code Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del codice è incluso nei token ID solo quando quest'ultimo viene rilasciato insieme a un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Access Token Hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash del token di accesso è incluso nei token ID solo quando quest'ultimo viene rilasciato insieme a un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| Nonce | `nonce` | `12345` | L'attestazione Nonce è una strategia per ridurre gli attacchi di riproduzione del token. L'app può specificare un'attestazione Nonce in una richiesta di autorizzazione usando il parametro di query `nonce`. Il valore specificato nella richiesta verrà generato nell'attestazione `nonce` del token ID senza modifica. In questo modo l'app può verificare il valore rispetto al valore specificato nella richiesta che associa la sessione dell'app a un determinato token ID. L'app deve eseguire la convalida durante il processo di convalida del token ID. |
| Name | `name` | `Babe Ruth` | L'attestazione Name fornisce un valore leggibile che identifica l'oggetto del token. Questo valore potrebbe non essere univoco, è mutevole e può essere usato solo per scopi di visualizzazione. Per ricevere questa attestazione è necessario l'ambito `profile`. |
| Email | `email` | `thegreatbambino@nyy.onmicrosoft.com` | Indirizzo di posta elettronica primario associato all'account utente, se presente. Il valore è modificabile e può cambiare nel tempo per un determinato utente. Per ricevere questa attestazione è necessario l'ambito `email`. |
| Preferred Username | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | Nome utente primario usato per rappresentare l'utente nell'endpoint 2.0. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può cambiare nel tempo per un determinato utente. Per ricevere questa attestazione è necessario l'ambito `profile`. |
| Oggetto | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato, pertanto è possibile usarlo per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa. Poiché il soggetto è sempre presente nei token emessi da Azure AD, è consigliabile l'uso di questo valore in un sistema di autorizzazione generale. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | ID oggetto dell'account aziendale o dell'istituto di istruzione nel sistema di Azure AD. Questa attestazione non verrà generata per account personali Microsoft. Per ricevere questa attestazione è necessario l'ambito `profile`. |


## Token di accesso

I token di accesso rilasciati dall'endpoint della versione 2.0 attualmente possono essere utilizzati solo dai servizi Microsoft. Le app non devono eseguire la convalida o l'ispezione dei token di accesso per gli scenari attualmente supportati. È possibile considerare i token di accesso completamente opachi. Si tratta di semplici stringhe che l'app può passare a Microsoft nelle richieste HTTP.

In una fase successiva, l'endpoint 2.0 introdurrà la possibilità per l'app di ricevere i token di accesso da altri client. A quel punto, queste informazioni verranno aggiornate con le informazioni necessarie all'app per eseguire la convalida del token di accesso e altre operazioni simili.

Quando si richiede un token di accesso dall'endpoint 2.0, quest'ultimo restituisce anche alcuni metadati relativi ai token di accesso per l'utilizzo dell'app. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per i quali è valido. In questo modo l'app può eseguire operazioni di memorizzazione intelligente dei token di accesso senza la necessità di analizzare il token di accesso stesso.

## Token di aggiornamento

I token di aggiornamento sono token di sicurezza che l'app può usare per acquisire nuovi token di accesso in un flusso di OAuth 2.0. Consentono all'app di ottenere l'accesso a lungo termine alle risorse per conto dell'utente senza l'interazione dell'utente.

Si tratta di token a più risorse, ossia un token di aggiornamento ricevuto durante una richiesta di token per una risorsa può essere riscattato per i token di accesso che fanno riferimento a una risorsa completamente diversa.

Per ricevere un aggiornamento in una risposta del token, l'app deve richiedere e concedere l'autorizzazione per l'ambito `offline_acesss`. Per altre informazioni sull'ambito `offline_access`, fare riferimento all'[articolo su consenso e ambiti](active-directory-v2-scopes.md).

I token di aggiornamento sono e saranno sempre, completamente opachi per l'app. Sono emessi dall'endpoint 2.0 di Azure AD e possono essere controllati e interpretati solo dall'endpoint 2.0. Hanno una durata elevata, ma l'app non deve essere scritta aspettandosi che un token di aggiornamento duri per un periodo indefinito. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'app di sapere se un token di aggiornamento è valido è tentare di riscattarlo mediante una richiesta di token all'endpoint 2.0.

Quando si riscattano i token di aggiornamento per un nuovo token di accesso (e se l'app ha concesso l'autorizzazione per l'ambito `offline_access`), viene visualizzato un nuovo token di aggiornamento nella risposta del token. È consigliabile salvare il token di aggiornamento appena generato sostituendo quello usato nella richiesta. In questo modo, il token di aggiornamento rimarrà valido più a lungo possibile.

## Convalida dei token

Attualmente, l'unica convalida dei token che le app devono eseguire è la convalida dei token ID. Per convalidare un token ID, l'app deve convalidarne la firma e le attestazioni incluse.

<!-- TODO: Link -->
Sono disponibili librerie ed esempi di codice che illustrano come gestire facilmente la convalida dei token. Le informazioni seguenti vengono fornite a titolo esemplificativo per chi desidera comprenderne il processo sottostante. Sono inoltre disponibili numerose librerie open source di terze parti per la convalida dei token JWT: esiste almeno un'opzione per ogni piattaforma e linguaggio utilizzato.

#### Convalida della firma
Un token JWT contiene tre segmenti separati dal carattere `.`. Il primo segmento è noto come **intestazione**, il secondo come **corpo** e il terzo come **firma**. Il segmento di firma può essere usato per convalidare l'autenticità del token ID in modo che possa essere considerato attendibile dall'app.

I token ID vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token ID contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token, mentre l'attestazione `kid` indica la chiave pubblica specifica usata per firmare il token.

In qualsiasi momento, l'endpoint 2.0 può firmare un token ID usando un set di coppie di chiavi pubbliche/private. L'endpoint 2.0 ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per cercare gli aggiornamenti per le chiavi pubbliche usate dall'endpoint 2.0 è di circa 24 ore.

È possibile acquisire i dati della chiave per la firma necessari per convalidare la firma usando il documento dei metadati OpenID Connect disponibile all'indirizzo:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Provare questo URL in un browser.

Il documento dei metadati è un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione di OpenID Connect.

Include inoltre un oggetto `jwks_uri` che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Il documento JSON in `jwks_uri` contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione JWT per selezionare quale chiave pubblica in questo documento è stata usata per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

L'esecuzione della convalida della firma non rientra nell'ambito di questo documento, sono tuttavia disponibili numerose librerie open source che contengono informazioni per eseguire questa operazione.

#### Convalida delle attestazioni
Quando l'app riceve un token ID all'accesso dell'utente, deve eseguire anche alcuni controlli a fronte delle attestazioni nel token ID. Sono incluse, ad esempio:

- Attestazione **Audience**: per verificare che il token ID era destinato all'app.
- Attestazioni **Not Before** ed **Expiration Time**: per verificare che il token ID non sia scaduto.
- Attestazione **Issuer**: per verificare che il token sia stato effettivamente rilasciato all'app dall'endpoint 2.0.
- Attestazione **Nonce**: come prevenzione di attacchi di riproduzione del token.
- E altro ancora...

Per un elenco completo delle convalide di attestazione che l'app deve eseguire, vedere la pagina relativa alle [specifiche di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Informazioni dettagliate sui valori previsti per tali attestazioni, sono incluse nella precedente sezione [Token ID](#id_tokens).


## Durata dei token

Di seguito vengono fornite informazioni sulla durata dei token utili per gli utenti che sviluppano ed eseguono il debug di app. Le app non devono essere scritte aspettandosi che queste durate rimangano costanti, queste infatti possono e devono cambiare in qualsiasi momento.

| Token | Durata | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| Token ID (account aziendali o dell'istituto di istruzione) | 1 ora | I token ID sono in genere validi per un'ora. L'app Web può usare questa durata per la gestione di una sessione con l'utente (scelta consigliata) o scegliere una durata di sessione completamente diversa. Se l'app deve ottenere un nuovo token ID, deve semplicemente eseguire una nuova richiesta di accesso all'endpoint di autorizzazione 2.0. Se l'utente dispone di una sessione del browser valida con l'endpoint 2.0, può non essere necessario immettere nuovamente le credenziali. |
| Token ID (account personali) | 24 ore | I token ID per gli account personali sono in genere validi per 24 ore. L'app Web può usare questa durata per la gestione di una sessione con l'utente (scelta consigliata) o scegliere una durata di sessione completamente diversa. Se l'app deve ottenere un nuovo token ID, deve semplicemente eseguire una nuova richiesta di accesso all'endpoint di autorizzazione 2.0. Se l'utente dispone di una sessione del browser valida con l'endpoint 2.0, può non essere necessario immettere nuovamente le credenziali. |
| Token di accesso (account aziendali o dell'istituto di istruzione) | 1 ora | Indicati nelle risposte del token come parte dei metadati del token. |
| Token di accesso (account personali) | 1 ora | Indicati nelle risposte del token come parte dei metadati del token. I token di accesso rilasciati per conto di account personali possono essere configurati per una durata diversa, ma un'ora è il caso più comune. |
| Token di aggiornamento (account aziendali o dell'istituto di istruzione) | Fino a 14 giorni | Un singolo token di aggiornamento è valido per un periodo massimo di 14 giorni. Tuttavia, il token di aggiornamento può scadere in qualsiasi momento per diversi motivi, quindi l'app deve continuare a provare e usare un token di aggiornamento fino a quando non si verifica un errore o fino a quando non lo sostituisce con uno nuovo. Inoltre, un token di aggiornamento non risulterà più valido se sono trascorsi 90 giorni dall'immissione delle credenziali da parte dell'utente. |
| Token di aggiornamento (account personali) | Fino a 1 anno | Un singolo token di aggiornamento è valido per un periodo massimo di 1 anno. Tuttavia, il token di aggiornamento può essere invalidato in qualsiasi momento per diversi motivi, quindi l'app deve continuare a provare e usare un token di aggiornamento fino a quando non si verifica un errore. |
| Codici di autorizzazione (account aziendali o dell'istituto di istruzione) | 10 minuti | I codici di autorizzazione sono intenzionalmente di breve durata e devono essere immediatamente riscattati per i token di accesso e di aggiornamento quando vengono ricevuti. |
| Codici di autorizzazione (account personali) | 5 minuti | I codici di autorizzazione sono intenzionalmente di breve durata e devono essere immediatamente riscattati per i token di accesso e di aggiornamento quando vengono ricevuti. I codici di autorizzazione emessi per conto degli account personali sono anche adatti a un uso occasionale. |

<!---HONumber=AcomDC_0316_2016-->