---
title: Procedure consigliate per la protezione dell'accesso amministrativo in Azure AD | Microsoft Docs
description: Verificare la sicurezza degli account amministratore e dell'accesso amministrativo nell'organizzazione. Per progettisti di sistemi e professionisti IT che configurano Azure AD, Azure e i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 166171dc8d8d694ef253ed6809c53b54577535e2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD

La sicurezza degli asset aziendali, o della maggior parte di essi, in un'organizzazione moderna dipende dall'integrità degli account con privilegi usati per amministrare e gestire i sistemi IT. Attori dannosi, come gli utenti malintenzionati, prendono spesso di mira gli account amministratore e altri elementi di accesso con privilegi per cercare di accedere rapidamente ai dati sensibili e ai sistemi usando attacchi con furto di credenziali. Per i servizi cloud, prevenzione e risposta sono responsabilità comuni del provider di servizi cloud e del cliente. Per altre informazioni sulle minacce più recenti agli endpoint e nel cloud, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/sir/default.aspx). Questo articolo può aiutare a sviluppare una roadmap per colmare il divario tra i piani correnti e le indicazioni fornite di seguito.

> [!NOTE] 
> Microsoft si impegna a offrire i massimi livelli di attendibilità, trasparenza, conformità agli standard e conformità ai requisiti normativi. Per altre informazioni su come il team Microsoft globale di risposta agli eventi imprevisti aiuta a mitigare gli effetti degli attacchi contro i servizi cloud e sull'integrazione della sicurezza nei servizi cloud e nei prodotti aziendali Microsoft, vedere la pagina dedicata alla [sicurezza in Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/security), mentre per informazioni sugli obiettivi di conformità Microsoft, vedere la pagina dedicata alla [conformità in Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Per la maggior parte delle organizzazioni, la sicurezza degli asset aziendali dipende dall'integrità degli account con privilegi usati per amministrare e gestire i sistemi IT. Gli utenti malintenzionati prendono di mira l'accesso con privilegi ai sistemi di infrastruttura (ad esempio Active Directory e Azure Active Directory) per ottenere l'accesso ai dati sensibili dell'organizzazione. 

Gli approcci tradizionali che puntano a proteggere i punti di ingresso e di uscita di una rete come perimetro di sicurezza principale risultano meno efficaci a causa dell'aumento dell'uso di app SaaS e di dispositivi personali in Internet. Il naturale corrispondente del perimetro di sicurezza di rete in un'azienda moderna complessa è rappresentato dai controlli di autenticazione e autorizzazione in un livello di identità dell'organizzazione. 

Questo nuovo "perimetro di sicurezza" permette un controllo efficace sugli account amministrativi con privilegi. È fondamentale proteggere l'accesso con privilegi, indipendentemente dal fatto l'ambiente sia locale, cloud o ibrido con servizi ospitati nel cloud e locali. La protezione dell'accesso amministrativo da avversari determinati richiede l'adozione di un approccio completo e approfondito per isolare i sistemi aziendali dai rischi. 

La protezione dell'accesso con privilegi richiede modifiche a
* Processi, procedure amministrative e gestione delle informazioni
* Componenti tecnici, ad esempio strumenti di difesa degli host, protezioni degli account e gestione delle identità

Questo documento è incentrato principalmente sulla creazione di una roadmap per la protezione delle identità e degli accessi gestiti o segnalati in Azure AD, Microsoft Azure, Office 365 e altri servizi cloud. Le organizzazioni che hanno account amministrativi locali possono fare riferimento alle indicazioni per la gestione dell'accesso con privilegi in ambienti locali e ibridi da Active Directory in [Protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Le indicazioni riportate in questo articolo si riferiscono principalmente alle funzionalità di Azure Active Directory incluse nei piani di Azure Active Directory Premium P1 e P2. Azure Active Directory Premium P2 è incluso nelle suite EMS E5 e Microsoft 365 E5. Queste indicazioni presuppongono che l'organizzazione abbia già acquistato le licenze di Azure AD Premium P2 per gli utenti. Se non si hanno queste licenze, alcune delle indicazioni fornite potrebbero non essere applicabili all'organizzazione. Nell'articolo, inoltre, il termine amministratore globale è sinonimo di "amministratore società" o "amministratore tenant".

## <a name="develop-a-roadmap"></a>Sviluppare una roadmap 

Microsoft consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. È sempre possibile modificare la roadmap per adattarla alle capacità esistenti e ai requisiti specifici dell'organizzazione. Ogni fase della roadmap deve ostacolare sempre di più gli avversari che cercano di attaccare l'accesso con privilegi per gli asset locali, cloud e ibridi. Microsoft consiglia le seguenti quattro fasi della roadmap: questa roadmap consigliata prevede di iniziare con le implementazioni più efficaci e più rapide, in base all'esperienza di Microsoft con gli episodi di attacchi informatici e l'implementazione delle risposte. Le sequenze temporali per la roadmap sono approssimative.

![Fasi della roadmap con sequenze temporali](./media/admin-roles-best-practices/roadmap-timeline.png)

* Fase 1 (24-48 ore): elementi critici che è consigliabile gestire immediatamente

* Fase 2 (2-4 settimane): attenuazione delle tecniche di attacco usate più di frequente

* Fase 3 (1-3 mesi): implementazione di visibilità e controllo completo sull'attività di amministrazione

* Fase 4 (sei mesi e oltre): implementazione continuativa di difese per rafforzare ulteriormente la piattaforma di sicurezza

Questo framework di roadmap è progettato per ottimizzare l'uso delle tecnologie Microsoft che potrebbero essere già state distribuite. È anche possibile sfruttare le principali tecnologie di sicurezza correnti e future e integrare strumenti di sicurezza di altri fornitori che sono già stati distribuiti o si prevede di distribuire. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: elementi critici che è consigliabile gestire immediatamente

![Fase 1](./media/admin-roles-best-practices/stage-one.png)

La fase 1 della roadmap è incentrata sulle attività critiche che possono essere implementate in modo semplice e rapido. È consigliabile eseguire queste poche operazioni immediatamente, entro le prime 24-48 ore, per garantire un livello di base di sicurezza dell'accesso con privilegi. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include le attività seguenti:

### <a name="general-preparation"></a>Preparazione generale

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Attivare Azure AD Privileged Identity Management

Se Azure AD Privileged Identity Management (PIM) non è ancora stato attivato, attivarlo nel tenant di produzione. Dopo l'attivazione di Privileged Identity Management, si riceveranno messaggi di posta elettronica di notifica in caso di modifiche ai ruoli di accesso con privilegi. Queste notifiche forniscono un avviso tempestivo in caso di aggiunta di ulteriori utenti a ruoli con privilegi elevati nella directory.

Azure AD Privileged Identity Management è incluso in Azure AD Premium P2 o EMS E5. Queste soluzioni aiutano a proteggere l'accesso alle applicazioni e alle risorse nell'ambiente locale e nel cloud. Se non si ha già Azure AD Premium P2 o E5 EMS e si vuole valutare più a fondo le funzionalità descritte in questa roadmap, registrarsi per la [versione di valutazione gratuita di 90 giorni di Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Usare queste versioni di valutazione delle licenze per provare Azure AD Privileged Identity Management e Azure AD Identity Protection, per monitorare l'attività mediante funzioni avanzate di report di sicurezza, controllo e avvisi di Azure AD.

Dopo avere attivato Azure AD Privileged Identity Management:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account di amministratore globale del tenant di produzione.

2. Per selezionare il tenant dove usare Privileged Identity Management, selezionare il nome utente nell'angolo in alto a destra del portale di Azure.

3. Selezionare **Tutti i servizi** e filtrare l'elenco per **Azure AD Privileged Identity Management**.

4. Aprire Privileged Identity Management dall'elenco **Tutti i servizi** e aggiungerlo al dashboard.

Alla prima persona che usa Azure AD Privileged Identity Management nel tenant vengono assegnati automaticamente i ruoli **Amministratore della sicurezza** e **Amministratore del ruolo con privilegi**. Solo gli amministratori del ruolo con privilegi possono gestire le assegnazioni di ruoli della directory di Azure AD degli utenti. Dopo l'aggiunta di Azure AD Privileged Identity Management, viene inoltre visualizzata la procedura guidata relativa alla sicurezza che guida nei passaggi inziali di individuazione e assegnazione. Al momento, è possibile uscire dalla procedura guidata senza apportare modifiche aggiuntive. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificare e classificare gli account che si trovano in ruoli con privilegi elevati 

Dopo avere attivato Azure AD Privileged Identity Management, visualizzare gli utenti con i ruoli della directory di amministratore globale, amministratore del ruolo con privilegi, amministratore di Exchange Online e amministratore di SharePoint Online. Se nel tenant non è presente Azure AD PIM, è possibile usare l'[API di PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Iniziare con il ruolo di amministratore globale, che è un ruolo generico: un utente a cui è assegnato questo ruolo amministratore ha le stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione, indipendentemente dal fatto che il ruolo sia stato assegnato nel portale di Office 365, nel portale di Azure oppure usando il modulo di Azure AD per Microsoft PowerShell. 

Rimuovere tutti gli account che non sono più necessari in questi ruoli e classificare gli account rimanenti assegnati ai ruoli di amministratore:

* Singolarmente assegnati a utenti amministratori e che possono anche essere usati per scopi non amministrativi (ad esempio, posta elettronica personale)
* Singolarmente assegnati a utenti amministratori e designati esclusivamente per scopi amministrativi
* Condivisi tra più utenti
* Per scenari critici di accesso di emergenza
* Per script automatizzati
* Per utenti esterni

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definire almeno due account di accesso di emergenza 

Assicurarsi che non possano verificarsi situazioni in cui agli utenti venga inavvertitamente impedito di eseguire attività di amministrazione del tenant di Azure AD perché non è possibile accedere o attivare un account di un singolo utente esistente come amministratore. Se, ad esempio, l'organizzazione è federata con un provider di identità locale, tale provider di identità potrebbe non essere disponibile e quindi gli utenti non sarebbero in grado di accedere in locale. È possibile ridurre l'impatto di una accidentale mancanza di accesso amministrativo archiviando due o più account di accesso di emergenza nel tenant.

Gli account di accesso di emergenza consentono alle organizzazioni di limitare l'accesso con privilegi in un ambiente Azure Active Directory esistente. Si tratta di account con privilegi elevati non assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono impegnarsi a controllare e limitare l'uso dell'account di emergenza solo per il tempo strettamente necessario. 

Valutare gli account assegnati o idonei per il ruolo di amministratore globale. Se non sono presenti account solo cloud con il dominio *.onmicrosoft.com (destinati all'accesso di emergenza in situazioni critiche), crearli. Per altre informazioni, vedere [Gestire gli account amministrativi di accesso di emergenza in Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Attivare l'autenticazione a più fattori e registrare tutti gli altri account amministratore con privilegi elevati non federati per utente singolo 

Richiedere Azure Multi-Factor Authentication (MFA) all'accesso per tutti i singoli utenti a cui sono assegnati in modo permanente uno o più ruoli di amministratore di Azure AD: amministratore globale, amministratore del ruolo con privilegi, amministratore di Exchange Online e amministratore di SharePoint Online. Usare la guida per abilitare [Multi-Factor Authentication (MFA) per gli account amministratore](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) e assicurarsi che tali utenti abbiano tutti eseguito la registrazione in [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Altre informazioni sono disponibili nei passaggi 2 e 3 della guida [Proteggere l'accesso a dati e servizi in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: attenuazione delle tecniche di attacco usate più di frequente

![Fase 2](./media/admin-roles-best-practices/stage-two.png)

La fase 2 della roadmap è incentrata sull'attenuazione delle tecniche di attacco usate più di frequente per il furto e l'abuso di credenziali ed è pensata per essere implementata in circa 2-4 settimane. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include le attività seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Eseguire un inventario di servizi, proprietari e amministratori

Con l'aumentare degli scenari BYOD (Bring Your Own Device) e di lavoro da casa e con la diffusione della connettività wireless nelle aziende, è fondamentale monitorare chi si connette alla rete. Un controllo di sicurezza efficace spesso individua dispositivi, applicazioni e programmi in esecuzione nella rete che non sono supportati dall'IT e quindi potenzialmente non sicuri. Per altre informazioni, vedere [Panoramica su gestione e monitoraggio della sicurezza di Azure](../security/security-management-and-monitoring-overview.md). Assicurarsi di includere tutte le attività seguenti nel processo di inventario. 

* Identificare gli utenti con ruoli amministrativi e i servizi che possono gestire.
* Usare Azure AD PIM per scoprire quali utenti dell'organizzazione hanno accesso amministrativo ad Azure AD, inclusi ruoli aggiuntivi oltre a quelli elencati nella fase 1.
* Oltre ai ruoli definiti in Azure AD, Office 365 include un set di ruoli di amministratore che è possibile assegnare agli utenti dell'organizzazione. Ogni ruolo di amministratore è mappato a funzioni di business comuni e concede alle persone dell'organizzazione le autorizzazioni per eseguire attività specifiche nell'interfaccia di amministrazione di Office 365. Usare l'interfaccia di amministrazione di Office per scoprire quali utenti dell'organizzazione hanno accesso amministrativo a Office 365, anche tramite ruoli non gestiti in Azure AD. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e [Procedure consigliate per la sicurezza per Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Eseguire l'inventario in altri servizi usati nell'organizzazione, ad esempio Azure, Intune o Dynamics 365.
* Assicurarsi che gli account amministratore (gli account usati a scopo amministrativo e non solo per le attività quotidiane degli utenti) abbiano indirizzi di posta elettronica funzionanti associati e abbiano eseguito la registrazione per Azure MFA oppure usino MFA in locale.
* Chiedere agli utenti il motivo per cui necessitano di accesso amministrativo.
* Rimuovere l'accesso amministrativo per gli utenti e i servizi che non ne hanno necessità.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identificare gli account Microsoft in ruoli amministrativi che devono essere spostati in account aziendali o dell'istituto di istruzione 

In alcuni casi, gli amministratori globali iniziali per un'organizzazione riutilizzano le credenziali esistenti dell'account Microsoft quando iniziano a usare Azure AD. Questi account Microsoft devono essere sostituiti da singoli account basati su cloud o sincronizzati. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Separare gli account utente e l'inoltro della posta elettronica per gli account amministratore globale 

Gli account amministratore globale non devono avere indirizzi di posta elettronica personali, che sono regolarmente sottoposti ad attacchi di phishing da parte di utenti malintenzionati. Per separare i rischi correlati a Internet (attacchi di phishing, esplorazione Web non intenzionale) dai privilegi amministrativi, creare account dedicati per ogni utente con privilegi amministrativi. 

Se non è già stato fatto, creare account separati per gli utenti per eseguire attività di amministrazione globale, in modo che gli utenti non aprano inavvertitamente messaggi di posta elettronica o eseguano programmi associati con gli account amministratore. Assicurarsi che la posta elettronica di questi account venga inoltrata a una cassetta postale funzionante.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Verificare che le password degli account amministrativi siano state modificate di recente

Verificare che tutti gli utenti abbiano eseguito l'accesso al proprio account amministrativo e abbiano modificato le password almeno una volta negli ultimi 90 giorni. Assicurarsi inoltre che per eventuali account condivisi di cui più utenti conoscono la password, le password siano state modificate di recente.

#### <a name="turn-on-password-synchronization"></a>Attivare la sincronizzazione password

La sincronizzazione password è una funzionalità usata per sincronizzare gli hash delle password utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud. Anche se si decide di usare la federazione con Active Directory Federation Services (AD FS) o altri provider di identità, è possibile configurare la sincronizzazione password come backup in caso di errore o temporanea indisponibilità dell'infrastruttura locale, ad esempio i server AD o ADFS. In questo modo, gli utenti potranno accedere al servizio usando la stessa password usata per accedere all'istanza di AD locale. Identity Protection può inoltre rilevare le credenziali compromesse confrontando gli hash delle password con password segnalate come compromesse, se un utente ha usato lo stesso indirizzo di posta elettronica e la stessa password in altri servizi non connessi ad Azure AD.  Per altre informazioni, vedere [Implementare la sincronizzazione password con il servizio di sincronizzazione Azure AD Connect](./connect/active-directory-aadconnectsync-implement-password-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Richiedere Multi-Factor Authentication (MFA) per gli utenti con tutti i ruoli con privilegi e gli utenti esposti

Azure AD consiglia di richiedere l'uso di Multi-Factor Authentication (MFA) per tutti gli utenti, compresi gli amministratori e tutti gli altri utenti per i quali, se l'account venisse compromesso, si verificherebbero problemi significativi (ad esempio, gli operatori finanziari). Ciò consente di ridurre il rischio di attacchi causato da una password compromessa.

Attivare:

* [MFA per gli account molto esposti](../multi-factor-authentication/multi-factor-authentication-security-best-practices.md), ad esempio gli account dei dirigenti di un'organizzazione 
* [MFA per ogni account amministratore associato a un singolo utente](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) per altre app SaaS connesse 
* MFA per tutti gli amministratori per le app SaaS Microsoft, inclusi gli amministratori con ruoli gestiti in Exchange Online e nel portale di Office

Se si usa Windows Hello for Business, è possibile soddisfare il requisito MFA con l'esperienza di accesso di Windows Hello. Per altre informazioni, vedere [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurare Identity Protection 

Azure AD Identity Protection è uno strumento di monitoraggio e creazione di report basato su algoritmo che consente di rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione. È possibile configurare risposte automatiche per le attività sospette rilevate e intraprendere l'azione appropriata per risolverle. Per altre informazioni, vedere [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Ottenere Office 365 Secure Score (se si usa Office 365)

Secure Score esamina i servizi di Office 365 in uso (ad esempio, OneDrive, SharePoint ed Exchange), quindi analizza le impostazioni e le attività e le confronta con informazioni di base stabilite da Microsoft. Si ottiene un punteggio che indica a che livello l'organizzazione è allineata alle procedure consigliate di sicurezza. Chiunque disponga delle autorizzazioni di amministratore (amministratore globale o un ruolo di amministratore personalizzato) per un abbonamento a Office 365 Business Premium o Enterprise può accedere a Secure Score all'indirizzo [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Esaminare le indicazioni di sicurezza e conformità di Office 365 (se si usa Office 365)

Il [piano per la sicurezza e la conformità](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) spiega in che modo un cliente deve configurare Office 365 e usare altre funzionalità di EMS. Esaminare quindi i passaggi da 3 a 6 della procedura in [Proteggere l'accesso a dati e servizi in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e vedere le indicazioni in [Monitorare la sicurezza e la conformità in Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurare Monitoraggio attività di Office 365 (se si usa Office 365)

È possibile monitorare il modo in cui le persone dell'organizzazione usano i servizi di Office 365, identificando gli utenti che hanno un account amministrativo e che potrebbero non necessitare di accesso a Office 365 in quanto non accedono a tali portali. Per altre informazioni, vedere [Report attività nell'interfaccia di amministrazione di Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Stabilire i proprietari del piano di risposta a eventi imprevisti ed emergenze

Rispondere in modo efficace agli eventi imprevisti è molto difficile. Per poter rispondere nel modo giusto agli eventi imprevisti sono quindi necessarie risorse e un'attenta pianificazione. È essenziale monitorare continuamente gli attacchi informatici e stabilire le procedure per classificare in ordine di priorità gli interventi di gestione degli eventi imprevisti. Metodi efficaci di raccolta e analisi dei dati e di creazione di report sono fondamentali per sviluppare le relazioni e favorire la comunicazione con altri gruppi interni e proprietari dei piani. Per altre informazioni, vedere [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteggere gli account amministrativi con privilegi locali, se non è già stato fatto

Se il tenant di Azure Active Directory è sincronizzato con Active Directory locale, seguire le indicazioni nella fase 1 in [Protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Le attività includono la creazione di account amministratore separati per gli utenti che devono eseguire attività amministrative in locale, la distribuzione di workstation con accesso con privilegi per gli amministratori di Active Directory e la creazione di password di amministratore locale univoche per le workstation e i server.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Completare un inventario delle sottoscrizioni

Usare Enterprise Portal e il portale di Azure per identificare le sottoscrizioni nell'organizzazione che ospitano le applicazioni di produzione. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Rimuovere gli account Microsoft dai ruoli di amministratore

Gli account Microsoft di altri programmi, ad esempio Xbox Live e Outlook, non devono essere usati come account amministratore per le sottoscrizioni dell'organizzazione. Rimuovere lo stato di amministratore da tutti gli account Microsoft e sostituirli con account aziendali o dell'istituto di istruzione di Active Directory (ad esempio, chris@contoso.com).

#### <a name="monitor-azure-activity"></a>Monitorare l'attività di Azure

Il log attività di Azure fornisce una cronologia degli eventi a livello di sottoscrizione in Azure. Offre informazioni su chi ha creato, aggiornato ed eliminato quali risorse e su quando tali eventi si sono verificati. Per altre informazioni, vedere [Controllare e ricevere notifiche sulle azioni importanti nella sottoscrizione di Azure](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad altre app cloud tramite Azure AD 

#### <a name="configure-conditional-access-policies"></a>Configurare criteri di accesso condizionale

Preparare criteri di accesso condizionale per applicazioni locali e ospitate nel cloud. Se ci sono dispositivi degli utenti aggiunti all'area di lavoro, vedere [Configurazione dell'accesso condizionale locale usando il servizio Registrazione dispositivo di Azure Active Directory](active-directory-device-registration-on-premises-setup.md) per altre informazioni.


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: implementazione di visibilità e controllo completo sull'attività di amministrazione

![Fase 3](./media/admin-roles-best-practices/stage-three.png)

La fase 3 si basa sulle mitigazioni della fase 2 ed è pensata per essere implementata in circa 1-3 mesi. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include i componenti seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Completare una verifica di accesso degli utenti con ruoli di amministratore

Un numero sempre maggiore di utenti aziendali ottiene l'accesso con privilegi tramite i servizi cloud e questo può portare a una piattaforma sempre meno gestita. Ci sono utenti che diventano amministratori globali di Office 365 o delle sottoscrizioni di Azure e utenti che hanno accesso amministrativo alle macchine virtuali o tramite app SaaS. Le organizzazioni dovrebbero invece fare in modo che tutti i dipendenti, in particolare gli amministratori, gestiscano le transazioni aziendali quotidiane come utenti senza privilegi e usino i diritti di amministratore solo quando necessario. Poiché il numero di utenti con ruoli di amministratore potrebbe essere aumentato dall'adozione iniziale, eseguire verifiche di accesso per identificare e confermare ogni utente idoneo ad attivare i privilegi di amministratore. 

Eseguire le operazioni seguenti:

* Determinare quali utenti sono amministratori di Azure AD, abilitare l'accesso amministrativo JIT on demand e applicare controlli di sicurezza basata sui ruoli.
* Convertire gli utenti che non necessitano di accesso con privilegi di amministratore a un ruolo diverso (in assenza di un ruolo idoneo, rimuoverli).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuare l'implementazione di metodi di autenticazione più avanzati per tutti gli utenti 

Richiedere a dirigenti, manager di alto livello, personale addetto alla sicurezza e IT critico e altri utenti molto esposti di usare metodi di autenticazione moderni e affidabili, come Azure MFA o Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Usare workstation dedicate per l'amministrazione di Azure AD

Gli utenti malintenzionati possono tentare di prendere di mira account con privilegi per ottenere l'accesso ai dati e ai sistemi di un'organizzazione in modo da compromettere l'integrità e l'autenticità dei dati, tramite codice dannoso che consente di modificare la logica di programma o di osservare un amministratore mentre immette le credenziali. Per le attività sensibili, le workstation dotate di accesso con privilegi (PAW, Privileged Access Workstation) forniscono un sistema operativo dedicato, protetto dagli attacchi provenienti da Internet e dai vettori di minacce. Separare le attività e gli account sensibili da workstation e dispositivi usati quotidianamente assicura una protezione estrema contro gli attacchi di phishing, le vulnerabilità di applicazioni e sistemi operativi, svariati attacchi di rappresentazione e tecniche di furto delle credenziali, come registrazione delle pressioni di tasti, Pass the Hash e Pass The Ticket. La distribuzione di workstation con accesso con privilegi consente di ridurre il rischio che gli amministratori immettano credenziali di amministratore, se non in un ambiente desktop con sicurezza avanzata. Per altre informazioni, vedere [Privileged Access Workstations](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/privileged-access-workstations) (Workstation con accesso con privilegi).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Esaminare le indicazioni del National Institute of Standards and Technology per la gestione degli eventi imprevisti 

Il National Institute of Standards e Technology (NIST) fornisce linee guida per la gestione degli eventi imprevisti, in particolare per analizzare i dati relativi agli eventi imprevisti e determinare la risposta appropriata per ogni evento. Per altre informazioni, vedere il documento [NIST Computer Security Incident Handling Guide (SP 800-61, revisione 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementare Privileged Identity Management (PIM) per JIT per altri ruoli amministrativi

Per Azure Active Directory, usare la funzionalità [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md). L'attivazione a tempo limitato dei ruoli con privilegi consente di:

* Attivare i privilegi di amministratore per eseguire un'attività specifica
* Applicare MFA durante il processo di attivazione
* Usare gli avvisi per informare gli amministratori delle modifiche fuori banda
* Permettere agli utenti di mantenere determinati privilegi per un periodo di tempo preconfigurato
* Permettere agli amministratori della sicurezza di individuare tutte le identità con privilegi, visualizzare i report di controllo e creare verifiche di accesso per identificare tutti gli utenti idonei per l'attivazione dei privilegi di amministratore

Se si usa già Azure AD Privileged Identity Management, modificare gli intervalli di tempo per i privilegi con limiti di tempo in base alle esigenze (ad esempio, per le finestre di manutenzione).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinare l'esposizione a protocolli di accesso basati su password (se si usa Exchange Online)

In passato, i protocolli presupponevano che le combinazioni di nome utente/password fossero incorporate in dispositivi, account di posta elettronica, telefoni e così via. Ora, con il rischio di attacchi informatici nel cloud, è consigliabile identificare tutti gli utenti le cui credenziali, se compromesse, potrebbero comportare conseguenze molto gravi per l'organizzazione e non permettere a tali utenti di eseguire l'accesso alla posta elettronica tramite nome utente e password, implementando requisiti di autenticazione avanzata e accesso condizionale. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Completare una valutazione di verifica dei ruoli per i ruoli di Office 365 (se si usa Office 365)

Valutare se tutti gli utenti amministratori hanno i ruoli corretti (eliminare e riassegnare i ruoli in base a questa valutazione).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Esaminare l'approccio di gestione degli eventi imprevisti relativi alla sicurezza usato in Office 365 e confrontarlo con quello della propria organizzazione

È possibile scaricare il report in [Security Incident Management in Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302) (Gestione degli eventi imprevisti relativi alla sicurezza in Microsoft Office 365).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuare a proteggere gli account amministrativi con privilegi locali

Se il servizio Azure Active Directory è connesso con Active Directory locale, seguire le indicazioni nella fase 2 in [Protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Le attività includono la distribuzione di workstation con accesso con privilegi per tutti gli amministratori, l'applicazione di MFA, l'uso di Just Enough Administration per la manutenzione del controller di dominio, la riduzione della superficie di attacco dei domini e la distribuzione di ATA per il rilevamento degli attacchi.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure

#### <a name="establish-integrated-monitoring"></a>Implementare un monitoraggio integrato

Il [Centro sicurezza di Azure](../security-center/security-center-intro.md) offre strumenti integrati di monitoraggio della sicurezza e gestione dei criteri per le sottoscrizioni di Azure, aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate ed è compatibile con un ampio ecosistema di soluzioni di sicurezza.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Creare un inventario degli account con privilegi nelle macchine virtuali ospitate

Nella maggior parte dei casi, non è necessario concedere agli utenti autorizzazioni senza restrizioni per tutte le sottoscrizioni o le risorse di Azure. È possibile usare i ruoli di amministratore di Azure AD per separare i compiti all'interno dell'organizzazione e concedere agli utenti l'accesso solo nella misura necessaria per consentire loro di svolgere lavori specifici. Usare, ad esempio, i ruoli di amministratore di Azure AD per consentire a un amministratore di gestire solo le macchine virtuali in una sottoscrizione, mentre un altro amministratore può gestire i database SQL nella stessa sottoscrizione. Per altre informazioni, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](role-based-access-control-what-is.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementare PIM per i ruoli di amministratore di Azure AD

Usare Privileged identity Management con i ruoli di amministratore di Azure AD per gestire, controllare e monitorare l'accesso alle risorse di Azure. PIM consente di proteggere gli account con privilegi da attacchi informatici riducendo il tempo di esposizione dei privilegi e aumentando la visibilità sul loro uso tramite report e avvisi. Per altre informazioni, vedere [Gestire l'accesso alle risorse di Azure con Privileged Identity Management](pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usare le integrazioni log di Azure per inviare i log di Azure rilevanti ai sistemi di informazioni di sicurezza e gestione degli eventi 

Il servizio Integrazione log di Azure consente di integrare i log non elaborati delle risorse di Azure nei sistemi di informazioni di sicurezza e gestione degli eventi dell'organizzazione. [Integrazione log di Azure](../security/security-azure-log-integration-overview.md) raccoglie gli eventi di Windows dai log del Visualizzatore eventi di Windows e quelli delle risorse di Azure dai log attività di Azure, dagli avvisi del Centro sicurezza di Azure e dai log di Diagnostica di Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad altre app cloud tramite Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementare il provisioning degli utenti per le app connesse

Azure AD consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni cloud (SaaS), ad esempio Dropbox, Salesforce, ServiceNow e così via. Per altre informazioni, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrare la protezione delle informazioni

MCAS consente di esaminare i file e impostare criteri in base alle etichette di classificazione di Azure Information Protection, offrendo visibilità e controllo maggiori sui dati nel cloud. È possibile analizzare e classificare i dati nel cloud e applicare etichette di Azure Information Protection. Per altre informazioni, vedere [Integrazione di Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurare l'accesso condizionale

Configurare l'accesso condizionale basato sull'importanza di gruppi, posizioni e applicazioni per le [app SaaS](https://azure.microsoft.com/overview/what-is-saas/) e le app connesse di Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorare l'attività nelle app cloud connesse

Per proteggere l'accesso degli utenti anche nelle applicazioni connesse, è consigliabile usare [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Questo strumento consente di proteggere l'accesso aziendale alle app cloud, oltre a proteggere gli account di amministratore, grazie a:

* Visibilità e controllo maggiori sulle app cloud
* Creazione di criteri per l'accesso, le attività e la condivisione dei dati
* Identificazione automatica di attività rischiose, comportamenti anomali e minacce
* Prevenzione della perdita dei dati
* Riduzione al minimo dei rischi e applicazione dei criteri e prevenzione delle minacce in modo automatico

L'agente SIEM di Cloud App Security integra Cloud App Security con il server SIEM per consentire il monitoraggio centralizzato di attività e avvisi di Office 365. Viene eseguito nel server ed esegue il pull di avvisi e attività da Cloud App Security e li trasmette al server SIEM. Per altre informazioni, vedere [Integrazione SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: implementazione continuativa di difese per interventi di sicurezza più proattivi


![Fase 4](./media/admin-roles-best-practices/stage-four.png)

La fase 4 della roadmap si basa sulla visibilità ottenuta nella fase 3 ed è pensata per essere implementata in sei mesi e oltre. Il completamento di una roadmap aiuta a sviluppare protezioni avanzate dell'accesso con privilegi per difendersi dai potenziali attacchi attualmente noti e presenti. Sfortunatamente, le minacce alla sicurezza si evolvono e cambiano continuamente, quindi è necessario considerare la sicurezza come un processo continuativo mirato a ostacolare i tentativi degli avversari che prendono di mira l'ambiente.

La protezione dell'accesso con privilegi è il primo importante passaggio per garantire la sicurezza degli asset aziendali in un'organizzazione moderna, ma non è l'unico aspetto di un programma di sicurezza completo che deve includere elementi come criteri, operazioni, sicurezza delle informazioni, server, applicazioni, PC, dispositivi, infrastruttura cloud e altri componenti che richiedono sicurezza costante. 

Oltre a gestire gli account di accesso con privilegi, è consigliabile prestare regolarmente attenzione agli aspetti seguenti:

* Verificare che gli amministratori eseguano le attività quotidiane come utenti senza privilegi.
* Concedere l'accesso con privilegi solo quando necessario e successivamente rimuoverlo (JIT).
* Conservare ed esaminare le attività di controllo correlate agli account con privilegi.

Per altre informazioni sulla creazione di una roadmap di sicurezza completa, vedere [Risorse sull'architettura IT del cloud Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Per altre informazioni sull'uso dei servizi Microsoft per le attività illustrate in questi argomenti, contattare il rappresentante Microsoft oppure vedere [Build critical cyber defenses to protect your enterprise](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx) (Preparare difese informatiche critiche per proteggere l'azienda).

La fase finale e continuativa della roadmap per la sicurezza dell'accesso con privilegi include i componenti seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="review-admin-roles-in-azure-active-directory"></a>Esaminare i ruoli di amministratore in Azure Active Directory 

Determinare se i ruoli di amministratore di Azure AD predefiniti correnti sono ancora aggiornati e assicurarsi che gli utenti siano associati solo ai ruoli e alle deleghe per cui hanno bisogno delle autorizzazioni corrispondenti. Azure AD consente di designare amministratori diversi per le diverse funzioni. Per altre informazioni, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Esaminare gli utenti che amministrano i dispositivi aggiunti ad Azure AD

Per altre informazioni, vedere [Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Esaminare i membri dei [ruoli di amministratore predefiniti di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se si usa Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Convalidare il piano di risposta agli eventi imprevisti

Per un continuo miglioramento del piano, Microsoft consiglia di convalidarne regolarmente il funzionamento:

* Esaminare la roadmap esistente per verificare se manca qualcosa
* In base all'analisi post mortem, esaminare le procedure consigliate esistenti o definirne di nuove
* Verificare che il piano di risposta agli eventi imprevisti aggiornato e le procedure consigliate siano distribuiti in tutta l'organizzazione


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure 

Determinare se è necessario [trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Rompere il vetro": cosa fare in caso di emergenza

![Emergenza](./media/admin-roles-best-practices/emergency.jpeg)

1. Comunicare ai principali dirigenti e ai responsabili della sicurezza le informazioni pertinenti relative all'evento imprevisto.

2. Esaminare la strategia da adottare in caso di attacco. 

3. Recuperare la combinazione di nome utente/password dell'account per le emergenze per accedere ad Azure AD. 

4. Chiedere aiuto a Microsoft [creando una richiesta di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md).

5. Esaminare i [report di accesso di Azure AD](active-directory-reporting-azure-portal.md). Potrebbe esserci un ritardo tra il momento in cui si verifica un evento e quello in cui viene incluso nel report.

6. Per gli ambienti ibridi, in caso di federazione e se il server AD FS non è disponibile, potrebbe essere necessario passare temporaneamente dall'autenticazione federata all'uso della sincronizzazione degli hash delle password. In questo modo, si passa dalla federazione del dominio all'autenticazione gestita fino a quando il server AD FS non ritorna disponibile.

7. Monitorare la posta elettronica per gli account con privilegi.

8. Assicurarsi di salvare i backup dei log rilevanti per potenziali indagini forensi e legali.

Per altre informazioni su come Microsoft Office 365 gestisce gli eventi di sicurezza, vedere [Security Incident Management in Microsoft Office 365](http://aka.ms/Office365SIM) (Gestione degli eventi di sicurezza in Microsoft Office 365).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Domande frequenti: domande comuni relative alla protezione dell'accesso con privilegi  


**D:** Cosa è possibile fare se non sono ancora stati implementati i componenti per l'accesso sicuro?

**Risposta:** Definire almeno due account di emergenza, assegnare MFA agli account amministratore con privilegi e separare gli account utente dagli account amministratore globale.


**D:** Dopo una violazione, qual è il problema principale da affrontare?

**Risposta:** Assicurarsi di imporre l'uso del livello di autenticazione più elevato per gli utenti molto esposti.


**D:** Cosa succede se gli amministratori con privilegi sono stati disattivati?

**Risposta:** Creare un account amministratore globale che viene mantenuto sempre aggiornato.


**D:** Cosa succede se è rimasto un solo amministratore globale e non è possibile raggiungerlo? 

**Risposta:** Usare uno degli account di emergenza per ottenere immediatamente l'accesso con privilegi.


**D:** Come è possibile proteggere gli amministratori nell'organizzazione?

**Risposta:** Fare in modo che gli amministratori eseguano le attività quotidiane come utenti "standard" senza privilegi.
 

**D:** Quali sono le procedure consigliate per la creazione di account amministratore in Azure AD?

**Risposta:** Riservare l'accesso con privilegi ad attività amministrative specifiche.


**D:** Quali strumenti sono disponibili per ridurre l'accesso amministratore permanente?

**Risposta:** I ruoli di amministratore di Azure AD e Privileged Identity Management (PIM).


**D:** Qual è la posizione di Microsoft sulla sincronizzazione degli account amministratore in Azure AD?

**Risposta:** Gli account amministratore di livello 0 (inclusi account, gruppi e altri asset che hanno controllo amministrativo diretto o indiretto su foresta, domini o controller di dominio e tutti gli asset di AD) vengono usati solo per gli account AD locali e non sono in genere sincronizzati per Azure AD per il cloud. 


**D:** Come è possibile impedire agli amministratori di assegnare l'accesso amministratore casuale nel portale?

**Risposta:** Usare account senza privilegi per tutti gli utenti e per la maggior parte degli amministratori. Iniziare sviluppando un footprint dell'organizzazione per determinare i pochi account amministratore che devono avere privilegi. Monitorare inoltre gli utenti amministratori appena creati.


## <a name="next-steps"></a>Passaggi successivi

* [Microsoft Trust Center per la sicurezza dei prodotti](https://www.microsoft.com/en-us/trustcenter/security) - Funzionalità di sicurezza dei prodotti e dei servizi Microsoft

* [Microsoft Trust Center - Conformità](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) - Set completo di offerte Microsoft per i servizi cloud

* [Indicazioni su come svolgere una valutazione dei rischi](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) - Gestire i requisiti di sicurezza e conformità per i servizi cloud Microsoft

### <a name="other-ms-online-services"></a>Altri servizi Microsoft online 

* [Sicurezza di Microsoft Intune](https://www.microsoft.com/en-us/trustcenter/security/intune-security) - Intune offre funzionalità di gestione di dispositivi mobili, gestione di applicazioni mobili e gestione di PC dal cloud.

* [Sicurezza di Microsoft Dynamics 365](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) - Dynamics 365 è la soluzione Microsoft basata su cloud che combina funzionalità CRM (Customer Relationship Management) ed ERP (Enterprise Resource Planning).

 
