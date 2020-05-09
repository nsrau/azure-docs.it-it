---
title: Procedure consigliate per la sicurezza di desktop virtuali Windows-Azure
description: Procedure consigliate per garantire la sicurezza dell'ambiente desktop virtuale di Windows.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5330c427088734b049b2cb4f7735ac0099a52b47
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966675"
---
# <a name="security-best-practices"></a>Procedure consigliate per la sicurezza

Desktop virtuale di Windows è un servizio desktop virtuale gestito che include molte funzionalità di sicurezza per garantire la sicurezza dell'organizzazione. In una distribuzione desktop virtuale di Windows, Microsoft gestisce parti dei servizi per conto del cliente. Il servizio dispone di molte funzionalità di sicurezza avanzate predefinite, ad esempio la connessione inversa, che riducono i rischi correlati alla possibilità di accedere a desktop remoti ovunque ci si trovi.

Questo articolo descrive i passaggi aggiuntivi che è possibile eseguire come amministratore per proteggere le distribuzioni di desktop virtuali Windows dei clienti.

## <a name="security-responsibilities"></a>Responsabilità della sicurezza

Ciò che rende i servizi cloud diversi dalle tradizionali infrastrutture desktop virtuali locali (VDIs) è il modo in cui gestiscono le responsabilità di sicurezza. Ad esempio, in un'infrastruttura VDI locale tradizionale, il cliente è responsabile di tutti gli aspetti della sicurezza. Tuttavia, nella maggior parte dei servizi cloud, queste responsabilità sono condivise tra il cliente e la società.

Quando si usa desktop virtuale di Windows, è importante comprendere che, mentre alcuni componenti sono già protetti per l'ambiente in uso, sarà necessario configurare altre aree in modo da adattarsi alle esigenze di sicurezza dell'organizzazione.

Di seguito sono riportate le esigenze di sicurezza di cui si è responsabili nella distribuzione di desktop virtuali Windows:

| Necessità di sicurezza | Il cliente è responsabile di questo? |
|---------------|:-------------------------:|
|Identità|Sì|
|Dispositivi utente (dispositivi mobili e PC)|Sì|
|Sicurezza app|Sì|
|Sistema operativo host sessione|Sì|
|Configurazione di distribuzione|Sì|
|Controlli di rete|Sì|
|Piano di controllo della virtualizzazione|No|
|Host fisici|No|
|Rete fisica|No|
|Data Center fisico|No|

Le esigenze di sicurezza di cui il cliente non è responsabile sono gestite da Microsoft.

## <a name="azure-security-best-practices"></a>Procedure consigliate per la sicurezza di Azure

Desktop virtuale di Windows è un servizio di Azure. Per ottimizzare la sicurezza della distribuzione di desktop virtuali Windows, è necessario assicurarsi di proteggere anche l'infrastruttura di Azure e il piano di gestione circostanti. Per proteggere l'infrastruttura, valutare il modo in cui desktop virtuale Windows si inserisce nel più ampio ecosistema di Azure. Per altre informazioni sull'ecosistema di Azure, vedere procedure consigliate [e modelli](../security/fundamentals/best-practices-and-patterns.md)per la sicurezza di Azure.

Questa sezione descrive le procedure consigliate per la protezione dell'ecosistema di Azure.

### <a name="enable-azure-security-center"></a>Abilitare il Centro sicurezza di Azure

Si consiglia di abilitare il Centro sicurezza di Azure standard per le sottoscrizioni, le macchine virtuali, gli insiemi di credenziali delle chiavi e gli account di archiviazione.

Con il Centro sicurezza di Azure standard è possibile:

* Gestire le vulnerabilità.
* Valutare la conformità con i Framework comuni come PCI.
* Rafforzare la protezione complessiva dell'ambiente.

Per altre informazioni, vedere [onboarding your Azure Subscription to Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Migliorare il Punteggio sicuro

Il Punteggio sicuro fornisce consigli e consigli sulle procedure consigliate per migliorare la sicurezza complessiva. Queste raccomandazioni sono classificate in ordine di priorità per facilitare la scelta di quelle più importanti e le opzioni di correzione rapida consentono di risolvere rapidamente le potenziali vulnerabilità. Questi consigli vengono aggiornati anche nel corso del tempo, mantenendo aggiornati i modi migliori per gestire la sicurezza dell'ambiente. Per altre informazioni, vedere [migliorare il Punteggio sicuro nel centro sicurezza di Azure](../security-center/security-center-secure-score.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Procedure consigliate per la sicurezza di desktop virtuali Windows

Desktop virtuale di Windows include molti controlli di sicurezza incorporati. In questa sezione verranno illustrati i controlli di sicurezza che è possibile usare per proteggere gli utenti e i dati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

La richiesta di autenticazione a più fattori per tutti gli utenti e gli amministratori in desktop virtuali Windows migliora la sicurezza dell'intera distribuzione. Per altre informazioni, vedere [abilitare Azure multi-factor authentication per desktop virtuale di Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Abilitare l'accesso condizionale

L'abilitazione dell' [accesso condizionale](../active-directory/conditional-access/best-practices.md) consente di gestire i rischi prima di concedere agli utenti l'accesso all'ambiente desktop virtuale di Windows. Quando si decidono gli utenti a cui concedere l'accesso, si consiglia di considerare anche chi è l'utente, il modo in cui accedono e il dispositivo in uso.

### <a name="collect-audit-logs"></a>Raccogliere log di controllo

L'abilitazione della raccolta dei log di controllo consente di visualizzare l'attività di utenti e amministratori correlata al desktop virtuale di Windows. Di seguito sono riportati alcuni esempi di log di controllo principali:

-   [Azure Activity Log](../azure-monitor/platform/activity-log-collect.md)
-   [Log attività Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Host sessione](../azure-monitor/platform/agent-windows.md)
-   [Log di diagnostica del desktop virtuale di Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Log di insiemi di credenziali delle chiavi](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Utilizzare RemoteApps

Quando si sceglie un modello di distribuzione, è possibile consentire agli utenti remoti di accedere a interi desktop virtuali o solo selezionare le applicazioni. Le applicazioni remote, o RemoteApp, offrono un'esperienza uniforme quando l'utente lavora con le app sul proprio desktop virtuale. RemoteApps riduce il rischio semplicemente consentendo all'utente di usare con un subset del computer remoto esposto dall'applicazione.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorare l'utilizzo con monitoraggio di Azure

Monitoraggio dell'utilizzo e della disponibilità del servizio desktop virtuale di Windows con [monitoraggio di Azure](https://azure.microsoft.com/services/monitor/). Prendere in considerazione la creazione di [avvisi di integrità del servizio](../service-health/alerts-activity-log-service-notifications.md) per il servizio desktop virtuale Windows per ricevere notifiche ogni volta che si verifica un evento di interesse del servizio.

## <a name="session-host-security-best-practices"></a>Procedure consigliate per la sicurezza dell'host sessione

Gli host di sessione sono macchine virtuali che vengono eseguite all'interno di una sottoscrizione di Azure e di una rete virtuale. La sicurezza complessiva della distribuzione del desktop virtuale di Windows dipende dai controlli di sicurezza inseriti negli host della sessione. In questa sezione vengono descritte le procedure consigliate per mantenere protetti gli host della sessione.

### <a name="enable-endpoint-protection"></a>Abilita Endpoint Protection

Per proteggere la distribuzione da software dannoso noto, è consigliabile abilitare Endpoint Protection in tutti gli host di sessione. È possibile utilizzare Windows Defender antivirus o un programma di terze parti. Per altre informazioni, vedere [Guida alla distribuzione di Windows Defender antivirus in un ambiente VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus). 

Per le soluzioni di profilo, ad esempio FSLogix o altre soluzioni che montano file VHD, è consigliabile escludere le estensioni di file VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Installare un prodotto di rilevamento e risposta dell'endpoint

Si consiglia di installare un prodotto di rilevamento e risposta dell'endpoint per fornire funzionalità avanzate di rilevamento e risposta. Per i sistemi operativi server con il [Centro sicurezza di Azure](../security-center/security-center-services.md) abilitato, l'installazione di un prodotto EDR distribuirà Defender ATP. Per i sistemi operativi client, è possibile distribuire [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) o un prodotto di terze parti a tali endpoint.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Abilitare le valutazioni della gestione delle minacce e delle vulnerabilità

Identificare le vulnerabilità software presenti nei sistemi operativi e nelle applicazioni è essenziale per garantire la protezione dell'ambiente. Il Centro sicurezza di Azure consente di identificare le aree problematiche tramite valutazioni delle vulnerabilità per i sistemi operativi server. È anche possibile usare Defender ATP, che fornisce la gestione delle minacce e delle vulnerabilità per i sistemi operativi desktop. È anche possibile usare prodotti di terze parti se si è troppo inclini, anche se è consigliabile usare il Centro sicurezza di Azure e Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Applicare patch alle vulnerabilità del software nell'ambiente

Una volta identificata una vulnerabilità, è necessario applicarvi una patch. Questo vale anche per gli ambienti virtuali, inclusi i sistemi operativi in esecuzione, le applicazioni distribuite all'interno di essi e le immagini da cui vengono creati nuovi computer. Seguire le comunicazioni delle notifiche patch del fornitore e applicare le patch in modo tempestivo. Si consiglia di applicare le patch alle immagini di base mensilmente per assicurarsi che i computer appena distribuiti siano più protetti possibile.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Stabilire il tempo massimo di inattività e i criteri di disconnessione

La firma degli utenti quando sono inattive conserva le risorse e impedisce l'accesso da parte di utenti non autorizzati. È consigliabile che i timeout Bilancino la produttività degli utenti e l'utilizzo delle risorse. Per gli utenti che interagiscono con le applicazioni senza stato, prendere in considerazione criteri più aggressivi che spengono i computer e mantengono le risorse. Disconnettere le applicazioni a esecuzione prolungata che continuano a essere eseguite se un utente è inattivo, ad esempio una simulazione o un rendering CAD, può interrompere il lavoro dell'utente e potrebbe anche richiedere il riavvio del computer.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurare blocchi schermo per le sessioni inattive

È possibile impedire l'accesso indesiderato al sistema configurando desktop virtuale di Windows per bloccare lo schermo di un computer durante il tempo di inattività e richiedere l'autenticazione per sbloccarlo.

### <a name="establish-tiered-admin-access"></a>Stabilire l'accesso amministratore a più livelli

Si consiglia di non concedere all'amministratore degli utenti l'accesso ai desktop virtuali. Se sono necessari pacchetti software, è consigliabile renderli disponibili tramite utilità di gestione della configurazione come Microsoft Endpoint Manager. In un ambiente con più sessioni, è consigliabile non consentire agli utenti di installare direttamente il software.

### <a name="consider-which-users-should-access-which-resources"></a>Considerare gli utenti che devono accedere alle risorse

Considerare gli host di sessione come un'estensione della distribuzione desktop esistente. Si consiglia di controllare l'accesso alle risorse di rete in modo analogo ad altri desktop nell'ambiente, ad esempio usando la segmentazione e il filtro di rete. Per impostazione predefinita, gli host di sessione possono connettersi a qualsiasi risorsa in Internet. Esistono diversi modi per limitare il traffico, tra cui l'uso del firewall di Azure, di appliance virtuali di rete o di proxy. Se è necessario limitare il traffico, assicurarsi di aggiungere le regole appropriate in modo che desktop virtuale Windows possa funzionare correttamente.

### <a name="manage-office-pro-plus-security"></a>Gestire Office Pro Plus Security

Oltre a proteggere gli host di sessione, è importante proteggere anche le applicazioni in esecuzione all'interno di esse. Office Pro Plus è una delle applicazioni più comuni distribuite negli host di sessione. Per migliorare la sicurezza della distribuzione di Office, è consigliabile usare l' [Advisor di criteri di sicurezza](/DeployOffice/overview-of-security-policy-advisor) per le app Microsoft 365 per Enterprise. Questo strumento identifica i criteri che è possibile applicare alla distribuzione per una maggiore sicurezza. Security Policy Advisor consiglia inoltre i criteri in base al loro effetto sulla sicurezza e la produttività.

### <a name="other-security-tips-for-session-hosts"></a>Altri suggerimenti sulla sicurezza per gli host di sessione

Limitando le funzionalità del sistema operativo, è possibile rafforzare la sicurezza degli host della sessione. Ecco alcune operazioni che è possibile eseguire:

- Controllare il reindirizzamento dei dispositivi reindirizzando le unità, le stampanti e i dispositivi USB al dispositivo locale di un utente in una sessione di desktop remoto. Si consiglia di valutare i requisiti di sicurezza e verificare se queste funzionalità devono essere disabilitate o meno.

- Limitare l'accesso a Esplora risorse nascondendo i mapping delle unità locali e remote. In questo modo si impedisce agli utenti di individuare informazioni indesiderate sulla configurazione del sistema e sugli utenti.

- Evitare l'accesso RDP diretto agli host di sessione nell'ambiente in uso. Se è necessario l'accesso RDP diretto per l'amministrazione o la risoluzione dei problemi, abilitare l'accesso [just-in-Time](../security-center/security-center-just-in-time.md) per limitare la potenziale superficie di attacco in un host sessione.

- Concedere agli utenti autorizzazioni limitate quando accedono a file system locali e remoti. È possibile limitare le autorizzazioni assicurandosi che i file system locali e remoti usino gli elenchi di controllo di accesso con privilegi minimi. In questo modo, gli utenti possono accedere solo a ciò che necessitano e non possono modificare o eliminare le risorse critiche.

- Impedire l'esecuzione di software indesiderato negli host di sessione. È possibile abilitare app Locker per la sicurezza aggiuntiva negli host di sessione, assicurando che solo le app consentite possano essere eseguite nell'host.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare l'autenticazione a più fattori, vedere [configurare l'autenticazione](set-up-mfa.md)a più fattori.