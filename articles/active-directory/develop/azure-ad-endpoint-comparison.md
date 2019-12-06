---
title: Perché eseguire l'aggiornamento alla piattaforma di identità Microsoft (v 2.0) | Azure
description: Conoscere le differenze tra l'endpoint della piattaforma Microsoft Identity (v 2.0) e l'endpoint Azure Active Directory (Azure AD) v 1.0 e i vantaggi dell'aggiornamento alla versione 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 882b4a8bfe951212d2d1fe081d16b25641dfa4d4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843887"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Perché eseguire l'aggiornamento a Microsoft Identity Platform (v 2.0)?

Quando si sviluppa una nuova applicazione, è importante comprendere le differenze tra gli endpoint di Microsoft Identity Platform (v 2.0) e Azure Active Directory (v 1.0). Questo articolo illustra le principali differenze tra gli endpoint e alcune limitazioni esistenti per la piattaforma di identità Microsoft.

> [!NOTE]
> L'endpoint della piattaforma Microsoft Identity non supporta tutti gli scenari e le funzionalità di Azure AD. Per determinare se è necessario usare l'endpoint della piattaforma Microsoft Identity, vedere le informazioni sulle [limitazioni della piattaforma di identità Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

![Utenti autorizzati a effettuare l'accesso con gli endpoint v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* L'endpoint v1.0 consente l'accesso all'applicazione (Azure AD) solo agli account aziendali e dell'istituto di istruzione
* L'endpoint della piattaforma Microsoft Identity consente agli account aziendali e dell'Istituto di istruzione di Azure AD e personal account Microsoft (MSA), ad esempio hotmail.com, outlook.com e msn.com, di eseguire l'accesso.
* Entrambi gli endpoint accettano anche gli accessi degli *[utenti Guest](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* di una directory Azure ad per le applicazioni configurate come *[tenant singolo](single-and-multi-tenant-apps.md)* o per le applicazioni *multi-tenant* configurate in modo da puntare all'endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`).

L'endpoint della piattaforma Microsoft Identity consente di scrivere app che accettano accessi da account Microsoft personali e account aziendali o dell'Istituto di istruzione. Questo consente di scrivere l'app senza tenere conto dell'account utilizzato per l'accesso. Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli account aziendali saranno disponibili funzionalità e dati aggiuntivi, come i siti di SharePoint o i dati delle directory. Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), lo stesso codice può tuttavia accedere al messaggio di posta elettronica sia per gli account personali che per quelli aziendali e dell'istituto di istruzione.

Per l'endpoint della piattaforma di identità Microsoft, è possibile usare Microsoft Authentication Library (MSAL) per ottenere l'accesso ai mondi utente, didattici ed aziendali. L'endpoint v1.0 di Azure AD accetta gli accessi solo da account aziendali e dell'istituto di istruzione.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

Le app che usano l'endpoint v1.0 di Azure AD devono specificare in anticipo le autorizzazioni OAuth 2.0 richieste, ad esempio:

![Esempio che mostra l'interfaccia utente di registrazione delle autorizzazioni](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Le autorizzazioni impostate direttamente nella registrazione dell'applicazione sono di tipo **statico**. Nonostante la definizione delle autorizzazioni statiche dell'app nel portale di Azure consenta di mantenere il codice chiaro e semplice, questa opzione può presentare problemi per gli sviluppatori:

* Al primo accesso dell'utente l'app deve richiedere tutte le autorizzazioni di cui potrebbe avere bisogno. Questo può richiedere di creare un lungo elenco di autorizzazioni che dovevano essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'accesso da parte di quest'ultimo.

* L'app deve conoscere in anticipo tutte le risorse a cui potrebbe dover accedere. Era difficile creare app che potessero accedere a un numero arbitrario di risorse.

Con l'endpoint della piattaforma di identità Microsoft, è possibile ignorare le autorizzazioni statiche definite nelle informazioni di registrazione dell'app nel portale di Azure e richiedere le autorizzazioni in modo incrementale, il che significa richiedere un set minimo di autorizzazioni in anticipo e sempre più nel tempo, perché il cliente usa funzionalità aggiuntive dell'app. A tale scopo è possibile specificare gli ambiti necessari per l'app in qualsiasi momento, inclusi i nuovi ambiti nel parametro `scope` quando si richiede un token di accesso, senza doverli definire in precedenza nelle informazioni di registrazione dell'applicazione. Se l'utente non ha ancora fornito il consenso per i nuovi ambiti aggiunti alla richiesta, gli verrà chiesto di farlo solo per le nuove autorizzazioni. Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope`, gli sviluppatori hanno il controllo completo dell'esperienza dell'utente. È anche possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale. Se l'app richiede un numero elevato di autorizzazioni, è possibile raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

Il consenso dell'amministratore fornito per conto di un'organizzazione richiede comunque le autorizzazioni statiche registrate per l'app, perciò si deve impostare tali autorizzazioni per le app nel portale di registrazione dell'app se è necessario che un amministratore fornisca il consenso per conto dell'intera organizzazione. Questo riduce i cicli richiesti dall'amministratore dell'organizzazione per configurare l'applicazione.

## <a name="scopes-not-resources"></a>Ambiti e non risorse

Per le app che usano l'endpoint v1.0, un'app può comportarsi come una **risorsa** o come un destinatario di token. Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token dalla risorsa per un determinato set di ambiti. Si consideri l'API Microsoft Graph come esempio di una risorsa:

* Identificatore della risorsa o `AppID URI`: `https://graph.microsoft.com/`
* Ambiti o `oAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via.

Questo vale per l'endpoint della piattaforma Microsoft Identity. Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI. Le app client possono richiedere ancora l'accesso a questi ambiti, Tuttavia, il modo in cui un client richiede tali autorizzazioni è stato modificato.

Per l'endpoint v1.0, l'aspetto di una richiesta di autorizzazione OAuth 2.0 per Azure AD è simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Qui il parametro **resource** indicava la risorsa per la quale l'app client richiedeva l'autorizzazione. Azure AD calcolava le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilasciava i token di conseguenza.

Per le applicazioni che usano l'endpoint della piattaforma di identità Microsoft, la stessa richiesta di autorizzazione OAuth 2,0 è simile alla seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Qui il parametro **scope** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta: è inclusa in ognuno dei valori del parametro di ambito. L'uso del parametro scope in questo modo consente all'endpoint della piattaforma di identità Microsoft di essere più conforme alla specifica OAuth 2,0 e si allinea più strettamente con le comuni procedure di settore. Consente inoltre alle app di eseguire [autorizzazioni incrementali](#incremental-and-dynamic-consent), richiedendo solo le autorizzazioni quando l'applicazione le richiede, anziché prima.

## <a name="well-known-scopes"></a>Ambiti conosciuti

### <a name="offline-access"></a>Accesso offline

Le app che usano l'endpoint della piattaforma Microsoft Identity possono richiedere l'uso di una nuova autorizzazione nota per le app: l'ambito `offline_access`. Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app. L'ambito `offline_access` viene visualizzato all'utente in finestre di dialogo di consenso, come **Accedi ai dati personali in qualsiasi momento**, che l'utente deve accettare. La richiesta dell'autorizzazione `offline_access` consentirà all'app Web di ricevere refresh_tokens OAuth 2,0 dall'endpoint della piattaforma di identità Microsoft. I token di aggiornamento hanno una lunga durata e possono essere scambiati con i nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Ciò significa che, quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione OAuth 2.0, si riceve solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimane valido per un breve periodo di tempo, in genere un'ora, poi scade. A questo punto, l'app deve reindirizzare l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento l'utente può o meno dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per ulteriori informazioni su OAuth 2,0, `refresh_tokens`e `access_tokens`, vedere le informazioni di [riferimento sul protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilo e indirizzo di posta elettronica

Storicamente, il flusso di accesso OpenID Connect più semplice con la piattaforma di identità Microsoft fornirebbe numerose informazioni sull'utente nell' *id_token*risultante. Le attestazioni nel token ID includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Le informazioni a cui l'app ha accesso tramite l'ambito `openid` sono ora limitate. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente. Per ottenere dati personali sull'utente nell'app, questa deve richiedere autorizzazioni aggiuntive all'utente. Due nuovi ambiti, `email` e `profile`, consentiranno di richiedere autorizzazioni aggiuntive.

* L'ambito `email` consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nell'id_token, ammesso che l'utente abbia un indirizzo e-mail utilizzabile.
* L'ambito `profile` consente all'app di accedere a tutte le altre informazioni di base sull'utente, ad esempio il nome, il nome utente preferito, l'ID oggetto e così via, nella id_token.

Questi ambiti permettono di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima ed è possibile chiedere all'utente solo il set di informazioni di cui l'app ha bisogno per svolgere le sue funzioni. Per ulteriori informazioni su questi ambiti, vedere [il riferimento all'ambito della piattaforma Microsoft Identity](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Attestazioni nei token

Per impostazione predefinita, l'endpoint della piattaforma di identità Microsoft rilascia un set più piccolo di attestazioni nei token per evitare che i payload siano ridotti. Se sono presenti app e servizi che hanno una dipendenza da un'attestazione specifica in un token v 1.0 che non è più disponibile per impostazione predefinita in un token della piattaforma di identità Microsoft, è consigliabile usare la funzionalità di [attestazione facoltativa](active-directory-optional-claims.md) per includere tale attestazione.

> [!IMPORTANT]
> i token v 1.0 e v 2.0 possono essere emessi sia dagli endpoint v 1.0 che dalla versione 2.0. id_tokens corrisponde *sempre* all'endpoint da cui sono stati richiesti e i token di accesso corrispondono *sempre* al formato previsto dall'API Web che il client chiamerà usando tale token.  Quindi, se l'app usa l'endpoint v 2.0 per ottenere un token per chiamare Microsoft Graph, che prevede i token di accesso in formato v 1.0, l'app riceverà un token nel formato v 1.0.  

## <a name="limitations"></a>Limitazioni

Quando si usa la piattaforma di identità Microsoft, è necessario tenere presenti alcune limitazioni.

Quando si compilano applicazioni che si integrano con la piattaforma di identità Microsoft, è necessario decidere se l'endpoint e i protocolli di autenticazione della piattaforma di identità Microsoft soddisfino le proprie esigenze. L'endpoint e la piattaforma v 1.0 sono ancora completamente supportati e, per alcuni aspetti, è più ricco di funzionalità rispetto alla piattaforma delle identità Microsoft. Tuttavia, la piattaforma di identità Microsoft [introduce vantaggi significativi](azure-ad-endpoint-comparison.md) per gli sviluppatori.

Ecco una raccomandazione semplificata per gli sviluppatori:

* Se si vuole o è necessario supportare account Microsoft personali nell'applicazione o si sta scrivendo una nuova applicazione, usare la piattaforma di identità Microsoft. È necessario tuttavia conoscere i limiti illustrati in questo articolo.
* Se si sta eseguendo la migrazione o l'aggiornamento di un'applicazione che si basa su SAML, non è possibile usare la piattaforma di identità Microsoft. In alternativa, fare riferimento alla [guida Azure ad v 1.0](v1-overview.md).

L'endpoint della piattaforma Microsoft Identity verrà sviluppato per eliminare le restrizioni elencate in questo articolo, in modo che sia necessario usare solo l'endpoint della piattaforma di identità Microsoft. Nel frattempo, usare questo articolo per determinare se l'endpoint della piattaforma Microsoft Identity è adatto alle proprie proprie. Si continuerà ad aggiornare questo articolo in modo da riflettere lo stato corrente dell'endpoint della piattaforma di identità Microsoft. Controllare nuovamente per rivalutare i requisiti in base alle funzionalità della piattaforma di identità Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app

Per ogni app che si vuole integrare con l'endpoint della piattaforma di identità Microsoft, è possibile creare una registrazione dell'app nella nuova [esperienza **Registrazioni app** ](https://aka.ms/appregistrations) nel portale di Azure. Le app account Microsoft esistenti non sono compatibili con il portale, ma tutte le app Azure AD sono, indipendentemente da dove o quando sono state registrate.

Le Registrazioni app che supportano gli account aziendali e dell'istituto di istruzione e gli account personali sono soggette alle condizioni seguenti:

* Per ogni ID applicazione sono consentiti solo due segreti dell'app.
* Un'applicazione che non è stata registrata in un tenant può essere gestita solo dall'account che l'ha registrata. Non può essere condivisa con altri sviluppatori. Questo è il caso della maggior parte delle app che sono state registrate usando un account Microsoft personale nel portale di registrazione delle app. Se si vuole condividere la registrazione dell'app con più sviluppatori, registrare l'applicazione in un tenant usando la nuova sezione **registrazioni app** della portale di Azure.
* Ci sono diverse restrizioni relative al formato dell'URL di reindirizzamento consentito. Per altre informazioni sull'URL di reindirizzamento, vedere la sezione successiva.

### <a name="restrictions-on-redirect-urls"></a>Restrizioni relative agli URL di reindirizzamento

Le app registrate per la piattaforma di identità Microsoft sono limitate a un set limitato di valori di URL di reindirizzamento. L'URL di reindirizzamento per i servizi e le app Web deve iniziare con lo schema `https` e tutti i valori degli URL di reindirizzamento devono condividere un singolo dominio DNS.  Il sistema di registrazione confronta l'intero nome DNS dell'URL di reindirizzamento esistente con il nome DNS dell'URL di reindirizzamento da aggiungere. Come URL di reindirizzamento è supportato anche `http://localhost`.  

La richiesta di aggiungere il nome DNS non viene soddisfatta se si verificano le condizioni seguenti:  

* L'intero nome DNS del nuovo URL di reindirizzamento non corrisponde al nome DNS dell'URL di reindirizzamento esistente.
* L'intero nome DNS del nuovo URL di reindirizzamento non è un sottodominio dell'URL di reindirizzamento esistente.

#### <a name="example-1"></a>Esempio 1

Se l'app ha `https://login.contoso.com` come URL di reindirizzamento, è possibile aggiungere un URL di reindirizzamento in cui il nome DNS corrisponde esattamente, come illustrato nell'esempio seguente:

`https://login.contoso.com/new`

In alternativa si può fare riferimento al sottodominio DNS login.contoso.com, come illustrato nell'esempio seguente:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Esempio 2

Se si vuole un'app con gli URL di reindirizzamento `login-east.contoso.com` e `login-west.contoso.com`, è necessario aggiungere tali URL nell'ordine seguente:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

È possibile aggiungere gli ultimi due perché si tratta di sottodomini del primo URL di reindirizzamento, contoso.com.

È possibile avere solo 20 URL di risposta per una particolare applicazione. questo limite si applica a tutti i tipi di app supportati dalla registrazione (applicazione a pagina singola (SPA), client nativo, app Web e servizio).  

Per informazioni su come registrare un'app per l'uso con la piattaforma di identità Microsoft, vedere [registrare un'app con la nuova esperienza registrazioni app](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK

Attualmente, il supporto delle librerie per l'endpoint della piattaforma Microsoft Identity è limitato. Se si vuole usare l'endpoint della piattaforma Microsoft Identity in un'applicazione di produzione, sono disponibili le opzioni seguenti:

* Se si sta creando un'applicazione Web, è possibile usare in modo sicuro il middleware sul lato server disponibile a livello generale per eseguire l'accesso e la convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware Microsoft, vedere la sezione [Introduzione a Microsoft Identity Platform](v2-overview.md#getting-started) .
* Se si sta creando un'applicazione desktop o per dispositivi mobili, è possibile usare una delle librerie di autenticazione Microsoft (MSAL). Queste librerie sono disponibili a livello generale o in un'anteprima supportata in produzione, pertanto è possibile usarle in applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere le [informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md).
* Per le piattaforme non coperte da Microsoft Libraries, è possibile eseguire l'integrazione con l'endpoint della piattaforma Microsoft Identity inviando e ricevendo direttamente i messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth [sono documentati in modo esplicito](active-directory-v2-protocols.md) per facilitare l'integrazione.
* Infine, è possibile usare OpenID Connect e librerie OAuth open source per l'integrazione con l'endpoint della piattaforma di identità Microsoft. L'endpoint della piattaforma di identità Microsoft dovrebbe essere compatibile con molte librerie di protocollo Open Source senza modifiche. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [Microsoft Identity Platform and Authentication Libraries](reference-v2-libraries.md)e l'elenco di librerie client open source ed esempi testati con l'endpoint della piattaforma di identità Microsoft.
* Come riferimento, l'endpoint `.well-known` per l'endpoint comune Microsoft Identity Platform è `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Sostituire `common` con l'ID tenant per ottenere i dati specifici del tenant.  

### <a name="protocol-changes"></a>Modifiche al protocollo

L'endpoint della piattaforma Microsoft Identity non supporta SAML o WS-Federation. supporta solo OpenID Connect e OAuth 2,0.  Le modifiche degne di nota ai protocolli OAuth 2.0 dall'endpoint v1.0 sono: 

* Viene restituita l'attestazione `email` se si configura un'attestazione facoltativa **o** è stato specificato scope=email nella richiesta. 
* Ora il parametro `scope` è supportato al posto del parametro `resource`.  
* Molte risposte sono state modificate in modo da renderle più conformi alla specifica OAuth 2.0, ad esempio per ottenere la corretta restituzione di `expires_in` come valore intero anziché come valore stringa.  

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint della piattaforma di identità Microsoft, vedere informazioni di [riferimento sul protocollo OpenID Connect e OAuth 2,0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrizioni relative a SAML

Se è stato usato Active Directory Authentication Library (ADAL) nelle applicazioni Windows, è possibile che sia stata sfruttata l'autenticazione integrata di Windows, che usa la concessione di asserzione Security Assertion Markup Language (SAML). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione dell'asserzione SAML non è supportata nell'endpoint della piattaforma di identità Microsoft.
