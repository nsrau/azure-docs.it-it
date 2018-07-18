---
title: Cinque passaggi per proteggere l'infrastruttura di identità in Azure Active Directory
description: Questo documento descrive un elenco di azioni importanti che gli amministratori devono implementare per proteggere meglio l'organizzazione con le funzionalità di Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36940025"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Cinque passaggi per proteggere l'infrastruttura di identità

Chi legge questo documento è consapevole dell'importanza della sicurezza e sicuramente è già responsabile della sicurezza dell'organizzazione. Per convincere altri dell'importanza della sicurezza, invitarli a leggere l'ultimo [Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

Questo documento consente di migliorare l'approccio alla sicurezza con le funzionalità di Azure Active Directory usando un elenco di controllo di cinque passaggi per difendere l'organizzazione da attacchi informatici.

Questo elenco di controllo permette di distribuire rapidamente le azioni critiche consigliate per proteggere subito l'organizzazione spiegando in che modo:

* Rafforzare le credenziali.
* Ridurre la superficie di attacco.
* Automatizzare la risposta alle minacce.
* Migliorare la consapevolezza in merito al controllo e al monitoraggio.
* Garantire un livello di sicurezza più prevedibile e completo per gli utenti finali con self-help.

> [!NOTE]
> Molti consigli forniti in questo documento si applicano solo alle applicazioni, configurate per l'uso di Azure Active Directory come provider di identità. La configurazione di app per il Single Sign-On garantisce i vantaggi associati a criteri per le credenziali, rilevamento delle minacce, controllo, registrazione e altre funzionalità aggiunte a queste applicazioni. L'[accesso Single Sign-On tramite Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) costituisce l'elemento su cui si basano questi consigli.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Prima di iniziare: proteggere gli account con privilegi mediante MFA

Prima di iniziare questo elenco di controllo, verificare che l'account non venga compromesso durante la lettura. È innanzitutto necessario proteggere gli account con privilegi.

Gli utenti malintenzionati che ottengono il controllo di account con privilegi possono causare enormi danni, pertanto è fondamentale proteggere questi account prima di tutto. Abilitare e richiedere [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) per tutti gli amministratori nell'organizzazione usando la [protezione di base](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Implementare MFA, se non è stato già fatto. È davvero importante.

Tutto pronto? Iniziamo con l'elenco di controllo.

## <a name="step-1---strengthen-your-credentials"></a>Passaggio 1 - Rafforzare le credenziali 

La maggior parte delle violazioni della sicurezza delle organizzazioni ha origine con un account compromesso mediante uno di alcuni metodi, tra cui un attacco password spraying, riproduzione della violazione o phishing. Altre informazioni su questi attacchi sono disponibili in questo video:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Se gli utenti nel sistema di identità usano password vulnerabili e non le rafforzano con autenticazione a più fattori, non è più una questione di se o quando gli account verranno compromessi, ma solo "quanto spesso".

### <a name="make-sure-your-organization-use-strong-authentication"></a>Assicurarsi che l'organizzazione usi l'autenticazione avanzata

Poiché le password vengono individuate, sottoposte a phishing, sottratte con malware o riusate con una certa frequenza, è fondamentale supportarle con un tipo di credenziali avanzate: altre informazioni su [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Disattivare le regole di complessità e scadenza tradizionali e iniziare a vietare la password attaccate più spesso

Molte organizzazioni usano le regole tradizionali di complessità (ad esempio, con caratteri speciali) e di scadenza delle password. Le ricerche di Microsoft hanno dimostrato che questi criteri sono dannosi, costringendo gli utenti a scegliere password più facili da indovinare.

In conformità con le [linee guida NIST](https://pages.nist.gov/800-63-3/sp800-63b.html), Microsoft consiglia di implementare questi tre requisiti:

1. Richiedere password di almeno 8 caratteri. Password più lunghe non sono necessariamente migliori, in quanto gli utenti si trovano a scegliere password prevedibili, a salvarle in file o a prenderne nota.
2. Disabilitare le regole di scadenza, che spingono gli utenti a scegliere password facilmente identificabili, ad esempio **Esate2018!**.
3. Disabilitare i requisiti di composizione di caratteri e impedire agli utenti di scegliere password comunemente sottoposte ad attacchi, in quanto potrebbero scegliere sostituzioni di caratteri prevedibili.

È possibile usare [PowerShell per impedire la scadenza delle password](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) per gli utenti se si creano direttamente identità in Azure AD. Le organizzazioni che usano AD in locale con Azure AD Connect per la sincronizzazione delle identità in Azure AD (nota anche come distribuzione ibrida) devono implementare [criteri di password intelligenti](https://aka.ms/passwordguidance) in locale usando [impostazioni di Criteri di gruppo di domini](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) oppure [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

La funzionalità di [divieto dinamico delle password](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) di Azure Active Directory usa il comportamento degli utenti malintenzionati per impedire agli utenti di impostare password facilmente individuabili. Questa funzionalità è sempre attiva e le organizzazioni con una distribuzione ibrida possono usufruirne abilitando il [writeback delle password](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) o possono distribuire la [protezione delle password di Azure AD per Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). La protezione delle password di Azure AD impedisce agli utenti di scegliere in generale password comuni e password personalizzate configurabili.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Proteggersi da credenziali perse e aggiungere resilienza da interruzioni

Se l'organizzazione usa una soluzione di identità ibrida, è consigliabile abilitare la sincronizzazione dell'hash delle password per i due motivi seguenti:

* Il report [Utenti con credenziali perse](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) nella gestione di Azure AD avvisa l'utente di coppie di nome utente e password esposte nel Dark Web. Una quantità incredibile di password viene persa tramite phishing, malware e riuso delle password nei siti di terze parti che verranno sottoposti a violazioni in un secondo momento. Microsoft individua molte di queste credenziali perse e, in questo report, indica se corrispondono alle credenziali di un'organizzazione, ma solo se l'utente [abilita la sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* In caso di interruzione locale (ad esempio, in un attacco ransomware), l'utente è in grado di passare all'[autenticazione cloud mediante sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Questo metodo di autenticazione di backup consente di continuare ad accedere alle app configurate per l'autenticazione con Azure Active Directory, incluso Office 365.

Altre informazioni sul funzionamento della [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

### <a name="implement-ad-fs-extranet-lockout"></a>Implementare il blocco della Extranet di AD FS

Le organizzazioni che configurano applicazioni per l'autenticazione diretta con Azure AD usufruiscono del [blocco intelligente di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Chi usa AD FS dovrà implementare il [blocco della Extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection) di AD FS. Il blocco della Extranet protegge dagli attacchi di forza bruta che mirano ad AD FS impedendo al contempo agli utenti di essere bloccati in Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Sfruttare i vantaggi di credenziali sicure e più facili da usare

Con [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) è possibile sostituire le password con l'autenticazione avanzata a due fattori nei PC e nei dispositivi mobili. Questo processo di autenticazione è costituito da un nuovo tipo di credenziale utente, che è associata a un dispositivo e usa un sensore biometrico o un PIN.

## <a name="step-2---reduce-your-attack-surface"></a>Passaggio 2 - Ridurre la superficie di attacco

Poiché le password vengono frequentemente compromesse, è fondamentale per l'organizzazione ridurre al minimo la superficie di attacco. Eliminare l'uso di protocolli precedenti e meno sicuri, limitare l'accesso di punti di ingresso ed esercitare un controllo più significativo di accesso amministrativo alle risorse consentono di ridurre la superficie di attacco.

### <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy

Le app che usano propri metodi legacy per l'autenticazione con Azure AD e l'accesso ai dati aziendali pongono un altro rischio per le organizzazioni. Esempi di app che usano l'autenticazione legacy sono i client POP3, IMAP4 o SMTP. Le app con autenticazione legacy eseguono l'autenticazione per conto dell'utente e impediscono ad Azure AD di eseguire valutazioni avanzate sulla sicurezza. L'alternativa autenticazione moderna consentirà di ridurre i rischi di sicurezza, poiché supporta l'autenticazione a più fattori e l'accesso condizionale. È consigliabile implementare le tre azioni seguenti:

1. Bloccare l'[autenticazione legacy se si usa AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Configurare [SharePoint Online ed Exchange Online per l'uso dell'autenticazione moderna](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Usare [criteri di accesso condizionale per bloccare l'autenticazione legacy](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Bloccare punti di ingresso di autenticazione non validi

Con un approccio di presunzione di violazione è consigliabile ridurre l'impatto di credenziali utente compromesse quando si verifica questa condizione. Per ogni app nell'ambiente considerare i casi d'uso validi: quali gruppi, quali reti, quali dispositivi e altri elementi sono autorizzati, quindi bloccare il resto. Prestare attenzione a limitare l'uso di [account di servizio o con privilegi elevati](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Con l'[accesso condizionale di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) è possibile controllare la modalità di accesso degli utenti autorizzati alle app e alle risorse in base a specifiche condizioni definite.

Prestare particolare attenzione agli account di servizio (usati per eseguire attività in modo automatico). Grazie all'accesso condizionale è possibile verificare che questi account possano essere eseguiti solo a fronte del servizio, dall'indirizzo IP e all'ora del giorno appropriati.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementare Azure AD Privileged Identity Management

Un altro impatto determinato dalla presunzione di violazione è la necessità di ridurre al minimo le probabilità che un account compromesso possa essere usato con un ruolo con privilegi. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) consente di ridurre al minimo gli account con privilegi grazie alla possibilità di:

* Identificare e gestire gli utenti assegnati a ruoli amministrativi.
* Capire quali ruoli con privilegi eccessivi o inutilizzati è opportuno rimuovere.
* Definire regole per verificare che i ruoli con privilegi siano protetti con autenticazione a più fattori.
* Definire regole per verificare che i ruoli con privilegi vengano concessi solo per il tempo sufficiente a completare l'attività con privilegi.

Abilitare Azure AD PIM, visualizzare gli utenti a cui sono assegnati ruoli amministrativi e rimuovere gli account non necessari in questi ruoli. Per gli utenti con privilegi rimanenti, spostarli da permanenti a idonei. Infine, definire criteri appropriati per verificare possano ottenere accesso a questi ruoli con privilegi in modo sicuro.

Nell'ambito del processo di distribuzione degli account con privilegi, seguire le [procedure consigliate per la creazione di almeno due account di emergenza](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) per avere sempre accesso ad Azure AD, anche in caso di blocco.

## <a name="step-3---automate-threat-response"></a>Passaggio 3 - Automatizzare la risposta alle minacce

Azure Active Directory offre molte funzionalità che intercettano automaticamente gli attacchi, per rimuovere la latenza tra rilevamento e risposta. Riducendo il tempo usato dai criminali per insinuarsi nell'ambiente, è possibile ridurre anche costi e rischi. Ecco i passaggi concreti che è possibile attuare.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementare criteri di sicurezza per il rischio utente mediante Azure AD Identity Protection

Il rischio utente indica la probabilità che l'identità di un utente sia stata compromessa e viene calcolato in base agli [eventi di rischio utente](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) associati all'identità di un utente. Un criterio di rischio utente è un criterio di accesso condizionale che valuta il livello di rischio per un utente o gruppo specifico. Basato su un livello di rischio basso, medio o elevato, un criterio può essere configurato per bloccare l'accesso o richiedere una modifica sicura della password mediante autenticazione a più fattori. Microsoft consiglia di richiedere una modifica sicura della password per gli utenti con rischio alto.

![Utenti contrassegnati per il rischio](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementare criteri di rischio di accesso mediante Azure AD Identity Protection

Il rischio di accesso è la probabilità che un utente diverso dal proprietario dell'account tenti di eseguire l'accesso usando l'identità. Un [criterio di rischio di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) è un criterio di accesso condizionale che valuta il livello di rischio per un utente o gruppo specifico. Basato sul livello di rischio (alto/medio/basso), un criterio può essere configurato per bloccare l'accesso o forzare l'autenticazione a più fattori. Verificare di forzare l'autenticazione a più fattori nei rischi di accesso a partire dal livello medio.

![Accedere da indirizzi IP anonimi](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Passaggio 4 - Migliorare la consapevolezza

Il controllo e la registrazione di eventi relativi alla sicurezza e di avvisi correlati sono componenti essenziali di una strategia di protezione efficace. I report e i log di sicurezza offrono un record elettronico delle attività sospette e aiutano a rilevare criteri che possono indicare tentativi esterni di penetrazione nella rete e attacchi interni. È possibile usare la funzione di controllo per monitorare l'attività dell'utente, documentare la conformità alle normative, eseguire analisi forensi e altro ancora. Gli avvisi forniscono notifiche di eventi di sicurezza.

### <a name="monitor-azure-ad"></a>Monitorare Azure AD

Le funzionalità e i servizi di Microsoft Azure offrono opzioni di controllo e registrazione di sicurezza configurabili che consentono di identificare e correggere i gap nei meccanismi e nei criteri di sicurezza per evitare violazioni. È possibile usare le funzionalità di [registrazione e controllo di Azure](https://docs.microsoft.com/azure/security/azure-log-audit) e i [report delle attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Monitorare Azure AD Connect Health in ambienti ibridi

Il [monitoraggio di AD FS con Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) offre informazioni approfondite sui potenziali problemi e conferisce maggiore visibilità sugli attacchi in un'infrastruttura AD FS. Azure AD Connect Health fornisce avvisi con dettagli, procedure di risoluzione e collegamenti alla documentazione correlata, analisi di utilizzo per varie metriche correlate al traffico di autenticazione, monitoraggio e report delle prestazioni.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Monitorare eventi di Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) è uno strumento di notifica, monitoraggio e creazione di report che consente di rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione. Rileva eventi di rischio, ad esempio credenziali perse, comunicazione impossibile e accessi da dispositivi infettati, indirizzi IP anonimi, indirizzi IP associati a un'attività sospetta e posizioni sconosciute. Abilitare avvisi di notifica per ricevere un messaggio di posta elettronica degli utenti a rischio e/o un messaggio di posta elettronica di riepilogo settimanale.

![Utenti contrassegnati per il rischio](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Passaggio 5 - Abilitare il self-help degli utenti finali

Per quanto possibile è opportuno bilanciare sicurezza e produttività. In linea con lo stesso approccio mentale con cui un utente predispone una base per la sicurezza a lungo termine, è possibile rimuovere attrito dall'organizzazione potenziando gli utenti, ma rimanendo sempre vigili. 

### <a name="implement-self-service-password-reset"></a>Implementare la reimpostazione self-service delle password

La [reimpostazione self-service delle password](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) di Azure offre agli amministratori IT una modalità semplice per consentire agli utenti di reimpostare o sbloccare le password o gli account senza intervento dell'amministratore. Il sistema include report dettagliati per tenere traccia del momento in cui gli utenti accedono al sistema, oltre a notifiche per segnalare usi impropri. 

### <a name="implement-self-service-group-management"></a>Implementare la gestione gruppi self-service

Azure AD offre la possibilità di gestire l'accesso alle risorse con gruppi di sicurezza e gruppi di Office 365. Questi gruppi possono essere gestiti dai proprietari, anziché dagli amministratori IT. Nota come [gestione gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), questa funzionalità consente ai proprietari di gruppi sprovvisti di ruoli amministrativi di creare e gestire gruppi senza che un amministratore debba gestire le loro richieste.

### <a name="implement-azure-ad-access-reviews"></a>Implementare verifiche di accesso di Azure AD

Con le [verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview) è possibile gestire le appartenenze ai gruppi, l'accesso ad applicazioni aziendali e le assegnazioni di ruoli con privilegi per verificare di mantenere uno standard di sicurezza che non conceda agli utenti l'accesso per periodi prolungati, se non necessario.

## <a name="summary"></a>Summary

Sono molti gli aspetti che ruotano attorno a un'infrastruttura di identità sicura, ma questo elenco di controllo di cinque passaggi consente di crearne una più sicura e protetta:

* Rafforzare le credenziali.
* Ridurre la superficie di attacco.
* Automatizzare la risposta alle minacce.
* Migliorare la consapevolezza in merito al controllo e al monitoraggio.
* Garantire un livello di sicurezza più prevedibile e completo per gli utenti finali con self-help.

Apprezziamo la serietà con cui gli utenti si approcciano alla sicurezza delle identità e ci auguriamo che questo documento rappresenti un utile piano di azione per migliorare il livello di sicurezza di un'organizzazione.

## <a name="next-steps"></a>Passaggi successivi
Se occorre assistenza per pianificare e distribuire i consigli, fare riferimento ai [piani di distribuzione del progetto di Azure AD](http://aka.ms/deploymentplans).
