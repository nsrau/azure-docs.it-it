---
title: Indicazioni per gli sviluppatori di Azure AD Conditional AccessAzure AD Conditional Access developer guidance
description: Linee guida e scenari per gli sviluppatori per l'accesso condizionale di Azure ADDeveloper guidance and scenarios for Azure AD Conditional Access
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: 92acb1a475fbd41bfb7351d73c61db866ce2bbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154934"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Indicazioni per gli sviluppatori per l'accesso condizionale di Azure Active DirectoryDeveloper guidance for Azure Active Directory Conditional Access

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

La funzionalità Accesso condizionale in Azure Active Directory (Azure AD) offre uno dei diversi modi che è possibile usare per proteggere l'app e un servizio. L'accesso condizionale consente agli sviluppatori e ai clienti aziendali di proteggere i servizi in molti modi, tra cui:

* Autenticazione a più fattori
* Autorizzazione dell'accesso a servizi specifici solo per dispositivi registrati in Intune
* Limitazione delle posizioni dell'utente e degli intervalli IP

Per altre informazioni sulle funzionalità complete dell'accesso condizionale, vedere [Accesso condizionale in Azure Active Directory.For](../active-directory-conditional-access-azure-portal.md)more information on the full capabilities of Conditional Access, see Conditional Access in Azure Active Directory .

Per gli sviluppatori che creano app per Azure AD, questo articolo illustra come usare l'accesso condizionale e si apprenderà anche l'impatto dell'accesso alle risorse a cui non è stato applicato il controllo sui criteri di accesso condizionale. L'articolo esplora anche le implicazioni dell'accesso condizionale nel flusso per conto di, nelle app Web, nell'accesso a Microsoft Graph e nelle API di chiamata.

Si presuppone una conoscenza delle app a [tenant singolo e multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) e dei [modelli di autenticazione comuni](v1-authentication-scenarios.md).

## <a name="how-does-conditional-access-impact-an-app"></a>In che modo l'accesso condizionale influisce su un'app?

### <a name="app-types-impacted"></a>Tipi di app interessati

Nella maggior parte dei casi, l'accesso condizionale non modifica il comportamento di un'app o richiede modifiche da parte dello sviluppatore.Solo in alcuni casi, quando un'app richiede indirettamente o in modo invisibile un token per un servizio, un'app richiede modifiche al codice per gestire le "sfide" dell'accesso condizionale.L'operazione può essere semplice quanto l'esecuzione di una richiesta di accesso interattiva.

In particolare, gli scenari seguenti richiedono codice per gestire le "sfide" di accesso condizionale:Specifically, the following scenarios require code to handle Conditional Access "challenges":

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js
* App Web che chiamano una risorsa

I criteri di accesso condizionale possono essere applicati all'app, ma possono anche essere applicati a un'API Web a cui accede l'app. Per altre informazioni su come configurare un criterio di accesso condizionale, vedere [Guida introduttiva: Richiedere l'autenticazione a più fattori per app specifiche con l'accesso condizionale](../conditional-access/app-based-mfa.md)di Azure Active Directory.

A seconda dello scenario, un cliente aziendale può applicare e rimuovere i criteri di accesso condizionale in qualsiasi momento. Affinché l'app continui a funzionare quando vengono applicati nuovi criteri, è necessario implementare la gestione delle richieste. Gli esempi seguenti illustrano la gestione delle richieste.

### <a name="conditional-access-examples"></a>Esempi di accesso condizionale

Alcuni scenari richiedono modifiche al codice per gestire l'accesso condizionale, mentre altri funzionano così com'è. Ecco alcuni scenari che usano l'accesso condizionale per eseguire l'autenticazione a più fattori che fornisce alcune informazioni sulla differenza.

* Si sta creando un'app iOS a tenant singolo e si applicano criteri di accesso condizionale. L'app consente l'accesso di un utente e non richiede l'accesso a un'API. Quando l'utente accede, i criteri vengono richiamati automaticamente e l'utente deve eseguire l'autenticazione a più fattori (MFA). 
* Si sta creando un'app nativa che usa un servizio di livello intermedio per accedere a un'API downstream. Un cliente aziendale in azienda che usa questa app applica i criteri all'API downstream. Quando un utente finale esegue l'accesso, l'app nativa richiede l'accesso al livello intermedio e invia il token. Il livello intermedio esegue il flusso on-behalf-of per richiedere l'accesso all'API downstream. A questo punto, viene presentata una richiesta di attestazioni al livello intermedio. Il livello intermedio invia la richiesta di verifica all'app nativa, che deve essere conforme ai criteri di accesso condizionale.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph presenta considerazioni speciali per la creazione di app in ambienti di accesso condizionale. In genere, i meccanismi dell'accesso condizionale si comportano allo stesso modo, ma i criteri visualizzati dagli utenti saranno basati sui dati sottostanti che l'app richiede dal grafico. 

In particolare, tutti gli ambiti di Microsoft Graph rappresentano alcuni set di dati che possono essere applicati singolarmente i criteri. Poiché ai criteri di accesso condizionale vengono assegnati i set di dati specifici, Azure AD applicherà i criteri di accesso condizionale basati sui dati sottostanti Graph, anziché Graph stesso.

Ad esempio, se un'app richiede i seguenti ambiti di Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Un'app può aspettarsi che gli utenti soddisfino tutti i criteri impostati su Prenotazioni ed Exchange. Alcuni ambiti possono eseguire il mapping a più set di dati se concede l'accesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Conformità ai criteri di accesso condizionaleComplying with a Conditional Access policy

Per diverse topologie di app, viene valutato un criterio di accesso condizionale quando viene stabilita la sessione. Poiché un criterio di accesso condizionale opera sulla granularità di app e servizi, il punto in cui viene richiamato dipende in larga misura dallo scenario che si sta tentando di eseguire.

Quando l'app tenta di accedere a un servizio con un criterio di accesso condizionale, potrebbe verificarsi una richiesta di problemi di accesso condizionale. Questa richiesta di `claims` verifica viene codificata nel parametro fornito in una risposta da Azure AD. Ecco un esempio del parametro della richiesta: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Gli sviluppatori possono aggiungere questa richiesta a una nuova richiesta per Azure AD. Il passaggio di questo stato richiede all'utente finale di eseguire tutte le azioni necessarie per rispettare i criteri di accesso condizionale. Negli scenari seguenti vengono illustrate informazioni specifiche sull'errore e su come estrarre il parametro.

## <a name="scenarios"></a>Scenari

### <a name="prerequisites"></a>Prerequisiti

L'accesso condizionale di Azure AD è una funzionalità inclusa in [Azure AD Premium.](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) Per altre informazioni sui requisiti relativi alle licenza, vedere [Report Utilizzo senza licenza](../active-directory-conditional-access-unlicensed-usage-report.md). Gli sviluppatori possono partecipare alla [rete per sviluppatori Microsoft](https://msdn.microsoft.com/dn308572.aspx), che include una sottoscrizione gratuita di Enterprise Mobility Suite, che include Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerazioni per scenari specifici

Le informazioni seguenti si applicano solo in questi scenari di accesso condizionale:The following information only applies in these Conditional Access scenarios:

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js

Le sezioni seguenti illustrano scenari comuni più complessi. Il principio operativo di base è che i criteri di accesso condizionale vengono valutati nel momento in cui viene richiesto il token per il servizio a cui sono applicati i criteri di accesso condizionale.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App che esegue il flusso on-behalf-of

In questo scenario viene illustrato il caso in cui un'app nativa chiama un'API o un servizio Web. A sua volta, il servizio esegue il flusso "on-behalf-of" per chiamare un servizio downstream. Nel nostro caso, abbiamo applicato i criteri di accesso condizionale al servizio downstream (API Web 2) e si usa un'app nativa anziché un'app server/daemon. 

![Diagramma per le app che eseguono il flusso on-behalf-of](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

La richiesta di token iniziale per l'API Web 1 non comporta la richiesta all'utente finale dell'autenticazione a più fattori in quanto l'API Web 1 non sempre raggiunge l'API downstream. Quando l'API Web 1 prova a richiedere un token per conto dell'utente per l'API Web 2, la richiesta non riesce perché l'utente non ha eseguito l'accesso con l'autenticazione a più fattori.

Azure AD restituisce una risposta HTTP con alcuni dati interessanti:

> [!NOTE]
> In questo caso è una descrizione dell'errore di autenticazione a `interaction_required` più fattori, ma esiste un'ampia gamma di possibili relative all'accesso condizionale.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Nell'API Web 1 viene intercettato l'errore `error=interaction_required` e viene inviata nuovamente la richiesta `claims` all'app desktop. A questo punto, l'app desktop può chiamare di nuovo `acquireToken()` e aggiungere la richiesta `claims` come parametro di stringa di query aggiuntivo. La nuova richiesta impone all'utente l'uso dell'autenticazione a più fattori e quindi invia il nuovo token all'API Web 1 e completa il flusso on-behalf-of.

Per provare questo scenario, vedere l'[esempio di codice .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Viene illustrato come passare la richiesta di attestazioni dall'API Web 1 all'app nativa e costruire una nuova richiesta all'interno dell'app client.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App che accede a più servizi

In questo scenario viene esaminato il caso in cui un'app Web accede a due servizi, uno dei quali dispone di un criterio di accesso condizionale assegnato. A seconda della logica dell'app, potrebbe esserci un percorso in cui l'app non richiede l'accesso a entrambi i servizi Web. In questo scenario, l'ordine in cui si richiede un token ha un ruolo importante nell'esperienza dell'utente finale.

Si supponga di disporre di servizi Web A e B e del servizio Web B dei criteri di accesso condizionale applicati. Sebbene la richiesta di autenticazione interattiva iniziale richieda il consenso per entrambi i servizi, i criteri di accesso condizionale non sono necessari in tutti i casi. Se l'app richiede un token per il servizio Web B, i criteri vengono richiamati e anche le richieste successive per il servizio Web A hanno esito positivo, come indicato di seguito.

![Diagramma di flusso per le app che accedono a più servizi](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

In alternativa, se l'app richiede inizialmente un token per il servizio Web A, l'utente finale non richiama i criteri di accesso condizionale. Ciò consente allo sviluppatore dell'app di controllare l'esperienza dell'utente finale e di non forzare i criteri di accesso condizionale da richiamare in tutti i casi. Il caso complicato è se l'app richiede successivamente un token per il servizio Web B.The tricky case if the app subsequently requests a token for web service B. A questo punto, l'utente finale deve rispettare i criteri di accesso condizionale. Quando l'app prova a eseguire `acquireToken`, può venire generato l'errore seguente (illustrato nel diagramma seguente):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App che accede a più servizi e richiede un nuovo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se l'app usa la libreria ADAL, in caso di errore di acquisizione del token viene sempre eseguito un nuovo tentativo in modo interattivo. Quando si verifica questa richiesta interattiva, l'utente finale ha la possibilità di rispettare l'accesso condizionale. Ciò è vero a `AcquireTokenSilentAsync` `PromptBehavior.Never` meno che la richiesta non ```AcquireToken``` sia un o nel qual caso l'app deve eseguire una richiesta interattiva per dare all'utente finale la possibilità di rispettare i criteri.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: App a pagina singola che usa ADAL.js

In questo scenario viene esaminato il caso in cui si dispone di un'app a pagina singola (SPA), utilizzando ADAL.js per chiamare un'API Web protetta da accesso condizionale. Si tratta di un'architettura semplice, ma presenta alcune sfumature che devono essere prese in considerazione quando si sviluppa intorno all'accesso condizionale.

In ADAL.js ci sono poche funzioni che ottengono token: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()`ottiene un token ID tramite una richiesta di accesso interattiva, ma non ottiene i token di accesso per qualsiasi servizio (inclusa un'API Web protetta da accesso condizionale).
* È quindi possibile usare `acquireToken(…)` per ottenere automaticamente un token di accesso, senza visualizzazione dell'interfaccia utente in nessun caso.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` vengono entrambi usati per richiedere in modo interattivo un token per una risorsa, quindi l'interfaccia utente di accesso viene sempre visualizzata.

Quando un'app necessita di un token di accesso per chiamare un'API Web, viene eseguito un tentativo di chiamata a `acquireToken(…)`. Se la sessione di token è scaduta o è necessario rispettare i criteri `acquireTokenPopup()` di `acquireTokenRedirect()`accesso condizionale, la funzione *acquireToken* ha esito negativo e l'app utilizza o .

![Diagramma di flusso per le app a pagina singola che usano ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Esaminiamo un esempio con lo scenario di accesso condizionale. L'utente finale è appena atterrato sul sito e non ha una sessione. Si esegue una chiamata a `login()` e si ottiene un token ID senza autenticazione a più fattori. L'utente preme quindi un pulsante che comporta una richiesta di dati dall'app a un'API Web. L'app tenta `acquireToken()` di eseguire una chiamata ma non riesce poiché l'utente non ha ancora eseguito l'autenticazione a più fattori e deve essere conforme ai criteri di accesso condizionale.

Azure AD invia la risposta HTTP seguente:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

L'app deve intercettare l'errore `error=interaction_required`. L'applicazione può quindi usare `acquireTokenPopup()` o `acquireTokenRedirect()` sulla stessa risorsa. L'utente deve eseguire l'autenticazione a più fattori. Dopo che l'utente ha completato l'autenticazione a più fattori, viene emesso un nuovo token di accesso per l'app per la risorsa richiesta.

Per provare questo scenario, vedere l'[esempio di codice on-behalf-of JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). In questo esempio di codice vengono utilizzati i criteri di accesso condizionale e l'API Web registrata in precedenza con JS SPA per illustrare questo scenario. Mostra come gestire correttamente la richiesta di attestazioni e ottenere un token di accesso che può essere usato per l'API Web. In alternativa, vedere l'[esempio di codice Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) generale, per informazioni su Angular SPA

## <a name="see-also"></a>Vedere anche

* Per altre informazioni sulle funzionalità, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Per altri esempi di codice di Azure AD, vedere il [repository GitHub di esempi di codice](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Per altre informazioni su ADAL SDK e sull'accesso alla documentazione di riferimento, vedere la [guida alle librerie](active-directory-authentication-libraries.md).
* Per altre informazioni sugli scenari multi-tenant, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
