---
title: Token di aggiornamento primario e Azure AD - Azure Active Directory
description: Qual è il ruolo del token di aggiornamento primario in Azure Active Directory e come è possibile gestirlo?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f2b059bb6ae63d7f427ce970b2538da922e2dec
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837264"
---
# <a name="what-is-a-primary-refresh-token"></a>Che cos'è un token di aggiornamento primario?

Un token di aggiornamento primario (PRT) è un elemento chiave dell'autenticazione Azure AD in Windows 10, Windows Server 2016 e versioni successive, dispositivi iOS e Android. Si tratta di un token JSON Web (JWT) rilasciato appositamente per i broker di token proprietari Microsoft per consentire l'accesso Single Sign-On (SSO) nelle applicazioni usate in tali dispositivi. In questo articolo vengono fornite informazioni dettagliate sul modo in cui un token di aggiornamento primario viene rilasciato, usato e protetto nei dispositivi Windows 10.

Questo articolo presuppone che si abbia già familiarità con i diversi stati dei dispositivi disponibili in Azure AD e con il funzionamento della funzionalità Single Sign-On in Windows 10. Per altre informazioni sui dispositivi in Azure AD, vedere [Informazioni sulla gestione dei dispositivi in Azure Active Directory](overview.md).

## <a name="key-terminology-and-components"></a>Terminologia e componenti chiave

I componenti di Windows seguenti svolgono un ruolo chiave nella richiesta e nell'uso di un token di aggiornamento primario:

* **Provider di autenticazione cloud** (CloudAP): CloudAP è il provider di autenticazione moderno per l'accesso a Windows, che verifica l'accesso degli utenti a un dispositivo Windows 10. CloudAP fornisce un framework di plug-in che i provider di identità possono usare per consentire l'autenticazione in Windows tramite le credenziali del provider di identità stesso.
* **Gestione account Web** (WAM, Web Account Manager): WAM è il broker di token predefinito nei dispositivi Windows 10. WAM fornisce anche un framework di plug-in che i provider di identità possono usare per abilitare l'accesso SSO alle loro applicazioni tramite il provider di identità stesso.
* **Plug-in CloudAP di Azure AD**: plug-in specifico di Azure AD basato sul framework CloudAP, che verifica le credenziali utente con Azure AD durante l'accesso a Windows.
* **Plug-in WAM di Azure AD**: plug-in specifico di Azure AD basato sul framework WAM, che consente l'accesso SSO alle applicazioni che usano Azure AD per l'autenticazione.
* **Dsreg**: componente specifico di Azure AD in Windows 10, che gestisce il processo di registrazione dei dispositivi per tutti gli stati dei dispositivi.
* **Trusted Platform Module** (TPM): un TPM è un componente hardware integrato in un dispositivo, che fornisce funzioni di sicurezza basate su hardware per i segreti di utenti e dispositivi. Per maggiori informazioni, vedere l'articolo [Panoramica della tecnologia Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Che cosa contiene un token di aggiornamento primario?

Un token di aggiornamento primario contiene attestazioni generalmente incluse in un token di aggiornamento di Azure AD. Il token di aggiornamento primario contiene anche alcune attestazioni specifiche del dispositivo. Ecco quali sono:

* **ID dispositivo**: un token di aggiornamento primario viene rilasciato a un utente in un dispositivo specifico. L'attestazione dell'ID dispositivo `deviceID` determina il dispositivo in cui è stato rilasciato il token di aggiornamento primario per l'utente. Questa attestazione viene successivamente rilasciata ai token ottenuti tramite il token di aggiornamento primario. L'attestazione dell'ID dispositivo viene usata per determinare l'autorizzazione per l'accesso condizionale in base allo stato o alla conformità del dispositivo.
* **Chiave della sessione**: la chiave della sessione è una chiave simmetrica crittografata, generata dal servizio di autenticazione di Azure AD, rilasciata come parte del token di aggiornamento primario. La chiave della sessione funge da dimostrazione del possesso quando viene usato un token di aggiornamento primario per ottenere token per altre applicazioni.

### <a name="can-i-see-whats-in-a-prt"></a>È possibile vedere il contenuto di un token di aggiornamento primario?

Un token di aggiornamento primario è un BLOB opaco inviato da Azure AD il cui contenuto non è noto ad alcun componente client. Non è possibile vedere il contenuto di un token di aggiornamento primario.

## <a name="how-is-a-prt-issued"></a>Come viene rilasciato un token di aggiornamento primario?

La registrazione del dispositivo è un prerequisito per l'autenticazione basata su dispositivo in Azure AD. Un token di aggiornamento primario viene rilasciato agli utenti solo nei dispositivi registrati. Per informazioni più dettagliate sulla registrazione dei dispositivi, vedere l'articolo [Windows Hello for Business e registrazione dei dispositivi](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante la registrazione del dispositivo, il componente dsreg genera due set di coppie di chiavi crittografiche:

* Chiave del dispositivo (dkpub/dkpriv)
* Chiave di trasporto (tkpub/tkpriv)

Le chiavi private sono associate al TPM del dispositivo se il dispositivo dispone di un TPM valido e funzionante, mentre le chiavi pubbliche vengono inviate ad Azure AD durante il processo di registrazione del dispositivo. Queste chiavi vengono usate per convalidare lo stato del dispositivo durante le richieste del token di aggiornamento primario.

Il token di aggiornamento primario viene rilasciato durante l'autenticazione utente in un dispositivo Windows 10 in due scenari:

* **Aggiunto ad Azure AD** o **Aggiunto ad Azure AD ibrido**: un token di aggiornamento primario viene rilasciato durante l'accesso a Windows quando un utente accede con le credenziali aziendali. Un token di aggiornamento primario viene rilasciato con tutte le credenziali supportate da Windows 10, ad esempio password e Windows Hello for Business. In questo scenario il plug-in CloudAP di Azure AD è l'autorità primaria per il token di aggiornamento primario.
* **Dispositivo registrato in Azure AD**: un token di aggiornamento primario viene rilasciato quando un utente aggiunge un account aziendale secondario nel dispositivo Windows 10. Gli utenti possono aggiungere un account in Windows 10 in due modi diversi:  
   * Tramite il prompt **Usa questo account ovunque nel dispositivo** dopo l'accesso a un'app, ad esempio Outlook
   * Da **Impostazioni** > **Account** > **Accedi all'azienda o all'istituto di istruzione** > **Connetti**

Negli scenari con dispositivi registrati in Azure AD, il plug-WAM di Azure AD è l'autorità primaria per il token di aggiornamento primario perché l'accesso a Windows non viene effettuato con questo account Azure AD.

> [!NOTE]
> I provider di identità di terze parti devono supportare il protocollo WS-Trust per consentire il rilascio dei token di aggiornamento primario nei dispositivi Windows 10. Senza WS-Trust, non è possibile rilasciare PRT per gli utenti in Azure AD ibrido aggiunti o Azure AD dispositivi aggiunti. Solo in ADFS sono necessari endpoint usernamemixed. ADFS/Services/Trust/2005/windowstransport e ADFS/Services/Trust/13/windowstransport devono essere abilitati solo come endpoint con accesso alla Intranet e **non devono essere esposti** come endpoint con rete Extranet tramite il proxy applicazione Web

## <a name="what-is-the-lifetime-of-a-prt"></a>Qual è la durata di un token di aggiornamento primario?

Una volta rilasciato, un token di aggiornamento primario è valido per 14 giorni e viene rinnovato continuamente fino a quando l'utente usa attivamente il dispositivo.  

## <a name="how-is-a-prt-used"></a>Come viene usato un token di aggiornamento primario?

Un token di aggiornamento primario viene usato da due componenti chiave in Windows:

* **Plug-in CloudAP di Azure AD**: durante l'accesso a Windows, il plug-in CloudAP di Azure AD richiede un token di aggiornamento primario da Azure AD con le credenziali fornite dall'utente. Il token di aggiornamento primario viene anche memorizzato nella cache per consentire l'accesso dalla cache quando l'utente non dispone di una connessione Internet.
* **Plug-in WAM di Azure AD**: quando gli utenti provano ad accedere alle applicazioni, il plug-in WAM di Azure AD usa il token di aggiornamento primario per abilitare l'accesso SSO in Windows 10. Il plug-in WAM di Azure AD usa il token di aggiornamento primario per richiedere i token di aggiornamento e di accesso per le applicazioni che si basano su WAM per le richieste di token. Viene inoltre abilitato l'accesso SSO nei browser inserendo il token di aggiornamento primario nelle richieste del browser. L'accesso SSO per i browser in Windows 10 è supportato in Microsoft Edge (in modalità nativa) e Chrome (tramite le estensioni [Windows 10 Accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) o [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en)).

## <a name="how-is-a-prt-renewed"></a>Come viene rinnovato un token di aggiornamento primario?

Un token di aggiornamento primario viene rinnovato in due modi diversi:

* **Plug-in CloudAP di Azure AD ogni 4 ore**: il plug-in CloudAP rinnova il token di aggiornamento primario ogni 4 ore durante l'accesso a Windows. Se l'utente non ha una connessione Internet durante tale intervallo di tempo, il plug-in CloudAP rinnova il token di aggiornamento primario quando il dispositivo si connette a Internet.
* **Plug-in WAM di Azure AD durante le richieste di token delle app**: il plug-in WAM consente l'accesso SSO nei dispositivi Windows 10 abilitando le richieste automatiche di token per le applicazioni. Il plug-in WAM può rinnovare il token di aggiornamento primario durante queste richieste di token in due modi diversi:
   * Un'app richiede a WAM un token di accesso in modo automatico, ma non è disponibile alcun token di aggiornamento per tale app. In questo caso, WAM usa il token di aggiornamento primario per richiedere un token per l'app e restituisce un nuovo token di aggiornamento primario nella risposta.
   * Un'app richiede WAM per un token di accesso, ma il PRT non è valido o Azure AD richiede un'autorizzazione aggiuntiva, ad esempio Azure AD Multi-Factor Authentication. In questo scenario, WAM avvia un accesso interattivo che richiede all'utente di eseguire di nuovo l'autenticazione o di fornire una verifica aggiuntiva e al completamento dell'autenticazione viene rilasciato un nuovo token di aggiornamento primario.

In un ambiente ADFS, non è necessario che la riga di controllo diretta per il controller di dominio rinnovi il PRT. Il rinnovo PRT richiede solo gli endpoint/ADFS/Services/Trust/2005/usernamemixed e/ADFS/Services/Trust/13/usernamemixed abilitati sul proxy usando il protocollo WS-Trust.

Gli endpoint di trasporto di Windows sono necessari per l'autenticazione della password solo quando viene modificata una password, non per il rinnovo PRT.

### <a name="key-considerations"></a>Considerazioni sulle chiavi

* Un token di aggiornamento primario viene rilasciato e rinnovato solo durante l'autenticazione dell'app nativa. Un token di aggiornamento primario non viene rinnovato o rilasciato durante una sessione del browser.
* Nei dispositivi aggiunti ad Azure AD e aggiunti ad Azure AD ibrido, il plug-in CloudAP è l'autorità primaria per un token di aggiornamento primario. Se un token di aggiornamento primario viene rinnovato durante una richiesta di token basata su WAM, il token di aggiornamento primario viene restituito al plug-in CloudAP, che ne verifica la validità con Azure AD prima di accettarlo.

## <a name="how-is-the-prt-protected"></a>Come viene protetto il token di aggiornamento primario?

Un token di aggiornamento primario viene protetto mediante l'associazione al dispositivo in cui l'utente ha effettuato l'accesso. Azure AD e Windows 10 consentono la protezione del token di aggiornamento primario nei modi seguenti:

* **Durante il primo accesso**: durante il primo accesso, viene rilasciato un token di aggiornamento primario firmando le richieste usando la chiave del dispositivo generata crittograficamente durante la registrazione del dispositivo. In un dispositivo con un TPM valido e funzionante, la chiave del dispositivo è protetta dal TPM che impedisce l'accesso da parte di utenti malintenzionati. Un token di aggiornamento primario non viene rilasciato se non è possibile convalidare la firma della chiave del dispositivo corrispondente.
* **Durante le richieste e il rinnovo di token**: quando viene rilasciato un token di aggiornamento primario, Azure AD rilascia anche una chiave della sessione crittografata per il dispositivo. La crittografia viene eseguita con la chiave di trasporto pubblica (tkpub) generata e inviata a Azure AD come parte della registrazione del dispositivo. Questa chiave della sessione può essere decrittografata solo tramite la chiave di trasporto privata (tkpriv) protetta dal TPM. La chiave della sessione è la chiave di dimostrazione del possesso (PoP, Proof-of-Possession) per tutte le richieste inviate ad Azure AD.  Anche la chiave della sessione è protetta dal TPM e nessun altro componente del sistema operativo può accedervi. Le richieste di token o le richieste di rinnovo del token di aggiornamento primario sono firmate in modo sicuro da questa chiave della sessione tramite il TPM e pertanto non possono essere manomesse. Azure AD invalida tutte le richieste provenienti dal dispositivo che non sono firmate dalla chiave della sessione corrispondente.

Grazie alla protezione di queste chiavi con il TPM, gli utenti malintenzionati non possono rubare le chiavi né riprodurre il token di aggiornamento primario altrove perché il TPM non è accessibile anche se un utente malintenzionato ha il possesso fisico del dispositivo.  L'uso di un TPM migliora quindi significativamente la sicurezza dei dispositivi aggiunti ad Azure AD, aggiunti ad Azure AD ibrido e registrati in Azure AD, proteggendoli dal furto di credenziali. Per garantire prestazioni e affidabilità, è consigliata la versione TPM 2.0 per tutti gli scenari di registrazione di dispositivi Azure AD in Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Come vengono protetti i token delle app e i cookie del browser?

**Token delle app**: quando un'app richiede un token tramite WAM, Azure AD rilascia un token di aggiornamento e un token di accesso. Tuttavia, WAM restituisce solo il token di accesso all'app e protegge il token di aggiornamento nella relativa cache crittografandolo con la chiave DPAPI (Data Protection Application Programming Interface) dell'utente. WAM usa in modo sicuro il token di aggiornamento firmando le richieste con la chiave della sessione per rilasciare ulteriori token di accesso. La chiave DPAPI è protetta da una chiave simmetrica basata su Azure AD in Azure AD stesso. Quando il dispositivo deve decrittografare il profilo utente con la chiave DPAPI, Azure AD fornisce la chiave DPAPI crittografata dalla chiave della sessione, che il plug-in CloudAP richiede al TPM di decrittografare. Questa funzionalità garantisce la coerenza nella protezione dei token di aggiornamento ed evita che le applicazioni implementino meccanismi di protezione individuali.  

**Cookie del browser**: in Windows 10 Azure AD supporta l'accesso SSO del browser in Internet Explorer e Microsoft Edge in modalità nativa o in Google Chrome tramite l'estensione Windows 10 Accounts. La sicurezza è integrata non solo per proteggere i cookie, ma anche gli endpoint a cui i cookie vengono inviati. I cookie del browser sono protetti nello stesso modo del token di aggiornamento primario, usando la chiave della sessione per firmare e proteggere i cookie.

Quando un utente avvia un'interazione nel browser, il browser (o l'estensione) richiama un host client nativo COM. L'host client nativo verifica che la pagina provenga da uno dei domini consentiti. Il browser può inviare altri parametri all'host client nativo, incluso un oggetto nonce, tuttavia l'host client nativo garantisce la convalida del nome host. L'host client nativo richiede un cookie del token di aggiornamento primario al plug-in CloudAP, che lo crea e lo firma con la chiave della sessione protetta dal TPM. Poiché il cookie del token di aggiornamento primario è firmato dalla chiave della sessione, non può essere manomesso. Il cookie del token di aggiornamento primario è incluso nell'intestazione della richiesta per consentire ad Azure AD di convalidare il dispositivo da cui proviene. Se si usa il browser Chrome, solo l'estensione definita in modo esplicito nel manifesto dell'host client nativo può eseguire la richiamata, impedendo alle estensioni arbitrarie di effettuare le richieste. Dopo che Azure AD ha convalidato il cookie del token di aggiornamento primario, rilascia un cookie di sessione per il browser. Questo cookie di sessione contiene anche la stessa chiave della sessione rilasciata con un token di aggiornamento primario. Durante le richieste successive, la chiave della sessione viene convalidata in modo efficace associando il cookie al dispositivo e impedendo le riproduzione da altre posizioni.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando un token di aggiornamento primario riceve un'attestazione MFA?

Un token di aggiornamento primario può ottenere un'attestazione MFA (Multi-Factor Authentication) in scenari specifici. Quando si usa un token di aggiornamento primario basato su MFA per richiedere token per le applicazioni, l'attestazione MFA viene trasferita a tali token delle app. Questa funzionalità offre un'esperienza uniforme agli utenti, evitando le richieste di autenticazione MFA per ogni app che la richiede. Un token di aggiornamento primario può ottenere un'attestazione MFA nei modi seguenti:

* **Accesso con Windows Hello for Business**: Windows Hello for Business sostituisce le password e usa le chiavi crittografiche per offrire un'autenticazione a due fattori avanzata. Windows Hello for Business è una funzionalità specifica di un utente su un dispositivo e a sua volta richiede MFA per il provisioning. Quando un utente accede con Windows Hello for Business, il relativo token di aggiornamento primario ottiene un'attestazione MFA. Questo scenario si applica anche agli utenti che accedono con smart card se l'autenticazione tramite smart card genera un'attestazione MFA da ADFS.
   * Poiché Windows Hello for Business è considerato un tipo di autenticazione a più fattori, l'attestazione MFA viene aggiornata quando si aggiorna il token di aggiornamento primario stesso, quindi la durata dell'autenticazione a più fattori viene estesa continuamente quando gli utenti eseguono l'accesso con Windows Hello for Business
* **MFA durante l'accesso interattivo WAM**: durante una richiesta di token tramite WAM, se è necessario che un utente esegua l'autenticazione a più fattori per accedere all'app, il token di aggiornamento primario rinnovato durante questa interazione viene contrassegnato con un'attestazione MFA.
   * In questo caso, l'attestazione MFA non viene aggiornata continuamente, quindi la durata dell'autenticazione a più fattori è basata sulla durata impostata nella directory.
   * Quando vengono usati un token di aggiornamento primario e un token di aggiornamento esistenti per l'accesso a un'app, i due token vengono considerati come la prima prova di autenticazione. Un nuovo token di accesso verrà richiesto con una seconda prova e un'attestazione MFA contrassegnata. Verranno anche rilasciati un nuovo token di aggiornamento primario e un token di aggiornamento.
* **MFA durante la registrazione del dispositivo**: se un amministratore ha configurato le impostazioni del dispositivo in Azure AD per [richiedere la registrazione dei dispositivi tramite MFA](device-management-azure-portal.md#configure-device-settings), l'utente deve eseguire l'autenticazione a più fattori per completare la registrazione. Durante questo processo, il token di aggiornamento primario rilasciato all'utente dispone dell'attestazione MFA ottenuta durante la registrazione. Questa funzionalità si applica solo all'utente che ha eseguito l'operazione di aggiunta e non ad altri utenti che accedono a tale dispositivo.
   * Analogamente all'accesso interattivo WAM, l'attestazione MFA non viene aggiornata continuamente, quindi la durata dell'autenticazione a più fattori è basata sulla durata impostata nella directory.

Windows 10 gestisce un elenco partizionato di token di aggiornamento primario per ogni credenziale. È quindi presente un token di aggiornamento primario per ogni credenziale di tipo Windows Hello for Business, password o smart card. Questo partizionamento garantisce che le attestazioni MFA siano isolate in base alle credenziali usate e non siano confuse durante le richieste di token.

## <a name="how-is-a-prt-invalidated"></a>Come viene invalidato un token di aggiornamento primario?

Un token di aggiornamento primario viene invalidato negli scenari seguenti:

* **Utente non valido**: se un utente viene eliminato o disabilitato in Azure AD, il relativo token di aggiornamento primario viene invalidato e non può essere usato per ottenere i token per le applicazioni. Se un utente eliminato o disabilitato ha già eseguito l'accesso a un dispositivo in precedenza, l'accesso dalla cache consente all'utente la connessione, fino a quando CloudAP non rileva lo stato non valido. Quando CloudAP determina che l'utente non è valido, blocca gli accessi successivi. A un utente non valido viene automaticamente impedito l'accesso ai nuovi dispositivi che non dispongono di credenziali memorizzate nella cache.
* **Dispositivo non valido**: se un dispositivo viene eliminato o disabilitato in Azure AD, il token di aggiornamento primario ottenuto nel dispositivo viene invalidato e non può essere usato per ottenere i token per altre applicazioni. Se un utente ha già eseguito l'accesso a un dispositivo non valido, può continuare a farlo. Tuttavia, tutti i token nel dispositivo vengono invalidati e l'utente non dispone di accesso SSO a nessuna risorsa in tale dispositivo.
* **Modifica della password**: dopo che un utente ha modificato la password, il token di aggiornamento primario ottenuto con la password precedente viene invalidato da Azure AD. In seguito alla modifica della password, l'utente ottiene un nuovo token di aggiornamento primario. Questo invalidamento può avvenire in due modi diversi:
   * Se l'utente accede a Windows con la nuova password, CloudAP rimuove il vecchio token di aggiornamento primario e richiede ad Azure AD di rilasciare un nuovo token di aggiornamento primario con la nuova password. Se l'utente non dispone di una connessione Internet, la nuova password non può essere convalidata e Windows può richiedere all'utente di immettere la vecchia password.
   * Se un utente ha eseguito l'accesso con la password precedente o ha modificato la password dopo l'accesso a Windows, il token di aggiornamento primario precedente viene usato per tutte le richieste di token basate su WAM. In questo scenario, all'utente viene richiesto di ripetere l'autenticazione durante la richiesta di token WAM e viene rilasciato un nuovo token di aggiornamento primario.
* **Problemi del TPM**: talvolta può verificarsi un errore o un problema del TPM di un dispositivo che impedisce l'accesso alle chiavi protette dal TPM. In questo caso, il dispositivo non è in grado di ottenere un token di aggiornamento primario o di richiedere token usando un token di aggiornamento primario esistente perché non è in grado di dimostrare il possesso delle chiavi crittografiche. Di conseguenza, qualsiasi token di aggiornamento primario esistente viene invalidato da Azure AD. Quando Windows 10 rileva un errore, avvia un flusso di ripristino per registrare di nuovo il dispositivo con nuove chiavi crittografiche. Con l'aggiunta ad Azure AD ibrido, analogamente alla registrazione iniziale, il ripristino viene eseguito automaticamente senza l'input dell'utente. Per i dispositivi aggiunti ad Azure AD o registrati in Azure AD, il ripristino deve essere eseguito da un utente che dispone di privilegi di amministratore nel dispositivo. In questo scenario, il flusso di ripristino viene avviato da un prompt di Windows che guida l'utente nel ripristino del dispositivo.

## <a name="detailed-flows"></a>Flussi dettagliati

I diagrammi seguenti illustrano i dettagli sottostanti relativi a rilascio, rinnovo e uso di un token di aggiornamento primario per richiedere un token di accesso per un'applicazione. Questi passaggi descrivono inoltre come vengono applicati i meccanismi di sicurezza illustrati in precedenza durante queste interazioni.

### <a name="prt-issuance-during-first-sign-in"></a>Rilascio del token di aggiornamento primario durante il primo accesso

![Flusso dettagliato di rilascio del token di aggiornamento primario durante il primo accesso](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Nei dispositivi aggiunti ad Azure AD questo scambio avviene in modo sincrono per rilasciare un token di aggiornamento primario prima che l'utente possa accedere a Windows. Nei dispositivi aggiunti ad Azure AD ibrido, l'istanza locale di Active Directory locale è l'autorità primaria. L'utente quindi attende solo fino a quando non acquisisce un ticket TGT per l'accesso, mentre il rilascio del token di aggiornamento primario si verifica in modo asincrono. Questo scenario non si applica ai dispositivi registrati in Azure AD in quanto per l'accesso non vengono usate le credenziali di Azure AD.

| Passaggio | Descrizione |
| :---: | --- |
| A | L'utente immette la sua password nell'interfaccia utente di accesso. L'interfaccia utente di accesso passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP inoltra la richiesta al plug-in CloudAP. |
| B | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider di servizi di federazione, Azure AD restituisce l'endpoint MEX (Metadata Exchange endpoint) del provider di servizi di federazione. In caso contrario, Azure AD indica che l'utente è gestito, specificando che può eseguire l'autenticazione con Azure AD. |
| C | Se l'utente è gestito, CloudAP ottiene l'oggetto nonce da Azure AD. Se l'utente è federato, il plug-in CloudAP richiede un token SAML al provider di servizi di federazione con le credenziali dell'utente. Una volta ricevuto il token SAML, richiede un oggetto nonce ad Azure AD. |
| D | Il plug-in CloudAP crea la richiesta di autenticazione con le credenziali dell'utente, l'oggetto nonce e un ambito del broker, firma la richiesta con la chiave del dispositivo (dkpriv) e la invia ad Azure AD. In un ambiente federato il plug-in CloudAP usa il token SAML restituito dal provider di servizi di federazione anziché le credenziali dell'utente. |
| E | Azure AD convalida le credenziali dell'utente, l'oggetto nonce e la firma del dispositivo, verifica che il dispositivo sia valido nel tenant e rilascia il token di aggiornamento primario crittografato. Insieme al token di aggiornamento primario, Azure AD rilascia anche una chiave simmetrica, denominata chiave della sessione, crittografata da Azure AD tramite la chiave di trasporto (tkpub). La chiave della sessione è anche incorporata nel token di aggiornamento primario. La chiave della sessione funge da chiave di dimostrazione del possesso (PoP, Proof-of-Possession) per le successive richieste con il token di aggiornamento primario. |
| F | Il plug-in CloudAP passa il token di aggiornamento primario e la chiave della sessione crittografati a CloudAP. CloudAP richiede al TPM di decrittografare la chiave della sessione usando la chiave di trasporto (tkpriv) e di crittografarla di nuovo usando la chiave del TPM stesso. CloudAP archivia la chiave della sessione crittografata nella cache insieme al token di aggiornamento primario. |

### <a name="prt-renewal-in-subsequent-logons"></a>Rinnovo del token di aggiornamento primario agli accessi successivi

![Rinnovo del token di aggiornamento primario agli accessi successivi](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passaggio | Descrizione |
| :---: | --- |
| A | L'utente immette la sua password nell'interfaccia utente di accesso. L'interfaccia utente di accesso passa le credenziali in un buffer di autenticazione a LSA, che a sua volta le passa internamente a CloudAP. CloudAP inoltra la richiesta al plug-in CloudAP. |
| B | Se l'utente ha già eseguito l'accesso in precedenza, Windows avvia l'accesso dalla cache e convalida le credenziali per consentire all'utente di accedere. Ogni 4 ore il plug-in CloudAP avvia il rinnovo del token di aggiornamento primario in modo asincrono. |
| C | Il plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di una configurazione del provider di servizi di federazione, Azure AD restituisce l'endpoint MEX (Metadata Exchange endpoint) del provider di servizi di federazione. In caso contrario, Azure AD indica che l'utente è gestito, specificando che può eseguire l'autenticazione con Azure AD. |
| D | Se l'utente è federato, il plug-in CloudAP richiede un token SAML al provider di servizi di federazione con le credenziali dell'utente. Una volta ricevuto il token SAML, richiede un oggetto nonce ad Azure AD. Se l'utente è gestito, CloudAP ottiene l'oggetto nonce direttamente da Azure AD. |
| E | Il plug-in CloudAP crea la richiesta di autenticazione con le credenziali dell'utente, l'oggetto nonce e il token di aggiornamento primario esistente, firma la richiesta con la chiave della sessione e la invia ad Azure AD. In un ambiente federato il plug-in CloudAP usa il token SAML restituito dal provider di servizi di federazione anziché le credenziali dell'utente. |
| F | Azure AD convalida la firma della chiave della sessione confrontandola con la chiave della sessione incorporata nel token di aggiornamento primario, convalida l'oggetto nonce, verifica che il dispositivo sia valido nel tenant e rilascia un nuovo token di aggiornamento primario. Come illustrato in precedenza, il token di aggiornamento primario viene di nuovo associato alla chiave della sessione crittografata dalla chiave di trasporto (tkpub). |
| G | Il plug-in CloudAP passa il token di aggiornamento primario e la chiave della sessione crittografati a CloudAP. CloudAP richiede al TPM di decrittografare la chiave della sessione usando la chiave di trasporto (tkpriv) e di crittografarla di nuovo usando la chiave del TPM stesso. CloudAP archivia la chiave della sessione crittografata nella cache insieme al token di aggiornamento primario. |

> [!NOTE]
> Un PRT può essere rinnovato esternamente senza la necessità di una connessione VPN quando gli endpoint usernamemixed sono abilitati esternamente.

### <a name="prt-usage-during-app-token-requests"></a>Uso del token di aggiornamento primario durante le richieste di token dell'app

![Uso del token di aggiornamento primario durante le richieste di token dell'app](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passaggio | Descrizione |
| :---: | --- |
| A | Un'applicazione, ad esempio Outlook, OneNote e così via, avvia una richiesta di token per WAM. WAM, a sua volta, chiede al plug-in WAM di Azure AD di rispondere alla richiesta di token. |
| B | Se è già disponibile un token di aggiornamento per l'applicazione, il plug-in WAM di Azure AD lo usa per richiedere un token di accesso. Per fornire la prova dell'associazione del dispositivo, il plug-in WAM firma la richiesta con la chiave della sessione. Azure AD convalida la chiave della sessione e rilascia un token di accesso e un nuovo token di aggiornamento per l'app, crittografati dalla chiave della sessione. Il plug-in WAM richiede al plug-in CloudAP di decrittografare i token e questo, a sua volta, richiede al TPM di eseguire la decrittografia usando la chiave della sessione. Di conseguenza, il plug-in WAM ottiene entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre esegue di nuovo la crittografia del token di aggiornamento con DPAPI e lo archivia nella propria cache.  |
| C |  Se non è disponibile un token di aggiornamento per l'applicazione, il plug-in WAM di Azure AD usa il token di aggiornamento primario per richiedere un token di accesso. Per fornire la dimostrazione del possesso, il plug-in WAM firma la richiesta contenente il token di aggiornamento primario con la chiave della sessione. Azure AD convalida la firma della chiave della sessione confrontandola con la chiave della sessione incorporata nel token di aggiornamento primario, verifica che il dispositivo sia valido e rilascia un token di accesso e un token di aggiornamento per l'applicazione. Inoltre, Azure AD può rilasciare un nuovo token di aggiornamento primario (in base al ciclo di aggiornamento), con la crittografia della chiave della sessione. |
| D | Il plug-in WAM richiede al plug-in CloudAP di decrittografare i token e questo, a sua volta, richiede al TPM di eseguire la decrittografia usando la chiave della sessione. Di conseguenza, il plug-in WAM ottiene entrambi i token. Successivamente, il plug-in WAM fornisce solo il token di accesso all'applicazione, mentre esegue di nuovo la crittografia del token di aggiornamento con DPAPI e lo archivia nella propria cache. Da questo momento, il plug-in WAM userà il token di aggiornamento per l'applicazione. Il plug-in WAM restituisce anche il nuovo token di aggiornamento primario al plug-in CloudAP, che convalida il token di aggiornamento primario con Azure AD prima di aggiornarlo nella propria cache. Da questo momento, il plug-in CloudAP userà il nuovo token di aggiornamento primario. |
| E | WAM fornisce il nuovo token di accesso rilasciato a WAM, che a sua volta lo restituisce all'applicazione chiamante.|

### <a name="browser-sso-using-prt"></a>Accesso SSO del browser con il token di aggiornamento primario

![Accesso SSO del browser con il token di aggiornamento primario](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passaggio | Descrizione |
| :---: | --- |
| A | L'utente accede a Windows con le proprie credenziali per ottenere un token di aggiornamento primario. Quando l'utente apre il browser, il browser o l'estensione carica gli URL dal registro. |
| B | Quando un utente apre un URL di accesso di Azure AD, il browser o l'estensione convalida l'URL con quelli ottenuti dal registro. Se corrispondono, il browser richiama l'host client nativo per ottenere un token. |
| C | L'host client nativo verifica che gli URL appartengano ai provider di identità Microsoft (account Microsoft o Azure AD), estrae un oggetto nonce inviato dall'URL ed effettua una chiamata al plug-in CloudAP per ottenere un cookie del token di aggiornamento primario. |
| D | Il plug-in CloudAP crea il cookie del token di aggiornamento primario, effettua l'accesso con la chiave della sessione associata al TPM e invia di nuovo il cookie all'host client nativo. Poiché il cookie è firmato dalla chiave della sessione, non può essere manomesso. |
| E | L'host client nativo restituisce il cookie del token di aggiornamento primario al browser, che lo include come parte dell'intestazione della richiesta denominata x-ms-RefreshTokenCredential e richiede i token ad Azure AD. |
| F | Azure AD convalida la firma della chiave della sessione nel cookie del token di aggiornamento primario, convalida l'oggetto nonce, verifica che il dispositivo sia valido nel tenant e rilascia un token ID per la pagina Web e un cookie di sessione crittografato per il browser. |

> [!NOTE]
> Il flusso SSO del browser descritto nei passaggi precedenti non si applica alle sessioni in modalità private, ad esempio InPrivate in Microsoft Edge, o in incognito in Google Chrome (quando si usa l'estensione degli account Microsoft).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi correlati ai token di aggiornamento primario, vedere l'articolo [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).
