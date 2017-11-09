---
title: Limiti e restrizioni dell'endpoint 2.0 di Azure Active Directory | Documentazione Microsoft
description: Elenco di limiti e restrizioni per l'endpoint 2.0 di Azure AD.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5a9d455203e50da47208ef1494d38a950161bee1
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2017
---
# <a name="should-i-use-the-v20-endpoint"></a>Perché usare l'endpoint 2.0
Nella compilazione di applicazioni che si integrano con Azure Active Directory è necessario stabilire se l'endpoint 2.0 e i protocolli di autenticazione rispondono ai requisiti previsti. L'endpoint originale di Azure Active Directory è ancora completamente supportato e, per alcuni aspetti, include più funzionalità della versione 2.0. L'endpoint 2.0 [introduce vantaggi significativi](active-directory-v2-compare.md) per gli sviluppatori.

Di seguito sono riportati alcuni consigli per gli sviluppatori, opportunamente semplificati:

* Se nell'applicazione devono essere supportati account Microsoft personali, usare l'endpoint 2.0. È necessario tuttavia comprendere prima i limiti illustrati in questo articolo.
* Se l'applicazione deve supportare solo account Microsoft aziendali o dell'istituto di istruzione, non usare l'endpoint 2.0. In questo caso, fare riferimento alla [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

Nel corso del tempo, l'endpoint 2.0 verrà migliorato per eliminare le restrizioni elencate di seguito, in modo da consentire l'uso esclusivo dell'endpoint 2.0. Nel frattempo, questo articolo consente di determinare se l'endpoint 2.0 è adatto alle proprie esigenze. L'articolo verrà aggiornato periodicamente per riflettere lo stato corrente dell'endpoint 2.0. È consigliabile tornare a consultarlo regolarmente per valutare i propri requisiti rispetto alle funzionalità della versione 2.0.

Se è disponibile un'app Azure AD che non usa l'endpoint 2.0, non è necessario iniziare da zero. In futuro sarà possibile usare le applicazioni di Azure AD esistenti con l'endpoint 2.0.

## <a name="restrictions-on-app-types"></a>Restrizioni relative ai tipi di app
I tipi di app seguenti non sono attualmente supportati dall'endpoint 2.0. Per una descrizione dei tipi di app supportati, vedere [Tipi di app per l'endpoint 2.0 di Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API Web autonome
È possibile usare l'endpoint 2.0 per [compilare un'API Web protetta con OAuth 2.0](active-directory-v2-flows.md#web-apis). L'API Web, tuttavia, può ricevere i token solo da un'applicazione che condivide lo stesso ID applicazione. Non è possibile accedere a un'API Web da un client con un ID applicazione diverso perché il client non sarà in grado di richiedere o ottenere autorizzazioni per l'API Web.

Per informazioni su come compilare un'API Web che accetti token da un client con lo stesso ID applicazione, vedere gli esempi di API Web dell'endpoint 2.0 nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app
Per ogni app da integrare con l'endpoint 2.0, è necessario attualmente creare una registrazione di app nel nuovo [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Le app Azure AD o degli account Microsoft esistenti non sono compatibili con l'endpoint 2.0 né lo sono quelle registrate in qualsiasi portale diverso da quello di registrazione delle applicazioni. In futuro si prevede di offrire un modo per usare un'applicazione esistente come app 2.0, ma attualmente non esiste alcun percorso di migrazione che consenta a un'app esistente di funzionare con l'endpoint 2.0.

Per le registrazioni di app create nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), è necessario anche tenere in considerazione gli aspetti seguenti:

* Sono consentiti solo due segreti dell'app per ogni ID applicazione.
* La registrazione di un'app eseguita da un utente con un account Microsoft personale può essere visualizzata e gestita solo da un singolo account sviluppatore. Non può essere condiviso tra più sviluppatori.  Se si vuole condividere la registrazione dell'app tra più sviluppatori, è possibile creare l'applicazione accedendo al portale di registrazione con un account di Azure AD.
* Sono presenti diverse restrizioni relative al formato dell'URI di reindirizzamento consentito. Per altre informazioni sugli URI di reindirizzamento, vedere la sezione successiva.

## <a name="restrictions-on-redirect-uris"></a>Restrizioni relative agli URI di reindirizzamento
Le app registrate nel portale di registrazione delle applicazioni sono attualmente limitate a un set ristretto di valori di URI di reindirizzamento. L'URI di reindirizzamento per le app e i servizi Web deve iniziare con lo schema `https` e tutti i valori degli URI di reindirizzamento devono condividere un singolo dominio DNS. Non è possibile ad esempio registrare un'app Web con uno di questi URI di reindirizzamento:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Il sistema di registrazione confronta l'intero nome DNS dell'URI di reindirizzamento esistente con il nome DNS dell'URI di reindirizzamento da aggiungere. La richiesta di aggiungere il nome DNS non viene soddisfatta se si verificano le condizioni seguenti:  

* Il nome DNS intero del nuovo URI di reindirizzamento non corrisponde al nome DNS dell'URI di reindirizzamento esistente.
* Il nome DNS intero del nuovo URI di reindirizzamento non è un sottodominio dell'URI di reindirizzamento esistente.

Se ad esempio all'app è associato l'URI di reindirizzamento seguente:

`https://login.contoso.com`

è possibile eseguire un'aggiunta analoga alla seguente:

`https://login.contoso.com/new`

In questo caso, il nome DNS corrisponde esattamente. In alternativa, è possibile eseguire un'aggiunta analoga alla seguente:

`https://new.login.contoso.com`

In questo caso, si fa riferimento a un sottodominio DNS di login.contoso.com. Se si desidera un'app con URI di reindirizzamento login-east.contoso.com e login-west.contoso.com, è necessario aggiungere tali URI nell'ordine seguente:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Gli ultimi due URI possono essere aggiunti perché si tratta di sottodomini del primo URI di reindirizzamento, ovvero contoso.com. Questa limitazione verrà rimossa in una versione futura.

Si noti inoltre che è possibile avere solo 20 URL di risposta per una determinata applicazione.

Per informazioni su come registrare un'app nel portale di registrazione delle applicazioni, vedere [Come registrare un'app con l'endpoint 2.0](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services-and-apis"></a>Restrizioni relative a servizi e API
L'endpoint 2.0 supporta attualmente l'accesso di qualsiasi app registrata nel portale di registrazione delle applicazioni e presente nell'elenco di [flussi di autenticazione supportati](active-directory-v2-flows.md). Tali app possono tuttavia acquisire il token di accesso di OAuth 2.0 per un set molto limitato di risorse. L'endpoint 2.0 rilascia token di accesso solo per gli elementi seguenti:

* L'app che ha richiesto il token. Un'app può acquisire un token di accesso per se stessa, se l'app per la logica è costituita da diversi componenti o livelli. Per vedere questo scenario, consultare le esercitazioni nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started) .
* Le API REST di Posta, Calendario e Contatti di Outlook che si trovano in https://outlook.office.com. Per informazioni su come scrivere un'app in grado di accedere a queste API, vedere le esercitazioni nella [sezione introduttiva di Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
* API Microsoft Graph. È possibile accedere ad altre informazioni su [Microsoft Graph](https://graph.microsoft.io) e sui dati disponibili per l'utente.

Attualmente non sono supportati altri servizi. In futuro verranno aggiunti altri componenti di Microsoft Online Services, oltre al supporto per le API e i servizi Web personalizzati.

## <a name="restrictions-on-libraries-and-sdks"></a>Restrizioni relative alle librerie e agli SDK
Il supporto delle librerie per l'endpoint 2.0 è attualmente limitato. Per usare l'endpoint 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

* Se si compila un'applicazione Web, è possibile usare il middleware lato server di Microsoft disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. Sono inclusi il middleware OpenID Connect OWIN per ASP.NET e il plug-in Passport per NodeJS. Per esempi di codice che usano il middleware di Microsoft, vedere la sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started).
* Se si compila un'applicazione desktop o portatile, è possibile usare una delle Microsoft Authentication Library (MSAL) di anteprima.  Queste librerie sono disponibili in anteprima supportata di produzione ed è quindi consigliabile usarle in applicazioni di produzione. Per altre informazioni sui termini e condizioni dell'anteprima e sulle librerie disponibili, vedere l'articolo di [riferimento sulle librerie di autenticazione](active-directory-v2-libraries.md).
* In caso di piattaforme non coperte da librerie Microsoft, è possibile integrarle con l'endpoint 2.0 tramite l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth della versione 2.0 [sono documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.
* Per realizzare l'integrazione con l'endpoint 2.0, è anche possibile usare librerie OpenID Connect e OAuth di tipo open source. Il protocollo della versione 2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche rilevanti. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [Azure Active Directory 2.0 e librerie di autenticazione](active-directory-v2-libraries.md), l'elenco di librerie client open source e gli esempi testati con l'endpoint 2.0.

## <a name="restrictions-on-protocols"></a>Restrizioni relative ai protocolli
L'endpoint 2.0 non supporta protocolli SAML o WS-Federation, ma solo Open ID Connect e OAuth 2.0.  Non tutte le funzionalità e caratteristiche dei protocolli OAuth, tuttavia, sono state incorporate nell'endpoint 2.0. Di seguito sono elencate le funzionalità e le caratteristiche di protocollo attualmente *non disponibili* nell'endpoint 2.0:

* I token ID emessi dall'endpoint 2.0 non contengono alcuna attestazione `email` per l'utente, anche se si acquisisce l'autorizzazione da parte dell'utente per visualizzare la posta elettronica.
* L'endpoint UserInfo di OpenID Connect non è implementato nell'endpoint 2.0. Tutti i dati del profilo utente potenzialmente ricevuti sull'endpoint sono tuttavia disponibili usando l'endpoint `/me` di Microsoft Graph.
* L'endpoint 2.0 non supporta attualmente l'emissione di attestazioni basate su ruolo o gruppo nei token ID.
* La concessione [OAuth 2.0 Resource Owner Password Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.3) non è supportata dall'endpoint 2.0.

Tenere presente inoltre che l'endpoint v 2.0 non supporta alcuna forma dei protocolli SAML o WS-Federation.

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint 2.0, vedere l'articolo [Riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrizioni relative agli account aziendali o dell'istituto di istruzione
Se si usa Active Directory Authentication Library (ADAL) in applicazioni Windows, è possibile che si debba ricorrere all'autenticazione integrata di Windows, che sfrutta la concessione per l'asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione per l'asserzione SAML non è supportata dall'endpoint 2.0.