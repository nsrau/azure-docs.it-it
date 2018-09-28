---
title: Confronto tra gli endpoint v2.0 e v1.0 di Azure AD | Microsoft Docs
description: Informazioni sulle differenze tra l'endpoint v2.0 e l'endpoint v1.0 di Azure AD
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: andret
ms.reviewer: hirsin, celested
ms.custom: aaddev
ms.openlocfilehash: 02c7edc84d2ac3a91c33d8f266d022db5cd5cb40
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948958"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-v10-endpoint"></a>Confronto tra gli endpoint v2.0 e v1.0 di Azure AD

Quando si sviluppa una nuova applicazione, è importante conoscere le differenze tra gli endpoint v1.0 e v2.0. Di seguito sono illustrate le differenze principali, oltre ad alcune limitazioni esistenti per l'endpoint v2.0.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint 2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](#limitations).

## <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

![Utenti autorizzati a effettuare l'accesso con gli endpoint v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* L'endpoint v1.0 consente l'accesso all'applicazione (Azure AD) solo agli account aziendali e dell'istituto di istruzione

* L'endpoint v2.0 consente l'accesso agli account aziendali e dell'istituto di istruzione da Azure Active Directory e agli account personali (account del servizio gestito) (hotmail.com, outlook.com, msn.com).

* Entrambi gli endpoint v1.0 e v2.0 accettano anche l'accesso degli *[utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* di una directory di Azure AD per le applicazioni configurate come *[a tenant singolo](single-and-multi-tenant-apps.md)* o per le applicazioni *multi-tenant* configurate per puntare all'endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`).

L'endpoint v2.0 consente di scrivere app che supportano l'accesso da account sia personali che aziendali e dell'istituto di istruzione e permette quindi di scrivere app indipendenti dall'account. Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli account aziendali saranno disponibili funzionalità e dati aggiuntivi, ad esempio i siti di SharePoint o i dati della directory. Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), lo stesso codice può tuttavia accedere al messaggio di posta elettronica sia per gli account personali che per quelli aziendali e dell'istituto di istruzione.

Per l'endpoint v2.0, è possibile usare una singola libreria (MSAL) per accedere a scenari consumer, didattici e aziendali.

 L'endpoint v1.0 di Azure AD accetta gli accessi solo da account aziendali e dell'istituto di istruzione.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

Le app che usano l'endpoint v1.0 di Azure AD devono specificare in anticipo le autorizzazioni OAuth 2.0 richieste, ad esempio:

![Interfaccia utente della registrazione delle autorizzazioni](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Le autorizzazioni impostate direttamente nella registrazione dell'applicazione sono di tipo **statico**. Sebbene le autorizzazioni statiche dell'app possano essere definite nel portale di Azure e richiedano un codice chiaro e semplice, possono presentare alcuni problemi per gli sviluppatori:

* È necessario definire in fase di creazione tutte le autorizzazioni che potrebbero essere necessarie per l'app. La possibilità di aggiungere le autorizzazioni in fasi successive è un processo difficile.

* L'app deve conoscere in anticipo tutte le risorse a cui potrebbe dover accedere. È difficile creare app che possono accedere a un numero arbitrario di risorse.

* L'app deve richiedere al primo accesso dell'utente tutte le autorizzazioni di cui potrebbe avere bisogno. In alcuni casi ciò comporta l'esigenza di creare un lungo elenco di autorizzazioni che devono essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'iscrizione da parte di quest'ultimo.

Con l'endpoint v2.0, è possibile ignorare le autorizzazioni definite in modo statico nelle informazioni di registrazione dell'app nel portale di Azure e specificare le autorizzazioni richieste dall'app **dinamicamente** in fase di esecuzione durante il normale uso dell'app, indipendentemente dalle autorizzazioni definite staticamente nelle informazioni di registrazione dell'applicazione.

A tale scopo, è possibile specificare gli ambiti necessari per l'app in un determinato punto nel tempo applicazione, includendo i nuovi ambiti nel parametro `scope` quando si richiede un token di accesso, senza che sia necessario definirli in precedenza nelle informazioni di registrazione dell'applicazione.

Se l'utente non ha ancora fornito il consenso per i nuovi ambiti aggiunti alla richiesta, gli verrà chiesto di farlo solo per le nuove autorizzazioni. Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope`, gli sviluppatori hanno il controllo completo dell'esperienza dell'utente. Se si desidera, è anche possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale. In alternativa, se l'app richiede un numero elevato di autorizzazioni, è possibile scegliere di raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

Si noti che il consenso dell'amministratore fornito per conto di un'organizzazione usa ancora le autorizzazioni statiche registrate per l'app, quindi è consigliabile impostare tali autorizzazioni per le app usando l'endpoint v2.0, se è necessario che un amministratore fornisca il consenso per conto dell'intera organizzazione. Ciò riduce i cicli richiesti dall'amministratore dell'organizzazione per configurare l'applicazione

## <a name="scopes-not-resources"></a>Ambiti e non risorse

Per le app che usano l'endpoint v1.0, un'app può comportarsi come una **risorsa** o come un destinatario di token. Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token per la risorsa per un determinato set di ambiti. Si consideri l'API Graph di Azure AD come esempio di una risorsa:

* Identificatore della risorsa o `AppID URI`: `https://graph.windows.net/`

* Ambiti o `OAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via.

Quanto descritto si applica all'endpoint 2.0. Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI. Le app client possono richiedere ancora l'accesso a questi ambiti, tuttavia, è stata modificata la modalità con cui un client esegue la richiesta delle autorizzazioni. Per l'endpoint v1.0, l'aspetto di una richiesta di autorizzazione OAuth 2.0 per Azure AD è simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

dove il parametro della **risorsa** indica la risorsa per la quale l'app client richiede l'autorizzazione. Azure AD calcola le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilascia i token di conseguenza. Per le applicazioni che usano l'endpoint v2.0, la stessa richiesta di autorizzazione OAuth 2.0 ha un aspetto simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Dove il parametro **scope** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta, semplicemente è inclusa in ognuno dei valori del parametro di ambito. Tale uso del parametro di ambito consente all'endpoint 2.0 di essere più conforme alla specifica OAuth 2.0 e alle pratiche comuni del settore. Consente inoltre alle app di eseguire il [consenso incrementale](#incremental-and-dynamic-consent), descritto nella sezione precedente.

## <a name="well-known-scopes"></a>Ambiti conosciuti

### <a name="offline-access"></a>Accesso offline

Per le app che usano l'endpoint 2.0 è possibile che sia necessaria una nuova autorizzazione nota per le app: l'ambito `offline_access`. Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app. L'ambito `offline_access` viene visualizzato all'utente in finestre di dialogo di consenso, come **Accedi ai dati personali in qualsiasi momento**, che l'utente deve accettare. La richiesta dell'autorizzazione `offline_access` consentirà all'app Web di ricevere token di aggiornamento di OAuth 2.0 dall'endpoint 2.0. I token di aggiornamento hanno una lunga durata e possono essere scambiati con i nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Ciò significa che quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione OAuth 2.0, si riceve solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimane valido per un breve periodo di tempo (in genere un'ora), per poi scadere. A questo punto, l'app deve reindirizzare l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, l'utente può o meno avere esigenza di immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su OAuth 2.0, `refresh_tokens` e `access_tokens`, vedere le [informazioni di riferimento sui protocolli v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilo e indirizzo di posta elettronica

Tradizionalmente, il flusso di accesso più semplice di OpenID Connect in Azure AD fornisce molte informazioni sull'utente nell'oggetto *id_token* risultante. Le attestazioni in un oggetto *id_token* possono includere il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Le informazioni a cui l'app ha accesso tramite l'ambito `openid` sono ora limitate. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente. Per ottenere dati personali sull'utente nell'app, questa dovrà richiedere autorizzazioni aggiuntive all'utente. Due nuovi ambiti, `email` e `profile`, consentiranno di richiedere autorizzazioni aggiuntive.

L'ambito `email` consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nel token ID. L'ambito `profile` concede all'app l'accesso a tutte le altre informazioni di base sull'utente, vale a dire il nome, il nome utente preferito, l'ID oggetto e così via.

Questo permette di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima, chiedendo all'utente solo il set di informazioni necessario per il funzionamento dell'app. Per altre informazioni su questi ambiti, vedere le [informazioni di riferimento sugli ambiti della versione 2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Attestazioni nei token

Le attestazioni nei token rilasciati dall'endpoint v2.0 non saranno identiche a quelle dei token rilasciati dagli endpoint di Azure AD disponibili a livello generale. Le app che eseguono la migrazione al nuovo servizio non devono presupporre che esista un'attestazione particolare nei token ID o di accesso. Altri dettagli sui diversi tipi di token usati nell'endpoint v2.0 sono disponibili nelle informazioni di riferimento sui [token di accesso](access-tokens.md) e nelle [informazioni di riferimento su `id_token`](id-tokens.md).

## <a name="limitations"></a>Limitazioni

Ci sono alcune limitazioni da tenere in considerazione quando si usa la versione 2.0.

Quando si creano applicazioni che si integrano con Microsoft Identity Platform, è necessario stabilire se i protocolli di autenticazione e l'endpoint v2.0 soddisfano le proprie esigenze. La piattaforma e l'endpoint v1.0 sono ancora supportati e, per alcuni aspetti, offrono più funzionalità della versione 2.0. Nella versione 2.0, tuttavia, vengono [introdotti vantaggi significativi](azure-ad-endpoint-comparison.md) per gli sviluppatori.

Di seguito sono riportati alcuni consigli per gli sviluppatori, opportunamente semplificati:

* Se nell'applicazione devono essere supportati account Microsoft personali, usare la versione 2.0. È necessario tuttavia comprendere prima i limiti illustrati in questo articolo.

* Se l'applicazione deve supportare solo account Microsoft aziendali e dell'istituto di istruzione, non usare la versione 2.0. Vedere invece la [guida alla versione 1.0](azure-ad-developers-guide.md).

L'endpoint 2.0 verrà migliorato per eliminare le restrizioni elencate di seguito, in modo da consentire l'uso esclusivo dell'endpoint 2.0. Nel frattempo, usare questo articolo per determinare se l'endpoint 2.0 è adatto alle proprie esigenze. L'articolo verrà aggiornato periodicamente per riflettere lo stato corrente dell'endpoint 2.0. È consigliabile tornare a consultarlo regolarmente per valutare i propri requisiti rispetto alle funzionalità della versione 2.0.

### <a name="restrictions-on-app-types"></a>Restrizioni relative ai tipi di app

I tipi di app seguenti non sono attualmente supportati dall'endpoint 2.0. Per una descrizione dei tipi di app supportati, vedere [Tipi di app per l'endpoint v2.0](v2-app-types.md).

#### <a name="standalone-web-apis"></a>API Web autonome

È possibile usare l'endpoint 2.0 per [compilare un'API Web protetta con OAuth 2.0](v2-app-types.md#web-apis). L'API Web, tuttavia, può ricevere i token solo da un'applicazione che condivide lo stesso ID applicazione. Non è possibile accedere a un'API Web da un client con un ID applicazione diverso perché il client non sarà in grado di richiedere o ottenere autorizzazioni per l'API Web.

Per informazioni su come creare un'API Web che accetta token da un client con lo stesso ID applicazione, vedere gli esempi di API Web dell'endpoint v2.0 nella sezione di [introduzione alla versione 2.0](v2-overview.md#getting-started).

### <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app

Per ogni app da integrare con l'endpoint 2.0, è necessario attualmente creare una registrazione di app nel nuovo [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Le app Azure AD o degli account Microsoft esistenti non sono compatibili con l'endpoint 2.0 né lo sono quelle registrate in qualsiasi portale diverso da quello di registrazione delle applicazioni.

Per le registrazioni di app create nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), è necessario anche tenere in considerazione gli aspetti seguenti:

* Sono consentiti solo due segreti dell'app per ogni ID applicazione.

* La registrazione di un'app eseguita da un utente con un account Microsoft personale può essere visualizzata e gestita solo da un singolo account sviluppatore. Non può essere condiviso tra più sviluppatori. Se si vuole condividere la registrazione dell'app tra più sviluppatori, è possibile creare l'applicazione accedendo al portale di registrazione con un account di Azure AD.

* Ci sono diverse restrizioni relative al formato dell'URL di reindirizzamento consentito. Per altre informazioni sull'URL di reindirizzamento, vedere la sezione successiva.

### <a name="restrictions-on-redirect-urls"></a>Restrizioni relative agli URL di reindirizzamento

Le app registrate nel portale di registrazione delle applicazioni possono usare un set limitato di valori di URL di reindirizzamento. L'URL di reindirizzamento per i servizi e le app Web deve iniziare con lo schema `https` e tutti i valori degli URL di reindirizzamento devono condividere un singolo dominio DNS. Non è possibile, ad esempio, registrare un'app Web con uno di questi URL di reindirizzamento:

* `https://login-east.contoso.com`  
* `https://login-west.contoso.com`

Il sistema di registrazione confronta l'intero nome DNS dell'URL di reindirizzamento esistente con il nome DNS dell'URL di reindirizzamento da aggiungere. La richiesta di aggiungere il nome DNS non viene soddisfatta se si verificano le condizioni seguenti:  

* L'intero nome DNS del nuovo URL di reindirizzamento non corrisponde al nome DNS dell'URL di reindirizzamento esistente.

* L'intero nome DNS del nuovo URL di reindirizzamento non è un sottodominio dell'URL di reindirizzamento esistente.

Se, ad esempio, l'app ha questo URL di reindirizzamento:

`https://login.contoso.com`

è possibile eseguire un'aggiunta analoga alla seguente:

`https://login.contoso.com/new`

In questo caso, il nome DNS corrisponde esattamente. In alternativa, è possibile eseguire un'aggiunta analoga alla seguente:

`https://new.login.contoso.com`

In questo caso, si fa riferimento a un sottodominio DNS di login.contoso.com. Se si vuole un'app con gli URL di reindirizzamento `login-east.contoso.com` e `login-west.contoso.com`, è necessario aggiungere tali URL nell'ordine seguente:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Gli ultimi due URL possono essere aggiunti perché si tratta di sottodomini del primo URL di reindirizzamento, ovvero contoso.com. Questa limitazione verrà rimossa in una versione futura.

Si noti inoltre che è possibile avere solo 20 URL di risposta per una determinata applicazione.

Per informazioni su come registrare un'app nel portale di registrazione delle applicazioni, vedere [Come registrare un'app con l'endpoint 2.0](quickstart-v2-register-an-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK

Il supporto delle librerie per l'endpoint 2.0 è attualmente limitato. Per usare l'endpoint 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

* Se si compila un'applicazione Web, è possibile usare il middleware lato server di Microsoft disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware Microsoft, vedere la sezione di [introduzione alla versione 2.0](v2-overview.md#getting-started).

* Se si compila un'applicazione desktop o portatile, è possibile usare una delle librerie di autenticazione Microsoft (MSAL) di anteprima. Queste librerie sono disponibili in anteprima supportata di produzione ed è quindi consigliabile usarle in applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere le [informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md).

* In caso di piattaforme non coperte da librerie Microsoft, è possibile integrarle con l'endpoint 2.0 tramite l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth della versione 2.0 [sono documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.

* Per realizzare l'integrazione con l'endpoint 2.0, è anche possibile usare librerie OpenID Connect e OAuth di tipo open source. Il protocollo della versione 2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche rilevanti. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [Azure Active Directory 2.0 e librerie di autenticazione](reference-v2-libraries.md), l'elenco di librerie client open source e gli esempi testati con l'endpoint 2.0.

* Per riferimento, l'endpoint `.well-known` per l'endpoint comune v2.0 è `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` .  Sostituire `common` con l'ID tenant per ottenere i dati specifici del tenant.  

### <a name="restrictions-on-protocols"></a>Restrizioni relative ai protocolli

L'endpoint 2.0 non supporta protocolli SAML o WS-Federation, ma solo Open ID Connect e OAuth 2.0. Non tutte le funzionalità e caratteristiche dei protocolli OAuth, tuttavia, sono state incorporate nell'endpoint 2.0.

Le funzionalità del protocollo seguenti *non sono attualmente disponibili* nell'endpoint 2.0:

* Correntemente, l'attestazione `email` viene restituita solo se viene configurata un'attestazione facoltativa e l'ambito specificato nella richiesta è scope=email. Questo comportamento, tuttavia, verrà modificato quando l'endpoint 2.0 viene aggiornato per ragioni di compatibilità con gli standard Open ID Connect e OAuth2.0.

* L'endpoint 2.0 non supporta attualmente l'emissione di attestazioni basate su ruolo o gruppo nei token ID.

* La concessione [OAuth 2.0 Resource Owner Password Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.3) non è supportata dall'endpoint 2.0.

Tenere presente inoltre che l'endpoint v2.0 non supporta alcuna forma dei protocolli SAML o WS-Federation.

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint 2.0, vedere l'articolo [Riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrizioni relative a SAML

Se si usa Active Directory Authentication Library (ADAL) in applicazioni Windows, è possibile che si debba ricorrere all'autenticazione integrata di Windows, che sfrutta la concessione per l'asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione per l'asserzione SAML non è supportata dall'endpoint 2.0.
