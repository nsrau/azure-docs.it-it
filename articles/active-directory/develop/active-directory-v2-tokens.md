---
title: Informazioni di riferimento sui token di Azure Active Directory 2.0 | Documentazione Microsoft
description: Tipi di token e attestazioni generati dall&quot;endpoint di Azure AD 2.0.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 3d5ad974c01e0ee3954da4f990da87338b2d1756
ms.openlocfilehash: 3a3d5c8bf4da9255015fab64f2b59637c4c030ea
ms.contentlocale: it-it
ms.lasthandoff: 02/23/2017


---
# <a name="azure-active-directory-v20-tokens-reference"></a>Informazioni di riferimento sui token di Azure Active Directory 2.0
L'endpoint di Azure Active Directory (Azure AD) 2.0 genera tipi diversi di token di sicurezza in ogni [flusso di autenticazione](active-directory-v2-flows.md). Questo articolo di riferimento descrive il formato, le caratteristiche di sicurezza e i contenuti di ogni tipo di token.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint 2.0, vedere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
>
>

## <a name="types-of-tokens"></a>Tipi di token
L'endpoint 2.0 supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-v2-protocols.md), che usa sia token di accesso che token di aggiornamento. L'endpoint 2.0 supporta anche l'autenticazione e l'accesso tramite [OpenID Connect](active-directory-v2-protocols.md). OpenID Connect introduce un terzo tipo di token, il token ID. Ognuno di questi token viene rappresentato come *token di connessione*.

Un token di connessione è un token di sicurezza leggero che consente al portatore di accedere a una risorsa protetta. Per "portatore" si intende qualsiasi parte che possa presentare il token. Anche se la ricezione del token di connessione è subordinata al processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Alcuni token di sicurezza hanno un meccanismo predefinito che ne impedisce l'uso da parti non autorizzate, mentre i token di connessione non lo hanno e devono essere trasportati usando un canale protetto, ad esempio il protocollo Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza questo tipo di sicurezza, un utente malintenzionato potrebbe ricorrere a un attacco man-in-the-middle per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Molti dei token generati dall'endpoint 2.0 vengono implementati come token JWT (token JSON Web). Un token JWT è una soluzione compatta e sicura per gli URL per trasferire informazioni tra due parti. Le informazioni contenute in un token JWT sono dette *attestazioni*. Si tratta di asserzioni di informazioni relative alla connessione e all'oggetto del token. Le attestazioni in un token JWT sono oggetti JavaScript Object Notation (JSON) codificati e serializzati per la trasmissione. Dato che i token JWT generati dall'endpoint 2.0 sono firmati, ma non crittografati, è possibile esaminarne facilmente i contenuti a scopo di debug. Per altre informazioni sui token JWT, vedere le [specifiche dei token JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Token ID
I token ID sono un tipo di token di sicurezza di accesso che l'app riceve quando esegue l'autenticazione usando [OpenID Connect](active-directory-v2-protocols.md). Vengono rappresentati come token [JWT](#types-of-tokens) e contengono attestazioni che è possibile usare per l'accesso degli utenti all'app. Le attestazioni nei token ID possono essere usate in diversi modi. In genere, gli amministratori usano i token ID per visualizzare informazioni sull'account o per prendere decisioni relative al controllo di accesso in un'app. L'endpoint 2.0 genera solo un tipo di token ID che dispone di un set coerente di attestazioni, indipendentemente dal tipo di utente che ha eseguito l'accesso. Il formato e il contenuto dei token ID sono identici per gli utenti con account Microsoft personali e per quelli con account aziendali o dell'istituto di istruzione.

Attualmente i token ID sono firmati, ma non crittografati. Quando l'app riceve un token ID, deve [convalidare la firma](#validating-tokens) per dimostrare l'autenticità del token e convalidare alcune attestazioni nel token per dimostrarne la validità. A seconda dei requisiti dello scenario, le attestazioni convalidate da un'app possono variare, ma l'app deve eseguire alcune [operazioni comuni di convalida delle attestazioni](#validating-tokens) in ogni scenario.

Le sezioni seguenti contengono informazioni dettagliate sulle attestazioni nei token ID e un token ID di esempio. Si noti che le attestazioni nei token ID non vengono restituite in un ordine specifico. Si noti anche che possono essere introdotte nuove attestazioni nei token ID in qualsiasi momento. L'introduzione di nuove attestazioni non deve interrompere il funzionamento dell'app. L'elenco seguente include le attestazioni che l'app può interpretare in modo affidabile allo stato attuale. Per informazioni più dettagliate, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-id-token"></a>Token ID di esempio
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> A scopo di esercitazione, provare a verificare le attestazioni nel token ID di esempio incollando quest'ultimo in [calebb.net](http://calebb.net/).
>
>

#### <a name="claims-in-id-tokens"></a>Attestazioni nei token ID
| Nome | Attestazione | Valore di esempio | Descrizione |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |Identifica il destinatario del token. Nei token ID il destinatario è l'ID applicazione dell'app, assegnato a quest'ultima nel portale di registrazione delle applicazioni di Microsoft. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
| autorità di certificazione |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. L'app deve convalidare l'attestazione Autorità di certificazione per assicurarsi che il token sia stato fornito dall'endpoint 2.0. Deve usare la parte GUID dell'attestazione per limitare il set di tenant che può accedere all'app. Il GUID che indica che l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| ora di emissione |`iat` |`1452285331` |Ora in cui il token è stato generato, rappresentata dal valore epoch time. |
| scadenza |`exp` |`1452289231` |Ora in cui il token non è più valido, rappresentata dal valore epoch time. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| non prima |`nbf` |`1452285331` |L’ora in cui il token diventa valido, rappresentata dal valore epoch time. È in genere uguale all’ora di emissione. L'app deve usare questa attestazione per verificare la validità della durata del token. |
| version |`ver` |`2.0` |Versione del token ID, come definito da Azure AD. Per l'endpoint 2.0 il valore è `2.0`. |
| ID tenant |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |Valore GUID che rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID è l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali il valore è `9188040d-6c67-4c5b-b112-36a304b66dad`. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| hash del codice |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |L'hash del codice è incluso in un token ID solo quando quest'ultimo viene generato con un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| hash del token di accesso |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |L'hash del token di accesso è incluso in un token ID solo quando quest'ultimo viene generato con un token di accesso di OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html). |
| nonce |`nonce` |`12345` |L'attestazione Nonce è una strategia per ridurre gli attacchi di riproduzione del token. L'app può specificare un'attestazione Nonce in una richiesta di autorizzazione usando il parametro di query `nonce` . Il valore specificato nella richiesta viene generato nell'attestazione `nonce` del token ID, senza essere modificato. L'app può verificare il valore rispetto al valore specificato nella richiesta che associa la sessione dell'app a un token ID specifico. L'app deve eseguire la convalida durante il processo di convalida del token ID. |
| name |`name` |`Babe Ruth` |Questa attestazione fornisce un valore leggibile che identifica l'oggetto del token. Il valore potrebbe non essere univoco, è modificabile e può essere usato solo per scopi di visualizzazione. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| email |`email` |`thegreatbambino@nyy.onmicrosoft.com` |Indirizzo di posta elettronica primario associato all'account utente, se presente. Il valore è modificabile e può variare nel tempo. Per ricevere questa attestazione, è necessario l'ambito `email` . |
| nome utente preferito |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |Nome utente primario che rappresenta l'utente nell'endpoint 2.0. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può variare nel tempo. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa oppure come chiave nelle tabelle di database. Dato che il soggetto è sempre presente nei token generati da Azure AD, è consigliabile usare questo valore in un sistema di autorizzazione di uso generico. L'oggetto è tuttavia un identificatore pairwise univoco per un ID di applicazione specifico.  Se quindi un singolo utente accede a due app diverse usando due ID client differenti, queste app riceveranno due valori differenti per l'attestazione dell'oggetto.  Questa condizione può non essere appropriata a seconda dei requisiti a livello di architettura e di privacy. |
| ID oggetto |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Identificatore non modificabile per un oggetto nel sistema di identità Microsoft, in questo caso, un account utente.  Può essere usato anche per eseguire i controlli di autorizzazioni in modo sicuro e come chiave nelle tabelle di database. Questo ID identifica in modo univoco l'utente nelle applicazioni; due applicazioni differenti che consentono l'accesso dello stesso utente riceveranno lo stesso valore nell'attestazione `oid`.  Ciò significa che può essere usato quando si eseguono query in Microsoft Online Services, ad esempio Microsoft Graph.  Microsoft Graph restituirà l'ID come proprietà `id` per un determinato account utente.  Poiché `oid` consente a più app di correlare utenti, per ricevere questa attestazione è necessario l'ambito `profile`. Si noti che se un singolo utente è presente in più tenant, l'utente conterrà un ID oggetto diverso in ogni tenant; vengono considerati account diversi, anche se l'utente accede a ogni account con le stesse credenziali. |

### <a name="access-tokens"></a>Token di accesso
Attualmente, i token di accesso generati dall'endpoint 2.0 possono essere utilizzati solo dai servizi Microsoft. Le app non devono eseguire la convalida o l'ispezione dei token di accesso per gli scenari attualmente supportati. I token di accesso possono essere considerati completamente opachi. Si tratta semplicemente di stringhe che l'app può passare a Microsoft nelle richieste HTTP.

In una fase successiva, l'endpoint 2.0 introdurrà la possibilità per l'app di ricevere i token di accesso da altri client. A quel punto, il contenuto di questo articolo di riferimento verrà aggiornato con le informazioni necessarie perché l'app possa eseguire la convalida del token di accesso e altre operazioni simili.

Quando si richiede un token di accesso dall'endpoint 2.0, quest'ultimo restituisce anche alcuni metadati relativi al token di accesso che l'app deve usare. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per i quali è valido. L'app usa i metadati per eseguire operazioni di memorizzazione intelligente dei token di accesso nella cache senza la necessità di analizzare il token di accesso stesso.

### <a name="refresh-tokens"></a>Token di aggiornamento
I token di aggiornamento sono token di sicurezza che l'app può usare per ottenere nuovi token di accesso in un flusso di OAuth 2.0. Consentono all'app di ottenere l'accesso a lungo termine alle risorse per conto dell'utente senza richiedere l'interazione con l'utente.

Si tratta di token a più risorse, ossia un token di aggiornamento ricevuto durante una richiesta di token per una risorsa può essere riscattato per i token di accesso a una risorsa completamente diversa.

Per ricevere un aggiornamento in una risposta del token, l'app deve richiedere e ottenere l'ambito `offline_acesss`. Per altre informazioni sull'ambito `offline_access`, vedere l'articolo relativo a [consenso e ambiti](active-directory-v2-scopes.md).

I token di aggiornamento sono e saranno sempre completamente opachi per l'app. Vengono generati dall'endpoint 2.0 di Azure AD e possono essere controllati e interpretati solo dall'endpoint 2.0. Hanno una durata elevata, ma l'app non deve essere scritta aspettandosi che un token di aggiornamento duri per un periodo indefinito. I token di aggiornamento possono essere annullati in qualsiasi momento per vari motivi. L'unico modo per l'app di sapere se un token di aggiornamento è valido è tentare di riscattarlo mediante una richiesta di token all'endpoint 2.0.

Quando si riscatta un token di aggiornamento per un nuovo token di accesso e l'app ha ottenuto l'ambito `offline_access`, si riceve un nuovo token di aggiornamento nella risposta del token. Salvare il token di aggiornamento appena generato per sostituire quello usato nella richiesta. In questo modo è possibile prolungare la validità dei token di aggiornamento.

## <a name="validating-tokens"></a>Convalida dei token
Attualmente, l'unica convalida dei token che le app devono eseguire è la convalida dei token ID. Per convalidare un token ID, l'app deve convalidarne la firma e le attestazioni incluse.

<!-- TODO: Link -->
Microsoft fornisce esempi di codice e librerie che illustrano come gestire in modo semplice la convalida dei token. Le sezioni successive illustrano il processo sottostante. Sono anche disponibili varie librerie open source di terze parti per la convalida dei token JWT. È disponibile almeno una libreria per ogni piattaforma e linguaggio.

### <a name="validate-the-signature"></a>convalidare la firma
Un token JWT contiene tre segmenti separati dal carattere `.` . Il primo segmento è noto come *intestazione*, il secondo come *corpo* e il terzo come *firma*. Il segmento della firma può essere usato per convalidare l'autenticità del token ID perché venga considerato attendibile dall'app.

I token ID vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token ID contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token, ad esempio:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token. L'attestazione `kid` indica la chiave pubblica usata per firmare il token.

In qualsiasi momento, l'endpoint 2.0 può firmare un token ID usando una qualsiasi coppia di chiavi pubblica-privata di un set specifico. L'endpoint 2.0 ruota periodicamente il set di chiavi, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate dall'endpoint 2.0 è di circa 24 ore.

È possibile acquisire i dati della chiave di firma necessari per convalidare la firma usando il documento di metadati di OpenID Connect disponibile all'indirizzo:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Provare l'URL in un browser.
>
>

Il documento di metadati è un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per l'autenticazione di OpenID Connect.  Il documento include anche un oggetto *jwks_uri* che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Il documento JSON in jwks_uri include tutte le informazioni sulla chiave pubblica corrente. L'app può usare l'attestazione `kid` nell'intestazione del token JWT per selezionare la chiave pubblica del documento usata per firmare un token. Esegue quindi la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

La convalida della firma non rientra nelle finalità di questo documento. A tale scopo è possibile consultare le varie librerie open source disponibili.

### <a name="validate-the-claims"></a>Convalidare le attestazioni
Quando l'app riceve un token ID all'accesso dell'utente, deve eseguire anche alcuni controlli in base alle attestazioni nel token ID. Sono incluse, ad esempio:

* Attestazione **destinatari**: verifica che il token ID fosse destinato all'app.
* Attestazioni **non prima** e **scadenza**: verificano che il token ID non sia scaduto.
* Attestazione **autorità di certificazione**: verifica che il token sia stato rilasciato all'app dall'endpoint 2.0.
* Attestazione **nonce**: per ridurre gli attacchi di riproduzione dei token.

Per un elenco completo delle convalide di attestazione che l'app deve eseguire, vedere la [specifica di OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Per informazioni dettagliate sui valori previsti per tali attestazioni, vedere la sezione [Token ID](# ID tokens).

## <a name="token-lifetimes"></a>Durata dei token
Le indicazioni sulla durata dei token fornite di seguito sono solo a scopo informativo e possono risultare utili per lo sviluppo e il debug delle app. Le app non devono essere scritte in base a una durata specifica prevista come costante. La durata del token può cambiare in qualsiasi momento.

| Token | Durata | Descrizione |
| --- | --- | --- |
| Token ID (account aziendale o dell'istituto di istruzione) |1 ora |I token ID hanno in genere una validità di un'ora. L'app Web può usare questa durata per la gestione di una sessione con l'utente (scelta consigliata) oppure è possibile scegliere una durata di sessione completamente diversa. Per ottenere un nuovo token ID, l'app deve eseguire una nuova richiesta di accesso all'endpoint di autorizzazione 2.0. Se l'utente ha una sessione del browser valida con l'endpoint 2.0, può non essere necessario immettere nuovamente le credenziali. |
| Token ID (account personale) |24 ore |I token ID per gli account personali hanno in genere una validità di 24 ore. L'app Web può usare questa durata per la gestione di una sessione con l'utente (scelta consigliata) oppure è possibile scegliere una durata di sessione completamente diversa. Per ottenere un nuovo token ID, l'app deve eseguire una nuova richiesta di accesso all'endpoint di autorizzazione 2.0. Se l'utente ha una sessione del browser valida con l'endpoint 2.0, può non essere necessario immettere nuovamente le credenziali. |
| Token di accesso (account aziendale o dell'istituto di istruzione) |1 ora |Indicati nelle risposte del token come parte dei metadati del token. |
| Token di accesso (account personale) |1 ora |Indicati nelle risposte del token come parte dei metadati del token. I token di accesso generati per conto di account personali possono essere configurati con una durata diversa, ma la durata tipica è un'ora. |
| Token di aggiornamento (account aziendale o dell'istituto di istruzione) |Fino a 14 giorni |Un singolo token di aggiornamento è valido per un periodo massimo di 14 giorni. Tuttavia, il token di aggiornamento può diventare non valido in qualsiasi momento per diversi motivi, quindi l'app deve continuare a provare a usare un token di aggiornamento fino a quando non si verifica un errore o fino a quando non lo sostituisce con uno nuovo. Inoltre, il token di aggiornamento può diventare non valido dopo 90 giorni dall'immissione delle credenziali da parte dell'utente. |
| Token di aggiornamento (account personale) |Fino a 1 anno |Un singolo token di aggiornamento è valido per un periodo massimo di 1 anno. Tuttavia, il token di aggiornamento può diventare non valido in qualsiasi momento per diversi motivi, quindi l'app deve continuare a provare a usare un token di aggiornamento fino a quando non si verifica un errore. |
| Codici di autorizzazione (account aziendale o dell'istituto di istruzione) |10 minuti |I codici di autorizzazione sono intenzionalmente di breve durata e devono essere immediatamente riscattati per i token di accesso e di aggiornamento quando vengono ricevuti. |
| Codici di autorizzazione (account personale) |5 minuti |I codici di autorizzazione sono intenzionalmente di breve durata e devono essere immediatamente riscattati per i token di accesso e di aggiornamento quando vengono ricevuti. I codici di autorizzazione generati per conto degli account personali possono essere usati una sola volta. |

