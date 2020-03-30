---
title: Perché eseguire l'aggiornamento a Microsoft Identity Platform (v2.0) Azure
description: Conoscere le differenze tra l'endpoint Microsoft Identity Platform (v2.0) e l'endpoint Azure Active Directory (Azure AD) v1.0 e scoprire i vantaggi dell'aggiornamento alla versione 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154917"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Perché eseguire l'aggiornamento a Microsoft Identity Platform (v2.0)?

Quando si sviluppa una nuova applicazione, è importante conoscere le differenze tra gli endpoint della piattaforma di identità Microsoft (v2.0) e gli endpoint di Azure Active Directory (v1.0). In questo articolo vengono illustrate le principali differenze tra gli endpoint e alcune limitazioni esistenti per la piattaforma di identità Microsoft.This article covers the main differences between the endpoints and some existing limitations for Microsoft identity platform.

> [!NOTE]
> L'endpoint della piattaforma di identità Microsoft non supporta tutti gli scenari e le funzionalità di Azure AD. Per determinare se è necessario utilizzare l'endpoint della piattaforma di identità Microsoft, leggere le informazioni sulle [limitazioni della piattaforma](#limitations)di identità Microsoft .

## <a name="who-can-sign-in"></a>Utenti autorizzati a effettuare l'accesso

![Utenti autorizzati a effettuare l'accesso con gli endpoint v1.0 e v2.0](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* L'endpoint v1.0 consente l'accesso all'applicazione (Azure AD) solo agli account aziendali e dell'istituto di istruzione
* L'endpoint della piattaforma di identità Microsoft consente l'accesso agli account aziendali e dell'istituto di istruzione di Azure AD e agli account Microsoft personali, ad esempio hotmail.com, outlook.com e msn.com.
* Entrambi gli endpoint accettano anche gli accessi degli *[utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* di una directory di Azure AD per le applicazioni configurate come *[single-tenant](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* o per le applicazioni *multi-tenant* configurate in modo che puntino all'endpoint specifico del tenant (`https://login.microsoftonline.com/{TenantId_or_Name}`).

L'endpoint della piattaforma di identità Microsoft consente di scrivere app che accettano gli egli da account Microsoft personali e da account aziendali e dell'istituto di istruzione. Questo consente di scrivere l'app senza tenere conto dell'account utilizzato per l'accesso. Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli account aziendali saranno disponibili funzionalità e dati aggiuntivi, come i siti di SharePoint o i dati delle directory. Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0), lo stesso codice può tuttavia accedere al messaggio di posta elettronica sia per gli account personali che per quelli aziendali e dell'istituto di istruzione.

Per l'endpoint della piattaforma di identità Microsoft, è possibile utilizzare Microsoft Authentication Library (MSAL) per accedere ai mondi consumer, didattico e aziendale. L'endpoint v1.0 di Azure AD accetta gli accessi solo da account aziendali e dell'istituto di istruzione.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico

Le app che usano l'endpoint v1.0 di Azure AD devono specificare in anticipo le autorizzazioni OAuth 2.0 richieste, ad esempio:

![Esempio che mostra l'interfaccia utente di registrazione delle autorizzazioniExample showing the Permissions Registration UI](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

Le autorizzazioni impostate direttamente nella registrazione dell'applicazione sono di tipo **statico**. Nonostante la definizione delle autorizzazioni statiche dell'app nel portale di Azure consenta di mantenere il codice chiaro e semplice, questa opzione può presentare problemi per gli sviluppatori:

* Al primo accesso dell'utente l'app deve richiedere tutte le autorizzazioni di cui potrebbe avere bisogno. Questo può richiedere di creare un lungo elenco di autorizzazioni che dovevano essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'accesso da parte di quest'ultimo.

* L'app deve conoscere in anticipo tutte le risorse a cui potrebbe dover accedere. Era difficile creare app che potessero accedere a un numero arbitrario di risorse.

Con l'endpoint della piattaforma di identità Microsoft, è possibile ignorare le autorizzazioni statiche definite nelle informazioni di registrazione dell'app nel portale di Azure e richiedere le autorizzazioni in modo incrementale, ovvero richiedere un set minimo di autorizzazioni iniziale e crescere di più nel tempo come il cliente utilizza funzionalità aggiuntive dell'app. A tale scopo è possibile specificare gli ambiti necessari per l'app in qualsiasi momento, inclusi i nuovi ambiti nel parametro `scope` quando si richiede un token di accesso, senza doverli definire in precedenza nelle informazioni di registrazione dell'applicazione. Se l'utente non ha ancora fornito il consenso per i nuovi ambiti aggiunti alla richiesta, gli verrà chiesto di farlo solo per le nuove autorizzazioni. Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope`, gli sviluppatori hanno il controllo completo dell'esperienza dell'utente. È anche possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale. Se l'app richiede un numero elevato di autorizzazioni, è possibile raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

Il consenso dell'amministratore fornito per conto di un'organizzazione richiede comunque le autorizzazioni statiche registrate per l'app, perciò si deve impostare tali autorizzazioni per le app nel portale di registrazione dell'app se è necessario che un amministratore fornisca il consenso per conto dell'intera organizzazione. Questo riduce i cicli richiesti dall'amministratore dell'organizzazione per configurare l'applicazione.

## <a name="scopes-not-resources"></a>Ambiti e non risorse

Per le app che usano l'endpoint v1.0, un'app può comportarsi come una **risorsa** o come un destinatario di token. Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token dalla risorsa per un determinato set di ambiti. Si consideri l'API Microsoft Graph come esempio di una risorsa:Consider the Microsoft Graph API as an example of a resource:

* Identificatore della risorsa o `AppID URI`: `https://graph.microsoft.com/`
* Ambiti o `oAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via.

Ciò vale per l'endpoint della piattaforma di identità Microsoft.This hold true for the Microsoft identity platform endpoint. Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI. Le app client possono richiedere ancora l'accesso a questi ambiti, Tuttavia, il modo in cui un client richiede tali autorizzazioni è stato modificato.

Per l'endpoint v1.0, l'aspetto di una richiesta di autorizzazione OAuth 2.0 per Azure AD è simile al seguente:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Qui il parametro **resource** indicava la risorsa per la quale l'app client richiedeva l'autorizzazione. Azure AD calcolava le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilasciava i token di conseguenza.

Per le applicazioni che usano l'endpoint della piattaforma di identità Microsoft, la stessa richiesta di autorizzazione OAuth 2.0 è simile alla:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Qui il parametro **scope** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta: è inclusa in ognuno dei valori del parametro di ambito. L'utilizzo del parametro scope in questo modo consente all'endpoint della piattaforma di identità Microsoft di essere più conforme alla specifica OAuth 2.0 e di allinearsi più strettamente alle procedure comuni del settore. Consente inoltre alle app di eseguire il [consenso incrementale,](#incremental-and-dynamic-consent) richiedendo solo le autorizzazioni quando l'applicazione le richiede anziché in anticipo.

## <a name="well-known-scopes"></a>Ambiti conosciuti

### <a name="offline-access"></a>Accesso offline

Le app che usano l'endpoint della piattaforma di identità Microsoft `offline_access` possono richiedere l'uso di una nuova autorizzazione nota per le app, ovvero l'ambito. Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app. L'ambito `offline_access` viene visualizzato all'utente in finestre di dialogo di consenso, come **Accedi ai dati personali in qualsiasi momento**, che l'utente deve accettare. La richiesta `offline_access` dell'autorizzazione consentirà all'app Web di ricevere il refresh_tokens OAuth 2.0 dall'endpoint della piattaforma di identità Microsoft.Requesting the permission will enable your web app to receive OAuth 2.0 refresh_tokens from the Microsoft identity platform endpoint. I token di aggiornamento hanno una lunga durata e possono essere scambiati con i nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.

Se l'app non `offline_access` richiede l'ambito, non riceverà token di aggiornamento. Ciò significa che, quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione OAuth 2.0, si riceve solo un token di accesso dall'endpoint `/token`. Tale token di accesso rimane valido per un breve periodo di tempo, in genere un'ora, poi scade. A questo punto, l'app deve reindirizzare l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento l'utente può o meno dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per ulteriori informazioni su OAuth `refresh_tokens`2.0, , e `access_tokens`, vedere informazioni su Microsoft Identity Platform Protocol [reference](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profilo e indirizzo di posta elettronica

Storicamente, il flusso di accesso OpenID Connect più semplice con la piattaforma di identità Microsoft fornirebbe molte informazioni sull'utente nel *id_token*risultante. Le attestazioni nel token ID includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Le informazioni a cui l'app ha accesso tramite l'ambito `openid` sono ora limitate. L'ambito `openid` consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente. Per ottenere dati personali sull'utente nell'app, questa deve richiedere autorizzazioni aggiuntive all'utente. Due nuovi ambiti, `email` e `profile`, consentiranno di richiedere autorizzazioni aggiuntive.

* L'ambito `email` consente all'app di accedere all'indirizzo di posta elettronica principale dell'utente tramite l'attestazione `email` nel id_token, presupponendo che l'utente disponga di un indirizzo di posta elettronica indirizzabile.
* L'ambito `profile` consente all'app di accedere a tutte le altre informazioni di base sull'utente, ad esempio il nome, il nome utente preferito, l'ID oggetto e così via, nel id_token.

Questi ambiti permettono di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima ed è possibile chiedere all'utente solo il set di informazioni di cui l'app ha bisogno per svolgere le sue funzioni. Per ulteriori informazioni su questi ambiti, vedere le informazioni di [riferimento sull'ambito della piattaforma](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)di identità Microsoft .

## <a name="token-claims"></a>Attestazioni nei token

L'endpoint della piattaforma di identità Microsoft rilascia un set di attestazioni più piccolo nei token per impostazione predefinita per mantenere piccoli i payload. Se si dispone di app e servizi che hanno una dipendenza da una determinata attestazione in un token v1.0 che non è più fornito per impostazione predefinita in un token della piattaforma di identità Microsoft, è consigliabile usare la funzionalità [di attestazioni facoltative](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) per includere tale attestazione.

> [!IMPORTANT]
> I token v1.0 e v2.0 possono essere emessi da entrambi gli endpoint v1.0 e v2.0! id_tokens *sempre* corrispondono all'endpoint da cui vengono richiesti e i token di accesso corrispondono *sempre* al formato previsto dall'API Web che il client chiamerà utilizzando tale token.  Pertanto, se l'app usa l'endpoint v2.0 per ottenere un token per chiamare Microsoft Graph, che prevede token di accesso in formato v1.0, riceverà un token nel formato v1.0.  

## <a name="limitations"></a>Limitazioni

Esistono alcune restrizioni da tenere presenti quando si utilizza la piattaforma di identità Microsoft.There are a few restrictions to be aware of when using Microsoft identity platform.

Quando si creano applicazioni che si integrano con la piattaforma di identità Microsoft, è necessario decidere se l'endpoint della piattaforma di identità Microsoft e i protocolli di autenticazione soddisfano le proprie esigenze. L'endpoint e la piattaforma v1.0 sono ancora completamente supportati e, per certi aspetti, sono più ricchi di funzionalità rispetto alla piattaforma di identità Microsoft.The v1.0 endpoint and platform is still fully supported and, in some respect, is more feature rich than Microsoft identity platform. Tuttavia, la piattaforma di identità Microsoft [introduce vantaggi significativi](azure-ad-endpoint-comparison.md) per gli sviluppatori.

Ecco una raccomandazione semplificata per gli sviluppatori ora:

* Se si desidera o è necessario supportare gli account Microsoft personali nell'applicazione o si sta scrivendo una nuova applicazione, utilizzare la piattaforma di identità Microsoft. È necessario tuttavia conoscere i limiti illustrati in questo articolo.
* Se stai eseguendo la migrazione o l'aggiornamento di un'applicazione che si basa su SAML, non puoi utilizzare la piattaforma di identità Microsoft. Fare invece riferimento alla guida di [Azure AD v1.0](v1-overview.md).

L'endpoint della piattaforma di identità Microsoft si evolverà per eliminare le restrizioni elencate di seguito, in modo che sia necessario utilizzare solo l'endpoint della piattaforma di identità Microsoft.The Microsoft identity platform endpoint will evolve to eliminate the restrictions listed here, so that you'll only need to use the Microsoft identity platform endpoint. Nel frattempo, usare questo articolo per determinare se l'endpoint della piattaforma di identità Microsoft è adatto alle persone. Continueremo ad aggiornare questo articolo per riflettere lo stato corrente dell'endpoint della piattaforma di identità Microsoft. Ricontrolla per rivalutare i tuoi requisiti rispetto alle funzionalità della piattaforma di identità Microsoft.

### <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app

Per ogni app che vuoi integrare con l'endpoint della piattaforma di identità Microsoft, puoi creare una registrazione dell'app nella nuova esperienza di registrazione delle app nel portale di Azure.For each app that you want to integrate with the Microsoft identity platform endpoint, you can create an app registration in the new [ **App registrations** experience](https://aka.ms/appregistrations) in the Azure portal. Le app dell'account Microsoft esistenti non sono compatibili con il portale, ma tutte le app di Azure AD lo sono, indipendentemente da dove o quando sono state registrate.

Le Registrazioni app che supportano gli account aziendali e dell'istituto di istruzione e gli account personali sono soggette alle condizioni seguenti:

* Per ogni ID applicazione sono consentiti solo due segreti dell'app.
* Un'applicazione che non è stata registrata in un tenant può essere gestita solo dall'account che l'ha registrata. Non può essere condiviso con altri sviluppatori. Questo è il caso della maggior parte delle app che sono state registrate usando un account Microsoft personale nel portale di registrazione delle app. Se si vuole condividere la registrazione dell'app con più sviluppatori, registrare l'applicazione in un tenant usando la nuova sezione Registrazioni app del portale di Azure.If you'd you to share your app registration with multiple developers, register the application in a tenant using the new **App registrations** section of the Azure portal.
* Ci sono diverse restrizioni relative al formato dell'URL di reindirizzamento consentito. Per altre informazioni sull'URL di reindirizzamento, vedere la sezione successiva.

### <a name="restrictions-on-redirect-urls"></a>Restrizioni relative agli URL di reindirizzamento

Le app registrate per la piattaforma di identità Microsoft sono limitate a un set limitato di valori dell'URL di reindirizzamento. L'URL di reindirizzamento per i servizi e le app Web deve iniziare con lo schema `https` e tutti i valori degli URL di reindirizzamento devono condividere un singolo dominio DNS.  Il sistema di registrazione confronta l'intero nome DNS dell'URL di reindirizzamento esistente con il nome DNS dell'URL di reindirizzamento da aggiungere. Come URL di reindirizzamento è supportato anche `http://localhost`.  

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

È possibile aggiungere gli ultimi due perché sono sottodomini del primo URL di reindirizzamento, contoso.com.

Puoi avere solo 20 URL di risposta per una determinata applicazione: questo limite si applica a tutti i tipi di app supportati dalla registrazione (applicazione a pagina singola ( SPA), client nativo, app Web e servizio).  

Per informazioni su come registrare un'app per l'utilizzo con la piattaforma di identità Microsoft, vedere [Registrare un'app usando la nuova esperienza](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)di registrazione delle app.

### <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK

Attualmente, il supporto della libreria per l'endpoint della piattaforma di identità Microsoft è limitato. Se si desidera utilizzare l'endpoint della piattaforma di identità Microsoft in un'applicazione di produzione, sono disponibili le opzioni seguenti:If you want to use the Microsoft identity platform endpoint in a production application, you have these options:

* Se si compila un'applicazione Web, è possibile utilizzare in modo sicuro il middleware sul lato server in genere disponibile per eseguire l'accesso e la convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware Microsoft, vedere la sezione introduttiva della piattaforma di [identità Microsoft.For](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) code samples that use Microsoft middleware, see the Microsoft identity platform getting started section.
* Se si sta creando un'applicazione desktop o mobile, è possibile utilizzare una delle librerie di autenticazione Microsoft (MSAL). Queste librerie sono generalmente disponibili o in un'anteprima supportata dalla produzione, pertanto è sicuro usarle nelle applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere le [informazioni di riferimento sulle librerie di autenticazione](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
* Per le piattaforme non coperte dalle librerie Microsoft, è possibile integrarsi con l'endpoint della piattaforma di identità Microsoft inviando e ricevendo direttamente messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth [sono documentati in modo esplicito](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) per facilitare l'integrazione di questo tipo.
* Infine, è possibile utilizzare le librerie OpenID Connect e OAuth open-source per l'integrazione con l'endpoint della piattaforma di identità Microsoft.Finally, you can use open-source OpenID Connect and OAuth libraries to integrate with the Microsoft identity platform endpoint. L'endpoint della piattaforma di identità Microsoft deve essere compatibile con molte librerie di protocolli open source senza modifiche. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](https://openid.net/connect/) e [OAuth 2.0](https://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per ulteriori informazioni, vedere [Microsoft identity platform e le librerie](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)di autenticazione e l'elenco delle librerie client open source e gli esempi che sono stati testati con l'endpoint della piattaforma di identità Microsoft.
* Per riferimento, `.well-known` l'endpoint per l'endpoint comune della piattaforma di identità Microsoft è `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Sostituire `common` con l'ID tenant per ottenere i dati specifici del tenant.  

### <a name="protocol-changes"></a>Modifiche al protocollo

L'endpoint della piattaforma di identità Microsoft non supporta SAML o WS-Federation; supporta solo OpenID Connect e OAuth 2.0.  Le modifiche degne di nota ai protocolli OAuth 2.0 dall'endpoint v1.0 sono: 

* Viene restituita l'attestazione `email` se si configura un'attestazione facoltativa **o** è stato specificato scope=email nella richiesta. 
* Ora il parametro `scope` è supportato al posto del parametro `resource`.  
* Molte risposte sono state modificate in modo da renderle più conformi alla specifica OAuth 2.0, ad esempio per ottenere la corretta restituzione di `expires_in` come valore intero anziché come valore stringa.  

Per comprendere meglio l'ambito delle funzionalità del protocollo supportate nell'endpoint della piattaforma di identità Microsoft, vedere Informazioni di riferimento sui [protocolli OpenID Connect e OAuth 2.0.](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

#### <a name="saml-restrictions"></a>Restrizioni relative a SAML

Se hai utilizzato Active Directory Authentication Library (ADAL) nelle applicazioni Windows, potresti aver sfruttato l'autenticazione integrata di Windows, che utilizza la concessione di asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione di asserzione SAML non è supportata nell'endpoint della piattaforma di identità Microsoft.The SAML assertion grant isn't supported on the Microsoft identity platform endpoint.
