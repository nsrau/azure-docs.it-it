---
title: Token di aggiornamento primario (PRT) e Azure AD - Azure Active Directory
description: Qual è il ruolo e come si gestisce il token di aggiornamento primario (PRT) in Azure Active Directory?
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

Un token di aggiornamento primario (PRT) è un elemento chiave dell'autenticazione di Azure AD nei dispositivi Windows 10, iOS e Android.A Primary Refresh Token (PRT) is a key artifact of Azure AD authentication on Windows 10, iOS, and Android devices. Si tratta di un JSON Web Token (JWT) appositamente rilasciato aMicrosoft broker di token di primo-partito per abilitare Single Sign-On (SSO) tra le applicazioni utilizzate su tali dispositivi. In questo articolo verranno forniti dettagli su come un PRT viene rilasciato, usato e protetto nei dispositivi Windows 10.

Questo articolo presuppone che tu abbia già compreso i diversi stati del dispositivo disponibili in Azure AD e il funzionamento di Single Sign-On in Windows 10. Per altre informazioni sui dispositivi in Azure AD, vedere l'articolo [Che cos'è](overview.md) la gestione dei dispositivi in Azure Active Directory?

## <a name="key-terminology-and-components"></a>Terminologia e componenti chiave

I seguenti componenti di Windows svolgono un ruolo chiave nella richiesta e nell'utilizzo di un PRT:

* Provider di **autenticazione cloud** (CloudAP): CloudAP è il provider di autenticazione moderno per l'accesso a Windows, che verifica la registrazione degli utenti a un dispositivo Windows 10. CloudAP fornisce un framework di plug-in che i provider di identità possono basare per abilitare l'autenticazione a Windows usando le credenziali del provider di identità.
* **Web Account Manager** (WAM): WAM è il broker di token predefinito nei dispositivi Windows 10. WAM fornisce inoltre un framework di plug-in su cui i provider di identità possono basarsi e abilitare SSO alle applicazioni che si basano su tale provider di identità.
* **Plug-in CloudAP**di Azure AD: un plug-in specifico di Azure AD basato sul framework CloudAP, che verifica le credenziali utente con Azure AD durante l'accesso a Windows.Azure AD CloudAP plugin : An Azure AD specific plugin built on the CloudAP framework, that verifies user credentials with Azure AD during Windows sign in.
* **Plug-in WAM di Azure AD:** un plug-in specifico di Azure AD basato sul framework WAM, che consente a SSO di applicazioni basate su Azure AD per l'autenticazione.
* **Dsreg:** un componente specifico di Azure AD in Windows 10, che gestisce il processo di registrazione dei dispositivi per tutti gli stati del dispositivo.
* **Trusted Platform Module** (TPM): un TPM è un componente hardware integrato in un dispositivo che fornisce funzioni di sicurezza basate su hardware per i segreti di utenti e dispositivi. Ulteriori dettagli sono disponibili nell'articolo [Trusted Platform Module Technology Overview](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Cosa contiene il PRT?

Un PRT contiene attestazioni in genere contenute in qualsiasi token di aggiornamento di Azure AD. Inoltre, ci sono alcune attestazioni specifiche del dispositivo incluse nel PRT. Ecco quali sono:

* **DEVICE ID**: Viene rilasciato un PRT a un utente su un dispositivo specifico. L'attestazione `deviceID` dell'ID dispositivo determina il dispositivo su cui è stato rilasciato il PRT all'utente. Questa attestazione viene successivamente rilasciata ai token ottenuti tramite il PRT. L'attestazione dell'ID dispositivo viene usata per determinare l'autorizzazione per l'accesso condizionale in base allo stato o alla conformità del dispositivo.
* **Chiave di sessione:** la chiave di sessione è una chiave simmetrica crittografata, generata dal servizio di autenticazione di Azure AD, emessa come parte del PRT. La chiave di sessione funge da prova di possesso quando un PRT viene utilizzato per ottenere token per altre applicazioni.

### <a name="can-i-see-whats-in-a-prt"></a>Posso vedere cosa c'è in un PRT?

Un PRT è un BLOB opaco inviato da Azure AD il cui contenuto non è noto ai componenti client. Non puoi vedere cosa c'è dentro un PRT.

## <a name="how-is-a-prt-issued"></a>Come viene emesso un PRT?

La registrazione dei dispositivi è un prerequisito per l'autenticazione basata su dispositivo in Azure AD. Un PRT viene rilasciato agli utenti solo su dispositivi registrati. Per dettagli più approfonditi sulla registrazione del dispositivo, vedere l'articolo [Windows Hello for Business e Registrazione dispositivo](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante la registrazione del dispositivo, il componente dsreg genera due set di coppie di chiavi crittografiche:

* Chiave dispositivo (dkpub/dkpriv)
* Chiave di trasporto (tkpub/tkpriv)

Le chiavi private sono associate al TPM del dispositivo se il dispositivo ha un TPM valido e funzionante, mentre le chiavi pubbliche vengono inviate ad Azure AD durante il processo di registrazione del dispositivo. Queste chiavi vengono utilizzate per convalidare lo stato del dispositivo durante le richieste PRT.

Il PRT viene rilasciato durante l'autenticazione utente su un dispositivo Windows 10 in due scenari:

* **Aggiunta ad Azure AD** o **aggiunta ad Azure AD ibrido:** viene generato un PRT durante l'accesso a Windows quando un utente accede con le credenziali dell'organizzazione. Viene rilasciato un PRT con tutte le credenziali supportate di Windows 10, ad esempio password e Windows Hello for Business. In questo scenario, il plug-in CloudAP di Azure AD è l'autorità principale per il PRT.
* **Dispositivo registrato di Azure AD:** viene emesso un PRT quando un utente aggiunge un account di lavoro secondario al dispositivo Windows 10.Azure AD registered device : A PRT is issued when a user adds a secondary work account to their Windows 10 device. Gli utenti possono aggiungere un account a Windows 10 in due modi diversi:  
   * Aggiunta di un account tramite il prompt **Usa questo account ovunque in questo dispositivo** dopo l'accesso a un'app (ad esempio, Outlook)
   * Aggiunta di un account da **Impostazioni** > **Account** > **Accesso al lavoro o all'istituto di** > **istruzione Connect**

Negli scenari di dispositivi registrati di Azure AD, il plug-in WAM di Azure AD è l'autorità principale per PRT poiché l'accesso a Windows non avviene con questo account Azure AD.

> [!NOTE]
> I provider di identità di terze parti devono supportare il protocollo WS-Trust per abilitare il rilascio di PRT nei dispositivi Windows 10. Senza WS-Trust, PRT non può essere rilasciato agli utenti in Hybrid Azure AD aggiunto o dispositivi aggiunti ad Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual è la durata di un PRT?

Una volta emesso, un PRT è valido per 14 giorni e viene continuamente rinnovato finché l'utente utilizza attivamente il dispositivo.  

## <a name="how-is-a-prt-used"></a>Come viene utilizzato un PRT?

Un PRT viene utilizzato da due componenti chiave in Windows:

* **Plug-in CloudAP**di Azure AD: durante l'accesso a Windows, il plug-in CloudAP di Azure AD richiede un PRT da Azure AD usando le credenziali fornite dall'utente. Memorizza inoltre nella cache il PRT per abilitare l'accesso memorizzato nella cache quando l'utente non ha accesso a una connessione Internet.
* **Plug-in WAM di Azure AD:** quando gli utenti tentano di accedere alle applicazioni, il plug-in WAM di Azure AD usa il plug-in PRT per abilitare SSO in Windows 10. Il plug-in WAM di Azure AD usa il prT per richiedere i token di aggiornamento e accesso per le applicazioni che si basano su WAM per le richieste di token. Consente inoltre SSO sui browser iniettando il PRT nelle richieste del browser. Il browser SSO in Windows 10 è supportato su Microsoft Edge (in definitiva) e Chrome (tramite l'account Windows 10 o l'estensione Office Online).

## <a name="how-is-a-prt-renewed"></a>Come viene rinnovato un PRT?

Un PRT viene rinnovato in due diversi metodi:

* **Plug-in CloudAP di Azure AD ogni 4 ore:** il plug-in CloudAP rinnova il prT ogni 4 ore durante l'accesso a Windows. Se l'utente non dispone di una connessione internet durante tale periodo, il plug-in CloudAP rinnoverà il PRT dopo che il dispositivo è connesso a Internet.
* **Plug-in WAM**di Azure AD durante le richieste di token dell'app: il plug-in WAM abilita SSO nei dispositivi Windows 10 abilitando le richieste di token silenziose per le applicazioni. Il plug-in WAM può rinnovare il PRT durante queste richieste di token in due modi diversi:
   * Un'app richiede WAM per un token di accesso in modo invisibile all'utente, ma non è disponibile alcun token di aggiornamento per l'app. In questo caso, WAM utilizza il PRT per richiedere un token per l'app e ottiene un nuovo PRT nella risposta.
   * Un'app richiede WAM per un token di accesso, ma il PRT non è valido o Azure AD richiede autorizzazioni aggiuntive (ad esempio, Azure Multi-Factor Authentication). In questo scenario, WAM avvia un accesso interattivo che richiede all'utente di eseguire nuovamente l'autenticazione o fornire ulteriore verifica e viene emesso un nuovo PRT al termine dell'autenticazione.

### <a name="key-considerations"></a>Considerazioni sulle chiavi

* Un PRT viene emesso e rinnovato solo durante l'autenticazione dell'app nativa. Un PRT non viene rinnovato o rilasciato durante una sessione del browser.
* Nei dispositivi aggiunti ad Azure AD e ibridi di Azure AD, il plug-in CloudAP è l'autorità principale per un PRT. Se un PRT viene rinnovato durante una richiesta di token basata su WAM, il PRT viene inviato nuovamente al plug-in CloudAP, che verifica la validità del PRT con Azure AD prima di accettarlo.

## <a name="how-is-the-prt-protected"></a>Come è protetto il PRT?

Un PRT è protetto associandolo al dispositivo a cui l'utente ha eseguito l'accesso. Azure AD e Windows 10 abilitano la protezione PRT tramite i metodi seguenti:Azure AD and Windows 10 enable PRT protection through the following methods:

* Durante il **primo accesso:** durante il primo accesso, viene emesso un PRT tramite richieste di firma utilizzando la chiave del dispositivo generata crittograficamente durante la registrazione del dispositivo. In un dispositivo con un TPM valido e funzionante, la chiave del dispositivo è protetta dal TPM che impedisce l'accesso dannoso. Un PRT non viene generato se la firma della chiave del dispositivo corrispondente non può essere convalidata.
* **Durante le richieste di token e**il rinnovo: quando viene emesso un PRT, Azure AD rilascia anche una chiave di sessione crittografata al dispositivo. Viene crittografato con la chiave di trasporto pubblico (tkpub) generata e inviata ad Azure AD come parte della registrazione del dispositivo. Questa chiave di sessione può essere decrittografata solo dalla chiave di trasporto privata (tkpriv) protetta dal TPM. La chiave di sessione è la chiave POP (Proof-of-Possession) per tutte le richieste inviate ad Azure AD.  La chiave di sessione è protetta anche dal TPM e nessun altro componente del sistema operativo può accedervi. Le richieste di token o le richieste di rinnovo PRT vengono firmate in modo sicuro da questa chiave di sessione tramite il TPM e, pertanto, non possono essere manomesse. Azure AD invaliderà tutte le richieste dal dispositivo che non sono firmate dalla chiave di sessione corrispondente.

Proteggendo queste chiavi con il TPM, gli attori malintenzionati non possono rubare le chiavi né riprodurre il PRT altrove come il TPM è inaccessibile anche se un utente malintenzionato ha il possesso fisico del dispositivo.  Pertanto, l'uso di un TPM migliora notevolmente la sicurezza dei dispositivi di aggiunta ad Azure AD, aggiunti ad Azure AD ibridi e registrati di Azure AD contro il furto di credenziali. Per prestazioni e affidabilità, TPM 2.0 è la versione consigliata per tutti gli scenari di registrazione dei dispositivi di Azure AD in Windows 10.For performance and reliability, TPM 2.0 is the recommended version for all Azure AD device registration scenarios on Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Come vengono protetti i token dell'app e i cookie del browser?

**Token dell'app:** quando un'app richiede un token tramite WAM, Azure AD rilascia un token di aggiornamento e un token di accesso. Tuttavia, WAM restituisce solo il token di accesso all'app e protegge il token di aggiornamento nella cache crittografandolo con la chiave DPAPI (Data Protection Application Programming Interface) dell'utente. WAM utilizza in modo sicuro il token di aggiornamento firmando le richieste con la chiave di sessione per rilasciare ulteriori token di accesso. La chiave DPAPI è protetta da una chiave simmetrica basata su Azure AD in Azure AD stesso. Quando il dispositivo deve decrittografare il profilo utente con la chiave DPAPI, Azure AD fornisce la chiave DPAPI crittografata dalla chiave di sessione, quale plug-in CloudAP richiede TPM per decrittografare. Questa funzionalità garantisce la coerenza nella protezione dei token di aggiornamento ed evita alle applicazioni che implementano i propri meccanismi di protezione.  

**Cookie del browser**: In Windows 10, Azure AD supporta il browser SSO in Internet Explorer e Microsoft Edge in modo nativo o in Google Chrome tramite l'estensione account di Windows 10. La sicurezza è costruita non solo per proteggere i cookie, ma anche gli endpoint a cui vengono inviati i cookie. I cookie del browser sono protetti allo stesso modo di un PRT, utilizzando la chiave di sessione per firmare e proteggere i cookie.

Quando un utente avvia un'interazione del browser, il browser (o estensione) richiama un host client nativo COM. L'host client nativo garantisce che la pagina provenga da uno dei domini consentiti. Il browser potrebbe inviare altri parametri all'host client nativo, incluso un nonce, tuttavia l'host client nativo garantisce la convalida del nome host. L'host client nativo richiede un cookie PRT dal plug-in CloudAP, che lo crea e lo firma con la chiave di sessione protetta dal TPM. Poiché il cookie PRT è firmato dalla chiave di sessione, non può essere manomesso. Questo cookie PRT è incluso nell'intestazione della richiesta per Azure AD per convalidare il dispositivo da cui ha origine. Se si utilizza il browser Chrome, solo l'estensione definita in modo esplicito nel manifesto dell'host client nativo può richiamare impedendo alle estensioni arbitrarie di effettuare queste richieste. Dopo che Azure AD convalida il cookie PRT, rilascia un cookie di sessione al browser. Questo cookie di sessione contiene anche la stessa chiave di sessione emessa con un PRT. Durante le richieste successive, la chiave di sessione viene convalidata legando in modo efficace il cookie al dispositivo e impedendo i replay da un'altra parte.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando un PRT ottiene un reclamo MFA?

Un PRT può ottenere un'attestazione di autenticazione a più fattori (MFA) in scenari specifici. Quando un PRT basato su MFA viene utilizzato per richiedere token per le applicazioni, l'attestazione di autenticazione a più fattori viene trasferita a tali token dell'app. Questa funzionalità offre un'esperienza senza soluzione di continuità agli utenti evitando le sfide dell'autenticazione a più fattori per ogni app che lo richiede. Un PRT può ottenere un reclamo MFA nei modi seguenti:A PRT can get an MFA claim in the following ways:

* **Accedi con Windows Hello for Business:** Windows Hello for Business sostituisce le password e usa le chiavi di crittografia per fornire un'autenticazione avanzata a due fattori. Windows Hello for Business è specifico di un utente in un dispositivo e richiede il provisioning dell'autenticazione a più fattori. Quando un utente accede con Windows Hello for Business, PRT dell'utente ottiene un'attestazione MFA. Questo scenario si applica anche agli utenti che accedono con smart card se l'autenticazione con smart card produce un'attestazione di autenticazione a più fattori da ADFS.
   * Come Windows Hello for Business è considerato autenticazione a più fattori, l'attestazione di autenticazione a più fattori viene aggiornata quando viene aggiornata la richiesta di autenticazione a più fattori, pertanto la durata dell'autenticazione a più fattori si estenderà continuamente quando gli utenti accedono con WIndows Hello for Business
* **MFA durante l'accesso interattivo WAM:** durante una richiesta di token tramite WAM, se un utente è tenuto a eseguire l'autenticazione a più fattori per accedere all'app, il PRT che viene rinnovato durante questa interazione viene stampato con un'attestazione MFA.
   * In questo caso, l'attestazione Di autenticazione a più fattori non viene aggiornata continuamente, pertanto la durata dell'autenticazione a più fattori si basa sulla durata impostata nella directory.
   * Quando un PRT e RT esistenti precedenti vengono utilizzati per l'accesso a un'app, PRT e RT verranno considerati come la prima prova di autenticazione. Sarà richiesto un nuovo AT con una seconda prova e un reclamo di MFA impresso. In questo modo verrà emettere un nuovo PRT e RT.
* **Autenticazione**a più fattori durante la registrazione del dispositivo: se un amministratore ha configurato le impostazioni del dispositivo in Azure AD per [richiedere l'autenticazione](device-management-azure-portal.md#configure-device-settings)a più fattori per registrare i dispositivi, l'utente deve eseguire l'autenticazione a più fattori per completare la registrazione. Durante questo processo, il PRT che viene rilasciato all'utente ha l'attestazione MFA ottenuta durante la registrazione. Questa funzionalità si applica solo all'utente che ha eseguito l'operazione di partecipazione, non ad altri utenti che accedono a tale dispositivo.
   * Analogamente all'accesso interattivo WAM, l'attestazione MFA non viene aggiornata continuamente, pertanto la durata dell'autenticazione a più fattori si basa sulla durata impostata nella directory.

Windows 10 gestisce un elenco partizionato di PRT per ogni credenziale. Quindi, c'è un PRT per ogni di Windows Hello for Business, password o smart card. Questo partizionamento garantisce che le attestazioni di autenticazione a più fattori siano isolate in base alle credenziali usate e non confonde durante le richieste di token.

## <a name="how-is-a-prt-invalidated"></a>Come viene invalidato un PRT?

Un PRT viene invalidato negli scenari seguenti:A PRT is invalidated in the following scenarios:

* **Utente non valido:** se un utente viene eliminato o disabilitato in Azure AD, il PRT viene invalidato e non può essere usato per ottenere token per le applicazioni. Se un utente eliminato o disabilitato ha già eseguito l'accesso a un dispositivo in precedenza, l'accesso memorizzato nella cache lo effettuerebbe l'accesso, fino a quando CloudAP non viene a conoscenza dello stato non valido. Quando CloudAP determina che l'utente non è valido, blocca gli accessi successivi. Un utente non valido viene bloccato automaticamente dall'accesso ai nuovi dispositivi che non dispongono delle credenziali memorizzate nella cache.
* **Dispositivo non valido:** se un dispositivo viene eliminato o disabilitato in Azure AD, il PRT ottenuto in tale dispositivo viene invalidato e non può essere usato per ottenere token per altre applicazioni. Se un utente ha già eseguito l'accesso a un dispositivo non valido, può continuare a farlo. Ma tutti i token sul dispositivo vengono invalidati e l'utente non dispone di SSO per le risorse da quel dispositivo.
* **Modifica della password:** dopo che un utente ha modificato la password, il PRT ottenuto con la password precedente viene invalidato da Azure AD. La modifica della password comporta che l'utente ottrae un nuovo PRT. Questa invalidazione può avvenire in due modi diversi:This invalidation can happen in two different ways:
   * Se l'utente accede a Windows con la nuova password, CloudAP elimina il PRT precedente e richiede ad Azure AD di inviare un nuovo PRT con la nuova password. Se l'utente non dispone di una connessione a Internet, la nuova password non può essere convalidata, Windows potrebbe richiedere all'utente di immettere la vecchia password.
   * Se un utente ha eseguito l'accesso con la vecchia password o ha modificato la password dopo l'accesso a Windows, il vecchio PRT viene utilizzato per tutte le richieste di token basate su WAM. In questo scenario, l'utente viene richiesto di eseguire nuovamente l'autenticazione durante la richiesta di token WAM e viene emesso un nuovo PRT.
* **Problemi tPM**: A volte, il TPM di un dispositivo può vacillare o fallire, causando l'inaccessibilità delle chiavi protette dal TPM. In questo caso, il dispositivo non è in grado di ottenere un PRT o di richiedere token utilizzando un PRT esistente in quanto non può dimostrare il possesso delle chiavi crittografiche. Di conseguenza, qualsiasi PRT esistente viene invalidato da Azure AD. Quando Windows 10 rileva un errore, avvia un flusso di ripristino per registrare nuovamente il dispositivo con nuove chiavi di crittografia. Con l'aggiunta ad un annuncio di Azure ibrido, proprio come la registrazione iniziale, il ripristino avviene automaticamente senza input dell'utente. Per i dispositivi aggiunti ad Azure AD o registrati ad Azure AD, il ripristino deve essere eseguito da un utente con privilegi di amministratore sul dispositivo. In questo scenario, il flusso di ripristino viene avviato da un prompt di Windows che guida l'utente a ripristinare correttamente il dispositivo.

## <a name="detailed-flows"></a>Flussi dettagliati

I diagrammi seguenti illustrano i dettagli sottostanti nell'emissione, nel rinnovo e nell'utilizzo di un PRT per richiedere un token di accesso per un'applicazione. Inoltre, questi passaggi descrivono anche come vengono applicati i meccanismi di sicurezza di cui sopra durante queste interazioni.

### <a name="prt-issuance-during-first-sign-in"></a>Emissione PRT durante il primo accesso

![Rilascio PRT durante il primo accesso nel flusso dettagliato](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> In Azure AD joined devices, this exchange happens synchronously to issue a PRT before the user can logon to Windows. Nei dispositivi aggiunti ad Azure AD ibridi Active Directory locale è l'autorità principale. Così, l'utente è solo in attesa fino a quando non possono acquisire un TGT per accedere, mentre il rilascio PRT avviene in modo asincrono. Questo scenario non si applica ai dispositivi registrati di Azure AD poiché l'accesso non usa le credenziali di Azure AD.

| Passaggio | Descrizione |
| :---: | --- |
| Una  | L'utente immette la password nell'interfaccia utente di accesso. LogonUI passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP inoltra questa richiesta al plug-in CloudAP. |
| b | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider federativo, Azure AD restituisce l'endpoint di scambio metadati (MEX) del provider federativo. In caso contrario, Azure AD restituisce che l'utente è gestito indicando che l'utente può eseguire l'autenticazione con Azure AD. |
| C | Se l'utente è gestito, CloudAP otterrà il nonce da Azure AD. Se l'utente è federato, il plug-in CloudAP richiede un token SAML al provider federativo con le credenziali dell'utente. Una volta ricevuto, il token SAML richiede un nonce da Azure AD. |
| D | Il plug-in CloudAP costruisce la richiesta di autenticazione con le credenziali dell'utente, il nonce e un ambito del broker, firma la richiesta con la chiave Device (dkpriv) e la invia ad Azure AD. In un ambiente federato, il plug-in CloudAP utilizza il token SAML restituito dal provider federativo anziché le credenziali dell'utente. |
| E | Azure AD convalida le credenziali utente, il nonce e la firma del dispositivo, verifica che il dispositivo sia valido nel tenant ed emette il PRT crittografato. Insieme a PRT, Azure AD rilascia anche una chiave simmetrica, denominata chiave di sessione crittografata da Azure AD usando la chiave di trasporto (tkpub). Inoltre, la chiave Session è incorporata anche in PRT. Questa chiave Session funge da chiave PoP (Proof-of-possession) per le successive richieste con il PRT. |
| F | Il plug-in CloudAP passa il PRT crittografato e la chiave di sessione a CloudAP. CloudAP richiede al TPM di decrittografare la chiave di sessione utilizzando la chiave di trasporto (tkpriv) e di ricrittografarla utilizzando la chiave del TPM. CloudAP archivia la chiave di sessione crittografata nella cache insieme al PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Rinnovo PRT negli accessi successivi

![Rinnovo PRT negli accessi successivi](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una  | L'utente immette la password nell'interfaccia utente di accesso. LogonUI passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP inoltra questa richiesta al plug-in CloudAP. |
| b | Se l'utente ha eseguito l'accesso precedentemente all'utente, Windows avvia l'accesso memorizzato nella cache e convalida le credenziali per l'accesso. Ogni 4 ore, il plugin CloudAP avvia il rinnovo PRT in modo asincrono. |
| C | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider federativo, Azure AD restituisce l'endpoint di scambio metadati (MEX) del provider federativo. In caso contrario, Azure AD restituisce che l'utente è gestito indicando che l'utente può eseguire l'autenticazione con Azure AD. |
| D | Se l'utente è federato, il plug-in CloudAP richiede un token SAML al provider federativo con le credenziali dell'utente. Una volta ricevuto, il token SAML richiede un nonce da Azure AD. Se l'utente è gestito, CloudAP otterrà direttamente il nonce da Azure AD. |
| E | Il plug-in CloudAP crea la richiesta di autenticazione con le credenziali dell'utente, il nonce e il PRT esistente, firma la richiesta con la chiave Session e la invia ad Azure AD. In un ambiente federato, il plug-in CloudAP utilizza il token SAML restituito dal provider federativo anziché le credenziali dell'utente. |
| F | Azure AD convalida la firma della chiave di sessione confrontandola con la chiave Session incorporata in PRT, convalida il nonce e verifica che il dispositivo sia valido nel tenant ed emette un nuovo PRT. Come si è visto in precedenza, il PRT è nuovamente accompagnato con la chiave di sessione crittografata dalla chiave di trasporto (tkpub). |
| G | Il plug-in CloudAP passa il PRT crittografato e la chiave di sessione a CloudAP. CloudAP richiede al TPM di decrittografare la chiave di sessione utilizzando la chiave di trasporto (tkpriv) e di ricrittografarla utilizzando la chiave del TPM. CloudAP archivia la chiave di sessione crittografata nella cache insieme al PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilizzo PRT durante le richieste di token dell'appPRT usage during app token requests

![Utilizzo PRT durante le richieste di token dell'appPRT usage during app token requests](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una  | Un'applicazione (ad esempio Outlook, OneNote e così via) avvia una richiesta di token a WAM. WAM, a sua volta, chiede al plug-in WAM di Azure AD di soddisfare la richiesta di token. |
| b | Se un token di aggiornamento per l'applicazione è già disponibile, il plug-in WAM di Azure AD lo usa per richiedere un token di accesso. Per fornire la prova dell'associazione del dispositivo, il plug-in WAM firma la richiesta con il tasto Session. Azure AD convalida la chiave di sessione ed emette un token di accesso e un nuovo token di aggiornamento per l'app, crittografato dalla chiave di sessione. Il plug-in WAM richiede il plug-in Cloud AP per decrittografare i token, il che, a sua volta, richiede al TPM di decrittografare utilizzando la chiave Session, con conseguente plug-in WAM che ottiene entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre crittografa nuovamente il token di aggiornamento con DPAPI e lo archivia nella propria cache  |
| C |  Se non è disponibile un token di aggiornamento per l'applicazione, il plug-in WAM di Azure AD usa il token PRT per richiedere un token di accesso. Per fornire la prova del possesso, il plug-in WAM firma la richiesta contenente il PRT con la chiave Session. Azure AD convalida la firma della chiave di sessione confrontandola con la chiave Session incorporata in PRT, verifica che il dispositivo sia valido e rilascia un token di accesso e un token di aggiornamento per l'applicazione. Inoltre, Azure AD può rilasciare un nuovo PRT (in base al ciclo di aggiornamento), tutti crittografati dalla chiave di sessione. |
| D | Il plug-in WAM richiede il plug-in Cloud AP per decrittografare i token, il che, a sua volta, richiede al TPM di decrittografare utilizzando la chiave Session, con conseguente plug-in WAM che ottiene entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre crittografa nuovamente il token di aggiornamento con DPAPI e lo archivia nella propria cache. Il plug-in WAM utilizzerà il token di aggiornamento in futuro per questa applicazione. Il plug-in WAM restituisce anche il nuovo plug-in da PRT a Cloud AP, che convalida il plug-in PRT con Azure AD prima di aggiornarlo nella propria cache. Il plug-in Cloud AP utilizzerà il nuovo PRT in futuro. |
| E | WAM fornisce il token di accesso appena rilasciato a WAM, che a sua volta, lo fornisce all'applicazione chiamante|

### <a name="browser-sso-using-prt"></a>SSO del browser con PRT

![SSO del browser con PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una  | L'utente accede a Windows con le proprie credenziali per ottenere un PRT. Una volta che l'utente apre il browser, il browser (o l'estensione) carica gli URL dal Registro di sistema. |
| b | Quando un utente apre un URL di accesso di Azure AD, il browser o l'estensione convalida l'URL con quelli ottenuti dal Registro di sistema. Se corrispondono, il browser richiama l'host client nativo per ottenere un token. |
| C | L'host client nativo convalida che gli URL appartengono ai provider di identità Microsoft (account Microsoft o Azure AD), estrae un nonce inviato dall'URL ed effettua una chiamata al plug-in CloudAP per ottenere un cookie PRT. |
| D | Il plug-in CloudAP creerà il cookie PRT, accederà con la chiave di sessione associata al TPM e lo invierà all'host client nativo. Poiché il cookie è firmato dalla chiave di sessione, non può essere manomesso. |
| E | L'host client nativo restituirà questo cookie PRT al browser, che lo includerà come parte dell'intestazione della richiesta denominata x-ms-RefreshTokenCredential e i token di richiesta da Azure AD. |
| F | Azure AD convalida la firma della chiave di sessione nel cookie PRT, convalida il nonce, verifica che il dispositivo sia valido nel tenant e rilascia un token ID per la pagina Web e un cookie di sessione crittografato per il browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi relativi a PRT, vedere l'articolo Risoluzione dei problemi relativi [ad Azure Active Directory ibrido aggiunti a i dispositivi Windows 10 e Windows Server 2016.](troubleshoot-hybrid-join-windows-current.md)
