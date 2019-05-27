---
title: Linee guida per gli sviluppatori per l'accesso condizionale di Azure Active Directory
description: Scenari e linee guida per gli sviluppatori per l'accesso condizionale di Azure AD
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0674934f7105df3874048308e98fd582d32e72bc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962845"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Linee guida per gli sviluppatori per l'accesso condizionale di Azure Active Directory

La funzionalità di accesso condizionale in Azure Active Directory (Azure AD) offre un'opzione per garantire la sicurezza dell'app e proteggere un servizio. L'accesso condizionale consente agli sviluppatori e ai clienti aziendali di proteggere i servizi in molti modi, tra cui:

* Autenticazione a più fattori
* Autorizzazione dell'accesso a servizi specifici solo per dispositivi registrati in Intune
* Limitazione delle posizioni dell'utente e degli intervalli IP

Per altre informazioni sulle funzionalità di accesso condizionale complete, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Per gli sviluppatori che creano app per Azure AD, questo articolo illustra come usare l'accesso condizionale e offre informazioni sull'impatto dell'accesso alle risorse su cui non si ha controllo e sulle quali è possibile che siano stati applicati criteri di accesso condizionale. L'articolo analizza anche le implicazioni dell'accesso condizionale sul flusso on-behalf-of, sulle app Web, sull'accesso a Microsoft Graph e sulle chiamate alle API.

Si presuppone una conoscenza delle app a [tenant singolo](quickstart-v1-integrate-apps-with-azure-ad.md) e [multi-tenant](howto-convert-app-to-be-multi-tenant.md) e dei [modelli di autenticazione comuni](authentication-scenarios.md).

## <a name="how-does-conditional-access-impact-an-app"></a>Qual è l'impatto dell'accesso condizionale su un'app?

### <a name="app-types-impacted"></a>Tipi di app interessati

Nella maggior parte dei casi, l'accesso condizionale non modifica il comportamento di un'app né richiede modifiche da parte dello sviluppatore. Solo in alcuni casi, quando un'app richiede in modo indiretto o automatico un token per un servizio, sono necessarie modifiche al codice per gestire le richieste di accesso condizionale. L'operazione può essere semplice quanto l'esecuzione di una richiesta di accesso interattiva.

In particolare, gli scenari seguenti richiedono che il codice gestisca le richieste di accesso condizionale:

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js
* App Web che chiamano una risorsa

I criteri di accesso condizionale possono essere applicati all'app, ma anche a un'API Web a cui l'app accede. Per altre informazioni su come configurare un criterio di accesso condizionale, vedere [Avvio rapido: Richiedere l'autenticazione a più fattori per app specifiche con l'accesso condizionale di Azure Active Directory](../conditional-access/app-based-mfa.md).

A seconda dello scenario, un cliente aziendale può applicare e rimuovere i criteri di accesso condizionale in qualsiasi momento. Affinché l'app continui a funzionare quando vengono applicati nuovi criteri, è necessario implementare la gestione delle richieste. Gli esempi seguenti illustrano la gestione delle richieste.

### <a name="conditional-access-examples"></a>Esempi di accesso condizionale

Alcuni scenari richiedono modifiche al codice per la gestione dell'accesso condizionale, mentre altri funzionano senza modifiche. Ecco alcuni scenari in cui l'accesso condizionale viene usato per l'autenticazione a più fattori e che aiutano a comprendere la differenza.

* Si sta creando un'app iOS a tenant singolo e si applicano criteri di accesso condizionale. L'app consente l'accesso di un utente e non richiede l'accesso a un'API. Quando l'utente accede, i criteri vengono richiamati automaticamente e l'utente deve eseguire l'autenticazione a più fattori (MFA). 
* Si sta creando un'app nativa che usa un servizio di livello intermedio per accedere a un'API downstream. Un cliente aziendale in azienda che usa questa app applica i criteri all'API downstream. Quando un utente finale esegue l'accesso, l'app nativa richiede l'accesso al livello intermedio e invia il token. Il livello intermedio esegue il flusso on-behalf-of per richiedere l'accesso all'API downstream. A questo punto, viene presentata una richiesta di attestazioni al livello intermedio. Il livello intermedio invia la richiesta di nuovo all'app nativa, che deve rispettare i criteri di accesso condizionale.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph contiene considerazioni speciali durante la compilazione di App in ambienti di accesso condizionale. In generale, i meccanismi di accesso condizionale si comportano allo stesso, ma i criteri visualizzati utenti si baseranno sui dati sottostanti che dal grafico richiesto dall'app. 

In particolare, tutti gli ambiti di Microsoft Graph rappresentano alcuni set di dati che possono avere singolarmente i criteri applicati. Poiché i criteri di accesso condizionale vengono assegnati i set di dati specifico, Azure AD verrà applicare i criteri di accesso condizionale in base ai dati protetti da Graph - anziché del grafico stesso.

Ad esempio, se un'app richiede i seguenti ambiti di Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Un'app può prevedere agli utenti di soddisfare tutti i criteri impostati in Bookings ed Exchange. Alcuni ambiti possono eseguire il mapping a più set di dati se concede l'accesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Conformità ai criteri di accesso condizionale

Per diverse topologie di app, i criteri di accesso condizionale vengono valutati quando viene stabilita la sessione. Poiché i criteri di accesso condizionale operano a livello di app e servizi, il punto in corrispondenza del quale vengono richiamati dipende principalmente dallo scenario specifico.

Quando l'app tenta di accedere a un servizio con criteri di accesso condizionale, potrebbe riscontrare una richiesta di accesso condizionale. La richiesta è codificata nel `claims` parametro che viene ricevuta una risposta da Azure AD. Ecco un esempio del parametro della richiesta: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Gli sviluppatori possono aggiungere questa richiesta a una nuova richiesta per Azure AD. Quando viene passato questo stato, l'utente finale deve eseguire qualsiasi azione necessaria per la conformità ai criteri di accesso condizionale. Negli scenari seguenti vengono illustrate informazioni specifiche sull'errore e su come estrarre il parametro.

## <a name="scenarios"></a>Scenari

### <a name="prerequisites"></a>Prerequisiti

L'accesso condizionale di Azure AD è una funzionalità inclusa in [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Per altre informazioni sui requisiti relativi alle licenza, vedere [Report Utilizzo senza licenza](../active-directory-conditional-access-unlicensed-usage-report.md). Gli sviluppatori possono entrare a far parte di [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) e ottenere così una sottoscrizione gratuita a Enterprise Mobility Suite, che include Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerazioni per scenari specifici

Le informazioni seguenti si applicano solo a questi scenari di accesso condizionale:

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js

Le sezioni seguenti illustrano scenari comuni più complessi. Il concetto principale funzionamento principio è l'accesso condizionale i criteri vengono valutati al momento che viene richiesto il token per il servizio di cui è applicato un criterio di accesso condizionale.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App che esegue il flusso on-behalf-of

In questo scenario viene illustrato il caso in cui un'app nativa chiama un'API o un servizio Web. Questo servizio è a sua volta, il flusso "on-behalf-of" per chiamare un servizio downstream. In questo caso sono stati applicati criteri di accesso condizionale al servizio downstream (API Web 2) e viene usata un'app nativa invece di un'app demon/server. 

![Diagramma per le app che eseguono il flusso on-behalf-of](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

La richiesta di token iniziale per l'API Web 1 non comporta la richiesta all'utente finale dell'autenticazione a più fattori in quanto l'API Web 1 non sempre raggiunge l'API downstream. Quando l'API Web 1 prova a richiedere un token per conto dell'utente per l'API Web 2, la richiesta non riesce perché l'utente non ha eseguito l'accesso con l'autenticazione a più fattori.

Azure AD restituisce una risposta HTTP con alcuni dati interessanti:

> [!NOTE]
> In questa istanza c'è una descrizione dell'errore di autenticazione a più fattori, ma ci sono molti elementi `interaction_required` possibili correlati all'accesso condizionale.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Nell'API Web 1 viene intercettato l'errore `error=interaction_required` e viene inviata nuovamente la richiesta `claims` all'app desktop. A questo punto, l'app desktop può chiamare di nuovo `acquireToken()` e aggiungere la richiesta `claims` come parametro di stringa di query aggiuntivo. La nuova richiesta impone all'utente l'uso dell'autenticazione a più fattori e quindi invia il nuovo token all'API Web 1 e completa il flusso on-behalf-of.

Per provare questo scenario, vedere l'[esempio di codice .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Viene illustrato come passare la richiesta di attestazioni dall'API Web 1 all'app nativa e costruire una nuova richiesta all'interno dell'app client.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App che accede a più servizi

In questo scenario viene illustrato il caso in cui un'app Web accede a due servizi di cui uno ha criteri di accesso condizionale assegnati. A seconda della logica dell'app, potrebbe esserci un percorso in cui l'app non richiede l'accesso a entrambi i servizi Web. In questo scenario, l'ordine in cui si richiede un token ha un ruolo importante nell'esperienza dell'utente finale.

Si supponga di avere i servizi Web A e B e che al servizio Web B siano applicati i criteri di accesso condizionale. Mentre la richiesta di autenticazione interattiva iniziale richiede il consenso per entrambi i servizi, i criteri di accesso condizionale non sono necessari in tutti i casi. Se l'app richiede un token per il servizio Web B, i criteri vengono richiamati e anche le richieste successive per il servizio Web A hanno esito positivo, come indicato di seguito.

![Diagramma di flusso per le app che accedono a più servizi](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

In alternativa, se l'app richiede inizialmente un token per il servizio Web A, l'utente finale non richiama i criteri di accesso condizionale. In questo modo, lo sviluppatore dell'app può controllare l'esperienza dell'utente finale senza imporre che i criteri di accesso condizionale vengano richiamati in tutti i casi. La situazione diventa complessa se l'app successivamente richiede un token per il servizio Web B. A questo punto, l'utente finale deve rispettare i criteri di accesso condizionale. Quando l'app prova a eseguire `acquireToken`, può venire generato l'errore seguente (illustrato nel diagramma seguente):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App che accede a più servizi e richiede un nuovo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se l'app usa la libreria ADAL, in caso di errore di acquisizione del token viene sempre eseguito un nuovo tentativo in modo interattivo. Quando si verifica questa richiesta interattiva, l'utente finale ha la possibilità di rispettare l'accesso condizionale. Ciò è vero, a meno che la richiesta non è un `AcquireTokenSilentAsync` oppure `PromptBehavior.Never` caso in cui l'app deve eseguire un interattiva ```AcquireToken``` richiesta per offrire all'utente finale la possibilità di conformarsi ai criteri.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: App a pagina singola che usa ADAL.js

In questo scenario viene illustrato il caso di un'app a pagina singola (SPA) che usa ADAL.js per chiamare un'API Web protetta con accesso condizionale. Si tratta di un'architettura semplice, ma ci sono alcuni aspetti da prendere in considerazione in caso di sviluppo incentrato sull'accesso condizionale.

In ADAL.js ci sono poche funzioni che ottengono token: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()` ottiene un token ID tramite una richiesta di accesso interattiva, ma non ottiene token di accesso per alcun servizio (inclusa un'API Web protetta con accesso condizionale).
* È quindi possibile usare `acquireToken(…)` per ottenere automaticamente un token di accesso, senza visualizzazione dell'interfaccia utente in nessun caso.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` vengono entrambi usati per richiedere in modo interattivo un token per una risorsa, quindi l'interfaccia utente di accesso viene sempre visualizzata.

Quando un'app necessita di un token di accesso per chiamare un'API Web, viene eseguito un tentativo di chiamata a `acquireToken(…)`. Se la sessione del token è scaduta o è necessaria la conformità ai criteri di accesso condizionale, la funzione *acquireToken* ha esito negativo e l'app usa `acquireTokenPopup()` o `acquireTokenRedirect()`.

![Diagramma di flusso per le app a pagina singola che usano ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Verrà ora esaminato un esempio con lo scenario di accesso condizionale. L'utente finale è appena arrivato nel sito e non ha una sessione. Si esegue una chiamata a `login()` e si ottiene un token ID senza autenticazione a più fattori. L'utente preme quindi un pulsante che comporta una richiesta di dati dall'app a un'API Web. L'app prova a eseguire una chiamata a `acquireToken()`, che ha però esito negativo perché l'utente non ha eseguito ancora l'autenticazione a più fattori e deve rispettare i criteri di accesso condizionale.

Azure AD invia la risposta HTTP seguente:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

L'app deve intercettare l'errore `error=interaction_required`. L'applicazione può quindi usare `acquireTokenPopup()` o `acquireTokenRedirect()` sulla stessa risorsa. L'utente deve eseguire l'autenticazione a più fattori. Dopo che l'utente ha completato l'autenticazione a più fattori, viene emesso un nuovo token di accesso per l'app per la risorsa richiesta.

Per provare questo scenario, vedere l'[esempio di codice on-behalf-of JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Questo esempio di codice usa i criteri di accesso condizionale e l'API Web registrata in precedenza con JS SPA per illustrare questo scenario. Mostra come gestire correttamente la richiesta di attestazioni e ottenere un token di accesso che può essere usato per l'API Web. In alternativa, vedere l'[esempio di codice Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) generale, per informazioni su Angular SPA

## <a name="see-also"></a>Vedere anche 

* Per altre informazioni sulle funzionalità, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Per altri esempi di codice di Azure AD, vedere il [repository GitHub di esempi di codice](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Per altre informazioni su ADAL SDK e sull'accesso alla documentazione di riferimento, vedere la [guida alle librerie](active-directory-authentication-libraries.md).
* Per altre informazioni sugli scenari multi-tenant, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md).
