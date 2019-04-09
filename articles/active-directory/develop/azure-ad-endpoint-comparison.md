---
title: Confronto tra l'endpoint di piattaforma (v2.0) di Microsoft identity con l'endpoint v1.0 di Azure AD | Microsoft Docs
description: Conoscere le differenze tra l'endpoint di Microsoft identity platform (v2.0) e l'endpoint v1.0 di Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd443c95e8cf6dbddd66e5531b182469a118e4c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260732"
---
# <a name="comparing-the-microsoft-identity-platform-endpoint-and-azure-ad-v10-endpoint"></a>Confronto tra l'endpoint di Microsoft identity platform ed endpoint v1.0 di Azure AD

Quando si sviluppa una nuova applicazione, è importante conoscere le differenze tra gli endpoint di Azure Active Directory (v1.0) e la piattaforma delle identità di Microsoft (v2.0). Questo articolo illustra le differenze principali tra gli endpoint e alcune limitazioni esistenti per la piattaforma delle identità Microsoft.

> [!NOTE]
> L'endpoint di piattaforma di identità di Microsoft non supporta tutti gli scenari di Azure AD e le funzionalità. Per determinare se è necessario usare l'endpoint di piattaforma Microsoft identity, a conoscenza [limitazioni della piattaforma di identità Microsoft](#limitations).

## <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

![Utenti autorizzati a effettuare l'accesso con gli endpoint v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* L'endpoint v1.0 consente l'accesso all'applicazione (Azure AD) solo agli account aziendali e dell'istituto di istruzione
* L'endpoint di Microsoft identity platform consente di lavoro e gli account dell'istituto di istruzione da Azure AD e gli account Microsoft personali (MSA), ad esempio hotmail.com, outlook.com e msn.com, eseguire l'accesso.
* Entrambi gli endpoint anche accettano accessi sia di *[gli utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* di Azure Active directory per le applicazioni configurate come *[a tenant singolo](single-and-multi-tenant-apps.md)* o per *multi-tenant* le applicazioni configurate per fare riferimento all'endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`).

L'endpoint di Microsoft identity platform consente di scrivere App che supportano l'accesso degli account Microsoft personali e account aziendali e dell'istituto di istruzione. Questo consente di scrivere l'app senza tenere conto dell'account utilizzato per l'accesso. Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli account aziendali saranno disponibili funzionalità e dati aggiuntivi, come i siti di SharePoint o i dati delle directory. Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), lo stesso codice può tuttavia accedere al messaggio di posta elettronica sia per gli account personali che per quelli aziendali e dell'istituto di istruzione.

Per endpoint di piattaforma delle identità di Microsoft, è possibile usare Microsoft Authentication Library (MSAL) per ottenere l'accesso al consumer, didattici e i mondi enterprise. L'endpoint v1.0 di Azure AD accetta gli accessi solo da account aziendali e dell'istituto di istruzione.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

Le app che usano l'endpoint v1.0 di Azure AD devono specificare in anticipo le autorizzazioni OAuth 2.0 richieste, ad esempio:

![Interfaccia utente della registrazione delle autorizzazioni](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Le autorizzazioni impostate direttamente nella registrazione dell'applicazione sono di tipo **statico**. Nonostante la definizione delle autorizzazioni statiche dell'app nel portale di Azure consenta di mantenere il codice chiaro e semplice, questa opzione può presentare problemi per gli sviluppatori:

* Al primo accesso dell'utente l'app deve richiedere tutte le autorizzazioni di cui potrebbe avere bisogno. Questo può richiedere di creare un lungo elenco di autorizzazioni che dovevano essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'accesso da parte di quest'ultimo.

* L'app deve conoscere in anticipo tutte le risorse a cui potrebbe dover accedere. Era difficile creare app che potessero accedere a un numero arbitrario di risorse.

Con l'endpoint di piattaforma delle identità di Microsoft, è possibile ignorare le autorizzazioni statiche definite nelle informazioni di registrazione di app nelle autorizzazioni di richiesta e portale di Azure in modo incrementale, invece, il che significa che richiede un set di autorizzazioni iniziali minime bare e più conseguiti nel tempo se il cliente Usa le funzionalità aggiuntive di app. A tale scopo è possibile specificare gli ambiti necessari per l'app in qualsiasi momento, inclusi i nuovi ambiti nel parametro `scope` quando si richiede un token di accesso, senza doverli definire in precedenza nelle informazioni di registrazione dell'applicazione. Se l'utente non ha ancora fornito il consenso per i nuovi ambiti aggiunti alla richiesta, gli verrà chiesto di farlo solo per le nuove autorizzazioni. Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope`, gli sviluppatori hanno il controllo completo dell'esperienza dell'utente. È anche possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale. Se l'app richiede un numero elevato di autorizzazioni, è possibile raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

Il consenso dell'amministratore fornito per conto di un'organizzazione richiede comunque le autorizzazioni statiche registrate per l'app, perciò si deve impostare tali autorizzazioni per le app nel portale di registrazione dell'app se è necessario che un amministratore fornisca il consenso per conto dell'intera organizzazione. Questo riduce i cicli richiesti dall'amministratore dell'organizzazione per configurare l'applicazione.

## <a name="scopes-not-resources"></a>Ambiti e non risorse

Per le app che usano l'endpoint v1.0, un'app può comportarsi come una **risorsa** o come un destinatario di token. Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token dalla risorsa per un determinato set di ambiti. Si consideri l'API Graph di Azure AD come esempio di una risorsa:

* Identificatore di risorsa o `AppID URI`: `https://graph.windows.net/`
* Ambiti o `oAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via.

Ciò vale per l'endpoint di piattaforma di identità Microsoft. Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI. Le app client possono richiedere ancora l'accesso a questi ambiti, tuttavia, è stata modificata la modalità con cui un client esegue la richiesta delle autorizzazioni.

Per l'endpoint v1.0, l'aspetto di una richiesta di autorizzazione OAuth 2.0 per Azure AD è simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Qui il parametro **resource** indicava la risorsa per la quale l'app client richiedeva l'autorizzazione. Azure AD calcolava le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilasciava i token di conseguenza.

Per le applicazioni usano l'endpoint di piattaforma Microsoft identity, la stessa di OAuth 2.0 per autorizzare richiesta simile:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Qui il parametro **scope** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta: è inclusa in ognuno dei valori del parametro di ambito. Usando il parametro di ambito in questo modo consente all'endpoint di piattaforma Microsoft identity sia più conforme alla specifica di OAuth 2.0 e maggiormente in linea con pratiche comuni del settore. Consente inoltre alle app di usare il [consenso incrementale](#incremental-and-dynamic-consent): richiedere le autorizzazioni solo quando l'applicazione ne ha bisogno anziché in anticipo.

## <a name="well-known-scopes"></a>Ambiti conosciuti

### <a name="offline-access"></a>Accesso offline

Le app usando l'endpoint di piattaforma di identità Microsoft possono richiedere l'uso di una nuova autorizzazione nota per le app - il `offline_access` ambito. Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app. L'ambito `offline_access` viene visualizzato all'utente in finestre di dialogo di consenso, come **Accedi ai dati personali in qualsiasi momento**, che l'utente deve accettare. La richiesta di `offline_access` autorizzazioni consentirà all'app web di ricevere token di aggiornamento di OAuth 2.0 dall'endpoint di piattaforma di Microsoft identity. I token di aggiornamento hanno una lunga durata e possono essere scambiati con i nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Ciò significa che, quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione OAuth 2.0, si riceve solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimane valido per un breve periodo di tempo, in genere un'ora, poi scade. A questo punto, l'app deve reindirizzare l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento l'utente può o meno dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su OAuth 2.0 `refresh_tokens`, e `access_tokens`, consultare il [riferimento al protocollo di Microsoft identity platform](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilo e indirizzo di posta elettronica

In passato, il flusso più semplice di OpenID Connect Accedi con la piattaforma Microsoft identity fornisce molte informazioni sull'utente nell'oggetto risultante *id_token*. Le attestazioni nel token ID includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Le informazioni a cui l'app ha accesso tramite l'ambito `openid` sono ora limitate. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente. Per ottenere dati personali sull'utente nell'app, questa deve richiedere autorizzazioni aggiuntive all'utente. Due nuovi ambiti, `email` e `profile`, consentiranno di richiedere autorizzazioni aggiuntive.

* L'ambito `email` consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nell'id_token, ammesso che l'utente abbia un indirizzo e-mail utilizzabile. 
* L'ambito `profile` concede all'app l'accesso a tutte le altre informazioni di base sull'utente, quali nome, nome utente preferito, ID oggetto e così via nell'id_token.

Questi ambiti permettono di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima ed è possibile chiedere all'utente solo il set di informazioni di cui l'app ha bisogno per svolgere le sue funzioni. Per altre informazioni su questi ambiti, vedere [riferimento all'ambito di Microsoft identity platform](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Attestazioni nei token

L'endpoint di piattaforma di identità Microsoft rilascia un set di attestazioni disponibili nei relativi token ridotto per impostazione predefinita per mantenere i payload di piccole dimensioni. Se si dispone di App e servizi che presentano una dipendenza da una determinata attestazione nel token v1.0 che non viene più fornito per impostazione predefinita in un token di piattaforma Microsoft identity, è consigliabile usare la [attestazioni facoltative](active-directory-optional-claims.md) funzionalità per includere tale attestazione.

## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni da tenere presenti quando si usa una piattaforma delle identità Microsoft.

Quando si compilano applicazioni che si integrano con la piattaforma delle identità Microsoft, è necessario decidere se i Microsoft identity platform endpoint e protocolli di autenticazione soddisfano le proprie esigenze. L'endpoint v1.0 e la piattaforma è ancora completamente supportato e, in alcuni aspetti, include più funzionalità rispetto a piattaforma delle identità Microsoft. Tuttavia, piattaforma delle identità di Microsoft [introduce vantaggi significativi](azure-ad-endpoint-comparison.md) per gli sviluppatori.

Di seguito sono riportati alcuni consigli per gli sviluppatori, opportunamente semplificati:

* Se è preferibile o necessario supportare gli account Microsoft personali nell'applicazione o si sta scrivendo una nuova applicazione, usare piattaforma delle identità Microsoft. È necessario tuttavia conoscere i limiti illustrati in questo articolo.
* Se si esegue la migrazione o aggiornamento di un'applicazione basata su SAML, è possibile usare una piattaforma delle identità Microsoft. Al contrario, vedere il [Guida v1.0 di Azure AD](v1-overview.md).

L'endpoint di piattaforma Microsoft identity si evolverà per eliminare le restrizioni elencate in questo caso, in modo che è necessario solo usare l'endpoint di piattaforma di identità Microsoft. Nel frattempo, usare questo articolo per determinare se l'endpoint di Microsoft identity platform è adatta a te. Continueremo a questo articolo in modo da riflettere lo stato corrente dell'endpoint di Microsoft identity platform aggiornato. Controllare tornare a consultarlo regolarmente per i propri requisiti rispetto funzionalità della piattaforma Microsoft identity.

### <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app

Per ogni app che si vuole integrare con l'endpoint di piattaforma Microsoft identity, è possibile creare una registrazione dell'app nel nuovo [ **registrazioni per l'App** esperienza](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) nel portale di Azure. Le App dell'account Microsoft esistenti non sono compatibili con il portale di anteprima, ma sono tutte le app di Azure AD, indipendentemente dalla posizione e il momento sono stati registrati.

Le Registrazioni app che supportano gli account aziendali e dell'istituto di istruzione e gli account personali sono soggette alle condizioni seguenti:

* Per ogni ID applicazione sono consentiti solo due segreti dell'app.
* Un'applicazione che non è stata registrata in un tenant può essere gestita solo dall'account che l'ha registrata. Non può essere condivisa con altri sviluppatori. Questo è il caso della maggior parte delle app che sono state registrate usando un account Microsoft personale nel portale di registrazione delle app. Se si desidera condividere la registrazione dell'app con più sviluppatori, registrare l'applicazione in un tenant usando le nuove **registrazioni per l'App** sezione del portale di Azure.
* Ci sono diverse restrizioni relative al formato dell'URL di reindirizzamento consentito. Per altre informazioni sull'URL di reindirizzamento, vedere la sezione successiva.

### <a name="restrictions-on-redirect-urls"></a>Restrizioni relative agli URL di reindirizzamento

Le app che sono registrate per piattaforma delle identità di Microsoft sono limitate a un set limitato di valori di URL di reindirizzamento. L'URL di reindirizzamento per i servizi e le app Web deve iniziare con lo schema `https` e tutti i valori degli URL di reindirizzamento devono condividere un singolo dominio DNS.  Il sistema di registrazione confronta l'intero nome DNS dell'URL di reindirizzamento esistente con il nome DNS dell'URL di reindirizzamento da aggiungere. `http://localhost` è anche supportato come un URL di reindirizzamento.  

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

Gli ultimi due URL possono essere aggiunti perché si tratta di sottodomini del primo URL di reindirizzamento, ovvero contoso.com. Questa limitazione verrà rimossa in una versione futura.

Si noti inoltre che è possibile avere solo 20 URL di risposta per una determinata applicazione: questo limite si applica a tutti i tipi di app che la registrazione supporta, ovvero applicazione a pagina singola, client nativo, app Web e servizi.  

Per informazioni su come registrare un'app per l'uso con la piattaforma Microsoft identity, vedere [registrare un'app usando la nuova esperienza di registrazioni per l'App](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK

Supporto delle librerie per l'endpoint di piattaforma di identità Microsoft è attualmente limitato. Se si desidera usare l'endpoint di piattaforma Microsoft identity in un'applicazione di produzione, sono disponibili queste opzioni:

* Se si compila un'applicazione Web è possibile usare il middleware lato server disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware di Microsoft, vedere la [piattaforma delle identità Microsoft introduttiva](v2-overview.md#getting-started) sezione.
* Se si compila un'applicazione desktop o portatile, è possibile usare una delle librerie di autenticazione Microsoft (MSAL) di anteprima. Queste librerie sono disponibili in anteprima supportata di produzione ed è quindi consigliabile usarle in applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere le [informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md).
* Per le piattaforme non coperte da librerie Microsoft, è possibile integrare con l'endpoint di Microsoft identity platform direttamente inviando e ricevendo messaggi di protocollo nel codice dell'applicazione. I protocolli OAuth e OpenID Connect [vengono documentati in modo esplicito](active-directory-v2-protocols.md) che consentono di eseguire tale integrazione.
* Infine, è possibile usare librerie di OpenID Connect e OAuth open source per l'integrazione con l'endpoint di piattaforma di identità Microsoft. L'endpoint di Microsoft identity platform deve essere compatibile con molte librerie di protocollo open source senza modifiche. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [librerie di autenticazione e della piattaforma di identità Microsoft](reference-v2-libraries.md)e l'elenco delle librerie client open source e gli esempi che sono stati testati con l'endpoint di piattaforma di identità Microsoft.
* Per riferimento, il `.well-known` endpoint per l'endpoint comune di Microsoft identity platform è `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Sostituire `common` con l'ID tenant per ottenere i dati specifici del tenant.  

### <a name="protocol-changes"></a>Modifiche al protocollo

L'endpoint di piattaforma di identità di Microsoft non supporta SAML o WS-Federation; supporta solo OpenID Connect e OAuth 2.0.  Le modifiche degne di nota ai protocolli OAuth 2.0 dall'endpoint v1.0 sono: 

* Viene restituita l'attestazione `email` se si configura un'attestazione facoltativa **o** è stato specificato scope=email nella richiesta. 
* Ora il parametro `scope` è supportato al posto del parametro `resource`.  
* Molte risposte sono state modificate in modo da renderle più conformi alla specifica OAuth 2.0, ad esempio per ottenere la corretta restituzione di `expires_in` come valore intero anziché come valore stringa.  

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint della piattaforma Microsoft identity, vedere [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrizioni relative a SAML

Se si usa Active Directory Authentication Library (ADAL) in applicazioni Windows, è possibile che si debba ricorrere all'autenticazione integrata di Windows, che sfrutta la concessione per l'asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. L'autorizzazione all'asserzione SAML non è supportata nell'endpoint della piattaforma Microsoft identity.
