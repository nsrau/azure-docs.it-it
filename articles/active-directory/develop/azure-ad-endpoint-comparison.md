---
title: Confronto tra gli endpoint v2.0 e v1.0 di Azure AD | Microsoft Docs
description: Informazioni sulle differenze tra l'endpoint v2.0 e l'endpoint v1.0 di Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin, andret, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, dadobali, negoe
ms.custom: aaddev
ms.openlocfilehash: 6c11062dbae18eed618fe7dde95daa55024d2ddd
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444852"
---
# <a name="comparing-the-azure-ad-v20-endpoint-with-the-v10-endpoint"></a>Confronto tra gli endpoint v2.0 e v1.0 di Azure AD

Quando si sviluppa una nuova applicazione, è importante conoscere le differenze tra gli endpoint Azure Active Directory (Azure AD) v1.0 e v2.0. Questo articolo illustra le differenze principali tra gli endpoint e alcune limitazioni esistenti per l'endpoint v2.0.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure AD sono supportati dall'endpoint v2.0. Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](#limitations).

## <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

![Utenti autorizzati a effettuare l'accesso con gli endpoint v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.png)

* L'endpoint v1.0 consente l'accesso all'applicazione (Azure AD) solo agli account aziendali e dell'istituto di istruzione
* L'endpoint v2.0 consente l'accesso agli account aziendali e dell'istituto di istruzione da Azure AD e agli account Microsoft personali (account del servizio gestito), come hotmail.com, outlook.com e msn.com.
* Entrambi gli endpoint v1.0 e v2.0 accettano anche l'accesso degli *[utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* di una directory di Azure AD per le applicazioni configurate come *[a tenant singolo](single-and-multi-tenant-apps.md)* o per le applicazioni *multi-tenant* configurate per puntare all'endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`).

L'endpoint v2.0 consente di scrivere app che supportano l'accesso dagli account Microsoft personali e dagli account aziendali e dell'istituto di istruzione. Questo consente di scrivere l'app senza tenere conto dell'account utilizzato per l'accesso. Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli account aziendali saranno disponibili funzionalità e dati aggiuntivi, come i siti di SharePoint o i dati delle directory. Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), lo stesso codice può tuttavia accedere al messaggio di posta elettronica sia per gli account personali che per quelli aziendali e dell'istituto di istruzione.

Per l'endpoint v2.0 è possibile usare Microsoft Authentication Library (MSAL) per accedere a scenari consumer, didattici e aziendali. L'endpoint v1.0 di Azure AD accetta gli accessi solo da account aziendali e dell'istituto di istruzione.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

Le app che usano l'endpoint v1.0 di Azure AD devono specificare in anticipo le autorizzazioni OAuth 2.0 richieste, ad esempio:

![Interfaccia utente della registrazione delle autorizzazioni](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Le autorizzazioni impostate direttamente nella registrazione dell'applicazione sono di tipo **statico**. Nonostante la definizione delle autorizzazioni statiche dell'app nel portale di Azure consenta di mantenere il codice chiaro e semplice, questa opzione può presentare problemi per gli sviluppatori:

* Al primo accesso dell'utente l'app deve richiedere tutte le autorizzazioni di cui potrebbe avere bisogno. Questo può richiedere di creare un lungo elenco di autorizzazioni che dovevano essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'accesso da parte di quest'ultimo.

* L'app deve conoscere in anticipo tutte le risorse a cui potrebbe dover accedere. Era difficile creare app che potessero accedere a un numero arbitrario di risorse.

Con l'endpoint v2.0 è possibile ignorare le autorizzazioni statiche definite nelle informazioni di registrazione dell'app nel portale di Azure e richiedere invece le autorizzazioni in modo incrementale, vale a dire richiedere un insieme di autorizzazioni iniziali minimo e aumentarle nel tempo quando il cliente usa funzionalità aggiuntive dell'app. A tale scopo è possibile specificare gli ambiti necessari per l'app in qualsiasi momento, inclusi i nuovi ambiti nel parametro `scope` quando si richiede un token di accesso, senza doverli definire in precedenza nelle informazioni di registrazione dell'applicazione. Se l'utente non ha ancora fornito il consenso per i nuovi ambiti aggiunti alla richiesta, gli verrà chiesto di farlo solo per le nuove autorizzazioni. Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](v2-permissions-and-consent.md).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope`, gli sviluppatori hanno il controllo completo dell'esperienza dell'utente. È anche possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale. Se l'app richiede un numero elevato di autorizzazioni, è possibile raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

Il consenso dell'amministratore fornito per conto di un'organizzazione richiede comunque le autorizzazioni statiche registrate per l'app, perciò si deve impostare tali autorizzazioni per le app nel portale di registrazione dell'app se è necessario che un amministratore fornisca il consenso per conto dell'intera organizzazione. Questo riduce i cicli richiesti dall'amministratore dell'organizzazione per configurare l'applicazione.

## <a name="scopes-not-resources"></a>Ambiti e non risorse

Per le app che usano l'endpoint v1.0, un'app può comportarsi come una **risorsa** o come un destinatario di token. Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token dalla risorsa per un determinato set di ambiti. Si consideri l'API Graph di Azure AD come esempio di una risorsa:

* Identificatore della risorsa o `AppID URI`: `https://graph.windows.net/`
* Ambiti o `oAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via.

Quanto descritto si applica all'endpoint v2.0. Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI. Le app client possono richiedere ancora l'accesso a questi ambiti, tuttavia, è stata modificata la modalità con cui un client esegue la richiesta delle autorizzazioni. 

Per l'endpoint v1.0, l'aspetto di una richiesta di autorizzazione OAuth 2.0 per Azure AD è simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Qui il parametro **resource** indicava la risorsa per la quale l'app client richiedeva l'autorizzazione. Azure AD calcolava le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilasciava i token di conseguenza. 

Per le applicazioni che usano l'endpoint v2.0, la stessa richiesta di autorizzazione OAuth 2.0 ha un aspetto simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Qui il parametro **scope** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta: è inclusa in ognuno dei valori del parametro di ambito. Tale uso del parametro di ambito consente all'endpoint 2.0 di essere più conforme alla specifica OAuth 2.0 e alle pratiche comuni del settore. Consente inoltre alle app di usare il [consenso incrementale](#incremental-and-dynamic-consent): richiedere le autorizzazioni solo quando l'applicazione ne ha bisogno anziché in anticipo.

## <a name="well-known-scopes"></a>Ambiti conosciuti

### <a name="offline-access"></a>Accesso offline

Per le app che usano l'endpoint 2.0 è possibile che sia necessaria una nuova autorizzazione nota per le app: l'ambito `offline_access`. Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app. L'ambito `offline_access` viene visualizzato all'utente in finestre di dialogo di consenso, come **Accedi ai dati personali in qualsiasi momento**, che l'utente deve accettare. La richiesta dell'autorizzazione `offline_access` consentirà all'app Web di ricevere token di aggiornamento di OAuth 2.0 dall'endpoint 2.0. I token di aggiornamento hanno una lunga durata e possono essere scambiati con i nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento. Ciò significa che, quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione OAuth 2.0, si riceve solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimane valido per un breve periodo di tempo, in genere un'ora, poi scade. A questo punto, l'app deve reindirizzare l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento l'utente può o meno dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su OAuth 2.0, `refresh_tokens` e `access_tokens`, vedere le [informazioni di riferimento sui protocolli v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilo e indirizzo di posta elettronica

Tradizionalmente, il flusso di accesso più semplice di OpenID Connect in Azure AD fornisce molte informazioni sull'utente nell'oggetto *id_token* risultante. Le attestazioni nel token ID includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Le informazioni a cui l'app ha accesso tramite l'ambito `openid` sono ora limitate. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente. Per ottenere dati personali sull'utente nell'app, questa deve richiedere autorizzazioni aggiuntive all'utente. Due nuovi ambiti, `email` e `profile`, consentiranno di richiedere autorizzazioni aggiuntive.

* L'ambito `email` consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nell'id_token, ammesso che l'utente abbia un indirizzo e-mail utilizzabile. 
* L'ambito `profile` concede all'app l'accesso a tutte le altre informazioni di base sull'utente, quali nome, nome utente preferito, ID oggetto e così via nell'id_token.

Questi ambiti permettono di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima ed è possibile chiedere all'utente solo il set di informazioni di cui l'app ha bisogno per svolgere le sue funzioni. Per altre informazioni su questi ambiti, vedere le [informazioni di riferimento sugli ambiti della versione 2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Attestazioni nei token

L'endpoint v2.0 genera un set di attestazioni minore nei suoi token per impostazione predefinita in modo da mantenere ridotti i payload. Se si dispone di app e servizi che presentano una dipendenza da una determinata attestazione in un token v1.0 che non è più fornita per impostazione predefinita in un token v2.0, è consigliabile usare la funzionalità [attestazioni facoltative](active-directory-optional-claims.md) per includere tale attestazione.

## <a name="limitations"></a>Limitazioni

Ci sono alcune limitazioni da tenere in considerazione quando si usa la versione 2.0.

Quando si creano applicazioni che si integrano con Microsoft Identity Platform, è necessario stabilire se i protocolli di autenticazione e l'endpoint v2.0 soddisfano le proprie esigenze. La piattaforma e l'endpoint v1.0 sono ancora supportati e, per alcuni aspetti, offrono più funzionalità della versione 2.0. Nella versione 2.0, tuttavia, vengono [introdotti vantaggi significativi](azure-ad-endpoint-comparison.md) per gli sviluppatori.

Di seguito sono riportati alcuni consigli per gli sviluppatori, opportunamente semplificati:

* Se si vuole o si deve supportare gli account Microsoft personali nell'applicazione o si scrive una nuova applicazione, usare la v2.0. È necessario tuttavia conoscere i limiti illustrati in questo articolo.
* Se si esegue la migrazione o l'aggiornamento di un'applicazione basata su SAML, non è possibile usare la v2.0. Vedere invece la [guida alla versione 1.0](v1-overview.md).

L'endpoint v2.0 verrà migliorato per eliminare le restrizioni elencate di seguito, in modo da consentire l'uso esclusivo dell'endpoint v2.0. Nel frattempo, usare questo articolo per determinare se l'endpoint 2.0 è adatto alle proprie esigenze. L'articolo sarà aggiornato periodicamente per riflettere lo stato corrente dell'endpoint v2.0. È consigliabile tornare a consultarlo regolarmente per valutare i propri requisiti rispetto alle funzionalità della versione 2.0.

### <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app

Per ogni app da integrare con l'endpoint v2.0 è possibile creare una registrazione di app nel [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). In alternativa si può registrare un'app usando l'[esperienza **Registrazioni app (anteprima)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) nel portale di Azure. Le app dell'account Microsoft esistenti non sono compatibili con il portale di anteprima, mentre lo sono tutte le app AAD indipendentemente da dove e quando sono state registrate. 

Le Registrazioni app che supportano gli account aziendali e dell'istituto di istruzione e gli account personali sono soggette alle condizioni seguenti:

* Per ogni ID applicazione sono consentiti solo due segreti dell'app.
* Un'applicazione che non è stata registrata in un tenant può essere gestita solo dall'account che l'ha registrata. Non può essere condivisa con altri sviluppatori. Questo è il caso della maggior parte delle app che sono state registrate usando un account Microsoft personale nel portale di registrazione delle app. Se si vuole condividere la registrazione dell'app con più sviluppatori, registrare l'applicazione in un tenant nella sezione **Registrazioni app (anteprima)** del portale di Azure.
* Ci sono diverse restrizioni relative al formato dell'URL di reindirizzamento consentito. Per altre informazioni sull'URL di reindirizzamento, vedere la sezione successiva.

### <a name="restrictions-on-redirect-urls"></a>Restrizioni relative agli URL di reindirizzamento

Le app registrate per la v2.0 possono usare un set limitato di valori di URL di reindirizzamento. L'URL di reindirizzamento per i servizi e le app Web deve iniziare con lo schema `https` e tutti i valori degli URL di reindirizzamento devono condividere un singolo dominio DNS.  Il sistema di registrazione confronta l'intero nome DNS dell'URL di reindirizzamento esistente con il nome DNS dell'URL di reindirizzamento da aggiungere. Come URL di reindirizzamento è supportato anche `http://localhost`.  

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

Per informazioni su come registrare un'app per l'uso con la v2.0, vedere queste guide introduttive:

* [Registrare un'app mediante il portale di registrazione delle applicazioni](quickstart-v2-register-an-app.md)
* [Registrare un'app mediante l'esperienza Registrazioni app (anteprima)](quickstart-register-app.md)

### <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK

Il supporto delle librerie per l'endpoint 2.0 è attualmente limitato. Per usare l'endpoint 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

* Se si compila un'applicazione Web è possibile usare il middleware lato server disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware Microsoft, vedere la sezione di [introduzione alla versione 2.0](v2-overview.md#getting-started).
* Se si compila un'applicazione desktop o portatile, è possibile usare una delle librerie di autenticazione Microsoft (MSAL) di anteprima. Queste librerie sono disponibili in anteprima supportata di produzione ed è quindi consigliabile usarle in applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere le [informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md).
* In caso di piattaforme non coperte da librerie Microsoft, è possibile integrarle con l'endpoint 2.0 tramite l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth della versione 2.0 [sono documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.
* Per realizzare l'integrazione con l'endpoint v2.0, è anche possibile usare librerie OpenID Connect e OAuth di tipo open source. Il protocollo della v2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [Azure Active Directory 2.0 e librerie di autenticazione](reference-v2-libraries.md), l'elenco di librerie client open source e gli esempi testati con l'endpoint 2.0.
* Per riferimento, l'endpoint `.well-known` per l'endpoint comune v2.0 è `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Sostituire `common` con l'ID tenant per ottenere i dati specifici del tenant.  

### <a name="protocol-changes"></a>Modifiche al protocollo

L'endpoint v2.0 non supporta protocolli SAML o WS-Federation, ma solo Open ID Connect e OAuth 2.0.  Le modifiche degne di nota ai protocolli OAuth 2.0 dall'endpoint v1.0 sono: 

* Viene restituita l'attestazione `email` se si configura un'attestazione facoltativa **o** è stato specificato scope=email nella richiesta. 
* Ora il parametro `scope` è supportato al posto del parametro `resource`.  
* Molte risposte sono state modificate in modo da renderle più conformi alla specifica OAuth 2.0, ad esempio per ottenere la corretta restituzione di `expires_in` come valore intero anziché come valore stringa.  

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint v2.0, vedere l'articolo [Riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>Restrizioni relative a SAML

Se si usa Active Directory Authentication Library (ADAL) in applicazioni Windows, è possibile che si debba ricorrere all'autenticazione integrata di Windows, che sfrutta la concessione per l'asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione per l'asserzione SAML non è supportata dall'endpoint v2.0.
