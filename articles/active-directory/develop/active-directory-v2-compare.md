---
title: Endpoint 2.0 di Azure AD | Microsoft Docs
description: Confronto tra gli endpoint originali di Azure AD e gli endpoint della versione 2.0.
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 8a8f8af9b23c66461385a73f80c148a2fdd652d5


---
# <a name="whats-different-about-the-v20-endpoint"></a>Differenze dell'endpoint 2.0.
Se si ha familiarità con Azure Active Directory o si sono svolte attività di integrazione di app con Azure AD in passato, si noteranno alcune differenze inaspettate nell'endpoint 2.0.  Questo documento descrive tali differenze per una maggiore comprensione da parte dell'utente.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è consigliabile usare l'endpoint 2.0, leggere l'articolo relativo alle [limitazioni della versione 2.0](active-directory-v2-limitations.md).
>
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Account Microsoft e account Azure AD
L'endpoint 2.0 consente agli sviluppatori di scrivere app che supportano l'accesso da account Microsoft e Azure AD mediante un unico endpoint di autorizzazione.  In questo modo, è possibile scrivere l'app senza tenere conto dell'account usato per l'accesso. Ossia, l'app non contiene informazioni riguardo al tipo di account con cui accede l'utente.  È ovviamente *possibile* inserire nell'app informazioni sul tipo di account usato in una determinata sessione, ma è facoltativo.

Se, ad esempio, l'app chiama [Microsoft Graph](https://graph.microsoft.io), per gli utenti aziendali saranno disponibili funzionalità e dati aggiuntivi, quali i siti di SharePoint o i dati di Directory.  Per numerose azioni, ad esempio la [lettura di un messaggio di posta elettronica dell'utente](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), il codice può tuttavia essere scritto esattamente nello stesso modo per gli account Microsoft e quelli Azure AD.  

L'integrazione dell'app con account Microsoft e Azure AD è ora un processo semplice.  È possibile usare un unico set di endpoint, un'unica libreria e un'unica registrazione dell'app per accedere ai vantaggi di livello consumer e aziendale.  Per altre informazioni sull'endpoint 2.0, consultare [la panoramica](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nuovo portale di registrazione delle app
L'endpoint 2.0 può essere registrato solo in una nuova posizione: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Questo è il portale in cui è possibile ottenere un ID applicazione, personalizzare l'aspetto della pagina di accesso dell'app e svolgere molte altre operazioni.  Per accedere al portale è sufficiente un account con tecnologia Microsoft, personale oppure dell'azienda o dell'istituto di istruzione.  

In futuro altre funzionalità verranno aggiunte al portale di registrazione delle app.  L'obiettivo è che il portale diventi il nuovo percorso in cui gestire qualsiasi elemento correlato alle app Microsoft.

## <a name="one-app-id-for-all-platforms"></a>ID app univoco per tutte le piattaforme
È possibile che nel servizio Azure Active Directory originale siano state registrate app diverse per un unico progetto.  È stato necessario usare registrazioni dell'app separate per i client nativi e le app web:

![Interfaccia utente della registrazione dell'applicazione precedente](../../media/active-directory-v2-flows/old_app_registration.PNG)

Se, ad esempio, sono stati creati sia un sito Web sia un'app per iOS, è stato necessario registrarli separatamente, usando due diversi ID applicazione.  Se si disponeva di un sito Web e un'API Web back-end, è possibile che ogni elemento sia stato registrato come un'app separata in Azure AD.  Se si disponeva di un'app per iOS e di un'app per Android, è possibile che siano state registrate due app diverse.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Ora invece, è sufficiente registrare una sola app e disporre di un solo ID applicazione per ogni progetto.  È possibile aggiungere più "piattaforme" a un progetto e fornire i dati appropriati per ogni piattaforma aggiunta.  Naturalmente, è possibile creare quante app si desidera in base alle esigenze, ma nella maggior parte dei casi è necessario un solo ID applicazione.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

L'obiettivo è ottenere un'esperienza di sviluppo e gestione delle app più semplificata e creare una vista più consolidata del progetto al quale si lavora.

## <a name="scopes-not-resources"></a>Ambiti e non risorse
Nel servizio Azure AD originale un'app può comportarsi come una **risorsa**o come un destinatario di token.  Una risorsa può definire diversi **ambiti** o autorizzazioni **oAuth2Permissions** che può riconoscere, consentendo alle app client di richiedere token per la risorsa per un determinato set di ambiti.  Si consideri l'API Graph di Azure AD come esempio di una risorsa:

* Identificatore della risorsa o `AppID URI`: `https://graph.windows.net/`
* Ambiti o `OAuth2Permissions`: `Directory.Read`, `Directory.Write` e così via  

Quanto descritto si applica all'endpoint 2.0.  Un'app può comunque comportarsi come una risorsa, definire gli ambiti ed essere identificata da un URI.  Le app client possono richiedere ancora l'accesso a questi ambiti,  tuttavia, è stata modificata la modalità con cui un client esegue la richiesta delle autorizzazioni.  In precedenza, l'aspetto di una richiesta di autorizzazione OAuth 2.0 a Azure AD era simile al seguente:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

dove il parametro della **risorsa** indicava la risorsa per la quale l'app client richiedeva l'autorizzazione.  Azure AD calcolava le autorizzazioni obbligatorie per l'app in base alla configurazione statica nel portale di Azure e rilasciava i token di conseguenza.  Ora invece, l'aspetto della stessa richiesta di autorizzazione OAuth 2.0 è simile al seguente:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

dove il parametro dell' **ambito** indica per quali risorse e autorizzazioni l'app richiede l'autorizzazione. La risorsa desiderata è ancora presente nella richiesta, semplicemente è inclusa in ognuno dei valori del parametro di ambito.  Tale uso del parametro di ambito consente all'endpoint 2.0 di essere più conforme alla specifica OAuth 2.0 e alle pratiche comuni del settore.  Consente inoltre alle app di eseguire il [consenso incrementale](#incremental-and-dynamic-consent)descritto nella sezione seguente.

## <a name="incremental-and-dynamic-consent"></a>Consenso incrementale e dinamico
Per le app registrate nel servizio Azure AD disponibile a livello generale era necessario specificare le autorizzazioni OAuth 2.0 obbligatorie nel portale di Azure al momento della creazione dell'app:

![Interfaccia utente della registrazione delle autorizzazioni](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

Le autorizzazioni richieste da un'app venivano configurate **staticamente**.  Se da un lato ciò consentiva che la configurazione dell'app esistesse nel portale di Azure e che il codice fosse chiaro e semplice, dall'altro presentava alcuni problemi per gli sviluppatori:

* Era necessario definire in fase di creazione tutte le autorizzazioni che sarebbero state necessarie all'app.  La possibilità di aggiungere le autorizzazioni in fasi successive era un processo difficile.
* Era necessario definire in anticipo tutte le risorse a cui l'app avrebbe dovuto accedere.  Era difficile creare app che potessero accedere a un numero arbitrario di risorse.
* Era necessario richiedere al primo accesso dell'utente tutte le autorizzazioni di cui l'app avrebbe avuto bisogno.  In alcuni casi ciò comportava l'esigenza di creare un lungo elenco di autorizzazioni che dovevano essere approvate dall'utente al primo accesso, causando spesso la rinuncia all'iscrizione da parte di quest'ultimo.

Nell'endpoint 2.0 è possibile specificare le autorizzazioni necessarie per l'app **dinamicamente**, in fase di esecuzione, durante l'utilizzo regolare dell'app.  A tale scopo, è possibile specificare gli ambiti necessari per l'app in qualsiasi momento, includendoli nel parametro `scope` di una richiesta di autorizzazione:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Il codice precedente richiede l'autorizzazione per l'app di leggere i dati di directory di un utente di Azure AD, nonché di scrivere dati in tale directory.  Se l'utente ha acconsentito a tali autorizzazioni in precedenza per questa particolare app, sarà sufficiente immettere le proprie credenziali ed eseguire l'accesso all'app.  Se l'utente non ha acconsentito a nessuna di queste autorizzazioni, l'endpoint 2.0 chiederà all'utente di fornire il consenso.  Per altre informazioni, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md).

Consentendo a un'app di richiedere le autorizzazioni in modo dinamico tramite il parametro `scope` gli sviluppatori hanno il controllo completo dell'esperienza dell'utente.  Se si desidera, è possibile scegliere di agire d'anticipo chiedendo il consenso per tutte le autorizzazioni in un'unica richiesta iniziale.  In alternativa, se l'app richiede un numero elevato di autorizzazioni, è possibile scegliere di raccogliere tali autorizzazioni dall'utente in modo incrementale, man mano che determinate funzionalità dell'app vengono usate.

## <a name="well-known-scopes"></a>Ambiti conosciuti
#### <a name="offline-access"></a>Accesso offline
L'endpoint 2.0 può richiedere l'uso di una nuova autorizzazione nota per le app: l'ambito `offline_access` .  Tutte le app dovranno richiedere questa autorizzazione, se devono accedere alle risorse per conto di un utente per un periodo di tempo prolungato, anche se l'utente non sta usando attivamente l'app.  L'utente visualizzerà l'ambito `offline_access` in finestre di dialogo di consenso, quali "Accedere ai dati offline", che dovrà accettare.  La richiesta dell'autorizzazione `offline_access` consentirà all'app Web di ricevere token di aggiornamento di OAuth 2.0 dall'endpoint 2.0.  I token di aggiornamento hanno una durata elevata e possono essere scambiati con nuovi token di accesso di OAuth 2.0 per periodi prolungati di accesso.  

Se l'app non richiede l'ambito `offline_access`, non riceverà i token di aggiornamento.  Pertanto, se si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione di OAuth 2.0, si riceverà solo un token di accesso dall'endpoint `/token`.  Tale token di accesso rimarrà valido per un breve periodo di tempo (in genere un'ora), per poi scadere.  A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione.  Durante il reindirizzamento, l'utente può o meno avere esigenza di immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni, a seconda del tipo di app.

Per altre informazioni su OAuth 2.0, token di aggiornamento e token di accesso, vedere le [informazioni di riferimento sui protocolli della versione 2.0](active-directory-v2-protocols.md).

#### <a name="openid-profile--email"></a>OpenID, profilo e indirizzo di posta elettronica
Nel servizio di Azure Active Directory originale il flusso di accesso più semplice di OpenID Connect fornisce molte di informazioni sull'utente nel token ID risultante.  Le attestazioni nel token ID includono, ad esempio, il nome dell'utente, il nome utente preferito, l'indirizzo di posta elettronica, l'ID oggetto e altro ancora.

Attualmente vengono limitate le informazioni a cui l'app ha accesso tramite l'ambito `openid`.  L'ambito "openid" consente all'app di far accedere l'utente e di ricevere un identificatore specifico dell'app per l'utente.  Per ottenere informazioni personali sull'utente nell'app, questa dovrà richiedere autorizzazioni aggiuntive all'utente.  Vengono introdotti due nuovi ambiti, `email` e `profile`, che consentono di eseguire questa operazione.

L'ambito `email` è molto semplice: consente all'app di accedere all'indirizzo di posta elettronica primario dell'utente tramite l'attestazione `email` nell'id_token.  L'ambito `profile` concede all'app l'accesso a tutte le altre informazioni di base sull'utente, vale a dire il nome, il nome utente preferito, l'ID oggetto e così via.

Questo consente di creare il codice dell'app in modo che la divulgazione delle informazioni sia minima, chiedendo all'utente solo il set di informazioni necessario per il funzionamento dell'app.  Per altre informazioni su questi ambiti, vedere l'articolo relativo al [riferimento all'ambito della versione 2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Attestazioni nei token
Le attestazioni nei token rilasciati dall'endpoint 2.0 non sono identiche a quelle nei token rilasciati dagli endpoint di Azure AD disponibile a livello generale. Le app che eseguono la migrazione al nuovo servizio non devono presupporre che esista un'attestazione particolare nei token ID o di accesso.   I token rilasciati dall'endpoint 2.0 sono compatibili con le specifiche di OAuth 2.0 e OpenID Connect, ma possono seguire una semantica diversa rispetto al servizio Azure AD disponibile a livello generale.

Per altre informazioni sulle attestazioni specifiche generate nei token 2.0, vedere l'articolo relativo al [riferimento al token della versione 2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Limitazioni
Esistono alcune limitazioni da tenere in considerazione quando si usa l'endpoint 2.0.  Fare riferimento al [documento relativo alle limitazioni della versione 2.0](active-directory-v2-limitations.md) per verificare se una di queste restrizioni si applica a un particolare scenario.



<!--HONumber=Jan17_HO3-->


