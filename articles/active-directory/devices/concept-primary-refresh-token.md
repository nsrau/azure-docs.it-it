---
title: Token di aggiornamento primario (PRT) e Azure AD-Azure Active Directory
description: Qual è il ruolo di e in che modo è possibile gestire il token di aggiornamento primario (PRT) in Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672641"
---
# <a name="what-is-a-primary-refresh-token"></a>Che cos'è un token di aggiornamento primario?

Un token di aggiornamento primario (PRT) è un elemento chiave dell'autenticazione Azure AD nei dispositivi Windows 10, iOS e Android. Si tratta di un token JSON Web (JWT) rilasciato appositamente per i broker di token della prima parte Microsoft per abilitare il Single Sign-On (SSO) tra le applicazioni usate in tali dispositivi. In questo articolo vengono fornite informazioni dettagliate sul modo in cui un PRT viene emesso, usato e protetto nei dispositivi Windows 10.

Questo articolo presuppone che siano già stati illustrati i diversi stati dei dispositivi disponibili in Azure AD e il funzionamento di Single Sign-On in Windows 10. Per ulteriori informazioni sui dispositivi in Azure AD, vedere l'articolo [che cos'è la gestione dei dispositivi in Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Terminologia e componenti principali

I componenti di Windows seguenti svolgono un ruolo chiave nella richiesta e nell'uso di un PRT:

* **Cloud authentication provider** (CloudAP): CloudAP è il provider di autenticazione moderno per l'accesso a Windows, che verifica la registrazione degli utenti in un dispositivo Windows 10. CloudAP fornisce un Framework di plug-in che i provider di identità possono compilare per consentire l'autenticazione a Windows usando le credenziali del provider di identità.
* **Web Account Manager** (WAM): WAM è il broker di token predefinito nei dispositivi Windows 10. WAM fornisce anche un Framework di plug-in che i provider di identità possono compilare e abilitare l'accesso SSO alle applicazioni che si basano su tale provider di identità.
* **Azure ad plug**-in CloudAP: un plug-in specifico Azure ad basato sul Framework CloudAP, che verifica le credenziali utente con Azure ad durante l'accesso a Windows.
* **Plug-in Azure ad WAM**: un plug-in specifico Azure ad basato sul Framework WAM, che consente l'accesso SSO alle applicazioni che si basano su Azure ad per l'autenticazione.
* **Dsreg**: componente specifico di Azure ad in Windows 10, che gestisce il processo di registrazione del dispositivo per tutti gli Stati dei dispositivi.
* **Trusted Platform Module** (TPM): un TPM è un componente hardware integrato in un dispositivo che fornisce funzioni di sicurezza basate su hardware per i segreti di utenti e dispositivi. Per ulteriori informazioni, vedere l'articolo [Panoramica della tecnologia Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Che cosa contiene il PRT?

Un PRT contiene attestazioni generalmente contenute in un token di aggiornamento Azure AD. Inoltre, nel PRT sono incluse alcune attestazioni specifiche del dispositivo. Ecco quali sono:

* **ID dispositivo**: un PRT viene rilasciato a un utente in un dispositivo specifico. L'attestazione `deviceID` ID dispositivo determina il dispositivo su cui è stato emesso il PRT. Questa attestazione viene successivamente rilasciata ai token ottenuti tramite il PRT. L'attestazione ID dispositivo viene usata per determinare l'autorizzazione per l'accesso condizionale in base allo stato del dispositivo o alla conformità.
* **Chiave di sessione**: la chiave della sessione è una chiave simmetrica crittografata, generata dal servizio di autenticazione Azure ad, emessa come parte del PRT. La chiave della sessione funge da prova del possesso quando un PRT viene usato per ottenere i token per altre applicazioni.

### <a name="can-i-see-whats-in-a-prt"></a>È possibile vedere cosa c'è in un PRT?

Un PRT è un BLOB opaco inviato da Azure AD il cui contenuto non è noto ad alcun componente client. Non è possibile visualizzare ciò che si trova all'interno di un PRT.

## <a name="how-is-a-prt-issued"></a>Come viene emesso un PRT?

La registrazione del dispositivo è un prerequisito per l'autenticazione basata su dispositivo in Azure AD. Un PRT viene rilasciato agli utenti solo sui dispositivi registrati. Per informazioni più dettagliate sulla registrazione dei dispositivi, vedere l'articolo [Windows Hello for business e registrazione del dispositivo](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante la registrazione del dispositivo, il componente dsreg genera due set di coppie di chiavi crittografiche:

* Chiave del dispositivo (dkpub/dkpriv)
* Chiave di trasporto (tkpub/tkpriv)

Le chiavi private vengono associate al TPM del dispositivo se il dispositivo dispone di un TPM valido e funzionante, mentre le chiavi pubbliche vengono inviate a Azure AD durante il processo di registrazione del dispositivo. Queste chiavi vengono usate per convalidare lo stato del dispositivo durante le richieste PRT.

Il PRT viene emesso durante l'autenticazione utente in un dispositivo Windows 10 in due scenari:

* **Azure ad** unito o **Azure ad ibrido Unito**: viene emesso un PRT durante l'accesso a Windows quando un utente accede con le credenziali aziendali. Viene emesso un PRT con tutte le credenziali supportate di Windows 10, ad esempio password e Windows Hello for business. In questo scenario Azure AD plug-in CloudAP è l'autorità primaria per il PRT.
* **Azure ad dispositivo registrato**: viene emesso un PRT quando un utente aggiunge un account di lavoro secondario al dispositivo Windows 10. Gli utenti possono aggiungere un account a Windows 10 in due modi diversi:  
   * Aggiunta di un account tramite l' **uso di questo account ovunque in questo dispositivo** prompt del dispositivo dopo l'accesso a un'app (ad esempio Outlook)
   * Aggiunta di un account da **Impostazioni** > **account** > **Accedi all'ufficio o all'Istituto di istruzione** > **Connetti**

In Azure AD scenari di dispositivi registrati, il plug-in Azure AD WAM è l'autorità primaria per il PRT perché l'accesso a Windows non viene effettuato con questo account Azure AD.

> [!NOTE]
> i provider di identità di terze parti devono supportare il protocollo WS-Trust per abilitare il rilascio PRT nei dispositivi Windows 10. Senza WS-Trust, il PRT non può essere emesso per gli utenti in Azure AD ibrido aggiunti o Azure AD dispositivi aggiunti

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual è la durata di un PRT?

Una volta emesso, un PRT è valido per 14 giorni e viene rinnovato continuamente fino a quando l'utente usa attivamente il dispositivo.  

## <a name="how-is-a-prt-used"></a>Come viene usato un PRT?

Un PRT viene usato da due componenti chiave di Windows:

* **Azure ad il plug**-in CloudAP: durante l'accesso a Windows, il plug-in Azure ad CloudAP richiede un PRT da Azure ad usando le credenziali fornite dall'utente. Memorizza anche nella cache il PRT per abilitare l'accesso memorizzato nella cache quando l'utente non ha accesso a una connessione Internet.
* Plug-in di **Azure ad WAM**: quando gli utenti provano ad accedere alle applicazioni, il plug-in Azure ad WAM usa il PRT per abilitare l'accesso SSO in Windows 10. Azure AD il plug-in WAM usa il PRT per richiedere i token di aggiornamento e di accesso per le applicazioni che si basano su WAM per le richieste di token. Consente inoltre l'accesso SSO nei browser inserendo il PRT nelle richieste del browser. Browser SSO in Windows 10 è supportato in Microsoft Edge (in modalità nativa) e Chrome (tramite gli account Windows 10 o l'estensione Office Online).

## <a name="how-is-a-prt-renewed"></a>Come viene rinnovato un PRT?

Un PRT viene rinnovato in due metodi diversi:

* **Azure ad il plug-in CloudAP ogni 4 ore**: il plug-in CloudAP rinnova il PRT ogni 4 ore durante l'accesso a Windows. Se l'utente non ha una connessione a Internet durante tale periodo di tempo, il plug-in CloudAP rinnoverà il PRT dopo che il dispositivo è connesso a Internet.
* **Azure ad plugin WAM durante le richieste di token dell'app**: il plug-in di WAM Abilita l'accesso SSO nei dispositivi Windows 10 abilitando le richieste di token invisibile per le applicazioni. Il plug-in WAM può rinnovare il PRT durante queste richieste di token in due modi diversi:
   * Un'app richiede WAM per un token di accesso in modo invisibile all'utente, ma non è disponibile alcun token di aggiornamento per tale app. In questo caso, WAM usa il PRT per richiedere un token per l'app e restituisce un nuovo PRT nella risposta.
   * Un'app richiede WAM per un token di accesso, ma il PRT non è valido o Azure AD richiede un'autorizzazione aggiuntiva, ad esempio Multi-Factor Authentication di Azure. In questo scenario, WAM avvia un accesso interattivo che richiede all'utente di eseguire di nuovo l'autenticazione o di fornire una verifica aggiuntiva e viene emesso un nuovo PRT al completamento dell'autenticazione.

### <a name="key-considerations"></a>Considerazioni sulle chiavi

* Un PRT viene emesso e rinnovato solo durante l'autenticazione dell'app nativa. Un PRT non viene rinnovato o emesso durante una sessione del browser.
* Nei dispositivi Azure AD aggiunti e ibridi Azure AD aggiunti, il plug-in CloudAP è l'autorità primaria per un PRT. Se un PRT viene rinnovato durante una richiesta di token basata su WAM, il PRT viene restituito al plug-in CloudAP, che verifica la validità del PRT con Azure AD prima di accettarlo.

## <a name="how-is-the-prt-protected"></a>Come viene protetto il PRT?

Un PRT viene protetto mediante associazione al dispositivo a cui l'utente ha effettuato l'accesso. Azure AD e Windows 10 abilitano la protezione PRT con i metodi seguenti:

* **Durante il primo accesso**: durante il primo accesso, un PRT viene emesso dalle richieste di firma usando la chiave del dispositivo crittograficamente generata durante la registrazione del dispositivo. In un dispositivo con un TPM valido e funzionante, la chiave del dispositivo è protetta dal TPM che impedisce l'accesso dannoso. Un PRT non viene emesso se non è possibile convalidare la firma della chiave del dispositivo corrispondente.
* **Durante le richieste e il rinnovo del token**: quando viene emesso un PRT, Azure ad rilascia anche una chiave di sessione crittografata al dispositivo. Viene crittografata con la chiave di trasporto pubblica (tkpub) generata e inviata a Azure AD come parte della registrazione del dispositivo. Questa chiave di sessione può essere decrittografata solo dalla chiave di trasporto privata (tkpriv) protetta dal TPM. La chiave della sessione è la chiave di prova del possesso (POP) per tutte le richieste inviate a Azure AD.  La chiave della sessione è protetta anche dal TPM e nessun altro componente del sistema operativo può accedervi. Le richieste di token o le richieste di rinnovo PRT sono firmate in modo sicuro da questa chiave di sessione tramite il TPM e pertanto non possono essere manomesse. Azure AD invalida tutte le richieste provenienti dal dispositivo che non sono firmate dalla chiave di sessione corrispondente.

Proteggendo queste chiavi con il TPM, gli attori malintenzionati non possono rubare le chiavi né riprodurre il PRT altrove perché il TPM non è accessibile anche se un utente malintenzionato ha il possesso fisico del dispositivo.  In questo modo, l'utilizzo di un TPM migliora significativamente la sicurezza dei Azure AD Uniti, Azure AD ibrido Uniti e Azure AD dispositivi registrati contro il furto di credenziali. Per prestazioni e affidabilità, TPM 2,0 è la versione consigliata per tutti gli scenari di registrazione dei dispositivi Azure AD in Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Come vengono protetti i token delle app e i cookie del browser?

**Token dell'app**: quando un'app richiede un token tramite WAM, Azure ad rilascia un token di aggiornamento e un token di accesso. Tuttavia, WAM restituisce solo il token di accesso all'app e protegge il token di aggiornamento nella relativa cache mediante la crittografia con la chiave Data Protection Application Programming Interface (DPAPI) dell'utente. WAM USA in modo sicuro il token di aggiornamento firmando le richieste con la chiave della sessione per emettere ulteriori token di accesso. La chiave DPAPI è protetta da una chiave simmetrica basata su Azure AD in Azure AD stessa. Quando il dispositivo deve decrittografare il profilo utente con la chiave DPAPI, Azure AD fornisce la chiave DPAPI crittografata dalla chiave della sessione, che il plug-in CloudAP richiede la decrittografia del TPM. Questa funzionalità garantisce la coerenza nella protezione dei token di aggiornamento ed evita che le applicazioni implementino i propri meccanismi di protezione.  

**Cookie del browser**: in Windows 10, Azure ad supporta l'accesso SSO del browser in Internet Explorer e Microsoft Edge in modalità nativa o in Google Chrome tramite l'estensione account Windows 10. La sicurezza è compilata non solo per proteggere i cookie, ma anche per gli endpoint a cui vengono inviati i cookie. I cookie del browser sono protetti allo stesso modo di un PRT, utilizzando la chiave della sessione per firmare e proteggere i cookie.

Quando un utente avvia un'interazione del browser, il browser (o l'estensione) richiama un host client nativo COM. L'host di Native client garantisce che la pagina provenga da uno dei domini consentiti. Il browser può inviare altri parametri all'host native client, incluso un parametro nonce, tuttavia l'host client nativo garantisce la convalida del nome host. L'host di Native Client richiede un cookie PRT dal plug-in CloudAP, che lo crea e lo firma con la chiave della sessione protetta da TPM. Poiché il cookie PRT è firmato dalla chiave di sessione, non può essere alterato. Il cookie PRT è incluso nell'intestazione della richiesta per Azure AD per convalidare il dispositivo da cui proviene. Se si usa il browser Chrome, solo l'estensione definita in modo esplicito nel manifesto dell'host di Native client può richiamarla impedendo alle estensioni arbitrarie di effettuare tali richieste. Una volta che Azure AD convalidato il cookie PRT, emette un cookie di sessione nel browser. Questo cookie di sessione contiene anche la stessa chiave di sessione rilasciata con un PRT. Durante le richieste successive, la chiave della sessione viene convalidata in modo efficace associando il cookie al dispositivo e impedendo le riesecuzione da un'altra posizione.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando un PRT riceve un'attestazione di autenticazione a più fattori?

Un PRT può ottenere un'attestazione multi-factor authentication in scenari specifici. Quando si usa un PRT basato su multi-factor authentication per richiedere token per le applicazioni, l'attestazione dell'autenticazione a più fattori viene trasferita ai token dell'app. Questa funzionalità offre un'esperienza uniforme agli utenti impedendo la richiesta di autenticazione a più fattori per ogni app che la richiede. Un PRT può ottenere un'attestazione di autenticazione a più fattori nei modi seguenti:

* **Accedi con Windows Hello for business**: Windows Hello for business sostituisce le password e usa le chiavi crittografiche per offrire un'autenticazione a due fattori avanzata. Windows Hello for business è specifico di un utente su un dispositivo e a sua volta richiede l'autenticazione a più fattori per il provisioning. Quando un utente accede con Windows Hello for business, il PRT dell'utente riceve un'attestazione di autenticazione a più fattori. Questo scenario si applica anche agli utenti che accedono con smartcard se l'autenticazione con smart card genera un'attestazione di autenticazione a più fattori da ADFS.
   * Poiché Windows Hello for business è considerato multi-factor authentication, l'attestazione dell'autenticazione a più fattori viene aggiornata quando si aggiorna il PRT stesso, quindi la durata dell'autenticazione a più fattori si estenderà continuamente quando gli utenti eseguono l'accesso con WIndows Hello for business
* Autenticazione a più fattori durante l'accesso **interattivo di WAM**: durante una richiesta di token tramite WAM, se è necessario che un utente esegua l'autenticazione a più fattori per accedere all'app, il PRT rinnovato durante questa interazione viene stampato con un'attestazione di autenticazione a più fattori.
   * In questo caso, l'attestazione dell'autenticazione a più fattori non viene aggiornata continuamente, quindi la durata dell'autenticazione a più fattori è basata sulla durata impostata nella directory.
   * Quando vengono usati i precedenti PRT e RT esistenti per l'accesso a un'app, i PRT e RT verranno considerati come la prima prova di autenticazione. Un nuovo in verrà richiesto con una seconda prova e un'attestazione di autenticazione a più fattori non stampata. Verrà anche emesso un nuovo PRT e RT.
* Autenticazione a più fattori **durante la registrazione del dispositivo**: se un amministratore ha configurato le impostazioni del dispositivo in Azure ad per richiedere l'autenticazione a più fattori per la registrazione dei [dispositivi](device-management-azure-portal.md#configure-device-settings), è necessario che l'utente esegua l'autenticazione a più fattori Durante questo processo, il PRT emesso per l'utente ha l'attestazione dell'autenticazione a più fattori ottenuta durante la registrazione. Questa funzionalità si applica solo all'utente che ha eseguito l'operazione di join e non ad altri utenti che effettuano l'accesso a tale dispositivo.
   * Analogamente a WAM Interactive Sign in, l'attestazione dell'autenticazione a più fattori non viene aggiornata continuamente, quindi la durata dell'autenticazione a più fattori è basata sulla durata impostata nella directory.

Windows 10 gestisce un elenco partizionato di PRT per ogni credenziale. È quindi presente un PRT per ogni Windows Hello for business, password o smart card. Questo partizionamento garantisce che le attestazioni multi-factor authentication siano isolate in base alle credenziali usate e non siano confuse durante le richieste di token.

## <a name="how-is-a-prt-invalidated"></a>Come viene invalidato un PRT?

Un PRT viene invalidato negli scenari seguenti:

* **Utente non valido**: se un utente viene eliminato o disabilitato in Azure ad, il PRT viene invalidato e non può essere usato per ottenere i token per le applicazioni. Se un utente eliminato o disabilitato ha già eseguito l'accesso a un dispositivo prima, l'accesso memorizzato nella cache li registrerà, fino a quando CloudAP non sarà in grado di riconoscere lo stato non valido. Quando CloudAP determina che l'utente non è valido, blocca gli accessi successivi. Un utente non valido viene automaticamente bloccato dall'accesso ai nuovi dispositivi che non dispongono di credenziali memorizzate nella cache.
* **Dispositivo non valido**: se un dispositivo viene eliminato o disabilitato in Azure ad, il PRT ottenuto sul dispositivo viene invalidato e non può essere usato per ottenere i token per altre applicazioni. Se un utente ha già eseguito l'accesso a un dispositivo non valido, può continuare a farlo. Tuttavia tutti i token nel dispositivo vengono invalidati e l'utente non dispone di SSO per le risorse del dispositivo.
* **Modifica della password**: dopo che un utente ha modificato la password, il PRT ottenuto con la password precedente viene invalidato da Azure ad. La modifica della password comporta l'acquisizione di un nuovo PRT da parte dell'utente. Questa invalidazione può essere eseguita in due modi diversi:
   * Se l'utente accede a Windows con la nuova password, CloudAP rimuove il vecchio PRT e le richieste Azure AD per emettere un nuovo PRT con la nuova password. Se l'utente non dispone di una connessione Internet, la nuova password non può essere convalidata, Windows potrebbe richiedere all'utente di immettere la vecchia password.
   * Se un utente ha effettuato l'accesso con la vecchia password o ha modificato la password dopo l'accesso a Windows, il PRT precedente viene usato per tutte le richieste di token basati su WAM. In questo scenario, all'utente viene richiesto di ripetere l'autenticazione durante la richiesta del token WAM e viene emesso un nuovo PRT.
* **Problemi relativi al TPM**: talvolta il TPM di un dispositivo può vacillare o non riuscire, causando l'inaccessibilità delle chiavi protette dal TPM. In questo caso, il dispositivo non è in grado di ottenere un PRT o richiedere token usando un PRT esistente perché non è in grado di dimostrare il possesso delle chiavi crittografiche. Di conseguenza, qualsiasi PRT esistente viene invalidato da Azure AD. Quando Windows 10 rileva un errore, avvia un flusso di ripristino per registrare nuovamente il dispositivo con nuove chiavi crittografiche. Con aggiunta ad Azure ad ibrido, come la registrazione iniziale, il ripristino viene eseguito automaticamente senza l'input dell'utente. Per i dispositivi Azure AD aggiunti o Azure AD registrati, il ripristino deve essere eseguito da un utente che dispone di privilegi di amministratore per il dispositivo. In questo scenario, il flusso di ripristino viene avviato da un prompt di Windows che guida l'utente a ripristinare correttamente il dispositivo.

## <a name="detailed-flows"></a>Flussi dettagliati

I diagrammi seguenti illustrano i dettagli sottostanti nell'emissione, nel rinnovo e nell'uso di un PRT per richiedere un token di accesso per un'applicazione. Inoltre, questi passaggi descrivono come vengono applicati i meccanismi di sicurezza indicati sopra durante queste interazioni.

### <a name="prt-issuance-during-first-sign-in"></a>Rilascio PRT durante il primo accesso

![Rilascio PRT durante il primo accesso in un flusso dettagliato](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> In Azure AD dispositivi aggiunti, questo scambio viene eseguito in modo sincrono per emettere un PRT prima che l'utente possa accedere a Windows. Nei dispositivi ibridi Azure AD aggiunti, l'Active Directory locale è l'autorità primaria. Quindi, l'utente è in attesa solo finché non è in grado di acquisire un TGT per l'accesso, mentre il rilascio PRT si verifica in modo asincrono. Questo scenario non si applica ai dispositivi Azure AD registrati perché l'accesso non usa le credenziali Azure AD.

| Passaggio | Descrizione |
| :---: | --- |
| Una | L'utente immette la password nell'interfaccia utente di accesso. LogonUI passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP Invia la richiesta al plug-in CloudAP. |
| b | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider federativo, Azure AD restituisce l'endpoint MEX (Metadata Exchange endpoint) del provider di Federazione. In caso contrario, Azure AD restituisce che l'utente è gestito indicando che l'utente può eseguire l'autenticazione con Azure AD. |
| C | Se l'utente è gestito, CloudAP otterrà il parametro nonce da Azure AD. Se l'utente è federato, il plug-in CloudAP richiede un token SAML dal provider federativo con le credenziali dell'utente. Una volta ricevuta, il token SAML richiede un parametro nonce da Azure AD. |
| D | Il plug-in CloudAP crea la richiesta di autenticazione con le credenziali dell'utente, il parametro nonce e un ambito di Service Broker, firma la richiesta con la chiave del dispositivo (dkpriv) e la invia al Azure AD. In un ambiente federato, il plug-in CloudAP usa il token SAML restituito dal provider di federazione anziché le credenziali dell'utente. |
| E | Azure AD convalida le credenziali utente, il parametro nonce e la firma del dispositivo, verifica che il dispositivo sia valido nel tenant e rilascia il PRT crittografato. Insieme al PRT, Azure AD rilascia anche una chiave simmetrica, denominata chiave di sessione crittografata da Azure AD mediante la chiave di trasporto (tkpub). Inoltre, anche la chiave della sessione è incorporata nel PRT. Questa chiave di sessione funge da chiave di prova del possesso (PoP) per le successive richieste con il PRT. |
| F | Il plug-in CloudAP passa il PRT e la chiave della sessione crittografati a CloudAP. CloudAP richiedere al TPM di decrittografare la chiave della sessione utilizzando la chiave di trasporto (tkpriv) e crittografarla di nuovo utilizzando la chiave del TPM. CloudAP archivia la chiave della sessione crittografata nella cache insieme al PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Rinnovo PRT negli accessi successivi

![Rinnovo PRT negli accessi successivi](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | L'utente immette la password nell'interfaccia utente di accesso. LogonUI passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP Invia la richiesta al plug-in CloudAP. |
| b | Se l'utente ha già eseguito l'accesso all'utente, Windows avvia l'accesso memorizzato nella cache e convalida le credenziali per l'accesso dell'utente. Ogni 4 ore il plug-in CloudAP avvia il rinnovo del PRT in modo asincrono. |
| C | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider federativo, Azure AD restituisce l'endpoint MEX (Metadata Exchange endpoint) del provider di Federazione. In caso contrario, Azure AD restituisce che l'utente è gestito indicando che l'utente può eseguire l'autenticazione con Azure AD. |
| D | Se l'utente è federato, il plug-in CloudAP richiede un token SAML dal provider federativo con le credenziali dell'utente. Una volta ricevuta, il token SAML richiede un parametro nonce da Azure AD. Se l'utente è gestito, CloudAP otterrà direttamente il parametro nonce da Azure AD. |
| E | Il plug-in CloudAP costruisce la richiesta di autenticazione con le credenziali dell'utente, il parametro nonce e il PRT esistente, firma la richiesta con la chiave della sessione e la invia a Azure AD. In un ambiente federato, il plug-in CloudAP usa il token SAML restituito dal provider di federazione anziché le credenziali dell'utente. |
| F | Azure AD convalida la firma della chiave della sessione confrontandolo con la chiave di sessione incorporata nel PRT, convalida il parametro nonce e verifica che il dispositivo sia valido nel tenant e emette un nuovo PRT. Come illustrato in precedenza, il PRT viene nuovamente accompagnato con la chiave di sessione crittografata dalla chiave di trasporto (tkpub). |
| G | Il plug-in CloudAP passa il PRT e la chiave della sessione crittografati a CloudAP. CloudAP richiede al TPM di decrittografare la chiave della sessione utilizzando la chiave di trasporto (tkpriv) e crittografarla di nuovo utilizzando la chiave del TPM. CloudAP archivia la chiave della sessione crittografata nella cache insieme al PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilizzo di PRT durante le richieste di token dell'app

![Utilizzo di PRT durante le richieste di token dell'app](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | Un'applicazione, ad esempio Outlook, OneNote e così via, avvia una richiesta di token a WAM. WAM, a sua volta, chiede al plug-in Azure AD WAM di servire la richiesta di token. |
| b | Se è già disponibile un token di aggiornamento per l'applicazione, Azure AD il plug-in WAM lo usa per richiedere un token di accesso. Per fornire la prova dell'associazione di dispositivi, WAM plugin firma la richiesta con la chiave della sessione. Azure AD convalida la chiave della sessione e rilascia un token di accesso e un nuovo token di aggiornamento per l'app, crittografato con la chiave della sessione. WAM plugin richiede il plug-in cloud AP per decrittografare i token, che, a sua volta, richiede il TPM per la decrittografia tramite la chiave della sessione, causando il plug-in WAM per ottenere entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre esegue nuovamente la crittografia del token di aggiornamento con DPAPI e lo archivia nella propria cache  |
| C |  Se non è disponibile un token di aggiornamento per l'applicazione, Azure AD il plug-in WAM usa il PRT per richiedere un token di accesso. Per fornire la prova del possesso, il plug-in WAM firma la richiesta contenente il PRT con la chiave della sessione. Azure AD convalida la firma della chiave della sessione confrontandolo con la chiave di sessione incorporata nel PRT, verifica che il dispositivo sia valido e rilascia un token di accesso e un token di aggiornamento per l'applicazione. Inoltre, Azure AD possibile emettere un nuovo PRT (basato sul ciclo di aggiornamento), crittografato tutti con la chiave della sessione. |
| D | WAM plugin richiede il plug-in cloud AP per decrittografare i token, che, a sua volta, richiede il TPM per la decrittografia tramite la chiave della sessione, causando il plug-in WAM per ottenere entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre esegue nuovamente la crittografia del token di aggiornamento con DPAPI e lo archivia nella propria cache. Il plug-in WAM utilizzerà il token di aggiornamento per l'applicazione. WAM plugin restituisce anche il nuovo PRT per il plug-in AP cloud, che convalida il PRT con Azure AD prima di aggiornarlo nella propria cache. Il plug-in cloud AP userà il nuovo PRT in futuro. |
| E | WAM fornisce il token di accesso appena emesso a WAM, che a sua volta lo riporta all'applicazione chiamante|

### <a name="browser-sso-using-prt"></a>SSO del browser con PRT

![SSO del browser con PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | L'utente accede a Windows con le credenziali per ottenere un PRT. Quando l'utente apre il browser, il browser o l'estensione carica gli URL dal registro di sistema. |
| b | Quando un utente apre un URL di accesso Azure AD, il browser o l'estensione convalida l'URL con quelli ottenuti dal registro di sistema. Se corrispondono, il browser richiama l'host di Native Client per ottenere un token. |
| C | L'host di Native Client verifica che gli URL appartengano ai provider di identità Microsoft (account Microsoft o Azure AD), estrae un parametro nonce inviato dall'URL ed effettua una chiamata al plug-in CloudAP per ottenere un cookie PRT. |
| D | Il plug-in CloudAP creerà il cookie PRT, effettuerà l'accesso con la chiave della sessione associata al TPM e lo invierà di nuovo all'host Native Client. Poiché il cookie è firmato dalla chiave di sessione, non può essere alterato. |
| E | L'host di Native Client restituirà il cookie PRT al browser, che lo includerà come parte dell'intestazione della richiesta denominata x-ms-RefreshTokenCredential e token di richiesta da Azure AD. |
| F | Azure AD convalida la firma della chiave della sessione nel cookie PRT, convalida il parametro nonce, verifica che il dispositivo sia valido nel tenant e rilascia un token ID per la pagina Web e un cookie di sessione crittografato per il browser. |

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi correlati a PRT, vedere l'articolo [risoluzione dei problemi relativi a hybrid Azure Active Directory aggiunti a dispositivi Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
