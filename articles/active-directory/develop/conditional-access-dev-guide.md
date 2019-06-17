---
title: Linee guida per sviluppatori per Azure Active Directory l'accesso condizionale
description: Linee guida per sviluppatori e gli scenari per l'accesso condizionale di Azure AD
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
ms.openlocfilehash: 9e4e0eb830d5ede910e72ec3193cfd613561811b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111530"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Linee guida per sviluppatori per Azure Active Directory l'accesso condizionale

La funzionalità di accesso condizionale in Azure Active Directory (Azure AD) offre uno dei vari modi in cui è possibile usare per proteggere l'app e proteggere un servizio. L'accesso condizionale consente agli sviluppatori e i clienti aziendali di proteggere i servizi in una vasta gamma di modi, tra cui:

* Autenticazione a più fattori
* Autorizzazione dell'accesso a servizi specifici solo per dispositivi registrati in Intune
* Limitazione delle posizioni dell'utente e degli intervalli IP

Per altre informazioni su tutte le funzionalità di accesso condizionale, vedere [accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Per gli sviluppatori che creano App per Azure AD, questo articolo illustra come è possibile usare l'accesso condizionale e scoprirai anche sull'impatto dell'accesso alle risorse che non è necessario controllare che potrebbe essere applicati i criteri di accesso condizionale. L'articolo esamina anche le implicazioni dell'accesso condizionale nel flusso on-behalf-of, App web, l'accesso a Microsoft Graph e chiama le API.

Si presuppone una conoscenza delle app a [tenant singolo](quickstart-v1-integrate-apps-with-azure-ad.md) e [multi-tenant](howto-convert-app-to-be-multi-tenant.md) e dei [modelli di autenticazione comuni](authentication-scenarios.md).

## <a name="how-does-conditional-access-impact-an-app"></a>Impatto di un'app ha accesso condizionale

### <a name="app-types-impacted"></a>Tipi di app interessati

Nella maggior parte dei casi, l'accesso condizionale non viene modificato il comportamento di un'app o richiede modifiche da parte dello sviluppatore. Solo in alcuni casi quando un'app in modo indiretto o automatico richiede un token per un servizio, un'app richiede modifiche al codice per gestire l'accesso condizionale "problematiche". L'operazione può essere semplice quanto l'esecuzione di una richiesta di accesso interattiva.

In particolare, gli scenari seguenti richiedono codice per gestire l'accesso condizionale "problematiche":

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js
* App Web che chiamano una risorsa

Accesso condizionale, i criteri possono essere applicati all'app, ma possono anche essere applicati a un'API web consente di accedere all'app. Per altre informazioni su come configurare un criterio di accesso condizionale, vedere [Guida introduttiva: Richiedere l'autenticazione MFA per App specifiche con Azure Active Directory l'accesso condizionale](../conditional-access/app-based-mfa.md).

A seconda dello scenario, un cliente aziendale può applicare e rimuovere criteri di accesso condizionale in qualsiasi momento. Affinché l'app continui a funzionare quando vengono applicati nuovi criteri, è necessario implementare la gestione delle richieste. Gli esempi seguenti illustrano la gestione delle richieste.

### <a name="conditional-access-examples"></a>Esempi di accesso condizionali

Alcuni scenari richiedono modifiche al codice per gestire l'accesso condizionale, mentre altri funzionano senza. Di seguito sono proposti alcuni scenari di uso dell'accesso condizionale per eseguire l'autenticazione a più fattori che aiutano a comprendere la differenza.

* Si sta creando un'app iOS a tenant singolo e applicare un criterio di accesso condizionale. L'app consente l'accesso di un utente e non richiede l'accesso a un'API. Quando l'utente accede, i criteri vengono richiamati automaticamente e l'utente deve eseguire l'autenticazione a più fattori (MFA). 
* Si sta creando un'app nativa che usa un servizio di livello intermedio per accedere a un'API downstream. Un cliente aziendale in azienda che usa questa app applica i criteri all'API downstream. Quando un utente finale esegue l'accesso, l'app nativa richiede l'accesso al livello intermedio e invia il token. Il livello intermedio esegue il flusso on-behalf-of per richiedere l'accesso all'API downstream. A questo punto, viene presentata una richiesta di attestazioni al livello intermedio. Il livello intermedio invia la richiesta di verifica all'App nativa, che deve essere conforme ai criteri di accesso condizionale.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph contiene considerazioni speciali durante la compilazione di App in ambienti di accesso condizionale. In generale, i meccanismi di accesso condizionale si comportano allo stesso, ma i criteri visualizzati utenti si baseranno sui dati sottostanti che dal grafico richiesto dall'app. 

In particolare, tutti gli ambiti di Microsoft Graph rappresentano alcuni set di dati che possono avere singolarmente i criteri applicati. Poiché i criteri di accesso condizionale vengono assegnati i set di dati specifico, Azure AD verrà applicare i criteri di accesso condizionale in base ai dati protetti da Graph - anziché del grafico stesso.

Ad esempio, se un'app richiede i seguenti ambiti di Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Un'app può prevedere agli utenti di soddisfare tutti i criteri impostati in Bookings ed Exchange. Alcuni ambiti possono eseguire il mapping a più set di dati se concede l'accesso. 

### <a name="complying-with-a-conditional-access-policy"></a>Conformità ai criteri di accesso condizionale

Per diverse topologie di app, un criterio di accesso condizionale viene valutato quando viene stabilita la sessione. Come criteri di accesso condizionale operano a livello di App e servizi, il punto in corrispondenza del quale viene richiamato dipende molto lo scenario in cui che si sta cercando di realizzare.

Quando l'app tenta di accedere a un servizio con un criterio di accesso condizionale, è possibile riscontrare una richiesta di accesso condizionale. La richiesta è codificata nel `claims` parametro che viene ricevuta una risposta da Azure AD. Ecco un esempio del parametro della richiesta: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Gli sviluppatori possono aggiungere questa richiesta a una nuova richiesta per Azure AD. L'utente finale per eseguire qualsiasi azione necessaria per la conformità con i criteri di accesso condizionale viene passato questo stato. Negli scenari seguenti vengono illustrate informazioni specifiche sull'errore e su come estrarre il parametro.

## <a name="scenarios"></a>Scenari

### <a name="prerequisites"></a>Prerequisiti

Accesso condizionale di Azure AD è una funzionalità inclusa nella [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Per altre informazioni sui requisiti relativi alle licenza, vedere [Report Utilizzo senza licenza](../active-directory-conditional-access-unlicensed-usage-report.md). Gli sviluppatori possono entrare a far parte di [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) e ottenere così una sottoscrizione gratuita a Enterprise Mobility Suite, che include Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Considerazioni per scenari specifici

Le informazioni seguenti si applicano solo in questi scenari di accesso condizionale:

* App che eseguono il flusso on-behalf-of
* App che accedono a più servizi o risorse
* App a pagina singola che usano ADAL.js

Le sezioni seguenti illustrano scenari comuni più complessi. Il concetto principale funzionamento principio è criteri di accesso condizionale vengono valutati al momento che viene richiesto il token per il servizio di cui è applicato un criterio di accesso condizionale.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenario: App che esegue il flusso on-behalf-of

In questo scenario viene illustrato il caso in cui un'app nativa chiama un'API o un servizio Web. Questo servizio è a sua volta, il flusso "on-behalf-of" per chiamare un servizio downstream. In questo caso, è stato applicato il criterio di accesso condizionale per il servizio downstream (API Web 2) e usa un'app nativa invece di un'app Demon/server. 

![Diagramma per le app che eseguono il flusso on-behalf-of](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

La richiesta di token iniziale per l'API Web 1 non comporta la richiesta all'utente finale dell'autenticazione a più fattori in quanto l'API Web 1 non sempre raggiunge l'API downstream. Quando l'API Web 1 prova a richiedere un token per conto dell'utente per l'API Web 2, la richiesta non riesce perché l'utente non ha eseguito l'accesso con l'autenticazione a più fattori.

Azure AD restituisce una risposta HTTP con alcuni dati interessanti:

> [!NOTE]
> In questa istanza è una descrizione dell'errore di autenticazione a più fattori, ma è presente un'ampia gamma di `interaction_required` possibili correlati all'accesso condizionale.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Nell'API Web 1 viene intercettato l'errore `error=interaction_required` e viene inviata nuovamente la richiesta `claims` all'app desktop. A questo punto, l'app desktop può chiamare di nuovo `acquireToken()` e aggiungere la richiesta `claims` come parametro di stringa di query aggiuntivo. La nuova richiesta impone all'utente l'uso dell'autenticazione a più fattori e quindi invia il nuovo token all'API Web 1 e completa il flusso on-behalf-of.

Per provare questo scenario, vedere l'[esempio di codice .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Viene illustrato come passare la richiesta di attestazioni dall'API Web 1 all'app nativa e costruire una nuova richiesta all'interno dell'app client.

## <a name="scenario-app-accessing-multiple-services"></a>Scenario: App che accede a più servizi

In questo scenario viene illustrato il caso in cui un'app web accede a due servizi in uno dei quali ha un criterio di accesso condizionale assegnato. A seconda della logica dell'app, potrebbe esserci un percorso in cui l'app non richiede l'accesso a entrambi i servizi Web. In questo scenario, l'ordine in cui si richiede un token ha un ruolo importante nell'esperienza dell'utente finale.

Si supponga di avere i servizi web A e B e servizio web B siano applicati i criteri di accesso condizionale. Mentre la richiesta di autenticazione interattiva iniziale richiede il consenso per entrambi i servizi, i criteri di accesso condizionale non sono necessario in tutti i casi. Se l'app richiede un token per il servizio Web B, i criteri vengono richiamati e anche le richieste successive per il servizio Web A hanno esito positivo, come indicato di seguito.

![Diagramma di flusso per le app che accedono a più servizi](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

In alternativa, se l'app richiede inizialmente un token per il servizio web, l'utente finale non richiama il criterio di accesso condizionale. In questo modo lo sviluppatore dell'app al controllo all'utente finale di esperienza e non imporre i criteri di accesso condizionale di essere richiamati in tutti i casi. La situazione diventa complessa se l'app successivamente richiede un token per il servizio web B. A questo punto, l'utente finale deve rispettare i criteri di accesso condizionale. Quando l'app prova a eseguire `acquireToken`, può venire generato l'errore seguente (illustrato nel diagramma seguente):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App che accede a più servizi e richiede un nuovo token](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Se l'app usa la libreria ADAL, in caso di errore di acquisizione del token viene sempre eseguito un nuovo tentativo in modo interattivo. Quando si verifica questa richiesta interattiva, all'utente finale ha la possibilità di rispettare l'accesso condizionale. Ciò è vero, a meno che la richiesta non è un `AcquireTokenSilentAsync` oppure `PromptBehavior.Never` caso in cui l'app deve eseguire un interattiva ```AcquireToken``` richiesta per offrire all'utente finale la possibilità di conformarsi ai criteri.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenario: App a pagina singola che usa ADAL.js

In questo scenario viene illustrato il caso quando si dispone di un'app a singola pagina (SPA), Usa adal. js per chiamare un'API web protetta con accesso condizionale. Si tratta di un'architettura semplice ma ci sono alcuni aspetti che devono essere prese in considerazione durante lo sviluppo incentrato sull'accesso condizionale.

In ADAL.js ci sono poche funzioni che ottengono token: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` e `acquireTokenRedirect(…)`.

* `login()` Ottiene un token ID tramite una richiesta di accesso interattiva, ma non ottiene token di accesso per qualsiasi servizio (tra cui un'API web protetta con accesso condizionale).
* È quindi possibile usare `acquireToken(…)` per ottenere automaticamente un token di accesso, senza visualizzazione dell'interfaccia utente in nessun caso.
* `acquireTokenPopup(…)` e `acquireTokenRedirect(…)` vengono entrambi usati per richiedere in modo interattivo un token per una risorsa, quindi l'interfaccia utente di accesso viene sempre visualizzata.

Quando un'app necessita di un token di accesso per chiamare un'API Web, viene eseguito un tentativo di chiamata a `acquireToken(…)`. Se la sessione del token è scaduta o si devono essere conformi ai criteri di accesso condizionale, il *acquireToken* funzione ha esito negativo e l'app Usa `acquireTokenPopup()` o `acquireTokenRedirect()`.

![Diagramma di flusso per le app a pagina singola che usano ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Di seguito viene illustrato un esempio con questo scenario di accesso condizionale. L'utente finale è appena arrivato nel sito e non ha una sessione. Si esegue una chiamata a `login()` e si ottiene un token ID senza autenticazione a più fattori. L'utente preme quindi un pulsante che comporta una richiesta di dati dall'app a un'API Web. L'app prova a eseguire un `acquireToken()` chiamata ma ha esito negativo perché l'utente non ha eseguito ancora l'autenticazione a più fattori e deve rispettare i criteri di accesso condizionale.

Azure AD invia la risposta HTTP seguente:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

L'app deve intercettare l'errore `error=interaction_required`. L'applicazione può quindi usare `acquireTokenPopup()` o `acquireTokenRedirect()` sulla stessa risorsa. L'utente deve eseguire l'autenticazione a più fattori. Dopo che l'utente ha completato l'autenticazione a più fattori, viene emesso un nuovo token di accesso per l'app per la risorsa richiesta.

Per provare questo scenario, vedere l'[esempio di codice on-behalf-of JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Questo esempio di codice Usa le API web registrata in precedenza con JS SPA per illustrare questo scenario e i criteri di accesso condizionale. Mostra come gestire correttamente la richiesta di attestazioni e ottenere un token di accesso che può essere usato per l'API Web. In alternativa, vedere l'[esempio di codice Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) generale, per informazioni su Angular SPA

## <a name="see-also"></a>Vedere anche

* Per altre informazioni sulle funzionalità, vedere [Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Per altri esempi di codice di Azure AD, vedere il [repository GitHub di esempi di codice](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Per altre informazioni su ADAL SDK e sull'accesso alla documentazione di riferimento, vedere la [guida alle librerie](active-directory-authentication-libraries.md).
* Per altre informazioni sugli scenari multi-tenant, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant](howto-convert-app-to-be-multi-tenant.md).
