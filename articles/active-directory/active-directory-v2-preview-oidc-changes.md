<properties
	pageTitle="Modifiche all'endpoint v2.0 di Azure AD | Microsoft Azure"
	description="Descrizione delle modifiche in corso ai protocolli del Modello app 2.0 disponibili in anteprima pubblica."
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
	ms.date="09/16/2016"
	ms.author="dastrock"/>  

# Aggiornamenti importanti ai protocolli di autenticazione della versione 2.0
Nota per gli sviluppatori: nelle prossime due settimane verranno rilasciati alcuni aggiornamenti ai protocolli di autenticazione della versione 2.0 che potrebbero comportare modifiche di rilievo per le app scritte durante il periodo di anteprima.

## Parti interessate
Le app scritte per l'uso dell'endpoint di autenticazione convergente della versione 2.0,

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Altre informazioni sull'endpoint della versione 2.0 sono disponibili [qui](active-directory-appmodel-v2-overview.md).

Se è stata compilata un'app con l'endpoint della versione 2.0 creando il codice direttamente nel protocollo della versione 2.0 e usando il middleware Web OAuth o OpenID Connect oppure usando altre librerie di terze parti per eseguire l'autenticazione, è necessario testare i progetti e apportare le eventuali modifiche necessarie.

## Parti non interessate
Le app scritte in base all'endpoint di autenticazione di Azure AD di produzione,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Questo protocollo è fisso e non subirà modifiche.

Se l'app usa **solo** la libreria ADAL per eseguire l'autenticazione, non è necessario apportare alcuna modifica. ADAL protegge l'app dalle modifiche.

## Dettaglio delle modifiche
### Rimozione del valore x5t dalle intestazioni JWT
L'endpoint della versione 2.0 fa ampio uso dei token JWT, che contengono una sezione dei parametri di intestazione con i relativi metadati sul token. Decodificando l'intestazione di uno dei JWT correnti, si otterrebbe un risultato simile al seguente:

```
{ 
	"type": "JWT",
	"alg": "RS256",
	"x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
	"kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Dove entrambe le proprietà "x5t" e "kid" identificano la chiave pubblica da usare per convalidare la firma del token, recuperata dall'endpoint dei metadati di OpenID Connect.

La modifica consiste nella rimozione della proprietà "x5t". È possibile continuare a usare gli stessi meccanismi per la convalida dei token, ma per il recupero della chiave pubblica corretta è possibile basarsi unicamente sulla proprietà "kid", come specificato nel protocollo OpenID Connect.

> [AZURE.IMPORTANT] **Assicurarsi che l'app non dipenda dall'esistenza del valore x5t.**

### Rimozione di profile\_info
In precedenza, l'endpoint della versione 2.0 restituiva un oggetto JSON con codifica Base64 denominato `profile_info` nelle risposte dei token. Quando si richiedeva un token di accesso dall'endpoint della versione 2.0 inviando una richiesta a:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

La risposta era simile all'oggetto JSON seguente:
```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Il valore `profile_info` conteneva informazioni sull'utente che aveva eseguito l'accesso all'app, ad esempio il nome visualizzato, il nome, il cognome, l'indirizzo di posta elettronica, l'identificatore e così via. `profile_info` veniva usato principalmente per la memorizzazione nella cache dei token e ai fini della visualizzazione.

Il valore `profile_info` è stato rimosso e le informazioni vengono ora fornite agli sviluppatori in una posizione leggermente diversa. Invece di `profile_info`, l'endpoint della versione 2.0 restituisce ora un `id_token` in ogni risposta del token:

```
{ 
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https://outlook.office.com/mail.read",
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

È possibile decodificare e analizzare l'id\_token per recuperare le stesse informazioni fornite da profile\_info. L'id\_token è un token Web JSON (JWT) il cui contenuto è specificato da OpenID Connect. Il codice per eseguire questa operazione dovrebbe essere molto simile. È sufficiente estrarre il segmento intermedio (il corpo) dell'id\_token e decodificarlo con Base64 per accedere all'oggetto JSON al suo interno.

Nelle prossime due settimane è consigliabile scrivere il codice dell'app in modo che le informazioni sull'utente vengano recuperate da `id_token` o `profile_info`, a seconda del valore presente. In questo modo, quando verrà apportata la modifica l'app potrà gestire senza problemi la transizione da `profile_info` a `id_token`, senza interruzioni.

> [AZURE.IMPORTANT] **Assicurarsi che l'app non dipenda dall'esistenza del valore `profile_info`.**

### Rimozione di id\_token\_expires\_in
Oltre a `profile_info` verrà rimosso anche il parametro `id_token_expires_in` dalle risposte. In precedenza, l'endpoint della versione 2.0 restituiva un valore per `id_token_expires_in` insieme a ogni risposta dell'id\_token, ad esempio in una risposta di autorizzazione:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

O in una risposta del token:

```
{ 
	"token_type": "Bearer",
	"id_token_expires_in": 3599,
	"scope": "openid",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
	"refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
	"profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Il valore `id_token_expires_in` indicava il numero di secondi di validità dell'id\_token. Il valore `id_token_expires_in` viene ora completamente rimosso. Al suo posto è possibile usare le attestazioni `nbf` e `exp` dello standard OpenID Connect per esaminare la validità di un id\_token. Per altre informazioni su queste attestazioni, vedere il [riferimento al token della versione 2.0](active-directory-v2-tokens.md).

> [AZURE.IMPORTANT] **Assicurarsi che l'app non dipenda dall'esistenza del valore `id_token_expires_in`.**


### Modifica delle attestazioni restituite da scope=openid
Questa sarà la modifica più significativa, che influirà su quasi tutte le app che usano l'endpoint della versione 2.0. Molte applicazioni inviano richieste all'endpoint della versione 2.0 usando l'ambito `openid`, ad esempio:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Attualmente, quando l'utente concede l'autorizzazione per l'ambito `openid`, l'applicazione riceve molte informazioni sull'utente nell'id\_token risultante. Le attestazioni includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

In questo aggiornamento vengono modificate le informazioni a cui l'app ha accesso tramite l'ambito `openid`, per una maggiore conformità alle specifiche di OpenID Connect. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente nell'attestazione `sub` dell'id\_token. Le attestazioni in un id\_token a cui è stato concesso solo l'ambito `openid` saranno prive di informazioni personali. Di seguito sono riportati alcuni esempi di attestazioni dell'id\_token:

```
{ 
	"aud": "580e250c-8f26-49d0-bee8-1c078add1609",
	"iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
	"iat": 1449520283,
	"nbf": 1449520283,
	"exp": 1449524183,
	"nonce": "12345",
	"sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
	"tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
	"ver": "2.0",
}
```

Per ottenere informazioni personali sull'utente nell'app, questa dovrà richiedere autorizzazioni aggiuntive all'utente. Viene ora introdotto il supporto per due nuovi ambiti dalla specifica di OpenID Connect, `email` e `profile`, che consentono di eseguire questa operazione.

- L'ambito `email` è molto semplice: consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nell'id\_token. Si noti che l'attestazione `email` non sarà sempre presente nell'id\_token, ma verrà inclusa solo se disponibile nel profilo dell'utente.
- L'ambito `profile` concede all'app l'accesso a tutte le altre informazioni di base sull'utente, vale a dire il nome, il nome utente preferito, l'ID oggetto e così via.

Questo permette di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima, chiedendo all'utente solo il set di informazioni necessario per il funzionamento dell'app. Se si vuole continuare a ottenere il set di informazioni utente completo attualmente ricevuto dall'app, è necessario includere tutti e tre gli ambiti nelle richieste di autorizzazione:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

L'applicazione può iniziare immediatamente a inviare gli ambiti `email` e `profile` e, dopo averli accettati, l'endpoint della versione 2.0 inizia a richiedere agli utenti le autorizzazioni necessarie. Tuttavia, la modifica all'interpretazione dell'ambito `openid` non sarà effettiva ancora per alcune settimane.

> [AZURE.IMPORTANT] **Aggiungere gli ambiti `profile` e `email` se l'app richiede informazioni sull'utente.** Si noti che ADAL includerà entrambe le autorizzazioni nelle richieste per impostazione predefinita.

### Rimozione della barra finale dell'autorità di certificazione
In precedenza, il valore dell'autorità di certificazione visualizzato nei token dall'endpoint della versione 2.0 aveva il formato

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

dove guid era l'ID tenant del tenant di Azure AD che aveva emesso il token. Con queste modifiche, il valore dell'autorità di certificazione diventa

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

sia nei token che nel documento di individuazione di OpenID Connect.

> [AZURE.IMPORTANT] **Assicurarsi che l'app accetti il valore dell'autorità di certificazione con e senza una barra finale durante la convalida dell'autorità di certificazione.**

## Perché cambiare
La motivazione principale per l'introduzione di queste modifiche è la conformità alle specifiche dello standard OpenID Connect. Con la conformità a OpenID Connect, Microsoft punta a ridurre al minimo le differenze tra l'integrazione con i servizi di identità Microsoft e con altri servizi di identità del settore. Gli sviluppatori devono avere la possibilità di usare le librerie di autenticazione open source preferite, senza doverle adattare alle differenze di Microsoft.

## Cosa fare
A partire da oggi, è possibile iniziare ad apportare tutte le modifiche descritte sopra. Nell'immediato:

1.	**Rimuovere tutte le dipendenze dal parametro di intestazione `x5t`.**
2.	**Gestire correttamente la transizione da `profile_info` a `id_token` nelle risposte dei token.**
3.  **Rimuovere tutte le dipendenze dal parametro di risposta `id_token_expires_in`.**
3.	**Aggiungere gli ambiti `profile` e `email` all'app se sono necessarie informazioni utente di base.**
4.	**Accettare i valori dell'autorità di certificazione nei token con e senza una barra finale.**

La [documentazione relativa al protocollo della versione 2.0](active-directory-v2-protocols.md) è già stata aggiornata in base a queste modifiche e può essere usata come riferimento nell'aggiornamento del codice.

Per qualsiasi domanda sull'ambito delle modifiche, è possibile contattare @AzureAD su Twitter.

## Frequenza delle modifiche ai protocolli
Non sono previste altre modifiche di rilievo ai protocolli di autenticazione. Microsoft sta unendo intenzionalmente queste modifiche in un unico rilascio per evitare la necessità di eseguire questo tipo di processo di aggiornamento nel prossimo futuro. Verranno naturalmente aggiunte altre funzionalità al servizio di autenticazione convergente della versione 2.0, ma non comporteranno modifiche di rilievo al codice esistente.

Microsoft ringrazia gli utenti per la disponibilità nel periodo di anteprima. Le informazioni e l'esperienza dei primi utenti sono state preziose e Microsoft si augura che continueranno a condividere idee e opinioni.

Buon lavoro.

Microsoft Identity Division

<!---HONumber=AcomDC_0921_2016-->