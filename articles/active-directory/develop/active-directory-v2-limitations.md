---
title: Limiti e restrizioni dell&quot;endpoint 2.0 di Azure Active Directory | Documentazione Microsoft
description: Elenco di limiti e restrizioni per l&quot;endpoint 2.0 di Azure AD.
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
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 5d1ceabeeee8cef0170b928703488845f70656ef


---
# <a name="should-i-use-the-v20-endpoint"></a>Perché usare l'endpoint 2.0
Nella compilazione di applicazioni che si integrano con Azure Active Directory (Azure AD) è necessario stabilire se l'endpoint 2.0 e i protocolli di autenticazione rispondono ai requisiti specifici. L'endpoint di Azure AD originale è ancora completamente supportato e, per alcuni aspetti, include più funzionalità della versione 2.0. L'endpoint 2.0 [introduce vantaggi significativi](active-directory-v2-compare.md) per gli sviluppatori che potrebbero indurre l'utente a usare il nuovo modello di programmazione.

Di seguito vengono indicati i consigli per usare attualmente l'endpoint 2.0.

* Se si desidera supportare account Microsoft personali nell'applicazione, usare l'endpoint 2.0. È necessario prima comprendere i limiti illustrati in questo articolo, in particolare quelli che si applicano ad account aziendali o dell'istituto di istruzione.
* Se l'applicazione deve supportare solo account aziendali e dell'istituto di istruzione, usare [gli endpoint di Azure AD originali](active-directory-developers-guide.md).

Nel corso del tempo, l'endpoint 2.0 verrà migliorato per eliminare le restrizioni elencate di seguito, in modo da consentire l'uso esclusivo dell'endpoint 2.0. Nel frattempo, questo articolo consente di determinare se l'endpoint 2.0 è adatto alle proprie esigenze. L'articolo verrà aggiornato periodicamente per riflettere lo stato corrente dell'endpoint 2.0. È consigliabile tornare a consultarlo regolarmente per valutare i propri requisiti rispetto alle funzionalità della versione 2.0.

Se è disponibile un'app Azure AD che non usa l'endpoint 2.0, non è necessario iniziare da zero. In futuro sarà possibile usare le applicazioni di Azure AD esistenti con l'endpoint 2.0.

## <a name="restrictions-on-app-types"></a>Restrizioni relative ai tipi di app
I tipi di app seguenti non sono attualmente supportati dall'endpoint 2.0. Per una descrizione dei tipi di app supportati, vedere [Tipi di app per l'endpoint 2.0 di Azure Active Directory](active-directory-v2-flows.md).

### <a name="standalone-web-apis"></a>API Web autonome
È possibile usare l'endpoint 2.0 per [compilare un'API Web protetta con OAuth 2.0](active-directory-v2-flows.md#web-apis). L'API Web, tuttavia, può ricevere i token solo da un'applicazione che condivide lo stesso ID applicazione. Non è possibile accedere a un'API Web da un client con un ID applicazione diverso perché il client non sarà in grado di richiedere o ottenere autorizzazioni per l'API Web.

Per informazioni su come compilare un'API Web che accetti token da un client con lo stesso ID applicazione, vedere gli esempi di API Web dell'endpoint 2.0 nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started).

### <a name="web-api-on-behalf-of-flow"></a>Flusso on-behalf-of dell'API Web
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette dall'endpoint v2.0. Questo scenario è comune nei client nativi per cui è presente un back-end dell'API Web, che a sua volta chiama un'istanza di Microsoft Online Services o un'altra API Web personalizzata che supporta Azure AD.

È possibile creare questo scenario usando la concessione delle credenziali tramite token di connessione JWT (token JSON Web) di OAuth 2.0, nota anche come flusso on-behalf-of. Tale flusso, tuttavia, non è attualmente supportato dall'endpoint 2.0. Per verificare il funzionamento di questo flusso nel servizio Azure AD disponibile a livello generale, vedere l'[esempio di codice on-behalf-of su GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Restrizioni relative alle registrazioni di app
Per ogni app da integrare con l'endpoint 2.0, è necessario attualmente creare una registrazione di app nel nuovo [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Le app Azure AD o degli account Microsoft esistenti non sono compatibili con l'endpoint 2.0 né lo sono quelle registrate in qualsiasi portale diverso da quello di registrazione delle applicazioni. In futuro si prevede di offrire un modo per usare un'applicazione esistente come app 2.0, ma attualmente non esiste alcun percorso di migrazione che consenta a un'app esistente di funzionare con l'endpoint 2.0.

Le app registrate nel portale di registrazione delle applicazioni non funzioneranno con l'endpoint di autenticazione AD Azure originale. È possibile tuttavia usare le app create nel portale di registrazione delle applicazioni per realizzare l'integrazione completa con l'endpoint di autenticazione dell'account Microsoft `https://login.live.com`.

Per le registrazioni di app create nel [portale di registrazione delle applicazioni](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), è necessario anche tenere in considerazione gli aspetti seguenti:

* La proprietà **homepage**, nota anche come *URL di accesso*, non è supportata. Senza una home page, queste applicazioni non verranno visualizzate nel pannello Mie app di Office.
* Attualmente sono consentiti solo due segreti dell'app per ogni ID applicazione.
* La registrazione di un'app può essere visualizzata e gestita solo da un singolo account sviluppatore. Non può essere condiviso tra più sviluppatori.
* Sono presenti diverse restrizioni relative al formato dell'URI di reindirizzamento consentito. Per altre informazioni sugli URI di reindirizzamento, vedere la sezione successiva.

## <a name="restrictions-on-redirect-uris"></a>Restrizioni relative agli URI di reindirizzamento
Le app registrate nel portale di registrazione delle applicazioni sono attualmente limitate a un set ristretto di valori di URI di reindirizzamento. L'URI di reindirizzamento per le app e i servizi Web deve iniziare con lo schema `https` e tutti i valori degli URI di reindirizzamento devono condividere un singolo dominio DNS. Non è possibile ad esempio registrare un'app Web con uno degli URI di reindirizzamento seguenti:

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
* Per altre piattaforme e per le applicazioni native e per dispositivi mobili, è possibile realizzare l'integrazione con l'endpoint 2.0 con l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OpenID Connect e OAuth della versione 2.0 [sono documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.
* Per realizzare l'integrazione con l'endpoint 2.0, è anche possibile usare librerie OpenID Connect e OAuth di tipo open source. Il protocollo della versione 2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche rilevanti. La disponibilità di questi tipi di librerie varia in base a linguaggio e piattaforma. Nei siti Web di [OpenID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) è disponibile un elenco delle implementazioni più diffuse. Per altre informazioni, vedere [Azure Active Directory 2.0 e librerie di autenticazione](active-directory-v2-libraries.md), l'elenco di librerie client open source e gli esempi testati con l'endpoint 2.0.

Microsoft ha rilasciato anche un'anteprima iniziale di [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) solo per .NET. Gli utenti sono invitati a provare questa libreria nelle applicazioni client e server .NET. In quanto versione di anteprima, tale libreria non dispone ancora di supporto di qualità a livello generale.

## <a name="restrictions-on-protocols"></a>Restrizioni relative ai protocolli
L'endpoint 2.0 supporta solo OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e caratteristiche dei vari protocolli sono state incorporate nell'endpoint 2.0.

Le funzionalità di protocollo tipiche attualmente *non disponibili* nell'endpoint 2.0 sono le seguenti:

* Il parametro `end_session_endpoint` di OpenID Connect, che consente a un'app di terminare la sessione utente, non è disponibile con l'endpoint 2.0.
* I token ID emessi dall'endpoint 2.0 contengono solo un identificatore pairwise per l'utente. Ciò significa che due applicazioni diverse ricevono ID diversi per lo stesso utente. Si noti che, eseguendo una query sull'endpoint `/me` di Microsoft Graph, si ottiene un ID correlabile per l'utente da usare nelle diverse applicazioni.
* I token ID emessi dall'endpoint 2.0 non contengono alcuna attestazione `email` per l'utente, anche se si acquisisce l'autorizzazione da parte dell'utente per visualizzare la posta elettronica.
* L'endpoint UserInfo di OpenID Connect non è implementato nell'endpoint 2.0. Tutti i dati del profilo utente potenzialmente ricevuti sull'endpoint sono tuttavia disponibili usando l'endpoint `/me` di Microsoft Graph.
* L'endpoint 2.0 non supporta attualmente l'emissione di attestazioni basate su ruolo o gruppo nei token ID.

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint 2.0, vedere l'articolo [Riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Restrizioni relative agli account aziendali o dell'istituto di istruzione
Alcune funzionalità specifiche per gli utenti aziendali Microsoft non sono ancora supportate dall'endpoint 2.0. Per altre informazioni, leggere le sezioni successive.

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>Accesso condizionale basato su dispositivo, applicazioni native e per dispositivi mobili e Microsoft Graph
L'endpoint 2.0 non supporta ancora l'autenticazione del dispositivo per applicazioni native e per dispositivi mobili, ad esempio app native di iOS o Android. Per alcune organizzazioni, questa situazione potrebbe impedire a un'applicazione nativa di eseguire chiamate a Microsoft Graph. L'autenticazione del dispositivo è necessaria quando un amministratore imposta un criterio di accesso condizionale basato su dispositivo in un'applicazione. Per l'endpoint 2.0, lo scenario più probabile per l'accesso condizionale basato su dispositivo si verifica se un amministratore imposta i criteri su una risorsa di Microsoft Graph, ad esempio l'API di Outlook. Se un amministratore imposta tali criteri e l'applicazione nativa richiede un token a Microsoft Graph, la richiesta avrà esito negativo perché l'autenticazione del dispositivo non è ancora supportata. Le applicazioni Web che richiedono token a Microsoft Graph sono tuttavia supportate se sono configurati criteri basati sul dispositivo. In tale scenario l'autenticazione del dispositivo viene eseguita con il Web browser dell'utente.

È possibile che gli sviluppatori non riescano a controllare il momento in cui i criteri vengono impostati sulle risorse di Microsoft Graph o che non siano consapevoli che l'impostazione è stata eseguita. Se si compila un'applicazione per utenti aziendali e dell'istituto di istruzione, è necessario usare [l'endpoint originale di Azure AD](active-directory-developers-guide.md) fino a quando l'endpoint 2.0 non supporta l'autenticazione del dispositivo. Per altre informazioni, vedere l'articolo sull'[accesso condizionale basato su dispositivo in Azure AD](../active-directory-conditional-access.md#device-based-conditional-access).

### <a name="windows-integrated-authentication-for-federated-tenants"></a>Autenticazione integrata di Windows per tenant federati
Se in applicazioni Windows si usa Active Directory Authentication Library (ADAL) con l'endpoint di Azure AD originale, è possibile sfruttare la concessione per l'asserzione SAML (Security Assertion Markup Language). Tale concessione consente agli utenti dei tenant di Azure AD federati di eseguire automaticamente l'autenticazione all'istanza di Active Directory locale senza immettere le credenziali. La concessione per l'asserzione SAML non è supportata dall'endpoint 2.0.




<!--HONumber=Jan17_HO3-->


