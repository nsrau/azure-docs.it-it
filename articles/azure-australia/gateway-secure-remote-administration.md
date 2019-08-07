---
title: Amministrazione remota sicura del gateway in Azure Australia
description: Indicazioni sulla configurazione dell'amministrazione remota sicura all'interno delle aree australiane per soddisfare i requisiti specifici dei criteri, delle normative e delle normative del governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824200"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Amministrazione remota sicura del gateway in Azure Australia

È fondamentale per la disponibilità e l'integrità di qualsiasi sistema che le attività amministrative vengono eseguite in modo sicuro e controllate. Le attività amministrative devono essere eseguite da un dispositivo protetto, su una connessione sicura ed essere supportate da processi di autenticazione e autorizzazione avanzati. Amministrazione remota sicura garantisce che vengano eseguite solo le azioni autorizzate e solo gli amministratori autorizzati.

Questo articolo fornisce informazioni sull'implementazione di una funzionalità di amministrazione remota sicura per un sistema accessibile da Internet ospitato in Azure che è allineato al materiale sussidiario ACSC (Australian Cyber Security Centre) e allo scopo delle informazioni del ACSC Manuale della sicurezza (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisiti di ACSC (Australian Cyber Security Centre)

I requisiti di sicurezza generali per i sistemi Commonwealth sono definiti in ISM. Per aiutare le entità del Commonwealth a fornire un'amministrazione sicura, acsc [ha pubblicato ACSC Protect: Amministrazione sicura](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

In questo documento viene illustrata l'importanza dell'amministrazione sicura e viene suggerito un metodo per implementare un ambiente di amministrazione protetto. Nel documento vengono descritti gli elementi di una soluzione di amministrazione sicura come indicato di seguito:

|Elemento   |Descrizione   |
|---|---|
|Controllo di accesso con privilegi   |Controllare l'accesso agli account con privilegi è un controllo di sicurezza fondamentale che protegge gli account con privilegi da un utilizzo improprio. La metodologia di controllo degli accessi includerà i concetti di "privilegio minimo" e "necessità di avere", nonché processi e procedure per la gestione degli account del servizio e dei movimenti del personale.   |
|Autenticazione a più fattori   |L'implementazione di altri fattori di autenticazione oltre a nomi utente e passphrase, ad esempio i token fisici o le smart card, può aiutare a proteggere le risorse critiche. Se un antagonista compromette le credenziali per gli account con privilegi, dal momento che tutte le azioni amministrative devono prima passare attraverso una forma di autenticazione a più fattori, le conseguenze possono essere notevolmente ridotte.|
|Workstation con privilegi|L'uso di un ambiente protetto noto per le attività amministrative può comportare un minor rischio di compromissione della rete a causa dell'implementazione di controlli di sicurezza aggiuntivi.|
|Registrazione e controllo   |La generazione, la raccolta e l'analisi automatizzate degli eventi di sicurezza e di amministrazione da workstation, server, dispositivi di rete e caselle di salto consentiranno di rilevare i compromessi e i tentativi compromessi. L'automazione consente alle organizzazioni di rispondere più rapidamente, riducendo le implicazioni di un compromesso.|
|Segmentazione e separazione della rete|Segmentare una rete in zone logiche, ad esempio domini di sicurezza diversi, e separare ulteriormente queste reti logiche limitando i tipi di dati che fluiscono da una zona a un'altra, limita lo spostamento laterale. La segmentazione impedisce a un antagonista di accedere ad altre risorse.|
|Jump box|Un jump box è un server di accesso remoto con protezione avanzata, che in genere utilizza il software Microsoft Servizi Desktop remoto o Secure Shell (SSH). Le caselle di salto fungono da punto di partenza per gli amministratori che accedono a sistemi critici con tutte le azioni amministrative eseguite dall'host dedicato.|

Questo articolo fornisce un'architettura di riferimento per il modo in cui gli elementi sopra indicati possono essere usati per l'amministrazione sicura dei sistemi distribuiti in Azure.

## <a name="architecture"></a>Architettura

Fornire una funzionalità di amministrazione sicura richiede più componenti che interagiscono tra loro per formare una soluzione coerente. Nell'architettura di riferimento fornita, viene eseguito il mapping dei componenti agli elementi descritti [in ACSC Protect: Amministrazione sicura](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Architettura di amministrazione remota sicura di Azure](media/remote-admin.png)

## <a name="components"></a>Componenti

L'architettura è progettata per garantire che a un account con privilegi vengono concesse solo le autorizzazioni necessarie, che sia identificato in modo sicuro e che quindi venga fornito l'accesso alle interfacce amministrative solo da un dispositivo autorizzato e tramite comunicazioni sicure. meccanismi controllati e controllati.

|Soluzione| Componenti|Elementi|
|---|---|---|
|Proteggere i dispositivi |<ul><li>Workstation con privilegi</li><li>Dispositivo mobile</li><li>Microsoft Intune</li><li>Criteri di gruppo</li><li>Jump server/host Bastion</li><li>Amministrazione JIT (just-in-Time)</li></ul> |<ul><li>Workstation con privilegi</li><li>Jump box</li></ul>|
|Comunicazioni protette |<ul><li>Portale di Azure</li><li>Gateway VPN di Azure</li><li>Gateway Desktop remoto (RD)</li><li>Gruppi di sicurezza di rete (NGS)</li></ul> |<ul><li>Segmentazione e separazione della rete</li></ul>|
|Autenticazione avanzata |<ul><li>Controller di dominio (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Server dei criteri di rete</li><li>Azure MFA</li></ul> |<ul><li>Autenticazione a più fattori</li></ul> |
|Autorizzazione avanzata |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Accesso condizionale</li></ul>|<ul><li>Controllo di accesso con privilegi</li></ul>|
|||

>[!NOTE]
>Per ulteriori informazioni sull'elemento di registrazione e controllo, vedere l'articolo sulla registrazione, il [controllo e la visibilità del gateway](gateway-log-audit-visibility.md)

## <a name="administration-workflow"></a>Flusso di lavoro di amministrazione

L'amministrazione dei sistemi distribuiti in Azure è divisa in due categorie distinte, amministrando la configurazione di Azure e amministrando i carichi di lavoro distribuiti in Azure. La configurazione di Azure viene eseguita tramite il portale di Azure e l'amministrazione del carico di lavoro viene completata tramite meccanismi amministrativi come Remote Desktop Protocol (RDP), Secure Shell (SSH) o per le funzionalità PaaS, usando strumenti come SQL Management Studio.

Ottenere l'accesso per l'amministrazione è un processo in più passaggi che interessa i componenti elencati nell'architettura e richiede l'accesso alla portale di Azure e alla configurazione di Azure prima di poter effettuare l'accesso ai carichi di lavoro di Azure.

>[!NOTE]
> I passaggi descritti di seguito sono il processo generale con i componenti dell'interfaccia utente grafica (GUI) di Azure. Questi passaggi possono essere completati anche tramite altre interfacce, ad esempio PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Configurazione di Azure e accesso portale di Azure

|Passaggio |Descrizione |
|---|---|
|Accesso alla workstation con privilegi |L'amministratore firma la workstation con privilegi con credenziali amministrative. Criteri di gruppo controlli impediscono l'autenticazione degli account non amministrativi nella workstation con privilegi e impedisce l'autenticazione degli account amministrativi per le workstation senza privilegi. Microsoft Intune gestisce la conformità della workstation con privilegi per assicurarsi che sia aggiornata con patch software, antimalware e altri requisiti di conformità. |
|Accesso portale di Azure |L'amministratore apre un Web browser al portale di Azure, che viene crittografato con Transport Layer Security (TLS), e accede con credenziali amministrative. La richiesta di autenticazione viene elaborata tramite Azure Active Directory direttamente o tramite meccanismi di autenticazione quali Active Directory Federation Services (AD FS) o l'autenticazione pass-through. |
|Azure MFA |Autenticazione a più fattori di Azure invia una richiesta di autenticazione al dispositivo mobile registrato dell'account con privilegi. Il dispositivo mobile è gestito da Intune per garantire la conformità ai requisiti di sicurezza. L'amministratore deve prima eseguire l'autenticazione al dispositivo mobile e quindi all'app Microsoft Authenticator usando un PIN o un sistema biometrico prima che il tentativo di autenticazione sia autorizzato all'autenticazione a più fattori di Azure. |
|Accesso condizionale |Criteri di accesso condizionale verificare il tentativo di autenticazione per verificare che soddisfi i requisiti necessari, ad esempio l'indirizzo IP da cui provengono la connessione, l'appartenenza al gruppo per l'account con privilegi e lo stato di gestione e conformità del workstation con privilegi come segnalato da Intune. |
|Privileged Identity Management (PIM) |Tramite il portale di Azure l'amministratore può ora attivare o richiedere l'attivazione per i ruoli con privilegi per i quali ha l'autorizzazione tramite PIM. PIM garantisce che gli account con privilegi non dispongano di privilegi amministrativi permanenti e che tutte le richieste di accesso con privilegi siano solo per il tempo necessario per eseguire attività amministrative. PIM fornisce inoltre la registrazione di tutte le richieste e attivazioni a scopo di controllo. |
|Gestione delle identità e degli accessi|Una volta che l'account con privilegi è stato identificato in modo sicuro e i ruoli sono stati attivati, all'amministratore viene concesso l'accesso alle sottoscrizioni e alle risorse di Azure a cui sono state assegnate le autorizzazioni tramite la gestione delle identità e dell'accesso.|

Una volta che l'account con privilegi ha completato i passaggi per ottenere l'accesso amministrativo alla portale di Azure, è possibile configurare l'accesso ai carichi di lavoro e stabilire le connessioni amministrative.

### <a name="azure-workload-administration"></a>Amministrazione dei carichi di lavoro di Azure

|Passaggio |DESCRIZIONE|
|---|---|
|Accesso just-in-time (JIT)|Per ottenere l'accesso alle macchine virtuali, l'amministratore utilizza JIT per richiedere l'accesso a RDP al server di salto dall'indirizzo IP Gateway Desktop remoto e da RDP o SSH dal server di salto alle macchine virtuali del carico di lavoro rilevanti.|
|Gateway VPN di Azure|L'amministratore ora stabilisce una connessione VPN IPSec da punto a sito dalla workstation con privilegi al gateway VPN di Azure, che esegue l'autenticazione del certificato per stabilire la connessione.|
|Gateway Desktop remoto|L'amministratore tenta ora una connessione RDP al Jump server con Gateway Desktop remoto specificato nella configurazione del Connessione Desktop remoto. Il Gateway Desktop remoto ha un indirizzo IP privato raggiungibile tramite la connessione del gateway VPN di Azure. I criteri nel Gateway Desktop remoto controllano se l'account con privilegi è autorizzato ad accedere al Jump server richiesto. Il Gateway Desktop remoto richiede all'amministratore le credenziali e le invia alla richiesta di autenticazione al server dei criteri di rete.|
|Server dei criteri di rete|Il server dei criteri di accesso riceve la richiesta di autenticazione dal Gateway Desktop remoto e convalida il nome utente e la password rispetto a Active Directory prima di inviare una richiesta al Azure Active Directory per attivare una richiesta di autenticazione a più fattori di Azure.|
|Azure MFA|Autenticazione a più fattori di Azure invia una richiesta di autenticazione al dispositivo mobile registrato dell'account con privilegi. Il dispositivo mobile è gestito da Intune per garantire la conformità ai requisiti di sicurezza. L'amministratore deve prima eseguire l'autenticazione al dispositivo mobile e quindi all'app Microsoft Authenticator usando un PIN o un sistema biometrico prima che il tentativo di autenticazione sia autorizzato all'autenticazione a più fattori di Azure.|
|Jump server|Una volta eseguita l'autenticazione, la connessione RDP viene crittografata usando Transport Layer Security (TLS) e quindi inviata tramite il tunnel IPSec crittografato al gateway VPN di Azure, tramite il Gateway Desktop remoto e il server di salto. Dal Jump server, l'amministratore può ora usare RDP o SSH per le macchine virtuali del carico di lavoro come specificato nella richiesta JIT.|

## <a name="general-guidance"></a>Indicazioni generali

Quando si implementano i componenti elencati in questo articolo, si applicano le linee guida generali seguenti:

* Convalidare la disponibilità dell'area dei servizi, assicurandosi che tutti i dati rimangano entro i percorsi autorizzati e distribuiti in AU Central o AU Central 2 come prima preferenza per i carichi di lavoro protetti

* Per informazioni sullo stato della certificazione dei singoli servizi, vedere la pubblicazione *2018 protetta del report sulla certificazione Azure-ACSC* ed eseguire la valutazione automatica di tutti i componenti rilevanti non inclusi nel report in base alla guida per i *consumatori di ACSC: Microsoft Azure in protetto*

* Assicurarsi che la connettività di rete e qualsiasi configurazione proxy necessaria per accedere ai componenti di autenticazione necessari, ad esempio Azure AD, ADFS e PTA

* Usare i criteri di Azure per monitorare e applicare la conformità ai requisiti

* Assicurarsi che le macchine virtuali, in particolare i controller di Dominio di Active Directory, siano archiviate in account di archiviazione crittografati e utilizzino crittografia dischi di Azure

* Creare e gestire processi e governance affidabili per la gestione dei privilegi amministrativi e delle identità per sostenere i controlli tecnici elencati in questo articolo

|Risorsa|URL|
|---|---|
|Documenti australiani sulla conformità alle normative e ai criteri|[Documenti australiani sulla conformità alle normative e ai criteri](https://aka.ms/au-irap)|
|Prodotti Azure-aree australiane e non internazionali|[Prodotti Azure-aree australiane e non internazionali](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Strategie per mitigare gli eventi imprevisti della sicurezza informatica|[Strategie per mitigare gli eventi imprevisti della sicurezza informatica](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|Protezione ACSC: Amministrazione sicura|[Protezione ACSC: Amministrazione sicura](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Procedura: Integrare l'infrastruttura Gateway Desktop remoto con Azure MFA usando l'estensione NPS (Network Policy Server, Server dei criteri di rete) e Azure AD|[Integrare Gateway Desktop remoto con NPS e Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Linee guida sui componenti

In questa sezione vengono fornite informazioni sullo scopo di ogni componente e sul relativo ruolo nell'architettura di amministrazione remota protetta complessiva. Sono disponibili collegamenti aggiuntivi per accedere a risorse utili, ad esempio documentazione di riferimento, guide ed esercitazioni.

## <a name="secure-devices"></a>Proteggere i dispositivi

I dispositivi fisici usati dagli utenti con privilegi per eseguire funzioni amministrative sono destinazioni utili per gli attori malintenzionati. Mantenere la sicurezza e l'integrità dei dispositivi fisici e garantire che siano privi di software dannoso e proteggerli dalla compromissione è un aspetto fondamentale per fornire una funzionalità di amministrazione remota sicura. Questo implica la configurazione della sicurezza ad alta priorità come specificato nelle otto strategie essenziali di ACSC per mitigare gli eventi imprevisti della sicurezza informatica, ad esempio l'aggiunta di applicazioni, l'applicazione di patch, la protezione avanzata e l'applicazione di patch ai sistemi operativi. Queste funzionalità devono essere installate, configurate, controllate, convalidate e segnalate in per garantire che lo stato di un dispositivo sia conforme ai requisiti dell'organizzazione.

### <a name="privileged-workstation"></a>Workstation con privilegi

La workstation con privilegi è un computer con protezione avanzata che può essere utilizzato per eseguire mansioni amministrative ed è accessibile solo agli account amministrativi. La workstation con privilegi deve disporre di criteri e configurazione per limitare il software che può essere eseguito, l'accesso alle risorse di rete e Internet e le credenziali devono essere protette nel caso in cui il dispositivo venga rubato o compromesso. |

|Risorse|Collegamento|
|---|---|
|Panoramica dell'architettura di workstation con accesso con privilegi|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Materiale di riferimento per la protezione dell'accesso con privilegi|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>Dispositivo mobile

Un dispositivo mobile è a maggiore rischio di perdita o furto accidentale a causa della portabilità e delle dimensioni e deve essere protetto in modo appropriato. Il dispositivo mobile fornisce un importante fattore aggiuntivo per l'autenticazione, data la possibilità di applicare l'autenticazione per l'accesso ai dispositivi, la tracciabilità tramite i servizi di posizione, le funzioni di crittografia e la possibilità di essere cancellati in modalità remota. Quando si usa un dispositivo mobile come fattore di autenticazione aggiuntivo per Azure, il dispositivo deve essere configurato per l'uso dell'app Microsoft Authenticator con l'autenticazione PIN o biometrica e non per le chiamate telefoniche o gli SMS.

|Risorse|Collegamento|
|---|---|
|Metodi di Autenticazione di Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Come usare l'app Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune è il componente di Enterprise Mobility + Security che gestisce i dispositivi mobili e le app. Si integra strettamente con altri componenti come Azure Active Directory per il controllo delle identità e degli accessi e Azure Information Protection per la protezione dei dati. Intune fornisce criteri per le workstation e i dispositivi mobili per impostare i requisiti di conformità per l'accesso alle risorse e fornisce funzionalità di Reporting e controllo per ottenere informazioni sullo stato dei dispositivi amministrativi.

|Risorse|Collegamento|
|---|---|
|Documentazione di Microsoft Intune|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Introduzione alla conformità dei dispositivi in Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>Criteri di gruppo

Criteri di gruppo viene utilizzato per controllare la configurazione di sistemi operativi e applicazioni. I criteri di sicurezza controllano le impostazioni di autenticazione, autorizzazione e controllo di un sistema. Criteri di gruppo viene usato per rafforzare la workstation con privilegi, proteggere le credenziali amministrative e limitare l'accesso ai dispositivi con privilegi da parte degli account senza privilegi.

|Risorse|Collegamento|
|---|---|
|Impostazione Consenti accesso locale Criteri di gruppo|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>Jump server/host Bastion

Il Jump server/Bastion host è un punto centralizzato per l'amministrazione. Dispone degli strumenti necessari per eseguire mansioni amministrative, ma ha anche l'accesso alla rete necessario per connettersi alle risorse sulle porte amministrative. Il Jump server è il punto centrale per l'amministrazione dei carichi di lavoro delle macchine virtuali in questo articolo, ma può anche essere configurato come punto autorizzato per l'amministrazione delle funzionalità di piattaforma distribuita come servizio (PaaS), ad esempio SQL. L'accesso alle funzionalità di PaaS può essere limitato in base al servizio usando i controlli di identità e di rete.

|Risorse|Collegamento|
|---|---|
|Implementazione di host amministrativi protetti|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Accesso just-in-time (JIT)

JIT è una funzionalità del Centro sicurezza di Azure che utilizza i gruppi di sicurezza di rete (gruppi) per bloccare l'accesso a protocolli amministrativi come RDP e SSH nelle macchine virtuali. Le applicazioni ospitate nelle macchine virtuali continuano a funzionare come di consueto, ma per poter ottenere l'accesso amministrativo è necessario richiedere l'autorizzazione solo per un determinato periodo di tempo. Tutte le richieste vengono registrate a scopo di controllo.

|Risorse |Collegamento |
|---|---|
|Gestisci accesso just-in-time (JIT)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automazione dell'accesso just-in-time alle macchine virtuali di Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>Comunicazione protetta

Il traffico di comunicazione per le attività di amministrazione può contenere informazioni estremamente riservate, ad esempio le credenziali amministrative, che devono essere gestite e protette di conseguenza. Fornire comunicazioni sicure comporta funzionalità di crittografia affidabili per impedire intercettazioni e segmentazione di rete e restrizioni che limitano il traffico amministrativo agli endpoint autorizzati e controlla lo spostamento laterale in caso di compromissione di un sistema.

### <a name="azure-portal"></a>Portale di Azure

Le comunicazioni con il portale di Azure vengono crittografate tramite Transport Layer Security (TLS) e l'uso del portale di Azure è stato certificato da ACSC. Le entità del Commonwealth devono seguire le indicazioni contenute nella Guida per i *consumatori di ACSC* e configurare i browser Web per assicurarsi che stiano usando la versione più recente di TLS e con algoritmi di crittografia supportati.

|Risorse |Collegamento |
|---|---|
|Panoramica della crittografia di Azure-crittografia in transito|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Gateway VPN di Azure

Il gateway VPN di Azure fornisce la connessione crittografata sicura dalla workstation con privilegi ad Azure. Il gateway VPN di Azure è stato certificato da ACSC per fornire la comunicazione IPSec sicura. Le entità Commonwealth devono configurare il gateway VPN di Azure in base alla guida per i consumatori di ACSC, al report sulla certificazione ACSC e ad altre istruzioni specifiche.

|Risorse |Collegamento |
|---|---|
|Informazioni sulle connessioni da punto a sito|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Dettagli crittografia del gateway VPN di Azure|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Configurazione del gateway VPN di Azure|[Configurazione del gateway VPN di Azure](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>Gateway Desktop remoto (RD)

Gateway Desktop remoto è un meccanismo protetto per il controllo e l'autorizzazione delle connessioni RDP ai sistemi. Funziona incapsulando il traffico RDP in HyperText Transfer Protocol Secure (HTTPS) e crittografato usando TLS. TLS fornisce un livello aggiuntivo di sicurezza per il traffico amministrativo.

|Risorse |Collegamento |
|---|---|
|Architettura Servizi Desktop remoto|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NGS)

Gruppi funzionerà come elenchi di controllo di accesso (ACL) per il traffico di rete in ingresso o in uscita da subnet o macchine virtuali. Gruppi forniscono la segmentazione della rete e forniscono un meccanismo per il controllo e la limitazione dei flussi di comunicazione consentiti tra i sistemi. Gruppi sono un componente principale di just-in-Time Administration (JIT) per consentire o negare l'accesso ai protocolli amministrativi.

|Risorse |Collegamento |
|---|---|
|Panoramica sui gruppi di sicurezza di Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Procedura: Pianificare le reti virtuali|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>Autenticazione avanzata

Identificare in modo sicuro gli utenti con privilegi prima di concedere l'accesso ai sistemi è un componente fondamentale dell'amministrazione sicura. Per proteggere le credenziali associate a un account con privilegi e impedire agli utenti malintenzionati di accedere ai sistemi tramite rappresentazione o furto di credenziali, è necessario disporre dei meccanismi.

### <a name="domain-controller-dc"></a>Controller di dominio (DC)

A un livello elevato, un controller di dominio ospita una copia del database di Active Directory, che contiene tutti gli utenti, i computer e i gruppi all'interno di un dominio. I controller di dominio eseguono l'autenticazione per utenti e computer. I controller di dominio in questa architettura sono ospitati come macchine virtuali in Azure e forniscono servizi di autenticazione per gli account con privilegi che si connettono a Jump server e macchine virtuali del carico di lavoro

|Risorse |Collegamento |
|---|---|
|Panoramica di Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD è il servizio di autenticazione per Azure. Contiene il cloud

identità e fornisce l'autenticazione e l'autorizzazione per un ambiente Azure. Azure AD possono essere sincronizzati con Active Directory tramite Azure AD Connect e possono fornire l'autenticazione federata tramite Active Directory Federation Services (AD FS) e Azure AD Connect. Azure AD è un componente principale dell'amministrazione sicura.

|Risorse |Collegamento |
|---|---|
|Documentazione di Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Documentazione di identità ibrida|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>Server dei criteri di rete

Un server dei criteri di server è un server di autenticazione e criteri che fornisce processi avanzati di autenticazione e autorizzazione. Il server NPS in questa architettura viene fornito per integrare l'autenticazione a più fattori di Azure con le richieste di autenticazione di Gateway Desktop remoto. NPS dispone di un plug-in specifico per supportare l'integrazione con l'autenticazione a più fattori di Azure in Azure AD.

|Risorse |Collegamento |
|---|---|
|Documentazione del server dei criteri di rete|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure multi-factor authentication è un servizio di autenticazione fornito all'interno Azure Active Directory per consentire le richieste di autenticazione oltre un nome utente e una password per accedere alle risorse cloud, ad esempio il portale di Azure. L'autenticazione a più fattori di Azure supporta una gamma di metodi di autenticazione e questa architettura utilizza l'app Microsoft Authenticator per migliorare la sicurezza e l'integrazione con NPS.

|Risorse |Collegamento |
|---|---|
|Come funziona: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Procedura: Distribuire il servizio Azure Multi-Factor Authentication basato sul cloud|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>Autorizzazione avanzata

Una volta identificato in modo sicuro un account con privilegi, è possibile concedere l'accesso alle risorse. Autorizzazione controlla e gestisce i privilegi assegnati a un account specifico. I processi di autorizzazione avanzata si allineano alla strategia essenziale di ACSC per mitigare gli eventi imprevisti di sicurezza informatici per limitare i privilegi amministrativi.

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

L'accesso per eseguire azioni con privilegi in Azure è basato sui ruoli assegnati a tale account. Azure include un intervallo completo e granulare di ruoli con autorizzazioni specifiche per attività specifiche intraprese. Questi ruoli possono essere concessi a più livelli, ad esempio una sottoscrizione o un gruppo di risorse. L'assegnazione di ruolo e la gestione delle autorizzazioni sono basate su account e gruppi in Azure Active Directory e vengono gestite tramite il controllo di accesso (IAM) in Azure.

|Risorse |Collegamento |
|---|---|
|Controllo degli accessi in base al ruolo di Azure|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Informazioni sulle definizioni di ruolo|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM è un componente Azure Active Directory che controlla l'accesso ai ruoli con privilegi. Per gli account con privilegi non è necessario un accesso con privilegi permanente o permanente, ma è invece possibile concedere l'accesso con privilegi per un determinato periodo di tempo per completare le attività con privilegi. PIM fornisce controlli aggiuntivi per la gestione e la limitazione dell'accesso con privilegi, nonché per la registrazione e il controllo per tenere traccia delle istanze di utilizzo dei privilegi.

|Risorse |Collegamento |
|---|---|
|Documentazione di Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Iniziare a usare PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>Accesso condizionale

L'accesso condizionale è un componente di Azure Active Directory che consente o nega l'accesso alle risorse in base alle condizioni. Queste condizioni possono essere basate sul percorso di rete, il tipo di dispositivo, lo stato di conformità, l'appartenenza ai gruppi e altro ancora. L'accesso condizionale viene usato per applicare l'autenticazione a più fattori, la gestione dei dispositivi e la conformità tramite Intune e l'appartenenza ai gruppi degli account amministrativi.

|Risorse |Collegamento |
|---|---|
|Documentazione sull'accesso condizionale|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Procedura: Richiedi i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sul controllo dei flussi di traffico attraverso i componenti del gateway in Azure, vedere l'articolo sulla [gestione e sul controllo del traffico in ingresso gateway](gateway-ingress-traffic.md) .
