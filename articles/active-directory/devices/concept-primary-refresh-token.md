---
title: Token di aggiornamento primari (PRT) e Azure Active Directory - Azure Active Directory
description: Che cos'è il ruolo del e come si gestisce il primario Refresh Token Qualcosa in Azure Active Directory?
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
ms.openlocfilehash: 16e4a5f63ba80b02a967888ad76fedf165a576c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473392"
---
# <a name="what-is-a-primary-refresh-token"></a>Che cos'è un Token di aggiornamento primario?

Un principale Refresh Token Qualcosa è un elemento chiave di autenticazione di Azure AD su dispositivi Android, iOS e Windows 10. Si tratta di un JSON Web Token (token JWT) rilasciati in modo speciale da Microsoft prima entità token Broker per abilitare il single sign-on (SSO) tra le applicazioni usate in tali dispositivi. In questo articolo, Microsoft fornirà informazioni dettagliate su come un PRT viene rilasciato, usato e protetti nei dispositivi Windows 10.

Questo articolo si presuppone che se si conoscono già gli stati di dispositivo diversi disponibili in Azure AD e funzionamento di single sign-on funziona in Windows 10. Per altre informazioni sui dispositivi in Azure AD, vedere l'articolo [che cos'è gestione dei dispositivi in Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Componenti e la terminologia chiave

I seguenti componenti di Windows svolgono un ruolo chiave nella richiesta e usando un PRT:

* **Provider di autenticazione cloud** (CloudAP): CloudAP è il provider di autenticazione moderne per Windows l'accesso, che verifica gli utenti che accedono a un dispositivo Windows 10. CloudAP fornisce un framework di plug-in tale identità per i provider possono creare in per abilitare l'autenticazione di Windows utilizzando le credenziali del provider di identità.
* **Gestione Account Web** (WAM): WAM è il gestore di token predefinito nei dispositivi Windows 10. WAM fornisce inoltre un framework di plug-in tale identità provider può compilare in e abilitare l'accesso SSO alle applicazioni basarsi su tale provider di identità.
* **Plug-in Azure AD CloudAP**: Un Azure AD specifico plug-in basato su framework CloudAP, che verifica che le credenziali utente con Azure AD in fase di accesso Windows.
* **Plug-in Azure AD WAM**: Un Azure AD specifico plug-in basato su framework Web, che consente l'accesso SSO alle applicazioni che si basano su Azure AD per l'autenticazione.
* **Dsreg**: Un componente specifico di Azure AD in Windows 10, che gestisce il processo di registrazione dispositivi per tutti gli stati di dispositivo.
* **Trusted Platform Module** (TPM): Un modulo TPM è un componente hardware incorporato in un dispositivo, che offre funzioni di protezione hardware per i segreti di utenti e dispositivi. Altre informazioni sono reperibili nell'articolo [Trusted Platform Module tecnologia Overview](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Cosa contiene la PRT?

Un PRT contiene le attestazioni in genere contenute in qualsiasi token di aggiornamento di Azure AD. Inoltre, esistono alcune attestazioni specifiche del dispositivo incluse nella PRT. Ecco quali sono:

* **ID dispositivo**: Un PRT viene emesso a un utente in un dispositivo specifico. L'attestazione dell'ID dispositivo `deviceID` determina la PRT emesso per l'utente nel dispositivo. Questa attestazione viene generata in un secondo momento per i token ottenuti tramite la PRT. L'attestazione dell'ID dispositivo viene usato per determinare l'autorizzazione per l'accesso condizionale in base allo stato del dispositivo o di conformità.
* **Chiave della sessione**: La chiave della sessione è una chiave simmetrica crittografata, generato dal servizio di autenticazione di Azure AD, rilasciato come parte della PRT. La chiave di sessione funge dalla prova di possesso durante un PRT viene usato per ottenere i token per le altre applicazioni.

### <a name="can-i-see-whats-in-a-prt"></a>Posso vedere quali sono le novità un PRT?

Un PRT è blob opaco inviato da Azure AD il cui contenuto non è noti a tutti i componenti client. Non è possibile visualizzare ciò che si trova all'interno di un PRT.

## <a name="how-is-a-prt-issued"></a>Modo in cui viene emesso un PRT?

Registrazione dei dispositivi è un prerequisito per l'autenticazione basati su dispositivo di Azure AD. Viene generato un PRT agli utenti solo in dispositivi registrati. Per altri dettagli sulla registrazione del dispositivo, vedere l'articolo [Windows Hello for Business e registrazione del dispositivo](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Durante la registrazione del dispositivo, il componente dsreg genera due set di coppie di chiavi crittografiche:

* Chiave del dispositivo (dkpub/dkpriv)
* Chiave di trasporto (tkpub/tkpriv)

Le chiavi private vengono associate a TPM del dispositivo se il dispositivo dispone di un TPM valido e che funzionante, mentre le chiavi pubbliche vengono inviate ad Azure AD durante il processo di registrazione dispositivi. Queste chiavi vengono usate per convalidare lo stato del dispositivo durante la PRT richieste.

La PRT viene generata durante l'autenticazione dell'utente in un dispositivo Windows 10 in due scenari:

* **Aggiunto ad Azure AD** oppure **Azure AD ibrido**: Un PRT viene generato durante l'accesso di Windows quando un utente accede con le credenziali dell'organizzazione. Un PRT viene emesso con tutte le credenziali di Windows 10 è supportato, ad esempio, password e Windows Hello for Business. In questo scenario, plug-in Azure AD CloudAP è l'autorità principale per la PRT.
* **Dispositivi registrati in Azure AD**: Un PRT viene generato quando un utente aggiunge un account di lavoro secondari nel dispositivo Windows 10. Gli utenti possono aggiungere un account a Windows 10 in due modi diversi:  
   * Aggiunta di un account tramite il **Usa questo account ovunque in questo dispositivo** dei messaggi di richiesta dopo l'accesso a un'app (ad esempio, Outlook)
   * Aggiunta di un account dal **le impostazioni** > **account** > **accesso azienda o dell'istituto di istruzione** > **Connect**

In questi scenari, il plug-in Azure AD WAM è l'autorità principale per la PRT dall'accesso di Windows non si verifica con questo account di Azure AD.

## <a name="what-is-the-lifetime-of-a-prt"></a>Che cos'è la durata di un PRT?

Una volta emesso, un PRT è valido per 14 giorni e viene rinnovato in modo continuo purché l'utente Usa attivamente il dispositivo.  

## <a name="how-is-a-prt-used"></a>Come viene usato un PRT?

Un PRT viene usato da due componenti principali di Windows:

* **Plug-in Azure AD CloudAP**: Durante l'accesso Windows, il plug-in Azure AD CloudAP richiede un PRT da Azure AD usando le credenziali fornite dall'utente. Memorizza anche la PRT per abilitare accesso memorizzato nella cache quando l'utente non ha accesso a una connessione a internet.
* **Plug-in Azure AD WAM**: Quando gli utenti provano ad accedere alle applicazioni, il plug-in Azure AD WAM Usa la PRT per abilitare SSO in Windows 10. Plug-in Azure AD WAM Usa la PRT per richiedere i token di accesso e aggiornamento per le applicazioni basate su Web per le richieste di token. Inoltre, consente l'accesso SSO nei browser inserendo la PRT nelle richieste del browser. Browser del servizio SSO in Windows 10 è supportato (in modo nativo) in Microsoft Edge e Chrome (tramite l'estensione degli account di Windows 10 o in Office Online).

## <a name="how-is-a-prt-renewed"></a>Come viene rinnovato un PRT?

Un PRT viene rinnovato in due modi diversi:

* **Azure AD CloudAP plug-in di ogni 4 ore**: Il plug-in CloudAP rinnova la PRT ogni 4 ore durante l'accesso di Windows. Se l'utente non ha accesso a Internet durante tale periodo, plug-in CloudAP rinnoverà la PRT dopo che il dispositivo è connesso a internet.
* **Plug-in Azure AD WAM durante le richieste di token di app**: Il plug-in WAM Abilita SSO nei dispositivi Windows 10 per l'abilitazione di richieste di token invisibile all'utente per le applicazioni. Il plug-in WAM possono rinnovare la PRT durante queste richieste di token in due modi diversi:
   * Un'app richiede WAM invisibile all'utente per un token di accesso ma non è disponibile per l'app alcun token di aggiornamento. In questo caso, WAM Usa la PRT per richiedere un token per l'app e riceve un nuovo PRT nella risposta.
   * Un'app per le richieste Web per un token di accesso, ma la PRT non è valida o Azure AD richiede l'autorizzazione aggiuntiva (ad esempio, Azure multi-Factor Authentication). In questo scenario, WAM avvia un accesso interattivo che richiede all'utente di ripetere l'autenticazione o fornire un'ulteriore verifica e viene emesso un nuovo PRT su autenticazione riuscita.

### <a name="key-considerations"></a>Considerazioni sulle chiavi

* Un PRT solo emesso e rinnovata durante l'autenticazione dell'app nativa. Un PRT non viene rinnovato o emessa durante una sessione del browser.
* In Azure AD aggiunti e i dispositivi aggiunti ad Azure AD ibrido, il plug-in CloudAP rappresenta l'autorità principale per un PRT. Se un PRT viene rinnovata durante una richiesta di token basati su Web, la PRT viene inviata al plug-in CloudAP, che consente di verificare la validità della PRT con Azure AD prima di accettarlo.

## <a name="how-is-the-prt-protected"></a>Modalità la PRT di protezione?

Un PRT è protetto dall'associazione per il dispositivo, l'utente abbia eseguito l'accesso. Azure AD e Windows 10 abilitare la protezione PRT tramite i metodi seguenti:

* **Durante il primo accesso**: Durante il primo accesso, viene emesso un PRT effettuando l'accesso delle richieste effettuate utilizzando la chiave di dispositivo a livello di crittografia generata durante la registrazione del dispositivo. In un dispositivo con un TPM valido e che funzionante, la chiave del dispositivo è protetto da TPM impedire eventuali accessi non autorizzati. Un PRT non viene generato se non è possibile convalidare la firma con chiave dispositivo corrispondente.
* **Durante le richieste di token e il rinnovo**: Quando viene rilasciato un PRT, Azure AD rilascia anche una chiave di sessione crittografata al dispositivo. Viene crittografato con la chiave pubblica di trasporto (tkpub) generati e inviati ad Azure AD come parte della registrazione del dispositivo. Questa chiave di sessione può essere decrittografata solo tramite la chiave privata di trasporto (tkpriv) protetta da TPM. La chiave della sessione è la chiave di Proof-of-Possession (POP) per tutte le richieste inviate ad Azure AD.  La chiave della sessione è protetto anche dal TPM e nessun altro componente del sistema operativo può accedervi. Le richieste di rinnovo PRT o richieste di token sono firmate in modo sicuro da questa chiave di sessione tramite il TPM e di conseguenza, non possano essere manomessi. Azure AD invalida tutte le richieste dal dispositivo che non sono firmate mediante la chiave di sessione corrispondente.

Proteggere le chiavi con il TPM, gli attori dannosi non possa rubare le chiavi né la PRT di riproduzione in un' posizione come il TPM non è accessibile anche se un utente malintenzionato ha fisicamente in possesso del dispositivo.  Di conseguenza, notevolmente l'utilizzo di un TPM migliora la sicurezza di Azure AD unita in join ibridi aggiunti ad Azure AD, e i dispositivi dai furti di credenziali registrati in Azure AD. Per prestazioni e affidabilità, TPM 2.0 è la versione consigliata per tutti gli scenari di registrazione dispositivo Azure AD in Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Come vengono protetti i cookie del browser e i token dell'app?

**I token dell'app**: Quando un'app per le richieste di token tramite WAM, Azure AD rilascia un token di aggiornamento e un token di accesso. Tuttavia, solo WAM restituisce il token di accesso all'app e protegge i token di aggiornamento nella propria cache crittografandola con la chiave dell'utente data protection application programmazione interface (DPAPI). WAM Usa in modo sicuro il token di aggiornamento da richieste di firma con la chiave della sessione per un'ulteriore rilasciare token di accesso. La chiave DPAPI è protetto da una chiave simmetrica basata su Azure AD Azure AD. Quando il dispositivo deve decrittografare il profilo utente con la chiave DPAPI, Azure AD fornisce la chiave DPAPI crittografata da cui plugin CloudAP richiede TPM per decrittografare la chiave di sessione. Questa funzionalità garantisce la coerenza nella protezione dei token di aggiornamento ed evita che implementa i propri meccanismi di protezione delle applicazioni.  

**I cookie del browser**: In Windows 10, Azure AD supporta i browser SSO in Internet Explorer e Microsoft Edge in modo nativo o in Google Chrome tramite l'estensione di account di Windows 10. La protezione si basa non solo per proteggere i cookie, ma anche gli endpoint a cui vengono inviati i cookie. I cookie del browser sono protette esattamente un PRT è, utilizzando la chiave della sessione per accedere e proteggere i cookie.

Quando un utente avvia un'interazione di browser, il browser (o l'estensione) richiama un host di native client COM. L'host del client nativo assicura che la pagina deriva da uno dei domini consentiti. Il browser è stato possibile inviare che ad altri parametri per il client nativo ospitano, tra cui un parametro nonce, tuttavia l'host del client nativo garanzie di convalida del nome host. L'host del client nativo richiede un PRT-cookie dal plug-in CloudAP, che crea e viene firmato con la chiave della sessione protette da TPM. Il PRT-cookie viene firmato dalla chiave di sessione, si potrà essere alterato. Questo PRT-cookie è incluso nell'intestazione della richiesta per Azure AD convalidare il dispositivo che è stato originato. Se si usa il browser Chrome, solo l'estensione definito in modo esplicito nel manifesto dell'host client native possa richiamarlo impediscono l'esecuzione di queste richieste estensioni arbitrarie. Dopo che Azure AD convalida il cookie PRT, genera un cookie di sessione nel browser. Questo cookie di sessione contiene inoltre la stessa chiave di sessione rilasciata con una PRT. Durante le richieste successive, la chiave di sessione viene convalidata in modo efficace il cookie di associazione per il dispositivo e impedendo riproduzioni da un' posizione.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Quando un PRT riceve un'attestazione di autenticazione a più fattori?

Un PRT possibile ottenere un'attestazione di autenticazione a più fattori (MFA) in scenari specifici. Quando un PRT basato su MFA viene usato per richiedere token per le applicazioni, l'attestazione MFA viene trasferito a tali token dell'app. Questa funzionalità offre un'esperienza senza problemi agli utenti, impedendo la richiesta di autenticazione MFA per ogni app che lo richiede. Un PRT possibile ottenere un'attestazione MFA nei modi seguenti:

* **Accedere con Windows Hello for Business**: Windows Hello for Business consente di sostituire le password e Usa le chiavi crittografiche per fornire l'autenticazione a due fattori avanzata. Windows Hello for Business è specifico di un utente in un dispositivo e di per sé richiede MFA per eseguire il provisioning. Quando un utente accede con Windows Hello for Business, PRT il suo Ottiene un'attestazione di autenticazione a più fattori. Questo scenario si applica anche agli utenti l'accesso con smart card se l'autenticazione della smart card produce un'attestazione MFA da ad FS.
   * Come Windows Hello for Business è considerata multi-factor authentication, l'attestazione MFA viene aggiornato quando viene aggiornata la PRT stesso, pertanto la durata MFA estenderà continuamente quando gli utenti accedono con WIndows Hello for Business
* **Autenticazione a più fattori durante l'accesso interattivo WAM in**: Durante una richiesta di token tramite WAM, se un utente viene richiesto di eseguire l'autenticazione a più fattori per accedere all'app, la PRT che viene rinnovata durante questa interazione è adesiva con un'attestazione di autenticazione a più fattori.
   * In questo caso, l'attestazione di autenticazione a più fattori non viene aggiornato in modo continuo, in modo che la durata MFA è basata sulla durata impostata sulla directory.
* **Autenticazione a più fattori durante la registrazione del dispositivo**: Se un amministratore ha configurato le impostazioni di dispositivo in Azure AD [richiede l'autenticazione MFA registrare i dispositivi](device-management-azure-portal.md#configure-device-settings), l'utente deve eseguire l'autenticazione a più fattori per completare la registrazione. Durante questo processo, la PRT emesso per l'utente ha l'attestazione MFA ottenuto durante la registrazione. Questa funzionalità si applica solo all'utente che ha eseguito l'operazione di join, non ad altri utenti che accedono a tale dispositivo.
   * Simile al WAM interattiva di accesso, l'attestazione di autenticazione a più fattori non viene aggiornata in modo continuo, in modo che la durata MFA è basata sulla durata impostata sulla directory.

Windows 10 mantiene un elenco partizionato di PRT per ciascuna credenziale. Pertanto, non sia presente un PRT per ognuno di Windows Hello for Business, password o smart card. Tale partizionamento assicura che le attestazioni di autenticazione a più fattori siano isolate in base alla credenziale usato e non confuse durante le richieste di token.

## <a name="how-is-a-prt-invalidated"></a>Come è stato invalidato un PRT?

Un PRT viene invalidata negli scenari seguenti:

* **Utente non valido**: Se un utente viene eliminato o disabilitato in Azure AD, la PRT viene invalidato e non può essere usato per ottenere token per le applicazioni. Se un utente eliminato o disabilitato già effettuato l'accesso a un dispositivo prima di, accedi memorizzato nella cache sarebbero registrarli, fino a quando non CloudAP è a conoscenza del relativo stato non valido. Una volta CloudAP determina che l'utente non è valido, consente di bloccare gli accessi successivi. Un utente non valido viene bloccato dall'accesso automaticamente ai nuovi dispositivi che non hanno le proprie credenziali memorizzate nella cache.
* **Dispositivo non è valido**: Se un dispositivo viene eliminato o disabilitato in Azure AD, la PRT ottenuta in tale dispositivo viene invalidata e non può essere usata per ottenere token per le altre applicazioni. Se un utente è già connesso a un dispositivo non è valido, è possibile continuare a eseguire questa operazione. Ma vengono invalidati tutti i token del dispositivo e l'utente non dispone SSO a tutte le risorse da tale dispositivo.
* **Modifica della password**: Dopo che un utente modifica la password, la PRT ottenuta con la password precedente viene invalidata da Azure AD. I risultati dell'utente, ottenere un nuovo PRT di modifica della password. L'invalidamento questa situazione può verificarsi in due modi diversi:
   * Se l'utente accede a Windows con la nuova password, CloudAP ignora la PRT precedente e richiede Azure AD di rilasciare un nuovo PRT con la nuova password. Se l'utente non dispone di una connessione a internet, la nuova password non può essere convalidata, Windows può richiedere all'utente di immettere la vecchia password.
   * Se un utente ha eseguito l'accesso con la vecchia password o modificato la password dopo l'accesso in Windows, la PRT precedente viene usata per tutte le richieste di token basati su Web. In questo scenario, l'utente viene richiesto di ripetere l'autenticazione durante la richiesta di token WAM e viene emesso un nuovo PRT.
* **Problemi di TPM**: In alcuni casi, TPM un dispositivo può instabilità dell'o esito negativo, causando l'inaccessibilità di chiavi protette dal TPM. In questo caso, il dispositivo è in grado di ottenere un PRT o richiesta di token utilizzando un PRT esistente non è possibile dimostrare che possiede le chiavi di crittografia. Di conseguenza, qualsiasi PRT esistente viene invalidato da Azure AD. Quando Windows 10 rileva un errore, all'avvio di un flusso di ripristino per registrare nuovamente il dispositivo con nuove chiavi di crittografia. Con l'aggiunta Ad Azure Hybrid, esattamente come la registrazione iniziale, il ripristino avviene automaticamente senza l'intervento dell'utente. Per i dispositivi registrati in Azure AD unita in join o in Azure AD, il ripristino deve essere eseguita da un utente con privilegi di amministratore del dispositivo. In questo scenario, il flusso di ripristino viene avviato da un prompt dei comandi di Windows che guidano l'utente per ripristinare correttamente il dispositivo.

## <a name="detailed-flows"></a>Flussi dettagliati

I diagrammi seguenti illustrano i dettagli sottostanti nel rilascio, rinnovare e Usa un PRT per richiedere un token di accesso per un'applicazione. Inoltre, questi passaggi descrivono anche come i meccanismi di sicurezza indicati in precedenza vengono applicati durante queste interazioni.

### <a name="prt-issuance-during-first-sign-in"></a>Rilascio PRT durante il primo accesso

![Rilascio PRT durante il primo accesso flusso dettagliato](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> In Azure AD dispositivi aggiunti all'identità, questo scambio si verifica in modo sincrono per emettere un PRT prima che l'utente può accedere a Windows. In un ambiente ibrido dispositivi aggiunti ad Azure AD, un'istanza locale di Active Directory è l'autorità principale. Pertanto, l'utente è solo in attesa fino a quando non possano acquisire un TGT per account di accesso, mentre il rilascio PRT avviene in modo asincrono. Questo scenario non è applicabile ai dispositivi registrati in Azure AD perché l'accesso non utilizza credenziali di Azure AD.

| Passaggio | Descrizione |
| :---: | --- |
| Una | Utente immette la password di accesso nell'interfaccia utente. LogonUI passa le credenziali in un buffer di autenticazione per LSA, che a sua volta passa internamente a CloudAP. CloudAP inoltra la richiesta per il plug-in CloudAP. |
| b | Plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di un'installazione del provider di federazione, Azure AD restituisce lo scambio di metadati del provider di federazione dell'endpoint di endpoint (MEX). In caso contrario, Azure AD restituisce che l'utente sia gestito che indica che l'utente può eseguire l'autenticazione con Azure AD. |
| C | Se l'utente è gestito, CloudAP otterrà il parametro nonce da Azure AD. Se l'utente è federato, CloudAP plug-in richiede un token SAML dal provider di federazione con le credenziali dell'utente. Una volta ricevuti, il token SAML, richiede un parametro nonce da Azure AD. |
| D | Plug-in CloudAP costruisce la richiesta di autenticazione con le credenziali dell'utente, parametro nonce e un ambito di Service broker, firma la richiesta con la chiave del dispositivo (dkpriv) e li invia ad Azure AD. In un ambiente federato, CloudAP plug-in Usa il token SAML restituito dal provider di federazione invece l'utente ' credenziali. |
| E | Azure AD convalida le credenziali utente, il parametro nonce, e dispositivi firma, verifica che il dispositivo sia valido nel tenant e rilascia la PRT crittografata. Con la PRT, Azure AD rilascia anche una chiave simmetrica denominata chiave di sessione crittografata tramite Azure AD usando la chiave di trasporto (tkpub). Inoltre, la chiave della sessione è incorporata anche nella PRT. Questa chiave di sessione funge da chiave Proof-of-possession (PoP) per le richieste successive con la PRT. |
| F | Plug-in CloudAP passa la chiave di sessione e PRT crittografata CloudAP. CloudAP richiedere il TPM per decrittografare la chiave di sessione usando la chiave di trasporto (tkpriv) e crittografare nuovamente utilizzando la chiave del TPM. CloudAP archivia la chiave di sessione crittografata nella propria cache insieme la PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Rinnovo PRT di successivi accessi

![Rinnovo PRT di successivi accessi](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | Utente immette la password di accesso nell'interfaccia utente. LogonUI passa le credenziali in un buffer di autenticazione per LSA, che a sua volta passa internamente a CloudAP. CloudAP inoltra la richiesta per il plug-in CloudAP. |
| b | Se l'utente ha già eseguito l'accesso all'utente, avvia Windows memorizzato nella cache l'accesso e convalida le credenziali per eseguire l'accesso all'utente. Ogni 4 ore, il plug-in CloudAP avvia rinnovo PRT in modo asincrono. |
| C | Plug-in CloudAP avvia una richiesta di individuazione dell'area di autenticazione per identificare il provider di identità per l'utente. Se il tenant dell'utente dispone di un'installazione del provider di federazione, Azure AD restituisce lo scambio di metadati del provider di federazione dell'endpoint di endpoint (MEX). In caso contrario, Azure AD restituisce che l'utente sia gestito che indica che l'utente può eseguire l'autenticazione con Azure AD. |
| D | Se l'utente è federato, CloudAP plug-in richiede un token SAML dal provider di federazione con le credenziali dell'utente. Una volta ricevuti, il token SAML, richiede un parametro nonce da Azure AD. Se l'utente è gestito, CloudAP otterrà direttamente il parametro nonce da Azure AD. |
| E | Plug-in CloudAP costruisce la richiesta di autenticazione con le credenziali dell'utente, parametro nonce e la PRT esistente, firma la richiesta con la chiave di sessione e li invia ad Azure AD. In un ambiente federato, CloudAP plug-in Usa il token SAML restituito dal provider di federazione invece l'utente ' credenziali. |
| F | Azure AD convalida la firma della chiave di sessione confrontandolo con la chiave della sessione incorporata nella PRT, convalida il parametro nonce e verifica che il dispositivo sia valido nel tenant e rilascia un nuovo PRT. Come illustrato in precedenza, la PRT è accompagnata nuovamente con la chiave della sessione crittografata dalla chiave di trasporto (tkpub). |
| G | Plug-in CloudAP passa la chiave di sessione e PRT crittografata CloudAP. CloudAP richiede il TPM per decrittografare la chiave di sessione usando la chiave di trasporto (tkpriv) e crittografare nuovamente utilizzando la chiave del TPM. CloudAP archivia la chiave di sessione crittografata nella propria cache insieme la PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Utilizzo PRT durante le richieste di token di app

![Utilizzo PRT durante le richieste di token di app](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | Un'applicazione (ad esempio, Outlook, OneNote e così via) avvia una richiesta di token a WAM. WAM, a sua volta, richiede il plug-in Azure AD WAM per soddisfare la richiesta di token. |
| b | Se un token di aggiornamento per l'applicazione è già disponibile, plug-in Azure AD WAM lo usa per richiedere un token di accesso. Per fornire la prova di associazione del dispositivo, plug-in WAM firma la richiesta con la chiave della sessione. Azure AD convalida la chiave di sessione e rilascia un token di accesso e un nuovo token di aggiornamento per l'app, crittografata con la chiave di sessione. Plug-in WAM richieste plug-in Asia Pacifico Cloud per decrittografare i token, quale, a sua volta, richiede il TPM per decrittografare utilizzando la chiave di sessione, causando plug-in WAM ottenere entrambi i token. Successivamente, WAM plug-in fornisce solo il token di accesso all'applicazione, mentre il token di aggiornamento con DPAPI per crittografare nuovamente e lo archivia nella propria cache  |
| C |  Se un token di aggiornamento per l'applicazione non è disponibile, plug-in Azure AD WAM Usa la PRT per richiedere un token di accesso. Per garantire la verifica del possesso, plug-in WAM firma la richiesta contenente la PRT con la chiave della sessione. Azure AD convalida la firma della chiave di sessione confrontandolo con la chiave della sessione incorporata nella PRT, verifica che il dispositivo sia valido e rilascia un token di accesso e un token di aggiornamento per l'applicazione. Inoltre, Azure AD può emettere un nuovo PRT (basati sul ciclo di aggiornamento), tutti gli elementi crittografati con la chiave di sessione. |
| D | Plug-in WAM richieste plug-in Asia Pacifico Cloud per decrittografare i token, quale, a sua volta, richiede il TPM per decrittografare utilizzando la chiave di sessione, causando plug-in WAM ottenere entrambi i token. Successivamente, WAM plug-in fornisce solo il token di accesso all'applicazione, mentre il token di aggiornamento con DPAPI per crittografare nuovamente e lo archivia nella propria cache. Plug-in WAM utilizzerà il token di aggiornamento in futuro per questa applicazione. Plug-in WAM anche restituisce la PRT di nuovo al plug-in Asia Pacifico Cloud, che convalida la PRT con Azure AD prima di aggiornarlo nella propria cache. Plug-in Asia Pacifico cloud utilizzerà la PRT di nuovo in futuro. |
| E | WAM fornisce il token di accesso appena rilasciato a WAM, che a sua volta, fornisce all'applicazione chiama|

### <a name="browser-sso-using-prt"></a>Browser SSO tramite PRT

![Browser SSO tramite PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Passaggio | Descrizione |
| :---: | --- |
| Una | L'utente accede a Windows con le proprie credenziali per ottenere un PRT. Una volta che l'utente apre il browser, browser (o estensione) carica gli URL dal Registro di sistema. |
| b | Quando un utente apre un URL di accesso di Azure AD, il browser o l'estensione convalida l'URL con quelli ottenuti dal Registro di sistema. Se corrispondono, il browser richiama l'host del client nativo per ottenere un token. |
| C | L'host di native client verifica che gli URL appartengono ai provider di identità di Microsoft (account Microsoft o Azure AD), estrae un nonce inviato dall'URL ed effettua una chiamata a CloudAP plug-in per ottenere un cookie PRT. |
| D | Il plug-in CloudAP crea cookie PRT, accedere con la chiave della sessione associata al TPM e inviarlo all'host del client nativo. Il cookie viene firmato dalla chiave di sessione, si potrà essere alterato. |
| E | L'host del client nativo restituirà questo cookie PRT al browser, che include come parte dell'intestazione della richiesta denominata x-ms-RefreshTokenCredential e richiesta di token da Azure AD. |
| F | Azure AD convalida la firma della chiave di sessione dal cookie PRT, convalida il parametro nonce, verifica che il dispositivo è valido nel tenant e rilascia un token ID per la pagina web e un cookie di sessione crittografata per il browser. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi relativi alla PRT, vedere l'articolo [i dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di risoluzione dei problemi Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).
