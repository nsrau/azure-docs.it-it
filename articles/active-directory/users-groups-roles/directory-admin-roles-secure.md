---
title: Procedure di accesso protetto per gli amministratori in Azure AD | Microsoft Docs
description: Verificare la sicurezza degli account amministratore e dell'accesso amministrativo nell'organizzazione. Per progettisti di sistemi e professionisti IT che configurano Azure AD, Azure e i Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c580a39db97e1ce50c3d244db3023bf422bca08
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837193"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD

La sicurezza degli asset aziendali dipende dall'integrità degli account con privilegi che amministrano i sistemi IT. Gli utenti malintenzionati usano gli attacchi di furto delle credenziali per gli account amministratore di destinazione e altri accessi con privilegi per provare ad accedere ai dati sensibili.

Per i servizi cloud, prevenzione e risposta sono responsabilità comuni del provider di servizi cloud e del cliente. Per altre informazioni sulle minacce più recenti agli endpoint e nel cloud, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Questo articolo può aiutare a sviluppare una roadmap per colmare il divario tra i piani correnti e le indicazioni fornite di seguito.

> [!NOTE]
> Microsoft si impegna a offrire i massimi livelli di attendibilità, trasparenza, conformità agli standard e conformità ai requisiti normativi. Per altre informazioni su come il team Microsoft globale di risposta agli eventi imprevisti aiuta a mitigare gli effetti degli attacchi contro i servizi cloud e sull'integrazione della sicurezza nei servizi cloud e nei prodotti aziendali Microsoft, vedere la pagina dedicata alla [sicurezza in Microsoft Trust Center](https://www.microsoft.com/trustcenter/security), mentre per informazioni sugli obiettivi di conformità Microsoft, vedere la pagina dedicata alla [conformità in Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance).

Tradizionalmente, la sicurezza aziendale è stata incentrata sui punti di ingresso e di uscita di una rete come perimetro di sicurezza. Tuttavia, le app SaaS e i dispositivi personali su Internet hanno reso questo approccio meno efficace. In Azure AD, il perimetro di sicurezza di rete viene sostituito con l'autenticazione nel livello di identità dell'organizzazione, con gli utenti assegnati ai ruoli amministrativi con privilegi in controllo. L'accesso deve essere protetto, indipendentemente dal fatto che l'ambiente sia locale, cloud o ibrido.

Per proteggere l'accesso con privilegi è necessario apportare le modifiche seguenti:

* Processi, procedure amministrative e gestione delle informazioni
* Componenti tecnici, ad esempio strumenti di difesa degli host, protezioni degli account e gestione delle identità

È possibile proteggere l'accesso con privilegi in modo che sia gestito e segnalato nei servizi Microsoft a cui si è interessati. Se si dispone di account di amministratore locale, vedere le linee guida per l'accesso locale e con privilegi ibridi in Active Directory in [protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> Le indicazioni riportate in questo articolo si riferiscono principalmente alle funzionalità di Azure Active Directory incluse nei piani di Azure Active Directory Premium P1 e P2. Azure Active Directory Premium P2 è incluso nelle suite EMS E5 e Microsoft 365 E5. Queste indicazioni presuppongono che l'organizzazione abbia già acquistato le licenze di Azure AD Premium P2 per gli utenti. Se non si hanno queste licenze, alcune delle indicazioni fornite potrebbero non essere applicabili all'organizzazione. Inoltre, in questo articolo il termine amministratore globale (o amministratore globale) indica la stessa cosa di "amministratore società" o "amministratore tenant".

## <a name="develop-a-roadmap"></a>Sviluppare una roadmap

Microsoft consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. È sempre possibile modificare la roadmap per adattarla alle capacità esistenti e ai requisiti specifici dell'organizzazione. Ogni fase della roadmap deve ostacolare sempre di più gli avversari che cercano di attaccare l'accesso con privilegi per gli asset locali, cloud e ibridi. Microsoft consiglia le seguenti quattro fasi della roadmap. Pianificare prima di tutto le implementazioni più efficaci e più rapide. Questo articolo può essere la tua guida, in base alle esperienze di Microsoft con l'implementazione di attacchi e richieste di attacchi informatici. Le sequenze temporali per questa roadmap sono approssimazioni.

![Fasi della roadmap con sequenze temporali](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 ore): elementi critici che è consigliabile gestire immediatamente

* Fase 2 (2-4 settimane): attenuazione delle tecniche di attacco usate più di frequente

* Fase 3 (1-3 mesi): implementazione di visibilità e controllo completo sull'attività di amministrazione

* Fase 4 (sei mesi e oltre): implementazione continuativa di difese per rafforzare ulteriormente la piattaforma di sicurezza

Questo framework di roadmap è progettato per ottimizzare l'uso delle tecnologie Microsoft che potrebbero essere già state distribuite. Prendere in considerazione la possibilità di eseguire il collegamento a tutti gli strumenti di sicurezza di altri fornitori già distribuiti o di considerare la distribuzione.

## <a name="stage-1-critical-items-to-do-right-now"></a>Fase 1: elementi critici da eseguire in questo momento

![Fasi 1 elementi critici da eseguire prima](./media/directory-admin-roles-secure/stage-one.png)

La fase 1 della roadmap è incentrata sulle attività critiche che possono essere implementate in modo semplice e rapido. È consigliabile eseguire queste poche operazioni immediatamente, entro le prime 24-48 ore, per garantire un livello di base di sicurezza dell'accesso con privilegi. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include le attività seguenti:

### <a name="general-preparation"></a>Preparazione generale

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Attivare Azure AD Privileged Identity Management

Si consiglia di attivare Azure AD Privileged Identity Management (PIM) nell'ambiente di produzione Azure AD. Dopo aver acceso PIM, si riceveranno messaggi di posta elettronica di notifica per le modifiche ai ruoli di accesso con privilegi. Le notifiche forniscono avvisi tempestivi quando vengono aggiunti altri utenti ai ruoli con privilegi elevati.

Azure AD Privileged Identity Management è incluso in Azure AD Premium P2 o EMS E5. Per proteggere l'accesso ad applicazioni e risorse in locale e nel cloud, iscriversi al [Enterprise Mobility + Security versione di valutazione gratuita di 90 giorni](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Azure AD Privileged Identity Management e Azure AD Identity Protection monitorare l'attività di sicurezza tramite Azure AD Reporting, il controllo e gli avvisi.

Dopo aver acceso Azure AD Privileged Identity Management:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account che sia un amministratore globale dell'organizzazione di produzione Azure ad.

2. Per selezionare l'organizzazione Azure AD in cui si vuole usare Privileged Identity Management, selezionare il nome utente nell'angolo in alto a destra della portale di Azure.

3. Nel menu portale di Azure selezionare tutti i **Servizi** e filtrare l'elenco per **Azure ad Privileged Identity Management**.

4. Aprire Privileged Identity Management dall'elenco **Tutti i servizi** e aggiungerlo al dashboard.

Il primo utente che utilizza PIM nell'organizzazione viene assegnato ai ruoli **amministratore della sicurezza** e **amministratore del ruolo con privilegi** . Solo gli amministratori del ruolo con privilegi possono gestire le assegnazioni di ruoli della directory di Azure AD degli utenti. La procedura guidata di sicurezza PIM illustra l'esperienza iniziale di individuazione e assegnazione. Al momento, è possibile uscire dalla procedura guidata senza apportare modifiche aggiuntive.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identificare e classificare gli account che si trovano in ruoli con privilegi elevati

Dopo l'attivazione di Azure AD Privileged Identity Management, visualizzare gli utenti che hanno i ruoli Azure AD seguenti:

* Amministratore globale
* Amministratore dei ruoli con privilegi
* Amministratore di Exchange Online
* Amministratore di SharePoint Online

Se non si ha Azure AD Privileged Identity Management nell'organizzazione, è possibile usare l' [API di PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Iniziare con il ruolo di amministratore globale perché un amministratore globale ha le stesse autorizzazioni in tutti i servizi cloud per cui l'organizzazione ha sottoscritto. Queste autorizzazioni vengono concesse indipendentemente dalla loro assegnazione: nell'interfaccia di amministrazione di Microsoft 365, nella portale di Azure o dal modulo Azure AD per Microsoft PowerShell.

Rimuovere tutti gli account non più necessari in questi ruoli. Classificare quindi gli account rimanenti assegnati ai ruoli di amministratore:

* Assegnati a utenti amministratori, ma usati anche per scopi non amministrativi (ad esempio, posta elettronica personale)
* Assegnati a utenti amministratori e utilizzati solo a scopo amministrativo
* Condivisi tra più utenti
* Per scenari critici di accesso di emergenza
* Per script automatizzati
* Per utenti esterni

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definire almeno due account di accesso di emergenza

È possibile che un utente sia accidentalmente bloccato dal proprio ruolo. Ad esempio, se un provider di identità locale federato non è disponibile, gli utenti non possono accedere o attivare un account amministratore esistente. È possibile preparare la mancanza accidentale di accesso archiviando due o più account di accesso di emergenza.

Gli account di accesso di emergenza consentono di limitare l'accesso con privilegi in un'organizzazione Azure AD. Questi account hanno privilegi elevati e non sono assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a situazioni di emergenza per gli scenari "Break Glass" in cui non è possibile usare gli account amministrativi normali. Assicurarsi di controllare e ridurre l'utilizzo dell'account di emergenza solo a tale tempo per cui è necessario.

Valutare gli account assegnati o idonei per il ruolo di amministratore globale. Se non vengono visualizzati account solo cloud che usano il \*dominio. onmicrosoft.com (per l'accesso di emergenza "Break Glass"), crearli. Per altre informazioni, vedere [Gestire gli account amministrativi di accesso di emergenza in Azure AD](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Attivare l'autenticazione a più fattori e registrare tutti gli altri account amministratore non federati a utente singolo con privilegi elevati

Richiedere Azure Multi-Factor Authentication (MFA) all'accesso per tutti i singoli utenti a cui sono assegnati in modo permanente uno o più ruoli di amministratore di Azure AD: amministratore globale, amministratore del ruolo con privilegi, amministratore di Exchange Online e amministratore di SharePoint Online. Usare la guida per abilitare [Multi-Factor Authentication (MFA) per gli account amministratore](../authentication/howto-mfa-userstates.md) e assicurarsi che tali utenti abbiano tutti eseguito la registrazione in [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Altre informazioni sono disponibili nei passaggi 2 e 3 della guida [Proteggere l'accesso a dati e servizi in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Fase 2: mitigare gli attacchi usati di frequente

![Fase 2 attenuare gli attacchi usati di frequente](./media/directory-admin-roles-secure/stage-two.png)

La fase 2 della roadmap è incentrata sull'attenuazione delle tecniche di attacco usate più di frequente per il furto e l'abuso di credenziali e può essere implementata in circa 2-4 settimane. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include le attività seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Eseguire un inventario di servizi, proprietari e amministratori

L'aumento del "Bring Your Own Device" e l'utilizzo dei criteri domestici e la crescita della connettività wireless rendono cruciale il monitoraggio della connessione alla rete. Un controllo di sicurezza può rivelare i dispositivi, le applicazioni e i programmi della rete che l'organizzazione non supporta e che rappresentano un rischio elevato. Per altre informazioni, vedere [Panoramica su gestione e monitoraggio della sicurezza di Azure](../../security/fundamentals/management-monitoring-overview.md). Assicurarsi di includere tutte le attività seguenti nel processo di inventario.

* Identificare gli utenti con ruoli amministrativi e i servizi che possono gestire.
* Usare Azure AD PIM per scoprire quali utenti dell'organizzazione hanno accesso amministrativo ai Azure AD.
* Oltre ai ruoli definiti in Azure AD, Office 365 include un set di ruoli di amministratore che è possibile assegnare agli utenti dell'organizzazione. Ogni ruolo di amministratore esegue il mapping a funzioni di business comuni e consente agli utenti dell'organizzazione di eseguire attività specifiche nell'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com). Usare l'interfaccia di amministrazione di Microsoft 365 per scoprire quali utenti dell'organizzazione hanno accesso amministrativo a Office 365, anche tramite ruoli non gestiti in Azure AD. Per ulteriori informazioni, vedere [informazioni sui ruoli di amministratore di office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) e sulle [procedure di sicurezza per Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Eseguire l'inventario nei servizi su cui si basa l'organizzazione, ad esempio Azure, Intune o Dynamics 365.
* Assicurarsi che gli account utilizzati per scopi amministrativi:

  * Indirizzi di posta elettronica di lavoro allegati
  * Hanno effettuato la registrazione per Azure Multi-Factor Authentication o usano l'autenticazione a più fattori in locale
* Chiedere agli utenti il motivo per cui necessitano di accesso amministrativo.
* Rimuovere l'accesso amministrativo per gli utenti e i servizi che non ne hanno necessità.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identificare gli account Microsoft in ruoli amministrativi che devono essere spostati in account aziendali o dell'istituto di istruzione

Se gli amministratori globali iniziali riutilizzano le credenziali di account Microsoft esistenti quando iniziano a usare Azure AD, sostituire gli account Microsoft con singoli account basati sul cloud o sincronizzati.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Separare gli account utente e l'inoltro della posta elettronica per gli account amministratore globale

Gli account di posta elettronica personali sono regolarmente phishing da utenti malintenzionati, un rischio che rende gli indirizzi di posta elettronica personali inaccettabili per gli account amministratore globale. Per separare i rischi per Internet dai privilegi amministrativi, creare account dedicati per ogni utente con privilegi amministrativi.

* Assicurarsi di creare account distinti per consentire agli utenti di eseguire attività di amministrazione globali
* Assicurarsi che gli amministratori globali non aprano accidentalmente i messaggi di posta elettronica o eseguano programmi con gli account amministratore
* Assicurarsi che gli account dispongano di un messaggio di posta elettronica di inoltro a una cassetta postale di lavoro

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Verificare che le password degli account amministrativi siano state modificate di recente

Assicurarsi che tutti gli utenti abbiano eseguito l'accesso agli account amministrativi e abbiano modificato le password almeno una volta negli ultimi 90 giorni. Verificare inoltre che le password degli account condivisi siano state modificate di recente.

#### <a name="turn-on-password-hash-synchronization"></a>Attivare la sincronizzazione dell'hash delle password

Azure AD Connect sincronizza un hash dell'hash della password di un utente da Active Directory locale a un'organizzazione di Azure AD basata sul cloud. È possibile usare la sincronizzazione dell'hash delle password come backup se si usa la Federazione con Active Directory Federation Services (AD FS). Questo backup può essere utile se la Active Directory locale o i server AD FS sono temporaneamente non disponibili.

La sincronizzazione dell'hash delle password consente agli utenti di accedere a un servizio usando la stessa password usata per accedere all'istanza di Active Directory locale. La sincronizzazione dell'hash delle password consente a Identity Protection di rilevare le credenziali compromesse confrontando gli hash delle password con le password note per essere compromesse. Per altre informazioni, vedere [implementare la sincronizzazione dell'hash delle password con Azure ad Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Richiedi autenticazione a più fattori per gli utenti in ruoli con privilegi e utenti esposti

Azure AD consiglia di richiedere l'autenticazione a più fattori per tutti gli utenti. Assicurarsi di prendere in considerazione gli utenti che hanno un impatto significativo se il loro account è stato compromesso (ad esempio, funzionari finanziari). Multi-factor authentication riduce il rischio di un attacco a causa di una password compromessa.

Attivare:

* Autenticazione a più fattori [usando i criteri di accesso condizionale](../authentication/howto-mfa-getstarted.md) per tutti gli utenti dell'organizzazione.

Se si usa Windows Hello for business, il requisito di autenticazione a più fattori può essere soddisfatto usando l'esperienza di accesso di Windows Hello. Per altre informazioni, vedere [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport).

#### <a name="configure-identity-protection"></a>Configurare Identity Protection

Azure AD Identity Protection è uno strumento di monitoraggio e creazione di report basato su algoritmi che rileva le potenziali vulnerabilità che interessano le identità dell'organizzazione. È possibile configurare risposte automatiche per le attività sospette rilevate e intraprendere l'azione appropriata per risolverle. Per altre informazioni, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Ottenere Office 365 Secure Score (se si usa Office 365)

Il Punteggio sicuro esamina le impostazioni e le attività per i servizi di Office 365 in uso e le confronta con una baseline stabilita da Microsoft. Si otterrà un punteggio in base alla modalità di allineamento delle procedure di sicurezza. Chiunque disponga delle autorizzazioni di amministratore per una sottoscrizione Office 365 Business Premium o Enterprise può accedere al Punteggio sicuro all' [https://securescore.office.com](https://securescore.office.com/)indirizzo.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Esaminare le indicazioni di sicurezza e conformità di Office 365 (se si usa Office 365)

Il [piano di sicurezza e conformità](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) descrive l'approccio per un cliente di Office 365 per configurare Office 365 e abilitare altre funzionalità di EMS. Esaminare quindi i passaggi da 3 a 6 della procedura in [Proteggere l'accesso a dati e servizi in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) e vedere le indicazioni in [Monitorare la sicurezza e la conformità in Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurare Monitoraggio attività di Office 365 (se si usa Office 365)

Monitorare l'organizzazione per gli utenti che usano Office 365 per identificare il personale che dispone di un account amministratore ma potrebbe non avere bisogno dell'accesso a Office 365 perché non accedono a questi portali. Per altre informazioni, vedere [report attività nell'interfaccia di amministrazione di Microsoft 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Stabilire i proprietari del piano di risposta a eventi imprevisti ed emergenze

Per stabilire una funzionalità di risposta agli eventi imprevisti corretta, è necessario pianificare e risorse. È necessario monitorare continuamente gli attacchi informatici e stabilire le priorità per la gestione degli eventi imprevisti. Raccogli, analizza e segnala i dati degli eventi imprevisti per creare relazioni e stabilire la comunicazione con altri gruppi interni e proprietari del piano. Per altre informazioni, vedere [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717).

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Proteggere gli account amministrativi con privilegi locali, se non è già stato fatto

Se la Azure Active Directory organizzazione viene sincronizzata con Active Directory locale, seguire le indicazioni in [sicurezza Privileged Access roadmap](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): questa fase include:

* Creazione di account amministrativi separati per gli utenti che devono eseguire attività amministrative locali
* Distribuzione di workstation con accesso con privilegi per amministratori di Active Directory
* Creazione di password di amministratore locale univoche per workstation e server

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Completare un inventario delle sottoscrizioni

Usare Enterprise Portal e il portale di Azure per identificare le sottoscrizioni nell'organizzazione che ospitano le applicazioni di produzione.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Rimuovere gli account Microsoft dai ruoli di amministratore

Gli account Microsoft di altri programmi, ad esempio Xbox, Live e Outlook, non devono essere usati come account di amministratore per le sottoscrizioni dell'organizzazione. Rimuovere lo stato amministratore da tutti gli account Microsoft e sostituire con Azure AD (ad esempio chris@contoso.com,) account aziendali o dell'Istituto di istruzione. Per gli scopi amministrativi, dipendono da account autenticati in Azure AD e non in altri servizi.

#### <a name="monitor-azure-activity"></a>Monitorare l'attività di Azure

Il log attività di Azure fornisce una cronologia degli eventi a livello di sottoscrizione in Azure. Offre informazioni su chi ha creato, aggiornato ed eliminato quali risorse e su quando tali eventi si sono verificati. Per altre informazioni, vedere [Controllare e ricevere notifiche sulle azioni importanti nella sottoscrizione di Azure](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad altre app cloud tramite Azure AD

#### <a name="configure-conditional-access-policies"></a>Configurare i criteri di accesso condizionale

Preparare i criteri di accesso condizionale per applicazioni locali e ospitate nel cloud. Se sono stati aggiunti dispositivi all'area di lavoro, è possibile ottenere altre informazioni dalla [configurazione dell'accesso condizionale locale usando Azure Active Directory la registrazione del dispositivo](../active-directory-device-registration-on-premises-setup.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Fase 3: assumere il controllo delle attività amministrative

![Fase 3: assumere il controllo delle attività amministrative](./media/directory-admin-roles-secure/stage-three.png)

La fase 3 si basa sulle mitigazioni della fase 2 e deve essere implementata in circa 1-3 mesi. Questa fase della roadmap per la sicurezza dell'accesso con privilegi include i componenti seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Completare una verifica di accesso degli utenti con ruoli di amministratore

Un maggior numero di utenti aziendali sta ottenendo l'accesso con privilegi tramite i servizi cloud, che possono causare l'accesso non gestito. Oggi gli utenti possono diventare amministratori globali per Office 365, amministratori della sottoscrizione di Azure o avere l'accesso amministrativo alle macchine virtuali o tramite app SaaS.

L'organizzazione deve avere tutti i dipendenti che gestiscono le transazioni aziendali ordinarie come utenti senza privilegi e quindi concedere diritti di amministratore solo se necessario. Completare le verifiche di accesso per identificare e verificare gli utenti idonei per l'attivazione dei privilegi di amministratore.

È consigliabile:

1. Determinare quali utenti sono amministratori di Azure AD, abilitare l'accesso amministrativo JIT on demand e applicare controlli di sicurezza basata sui ruoli.
2. Convertire gli utenti che non necessitano di accesso con privilegi di amministratore a un ruolo diverso (in assenza di un ruolo idoneo, rimuoverli).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Continuare l'implementazione di metodi di autenticazione più avanzati per tutti gli utenti

Richiedere agli utenti altamente esposti di avere un'autenticazione moderna e avanzata, ad esempio l'autenticazione a più fattori di Azure o Windows Hello. Di seguito sono riportati alcuni esempi di utenti altamente esposti:

* Dirigenti C-Suite
* Manager di alto livello
* Personale IT e di sicurezza critico

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Usare workstation dedicate per l'amministrazione di Azure AD

Gli utenti malintenzionati potrebbero provare a individuare gli account con privilegi in modo che possano compromettere l'integrità e l'autenticità dei dati. Spesso usano codice dannoso che modifica la logica del programma o spia l'amministratore che immette una credenziale. Le workstation PAW (Privileged Access Workstation, workstation amministrativa con privilegi) dispongono di un sistema operativo dedicato per le attività sensibili che devono essere protette dagli attacchi provenienti da Internet e dai vettori di minacce di qualsiasi tipo. Separare le attività e gli account sensibili dalle workstation e dai dispositivi usati quotidianamente garantisce una protezione avanzata da:

* Attacchi di phishing
* Vulnerabilità del sistema operativo e dell'applicazione
* Attacchi di rappresentazione
* Attacchi per il furto di credenziali, ad esempio la registrazione di tasti, pass-the-hash e pass-the-ticket

Distribuendo workstation con accesso con privilegi, è possibile ridurre il rischio di immissione delle credenziali da parte degli amministratori in un ambiente desktop che non è stato finalizzato. Per altre informazioni, vedere [Privileged Access Workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) (Workstation con accesso con privilegi).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Esaminare le indicazioni del National Institute of Standards and Technology per la gestione degli eventi imprevisti

Il National Institute of Standards e Technology (NIST) fornisce linee guida per la gestione degli eventi imprevisti, in particolare per analizzare i dati relativi agli eventi imprevisti e determinare la risposta appropriata per ogni evento. Per altre informazioni, vedere il documento [NIST Computer Security Incident Handling Guide (SP 800-61, revisione 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implementare Privileged Identity Management (PIM) per JIT per altri ruoli amministrativi

Per Azure Active Directory, usare la funzionalità [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). L'attivazione a tempo limitato dei ruoli con privilegi consente di:

* Attivare i privilegi di amministratore per eseguire un'attività specifica
* Applicare MFA durante il processo di attivazione
* Usare gli avvisi per informare gli amministratori delle modifiche fuori banda
* Consentire agli utenti di garantire l'accesso con privilegi per un periodo di tempo preconfigurato
* Consenti agli amministratori della sicurezza di:

  * Individuare tutte le identità con privilegi
  * Visualizzare i report di controllo
  * Creare verifiche di accesso per identificare tutti gli utenti idonei per l'attivazione dei privilegi di amministratore

Se si usa già Azure AD Privileged Identity Management, modificare gli intervalli di tempo per i privilegi con limiti di tempo in base alle esigenze (ad esempio, per le finestre di manutenzione).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Determinare l'esposizione a protocolli di accesso basati su password (se si usa Exchange Online)

Si consiglia di identificare tutti i potenziali utenti che potrebbero essere catastrofici per l'organizzazione se le credenziali sono state compromesse. Per questi utenti, inserire i requisiti di autenticazione avanzata e usare Azure AD l'accesso condizionale per impedire l'accesso alla posta elettronica tramite nome utente e password. È possibile bloccare [l'autenticazione legacy usando l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)ed è possibile [bloccare l'autenticazione di base](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) tramite Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Completare una valutazione di verifica dei ruoli per i ruoli di Office 365 (se si usa Office 365)

Valutare se tutti gli utenti amministratori hanno i ruoli corretti (eliminare e riassegnare i ruoli in base a questa valutazione).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Esaminare l'approccio di gestione degli eventi imprevisti relativi alla sicurezza usato in Office 365 e confrontarlo con quello della propria organizzazione

È possibile scaricare il report in [Security Incident Management in Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302) (Gestione degli eventi imprevisti relativi alla sicurezza in Microsoft Office 365).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuare a proteggere gli account amministrativi con privilegi locali

Se il servizio Azure Active Directory è connesso con Active Directory locale, seguire le indicazioni nella fase 2 in [Protezione dell'accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). In questa fase è possibile:

* Distribuire workstation con accesso con privilegi per tutti gli amministratori
* Richiedi MFA
* Utilizzare un amministratore sufficiente per la manutenzione del controller di dominio, abbassando la superficie di attacco dei domini
* Distribuire Advanced Threat Assessment per il rilevamento degli attacchi

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure

#### <a name="establish-integrated-monitoring"></a>Implementare un monitoraggio integrato

Il [Centro sicurezza di Azure](../../security-center/security-center-intro.md):

* Offre funzionalità di monitoraggio della sicurezza e gestione dei criteri integrati nelle sottoscrizioni di Azure
* Consente di rilevare le minacce che altrimenti potrebbero non essere rilevate
* Funziona con un'ampia gamma di soluzioni di sicurezza

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Creare un inventario degli account con privilegi nelle macchine virtuali ospitate

Non è in genere necessario concedere agli utenti autorizzazioni senza restrizioni per tutte le sottoscrizioni o le risorse di Azure. Usare i ruoli di amministratore di Azure AD per concedere solo l'accesso agli utenti che devono svolgere i propri processi. È possibile usare Azure AD ruoli di amministratore per consentire a un amministratore di gestire solo le macchine virtuali in una sottoscrizione, mentre un altro può gestire i database SQL all'interno della stessa sottoscrizione. Per altre informazioni, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implementare PIM per i ruoli di amministratore di Azure AD

Usare Privileged identity Management con i ruoli di amministratore di Azure AD per gestire, controllare e monitorare l'accesso alle risorse di Azure. L'uso di PIM protegge riducendo il tempo di esposizione dei privilegi e aumentando la visibilità nell'uso tramite report e avvisi. Per altre informazioni, vedere [Gestire l'accesso alle risorse di Azure con Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Usare le integrazioni log di Azure per inviare i log di Azure rilevanti ai sistemi di informazioni di sicurezza e gestione degli eventi

Il servizio Integrazione log di Azure consente di integrare i log non elaborati delle risorse di Azure nei sistemi di informazioni di sicurezza e gestione degli eventi dell'organizzazione. [Integrazione log di Azure](../../security/fundamentals/azure-log-integration-overview.md) raccoglie gli eventi di Windows dai log di Windows Visualizzatore eventi e dalle risorse di Azure da:

* Log attività di Azure
* Avvisi del Centro sicurezza di Azure
* Log delle risorse di Azure

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad altre app cloud tramite Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implementare il provisioning degli utenti per le app connesse

Azure AD consente di automatizzare la creazione e la gestione delle identità utente in app cloud come Dropbox, Salesforce e ServiceNow. Per altre informazioni, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Integrare la protezione delle informazioni

Microsoft Cloud App Security consente di analizzare i file e impostare criteri in base alle etichette di classificazione Azure Information Protection, consentendo una maggiore visibilità e controllo dei dati cloud. È possibile analizzare e classificare i dati nel cloud e applicare etichette di Azure Information Protection. Per altre informazioni, vedere [Integrazione di Azure Information Protection](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Configurare l'accesso condizionale

Configurare l'accesso condizionale in base a un gruppo, alla posizione e alla sensibilità dell'applicazione per le [app Saas](https://azure.microsoft.com/overview/what-is-saas/) e le app connesse Azure ad. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Monitorare l'attività nelle app cloud connesse

Si consiglia di usare [Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) per assicurarsi che l'accesso utente sia protetto anche nelle applicazioni connesse. Questa funzionalità consente di proteggere l'accesso aziendale alle app cloud e di proteggere gli account amministrativi, consentendo di:

* Visibilità e controllo maggiori sulle app cloud
* Creazione di criteri per l'accesso, le attività e la condivisione dei dati
* Identificazione automatica di attività rischiose, comportamenti anomali e minacce
* Prevenzione della perdita dei dati
* Riduzione al minimo dei rischi e applicazione dei criteri e prevenzione delle minacce in modo automatico

L'agente SIEM di Cloud App Security integra Cloud App Security con il server SIEM per consentire il monitoraggio centralizzato di attività e avvisi di Office 365. Viene eseguito nel server ed esegue il pull di avvisi e attività da Cloud App Security e li trasmette al server SIEM. Per altre informazioni, vedere [Integrazione SIEM](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses"></a>Fase 4: continuare a creare difese

![Fase 4: adottare un comportamento di sicurezza attivo](./media/directory-admin-roles-secure/stage-four.png)

La fase 4 della roadmap deve essere implementata a sei mesi e oltre. Completa la roadmap per rafforzare le protezioni di accesso con privilegi da potenziali attacchi noti oggi. Per le minacce alla sicurezza di domani, è consigliabile visualizzare la protezione come processo in corso per aumentare i costi e ridurre la percentuale di esecuzioni riuscite destinate all'ambiente.

La protezione dell'accesso con privilegi è importante per stabilire le garanzie di sicurezza per le risorse aziendali. Tuttavia, deve far parte di un programma di sicurezza completo che fornisce le garanzie di sicurezza in corso. Questo programma deve includere elementi quali:

* Criteri di
* Operazioni
* Sicurezza delle informazioni
* Server
* APPLICAZIONI
* PC
* Dispositivi
* Infrastruttura cloud

Quando si gestiscono gli account di accesso con privilegi, è consigliabile eseguire le procedure seguenti:

* Assicurarsi che gli amministratori stiano eseguendo attività quotidiane come utenti senza privilegi
* Concedere l'accesso con privilegi solo quando necessario e rimuoverlo in seguito (just-in-Time)
* Mantieni i log delle attività di controllo relativi agli account con privilegi

Per altre informazioni sulla creazione di una roadmap di sicurezza completa, vedere [Risorse sull'architettura IT del cloud Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Per interagire con i servizi Microsoft per consentire l'implementazione di qualsiasi parte della roadmap, contattare il rappresentante Microsoft o vedere [creare difese informatiche critiche per proteggere l'azienda](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

La fase finale e continuativa della roadmap per la sicurezza dell'accesso con privilegi include i componenti seguenti.

### <a name="general-preparation"></a>Preparazione generale

#### <a name="review-admin-roles-in-azure-ad"></a>Esaminare i ruoli di amministratore in Azure AD

Determinare se i ruoli di amministratore Azure AD predefiniti correnti sono ancora aggiornati e assicurarsi che gli utenti siano solo i ruoli necessari. Con Azure AD è possibile assegnare amministratori distinti per gestire funzioni diverse. Per ulteriori informazioni, vedere [assegnazione dei ruoli di amministratore in Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Esaminare gli utenti che amministrano i dispositivi aggiunti ad Azure AD

Per altre informazioni, vedere [Come configurare dispositivi aggiunti all'identità ibrida di Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Esaminare i membri dei [ruoli di amministratore predefiniti di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Se non si usa Office 365, ignorare questo passaggio.
‎
#### <a name="validate-incident-response-plan"></a>Convalidare il piano di risposta agli eventi imprevisti

Per un continuo miglioramento del piano, Microsoft consiglia di convalidarne regolarmente il funzionamento:

* Esaminare la roadmap esistente per verificare se manca qualcosa
* In base all'analisi post mortem, modificare le procedure esistenti o definire nuove procedure
* Assicurarsi che le procedure e il piano di risposta agli eventi imprevisti aggiornati siano distribuiti nell'organizzazione


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Passaggi aggiuntivi per le organizzazioni che gestiscono l'accesso ad Azure 

Determinare se è necessario [trasferire la proprietà di una sottoscrizione di Azure a un altro account](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Rompere il vetro": cosa fare in caso di emergenza

![Account per l'accesso al vetro con interruzioni di emergenza](./media/directory-admin-roles-secure/emergency.jpeg)

1. Inviare notifiche ai responsabili delle chiavi e ai responsabili della sicurezza con le informazioni sull'evento imprevisto.

2. Esaminare la strategia da adottare in caso di attacco.

3. Accedere alla combinazione di nome utente e password dell'account "Break Glass" per accedere Azure AD.

4. Chiedere aiuto a Microsoft [creando una richiesta di supporto di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Esaminare i [report di accesso di Azure AD](../reports-monitoring/overview-reports.md). Potrebbe verificarsi un certo tempo tra un evento che si verifica e quando viene incluso nel report.

6. Per gli ambienti ibridi, se l'infrastruttura locale è federata e il server AD FS non è disponibile, è possibile passare temporaneamente dall'autenticazione federata per usare la sincronizzazione dell'hash delle password. Questa opzione Ripristina la Federazione del dominio fino a quando non diventa disponibile il server AD FS.

7. Monitorare la posta elettronica per gli account con privilegi.

8. Assicurarsi di salvare i backup dei log rilevanti per potenziali indagini forensi e legali.

Per altre informazioni su come Microsoft Office 365 gestisce gli eventi di sicurezza, vedere [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM) (Gestione degli eventi di sicurezza in Microsoft Office 365).

## <a name="faq-answers-for-securing-privileged-access"></a>Domande frequenti: risposte per la protezione dell'accesso con privilegi  

**D:** Cosa è possibile fare se non sono ancora stati implementati i componenti per l'accesso sicuro?

**Risposta:** Definire almeno due account di emergenza, assegnare MFA agli account amministratore con privilegi e separare gli account utente dagli account amministratore globale.

**D:** Dopo una violazione, qual è il problema principale da affrontare?

**Risposta:** Assicurarsi di richiedere l'autenticazione più avanzata per gli utenti altamente esposti.

**D:** Cosa succede se gli amministratori con privilegi sono stati disattivati?

**Risposta:** Creare un account di amministratore globale sempre aggiornato.

**D:** Cosa accade se è rimasto un solo amministratore globale e non è possibile raggiungerlo?

**Risposta:** Usare uno degli account di emergenza per ottenere immediatamente l'accesso con privilegi.

**D:** Come è possibile proteggere gli amministratori nell'organizzazione?

**Risposta:** Fare in modo che gli amministratori eseguano le attività quotidiane come utenti "standard" senza privilegi.

**D:** Quali sono le procedure consigliate per la creazione di account amministratore in Azure AD?

**Risposta:** Riservare l'accesso con privilegi ad attività amministrative specifiche.

**D:** Quali strumenti sono disponibili per ridurre l'accesso amministratore permanente?

**Risposta:** I ruoli di amministratore di Azure AD e Privileged Identity Management (PIM).

**D:** Qual è la posizione di Microsoft sulla sincronizzazione degli account amministratore in Azure AD?

**Risposta:** Gli account amministratore di livello 0 vengono usati solo per gli account AD locali. Questi account non sono in genere sincronizzati con Azure AD nel cloud. Gli account amministratore di livello 0 includono account, gruppi e altre risorse che hanno il controllo amministrativo diretto o indiretto della foresta Active Directory locale, dei domini, dei controller di dominio e degli asset.

**D:** Come è possibile impedire agli amministratori di assegnare l'accesso amministratore casuale nel portale?

**Risposta:** Usare account senza privilegi per tutti gli utenti e per la maggior parte degli amministratori. Iniziare sviluppando un footprint dell'organizzazione per determinare i pochi account amministratore che devono avere privilegi. E monitorano gli utenti amministratori appena creati.

## <a name="next-steps"></a>Passaggi successivi

* [Microsoft Trust Center per la sicurezza dei prodotti](https://www.microsoft.com/trustcenter/security) - Funzionalità di sicurezza dei prodotti e dei servizi Microsoft

* [Microsoft Trust Center - Conformità](https://www.microsoft.com/trustcenter/compliance/complianceofferings) - Set completo di offerte Microsoft per i servizi cloud

* [Indicazioni su come eseguire una valutazione dei rischi](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -gestire i requisiti di sicurezza e conformità per i servizi cloud Microsoft

### <a name="other-microsoft-online-services"></a>Altri Microsoft Online Services

* [Sicurezza di Microsoft Intune](https://www.microsoft.com/trustcenter/security/intune-security) - Intune offre funzionalità di gestione di dispositivi mobili, gestione di applicazioni mobili e gestione di PC dal cloud.

* [Sicurezza di Microsoft Dynamics 365](https://www.microsoft.com/trustcenter/security/dynamics365-security) - Dynamics 365 è la soluzione Microsoft basata su cloud che combina funzionalità CRM (Customer Relationship Management) ed ERP (Enterprise Resource Planning).
