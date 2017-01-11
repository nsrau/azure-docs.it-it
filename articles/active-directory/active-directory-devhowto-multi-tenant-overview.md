---
title: "Come creare un&quot;applicazione che può consentire l&quot;accesso a qualsiasi utente di Azure Active Directory | Documentazione Microsoft"
description: Istruzioni dettagliate per la creazione di un&quot;applicazione che consenta a un utente di accedere da qualsiasi tenant Azure Active Directory, nota anche come applicazione multi-tenant.
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 60874690edfe647c97849bd4099052ab7bc33f80


---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant
Se si offre un'applicazione Software as a Service a molte organizzazioni, è possibile configurare l'applicazione in modo da consentire accessi da qualsiasi tenant di Azure AD.  In Azure AD questa operazione viene definita impostazione dell'applicazione multi-tenant.  Gli utenti in qualsiasi tenant Azure AD saranno in grado di accedere all'applicazione dopo il consenso ad usare il loro account con l'applicazione.  

Se si dispone di un'applicazione esistente con un proprio sistema di account e che supporta altri tipi di accesso da altri provider di cloud, l'aggiunta dell'accesso Azure AD da qualsiasi tenant è semplice come la registrazione dell'app, l'aggiunta di codice di accesso tramite OAuth2, OpenID Connect o SAML e l'inserimento di un pulsante Accedi con Microsoft nell'applicazione. Fare clic sul pulsante seguente per ottenere altre informazioni sulla personalizzazione dell'applicazione.

[![Pulsante di accesso][AAD-Sign-In]][AAD-App-Branding]

Questo articolo presuppone che l'utente abbia già familiarità con la creazione di un'applicazione single-tenant per Azure AD.  In caso contrario, tornare alla [home page della Guida per sviluppatori][AAD-Dev-Guide] e provare una delle procedure di avvio rapido.

Quattro semplici passaggi consentono di convertire l'applicazione in un'applicazione multi-tenant di Azure AD:

1. Aggiornare la registrazione dell'applicazione in modo che sia multi-tenant
2. Aggiornare il codice per l'invio delle richieste all'endpoint /common 
3. Aggiornare il codice per gestire più valori dell'autorità di certificazione
4. Informarsi sul consenso dell'utente e dell'amministratore e apportare le modifiche appropriate al codice

Esaminiamo in dettaglio ogni passaggio. È anche possibile passare direttamente a [questo elenco di esempi multi-tenant][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Aggiornare la registrazione in modo che sia multi-tenant
Per impostazione predefinita, le registrazioni di API o app Web in Azure AD sono single-tenant.  È possibile rendere la registrazione multi-tenant individuando l'opzione corrispondente nella pagina di configurazione della registrazione dell'applicazione nel [portale di Azure classico][AZURE-classic-portal] e impostandola su "Sì".

Nota: un'applicazione può diventare multi-tenant se l'URI dell'ID App dell'applicazione è univoco a livello globale. L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo.  Per un'applicazione single-tenant, è sufficiente che l'URI dell'ID App sia univoco all'interno del tenant.  Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'applicazione in tutti i tenant.  L'univocità globale viene applicata richiedendo che l'URI dell'ID App abbia un nome host corrispondente a un dominio verificato del tenant di Azure AD.  Ad esempio, se il nome del tenant è contoso.onmicrosoft.com, l'URI ID app sarà `https://contoso.onmicrosoft.com/myapp`.  Se il tenant ha un dominio verificato `contoso.com`, l'URI ID app valido sarà `https://contoso.com/myapp`.  L'impostazione di un'applicazione come multi-tenant avrà esito negativo se l'URI dell'ID App non segue questo modello.

Per impostazione predefinita, le registrazioni client native sono multi-tenant.  Non è necessario intraprendere alcuna azione per rendere multi-tenant una registrazione nativa dell'applicazione client.

## <a name="update-your-code-to-send-requests-to-common"></a>Aggiornare il codice per l'invio delle richieste all'endpoint /common
In un'applicazione single-tenant le richieste di accesso vengono inviate all'endpoint di accesso del tenant.   Ad esempio, per contoso.onmicrosoft.com l'endpoint sarà:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Le richieste inviate all'endpoint del tenant possono eseguire l'accesso degli utenti (o guest) in questo tenant alle applicazioni in questo tenant.  Con un'applicazione multi-tenant, l'applicazione non determina in anticipo il tenant di provienienza dell'utente, pertanto non è possibile inviare richieste all'endpoint del tenant.  Al contrario, le richieste vengono inviate a un endpoint che esegue il multiplexing tra tutti i tenant di Azure Active Directory:

    https://login.microsoftonline.com/common

Quando Azure AD riceve una richiesta sull'endpoint /common, consente l'accesso dell'utente e quindi individua il tenant di provenienza dell'utente.  L'endpoint /common funziona con tutti i protocolli di autenticazione supportati da Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 e WS-Federation.

La risposta di accesso all'applicazione di accesso contiene un token che rappresenta l'utente.  Il valore dell'autorità di certificazione nel token indica a un'applicazione il tenant di provenienza dell'utente.  Quando l'endpoint /common restituisce una risposta, il valore dell'autorità di certificazione nel token corrisponderà al tenant dell'utente.  È importante notare che l'endpoint /common non è un tenant o un'autorità di certificazione, ma è semplicemente un multiplexer.  Quando si usa l'endpoint /common, è necessario aggiornare la logica dell'applicazione per la convalida dei token in modo da tenerne conto. 

Come indicato in precedenza, le applicazioni multi-tenant devono offrire anche un'esperienza di accesso coerente per gli utenti, adeguandosi alle linee guida di personalizzazione delle applicazioni di Azure AD. Fare clic sul pulsante seguente per ottenere altre informazioni sulla personalizzazione dell'applicazione.

[![Pulsante di accesso][AAD-Sign-In]][AAD-App-Branding]

È ora possibile esaminare in modo più dettagliato l'uso dell'endpoint /common e l'implementazione del codice.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aggiornare il codice per gestire più valori dell'autorità di certificazione
Le applicazioni Web e le API Web ricevono e convalidano i token da Azure AD.  

> [!NOTE]
> Le applicazioni client native richiedono e ricevono i token da Azure AD e li inviano alle API in cui vengono convalidati.  Le applicazioni native non convalidano i token e devono gestirli come opachi.
> 
> 

Passiamo ora al modo in cui un'applicazione convalida i token ricevuti da Azure AD.  Un'applicazione single-tenant prende in genere un valore dell'endpoint come:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

e lo usa per creare un URL di metadati, in questo caso OpenID Connect, come:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

per scaricare due informazioni critiche che vengono usate per convalidare i token: il valore dell'autorità di certificazione e le chiavi di firma del tenant.  Ogni tenant di Azure AD ha un valore univoco dell'autorità di certificazione del formato:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

dove il valore GUID è la versione sicura di ridenominazione dell'ID tenant del tenant.  Se si fa clic sul collegamento dei metadati precedente per `contoso.onmicrosoft.com`, è possibile visualizzare il valore dell'autorità di certificazione nel documento.

Quando un'applicazione single-tenant convalida un token, verifica la firma del token con le chiavi di firma del documento di metadati e verifica che il valore dell'autorità di certificazione nel token corrisponda a quello trovato nel documento di metadati.

Poiché l'endpoint /common non corrisponde a un tenant e non è un'autorità di certificazione, il valore dell'autorità di certificazione nei metadati per /common ha un URL basato su modello anziché un valore effettivo:

    https://sts.windows.net/{tenantid}/

Un'applicazione multi-tenant non può quindi convalidare i token semplicemente confrontando il valore dell'autorità di certificazione nei metadati con il valore `issuer` nel token.  Un'applicazione multi-tenant richiede una logica per decidere quali valori di autorità di certificazione sono validi, in base alla parte ID del tenant del valore dell'autorità di certificazione.  

Se ad esempio un'applicazione multi-tenant consente l'accesso solo da tenant specifici che hanno eseguito l'accesso ai servizi, è necessario verificare il valore dell'autorità di certificazione o il valore di attestazione `tid` nel token per assicurarsi che sia incluso nel relativo elenco di sottoscrittori.  Se un'applicazione multi-tenant gestisce solo persone e non adotta decisioni di accesso basate su tenant, è possibile ignorare il valore dell'autorità di certificazione.

Negli esempi multi-tenant disponibili nella sezione [Contenuti correlati](#related-content) alla fine di questo articolo la convalida dell'autorità di certificazione è disattivata per consentire l'accesso a qualsiasi tenant di Azure AD.

Esaminiamo l'esperienza utente per gli utenti che accedono ad applicazioni multi-tenant.

## <a name="understanding-user-and-admin-consent"></a>Informazioni sul consenso dell'utente e dell'amministratore
Per fare in modo che un utente possa accedere a un'applicazione in Azure AD, l'applicazione deve essere rappresentata nel tenant dell'utente.  Ciò consente alle organizzazioni di eseguire operazioni come applicare criteri univoci quando gli utenti dal tenant accedono all'applicazione.  Per un'applicazione single-tenant questa registrazione è semplice. È l'azione che viene eseguita quando si registra l'applicazione nel [portale di Azure classico][AZURE-classic-portal].

Per un'applicazione multi-tenant, la registrazione iniziale per l'applicazione si trova nel tenant di Azure AD usato dallo sviluppatore.  Quando un utente di un tenant diverso accede all'applicazione per la prima volta, Azure AD richiede il consenso alle autorizzazioni richieste dall'applicazione.  Se fornisce il consenso, viene creata una rappresentazione dell'applicazione denominata *entità servizio* nel tenant dell'utente ed è possibile procedere con l'accesso. Viene anche creata una delega nella directory che registra il consenso dell'utente all'applicazione. Vedere [Oggetti applicazione e oggetti entità servizio][AAD-App-SP-Objects] per informazioni dettagliate sugli oggetti applicazione ed entità servizio dell'applicazione e su come interagiscono tra loro.

![Consenso per l'app a livello singolo][Consent-Single-Tier] 

Questa esperienza di consenso è interessata dalle autorizzazioni richieste dall'applicazione.  Azure AD supporta due tipi di autorizzazioni, delegate e solo app:

* Un'autorizzazione delegata concede a un'applicazione la possibilità di agire come utente connesso per un sottoinsieme di operazioni che l'utente può eseguire.  Ad esempio, è possibile concedere a un'applicazione l'autorizzazione delegata per la lettura del calendario dell'utente connesso.
* Un'autorizzazione solo app viene concessa direttamente all'identità dell'applicazione.  Ad esempio, è possibile concedere a un'applicazione l'autorizzazione solo app per leggere l'elenco di utenti in un tenant e sarà possibile eseguire questa operazione indipendentemente dall'utente che ha eseguito l'accesso all'applicazione.

Alcune autorizzazioni possono essere concesse da un utente normale, mentre altre richiedono il consenso dell'amministratore tenant. 

### <a name="admin-consent"></a>Consenso dell'amministratore
Le autorizzazioni solo app richiedono il consenso dell'amministratore tenant.  Se l'applicazione richiede un'autorizzazione solo per app e un utente comune tenta di accedere all'applicazione, l'applicazione visualizzerà un messaggio di errore che informa che l'utente non è in grado di fornire il consenso.

Alcune autorizzazioni delegate richiedono anche il consenso dell'amministratore tenant.  Ad esempio, il writeback in Azure AD come utente connesso richiede il consenso dell'amministratore tenant.  Come le autorizzazioni solo app, se un utente comune tenta di accedere a un'applicazione che richiede un'autorizzazione delegata che necessita del consenso dell'amministratore, verrà visualizzato un errore nell'applicazione.  La richiesta del consenso di amministratore da parte di un'applicazione è determinata dallo sviluppatore che ha pubblicato la risorsa ed è presente nella documentazione per la risorsa.  Collegamenti ad argomenti che descrivono le autorizzazioni disponibili per le API Graph di Azure AD e API Graph di Microsoft sono disponibili nella sezione [Contenuti correlati](#related-content) di questo articolo.

Se l'applicazione usa autorizzazioni che richiedono il consenso dell'amministratore, è necessario che sia presente un movimento nell'applicazione, ad esempio un pulsante o un collegamento, con cui l'amministratore può avviare l'azione.  La richiesta inviata dall'applicazione per questa azione è una richiesta di autorizzazione OAuth2/OpenID Connect standard, ma che include anche il parametro `prompt=admin_consent` della stringa di query.  Dopo che l'amministratore ha fornito il consenso e l'entità servizio è stata creata nel tenant del cliente, le successive richieste di accesso non richiedono il parametro `prompt=admin_consent` .   Poiché l'amministratore ha deciso che le autorizzazioni richieste sono accettabili, a nessun altro utente nel tenant verrà richiesto il consenso da questo punto in poi.

Il parametro `prompt=admin_consent` può essere usato anche dalle applicazioni che richiedono autorizzazioni che non necessitano del consenso dell'amministratore, ma che offrono un'esperienza in cui l'amministratore tenant "esegue l'accesso" per l'applicazione una sola volta e a nessun altro utente viene richiesto il consenso da questo punto in poi.

Se un'applicazione richiede il consenso dell'amministratore e l'amministratore accede all'applicazione, ma il parametro `prompt=admin_consent` non viene inviato, l'amministratore sarà in grado di fornire correttamente il consenso all'applicazione, ma il consenso verrà fornito solo per il proprio account utente.  Gli utenti normali non saranno ancora in grado di eseguire l'accesso e fornire il consenso all'applicazione.  Questa condizione è utile se si vuole assegnare all'amministratore tenant la possibilità di esplorare l'applicazione prima di consentire l'accesso ad altri utenti.

Un amministratore tenant può disabilitare la possibilità che gli utenti normali possano il consenso alle applicazioni.  Se questa funzionalità è disabilitata, è necessario impostare il consenso dell'amministratore come obbligatorio sempre per l'applicazione nel tenant.  Se si vuole testare l'applicazione con il consenso dell'utente normale disabilitato, è possibile trovare l'opzione di configurazione nella sezione di configurazione del tenant di Azure AD del [portale di Azure classico][AZURE-classic-portal].

> [!NOTE]
> Alcune applicazioni offrono un'esperienza in cui gli utenti normali sono inizialmente in grado di fornire il consenso e successivamente l'applicazione può coinvolgere l'amministratore e richiedere le autorizzazioni che necessitano del consenso dell'amministratore.  Non è attualmente possibile eseguire questa operazione con una singola registrazione all'applicazione in Azure AD.  L'imminente endpoint Azure AD v2 consente alle applicazioni di richiedere le autorizzazioni in fase di esecuzione, anziché al momento della registrazione abilitando questo scenario.  Per altre informazioni, vedere la [Guida per gli sviluppatori del modello di app di Azure AD versione 2][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Consenso e applicazioni multilivello
L'applicazione può avere più livelli, ognuno rappresentato dalla propria registrazione in Azure AD.  Un esempio è un'applicazione nativa che esegue una chiamata a un'API Web o un'applicazione Web che esegue una chiamata a un'API Web.  In entrambi i casi, il client (app nativa o app Web) richiede le autorizzazioni per eseguire la chiamata alla risorsa (API Web).  Per fare in modo il client venga autorizzato correttamente nel tenant del cliente, tutte le risorse a cui richiede le autorizzazioni devono esistere già nel tenant del cliente.  Se questa condizione non viene soddisfatta, Azure AD restituirà un errore indicante che prima deve essere aggiunta la risorsa.

Può trattarsi di un problema se l'applicazione logica è costituita da due o più registrazioni di applicazioni, ad esempio un client e una risorsa separati.  Come ottenere prima la risorsa nel tenant del cliente?  Azure AD illustra questo caso abilitando la concessione del consenso al client e alle risorse in un unico passaggio, in cui l'utente visualizza la somma della autorizzazioni richieste dal client e dalla risorsa nella pagina del consenso.  Per abilitare questo comportamento, la registrazione dell'applicazione della risorsa deve includere l'ID app del client come `knownClientApplications` nel manifesto dell'applicazione.  Ad esempio:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Questa proprietà può essere aggiornata tramite il [manifesto dell'applicazione][AAD-App-Manifest] della risorsa e viene illustrata in un client nativo multilivello che esegue la chiamata all'esempio di API Web nella sezione [Contenuti correlati](#related-content) alla fine di questo articolo. Il diagramma seguente fornisce una panoramica del consenso per un'app multilivello:

![Consenso per l'app client nota multilivello][Consent-Multi-Tier-Known-Client] 

Un caso simile si verifica se i diversi livelli di un'applicazione vengono registrati in tenant diversi.  Ad esempio, si consideri il caso della creazione di un'applicazione client nativa che esegue la chiamata all'API di Office 365 Exchange Online.  Per sviluppare l'applicazione nativa e successivamente eseguire l'applicazione nativa nel tenant del cliente, è necessario che sia presente l'entità servizio Exchange Online.  In questo caso il cliente deve acquistare Exchange Online per creare l'entità servizio nel tenant.  Nel caso di un'API creata da un'organizzazione diversa da Microsoft, lo sviluppatore dell'API deve includere un modo che consenta ai clienti di fornire il consenso per l'applicazione nel tenant del cliente, ad esempio una pagina Web di consenso usando i meccanismi descritti in questo articolo.  Dopo aver creato l'entità servizio nel tenant, l'applicazione nativa può ottenere i token per l'API.

Il diagramma seguente fornisce una panoramica del consenso per un'app multilivello registrata in diversi tenant:

![Consenso per l'app di terze parti multilivello][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Revoca del consenso
Gli utenti e gli amministratori possono revocare il consenso all'applicazione in qualsiasi momento:

* Gli utenti revocano l'accesso alle singole applicazioni rimuovendole dall'elenco [Applicazioni riquadro di accesso][AAD-Access-Panel].
* Gli amministratori revocano l'accesso alle applicazioni rimuovendole da Azure AD usando la sezione di gestione di Azure AD del [portale di Azure classico][AZURE-classic-portal].

Se un amministratore fornisce il consenso a un'applicazione per tutti gli utenti in un tenant, gli utenti non possono revocare l'accesso singolarmente.  Solo l'amministratore può revocare l'accesso e soltanto per l'intera applicazione.

### <a name="consent-and-protocol-support"></a>Supporto del consenso e dei protocolli
Il consenso è supportato in Azure AD tramite i protocolli OAuth, OpenID Connect, WS-Federation e SAML.  I protocolli WS-Federation e SAML non supportano il parametro `prompt=admin_consent` e il consenso dell'amministratore è possibile solo tramite OAuth e OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Applicazioni multi-tenant e memorizzazione nella cache dei token di accesso
Le applicazioni multi-tenant possono anche ottenere i token di accesso per eseguire chiamate alle API protette da Azure AD.  Un errore comune quando si usa Active Directory Authentication Library (ADAL) con un'applicazione multi-tenant è quello di richiedere inizialmente un token per un utente tramite /common, ricevere una risposta e quindi richiedere un token successivo per lo stesso utente usando sempre /common.  Poiché la risposta da Azure AD proviene da un tenant, non /common, la libreria ADAL memorizza nella cache il token come proveniente dal tenant. Nella chiamata successiva a /common per ottenere un token di accesso per l'utente non è presente la voce della cache e all'utente viene richiesto di accedere di nuovo.  Per evitare questo errore della cache, assicurarsi che le chiamate successive per un utente già connesso vengano eseguite all'endpoint del tenant.

## <a name="related-content"></a>Contenuti correlati
* [Esempi di applicazioni multi-tenant][AAD-Samples-MT]
* [Linee guida sulla personalizzazione per le applicazioni][AAD-App-Branding]
* [Guida per sviluppatori di Azure AD][AAD-Dev-Guide]
* [Oggetti applicazione e oggetti entità servizio][AAD-App-SP-Objects]
* [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps]
* [Panoramica del framework di consenso][AAD-Consent-Overview]
* [Ambiti di autorizzazione di Microsoft API Graph][MSFT-Graph-AAD]
* [Ambiti di autorizzazione di Azure AD API Graph][AAD-Graph-Perm-Scopes]

La sezione dei commenti Disqus di seguito consente di fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[MSFT-Graph-AAD]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ./active-directory-appmodel-v2-overview.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

















<!--HONumber=Dec16_HO4-->


