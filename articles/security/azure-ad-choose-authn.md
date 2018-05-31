---
title: Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD | Microsoft Docs
description: Questa guida è destinata ad aiutare CEO, CIO, CISO, Chief Identity Architect, Enterprise Architect e decisori dei settori IT e Security responsabili nella scelta di un metodo di autenticazione per la soluzione ibrida di gestione delle identità di Azure AD in organizzazioni di medie e grandi dimensioni.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895521"
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Scelta del metodo di autenticazione appropriato per la soluzione di identità ibrida di Azure AD 

Questo articolo è il primo di una serie di articoli finalizzati ad aiutare le organizzazioni a implementare una soluzione ibrida di gestione delle identità di Azure AD completa. Una soluzione ibrida di gestione delle identità di Azure AD completa è stata presentata come Hybrid Identity Digital Transformation Framework e include i risultati di business e gli obiettivi su cui le organizzazioni devono concentrarsi per assicurarsi di avere implementato una soluzione ibrida di gestione delle identità affidabile e sicura. Il primo risultato aziendale del framework illustra in dettaglio i requisiti necessari alle organizzazioni per proteggere il processo di autenticazione quando gli utenti accedono alle applicazioni cloud. Il primo obiettivo aziendale del risultato di business protetto tramite autenticazione è la possibilità per gli utenti di accedere ad applicazioni cloud usando nomi utente e password locali. Questo processo di accesso e la modalità di autenticazione degli utenti permettono tutto ciò nel cloud.

La scelta del metodo di autenticazione corretto rappresenta la priorità assoluta per le organizzazioni che desiderano spostare le proprie applicazioni nel cloud. Questa decisione non deve essere presa alla leggera per i motivi seguenti:

1. È la priorità assoluta per un'organizzazione che desideri passare al cloud. 

2. Il metodo di autenticazione è un componente fondamentale della presenza di un'organizzazione nel cloud, perché controlla l'accesso a tutti i dati e le risorse presenti nel cloud.

3. È alla base di tutte le altre funzioni avanzate di sicurezza e dell'esperienza utente in Azure AD.

4. Difficoltà di modifica del metodo di autenticazione implementato.

Con l'identità come nuovo piano di controllo della sicurezza IT, l'autenticazione è la clausola guard di accesso dell'organizzazione al nuovo ambiente cloud. Le organizzazioni devono assicurarsi che il piano di controllo delle identità renda il livello di sicurezza più avanzato e protegga le applicazioni cloud dagli intrusi.

### <a name="out-of-scope"></a>Fuori ambito

Le organizzazioni che non dispongono di un footprint di directory in locale esistenti non sono interessate dal presente articolo. In genere, tali aziende creano identità solo nel cloud e ciò non richiede una soluzione ibrida di gestione delle identità. Le identità cloud-only esistono esclusivamente nel cloud e non sono associate a identità locali corrispondenti.  

## <a name="choosing-the-right-authentication-method"></a>Scelta del metodo di autenticazione appropriato

Con la soluzione ibrida di gestione delle identità di Azure AD come nuovo piano di controllo, l'autenticazione è alla base dell'accesso al cloud. La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. L'implementazione del metodo di autenticazione è configurata con Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud. 

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo. 

Azure Active Directory supporta i seguenti metodi di autenticazione per le soluzioni ibride di gestione delle identità:

### <a name="cloud-authentication"></a>Autenticazione cloud
Quando si sceglie questo metodo di autenticazione, Azure Active Directory gestisce il processo di accesso per gli utenti. Affiancandolo a un accesso Single Sign-On trasparente, gli utenti possono accedere alle applicazioni cloud senza dover immettere nuovamente le proprie credenziali. Con l'autenticazione cloud è possibile scegliere tra due opzioni: 

**Sincronizzazione dell'hash delle password**: il modo più semplice per abilitare l'autenticazione per gli oggetti directory in locale in Azure AD. La sincronizzazione dell'hash delle password consente agli utenti di usare gli stessi nome utente e password usati in locale senza la necessità di implementare infrastrutture aggiuntive. Alcune funzionalità premium di Azure AD, come ad esempio Identity Protection, richiedono la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione selezionato.

> [!NOTE] 
> Le password non vengono mai archiviate in testo non crittografato o crittografate con un algoritmo reversibile in Azure AD. Per altre informazioni sul processo di sincronizzazione dell'hash delle password, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Autenticazione pass-through**: fornisce una convalida delle password semplice per i servizi di autenticazione di Azure Active Directory utilizzando un agente software in esecuzione su uno o più server locali per convalidare gli utenti direttamente con Active Directory locale, assicurando che la convalida della password non avvenga nel cloud. Le società che per questioni di sicurezza devono applicare immediatamente gli stati degli account utente locali, i criteri di gestione delle password e l'orario di accesso usano questo metodo di autenticazione. Per altre informazioni sul processo effettivo di autenticazione pass-through, vedere [Accesso utente con l'autenticazione pass-through di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticazione federata
Scegliendo questo metodo di autenticazione, Azure Active Directory trasferisce il processo di autenticazione a un sistema di autenticazione attendibile separato, ad esempio un sistema Active Directory Federation Services locale(AD FS), per convalidare la password dell'utente. Il sistema di autenticazione può fornire requisiti di autenticazione aggiuntivi, come ad esempio un'autenticazione basata su smart card o un'autenticazione a più fattori di terze parti. Per altre informazioni, vedere [Implementazione di Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

La sezione seguente è utile per decidere quale metodo di autenticazione è adatto alle esigenze, utilizzando un albero delle decisioni. Questo risulta utile per scegliere se implementare l'autenticazione federata o cloud per la soluzione ibrida di gestione delle identità di Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Albero delle decisioni per l'autenticazione di Azure Active Directory

![Immagine1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Considerazioni dettagliate sui metodi di autenticazione

### <a name="cloud-authentication-password-hash-sync"></a>Autenticazione cloud: sincronizzazione dell'hash delle password 

* **Lavoro richiesto:** la sincronizzazione dell'hash delle password richiede il minimo sforzo di implementazione, manutenzione e infrastruttura per le organizzazioni che necessitano solo di consentire agli utenti l'accesso a Office 365, app SaaS e altre risorse basate su Azure Active Directory. Quando abilitata, la sincronizzazione dell'hash delle password rientra nel processo del servizio di sincronizzazione Azure AD Connect e viene eseguita ogni due minuti. 

* **Esperienza utente:** si consiglia alle organizzazioni di implementare un accesso Single Sign-On (SSO) trasparente con sincronizzazione dell'hash delle password per migliorare l'esperienza di accesso dell'utente, evitando richieste inutili una volta effettuata la connessione.

* **Scenari avanzati:** le organizzazioni possono scegliere di usare informazioni tratte dalle identità con i report di Azure AD Identity Protection, ad esempio report sulle credenziali perse. Windows Hello for Business è un'altra opzione dotata di [requisiti specifici quando si usa la sincronizzazione dell'hash delle password](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Le organizzazioni che richiedono l'autenticazione a più fattori con sincronizzazione dell'hash delle password devono usare l'autenticazione a più fattori di Azure Active Directory e non possono usare metodi di autenticazione a più fattori locali di terze parti.

* **Continuità aziendale:** la sincronizzazione dell'hash delle password è intrinsecamente altamente disponibile come servizio cloud in grado di consentire la scalabilità a tutti i data center Microsoft. È consigliabile implementare un secondo server di Azure AD Connect in modalità staging con una configurazione standby per scopi di ripristino di emergenza.

* **Considerazioni:** attualmente, la sincronizzazione dell'hash delle password non applica immediatamente le modifiche apportate allo stato degli account locali. In questo caso, l'utente avrà accesso alle applicazioni cloud fino a quando lo stato dell'account utente è sincronizzato con Azure Active Directory. Per superare questa limitazione, è consigliabile attivare un nuovo ciclo di sincronizzazione dopo gli aggiornamenti di massa agli stati degli account utente locali effettuati dagli amministratori, come ad esempio la disattivazione di account. 

> [!NOTE] 
> Gli stati Password scaduta e Account bloccato non sono attualmente sincronizzati in Azure Active Directory con Azure AD Connect. 

Per la procedura di implementazione, fare riferimento all'[implementazione della sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization).

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticazione cloud: autenticazione pass-through  

* **Lavoro richiesto:** per l'autenticazione pass-through, sono necessari uno o più agenti leggeri (se ne consigliano tre) installati sui server esistente con accesso ad Active Directory Domain Services locale, incluso l'accesso ai controller di dominio AD locali. Questi agenti richiedono l'accesso in uscita a Internet e l'accesso ai controller di dominio. Per questo motivo non è possibile distribuire gli agenti in una rete perimetrale, perché ciò richiederebbe un accesso non vincolato della rete ai controller di dominio. Tutto il traffico di rete è crittografato e limitato alle richieste di autenticazione. Per altre informazioni su questo processo, vedere l'[approfondimento sulla sicurezza](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) per l'autenticazione pass-through.

* **Esperienza utente:** si consiglia alle organizzazioni di implementare un accesso Single Sign-On trasparente con autenticazione pass-through per migliorare l'esperienza di accesso dell'utente, evitando richieste inutili una volta effettuata la connessione.

* **Scenari avanzati:** l'autenticazione pass-through applica il criterio di account locale al momento dell'accesso. Ad esempio, l'accesso viene negato quando lo stato dell'account di un utente locale è disabilitato o bloccato, [la password è scaduta](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication ) o non rientra nell'orario di accesso consentito dell'utente.  Le organizzazioni che richiedono l'autenticazione a più fattori con autenticazione pass-through devono usare Azure Multi-Factor Authentication (MFA) e non possono usare metodi di autenticazione a più fattori locali di terze parti. Funzionalità avanzate, come ad esempio i report sulle credenziali perse di Identity Protection, richiedono l'implementazione della sincronizzazione dell'hash delle password indipendentemente dalla scelta o meno dell'autenticazione pass-through.

* **Continuità aziendale:** è consigliabile implementare due agenti di autenticazione pass-through extra oltre al primo agente nel server di Azure AD Connect per assicurare una disponibilità elevata delle richieste di autenticazione. Quando si dispone di tre agenti di distribuzione, un agente può avere comunque esito negativo quando un altro agente è inattivo per manutenzione. Un altro vantaggio dell'implementazione della sincronizzazione dell'hash delle password, oltre all'autenticazione pass-through, è che può essere usata come metodo di autenticazione di backup quando il metodo di autenticazione principale non è più disponibile, ad esempio quando i server locali non sono disponibili.

* **Considerazioni:** se si usa la sincronizzazione dell'hash delle password come metodo di autenticazione di backup per l'autenticazione pass-through e gli agenti non sono in grado di convalidare le credenziali dell'utente, il failover alla sincronizzazione dell'hash delle password non viene eseguito automaticamente. È necessario cambiare il metodo di accesso manualmente con Azure AD Connect. L'autenticazione pass-through supporta solo le applicazioni cloud che usano l'autenticazione moderna e protocolli di Exchange Online specifici, come ad esempio ActiveSync, POP3 e IMAP4. Ad esempio, vedere [Microsoft Office 2013 e versioni successive supporta l'autenticazione moderna, ma non nelle versioni precedenti](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) per altre informazioni sul supporto delle app di Office. Vedere le [Domande frequenti](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) e altre considerazioni sull'autenticazione pass-through, incluso il supporto ID alternativo.

Per la procedura di distribuzione, fare riferimento all'[implementazione dell'autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Autenticazione federata

* **Lavoro richiesto:** usando un sistema di autenticazione federata si fa riferimento a un sistema esterno per autenticare gli utenti. Alcune aziende scelgono di riutilizzare i propri investimenti in sistemi federati esistenti con la soluzione ibrida di gestione delle identità di Azure AD. La manutenzione e la gestione del sistema federato non rientra nella sfera di controllo di Azure Active Directory. È compito dell'organizzazione assicurarsi che il sistema federato usato venga implementato in modo sicuro e sia in grado di gestire il carico di autenticazione. 

* **Esperienza utente:** l'esperienza utente di autenticazione federata dipende dall'implementazione delle funzionalità, dalla topologia e dalla configurazione della farm federativa. Alcune organizzazioni richiedono tale flessibilità per adattare e configurare l'accesso alla farm federativa in base alle proprie esigenze di sicurezza. Ad esempio, è possibile configurare gli utenti e i dispositivi connessi internamente perché effettuino l'accesso automaticamente, senza richiedere l'immissione di credenziali, in quanto essi hanno già effettuato l'accesso dai propri dispositivi. D'altro canto, se necessario, alcune funzionalità di sicurezza avanzate possono complicare il processo di accesso dell'utente.

* **Scenari avanzati:** una soluzione di autenticazione federata è in genere richiesta quando i clienti hanno requisiti di autenticazione non supportati da Azure Active Directory (informazioni dettagliate sono [elencate qui](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)). I requisiti comuni includono:

    * Richieste di autenticazione con smart card o certificati
    * Uso di server MFA locali o provider di procedure di autenticazione a più fattori di terze parti.
    * Autenticazione tramite soluzioni di autenticazione di terze parti. Vedere l'[Elenco di compatibilità di federazione di Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Gli utenti devono accedere con i relativi sAMAccountName, ad esempio DOMINIO\nome utente, anziché usare un nome dell'entità utente (UPN), ad esempio user@domain.com

* **Continuità aziendale:** i sistemi federati richiedono in genere la configurazione di un carico bilanciato di array di server, noto anche come farm, in una topologia di rete interna e perimetrale per garantire la disponibilità elevata per le richieste di autenticazione. La sincronizzazione dell'hash delle password può essere implementata assieme all'autenticazione federata come metodo di autenticazione di backup quando il metodo di autenticazione principale non è più disponibile, ad esempio quando i server locali non sono disponibili. Alcune organizzazioni aziendali di grandi dimensioni richiedono una soluzione di federazione per supportare più punti di ingresso Internet configurati con geo-DNS per richieste di autenticazione a bassa latenza.

* **Considerazioni:** i sistemi federati richiedono in genere un investimento più significativo nell'infrastruttura locale. La maggior parte delle organizzazioni sceglie questa opzione se dispone già di un investimento in sistemi di federazione locali; un importante requisito aziendale prevede l'uso di un provider di identità unico. La federazione prevede modalità d'uso e risoluzione dei problemi più complesse rispetto alle soluzioni di autenticazione cloud. L'uso di ID utente con un dominio non instradabile che non possono essere verificati in Azure Active Directory per accedere richiede l'implementazione di una configurazione extra. Questo requisito è noto come supporto per l'ID di accesso alternativo. Per requisiti e limitazioni, vedere [Configurazione dell'ID di accesso alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). Se si sceglie di usare un provider di autenticazione a più fattori di terze parti con federazione, verificare che il provider supporti WS-Trust per consentire di aggiungere i dispositivi ad Azure Active Directory.

Per la procedura di distribuzione, fare riferimento all'[implementazione di servizi di federazione](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers).

> [!NOTE] 
> Quando si implementa la soluzione di identità ibrida di Azure AD è necessario assicurarsi di implementare una delle topologie supportate di Azure AD Connect. Altre informazioni sulle configurazioni supportate o meno sono disponibili in [Topologie per Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagrammi di architettura

Il seguente diagramma definisce i componenti dell'architettura di alto livello necessari per ogni metodo di autenticazione che è possibile usare con la soluzione di identità ibrida di Azure AD. Esso fornisce una panoramica per confrontare le differenze tra le soluzioni.

Il diagramma seguente illustra la semplicità di una soluzione di sincronizzazione dell'hash delle password:

![Sincronizzazione dell'hash delle password](media/azure-ad/azure-ad-authn-image2.png)

Il diagramma seguente illustra i requisiti dell'agente di autenticazione pass-through:

![Autenticazione pass-through](media/azure-ad/azure-ad-authn-image3.png)

Il diagramma seguente illustra i componenti necessari per la federazione nella rete interna e perimetrale dell'organizzazione:

![AD FS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>Confronto dei metodi di autenticazione

|Considerazioni|Sincronizzazione dell'hash delle password + Accesso Single Sign-On facile|Autenticazione pass-through + Accesso Single Sign-On facile|Federazione con ADFS|
|:-----|:-----|:-----|:-----|
|Dove si verifica l'autenticazione?|Nel cloud|Nel cloud, dopo uno scambio della verifica della password di protezione con l'agente di autenticazione locale|Locale|
|Quali sono i requisiti del server locale oltre il sistema di provisioning Azure AD Connect?|Nessuna|1 server per ogni agente di autenticazione aggiuntivo|2 o più server AD FS<br>2 o più server WAP nella rete perimetrale|
|Quali sono i requisiti di rete e Internet locali oltre al sistema di provisioning?|Nessuna|[Accesso a Internet in uscita](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) dai server in cui sono in esecuzione gli agenti di autenticazione|[Accesso a Internet in ingresso](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) ai server WAP nelle reti perimetrali<br>Accesso alla rete in ingresso ai server AD FS dai server WAP nelle reti perimetrali<br>Bilanciamento del carico di rete|
|Esiste un requisito per il certificato SSL?|No |No |Sì|
|Esiste una soluzione di monitoraggio dello stato?|Facoltativo|Stato agente fornito dall'[interfaccia di amministrazione di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Gli utenti ottengono l'accesso Single Sign-On alle risorse cloud dai dispositivi aggiunti al dominio all'interno della rete aziendale?|Sì, con l'[accesso Single Sign-On facile](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sì, con l'[accesso Single Sign-On facile](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Sì|
|Quali tipi di accesso sono supportati?|UserPrincipalName + Password<br>Autenticazione integrata di Windows con l'[accesso Single Sign-On facile](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[ID di accesso alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + Password<br>Autenticazione integrata di Windows con l'[accesso Single Sign-On facile](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[ID di accesso alternativo](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + Password<br>sAMAccountName + Password<br>Autenticazione integrata di Windows<br>[Autenticazione con certificato e smart card](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[ID di accesso alternativo](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Windows Hello for Business è supportato?|[Modello di attendibilità chiavi](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modello di attendibilità certificati con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modello di attendibilità chiavi](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modello di attendibilità certificati con Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Modello di attendibilità chiavi](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Modello di attendibilità certificati](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quali sono le opzioni di Multi-Factor Authentication?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Server di Azure MFA](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[MFA di terze parti](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Quali stati dell'account utente sono supportati?|Account disabilitati<br>(fino a 30 minuti di ritardo)|Account disabilitati<br>Account bloccato<br>Password scaduta<br>Orario di accesso|Account disabilitati<br>Account bloccato<br>Password scaduta<br>Orario di accesso|
|Quali sono le opzioni di accesso condizionale?|[Accesso condizionale di Azure AD ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Accesso condizionale di Azure AD ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Accesso condizionale di Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[Regole di attestazione per AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Il blocco dei protocolli legacy è supportato?|No |No |[Sì](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|È possibile personalizzare il logo, l'immagine e la descrizione nelle pagine di accesso?|[Sì, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sì, con Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Sì](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Quali scenari avanzati sono supportati?|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Report delle credenziali perse](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart Password Lockout](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Sistema di autenticazione multisito a bassa latenza<br>[Blocco della Extranet di AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integrazione con i sistemi di identità di terze parti](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Raccomandazioni e considerazioni da Azure Active Directory

Il sistema di identità garantisce che gli utenti abbiano accesso alle applicazioni cloud e line-of-business che vengono migrate e rese disponibili nel cloud. L'autenticazione controlla l'accesso alle applicazioni per mantenere gli utenti autorizzati produttivi e tenere i malintenzionati alla larga dai dati sensibili dell'organizzazione. Per questo motivo, nella scelta del metodo di autenticazione appropriato per l'organizzazione è opportuno tenere conto delle raccomandazioni seguenti. 

Usare o abilitare la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione scelto, per i motivi seguenti:

1. **Disponibilità elevata e ripristino di emergenza:** l'autenticazione pass-through e federata si basano sull'infrastruttura locale. Per l'autenticazione pass-through, il footprint locale include l'hardware del server e di rete richiesto dagli agenti di autenticazione pass-through. Per la federazione, il footprint locale è ancora più grande, poiché richiede che i server nella rete perimetrale inoltrino le richieste di autenticazione ai server federativi interni. Per evitare singoli punti di guasto, l'organizzazione deve implementare server ridondanti per garantire che le richieste di autenticazione siano sempre soddisfatte anche nel caso in cui un componente abbia esito negativo. Sia l'autenticazione pass-through che la federazione fanno anche affidamento su controller di dominio per rispondere alle richieste di autenticazione che possono anche non riuscire. Molti di questi componenti necessitano costantemente di manutenzione per rimanere integri, mentre è più probabile che si verifichino interruzioni quando la manutenzione non è pianificata né implementata in modo corretto. Eventuali interruzioni possono essere evitate usando la sincronizzazione dell'hash delle password perché il servizio di autenticazione cloud di Microsoft Azure Active Directory scala a livello globale ed è sempre disponibile.

2. **Sopravvivenza alle interruzioni locali:** le conseguenze di un'interruzione locale causata da attacchi informatici o emergenze possono essere significative e spaziano da un danno reputazionale del marchio alla paralisi delle organizzazioni che non sono in grado di gestire l'attacco. Nell'ultimo anno, molte organizzazioni sono state vittime di attacchi di malware, inclusi ransomware targettizzati, che hanno portato alla mancata accessibilità dei server locali. Nel fornire il proprio supporto agli utenti per affrontare questi tipi di attacchi, Microsoft ha notato due categorie di organizzazioni:

   a. Le organizzazioni che avevano precedentemente abilitato la sincronizzazione dell'hash delle password sono tornate online in poche ore modificando il metodo di autenticazione per l'uso della sincronizzazione dell'hash delle password. Usando l'accesso alla posta elettronica tramite Office 365 sono state in grado di risolvere i problemi e disporre di altri carichi di lavoro basati su cloud.

   b. Le organizzazioni che in precedenza non avevano abilitato la sincronizzazione dell'hash delle password hanno dovuto ricorrere a sistemi di posta elettronica consumer esterni non attendibili per le comunicazioni e la risoluzione dei problemi. In questi casi, ci sono volute settimane o più per tornare di nuovo in attività.

3. **Identity Protection:** uno dei modi migliori per proteggere gli utenti nel cloud è Azure AD Identity Protection. Microsoft esegue continuamente l'analisi di Internet alla ricerca degli elenchi di nomi utenti e password venduti e resi disponibili sul dark web dai malintenzionati. Azure AD è in grado di usare queste informazioni per verificare se uno qualsiasi dei nomi utente e delle password dell'organizzazione sono compromessi. È pertanto essenziale abilitare la sincronizzazione dell'hash delle password indipendentemente dal metodo di autenticazione usato, sia che si tratti di autenticazione federata o pass-through. Le credenziali perse, presentate sotto forma di report, possono essere usate per bloccare od obbligare gli utenti a cambiare password quando provano ad accedere con una password persa.

Infine, secondo [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html) Microsoft offre il set di funzioni di gestione delle identità e degli accessi più completo. Microsoft gestisce ogni mese [450 miliardi di richieste di autenticazione](https://www.microsoft.com/en-us/security/intelligence-report) per fornire l'accesso a migliaia di applicazioni SaaS come Office 365 praticamente da qualsiasi dispositivo. 

## <a name="conclusion"></a>Conclusioni

In questo articolo sono descritte diverse opzioni di autenticazione che le organizzazioni possono configurare e implementare per supportare l'accesso alle applicazioni cloud. Per soddisfare esigenze aziendali, tecniche e di sicurezza diverse, le organizzazioni possono scegliere tra sincronizzazione dell'hash delle password, autenticazione pass-through e federazione. Con ogni metodo di autenticazione, l'organizzazione può scegliere se i requisiti aziendali vengono indirizzati mediante il lavoro richiesto per distribuire la soluzione e l'esperienza utente nel processo di accesso. È necessario valutare anche se l'organizzazione necessita degli scenari avanzati e delle funzionalità di continuità aziendale disponibili con ogni metodo di autenticazione. Infine, è necessario valutare le considerazioni su ogni metodo di autenticazione per vedere se impediscono l'implementazione della propria scelta.

## <a name="next-steps"></a>Passaggi successivi

Oggigiorno, le minacce sono presenti 24 ore al giorno e provengono da ovunque. L'implementazione del metodo di autenticazione corretto consente di attenuare i rischi di sicurezza e proteggere le identità. 

Ciò è possibile tramite l'[Introduzione](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) ad Azure Active Directory e la distribuzione di una soluzione di autenticazione appropriata per l'organizzazione.

Se si intende eseguire la migrazione dall'autenticazione cloud a federativa, sono disponibili altre informazioni [sulla modifica del metodo di accesso](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Per pianificare e implementare la migrazione è possibile usare [questi piani del progetto](http://aka.ms/deploymentplans).
